---
title: Technische Spezifikationen von StorSimple | Microsoft Docs
description: Beschreibt die technischen Spezifikationen und die einzuhaltenden gesetzlichen Richtlinien für die StorSimple-Hardwarekomponenten.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 33becd46747f957cce67fb7b2ace170a9aa402ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606923"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Technische Spezifikationen und Kompatibilität des StorSimple-Geräts

## <a name="overview"></a>Übersicht

Die Hardwarekomponenten des Microsoft Azure StorSimple-Geräts verfügen über die in diesem Artikel beschriebenen technischen Spezifikationen und entsprechen den aufgeführten gesetzlichen Richtlinien. In den technischen Spezifikationen werden die Stromversorgungs- und Kühleinheiten (PCMs), Laufwerke, Speicherkapazität und Gehäuse beschrieben. Die Richtlinieninformationen umfassen z. B. internationale Standards sowie Sicherheits-, Emissions- sowie Verkabelungsbestimmungen.

## <a name="power-and-cooling-module-specifications"></a>Spezifikationen der Stromversorgungs- und Kühleinheit

Das StorSimple-Gerät verfügt über zwei SBB-kompatible Stromversorgungs- und Kühleinheiten (PCMs) mit Dual-Lüftern mit 100–240 V. Dadurch wird eine redundante Stromversorgungskonfiguration gewährleistet. Beim Ausfall eines PCMs wird das Gerät mit der anderen PCM normal weiterbetrieben, bis die fehlerhafte Einheit ausgetauscht wird.

Das EBOD-Gehäuse enthält ein PCM mit 580 W, und das primäre Gehäuse wird mit einer PCM-Stromversorgung mit 764 W betrieben. In den folgenden Tabellen sind die technischen Spezifikationen der PCMs aufgeführt.

| Spezifikation | 580 W PCM (EBOD) | 764 W PCM (primär) |
| --- | --- | --- |
| Maximale Ausgangsleistung |580 W |764 |
| Frequency |50/60 Hz |50/60 Hz |
| Spannungsbereichsauswahl |Automatischer Spannungsbereich: 90–264 V Wechselstrom, 47/63 Hz |Automatischer Spannungsbereich: 90–264 V Wechselstrom, 47/63 Hz |
| Maximaler Eingangsstrom |20 A |20 A |
| Leistungsfaktorkorrektur |> 95 % Nenneingangsspannung |> 95 % Nenneingangsspannung |
| Oberschwingungen |Erfüllt EN61000-3-2 |Erfüllt EN61000-3-2 |
| Output |5 V Standbyspannung \@ 2,0 A |5 V Standbyspannung \@ 2,7 A |
| +5 V \@ 42 A |+5 V \@ 40 A | |
| +12 V \@ 38 A |+12 V \@ 38 A | |
| Hot-Plug-fähig |Ja |Ja |
| Switches und LEDs |Netzteil-Ein-/Aus-Schalter und vier Status-LEDs |Netzteil-Ein-/Aus-Schalter und sechs Status-LEDs |
| Gehäusekühlung |Axialer Lüfter mit variabler Lüftungssteuerung |Axialer Lüfter mit variabler Lüftungssteuerung |

## <a name="power-consumption-statistics"></a>Stromverbrauchsstatistiken

Die folgende Tabelle enthält die typischen Stromverbrauchsdaten für die verschiedenen Modelle von StorSimple-Geräten (tatsächliche Werte können von den veröffentlichten abweichen).

| Bedingungen | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Lüfter langsam, Laufwerke im Leerlauf |1,45 A |0,31 kW |1057,76 BTU/h |3.19 A |0,34 kW |1160,13 BTU/h |
|  Lüfter langsam, Laufwerkszugriff |1,54 A |0,33 kW |1126,01 BTU/h |3.27 A |0,36 kW |1228,37 BTU/h |
|  Lüfter schnell, Laufwerke im Leerlauf, Betrieb mit zwei Netzteilen |2,14 A |0,49 kW |1671,95 BTU/h |4,99 A |0,54 kW |1842,56 BTU/h |
|  Lüfter schnell, Laufwerke im Leerlauf, ein Netzteil zur Stromversorgung und das andere im Leerlauf |2,05 A |0,48 kW |1637,83 BTU/h |4,58 A |0,50 kW |1706,07 BTU/h |
|  Lüfter schnell, Laufwerkszugriff, Betrieb mit zwei Netzteilen |2,26 A |0,51 kW |1740,19 BTU/h |4,95 A |0,54 kW |1842,56 BTU/h |
|  Lüfter schnell, Laufwerkszugriff, ein Netzteil zur Stromversorgung und das andere im Leerlauf |2,14 A |0,49 kW |1671,95 BTU/h |4,81 A |0,53 kW |1808,44 BTU/h |

