# Connectors

* J2 - UART of LDR6282 for its firmware update.
* J11 - UART interface of the SoC (AW1): TX/GND/RX, 115200 bps.
* J14 - JTAG of AWB1.
* J16 - USB hub (FE1.1S) port in parallel with EM78F611.
* J17 - looks like UART of TSUM052GDG-1, but I've seen no output.
* J18 - UART of the AWB1: TX/GND/RX, 115200 bps.

UARTs require 3.3V levels. I've used [this CP2102 board with microUSB connector](https://aliexpress.com/item/32716109900.html): it works great and easily can be installed inside the PhoneBook.

# Test points

* TX5/RX5 - UART I/O between AW1 and AWB1.
* DN1/DP3 - USB 2.0 data lines of the Type-C connector to AW1.
