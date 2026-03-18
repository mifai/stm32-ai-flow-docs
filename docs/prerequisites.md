# STM32 Workflow Prerequisites

This page defines prerequisites for the reusable STM32 agentic workflow.

## Summary

- Primary tested environment: Windows

- Auto-installable (when `winget` is available):
  - `cmake`
  - `ninja`
  - `arm-none-eabi-gcc` toolchain
  - optional `openocd`
- Manual install required from ST:
  - STM32CubeProgrammer (`STM32_Programmer_CLI`)
  - STM32CubeMX
- Project source prerequisites:
  - HAL/CMSIS sources available in your project (vendored or generated)
  - optional FreeRTOS sources when RTOS is enabled

Linux and macOS are possible targets for the core workflow, but the current
helper scripts still assume Windows-first installation and device discovery.

## How A Human Should Use This Page

In normal use, you do not need to drive prerequisite setup by hand.

Useful prompts are:

- "Check whether the STM32 workflow prerequisites are installed on this machine."
- "Install the missing workflow prerequisites and then verify the environment."
- "Check whether CubeMX and CubeProgrammer are visible to the workflow."

If you want the exact installer commands, use `docs/reference/workflow-scripts.md`.

## Manual ST Tool Installation

These ST tools still need manual installation:

1. STM32CubeProgrammer
   - ST page:
     `https://www.st.com/en/development-tools/stm32cubeprog.html`
   - command the workflow expects to find:
     `STM32_Programmer_CLI`
   - typical Windows path:
     `C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeProgrammer\bin\STM32_Programmer_CLI.exe`
2. STM32CubeMX
   - ST page:
     `https://www.st.com/en/development-tools/stm32cubemx.html`
   - typical Windows path:
     `C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeMX\STM32CubeMX.exe`

Paths can vary if you choose a custom install location, but these are the
default Windows locations the current workflow most naturally aligns with.

## Minimal Verification Checklist

After installation, ask the agent to verify the environment. A good result
should confirm:

- the Arm toolchain is visible
- `cmake` and `ninja` are visible
- STM32CubeProgrammer is visible to the workflow
- STM32CubeMX is visible to the workflow
- the repo can pass toolchain/config/doctor checks

Useful prompts:

- "Check whether the STM32 workflow prerequisites are installed on this machine."
- "Verify that STM32CubeMX and STM32CubeProgrammer are visible to the workflow."
- "Run the toolchain, config, and doctor checks and tell me what is still missing."

## Flash Hardware Expectation

- the default `flash` stage uses STM32CubeProgrammer
- that means a connected supported programming probe is required for flash
- in this workflow today, the expected default probe path is ST-LINK unless a
  project deliberately adds another flashing method
- another project may deliberately choose a different flashing method such as
  the STM32 built-in bootloader over DFU/UART/USB, but that is not the default
  flash path described by this repo's workflow docs

## HAL/CMSIS and FreeRTOS Notes

- Build requires matching headers/sources for the selected MCU family.
- If using CubeMX as source-of-truth, keep `cubemx.enabled=true` so generated code stays synchronized.
- For FreeRTOS projects, ensure generated middleware paths are part of the build system.

