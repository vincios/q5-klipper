# Calibration
This document adds some notes to official documentation and other tutorials/guides, linked in each paragraph. Always read the linked documents before.

Legend:

`📄document.ext` indicate a file on the files directory of this repository

`🔹parameter_name`/`🔹[section]` indicate a parameter or a section of the `printer.cfg` file.


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

### Delta Calibration
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

