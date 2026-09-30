# Puppy Chat user guide

This guide is also available inside the app from Help → Documentation. It is bundled with Puppy Chat and can be read without a model connection.

## Getting started

Puppy Chat brings conversations, reusable skills, files, and saved outputs into one workspace.

1. Select New task beside the puppy chat logo. Describe your goal, the information to use, and the result you want.
2. Use Chat for questions and discussion. Choose Work in the composer when a task involves files or tools, then check the working folder shown beside it.
3. Add an attachment with the composer’s attachment control, or use Projects for reference material you will reuse.
4. Open Workspace to see Outputs, Context, and Activity. Activity shows what ran and whether a task needs attention.

Try: “Turn these notes into a launch checklist with owners, dates, and open questions.”

The model indicator in the top bar opens connection settings. Chat and tool-driven work need the configured services to be reachable. Saved conversations, Help, and locally saved outputs remain available when the model is offline.

## Reading and using answers

Answers support headings, nested lists, checklists, tables, code, and equations. Wide tables, code, and diagrams scroll within the answer; focus them with Tab and use the arrow keys when needed. Checklists in answers are read-only.

Use **Copy** beneath an answer to copy its Markdown. In a code block, **Copy** copies just that snippet, and **Open output** opens its preview and editing tools. **More code actions** contains Save to file, Refactor, and Write tests. Refactor and Write tests send a new request to the model. Open output and file actions become available when a streaming code block closes.

Source links are underlined. Images in replies load automatically as they approach the visible area; click an image to enlarge it. Previews occupy centered rows while the answer streams. Uploaded images and supported tool screenshots appear immediately. SVG drawings and Mermaid diagrams can also appear directly in the answer.

For a preferred writing style, include it in your prompt or in Workspace → Context → task instructions: for example, “Lead with the recommendation, use short paragraphs, and include a compact comparison table.” Presentation defaults do not override an explicitly requested format such as JSON.

## Long tasks and the task workspace

The desktop app automatically saves original tool-result text locally before shortening it for the model. This includes parallel reads and Workforce results. It also saves assistant text so earlier explanations can be retrieved. Open **Workspace → Context → Task workspace** to inspect sources, working notes, and storage use.

When a request becomes too large, Puppy Chat replaces saved result bodies with short source references. It keeps your user messages verbatim, including corrections, dates, and “do not” constraints, together with complete tool exchanges and their action receipts. The assistant can read or search the original evidence by ID without running the source tool again. If those protected instructions and receipts cannot fit a model, the task stops with an explanation; a smaller fallback is skipped. Use a larger context window or begin a new task with a focused brief.

The **Working notebook** organizes the objective, constraints, decisions, findings, completed work, open questions, and next steps. Notes include references to user messages or saved evidence. The assistant can update it during a task. Try: “Keep a notebook of verified findings and next steps. Preserve my date and venue constraints.” Later user messages take precedence over older notes. Notebook updates are versioned, and stale updates are rejected. This release does not make an additional summarization request after every tool call; preserving the conversation and compacting saved results happens in the app.

Under **Saved evidence**, select a source to read its full original text in pages. This also works while the model is offline. A branch receives independent copies of the evidence available at its branch point. Pausing, restarting, or falling back to another main model retains the workspace. Workers have separate notebooks; a workflow reviewer can read evidence from its assigned workers.

**Storage & retention** applies to local evidence files. Defaults are 8 MB per result, 128 MB per task, 1 GB total, and 30 days of inactivity. Limits are editable and there is also a 10,000-result ceiling per task. Expiry runs when the app starts or history is cleaned up; unfinished tasks and active readers are protected. Expired workspaces lose their evidence files and temporary notebook revisions, while conversation messages and action receipts remain. Orphaned files receive at least one day of grace before collection.

**Clear this task’s evidence** removes original cached results after a confirmation. Finish or end an unfinished task first. It keeps the conversation, notebook, outputs, permanent Memory, and action receipts. Missing evidence does not mean an action failed and must not cause a completed action to repeat. If storage fills up after a tool finishes, Puppy Chat saves the available receipt and pauses. Free space or adjust the limits, then Resume. An unknown action outcome still requires review in Activity.

