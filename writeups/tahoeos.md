# TahoeOS: Smart Watch Firmware Bring-Up

*Personal project — STM32F411, FreeRTOS, LVGL*  
*Ongoing*

## Summary
TahoeOS is my custom smartwatch firmware built on STM32F411 with FreeRTOS and an LVGL-based UI. It started as a bring-up exercise, then grew into a small OS with touch UI, sensors, power management, and OTA firmware updates.

## What I Built
- FreeRTOS-based firmware with LVGL touch UI and gesture support.
- Sensor processing for motion, heart rate, and environment data.
- Power management features like wrist-lift wake and multi-stage sleep.
- Bootloader + app split with OTA upgrade support (IAP).

## Personal Notes
This project taught me to treat bring-up as a repeatable ritual, not a one-time hack. I wrote a checklist for power, reset, SWD, clock tree, and RTOS validation so I could always recover when something broke. That checklist saved me more than once when a new build or hardware tweak left the board silent.

LVGL forced me to think differently about embedded UI performance and memory. I learned how expensive redraws can be and how to budget CPU time around the scheduler. Power management was another surprise: the UI may look done, but the device isn't done until sleep and wake cycles are reliable.

I like how this project connects low-level work (bootloader, clocks, drivers) with a tangible user experience. It feels like a complete system rather than a single module.

## Hardware/Stack
- MCU: STM32F411CEU6
- OS: FreeRTOS
- GUI: LVGL v8.2
- Sensors: MPU6050 (IMU), EM7028 (heart rate)
- Display: 1.69" touch LCD

## Repo Notes
Bootloader and app are split across flash; bring-up steps cover power, SWD, clock setup, and minimal RTOS scheduling validation.
