# Main Controller Firmware Repository

[中文版](./README.md)

## Table of Contents
- [**1. Keyboard Modes**](#1-keyboard-modes)
- [**2. Mode Switching**](#2-mode-switching)
- [**3. Wireless Pairing/Channel Selection**](#3-wireless-pairing-and-channel-selection)
- [**4. Battery Status**](#4-battery-status)
- [**5. Firmware Upgrade**](#5-firmware-upgrade)

## 1. Keyboard Modes
The keyboard supports various modes for configuration, and some modes require specific keyboard support:

- FS USB Mode: The keyboard is connected to the computer via a USB interface. In FS USB Mode, the report rate is 1000Hz, and it can be switched between 6-key and full-key rollover modes.

- HS USB Mode: The keyboard is connected to the computer via a USB interface. In HS USB Mode, the report rate is 8000Hz, and it has full-key rollover mode by default.
<span style="color:yellow;">**HS USB Mode support is required**</span>

- BLE Bluetooth Mode: The keyboard is connected to devices via Bluetooth, providing the convenience of wireless connection. In BLE Bluetooth Mode, the report rate is 133Hz. The BLE version is 5.4, and for optimal performance, it is recommended to use a Bluetooth receiver that supports 2M PHY. USB receivers marketed as compatible with 5.0 or 5.1 often do not support it. AX200, AX210, and BE200 are recommended.
<span style="color:yellow;">**BLE Bluetooth Mode support is required**</span>

- LLPM Mode: 2.4G wireless mode, supporting low latency and a report rate of 1000Hz. LLPM Mode provides a stable wireless connection with lower response latency, around 1mS.
<span style="color:yellow;">**LLPM Mode support is required, and a corresponding LLPM receiver is needed**</span>

- ESB Mode: 2.4G wireless mode, supporting ultra-low latency and a report rate of 4000Hz. ESB Mode provides extremely low response latency, around 250uS.
<span style="color:yellow;">**ESB Mode support is required, and a corresponding ESB receiver is needed**</span>

- <span style="color:yellow;">**A keyboard that supports both LLPM and ESB modes simultaneously does not exist**</span>

## 2. Mode Switching
- The keyboard is set to the Auto mode by default, and there are three modes for switching: Auto mode, Forced USB mode, and Forced Wireless mode. Please open VIA to see the location of the mode switching keys.

### Mode Switching Table
| USB Cable | Auto Mode | Forced USB | Forced Wireless |
|:------:|:------:|:------:|:------:|
| Connected via USB | USB Mode | USB Mode | Wireless Mode |
| Connected to power | Wireless Mode | USB Mode | Wireless Mode |
| Not connected | Wireless Mode | USB Mode | Wireless Mode |

### VIA Keycodes
| Function | Key Name | Key HEX |
|:-------:|:-------:|:------:|
| Auto Mode | OU_AUTO | 0x7C20 |
| Forced USB | OU_USB   | 0x7C21 |
| Forced Wireless |  0x7C22  | 0x7C22 |

<span style="color:yellow;">**To change the key positions, you can use the ANY key in VIA and enter the desired key name or HEX code.**</span>

- Some keyboards have a three-position switch for USB, BLE Bluetooth, and 2.4G modes (LLPM, ESB).
    | Switch Position | Function   | Note                                               |
    |:------:|:---------:|:-------------------------------------------------|
    |  USB   | Power Off    | Selecting the Wireless mode as BLE, connecting the USB cable to power will automatically turn on the power supply. Powering off can significantly reduce standby power consumption. |
    |  BLE   | Wireless Mode (BLE) | Selecting the Wireless mode as BLE allows you to switch between multiple channels and enter standby mode after a long period of inactivity. However, the power consumption is higher compared to powering off. |
    |  2.4G  | Wireless Mode (2.4G) | Selecting the Wireless mode as 2.4G provides a unique pairing channel and enters standby mode after a long period of inactivity. However, the power consumption is higher compared to powering off. |

<span style="color:yellow;">**Currently, key selection for Wireless BLE and 2.4G modes is not supported.**</span>

## 3. Wireless Pairing and Channel Selection
- Wireless pairing and channel selection are currently controlled by a single dedicated key called the Pairing key. Please open VIA to see the location of the Pairing key.
- The keyboard has a pairing indicator light to indicate the pairing status of different channels.

    ### Pairing Key Operations
    | Operation | Key Function |
    |:-------:|:-------:|
    | Single Click | Channel Selection |
    | Long Press | Repairing |
    | Double Click | Confirm Operation |

    ### Pairing Indicator Light Status
    | Indicator Light | Status | Note |
    |:----------:|:-------:|:-------------------------------------------|
    | Off | Not Connected | In multiplex mode, the pairing indicator light will switch to the keyboard status indicator light after 3 seconds. |
    | Solid White | Connected | In multiplex mode, the pairing indicator light will switch to the keyboard status indicator light after 3 seconds. |
    | Slow Breathing White | Waiting to Connect | |
    | Fast Breathing White | Waiting to Pair | Returns to the original state if pairing is not successful after 30 seconds. |
    | Fast Flashing Yellow | Waiting for Confirmation | Waits for user further action, returns to the original state if there is no action for 10 seconds. |
    | Solid Red | Serious Error | Needs to enter BootLoader for firmware recovery. |

    ### Channel Selection Operations
    - Single click the Pairing key to output the channel selection operation. The pairing indicator light of the selected channel will flash fast in yellow, indicating that the keyboard is in waiting for confirmation operation.
    - Continue to single click the Pairing key to switch to the next channel. The pairing indicator light of the selected channel will flash fast in yellow, indicating that the keyboard is in waiting for confirmation operation.
    - Double click the Pairing key to output the confirmation operation. The pairing indicator light of the selected channel will change to slow breathing white, indicating that the keyboard is in waiting to connect state.
    - After entering the waiting to connect state, if the keyboard successfully establishes a connection with the paired device, the pairing indicator light of the selected channel will stay solid white.
    - If this channel has never been paired before, the keyboard's Bluetooth can be discovered.

    ### Repairing Operations
    - Long press the Pairing key to output the repairing operation. The pairing indicator light of the currently selected channel will flash fast in yellow, indicating that the keyboard is in waiting for confirmation operation.
    - If you single click the Pairing key, the repairing operation will be canceled.
    - If you double click the Pairing key, the confirmation operation will be output. The pairing indicator light of the currently selected channel will start fast breathing white, indicating that the keyboard is in repairing state.
    - After entering the repairing state, the keyboard can be discovered.
    - Quick pairing is supported on Win10 and Win11. You need to check the "☑️Show notifications to connect using Swift Pair" option in the Bluetooth settings interface.

    ### VIA Keycodes
    | Key Function | Key Name | Key HEX | 
    |:-------:|:-------:|:------:|
    | Pairing Key | 0x5700 | 0x5700 |

    <span style="color:yellow;">**To change the key position, you can use the ANY key in VIA and enter the desired key name or HEX code.**</span>

## 4. Battery Status
- The keyboard has an indicator light to display the battery status.
    | Indicator Light | USB Cable | Battery Status |
    |:--------------:|:---------:|:--------------|
    | Off            | Connected | Fully charged |
    | Off            | Disconnected | High battery level |
    | Green Breathing | | Charging |
    | Red Flashing | | Battery not detected |
    | Flashing once every 5 seconds | Disconnected | Low battery level (20%) |

## 5. Firmware Upgrade
- Firmware upgrade is done using a USB drive. When connected via USB, a popup drive will appear.
- Entering Bootloader mode:
    - Use VIA3 and click "EnterBootloader"
    - While the keyboard is powered off (for three-mode keyboards, switch should be turned off), hold down the ESC key and plug in the USB cable.
    - Press the Bootloader shortcut key. Refer to VIA for the specific key position.
    - Locate the two half circles labeled UF2BOOT on the keyboard PCB. While the keyboard is powered off, short these two half circles and keep them connected, then plug in the USB cable.
    - If the keyboard firmware upgrade fails, it will automatically enter Bootloader mode and wait for the upgrade.
- Firmware naming convention, taking "release_1.0.0+0_molly60_r1_nucleo_f411re_osc25_app.encrypted.signed.uf2" as an example:
    - "release" indicates an official release version.
    - "1.0.0+0" represents the APP version.
    - "molly60_r1" represents the keyboard model.
    - "nucleo_f411re_osc25" represents the keyboard's main controller board.
    - "app" indicates that this is an APP firmware.
    - "encrypted" indicates that the firmware is encrypted.
    - "signed" indicates that the firmware is signed.
    - "uf2" indicates that this is a firmware for USB drive update.
- Firmware upgrade for single-mode keyboards:
    - Connect via USB to enter Bootloader mode.
    - Copy the firmware for USB drive update to the popup drive.
    - Wait for the transfer to complete.
    - Wait for 10-30 seconds for the keyboard to automatically restart. After the restart, the upgrade is complete, and the user configuration will be automatically cleared.
    - If there is a power interruption during the restart, the upgrade will fail, and the keyboard will automatically enter Bootloader mode to wait for re-upgrading.
    - <span style="color:yellow;">You must use the corresponding firmware for your keyboard model. Using other firmware will be rejected by the keyboard.</span>
- Firmware upgrade for three-mode keyboards:
    - Connect via USB to enter Bootloader mode.
    - Copy both the main controller firmware and the wireless module firmware to the popup drive for USB drive update.
    - Wait for the transfer to complete.
    - Wait for 10-30 seconds for the keyboard to automatically restart. After the restart, the upgrade is complete, and the user configuration will be automatically cleared.
    - If there is a power interruption during the restart, the upgrade will fail, and the keyboard will automatically enter Bootloader mode to wait for re-upgrading.
    - <span style="color:yellow;">You must use the corresponding firmware for your keyboard model. Using other firmware will be rejected by the keyboard.</span>
- Firmware Repository Links:
    - For users in China:
        - [Main controller firmware](https://gitee.com/PhageStudio/firmware)
        - [Wireless module firmware](https://gitee.com/PhageStudio/wm_firmware)
    - For users outside China:
        - [Main controller firmware](https://github.com/PhageStudio/firmware)
        - [Wireless module firmware](https://github.com/PhageStudio/wm_firmware)