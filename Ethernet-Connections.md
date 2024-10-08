# Ethernet Connections

| Requested by: | **GHESA**  |
| ------------- | ---------- |
| Code          | Doc. Code  |
| Editor:       | A. Izpizua |
| Approved by:  | J. Garcia  |

## Introduction

This document describes the network architecture designed for the MCS developed by Tekniker. This document does not cover
any other networks bellow this architecture, as these are not developed by Tekniker.

## Reference document list

| **No.** | **DOCUMENT**                     | **CODE**      | **VERSION** |
| ------- | -------------------------------- | ------------- | ----------- |
| **1**   | MCS Design Electrical Schematics | 3151_MCS_0022 | 9.0         |

## Network topology

The network topology is shown in the next diagram. The MCC computer will be access point to the MCS network. To implement
the networks the IE3200 and IE3000 CISCO switched are used.

```{uml}
@startuml Network Architecture

nwdiag {
    network rubin {
        address = "139.229.178.x/24"
        MCC_EUI [address = "139.229.178.30"]
    }
    network TMA{
        address = "139.229.171.x/24";
        MCC_EUI [address = "139.229.171.6"]
        TMAPXI [address = "139.229.171.3"]
        AUXPXI [address = "139.229.171.4"]
        SupportPC [address = "139.229.171.5"]
        MCC [address = "139.229.171.6"]
        AZ0001TempController [address = "139.229.171.8"]
        TempControllers [address = "139.229.171.9, 139.229.171.16-19"]
        TopEndChiller [address = "139.229.171.10-15"]
        BoschController [address = "139.229.171.23"]
        AXESPXI [address = "139.229.171.26"]
        HHD_1_Rubin_AURA [address = "139.229.171.28"]
        HHD_2_Tekniker_UTE [address = "139.229.171.29"]
    }
    network EIB{
        address ="139.229.161.x/28"
        TMAPXI [address = "139.229.161.49"]
        AXESPXI [address = "139.229.161.51"]
        EIB [address = "139.229.161.50"]
    }
    network Safety{
        address ="192.168.180.x/24"
        TMAPXI [address = "192.168.180.100"]
        TMAIS_CPU [address = "192.168.180.10"]
        TMAIS_Pier [address = "192.168.180.11"]
        TMAIS_AzAux [address = "192.168.180.12"]
        TMAIS_EL1 [address = "192.168.180.13"] 
        TMAIS_EL2 [address = "192.168.180.14"]
    }
}
@enduml
```

## Components and descriptions

### MCC

This is the computer located in the server room and hosts the EUI. It has two nic, one is dedicated to the local network
and the other to connect to the telescope network.

### Cisco IE3000 and IE3200

These are two switches located inside the TMA-AZ-CS-CBT-0001 cabinet. The IE3000 is connected to the MCC using a fiber
optics line. IE3000 has a trunk port connected to IE3200.

### TMA PXI, AuxPXI and AxesPXI

They are the different controllers used in the MCS. They have several nic to connect to different VLANs.

### EIB

Encoder controller.

### HHD

It is a Hand Held Device, portable user interface, that can be connected only in some points in the telescope. It has 4 connection points
available in the switch but it uses only one each time.

### TMA IS

It is the PILZ controller for the TMA interlock and safety functions. It is connected to the safety network.

### Bosch Rexroth drive controller

This controller manages all the auxiliary drives in the TMA.

### Temperature controllers

There are several temperature controllers for cabinets. Those are managed by the MCS via modbus protocol.

### Top end chiller

PLC located in the telescope top end. It manages the cooling system of the top end.

### OSS

Oil supply system controller located in level 1.

### Phase support PC

It is a PC located in TMA-AZ-DR-CBT-0001 cabinet. The connection to the IE3200 was made by Rubin IT.

### Support PC

This is a support PC provided by Tekniker. It is used for engineering purposes. This PC is linked to the VLAN209 and to the
wireless Rubin network. Originally this PC was installed by Tekniker in level 6, but ir was relocated by Rubin staff
after Tekniker's departure

