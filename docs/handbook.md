# STM32 AI Flow Handbook

> WORKFLOW-OWNED: This handbook is part of the STM32 workflow layer. Edit it
> intentionally and keep it aligned with `AGENTS.md` and the workflow skill.

This is the operator guide for the starter kit.

Use this file when you want the shortest practical answer to:

- what should I read first
- what does a normal session look like
- what is core
- where do I look for evidence

If you want explanation, tradeoffs, and onboarding guidance, use
`docs/user-manual.md`.

If you want the short safety rules for trusted input, repo boundaries, and
high-risk actions, use `docs/workflow-safety.md`.

> **Use this file as the task view**
> Read this handbook when you want the shortest operational path.
> Read `docs/user-manual.md` when you need explanation, tradeoffs, or onboarding context.

## The Quickest First Win

If you want the fastest proof that this workflow is worth using:

1. check toolchain and board readiness
2. initialize config from `.ioc`
3. run the normal development workflow once
4. review the recorded evidence

That first pass is enough to prove whether the machine, repo, and workflow path
are coherent before you invest in product work.

If you want the smallest practical first bring-up, read
`docs/simple-example.md`.

If you want one concrete example of what that kind of workflow can later grow
into, read `docs/project-case-study.md`.

## Environment Assumption

This workflow is repo-native, not VS Code specific.
It is intended primarily for Windows today.

It works best in an agent environment that can:

- read repo files
- run shell commands
- edit files
- follow repo-local instructions

If those capabilities are missing, the docs still transfer, but the operational
workflow becomes partial.

Instruction compatibility note:

- `AGENTS.md` is the repo instruction contract
- `.github/copilot-instructions.md` is the GitHub/Copilot-specific shim

Practical note:

- build-oriented use can be ported to Linux or macOS
- full parity still needs the Windows-specific install, discovery, and export assumptions to be replaced
- the workflow is git-host neutral; `.github/workflows/ci.yml` is the provided CI example, not a GitHub lock-in

## Read These First

Keep the active context small:

- `README.md`
- `AGENTS.md`
- `docs/handbook.md`
- `docs/current-state.md`
- `docs/session-memory.md`
- `docs/progress.md`
- `skills/stm32-agent-mode/SKILL.md`

Everything else is reference material.

> **Do not load everything by default**
> The startup set is intentionally small. Open deeper docs only when the task actually needs them.

## Normal Session

The normal loop is:

1. Check environment and board readiness.
2. Initialize config from `.ioc` if needed.
3. Run the development workflow.
4. Review evidence and blocked steps.

Typical prompts:

- "Check whether the STM32 workflow prerequisites and board connection are ready."
- "Initialize workflow config from this `.ioc`."
- "Run the normal development workflow for this project."

Operator note:

- when the agent has shell access to this repo, it should run the relevant
  scripts and checks itself rather than asking the user to do that work
- ask the user only when the agent is blocked, a high-risk action needs
  confirmation, or the intended action is genuinely unclear

If you want a short guide for writing better prompts, use
`docs/agent-communication-guide.md`.

Before high-risk actions such as installs, bootstrap, cross-repo edits, or
pushes, review `docs/workflow-safety.md`.

If code was generated manually in CubeMX, insert this before the normal loop:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 reconcile -ConfigPath .\stm32-workflow.local.json
```

> **Important**
> Manual CubeMX generation is not the end of the workflow step. Reconcile before you build, flash, or claim the generated tree is aligned.

## What The Workflow Covers

Core user workflow:

- toolchain and config validation
- doctor preflight
- `.ioc` initialization and consistency checks
- build and flash
- reconcile after manual CubeMX generation
- continuity files and run evidence

Because this repo is itself the public starter kit, maintainers also keep
packaging safeguards such as lint, starter self-test, and hardware-light CI in
place. Treat those as repo-level protection for the kit, not as part of the
normal downstream user loop.

Optional modules:

- serial validation and live monitoring
- IOC profile automation
- debug tooling
- migration aids and other heavier helpers

Use the core by default. Add optional modules only when they remove real
repeated work in a downstream repo.

Practical note:

- `scripts/stm32.ps1 doctor` now does a short STM32/ST-LINK rescan when the
  first hardware probe is empty and reports last-known hardware context from
  `docs/last-device-state.json` or workflow run history when available
- `validation_policy` in workflow config now makes flash and serial gates
  explicit, so missing targets can either fail early with a clear message or
  skip cleanly for hardware-light runs
- `scripts/stm32.ps1 hal-suggest` now checks `.ioc`-enabled peripherals
  against the local HAL driver tree and suggests exact `hal-sync` commands
  after `reconcile` and before `build` when likely module gaps are found
- `scripts/stm32.ps1 browser-diag` now classifies live browser processes so
  it is easy to tell normal Edge usage from Edge headless PDF export and
  STM32CubeMX JxBrowser Chromium trees
- `scripts/stm32-ioc-consistency-check.ps1` now accepts valid CubeMX PLL source
  alias variants when the present keys agree, reducing false-positive clock warnings
- `scripts/stm32.ps1 reconcile` now reports what it actually restored versus
  what was already aligned, plus the current `.ioc` warning/error summary
- the default `flash` stage requires a connected supported programming probe
  visible to STM32CubeProgrammer; in this workflow today that means ST-LINK
  unless a project deliberately adds another flash path
- some projects may choose a different flashing backend such as the MCU's
  built-in bootloader over DFU/UART/USB, but that is a project-specific
  workflow decision rather than the default path here
- docs-only public export now belongs to the maintainer repo rather than this
  public starter surface

CubeIDE coexistence note:

- coexistence is supported
- the preferred hybrid mode is CMake/workflow for build outputs and CubeIDE for debug/inspection
- avoid running two separate build systems in parallel unless the project really needs that complexity

## `.ioc` Rule

If the project is CubeMX-managed, `.ioc` is the configuration source of truth.

That means:

- configuration changes start in `.ioc`
- generated code is regenerated rather than hand-edited outside USER CODE blocks
- project behavior stays in modules, not generated glue

If you need the full generated-file contract, use
`docs/cubemx-user-code-contract.md`.

## What Counts As Success

A clean result says which enabled stages passed:

- validation
- build
- flash
- serial validation

If a step was skipped or blocked, say that explicitly.

If a step was skipped because a validation gate chose `skip`, report that too.
That is a policy decision, not an invisible success.

If `reconcile` or `workflow` reports HAL sync suggestions, report those too.
They are advisory, not a passing build result.

> **Reporting rule**
> Do not compress "build passed" into "workflow passed" unless the other enabled stages also passed.

## Where To Look After A Run

Primary evidence:

- `docs/current-state.md`
- `docs/progress.md`

Workflow metadata:

- `docs/workflow-capabilities.json`

## When To Open Other Docs

Open these only when the task needs them:

- `docs/user-manual.md` for onboarding and decision guidance
- `docs/simple-example.md` for the smallest concrete first-run scenario
- `docs/project-case-study.md` for a larger real-project growth example
- `docs/optional-modules.md` for core-versus-optional choices
- `docs/workflow-topology.md` for vendored-versus-central layout
- `docs/workflow-scripts.md` for exact commands
- `docs/cubemx-new-project-onboarding.md` for new-project setup
- `docs/migrating-existing-cubeide-cubemx-projects.md` for adoption work
- `docs/maintainer-guide.md` for maintainer-only workflow operations
- `docs/decisions.md`, `docs/open-issues.md`, and `docs/board-notes.md` only when the task needs deeper context
