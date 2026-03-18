# STM32 AI Flow

STM32 AI Flow is a starter kit for STM32 development teams who want
AI-assisted work to stay reproducible instead of ad hoc.

It helps teams use AI for STM32 work without letting build logic, CubeMX
ownership, and validation discipline dissolve into chat history.

This public repository is currently the main public STM32 AI Flow surface. It
publishes the user-facing documentation for the starter kit while the full
working starter repository is still being refined.

Instead of starting every project from scratch, STM32 AI Flow gives a new STM32
repo a clean operating model from day one:

- `.ioc` stays the source of truth for generated configuration
- build, flash, and validation steps stay reproducible
- workflow evidence lives in the repo instead of memory
- later sessions can restart from repo truth instead of reconstructing context

The core workflow is ready to use today. Beyond that core, STM32 AI Flow also
includes a carefully curated early set of optional capabilities for common
extensions, with room for deliberate expansion over time.

![STM32 AI Flow structure and workflow](docs/assets/readme-workflow-structure-preview.svg)

## Why Use It

This is most compelling when you want more than ad-hoc AI help.

What it adds on top of a generic chat-first approach:

- a reproducible `validate -> build -> flash -> verify` loop
- an agent-use rule that repo scripts and checks should be run by the agent
  directly when possible instead of being handed back to the user
- an explicit `.ioc`-first regeneration story instead of fragile memory
- continuity docs so the next session starts from repo truth, not guesswork
- a workflow surface that stays small enough to learn and inspect

## The Smallest Real First Win

If you want to know whether this is worth trying, the smallest realistic first
win is not a toy demo. It is an honest first bring-up path:

1. create a new STM32 project repo from the starter kit
2. save the board `.ioc`
3. initialize workflow config from that `.ioc`
4. run toolchain, config, and doctor checks once

That first pass already proves something important:

- the machine can run the workflow
- the repo has a coherent startup path
- `.ioc` can be made authoritative before the project gets messy

If you want exactly that smallest realistic bring-up, start with
`docs/simple-example.md`.

## What This Can Grow Into

The starter kit is intentionally generic, but it is not hypothetical.

The case study in `docs/project-case-study.md` shows this workflow growing into
a real STM32 stepper-control project with:

- interrupt-driven UART command handling and debug output
- stepper pulse generation and motion-control logic
- ADC plus DMA acquisition
- FreeRTOS task orchestration
- a USB binary control path beside the human UART CLI
- a C# desktop operator UI

That example matters because it shows the workflow is not just about
generating files. It can support a real firmware path from early bring-up to a
usable host tool.

## Best Fit

Best fit:

- STM32 projects expected to live longer than a quick experiment
- repos that use CubeMX and need `.ioc` plus generated code to stay coherent
- engineers who want AI help without giving up reproducibility and evidence

Less compelling:

- tiny disposable throwaway tests
- projects that do not want repo-native workflow structure at all
- teams that are not ready to keep `.ioc` authoritative when CubeMX is in use

## Choose Your Path

If you are evaluating STM32 AI Flow for the first time, start from the path
that matches your current goal:

- smallest realistic first bring-up on a real board:
  `docs/simple-example.md`
- shortest operator path once the model already makes sense:
  `docs/handbook.md`
- fuller explanation of how the starter kit works and how to adopt it:
  `docs/user-manual.md`
- understanding `.ioc`, CubeMX, and workflow-side configuration boundaries:
  `docs/understanding-ioc-and-cubemx.md`
- adopting the workflow in an existing STM32 repo:
  `docs/migrating-existing-cubeide-cubemx-projects.md`
- seeing what this can grow into beyond board bring-up:
  `docs/project-case-study.md`
- exact manual command mapping:
  `docs/workflow-scripts.md`

## What You Get

The starter kit gives you a practical baseline:

- a prompt-first workflow surface for normal use
- validation, build, flash, and optional serial workflow steps
- a doctor preflight that retries STM32/ST-LINK detection briefly and reports
  last-known hardware context when the current scan is empty
- a clear `.ioc`-first story for CubeMX-managed projects
- documentation that explains both how to use the workflow and how to extend it

## Optional Capabilities

The optional layer is meant to be useful without pretending to be a finished
catalog for every STM32 scenario.

What exists today is a curated early set of supported optional modules. Beyond
that, the starter kit also carries pattern notes and example-derived guidance
for topics that are useful but not yet part of the supported optional-module
surface.

That split matters:

- supported optional modules live in `docs/optional-modules.md`
- pattern notes live under `docs/modules/`
- project-specific logic belongs in downstream repos, not in the starter core

## Environment Compatibility

This starter kit was developed and tested mainly with VS Code and the Codex
extension. However, it is not tied to any specific editor, agent environment,
or exact model.

It is, however, Windows-first today.

It should work in other agent environments if they can:

- read repository files
- run PowerShell or equivalent shell commands
- edit files in the repo
- follow repo-native instructions

The current starter-kit implementation is tested under Windows and still makes
several Windows-specific assumptions:

- prerequisite installation uses `winget`
- tool discovery checks ST default install paths such as `C:\ST\...`
- board and serial discovery use Windows device queries
- many examples assume `COMx` naming
- styled PDF export currently uses Microsoft Edge

Linux and macOS are realistic targets for the build-oriented core, but they
are not yet first-class environments for the full workflow.

This implementation is designed for STM32 today, with STM32CubeMX and `.ioc`
as the current configuration source-of-truth path.

## Create A New Project From This Starter

When you have access to the working starter repo, the normal requests are:

- "Create a new STM32 project from this starter kit in central mode."
- "Create a new STM32 project from this starter kit in vendored mode."

Use `vendored` mode when you want the workflow files copied into the project
repo itself. Use `central` mode when you want projects to share one approved
central toolkit.

For the full step-by-step creation and bootstrap paths, use
`docs/user-manual.md` and `docs/reuse-workflow-kit.md`.

## Need Help Using It

If you are trying this workflow and want help using it or adapting it to a
real STM32 project, open a GitHub Discussion in this repository.

If you want early access to the full working starter kit while the public
surface is still documentation-first, ask there as well.

## Where To Read Next

Start with one of these depending on your goal:

- `docs/simple-example.md`
- `docs/handbook.md`
- `docs/user-manual.md`
- `docs/understanding-ioc-and-cubemx.md`
- `docs/migrating-existing-cubeide-cubemx-projects.md`
- `docs/project-case-study.md`
- `docs/workflow-safety.md`

Reading copies:

- `docs/pdf/user-manual.pdf`
- `docs/pdf/handbook.pdf`
- `docs/pdf/agent-communication-guide.pdf`
- `docs/pdf/simple-example.pdf`
- `docs/pdf/project-case-study.pdf`
- `docs/pdf/workflow-safety.pdf`
- `docs/html/user-manual.html`
- `docs/html/handbook.html`
- `docs/html/agent-communication-guide.html`
- `docs/html/simple-example.html`
- `docs/html/project-case-study.html`
- `docs/html/workflow-safety.html`
