# 主控固件仓库

[English](./README_en.md)

## 目录

- [**1. 键盘模式**](#1-键盘模式)
- [**2. 模式切换**](#2-模式切换)
- [**3. 无线配对/通道选择**](#3-无线配对及通道选择)
- [**4. 电池电量**](#4-电池电量)
- [**5. 固件升级**](#5-固件升级)

## 1. 键盘模式

键盘具有多种模式可以配置，部分模式需要特定键盘支持：

- FS USB 模式：键盘通过 USB 接口连接到计算机。在 FS USB 模式下，回报率为 1000Hz，可以切换为 6 键和全键无冲模式。

- HS USB 模式：键盘通过 USB 接口连接到计算机。在 HS USB 模式下，回报率为 8000Hz，默认全键无冲模式。
  <span style="color:yellow;">**需要支持 HS USB 模式**</span>

- BLE 蓝牙模式：键盘通过蓝牙连接到设备，提供无线连接的便利性。在 BLE 蓝牙模式下，回报率为 133Hz - 2000Hz 可配置。BLE 版本为 5.4，为了得到最佳效果，请使用支持 2M PHY 的蓝牙接收器，宣传页有兼容 5.0 5.1 字样的 USB 接收器很可能不支持。推荐使用 AX200，AX210，BE200。<span style="color:yellow;">**需要支持 BLE 蓝牙模式**</span>

- LLPM 模式：2.4G 无线模式，支持低延迟和 1000Hz 的回报率。LLPM 模式提供稳定的无线连接，并提供较低的响应延迟，~1mS。
  <span style="color:yellow;">**需要支持 LLPM 模式，并且需要配套 LLPM 接收器**</span>

- ESB 模式：2.4G 无线模式，支持超低延迟和 4000Hz 的回报率。ESB 模式提供极低的响应延迟，~250uS。<span style="color:yellow;">**需要支持 ESB 模式，并且需要配套 ESB 接收器**</span>

- <span style="color:yellow;">**不存在键盘同时支持 LLPM 和 ESB 模式**</span>

## 2. 模式切换

- 键盘默认为自动模式，模式切换有三种，自动模式，强制 USB 模式，强制无线模式。模式切换按键位置请打开 VIA 查看。

  ### 模式切换表

  | USB 线缆 | 自动模式 | 强制 USB | 强制无线 |
  | :------: | :------: | :------: | :------: |
  | 连接 USB | USB 模式 | USB 模式 | 无线模式 |
  | 连接电源 | 无线模式 | USB 模式 | 无线模式 |
  |  未连接  | 无线模式 | USB 模式 | 无线模式 |

  ### VIA 按键

  |   按键功能    | 按键名称 | 按键 HEX |
  | :-----------: | :------: | :------: |
  |   自动模式    | OUT_AUTO |  0x7C20  |
  |   强制 USB    | OUT_USB  |  0x7C21  |
  |   强制无线    |  OUT_WM  |  0x7C22  |
  | 切换蓝牙/2.4G | OUT_WMMD |  0x7C23  |

  <span style="color:yellow;">**改变按键位置可以通过 VIA 中的 ANY 按键，填入按键名称或 HEX**</span>

- 部分键盘具有一个三挡的开关，分别为 USB，BLE 蓝牙，2.4G 模式（LLPM、ESB）。
  | 开关档位 | 功能含义 | 备注 |
  |:------:|:---------:|:-------------------------------------------------|
  | USB |关闭电源 | 无线模式选择为 BLE ，USB 线缆连接电源会自动开启供电，关闭电源可以大幅降低待机功耗 |
  | BLE |无线模式 BLE | 无线模式选择为 BLE，可以切换多个通道，长时间无操作进入待机，但功耗相比关闭电源高 |
  | 2.4G |无线模式 2.4G| 无线模式选择为 2.4G，具有唯一配对通道，长时间无操作进入待机，但功耗相比关闭电源高 |

  <span style="color:yellow;">**目前还不支持按键选择无线 BLE 和 2.4G 模式**</span>

## 3. 无线配对及通道选择

- 无线的配对以及通道选择目前是一个唯一的按键进行控制，称为配对按键。配对按键位置请打开 VIA 查看。
- 键盘具有配对指示灯，用来指示不同通道的配对状态。

  ### 配对按键操作

  | 按键操作 | 按键功能 |
  | :------: | :------: |
  |   单击   | 通道选择 |
  |   长按   | 重新配对 |
  |   双击   | 确认操作 |

  ### 配对指示灯状态

  |  指示灯状态  |   状态   | 备注                                                           |
  | :----------: | :------: | :------------------------------------------------------------- |
  |     熄灭     |  未连接  | 复用模式下，配对指示灯将会在 3 秒后切换到键盘状态指示灯        |
  |   白色常亮   |  已连接  | 复用模式下，配对指示灯将会在 3 秒后切换到键盘状态指示灯        |
  | 白色缓慢呼吸 | 等待连接 |                                                                |
  | 白色快速呼吸 | 等待配对 | 30 秒后配对仍未成功恢复原状态                                  |
  | 黄色快速呼吸 | 等待确认 | 等待用户进一步操作，无操作 10 秒后恢复原状态                   |
  |   红色呼吸   | 严重错误 | 未能识别到无线模组，请尝试重启，更新固件，如不能解决请联系售后 |

  ### 通道选择操作

  - 单击配对按键，输出通道选择操作，被选择的通道的配对指示灯将会黄色快速闪烁，代表键盘进入等待确认操作
  - 继续单击配对按键，将切换下一个通道，被选择的通道的配对指示灯将会黄色快速闪烁，代表键盘进入等待确认操作
  - 双击配对按键，输出确认操作，被选择的通道的配对指示灯将变化为白色缓慢呼吸，代表键盘进入等待连接状态
  - 键盘进入等待连接状态后，如果与对方设备建立连接成功，被选择的通道的配对指示灯将会白色常亮。
  - 如果这个通道还从未配对过，键盘蓝牙可以被搜索到。

  ### 重新配对操作

  - 长按配对按键，输出重新配对操作，当前选择的通道的配对指示灯将会黄色快速闪烁，代表键盘进入等待确认操作
  - 如果单击配对按键，将会取消重新配对。
  - 如果双击配对按键，输出确认操作，当前选择的通道的配对指示灯将会白色快速呼吸，代表键盘进入重新配对状态
  - 键盘进入重新配对状态后，可以被搜索到。
  - Win10 及 Win11 支持迅速配对，需要在蓝牙设置界面勾选-> ☑️ 显示使用“迅速配对”进行连接的通知。

  ### VIA 按键

  | 按键功能 | 按键名称 | 按键 HEX |
  | :------: | :------: | :------: |
  | 配对按键 | WM_PEER  |  0x5700  |

  <span style="color:yellow;">**改变按键位置可以通过 VIA 中的 ANY 按键，填入按键名称或 HEX**</span>

## 4. 电池电量

- 电池具有一个指示灯用来指示电池状态
  | 指示灯状态 | USB 线缆 | 电池状态 |
  |:--------:|:-------:|:----------------|
  | 熄灭 | 已连接 | 已充满 |
  | 熄灭 | 未连接 | 电池电量较高 |
  | 设定灯效 | | 充电中 |
  | 红色高亮 | | 未检测到电池 |
  | 微弱红色常亮 | 未连接 | 电池电量较低 5% |

## 5. 固件升级

- 固件升级采用 U 盘升级方式，连接 USB 进入 Bootloader 以后，将会弹出一个 U 盘
- 进入 Bootloader 方式
  - 使用 VIA3，点击 EnterBootloader
  - 断电状态下（三模需要关闭开关），按住 ESC 插入 USB 线
  - 按进入 Bootloader 快捷键，具体按键位置打开 VIA 查看
  - 找到键盘 PCB 上的标注 UF2BOOT 的两个半圆，断电状态下将两个半圆短路并保持，插入 USB 线
  - 键盘升级失败自动进入 Bootloader 等待升级
- 升级固件命名原理，以 release_1.0.0+0_molly60_r1_nucleo_f411re_osc25_app.encrypted.signed.uf2 为例。
  - release 代表为正式发布版本
  - 1.0.0+0 代表 APP 版本
  - molly60_r1 代表键盘型号
  - nucleo_f411re_osc25 代表键盘采用的主控板
  - app 代表这是 APP 的固件
  - encrypted 代表固件已加密
  - signed 代表固件已签名
  - uf2 代表这是用于 U 盘更新的固件
- 单模键盘升级方式
  - 连接 USB 进入 Bootloader
  - 将用于 U 盘更新的固件复制到弹出的 U 盘中
  - 等待传输完成
  - 等待 10 秒-30 秒时间键盘将会自动重启，重启后升级完成，用户配置将被自动清除。
  - 如果重启过程中断电，将会导致升级失败，键盘将自动进入 Bootloader 模式等待重新升级。
  - <span style="color:yellow;">必须使用对应型号的 U 盘更新固件，使用其他的固件键盘将会拒绝升级</span>
- 三模键盘升级方式
  - 连接 USB 进入 Bootloader
  - 将用于 U 盘更新的主控固件以及无线模块固件两份文件都复制到弹出的 U 盘中
  - 等待传输完成
  - 等待 10 秒-30 秒时间键盘将会自动重启，重启后升级完成，用户配置将被自动清除。
  - 如果重启过程中断电，将会导致升级失败，键盘将自动进入 Bootloader 模式等待重新升级。
  - <span style="color:yellow;">必须使用对应型号的 U 盘更新固件，使用其他的固件键盘将会拒绝升级</span>
- 固件仓库连接
  - 国内访问
    - [主控固件](https://gitee.com/PhageStudio/firmware)
    - [无线模块固件](https://gitee.com/PhageStudio/wm_firmware)
  - 国外访问
    - [主控固件](https://github.com/PhageStudio/firmware)
    - [无线模块固件](https://github.com/PhageStudio/wm_firmware)
