---
name: create-hook
description: Add a command hook to DeepSeek Harness so a shell command runs at a session, prompt, tool, stop or subagent boundary. Use when the user asks to "add a hook", "run something before a tool", "block a dangerous command", "load context at session start", or wants event-driven automation around agent runs.
---

# Create Hook

Wire one command hook to the moment it should fire, then verify it against that moment.

## Step 1 — Scope

Interview with `ask_user_question`:

1. Which moment the hook fires at, and what it should do there.
2. Which scope it belongs to:
   - A project: `.agents/hooks/hooks.json` in the project root. Project hooks load when the market plugin's project-layout scan is enabled (`dsh-agent-plugins-market.scanProjectLayouts`); say so in the report when it is off.
   - A plugin: `com.deepseek.harness/hooks/hooks.json` inside the plugin directory.

| Event | Fires | What the hook can do |
| --- | --- | --- |
| `SessionStart` | a session starts | attach context the model sees in that session |
| `UserPromptSubmit` | a prompt arrives | block the prompt, or attach extra context |
| `PreToolUse` | before a tool runs | block the tool, or ask for approval first |
| `PostToolUse` | after a tool runs | block the result with feedback, or attach context |
| `Stop` | the run is about to stop | force another step with a reason |
| `SubagentStart` | a subagent starts | attach context to that running subagent |
| `SubagentStop` | a subagent ends | observe only |

## Step 2 — Write the hook command

The hook is a shell command; it runs in the project directory with the event payload on stdin as JSON — `session_id`, `cwd`, `hook_event_name`, plus the event's own fields (`tool_name` and `tool_input` for the tool events, `prompt` for `UserPromptSubmit`).

How the command answers:

- **Exit 0** — the action proceeds. Stdout may carry a JSON decision.
- **Exit 2** — the action is blocked, and stderr is the reason shown to the model. This is how a guard blocks a prompt or a tool call.
- **Any other exit** — logged as a failure, and the action proceeds.

Stdout JSON, for a decision instead of an exit code:

```json
{ "hookSpecificOutput": { "hookEventName": "PreToolUse", "permissionDecision": "ask", "permissionDecisionReason": "Writes outside the project" } }
```

- `permissionDecision` is `allow`, `deny` or `ask`; it is read for the event named in `hookEventName`.
- `additionalContext` carries text into the model's next request.
- `{"continue": false}` asks the run to stop.

Keep the script beside the hook file and reach it from the command: inside a plugin through `${PLUGIN_ROOT}`, in a project through a path relative to the project root. Keep the hook fast and free of interactive prompts — it runs inside the agent's turn.

## Step 3 — Write the declaration

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [{ "type": "command", "command": "node ${PLUGIN_ROOT}/hooks/guard.mjs", "timeout": 10 }]
      }
    ]
  }
}
```

- The document holds one `hooks` object keyed by event name; a project hook file may also carry the event table as the whole document.
- Each event holds an array of groups; a group is `{ "matcher": "...", "hooks": [...] }`.
- `matcher` is a regular expression read against the tool name (`PreToolUse`, `PostToolUse`), the session source (`SessionStart`) or the subagent kind (`SubagentStart`, `SubagentStop`). `UserPromptSubmit` and `Stop` take no matcher. An omitted or `*` matcher matches every subject.
- Every hook entry needs a non-empty `command`. `type` is `"command"`, `timeout` is in seconds, and `enabled: false` keeps the entry in the file without running it. Asynchronous hooks are not supported here.
- Groups and hooks run in file order, and identical event, matcher and command triples collapse into one.
- Placeholders in a command: `${PLUGIN_ROOT}` (the plugin's install directory) and `${PLUGIN_DATA}` (its persistent data directory) inside a plugin. Credential references are never written as literal values.

## Step 4 — Verify

1. The JSON parses; every event name is one of the seven above; every entry has a command.
2. Run the command by hand with a sample payload on stdin — `echo '{"hook_event_name":"PreToolUse","tool_name":"Bash","tool_input":{"command":"ls"}}' | <command>` — and confirm the exit code and the output the wiring depends on.
3. Confirm the file's scope: a project hook file sits in `.agents/hooks/`, a plugin hook file in the plugin's namespace directory.
4. Report the file path, the events wired, and how the user can watch the hook fire.
