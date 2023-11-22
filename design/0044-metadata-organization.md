# Proposal: Metadata organization improvement

Author(s): Laurent Vallet Last updated: 22/11/2023 Discussion at https://github.com/PlanktoScope/PlanktoScope/issues/44

## Abstract

This design document proposes a reorganization of the metadata variables around 3 json files in order to : 
- Improve the logic behind the configuration files to facilitate the search of a specific variable
- Share easily the config.json file to apply Planktoscope settings to another user without any personal information or specific hardware configuration
- Use a more precise hardware.json file to debug and have all the information about a specific machine for Fairscope use
- Improve data quality by adding more relevant scientific variables to the metadata
- Create a personal_info.json file that could be later on encrypted and used to commit modifications to the machine, export directly to Ecotaxa and other user oriented future features

## Background

For now, the PlanktoScope software uses 2 configuration files : 
- hardware.json stored in https://github.com/PlanktoScope/device-backend/blob/main/default-configs/
- config.json stored in https://github.com/PlanktoScope/PlanktoScope/blob/master/software/node-red-dashboard/default-configs/

The hardware.json file contains parameters to configure the hardware but not the caracteristics of the hardware.
The config.json file contains the GUI parameters selected by the user to make the acquisition of the data.
Both are used by the Node Red flows.
The variables stored in these files and/or set by the user via the Node Red interface are used to generate the metadata.json file that is exported by the acquisition process.

An inventory of all the metadata was made : https://docs.google.com/spreadsheets/d/1TSIaOFEIMvvYyqAFrsiZxVtGXZvWVdZbWO_LU-2A_TE/edit?usp=drive_link 
Only variables with the prefixes sample_, acq_, object_, process_ are exported to the metadata.json.

## Proposal

We propose to add a third file with the personal informations of the user and its scientific campaign : personal_info.json
We also propose to add metadata variables that could improve informations contained in Ecotaxa concerning the data acquired with the Planktoscope.
We finally propose to dispatch existing metadatas between those 3 files according to the following rules : 

- The hardware.json file should only have informations concerning the caracteristics of the machine.
  This file should not be modified by the user except for the hardware case version of the planktoscope and its serial number where we cannot retrieve automatically the information
  
- The config.json file should only store acquisition settings and camera settings selected by the user via the GUI
  Exchanging this file should asssure a comparable result.
  
- The personal_info.json file should store informations entered by the user via the GUI on the id of the user/team, campaign data informations and Ecotaxa login infos in order to use Ecotaxa API to directly export data.
  This file could be used between team members using Planktoscopes for the same campaign/study to assure an identical protocol/equipment used information.
  
Here is the current content of hardware.json : 
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
Here is what we propose for its content : 
{
  "inst_serial_number": "U072",
  "acq_inst_name": "PlanktoScope",
  "acq_inst_version": "v2.6.1",
  "acq_rpi_model": "Raspberry Pi 4 4Go",
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

Here is the current content of config.json : 
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
Here is what we propose for its content : 
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

Here is an example of the content of personal_info.json : 
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

This new file could be created at home/pi/PlanktoScope/

## Rationale

Having 3 files instead of 2 to simplify and reorganize the metadata may seem to be counter productive.
Maybe just a proper formatting of the json file with personal_info: {"key":"value", ...}, settings:{"key":"value", ...} can be another solution.
The key aspect of this proposal is to know where to look at when debugging, modifying the use of the Planktoscope and to be more user friendly in order to facilitate the growth of a "non-developpers community based users".
The use and limitations of prefixes (acq_, object_, sample_, process_) suppose that we respect EcoTaxa's specifications.

## Compatibility


## Implementation

The implementation impacts both Node Red and device-backend.

### Device Backend
The files impacted are listed below : 
device-backend/control/pscopehat/planktoscope/imager/_init_.py
device-backend/control/adafruithat/planktoscope/imager/_init_.py
device-backend/control/pscopehat/planktoscope/stepper.py
device-backend/control/adafruithat/planktoscope/stepper.py

The modification consists of loading the config.json file in addition of the hardware.json to retrieve the variables that we moved from one another.
Changing also the name of the variables if their names were modified.

### Node Red 
The files impacted are listed below :
adafruithat.json
pscopehat.json

The modification involves loading the new variables implemented in hardware.json and config.json, renaming the variables loaded in global context of node red to match the names in the json files.
Creating the personal_info.json if it does not exists.
Loading personal_info metadatas in the global context.
Adding new fields to retrieve these new metadatas in the GUI.
Writing these metadatas in the 3 json files at the end of each process (Sample, Optic config, Fluidic acquisition)


## Open issues (if applicable)

Maybe in a community based meeting we should fuel this proposal with recommandations from users, which metadata they would like to retrieve with their exported datas.

