# Table of Contents

- [Installation](#installation)
- [Configs](#configs)
- [Nozzle Alignment](#nozzle-alignmentt)
- [Printer Profiles](#printer-profile-information)
- [Nevermore Filter](#nevermore-filter)
- [Macros](#macros)
- [BlTouch](#bltouch)

# Installation

<details><summary>KIAUH</summary>
<p>

**Note:** KIAUH takes a little bit more work to set up, but is ideal for running
multiple instances of klipper, and also makes it possible to install any and
all frontends including Mainsail, Fluidd, and Octoprint! A huge thanks to
th33xitus for making [KIAUH](https://github.com/th33xitus/kiauh)!

**Step 1:**

Follow the instruction on [KIAUH's github page](https://github.com/th33xitus/kiauh#--%EF%B8%8F-instructions-) to install both the recommended operating
system for KIAUH and the script itself.

**Step 2:**

KIAUH is set by default to install klipper from the klipper3d repo, however, we
need to install it from the dockterj branch. This can be accomplished by editing
the file `klipper_repos.txt.example`.

SSH into your raspberry pi, and run the following commands:
```
cd kiauh
sudo nano klipper_repos.txt.example
```
Now, you should be presented with a screen that looks like this:

![](https://i.imgur.com/kWNYJNQ.png)

Delete the four uncommented lines, and add the line `https://github.com/dockterj/klipper` in their place.
Your screen should now look like this:

![](https://i.imgur.com/UTat7JL.png)

Now, hit Control+x to exit, then hit `y` to save the modified buffer.

**You must save the file as `klipper_repos.txt`, otherwise KIAUH will not recognize the file as valid.**

Hit `y` to save the file under a different name.

If you want to check to be sure the edit was successful, you can type `ls` into the command line. If the
file `klipper_repos.txt` is there, then the edit was successful.

**Step 3:**

Now you're ready to start KIAUH! Enter `./kiauh/kiauh.sh` to start the script.

Once in KIAUH, you should see a screen like this:

![](https://i.imgur.com/mJKOZqq.png)

KIAUH, by default, uses the official master branch of klipper. To tell KIAUH to
use this branch instead, we need to add the custom repo you created to KIAUH.

Input the number `6` to enter KIAUH's settings, then hit enter. 
You should now see a screen like this:

![](https://i.imgur.com/AMtFrQm.png)

Input `1` to set the custom repository. You'll see a screen like this:

![](https://i.imgur.com/w1uRnS1.png)

Input `0` to set the custom repository, you'll be presented with a screen like this:

![](https://i.imgur.com/0CWf11R.png)

Then input `b` and `b` again to get back to the main menu.

**Step 4:**

Now you can install Klipper! Input `1` to get to the installation menu. Once there,
input `1` again to enter the klipper installation procedure.

Input `1` to install python 3.x, then set the number of Klipper instances your would
like to install.

![](https://i.imgur.com/lEuWk4T.png)

You may be presented with a prompt like this:

![](https://i.imgur.com/mHdaWpC.png)

Input `y` for this prompt to avoid potential problems down the road.

Let the process complete, and now Klipper is fully installed! From here you can
install Moonraker, your interface of choice, and whatever other programs you may want 
that KIAUH offers.

**Step 5:**

Depending on the printer that you are running, you will either want to use the
`/config/printer-makerbot-replicator2-2012.cfg` config file or the
`/config/printer-makerbot-replicator2x-2012.cfg` config file.

Mainsail and Fluidd both allow you to copy over example configs from
within the web interface, however, Octoprint does not. These following steps
will detail the process of doing this through the ssh terminal. If this does
not apply to you, you can skip to the next step.
    
If you are still in KIAUH, input `Q` to close it, then type `cd` to navigate
back to the pi directory, which is represented by a `~`. The text preceding
your cursor will look similar to `pi@replicator2x:~ $`
    
Type `cd klipper/config` into the command line. You are now in Klipper's example
config folder. Type either `cp printer-makerbot-replicator2-2012.cfg ~/printer_data/config/`
or `cp printer-makerbot-replicator2x-2012.cfg ~/printer_data/config/` to copy the example
config for your printer into your printer's configuration directory.
    
Now type `cd ~/printer_data/config` to move over to your printer's configuration directory.
Type `rm printer.cfg` to remove the placeholder printer config that comes with klipper. Now
type `mv printer-makerbot-replicator2-2012.cfg printer.cfg` or 
`mv printer-makerbot-replicator2x-2012.cfg printer.cfg`, depening on your printer, to rename
the example config to the config name recognized by Klipper.

**Step 6:**

Now we're going to flash Klipper to the printer's mainboard.
    
Plug the printer into one of the Pi's usb ports. Verify that the device
appears in /dev/serial/by-id by executing the command `ls /dev/serial/by-id`.
If the printer is connected, this command will return a dialogue that looks like
this:

![](https://i.imgur.com/zxS6FMs.png)

Copy this by highlighting it, then right clicking it. Open up a temporary notepad document
and paste it into that, you'll need this to update your printer.cfg.

Find the section of your config that looks like this:
```
[mcu]
serial: dev/serial/by-id/[your-serial-id-here]
restart_method: mightyboard
baud: 250000
```

Update the `serial: [your-serial-id-here]` line with the line you saved earlier.
When completed the line should look similar to:
```
serial: /dev/serial/by-id/usb-MakerBot_Industries_The_Replicator_5533034353435160A141-if00
```
    
Now, go back to the command line and run:
```
cd ~/klipper/
make menuconfig
```
   
In this menu, choose the atmega1280, 16mhz, and uart0.

**Note:** See below for the note about atmega2560.
    
Once you enter those settings, hit `Q` to exit, then `Y` to save.
    
Now run the command `make`. This will build the firmware for your
particular machine.
    
Now, run the commands:
```
sudo service klipper stop
make flash FLASH_DEVICE=/dev/serial/by-id/[your-serial-id-here]
sudo service klipper start
```
Where an example of FLASH_DEVICE will look like:
```
FLASH_DEVICE=/dev/serial/by-id/usb-MakerBot_Industries_The_Replicator_5533034353435160A141-if00
```
This will flash your printer. If it fails, you may need to power off and then power on your printer,
or attempt to connect and disconnect with Klipper. It should flash after you try one or both of these things.

Congratulations! You now have Klipper installed on your Replicator 2 or 2X!
Head over to https://www.klipper3d.org/ for documentation on futher tuning
of your printer and to learn the specifics of what Klipper is capable of.

</p>
</details>

# Configs

Configuration files for the Replicator 1, 2, and 2X can all be found in the [Configs folder](https://github.com/Sgail7/Replicator-Revival-Project/tree/main/Klipper/Configs)

I have taken a modular approach to including functionality in these printer configs. The `printer.cfg` file itself has the bare essentials to facilitate functionality of the printer. Other functions, such as the configurations necessary for input shaper or beeper commands, can be found in the [Macros folder](https://github.com/Sgail7/Replicator-Revival-Project/tree/main/Klipper/Macros). Any of these macros can be included at the discretion of the user, depending on their needs and their printer's capabilities.

These configs are based on the example configs provided in Dockterj's Klipper Fork and the official config example repository. I have tuned my configs over many more hours than I care to admit. Mostly, these tunings include macros to add functionality and ease of use to your Replicator and provide all of the sections that I have found necessary to get the most out of these printers. Please choose the correct configuration for your printer, and also choose the correct slicer profile to accompany it.



# Nozzle Alignment

**This section is only relevant for the Replicator 1 Dual and Replicator 2X!**

**Note: This gcode is written for a printer with a center origin! It will not work if you are using anything other than a center origin configuration file! Once you have obtained your nozzle offset, you may switch back to a configuration file with a different origin and translate the values that you found to the coordinates of that configuration file.**

This fine piece of gcode was adapted from the [nozzle alignment script](https://github.com/makerbot/ReplicatorG/blob/master/machines/replicator2/onboard/nozzleCalibration.gcode) found in the ReplicatorG source code. It preforms the exact same alignment process as the original Makerbot firmware did and is by far the easiest way to calibrate the x and y nozzle offset of these printers.

In an ideal world, this tuning would be unnecessary. Unfortunately, no machining process is perfect, and as a result the x and y positions of the left nozzle relative to the right nozzle on the Replicator 1 Dual and the Replicator 2X are usually slightly misaligned. To correct for this, this gcode instructs your printer to make a series of lines in the x and y directions on your printer, offsetting each by 0.10 mm. Once these lines have been printed, visual inspection is employed to determine which lines match up best to each other.

13 Lines are printed in the X and Y directions respectively. The offset of line #1 in the X direction is -0.60mm, relative to the right nozzle; The offset of line 13# is 0.60mm, again relative to the right nozzle. Likewise, the offset of line #1 in the Y direction is -0.60mm, and the offset of line #13 is 0.60mm. Line #7 for both directions has an offset of 0.00mm. To find the offset nozzle offset for your printer, you will inspect each of these lines, starting at line #1, until you find the set of lines that look to be in parallel. Note the line number that you stopped on, and then do this again for the other axis. Use these line numbers to find your offset.

As an example, assume that I found that Line #5 in the X direction, and Line #7 in the Y direction were my two best line pairs.

For the X-axis: Since the offset of line #1 is -0.60mm, I can find that line #5 is -0.20mm.

For the Y-axis: Since the offset of line #7 is 0.00mm, I can find that there is no appreciable offset in the Y direction between my two hotends.


# Printer Profile Information

**Please be sure to use the correct origin profile for your printer configuration. The Center Origin profiles will not work with a Left Front Origin configured printer and vice versa.**

PrusaSlicer is currently the slicer of choice for both my personal use and use at my university's Makerspace. I recommend using these profiles, as they will be up-to-date and are much more thoroughly tested. SuperSlicer is also a good option for these printers as the extra part cooling fan settings it provides can help overcome issues of poor top solid infill finishes. If you are using a PrusaSlicer version that is older than 2.6, I highly recommend commenting out the M204 macro found in the [`Important_Gcode_Functionality_Macros.cfg`](https://github.com/Sgail7/Replicator-Revival-Project/blob/main/Klipper/Macros/Important_Gcode_Functionality_Macros.cfg) file. I found that certain acceleration commands would cause errors in this macro, causing prints to fail.

# Nevermore Filter

## What a Nevermore Filter is
The Makerbot Replicator 2X was originally intended to be an ABS only printer. While it did achieve that goal, it didn't provide any solution for the fumes that ABS produces. Luckily the team at Nevermore3d has designed a VOC particle filter that can significantly reduce these fumes, to the point that they don't pose a risk to the health of living creatures. It also filters out the smell of ABS, making it much more pleasent to deal with. If you are going to be printing a lot of ABS or otherwise hazardous filaments, I highly recommend building one of these filters.

## Sealing up the Makerbot

The [Nevermore Micro](https://github.com/nevermore3d/Nevermore_Micro/tree/ddc341e032cfdb93479e6ed0c258ce544beba87f) was designed to be a recirculating filter, which means that the better the Makerbot frame can be sealed up, the better. For sealing up large gaps, I recommend checking out the [Printed Parts](https://github.com/Sgail7/Replicator-Revival-Project/tree/main/Printed%20Parts) section of this github. For the interior parts of the printer, such as the holes in each of the 4 corners of the bottom plate, I recommend using masking tape or painter's tape. Of course, any type of tape can be used for this purpose, however, masking or painter's will allow for an easy, clean removal of the tape without any extra hassle if you decide to stop printing ABS or other hazardous filaments. As for the front swinging panel, I recommend putting weather stripping around the inside opening for the panel, and again checking out the [Printed Parts](https://github.com/Sgail7/Replicator-Revival-Project/tree/main/Printed%20Parts) section to attach a latch to keep the panel tightly sealed when printing.

## Electronics

Since the Mightyboard Rev G and Rev H have no extra PWM controllable fan ports, we have to make use of a MOSFET board and the Mightyboard's provided 24V or 5V header. Start by soldering a short wire to the `gnd` section of the MOSFET board, then connecting that wire to the `Vin-` terminal on the same board. Now take two lengths of wire and connect one to the `Vin+` terminal, and the other to the `Vin-` terminal, the `Vin-` terminal should have two wires going into it. Solder a length of wire to the `trig/PWM` section of the board. All of the connections mentioned up until now each need to be terminated on one end by a single pin dupont connector. Connect the fan from the Nevermore filter to the `Vout+` and `Vout-` terminals. Now, connect the `Vin+` wire to the 24V or 5V header on the Mightyboard, whichever your chosen fan uses, next connect the `Vin-` wire to the ground in the `J18 1280_EXTRA_2` group of headers, lastly connect the `trig/PWM` wire to the pin labeled `IO5/PF1` in the `J18 1280_EXTRA_2` group of headers.

## Klipper and Gcode usage

Include the [`Nevermore_Filter.cfg`](https://github.com/Sgail7/Replicator-Revival-Project/blob/main/Klipper/Macros/Nevermore_Filter.cfg) macro in your `printer.cfg`. Doing this will make a new macro appear called `toggle_nevermore`; to check that your fan is wired up properly, click this macro, your filter fan should be turned on to 100%. If it is not, check that the `trig/PWM` wire is connected to the right pin, and that there are no cold solder joints or broken connections in the wiring.

All documentation of this macro can be found in its original repository, found [here](https://github.com/top-gun/Nevermore-Klipper). To make the filter run once a print starts, add `SET_FAN_SPEED FAN=Nevermore SPEED=1` to your start Gcode. To make it turn off when a print is finished, add `UPDATE_DELAYED_GCODE ID=filter_off DURATION=180` to your end Gcode. If you are using one of the profiles provided in this repository, then these lines will already be added for the appropriate filaments. If you would like your filter fan to run more slowly for quieter operation, you can reduce the `SPEED=1` line to something like `SPEED=0.5`. In that example, the filter fan will run at 50% speed instead of 100% speed. If you would like the filter to remain on for more or less time after a print is finished, you can edit `DURATION=180` to whatever number you would like. This command takes the number provided as seconds to keep fan powered.

# Macros

Macros are arguably the most useful aspect of Klipper as a firmware. They allow for customizability of pretty much any functionality of the firmware. All of the macros that I currently use can be found in the [Macros Folder](https://github.com/Sgail7/Replicator-Revival-Project/tree/main/Klipper/Macros). Any of these macros can be included in your printer.cfg file, simply add a line like the following to the start of your printer.cfg.
```
#Short description of macro
[include example.cfg]
```
Below are short descriptions of each of the macros included in this repository and what they do:

### Beeper_Commands.cfg
<details><summary>Click here to read about this macro</summary>
<p>

This macro includes commands enabling gcode commands for `M300` and `M72`.

M300 allows for the beeper on the mightyboard to be controlled by klipper. Setting a value `S` defines the frequency that the beeper outputs; setting a value `P` defines the duration of the tone that is defined by `S`. `S` is defined in Hertz (Hz), and `P` is defined in milliseconds(ms). An example of its usage would be `M300 S440 P1000`, running this example command would play a tone of 440 Hz for 1000 ms, or in other words it would play an A note for 1 second.

M72 has mappings to play various short songs. Setting a value `P` defines which song it plays.

- `P0` plays a "print error" song
- `P1` plays a "print done ta-da" song
- `P3` plays a "startup" tune
- `P4` plays a "makerbot tv" tune
- `P5` plays a short section of Beethoven's 5th Symphony
- `P9` plays a short section of Can-Can by Jacques Offenbach

As an example, `M72 P5` would play the short section of Beethoven's 5th Symphony.

</p>
</details>

### Important_Gcode_Functionality_Macros.cfg
<details><summary>Click here to read about this macro</summary>
<p>

This macro includes commands enabling gcode commands for `M900`, `M600`, and `M204`.

M900 allows for the Pressure Advance value used by the selected extruder to be set mid-print. This is extremely useful as it allows for a gcode command to be set in the slicer for each filament, preventing the user from having to change their pressure advance value manually in their printer.cfg every time the filament is changed. It imitates marlin's M900 command, using a value `K` to set pressure advance. As an example, `M900 K0.4` would set the pressure advance to 0.4.

M600 allows for a filament swap mid-print. This means that, at a certain layer height, an M600 command can be sent by the slicer to pause the print, allowing you to change the filament, and then start the print once again. The most common use of this command is color swapping filament mid-print, enabling multiple colors to be used in one print. The usage of this command is simply `M600`, there are no associated values with it.

M204 allows for the printer's acceleration to be set mid-print. This means that different acceleration values can be used for different parts of the model. For example, infill can be run at 3000 mm/s^2 to reduce print times, while outer perimeters can be run at 1500 mm/s^2, allowing for higher quality printing. A value `S` can be set to define the acceleration of the printer. As an example, `M204 S2000` will set the printer to run at 2000 mm/s^2. Keep in mind that klipper will not allow acceleration values higher than its `max_accel` definition, it will simply default to its `max_accel` definition's set value when an acceleration higher than it is sent via M204.

</p>
</details>

### Input_Shaper.cfg
<details><summary>Click here to read about this macro</summary>
<p>

This macro enables input shaping via an ADXL 345 connected to a raspberrypi. This macro adds no commands, it is purely a convience macro to allow for one line to be uncommented to enable input shaping. For a guide on how to set up your printer to run input shaper, please visit [the official Klipper documentation on input shaping](https://www.klipper3d.org/Measuring_Resonances.html).

</p>
</details>

### LED_Macros.cfg
<details><summary>Click here to read about this macro</summary>
<p>

This macro add convient commands to control the LED strip found on the Makerbot 2 and 2X. There is a short tutorial inside of the [LED_Macros.cfg](https://github.com/Sgail7/Replicator-Revival-Project/blob/main/Klipper/Macros/LED_Macros.cfg) on how to make your own LED macro, should the provided ones not include what you are looking for.

Below is a list of all included LED commands found in this macro:

- White
- Blue
- Red
- Yellow
- Green
- Purple
- Orange
- LED_Off
    - Turns off the LED strip
- LED_Heating
    - Essentially the same as Orange, just a different name for convience when including in start gcode.
- LED_Alert
    - Plays a sequence of Red, Green, then Blue, followed by a low intensity white light to indicate when the printer has been stopped due to an issue.
- LED_Print_Finished
    - Changes the LED color to Green for 20 seconds, followed by an indefinite Orange light.

</p>
</details>

### Nevermore_Filter.cfg
<details><summary>Click here to read about this macro</summary>
<p>

This macro adds commands to easy control your nevermore filter's operation during a print. It defines the Nevermore's fan inside of klipper and the pin that controls it. The commands `SET_FAN_SPEED FAN=Nevermore SPEED=1` and `UPDATE_DELAYED_GCODE ID=filter_off DURATION=180` are used in the start and end gcode, respectively, to control the filter's operation.

`SET_FAN_SPEED FAN=Nevermore SPEED=1` defines the speed that the fan runs at. It is definable in increments of 1%. For example, `SET_FAN_SPEED FAN=Nevermore SPEED=0.7` would set the nevermore's fan to run at 70% speed. This line can be added to your start gcode to turn the filter on when a print starts.

`UPDATE_DELAYED_GCODE ID=filter_off DURATION=180` defines the amount of time the Nevermore's fan should be left on after a print has been completed. This is definable in increments of 1 second. For example, `UPDATE_DELAYED_GCODE ID=filter_off DURATION=120` would leave the fan on for 120 seconds, or two minutes, after the command is run. This can be added to your end gcode to turn the filter off after a specified amount of time, allowing the filter to cleanse any remaining VOCs from the build chamber.

</p>
</details>

### Printer_Tuning_Macros.cfg
<details><summary>Click here to read about this macro</summary>
<p>

This macro adds a collection of convience macros for common tuning steps. The included macros are `PID_EXTRUDER`, `PID_EXTRUDER1`, `PID_BED`, `Level_to_Bed_Screws`, and `Pressure_Advance_Tuning`.

`PID_EXTRUDER` **Need to check PID commands to see how to input target temperature in command line**

`PID_EXTRUDER1`

`PID_BED`

`Level_to_Bed_Screws` simply runs the command `BED_SCREWS_ADJUST`, which is klipper's built in manual leveling helper script.

`Pressure_Advance_Tuning` is meant to be run right before starting the pressure advance tower defined in the [Pressure Advance section in the official documentation](https://www.klipper3d.org/Pressure_Advance.html). It runs the commands defined in the offical documentation for slowing acceleration and setting the increments of increase to those given for direct drive extruders.

</p>
</details>

### Temps.cfg
<details><summary>Click here to read about this macro</summary>
<p>

This macro adds the temperatures of the raspberry pi's MCU and the chamber temperature to the temperature readouts of a chosen interface.

**Note: The chamber temperature sensor in this macro is a DS18B20. This must be installed separately and be configured according to [the official documentation](https://www.klipper3d.org/Config_Reference.html#ds18b20-temperature-sensor). The Raspi MCU must also be configured in your `printer.cfg` for this sensor to be enabled. [See the hardware readme for more info on how to install this sensor](https://www.klipper3d.org/Config_Reference.html#ds18b20-temperature-sensor).

</p>
</details>

# BlTouch

A BLTouch is a bed probe that uses a plunger controlled by a solenoid to probe the distance between itself and the bed of a 3D printer. Taking measurements at multiple points around the bed and creating a mesh from them allows Klipper to automatically compensate for deformaties in the bed. I consider a BLTouch absolutely necessary to get good prints from these printers. Installing a BLTouch will require you to raise the left nozzle up by a few mm to provide clearance for the z-axis to move up and down during the print, which will take away the Dual Extrusion capabilities of this printer. However, I believe that a perfect first layer every print with no elephant's foot is far more valuable than the finicky Dual Extrusion the Replicator 2X is equipped with in its stock form. For an installation guide and more information on running a BLTouch on a Replicator machine, please see the write-up on it found in the [Hardware Section](https://github.com/Sgail7/Replicator-Revival-Project/tree/main/Hardware#BLTouch).