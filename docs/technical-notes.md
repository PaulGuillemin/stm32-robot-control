# Preserved implementation and limitations

## Snapshot selection

The complete original CubeIDE project was selected rather than combining isolated C files with unrelated generated drivers. Its foreground navigation code is active and its left/right PI loops are present.

A separate school-folder file labelled as the latest version contains a commented-out navigation loop and a different left-wheel time divisor. Other C variants change gains, target values and navigation branches. They are not combined into a fictional final implementation. No functional change was made during portfolio preparation.

## Runtime behaviour

- `main()` starts PWM, encoders and timer interrupts, then stops and restarts the motors after a one-second delay.
- The start-button interrupt sets `go = 1`; the foreground loop then sequences Y and X phases using the shared sonar distance.
- `x_obj` and `y_obj` are zero in the preserved snapshot. The first navigation transition must be reviewed against real coordinates.
- Straight-line wheel-speed control runs in TIM5 and TIM7 callbacks when `avancer == 1`.
- `tourner()` disables PI updates through `Arreter()`, commands opposite motor directions and waits a fixed 1500 ms, followed by a 1000 ms transition delay.
- Sonar distance averages five captured values, then applies `Distance = moyenne / 100`. The sensor model and distance calibration must be confirmed against the physical setup.
- Battery conversion is requested after a software counter exceeds 50 TIM6 updates. The raw ADC value is compared against `SEUIL_BATTERIE = 75`; this is not a calibrated voltage threshold.

## Issues to address before robust reuse

| Area | Observation |
| --- | --- |
| Startup | Motor actuation occurs before the start-button navigation condition |
| Command range | The PI output is passed to a `uint8_t` duty-cycle argument without explicit limiting to 0–100; negative or excessive floating-point values cannot be assumed to produce a valid duty command |
| Integral term | No anti-windup, explicit accumulator bounds or reset on every stop/restart |
| Encoder differences | Absolute differences do not handle counter wrap or retain direction information |
| Sampling scale | Left and right speed formulas use 0.2 s and 0.1 s respectively despite nominally equal timer periods |
| Initial estimate | Alternating counter storage may produce an invalid first speed difference if the counter has accumulated before the controller starts |
| Setpoint | `VITESSE = 40` differs from the original 30 cm/s assignment target |
| Turns | Fixed-duration actuation rather than measured heading control; one direction argument is `-1` even though the GPIO write interface expects a pin state |
| Navigation | Zero targets, blocking delays and no explicit sonar timeout or invalid-measurement handling |
| Interrupt workload | Blocking UART diagnostics from the ADC callback can interfere with timing |

These are source-inspection observations, not reproduced hardware failures. They are retained as engineering context for the archived work. Correcting them would be a new development step requiring separate verification.
