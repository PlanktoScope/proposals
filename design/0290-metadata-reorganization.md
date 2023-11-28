# Proposal: Metadata organization improvement

Author(s): Laurent Vallet ([@LaurentPV](https://github.com/LaurentPV)), Ethan Li ([@ethanjli](https://github.com/ethanjli))

Last updated: 2023-11-27

Discussion at: <https://github.com/PlanktoScope/PlanktoScope/issues/290>

## Abstract

This design document proposes a reorganization of the metadata fields into three JSON files, in order to:

- Have a more logical organization of metadata fields between the configuration files, to make it easier to find specific fields.
- Make it easy for a user to share their `config.json` file, so that they can their Planktoscope settings can be reused on other machines or by other users, but without sharing any personal information or specific hardware configurations.
- Have a more precise `hardware.json` file for debugging and for holding all information about a specific machine (e.g. which will be useful for FairScope).
- Create a `personal_info.json` file which eventually could be encrypted and used to commit modifications to the machine, export directly to Ecotaxa, or provide other other user-oriented features in the future.
- Improve data quality by adding more relevant scientific variables to the metadata.

## Background

Currently, the PlanktoScope software stores metadata in two configuration files:

- [`hardware.json`](https://github.com/PlanktoScope/device-backend/tree/v2023.9.0-beta.1/default-configs)
  - This file contains parameters to configure the hardware, but it does not describe the characteristics of the hardware.
  - Depending on the PlanktoScope hardware version selected by the user in the Node-RED dashboard, a `hardware.json` file for that version is copied from `/home/pi/device-backend/default-configs` to `/home/pi/PlanktoScope`.
  - Here is an example of the contents of the `hardware.json` file:
    ```
    {
      "stepper_reverse": false,
      "microsteps": 256,
      "focus_steps_per_mm": 40,
      "pump_steps_per_ml": 2045,
      "focus_max_speed": 5,
      "pump_max_speed": 50,
      "stepper_type": "pscope_hat",
      "red_gain": 2.4,
      "blue_gain": 1.35,
      "analog_gain": 1.0,
      "digital_gain": 1.0,
      "acq_fnumber_objective": 12,
      "process_pixel_fixed": 0.88
    }
    ```
    
- [`config.json`](https://github.com/PlanktoScope/PlanktoScope/tree/software/v2023.9.0-beta.1/software/node-red-dashboard/default-configs)
  - This file contains the GUI parameters selected by the user in the Node-RED dashboard for data acquisition.
  - Depending on the PlanktoScope hardware version selected by the user in the Node-RED dashboard, a `hardware.json` file for that version is copied from `/home/pi/PlanktoScope/software/node-red-dashboard/default-configs` to `/home/pi/PlanktoScope`
  - Here is an example of the contents of the `config.json` file:
    ```
    {
      "sample_project": "Project's name",
      "sample_id": 1,
      "sample_ship": "Vessel name",
      "sample_operator": "Operator's name",
      "sample_sampling_gear": "net",
      "sample_gear_net_opening": 40,
      "acq_id": 1,
      "acq_instrument": "PlanktoScope v2.5",
      "acq_celltype": 300,
      "acq_minimum_mesh": 10,
      "acq_maximum_mesh": 200,
      "acq_volume": 1,
      "object_depth_min": 1,
      "object_depth_max": 2,
      "process_id": 1,
      "nb_frame": 100,
      "sleep_before": 0.5,
      "imaging_pump_volume": 0.01
    }
    ```

Both configuration files are used by the Node-RED dashboard.
We have a ["Metadata Compilation" spreadsheet](https://docs.google.com/spreadsheets/d/1TSIaOFEIMvvYyqAFrsiZxVtGXZvWVdZbWO_LU-2A_TE/edit?usp=drive_link) which describes every metadata field, including metadata fields which are persistently stored in those files and metadata fields which are set by the user via the Node-RED dashboard but not persisted in files.
Both types of metadata fields are used to generate a `metadata.json` file which is exported by the Python backend's `ImagerProcess` module as part of image acquisition.
Only fields from our "Metadata Compilation" spreadsheet with field names containing one of the following prefixes are exported to the `metadata.json` file:
- `sample_`
- `acq_`
- `object_`
- `process_`

## Proposal

We propose to add a third file, to be named `personal_info.json`, which will store the user's personal information and information about the scientific mission for which the PlanktoScope is being operated.
We also propose to add some more metadata fields to improve the metadata exported to Ecotaxa.
Finally, we propose to reorganize existing metadata fields between three files, according to the following rules: 

- The `hardware.json` file should only have information about the characteristics of the machine.
  This file should not be modified by the user except for selecting the PlanktoScope's hardware version and the machine's serial number, and only when we cannot retrieve the information automatically (such as from the custom PlanktoScope HAT's EEPROM).
  - Here is an example of our proposal for the contents of the `hardware.json` file:
    ```
    {
      "inst_serial_number": "U072",
      "acq_inst_name": "PlanktoScope",
      "acq_inst_version": "v2.6.1",
      "acq_rpi_model": "Raspberry Pi 4 4Gb",
      "acq_camera_model": "Raspberry Pi High Quality Camera",
      "acq_HAT_model": "FairScope_HAT v1.3",
      "acq_objective_focal_length": 12,
      "acq_tube_focal_length": 25,
      "acq_LED_model": "Adafruit - 754",
      "acq_pump_model": "Kamoer - KAS B12 SF",
      "acq_flowcell_model": "FairScope Capillary - 300 um",
      "microsteps": 256,
      "pump_max_speed": 50,
      "pump_steps_per_ml": 2045,
      "stepper_reverse": false,
      "process_id": "1",
      "process_pixel_size": 0.75
    }
    ```
- The `config.json` file should only store acquisition settings and camera settings selected by the user via the GUI.
  Exchanging this file should assure a comparable result.
  - Here is an example of our proposal for the contents of the `config.json` file:
    ```
    {
      "acq_camera_iso": 100,
      "acq_focus_max_speed": 5,
      "acq_camera_shutter_speed": 125,
      "acq_camera_white_balance": false,
      "acq_volume_interframe": 0.01,
      "acq_nb_frames" : 10,
      "focus_steps_per_mm": 40,
      "sleep_before": 0.5,
      "object_camera_gain_analog": 1,
      "object_camera_gain_digital": 1,
      "object_camera_gain_red": 1.5,
      "object_camera_gain_blue": 1.9
    }
    ```
- The `personal_info.json` file should store information entered by the user (via the GUI) about the identity of the user/team, information about the mission where the PlanktoScope is being deployed, and Ecotaxa login credentials for the PlanktoScope software to interact with the Ecotaxa API (especially for exporting data directly to EcoTaxa).
  This file could be shared between team members using Planktoscopes for the same mission, to assure that an identical protocol/equipment used information.
  - Here is an example of our proposal for the contents of the `personal_info.json` file:
    ```
    {
      "sample_project": "FairScope Factory Settings",
      "sample_operator": "Thibaut Pollina",
      "sample_vessel": "La Baraka",
      "sample_method": "Pump Samplers",
      "sample_id": "Sample_1",
      "sample_net_mesh_size": 20,
      "sample_sieve_mesh size": 200,
      "sample_net_mouth_diameter": 30,
      "acq_id": "Tank_B",
      "object_depth_min": 0,
      "object_depth_max": 0,
      "process_id": "1"
    }
    ```
  - This new file could be stored at `/home/pi/PlanktoScope/`, which is where the two other configuration files are currently stored.

## Rationale

Having three files instead of two to simplify and reorganize the metadata may seem to be counterproductive.
An alternative solution could be to improve the organization of the json file with nested objects, such as `personal_info: {"key":"value", ...}` and `settings:{"key":"value", ...}`.
The key aspect of this proposal is to know where to go when debugging, modifying the use of the Planktoscope; and to be more user-friendly in order to facilitate the growth of a community of users who aren't software developers.
The use and limitations of prefixes (`acq_`, `object_`, `sample_`, `process_`) suppose that we respect EcoTaxa's specifications.

## Compatibility

There should be no compatibility issue unless we couple this proposal with the loading of `hardware.json` with caracteristics stored in the HAT EEPROM.

## Implementation

The implementation impacts both the Node-RED dashboard and the Python backend.

### Python Backend
The following files will be impacted:

- `device-backend/control/pscopehat/planktoscope/imager/_init_.py`
- `device-backend/control/adafruithat/planktoscope/imager/_init_.py`
- `device-backend/control/pscopehat/planktoscope/stepper.py`
- `device-backend/control/adafruithat/planktoscope/stepper.py`

These files will need to be modified to:

- Load the `config.json` file in addition to the `hardware.json` file, in order to retrieve the metadata field values which this proposal moved from `hardware.json` latter file to the `config.json` file.
- Change the names of metadata fields according to this proposal.

### Node-RED Dashboard 
The following files will be impacted:

- `adafruithat.json`
- `pscopehat.json`

The files will need to be modified to:

- Load the new variables implemented in `hardware.json` and `config.json`.
- Rename global variables in the Node-RED flows to match the metadata field names in the JSON files.
- Create the `personal_info.json` file if it does not already exist.
- Load metadata fields from `personal_info.json` as global variables in the Node-RED flows.
- Add new GUI input fields to set the values of new metadata fields as needed.
- Write metadata values to the three JSON files at the end of each process (Sample, Optic Configuration, Fluidic Acquisition)


## Open issues (if applicable)

- Maybe in a community-based meeting we should iterate on this proposal with recommendations from users, e.g. about which metadata fields they would like to retrieve with their exported data.
- Is there a version of the software that is not compatible with an old version of the hardware (to evaluate the compatibility issues)?

