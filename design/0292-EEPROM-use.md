# Proposal: Use the Planktoscope HAT EEPROM chip to store hardware configuration

Author(s): Vallet, Laurent Last updated: 27/11/2023 Discussion at https://github.com/PlanktoScope/PlanktoScope/issues/292

## Abstract

The following document proposes to store the Planktoscope (PS) hardware configuration in the 32 Kbits EEPROM chip of the Planktoscope HAT in order to : 
* automatically save the hardware state of the machine
* easily retrieve the hardware configuration for debugging
* feed the metadata with precise hardware information
* in the future replace the park machine file with a database fed by the user's machine

## Background

A specific HAT was developped to satisfy the Planktoscope needs. 
This Planktoscope HAT connected to the raspberry pi and all of the external devices (led, motors) contains a 32 Kbit EEPROM chip which is a "type of non-volatile ROM that enables individual bytes of data to be erased and reprogrammed" (Wikipedia).
It is therefore possible to store some data inside this chip and to read it back.

For now (Sunray version 2023.9.0-beta.1) the hardware configuration of the Planktoscope is entered by the user in the GUI and contains only the hardware version of the Planktoscope and the stepper version.
We would like to have an hardware configuration specific to each machine in case some specific modification is made and that do not recquire the user to enter any spacific information (except the Planktoscope case version).
We would like to store this configuration in a safe place, independent from the software installed on the raspberry pi.

## Proposal

We therefore propose to write a python script that launches at boot and feed the EEPROM chip with all the hardware configuration retrieved.
This configuration should contain : 
* Raspberry Pi version
* Memory use
* HAT version
* LED type
* Pump motor type
* Focus motor type

Those informations could be used to feed the metadata stored in the hardware.json file as described in the issue 290 (https://github.com/PlanktoScope/PlanktoScope/issues/290).

## Rationale

The raspberry pi 4 already as an EEPROM chip of 512 Kbit.
This EEPROM is in use by the raspberry, it has some memory available but it demands extra work for us to know which memory adress is free on the chip at the time we want to store data on it, and especially if it is at the boot of the Planktoscope we would need to know how the raspberry pi uses its EEPROM.

The use of our HAT is relevant as the PS HAT is linked to every device of the machine.
This proposal concerns only Planktoscope that uses our PS HAT and not the adafruit HAT. Not every adafruit HAT has an EEPROM chip.

## Compatibility

The main compatibility issue concerns the adafruit HAT users.
For those, a simple solution would be to continue selecting their configuration in the GUI, or adapt the script to store informations in the Raspberry pi EEPROM chip in case of not finding the PS HAT EEPROM adress.

## Implementation

The EEPROM chip model of the PS HAT is m24c32, documentation is available on the Fairscope drive : https://drive.google.com/file/d/132teUB6DbyinUFwnRRZi6CEhJYIZ_4s_/view?usp=drive_link
When connecting to the Planktoscope, you can via the interface open the cockpit GUI that lets you access a terminal to execute commands.

The command line to retrive EEPROM address is : 
```
sudo i2cdetect -y 1
```

The Raspberry pi EEPROM chip address is 0x50 by default
The PS HAT EEPROM address seems to be 0x64

Staring from a memory address like 0x50 it is possible to write each character of a string on a byte and to read the stored data staring from the 0x50 address.

There is an eeprom library existing : https://github.com/ARizzo35/python-eeprom
There is an smbus2 library that is working fine on the tests made to write data on the EEPROM : https://github.com/Gadgetoid/py-smbus/tree/master

If using the smbus2 python library to write on the EEPROM the SMBUS_MAX_BLOCK (maximum size of the block of data that we can write in one time) is 32 bytes.

The following code is to know if the EEPROM contains data : 
```
bus = smbus2.SMBus(1)

value = bus.read_byte_data(eeprom_adress, memory_adress)

if value == 0xFF:
print("The EEPROM chip is probably empty.")
else :
print("The EEPROM chip probably contains data.")

# To know which byte is occupied on the EEPROM (the 0x00 address can be modified depending on where you planned to write data)
data = []
with smbus2.SMBus(1) as bus :
data = bus.read_i2c_block_data(eeprom_adress, 0x00, 32)
print("Données de l'EEPROM: ", data)
```

Using the schematics of the printed circuit of the HAT (https://github.com/PlanktoScope/PlanktoScope/blob/master/hardware/v2.5/hat/Planktoscope-Hat-Schematic.pdf), we can read the GPIO mapping that could help us interrogate the PS HAT in order to retrieve hardware information.
We can see the following mapping (SDA = serial data, SDI = serial data in, SDO = serial data out) : 
* LED is on pin 3 of the GPIO (I2C1)
* EEPROM is on pin 27 (EEPROM_SDA)
* Controller 1 and 2 are on pin 21 (SPIO_MISO)

## Open issues (if applicable)

The tests that were made blocked on the reading of the stored data. 
The PS HAT documentation does not contain information on the encoding applied and all the encoding we tried gave us no or strange results (the byte data seems to not corresponds to the stored data).
The use of the subprocess library allowed us to retrieve easily raspberry pi version and memory informations.
The use of the smbus library gave us good results in writing those informations on the EEPROM but the data read (raw data from the EEPROM).
For example when the list of characters [0,1,2,3] was written, the raw data that we read back at the memory address was [0,2,0,0]

This proposal tends to implement another script at boot which may not simplify or shorten the boot time.

It could be discussed if we want this script to launch at every boot of the machine ?

Why not saving directly in the hardware.json file the data instead of storing them in the EEPROM ?

It remains to be confirmed that we can retrieve data from the focus and pump motors and if this data is sufficient to identify the pump model for example ?
