# CR-6 Klipper configuration helper
A repository for Klipper firmware details for the Creality CR-6 printers.
This repo focusses on using Klipper and the Fluidd front end. In this configuration there is no need for OctoPi - Fluidd is way more responsive than OctoPi.

This has been tested on a BTT CR6 board by myself. The 4.5.2, 4.5.3 and ERA boards have been reported to work correctly.

Bear in mind that the CR-6 screen will not work (yet) with Klipper. The Fluid interface runs on any PC or mobile device in a standard web browser. As an alternative to the CR-6 screen KlipperScreen can be used. KlipperScreen runs on a series of Raspberry pi touch screens or on an old phone. Please check out the KlipperScreen GitHub for detailed info.
# What is Klipper
Klipper is a complete package to run your 3D printer consisting of a series of 3 main tools:
* Klippy: the service running the printer. Klippy interpretes the gcode, produces move commands and sends these to the printer. Klippy is where the heavy lifting is actully done.
* Moonraker: The broker. Moonraker handles the communication between Klippy, the visual front end(s) and hardware on the rPi.
* Fluidd, Mainsail, KlipperScreen: The display and interface of Klipper. These all 3 do more or less the same (you only need one of them but can run all 3 if wanted). They are the user interface where we put/upload our gcode files, do all calibrations, view a bed mesh and so on.

## Installation description:

Download the FluidPI or Mainsail image from the FluiddPI or Mainsail GitHub repo:
* https://github.com/cadriel/FluiddPI/releases/
* https://github.com/mainsail-crew/mainsailOS/releases/

The image is burned to an SDcard as described here:
* https://www.raspberrypi.org/documentation/installation/installing-images/

In the Raspberry pi Imager click **Chose OS** - **Use Custom** and select the just downloaded image. The rest of the install procedure is the same as on the raspberry page.

Setup WiFi if needed:
* https://www.raspberrypi.org/documentation/configuration/wireless/headless.md

The default user and passowrd are **pi** and **raspberry**. We need to change the default password.

Use MobaXterm (or equivalent) as it is an SSH client and remote file system in one to change the password and copy a few files onto the PI.

For people not used to work with Raspberry PI's https://www.raspberrypi-spy.co.uk/2018/12/remote-access-pi-using-mobaxterm/ has a good description how to get into the PI.

After starting up the PI FIRST run :
```bash
 sudo raspi-config
          set a new passowrd (optiion 1, S3), optionally change the system name
 sudo reboot
```

```
The latest Raspberry Pi imager V1.7.1 and higher let us set the username and password from the imager.
Also SSH can be enabled from the imager. This is done trough the gearwheel in the bottom right.
Further Mainsail can now be downloaded and installed directly trough the Raspberry Pi Imager
```

Now we need to copy the printer definition from this repo to the main printer.cfg file. Next to the printer.cfg file also the CR6.cfg file needs to be copied as it contains the specific things of the CR-6 style printers.

Select the config file appropriate for your motherboard and copy it to /home/pi/klipper_config

Rename the file to klipper.cfg

For all motherboards copy CR6.cfg to /home/pi/klipper_config

## Now we can make the actual firmware to be flashed to the printer mainboard

For convenience precompiled firmwares have been provided in the firmware directory. For now the BTT CR6 is tested and in use by myself, the stock Creality one is only confirmed to connect properly to my old 4.5.2 board. I have not actually printed with it. Please mention your mileage so I can confirm their working.

For all stock creality boards set the following config:
```bash
cd klipper
sudo service klipper stop
make menuconfig
            select microcontroller - "STM32"
            processor model "STM32F103" (is the default)
            Bootloader - "28KiB"
            Communication interface "Serial (on USART1 PA10/PA9)"

```

For the BTT CR6 board:
```bash
            tick "enable extra low-level configuration options"
            select microcontroller - "STM32"
            processor model "STM32F103" (is the default)
            Bootloader - "28KiB"
            communication interface USB (is the default)
            set GPIO pins to set at micro-controller startup to "!PA14"
```

The screen should now **exactly** look like this for the 4.5.2, 4.5.3 and ERA boards:

![image](https://user-images.githubusercontent.com/13643644/125346549-2e670f80-e35a-11eb-8940-d584d0bb70d7.png)


The screen should now **exactly** look like this for the BTT CR6 board:

![image](https://user-images.githubusercontent.com/13643644/123483020-6a823c80-d606-11eb-8dfc-3924ef9c4a7f.png)

```bash
          Q and save the configuration
make
```

The make command will build the actual firmware to be flashed to the printer and output the file /home/pi/klipper/out/klipper.bin

download klipper.bin, rename it to firmware.bin and write to an SD card

<span style="color:red">

## The SDcard HAS TO BE maximum 16 GB formatted as FAT32 with 4096 bits sector size.

## Any other format and the processor simply cannot read the SD card.
</span>

A micro-SDcard in and SD adapter works perfectly fine as long as the formatting is correct.

The last step is to give Klipper the address of the USB connection. To ontain the addres in the ssh terminal run:
```bash
ls /dev/serial/by-id/*
```

the output should looke like this:
```bash
/dev/serial/by-id/usb-Klipper_stm32f103xe_36FFD8054255373740662057-if00
```

Copy this to the [mcu] section of Klipper.cfg and replace the string present there
## From this point you should have a working Klipper installation.
You can proceed to the functionality checks as described on the Klipper site: https://www.klipper3d.org/Config_checks.html

