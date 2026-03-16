# STM32 AI Flow Handbook

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

If you want one concrete example of what that kind of workflow can later grow
into, read `docs/example-project-development-path.md`.

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

Practical note:

- build-oriented use can be ported to Linux or macOS
- full parity still needs the Windows-specific install, discovery, and export assumptions to be replaced
- the workflow is git-host neutral; `.github/workflows/ci.yml` is the provided CI example, not a GitHub lock-in

## Read These First

Keep the active context small:

- `README.md`
- `docs/handbook.md`
- `docs/user-manual.md`
- `docs/workflow-safety.md`
- `docs/agent-communication-guide.md`
- `docs/example-project-development-path.md`
- `docs/prerequisites.md`

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

> **Important**
> Manual CubeMX generation is not the end of the workflow step. In the full
> starter implementation, generated output should be reconciled before you
> build, flash, or claim the tree is aligned.

## What The Workflow Covers

Core user workflow:

- toolchain and config validation
- doctor preflight
- `.ioc` initialization and consistency checks
- build and flash
- reconcile after manual CubeMX generation
- evidence and review of what actually passed, failed, or was skipped

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

- `validation_policy` in workflow config now makes flash and serial gates
  explicit, so missing targets can either fail early with a clear message or
  skip cleanly for hardware-light runs
- the starter implementation includes checks for likely HAL module gaps before
  build and after manual CubeMX reconciliation
- the starter implementation also includes diagnostics that help distinguish
  normal browser usage from workflow-related headless browser activity during
  tasks such as document export or CubeMX probing
- the `.ioc` consistency checks accept valid CubeMX PLL source
  alias variants when the present keys agree, reducing false-positive clock warnings
- the reconcile step is reviewable and reports what it restored versus what
  was already aligned, plus the current `.ioc` warning or error summary
- the default `flash` stage requires a connected supported programming probe
  visible to STM32CubeProgrammer; in this workflow today that means ST-LINK
  unless a project deliberately adds another flash path
- some projects may choose a different flashing backend such as the MCU's
  built-in bootloader over DFU/UART/USB, but that is a project-specific
  workflow decision rather than the default path here

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

The private starter implementation also keeps a more detailed generated-file
ownership contract, but that internal reference is not part of this public docs
set.

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

In the full starter implementation, each run produces evidence about what
passed, what was skipped, and what was blocked. This public docs set focuses on
the workflow model and user experience rather than publishing the internal
continuity and evidence files themselves.

## When To Open Other Docs

Open these only when the task needs them:

- `docs/user-manual.md` for onboarding and decision guidance
- `docs/example-project-development-path.md` for a concrete example of how one STM32 project can grow with this workflow
- `docs/workflow-safety.md` for trusted-input boundaries and high-risk actions
- `docs/prerequisites.md` for tool and environment expectations
- `docs/agent-communication-guide.md` for better prompt patterns
- `docs/pdf/handbook.pdf` if you want a polished reading copy
