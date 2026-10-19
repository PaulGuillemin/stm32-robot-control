# Provenance and verification

## Sources and scope

Original project period: June 2024. Portfolio preparation: 19 October 2026. Author presentation follows Paul Guillemin's direct clarification of his work on the project.

The selected firmware comes from the complete archived project `projet_robot_rebuild` in the former personal STM32 project collection. Despite its historical folder name, it is an existing 2024 project, not a new reconstruction. It includes a NUCLEO-L476RG IOC, generated support files, application code and UART retargeting.

It was compared with three isolated C variants from the school project folder. The chosen project retains active navigation logic and complete build support. This does not establish that it is the exact firmware used for every experiment.

The complete academic report is not distributed. It was consulted locally for project context and descriptions of historical experiments. The repository instead includes original standalone photos/captures and newly written English documentation. Original capture labels and firmware comments retain their original language.

## Build verification

| Check | Result |
| --- | --- |
| Import into a separate workspace | Original project recognised as `projet_robot_rebuild` |
| Configuration | Debug |
| IDE | STM32CubeIDE 1.19.0 |
| Compiler | GNU Tools for STM32 13.3.rel1 |
| Target | STM32L476RG, Cortex-M4F, hardware floating-point ABI |
| Compilation and linking | Successful, 0 errors and 0 warnings |
| Generated binary | `projet_robot_rebuild.elf` |
| `arm-none-eabi-size` output | text: 41168, data: 124, bss: 2788 bytes |

The size figures are sections reported for the Debug build, not maximum runtime memory consumption or power measurements. Generated binaries and build logs are excluded from Git.

## Integrity and presentation checks

Copied firmware files were compared with the source archive using SHA-256 after compilation. No functional source or configuration changes were made. Local documentation links, selected images, source credits and staged files were checked before committing. A targeted search for credentials and personal paths was performed; it does not guarantee exhaustive detection.

No new experiment, motor actuation, board flash or parking trial was performed. Historical plots are not presented as fresh measurements. In particular, ±5 cm parking accuracy remains an assignment target and a claim in the historical write-up, not a newly verified result.

Import commits are dated when they are created. The repository contains no fabricated historical development sequence.
