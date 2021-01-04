---
title: 'Microsoft Azure Stack Edge Pro: Technische Spezifikationen und Compliance | Microsoft-Dokumentation'
description: Hier erhalten Sie Informationen zu technischen Spezifikationen und Compliance für Ihr Azure Stack Edge Pro-Gerät
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 8b36bb34f4c9081d807998cb8287797443625a6b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460290"
---
# <a name="azure-stack-edge-pro-technical-specifications"></a>Technische Spezifikationen für Azure Stack Edge Pro

Die Hardwarekomponenten Ihres Microsoft Azure Stack Edge Pro-Geräts entsprechen den in diesem Artikel beschriebenen technischen Spezifikationen sowie den aufgeführten gesetzlichen Richtlinien. In den technischen Spezifikationen werden die Netzteile, die Speicherkapazität, Gehäuse und Umgebungsstandards beschrieben.

## <a name="compute-memory-specifications"></a>Spezifikationen zu Compute- und Arbeitsspeicherressourcen

Das Azure Stack Edge Pro-Gerät verfügt über die folgenden Spezifikationen für Compute- und Arbeitsspeicherressourcen:

| Spezifikation           | Wert                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10-Kern-CPU Intel Xeon Silver 4114 2,2 G                    |
| Arbeitsspeicher              | 128 GB RAM (8 x 16 GB RDIMM)                 |

## <a name="fpga-specifications"></a>FPGA-Spezifikationen

Auf jedem Azure Stack Edge Pro-Gerät, das Machine Learning (ML)-Szenarien unterstützt, befindet sich ein FPGA (Field Programmable Gate Array).