In level 6 there is a switch that allows the connection of other elements for diagnosis. It was the point where Alberto
and Julen connected their laptops when they were in the summit.

## VLANs (deprecated, now managed by Rubin IT)

The VLANs listed here are from the original design when the switches were managed by Tekniker, the current approach is different and managed by Rubin IT.

### 192.168.209.X

Main MCS network.

### 192.168.210.X

Network for modbus slaves.

### 192.168.211.X

Network for encoder system.

### 192.168.212.X

Network for auxiliary axes communications.

### 192.168.213.X

Network for AXES PXI communications.

### 192.168.180.X

Safety network.

## Protocols in the network

TCP, UPD and SSH.

## Devices with remote access and what protocol being used to access

There are 2 elements with remote access at this moment:

- MCC. VNC connection over ssh over VPN.
- Support PC. Teamviewer.

## List of users with remote access enabled

- Julen Garcia
- Alberto Izpizua

## List of macs and IPs

### MCC interfaces

This computer has 2 NICs one connected to Rubin network and the other to TMA private network.

| Network | MAC               | IP             | FQDN                         |
| ------- | ----------------- | -------------- | ---------------------------- |
| Rubin   | 00:1b:1b:c3:9b:33 | 139.229.178.30 | tma-controller01.cp.lsst.org |
| TMA     | 00:1b:1b:f4:58:76 | 139.229.171.6  | TODO:                        |

### IE3200 switch

| Switch port | Element             | MAC               | IP             | FQDN                      |
| ----------- | ------------------- | ----------------- | -------------- | ------------------------- |
| 1           | Rubin network fiber |                   |                |                           |
| 2           | Rubin network fiber |                   |                |                           |
| 3 [^1]      | Support PC          |                   |                |                           |
| 4           | TMA PXI             | 00:80:2f:23:aa:3d | 139.229.171.3  | tma-pxi02.cp.lsst.org     |
| 5           | IE3000              |                   |                |                           |
| 6           | TMA PXI (VLAN 213)  | 00:02:25:03:38:2b | down           |                           |
| 7           | AXES PXI (VLAN 213) | 00:80:2f:23:eb:99 | 139.229.171.26 | tma-axespxi02.cp.lsst.org |
| 8           | TMA PXI (VLAN 1610) | 00:02:25:03:38:3A | 139.229.161.49 | tma-pxi03.cp.lsst.org     |
| 9           | AXES PXI (VLAN 211) | 00:02:25:03:77:2A | 139.229.161.51 | tma-axespxi01.cp.lsst.org |
| 10          | EIB                 | 00:A0:CD:10:0E:64 | 139.229.161.50 | tma-eib.cp.lsst.org       |

### IE3000 switch