## <a name="disk-drive-specifications"></a>Laufwerksspezifikationen

Das StorSimple-Gerät unterstützt bis zu zwölf serielle SAS-Laufwerke (Serial Attached SCSI) im 3,5-Zoll-Formfaktor. Bei den tatsächlichen Laufwerken kann es sich je nach Produktkonfiguration um eine Mischumgebung aus Solid-State-Laufwerken (SSDs) oder Festplattenlaufwerken (HDDs) handeln. Die zwölf Laufwerkseinschübe befinden sich in einer 3 x 4-Konfiguration vor dem Gehäuse. Das EBOD-Gehäuse bietet zusätzlichen Platz für weitere zwölf Laufwerke. Dies müssen jedoch HDDs sein.

## <a name="storage-specifications"></a>Speicherspezifikation

Sowohl das StorSimple 8100 als auch das StorSimple 8600 verfügen über eine Kombination aus Festplatten- und Solid-State-Laufwerken. Die für 8100 und 8600 nutzbare Gesamtkapazität beträgt etwa 15 TB bzw. 38 TB. In der folgenden Tabelle sind die Details von SSD-, HDD- und Cloudkapazität im Kontext der Kapazität der StorSimple-Lösung dokumentiert.

| Gerätemodell/Kapazität | 8100 | 8600 |
| --- | --- | --- |
| Anzahl von Festplattenlaufwerken (HDD) |8 |19 |
| Anzahl von Solid-State-Laufwerken (SSDs) |4 |5 |
| Kapazität eines HDD-Laufwerks |4 TB |4 TB |
| Kapazität eines SSD-Laufwerks |400 GB |800 GB |
| Reservekapazität |4 TB |4 TB |
| Nutzbare HDD-Kapazität |14 TB |36 TB |
| Nutzbare SDD-Kapazität |800 GB |2 TB |
| Insgesamt nutzbare Kapazität* |~ 15 TB |~38 TB |
| Maximale Kapazität der Lösung (einschließlich Cloud) |200 TB |500 TB |

<sup>* </sup>- *Die nutzbare Gesamtkapazität beinhaltet die Kapazität, die für Daten, Metadaten und Puffer verfügbar ist. Sie können lokale Volumes von bis zu 8,5 TB auf dem 8100-Gerät und von bis zu 22,5 TB auf dem größeren 8600-Gerät bereitstellen. Weitere Informationen finden Sie unter [Lokale StorSimple-Volumes](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Gehäuseabmessungen und Gewicht

In den folgenden Tabellen sind die verschiedenen Gehäusespezifikationen (Abmessungen und Gewicht) aufgeführt.

### <a name="enclosure-dimensions"></a>Gehäuseabmessungen

Die folgenden Tabellen enthalten die Abmessungen des Gehäuses in Millimetern und Zoll.

| Gehäuse | Millimeter | Zoll |
| --- | --- | --- |
| Höhe |87,9 |3,46 |
| Breite über Rackbefestigung |483 |19,02 |
| Breite über Gehäusefläche |443 |17,44 |
| Tiefe von der vorderen Rackbefestigung bis zum Ende der Gehäusefläche |577 |22,72 |
| Tiefe vom Bedienfeld bis zum äußeren Ende des Gehäuses |630,5 |24,82 |
| Tiefe von der Befestigungsfläche bis zum äußeren Ende des Gehäuses |603 |23,74 |

### <a name="enclosure-weight"></a>Gehäusegewicht

Je nach Konfiguration kann ein vollständig beladenes primäres Gehäuse von 21 kg bis zu 33 kg wiegen und muss von zwei Personen getragen werden.

| Gehäuse | Gewicht |
| --- | --- |
| Max. Gewicht (abhängig von der Konfiguration) |30–33 kg |
| Leer (ohne Laufwerke) |21–23 kg |

## <a name="enclosure-environment-specifications"></a>Spezifikationen der Gehäuseumgebung

Dieser Abschnitt enthält die Spezifikationen im Zusammenhang mit der Gehäuseumgebung. Diese Kategorie enthält die Umgebungsvorgaben für Temperatur, Feuchtigkeit, Höhe, Stoßfestigkeit, Schwingungen, Ausrichtung, Sicherheit und elektromagnetische Kompatibilität (EMC).

### <a name="temperature-and-humidity"></a>Temperatur und Feuchtigkeit

| Gehäuse | Umgebungstemperaturspanne | Relative Luftfeuchtigkeit der Umgebung | Maximale Feuchttemperatur |
| --- | --- | --- | --- |
| Bei Betrieb |5 °C bis 35 °C |20–80 % nicht kondensierend |28 °C |
| Außerhalb des Betriebs |–40 °C bis 70 °C |5–100 % nicht kondensierend |29 °C |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Luftströmung, Höhe, Stöße, Schwingungen, Ausrichtung, Sicherheit und EMC

| Gehäuse | Betriebsspezifikationen |
| --- | --- |
| Luftströmung |Die Luftströmung im System verläuft von vorne nach hinten. Das System muss mit einem hinteren Luftstromauslass mit niedrigem Druck betrieben werden. Der Rückstau durch Gehäusetüren und Hindernisse sollte 5 Pascal (0,5 mm Wassersäule) nicht überschreiten. |
| Höhe, Betrieb |–30 m bis 3045 m (–100 bis 10.000 Fuß) mit einer um 5 °C herabgesetzten maximalen Betriebstemperatur bei Höhen über 7000 Fuß. |
| Höhe, außerhalb des Betriebs |–305 m bis 12.192 m (–1000 Fuß bis 40.000 Fuß) |
| Stoßfestigkeit, bei Betrieb |5g 10ms ½ Sinus |
| Stoßfestigkeit, außerhalb des Betriebs |30g 10ms ½ Sinus |
| Schwingungen, bei Betrieb |0,21 g RMS 5 bis 500 Hz zufällig |
| Schwingungen, außerhalb des Betriebs |1,04 g RMS 2 bis 200 Hz zufällig |
| Schwingungen, bei Umplatzierung |3 g 2 bis 200 Hz Sinus |
| Ausrichtung und Montage |19-Zoll-Rackmontage (2 EIA-Einheiten) |
| Rackschienen |Passend für IEC 297-kompatible Racks mit mindestens 700 mm (31,50 Zoll) Tiefe |
| Sicherheit und Genehmigungen |CE und UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CISPR – A), FCC A |

