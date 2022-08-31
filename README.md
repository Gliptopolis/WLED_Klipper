# LED Controls for Your Klipper/Moonraker Machine

First, let's clear the air: I'm a dumbass and I figured this out through trial and error. Your results may vary and all of this information is purely for entertainment purposes. My free opinions are worth to you exactly what you're paying for them. Of course, if you pay, it's probably worth a little less than what you paid.

Anyway, moving on, I spent quite a bit of my time trying to understand how I could add some simple neopixels to my RatRig printer. It ended up actually being pretty simple but it sure didn't seem that way at the time. 

I can tell you that it wasn't easy. Lots of information about different things related to neopixels, but not much for how to implement with my printer OS, especially at specific points during the printing cycle. I hope to clear that up for readers of this file and to make the process as as painless as possible. I'm a RatOS user, which is a branch of Klipper, so it should work for most klipper machines.

I have based my LED control system on two control boards, the ESP 8266 Dev Board and the ESP32. Either can be flashed with WLED and are great options for controlling your LEDs. I'm currently using both on my machines.


## Stuff You'll Need
I'm not suggesting any of the following hardware, just offering up options. <b><i> Make your own choices. </b></i>
 - Neopixels of your choice. Be sure they're individually addressable. 
 - Some wire to connect your LEDs to your controller. I use [this](https://www.amazon.com/StrivedayTM-3-core-Control-Shielded-Headphone/dp/B01LNH9ZYK/ref=sr_1_7?crid=2X5OBMKMEGJNO&keywords=24%2Bawg%2B3%2Bconductor%2Bwire&qid=1661885002&sprefix=24%2BAWG%2Caps%2C109&sr=8-7&th=1) or [this](https://www.amazon.com/C-able-Conductor-WS2811-WS2812b-Extension/dp/B082KNCKB3/ref=sr_1_1_sspa?crid=2X5OBMKMEGJNO&keywords=24+awg+3+conductor+wire&qid=1661885311&sprefix=24+AWG%2Caps%2C109&sr=8-1-spons&psc=1&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUEyN1I2TFhXMlhPUkZWJmVuY3J5cHRlZElkPUEwNzEzNTI1M1Y5M1NCMkZNMDEzMiZlbmNyeXB0ZWRBZElkPUEwMDczNDUwMU01NUQ5UEZHUzQzQyZ3aWRnZXROYW1lPXNwX2F0ZiZhY3Rpb249Y2xpY2tSZWRpcmVjdCZkb05vdExvZ0NsaWNrPXRydWU=), but you should use what you're comfortable using. My advice is worth what you pay for it.
 - [ESP 8266 Dev Board](https://www.amazon.com/HiLetgo-Internet-Development-Wireless-Micropython/dp/B081CSJV2V/ref=sr_1_3?crid=NYQ6QPIIUMM7&keywords=8266%2Bdev%2Bboard&qid=1661863407&sprefix=8266%2Bdev%2Bboard%2Caps%2C64&sr=8-3&th=1)
 - [ESP 32](https://www.amazon.com/ESP-WROOM-32-Development-Microcontroller-Integrated-Compatible/dp/B08D5ZD528/ref=sr_1_3?crid=38QCSRFSM52NX&keywords=esp+32&qid=1661863479&sprefix=esp+32%2Caps%2C76&sr=8-3)
 - [Flashing Software](https://github.com/marcelstoer/nodemcu-pyflasher)
 - Simple hand tools for cutting and soldering. Crimping tools if you decide to use crimp terminals to connect to the board.
 - Cable to connect the board to your PC
 - A [case](https://www.thingiverse.com/thing:4816827) to house your controller for ease of mounting. Note that if you use this for an ESP32, you should print X axis at 102% for a better fit.
 - A machine utilizing Mainsail / Klipper (Mainsail has built-in support for WLED)

# Getting Started
![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/walking-with-iphone-x.jpg)

Let's get started! The first thing you need to do is go over to [Tyson Nichols' great site](https://tynick.com/blog/11-03-2019/getting-started-with-wled-on-esp8266/) and follow his tutorial on installing WLED on an ESP 8266.

It's a great explanation of how to make everything talk and work together to get WLED up and running for your control system.

No need for me to reinvent the wheel when his method works so well. 

Assuming everything is now wired and programmed, it's time to start the integration with Moonraker and Klipper...

Now is the best time to go to the WLED app and create your custom profiles that will be called by Klipper later. When you open the app this is what you should see:
![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED01.jpg))

The first step will be to choose a color palette or a single color. That is from the home screen and you can choose up to 3 colors that can be blended or use a predefined palette. 

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED01%20(2).jpg))

After you choose your custom colors or a predefined set, go to the next tab, for Effects. This is where you choose the animations for your LEDs. Play around with them and find something you like. Once you're happy with the effects, it's time to move over to save a custom profile. 

Note:If you feel the need, you can go to the Segment tab and define effects for specific sections of your LED strings. I apply everything to the entire strings so I don't use this.

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED02.jpg))

Going to the Presets tab, you should see this screen:

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED04_B.jpg))

The first thing to do here is to click the Create preset button.

You should then see this screen:

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED04_D.jpg)

The most important section of this screen and process is the "Save to ID" number of the presets. It's how we will call the effects in the klipper macros so be sure and keep your numbering simple.I use a simple name to remind me what's what for the Nee preset section and make the Quick load label the same as the Save to ID number because I'm not smart and that's simple. It should also be noted that the preset will be created based on the effects and colors you've chosen on the other tabs. This just saves them. Simple.

Eventually you'll end up with something like this:

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED04_P.jpg)

These are my saved presets for the string of LEDs in the base of my printer. I would suggest that you take the time to make a preset for "On' and another for "Off" that you can use as well. My "On" is just white light for working and the "Off" is clearly no light at all. It comes in handy for the end print stuff later.

