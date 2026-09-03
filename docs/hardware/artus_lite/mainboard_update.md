# Flashing Mainboard Firmware

[`mainboard_updater.py`](https://github.com/Sarcomere-Dynamics/Sarcomere_Dynamics_Resources/blob/clean/api_single_entry/artusapi/firmware_update/mainboard_updater.py) flashes the **ESP32-S3** onboard the ARTUS Lite mainboard.

It uses the [`esptool`](https://github.com/espressif/esptool) Python library directly.

The script only accepts a [**merged binary**, which may be acquired from the Sarcomere Dynamics Inc. firmware releases.](https://github.com/Sarcomere-Dynamics/.github/releases)

## Requirements

- Python 3
- `esptool` installed:

  ```bash
  pip install esptool
  ```

- A merged firmware binary (`*merged*.bin`).
  The script rejects any file that is not a `.bin` with `merged` in its name.
- The masterboard _in boot mode_ connected over USB and its serial port identified
  (e.g. `COM3` on Windows, `/dev/ttyUSB0` on Linux, `/dev/tty.usbserial-*` on macOS).

## Instructions

### 1. Entering Bootloader Mode

The system must be in boot mode before powering on to allow the firmware update to take place.
The LED will remain blue when the system is in boot mode.

There are two methods of entering boot mode:

#### Tactile Button

1. Locate the boot button between the two nano M8 connectors.
2. Hold the button down while powering on the device.
   The status LED should turn blue and remain solid.

#### Bootloader Switch

1. Take off the 3x bolts on both sides of the base plate shown in the image below. (Requires a 2.5mm Hex bit)
   [!ARTUS Lite Baseplate bolts](/docs/assets/hardware/artus_lite/baseplate_bolts.jpg)
2. Locate SW2 and toggle the switch towards the circle marker.
   [!ARTUS Lite Boot Switch](/docs/assets/hardware/artus_lite/boot_toggle.jpg)
3. Power on the device.
   The status LED should turn blue and remain solid.

### 2. Run the mainboard updater script

Activate `mainboard_updater.py` via the command line as shown below.

```bash
python3 upload_esptool.py -p <serial_port> -f <merged_binary>
```

For example, on a Linux device, this may appear as:

```bash
python3 upload_esptool.py -p /dev/ttyUSB0 -f /home/<username>/Downloads/lite_left_merged_<VERSION>.bin
```

### 3. Exit bootloader mode

Remove power from the device.
If the [bootloader switch](#bootloader-switch) was set towards the BOOT position, return it to the original position.

Reapply power to the device.
The status LED should turn blue, turn off, then begin blinking green.

## Appendix

### Arguments

| Argument       | Required | Default   | Description                                                                              |
| -------------- | -------- | --------- | ---------------------------------------------------------------------------------------- |
| `-p`, `--port` | Yes      | —         | Serial port of the masterboard, e.g. `/dev/ttyUSB0`.                                     |
| `-f`, `--file` | Yes      | —         | Path to the merged binary (e.g. `master.ino.merged.bin`). Must be a `*merged*.bin` file. |
| `--baud`       | No       | `921600`  | Flashing baud rate.                                                                      |
| `--chip`       | No       | `esp32s3` | Target chip.                                                                             |
| `--erase`      | No       | off       | Erase the entire flash before writing (adds `--erase-all`).                              |

### Explanation

The script assembles and runs an `esptool write_flash` command, writing the merged binary at offset `0x0`:

```bash
esptool --chip esp32s3 --port <port> --baud <baud rate> \
  --before default_reset --after hard_reset \
  write_flash -z \
  --flash_mode keep --flash_freq keep --flash_size keep \
  0x0 <filename>.merged.bin
```

The exact command is printed to the console before flashing, so you can see precisely what is being run.

## Notes

- Use `--erase` when switching between significantly different firmware versions or if you suspect a corrupted flash. It clears the entire chip (including any stored NVS/calibration data) before writing.
- If flashing fails to connect, verify the port, that no other program is holding the serial connection, and try putting the board into bootloader/download mode.
- Power-cycle the hand after flashing so parameter changes take effect.
