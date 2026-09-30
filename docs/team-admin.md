# Team deployment and API-key setup

The team installer supports an in-app setup flow for internal teammates who already have Google Cloud access. This release targets macOS; other platforms need installer and CLI-launch validation.

## Configure the deployment

`build/team-connections.json` contains the tunnel targets and exact model IDs. `build/team-preset.template.json` contains chat, workforce, and fallback assignments. Keep ports and model IDs consistent between these files. The connection profile is packaged as a resource; recipients do not enter project IDs or Kubernetes resource names.

Kimi and DeepSeek use GKE port-forwarding. GLM, Qwen, and the browser gateway use IAP to existing bastions. The app runs the installed cloud tools directly, binds local listeners to 127.0.0.1, and uses per-endpoint kubeconfig files under its application profile. Selected account and project settings are supplied to those child processes. It does not change the user's default kubeconfig or global project. Google Cloud sign-in itself uses the normal local gcloud credential store.

Grant teammates the least access needed for the selected connections in both projects:

- IAP tunnel access to the designated bastions/ports, plus the Compute instance discovery permissions required by `gcloud start-iap-tunnel`.
- GKE cluster discovery/authentication and Kubernetes permission to read the relevant pods/services and create port-forwards in the required namespaces. A viewer role alone does not necessarily permit port-forwarding.

Ordinary recipients do not need permission to create clusters, bastions, firewall rules, or load balancers. Apply your organization's account/device requirements separately. The app cannot grant IAM access or start a stopped SGLang server.

References: [IAP TCP permissions](https://cloud.google.com/iap/docs/using-tcp-forwarding#permissions_details), [GKE client setup](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl).

## Give teammates API keys

Supported providers are Firecrawl, Jina, Finnhub, Twelve Data, and Google Maps. Teammates may enter their own keys or import team-supplied keys. Use **Download empty template** in the app, fill only the providers you intend to share, and distribute the completed file using your existing secure team channel. The empty template is also available in the app repository at `docs/puppy-api-keys.template.json`.

The import format is a version-1 JSON object with an `apiKeys` object containing `firecrawl`, `jina`, `finnhub`, `twelveData`, and/or `googleMaps`. Omit unavailable providers or leave their values blank. Unsupported fields, malformed JSON, multiline values, and oversized files are rejected before any change is saved.

The recipient reviews provider names and explicitly imports. Existing keys are preserved by default; the replacement checkbox changes only providers with nonempty values in the file. Credentials are stored locally using the OS-backed encryption already used for Google account and connector credentials. Renderer reads return presence flags rather than saved values. Existing plaintext provider-key settings migrate on load; a failed migration preserves the original file and reports the error instead of overwriting it.

The shared JSON file itself contains plaintext credentials. Deliver it through the secure channel and remove the recipient's downloaded copy after import according to team policy. The app does not export saved keys, include them in ordinary workspace backups, or put them into the installer. It does not test provider authorization during setup, and deleting a local key does not revoke it at the provider. Revoke/rotate shared keys through the provider when team access changes. Existing environment-variable credentials remain a separate fallback for tools.

## Validate the installer

Run `npm test`, `npm run build:app`, and `npm run prebuild`. Then build the DMG with `npm run dist:team` on the signing/release machine and test a clean user profile:

1. Missing CLI tools, installation, account selection, cancelled sign-in, and expired cloud credentials.
2. The exact least-privilege teammate account in both projects; both authorized and denied models.
3. Main-model inference, optional-model failure, and all workforce assignments.
4. App quit, reopening with automatic connection on/off, sleep/wake, and a lost network connection.
5. Existing external tunnels and occupied ports: they must remain untouched.
6. API-key entry, import, preservation/replacement, removal, restart, and a locked keychain.

The development-only `/test/team-setup.html` fixture exercises UI states without cloud commands, model requests, or real keys. Add `?case=missing`, `?case=save-error`, or `?theme=dark` for additional states. The backend regression suite uses mocked subprocesses/network and temporary profiles; it does not prove live IAM, server health, or signed-installer behavior.
