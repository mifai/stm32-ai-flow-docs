# CubeMX New Project Onboarding

Use this flow when starting a new STM32 board/MCU project.

## Goal

- Keep `.ioc` as source of truth.
- Execute repeatable `validate -> cubemx(optional) -> build -> flash -> serial` runs.
- Preserve run evidence in `docs/`.
- Use central-vs-local topology intentionally:
  - central toolkit for generic workflow,
  - local repo for project-specific files.

See: `docs/guides/workflow-topology.md`

## Step 0: Choose Workflow Topology

Normal prompts:

- "Create a new STM32 project from the starter kit in central mode."
- "Create a new STM32 project from the starter kit in vendored mode."

## Step 1: Create and Save `.ioc`

1. Create board/MCU project in CubeMX.
2. Configure clocks, pins, peripherals, middleware.
3. In CubeMX `Project Manager`, choose `CMake` as the toolchain.
4. Save the `.ioc` in repo.

## Step 2: Initialize Workflow Config

Normal prompt:

- "Initialize the workflow config from this `.ioc` file and show me the fields I should review."

Review fields:
- `project_dir`, `build_dir`, `toolchain_file`
- `flash.port`, `flash.sn`, `flash.frequency`
- `serial.enabled`, `serial.port`, `serial.expected_regex`

If the workflow will own the build path, the `.ioc` should reflect that choice
through `ProjectManager.TargetToolchain=CMake`.

## Step 3: Smoke Test CubeMX CLI

Normal prompt:

- "Run a CubeMX regeneration smoke test for this `.ioc` and tell me whether the workflow can drive it cleanly."

## Step 4: Run Agent Workflow

Normal prompt:

- "Run the normal development workflow for this new project and summarize the result."

## Step 5: Runtime Visibility

Useful prompts:

- "Monitor the board's serial output for a minute."

## Daily Loop

1. Update `.ioc` and firmware.
2. Ask the agent to run the workflow.
3. Review artifacts and logs.
4. Commit `.ioc`, firmware, config, and docs updates.

Notes:
- A full up-front spec is optional; minimal requirements + incremental steps is valid.
- Keep project requirements in project repo; keep generic workflow logic in central toolkit.

If you need the exact wrapper commands behind these steps, use
`docs/reference/workflow-scripts.md`.


