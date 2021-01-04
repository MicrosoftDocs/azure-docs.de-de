---
title: Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array
description: Beschreibt wichtige offene Probleme und Lösungen für StorSimple Virtual Array mit Update 0.3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 8301b45fe778bd3df7fc665db2662ba81d3e644f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966104"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array
## <a name="overview"></a>Übersicht
Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres StorSimple Virtual Arrays die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 0.3 entspricht Softwareversion **10.0.10288.0**.

> [!NOTE]
> Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Neuerungen in Update 0.3
Update 0.3 ist in erster Linie ein Build zur Fehlerkorrektur. In dieser Version wurden mehrere Fehler behoben, die in der vorherigen Version zu Fehlern bei Sicherungen geführt haben.

## <a name="issues-fixed-in-the-update-03"></a>In Update 0.3 behobene Probleme
Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in dieser Version behoben wurden:

| Nein. | Funktion | Problem |
| --- | --- | --- |
| 1 |Backups |In der vorherigen Version wurde das Problem festgestellt, dass die Sicherungen für eine Dateifreigabe nicht abgeschlossen werden konnten. In diesem Fall trat beim Sicherungsauftrag ein Fehler auf, und im StorSimple Manager-Dienst wurde eine kritische Warnung ausgelöst, um den Benutzer zu benachrichtigen. Das Problem wirkte sich nicht auf die Daten auf den Freigaben oder den Zugriff auf die Daten aus. Die Ursache wurde identifiziert und in dieser Version beseitigt. <br></br> Die Korrektur gilt nicht rückwirkend für Freigaben, bei denen dieses Problem bereits aufgetreten ist. Kunden, bei denen dieses Problem auftritt, sollten zuerst Update 0.3 anwenden und sich dann an den Microsoft-Support wenden, um zur Behebung des Problems eine vollständige Systemsicherung durchführen zu lassen. Anstatt den Microsoft-Support zu kontaktieren, können Kunden auch für die betroffenen Freigaben eine Wiederherstellung einer fehlerfreien Sicherung in einer neuen Freigabe ausführen. |
| 2 |iSCSI |In der vorherigen Version wurde ein Problem festgestellt, bei dem Volumes verschwanden, wenn Daten auf ein Volume des StorSimple Virtual Arrays kopiert wurden. Dieses Problem wurde in dieser Version behoben. <br></br> Die Korrekturen gelten nur für neu erstellte Volumes. Sie gelten nicht rückwirkend für Volumes, bei denen dieses Problem bereits aufgetreten ist. Kunden sollten die betroffenen Datenträger über das klassische Azure-Portal online schalten, eine Sicherung für diese Volumes durchführen und diese Volumes dann auf neuen Volumes wiederherstellen. |

