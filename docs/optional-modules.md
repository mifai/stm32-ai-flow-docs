# Optional Modules Registry

This starter kit is intentionally split into a thin core and opt-in extras.

The core workflow is ready to use today. The modules listed here are a
carefully curated early set of optional capabilities for common extensions,
with room for deliberate expansion as additional reusable patterns prove their
value.

Core workflow:

- validate machine and config
- run doctor preflight
- initialize from `.ioc`
- keep `.ioc` coherence
- build
- flash
- reconcile after manual CubeMX generation
- keep continuity and run evidence

Maintainer guardrails that stay with the starter kit:

- `scripts/stm32.ps1 lint`
- `scripts/stm32.ps1 starter-selftest`
- `scripts/stm32-starter-manifest.json`
- hardware-light CI

Why these stay core:

- they protect the seed from silent drift
- they do not force one product architecture on downstream repos
- they add less cost than the confusion they prevent

Why the modules below stay optional:

- they depend on board setup, debugging style, or project scope
- some repos benefit from them a lot, others do not need them at all
- making them mandatory would make the seed heavier and more restrictive

Choose an optional module when it removes repeated work for a real project.
Do not enable it just because it is available.
Do not read this registry as a promise that every STM32-specific need already
has a starter-kit module.

## Supported Modules Versus Other Guidance

This registry is only for supported optional modules.

That is not the same thing as every useful note the starter kit may carry.

- supported optional modules:
  reusable opt-in capabilities the starter kit already treats as part of its
  supported surface
- pattern notes:
  reusable guidance and tradeoff notes under `docs/modules/` that help with
  design choices but are not, by themselves, supported starter-kit modules
- example-project material:
  project-specific solutions that belong in downstream repos or case studies

This distinction is intentional. It lets the starter kit stay honest about
what is already supported, while still preserving useful engineering notes that
may later mature into better-defined optional modules.

## Active Modules

| Module | Purpose | When To Choose It | Docs |
|---|---|---|---|
| `cubemx-prebuild` | Regenerate project code from `.ioc` before build | Choose it when `.ioc` should drive each build automatically and the repo accepts regeneration in the loop. | `docs/guides/cubemx-new-project-onboarding.md` |
| `ioc-profile-automation` | Reusable `.ioc` feature rollouts via learned profiles | Choose it when you repeat the same peripheral or middleware patterns across repos or boards. | `docs/ioc-profiles/README.md` |
| `firmware-quality-gates` | Design-time quality gates for timer math, mode semantics, ISR safety, and integer overflow handling | Choose it when you want a stricter engineering checklist without making it mandatory for every seed consumer. | `docs/reference/firmware-quality-gates.md` |
| `serial-validation` | Validate runtime serial response in workflow | Choose it when the board exposes a stable serial contract worth gating. | `docs/reference/serial-troubleshooting.md` |
| `live-serial-cli` | Stream serial output to terminal and log | Choose it when bring-up and diagnosis benefit from continuous serial observation. | `docs/reference/workflow-scripts.md` |
| `openocd-gdb-debug` | Interactive halt/step/breakpoint debugging over ST-LINK | Choose it when logs and protocol checks are no longer enough to find the problem. | `docs/reference/workflow-scripts.md` |
| `freertos-cubemx-flow` | FreeRTOS projects managed via CubeMX generation | Choose it when the project really uses FreeRTOS and wants CubeMX to stay authoritative. | `docs/guides/cubemx-new-project-onboarding.md` |
| `existing-project-migration` | Adopt workflow in an existing CubeIDE/CubeMX repository in phases | Choose it when you need to introduce the workflow without a disruptive full migration. | `docs/guides/migrating-existing-cubeide-cubemx-projects.md` |

## What To Choose

Choose only the core when:

- you are starting a new repo and want the smallest stable path
- the board is still in early bring-up
- you want low documentation and maintenance overhead

Add optional modules when:

- they automate work you are already repeating
- they match a real transport, middleware, or debug need
- the extra config and docs burden is justified

## How To Add A New Optional Module

1. Add or update script/config support.
2. Add module usage notes in docs.
3. Add one row to this registry with `Module`, `Purpose`, `When To Choose It`, and `Docs`.
4. Keep module opt-in by default unless it belongs in the core workflow.
5. If the material is only a pattern note or design guide, keep it in
   `docs/modules/` instead of adding it here prematurely.


