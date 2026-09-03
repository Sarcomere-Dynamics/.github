# Updating Actuator Firmware via the General Example

This guide covers flashing **actuator firmware** on an ARTUS hand using the interactive [**artusapi general example**](https://github.com/Sarcomere-Dynamics/Sarcomere_Dynamics_Resources/tree/main/examples/general_example) menu.
The firmware binary is streamed to the driver(s) through the mainboard over the existing Modbus RTU/TCP connection without any additional tools.
The instructions in this guide are compatible with products using eithr brushed actuators (i.e., ARTUS Lite), or brushless actuators (e.g. ARTUS Talos, ARTUS Scorpion, ARTUS Dex).

**The following instructions are not the same as flashing the mainboard of an ARTUS product**.
Instead, users looking to update their mainboard must refer to [the product-specific documentation found in the Quickstart guide](/docs/quickstart.md).

> [!WARNING]
> **DO NOT flash firmware unless instructed by the Sarcomere Dynamics team.**
> Interrupting a firmware update or flashing an incorrect binary can leave a driver in an unrecoverable state.
> The menu deliberately requires an extra confirmation before proceeding.

## Prerequisites

- A functional copy of the [**artusapi** General Example](https://github.com/Sarcomere-Dynamics/Sarcomere_Dynamics_Resources/tree/main/examples/general_example) for installing requirements, finding the USB device, and configuring [`robot_config.yaml`](/examples/config/robot_config.yaml).
- [The correct actuator firmware `.bin` file, supplied by Sarcomere Dynamics](https://github.com/Sarcomere-Dynamics/.github/releases).

## Procedure

1. Run the general example:

   ```bash
    cd examples/general_example
    python3 general_example.py
   ```

2. Wake up the hand in position control mode — enter `3`, `3` at the menu.
3. Start the firmware update — enter `f` at the menu.
4. Confirm the safety prompt by entering `e` when asked.
   Any other input cancels the update procedure.
5. Enter the **driver to flash** when prompted:

   | Value                         | Description                                                                   |
   | ----------------------------- | ----------------------------------------------------------------------------- |
   | `1`-`<number of controllers>` | Update **a specific actuator driver**, mapped to its **controller index + 1** |
   | `0`                           | Update **all actuators** present on the device.                               |

   The value is validated against the `number_of_controllers` corresponding to the relevant `robot_model`;
   an invalid value is rejected, allowing the operator to try one more time.

6. Enter the **absolute path** to the firmware `.bin` file when prompted (e.g. `/home/user/firmware/driver.bin`).

The firmware upload then begins.
A progress bar (`Uploading Actuator Firmware`) shows pages as they are written, and the tool waits for the hand to acknowledge the flash.
When the hand leaves the `ACTUATOR_FLASHING` state, the update is complete and `Firmware flashed successfully` is logged.

## Explanation

The `f` handler in `handle_command()` calls:

```python
artusapi.update_actuator(file_location=file_location_, drivers_to_flash=driver)
```

This incurs the following procedure:

1. Reads the binary size via `ActuatorUpdater.get_bin_file_info()`.
2. Sends a packet containing the firmware command and the relevant actuator driver to the command register.
3. Streams the binary to the mainboard in 128-byte half-page chunks using [`ActuatorUpdater.update_actuator()`].
   The ActuatorUpdater waits for the initial flashing acknowledgment before starting and sending a terminating `[0x0, 0x0]` chunk when the flashing process is complete.
4. Polls `get_robot_status()` until the hand is no longer in the `ACTUATOR_FLASHING` state.

## Troubleshooting

- **The update stalls at the start** — the hand never sent a flashing acknowledgment (`flashing_ack_checker` returns `False`).
  Verify the connection is healthy and that the hand is in an idle/ready state before pressing `f`.
- **s`Invalid driver number`** — the driver you entered exceeds the number of controllers on the connected hand.
  Re-enter a valid value.
- **The hand reports an error state** — flashing failed.
  Do not power-cycle mid-flash; contact the Sarcomere Dynamics team.
- Power-cycle the hand after a successful update if parameter changes are expected to take effect.
