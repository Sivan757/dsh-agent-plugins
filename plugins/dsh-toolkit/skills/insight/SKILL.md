---
name: insight
description: Analyze this machine's DeepSeek Harness session history and report how sessions actually go — where work gets stuck, which workflows repeat, what to try next. Use when the user asks "how have I been using this", "analyze my sessions", "make an insight report", or wants a retrospective.
---

# Insight

Analyze recorded sessions and produce a report the user can act on. The analysis is reading and counting; conclusions come from what the data shows, not from assumptions about how the work usually goes.

## Step 1 — Collect

Sessions are recorded under `$DSH_HOME/sessions/<encoded-project>/<session-id>/` as `session.jsonl.zstd` (and versioned variants). Each line is one event; `zstd -dc` decodes the stream.

1. List the project directories and their sessions, newest first by modification time.
2. Choose the window: default to the sessions from the last 30 days, or the count and range the user names. State the window in the report.
3. Record the choice explicitly: which projects, how many sessions, the date range, and anything skipped (unreadable files, sessions too short to be meaningful — a session with fewer than two user messages rarely says anything).

## Step 2 — Measure

For each session in the window, extract and count:

- The opening request, to classify what the session was about (project areas, task types).
- Tool calls by name, and how many failed.
- Files read versus files written, and the size of the diff the session produced.
- The number of times the user corrected course — a message that restates or reverses a prior instruction.
- Long gaps between the request and the first action, and stretches of repeated search without a conclusion.
- Whether the session ended with the work delivered, abandoned mid-task, or handed to a subagent.

Keep a per-session record; the aggregates come from it, so a claim in the report can be traced back to the sessions that support it.

## Step 3 — Read the hard cases

Look at the sessions the counts flagged: the ones with many corrections, repeated failed tool calls, or long stretches without progress. Read enough of those transcripts to say what actually happened. Counting finds the pattern; reading explains it.

## Step 4 — Report

Write the report to `$DSH_HOME/insights/<date>-insight.md` and summarize it in the reply. Sections:

- **At a glance** — the window, the session count, and the two or three findings that matter.
- **What works** — the workflows that ran clean, with the evidence.
- **Where it gets stuck** — for each friction pattern: what happened, how often, in which sessions, and the concrete cause visible in the transcripts.
- **Next** — a short list of changes to try, each tied to one of the friction patterns, each phrased as something the user can do in a session.

Every count in the report names the sessions behind it. Where the reading is an inference rather than a count, say so.
