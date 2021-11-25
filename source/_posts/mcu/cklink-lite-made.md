---
title: 基于STM32最小开发板制作CKLink Lite调试器
categories: 炼丹记
date: 2021年11月17日 14点09分
tags: 单片机
---

# 开始
双十一买了个W806的板子，很便宜，可以ISP刷程序，其实平时够用，但是还是感觉有调试的感觉不错。

# 制作
工具地址 https://github.com/henrycoding/cklink-lite

SDK（选择T-Head）： https://occ.t-head.cn/community/download?id=616215132330000384  

群里面有CKLINK_LITE的固件，其实直接烧录到STM32F103就ok了，我这里用J-Flash烧录,烧录步骤如下

1. Target Interface选择SWD，CPU选择Device，然后按三个点选STM32F103RC
2. Connect, Erase Chip, Program and Verify
3. 拔掉SWD调试器，然后将STM32F103的USB接口接到电脑上

# 问题

连上电脑以后设备管理器显示设备，但是T-HEAD-DebugServer死活没法链接成功，打开 DebugConsole，提示如下

{% img /image/error_cklink.png %}

由提示可知是USB的问题，使用Zadig工具替换驱动即可解决问题， 替换的时候首先点Options -> List All Devices,
然后选择C-Sky开头的，驱动选 libusb-win32 ，然后点击Replace即可

{% img /image/zadig_use.png %}

{% img /image/cklink_zadig.png %},
替换完成后，再次打开T-head-debug-server， 即可

{% img /image/debug-server.png %},



特别注意，线的链接顺序

|  STM32   | W806  |
|  ----  | ----  |
| A0  | RST |
| A1  | PA1 |
| A5  | PA4 |
| 3V3 | 3V3 |
| GND | GND |

 
