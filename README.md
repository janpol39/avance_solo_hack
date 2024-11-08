This project involves hacking the firmware of the Avance Solo medical VAC device by the manufacturer Mölnlycke.
https://www.molnlycke.co.uk/products-solutions/avance-solo/

The manufacturer has set a 14-day usage limitation on their device, despite the fact that the physical vacuum pump doesn’t wear out after this period. This artificial limitation appears to be motivated by the manufacturer's greed. I believe that healthcare, as well as medical equipment, should be accessible to everyone. Additionally, the need to purchase a new device every 14 days generates a significant amount of electronic waste, which is highly unecological. For these reasons, reverse engineering of this device was carried out, its firmware was obtained, and the 14-day limitation imposed by the manufacturer was removed.

The firmware is fully functional and can be loaded onto the device from its first day of operation; however, I recommend installing it after your device has operated for 14 days, as it has only been tested on my device.

Firmware Process:
The device uses an STM32F051C8T6 microcontroller, which can be programmed via the SWD interface.

To flash the device, you’ll first need to solder the SWD connector contacts (in the future, I plan to provide a 3D printer schematic to create a programmer with spring-loaded pins, avoiding soldering). In the screenshot, you can see that the lower row of exposed pins is for the SWD connector, while the upper row is for the controller’s UART (which might be useful to you later but is not needed for the flashing process). Connect any SWD programmer (for example, ST-Link or RPi probe) to the exposed pins.

For flashing, we’ll use the RPi probe programmer, software to control the SWD bus (OpenOCD), and any Linux distribution as the host.

The controller’s firmware is protected by STM’s standard firmware protection mechanism, RDP level 1 (which can be easily bypassed, but that’s a separate topic). Therefore, to reflash the controller, you first need to erase the old firmware to disable the RDP protection. Then, flash the controller with the hacked firmware file, avance_solo_hacked.bin.

Commands:
Command to erase the old firmware:
sudo openocd -f interface/cmsis-dap.cfg -f target/stm32f0x.cfg -c "halt; stm32f1x mass_erase 0"

Command to write the new firmware:
sudo openocd -f interface/cmsis-dap.cfg -f target/stm32f0x.cfg -c "init; reset halt; flash write_image erase <path_to_bin_file>/avance_solo_hacked.bin 0x08000000; reset run; shutdown"
