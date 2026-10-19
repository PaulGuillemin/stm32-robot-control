# Credits and third-party components

## Project work

**Paul Guillemin** — embedded implementation, integration, control and testing for the 2024 robot project at Mines Saint-Étienne.

The educational robot platform, carrier electronics, motor drivers, sensors and development board are reused hardware. They are not presented as original hardware designs created for this repository.

## Software components

| Component | Attribution | Preserved information |
| --- | --- | --- |
| STM32 HAL and generated platform code | STMicroelectronics | Original source headers and `firmware/Drivers/STM32L4xx_HAL_Driver/LICENSE.txt` |
| CMSIS | ARM and contributors identified in individual files | `firmware/Drivers/CMSIS/LICENSE.txt` |
| STM32L4 device support | STMicroelectronics | `firmware/Drivers/CMSIS/Device/ST/STM32L4xx/LICENSE.txt` |
| UART retargeting | Carmine Noviello | Original credit and upstream source reference in `firmware/Core/Src/retarget.c` |

The retargeting helper is reused code, not original project work. Its original file header contains the upstream reference; no additional local licence for that helper was identified during preparation.

## Media and documents

The included hardware photos and controller traces come from the original project files. Their labels and content are preserved. `control-architecture.svg` is a new explanatory drawing based on the archived implementation, not an experimental result.

The full academic report and unrelated teaching materials are not included. No new global licence has been applied to the combined repository; original third-party notices remain in place.
