# Configuration - VORONklipper
> A config guide for VORONklipper

Even though `VORONklipper` tries to determin positioning mathematically instead 
of relying on modification of coordinates inside of gcode macros, some elements 
need explicit configuration. This applies to features that require safety (like 
the `homing_override`) and features that use additional user installed hardware 
(like a nozzle brush).

Unfortunately klipper does not allow to create custom config sections/values. 
This is why `VORONklipper` uses a gcode macro as a workaround. This gcode macro 
does not contain any gcode, but other macros can access its variables and use 
them as a configuration.

This approach has some advantages:
- The `VORONklipper` files do not have to be changed to configure your printer.
- You can update the macros without breaking your configuration.
- No more complicated editing of gcode to configure your printer motion.
- The configuration for all the macros is compact and easily readable in the 
  `printer.cfg`, where it belongs.
  
## The VORON_CONFIG macro

```
[gcode_macro VORON_CONFIG]
variable_z_endstop_x: 231
variable_z_endstop_y: 347

variable_brush_active: "true"
variable_brush_x: 100
variable_brush_y: 350
variable_brush_z: 3
variable_brush_width: 50
variable_brush_orientation: "x"
variable_brush_strokes: 5
gcode:
```

The code above is the macro in question. You need to add this to your 
`printer.cfg` to make use of `VORONklipper`. If you forget, 
your printer will throw an error and link to this page.

See below for an explanation of the different config values.

## Config values

### Endstop
Instead of editing a `homing_override` section yourself, you can remove it from 
your `printer.cfg` and instead use the one provided by `VORONklipper`:
- `variable_z_endstop_x`: The X position of your Z-endstop pin.
- `variable_z_endstop_y`: The Y position of your Z-endstop pin.

### Nozzle cleaning
`VORONklipper` includes a cleaning script for your nozzle. You can specify 
the location, size and orientation of your nozzle as well as the number of 
strokes to clean with.

- `variable_brush_active`: Set to `"true"` to activate nozzle cleaning during 
`print_start`.
- `variable_brush_x`: The X position of the center of your brush.
- `variable_brush_y`: The Y position of the center of your brush.
- `variable_brush_z`: The height to wipe the nozzle at (pick a height that lets 
   the nozzle wipe through the brush, but does not hit the carriage itself).
- `variable_brush_width`: The width of the brush (this will determine the length 
   of the wiping strokes performed).
- `variable_brush_orientation`: `"x"` or `"y"`. The axis to perform the wiping 
  strokes along.
- `variable_brush_strokes`: The number of strokes to wipe with (back and forth 
  counts as 1 stroke).