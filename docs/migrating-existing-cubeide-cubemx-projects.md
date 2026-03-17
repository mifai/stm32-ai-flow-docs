# Migrate Existing STM32CubeIDE/CubeMX Projects

Use this guide to adopt the workflow in an existing STM32 project without breaking your current development flow.

## Goal

- Keep your existing project structure.
- Add reproducible CLI validation and reporting.
- Migrate in phases: non-invasive first, full integration later.

## Phase 1: Non-Invasive Adoption (Recommended First)

Keep your current CubeIDE build process. Use the workflow for validation, flashing, and optional serial checks.

1. Ask the agent to bootstrap the STM32 workflow files into the existing repo.

2. Ask the agent to generate workflow config from the existing `.ioc`.

3. Edit generated config:
- Set `artifact.path` to your CubeIDE-built `.elf` path.
- Keep `cubemx.enabled=true` if `.ioc` is authoritative.
- Keep `serial.enabled=false` until serial behavior is ready for validation.

4. Ask the agent to run the workflow without the build step first, then report
   what passed.

## Phase 2: Full Build Integration

Move build into workflow once project supports CMake/Ninja in a stable way.

1. Ensure the project is ready for the workflow build path:
- In CubeMX `Project Manager`, switch the toolchain to `CMake`.
- Valid `CMakeLists.txt` in `project_dir`.
- Set `toolchain_file` if cross-toolchain is not auto-detected.

2. Update config fields:
- `project_dir`
- `build_dir`
- `toolchain_file` (if needed)
- `artifact.path` (optional; leave empty to auto-detect newest `.elf/.hex/.bin` in build output)

3. Ask the agent to run the full workflow once CMake/Ninja build integration is
   ready.

## Hybrid Mode: CMake Build + CubeIDE Debug

If you want CubeIDE debug features without dual-build drift:

1. Keep CMake as the single source of truth for build outputs.
2. Open/import the repo in CubeIDE and configure debug to use CMake-built `.elf`.
3. Use CubeIDE for debugging and inspection only.
4. Avoid maintaining an additional native CubeIDE build configuration unless there is a specific reason.

## Recommended Configuration Pattern

```json
{
  "project_dir": "path/to/firmware-or-cmake-project",
  "build_dir": "build",
  "cubemx": {
    "enabled": true,
    "ioc_path": "path/to/project.ioc"
  },
  "artifact": {
    "path": "",
    "address": "0x08000000"
  },
  "serial": {
    "enabled": false,
    "port": ""
  }
}
```

## CubeMX/CubeIDE Safety Rules During Migration

1. Treat `.ioc` as the source of truth for pins, clocks, and middleware.
2. Keep custom logic in USER CODE blocks or separate application files.
3. When the workflow owns the build path, keep `ProjectManager.TargetToolchain=CMake`.
4. Re-run generation after `.ioc` changes before validating behavior.
5. If board defaults injected unrelated IP, ask the agent to prune the unused `.ioc` defaults.
6. Ask the agent to run the generated-user-code guard before DONE.
7. If `.ioc` update is blocked, stop before non-USER-block config edits and notify user.
8. Follow `docs/cubemx-user-code-contract.md`.
9. Enable optional modules one at a time:
- `cubemx-prebuild`
- `serial-validation`
- `live-serial-cli`
- `live-serial-dashboard`

## Validation Milestones

1. Phase 1 pass: `validate` and `flash` pass with existing artifact (`-SkipBuild`).
2. Phase 2 pass: `validate`, `build`, and `flash` pass from workflow.
3. Optional pass: `serial` pass when `serial.enabled=true`.

## Legacy `.ioc` Compatibility (Requires CubeMX < 6.5)

If CubeMX reports that your `.ioc` requires a version earlier than 6.5:

1. Check `.ioc` metadata:
- `MxCube.Version=...`
- `ProjectManager.TargetToolchain=...`

2. Install an older CubeMX version side-by-side (for example 6.4.x), then point workflow to it:

```json
"cubemx": {
  "enabled": true,
  "ioc_path": "path/to/project.ioc",
  "cubemx_exe": "C:/path/to/older/STM32CubeMX.exe"
}
```

3. If legacy CubeMX is not available yet, continue Phase 1 without CubeMX/build:
- set `cubemx.enabled=false`
- set `artifact.path` to an existing firmware artifact built by your current toolchain
- ask the agent to run validation and flashing against that existing artifact

For exact commands, use `docs/workflow-scripts.md`.

