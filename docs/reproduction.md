# Build and bench-test guide

## Environment

| Item | Identified value |
| --- | --- |
| Board | NUCLEO-L476RG |
| MCU | STM32L476RGT3 / STM32L476RG |
| System clock | 80 MHz |
| Recorded CubeMX version | 6.11.1 |
| Recorded STM32Cube package | STM32Cube FW_L4 V1.18.0 |
| Verified IDE | STM32CubeIDE 1.19.0 on Windows |
| Verified compiler | GNU Tools for STM32 13.3.rel1 |
| Imported project name | `projet_robot_rebuild` |

The historical project name is retained for compatibility with its IDE configuration. It was not newly reconstructed for this portfolio. Required HAL and CMSIS sources are included under `firmware/Drivers/`.

## Build in STM32CubeIDE

1. Create a workspace separate from the original archive.
2. Use **File → Import → General → Existing Projects into Workspace** and select `firmware/`.
3. Import the project named `projet_robot_rebuild`.
4. Select the `Debug` configuration and run **Project → Build Project**.
5. Review the build summary and check for `firmware/Debug/projet_robot_rebuild.elf`.

Do not regenerate the IOC as a prerequisite: the preserved source and generated settings are already present. Regeneration could change the snapshot.

## Headless build on Windows

Run from the repository root, adjusting the installation path if necessary:

```powershell
& 'C:\ST\STM32CubeIDE_1.19.0\STM32CubeIDE\headless-build.bat' `
  -data "$env:TEMP\robot-build-workspace" `
  -import (Join-Path $PWD 'firmware') `
  -build 'projet_robot_rebuild/Debug'
```

Check the actual compilation and linking summary plus the generated ELF; the launcher's exit code alone is insufficient.

## Firmware pin assignments

These are MCU pins, not carrier-board connector numbers. Confirm the original carrier wiring before connecting hardware.

| Function | MCU pin(s) | Peripheral |
| --- | --- | --- |
| Left motor PWM | PA15 | TIM2 channel 1 |
| Right motor PWM | PB11 | TIM2 channel 4 |
| Left motor direction | PB2 | GPIO output |
| Right motor direction | PC8 | GPIO output |
| Right encoder A/B | PB4 / PB5 | TIM3 encoder inputs |
| Left encoder A/B | PB6 / PB7 | TIM4 encoder inputs |
| Ultrasonic echo | PA8 | TIM1 channel 1 input; channel 2 configured as indirect input |
| Ultrasonic trigger | PB10 | GPIO output |
| Battery sense | PB0 | ADC1 channel 15 |
| Battery warning | PA5 | GPIO output |
| Start button | PC13 | Falling-edge external interrupt |
| UART TX/RX | PA2 / PA3 | USART2, 115200 baud, 8N1 |
| Servo output configured in archive | PA11 | TIM1 channel 4; no complete servo positioning routine in the selected application |

The UART retargeting helper sends diagnostic `printf` output through USART2. The selected snapshot mainly prints battery messages; it does not continuously stream the wheel-speed plots shown in the historical captures.

## Before a hardware run

The archived `main()` calls `Arreter()`, waits approximately one second and then calls `Avancer()` before entering its button-dependent navigation loop. **The start button does not prevent initial wheel motion in this snapshot.** Target coordinates are also initialised to zero.

Begin with the wheels clear of the bench and an accessible motor-power disconnect. Check the wiring, motor driver, encoder supply, battery-sense divider and sonar signal levels against the actual platform. Do not flash and place the robot on the floor expecting it to wait for a button press.

Suggested staged verification, not performed during portfolio preparation:

1. Inspect the preserved startup and target settings before enabling motor power.
2. Check direction outputs and PWM with the drive system unloaded.
3. Rotate each wheel and verify encoder signs, counts and wrap behaviour.
4. Compare estimated speed against an independent measurement and verify controller periods.
5. Check sonar output against known distances and assess stale or missing readings.
6. Only after addressing the documented issues, run controlled motion trials and record speed and final position error.

See [implementation notes](technical-notes.md) for command conversion, missing saturation and other limitations. Building successfully is not proof of safe or correct physical operation.