| Switch port | Element                                                | MAC               | IP              | FQDN                     |
| ----------- | ------------------------------------------------------ | ----------------- | --------------- | ------------------------ |
| Gi1/1       | IE3200                                                 |                   |                 |                          |
| Gi1/2       | AuxPXI (VLAN 209)                                      | 00:01:05:6d:22:b6 | 139.229.171.4   | tma-auxpxi01.cp.lsst.org |
| Fa1/1       | HHD (HHD_2 Tekniker-UTE) [^5]                          | 00:01:29:60:56:74 | 139.229.171.29  | TBD                      |
| Fa1/2       | HHD (HHD_2 Tekniker-UTE) [^5]                          | Same as Fa1/1     | Same as Fa1/1   | TBD                      |
| Fa1/3       | HHD (HHD_2 Tekniker-UTE) [^5]                          | Same as Fa1/1     | Same as Fa1/1   | TBD                      |
| Fa1/4       | HHD (HHD_2 Tekniker-UTE) [^5]                          | Same as Fa1/1     | Same as Fa1/1   | TBD                      |
| Fa1/5       | TMA PXI Chassis                                        | 00:80:2F:23:06:75 | 139.229.171.1   | tma-pxi01.cp.lsst.org    |
| Fa1/6       | Free                                                   |                   |                 |                          |
| Fa1/7       | TMA PXI (VLAN 180)                                     | 00:02:25:03:38:2a | 192.168.180.100 | TBD                      |
| Fa1/8       | TMA IS                                                 | 00:02:48:44:5b:d4 | 192.168.180.10  | TBD                      |
| Fa2/1       | TMA PXI (VLAN 212)                                     | 00:02:25:03:38:0a | down            |                          |
| Fa2/2       | Bosch-Rexroth drives Controller                        | 00:30:d6:2d:8e:24 | 139.229.171.23  | TBD                      |
| Fa2/3       | AuxPXI (VLAN 210)                                      | 00:01:05:6d:22:b7 | 192.168.210.10  | tma-auxpxi02.cp.lsst.org |
| Fa2/4       | CS-CBT-0001 temperature controller                     | 00:03:aa:00:97:d6 | 139.229.171.8   | TBD                      |
| Fa2/5       | Phase Main Cabinet temperature controller              | 00:90:E8:68:57:79 | 139.229.171.9   | TBD                      |
| Fa2/6       | AZ-PD-CBT-0001 temperature controller                  | 00:90:E8:68:57:76 | 139.229.171.16  | tma-tempaz01.cp.lsst.org |
| Fa2/7       | EL-PD-CBT-0001 temperature controller                  | 00:90:E8:68:57:92 | 139.229.171.17  | tma-tempel01.cp.lsst.org |
| Fa2/8       | EL-PD-CBT-0002 temperature controller                  | 00:90:E8:68:57:35 | 139.229.171.18  | tma-tempel02.cp.lsst.org |
| Fa3/1       | AZ-PD-TRM-0001 temperature controller                  | 00:90:E8:68:57:8C | 139.229.171.19  | tma-tempaz02.cp.lsst.org |
| Fa3/2 [^2]  | Top End Chiller                                        |                   | 139.229.171.10  | tma-tec01.cp.lsst.org    |
| Fa3/2 [^2]  | Top End Chiller                                        |                   | 139.229.171.11  | tma-tec02.cp.lsst.org    |
| Fa3/2 [^2]  | Top End Chiller                                        |                   | 139.229.171.12  | tma-tec03.cp.lsst.org    |
| Fa3/2 [^2]  | Top End Chiller                                        |                   | 139.229.171.13  | tma-tec04.cp.lsst.org    |
| Fa3/2 [^2]  | Top End Chiller                                        |                   | 139.229.171.14  | tma-tec05.cp.lsst.org    |
| Fa3/2 [^2]  | Top End Chiller                                        |                   | 139.229.171.15  | tma-tec06.cp.lsst.org    |
| Fa3/3 [^3]  | OSS                                                    |                   | 192.168.210.50  | tma-oss01.cp.lsst.org    |
| Fa3/4       | Free                                                   |                   |                 |                          |
| Fa3/5       | Phase support PC                                       | 00-18-7D-9E-93-1E | 139.229.171.2   | tma-pmc01.cp.lsst.org    |
| Fa3/6       | TODO: PLC1 - David Jimenez                             | TODO:             | TODO:           | TODO:                    |
| Fa3/7       | TODO: tekniker-pc-gis                                  | TODO:             | TODO:           | TODO:                    |
| Fa3/8 [^4]  | Possible connection for the Support PC when is level 8 | 00:13:3B:5B:23:E4 | 139.229.171.5   | tma-pc01.cp.lsst.org     |

[^1]: In this port also Julen's and Alberto`s laptops are connected when they are in level 6. Julen's mac: 34-48-ED-15-CC-F3. Alberto's mac: 34:48:ed:4a:68:7c

[^2]: There is an additional switch not managed by Tekniker. The IPs are the listed ones

[^3]: There is an additional switch not managed by Tekniker. Tekniker does not know about the elements in the switch or the switch configuration.

[^4]: This is the same PC as the one connected in the IE3200 port 3. It is used when for any reason the PC is in level 8. This port could also be used.

[^5]: This could also be the Rubin HHD: 139.229.171.28 MAC: 00:01:29:96:0a:a4
