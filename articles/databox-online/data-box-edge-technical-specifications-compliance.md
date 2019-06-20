---
title: 'Microsoft Azure Data Box Edge: Technische Spezifikationen und Compliance | Microsoft-Dokumentation'
description: Überblick über technische Spezifikationen und Compliance für Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60755133"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Technische Spezifikationen für Azure Data Box Edge

Die Hardwarekomponenten des Microsoft Azure Data Box Edge-Geräts verfügen über die in diesem Artikel beschriebenen technischen Spezifikationen und entsprechen den aufgeführten gesetzlichen Richtlinien. In den technischen Spezifikationen werden die Netzteile, die Speicherkapazität, Gehäuse und Umgebungsstandards beschrieben. 

## <a name="power-supply-unit-specifications"></a>Spezifikationen für Netzteile

Das Data Box Edge-Gerät verfügt über zwei 100-240 Volt-Netzteile mit leistungsstarken Lüftern. Dadurch wird eine redundante Stromversorgungskonfiguration gewährleistet. Beim Ausfall eines Netzteils wird das Gerät mit dem anderen Netzteil normal weiterbetrieben, bis die fehlerhafte Einheit ausgetauscht wird. In der folgenden Tabelle sind die technischen Spezifikationen der Netzteile aufgeführt:

| Spezifikation           | 750 Watt-Netzteil                  |
|-------------------------|----------------------------|
| Maximale Ausgangsleistung    | 750 Watt                     |
| Frequency               | 50/60 Hz                   |
| Spannungsbereichsauswahl | Automatischer Spannungsbereich: 100-240 V AC |
| Hot-Plug-fähig           | Ja                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Speicherspezifikation

Die Data Box Edge-Geräte verfügen über 10 × 2,5-Zoll NVMe-SSD-Datenträger, von der jeder eine Kapazität von 1,6 TB aufweist. Von diesen SSD-Datenträgern sind zwei Betriebssystemdatenträger. Die anderen acht sind Datenträger für Daten. Die insgesamt verwendbare Kapazität für das Gerät beträgt etwa 12,5 TB. In der folgenden Tabelle sind die Details zur Speicherkapazität des Geräts aufgeführt:

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

Das Gerätepaket wiegt ca. 30 kg und sollte von zwei Personen getragen werden. Das Gewicht des Geräts hängt von der Konfiguration des Gehäuses ab.

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
|    Maximal Höhenlage, außerhalb des Betriebs    |    12\.000 Meter (39.370 Fuß)                                                                                                                                                                                         |
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

- [Bereitstellen von Azure Data Box Edge](data-box-edge-deploy-prep.md)
