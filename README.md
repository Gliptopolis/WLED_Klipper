# LED Controls for Your Klipper/Mainsail Machine

I spent quite a bit of my time trying to understand how I could add some simple neopixels to my RatRig printer 

I can tell you that it wasn't easy. Lots of information about different things related to neopixels, but not much for how to implelent with my printer OS, especially at specific points during the printing cycle. I hope to clear that up for readers of this file and to make the process as as painless as possible.

I have based my LED system on two control boards, the ESP 8266 Dev Board and the ESP32. Either can be flashed with WLED and are great options for controlling your LEDs


## Stuff You'll Need

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

Assuming everything is now wired and programmed, it's time to start the integration with Mainsail and Klipper...

Now is the best time to go to the WLED app and create your custom profiles that will be called by Klipper later. when you open the app this is what you should see:
![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED01.jpg))

The first step will be to choose a color palette or a single color. That is from the home screen and you can choose up to 3 colors that can be blended or use a predefined palette. 

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED01%20(2).jpg))

After you choose your custom colors or a predefined set, go to the next tab, for Effects. This is where you choose the animations for your LEDs. Play around with them and find something you like. Once you're happy with the effects, it's time to move over to save a custom profile. 

Note:If you feel the need, you can go to the Segment tab and define effects for specific sections of your LED strings. I apply everything to the entire strings so I don't use this.

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED02.jpg))

Going to the Presets tab, you should see this screen:

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED04.jpg))

The first thing to do here is to click the Create preset button:

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED04_C1.jpg)

You should then see this screen:

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED04_D.jpg)

The most important section of this screen and process is the "Save to ID" number of the presets. It's how we will call the effects in the klipper macros so be sure and keep your numbering simple.I use a simple name to remind me what's what for the Nee preset section and make the Quick load label the same as the Save to ID number because I'm not smart and that's simple.

Eventually you'll end up with something like this maybe:

![alt text](https://github.com/Gliptopolis/WLED_Klipper/blob/main/images/WLED04_P.jpg)

These are my saved presets for the string of LEDs in the base of my printer.


## Mainsail
I'm no expert, but the following is what has worked for me. If you have a better way to do it, then knock yourself out. 

First, we need to let Mainsail know that we have some LEDs that are being controlled by WLED. We need to edit the Moonraker.conf file with the following information:

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

I don't use the red, green, sections since I setup the WLED app to auto start with a saved profile, but you can do what suits you here. These values are based on a max of 1.

The chain count is very important for your effects to work properly. The controller needs to know how many neopixels are on your string in order to work properly. Just count them and put the number here.

Repeat this process for every controller you install. I curently have 4 on my printer controlling bed lights, upper work lights, an accent light on the back panel and a single on the hotend cover that changes colors with temps. I'm sure there's a way to utilize one controller for multiple strigns but no idea how to make that work. This method works for me.

That's it for Mainsail. Once you make the updates, save your new config file and get ready to move over to Klipper for the magic...

## Klipper
In order to control the WLED, you will need to modify your printer.cfg file with macros to control the LEDs and also update your start print and end print macros.
