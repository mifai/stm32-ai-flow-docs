# Workflow Topology

This workflow supports two valid ways to carry the workflow core.

## The Two Topologies

### `vendored`

- the workflow core is copied into each project repo
- project-specific code grows around that local workflow copy
- reusable workflow improvements can still be promoted later into a maintainer repo

![Vendored topology diagram](../assets/topology-vendored.svg)

### `central`

- the workflow core stays in one approved central toolkit repo or shared
  workflow repo
- project repos keep only a thin local layer plus wrappers that call the
  central toolkit
- project-specific needs stay local until they prove they are generic enough to
  promote into the central toolkit

![Central topology diagram](../assets/topology-central.svg)

## The Principal Difference

The main difference is where the workflow core is executed from and maintained.

- `vendored`: each repo owns its own workflow copy
- `central`: many repos use one shared workflow installation

## What Still Stays Local In Both

Regardless of topology, the project-specific layer still belongs in the project
repo:

- `.ioc` and generated firmware sources
- `stm32-workflow.local.json` or its project-specific equivalent
- project requirements, board notes, and local evidence
- continuity files such as `current-state.md` and `progress.md`

The topology choice changes where the reusable workflow core lives. It does not
mean the shared core should absorb every project-specific need.

## Which One Fits Better

Choose `vendored` when:

- workflow evolution is still project-driven
- you want the repo to remain self-contained
- you expect local experimentation in the workflow layer

Choose `central` when:

- the workflow core is already relatively stable
- you want several projects to share one toolkit
- you are willing to promote generic lessons deliberately instead of changing
  the shared core for every project need

## Project Creation

Vendored mode:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/create-new-project-from-template.ps1 `
  -ProjectName MyProject `
  -TargetRoot C:\tests `
  -WorkflowMode vendored
```

Central mode:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/create-new-project-from-template.ps1 `
  -ProjectName MyProject `
  -TargetRoot C:\tests `
  -WorkflowMode central `
  -CentralToolkitPath C:\path\to\approved-central-workflow-toolkit
```

## Daily Work In Central Mode

1. Work in the project repo as usual: `.ioc`, firmware, config, and local docs.
2. Run the local wrapper scripts.
3. Let those wrappers dispatch into the central toolkit automatically.
4. Keep project-specific behavior local unless it proves to be reusable.
5. Promote only the generic lessons back into the central toolkit.