| Spezifikation           | Wert                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Verfügbare Deep Neural Network (DNN)-Modelle sind identisch mit den Modellen, [die von Cloud-FPGA-Instanzen unterstützt werden](../machine-learning/how-to-deploy-fpga-web-service.md#fpga-support-in-azure).|

## <a name="power-supply-unit-specifications"></a>Spezifikationen für Netzteile

Das Azure Stack Edge Pro-Gerät verfügt über zwei Netzteile mit 100-240 V und leistungsstarken Lüftern. Dadurch wird eine redundante Stromversorgungskonfiguration gewährleistet. Beim Ausfall eines Netzteils wird das Gerät mit dem anderen Netzteil normal weiterbetrieben, bis die fehlerhafte Einheit ausgetauscht wird. In der folgenden Tabelle sind die technischen Spezifikationen der Netzteile aufgeführt:

| Spezifikation           | 750 Watt-Netzteil                  |
|-------------------------|----------------------------|
| Maximale Ausgangsleistung    | 750 Watt                     |
| Häufigkeit               | 50/60 Hz                   |
| Spannungsbereichsauswahl | Automatischer Spannungsbereich: 100-240 V AC |
| Hot-Plug-fähig           | Ja                        |

### <a name="azure-stack-edge-pro-power-cord-specifications-by-region"></a>Spezifikationen für Azure Stack Edge Pro-Netzkabel nach Region

Ihr Azure Stack Edge Pro-Gerät benötigt ein Netzkabel, das je nach Azure-Region variiert.
Technische Spezifikationen für alle unterstützten Netzkabel finden Sie unter [Spezifikationen für Azure Stack Edge Pro-Netzkabel nach Region](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge Pro device.-->

## <a name="network-interface-specifications"></a>Netzwerkschnittstellenspezifikationen

Ihr Azure Stack Edge Pro-Gerät hat 6 Netzwerkschnittstellen, PORT1 – PORT6.

| Spezifikation           | BESCHREIBUNG                 |
|-------------------------|----------------------------|
|  Netzwerkschnittstellen    | 2 Netzwerkschnittstellen (1 GbE), 1 davon zur Verwaltung, nicht vom Benutzer konfigurierbar, dient zur erstmaligen Einrichtung Die andere Schnittstelle ist vom Benutzer konfigurierbar, kann für die Datenübertragung verwendet werden und ist standardmäßig als DHCP-Schnittstelle konfiguriert. <br>2 Netzwerkschnittstellen (25 GbE), die auch als 10-GbE-Schnittstellen betrieben werden können. Diese Datenschnittstellen können vom Benutzer als DHCP (Standard) oder statisch konfiguriert werden. <br> 2 Netzwerkschnittstellen (25 GbE): Diese Datenschnittstellen können vom Benutzer als DHCP (Standard) oder statisch konfiguriert werden.                  |

Folgende Netzwerkadapter werden verwendet: 

| Spezifikation           | BESCHREIBUNG                 |
|-------------------------|----------------------------|
|Netzwerktochterkarte (rNDC) |QLogic FastLinQ 41264 Dual Port 25 GbE SFP+, Dual Port 1 GbE, rNDC|
|PCI-Netzwerkadapter |QLogic FastLinQ 41262 zwei Ports 25 Gbit/s SFP28 Adapter|

Weitere Informationen finden Sie in der Hardwarekompatibilitätsliste von Intel QLogic für kompatiblen Gigabit Interface Converter (GBIC). Gigabit Interface Converter (GBIC) sind in der Lieferung von Azure Stack Edge nicht enthalten. 

## <a name="storage-specifications"></a>Speicherspezifikation

Die Azure Stack Edge Pro-Geräte enthalten neun NVMe-SSD-Datenträger mit 2,5 Zoll und einer Kapazität von jeweils 1,6 TB. Von diesen SSD-Datenträgern ist einer ein Betriebssystemdatenträger, und die anderen acht sind Datenträger für Daten. Die insgesamt verwendbare Kapazität für das Gerät beträgt etwa 12,5 TB. In der folgenden Tabelle sind die Details zur Speicherkapazität des Geräts aufgeführt:

|     Spezifikation                          |     Wert             |
|--------------------------------------------|-----------------------|
|    Anzahl von Solid-State-Laufwerken (SSDs)     |    8                  |
|    Kapazität eines SSD-Laufwerks                     |    1,6 TB             |
|    Gesamtkapazität                          |    12,8 TB            |
|    Insgesamt nutzbare Kapazität*                  |    ~ 12,5 TB            |

**Etwas Speicherplatz ist für die interne Verwendung reserviert.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Gehäuseabmessungen und Gewicht

In den folgenden Tabellen sind die verschiedenen Gehäusespezifikationen (Abmessungen und Gewicht) aufgeführt.

### <a name="enclosure-dimensions"></a>Gehäuseabmessungen

Die folgenden Tabellen enthalten die Abmessungen des Gehäuses in Millimetern und Zoll.

|     Gehäuse     |     Millimeter     |     Zoll     |
|-------------------|---------------------|----------------|
|    Höhe         |    44,45            |    1,75 Zoll          |
|    Breite          |    434,1           |    17,09 Zoll          |
|    Länge          |    740,4           |    29,15 Zoll          |

Die folgenden Tabellen enthalten die Abmessungen der Versandverpackung in Millimetern und Zoll.

|     Paket     |     Millimeter     |     Zoll     |
|-------------------|---------------------|----------------|
|    Höhe         |    311,2            |    12,25 Zoll          |
|    Breite          |    642,8          |    25,31 Zoll          |
|    Länge          |   1\.051,1          |    41,38 Zoll          |

### <a name="enclosure-weight"></a>Gehäusegewicht

Das Gerätepaket wiegt 27,7 kg und sollte von zwei Personen getragen werden. Das Gewicht des Geräts hängt von der Konfiguration des Gehäuses ab.

|     Gehäuse                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    Gesamtgewicht einschließlich Verpackung:       |    27,7 kg.          |
|    Gerätegewicht:                       |    16 kg.          |

## <a name="enclosure-environment-specifications"></a>Spezifikationen der Gehäuseumgebung

Dieser Abschnitt enthält die Spezifikationen im Zusammenhang mit der Gehäuseumgebung, z. B. Temperatur, Luftfeuchtigkeit und Höhenlage.

### <a name="temperature-and-humidity"></a>Temperatur und Feuchtigkeit

|     Gehäuse         |     Umgebungstemperaturspanne     |     Relative Luftfeuchtigkeit der Umgebung     |     Maximaler Kondensationspunkt     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Bei Betrieb        |    10°C bis 35°C (50° F bis 86 F)         |    10–80 % nicht kondensierend         |    29 °C            |
|    Außerhalb des Betriebs    |    -40°C bis 65°C (-40°F bis 149°F)     |    5–95 % nicht kondensierend          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luftströmung, Höhe, Stöße, Schwingungen, Ausrichtung, Sicherheit und EMC

|     Gehäuse                           |     Betriebsspezifikationen                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Luftströmung                              |    Die Luftströmung im System verläuft von vorne nach hinten. Das System muss mit einem hinteren Luftstromauslass mit niedrigem Druck betrieben werden. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximale Betriebshöhe        |    3048 Meter (10.000 Fuß) mit einer maximalen Betriebstemperatur bei heruntergesetzter Leistung, die Sie der Tabelle unter [Spezifikationen für Betriebstemperatur bei heruntergesetzter Leistung](#operating-temperature-de-rating-specifications) entnehmen.                                                                                |
|    Maximal Höhenlage, außerhalb des Betriebs    |    12.000 Meter (39.370 Fuß)                                                                                                                                                                                         |
|    Stoßfestigkeit, bei Betrieb                   |    6 G für eine Dauer von 11 Millisekunden in sechs verschiedenen Ausrichtungen                                                                                                                                                                         |
|    Stoßfestigkeit, außerhalb des Betriebs               |    71 G für eine Dauer von zwei Millisekunden in sechs verschiedenen Ausrichtungen                                                                                                                                                                           |
|    Schwingungen, bei Betrieb               |    0,26 G<sub>QMW</sub> bei 5 Hertz bis 350 Hertz zufälliger Vibration                                                                                                                                                                                     |
|    Schwingungen, außerhalb des Betriebs           |    1,88 G<sub>QMW</sub> bei 10 Hertz bis 500 Hertz für die Dauer von 15 Minuten (von allen sechs Seiten getestet)                                                                                                                                                  |
|    Ausrichtung und Montage             |    19 Zoll-Rackmontage                                                                                                                                                                                        |
|    Sicherheit und Genehmigungen                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Klasse D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energieversorgung             |    Kommissionsregelung (EU) Nr. 617/2013.                                                                                                                                                                                        |
|    RoHS-Richtlinien           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Spezifikationen für Betriebstemperatur bei heruntergesetzter Leistung

|     Betriebstemperatur bei heruntergesetzter Leistung     |     Umgebungstemperaturspanne                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Bis zu 35°C (95°F)                       |    Die maximale Temperatur reduziert sich auf über 950 Metern (3.117 Fuß) alle 300 Meter um 1°C (1°F/547 Fuß).    |
|    35°C bis 40°C (95°F bis 104°F)            |    Die maximale Temperatur reduziert sich auf über 950 Metern (3.117 Fuß) alle 175 Meter um 1°C (1°F/319 Fuß).    |
|    40°C bis 45°C (104°F bis 113°F)           |    Die maximale Temperatur reduziert sich auf über 950 Metern (3.117 Fuß) alle 125 Meter um 1°C (1°F/228 Fuß).    |

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen Ihres Azure Stack Edge Pro-Geräts](azure-stack-edge-deploy-prep.md)