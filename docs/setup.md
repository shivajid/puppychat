# Puppy Chat — Setup & Installation Guide

> Installing the team DMG? Start with the [teammate quick start](team-quick-start.md). The guide below also covers development and infrastructure administration.

A native macOS desktop app that connects to remote, self-hosted LLMs
(KimiK3, GLM5.3, Qwen3.8) and a sandboxed browser gateway — all reachable on
your Mac via stable local endpoints.

This guide walks a new user from a fresh machine to a fully working setup.

---

## Table of Contents
1. [What you get](#what-you-get)
2. [Networking architecture](#networking-architecture-how-your-mac-reaches-the-models)
3. [Bastion host details](#bastion-host-details)
4. [Getting access](#getting-access-what-to-request)
5. [Prerequisites](#prerequisites)
6. [One-time GCP setup](#one-time-gcp-setup)
7. [Daily use — start the endpoints](#daily-use)
8. [Install & run the app](#install-run-the-app)
9. [Connect the app to a model](#connect-the-app-to-a-model)
10. [Endpoint reference](#endpoint-reference)
11. [Troubleshooting](#troubleshooting)
12. [FAQ](#faq)

---

## What you get

After setup, these **OpenAI-compatible** endpoints run on your Mac's localhost:

| Local port | Model / Service | Context window |
|-----------:|-----------------|----------------|
| `30100` | KimiK3 (`moonshotai/Kimi-K3`) | 1,048,576 |
| `30010` | KimiK3 — *reserved for the Puppy chat UI* | 1,048,576 |
| `30011` | GLM5.3 Flash (`zai-org/GLM-5.3-Flash`) | 1,048,576 |
| `30012` | Qwen3.8 Flash Next (`Qwen3.8-Flash-Next`) | 262,144 |
| `8080`  | Sandboxed browser gateway (`/healthz`) | — |

The desktop app points at any of these to chat with that model.

---

## Networking architecture (how your Mac reaches the models)

Understanding this makes troubleshooting *much* easier. Each model runs as a pod
on a private GKE cluster. Your Mac has **no direct network route** to those
clusters (the nodes have no public IPs and sit on isolated VPCs). So traffic
flows through a small, stable path:

```
┌─────────┐   IAP TCP tunnel    ┌────────────┐                ┌─────────┐   ┌──────────┐
│  Mac    │ ──────────────────► │ Bastion VM │ ─────────────► │   ILB   │─► │  Model   │
│localhost│  (gcloud start-     │  (nginx    │  VPC-internal  │(stable  │   │  pods    │
│ :PORT   │   iap-tunnel)       │  reverse-  │  private IP    │  IP)    │   │ (GKE)    │
└─────────┘                     │  proxy)    │                └─────────┘   └──────────┘
                                └────────────┘
```

**The three building blocks:**

1. **Internal Load Balancer (ILB)** — a Kubernetes `Service` of type
   `LoadBalancer` with `networking.gke.io/load-balancer-type: "Internal"`. GCP
   assigns it a **stable, reserved private IP** (e.g. `<GLM_ILB_IP>`) that never
   changes, survives pod restarts/reschedules, and load-balances across all
   healthy model pods. This is the *durable address* of a model inside its VPC.

2. **Bastion VM** — a tiny `e2-small` VM with **no external IP**, sitting on the
   same VPC as the model. It runs an `nginx` reverse-proxy (in a `--restart=always`
   Docker container) that forwards traffic from its own port to the ILB. Because
   the VM is on the VPC, it *can* reach the ILB; your Mac can't, so the bastion
   is the "door" into the private network.

3. **IAP TCP tunnel** — `gcloud compute start-iap-tunnel` creates an encrypted
   tunnel from a localhost port on your Mac, through **Identity-Aware Proxy**,
   to the bastion's port. IAP enforces auth (your Google identity + the
   `iap.tunnelResourceAccessor` role) without needing a VPN or any public IP.

**Why not just `kubectl port-forward`?** Two reasons:
- The **browser gateway** pod runs under **GKE Sandbox (gVisor)**, which the
  kubelet's port-forward *cannot* reach — it fails with a connection error. A
  bastion+IAP sidesteps gVisor entirely.
- Even where port-forward works (the GPU model clusters), it's tied to a single
  pod/process and dies on restarts. The bastion + ILB give a stable IP that
  outlives pods.

### The four networks (and why they're separate)
Each cluster lives on its **own isolated VPC**, and KimiK3's is in a **different
GCP project** — so there's no shared network and no VPC peering:

| Model | Cluster | Project | VPC | ILB IP |
|---|---|---|---|---|
| KimiK3 | `<KIMI_CLUSTER>` | `<KIMI_GCP_PROJECT>` | `<KIMI_VPC>` | `<KIMI_ILB_IP>` |
| GLM5.3 | `<GLM_CLUSTER>` | `<MODEL_GCP_PROJECT>` | `shivaj-minimax-...-net` | `<GLM_ILB_IP>` |
| Qwen3.8 | `<QWEN_CLUSTER>` | `<MODEL_GCP_PROJECT>` | `<QWEN_VPC>` | `<QWEN_ILB_IP>` |
| Browser | `<BROWSER_GATEWAY_CLUSTER>` | `<MODEL_GCP_PROJECT>` | `<BROWSER_VPC>` | `<BROWSER_ILB_IP>` |

Because the VPCs are isolated, **each model needs its own bastion on its own
VPC** (one bastion can't reach another VPC's ILB).

### KimiK3 is the exception (no bastion)
KimiK3's cluster is in `<KIMI_GCP_PROJECT>`, where users typically have **GKE
access but not compute/VM access** — so a bastion VM can't be created there.
Instead, KimiK3 uses a **supervised `kubectl port-forward` directly to the ILB
Service** (`svc/<KIMI_ILB_SERVICE>`). <KIMI_CLUSTER> is *not* gVisor, so port-forward works;
and because it targets the stable ILB (not a single pod) plus an auto-reconnect
loop, it's still reliable. If you later get compute access in that project, run
`scripts/setup-kimi-bastion.sh` to upgrade it to the full bastion pattern.

---

## Bastion host details

Each bastion is:
- **Machine type:** `e2-small` (~$13/mo; `e2-micro` works too for lower cost).
- **Image:** Container-Optimized OS (COS) — minimal, auto-updating, runs Docker.
- **Network:** no external IP (IAP-only ingress), on the model's VPC/subnet.
- **Proxy:** `nginx:alpine` container, `--restart=always`, forwarding
  `:<port>` → `http://<ILB_IP>:80`. Config lives in `/tmp/nginx/default.conf`.
- **Firewall:** two ingress rules — `tcp:22` (SSH) and `tcp:<port>` (proxy),
  both restricted to the IAP source range `35.235.240.0/20` only.
- **Internet egress:** needs **Cloud NAT** on the VPC so the VM can `docker pull`
  the nginx image (the GLM VPC required one to be created; Qwen's already had it).

Useful bastion commands:
```bash
# SSH into a bastion (via IAP)
gcloud compute ssh <GLM_BASTION> --zone=<ZONE> \
  --project=<MODEL_GCP_PROJECT> --tunnel-through-iap

# check the proxy container
sudo docker ps --filter name=<GLM_PROXY_CONTAINER>

# test the bastion -> ILB path manually
curl http://<GLM_ILB_IP>/v1/models
```

---

## Getting access (what to request)

You need different permissions per model, because they span two GCP projects.
Request these from the project owner / your admin:

### For GLM5.3, Qwen3.8, and the Browser gateway (project `<MODEL_GCP_PROJECT>`)
| Role | Why |
|---|---|
| `roles/iap.tunnelResourceAccessor` | create IAP TCP tunnels to the bastions |
| `roles/compute.osLogin` (or instance SSH key) | SSH into a bastion for debugging |
| `roles/container.viewer` (or higher) | `kubectl get-credentials` / inspect services |

### For KimiK3 (project `<KIMI_GCP_PROJECT>`)
| Role | Why |
|---|---|
| `roles/container.developer` (or GKE access) | run `kubectl port-forward` to the ILB |
| *(optional, for the full bastion)* `roles/compute.admin` + `roles/compute.networkAdmin` | create the bastion VM + firewall + reserved IP |

> **Note:** `<KIMI_GCP_PROJECT>` uses **IAM Conditions**, so when an admin
> grants a role they must attach a condition (e.g. the "Can delegate roles"
> condition). Also, that project's condition allowlist includes
> `roles/compute.admin` but **not** the granular `roles/compute.instanceAdmin.v1`
> — so request `compute.admin`.

### Verify your access
```bash
# Can you tunnel? (should not error with 403)
gcloud compute start-iap-tunnel <GLM_BASTION> 30011 \
  --local-host-port=localhost:30011 --zone=<ZONE> --project=<MODEL_GCP_PROJECT>

# Can you reach the KimiK3 cluster?
kubectl --context=gke_<KIMI_GCP_PROJECT>_<REGION>_<KIMI_CLUSTER> \
  get svc <KIMI_ILB_SERVICE> -n <NAMESPACE>
```

---

## Prerequisites

### 1. macOS
Tested on macOS (Apple Silicon and Intel). You need admin rights to install tools.

### 2. Homebrew (recommended)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 3. Node.js v22.12+ (for development and tests)
```bash
brew install node
node --version   # expect v22.12 or higher; tested with v22.22
```

### 4. Google Cloud SDK (`gcloud` + `kubectl`)
The models run on GKE; you reach them through `gcloud` IAP tunnels / `kubectl`.

```bash
# Install gcloud CLI
brew install --cask google-cloud-sdk

# kubectl component + the GKE auth plugin
gcloud components install kubectl gke-gcloud-auth-plugin
```

> **Python note (important on macOS):** the gcloud bundled with Homebrew can pick a
> broken Python. If `gcloud` errors with `No such file or directory` for Python,
> point it at a working interpreter:
> ```bash
> echo 'export CLOUDSDK_PYTHON=/usr/local/bin/python3.13' >> ~/.zshrc
> source ~/.zshrc
> ```
> (Adjust the path to whatever `python3 --version` works on your machine.)

### 5. GCP access
You need:
- A Google account with access to the relevant GCP project(s).
- The IAP API enabled (it already is on the model projects).
- Permission to use **IAP TCP tunneling** (`roles/iap.tunnelResourceAccessor`)
  on the bastion VMs, and **GKE** access for the KimiK3 cluster.

Authenticate:
```bash
gcloud auth login
gcloud auth application-default login
gcloud config set project <MODEL_GCP_PROJECT>
```

### 6. Corp SSO cert (Google internal users only)
If you are on a corp network / accessing corp-restricted VMs, refresh your SSO/SSH cert:
```bash
gcert
```
You'll need a valid cert for IAP tunnels to some bastions.

---

## One-time GCP setup

> **Already done for you?** If a teammate set up the bastions/ILBs, skip to
> [Daily use](#daily-use). The scripts below only need to run once per environment.

Fetch cluster credentials so `kubectl` can talk to the clusters:

```bash
# KimiK3 (different project)
gcloud container clusters get-credentials <KIMI_CLUSTER> \
  --region <REGION> --project <KIMI_GCP_PROJECT>

# GLM5.3
gcloud container clusters get-credentials <GLM_CLUSTER> \
  --zone <ZONE> --project <MODEL_GCP_PROJECT>

# Qwen3.8
gcloud container clusters get-credentials <QWEN_CLUSTER> \
  --zone <ZONE> --project <MODEL_GCP_PROJECT> --dns-endpoint

# Browser gateway
gcloud container clusters get-credentials <BROWSER_GATEWAY_CLUSTER> \
  --zone <ZONE> --project <MODEL_GCP_PROJECT>
```

### (Optional) Provision the infrastructure from scratch
If you're setting up a brand-new environment, the repo includes scripts that
create the bastion VMs, firewall rules, and Internal Load Balancers:

```bash
cd scripts

# GLM5.3 + Qwen3.8 (requires compute admin in <MODEL_GCP_PROJECT>)
bash setup-model-bastion.sh glm53  <GLM_CLUSTER> <ZONE> <REGION> \
     shivaj-minimax-g4-384-cluster-net shivaj-minimax-g4-384-cluster-net-subnet \
     default <GLM_SERVICE_NAME> 30000 <GLM_ILB_IP> 30011

bash setup-model-bastion.sh qwen38 <QWEN_CLUSTER> <ZONE> <REGION> \
     <QWEN_VPC> <QWEN_SUBNET> \
     default <QWEN_SERVICE_NAME> 30000 <QWEN_ILB_IP> 30012
```

> **KimiK3 note:** the `<KIMI_CLUSTER>` cluster lives in `<KIMI_GCP_PROJECT>`,
> where most users have **GKE access but not compute access**. So KimiK3 uses a
> stable Internal LB reached via a supervised `kubectl port-forward` (no bastion
> VM). This is handled automatically by `connect-kimi.sh`.

---

## Daily use

Start **all** endpoints with one command:

```bash
cd scripts
./models.sh up
```

Check health of everything:
```bash
./models.sh status
```
Expected output:
```
Endpoint status:
  30100  KimiK3         UP
  30010  KimiK3(Puppy)  UP
  30011  GLM5.3-Flash   UP
  30012  Qwen3.8-Flash  UP
  8080   Browser-gw     UP
```

Stop everything:
```bash
./models.sh down
```

### What `models.sh up` does
- **KimiK3** → starts a supervised `kubectl port-forward` to the stable Internal
  LB (`svc/<KIMI_ILB_SERVICE>`), with auto-reconnect, on **both** `30100` and `30010`.
- **GLM / Qwen / Browser** → starts persistent `gcloud` IAP TCP tunnels to each
  bastion VM, which reverse-proxies to the model's Internal LB.

---

## Install & run the app

```bash
# from the repo root (kk3desktop/)
npm install

# development mode (hot reload)
npm run dev

# OR build a packaged .app
npm run build
```

---

## Connect the app to a model

1. Launch the app.
2. Open **Settings** (`Cmd + ,`) or click the endpoint badge in the top bar.
3. Set the **SGLang Endpoint** to one of the local endpoints, e.g.:
   - `http://localhost:30100`  → KimiK3
   - `http://localhost:30011`  → GLM5.3 Flash
   - `http://localhost:30012`  → Qwen3.8 Flash
4. The model dropdown auto-populates from that endpoint's `/v1/models`.
5. Start chatting.

> **Puppy chat UI** is hardcoded to `http://localhost:30010` (KimiK3). Don't
> change that port — `models.sh` keeps `30010` up for it.

---

## Endpoint reference

All model endpoints are OpenAI-compatible. Quick checks:

```bash
# list models on an endpoint
curl http://localhost:30100/v1/models

# chat completion (example: GLM)
curl http://localhost:30011/v1/chat/completions \
  -H 'content-type: application/json' \
  -d '{"model":"zai-org/GLM-5.3-Flash","messages":[{"role":"user","content":"hello"}]}'

# browser gateway health
curl http://localhost:8080/healthz
```

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `gcloud: ... python3.9 ... No such file` | broken `CLOUDSDK_PYTHON` | `export CLOUDSDK_PYTHON=/usr/local/bin/python3.13` |
| `models.sh status` shows a model `DOWN` | tunnel/forward died | `./models.sh up` (KimiK3 auto-reconnects; IAP tunnels restart on `up`) |
| `Permission denied` / `403` on IAP | missing IAP role or expired cert | run `gcert`; confirm `roles/iap.tunnelResourceAccessor` |
| `kubectl: current-context is not set` | wrong/missing context | re-run the `get-credentials` commands above |
| KimiK3 `DOWN` but others `UP` | the supervised forward died | `./connect-kimi.sh stop && ./connect-kimi.sh bg` |
| `port already in use` (30010/30100/...) | stale process | `lsof -tiTCP:<port> | xargs kill` then `./models.sh up` |
| App shows no models | endpoint not started / wrong URL | `./models.sh status`, then re-check the endpoint URL in Settings |
| Browser gateway `domain not in allow-list` | requested site not allow-listed | only the 31 allow-listed domains (or run gateway with `ALLOW_ALL=true`) |

### Logs
- KimiK3 forward: `tail -f /tmp/kimi-pf.log`
- GLM tunnel: `tail -f /tmp/iap-glm53.log`
- Qwen tunnel: `tail -f /tmp/iap-qwen38.log`
- Browser tunnel: `tail -f /tmp/iap-browser.log`

---

## FAQ

**Q: Do I need to keep a terminal open?**
KimiK3's forward is supervised (auto-reconnects in the background). The IAP
tunnels for GLM/Qwen/Browser are background processes too, but if your Mac
reboots, just run `./models.sh up` again.

**Q: Which model should I use?**
- **KimiK3** — best for very long documents (1M context).
- **GLM5.3 Flash** — fast, also 1M context.
- **Qwen3.8 Flash** — fast, 256K context, good general purpose.

**Q: Can I add another model?**
Yes. Use `scripts/setup-model-bastion.sh` with the new cluster/service details,
then add a line to `models.sh`.

**Q: Is my data leaving my machine?**
Prompts go from your Mac to the self-hosted models running on your team's GKE
clusters over an encrypted IAP tunnel. Nothing is sent to a third-party hosted
LLM API.

**Q: Why does KimiK3 use port-forward but the others use bastions?**
KimiK3's cluster is in `<KIMI_GCP_PROJECT>`, where users typically have GKE
access but not compute/VM access — so a bastion can't be created there. The
supervised port-forward to the stable Internal LB is the robust fallback.

---

## Repo layout (relevant files)
```
kk3desktop/
├── README.md                     # app features & dev commands
├── docs/SETUP_GUIDE.md           # ← this file
├── scripts/
│   ├── models.sh                 # start/stop/status for ALL endpoints
│   ├── connect-kimi.sh           # supervised KimiK3 forward (30100+30010)
│   ├── connect-browser-gateway.sh# browser gateway tunnel helper
│   ├── setup-model-bastion.sh    # one-time: bastion+ILB for a model
│   └── setup-kimi-bastion.sh     # one-time: KimiK3 (needs compute perms)
└── browser-gateway/k8s/          # ILB / bastion manifests
```
