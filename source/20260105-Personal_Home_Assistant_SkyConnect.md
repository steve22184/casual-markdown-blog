# Making Your Own Personal Home Assistant SkyConnect — modkam.ru

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_6-800x451.jpg)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_6.jpg)

I'd like to share a short story about creating a USB device, a functional analogue of the [Home Assistant SkyConnect.](https://www.home-assistant.io/skyconnect/).
Below is a story about the inspiration and how I managed to implement my plan.
DISCLAIMER This project was created solely for personal use and educational purposes. Do not use this information for commercial purposes.

First of all, I'll answer everyone who's asking, "Why did you make another stick? I already have one":
1. The SkyConnect stick is automatically updated via Home Assistant, keeping the firmware always up-to-date.

2. The ability to change the protocol to Thread (Matter) or Thread (Matter) + Zigbee, and back to Zigbee in a couple of clicks, while maintaining the functionality of the previous configuration (details here).

Now I'll tell you why and how all this developed.

In my home, I've been trying out various home automation systems for many years, periodically testing everything that existed at the time. Home Assistant was no exception. I tried using it more than once, or even ten times, a few years ago, but each time something led to disappointment. Everything changed when the developers decided to take things seriously and founded the company [Nabu Casa](https://www.nabucasa.com/). After that, the development began to take on more positive features and became user-friendly. Most importantly, the [Zigbee Home Automation](https://www.home-assistant.io/integrations/zha/) (ZHA) component received significant attention and finally evolved into a truly working solution. Thanks to this, a couple of years ago I switched all my devices exclusively to the ZHA component. [![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_21-e1706636529384.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_21-e1706636529384.png)

