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
5. Add `VORONklipper` configuration (See [Usage](#usage) below)

## Updating

If you did not change the macro files, you can safely

1. SSH into your PI
2. Switch to your `VORONklipper` installation directory
3. Run
   ```
   git pull
   ```

## Usage

To make use of `VORONklipper` you need to configure some parameters first.
In order to do so you have to add one small gcode macro to your `printer.cfg`. 
Please read the [configuration document](docs/configuration.md) 
on how to do that and then continue with this page.

`VORONklipper` provides macros for you to call from octoprint/mainsail or what 
ever UI you use. Usage of the individual macros can be found in the 
[commands document](docs/commands.md).

Additionally you can make use of the built in `print_start` and `print_end` 
macros as well as the `homing_override`. In order to do so please consult the 
[overrides document](docs/overrides.md).

## Contribute

Feel free to [open an issue](https://github.com/rampage128/voron-klipper/issues) 
or submit a PR