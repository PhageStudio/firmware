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

- FS USB模式：键盘通过USB接口连接到计算机。在FS USB模式下，回报率为1000Hz，可以切换为6键和全键无冲模式。

- HS USB模式：键盘通过USB接口连接到计算机。在HS USB模式下，回报率为8000Hz，默认全键无冲模式。
<span style="color:yellow;">**需要支持HS USB模式**</span>

- BLE蓝牙模式：键盘通过蓝牙连接到设备，提供无线连接的便利性。在BLE蓝牙模式下，回报率为133Hz。BLE版本为5.4，为了得到最佳效果，请使用支持2M PHY的蓝牙接收器，宣传页有兼容5.0 5.1字样的USB接收器很可能不支持。推荐使用AX200，AX210，BE200。<span style="color:yellow;">**需要支持BLE蓝牙模式**</span>

- LLPM模式：2.4G无线模式，支持低延迟和1000Hz的回报率。LLPM模式提供稳定的无线连接，并提供较低的响应延迟，~1mS。
<span style="color:yellow;">**需要支持LLPM模式，并且需要配套LLPM接收器**</span>

- ESB模式：2.4G无线模式，支持超低延迟和4000Hz的回报率。ESB模式提供极低的响应延迟，~250uS。<span style="color:yellow;">**需要支持ESB模式，并且需要配套ESB接收器**</span>

- <span style="color:yellow;">**不存在键盘同时支持LLPM和ESB模式**</span>

## 2. 模式切换
- 键盘默认为自动模式，模式切换有三种，自动模式，强制USB模式，强制无线模式。模式切换按键位置请打开VIA查看。
    ### 模式切换表
    | USB线缆 | 自动模式 | 强制USB | 强制无线 |
    |:------:|:------:|:------:|:------:|
    | 连接USB | USB模式 | USB模式 | 无线模式 |
    | 连接电源 | 无线模式 | USB模式 | 无线模式 |
    | 未连接   | 无线模式 | USB模式 | 无线模式 |
    ### VIA按键
    | 按键功能 | 按键名称 | 按键HEX | 
    |:-------:|:-------:|:------:|
    | 自动模式 | OU_AUTO | 0x7C20 |
    | 强制USB | OU_USB   | 0x7C21 |
    | 强制无线 |  0x7C22  | 0x7C22 |

    <span style="color:yellow;">**改变按键位置可以通过VIA中的ANY按键，填入按键名称或HEX**</span>

- 部分键盘具有一个三挡的开关，分别为USB，BLE蓝牙，2.4G模式（LLPM、ESB）。
    | 开关档位 | 功能含义   | 备注                                               |
    |:------:|:---------:|:-------------------------------------------------|
    |  USB   |关闭电源    | 无线模式选择为BLE ，USB线缆连接电源会自动开启供电，关闭电源可以大幅降低待机功耗 |
    |  BLE   |无线模式BLE | 无线模式选择为BLE，可以切换多个通道，长时间无操作进入待机，但功耗相比关闭电源高 |
    |  2.4G  |无线模式2.4G| 无线模式选择为2.4G，具有唯一配对通道，长时间无操作进入待机，但功耗相比关闭电源高 |

    <span style="color:yellow;">**目前还不支持按键选择无线BLE和2.4G模式**</span>

