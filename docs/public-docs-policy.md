# Public Docs Policy

This note defines how STM32 AI Flow exports a documentation-only public surface
from the maintainer workflow into the public docs repository.

The public docs repo is generated output.
The canonical user-facing documentation source is the working public starter
repo `stm32-ai-flow`.
The maintainer repo owns the export policy and publication flow.

## Purpose

- let readers understand the workflow
- publish onboarding and evaluation material safely
- keep the reusable starter implementation private while it is still being
  refined
- let the docs-only public repo act as the current public-facing introduction
  to `STM32 AI Flow`
- mirror the working starter's user-facing documentation instead of maintaining
  a heavily reduced alternate manual set

## What May Be Exported

- repository-level reuse and mark-use files such as `LICENSE`, `NOTICE`, and
  `TRADEMARKS.md`
- canonical user-facing Markdown guides from the working starter repo
- a public-facing README that may closely mirror the working starter README
  while staying honest about the current docs-only publication boundary
- diagrams and image assets used by those guides
- HTML reading copies
- PDF reading copies

## What Must Stay Private

- `scripts/` implementation details not explicitly exported by allowlist
- `templates/`
- `skills/`
- `.maintainer/`
- workflow contract files such as `AGENTS.md`
- continuity docs such as `current-state.md`, `session-memory.md`, and
  `progress.md`
- maintainer and implementation references such as `workflow-scripts.md`,
  `workflow-capabilities.json`, `maintainer-guide.md`, and related internal
  policy or topology files

## Source Layout

- the public README source lives at `docs/public/README.md`
- canonical user-facing docs are exported from the working starter repo
- public-only variants should be the exception, not the normal model
- public README differences should be limited to what is needed to stay honest
  about the current publication boundary, not to create a separate product story

## Export Rules

- use an allowlist, not a blacklist
- copy only explicitly approved files and directories
- store the export manifest inside the generated public docs repo so reruns can
  remove previously managed files cleanly
- keep the export deterministic and reviewable
- keep repository-level reuse policy visible in the export by carrying the
  approved top-level license, notice, and trademark files alongside the docs
- keep public docs close to the working starter docs even when some referenced
  operational files are not yet published in the docs-only repo
- avoid making the public docs depend on files that are not exported; mentions
  of private surfaces should be explanatory, not required for navigation
