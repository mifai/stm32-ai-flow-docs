# Documentation Map

Start here by goal:

- new to the starter kit and want the main explanation:
  [User Manual](user/user-manual.md)
- want the shortest practical operating path:
  [User Handbook](user/handbook.md)
- want the fastest real first success on a board:
  [Simple Example](user/simple-example.md)
- already have an STM32 repo and want adoption guidance:
  [Migration Guide](guides/migrating-existing-cubeide-cubemx-projects.md)
- need to understand `.ioc`, CubeMX, and workflow boundaries:
  [Understanding `.ioc`, CubeMX, and Workflow Changes](user/understanding-ioc-and-cubemx.md)
- want to understand starter reuse and central versus vendored topology:
  [Reuse Workflow Kit](guides/reuse-workflow-kit.md)
  [Workflow Topology](guides/workflow-topology.md)
- need exact manual commands:
  [Workflow Scripts](reference/workflow-scripts.md)
- want to see what a larger real project can grow into:
  [Project Case Study](user/project-case-study.md)

Core reading paths:

- new project path:
  [User Manual](user/user-manual.md) ->
  [Simple Example](user/simple-example.md) ->
  [User Handbook](user/handbook.md)
- existing repo adoption path:
  [User Manual](user/user-manual.md) ->
  [Migration Guide](guides/migrating-existing-cubeide-cubemx-projects.md) ->
  [Workflow Scripts](reference/workflow-scripts.md)
- `.ioc` and generated-code ownership path:
  [Understanding `.ioc`, CubeMX, and Workflow Changes](user/understanding-ioc-and-cubemx.md) ->
  [CubeMX User Code Contract](reference/cubemx-user-code-contract.md)

Seeded downstream continuity lives only in
`templates/stm32-workflow-starter/docs/agent-state.json`,
`templates/stm32-workflow-starter/docs/agent-history.jsonl`, and in downstream
repos created from that template.

Documentation folders:

- [User Docs](user/README.md)
- [Agent Docs](agent/README.md)
- [Guides](guides/)
- [Reference](reference/)
- [IOC Profiles](ioc-profiles/README.md)
- [Modules](modules/README.md)
- [Artifacts](artifacts/README.md)
