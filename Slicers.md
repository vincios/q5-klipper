# Slicers configuration
Some tips to configure Slicers with Klipper


## All
To all printers set

- `🔸End GCODE` to

    ```gcode
    END_PRINT
    ```

> [!TIP]
> `🔸Start G-code` is slicer dependent since need to use slicer temperature variables

## SuperSlicer

- Set  `Printer Settings` -> `Custom G-code` -> `🔸Start G-code` to

    ```gcode
    START_PRINT EXTRUDER_TEMP=[first_layer_temperature] BED_TEMP=[first_layer_bed_temperature]
    ```

- Enable `Printer Settings` -> `Custom G-code` -> `🔸Only custom Start G-code`
- Set `Printer Settings` -> `General` -> `🔸G-code flavor` to **Klipper**
- Set `Printer Settings` -> `Extruder 1` -> `🔸Seam gap` to **0**. Do this also to the `Print settings`.

## OrcaSlicer

- Set  `Printer Settings` -> `Machine G-code` -> `🔸Machine Start G-code` to

    ```gcode
    M140 S0
    M104 S0
    START_PRINT EXTRUDER_TEMP=[nozzle_temperature_initial_layer] BED_TEMP=[bed_temperature_initial_layer_single]
    ```

- Set `Printer Settings` -> `Basic Information` -> `🔸G-code flavor` to **Klipper**
- Under the `Process` section, set `Quality` -> `🔸Seam gap` to **0**.