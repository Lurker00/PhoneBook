# PhoneBook

[Anyware PhoneBook](https://www.kickstarter.com/projects/johnsheng/phonebook-turn-any-smartphone-into-a-laptop-computer) is a laptop looking device aimed as an external display/keyboard/touchpad for computers and smartphones.

It is advertised to work with Windows and Mac computers, Android and iOS smartphones. It is explicitly mentioned, that MediaTek based smartphones are not supported, though they do work somehow: I own only MediaTek and Rockchip based Android devices, and their behavior is identical.

I don't care about Apple devices, so don't expect here anything related to iOS or Mac.

## Hardware
* [Realtek RTD1195](https://www.realtek.com/en/products/communications-network-ics/item/rtd1195) - SoC.
* [LDR6282](http://www.legendary.net.cn/html/en/product/USB-C_PD/202005/1166.html) - USB Type-C controller with Display Port Alternate mode and PD support.
* TSUM052GDG-1 - FullHD HDMI+Type-C controller.
* [EM78F611](http://www.emc.com.tw/emc/en/Product/Product/detail/216) - USB HID microcontroller (keyboard)
* [MIX3015A](http://www.mixinno.com/userfiles/productfile/MIX3015A.pdf) - Audio amplifier.

## Firmware basics

* Linux kernel 3.10.24 built from Android 4.4 sources.
* SDL for GUI.
* Realtek audio/video library (HDMI, hardware AV decoders).
* ActionsMicro AM8270 SDK (Miracast).

Most of the software looks like developed by Sage Electronics Technology Co.

## How does it work

The following information is based on quick reverse engineering of the firmware.

### Common

Keyboard, touchpad and mouse (which can be connected to one of the USB ports) are "exported" via Bluetooth: any device shall be paired via Bluetooth with PhoneBook before use. From the firmware code, a game controller can be connected and used as well.

Firmware forwards keyboard events not transparent way:
* some keycodes are handled directly (volume and brightness control)
* some key combinations may produce macro sequences (gaming mode)
* some keys are not handled (?) and, as such, are not passed to BT. For instance, "Win" key produces no effect.

### Windows

* HDMI: video and sound. Not all HDMI cables work: it has to be find out what is the reason.
* Type-C: video and sound work. USB touchscreen device appears in the list of devices (VID_0525&PID_B4B1), but it does not actually work.

### Android
