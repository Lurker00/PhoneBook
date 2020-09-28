# PhoneBook

[Anyware PhoneBook](https://www.kickstarter.com/projects/johnsheng/phonebook-turn-any-smartphone-into-a-laptop-computer) is a laptop looking device aimed as an external display/keyboard/touchpad for computers and smartphones.

It is advertised to work with Windows and Mac computers, Android and iOS smartphones. It is explicitly mentioned, that MediaTek based smartphones are not supported, though they do work somehow: I own only MediaTek and Rockchip based Android devices, and their behavior is identical.

I don't care about Apple devices, so don't expect here anything related to iOS or Mac.

## Hardware

Main board scans: [front](https://github.com/Lurker00/PhoneBook/blob/master/pictures/mainboard-front.jpg) and [back](https://github.com/Lurker00/PhoneBook/blob/master/pictures/mainboard-back.jpg).
* [Realtek RTD1195](https://www.realtek.com/en/products/communications-network-ics/item/rtd1195) - SoC.
* Toshiba TC58BVG0S3HTA00 - 128MB NAND flash.
* Nanya NT5CB64M16FP-DH - 128MB RAM (DRAM DDR3 64М*16).
* [ZB25VQ32](http://en.zbitsemi.com/intro/25.html) - 32Mbit SPI NOR flash.
* [LDR6282](http://www.legendary.net.cn/html/en/product/USB-C_PD/202005/1166.html) - USB Type-C controller with Display Port Alternate mode and PD support.
* [HD3SS460](https://www.ti.com/product/HD3SS460) - USB Type-C 4x6 Channels Alternate Mode MUX.
* TSUM052GDG-1 - FullHD HDMI+Type-C controller.
* [EM78F611](http://www.emc.com.tw/emc/en/Product/Product/detail/216) - USB HID microcontroller (keyboard, touchpad).
* [FE1.1S](http://www.jfd-ic.com/Documents/FE1.1s%20Data%20Sheet%20(Rev.%201.0).pdf) - USB 2.0 4-port hub.
* [MP26123](https://www.monolithicpower.com/en/documentview/productdocument/index/version/2/document_type/Datasheet/lang/en/sku/MP26123/) - 2/3-Cell Switching Li-ion Battery Charger.
* [ES7144S](http://www.everest-semi.com/pdf/ES7144S%20DS.pdf) - 24-Bit Stereo D/A Converter for PCM Audio.
* [MIX3015A](http://www.mixinno.com/?topclassid=11&classid=15) - Class D audio amplifier.

It has plenty of space inside, but the battery is only 4800mAh/48Wh (7.6V). It can work from the power supply without a battery.

It looks like it might have Wi-Fi and Ethernet for cheap, but it does not.

## Firmware basics

* Linux kernel 3.10.24 built from Android 4.4 sources.
* SDL for GUI.
* Realtek audio/video library (HDMI, hardware AV decoders).
* ActionsMicro AM8270 SDK (Miracast).

Most of the software looks like developed by [Sage Electronics Technology Co.](http://www.sagetech.hk/), the [developer of EZCast dongles](https://fccid.io/2AGM8-B20) and similar devices. Well, [EZCast](https://www.ezcast.com/) web site is copyrighted by [Actions Microelectronics Co., Ltd](https://www.actions-micro.com/), so, probably, they are either the same, or related. I have no idea how [Anyware](https://anywaretek.com/) is related to them...

## How does it work

The following information is based on quick (a few hours) reverse engineering of the firmware.

### Common

Keyboard, touchpad and mouse (which can be connected to one of the USB ports) are "exported" via Bluetooth: any device shall be paired via Bluetooth with PhoneBook before use. From the firmware code, a game controller can be connected and used as well.

Firmware forwards keyboard events not transparent way:
* some keycodes are handled directly (volume and brightness control)
* some key combinations may produce macro sequences (gaming mode)
* some keys are not handled (?) and, as such, are not passed to BT. For instance, <kbd>Windows</kbd> key produces no effect.

With Type-C connection, touchscreen is exported as Android Open Accessory (AOA) gadget.

### Windows

* HDMI: video and sound.
* Type-C: video and sound work. USB touchscreen device appears in the list of devices (VID_0525&PID_B4B1), but it does not actually work.

I tried only notebooks with touch screens. I've tried Control Panel settings to assign touch screens to displays, but Windows does not sense touches to PhoneBook.

### Android

**Note**: I have no Samsung or Huawei devices for which support for "desktop mode" mode is claimed. So, I can neither check nor comment this mode.

Video is streamed to a Miracast device. Android device can be charged from PhoneBook at the same time. I didn't hear that sound worked in Android.

If ADB is enabled on the device, the firmware establish connection and pushes jar-file and shell script to start a background process which should accept touch screen events from PhoneBook and simulate touches on Android devices. Check `/data/local/tmp/.sagevt/` for `sagevt.jar` and `script.sh`.

The firmware has a code to accept video stream via ADB connection, but I don't know whether it is actually used.

There is [Android application](https://play.google.com/store/apps/details?id=com.anyware.appctrl), which is, actually, only provides UI to PhoneBook firmware settings and updates. It has a "gamer" mode, which, in fact, only a launcher which turns PhoneBook into a mode which supports macros for keys.

For my smartphone ([Unihertz Atom](https://www.unihertz.com/atom.html), MTK), touchscreen does not work with the default settings. It works if I disable the 3rd item in Advanced Settings (shown in Chinese). With this option disabled, it simulate mouse events, so no multitouch.

### Linux

Tried Ubuntu 18.04 LTS via Type-C: video, sound and **touchscreen** work flawlessly! See common Type-C problem below.

## Problems found

### Keyboard

* <kbd>Windows</kbd> key has no effect.
* Some keys are available only in <kbd>Num Lock</kbd> off mode, but there is no way to make it the default state, nor even <kbd>Num Lock</kbd> state indicator.

### HDMI

Not all HDMI cables work. The reason is that some cables have not all ground pin wires. It is not a problem with most of devices, because they have shorted all ground lines together at their end. But PhoneBook has Pin 2 (DATA2 GND) left floating in the HDMI input socket, by the motherboard design! **The solutions are**:
* find a cable with all the required wires, or
* get a HDMIm-HDMIf adapter and solder pin 2 to the shield at the male side, or
* open PhoneBook and solder HDMI pin 2 (marked red in the [picture](https://github.com/Lurker00/PhoneBook/blob/master/pictures/HDMI-Pin2.jpg)) to the ground, or
* send your PhoneBook to the factory for repair :D

Seriously, I've made the adapter, and it works with the problematic cable and micro-HDMI pigtail that I have!

### Type-C

If a computer or smartphone consumes high current, PhoneBook periodically reboots, even being connected to an external power supply. I recommend to charge fully your device before using Type-C connection.

Though LDR6282 may support Power Delivery (PD), it seems its firmware has no support for PD. I've tried a mini-notebook ([Chuwi Minibook](https://chuwi.com/product/items/Chuwi-MiniBook.html)) which requests 15V/1.5A, and the picture on the PhoneBook is distorted, and it produces crackling sound. Looks like Minibook permanently tries to negotiate PD, which creates the noise. [One-Netbook OneMix 3](https://www.1netbook.com/onemix-3/) accepts 5V power source, but drains up to 2A, causing PhoneBook periodical reboots. I can only use this combo if OneMix 3 is fully charged, and external power supply is used.

The problem with PD looks like hardware design problem. Though LDR6282 supports different modes, its UART interface for its firmware updates is not connected to the SoC, and, as such, can't be changed with a usual OTA update.

### Touchscreen

I failed to see it working with Windows. The touchscreen by itself is good: touching the left bottom corner with any device connected, shows on-screen controls for display brightness/contrast and sound volume.

It works under Linux, which means, there should be a way to make it working under Windows as well.

Under Android, for me, it does not work via AOA gadget. With software simulation, it works as mouse simulation, i.e. no multitouch. In landscape, the software does not swap coordinates, which makes it useless, but this can be fixed in future software updates.

### Android application

* Some texts are in Chinese only, i.e. the 3rd item in Advanced Settings.
* The option to force landscape mode produces a message in Chinese and does not work, though it could be easily implemented on any device.
* The option to change display modes does not work on my devices, though I see no a reason why.
* On PhoneBook connection, it starts in "gamer" mode, and this behavior can't be disabled.
* It would be very useful if it initiated BT connection on USB connect, and BT disconnect on USB disconnect, but it does not.
* Software touch simulation should be fixed for landscape orientation, and should be improved for actual multitouch.

## Current conclusion

For me, it looks like a great idea, well implemented in hardware, but with several software flaws.

With Android devices, [`scrcpy`](https://github.com/Lurker00/scrcpy) looks like a better solution, and it could be implemented exactly the way like Anyware has implemented (yet not working) background process for touch simulation. [AOA-audio](https://github.com/rom1v/aoa-audio) project shows that it is possible to make sound working for Android as well.

Currently, if Anyware would fix touchscreen and sound issues, it would be very good. If they could fix Type-C power issue, even for those who can solder UART interface by himself, it would be even better. But making PhoneBook an open source platform would be *the* way to go!

Right now I'm waiting for UART-USB adapter to go inside the PhoneBook...