Having used Zigbee solutions for a long time, naturally, like many others, I tried many different Zigbee USB devices as a coordinator. And for some reason, the solution from Silicon Labs, a stick based on the [EFR32MG21](https://www.silabs.com/wireless/zigbee/efr32mg21-series-2-socs), piqued my interest the most. One of the first such sticks with this chip was from Sonoff, which wasn't yet a "Plus" model and didn't have a case, but it demonstrated fairly stable and consistent operation with the ZHA component. This turned out to be exactly what I was looking for. With the advent of a multiprotocol solution from Silicon Labs, I successfully tested it with Matter and Thread, and it turned out to be a very convenient solution, all in one.

Literally a short time later, new USB sticks from Sonoff appeared, as well as a USB stick from the creators of Home Assistant SkyConnect. At first glance, it's just like many other sticks based on the EFR32MG21 chip, but it turns out there was one very important difference: native support for Home Assistant. [![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_22.jpg)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_22.jpg)

I encountered this when, after another HA update, my Zigbee network suddenly stopped working. It turned out I needed to update the firmware on the coordinator. And then I realized I couldn't remember exactly how I did it. I couldn't even remember what model of stick I had, or what needed to be updated. At that moment, I remembered SkyConnect and how it didn't need to be updated manually, as it was easily done using Home Assistant, so I could forget about messing around with firmware.

When I tried to buy it, I discovered that it was quite popular and was out of stock everywhere. Since I had some eByte modules [E104-BT11](https://www.ru-ebyte.com/products/E104-BT11G-PCB) with the EFR32MG21A020F1024IM32 chip, exactly the same as the one used in SkyConnect, I thought about building my own version. I started gathering information and it turned out that it was quite feasible.

All information was taken from open sources.

1\. Configuration [of what is connected where](https://github.com/NabuCasa/silabs-firmware-builder/blob/main/EmberZNet/SkyConnect/0001-config-configure-usart-vcom-for-SkyConnect.patch) on the EFR32MG21 side.
2. Description of how exactly [it is determined in HA](https://github.com/home-assistant/core/blob/dev/homeassistant/components/homeassistant_sky_connect/manifest.json) that it is SkyConnect (PID/VID is standard from CP2102, the description should be SkyConnect v1.0.)
3. Description [of how add-ons determine that the connected stick is SkyConnect](https://github.com/home-assistant/addons/blob/master/silabs-multiprotocol/rootfs/etc/s6-overlay/scripts/universal-silabs-flasher-up) (manufacturer "Nabu Casa" and product "SkyConnect")

After reading the datasheets, I learned that USBXpress can be used to change the necessary parameters in the cp2102. All that's left is to draw a schematic. It's not that difficult, but I need to figure out how to lay out the boards beautifully. Of course, I could do it any way I want, but I wanted it to look good, so [@Jager](https://t.me/jager_f) helped me lay out this module.

The circuit is simple: [CP2102](https://www.silabs.com/interface/usb-bridges/classic/device.cp2102?tab=specs) + [E104-BT11](https://www.ru-ebyte.com/products/E104-BT11G-PCB), USB protection, several pull-up resistors, and power supply capacitors.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_5-800x488.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_5.png)

The PCB is two-layer, 1.6mm thick. This thickness is insufficient for good contact of the USB connector, so if you do not use a case, it is advisable to stick a piece of electrical tape on the back side.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_3.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_3.png)

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_4.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_4.png)

It was also immediately calculated that the manufacture of the board and assembly (except for the E104-BT11 module) can be ordered on [jlcpcb](https://jlcpcb.com/), for the really lazy, like me 🙂. The idea of ​​using a USB PCB, the same as in SkyConnect, also seemed reasonable.

I'm going further and ordering the printed circuit boards from [EasyEDA](https://easyeda.com/) with soldering pre-assembled components. The components are sized so you can solder them yourself, especially if you use a soldering/desoldering table for LED strips from AliExpress. All that's left to do is solder the module, again using this soldering table.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_7-800x354.jpg)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_7.jpg)

Turn on the module for the first time without the soldered module to ensure the power is correct and in the right place. I quickly solder the module and connect the programmer to the SWD/SWC (you can read how to do this in this [article](https://modkam.ru/2021/02/28/proshivka-stikov-efr32/))

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_8-800x413.jpg)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_8.jpg)

I launch Commander and see that the chip is detected correctly.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_10-800x245.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_10.png)

First, of course, you need to flash the bootloader. I remember seeing it on NabuCasa's GitHub before.
I downloaded the latest version (hmm, why two years ago? Oh well, I'll update later), erased the chip, and flashed it with the downloaded bootloader.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_23-800x326.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_23.png)

Next, I connected it to my computer's USB, and the system successfully recognized the CP210x USB-serial adapter. All that was left was to change the Manufacturer Name and Product Name. I opened USBXpress and changed the Product Name to "SkyConnect v1.0." I'm trying to change the Manufacturer Name, and my first problem is that USBXpress politely tells me that "it's impossible to change the Manufacturer Name on this chip, and that if I really want to, I can order one with the correct name directly from Silicon Labs" (but as far as I know, this is only for orders of 1,000 units). So, what a surprise.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_12-800x454.jpg)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_12.jpg)

Okay, I decided, we'll get back to that later, but for now it should work like any other USB Zigbee stick.

I open Elelabs_EzspFwUtility.py and run:

### `python3 Elelabs_EzspFwUtility.py probe -p /dev/tty.usbserial-0001`

I see the following in response:

### `2024/01/24 18:37:32 Elelabs_EzspFwUtility: Generic Zigbee EZSP adapter detected:`

### `2024/01/24 18:37:32 Elelabs_EzspFwUtility: Firmware: 7.1.1-17`

### `2024/01/24 18:37:32 Elelabs_EzspFwUtility: EZSP v9`

Everything looks good. I insert the stick into the Home Assistant server, and it happily reports that "SkyConnect v1.0 device found." This means I'm on the right track, but when I try to configure it, ZHA says the firmware is incorrect, but it refuses (or can't) to do anything about it, simply returning an "Unknown error." More obstacles...

I go to the [website](https://skyconnect.home-assistant.io/firmware-update/), connect the stick, select "flash", and it says "yes, there's firmware, let's update." When I try to update, it crashes.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_24-800x357.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_24.png)

After seeing that firmware-update uses universal-silabs-flasher, I install it.

I run:

### `universal-silabs-flasher --device /dev/tty.SLAB_USBtoUART probe`

I see the bootloader's response:

### `universal_silabs_flasher.flasher INFO Detected bootloader version '2.1.1'`

### `universal_silabs_flasher.flasher INFO Detected ApplicationType.GECKO_BOOTLOADER, version '2.1.1' at 115200 baudrate (bootloader baudrate 115200)`

Where's the firmware? After trying various combinations and trying all sorts of firmware, I decide I probably need to build my own, more recent bootloader.

I'm compiling the latest version 2.4.0 and uploading it via Commander

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_11-800x206.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_11.png)

Result

### `universal-silabs-flasher --device /dev/tty.SLAB_USBtoUART probe`

### `universal_silabs_flasher.flasher INFO Probing ApplicationType.GECKO_BOOTLOADER at 115200 baud`

### `universal_silabs_flasher.flasher INFO Detected bootloader version '2.4.0'`

### `universal_silabs_flasher.flasher INFO Detected ApplicationType.GECKO_BOOTLOADER, version '2.4.0' at 115200 baudrate (bootloader baudrate 115200)`

And lo and behold, all firmwares loaded via web flasher and responded without any issues. I connected it to ZHA, and everything worked. Well, that's a success, I thought. I tried a firmware with multiprotocol support, and naturally, the addon said, "I don't know who this is, so I won't flash it." The addon allows you to manually specify the firmware file to load, which is what I used.

Everything flashes, starts, and then... doesn't work, nothing but errors:

### `[19:12:40:603157] WARNING: In function 'core_process_rx_s_frame' in file /usr/src/cpc-daemon/server_core/core/core.c at line #818: Remote received a packet with an invalid checksum`

### `[19:12:40:632792] WARNING: In function 'core_process_rx_s_frame' in file /usr/src/cpc-daemon/server_core/core/core.c at line #818: Remote received a packet with an invalid checksum`

### `[19:12:40:660790] WARNING : In function 'core_process_rx_s_frame' in file /usr/src/cpc-daemon/server_core/core/core.c at line #818 : Remote received a packet with an invalid checksum`

But Zigbee works. What a miracle. I went to consult, and it immediately popped up that CTS should have been connected to RTS.
I cut the tracks, soldered the wires, and lo and behold, everything works!
The Manufacturer name remains undefeated, but the documentation says it can be changed, which isn't really true...
I decided to search for something old. I found a lot of projects on GitHub with source code, but almost all of them had placeholders for the Manufacturer Name (or Vendor name) entry, like this:

### `CP210x_STATUS CCP2102Device::SetManufacturerString(LPVOID lpvManufacturer, BYTE bLength, BOOL bConvertToUnicode) { return CP210x_FUNCTION_NOT_SUPPORTED; }`

So, they pointed me to [this project](https://github.com/VCTLabs/cp210x-program), which has the necessary keys and everything looks promising. I downloaded it, ran it, and again encountered a problem. I accept all the parameters, changed everything, but the Manufacturer Name remains Silicon Labs.
The morning is wiser than the evening. I wake up thinking that this must be stored in EEPROM, and that the utility can save the EEPROM and write it back. The idea comes to me to change it directly in the hex, and then someone suggests practically the same thing: the utility can change it directly in the file.
Saving the dump:

### `cp210x-program.py –read-cp210x -f eeprom-content.hex`

Changing:

### `cp210x-program.py -pc --set-product-string='SkyConnect v1.0' --set-vendor-string='Nabu Casa' --set-bus-powered=yes -F eeprom-content.hex > mod.hex`

Writing:

### `cp210x-program.py --write-cp210x -F mod.hex`

And finally, the long-awaited success. Everything is detected, switched, and flashed, both on the website and in add-ons, to multiprotocol and back. Zigbee.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_14-800x518.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_14.png)

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_15-800x372.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_15.png)

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_25-800x36 8.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_25.png)

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_17-800x356.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_17.png)

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_26-800x413.jpg)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_26.jpg)

