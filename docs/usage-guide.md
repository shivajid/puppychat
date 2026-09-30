# Puppy Chat usage guide

This guide walks through everyday workflows: your first task, working with answers, running long jobs, delegating to other models, and using the Library tools. For an exhaustive reference to every setting and behavior, see the [User guide](user-guide.md).

---

## 1. Start your first task

1. Select **New task** beside the puppy logo.
2. Describe your goal, the information to use, and the result you want.
3. Use **Chat** for questions and discussion. Choose **Work** when a task involves files or tools, then check the working folder shown beside it.
4. Add an attachment with the composer's attachment control, or put reusable material in a [Project](#8-organize-work-with-projects).
5. Open **Workspace** to watch **Outputs**, **Context**, and **Activity** while the task runs.

!!! tip "Try this"
    *"Turn these notes into a launch checklist with owners, dates, and open questions."*

The model indicator in the top bar opens connection settings. Chat and tool-driven work need the configured model services to be reachable; saved conversations, Help, and locally saved outputs remain available when the model is offline.

---

## 2. Read and reuse answers

Answers support headings, nested lists, checklists, tables, code, and equations. Wide tables and code scroll within the answer.

- **Copy** beneath an answer copies its Markdown; inside a code block it copies just that snippet.
- **Open output** on a code block opens the preview and editing tools in Workspace → Outputs.
- **More code actions** offers *Save to file*, *Refactor*, and *Write tests* (the last two send a new model request).
- Source links are underlined; click images to enlarge them.

For a preferred writing style, say so in the prompt or in **Workspace → Context → task instructions** — for example: *"Lead with the recommendation, use short paragraphs, and include a compact comparison table."*

---

## 3. Choose Fast or Deep mode

Use **Model mode** beside the attachment button:

| Mode | Best for | Connection used |
|---|---|---|
| **Deep** (default) | Research, complex reasoning, longer work | Your saved main model |
| **Fast** | Short summaries, extraction, first drafts | A General / Research / Coding worker connection you enabled in **Settings → Workforce** |

Fast allows up to 12 tool rounds per attempt; if it needs more, progress is saved and the task stops for review. For substantial work, open **Workspace → Activity**, **Pause**, switch to Deep, then **Resume** — completed actions are retained. The choice stays with the conversation.

Open **Library → Quality → Model choices** to compare measured Fast candidates against Deep before trusting the switch.

---

## 4. Run long tasks with confidence

Puppy Chat is built for multi-step work that outlives a single response.

### Queue and pause

- **Follow-ups** typed while a task runs are queued and start in order after completion.
- **Pause after step** finishes the current model response and started tool calls, saves everything, then stops.
- **Stop** cancels model requests and delegated work; completed external actions are preserved.
- **Task control** (sidebar or `Cmd/Ctrl + K`) lists active, queued, and attention-needing tasks across all conversations.

### Recovery after closing the app

Closing or reloading interrupts execution — Puppy Chat is a desktop app, not a background service. On reopening, nothing replays automatically; you decide per action:

- **Confirmed complete** — you observed the result; the action will not repeat.
- **Do not repeat** — skip it without assuming success.
- **Confirmed not run** — record how you verified; it becomes eligible to run again on Resume.

