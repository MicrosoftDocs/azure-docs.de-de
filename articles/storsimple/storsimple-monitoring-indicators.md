---
title: StorSimple-Überwachungsindikatoren | Microsoft Docs
description: Beschreibt die LEDs und akustischen Alarme, die zum Überwachen des Status Ihres StorSimple-Geräts verwendet werden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 97209dca7d30de037dbd21f5cc145b2941060e70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015399"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Verwalten von Geräten mithilfe von StorSimple-Überwachungsindikatoren


## <a name="overview"></a>Übersicht
Ihr StorSimple-Gerät bietet LEDs (Light-Emitting Diodes) und Alarme, mit denen Sie die Module und den Gesamtstatus des StorSimple-Geräts überwachen können. Die Überwachungsindikatoren befinden sich in den Hardwarekomponenten des primären Gehäuses des Geräts und des EBOD-Gehäuses. Die Überwachungsindikatoren können LEDs oder akustische Alarme sein.

Es gibt drei LED-Statusanzeigen, mit denen der Status eines Moduls gekennzeichnet wird: grün, grün bis rot-bernsteinfarben blinkend oder rot-bernsteinfarben.  

* Grüne LEDs weisen auf einen fehlerfreien Betrieb hin.  
* Grün bis rot-bernsteinfarben blinkende LEDs weisen auf unkritische Bedingungen hin, die möglicherweise einen Benutzereingriff erfordern.  
* Rot-bernsteinfarbene LEDs zeigen an, dass im Modul ein kritischer Fehler aufgetreten ist.  

In den folgenden Abschnitten finden Sie Informationen zu den verschiedenen Überwachungsindikator-LEDs, zu deren Positionen am StorSimple-Gerät, zum Gerätestatus für die jeweilige LED-Statusanzeige sowie zu den zugehörigen akustischen Alarmen.

## <a name="front-panel-indicator-leds"></a>Indikator-LEDs des vorderen Bedienfelds
Das vordere *Bedienfeld* oder *OPS-Bedienfeld* zeigt sämtliche Statusangaben zu allen Modulen im System an. Das vordere Bedienfeld ist beim primären StorSimple-Gehäuse und beim EBOD-Gehäuse identisch, wie nachfolgend dargestellt.  

   ![Vorderseite des Geräts][1]

Das vordere Bedienfeld enthält die folgenden Indikatoren:  

1. Taste zum Stummschalten
2. Betriebsanzeige-LED (grün/rot-bernsteinfarben)
3. Modulfehleranzeige-LED (EIN rot-bernsteinfarben/AUS)
4. Logische Fehleranzeige-LED (EIN rot-bernsteinfarben/AUS)
5. Anzeige der Gerätekennung  

Der Hauptunterschied zwischen den LEDs des vorderen Bedienfelds am Gerät und am EBOD-Gehäuse ist die **Systemeinheit-ID** , die auf der LED-Anzeige dargestellt wird. Die auf dem Gerät angezeigte Standard-ID der Einheit lautet **00**, während die auf dem EBOD-Gehäuse angezeigte Standard-ID der Einheit **01** lautet. Auf diese Weise können Sie schnell zwischen dem Gerät und dem EBOD-Gehäuse unterscheiden, wenn das Gerät eingeschaltet wird. Wenn Ihr Gerät ausgeschaltet ist, verwenden Sie die unter dem Thema [Einschalten eines neuen Geräts](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) bereitgestellten Informationen, um das Gerät vom EBOD-Gehäuse zu unterscheiden.  

## <a name="front-panel-led-status"></a>LED-Status am vorderen Bedienfeld
Anhand der folgenden Tabelle können Sie den Status, der über die LEDs auf dem vorderen Bedienfeld für das Gerät oder das EBOD-Gehäuse angezeigt wird, ermitteln.  

