---
name: insight
description: Analyze this machine's DeepSeek Harness session history and report how sessions actually go — project areas, interaction style, what works, friction points, and what to try next. Use when the user asks "how have I been using this", "analyze my sessions", "make an insight report", or wants a retrospective.
---

# Insight

Produce a usage-insights report from recorded sessions. The pipeline has two stages: extract structured facets per session, then aggregate the facets into a narrative report. Facets make the aggregates traceable — a claim in the report can be traced back to the sessions that support it.

## Stage 1 — Collect sessions

Sessions are recorded under `$DSH_HOME/sessions/<encoded-project>/<session-id>/` as `session.jsonl.zstd` (and versioned variants); `zstd -dc` decodes the stream.

1. List the project directories and their sessions, newest first by modification time.
2. Choose the window: default to the sessions from the last 30 days, or the count and range the user names. State the window in the report.
3. Record the choice explicitly: which projects, how many sessions, the date range, and anything skipped (unreadable files, sessions too short to be meaningful — a session with fewer than two user messages rarely says anything).

## Stage 2 — Extract facets per session

For each session, render the transcript to a compact form: session id (short), date, project, duration; then `[User]: <first 500 chars>` for each user message, `[Assistant]: <first 300 chars>` for each assistant text, and `[Tool: <name>]` for each tool call. Long transcripts may be summarized per chunk instead — keep file names, error messages, and user feedback; 3–5 sentences per chunk.

Then extract facets with a subagent (`subagent_run`) per session, feeding it the rendered transcript and this extraction contract:

CRITICAL GUIDELINES:

1. **goal_categories**: Count ONLY what the USER explicitly asked for.
   - DO NOT count the agent's autonomous codebase exploration
   - DO NOT count work the agent decided to do on its own
   - ONLY count when user says "can you...", "please...", "I need...", "let's..."

2. **user_satisfaction_counts**: Base ONLY on explicit user signals.
   - "Yay!", "great!", "perfect!" → happy
   - "thanks", "looks good", "that works" → satisfied
   - "ok, now let's..." (continuing without complaint) → likely_satisfied
   - "that's not right", "try again" → dissatisfied
   - "this is broken", "I give up" → frustrated

3. **friction_counts**: Be specific about what went wrong.
   - misunderstood_request: the agent interpreted incorrectly
   - wrong_approach: Right goal, wrong solution method
   - buggy_code: Code didn't work correctly
   - user_rejected_action: User said no/stop to a tool call
   - excessive_changes: Over-engineered or changed too much

4. If very short or just warmup, use warmup_minimal for goal_category

RESPOND WITH ONLY A VALID JSON OBJECT matching this schema:
{
  "underlying_goal": "What the user fundamentally wanted to achieve",
  "goal_categories": {"category_name": count, ...},
  "outcome": "fully_achieved|mostly_achieved|partially_achieved|not_achieved|unclear_from_transcript",
  "user_satisfaction_counts": {"level": count, ...},
  "agent_helpfulness": "unhelpful|slightly_helpful|moderately_helpful|very_helpful|essential",
  "session_type": "single_task|multi_task|iterative_refinement|exploration|quick_question",
  "friction_counts": {"friction_type": count, ...},
  "friction_detail": "One sentence describing friction or empty",
  "primary_success": "none|fast_accurate_search|correct_code_edits|good_explanations|proactive_help|multi_file_changes|good_debugging",
  "brief_summary": "One sentence: what user wanted and whether they got it"
}

Save one JSON file per session under `$DSH_HOME/insights/facets/<session-id>.json` so later runs can reuse them instead of re-extracting.

## Stage 3 — Aggregate into the report

Aggregate the facet files across the window, then produce the narrative. For each dimension below, hand the aggregated data to a subagent and ask for its JSON, then read the JSON into the report:

- **Project areas** — the 4–5 areas the sessions cluster into, with session counts and a 2–3 sentence description of what was worked on and how.
- **Interaction style** — 2–3 paragraphs on HOW the user works (iterate quickly vs detailed upfront specs? interrupt often or let the agent run?), in second person, with specific examples, plus a one-sentence key pattern.
- **What works** — 3 impressive workflows with title and 2–3 sentence description, second person.
- **Friction** — 3 friction categories with 1–2 sentences each and 2 concrete examples, second person. Cross-check against `friction_counts`: counting finds the pattern, reading the flagged transcripts explains it.
- **Suggestions** — improvements tied to the friction patterns: project-instructions additions for instructions the user repeated across 2+ sessions (prime candidates — they shouldn't have to repeat themselves), features to try, and usage patterns with a copyable prompt each.

## Stage 4 — Write and deliver

Write the report to `$DSH_HOME/insights/<date>-insight.md` and summarize it in the reply. Sections:

- **At a glance** — the window, the session count, and the two or three findings that matter.
- **Project areas** and **Interaction style**.
- **What works** — the workflows that ran clean, with the evidence.
- **Where it gets stuck** — for each friction pattern: what happened, how often, in which sessions, and the concrete cause visible in the transcripts.
- **Next** — the suggestions, each phrased as something the user can do in a session.

Every count in the report names the sessions behind it. Where the reading is an inference rather than a count, say so.