The workspace is under the existing desktop profile’s `task-workspaces` folder, not a cloud drive or an OS temporary folder. On macOS this is normally `~/Library/Application Support/kk3-claude-desktop/task-workspaces`. Relevant excerpts are sent to your configured model when it uses them. Temporary files and notebook revisions are excluded from normal backups. Keep important deliverables in Outputs or saved files. Chats created before this feature, restored backups, and expired workspaces cannot recover original text that was never retained.

Preserving the requested year helps continuity; the model still needs to verify that organizer sources support the actual event dates.

## Quality and evaluations

Open **Library → Quality** (or search for “Open Quality” in the command palette). **Recent tasks** shows saved execution outcomes without making extra model calls: successful receipts, tool errors, unknown outcomes, reused results, manual reviews, saved active time and attempts. These are parent-run records; inspect worker details in Activity. “Completed” describes execution. Answer quality remains **unreviewed**.

Use **Run checks** to try four repeatable synthetic tasks on your main model or an enabled Workforce connection. They test exact requirements, selecting the requested event year, retrieving an older saved result, and retrying a failed read-only lookup. The context case shares the app's compaction code. The event case uses sample records, so passing it does not verify live websites or real event dates.

1. Choose a model connection and cases, then select **Run checks**. This sends the selected case prompts to that endpoint. No run starts automatically.
2. Inspect each result's checks, answer and observed tool receipts. An execution error, timeout or stopped case is separate from a failed answer check. Missing metrics say unavailable; estimated tokens are labeled.
3. Choose **Use as baseline** on a finished report. Run the same cases on a different configured model and inspect improvements or regressions. Only identical case definitions compare. One latency measurement is not a speed guarantee.
4. Add a custom text case with a prompt and required phrases if useful. These are literal phrase checks, not judgments about meaning. Avoid private data in a case you intend to share.
5. **Export report** downloads JSON including prompts, answers and fixture receipts. Reports stay on this device until you export or delete them. They are separate from workspace backups and have no import feature yet.

Evaluations use a standard prompt and temporary fixture tools. They do not use your real chats, files, memory, connectors, Workforce delegation or fallback chain. Connection and reasoning settings are resolved as configured, with an evaluation-only answer allowance of at most 8,192 tokens plus the configured reasoning reserve. The report shows the total request cap. Your saved settings stay unchanged. No API keys, endpoint URLs, private system prompts or reasoning text are recorded in the report.

One suite runs at a time across app windows, with up to 12 cases, six tool rounds and three minutes per case, and fifteen minutes for the suite. **Stop checks** cancels the run. Closing this page keeps it active; closing the app stops it. Reopening Quality after a restart preserves finished cases and labels the abandoned run interrupted. Start a new run explicitly to try again.

Keep up to 50 reports and 24 custom cases. Export and delete older reports to make room; deleting a baseline also clears its selection. Temporary evidence is discarded after each case. Saved answers/receipts are bounded excerpts. Passing a check is evidence about that narrow test, not a guarantee that the model is correct or safe for every task.

## Fast and Deep modes

Use **Model mode** beside the attachment button to choose the connection for a task. **Deep** is the default and uses your saved main model for substantial research, complex reasoning and longer work. **Fast** uses a General, Research or Coding worker connection you enabled in **Settings → Workforce**. Try Fast for a short summary, extraction or first draft. The label shows the served model; actual speed depends on your deployment.

Fast preserves that connection's configured reasoning, output and context limits. Its reasoning controls live in Workforce settings. It uses your normal task instructions and available tools; selecting a Research connection does not restrict the conversation to research tools. Delegated planners, workers and reviewers keep their own role settings. Chat/Work and Fast/Deep are separate choices.

Fast allows up to 12 tool rounds per attempt and a final answer request. If it asks for more tools, progress is saved and the task stops for review. For substantial work, open **Workspace → Activity**, **Pause**, change to Deep in the composer, then **Resume**. Completed actions are retained. Saved pending actions may finish before the next model response. Unknown action outcomes still require review before resuming. You can also switch after an error; changing a mode does not restart the task by itself.

