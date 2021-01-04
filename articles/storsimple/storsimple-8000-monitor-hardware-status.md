---
title: Hardwarekomponenten und Status der StorSimple 8000-Serie| Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe des StorSimple-Geräte-Manager-Diensts die Hardwarekomponenten Ihres StorSimple-Geräts überwachen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960358"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Überwachen von Hardwarekomponenten und Status mithilfe des StorSimple-Geräte-Manager-Diensts

## <a name="overview"></a>Übersicht
Dieser Artikel beschreibt die verschiedenen physischen und logischen Komponenten Ihres lokalen StorSimple-Geräts der Serie 8000. Darüber hinaus wird erläutert, wie Sie den Status der Gerätekomponenten auf dem Blatt **Status und Hardwareintegrität** des StorSimple-Geräte-Manager-Diensts überwachen.

Das Blatt **Status und Hardwareintegrität** zeigt den Hardwarestatus aller StorSimple-Gerätekomponenten.

Unter der Liste mit den Komponenten für 8100 befinden sich drei Abschnitte, die Folgendes beschreiben:

* **Gemeinsam genutzte Komponenten**: Diese Komponenten sind nicht Teil der Controller. Hierbei handelt es sich beispielsweise um Laufwerke, Gehäuse, Komponenten der Stromversorgungs- und Kühleinheit (PCM) sowie Fühler für PCM-Temperatur, -Strom und -Spannung.
* **Controller 0-Komponenten**: Die Komponenten von Controller 0. Hierzu zählen beispielsweise Controller, SAS-Erweiterung und -Connector, Controllertemperaturfühler und die verschiedenen Netzwerkschnittstellen.
* **Controller 1-Komponenten** : Die Komponenten von Controller 1 (ähnlich wie bei Controller 0).

Ein Gerät vom Typ 8600 verfügt über zusätzliche Komponenten für das EBOD-Gehäuse. Unter der Komponentenliste befinden sich fünf Abschnitte. Davon enthalten drei Abschnitte die Komponenten des primären Gehäuses. Diese entsprechen der Beschreibung für 8100. Die beiden zusätzlichen Abschnitte beziehen sich auf das EBOD-Gehäuse und beschreiben Folgendes:

* **EBOD-Controller 0-Komponenten**: Die Komponenten im EBOD-Gehäuse 0. Hierzu zählen beispielsweise EBOD-Controller, SAS-Erweiterung und -Connector sowie Controllertemperaturfühler.
* **EBOD-Controller 1-Komponenten** : Die Komponenten von EBOD-Gehäuse 1 (ähnlich wie bei EBOD-Gehäuse 0).
* **Gemeinsam genutzte EBOD-Komponenten** : Die Komponenten im EBOD-Gehäuse und PCM, die nicht Teil des EBOD-Controllers sind.

> [!NOTE]
> **Der Hardwarestatus ist für StorSimple Cloud Appliances (8010/8020) nicht verfügbar.**


## <a name="monitor-the-hardware-status"></a>Überwachen des Hardwarestatus
Führen Sie die folgenden Schritte aus, um den Hardwarestatus einer Gerätekomponente anzuzeigen:

1. Navigieren Sie zu **Geräte**, und wählen Sie ein bestimmtes StorSimple-Gerät aus. Wechseln Sie zu **Überwachung > Hardwareintegrität**.

    ![Screenshot, der das Blatt „Einstellungen“ des Geräte-Manager-Diensts zeigt. Unter „Überwachung“ ist „Hardwareintegrität“ ausgewählt.](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Navigieren Sie zum Abschnitt **Hardwarekomponenten**, und wählen Sie eine der verfügbaren Komponenten aus. Klicken Sie einfach auf die Komponentenbezeichnung, um die Liste zu erweitern und den Status der verschiedenen Gerätekomponenten anzuzeigen. Weitere Informationen finden Sie in der [ausführlichen Komponentenliste für das primäre Gehäuse](#component-list-for-primary-enclosure-of-storsimple-device) sowie in der [ausführlichen Komponentenliste für das EBOD-Gehäuse](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![Screenshot, der eine ausgewählte Komponente im Abschnitt mit den Hardwarekomponenten zeigt. Auf einem angrenzenden Blatt werden der Name und der Status der Teile dieser Komponente angezeigt.](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Der Komponentenstatus basiert auf dem folgenden Farbschema:
   
   * **Grünes Häkchen**: gibt den Status fehlerfreier Komponenten als **OK** an
   * **Gelb**: gibt Komponenten mit einer **Warnung** an
   * **Rotes Ausrufezeichen**: gibt Komponenten mit einem **Fehler** an.
   * **Weiß mit schwarzem Text** : Die Komponente ist nicht vorhanden.
   
   Der folgende Screenshot zeigt ein Gerät, das Komponenten mit den Status **OK**, **Warnung** und **Fehler** aufweist.
       
   ![Screenshot, der drei Komponenten im Abschnitt mit den Hardwarekomponenten zeigt: eine mit dem Status „In Ordnung“, eine im Status „Fehler“ und eine im Status „Warnung“.](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Erweitern Sie Liste **Freigegebene Komponenten**, um zu sehen, dass der NVRAM und der Cluster fehlerhaft sind.

   ![Screenshot, der das ausgewählte Element „Freigegebene Komponenten“ anzeigt. Auf dem angrenzenden Blatt wird für den NVRAM und den Cluster der Status „Warnung“ angezeigt.](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Erweitern Sie die Liste **Controller 1-Komponenten**, um zu sehen, dass der Clusterknoten fehlerhaft ist.  

   ![Screenshot, der das ausgewählte Element „Controller 1-Komponenten“ anzeigt. Auf dem angrenzenden Blatt weist der Clusterknoten den Status „Fehler“ auf.](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Sollte sich eine Komponente nicht in einem **fehlerfreien** Zustand befinden, wenden Sie sich an den Support von Microsoft. Sind Warnungen auf dem Gerät aktiviert, werden Sie per E-Mail benachrichtigt. Informationen zum Austauschen fehlerhafter Hardwarekomponenten finden Sie unter [Austausch von StorSimple-Hardwarekomponenten](./storsimple-8000-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Komponentenliste für das primäre Gehäuse eines StorSimple-Geräts
Die folgende Tabelle gibt Aufschluss über die physischen und logischen Komponenten im primären Gehäuse Ihres lokalen StorSimple-Geräts (sowohl bei 8100 als auch bei 8600).

| Komponente | Modul | Typ | Standort | FRU (Field Replaceable Unit)? | BESCHREIBUNG |
| --- | --- | --- | --- | --- | --- |
| Laufwerk in Einschubfach [0-11] |Laufwerke |Physisch |Shared |Ja |Die einzelnen SSD- oder HDD-Laufwerke des primären Gehäuses werden jeweils in einer eigenen Zeile angegeben. |
| Ambiente-Temperatursensor |Gehäuse |Physisch |Shared |Nein |Misst die Temperatur im Gehäuse. |
| Midplane-Temperatursensor |Gehäuse |Physisch |Shared |Nein |Misst die Temperatur auf der mittleren Ebene. |
| Hörbarer Alarm |Gehäuse |Physisch |Shared |Nein |Gibt an, ob das Subsystem für den akustischen Alarm im Gehäuse funktionsfähig ist. |
| Gehäuse |Gehäuse |Physisch |Shared |Ja |Gibt an, dass ein Gehäuse vorhanden ist. |
| Gehäuseeinstellungen |Gehäuse |Physisch |Shared |Nein |Bezieht sich auf das vordere Bedienfeld des Gehäuses. |
| Spannungssensoren |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Spannungssensoren gibt Aufschluss darüber, ob die gemessene Spannung innerhalb des Toleranzbereichs liegt. |
| Stromsensoren |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Stromsensoren gibt Aufschluss darüber, ob die gemessene Stromstärke innerhalb des Toleranzbereichs liegt. |
| Temperatursensoren in PCM |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Temperatursensoren (beispielsweise Einlass- und Hotspot-Sensoren) gibt Aufschluss darüber, ob die gemessene Temperatur innerhalb des Toleranzbereichs liegt. |
| Netzteil [0-1] |PCM |Physisch |Shared |Ja |Für die einzelnen Stromversorgungen in den beiden PCMs an der Geräterückseite wird jeweils eine eigene Zeile angezeigt. |
| Kühlung [0-1] |PCM |Physisch |Shared |Ja |Für die vier Lüfter in den beiden PCMs wird jeweils eine eigene Zeile angezeigt. |
| Pufferbatterie [0-1] |PCM |Physisch |Shared |Ja |Für die einzelnen Sicherungsakkumodule im PCM wird jeweils eine eigene Zeile angezeigt. |
| Metis |– |Logisch |Shared |– |Zeigt den Zustand der Akkus an und gibt Aufschluss über Ladebedarf und Haltbarkeit. |
| Cluster |– |Logisch |Shared |– |Zeigt den Zustand des Clusters, der zwischen den beiden integrierten Controller-Modulen erstellt wird. |
| Clusterknoten |– |Logisch |Shared |– |Gibt den Zustand des Controllers als Teil des Clusters an. |
| Clusterquorum |– |Logisch | |– |Gibt an, dass die Mehrheitsdatenträger-Mitgliedschaft im HDD-Speicherpool vorhanden ist. |
| HDD-Datenbereich |– |Logisch |Shared |– |Der Speicherplatz, der für Daten im HDD-Speicherpool verwendet wird. |
| HDD-Verwaltungsbereich |– |Logisch |Shared |– |Der Platz, der im HDD-Speicherpool für Verwaltungsaufgaben reserviert ist. |
| HDD-Quorumbereich |– |Logisch |Shared |– |Der Platz, der im HDD-Speicherpool für das Clusterquorum reserviert ist. |
| HDD-Ersetzungsbereich |– |Logisch |Shared |– |Der Platz, der im HDD-Speicherpool für die Controllerersetzung reserviert ist. |
| SSD-Datenbereich |– |Logisch |Shared |– |Der Speicherplatz, der im SSD-Speicherpool für Daten verwendet wird. |
| SSD-NVRAM-Bereich |– |Logisch |Shared |– |Der Speicherplatz im SSD-Speicherpool, der für die NVRAM-Logik reserviert ist. |
| HDD-Speicherpool |– |Logisch |Shared |– |Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-HDDs erstellt wird. |
| SSD-Speicherpool |– |Logisch |Shared |– |Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-SSDs erstellt wird. |
| Controller [0-1] [Status] |E/A |Physisch |Controller |Ja |Zeigt den Zustand des Controllers im Gehäuse und gibt Aufschluss darüber, ob er aktiv oder im Standbymodus ist. |
| Temperatursensoren im Controller |E/A |Physisch |Controller |Nein |Der angezeigte Zustand zahlreicher Temperatursensoren (etwa für das E/A-Modul, für die CPU-Temperatur sowie DIMM- und PCIe-Sensoren) gibt Aufschluss darüber, ob die Temperatur innerhalb des Toleranzbereichs liegt. |
| SAS-Erweiterung |E/A |Physisch |Controller |Nein |Gibt den Zustand der SAS-Erweiterung an, über die der integrierte Speicher mit dem Controller verbunden wird. |
| SAS-Anschluss [0-1] |E/A |Physisch |Controller |Nein |Gibt den Zustand der einzelnen SAS-Verbinder an, über die der integrierte Speicher mit der SAS-Erweiterung verbunden wird. |
| SBB-Midplane-Interconnect |E/A |Physisch |Controller |Nein |Gibt den Zustand des Midplane-Verbinders an, über den die einzelnen Controller mit der mittleren Ebene verbunden werden. |
| Prozessorkern |E/A |Physisch |Controller |Nein |Gibt den Zustand der Prozessorkerne in den einzelnen Controllern an. |
| Gehäuseelektronikleistung |E/A |Physisch |Controller |Nein |Gibt den Zustand der Stromversorgung des Gehäuses an. |
| Gehäuseelektronikdiagnose |E/A |Physisch |Controller |Nein |Gibt den Zustand der vom Controller bereitgestellten Diagnosesubsysteme an. |
| Baseboard-Verwaltungscontroller |E/A |Physisch |Controller |Nein |Gibt den Zustand des Baseboard-Verwaltungscontrollers (Baseboard Management Controller, BMC) an. Hierbei handelt es sich um einen speziellen Dienstprozessor, der das Hardwaregerät mithilfe von Sensoren überwacht und mit dem Systemadministrator über eine unabhängige Verbindung kommuniziert. |
| Ethernet |E/A |Physisch |Controller |Nein |Gibt den Zustand der einzelnen Netzwerkschnittstellen (Verwaltungs- und Datenports des Controllers) an. |
| NVRAM |E/A |Physisch |Controller |Nein |Gibt den Zustand des NVRAM (permanenter, vom Akku versorgter Arbeitsspeicher zur Bewahrung anwendungskritischer Informationen bei einem Stromausfall) an. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Komponentenliste für das EBOD-Gehäuse eines StorSimple-Geräts
Die folgende Tabelle gibt Aufschluss über die physischen und logischen Komponenten im EBOD-Gehäuse Ihres lokalen StorSimple-Geräts (nur bei 8600).

| Komponente | Modul | Typ | Standort | FRU? | BESCHREIBUNG |
| --- | --- | --- | --- | --- | --- |
| Laufwerk in Einschubfach [0-11] |Laufwerke |Physisch |Shared |Ja |Die einzelnen HDD-Laufwerke an der Vorderseite des EBOD-Gehäuses werden jeweils in einer eigenen Zeile angegeben. |
| Ambiente-Temperatursensor |Gehäuse |Physisch |Shared |Nein |Misst die Temperatur im Gehäuse. |
| Midplane-Temperatursensor |Gehäuse |Physisch |Shared |Nein |Misst die Temperatur auf der mittleren Ebene. |
| Hörbarer Alarm |Gehäuse |Physisch |Shared |Nein |Gibt an, ob das Subsystem für den akustischen Alarm im Gehäuse funktionsfähig ist. |
| Gehäuse |Gehäuse |Physisch |Shared |Ja |Gibt an, dass ein Gehäuse vorhanden ist. |
| Gehäuseeinstellungen |Gehäuse |Physisch |Shared |Nein |Bezieht sich auf das OPS oder vordere Bedienfeld des Gehäuses. |
| Spannungssensoren |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Spannungssensoren gibt Aufschluss darüber, ob die gemessene Spannung innerhalb des Toleranzbereichs liegt. |
| Stromsensoren |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Stromsensoren gibt Aufschluss darüber, ob die gemessene Stromstärke innerhalb des Toleranzbereichs liegt. |
| Temperatursensoren in PCM |PCM |Physisch |Shared |Nein |Der angezeigte Zustand zahlreicher Temperatursensoren (beispielsweise Einlass- und Hotspot-Sensoren) gibt Aufschluss darüber, ob die gemessene Temperatur innerhalb des Toleranzbereichs liegt. |
| Netzteil [0-1] |PCM |Physisch |Shared |Ja |Für die einzelnen Stromversorgungen in den beiden PCMs an der Geräterückseite wird jeweils eine eigene Zeile angezeigt. |
| Kühlung [0-1] |PCM |Physisch |Shared |Ja |Für die vier Lüfter in den beiden PCMs wird jeweils eine eigene Zeile angezeigt. |
| Lokaler Speicher [HDD] |– |Logisch |Shared |– |Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-HDDs erstellt wird. |
| Controller [0-1] [Status] |E/A |Physisch |Controller |Ja |Zeigt den Zustand der Controller im EBOD-Modul an. |
| Temperatursensoren in EBOD |E/A |Physisch |Controller |Nein |Der angezeigte Zustand zahlreicher Temperatursensoren der einzelnen Controller gibt Aufschluss darüber, ob die Temperatur innerhalb des Toleranzbereichs liegt. |
| SAS-Erweiterung |E/A |Physisch |Controller |Nein |Gibt den Zustand der SAS-Erweiterung an, über die der integrierte Speicher mit dem Controller verbunden wird. |
| SAS-Anschluss [0-2] |E/A |Physisch |Controller |Nein |Gibt den Zustand der einzelnen SAS-Verbinder an, über die der integrierte Speicher mit der SAS-Erweiterung verbunden wird. |
| SBB-Midplane-Interconnect |E/A |Physisch |Controller |Nein |Gibt den Zustand des Midplane-Verbinders an, über den die einzelnen Controller mit der mittleren Ebene verbunden werden. |
| Gehäuseelektronikleistung |E/A |Physisch |Controller |Nein |Gibt den Zustand der Stromversorgung des Gehäuses an. |
| Gehäuseelektronikdiagnose |E/A |Physisch |Controller |Nein |Gibt den Zustand der vom Controller bereitgestellten Diagnosesubsysteme an. |
| Verbindung mit Gerätecontroller |E/A |Physisch |Controller |Nein |Gibt den Zustand der Verbindung zwischen dem EBOD-E/A-Modul und dem Gerätecontroller an. |

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Verwalten Ihres Geräts mit dem StorSimple-Geräte-Manager-Dienst finden Sie unter [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).
* Informationen zum Behandeln von Problemen mit einer beeinträchtigten oder fehlerhaften Gerätekomponente finden Sie unter [StorSimple-Überwachungsindikatoren](storsimple-monitoring-indicators.md).
* Informationen zum Austauschen fehlerhafter Hardwarekomponenten finden Sie unter [Austausch von StorSimple-Hardwarekomponenten](./storsimple-8000-hardware-component-replacement.md).
* Sollten weiterhin Geräteprobleme auftreten, [wenden Sie sich an den Microsoft-Support](storsimple-8000-contact-microsoft-support.md).