**UPD: Update (08.02.2024)**

Initially, the EFR32MG21 chip was powered by the integrated cp2102 LDO. Since the default ZHA power setting for SkyConnect is 8 dBm, and the EFR32MG21 consumes ~50 mA in this mode, the cp2102 LDO's output current is more than sufficient. However, the EFR32MG21 chip itself can output 20 dBm, consuming 185 mA during transmission, requiring an additional LDO for power in this mode. For this reason, a second version with minimal power consumption was developed. changes.

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_30-800x485.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_30.png)

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_31 .png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_31.png)

[![](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_36-800x526.png)](https://modkam.ru/wp-content/uploads/2024/01/stick_v5_36.png)

For For those who want to repeat the process, all the necessary files for both versions are in the [archive](https://www.dropbox.com/scl/fi/10pzt7yxlr2yk6jw6ah3u/Zigbee_V5.rar?rlkey=lpr22czuc0k9yvhqa7774atq1&dl=0) + [case](https://www.thingiverse.com/thing:6531080).

Instructions on what and how to flash the EFR32 from [@goofyk](https://t.me/goofyk) are available [here](https://modkam.ru/2021/02/28/proshivka-stikov-efr32/)

You can discuss this article in [the largest Russian-language zigbee chat](https://t.me/zigbeer) on Telegram.
