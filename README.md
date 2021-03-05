# FLSun-Q5
My repo for my FLSun Q5 3d printer

This is for me to keep and share my Marlin builds for the FLSun Q5 printer using the MKS Robin Nano V3 board.

The V3 kit that I got came with 4x 2209 Drivers, along with the 3.5" display (MKS TS35 V2.0)

# Physical Mounting
The board physically fits fine, although it does block the new USB drive port. You'll have to cut a hole in your machine if you want to use that feature, but it will otherwise fit, and everything from the OEM board just plugged right in to the new board, in the same locations.

The new display doesn't fit, and as soon as I get my design tweaked in, I'll upload some STL files for a mount adapter.

## Fan Wiring
Part cooling fan goes to "Fan 2" on the mobo.
Hotend fan goes to "Fan 1"
Internal fan goes to one of the 12/24v plugs so it's always on. I've got mine spliced into Fan 1 and it's been fine. You're usually not jogging around enough to heat things up without the extruder on anyway.

# Install Instructions & Notes

1. Load the 'assets' folder and the 'Robin_nano_v3.bin' file onto an SD card, load the card into the printer, and turn it on. This will flash Marlin.
2. Warm up the bed to around your normal print temp, I do 60C for PLA.
3. Run the "Auto Config" from the Settings->Configuration->Delta Calibration->Auto Calibration. That does an M33 command, feel free to do it more than once. https://marlinfw.org/docs/gcode/G033.html
4. Click Settings->Configuration->Store Settings to save the config you just did. 
5. Connect OctoPi or another terminal program so you can type in G-code. I think you can do the bed leveling through the menu, but I do G-codes.
6. Type in 'G28' (home)
7. Type in 'G29 P1' (create UBL bed mesh) We're following this: https://marlinfw.org/docs/features/unified_bed_leveling.html
8. Wait for the machine to probe the bed.
9. Type in 'G29 P3 T' and view the output. If the mesh isn't full of values everywhere, type the command in a few more times until it is. 
10. Type in 'G29 S1' to store the Mesh
11. Type in 'G29 A' to activate bed leveling (this will be added to your start code later)
12. M500 to save the settings to EEPROM. 
13. Set your Z probe offset height. This is where your first layering comes in. Mine is at ~19.00 for reference. 
14. Home/G28
15. Jog the Z down until the paper catches
16. Remember the current Z value, and add it to Settings->Configuration->Probe Z Offset

And that's it. I just did a full reset of my printer using these instructions, and it's good to go! You can stop here if you know what you're doing, the rest is for my own notes.

## Auto Configuration & G33

You should run G33 a couple times so that Marlin can tweak in your machine's individual build characteristics. Everyone's arm lengths are gonna be slightly off. Everyone's tower heights are gonna be slightly different. The endstops are mounted a few microns different. That's just manufacturing for you. Use the guide on Marlin's site.

https://marlinfw.org/docs/gcode/G033.html

This will tweak everything just a bit and make your prints MUCH more accurate.

## Bed Leveling

I have enabled UBL in marlin, and it works really well for me. You will need to follow Marlin's guide on how to set it up before using it. 

https://marlinfw.org/docs/features/unified_bed_leveling.html

Of note: G28 turns OFF bed leveling, so be sure to add the G29 L1 and G29 J to your start code in whatever slicer you're using. I have turned on the feature in marlin that turns leveling back on after homeing, but I haven't tested it yet.

Also of note, as it says you might have to do 'G29 P3 T' several times. You'll know when it's done because every dot in the grid will have a value in the console output. If you've still got dots, run 'G29 P3 T' again!

After setting it up, you'll just do a 3-point mesh tilt procedure before printing each time.

# Pre-built Firmware

I've included a folder called "pre-built firmware" in this repo. That's the firmware currently running on my Q5. If you wanted to use it, just copy the Robin_nano_v3.bin and assets folder to a SD card, and stick it in the SD slot, and it should flash it.

As with all software that someone else made: Use it at your own risk! If it breaks anything, I'm waiving all reponsibility. 

# Wifi
My kit also came with the WiFi module, but I haven't played around with that yet. Apparently it just involves activating it in Marlin, and flashing your own Wifi Access Point info using a procedure similar to flashing the main board, but again I haven't done it myself yet.

# Marlin Build Notes

This is mostly for me to remember 'why' I did things...

## build command

'pio run' (i'm using the PIO build system in windows and whatnot)

## Marlin display mode

I had issues accessing certain menus in the fancy LVGL display mode, so I swapped back to the #define TFT_COLOR_UI mode, and it seems to work well enough for me.

## Encoder pulses per step

I had an issue where the scroll wheel was jumping so I upped this and it seems to work well at 4. 

## Wifi

It's enabled in Marlin, but you'll need to flash it with your own SSID & Password, see here:
https://github.com/makerbase-mks/MKS-WIFI

## Extruder Fan (not part cooling fan)

I've got it configured in the Configuration_adv.h to use PC14 as a hotend fan. This means the fan only comes on when your extruder is above 50 degrees C. 

It helps quiet down the printer during non-printing times. My internal fan is doubled with my extruder fan, as I'm running 12v noctua's. This shuts them both off, and I should be more concerned about heat on the mobo, but I'll be okay.

## Fan1 vs Fan2

So, MKS has some 'splanin to do. Their wiring Schematic and their pinout say different things.

In the Silkscreen on the board, and on the "Pin" document, they list 
FAN1 as PC14 on connector J7
FAN2 as PB1  on connector J6

On the Schemtaic page (Rev V3.0..002), page 4, it lists:
FAN1 as PB1  on connector to J6
FAN2 as PC14 on connector to J7

So already we've had them switched. 
In Marlin, where we define what pin on the CPU does what, we've got:
#define FAN_PIN                             PB1   // FAN1, Pin36 board, FAN2 connector, part-cooling
#define FAN1_PIN                            PC14  // FAN2, Pin9 board, FAN1 connector

Note: I added the comments. 
As noted in the above section, PC14 is the Hotend-cooling fan, and PB1 is the Extruder fan. To achieve this wiring, do the following:

Extruder Fan plugged into "FAN1"/J7 connector
Part Cooling Fan plugged into "FAN2"/J6 connector
