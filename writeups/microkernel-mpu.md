# MicroKernel-MPU: RTOS Bring-Up, MPU Sandbox, and VM32

*Personal project — STM32F446 + FreeRTOS MPU + VM32*  
*Active development*

## Summary
MicroKernel-MPU started as a bare-metal bring-up exercise on STM32F446, then grew into a small protected RTOS environment with an MPU sandbox and a custom 32-bit virtual machine. It’s my attempt to understand the full stack: clocks and UART all the way to a traceable instruction set.

## What I Built
- Bare-metal bring-up (clock/GPIO/UART) with FreeRTOS + MPU.
- Privileged kernel + unprivileged user task separation.
- VM32: dual-stack, no unconditional jump, memory-mapped IO, hot-load/patch support.
- UART CLI for load/run/step/dump/trace and breakpoint control.
- Toolchain scripts for build, flash, and VM tooling.

## Personal Notes
This project forced me to think about isolation and trust. Once you separate privileged and unprivileged tasks, every interface becomes a contract. I learned to treat that boundary as a design surface, not just a protection feature.

The VM32 design was a personal experiment. Removing unconditional jumps made control flow more disciplined and easier to trace, but it also meant I had to think carefully about how programs should be structured. I enjoyed how that constraint pushed me toward simpler, clearer demos.

Debugging the VM over UART taught me patience. When a single byte or stack op is wrong, everything collapses. I got better at writing small reproducible programs and building tools that expose state rather than guessing.

## Highlights
- VM32 supports trace, breakpoints, and hot patching over UART.
- Memory-mapped IO for UART TX/RX, LED control, and instruction counter pacing.
- Tooling includes assembler, disassembler, and tiny C subset.

## Hardware/Stack
- MCU: STM32F446
- OS: FreeRTOS + MPU port
- Tooling: CMake, custom scripts, UART CLI

## Repo Notes
Key documents live in `docs/` for VM32 design, errors, and debug behavior. The VM runtime is implemented in `src/vm32.c`, with public opcodes in `include/vm32.h`.