## 3. 无线配对及通道选择
- 无线的配对以及通道选择目前是一个唯一的按键进行控制，称为配对按键。配对按键位置请打开VIA查看。
- 键盘具有配对指示灯，用来指示不同通道的配对状态。
    ### 配对按键操作
    | 按键操作 | 按键功能 |
    |:-------:|:-------:|
    | 单击    | 通道选择  |
    | 长按    | 重新配对  |
    | 双击    | 确认操作  |

    ### 配对指示灯状态
    | 指示灯状态   | 状态    | 备注 |
    |:----------:|:-------:|:-------------------------------------------|
    | 熄灭        | 未连接  | 复用模式下，配对指示灯将会在3秒后切换到键盘状态指示灯 |
    | 白色常亮     | 已连接  | 复用模式下，配对指示灯将会在3秒后切换到键盘状态指示灯 |
    | 白色缓慢呼吸 | 等待连接 |                                              |
    | 白色快速呼吸 | 等待配对 | 30秒后配对仍未成功恢复原状态                     |
    | 黄色快速闪烁 | 等待确认 | 等待用户进一步操作，无操作10秒后恢复原状态         |
    | 红色常亮    | 严重错误 | 需要进入BootLoader重新刷机恢复                  |

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
    - Win10及Win11支持迅速配对，需要在蓝牙设置界面勾选->  ☑️显示使用“迅速配对”进行连接的通知。


    ### VIA按键
    | 按键功能 | 按键名称 | 按键HEX | 
    |:-------:|:-------:|:------:|
    | 配对按键 | 0x5700 | 0x5700 |

    <span style="color:yellow;">**改变按键位置可以通过VIA中的ANY按键，填入按键名称或HEX**</span>

## 4. 电池电量
- 电池具有一个指示灯用来指示电池状态
    | 指示灯状态 | USB线缆 | 电池状态          | 
    |:--------:|:-------:|:----------------|
    | 熄灭      | 已连接    | 已充满          |
    | 熄灭      | 未连接    | 电池电量较高     |
    | 绿色呼吸   |          | 充电中          |
    | 红色闪烁   |          | 未检测到电池     |
    | 5秒闪烁1次 | 未连接    | 电池电量较低 20% |

## 5. 固件升级
- 固件升级采用U盘升级方式，连接USB进入Bootloader以后，将会弹出一个U盘
- 进入Bootloader方式
    - 使用VIA3，点击EnterBootloader
    - 断电状态下（三模需要关闭开关），按住ESC插入USB线
    - 按进入Bootloader快捷键，具体按键位置打开VIA查看
    - 找到键盘PCB上的标注UF2BOOT的两个半圆，断电状态下将两个半圆短路并保持，插入USB线
    - 键盘升级失败自动进入Bootloader等待升级
- 升级固件命名原理，以 release_1.0.0+0_molly60_r1_nucleo_f411re_osc25_app.encrypted.signed.uf2 为例。
    - release 代表为正式发布版本
    - 1.0.0+0 代表APP版本
    - molly60_r1 代表键盘型号
    - nucleo_f411re_osc25 代表键盘采用的主控板
    - app 代表这是APP的固件
    - encrypted 代表固件已加密
    - signed 代表固件已签名
    - uf2 代表这是用于U盘更新的固件
- 单模键盘升级方式
    - 连接USB进入Bootloader
    - 将用于U盘更新的固件复制到弹出的U盘中
    - 等待传输完成
    - 等待10秒-30秒时间键盘将会自动重启，重启后升级完成，用户配置将被自动清除。
    - 如果重启过程中断电，将会导致升级失败，键盘将自动进入Bootloader模式等待重新升级。
    - <span style="color:yellow;">必须使用对应型号的U盘更新固件，使用其他的固件键盘将会拒绝升级</span>
- 三模键盘升级方式
    - 连接USB进入Bootloader
    - 将用于U盘更新的主控固件以及无线模块固件两份文件都复制到弹出的U盘中
    - 等待传输完成
    - 等待10秒-30秒时间键盘将会自动重启，重启后升级完成，用户配置将被自动清除。
    - 如果重启过程中断电，将会导致升级失败，键盘将自动进入Bootloader模式等待重新升级。
    - <span style="color:yellow;">必须使用对应型号的U盘更新固件，使用其他的固件键盘将会拒绝升级</span>
- 固件仓库连接
    - 国内访问
        - [主控固件](https://gitee.com/PhageStudio/firmware)
        - [无线模块固件](https://gitee.com/PhageStudio/wm_firmware)
    - 国外访问
        - [主控固件](https://github.com/PhageStudio/firmware)
        - [无线模块固件](https://github.com/PhageStudio/wm_firmware)
