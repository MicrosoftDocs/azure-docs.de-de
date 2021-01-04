---
title: 'Microsoft Azure Stack Edge Pro R: Technische Spezifikationen und Compliance | Microsoft-Dokumentation'
description: Hier erhalten Sie Informationen zu technischen Spezifikationen und zur Compliance für Ihr Azure Stack Edge Pro R-Gerät.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 56d301762fde41f727b0b425d6c41a423f08103c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465450"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge Pro R: Technische Spezifikationen

Die Hardwarekomponenten Ihres Azure Stack Edge Pro R-Geräts entsprechen den in diesem Artikel beschriebenen technischen Spezifikationen. In den technischen Spezifikationen werden die Netzteile, die Speicherkapazität, Gehäuse und Umgebungsstandards beschrieben.


## <a name="compute-memory-specifications"></a>Spezifikationen zu Compute- und Arbeitsspeicherressourcen

Das Azure Stack Edge Pro R-Gerät verfügt über die folgenden Spezifikationen für Compute- und Arbeitsspeicherressourcen:

| Spezifikation       | Wert                  |
|---------------------|------------------------|
| CPU    | 2 X 10-Kern-CPU Intel Xeon Silver 4114 |
| Arbeitsspeicher              | 256 GB RAM (2666 MT/s)     |


## <a name="compute-acceleration-specifications"></a>Spezifikationen zur Computebeschleunigung

Jedes Gerät verfügt über einen Grafikprozessor (GPU), der die Implementierung von Kubernetes-, Deep Learning- und Machine Learning-Szenarien ermöglicht.

| Spezifikation           | Wert                  |
|-------------------------|----------------------------|
| GPU   | 1 nVidia T4-GPU <br> Weitere Informationen finden Sie unter [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 

## <a name="power-supply-unit-specifications"></a>Spezifikationen für Netzteile

Das Azure Stack Edge Pro R-Gerät verfügt über zwei Netzteile mit 100–240 V und leistungsstarken Lüftern. Dadurch wird eine redundante Stromversorgungskonfiguration gewährleistet. Beim Ausfall eines Netzteils wird das Gerät mit dem anderen Netzteil normal weiterbetrieben, bis die fehlerhafte Einheit ausgetauscht wird. In der folgenden Tabelle sind die technischen Spezifikationen der Netzteile aufgeführt:

| Spezifikation           | 550 Watt-Netzteil                  |
|-------------------------|----------------------------|
| Maximale Ausgangsleistung    | 550 W                      |
| Wärmeabgabe (maximal)                   | 2891 BTU/h                |
| Häufigkeit               | 50/60 Hz                   |
| Spannungsbereichsauswahl | Automatischer Spannungsbereich: 115–230 V AC |
| Hot-Plug-fähig           | Ja                        |

## <a name="network-specifications"></a>Netzwerkspezifikationen

Das Azure Stack Edge Pro R-Gerät verfügt über vier Netzwerkschnittstellen: PORT1–PORT4. 


|Spezifikation  |BESCHREIBUNG                              |
|----------------------|----------------------------------|
|Netzwerkschnittstellen    |**2 x 1 Gbe RJ45** <br> PORT 1 wird als Verwaltungsschnittstelle für die anfängliche Einrichtung verwendet. Dies ist standardmäßig ein statischer Port. Nach Abschluss der anfänglichen Einrichtung kann die Schnittstelle für Daten mit einer beliebigen IP-Adresse verwendet werden. Beim Zurücksetzen wird jedoch erneut eine statische IP-Adresse verwendet. <br>Die zweite Schnittstelle (PORT 2) kann vom Benutzer konfiguriert und für die Datenübertragung verwendet werden. Dieser Port ist standardmäßig als DHCP-Schnittstelle konfiguriert.     |
|Netzwerkschnittstellen    |**2 x 25 Gbe SFP28** <br> Diese Datenschnittstellen (PORT 3 und PORT 4) können als DHCP (Standard) oder statisch konfiguriert werden.            |

Ihr Azure Stack Edge Pro R-Gerät enthält folgende Netzwerkhardware:

* **Mellanox Dual Port 25G ConnectX-4-Kanal-Netzwerkadapter**: PORT 3 und PORT 4. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

Eine vollständige Liste der unterstützten Kabel, Switches und Transceiver für diese Netzwerkkarten finden Sie unter: [Mit Mellanox Dual Port 25G ConnectX-4-Kanal-Netzwerkadapter kompatible Produkte](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Speicherspezifikation

Die Azure Stack Edge Pro R-Geräte verfügen über 8 Datenträger und 2 M.2-SATA-Datenträger, die als Betriebssystem-Datenträger dienen. Weitere Informationen finden Sie in den Erläuterungen zu [M.2-SATA-Datenträgern](https://en.wikipedia.org/wiki/M.2).

#### <a name="storage-for-1-node-device"></a>Speicher für ein Gerät mit einem Knoten

In der folgenden Tabelle sind die Details zur Speicherkapazität des Geräts mit einem Knoten aufgeführt.

|     Spezifikation                          |     Wert             |
|--------------------------------------------|-----------------------|
|    Anzahl von Solid-State-Laufwerken (SSDs)     |    8                  |
|    Kapazität eines SSD-Laufwerks                     |    8 TB               |
|    Gesamtkapazität                          |    64 TB              |
|    Insgesamt nutzbare Kapazität*                  |    ~ 42 TB          |

**Etwas Speicherplatz ist für die interne Verwendung reserviert.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Gehäuseabmessungen und Gewicht

In den folgenden Tabellen sind die verschiedenen Gehäusespezifikationen (Abmessungen und Gewicht) aufgeführt.

### <a name="enclosure-dimensions"></a>Gehäuseabmessungen 

In der folgenden Tabelle sind die Abmessungen des Geräts und der USV im robusten Gehäuse in Millimetern und Zoll aufgeführt.

|     Gehäuse     |     Millimeter     |     Zoll     |
|-------------------|---------------------|----------------|
|    Höhe         |    301,2            |    11,86       |
|    Breite          |    604,5            |    23,80       |
|    Länge         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Gehäusegewicht 

Das Gewicht des Geräts hängt von der Konfiguration des Gehäuses ab.

|     Gehäuse                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    Gesamtgewicht des Geräts mit einem Knoten inklusive robustem Gehäuse mit Endkappen     |    ~ 52 kg          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Spezifikationen der Gehäuseumgebung

Dieser Abschnitt enthält die Spezifikationen zur Gehäuseumgebung, z. B. Temperatur, Vibration, Erschütterung und Einsatzhöhe.


|     Spezifikation              |     Wert    |
|--------------------------------|-------------------------------------------------------------------|
|     Temperaturbereich          |     0–43° C (Betrieb)    |
|     Vibration                  |     MIL-STD-810-Methode 514.7*<br>Prozedur I CAT 4, 20                  |
|     Erschütterung                      |     MIL-STD-810-Methode 516.7*<br>Prozedur IV, Logistic                 |
|     Höhe                   |     Betrieb:   3048 m (10.000 Fuß)<br>Außerhalb des Betriebs: 12.192 m (40.000 Fuß)          |

**Alle Verweise beziehen sich auf MIL-STD-810G-Änderung 1 (2014)*

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen Ihres Azure Stack Edge-Geräts](azure-stack-edge-placeholder.md)
