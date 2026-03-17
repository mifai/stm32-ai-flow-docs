# CubeMX Generated-Code Contract

Use this contract when a project keeps CubeMX/CubeIDE regeneration capability.

## Core Rule

In CubeMX-generated files, user logic that must survive regeneration should be placed only inside:

- `/* USER CODE BEGIN ... */`
- `/* USER CODE END ... */`

Edits outside USER blocks are considered non-persistent and may be overwritten by CubeMX.

## Configuration Sync Rule

If a change affects generated initialization/configuration code (pins, clocks, peripheral init, DMA/NVIC, middleware init):

1. Update the `.ioc` first (or apply an IOC profile that updates equivalent keys).
2. Regenerate code from `.ioc`.
3. Re-apply only USER-block logic if needed.

Do not keep configuration-only changes as manual source edits without matching `.ioc` updates.

## Blocker Rule (Mandatory Notification)

If the agent cannot reliably update `.ioc` (tooling blocked, unknown key mapping, profile failure, unsupported CubeMX path):

- Stop before applying non-USER-block config edits.
- Notify the user that `.ioc` sync is blocked.
- Ask for direction before proceeding.

## Recommended Structure

- Keep generated files as wrappers and HAL init shells.
- Keep custom behavior in non-generated modules (`app`, protocol, drivers, etc.).
- Keep generated files edited minimally, mainly through USER blocks.
- Use `scripts/stm32-generated-user-code-guard.ps1` to catch configured custom-pattern leaks outside USER blocks.

## Ownership Split For Parallel CubeMX Work

Use explicit ownership so CubeMX generation and workflow-driven edits can
happen in parallel without repeated merge fights:

- CubeMX owns generated declarations and thread creation in files such as `main.c`.
- The project owns task logic, protocol logic, and runtime behavior in
  non-generated modules.
- USER CODE blocks inside generated task functions should be thin trampolines
  into project-owned functions, not the place where the real logic lives.
- For ISR glue that must remain in generated files, keep the custom part inside
  a USER CODE block and keep it small.

In projects that use a workflow-owned reconcile script, treat those generated
USER CODE blocks as part of the project contract rather than ad hoc scratch
space.

## Manual CubeMX Generate Rule

If you press Generate inside CubeMX manually:

1. Save the `.ioc`.
2. Run `powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 reconcile -ConfigPath .\stm32-workflow.local.json`.
3. Then run the normal build or workflow command.

Do not hand-fix generated drift line by line when the reconcile script can
restore the agreed ownership boundary automatically.
