# Development

Puppy Chat is an Electron + React + TypeScript desktop app. The public documentation source lives in this repository (`puppychat`); the application source is developed in the private `kk3desktop` repository.

## Documentation site

This site is built with [MkDocs](https://www.mkdocs.org/) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) and hosted on [Read the Docs](https://puppychat.readthedocs.io/).

### Build locally

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install mkdocs-material mkdocs-redirects mkdocs-minify-plugin
mkdocs serve     # live preview at http://127.0.0.1:8000
mkdocs build     # static site in site/
```

### Layout

```
puppychat/
├── mkdocs.yml            # site config, nav, theme
├── .readthedocs.yaml     # Read the Docs build definition
├── requirements.txt      # Python deps for the docs build
└── docs/
    ├── index.md          # landing page
    ├── usage-guide.md    # task-based usage guide
    ├── user-guide.md     # complete feature reference
    ├── setup.md          # installation & model connectivity
    ├── team-quick-start.md
    ├── team-admin.md
    ├── development.md    # this page
    ├── assets/           # puppy logo (SVG)
    └── stylesheets/      # theme overrides
```

### Editing

- Pages are plain Markdown; the navigation order lives in `mkdocs.yml` under `nav:`.
- Pushing to `main` triggers a Read the Docs build automatically; every pull request gets a preview build.
- Internal GCP identifiers (project IDs, cluster names, VPCs, ILB IPs) must never appear in these public docs — use the `<PLACEHOLDER>` convention already used in [setup.md](setup.md).

## Application source

The app repository (`kk3desktop`) uses:

```bash
npm ci            # install dependencies
npm run dev       # development mode (hot reload)
npm run build     # typecheck + build + package the macOS app
npm run dev:web   # browser-only preview (no Electron)
npm test          # offline regression suite (Node 22+)
npm run typecheck # tsc --noEmit
```

Key directories:

| Path | Contents |
|---|---|
| `src/` | React renderer (components, services, store, styles) |
| `electron/` | Main process (MCP, documents, team connections, task workspace) |
| `browser-gateway/` | Sandboxed browser gateway service (Docker/K8s) |
| `scripts/` | Model tunnel helpers (`models.sh`, `connect-kimi.sh`, …) |
| `test/` | Offline regression and live end-to-end tests |
| `build/` | Packaging resources: icons, team connection profiles |
| `docs/` | In-app documentation sources (mirrored into this site) |

## Branding notes

- Display name: **Puppy Chat**; internal package/profile name remains `kk3-claude-desktop`, so existing local data keeps its location.
- The editable puppy mark is `src/assets/puppy.svg`; app icons are derived into `public/` and `build/`.
- Palette: warm paper `#FAF9F5` on ink `#1F1E1D`.