The choice stays with the conversation. Already queued follow-ups keep the mode selected when queued. Branches and backups preserve the choice, but model endpoints and keys are separate device settings. On another computer, configure the matching role or switch the restored conversation to Deep. A disabled or incomplete Fast connection shows a recoverable error instead of silently using your main model.

To send uploaded images through Fast, enable **This connection accepts image input in Fast mode** in its Workforce settings only when the served model supports it. This capability is not tested by the model probe. Large histories still need enough context space; switching to a small model cannot make protected requirements fit an undersized window. Connection fallbacks keep their existing rules, and replies identify the actual model used.

Open **Library → Quality → Model choices** to compare local measurements. Run all four built-in checks three times on each candidate and the main connection. The latest three matching trials must pass within 30 days; recent failures or incomplete trials prevent a recommendation. The comparison highlights a Fast option only when its median fixture time is at least 10% below Deep. Trials use the standard evaluation prompt and capped output allowance, so these figures do not establish real-task speed or overall ability. Rerun checks after deployment changes. Nothing changes your routing automatically.

On the same page, **Title and topic model** lets you move these small requests to a configured worker connection. This preference applies across the app. Leave **Follow the response model** to use the connection that answered. If a selected helper fails, local title and topic rules remain available. The main answer and delegated roles keep their own choices.

## Connection fallbacks

Open **Settings → Connection fallbacks** to keep chat and main task runs available when the primary model cannot respond.

1. Add connections in the order to try them. For example: keep KimiK3 as primary, then add GLM Flash and Qwen Flash. Enter each deployment's exact base URL and served model ID, or copy a configured Workforce connection.
2. Set each model's output limit, context window and supported input types. Tool-enabled tasks require a fallback that supports tool calling. Image conversations require image support. A fallback that cannot retain the current turn and its completed tool results is skipped.
3. Choose authentication explicitly. No primary API key is sent to a fallback unless you select **Use primary connection API key**. Separate keys and connection settings stay on this device and are not included in workspace backups.
4. Leave reasoning on **Server default** initially, then use **Test model**. The test checks model discovery and inference, including native tool calls when enabled; it never executes a tool. Image support is a declared capability and is not tested by this button.
5. Enable **automatic fallback**. Use the arrows to change priority, or disable an individual connection. Changes apply to the next run; a running task uses its saved settings snapshot.

Connection failures, temporary server errors, rate limits and an unavailable served model can trigger the next configured connection. The default wait for a response to start is 90 seconds; increase it for a large prompt or cold server. Reasoning and tool-call generation count as a started response. Authentication errors, invalid requests and malformed model output stop the task so they can be fixed.

Once an answer, reasoning or a tool call starts, a dropped connection leaves the partial work saved and requires **Resume**. Puppy Chat does not splice another model's response into it. A switch at a later model step keeps completed tool results; it does not restart the task's tools. The actual fallback model and reason appear above its response and remain in backups and Markdown exports.

A successful fallback handles the remaining steps of that run. The next new or resumed run tries the selected primary again: your main connection in Deep, or the selected worker connection in Fast. If every eligible connection fails, the task stops with an explanation and preserves progress. Fix the connections and select Resume. Dedicated Workforce roles continue to use their own connections; this fallback order applies to the main chat/task runner. In Fast, a fallback configured to use the main API key still uses the saved main connection's key. The primary URL and model are never overwritten or replaced by port scanning.

## Clarification questions

When an answer would change the work, Puppy Chat can show a question card just above the message box. It presents up to three questions, one at a time.

1. Choose an option or write your own answer. A Recommended badge is a suggestion; no option is selected for you.
2. Use Next and Back to review the questions. Answers are saved as drafts on this device as you edit them.
3. Select **Submit & continue** when every question is answered. Puppy Chat records your answers in the conversation and resumes the task using its saved progress.

You can minimize the card, switch tasks, or close the app and return later. The task stays marked **Needs answer** until you submit or select **End task**. Ending the task records that no answer was supplied and clears its queued follow-ups.

Messages entered in the normal composer while questions are waiting become follow-ups; they do not answer the card. They run after the current task finishes. Expand **Your answers** in the transcript to review a submitted response.

## Workforce models