Now it's time to start incorporating these presets in Klipper. First you need to let Mainsail know you've got some LEDs attached that crave being controlled.

## Moonraker
I'm no expert, but the following is what has worked for me. If you have a better way to do it, then knock yourself out. 

First, we need to let Moonraker know that we have some LEDs that are being controlled by WLED. We need to edit the Moonraker.conf file with the following information:

```bash
[wled chamber]
type: http
address: 192.168.0.45
initial_red: 0.5
initial_green: 0.4
initial_blue: 0.3
chain_count: 42
```
  For your specific installation, you should change the name from 'chamber' to whatever you want your lights to be called. For example, if you wanted to call them 'frame' just change to 
  
  [wled frame]  

I use the http type because I'm utilizing the built-in wifi on the controller. You can find the address on the WLED app and input it in the address section

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/Config_C.jpg)

I don't use the red, green, sections since I setup the WLED app to auto start with a saved profile, but you can do what suits you here. These values are based on a max of 1.

The chain count is very important for your effects to work properly. The controller needs to know how many neopixels are on your string in order to work properly. Just count them and put the number here.

Repeat this process for every controller you install. I curently have 4 on my printer controlling bed lights, upper work lights, an accent light on the back panel and a single on the hotend cover that changes colors with temps. I'm sure there's a way to utilize one controller for multiple strigns but no idea how to make that work. This method works for me.

That's it for Moonraker. Once you make the updates, save your new config file and get ready to move over to Klipper for the magic...

## Klipper
In order to control the WLED, you will need to modify your printer.cfg file with macros to control the LEDs and also update your start print and end print macros. Also, if you're a RatOS user, please don't modify the files in your config folder. Copy your existing start and end print macro sections from the macros.cfg file and paste in to your printer.cfg file so you can always revrt back to the original version without a lot of hassle. Ask me how I know...

Below are my Start and End Print macros including the calls for the macros specific to the WLED presets:

```bash
[gcode_macro START_PRINT]
description: Start print procedure, use this in your Slicer.
gcode:
  CLEAR_PAUSE
  SAVE_GCODE_STATE NAME=start_print_state
  Breathe_On
  # Metric values
  G21
  # Absolute positioning
  G90 
  # Set extruder to absolute mode
  M82
  # Home if needed
  MAYBE_HOME
  M117 Heating bed...
  RESPOND MSG="Heating bed..."
  led_heating
  # Wait for bed to heat up
  M190 S{params.BED_TEMP|default(printer.heater_bed.target, true) }
  # Run the customizable "AFTER_HEATING_BED" macro.
  _START_PRINT_AFTER_HEATING_BED
  # Run the customizable "BED_MESH" macro
  led_mesh
  _START_PRINT_BED_MESH
  # Start heating extruder
  M104 S{params.EXTRUDER_TEMP|default(printer.extruder.target, true) }
  # Run the customizable "PARK" macro
  led_printing
  _START_PRINT_PARK
  # Wait for extruder to heat up
  M117 Heating Extruder...
  RESPOND MSG="Heating Extruder..."
  led_heating
  M109 S{params.EXTRUDER_TEMP|default(printer.extruder.target, true) }
  # Run the customizable "AFTER_HEATING_EXTRUDER" macro.
  _START_PRINT_AFTER_HEATING_EXTRUDER
  M117 Printing...
  RESPOND MSG="Printing..."
  RESTORE_GCODE_STATE NAME=start_print_state
  led_printing
  # Set extrusion mode based on user configuration
  {% if printer["gcode_macro RatOS"].relative_extrusion|lower == 'true' %}
    M83
  {% else %}
    M82
  {% endif %}
  G92 E0

[gcode_macro END_PRINT]
description: End print procedure, use this in your Slicer.
gcode:
  SAVE_GCODE_STATE NAME=end_print_state
  _END_PRINT_BEFORE_HEATERS_OFF
  TURN_OFF_HEATERS
  _END_PRINT_AFTER_HEATERS_OFF
  _END_PRINT_PARK
  led_off
  Breathe_Off
  # Clear skew profile if any was loaded.
  {% if printer["gcode_macro RatOS"].skew_profile is defined %}
    SET_SKEW CLEAR=1
  {% endif %}
  # Steppers off
  M84
  # Part cooling fan off
  M107
  M117 Done :)
  RESPOND MSG="Done :)"
  RESTORE_GCODE_STATE NAME=end_print_state
```

Here is a screenshot with the macros highlighted:

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/start_print.jpg)

Of course, you can't call macros that you haven't made yet, so let's work on those now...

This is an example of a macro to go in your printer.cfg file to call the "led_heating" preset made in WLED:

```bash
[gcode_macro led_heating]
gcode:
  WLED_ON STRIP=chamber PRESET=3
```

This macro assumes a couple things: first that your LED string is named "chamber" in the mainsail.conf file and that the preset you want to initialize is number 3 from your "Saved to ID" number in WLED. If you chose a different name in Mainsail when you added the WLED entry, then that's the name to use in the macro. Also, if you want to call a different preset, just use the corresponding number for that preset.

Something to note: If you decide to wire your ESP module directly to 5V instead of using the wall wart, just wire the + and - from you chosen power source in parallel to your wiring to neopixels. Something like this: (HIGHLY professional drawing credit to Max.#6343 from Discord)

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/Max.%236343.jpg)

Hopefully this helps you light up your project. If I can be of help, please reeach out to me over at discord, Glipski#1809. I'll try and answer questions. Just remember that I'm not very smart.

## Contributing

If this helped you at all, please feel free to [Buy Me a Coffee, or Beer](https://www.buymeacoffee.com/gliptopoliB)


