# ESPhome configs

**h801_letto**: the RGBCCT LED bar on my bed.  
**sonoff_lampadario_luca**: the Sonoff Mini that controls my room's light.  
**esp32_ble_hub**: BLE hub made with an ESP32, mainly used to read data from a
[Xiaomi Mijia LYWSD03MMC](https://esphome.io/components/sensor/xiaomi_ble.html#lywsd03mmc)
with [this firmware](https://github.com/atc1441/ATC_MiThermometer).  
**esp32_ble_zb_hub**: as above, plus Zigbee functionality, I use it with this [Raspberry Pi hat](https://github.com/egony/cc2652p_E72-2G4M20S1E/wiki/Home-EN#shield-pi-hat-for-single-board-computers)
since my new server has no GPIO unlike my previous Raspberry Pi 3.  
**sonoff_zbbridge**: ESPhome firmware for the Sonoff Zigbee bridge. [Using this device is theoretically not recommended](https://github.com/home-assistant/home-assistant.io/issues/17170),
and it has been giving me many issues. If you really want to, I suggest
following [the Tasmota procedure first](https://zigbee.blakadder.com/Sonoff_ZBBridge.html), then moving to ESPhome.  

### What happened to the desk LED bar?
I moved the SMD RGBCCT strip from the desk to my bed, and replaced the one over
my desk with a CoB CCT strip and a MagicHome controller. Since these
controllers switched from their previous ESP8266 to new RISC-V controllers,
at times BL602 and others LF686, I had to move to [OpenBeken](https://github.com/openshwprojects/OpenBK7231T_App).  
OpenBeken has way less features than ESPhome, is not as intuitive, and AFAIK it
has no Home Assistant APIs, relying instead on MQTT. I had no choice, but in
the end it gets the jobs done so I can't complain.  
For reference, my config has GPIO3 set on PWM and set as channel 0 aka cold
white, and GPIO4 on PWM set as channel 1 aka warm white. The firmware
automatically set itself as a CCT controller.  
CoB LEDs slap, btw, you should get some. [This is the strip I bought](https://aliexpress.com/item/1005005820131997.html),
the CCT 608LEDs 10mm 12V version, fits my alluminum channels no problem.  

### ESP32 BLE+Zigbee bridge
For my first hub I flashed a CC2531 dongle with a CC2530 firmware [from here](https://github.com/Koenkk/Z-Stack-firmware/tree/master/coordinator).
Can't remember which one, but probably the [Z-Stack_Home_1.2 Default](https://github.com/Koenkk/Z-Stack-firmware/blob/master/coordinator/Z-Stack_Home_1.2/bin/default/CC2530_DEFAULT_20201127.zip).  
Should you want to replicate this, please get a CC2530 with properly broken out
pins since the pins on the side of the CC2531 USB dongle are incredibly small
and your wires WILL break, or a CC2530 + CC2591/CC2592 that would probably be
an even better choice for range; that said, those are old devices and can only
run Zigbee 1.2 firmware properly, you *can* flash a 3.0 firmware but you'll
probably encounter some issues. Consider something else like [this](https://github.com/egony/cc2652p_E72-2G4M20S1E/wiki/Home-EN#shield-pi-hat-for-single-board-computers),
if you need a wireless hub you just need to connect the TX and RX wired as
needed to the ESP32: as a matter of fact, this is my current solution.
I used to have the module attached to my Raspberry Pi, but I then moved to an
X86 mini PC and I didn't want to buy an USB coordinator.  

However, if you already have a CC2531 dongle, after flashing it with the CC2530
firmware, solder two wires on the IO connector, on the 0.2 and 0.3 pins, and
other two on the GND and VCC (3.3V) headers (maybe you'll have more success if
you remove them and use the pads). You could also solder on the USB pins and
use 5V instead if you feel like it, but I prefer not to mess with the USB port.  

![](CC2531_pins.png)  

I then added this in my zigbee2mqtt config (I'd prefer ZHA but it doesn't
detect double presses on devices that don't have them in their firmware):

```
serial:
  port: 'tcp://10.10.0.250:6638' # change as needed
```

Originally I only this hub to keep my RasPi's USB ports free (especially since
the Pi 3's USB cripple the ethernet port, even though the dongle doesn't transmit
enough data for it to be a problem). Maybe I'll evolve this into a "superhub"
with BLE, Zigbee, Matter, Z-Wave, and even 433MHz, all in a single PCB with no
ugly wires, but it's not gonna happen any time soon. Feel free to [donate on my Ko-Fi](https://ko-fi.com/exentio)
if you want me to work on that.  

![](not_a_fire_hazard.jpg)  

Huge thank you to Oxan for his stream server library! [oxan/esphome-stream-server](https://github.com/oxan/esphome-stream-server)