Workforce delegates a focused task to another model. Main chat uses the connection chosen by its Fast/Deep mode. A general worker can handle routine reading or analysis; research and coding workers have their own tool sets. Larger coding and research workflows add a planner and a reviewer. Independent research can run up to two workers at once; dependent steps wait for their prerequisites. Coding and older saved plans remain sequential. Delegation adds overhead, so use a focused worker when a full workflow would be excessive.

### Configure your models

1. Open Settings → Workforce. **Global** follows your current main connection, model, credentials, and reasoning settings.
2. Enable **Custom** for a role and enter the base URL and exact served model ID from your SGLang server. For example, assign a faster GLM or Qwen deployment to a general or research worker. Model names are not assumed or installed by Puppy Chat.
3. Choose authentication explicitly. A different endpoint receives no main API key unless you select **Use main connection API key**. A separate key stays in this device's settings.
4. Set output and context limits to values supported by that deployment. Leave space in the context window for instructions, tool definitions, and returned evidence.
5. Start with **Server default** reasoning. Select **Reasoning effort** or **Thinking template flag** only if that endpoint supports the corresponding request fields.
6. Select **Test model**. It checks authenticated model discovery and makes one inference request. Planner/reviewer tests require JSON output; worker tests require a native tool call. The test tool is never executed. A successful probe checks connectivity and basic format support, not model quality on your tasks.

Use /owl to request delegated work. Try: "Use a general worker to summarize these project notes and list open questions." For coding, use Work mode and select the working folder first. Workers inherit selected project instructions, reference material and memory, and can use only the tools available to the parent task. Built-in file paths and command working directories are resolved inside the chosen folder; shell commands and symlinks still follow the existing tool permissions and are not an operating-system sandbox.

For broader research, try: "/owl Research upcoming events in my city this year. Investigate event dates and venue options independently, verify organizer sources, and show the full dates." The assistant still decides whether to delegate. Configure a reachable Flash model for Planner and Research worker to move those calls off Kimi; Global roles continue using your main model.

Independent searches, page fetches, and file reads requested together can run up to three at once in each task or worker. Writes, commands, and browser calls remain ordered. Pause and Stop wait for already-started reads to settle and save their results. A failed worker stops new research dispatch; completed siblings remain available on Resume.

The assistant receives today's date and time zone on each model round. For event planning, specify the location and desired dates. It is instructed to verify the event's own year and dates on organizer pages and to label unannounced schedules clearly. Web results show retrieval time and possible year mismatches, but retrieval time is not an event date. Historical requests keep the year you explicitly request.

### Inspect and recover work

Open Workspace → Activity → **Open agent diagnostics**. Expand a run to see its actual model, endpoint, status, ID, result and saved operations. The operation records contain the arguments sent and the results returned by tools. Individual tools may limit how much output they return.

Coding review needs a recorded successful standalone build/test command after the changes. Commands with pipes, chaining or output redirection do not count as automatic verification. Shell edits also need a recorded diff or file readback. The reviewer must cover every subtask and cite valid verification receipts. If the evidence exceeds the reviewer's context window, the workflow remains unverified; it is not silently shortened to obtain a pass. Automated review remains a model assessment, not a guarantee of correctness.

- **Reviewer offline or invalid response:** work is saved and the parent task pauses. After fixing the role settings, Resume allows the assistant to retry the saved workflow using its ID and original goal. Completed workers are reused. You can also end the paused run and ask: "Resume workflow [ID] with its original goal; retry review only."
- **Review found a problem:** ask for a focused repair using the recorded critique. Workers are not automatically repeated across the whole project.
- **Interrupted action:** expand Saved operations and inspect the actual file or process. Choose the verified outcome and record what you checked. "Confirmed not run" permits that action to execute when the saved worker resumes; "Confirmed complete" supplies your recorded result; "Do not repeat" skips it without assuming success. If the parent delegation also has an unknown outcome, review it in Activity after reviewing its children. Retrying the parent reconnects to its saved delegation.
- **Stopped or truncated response:** partial work stays visible. Completed operations remain saved. An output limit does not count as a successful worker response.

