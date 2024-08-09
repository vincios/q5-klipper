# Install Klipper on a Flsun Q5

Reference guide: https://emanuelebono.medium.com/flsun-q5-install-klipper-and-fluidd-2de2204a201c

### Use drivers in UART mode
Follow the official [video](https://www.youtube.com/watch?v=7ShcFKXrVHo).


- Remove the drivers from the motherboard
- For each of them solder together the R8 and UART pin and connect a Dupont wire as shown in the video.

    > Note for BTT TMC2209

- Before inserting again the TMC2208 into the motherboard, **remove all the jumpers as shown in the official video at 1:12**
- Insert the drivers, and connect them


Connect the drivers following this schema:

```
X  ->  PC7
Y  ->  PC13
Z  ->  PA3
E1 ->  PA1
```

![UART pins](https://onedrive.live.com/embed?resid=2A6BE858ABEEB97B%21653098&authkey=%21AICl_FDI0-FI77U&width=1024)


See also this [guide](https://travis90x.altervista.org/tmc2209-tmc2208-uart-mks-robin-nano-v1-x-v1-1-v1-2/?doing_wp_cron=1678654770.1136291027069091796875) for other examples.

## Install software
We'll use [KIAUH](https://github.com/dw-0/kiauh) since it is the [recommended](https://docs.fluidd.xyz/installation/kiauh) method to install Fluidd.

### RPi OS
- Install `Raspberry Pi OS Lite 64bit` with the [official Raspberry Pi Imager](https://www.raspberrypi.com/software/).

> [!IMPORTANT]
> Make sure to go into the Advanced Option (the cog icon in the lower left corner of the main menu) and **enable SSH** and configure Wi-Fi/user.

- Insert the SD card and start the RPi

- Login with SSH and install updates

    ```sh
    sudo apt update
    sudo apt full-upgrade
    ```

- Run the raspi-config tool: `sudo raspi-config`
- Change your Password via 'System Options -> Password`
- Change your Timezone via 'Localization Options -> Timezone`
- Change your Locale via `Localization Options -> Locale`
- Optionally change your hostname via `System Options -> Hostname`
- Swap ports used by GPIO and Bluetooth 

  - `sudo nano /boot/firmware/config.txt`
  - Move the cursor to the very end and add:

    ```
    dtoverlay=miniuart-bt
    ```
- Disable the serial console 

  - `/boot/firmware/cmdline.txt`
  - Look for the following string (text) and delete it

    ```
    console=serial0,115200
    ```

- **IMPORTANT!** Enable Serial
  - `sudo raspi-config` 
  - Go to `Interfacing Options -> I6 — Serial` 
  - A couple of questions will be asked, **answer No at the first one** and Yes for the second one. Then go down to finish and reboot.

### KIAUH
Follow the official [guide](https://github.com/dw-0/kiauh?tab=readme-ov-file#-download-and-use-kiauh).

Run `kiauh.sh` and under the `Install` menu install:

- Klipper
- Moonraker
- Fluidd


## Connection
Connect the RPi to the mainboard following this scheme:

```
GPIO 6 (GND) → BLTOUCH GND
GPIO 4 (5v) → BLTOUCH 5v
GPIO 14 (TX) → WiFi RX-PA10
GPIO 15 (RX) → WiFi TX-PA9
```

![RPi Connection](https://miro.medium.com/v2/resize:fit:720/format:webp/1*0kJ7BhldbzURpcbtujKJwA.jpeg)


> [!IMPORTANT]
> If you power your RPi from an external source, make sure to share a common ground between the RPi and the MCU (i.e. leave the `GPIO 6 (GND) → BLTOUCH GND` wire connected).

## Build firmware
From your RPi home directory, run

```sh
$ cd klipper
$ make menuconfig
```

Configure as follow

```
Enable extra low-level configuration setup
Micro-controller Architecture -> STMicroelectronics SMT32
Processor model -> STM32F103
Bootloader offset -> 28Kib bootloader
Clock Reference -> 8 MHz crystal
Communication interface -> Serial (on USART1 PA10/PA9)
```

Leave the other ones untouched.

![Settings](https://miro.medium.com/v2/resize:fit:4800/format:webp/1*GkX3xWITFWZtppdcFqESCw.png)

Press `Q` and save.

Run the following commands

```sh
$ make
$ ./scripts/update_mks_robin.py out/klipper.bin out/Robin_nano.bin
```

Time to transfer the bin file from the Raspberry Pi to our local machine. You can use tools like WinSCP, or you can use just the scp command from your local machine:

```sh
scp raspi@q5:~/klipper/out/Robin_nano.bin <YOUR_LOCAL_PATH>
```

Now we have our bin file:

- Copy the bin into the root of the second SD card
- Power off the 3d printer
- Insert the SD card into the 3d printer motherboard
- Power on, the printer will start the flashing and the screen will show you the progress. It will be stuck at 100%, which is normal because as I said in the intro the Flsun stock display is not supported


### Configure
Upload the local `printer.cfg` file to the RPi `~/printer_data/config` folder. 

You can use WinSCP as well, or scp


```sh
scp ./priter.cfg raspi@q5:~/printer_data/config
```

Reboot the printer. Access Fluidd web page and everything should work as expected.

Now you can proceed to the calibration steps.

## Troubleshooting
### Moonraker fail to start
[Ref](https://github.com/Arksine/moonraker/issues/333#issuecomment-1013991512)


If Moonraker fail to start and `sudo journalctl -xe -u moonraker.service` show some missing libs error, run 

```sh
~/moonraker/scripts/install-moonraker.sh
```

To make sure dependencies are updated.


## Bonus: hardware boot button / led indicator for RPi
[Guide](https://gist.github.com/lbussy/9e81cbcc617952f1250e353bd42e7775)