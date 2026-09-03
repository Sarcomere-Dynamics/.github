# ARTUS Lite

![Sarcomere Dynamics Inc. Logo](/docs/assets/common/logo.svg)

## Table of Contents

- [Reference Documents](#reference-documents)
- [Safety](#safety)
- [Setup](#setup)
- [Usage](#usage)
- [Features](#features)

## Reference Documents

- [Quick Start PDF (Wiring Diagram)](/docs/hardware/artus_lite/Artus_Lite.pdf)
- [Technical Specification Sheet](/docs/hardware/artus_lite/Artus_Lite_Technical_Specification_Sheet.pdf)

## Safety

> [!CAUTION]
> The hand contains pinch points at every joint.
> Keep fingers, hair, and loose clothing clear from the device while powered.
> Never reach into the device while it is executing a motion command.
> Disconnect power immediately if unexpected behaviour is observed.

## Setup

Using the provided cables and power supply, the ARTUS Lite must be connected to power and a serial port based on the following pinout;
the operator may use either the **8-pin combination connector** or the **4-pin power-only connector and the USB-C port onboard the mainboard**.

![ARTUS Lite Wiring Diagram](/docs/assets/hardware/artus_lite/wiring_diagram_legacy.svg)

When applying power, **the ARTUS Lite should be connected to a 24V DC power supply, requiring a maximum instantaneous draw of 200W, minimum 48W and typical 100W.**

## Usage

[Navigate to the **artusapi general example** to test basic functionality of your ARTUS product.](https://github.com/Sarcomere-Dynamics/Sarcomere_Dynamics_Resources/tree/main/examples/general_example)

### Startup

When power is applied to the device, the user must always run a `wake_up()` command.

Afterwards, if all joints are at their starting position, then the system does not need to run a `calibrate()` before sending target commands.

### Shutdown

1. Send a zero position command to all joints so the hand opens.
2. Once open, call `sleep()` to save parameters to the SD card (if applicable).
3. Wait for the ACK — once received, the device can be powered off.

> [!NOTE]
> Unlike the Artus Lite Mk. 8, which automatically saved configuration data to SD card periodically, saving is now intentional and only happens on `sleep()`.

### Firmware Updates

The mainboard and actuator drivers onboard the ARTUS Lite can be updated independently.

- [To update the **mainboard** firmware, click here](/docs/hardware/artus_lite/mainboard_update.md)
- [To update the **actuator driver** firmware, click here](/docs/hardware/actuator_update.md)

## Features

### Status LED

Prior to operating the ARTUS Lite, the user should review the following table, ensuring their ability to identify the operation status.

| LED Colour    | Description                                                                       |
| ------------- | --------------------------------------------------------------------------------- |
| Blue          | Power on                                                                          |
| Green         | Idle (Ready to connect, ready for commands)                                       |
| Red           | Error state                                                                       |
| Orange/Yellow | Shutdown/Sleep mode, may require power cycle for parameter changes to take effect |
| Purple        | Flashing Actuators                                                                |

### Communication Methods

As of v9.10.XX firmware, all ARTUS Lites have the following communication channels available in parallel:

- ModbusRTU via USB-C **OR** RS485 on 8-pin connector
- ModbusTCP over WiFi

It is recommended to operate the ARTUS Lite via a wired connction (i.e., via the `RS485_RTU` transport) for improved speed and robustness.

### Hand Joint Map

Below is a joint index guide mapped to a normal human hand, with the naming convention and joint indices used for control purposes.

![ARTUS Lite Hand Joint Map](/docs/assets/hardware/artus_lite/joint_map.svg)

### Motion Parameters

| Parameter                  | Default Value |       Range       |       Units        |
| -------------------------- | :-----------: | :---------------: | :----------------: |
| Finger/Thumb Flexion Angle |     0deg      |   0deg - 90deg    |    **degrees**     |
| Finger Spread Angle        |     0deg      |  -17deg - 17deg   |    **degrees**     |
| Thumb Spread Angle         |     0deg      |  -40deg - 40deg   |    **degrees**     |
| Velocity                   |   150deg/s    | 0deg/s - 300deg/s | **degrees/second** |
| Force                      |      10N      |     0N - 20N      |    **Newtons**     |

> [!NOTE]
> For flexion angles, a more positive value represent a more closed position.
> A more negative value represents a more opened position.
>
> For finger/thumb spread angles, a positive value corresponds towards a position closer to the right hand thumb.
> Negative spread values are closer towards the pinky.

### Fingertip Force Sensors (ARTUS Lite+)

The ARTUS Lite+ has the same basic control and characteristics as the ARTUS Lite.
However, it is equipped with Contactile fingertip force sensors.

As such, ARTUS Lite+ also outputs the X, Y, and Z vector forces from its fingertips, in addition to the feedback telemetry data consistent with the ARTUS Lite.