The trace is saved with the task and is deleted when that task is deleted. It is not included in exported backups. Existing traces from before worker checkpoints were introduced remain historical records; their actions cannot be safely resumed as new checkpointed workers.

Questions are available in interactive conversations. Background automations and delegated workers do not open question cards. Backups retain question history and drafts, but restore tasks as inactive history. Do not put passwords or API keys in answers.

## Skills

A skill is a saved set of instructions for a repeatable job. It guides the model; tools and connections determine which actions the model can perform.

### Run an existing skill

1. Open Library → Skills → Installed.
2. Open **Details & versions** to read its saved instructions and any guided steps. Some skills write files or edit code.
3. For work on a folder, choose Work and set the folder in the composer first. Give the assistant any necessary context, such as a research topic or repository path.
4. Select Run on an enabled skill. This sends its instructions to the current task. Watch progress and tool results in Workspace → Activity.

The power button enables or disables future submissions. **Run** captures the current saved version, including when it queues behind another task. A disabled or removed skill can no longer be submitted, but already submitted work keeps its snapshot. Use Stop or End task to stop that work. The slash menu in the composer offers a separate set of shortcuts and does not capture a library skill version.

### Edit, compare and restore versions

In **Details & versions**, open **Edit skill**, change the instructions and add a version note, then **Save new version**. Each save keeps the earlier version. Built-in and official catalog skills are read-only; use **Make a custom copy** to adapt one.

**Version history** lets you choose a saved version, inspect it, and compare it with the current version. **Restore as new version** copies the selected instructions into a new current version. It preserves history and does not undo files, messages or other actions from a prior run. If another window changed the skill while you were editing, saving is rejected and your draft remains. Make a copy to preserve that draft or reopen the skill to load the latest version.

Use **Task results** to see the latest 30 executions across versions, including their model, status, active time and finished steps. Responses show their skill version. **Workspace → Activity** and **Library → Quality → Recent tasks** expose the exact saved instructions and links to step results. Execution completion means a response was saved; inspect the answer and tool receipts to assess quality. Different tasks or models are not a controlled comparison.

### Improve a skill with evidence

Open a custom skill's **Details & versions → Improvements → New experiment**. This currently supports instruction changes in skills without guided steps; checkpoint-aware workflow evaluation is a later extension.

1. Describe a specific failure. Choose 2–4 cases, with at least one development case and one held-out case. Add relevant custom cases first in **Library → Quality → Checks**. Optionally select a completed failure report; only matching failed development results are copied.
2. **Freeze cases and create draft** saves the baseline and test definitions. Edit and save a candidate, or choose a configured model and **Request a proposal**. A proposal receives the baseline instructions, your observation, development cases and selected development failures. It does not receive held-out definitions or results, chat history, or private system settings. Avoid putting held-out answers in your observation or instructions.
3. Review and save the candidate, then **Compare**. Both versions run twice per case, using the same effective model configuration and alternating order. The comparison uses isolated synthetic tools, fixed graders and no connection fallback. The installed version stays active during the experiment.
4. Expand the paired results and read both answers. Promotion requires development improvement in both repeats, all held-out checks passing, no check regression, and complete results. Equal scores, errors, timeouts and truncated answers do not qualify. Check the review box and select **Approve as new version** to install the candidate explicitly.
5. Review subsequent uses in **Task results**. The experiment counts uses and failed/interrupted executions among the skill's latest 30 runs; this does not grade answer quality. **Restore baseline as new version** preserves history and affects future runs. A newer independent edit blocks promotion or restore from overwriting it.

Limits are three proposal attempts per experiment, one comparison per experiment, three comparisons per baseline version, 30 saved reports, two repeats, three minutes per model task and 15 minutes per comparison. With four cases, comparison runs up to 16 model tasks, each with up to seven model rounds. Output is capped at 8,192 tokens plus bounded reasoning (at most 16,384 total per request); saved connection settings remain unchanged. **Stop experiment** cancels work in the window that started it. Closing a panel keeps it running; closing the app does not restart requests. Completed samples remain visible after interruption.

Held-out tasks are withheld from the proposer, but visible to you and synthetic rather than secret benchmark data. Already exposed tasks cannot be reused as held out for the same skill. Create fresh cases for another experiment. Literal checks and two repeats provide limited evidence; they do not certify semantic correctness or live tools. Nothing automatically edits app code, model weights, evaluators, permissions or installed skills.

