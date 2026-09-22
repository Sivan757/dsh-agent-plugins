---
description: Verify that a code change actually does what it's supposed to by running the app and observing behavior
argument-hint: "[what to verify]"
---
Verify this change by running the application.

## User Request

$ARGUMENTS

Follow the verify skill exactly: establish the full diff range first, find the surface where the change is observable, get a handle, drive the changed code path, and capture the evidence. Do not run tests or typechecks as a substitute — that is CI's output. Report the verdict (PASS / FAIL / BLOCKED) inline with the steps and captures, and when in doubt, FAIL.
