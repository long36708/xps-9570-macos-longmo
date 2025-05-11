## 配置

| 型号  | XPS15-9570/MacBookPro15,1     | 版本   | Ventura 15.4.1                                  |
|:----|:------------------------------|:-----|:------------------------------------------------|
| 处理器 | Intel Core i5-8300H/i7-8750H  | 图形显卡 | UHD Graphics 630                                |
| 内存  | Micron 2400MHz DDR4 8GB + 16G | 硬盘   | Samsung PM961 512GB                             |
| 声卡  | Realtek ALC298                | 网卡   | 博通 Broadcom 802.11ac Network Adapter / 0BB8A618 |
| 内屏  | 夏普 SHP149A 15.5寸              | 鼠标   | HID-compliant 鼠标、PS/2 兼容鼠标                      |

### 不工作的设备

- 独立显卡
- 雷电
- 指纹
- 有线网卡

### 存在的问题

- ~~温度居高不下~~
- 有线网无法使用

### NTFS 写入

你需要将 `UUID=xxx none ntfs rw,auto,nobrowse` 添加到 `/etc/fstab` 中，**xxx** 为你的 NTFS 分区的 UUID。

如果你的 NTFS 分区装有 Windows，你需要先在 Windows 的 powershell 上运行 `powercfg -h off` 关闭 Windows 的休眠。

### 触摸板单双击延迟

- 关闭拖拽或者使用三指拖拽可以避免单击的延迟
- 关闭智能缩放可以避免双击的延迟

参考 [is-it-possible-to-get-rid-of-the-delay-between-right-clicking-and-seeing-the-context-menu](https://apple.stackexchange.com/a/218181)

### 重建缓存

进入桌面后，从终端运行`sudo kextcache -i /`，重建缓存

## PS/2 Keyboard  PS/2 键盘

The internal keyboard is a PS/2 device, but macOS does not support PS/2. This can be implemented with
VoodooPS2Controller.
内部键盘是 PS/2 设备，但 macOS 不支持 PS/2。这可以通过 VoodooPS2Controller 来实现。

## Testing Wi-Fi speed  测试 Wi-Fi 速度

默认情况下，AirportBrcmFixup 似乎将 802.11ac 的 Wi-Fi 信道宽度限制为 40MHz 而不是 80MHz，即使国家/地区代码默认为 US。

解决方法是使用引导标志 brcmfx-country=#a （config.plist） 禁用国家/地区代码设置。

如果您有支持 802.11ac 的接入点，请将 XPS 9570 放在它旁边，然后按住 Option 键单击 Wi-Fi 图标。

几分钟后，您应该会看到一条类似于 Channel： 165 （5 GHz， 80 MHz） with Tx 的线

低于该端口的速率为 800 Mbps 或更高。公寓楼中拥堵的 Wi-Fi 环境可能会 降低此速率。

## Battery status  电池状态

安装 VirtualSMC 自带的 SMCBatteryManager.kext 以获取电池状态。

如果之前已安装 ACPIBatteryManager，请确保已删除它

## Audio  音频

XPS 9570 上的音频基于 Realtek ALC298 音频编解码器，Dell 将其称为“带有 Waves MaxxAudio Pro 的 ALC3266-CG”。

默认情况下，ALC298 在 macOS 上不受支持，因此我们使用 AppleALC 来启用它。

笔记本电脑上的音频管道似乎具有独特的放大器和增益设置，因此我们需要将 layout-id 传递给与 XPS 9570 兼容的 AppleALT。

唯一运行良好的 ID 是 layout-id=30 或 32

### Installation  安装

- config.plist
    - Devices/Properties/PciRoot(0)/Pci(0x1f,3)
        - Comment: AppleALC layout-id for ALC298 on XPS 9570
        - layout=id=30
- 安装 AppleALC.kext

### Known problems/caveats  已知问题/注意事项

- 错误的 layout-id 会导致 kernel_task 进程中出现持续的 CPU 活动（30 应该没问题）
- 缺少音频输出设备会导致视频播放问题，即搜索时停顿
- HDMI 音频输出尚未正确配置

## Touch ID / Goodix 指纹传感器

根据 RehabMan 的说法， 无法将指纹传感器用于 Touch ID。

也许可以使用集成的汇顶指纹传感器，并通过 PAM （TBD） 启用。

由于我们使用的是 MacBookPro15,1 SMBIOS，因此 macOS 希望 Touch ID 可用，从而导致密码提示延迟。

现在可以使用 NoTouchID kext 禁用此功能。

### Installation  安装

安装 [NoTouchID.kext](https://github.com/al3xtjames/NoTouchID)

## Trackpad and touchscreen  触控板和触摸屏

Both the Synaptics trackpad and Wacom touchscreen are I2C devices that can be driven with VoodooI2C.

VoodooPS2Controller also provides basic trackpad support, but the quality is not as good.

Synaptics 触控板和 Wacom 触摸屏都是 I2C 设备，可以使用 VoodooI2C。

VoodooPS2Controller 也提供了基本的触控板支持，但质量没有那么好。

### Installation  安装
- Include [VoodooI2C](https://github.com/alexandred/VoodooI2C)’s [VoodooI2c.kext](https://github.com/bavariancake/XPS9570-macOS/blob/master/EFI/CLOVER/kexts/Other/VoodooI2C.kext) and [VoodooI2CHID.kext](https://github.com/bavariancake/XPS9570-macOS/blob/master/EFI/CLOVER/kexts/Other/VoodooI2CHID.kext)

- Include [SSDT-I2C.aml](https://github.com/bavariancake/XPS9570-macOS/blob/master/EFI/CLOVER/ACPI/patched/SSDT-I2C.aml) ([SSDT-I2C.dsl](https://github.com/bavariancake/XPS9570-macOS/blob/master/EFI/CLOVER/ACPI/patched/SSDT-I2C.dsl))

[config.plist](https://github.com/bavariancake/XPS9570-macOS/blob/master/EFI/CLOVER/config.plist)

-   `ACPI/DSDT/Patches/Item n`  
    `ACPI/DSDT/补丁/项目 n`
    -   `Comment: Rename _CRS to XCRS, pair with SSDT-I2C.aml`
    -   `Key=Find, Type=Data, Value=<144c045f 43525300 a00e954f 5359530b dc07a453 424649>`
    -   `Key=Replace, Type=Data, Value=<144c0458 43525300 a00e954f 5359530b dc07a453 424649>`