**Library → Quality → Skill experiments** lists all saved experiments, including reports for deleted skills or skills that now have guided steps. **Export experiment** includes instructions, observations, case prompts, generated answers and fixture receipts. These reports are local, separate from workspace backups. Delete reports only after exporting anything you need. Deletion retains installed versions, attempt counts and held-out task history; it does not reset the limits.

### Build a guided workflow

In Create or Edit skill, add shared instructions and select **Add guided step**. Give each step a name and specific instructions. Add up to three checkpoint questions per step, with optional choices, one per line. If you omit custom questions, Puppy Chat asks whether you are ready to continue and lets you add constraints.

For example, use two steps: **Research organizer sources** with a city/year question, followed by **Prepare the event brief** with a review checkpoint. Keep instructions for later work in their own steps.

When you run the skill, a question card appears above the composer before step 1. Submit your answers to start that step. Its result is saved before the next checkpoint opens. The next step waits for another explicit submission. Checkpoint questions use the existing drafts, Back/Next controls and End task behavior; a model may also ask a clarification within a step. Ordinary composer messages remain queued follow-ups.

You can close the app and return to a waiting checkpoint, or pause within a step and Resume. Completed actions are retained; unknown outcomes still require review. Truncated responses and execution errors preserve the current step. Switching Fast/Deep at a checkpoint keeps the skill version and progress. Library edits or deletion do not change the submitted run. Checkpoint answers do not change tool permissions or replace existing approval requirements.

Guided steps are interactive and sequential. Existing parallel reads and research workers remain available within a step. This release does not automatically grade whether a step's answer meets its intended goal.

### Useful starting points

- **Research Brief:** provide a specific topic and audience. It asks for web research, citations, and a saved brief. Web access and file tools are needed.
- **TODO Digest:** choose a source folder. It collects TODO and FIXME comments into todos.md using search and file tools.
- **Weekly Report:** choose a Git repository. It summarizes the last seven days of commits and writes weekly-report.md. It needs command execution and file tools.
- **Code Review:** choose a Git repository. It inspects recent changes and may fix critical issues, then asks for build or typecheck verification. Read its instructions before running it.

### Create or import a skill

Select **Create**, enter a name, when to use it, and shared instructions. Optionally add guided steps, then select **Create skill**. **Import skill file** accepts SKILL.md or a version exported from Puppy Chat. Imports create independent copies; they do not overwrite an existing skill. A standard Markdown skill has a name and description in YAML front matter, followed by instructions:

```markdown
---
name: meeting-recap
description: Turn pasted meeting notes into decisions and next actions.
---
Read the notes in this conversation.
Summarize decisions, open questions, and actions with owners.
Mark unknown owners or dates as unspecified.
Return the recap in chat.
```

The **Browse** tab shows the bundled catalog and installed status. Skills do not install dependencies or grant account access. Existing skills migrate to version 1 on this device, preserving enabled/disabled choices and their original instructions.

**Export this version** downloads JSON containing its instructions and guided checkpoints. Import it on another device as a new custom skill at version 1. Export versions you want to keep before deleting a skill. The library and unused version history remain outside workspace backups; task backups contain the snapshots actually used by those tasks and restore them as inactive history, without installing or running a skill.

The library supports 200 skills and 40 versions per skill. A workflow supports eight steps, with 50,000 characters of shared instructions and 8,000 per step, subject to a total content limit. If a skill reaches its version limit, export it and make a custom copy to continue. Storage errors are shown instead of silently discarding a save.

## Outputs

Outputs are saved content attached to a task: code, Markdown, HTML, diagrams, charts, or supported interactive components.

1. Open Workspace → Outputs, or select Open output on a chat code snippet.
2. Use Preview for rendered content and Code for the saved source. Plain code may have no visual preview.
3. Select Co-Edit to change the content. Preview shows your unsaved edits, with a banner identifying the draft.
4. Select Review to compare the draft with the saved version, then Save as new version.
5. Use the version selector to inspect earlier versions. Restoring an earlier version creates a new revision and keeps the history.

