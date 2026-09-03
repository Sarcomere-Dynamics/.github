# ARTUS Scorpion

![Sarcomere Dynamics Inc. Logo](/docs/assets/common/logo.svg)

## Table of Contents

- [Reference Documents](#reference-documents)
- [Safety](#safety)
- [Setup](#setup)
- [Usage](#usage)
- [Features](#features)

## Reference Documents

- [Getting Started (Wiring)](/docs/assets/hardware/artus_scorpion/getting_started.png)
- [ModbusMap PDF](/docs/hardware/artus_scorpion/ModbusMap_Scorpion.pdf)

## Safety

> [!IMPORTANT]
> The gripper is backdriveable but still capable of causing pinching injuries while powered and closing.
> Keep fingers, hair, and loose clothing clear from the device while powered.
> Never reach into the device while it is executing a motion command.
> Disconnect power immediately if unexpected behaviour is observed.

## Setup

Using the provided cables and power supply, the ARTUS Scorpion must be connected to power and a serial port based on the following pinout.

![ARTUS Scorpion Wiring Diagram](/docs/assets/hardware/wiring_diagram.svg)

When applying power, **the ARTUS Scorpion should be connected a 24V DC power supply capable of maximum 48W output, and 2.4W idle/nominal output.**

## Usage

[Navigate to the **artusapi general example** to test basic functionality of your ARTUS product.](https://github.com/Sarcomere-Dynamics/Sarcomere_Dynamics_Resources/tree/main/examples/general_example)

### Startup

When power is applied to the device, the user must always run a `wake_up()` command.

> [!IMPORTANT]
> Unlike ARTUS Lite, ARTUS Scorpion **requires** a `calibrate()` call after `wake_up()` before it will accept additional commands.

### Shutdown

1. Send a zero position command to all joints so the hand opens.
2. Once open, call `sleep()` to save parameters to the SD card (if applicable).
3. Wait for the ACK — once received, the device can be powered off.

### Firmware Updates

Currently, only the actuator drivers onboard the ARTUS Scorpion may be updated.

Mainboard firmware update functionality is under development.

- [To update the **actuator driver** firmware, click here](/docs/hardware/actuator_update.md)

## Features

### Status LED

Prior to operating the ARTUS Scorpion, the user should review the following table, ensuring their ability to identify the operation status.

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

The system uses the Modbus RTU communication protocol.
See the [ModbusMap PDF](/docs/hardware/artus_scorpion/ModbusMap_Scorpion.pdf) for register-level detail if you are developing your own communication application.

### Motion Parameters

| Parameter       | Default |     Range      |    Units    |
| --------------- | :-----: | :------------: | :---------: |
| Linear Position |   0mm   |  0mm - 100mm   |     mm      |
| Velocity        | 50mm/s  | 0mm/s - 70mm/s |  mm/second  |
| Force           |   20N   |   0N - 100N    | Newtons (N) |

> [!NOTE]
> Scorpion has a single gripper joint (no left/right, no hand joint map) with these units:
>
> The starting position is 100mm wide (fully open, position 0).
> Since it is a single-drive gripper, a **commanded target position of 50mm fully closes the gripper.**
