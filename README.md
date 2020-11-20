# VORONKlipper

> A collection of config macros for VORON 3D-printers

This repository contains different [klipper](https://github.com/KevinOConnor/klipper) macros created for 
[VORON](http://vorondesign.com/) style 3D-Printers. These macros are supposed 
to ease the configuration of VORON printers and provide quality of life 
commands to assist in various scenarios (printer maintenance, nozzle 
preparation, ...).

## Background

While configuring my first VORON 2.4, I noticed that the config templates 
require a lot of manual tuning of coordinates in the config. Everything happens 
in one config file and it is pretty confusing at first. Also it requires to 
repeat some of the coordinates if you go strictly by the documentation inside 
those config templates.

This collection tries to reduce this effort by automatically calculating X/Y/Z 
positions based on the configured maximum build volume. It also provides a 
`print_start` and `print_end` macro that allows to hand in temperatures and a 
profile name. The printer will then automatically load or create a bed mesh 
with that profile name if it does not exist yet.

## Requirements

These macros require [klipper](https://github.com/KevinOConnor/klipper).
Also they are written for [VORON](http://vorondesign.com/) printers.
Technically the macros should work on any printer that homes to X/Y max and Z0.

## Disclaimer

> **Use at your own risk**

VORONklippper is provided as is. It is actively used and did not cause any 
issues so far. Despite that I am not responsible for any damage to your printer 
or your person inflicted while using the configurations or macros included.

Always make sure that the macros work with your machine and that all 
prerequisites are met to operate your printer safely (construction, firmware, 
calibration, ...).

## Installation

1. SSH into your PI
2. Switch to your `printer.cfg` directory
3. Clone the repositiory
   ```
   git clone https://github.com/rampage128/voron_klipper.git
   ```
4. Add an include to the top of your `printer.cfg`
   ```
   [include voron_klipper/main.cfg]
   ```

## Usage

To make use of VORONklipper you need to configure some parameters first.
In order to do so you have to add one gcode macro to your `printer.cfg`

```
[gcode_macro VORON_CONFIG]
variable_brush_active: false
variable_brush_x: 100
variable_brush_y: 350
variable_brush_z: 3
variable_brush_width: 50
variable_brush_orientation: x
variable_brush_strokes: 5
gcode:
```

Unfortunately klipper does not allow to create custom config sections/values. 
Adding this macro to your `printer.cfg` serves as a work-around to enable 
configuration of `VORONklipper` without having to edit the individual macro 
files directly.

VORONklipper provides macros for you to call from octoprint/mainsail or what 
ever UI you use. Usage of the individual macros can be found under the 
[Commands](#commands) section below.

Additionally you can make use of the built in `print_start` and `print_end` 
macros. In order to do so, just remove the ones you have in your `printer.cfg`.

## Commands
These are the commands that are meant to be directly used from outside 
(octoprint, ...).

### __clean nozzle__
```
CLEAN_NOZZLE X=<brush X position> Y=<brush Y position> Z=<brush top> L=<brush length> O=<Brush orientation x|y> [STROKES=<number of strokes>]
```
Will perform a cleaning routine with the nozzle on a brush. You can input the 
position and orientation of the brush as well as the amount of cleaning strokes.

The orientation of the brush can either be specified as `x` or `y`. It will be 
the axis along which the cleaning strokes will be performed. Originally this 
was planned to be an angle relative to the X-axis. Unfortunately klipper does 
not support sine and cosine functions, so we have to stick to two fixed axes.

By default the routine will perform 5 strokes (back and forth counts as 1).
You can override the amount by providing the `STROKES` parameter with your 
number of choice.

> _Please note:_ This command will automatically perform a homing routine if 
  the printer is currently not homed yet.

### __heat__
```
HEAT EXTRUDER=<extruder temperature> [BED=<bed temperature>] [BLOCK=<true|false>]
```
Will heat up the Extruder (and bed if a bed temperature is specified). By default 
this command will block the printer until the temperature is reached 
(M109/M190). You can specify `BLOCK=false` to avoid that.

### __load or create mesh__
```
LOAD_OR_CREATE_MESH [PROFILE=<profile name>]
```
This will load a bed mesh for the given profile name. If the bed mesh does not 
exist, it will be created and activated automatically. If you do not specify 
a profile name, the default profile will be used.

> _Please note:_ This command will automatically perform a homing routine if 
  the printer is currently not homed yet.

### __park__
```
PARK [X=<X coordinate>] [HOP=<Distance to hop>]
```
Will park the toolhead at the back of the build volume. You can specify `X` as 
absolute coordinate to move the toolhead on the X axis as well. If you do not 
provide `X`, the toolhead will stay on it's current X-coordinate. If you 
provide a value for `HOP`, the toolhead will move up by that amount _before_ 
commencing parking.

> _Please note:_ This command will automatically perform a homing routine if 
  the printer is currently not homed yet.

### __print end__
```
PRINT_END
```
Just retracts the filament, clears bed mesh and parks the toolhead at the back 
of the printer. To make it work you have to remove your own `print_end` from 
`printer.cfg`.

### __print start__
```
PRINT_START EXTRUDER=<extruder temperature> [BED=<bed temperature>] [PROFILE=<profile name>]
```
Macro to automate print starts for different profiles. You can call this from 
your slicer as start gcode. It will heat up the bed/extruder to the desired 
temperatures, perform QGL and nozzle cleaning and load the bed mesh for the 
given profile name. If there is no bed mesh yet, it will create one the first 
time you use that profile name. To make it work you have to remove your own 
`print_start` from `printer.cfg`.

> _Please note:_ If you do not provide a profile, no bed mesh will be used.

### __service__
```
SERVICE TYPE=<bed|extruder|nozzle|tool|z>
```
Moves the toolhead to a specific position in the build volume to allow easier 
servicing of various areas of the printer. Available types are:
- `bed`: Will move the toolhead to the X/Y center and move it all the way up. 
  For maximum space above the print bed.
- `extruder`: Will move the toolhead to the front and down and center it on X.
  For easy access to the filament/extruder.
- `nozzle`: Will move the toolhead to the front and up and center it on X.
  For easy access to the nozzle.
- `tool`: Will move the toolhead to the front and center it on X/Z.
  For access to the toolhead as a whole.
- `z`: Will center the toolhead on X/Y/Z. 
  For optimal access to all the Z-belts.

> _Please note:_ This command will automatically perform a homing routine if 
  the printer is currently not homed yet.


## Internal commands

There are some internal commands that are not meant to be used independently, 
but some of those might be helpful so here is an explanation:

### __center x/y__
```
CENTER_XY
```
Will center the toolhead on X and Y.


### __clear display__
```
UPDATE_DELAYED_GCODE ID=clear_display DURATION=10
```
Can be called like this to clear the printers display after the specified 
`DURATION`.

### __feedback__
```
FEEDBACK MSG="<some string>"
```
Will print a message as info into the console and onto the display (m117).
The messages in the console will be visible in octoklipper (and also show up 
in the octoprint navigation bar).

### __home__
```
HOME [OPTIONAL=<false|true>]
```
Will perform a G28 ... If parameter `OPTIONAL=true` is provided, it will only 
perform the G28 if the printer is not homed on one of the X/Y/Z axes.


## Contribute

Feel free to [open an issue](https://github.com/rampage128/voron_klipper/issues) 
or submit a PR