Revision notes can record questions and review comments. Copy and Download export the saved version, so save edits before exporting them.

React previews support React and React DOM imports, JSX, and TypeScript component syntax; arbitrary npm packages are not included. React and Python previews load their runtimes from public CDNs. If a runtime cannot load, the preview displays an error. Markdown previews and editing do not need those runtimes.

## Workspace files

Open the top bar’s ··· menu → Workspace files, or choose Browse workspace files in Outputs.

1. Check the folder path. It starts from your configured working folder. Enter another path and select Go, or use Choose folder in the desktop app.
2. Open a folder by selecting its row. Use Back, Up one level, or the path breadcrumbs to return.
3. Search filters names in the current folder. It does not search file contents or subfolders.
4. Select a text file to read it. The preview shows a read-only copy; selecting a file does not send its contents to the model.
5. Choose Ask about file to send a question with the first 8,000 characters to the current task. Choose Copy to Outputs to save a separate, editable snapshot in that task.

Editing the copy in Outputs does not overwrite the original file. Use the code editor for direct file editing. New file creates an empty text file in the folder currently shown; an existing name is rejected.

If a folder cannot be opened, the error appears above the browser. Check the path or choose a reachable folder. File browsing uses the app’s filesystem tools and can work without model inference when those tools are available.

## Projects and context

Projects group related tasks with reusable instructions and reference files.

1. Open Projects and create or select a project.
2. Add relevant reference files and project instructions.
3. Start a task in that project. Use Workspace → Context to inspect the task instructions, reference selection, memories, and available tool groups.

A reference file is stored context for the model. Workspace files are files reached through filesystem tools. An output is saved content created or copied into a task. These are separate copies and are not automatically synchronized.

Use Search workspace to find saved conversations, outputs, and project references. Search can take you back to a matching message or open the relevant result.

## Memory and branching

### Memory

Open Library → Memory to search, add, edit, or delete saved facts. Personal facts can apply across tasks; project facts stay with their project. Original source links are shown when available.

The “Remember explicit preferences automatically” setting controls learning new preferences. Turning it off keeps existing memories available. Review what is included for a particular task in Workspace → Context.

### Branching

Use the branch icon on a completed reply to copy the conversation through that reply into a new task. Use ··· → Branch this task to branch the full saved conversation. The new task can follow a different direction while preserving the original task.

Finish or end an active run before branching. The branch opens at the selected point so you can continue from there.

## Automations and recovery

Automations run a saved prompt on a schedule. Open Automations to set up and manage scheduled tasks, including the working folder where applicable.

The app must be running for its local scheduler to check due tasks. A saved schedule is not an always-on cloud service.

Task control gathers running, saved, and interrupted work. Open a task’s Activity to inspect completed tool results and unresolved operations. If an action may have completed before an interruption, review the outcome before retrying it. Follow-ups entered during a run are queued for that task.

## Library tools

Library gathers reusable capabilities in one place. Every Library section has Back to Library at the top; Close returns to your main workspace.

- **Skills:** reusable instructions, including your own workflows.
- **Connectors:** configure tool services and inspect their connection state. Each connector lists its required setup. Adding a connector may require a local runtime, account credentials, or a reachable service.
- **Memory:** manage saved personal and project facts.
- **Characters:** start a new conversation using a selected persona.
- **Shopping:** save product links and mark items as purchased.
- **Paint:** draw on the canvas; Sketch to Shop can try to identify a drawing and find products using its configured services.

A listed capability may still need setup. Opening its page does not establish an external connection or install its dependencies.

## Images and visual previews

Attach an image using the paperclip. Its thumbnail appears beside your draft and in the sent message. Click a thumbnail or a tool image to open a larger viewer; press **Escape** or **Close image** to return to the conversation. Image uploads and previews support PNG, JPEG, WebP, GIF and AVIF, up to 8 MB. Existing SVG drawing outputs still use their separate renderer.

Images linked in answers load automatically as you scroll them into view. Puppy Chat retrieves public HTTPS images without cookies, a referrer, or your model credentials. Downloads are queued and shared across streaming updates, with a bounded memory cache; reopening the app loads them again. Each preview occupies a centered row with space reserved during loading. **Open original** opens the source in your browser. A failed preview offers **Retry image**. Private network URLs, nonstandard ports, unsupported formats, excessive redirects and oversized downloads cannot load as web previews.

