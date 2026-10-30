# STM32 Step Counter

A wearable step counter that detects steps from a 3-axis accelerometer, classifies pace as walking, jogging or running, and shows the result on a small LCD. Built for the UTS Embedded Studio subject by a team of four. I designed the custom STM32 PCB and wrote most of the firmware (see the commit history).

<!-- Add a photo of the watch / PCB here: ![Step counter](docs/watch.jpg) -->

## How it works

1. **Calibration:** the user holds the device in six orientations. The firmware averages the accelerometer at each one and computes a per-axis offset and scale factor, so raw readings convert to acceleration in g.
2. **Filtering:** accelerometer readings are smoothed with a low-pass filter to remove jitter before step detection (`prototyping/Filtering_V1`).
3. **Step detection:** a step is counted when the acceleration magnitude crosses a threshold (1.1 g), with a 300 ms debounce so one step is never counted twice.
4. **Pace classification:** the time between steps sets the pace: over 850 ms is walking, 450 to 850 ms is jogging, and under 320 ms is running. LEDs and the LCD show the current pace.
5. **State machine:** a top-level state machine moves between idle, calibration, active tracking, self-test and battery monitoring.

## Hardware

- Custom STM32 PCB designed in Altium (firmware developed on a Nucleo-F401RE first)
- 3-axis analog accelerometer on A0 to A2
- ST7735 colour LCD over SPI (Adafruit GFX)
- Battery voltage sensing on A3
- Status LEDs for walk, jog and run

Pin assignments are in `state_machine/Step-tracker/include/BoardConfig.h`.

## Repository layout

| Folder | Contents |
|---|---|
| `state_machine/Step-tracker/` | Main firmware: calibration, step tracking, pace tracking, LCD and battery modules |
| `state_machine/Top_Level_State_Machine_V1/` | Top-level state machine (idle, calibration, tracking, self-test, battery) |
| `state_machine/Calibration/` | Stand-alone six-orientation calibration routine |
| `prototyping/` | Early tests of each routine: filtering, calibration, self-test, active tracking, voltage sensing |

## Building

Each folder under `state_machine/` and `prototyping/` is a separate PlatformIO project. Open one in VS Code with the PlatformIO extension, then build and upload to a Nucleo-F401RE:

```
pio run --target upload
```

## Credits

Team project with Elena Justo and two other teammates. Forked from [elenajusto/embedded-studio](https://github.com/elenajusto/embedded-studio).
