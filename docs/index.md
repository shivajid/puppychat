# Puppy Chat

**Happy to help.** A warm macOS assistant workspace connected to local or remote **SGLang** servers — with conversations, projects, tools, and interactive outputs.

![Puppy Chat](assets/puppy-chat.svg){: style="height:96px" }

Puppy Chat (internally `kk3desktop`) is a native macOS desktop app that connects to self-hosted, OpenAI-compatible model endpoints — KimiK3, GLM5.3, Qwen3.8 — plus a sandboxed browser gateway. Everything runs on infrastructure you control: prompts travel over encrypted tunnels to your own model servers, never to a third-party hosted LLM API.

## Highlights

- **Warm three-pane design** — a line-drawn puppy, paper color palette, and a tail wag when a task completes.
- **Artifacts Live Canvas** — sandboxed React/HTML previews, Mermaid diagrams, code inspector, version history.
- **Model Context Protocol (MCP)** — standard `claude_desktop_config.json`, local stdio and remote Streamable HTTP tool servers, autonomous multi-turn tool loops.
- **Workforce delegation** — hand focused subtasks to general, research, or coding worker models with a planner/reviewer workflow.
- **Deep Reasoning** — collapsible thinking accordion for DeepSeek-R1 / QwQ / thinking models.
- **Fast and Deep modes** — quick answers on a flash model, deep work on your main model, switchable per task.
- **Task control and recovery** — pause, resume, queue follow-ups, and recover interrupted runs with receipts.
- **Skills** — reusable instructions with versioning, guided checkpoints, and evidence-driven improvement experiments.
- **SGLang telemetry** — live model discovery, health checks, tokens-per-second and time-to-first-token.
- **Local persistence** — conversations and artifacts stay in an embedded local database.

## Where to start

| I want to… | Read |
|---|---|
| Learn what Puppy Chat can do and how to drive it | [Usage guide](usage-guide.md) |
| Find every setting, shortcut, and feature in detail | [User guide](user-guide.md) |
| Install the app and connect it to models | [Setup & installation](setup.md) |
| Set up Puppy Chat from the team installer | [Teammate quick start](team-quick-start.md) |
| Deploy and administer it for a team | [Team deployment (admin)](team-admin.md) |
| Build, test, or contribute to the code | [Development](development.md) |

## Documentation in the app

This documentation is bundled with Puppy Chat: open **Help → Documentation** or press **F1** inside the app to read it without a model connection.

## Links

- Source code: [github.com/shivajid/puppychat](https://github.com/shivajid/puppychat)
- Issues: [github.com/shivajid/puppychat/issues](https://github.com/shivajid/puppychat/issues)
