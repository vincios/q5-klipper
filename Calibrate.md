# Calibration
This document adds some notes to official documentation and other tutorials/guides, linked in each paragraph. Always read the linked documents before.

Legend:

`📄document.ext` indicate a file on the files directory of this repository

`🔹parameter_name`/`🔹[section]` indicate a parameter or a section of the `printer.cfg` file.

### Some notes about calibration order
About **delta calibration**, klipper documentation says

> 1. The delta calibration is dependent on the precision of the tower endstop switches ([source](https://www.klipper3d.org/Delta_Calibrate.html#delta-calibration))
> 2. If using an automatic probe then first calibrate the probe and then check for a probe location bias ([source](https://www.klipper3d.org/Delta_Calibrate.html#automatic-vs-manual-probing))
> 3. If using a probe that is mounted on the side of the hotend (that is, it has an X or Y offset) then note that performing delta calibration will invalidate the results of probe calibration. [...] be sure to rerun probe calibration after any delta calibration ([source](https://www.klipper3d.org/Delta_Calibrate.html#automatic-vs-manual-probing))

About the **temperature**, the documentation is ambigous, because in the [Bed Leveling](https://www.klipper3d.org/Bed_Level.html#the-paper-test) page says 

> Always perform the paper test when both nozzle and bed are at room temperature!
 
And in the [Probe Calibration](https://www.klipper3d.org/Probe_Calibrate.html#temperature-bias) page says

> It is recommended to run the bed leveling tools at a consistent temperature to account for this bias. For example, either always run the tools when the printer is at room temperature, or always run the tools after the printer has obtained a consistent print temperature. In either case, it is a good idea to wait several minutes after the desired temperature is reached, so that the printer apparatus is consistently at the desired temperature.

But since the q5 bed badly warps when hot, **it's better to calibrate near the print temperature (180° nozzle / 60° bed)**.

So, the correct order to calibrate should be:

1. [Extruder Spring](#extruder-spring) / [Extruder Rotation Distance](#extruder)
2. [PID](#pid)
3. [Endstop Phase](#endstop-phase)
4. **Heat bed and nozzle to working temps and let stabilize (~ five minutes)**
5. [Probe calibration](#probe-calibrate) (with [location](https://www.klipper3d.org/Probe_Calibrate.html#location-bias-check) and [temperature](https://www.klipper3d.org/Probe_Calibrate.html#temperature-bias) bias checks)
6. [Basic delta calibration](#basic-delta-calibration)
7. [Enhanced delta calibration](https://www.klipper3d.org/Delta_Calibrate.html#enhanced-delta-calibration)
8. [Probe calibration](#probe-calibrate) again
9. [Bed Mesh](#bed-mesh)

## Extruder Spring
[My Q5 Calibration tips](https://gist.github.com/vincios/b17958ad331af8cf458c066ca46a3da9#tensione-molla-estrusore)

## Motor current
[All3dp article](https://all3dp.com/2/vref-calculator-tmc2209-tmc2208-a4988/)

For Q5 stepper motor, set `🔹run_current` to `0.9`.


## Rotation distance
[Official Documentation](https://www.klipper3d.org/Rotation_Distance.html)


### Axis
The stock firmware `📄robin_nano_config.txt` set axis step_per_mm to 80, and the stock Q5 motor is a 1.8 degree stepper (`📄Motor 42SHDC4047Z-23SY Parameter Diagram.pdf`).

So according to [obtaining rotation_distance from steps_per_mm (or step_distance)](https://www.klipper3d.org/Rotation_Distance.html#obtaining-rotation_distance-from-steps_per_mm-or-step_distance), set `🔹[stepper_abc]` `🔹rotation_distance` to:

- `40` if `🔹microsteps` is 16
- `80` if `🔹microsteps` is 32


### Extruder
[Official documentation](https://www.klipper3d.org/Rotation_Distance..html#calibrating-rotation_distance-on-extruders)

[Ellis' guide](https://ellis3dp.com/Print-Tuning-Guide/articles/extruder_calibration.html) (**Recommended**)

### Endstop phase
[My Q5 calibration tips](https://gist.github.com/vincios/b17958ad331af8cf458c066ca46a3da9#endstop-phase-solo-klipper)

[Official documentation](https://www.klipper3d.org/Endstop_Phase.html#calibrating-endstop-phases)

### Basic Delta Calibration
[My Q5 calibration tips](https://gist.github.com/vincios/b17958ad331af8cf458c066ca46a3da9#delta-calibration)

[Official documentation](https://www.klipper3d.org/Delta_Calibrate.html#basic-delta-calibration)

Use Manual mode

```
DELTA_CALIBRATE METHOD=manual
```

It will ask you to test 7 points with the [paper test](https://www.klipper3d.org/Bed_Level.html#the-paper-test). 

Start going down until the nozzle block the paper, then with microstepping go up until the paper starts moving freely but with some amount of friction. The exact amount of friction felt isn't crucial, just try to obtain the same amount of friction each time one runs the test.

### Probe calibrate
[My Q5 calibration tips](https://gist.github.com/vincios/b17958ad331af8cf458c066ca46a3da9#z-offset)

[Official documentation](https://www.klipper3d.org/Probe_Calibrate.html#calibrating-probe-z-offset)

[Ellis' guide](https://ellis3dp.com/Print-Tuning-Guide/articles/first_layer_squish.html)

> [!NOTE]
> X and Y offsets are already configured, no need to touch it unless the probe was changed.

Calibrate the z-offset using the official guide, then check with SuperSlicer first layer test. 

If the z-offset is too high (holes on the print) adjust it with Mainsail babystepping options and reprint. 

As soon as the print have no more holes, the z-offset is ok. Save the the offset using the `Z_OFFSET_APPLY_ENDSTOP` command, under the save `💾` menu.

Lastly perform the Ellis' guide test.

> [!NOTE]
> You can ignore the Location Bias Check and Temperature Bias. Optionally do a [Repeatability check](https://www.klipper3d.org/Probe_Calibrate.html#repeatability-check).

## Bed Mesh
[Official documentation](https://www.klipper3d.org/Bed_Mesh.html#calibration)

To increase bed shape precision, prefer more probing points and less interpolation points.

A good configuration should be between 7 or 9 `🔹round_probe_count` points.

A good configuration

```
[bed_mesh]
speed: 50
horizontal_move_z: 25
mesh_radius: 90
mesh_origin: 0,0
round_probe_count: 9
fade_start: 1.0
fade_end: 10.0
mesh_pps: 2,2
algorithm: bicubic
```

To make more profiles, use

```
BED_MESH_CALIBRATE PROFILE=<name> 
```

Default profile is named `default`.

> [!TIP]
> Don't forget to load the mesh on your slicer start gcode
> ```
> BED_MESH_PROFILE LOAD=<name> 
> ```


## PID
[Offical Guide](https://www.klipper3d.org/Config_checks.html#calibrate-pid-settings)

## Pressure Advance

[Official documentation](https://www.klipper3d.org/Pressure_Advance.html)

[Ellis' Guide](https://ellis3dp.com/Print-Tuning-Guide/articles/pressure_linear_advance/tower_method.html)

[My Q5 Tips](https://gist.github.com/vincios/b17958ad331af8cf458c066ca46a3da9#linear-advance--pressure-advance)

