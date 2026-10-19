# PI control and experimental results

## Control structure

The application contains independent left- and right-wheel control paths in `HAL_TIM_PeriodElapsedCallback()` in `firmware/Core/Src/main.c`.

For each wheel, the implementation calculates:

```text
speed = round((encoder_delta × 2 × pi / 347) × 0.032 / assumed_interval × 100)
error = requested_speed - measured_speed
accumulated_error = accumulated_error + error
command = Kp × error + Ki × accumulated_error
```

The constants represent an assumed 347 encoder counts per revolution and a 0.032 m wheel radius. The final factor of 100 expresses speed in cm/s under those calibration assumptions.

The preserved snapshot uses `Kp = 1` and `Ki = 0.75` for both wheels. The integral term accumulates error per update without an explicit sampling-period factor. These are implementation-specific discrete gains, not directly interchangeable continuous-time gains.

## Timing and actuation

TIM5 and TIM7 are configured with prescaler 124 and auto-reload 63999. Assuming their configured 80 MHz timer clock, the nominal update interval is:

```text
(124 + 1) × (63999 + 1) / 80000000 = 0.1 s
```

The code alternates two encoder-counter storage variables and updates the speed calculation on each timer callback. The left-wheel formula uses 0.2 s, while the right-wheel formula uses 0.1 s. This discrepancy is preserved and documented rather than silently corrected.

Motor PWM uses TIM2 channels 1 and 4, with prescaler 0 and auto-reload 79999: nominally 1 kHz at an 80 MHz timer clock. Direction is controlled through separate GPIO outputs.

## Historical tuning captures

| Capture | What can be observed |
| --- | --- |
| [Proportional response](media/proportional-response.png) | A lower steady operating level and pronounced dips during disturbances |
| [PI response](media/pi-response.png) | Recovery towards the previous operating level after a disturbance; a transient peak remains visible |
| [Higher-gain response](media/higher-gain-response.png) | Larger transient peaks following disturbances |
| [High-integral experiment](media/high-integral-response.png) | Startup overshoot and transient excursions during an experiment identified as high integral gain in the original files |

The historical project write-up described comparisons with proportional-only control, high integral gain and different PI settings, including a `Kp = 1, Ki = 0.8` experiment. That description must not be confused with the `Ki = 0.75` value in the selected firmware. Capture filenames alone do not establish an exact gain-to-image mapping or the complete test protocol.

The screenshots contain a trace labelled `vitesseActuelleD`, the right-wheel speed variable. Raw logs, timestamps tied to a firmware revision, reference-speed traces and a complete calibration record were not found with the selected files. They support qualitative discussion of the experiments, not a newly verified numerical comparison.

## Navigation objective and evidence

The original assignment specified parking within ±5 cm, a maximum speed of 30 cm/s and 1 kHz PWM. These are **design targets**. The historical write-up described successful parking trials, but no raw repeated-trial position measurements were recovered. This repository therefore does not claim newly verified ±5 cm positioning accuracy.

The archived code contains `VITESSE = 40`, so the 30 cm/s assignment limit must not be presented as an enforced firmware limit. Its turning routine uses fixed delays rather than closed-loop angle control. Bluetooth hardware is visible on the platform, but smartphone control is not implemented in the selected firmware.

## Useful follow-up experiments

A future verification campaign could record requested and measured wheel speeds with timestamps, establish actual counts per revolution and update periods, compare controller settings under the same disturbance protocol, and measure final parking error across repeated runs. Such experiments have not been performed for this repository.
