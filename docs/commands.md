# Commands - VORONklipper
> A macro guide for VORONklipper

This document contains information on the commands (gcode macros) that 
`VORONklipper` provides for your use. For completeness there is also a list 
of internal commands that are not meant to be called directly.

## User Commands
These commands are meant to be called directly for your convenience.

### __clean nozzle__
```
CLEAN_NOZZLE
```
Will perform a cleaning routine with the nozzle on a brush. The position and 
orientation of the brush as well as the amount of cleaning strokes are defined 
in the [VORONklipper configuration](configuration.md#nozzle_cleaning).

> _Please note:_ 
> - This command will automatically perform a homing routine if 
>   the printer is currently not homed yet.
> - The brush has to be enabled in the [configuration](configuration.md#nozzle_cleaning).

### __heat__
```
HEAT [EXTRUDER=<extruder temperature>] [BED=<bed temperature>] [CHAMBER=<chamber temperature>] [BLOCK=<true|false>]
```
Will heat up the extruder/bed/chamber if the corresponding parameter is 
specified. By default this command will block the printer until the temperature 
is reached (M109/M190). You can specify `BLOCK=false` to avoid that.

> Please note:
> - You have to define a `M141` gcode override for the `CHAMBER` parameter to 
>   work.
> - The chamber command will never block since that is impractical with a 
>   passively heated chamber (temperature controlled by an exhaust fan only).

### __home__
```
HOME [OPTIONAL=<false|true>]
```
Will perform a G28 ... If parameter `OPTIONAL=true` is provided, it will only 
perform the G28 if the printer is not homed on one of the X/Y/Z axes.

### __load or create mesh__
```
LOAD_OR_CREATE_MESH [PROFILE=<profile name>]
```
This will load a bed mesh for the given profile name. If the bed mesh does not 
exist, it will be created and activated automatically. If you do not specify 
a profile name, the default profile will be used.

> _Please note:_ 
> - This command will automatically perform a homing routine if 
>   the printer is currently not homed yet.
> - This command is not functional since loading a non-existing profile 
>   currently causes an error in klipper :-(

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

### __check config__
```
CHECK_VORON_CONFIG
```
This macro will raise an error and link to the [configuration document](configuration.md) 
if the configuration macro is not present. It is used in other macros to 
prevent further execution of gcode if the required configuration is missing.

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

