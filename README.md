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

* HDMI: video and sound.
* Type-C: video and sound work. USB touchscreen device appears in the list of devices (VID_0525&PID_B4B1), but it does not actually work.

### Android

**Note**: I have no Samsung or Huawei devices for which support for "desktop mode" mode is claimed. So, I can neither check nor comment this mode.

Video is streamed to a Miracast device. There is a mention of Android Open Accessory (AOA) mode, but, currently, I have no idea how it is used and whether does it work. Android device can be charged from PhoneBook at the same time. I didn't hear that sound worked in Android.

If ADB is enabled on the device, the firmware establish connection and pushes jar-file and shell script to start a background process which should accept touch screen events from PhoneBook and simulate touches on Android devices. Check `/data/local/tmp/.sagevt/` for `sagevt.jar` and `script.sh`.

The firmware has a code to accept video stream via ADB connection, but I don't know whether it is actually used.

## Problems found

### HDMI

Not all HDMI cables work: it has to be find out what is the reason.

### Type-C

If a computer or smartphone consumes high current, PhoneBook periodically reboots, even being connected to an external power supply. I recommend to charge fully your device before using Type-C connection.

Though LDR6282 may support Power Delivery (PD), it seems its firmware has no support for PD. I've tried a mini-notebook (Chuwi Minibook) which requests 15V/1.5A, and the picture on the PhoneBook is distorted, and it produces crackling sound. Looks like Minibook permanently tries to negotiate PD, which creates the noise. OneNetbook One Mix 3 accepts 5V power source, but drains up to 2A, causing PhoneBook periodical reboots. I can only use this combo if Mix 3 is fully charged, and external power supply is used.

The problem with PD looks like hardware design problem. Though LDR6282 supports different modes, its UART interface for its firmware updates is not connected to the SoC, and, as such, can't be changed with a usual OTA update.

### Touchscreen

I failed to see it working neither with Windows, nor with Android. The touchscreen by itself is good: touching the left bottom corner with any device connected, shows on-screen controls for display brightness/contrast and sound volume. It looks like touches are not forwarded to the connected devices at all.

## Current conclusion

For me, it looks like a great idea, well implemented in hardware, but with several software flaws.

With Android devices, [`scrcpy`](https://github.com/Lurker00/scrcpy) looks like a better solution, and it could be implemented exactly the way like Anyware has implemented (yet not working) background process for touch simulation. [AOA-audio](https://github.com/rom1v/aoa-audio) project shows that it is possible to make sound working for Android as well.

Currently, if Anyware would fix touchscreen and sound issues, it would be very good. If the could fix Type-C power issue, even for those who can soldet UART interface by himself, it would be even better. But making PhoneBook an open source platform would be great!
