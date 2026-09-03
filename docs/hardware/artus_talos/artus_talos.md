# ARTUS Talos

![Sarcomere Dynamics Inc. Logo](/docs/assets/common/logo.svg)

## Table of Contents

- [Reference Documents](#reference-documents)
- [Safety](#safety)
- [Setup](#setup)
- [Usage](#usage)
- [Features](#features)

## Reference Documents

- [Getting Started (Wiring)](/docs/assets/hardware/artus_talos/getting_started.png)
- [ModbusMap PDF](/docs/hardware/artus_talos/ModbusMap_Talos_r1.pdf)

## Safety

> [!CAUTION]
> The hand contains pinch points at every joint.
> Keep fingers, hair, and loose clothing clear from the device while powered.
> Never reach into the device while it is executing a motion command.
> Disconnect power immediately if unexpected behaviour is observed.

## Setup

Using the provided cables and power supply, the ARTUS Talos must be connected to power and a serial port based on the following pinout.

![ARTUS Talos Wiring Diagram](/docs/assets/hardware/artus_talos/getting_started.png)

When applying power, **the ARTUS Talos should be connected to a 24V DC power supply, requiring a maximum instantaneous draw of 100W, nominal 35W, and idle 6W.**

## Usage

[Navigate to the **artusapi general example** to test basic functionality of your ARTUS product.](https://github.com/Sarcomere-Dynamics/Sarcomere_Dynamics_Resources/tree/main/examples/general_example)

### Startup

When power is applied to the device, the user must always run a `wake_up()` command.

> [!IMPORTANT]
> Unlike ARTUS Lite, ARTUS Talos **requires** a `calibrate()` call after `wake_up()` before it will accept additional commands.

### Shutdown

1. Send a zero position command to all joints so the hand opens.
2. Once open, call `sleep()` to save parameters to the SD card (if applicable).
3. Wait for the ACK — once received, the device can be powered off.s

### Firmware Updates

Currently, only the actuator drivers onboard the ARTUS Talos may be updated.

Mainboard firmware update functionality is under development.

- [To update the **actuator driver** firmware, click here](/docs/hardware/actuator_update.md)

## Features

### Status LED

Here is a detailed table of the LED states during normal operation and a description of the states.

| LED Colour     | Description                                                                       |
| -------------- | --------------------------------------------------------------------------------- |
| Blue           | Power on (Ready for Startup Sequence)                                             |
| Green          | Idle                                                                              |
| Flashing Green | Active mode                                                                       |
| Red/LED OFF    | Error state                                                                       |
| Orange/Yellow  | Shutdown/Sleep mode, may require power cycle for parameter changes to take effect |
| Yellow         | Flashing Actuators                                                                |

### Communication Methods

Hands are shipped with USBC and RS485 capabilities.

The system utilizes the Modbus RTU communication protocol.
See the [ModbusMap PDF](/docs/hardware/artus_talos/ModbusMap_Talos_r1.pdf) for register-level detail if you are developing your own communication application.

### Hand Joint Map

Below is a joint index guide mapped to a normal human hand, with the naming convention and joint indices used for control purposes.

![ARTUS Talos Hand Joint Map](/docs/assets/hardware/artus_talos/talos_hand_joint_map.png)

### Motion Parameters

| Parameter                  | Default  |        Range         |       Units        |
| -------------------------- | :------: | :------------------: | :----------------: |
| Finger/Thumb Flexion Angle |   0deg   |     0deg - 90deg     |    **degrees**     |
| Thumb Spread Angle         |   0deg   | -56.25deg - 56.25deg |    **degrees**     |
| Velocity                   | 200deg/s |  0deg/s - 300deg/s   | **degrees/second** |
| Force                      |   16N    |       2N - 40N       |    **Newtons**     |

> [!NOTE]
> For flexion angles, a more positive value represent a more closed position.
> A more negative value represents a more opened position.
>
> For thumb spread angles, a positive value corresponds towards a position closer to the right hand thumb.
> Negative spread values are closer towards the pinky.
