# Public Docs Policy

This note defines how STM32 AI Flow exports a documentation-only public surface
from the private source repository.

The private starter repo remains the source of truth.
The public docs repo is generated output.

## Purpose

- let readers understand the workflow
- publish onboarding and evaluation material safely
- keep the reusable starter implementation private while it is still being
  refined

## What May Be Exported

- public-facing Markdown guides
- public-safe variants of documents whose private versions assume access to the
  starter implementation
- diagrams and image assets used by those guides
- PDF reading copies

## What Must Stay Private

- `scripts/`
- `templates/`
- `skills/`
- `.agent/`
- `.maintainer/`
- workflow contract files such as `AGENT.md` and `AGENTS.md`
- continuity docs such as `current-state.md`, `session-memory.md`, and
  `progress.md`
- maintainer and implementation references such as `workflow-scripts.md`,
  `workflow-capabilities.json`, `maintainer-guide.md`, and related internal
  policy or topology files

## Source Layout

- shared public-safe docs may export directly from `docs/`
- docs that need public-only wording live under `docs/public/`
- the public README source lives at `README.public.md`

## Export Rules

- use an allowlist, not a blacklist
- copy only explicitly approved files and directories
- store the export manifest inside the generated public docs repo so reruns can
  remove previously managed files cleanly
- keep the export deterministic and reviewable

## Current Limitation

PDF reading copies may lag behind the public-safe Markdown variants until the
PDF export path is pointed at the public-facing sources rather than the private
starter docs.