| Systemstromversorgung | Modulfehler | Logischer Fehler | Alarm | Status |
| --- | --- | --- | --- | --- |
| Rot-bernsteinfarben |OFF |OFF |– |Stromausfall, Betrieb über alternative Stromversorgung, oder die Stromversorgung ist eingeschaltet, und die Controllermodule wurden entfernt. |
| Grün |EIN |EIN |– |Teststatus für eingeschaltete Stromversorgung des OPS-Bedienfelds (5s) |
| Grün |OFF |OFF |– |Eingeschaltet, alle Funktionen einwandfrei |
| Grün |EIN |– |PCM-Fehler-LEDs, Lüfterfehler-LEDs |Jeder PCM-Fehler, Lüfterfehler, zu hohe oder zu niedrige Temperatur |
| Grün |EIN |– |E/A-Modul-LEDs |Jeder Controllermodulfehler |
| Grün |EIN |– |– |Gehäuselogikfehler |
| Grün |Blinken |– |Modulstatus-LED am Controllermodul. PCM-Fehler-LEDs, Lüfterfehler-LEDs |Unbekannter Controllermodultyp installiert, I2C-Busfehler, Konfigurationsfehler bei wichtigen Produktdaten des Controllermoduls |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Indikator-LEDs der Stromversorgungs- und Kühleinheit
Die Indikator-LEDs der Stromversorgungs- und Kühleinheit (PCM) befinden sich auf der Rückseite des primären Gehäuses oder EBOD-Gehäuses der einzelnen PCM-Module. In diesem Thema wird die Verwendung der folgenden LEDs zur Überwachung des Status Ihres StorSimple-Geräts beschrieben.  

* LEDs der Stromversorgungs- und Kühleinheit (PCM) für das primäre Gehäuse
* LEDs der Stromversorgungs- und Kühleinheit (PCM) für das EBOD-Gehäuse

## <a name="pcm-leds-for-the-primary-enclosure"></a>LEDs der Stromversorgungs- und Kühleinheit (PCM) für das primäre Gehäuse
Das StorSimple-Gerät verfügt über ein 764-W-PCM-Modul mit zusätzlichem Akku. In der folgenden Abbildung wird das LED-Bedienfeld für das Gerät dargestellt.  

   ![LEDs der Stromversorgungs- und Kühleinheit (PCM) am primären Gehäuse][2]

LED-Legende:

1. Stromausfall (Wechselstrom)
2. Lüfterausfall
3. Batterieausfall
4. PCM OK
5. Gleichstromfehler
6. Akku OK  

Der Status der Stromversorgungs- und Kühleinheit (PCM) wird auf dem LED-Bedienfeld angezeigt. Das LED-Bedienfeld der Stromversorgungs- und Kühleinheit des Geräts verfügt über sechs LEDs. Vier dieser LEDs zeigen den Status der Stromversorgung und des Lüfters an. Die beiden übrigen LEDs zeigen den Status des Notfallakkus in der Stromversorgungs- und Kühleinheit an. Sie können den Status der Stromversorgungs- und Kühleinheit anhand der folgenden Tabellen ermitteln.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM-Indikator-LEDs für die Stromversorgung und den Lüfter
| Status | PCM OK (grün) | Stromausfall (bernsteinfarben) | Lüfterfehler (bernsteinfarben) | Gleichstromfehler (bernsteinfarben) |
| --- | --- | --- | --- | --- |
| Keine Stromversorgung (am Gehäuse) |OFF |OFF |OFF |OFF |
| Keine Stromversorgung (nur diese PCM) |OFF |EIN |OFF |EIN |
| Stromversorgung verfügbar, PCM EIN – OK |EIN |OFF |OFF |OFF |
| PCM-Fehler (Lüfterfehler) |OFF |OFF |EIN |– |
| PCM-Fehler (zu hohe Spannung oder Stromstärke) |OFF |EIN |EIN |EIN |
| PCM (Lüfter außerhalb des Toleranzbereichs) |EIN |OFF |OFF |EIN |
| Standby-Modus |Blinken |OFF |OFF |OFF |
| PCM-Firmwaredownload |OFF |Blinken |Blinken |Blinken |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM-Indikator-LEDs für Notfallakku
| Status | Akku OK (grün) | Akkufehler (bernsteinfarben) |
| --- | --- | --- |
| Akku nicht vorhanden |OFF |OFF |
| Akku vorhanden und aufgeladen |EIN |OFF |
| Akku wird aufgeladen oder zur Wartung entladen |Blinken |OFF |
| "Leichter" Akkufehler (wiederherstellbar) |OFF |Blinken |
| "Schwerer" Akkufehler (nicht wiederherstellbar) |OFF |EIN |
| Akku deaktiviert |Blinken |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>LEDs der Stromversorgungs- und Kühleinheit (PCM) für das EBOD-Gehäuse
Das EBOD-Gehäuse verfügt über eine 580-W-PCM ohne zusätzlichen Akku. Das PCM-Bedienfeld für das EBOD-Gehäuse besitzt nur für die Stromversorgung und den Lüfter Indikator-LEDs. Diese LEDs werden in der folgenden Abbildung dargestellt.

   ![LEDs der Stromversorgungs- und Kühleinheit (PCM) am EBOD-Gehäuse][3] 

