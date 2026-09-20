---
description: Set up a minimal AGENTS.md (and optionally skills and hooks) for this repo
argument-hint: [--local | --skills | --hooks]
---
Run the init-project skill's phases in order for this repository.

1. Phase 1 scope, adjusted by arguments: `--local` adds "Personal AGENTS.local.md" to the choice; `--skills` or `--hooks` preselect "Skills" / "Hooks" in the second question. With no arguments, ask both questions as written.
2. Phase 2: survey the repository (launch a subagent for the codebase survey).
3. Phase 3: interview me about everything the code cannot answer, then present the synthesized proposal and wait for acceptance.
4. Phases 4-7: write only the artifacts the accepted proposal and my Phase 1 choice cover.
5. Phase 8: recap what was written, then give the to-do list of further optimizations.

Apply every rule from the init-project skill: instruction lines only where removal would cause an agent mistake, commands recorded verbatim, deeper detail referenced rather than copied, and never silently overwrite an existing file.
