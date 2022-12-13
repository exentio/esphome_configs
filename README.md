# ESPhome configs

**h801_letto**: the RGBW LED bar on my bed.  
**h801_scrivania**: the RGBWW LED bar over my head on the desk.  
**sonoff_lampadario_luca**: the Sonoff Mini that controls my room's light.  
**esp32_ble_hub**: BLE hub made with an ESP32, mainly used to read data from a
[Xiaomi Mijia LYWSD03MMC](https://esphome.io/components/sensor/xiaomi_ble.html#lywsd03mmc)
with [this firmware](https://github.com/atc1441/ATC_MiThermometer).  
**esp32_ble_zb_hub**: as above, plus a CC2531 that adds Zigbee functionality, I
used to use it with an [OSRAM Smart+ Switch Mini](https://zigbee.blakadder.com/OSRAM_AC0251x00NJ.html)
before moving to this [Raspberry Pi hat](https://github.com/egony/cc2652p_E72-2G4M20S1E/wiki/Home-EN#shield-pi-hat-for-single-board-computers).  
**sonoff_zbbridge**: ESPhome firmware for the Sonoff Zigbee bridge. [Using this device is theoretically not recommended](https://github.com/home-assistant/home-assistant.io/issues/17170),
and it has been giving me many issues. If you really want to, I suggest
following [the Tasmota procedure first](https://zigbee.blakadder.com/Sonoff_ZBBridge.html), then moving to ESPhome.  

### ESP32 BLE+Zigbee bridge
I flashed a CC2531 dongle with a CC2530 firmware [from here](https://github.com/Koenkk/Z-Stack-firmware/tree/master/coordinator).
Can't remember which one, but probably the [Z-Stack_Home_1.2 Default](https://github.com/Koenkk/Z-Stack-firmware/blob/master/coordinator/Z-Stack_Home_1.2/bin/default/CC2530_DEFAULT_20201127.zip).  
Should you want to replicate this, please get a CC2530 with properly broken out
pins since the pins on the side of the CC2531 USB dongle are incredibly small
and your wires WILL break, or a CC2530 + CC2591/CC2592 that would probably be
an even better choice for range; that said, those are old devices and can only
run Zigbee 1.2 firmware properly, you *can* flash a 3.0 firmware but you'll
probably encounter some issues. Consider something else like [this](https://github.com/egony/cc2652p_E72-2G4M20S1E/wiki/Home-EN#shield-pi-hat-for-single-board-computers),
if you need a wireless hub you just need to connect the TX and RX wired as
needed to the ESP32.  
If it's in your budget, please just get a CC2652R/P/RB stick to use with ZHA or
zigbee2mqtt. Apparently, Silicon Labs's EZSP v8 modules (EFR32MG21) are very
good too, but if you plan to use it over WiFi, the caveats for the ZBBridge
apply.  

However, if you already have a CC2531 dongle, after flashing it with the CC2530
firmware, solder two wires on the IO connector, on the 0.2 and 0.3 pins, and
other two on the GND and VCC (3.3V) headers (maybe you'll have more success if
you remove them and use the pads). You could also solder on the USB pins and
use 5V instead if you feel like it, but I prefer not to mess with the USB port.  

![](CC2531_pins.png)  

I then added this in my zigbee2mqtt config (I'd prefer ZHA but it doesn't
detect double presses):

```
serial:
  port: 'tcp://10.10.0.250:6638' # change as needed
```

I only made this hub to keep my RasPi's USB ports free (especially since the
Pi 3's USB cripple the ethernet port, even though the dongle doesn't transmit
enough data for it to be a problem). Maybe I'll evolve this into a "superhub"
with BLE, Zigbee, Matter, Z-Wave, and even 433MHz, all in a single PCB with no
ugly wires, but it's not gonna happen any time soon. Feel free to [donate on my Ko-Fi](https://ko-fi.com/exentio)
if you want me to work on that.  

![](not_a_fire_hazard.jpg)  

Huge thank you to Oxan for his stream server library! [oxan/esphome-stream-server](https://github.com/oxan/esphome-stream-server)