Sie können den Status der PCM anhand der folgenden Tabelle ermitteln.  

| Status | PCM OK (grün) | Stromausfall (bernsteinfarben) | Lüfterfehler (bernsteinfarben) | Gleichstromfehler (bernsteinfarben) |
| --- | --- | --- | --- | --- |
| Keine Stromversorgung (am Gehäuse) |OFF |OFF |OFF |OFF |
| Keine Stromversorgung (nur diese PCM) |OFF |EIN |OFF |EIN |
| Stromversorgung verfügbar, PCM EIN – OK |EIN |OFF |OFF |OFF |
| PCM-Fehler (Lüfterfehler) |OFF |OFF |EIN |X |
| PCM-Fehler (zu hohe Spannung oder Stromstärke) |OFF |EIN |EIN |EIN |
| PCM (Lüfter außerhalb des Toleranzbereichs) |EIN |OFF |OFF |EIN |
| Standby-Modus |Blinken |OFF |OFF |OFF |
| PCM-Firmwaredownload |OFF |Blinken |Blinken |Blinken |

## <a name="controller-module-indicator-leds"></a>Indikator-LEDs des Controllermoduls
Das StorSimple-Gerät besitzt LEDs für den primären Controller und die EBOD-Controllermodule.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Indikator-LEDs für den primären Controller
Die folgende Abbildung unterstützt Sie bei der Identifizierung der LEDs für den primären Controller. (Zur einfacheren Orientierung werden alle Komponenten aufgelistet.)  

   ![Indikator-LEDs – primärer Controller][4]

Anhand der folgenden Tabelle können Sie ermitteln, ob das Controllermodul ordnungsgemäß funktioniert.  