Every tool intent and result receipt is committed to the local database before and after execution, and partial model responses are checkpointed about once a second. See [Long tasks and the task workspace](user-guide.md#long-tasks-and-the-task-workspace) for the full model.

### Clarification questions

When the answer would change the work, a question card appears above the composer — up to three questions, one at a time. Choose an option or write your own; **Submit & continue** resumes the task with your answers. Nothing is auto-selected; the task waits until you submit or end it.

---

## 5. Delegate with Workforce

Workforce hands a focused subtask to another model.

1. Open **Settings → Workforce**. **Global** roles follow your main connection; enable **Custom** to assign a faster model (for example a GLM or Qwen deployment) to General, Research, Coding, Planner, or Reviewer roles.
2. Enter the endpoint's base URL and exact served model ID, choose authentication explicitly, and set output/context limits that deployment actually supports.
3. Select **Test model** to verify discovery and one inference request.
4. Request delegation with `/owl`: *"Use a general worker to summarize these project notes and list open questions."*

Independent research can run two workers at once; coding workflows run sequentially with a planner and a reviewer that must cite real build/test receipts before approving. Inspect everything in **Workspace → Activity → Open agent diagnostics**.

---

## 6. Keep context with the task workspace

For long tasks, Puppy Chat saves original tool results locally before shortening them for the model, plus a **working notebook** (objective, constraints, decisions, findings, next steps).

- Inspect sources and storage under **Workspace → Context → Task workspace**.
- Read any saved source in full under **Saved evidence** — even while offline.
- Try: *"Keep a notebook of verified findings and next steps. Preserve my date and venue constraints."*

Defaults: 8 MB per result, 128 MB per task, 1 GB total, 30 days of inactivity — all editable in **Storage & retention**. The workspace lives under the app profile's `task-workspaces` folder, not a cloud drive.

---

## 7. Automate with Skills

A skill is saved, versioned instructions for a repeatable job.

- **Run one:** Library → Skills → Installed → **Run**. Watch progress in Activity.
- **Create one:** Library → Skills → **Create**, or **Import skill file** (SKILL.md format).
- **Guided workflows:** add up to eight sequential steps, each with up to three checkpoint questions. The task pauses at each checkpoint until you submit.
- **Improve one:** open a custom skill's **Improvements → New experiment** to compare a candidate instruction set against the baseline on frozen test cases, with held-out checks, before promoting it.

Useful bundled starting points: **Research Brief**, **TODO Digest**, **Weekly Report**, **Code Review**.

---

## 8. Organize work with Projects

Projects group related tasks with reusable instructions and reference files.

1. Open **Projects** and create or select one.
2. Add reference files and project instructions.
3. Start tasks inside the project; **Workspace → Context** shows exactly what the model receives.

A *reference file* is stored context for the model; *workspace files* are reached through filesystem tools; an *output* is saved content in the task. They are separate copies and are not synchronized.

---

## 9. Browse files and save outputs

- **Workspace files** (top-bar ··· menu) browses a folder: read text files, **Ask about file** (sends the first 8,000 characters), or **Copy to Outputs**.
- **Outputs** holds saved code, Markdown, HTML, diagrams, and charts. Use **Preview** / **Code**, **Co-Edit** to change, **Review** to diff, then **Save as new version**. Every revision is kept.
- React and Python previews load runtimes from public CDNs; Markdown previews need nothing.

---

## 10. Use the Library tools

| Tool | What it does |
|---|---|
| **Skills** | Reusable instructions and guided workflows |
| **Connectors** | Configure tool services (MCP servers, Instacart, browser sandbox) |
| **Memory** | Saved personal and project facts the model can recall |
| **Characters** | Start conversations with a selected persona |
| **Shopping** | Save product links and mark items purchased |
| **Paint** | Draw on canvas; Sketch to Shop can find products from a drawing |

Memory can learn explicit preferences automatically (toggle in Library → Memory); review what's included per task in Workspace → Context.

---

## 11. Images and Instacart

**Images:** attach with the paperclip (PNG, JPEG, WebP, GIF, AVIF up to 8 MB). Images in answers load as you scroll; MCP tool screenshots appear inline. Image understanding requires a vision-capable endpoint.

**Instacart:** connect your Developer Platform key in **Library → Connectors → Instacart**, then ask *"Create an Instacart grocery list for three vegetarian dinners for two people."* Follow **Continue on Instacart** to review and check out. The tools create pages, not paid orders.

---

## 12. Connect to team models

Using the team DMG? The in-app **Connect to team models** flow installs missing CLI tools, signs you in to Google Cloud, opens the tunnels, and verifies each model. See the [Teammate quick start](team-quick-start.md) — setup takes a few minutes and needs no source checkout.

To point Puppy Chat at any OpenAI-compatible endpoint yourself, see [Setup → Connect the app to a model](setup.md#connect-the-app-to-a-model).

---

## 13. Back up and restore

**Settings → Backup & restore → Download backup** exports projects, conversations, attachments, output revisions, execution history, and memories as JSON. Connection settings, skills, and unsaved drafts are *not* included — save those separately. Restores create labeled copies; nothing is overwritten.

---

## 14. Keyboard shortcuts

| Shortcut | Action |
|---|---|
| `Cmd/Ctrl + K` | Search workspace and commands |
| `Cmd/Ctrl + N` | New task |
| `Cmd/Ctrl + B` | Toggle sidebar |
| `Cmd/Ctrl + ,` | Settings |
| `Cmd/Ctrl + Shift + O` | Projects |
| `Cmd/Ctrl + J` | Connectors |
| `Cmd/Ctrl + T` | Automations |
| `Option + Space` | Spotlight quick launcher |
| `F1` | Help and documentation |
| `Escape` | Close current dialog |

---

## 15. When something doesn't work

| Symptom | First thing to try |
|---|---|
| No model response | Open the model indicator; check endpoint and connection status |
| A skill can't complete | Check Activity for the failed tool; verify its connector setup |
| Blank or failed preview | Check **Code** for saved content; React/Python previews need CDN access |
| Folder not found | Re-enter the path in Workspace files |
| Lost in Library | Use **Back to Library**, or **Close** to return to the conversation |

More troubleshooting: [User guide → When something does not work](user-guide.md#when-something-does-not-work) and [Setup → Troubleshooting](setup.md#troubleshooting).
