# FLSun-Q5
My repo for my FLSun Q5 3d printer

This is for me to keep and share my Marlin builds for the FLSun Q5 printer using the MKS Robin Nano V3 board.

The V3 kit that I got came with 4x 2209 Drivers, along with the 3.5" display (MKS TS35 V2.0)

# Physical Mounting
The board physically fits fine, although it does block the new USB drive port. You'll have to cut a hole in your machine if you want to use that feature, but it will otherwise fit, and everything from the OEM board just plugged right in to the new board, in the same locations.

The new display doesn't fit, and as soon as I get my design tweaked in, I'll upload some STL files for a mount adapter.

# Wifi
My kit also came with the WiFi module, but I haven't played around with that yet. Apparently it just involves activating it in Marlin, and flashing your own Wifi Access Point info using a procedure similar to flashing the main board, but again I haven't done it myself yet.

# Install Notes

## Auto Configuration

Once the firmware is loaded, do the autoconfig in the menu to setup your printer.

## Bed Leveling

I have enabled UBL in marlin, and it works really well for me. You will need to follow Marlin's guide on how to set it up before using it. 

https://marlinfw.org/docs/features/unified_bed_leveling.html

Of note: G28 turns OFF bed leveling, so be sure to add the G29 L1 and G29 J to your start code in whatever slicer you're using. I have turned on the feature in marlin that turns leveling back on after homeing, but I haven't tested it yet.

Also of note, as it says you might have to do 'G29 P3 T' several times. You'll know when it's done because every dot in the grid will have a value in the console output. If you've still got dots, run 'G29 P3 T' again!

After setting it up, you'll just do a 3-point mesh tilt procedure before printing each time.

# Pre-built Firmware

I've included a folder called "pre-built firmware" in this repo. That's the firmware currently running on my Q5. If you wanted to use it, just copy the Robin_nano_v3.bin and assets folder to a SD card, and stick it in the SD slot, and it should flash it.

As with all software that someone else made: Use it at your own risk! If it breaks anything, I'm waiving all reponsibility. 

# Marlin Build Notes

This is mostly for me to remember 'why' I did things...

## Marlin display mode

I had issues accessing certain menus in the fancy LVGL display mode, so I swapped back to the #define TFT_COLOR_UI mode, and it seems to work well enough for me.

## Encoder pulses per step

I had an issue where the scroll wheel was jumping so I upped this and it seems to work well at 3. 