## <a name="international-standards-compliance"></a>Einhaltung internationaler Standards

Ihr Microsoft Azure StorSimple-Gerät entspricht den folgenden internationalen Standards:  

* CE – EN 60950 – 1
* CB-Zertifizierung für IEC 60950 - 1
* UL und cUL für UL 60950 - 1

## <a name="safety-compliance"></a>Sicherheitskompatibilität

Ihr Microsoft Azure StorSimple-Gerät erfüllt die folgenden Sicherheitsstandards:

* System-Produkttypgenehmigung: UL, cUL, CE
* Sicherheitskompatibilität: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC-kompatibel

Ihr Microsoft Azure StorSimple-Gerät erfüllt die folgenden EMC-Standards:

### <a name="emissions"></a>Emissionen

Das Gerät ist EMC-konform für Leitungs- und Strahlungsemissionswerte.

* Leitungsemissions-Grenzwerte: CFR 47 Teil 15B Klasse A EN55022 Klasse A CISPR Klasse A
* Strahlungsemissions-Grenzwerte: CFR 47 Teil 15B Klasse A EN55022 Klasse A CISPR Klasse A

### <a name="harmonics-and-flicker"></a>Oberschwingungen und Flicker

Das Gerät entspricht EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Immunitätsgrenzwerte

Das Gerät entspricht EN55024.

## <a name="ac-power-cord-compliance"></a>Netzkabelkompatibilität

Der Stecker und das gesamte Netzkabelmodul müssen den Richtlinien des jeweiligen Landes/der jeweiligen Region entsprechen, in dem/der das Gerät verwendet wird, und sie müssen über die erforderlichen Sicherheitsgenehmigungen für das Land/die Region verfügen. In den folgenden Tabellen sind die Standards für USA und Europa aufgeführt.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Netzkabel – USA (muss NRTL-registriert sein)

| Komponente | Spezifikation |
| --- | --- |
| Kabeltyp |SV oder SVT, 18 AWG Minimum, 3 Leiter, 2,0 m Maximallänge |
| Stecker |NEMA 5-15P-Stecker mit Erdung und Nennwerten von 120 V, 10 A; oder IEC 320 C14, 250 V, 10 A |
| Steckdose |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Netzkabel – Europa

| Komponente | Spezifikation |
| --- | --- |
| Kabeltyp |Harmonisiert, H05-VVF-3G1.0 |
| Steckdose |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Unterstützte Netzwerkkabel

Für die 10-GbE-Netzwerkschnittstellen, DATA 2 und DATA 3, finden Sie Informationen in der [Liste der unterstützten Netzwerkkabel und Module](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Nächste Schritte

Sie können jetzt ein StorSimple-Gerät in Ihrem Datencenter bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen Ihres lokalen Geräts](storsimple-8000-deployment-walkthrough-u2.md).

