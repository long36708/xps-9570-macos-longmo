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

- 温度居高不下
- 有线网无法使用

### NTFS 写入

你需要将 `UUID=xxx none ntfs rw,auto,nobrowse` 添加到 `/etc/fstab` 中，**xxx** 为你的 NTFS 分区的 UUID。

如果你的 NTFS 分区装有 Windows，你需要先在 Windows 的 powershell 上运行 `powercfg -h off` 关闭 Windows 的休眠。

### 触摸板单双击延迟

- 关闭拖拽或者使用三指拖拽可以避免单击的延迟
- 关闭智能缩放可以避免双击的延迟

参考 [is-it-possible-to-get-rid-of-the-delay-between-right-clicking-and-seeing-the-context-menu](https://apple.stackexchange.com/a/218181)