## <a name="known-issues-in-the-update-03"></a>Bekannte Probleme in Update 0.3
Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme für StorSimple Virtual Array sowie die Probleme aus früheren Versionsinformationen. 

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
| **1.** |Aktualisierungen |In der Vorabversion erstellte virtuellen Geräte können nicht auf eine unterstützte Version für allgemeine Verfügbarkeit aktualisiert werden. |Für diese virtuellen Geräte muss mithilfe eines Notfallwiederherstellungs-Workflows ein Failover für die allgemein verfügbare Version ausgeführt werden. |
| **2.** |Bereitgestellter Datenträger |Nachdem Sie einen Datenträger mit einer bestimmten angegebenen Größe bereitgestellt und das entsprechende virtuelle StorSimple-Gerät erstellt haben, darf der Datenträger nicht erweitert oder verkleinert werden. Ein entsprechender Versuch führt zum Verlust aller Daten auf den lokalen Ebenen des Geräts. | |
| **3.** |Gruppenrichtlinie |Wenn ein Gerät Mitglied einer Domäne ist, kann das Anwenden einer Gruppenrichtlinie den Gerätebetrieb beeinträchtigen. |Stellen Sie sicher, dass sich Ihr virtuelles Array in einer eigenen Organisationseinheit (OU) für Active Directory befindet und keine Gruppenrichtlinienobjekte (GPO) darauf angewendet werden. |
| **4.** |Lokale Web-UI |Wenn in Internet Explorer (IE ESC) erweiterten Sicherheitsfeatures aktiviert sind, funktionieren einige lokale Web-UI-Seiten wie "Problembehandlung" oder "Wartung" möglicherweise nicht ordnungsgemäß. Außerdem funktionieren Schaltflächen auf diesen Seiten möglicherweise nicht. |Deaktivieren Sie erweiterte Sicherheitsfeatures in Internet Explorer. |
| **5.** |Lokale Web-UI |Auf einer virtuellen Hyper-V-Maschine werden die Netzwerkschnittstellen in der Web-UI als 10-Gbit/s-Schnittstellen angezeigt. |Dieses Verhalten ist eine Spiegelung von Hyper-V. Hyper-V zeigt immer 10 Gbit/s für virtuelle Netzwerkadapter an. |
| **6.** |Mehrstufige Volumes oder Freigaben |Bytebereichssperren für Anwendungen, die mit den mehrstufigen StorSimple-Volumes arbeiten, werden nicht unterstützt. Wenn Bytebereichssperren aktiviert sind, funktioniert die StorSimple-Staffelung nicht. |Empfohlene Maßnahmen: <br></br>Deaktivieren Sie Bytebereichsperren in der Anwendungslogik.<br></br>Platzieren Sie die Daten für diese Anwendung nicht in mehrstufigen Volumes, sondern in lokalen Volumes.<br></br>*Nachteil*: Wenn lokale Volumes verwendet werden und Bytebereichssperren aktiviert sind, beachten Sie, dass das lokale Volume online sein kann, bevor die Wiederherstellung abgeschlossen ist. Wenn in solchen Fällen eine Wiederherstellung ausgeführt wird, müssen Sie auf den Abschluss des Wiederherstellungsvorgangs warten. |
| **7.** |Mehrstufige Freigaben |Das Arbeiten mit großen Dateien kann zu einer langsamen Abstufung führen. |Bei der Arbeit mit großen Dateien sollte die größte Datei nach Möglichkeit kleiner als 3 % der Größe der Dateifreigabe sein. |
| **8.** |Für Freigaben genutzte Kapazität |Möglicherweise wird eine Nutzung durch die Freigabe angezeigt, wenn diese keine Daten enthält. Dies liegt daran, dass die für Freigaben genutzte Kapazität Metadaten umfasst. | |
| **9.** |Notfallwiederherstellung |Sie können die Notfallwiederherstellung eines Dateiservers nur in der Domäne des Quellgeräts ausführen. Die Notfallwiederherstellung auf einem Zielgerät in einer anderen Domäne wird in dieser Version nicht unterstützt. |Dies wird in einer späteren Version implementiert. |
| **10.** |Azure PowerShell |Die virtuellen StorSimple-Geräte können in dieser Version nicht mithilfe von Azure PowerShell verwaltet werden. |Die gesamte Verwaltung der virtuellen Geräte sollte über das klassische Azure-Portal und die lokale Web-UI erfolgen. |
| **11.** |Kennwortänderung |Die Virtual Array-Gerätekonsole akzeptiert Eingaben nur über eine En-US-Tastatur. | |
| **12.** |CHAP |Einmal erstellte CHAP-Anmeldeinformationen können nicht wieder entfernt werden. Wenn Sie die CHAP-Anmeldeinformationen ändern, müssen Sie außerdem die Volumes offline und erneut online schalten, damit die Änderungen wirksam werden. |Dieses Problem wird in einer künftigen Version behoben. |
| **13.** |iSCSI-Server |Der für ein iSCSI-Volume „verwendete Speicher“ kann im StorSimple Manager-Dienst und im iSCSI-Host unterschiedlich angezeigt werden. |Der iSCSI-Host verwendet die Dateisystemansicht.<br></br>Dem Gerät werden die Blöcke angezeigt, die dem Volume bei maximaler Größe zugeordnet waren. |
| **14.** |Dateiserver |Wenn einer Datei in einem Ordner ein alternativer Datenstrom (Alternate Data Stream, ADS) zugeordnet ist, wird dieser nicht gesichert oder mittels Notfallwiederherstellung, Klonen oder Wiederherstellung auf Elementebene wiederhergestellt. | |

## <a name="next-step"></a>Nächster Schritt
[Installieren Sie Update 0.3](./storsimple-virtual-array-install-update-06.md) für Ihr StorSimple Virtual Array.

## <a name="references"></a>References
Suchen Sie nach älteren Versionsanmerkungen? Wechseln Sie zu: 

* [Versionsanmerkungen zu Update 0.1 und Update 0.2 für das StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array – Version mit allgemeiner Verfügbarkeit – Versionsanmerkungen](./storsimple-virtual-array-update-06-release-notes.md)