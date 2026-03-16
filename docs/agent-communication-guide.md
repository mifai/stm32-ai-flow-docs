# Human-Agent Prompt Guide

This is a short guide for talking to the agent productively in this workflow.

The basic rule is simple:

- ask for outcomes, not random commands
- state the scope and constraints
- ask for honest reporting about what passed, failed, or was skipped

## Ask For Outcomes

Prefer:

- "Check whether the toolchain, workflow config, and board connection are healthy."
- "Build, flash, and validate the current firmware."
- "Regenerate from `.ioc`, reconcile, rebuild, and tell me what changed."

Less useful:

- "Run some scripts."
- "Do STM32 stuff."

## Give The Agent The Important Constraint

If something matters, say it up front.

Useful constraints:

- "Do not flash the board."
- "Keep this change minimal."
- "Use `.ioc` as the source of truth."
- "Explain first, do not change files yet."
- "Only fix the CI workflow."
- "Treat CubeIDE as debug-only."

## One Main Task Per Prompt

Good prompts usually have one primary goal.

Prefer:

- "Find why the build failed and fix it."
- "Add a short user-facing doc for this workflow step."
- "Check whether this repo is ready for a new session."

Avoid stacking many unrelated goals into one prompt unless they are part of the
same workflow loop.

## Ask For Honest Validation

When you want execution, ask for validation explicitly.

Good examples:

- "Build and tell me exactly what passed."
- "Flash and validate, and state clearly if serial validation was skipped."
- "Fix the issue and rerun the relevant checks."

This workflow is designed to report blocked or skipped steps honestly. Prompt
for that explicitly when it matters.

## Tell The Agent What Kind Of Answer You Want

Examples:

- "Give me the short answer only."
- "Explain the tradeoffs before changing anything."
- "Patch it directly and summarize the result."
- "Review this like a code review and list the main risks first."

## Good Prompt Patterns

Startup:

- "Check whether this machine, repo, and board are ready for the normal workflow."

Normal loop:

- "Run the normal development workflow and summarize the result."

CubeMX change:

- "I changed the `.ioc` in CubeMX. Reconcile the repo, rebuild, and tell me if anything drifted."

Targeted build work:

- "Make the smallest change needed to fix the build error, then rebuild."

Investigation:

- "The workflow passed locally but failed in GitHub Actions. Find the failing step and fix only that."

Explanation:

- "This part of the workflow is unclear to me. Explain how it works in this repo and what I should do next."

## When To Mention Repo-Specific Facts

Mention these when relevant:

- board connected or not connected
- whether flashing is allowed
- whether serial validation should run
- whether CubeMX GUI was used manually
- whether the task is starter-kit maintenance or project-specific work

These details help the agent avoid the wrong workflow path.

## A Good Default Style

If you are unsure how to phrase something, use this pattern:

1. goal
2. important constraint
3. expected validation or output

Example:

- "Fix the CI failure without changing firmware behavior, then rerun the relevant checks and summarize what changed."

## Final Rule

If the docs are unclear, ask the agent directly and make the question concrete.

Good example:

- "This workflow step is unclear to me. Explain it using this repo's current files and tell me the next action."
