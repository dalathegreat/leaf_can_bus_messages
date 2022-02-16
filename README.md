# Nissan LEAF CAN bus messages (in .DBC format!)

This is an evolution of the spreadsheet found at https://docs.google.com/spreadsheets/d/1EHa4R85BttuY4JZ-EnssH4YZddpsDVu6rUFm0P7ouwg/edit#gid=1

Proper CAN database files are also found in this repository along with the original excel spreadsheet. The .dbc files are easier to use when working with CAN messages. Please note that there are major differences between ZE0,AZEO and ZE1 LEAFs

Please fork the repo and make changes in your repo before creating a pullrequest, or message me if you have some CAN findings to share.

## LEAF generations explained
- Gen1 2010-2012, ZE0 (24kWh, electrical handbrake, EM61 motor, 24kWh LMO "Canary" chemistry 392V max charge)
- Gen2 2013-2014, AZE0-0 (24kWh, foot operated handbrake, EM57 motor, LMO "Wolf" chemistry 396V max charge)
- Gen2.5 2013-2014, AZE0-1 (24kWh, foot operated handbrake, EM57 motor,  LMO "Lizard" chemistry 396V max charge)
- Gen3 2016-2017, AZE0-2 (30kWh, foot operated handbrake, EM57 motor, NMC 396V)
- Gen4 2018- , ZE1 (facelifted exterior, EM57 motor 110kW, 40kWh NMC 404V)
- Gen5 2019- , ZE1 e+ (facelifted exterior, EM57 motor 160kW, 62kWh NMC 404V)

## How to browse the database?

Download 'Kvaser Database Editor 3' https://www.kvaser.com/download/
With this tool you can open the .DBC files and explore the frames and the position of the data inside the frame.
![alt text](https://github.com/dalathegreat/leaf_can_bus_messages/blob/master/DatabaseEditor.PNG)

DBC files are also extremely useful for reverse engineering, if you playback a CAN log these files can be used directly for translating the data.

## Some notes about CRC/CSUM/MPRUN

Nissan places a few error checking methods into the communication. This video explains the functions of these:
[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/oENNNfy5GSM/0.jpg)](https://www.youtube.com/watch?v=oENNNfy5GSM)

## What about active CAN-polling?

Actively asking the different control units for info is another thing. The database files here won't help you (those are for passive listening), but here is a list of the different control units query and response IDs. Please note that the availability of the control modules varies depending on model year (Only ZE0 has 'Shift' module for instance)

| Control Unit  |    ID Query   |  ID Response  |
| ------------- | ------------- | ------------- |
|   Consult3+   |     0x7D2     |               |
|      VCM      |     0x797     |     0x79A     |
|      BCM      |     0x745     |     0x765     |
|      ABS      |     0x740     |     0x760     |
|   LBC(BMS)    |     0x79B     |     0x7BB     |
|  INVERTER/MC  |     0x784     |     0x78C     |
|  M&A (Meter)  |     0x743     |     0x763     |
|     HVAC      |     0x744     |     0x764     |
|     BRAKE     |     0x70E     |     0x70F     |
|      VSP      |     0x73F     |     0x761     |
|      EPS      |     0x742     |     0x762     |
|      TCU      |     0x746     |     0x783     |
|   Multi AV    |     0x747     |     0x767     |
|   IPDM E/R    |     0x74D     |     0x76D     |
|    AIRBAG     |     0x752     |     0x772     |
|    CHARGER    |     0x792     |     0x793     |
|     SHIFT     |     0x79D     |     0x7BD     |
|      AVM      |     0x7B7     |               |

List on ZE1 (2018+) CAN polling: https://drive.google.com/file/d/1jH9cgm5v23qnqVnmZN3p4TvdaokWKPjM/view

Here's an example of a request, checking which gear is selected from the VCM:

Description: Gear position (1=Park, 2=Reverse, 3=Neutral, 4=Drive, 7=Eco). The fifth reply byte value is
the gear value. In the example gear is 1.

Query: 0x797 03 22 11 56 00 00 00 00

Answer: 0x79A 04 62 11 56 **01** 00 00 00