### <a name="controller-indicator-leds"></a>Indikator-LEDs des Controllers
| LED | BESCHREIBUNG |
| --- | --- |
| ID-LED (blau) |Zeigt an, dass das Modul identifiziert wurde. Wenn die blaue LED an einem laufenden Controller blinkt, ist dieser Controller der aktive Controller und der andere Controller der Standbycontroller. Weitere Informationen finden Sie unter [Identifizieren des aktiven Controllers auf Ihrem Gerät](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Fehler-LED (bernsteinfarben) |Zeigt einen Fehler im Controller an. |
| OK-LED (grün) |Gleichmäßig leuchtendes Grün zeigt an, dass der Controller ordnungsgemäß funktioniert. Blinkendes Grün zeigt einen VPD-Konfigurationsfehler des Controllers an. |
| SAS-Aktivitäts-LEDs (grün) |Gleichmäßiges leuchtendes Grün zeigt eine Verbindung ohne aktuelle Aktivität an. Blinkendes Grün zeigt an, dass für die Verbindung eine laufende Aktivität besteht. |
| Ethernetstatus-LEDs |Rechte Seite = Verbindung/Netzwerkaktivität: (gleichmäßig grün) Verbindung aktiv, (blinkend grün) Netzwerkaktivität. Linke Seite = Netzwerkgeschwindigkeit: 1000 MBit/s (gelb), 100 MBit/s (grün) und 10 MBit/s (AUS). Je nach Komponentenmodell kann diese LED auch blinken, wenn die Netzwerkschnittstelle nicht aktiviert ist. |
| POST-LEDs |Gibt den Startstatus an, wenn der Controller eingeschaltet wird. Wenn das StorSimple-Gerät nicht startet, kann der Microsoft-Support anhand dieser LED ermitteln, an welchem Punkt des Startvorgangs der Fehler aufgetreten ist. |

> [!IMPORTANT]
> Wenn die Fehler-LED leuchtet, besteht ein Problem mit dem Controllermodul, das eventuell durch einen Neustart des Controllers behoben werden kann. Wenden Sie sich an den Microsoft-Support, wenn dieses Problem nicht durch einen Neustart des Controllers behoben werden kann.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Indikator-LEDs für EBOD (EBOD-Gehäuse)
Jeder der sechs GBit/s-SAS-EBOD-Controller verfügt über LEDs, die den Status wie in der folgenden Abbildung dargestellt anzeigen.  

  ![Indikator-LEDs – EBOD-Gehäuse][5]

Anhand der folgenden Tabelle können Sie ermitteln, ob das EBOD-Controllermodul ordnungsgemäß funktioniert.  

### <a name="ebod-controller-module-indicator-leds"></a>Indikator-LEDs des EBOD-Controllermoduls
| Status | E/A-Modul OK (grün) | E/A-Modulfehler (bernsteinfarben) | Aktivität am Hostport (grün) |
| --- | --- | --- | --- |
| Controllermodul OK |EIN |OFF |- |
| Controllermodulfehler |OFF |EIN |- |
| Keine externe Hostportverbindung |- |- |OFF |
| Externe Hostportverbindung – keine Aktivität |- |- |EIN |
| Externe Hostportverbindung – Aktivität |- |- |Blinken |
| Controllermodul-Metadatenfehler |Blinken |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indikator-LEDs des Datenträgerlaufwerks am primären und am EBOD-Gehäuse
Das StorSimple-Gerät verfügt über Datenträgerlaufwerke am primären Gehäuse und am EBOD-Gehäuse. Jedes Datenträgerlaufwerk enthält Überwachungsindikator-LEDs, die in diesem Abschnitt beschrieben werden. 

Für die Datenträgerlaufwerke wird der Laufwerkstatus durch eine grüne LED und eine rot-bernsteinfarbene LED angezeigt, die an der Vorderseite jedes Laufwerkträgermoduls angebracht ist. Diese LEDs werden in der folgenden Abbildung dargestellt.

  ![LEDs des Datenträgerlaufwerks][6]

Anhand der folgenden Tabelle können Sie den Status der einzelnen Datenträgerlaufwerke bestimmen, der sich wiederum auf den LED-Status der gesamten Vorderseite auswirkt.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indikator-LEDs des Datenträgerlaufwerks am EBOD-Gehäuse
| Status | Aktivität OK-LED (grün) | Fehler-LED (rot-bernsteinfarben) | Zugehörige LED des Bedienfelds |
| --- | --- | --- | --- |
| Kein Laufwerk installiert |OFF |OFF |Keine |
| Laufwerk installiert und betriebsbereit |Blinken ein/aus bei Aktivität |X |Keine |
| Identität des SES-Geräts (SCSI Enclosure Services) festgelegt |EIN |Blinken eine Sekunde ein/eine Sekunde aus |Keine |
| Fehlerbit des SES-Geräts festgelegt |EIN |EIN |Logischer Fehler (rot) |
| Fehler des Energiesteuerungs-Schaltkreises |OFF |EIN |Modulfehler (rot) |

## <a name="audible-alarms"></a>Akustische Alarme
Ein StorSimple-Gerät enthält akustische Alarme, die dem primären Gehäuse und dem EBOD-Gehäuse zugeordnet sind. Der akustische Alarm befindet sich auf dem vorderen Bedienfeld (auch als OPS-Bedienfeld bezeichnet) der beiden Gehäuse. Der akustische Alarm gibt an, dass eine Fehlerbedingung vorhanden ist. Die folgenden Bedingungen aktivieren den Alarm:  

* Lüfterfehler oder -ausfall
* Spannung außerhalb des gültigen Bereichs
* Zu hohe oder zu niedrige Temperatur
* Temperaturüberlauf
* Systemfehler
* Logischer Fehler
* Stromversorgungsfehler
* Entfernung einer Stromversorgungs- und Kühleinheit (Power Cooling Module, PCM)  

Die verschiedenen Alarmzustände werden in der folgenden Tabelle beschrieben.  

### <a name="alarm-states"></a>Alarmzustände
| Alarmzustand | Action | Aktion mit gedrückter Stummtaste |
| --- | --- | --- |
| S0 |Normaler Modus: lautlos |Zweimaliger Piepton |
| S1 |Fehlermodus: eine Sekunde ein/eine Sekunde aus |Übergang in S2 oder S3 (siehe Anmerkungen) |
| S2 |Erinnerungsmodus: intermittierender Piepton |Keine |
| S3 |Stummgeschalteter Modus: lautlos |Keine |
| S4 |Kritischer Fehlermodus: Daueralarm |Nicht verfügbar: Stummschaltung nicht aktiv |

> [!NOTE]
> * Wenn Sie im Alarmzustand S1 die Stummschalttaste nicht innerhalb von zwei Minuten drücken, erfolgt automatisch ein Übergang in den Zustand S2 oder S3.  
> * Die Alarmzustände S1 bis S4 kehren zu S0 zurück, sobald die Fehlerbedingung behoben wurde.  
> * Der kritische Fehlerzustand S4 kann aus jedem anderen Zustand erreicht werden.  


Der akustische Alarm kann durch Drücken der Stummschalttaste auf dem OPS-Bedienfeld stumm geschaltet werden. Die automatische Stummschaltung erfolgt nach zwei Minuten, wenn der Stummschalter nicht manuell betrieben wird. Wenn der Alarm stummgeschaltet wird, erfolgen weiterhin kurze, intermittierende Pieptöne, um anzuzeigen, dass weiterhin ein Problem vorliegt. Der Alarm verstummt, wenn alle Probleme behoben sind.

Die verschiedenen Alarmbedingungen werden in der folgenden Tabelle beschrieben.

### <a name="alarm-conditions"></a>Alarmbedingungen
| Status | severity | Alarm | LED des Bedienfelds |
| --- | --- | --- | --- |
| PCM-Alarm – Verlust des Gleichstroms von einer PCM |Fehler – kein Verlust der Redundanz |S1 |Modulfehler |
| PCM-Alarm – Verlust des Gleichstroms von einer PCM |Fehler – Verlust der Redundanz |S1 |Modulfehler |
| PCM-Lüfterfehler |Fehler – Verlust der Redundanz |S1 |Modulfehler |
| SBB-Modul hat PCM-Fehler erkannt |Fehler |S1 |Modulfehler |
| PCM entfernt |Konfigurationsfehler |Keine |Modulfehler |
| Gehäusekonfigurationsfehler |Fehler – kritisch |S1 |Modulfehler |
| Alarm bei Warnung für zu niedrige Temperatur |Warnung |S1 |Modulfehler |
| Alarm bei Warnung für zu hohe Temperatur |Warnung |S1 |Modulfehler |
| Alarm bei Temperaturüberschreitung |Fehler – kritisch |S1 |Modulfehler |
| Fehler des I2C-Bus |Fehler – Verlust der Redundanz |S1 |Modulfehler |
| Kommunikationsfehler des OPS-Bedienfelds (I2C) |Fehler – kritisch |S1 |Modulfehler |
| Controllerfehler |Fehler – kritisch |S1 |Modulfehler |
| Fehler des SBB-Schnittstellenmoduls |Fehler – kritisch |S1 |Modulfehler |
| Fehler des SBB-Schnittstellenmoduls – keine funktionierenden Module verbleibend |Fehler – kritisch |S4 |Modulfehler |
| SBB-Schnittstellenmodul entfernt |Warnung |Keine |Modulfehler |
| Fehler der Energiesteuerung des Laufwerks |Warnung – kein Verlust der Laufwerkenergie |S1 |Modulfehler |
| Fehler der Energiesteuerung des Laufwerks |Fehler – kritisch – Verlust der Laufwerkenergie |S1 |Modulfehler |
| Laufwerk entfernt |Warnung |Keine |Modulfehler |
| Keine ausreichende Energie verfügbar |Warnung |none |Modulfehler |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu [StorSimple-Hardwarekomponenten und ihrem Status](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