Images returned as MCP image blocks appear alongside the tool's work, including Browser Sandbox screenshots when that service is available. Up to four images, totaling 8 MB, are retained per tool result. These images are saved with the conversation and included in branches and backups; their base64 data does not consume the model's text context. Displaying an image does not mean the model has analyzed it. Image understanding for an uploaded picture requires a vision-capable model endpoint.

## Instacart groceries and recipes

Create shoppable grocery lists and recipe pages from a conversation. Instacart then handles store selection, product matching and checkout.

1. In the **desktop app**, open **Library → Connectors → Instacart → Connect**.
2. Get an [Instacart Developer Platform API key](https://docs.instacart.com/developer_platform_api/get_started/api-keys). Choose **Development** for initial testing, or **Production** when Instacart has approved your production access.
3. Enter a key for that environment and select **Enable Connector**. Puppy Chat performs the MCP handshake and discovers the available recipe and shopping-list tools. A connected status includes the number of tools. An authorization or connection failure displays an error.
4. Ask, for example: “Create an Instacart grocery list for three vegetarian dinners for two people. Ask me about any missing preferences.”
5. After a successful tool result, follow **Continue on Instacart**. Choose your store and check the matched items, quantities, substitutions, prices and delivery options before completing checkout.

These tools create pages, not paid orders. Puppy Chat cannot confirm final prices, inventory, delivery slots or order status through this integration. Page creation sends the requested grocery/recipe information to Instacart. Existing successful links remain in the conversation; use them again rather than creating duplicate lists.

Use **Settings / reconnect** on the connector card to retry a connection or replace a key. A saved key stays hidden; leave it unchanged to reuse it. Switching environments requires entering the matching key again. **Disable** disconnects Instacart and removes its saved configuration; it does not delete previously created Instacart pages.

Keys are encrypted in the local desktop profile using the operating system's credential protection. They are not included in workspace backups or sent to your language model. The system keychain must be available; Puppy Chat does not fall back to storing this key in plain text. Re-enter the key on a new computer. Browser-only development preview does not configure live Instacart credentials.

Reference: [Instacart MCP documentation](https://docs.instacart.com/developer_platform_api/guide/tutorials/mcp/).

## Backup and restore

Open Settings → Backup & restore and select Download backup. The JSON file contains saved projects, conversations, attachments, output revisions and notes, execution history, and memories.

Connection settings, automations, agent traces, skills, unsaved drafts, and original workspace files are not included. Save those separately when moving to another machine.

To restore, choose a Puppy Chat backup file and inspect the preview. Restored tasks and projects are separate copies labeled “(restored)”; existing work is preserved. Restored execution history does not restart pending actions. The same backup is not imported twice.

The file contains readable saved content. Store it somewhere appropriate for the information it contains.

## Shortcuts and troubleshooting

### Keyboard shortcuts

Use Command on macOS or Control on Windows/Linux.

- **Command/Control + K:** search workspace and navigation commands.
- **Command/Control + N:** new task.
- **Command/Control + B:** toggle the sidebar.
- **Command/Control + comma:** settings.
- **Command/Control + Shift + O:** projects.
- **Command/Control + J:** connectors.
- **Command/Control + T:** automations.
- **F1:** Help and documentation.
- **Escape:** close the current dialog.

### When something does not work

- **No model response:** open the model indicator and check the endpoint and connection status. A self-hosted model must be reachable from the machine running Puppy Chat.
- **A skill cannot complete:** inspect Activity for the failed tool and check its connector setup. The skill’s instructions alone cannot supply a missing runtime or account connection.
- **A preview is blank or fails:** inspect Code to confirm the saved content, then read any preview error. React/Python runtimes need CDN access; unsupported package imports need to be removed or adapted.
- **Folder not found or permission denied:** enter another folder path in Workspace files. The app displays filesystem errors so you can correct the location.
- **Lost in Library:** use Back to Library at the top of the page. Close returns to your conversation.
