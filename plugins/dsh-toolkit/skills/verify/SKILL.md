---
name: verify
description: Verify that a change actually does what it should by running the application and observing behavior. Use when asked to "verify this change", "confirm the fix works", "test it manually", or before pushing local changes.
---

# Verify

Verification is runtime observation. Build the application, run it, drive it to where the changed code executes, and capture what you see. That capture is the evidence.

Do not substitute tests, typechecks, or reading the code for running it. Those were already covered by whatever gate the project runs; running them again proves the gate runs. The time goes to running the application. A function called through a hand-written script is a unit test in disguise: the real caller ends somewhere a user or another program can see, and that place is where the observation happens.

## Step 1 — Find the change

1. Establish the full range, not the last commit: `git log --oneline @{u}..` for the commit count, `git diff @{u}..` for the whole diff. In a pull-request context, read the pull request's diff too.
2. State the commit count in the report. A diff too large to read in one output is redirected to a file and read from there.
3. Read the stated claim (the pull request description, the issue, the user's request) alongside the diff. Where they disagree, that disagreement is a finding, and the diff is what actually changed.

## Step 2 — Find the surface

The surface is where a person or another program meets the change:

| The change reaches | Surface | Observation |
| --- | --- | --- |
| A command-line tool | the terminal | run the command, capture the output |
| A server or API | a socket | send the request, capture the response |
| A user interface | the screen | drive it and capture the frame |
| A library | its published entry point | call the exported surface, not an internal file |
| A prompt, skill, or agent definition | the agent | run the session and capture its behavior |
| A pipeline or scheduled job | the runner | dispatch it, read the run |

An internal function is not a surface. Something in the project calls it, and that caller ends at one of the rows above: follow it there and observe at that point.

When the change has no runtime surface at all (documentation, type declarations with no emit, build configuration with no behavioral effect), report **BLOCKED — no runtime surface**, with the reason. Do not fill the gap with tests.

## Step 3 — Get a handle

Look for existing knowledge before improvising: a project script that builds and launches the application, a contiguous setup guide in the README or the task runner, a documented development workflow. Where none exists, derive one from the manifests (package.json, Makefile, task files) and timebox it; a wall you cannot get past is **BLOCKED** with the exact point where it stopped.

## Step 4 — Drive it

Take the smallest path that makes the changed code execute: changed a flag, run with it; changed a handler, hit that route; changed error handling, trigger the error. Read the plan back before running it — when every step is build, typecheck, or run tests, the plan reaches no surface, and observation has to be replanned.

Once the claim checks out, keep going: break it (empty input, oversized input, interrupt mid-operation), combine it (new behavior with old behavior), and wander (what is adjacent, what looked off). The stated claim is what the author intended; the verification's job includes what they did not state.

## Step 5 — Capture

Captured output is evidence; memory is not. Something unexpected gets captured and noted, then judged as part of the change or part of the environment — never routed around. Shared process state (ports, lock files, terminals) is isolated per run.

## Report

```
## Verification: <one-line statement of what changed>

**Verdict:** PASS | FAIL | BLOCKED

**Claim:** <what the change is supposed to do, and any mismatch with the diff>

**Method:** <how the application was launched, and the handle used>

### Steps

1. <what was done to the running application> → <what it showed> — pass/fail
   <evidence: the application's own output>

**Sample:** <the one capture a reviewer should look at>

### Findings
<Claim mismatches, unrelated breakage, environment notes, nearby pre-existing bugs.>
```

- **PASS** — the application ran and the change did what it should at its surface.
- **FAIL** — it ran and did not, or it broke something else, or the claim and the diff disagree materially.
- **BLOCKED** — no state could be reached where the change is observable, or there is no runtime surface. This is not a verdict on the change.

There is no partial pass: "three of four passed" is FAIL until the fourth passes or is explained. When the output is ambiguous, FAIL and attach the raw capture rather than interpreting it.
