# Overrides - VORONklipper
> A guide to useful VORONklipper presets

Overrides are tuned versions of standard klipper commands or actual overrides 
of gcode commands that should probably be like this by default (at least for 
VORON printers).

Since klipper loads and applies the config from top to bottom. That means 
that in general any macro or override you have in your `printer.cfg` will be 
used instead of the ones provided by `VORONklipper`.
To make use of the included ones, you have to remove the corresponding entries 
from your `printer.cfg`.

## __homing override__
`VORONklipper` comes with its own `homing_override`. This changes the behaviour 
of the Z homing. This is a convenience to allow you to configure the X/Y 
coordinates of your Z-endstop pin in an easy to read place instead of modifying 
or writing your own override.

Besides that the included override also only homes X/Y again, if it has not 
been homed already and does not contain any unnecessary move commands (like 
centering the toolhead). This is done to save time when retrieving a new 
Z-offset during `print_start`. You can still force homing X/Y with a 
`G28 X0 Y0` if you feel the need to.

> To make it work you have to remove your own `homing_override` from 
`printer.cfg`.

> This override needs [configuration](configuration.md#endstop)!

## __print cancel__
```
PRINT_CANCEL
```
Just clears bed mesh and parks the toolhead at the back of the printer. Useful 
as gcode for the Octoprint cancel print feature.

> Please note: This macro will not retract your filament. This is done to 
prevent an error if the extrusion temperature is below klippers limit. 

## __print end__
```
PRINT_END
```
Just retracts the filament, clears bed mesh and parks the toolhead at the back 
of the printer. 

> To make it work you have to remove your own gcode macro 
`print_end` from `printer.cfg`.

## __print start__
```
PRINT_START EXTRUDER=<extruder temperature> [BED=<bed temperature>] [PROFILE=<profile name>] [CHAMBER=<chamber temperature>] [CLEAN=true]
```
Macro to automate print starts for different profiles. You can call this from 
your slicer as start gcode. 

It will 
1. heat up the bed/extruder/chamber to the desired temperatures
2. perform QGL 
3. perform nozzle cleaning if `CLEAN=true` is specified
4. load the bed mesh for the given profile name if `PROFILE` parameter is 
   specified

This macro has an optimized order of actions. That means heating will start 
emmideately while the printer does its initial homing. It will then center over 
the print bed and wait for the target temperature. 

QGL and nozzle cleaning can throw off the Z-offset, so this macro ensures that 
after every relevant step Z is homed again to have a correct Z-offset for the 
next step. It is highly recommended, that you use the built in 
`homing_override` instead of your own if possible, because it skips rehoming 
X/Y after every step. Especially after cleaning the nozzle this is important, 
because new plastic could ooze out of the nozzle and throw off the offset.

> _Please note:_ 
> - If you do not provide a profile, no bed mesh will be used.
> - Using `CLEAN=true` needs [configuration](configuration.md#nozzle_cleaning)!
> - You have to define a `M141` gcode override for the `CHAMBER` parameter to 
>   work.

> To make it work you have to remove your own gcode macro `print_start` from `printer.cfg`.