# MicroKernel-MPU: System Behavior Generator on Protected RTOS

*Personal project - STM32F446 + FreeRTOS MPU + VM32*  
*Active development*

## Summary
MicroKernel-MPU started as a bring-up exercise, but it has evolved into a behavior lab for protected RTOS systems.

The core direction is simple:
- generate system behavior with scripted VM scenarios
- constrain execution with bounded control-flow checks
- explain every crash through one fault pipeline

## Current Status
Direction 1 and Direction 2 are integrated, and host automation is in place.

Implemented:
- `vm scenario <name>` with unified runtime entry `scenario_run(name)`
- `scenarios/` assets in both `.asm` and `.vm`
- scenario catalog with `mpu_overflow`, `irq_starvation`, `io_flood`
- `ScenarioResult` as the normalized scenario output contract
- unified `fault_dispatch()` for CPU and VM faults
- normalized `FaultRecord` snapshot and human + JSON outputs
- CLI: `fault last`, `fault dump`
- host tooling: `tools/vm32 --json`, `verify`, `batch`

## Why This Design
I no longer treat VM32 as a toy app runtime.
It is the system behavior script engine, and scenarios are the unit of operational intent.

That shift made the system easier to reason about:
- scenarios describe behavior, not just opcodes
- faults become structured evidence, not ad-hoc panic text
- host-side verification and batch execution become testable contracts

## Runtime Architecture
- Privileged kernel + unprivileged user task (MPU isolation)
- VM32 interpreter with bounded profile support (`vm verify`, `vm runb`)
- scenario engine (`src/scenario.c`, `src/vm32_scenarios.c`)
- fault pipeline (`src/fault.c`, `src/interrupts.c`)
- UART CLI as operator control plane

## Fault Explainability
CPU and VM failures flow through one pipeline.

Snapshot coverage includes:
- CPU context (`PC/LR/SP/XPSR/CONTROL`)
- fault status (`CFSR/HFSR/MMFAR/BFAR`)
- privilege and MPU region mapping
- VM context (`vm_pc/opcode/error/ic`)

Operators can inspect faults via:
- `fault last`
- `fault dump`

Machine parsing is supported by JSON output on UART.

## Host Automation
`tools/vm32` now supports automation-friendly workflows:
- `--json` structured output
- `verify` for bounded CFG checks
- `batch` for repeatable command sequences

`batch` is used for CI-style smoke flows and preserves per-step stdout/stderr plus exit code.

## Practical Notes
- Boot now defaults to `log=off` so scenario and fault output remain clean.
- If needed, runtime logs can be re-enabled with `enable` and `mode all`.
- For board-side regression I run:
  - `vm scenario io_flood`
  - `vm scenario irq_starvation`
  - `fault last`
  - `fault dump`
  - `vm scenario mpu_overflow` (last, because fault handler halts)

## Hardware/Stack
- MCU: STM32F446
- OS: FreeRTOS MPU port
- Tooling: CMake, OpenOCD, UART CLI, Python host tools

## Repo Pointers
- Runtime CLI and boot flow: `src/main.c`
- VM runtime: `src/vm32.c`, `include/vm32.h`
- Scenario engine: `src/scenario.c`, `src/vm32_scenarios.c`, `scenarios/`
- Fault pipeline: `src/fault.c`, `src/interrupts.c`, `include/fault.h`
- Host tooling: `tools/vm32`, `tools/vm32_verify.py`
- Narrative/design docs: `docs/DEVELOPER_GUIDE.md`, `docs/PLATFORM_NARRATIVE.md`
