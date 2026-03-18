# Reuse STM32 Workflow Kit In Other Repositories

This starter kit can bootstrap the same workflow into another repository.

The reusable starter kit is workflow-only by default. Product firmware,
board-specific files, host applications, and project continuity logs are not
copied into a new repository unless you add them intentionally.

Before you ask the agent to create or bootstrap anything, make sure:

1. the starter-kit repo exists locally on your machine
2. the repo the agent should act in is open in your AI workspace
3. the agent can run PowerShell commands against local paths

Choose one distribution mode:
- `central` (recommended): thin project-local layer + wrappers to central toolkit.
- `vendored`: full local copy of workflow kit.

For a brand-new repository, the normal flow is:

1. Clone or download the starter-kit repo locally.
2. Open that starter-kit repo in your AI workspace.
3. Ask the agent to create a new STM32 project from the starter kit in
   `central` or `vendored` mode, into your chosen parent directory.
4. Open the new repo and continue target-specific setup there.

The normal requests are:

- "Create a new STM32 project from the starter kit in central mode."
- "Create a new STM32 project from the starter kit in vendored mode."

That creates the repo directory, copies the workflow starter assets declared in
`scripts/stm32-starter-manifest.json`, and initializes git in the new repo.
The new repo starts with its own history; it does not inherit the starter-kit
repo history.

## Existing Repo Bootstrap

For an existing repository, the normal flow is:

1. Keep the starter-kit repo available locally.
2. Open the existing target repo in your AI workspace.
3. Ask the agent to bootstrap the STM32 workflow kit into the current repo from
   that local starter-kit copy.
4. If the target repo already has its own `AGENTS.md`, merge the copied
   `stm32-workflow-agents-fragment.md` guidance intentionally instead of overwriting local
   policy blindly.

The normal request is:

- "Bootstrap the STM32 workflow kit into this existing repo."

Options:
- `-Overwrite`: replace existing copied files in target repo.
- `-IncludeFirmwareTemplate`: also copy the `firmware/` folder if present.
- `-WorkflowMode central|vendored`: choose thin-wrapper or full-copy installation.
- `-CentralToolkitPath`: path to an approved shared toolkit root (used in central mode).
- `-Json`: machine-readable summary.

## Post-Install Checklist

1. Merge `stm32-workflow-agents-fragment.md` into target `AGENTS.md`.
2. Confirm grouped docs are present:
   - `docs/README.md`
   - `docs/agent-state.json`
   - `docs/agent-history.jsonl`
3. Validate prerequisites in target repo.
4. Generate target config from `.ioc`.
5. Run minimal workflow and confirm artifacts.
6. Enable optional modules from `docs/reference/optional-modules.md` only as needed.
7. If target repo is an existing CubeIDE/CubeMX project, follow:
   `docs/guides/migrating-existing-cubeide-cubemx-projects.md`
8. If using central mode, pin and review `.stm32-agentic-central.json`.

## Update Strategy

1. Improve scripts/docs in the central toolkit repo.
2. In central mode projects, update central toolkit only (no mass copy).
3. In vendored mode projects, re-run bootstrap with `-Overwrite`.
4. Keep downstream board-specific configs and firmware local to each project.

For exact bootstrap and creation commands, use `docs/reference/workflow-scripts.md`.

For maintainer-only workflow update policy, use the separate maintainer repo rather than this public starter repo.


