# Hardware

Main board scans: [front](mainboard-front.jpg) and [back](mainboard-back.jpg).

PhoneBook has plenty of space inside, but the battery is only 4800mAh/48Wh (7.6V). It can work from the power supply without a battery.

The power adapter is 12V/2A, 3.5x1.35mm connector. I've soldered a cable from [12V PD decoy](https://www.aliexpress.com/item/4000456824088.html), to charge from PD charger or power bank.

It looks like it might have Wi-Fi and Ethernet for cheap, but it does not.

## Chips
* Anyware AW1 - looks like the main SoC. The firmware is built for [Realtek RTD1195](https://www.realtek.com/en/products/communications-network-ics/item/rtd1195), but RTD1195 [looks very different](https://www.cnx-software.com/wp-content/uploads/2014/11/902_board_Large.jpg).
* Anyware AWB1 - Bluetooth controller.
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
* [AON7401](http://www.aosmd.com/res/data_sheets/aon7401.pdf) - MOSFET for Type-C VDC (5V only).

## Connectors

* J2 - UART of LDR6282 for its firmware update.
* J11 - UART interface of the SoC (AW1): TX/GND/RX, 115200 bps, 3.3V.
* J14 - JTAG of AWB1.
* J16 - USB hub (FE1.1S) port in parallel with EM78F611.
* J17 - looks like UART of TSUM052GDG-1 (5V), but I've seen no output.
* J18 - UART of the AWB1: TX/GND/RX, 115200 bps, 3.3V.

I've used [this CP2102 board with microUSB connector](https://www.aliexpress.com/item/32716109900.html): it works great and easily can be installed inside the PhoneBook.

## Test points

* TX5/RX5 - UART I/O between AW1 and AWB1.
* DN1/DP3 - USB 2.0 data lines of the Type-C connector to AW1.
