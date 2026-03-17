# STM32 Workflow Scripts

This is the compact script index for the reusable STM32 starter kit.

Use the wrapper first:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 <command> [args...]
```

The individual scripts still exist and remain the low-level implementation
surface. Reach for them only when you need direct control.

## Primary Commands

- `toolchain`
- `config`
- `doctor`
- `agent`
- `workflow`
- `build`
- `lint`
- `role-check`
- `context-budget`
- `starter-selftest`
- `browser-diag`
- `flash`
- `cubemx`
- `reconcile`
- `init-config`
- `ioc-check`
- `ioc-learn`
- `ioc-apply`
- `hal-suggest`
- `hal-sync`
- `serial`
- `monitor`

## Common Usage

Validate tooling:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 toolchain
```

Validate workflow config:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 config -ConfigPath .\stm32-workflow.local.json
```

Validate repo-role boundaries:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 role-check -ExpectedRole public-starter
```

Run doctor:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 doctor
```

Run the default workflow:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 agent -ConfigPath .\stm32-workflow.local.json -Profile dev
```

Initialize config from `.ioc`:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 init-config -IocPath C:\path\to\project.ioc
```

Learn and apply reusable IOC profiles:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 ioc-learn `
  -BeforeIoc C:\path\to\before.ioc `
  -AfterIoc C:\path\to\after.ioc `
  -OutputPath docs/ioc-profiles/STM32F4/my-feature.json

powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 ioc-apply `
  -ProfilePath docs/ioc-profiles/STM32F4/my-feature.json `
  -IocPath C:\path\to\project.ioc
```

Build only:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 build `
  -ProjectDir firmware `
  -BuildDir build `
  -Config Debug
```

Flash only:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 flash `
  -Artifact C:\path\to\firmware.elf `
  -Port swd `
  -Reset
```

Optional module commands:

- `powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 cubemx ...`
- `powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 reconcile -ConfigPath .\stm32-workflow.local.json`
- `powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 hal-suggest -IocPath C:\path\to\project.ioc`
- `powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 monitor ...`
- `python scripts/serial-live-dashboard.py --port COMx --http-port 8765`
- `powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 context-budget -Profile startup`
- `powershell -NoProfile -ExecutionPolicy Bypass -File scripts/stm32.ps1 browser-diag`
- `powershell -NoProfile -ExecutionPolicy Bypass -File scripts/export-user-docs.ps1`

Notes:

- `scripts/stm32.ps1 doctor` now retries STM32/ST-LINK visibility a small
  number of times before warning and records the last known connected hardware
  snapshot in `docs/last-device-state.json` when a device is visible.
- `scripts/stm32.ps1 browser-diag` inspects live `msedge.exe`, `chrome.exe`,
  `chromium.exe`, and related `javaw.exe` ownership so you can quickly tell
  normal Edge usage from Edge PDF export automation and STM32CubeMX JxBrowser
  process trees.
- when the agent has shell access, these wrapper commands are meant to be run
  by the agent directly; they should not be pushed back onto the user unless
  the agent is blocked, a high-risk action needs confirmation, or intent is
  genuinely unclear.
- `scripts/stm32-ioc-consistency-check.ps1` now accepts valid CubeMX PLL source
  alias variants when the present keys agree, so one omitted metadata alias no
  longer produces a false-positive warning by itself.
- `scripts/stm32.ps1 reconcile` now summarizes changed generated files,
  restored workflow-owned USER CODE sections, and the current `.ioc` issue
  count so manual CubeMX recovery is easier to review quickly.
- `scripts/stm32.ps1 hal-suggest` now inspects `.ioc`-enabled peripherals
  against the repo-local HAL driver tree, `stm32*hal_conf.h`, and the CubeMX
  CMake driver list, then suggests exact `hal-sync` commands without mutating
  the repo.
- `reconcile` and the workflow pre-build path now surface the same HAL sync
  suggestions automatically so missing driver modules are easier to catch
  before build failures.
- `validation_policy` in workflow config now lets a project choose whether
  missing flash or serial targets should fail the run early or be skipped
  explicitly, with the gate decision recorded in workflow output.
- `scripts/stm32.ps1 agent -Profile ci` and `scripts/stm32.ps1 workflow -Profile ci`
  can now use the same config surface as local development while still making
  hardware-light gate behavior explicit in config instead of relying only on
  ad-hoc skip flags.
- the default `flash` command path uses `scripts/stm32-flash.ps1` through
  STM32CubeProgrammer, so a connected supported programming probe is required;
  in this workflow today that means ST-LINK unless a project adds another
  flashing backend on purpose
- an alternative project-specific flashing backend could target the MCU's
  built-in bootloader over DFU/UART/USB, but that is outside the default flash
  implementation in this repo
- `cubemx.post_generate_script` remains the supported way to run a project-local
  cleanup or reconciliation hook immediately after successful CubeMX generation.
- If code was generated manually in the CubeMX GUI, run
  `scripts/stm32.ps1 reconcile -ConfigPath .\stm32-workflow.local.json`
  before building so workflow-owned USER CODE blocks and the `.ioc` check are
  restored deterministically.
- Starter creation now follows `scripts/stm32-starter-manifest.json` so new
  repos get workflow assets by default instead of maintainer-repo state.
- `scripts/stm32.ps1 context-budget -Profile startup` reports an approximate
  token budget for the startup read set; use `-Profile all` when reviewing
  broader prompt-facing doc growth.
- `scripts/export-user-docs.ps1` refreshes the styled HTML/PDF copies of the
  starter-kit user manual, handbook, and agent communication guide, using the
  Edge DevTools print path so PDF bookmarks/outlines are generated when
  supported by the local Edge build.
- The same export set now also includes `docs/simple-example.md`, so the
  smallest first-run scenario is available in Markdown, HTML, and PDF.
- Docs-only public export now lives in the separate maintainer repo rather than
  this public starter repo.
- `scripts/install-stm32-prereqs.ps1` now exports winget-installed tool
  directories to `GITHUB_PATH` when it is running inside GitHub Actions and the
  installed command is not yet visible in `PATH`.
- Microsoft Edge is optional for the starter kit itself. If Edge is missing,
  `scripts/export-user-docs.ps1` still refreshes the HTML copies and simply
  skips PDF generation.
- `docs/workflow-capabilities.json` is the machine-readable summary of the
  starter-kit workflow surface, automation, and starter export contract.
- `docs/workflow-safety.md` is the short safe-usage guide for trusted inputs,
  prompt-injection awareness, and high-risk actions.
- `docs/maintainer-guide.md` is the maintainer-only reference for starter-kit
  updates and export policy.
- CI now runs from `.github/workflows/ci.yml` and currently covers example
  config validation, workflow-script linting, and starter packaging self-tests
  on `windows-latest`.
- Hosted CI now declares explicit least-privilege `contents: read`
  permissions.

Create new project:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/create-new-project-from-template.ps1 `
  -ProjectName my-stm32-project `
  -TargetRoot C:\path\to\repos `
  -WorkflowMode central `
  -CentralToolkitPath C:\path\to\stm32-agentic-template-maintainer
```
