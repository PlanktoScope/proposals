# Proposal: Use the PlanktoScope HAT EEPROM chip to store hardware configuration

Author(s): Vallet, Laurent; Le Corre, Cléa Last updated: 30/10/2024 Discussion at https://github.com/PlanktoScope/PlanktoScope/issues/292

## Abstract

The following document proposes to store the PlanktoScope (PS) hardware configuration in the 32 Kbits EEPROM chip of the PlanktoScope HAT in order to : 
* automatically save the hardware state of the machine
* easily retrieve the hardware configuration for debugging
* feed the metadata with precise hardware information

## Background

A specific HAT was developped to satisfy the PlanktoScope needs. 
This PlanktoScope HAT connected to the raspberry pi and all of the external devices (led, motors, pump, camera) contains a 32 Kbit EEPROM chip which is a "type of non-volatile ROM that enables individual bytes of data to be erased and reprogrammed" (Wikipedia).
It is therefore possible to store some data inside this chip and to read it back.

For now (Sunray version 2024.0.0-beta.2) some of the hardware configuration of the PlanktoScope is entered by the user in the GUI.
We would like to have an hardware configuration specific to each machine in case some specific modification is made.
We would like to store this configuration in a safe place, independent from the software installed on the raspberry pi.

## Proposal

We therefore propose to write a python script that allows the storage and the reading of the PlanktoScope hardware information on the EEPROM chip.

This information stored should contain : 
* PlanktoScope reference
* Plantoscope serial number
* PlanktoScope version
* Plantoscope fabrication date
* HAT serial number
* HAT version
* Driver reference
* Pump reference
* Stepper focus reference
* Objective lens reference
* Tube lens reference
* Flowcell thickness
* LED reference

Those informations could be used to feed the metadata stored in the hardware.json file as described in the issue 290 (https://github.com/PlanktoScope/PlanktoScope/issues/290).

## Rationale

The EEPROM chip currently used for the HAT has a capacity of 32Kbits. This provides adequate space for the necessary configuration without redundancy or excessive overhead.
The use of our HAT is relevant as the PlanktoScope HAT is linked to every device of the machine. This allows unique data storage for each PlanktoScope unit, helping track individual hardware changes or updates.
The EEPROM on the HAT is accessible directly through the I2C bus of the Raspberry Pi, simplifying software development.

This solution is exclusive to the PlanktoScope HAT, limiting compatibility with other configurations (e.g., Adafruit HATs without EEPROM).
Though currently sufficient, 32Kbits could become a constraint if future features or expanded data storage needs arise.

## Compatibility

The main compatibility issue concerns the adafruit HAT users.
For those, a simple solution would be to continue selecting their configuration in the GUI, or adapt the script to store informations in the Raspberry pi EEPROM chip in case of not finding the PlanktoScope HAT EEPROM adress.
The PlanktoScope versions V2.5 and V2.6 do not natively support writing data to the EEPROM chip because the write-protect (WP) pin of the EEPROM is enabled. To disable write protection, a solder point must be applied to the WP pin.

## Implementation
The EEPROM project has reached a functional stage, with all desired actions for writing and reading hardware information now operational. The next phase will focus on integrating this functionality into the broader PlanktoScope software, allowing users to access and manage their device's hardware information throughout its lifecycle. This integration will enhance the user experience, offering consistent hardware management for improved maintenance and lifecycle tracking of each PlanktoScope device.

## Open issues (if applicable)
One current limitation in this project is the requirement to manually solder a connection on the Write Protect (WP) pin of the EEPROM chip to enable writing operations. This step introduces some complexity and potential error for users or technicians during the assembly or modification process.

To address this, a planned improvement is to incorporate a bridge between the WP pin and GPIO pin 4 on the Raspberry Pi. By controlling the write protection via software, this enhancement would streamline write operations by allowing the program to enable or disable the WP functionality dynamically. This improvement would enhance flexibility and reduce the need for physical alterations.
