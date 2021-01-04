---
title: 'Microsoft Azure Stack Edge Mini R: Technische Spezifikationen und Compliance | Microsoft-Dokumentation'
description: Hier erhalten Sie Informationen zu technischen Spezifikationen und Compliance für Ihr Azure Stack Edge Mini R-Gerät.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: e6dff06e92126e2fc4538273e229dcb0904e3101
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465476"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini R: Technische Spezifikationen

Die Hardwarekomponenten Ihres Microsoft Azure Stack Edge Mini R-Geräts entsprechen den in diesem Artikel beschriebenen technischen Spezifikationen sowie den aufgeführten gesetzlichen Richtlinien. In den technischen Spezifikationen werden CPU, Arbeitsspeicher, Netzteile, Speicherkapazität, Gehäuseabmessungen und Gewicht beschrieben.


## <a name="compute-memory-specifications"></a>Spezifikationen zu Compute- und Arbeitsspeicherressourcen

Das Azure Stack Edge Mini R-Gerät verfügt über die folgenden Spezifikationen für Compute- und Arbeitsspeicherressourcen:

| Spezifikation           | Wert                  |
|-------------------------|------------------------|
| CPU    | CPU mit 16 Kernen, Intel Xeon-D 1577 |
| Arbeitsspeicher              | 48 GB RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>Spezifikationen zur Computebeschleunigung

Eine integrierte Bildverarbeitungseinheit (Vision Processing Unit, VPU) ist in jedem Azure Stack Edge Mini R-Gerät enthalten, das auf Kubernetes, Deep Neural Network und maschinellem Sehen basierende Anwendungen ermöglicht.

| Spezifikation           | Wert                  |
|-------------------------|------------------------|
| Karte für die Computebeschleunigung         | Intel Movidius Myriad X VPU <br> Weitere Informationen finden Sie unter [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX). |


## <a name="storage-specifications"></a>Speicherspezifikation

Das Azure Stack Edge Mini R-Gerät verfügt über einen Datenträger für Daten und einen Startdatenträger (der als Speicher für das Betriebssystem dient). In der folgenden Tabelle sind die Details zur Speicherkapazität des Geräts aufgeführt.

|     Spezifikation                          |     Wert             |
|--------------------------------------------|-----------------------|
|    Anzahl von Solid-State-Laufwerken (SSDs)     |    2 x 1 TB Datenträger <br> Ein Datenträger für Daten und ein Startdatenträger                  |
|    Kapazität eines SSD-Laufwerks                     |    1 TB               |
|    Gesamtkapazität (nur Daten)              |    1 TB              |
|    Insgesamt nutzbare Kapazität*                  |    ~ 750 GB        |

**Etwas Speicherplatz ist für die interne Verwendung reserviert.*

## <a name="network-specifications"></a>Netzwerkspezifikationen

Das Azure Stack Edge Mini R-Gerät verfügt über die folgenden Spezifikationen für das Netzwerk:


|Spezifikation  |Wert  |
|---------|---------|
|Netzwerkschnittstellen    |2 x 10 GbE SFP+ <br> Wird als ANSCHLUSS 3 und ANSCHLUSS 4 auf der lokalen Benutzeroberfläche angezeigt.           |
|Netzwerkschnittstellen    |2 x 1 GbE RJ45 <br> Wird als ANSCHLUSS 1 und ANSCHLUSS 2 auf der lokalen Benutzeroberfläche angezeigt.          |
|WLAN   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Spezifikationen für Netzteile

Das Azure Stack Edge Mini R-Gerät enthält einen externen 85 W Wechselstromadapter für die Stromversorgung und zum Laden des eingebauten Akkus.

Die folgende Tabelle zeigt die Netzteilspezifikationen:

| Spezifikation           | Wert                      |
|-------------------------|----------------------------|
| Maximale Ausgangsleistung    | 85 W                       |
| Häufigkeit               | 50/60 Hz                   |
| Spannungsbereichsauswahl | Automatischer Spannungsbereich: 100-240 V AC |



## <a name="included-battery"></a>Eingebauter Akku

Das Azure Stack Edge Mini R-Gerät enthält auch einen eingebauten Akku, der durch das Netzteil aufgeladen wird. 

Ein zusätzlicher Akku des Typs 2590 kann in Verbindung mit dem eingebauten Akku verwendet werden, um den Einsatz des Geräts zwischen den Ladevorgängen zu verlängern. Dieser Akku sollte allen Sicherheits-, Transport- und Umweltvorschriften des Landes entsprechen, in dem er verwendet wird.


| Spezifikation           | Wert                      |
|-------------------------|----------------------------|
| Kapazität des eingebauten Akkus | 73 WH                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Gehäuseabmessungen und Gewicht

In den folgenden Tabellen sind die verschiedenen Gehäusespezifikationen (Abmessungen und Gewicht) aufgeführt.

### <a name="enclosure-dimensions"></a>Gehäuseabmessungen

In der folgenden Tabelle sind die Abmessungen des Geräts und der USV im robusten Gehäuse in Millimetern und Zoll aufgeführt.

|     Gehäuse     |     Millimeter     |     Zoll     |
|-------------------|---------------------|----------------|
|    Höhe         |    68            |    2.68          |
|    Breite          |    208          |      8,19          |
|    Länge          |   259           |    10,20          |


### <a name="enclosure-weight"></a>Gehäusegewicht

In der folgenden Tabelle ist das Gewicht des Geräts einschließlich Akku aufgeführt.

|     Gehäuse                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    Gesamtgewicht des Geräts     |    ~ 3,2 kg (7 lbs)          |

## <a name="enclosure-environment-specifications"></a>Spezifikationen der Gehäuseumgebung


Dieser Abschnitt enthält die Spezifikationen im Zusammenhang mit der Gehäuseumgebung, z. B. Temperatur, Luftfeuchtigkeit und Höhenlage.


|     Spezifikationen             |     BESCHREIBUNG                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Temperaturbereich          |     0–43° C (Betrieb)                                              |
|     Vibration                  |     MIL-STD-810-Methode 514.7*<br> Prozedur I CAT 4, 20                  |
|     Erschütterung                      |     MIL-STD-810-Methode 516.7*<br> Prozedur IV, Logistic                 |
|     Höhe                   |     Betrieb:   3048 m (10.000 Fuß)<br> Außerhalb des Betriebs: 12.192 m (40.000 Fuß)          |

**Alle Verweise beziehen sich auf MIL-STD-810G-Änderung 1 (2014)*


## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen Ihrer Azure Stack Edge Mini R-Instanz](azure-stack-edge-placeholder.md)
