---
title: Versionshinweise zu Update 0.4 für StorSimple Virtual Array | Microsoft-Dokumentation
description: Beschreibt wichtige offene Probleme und Lösungen für StorSimple Virtual Array mit Update 0.4.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 392e930dcb69392b6f9425abba0efa516d5c82b8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967056"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Versionshinweise zu Update 0.4 für StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres StorSimple Virtual Arrays die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 0.4 entspricht Softwareversion **10.0.10289.0**.

> [!NOTE]
> Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen.


## <a name="whats-new-in-the-update-04"></a>Neuerungen in Update 0.4
Update 0.4 ist in erster Linie ein Fehlerbehebungs-Build in Verbindung mit ein paar Verbesserungen. In dieser Version wurden mehrere Fehler behoben, die in der vorherigen Version zu Fehlern bei Sicherungen geführt haben. Die wichtigsten Verbesserungen und Fehlerbehebungen:

- **Verbesserungen der Backupleistung:** Für diese Version wurden mehrere wichtige Verbesserungen an der Backupleistung vorgenommen. Dadurch wurde für vollständige und inkrementelle Backups, die eine große Anzahl von Dateien betreffen, eine erhebliche Zeiteinsparung erzielt.

- **Verbesserte Wiederherstellungsleistung:** Diese Version enthält Verbesserungen, die die Wiederherstellungsleistung bei Verwendung vieler Dateien erheblich steigern. Bei Verwendung von zwei bis vier Millionen Dateien wird die Bereitstellung eines virtuellen Arrays mit 16 GB RAM empfohlen, um die Verbesserungen zu sehen. Bei Verwendung von weniger als 2 Millionen Dateien beträgt die Mindestanforderung für den virtuellen Computer weiterhin 8 GB RAM.

- **Verbesserungen am Supportpaket:** Die Verbesserungen umfassen die Protokollierung der Statistiken von Datenträger, CPU, Arbeitsspeicher, Netzwerk und Cloud im Supportpaket und dadurch eine Verbesserung des Prozesses zum Diagnostizieren/Debuggen von Geräteproblemen.

- **Begrenzen lokaler iSCSI-Volumes auf 200 GB:** Für lokale Volumes empfehlen wir die Begrenzung auf ein iSCSI-Volume mit 200 GB in Ihrem StorSimple Virtual Array. Die lokale Reservierung bei mehrstufigen Volumes beträgt weiterhin 10 Prozent der Größe des bereitgestellten Volumes, höchstens allerdings 200 GB. 

- **Fehlerbehebungen für Backups:** In früheren Versionen der Software bestanden Probleme im Zusammenhang mit Backups, die zu Backupfehlern führten. Diese Probleme wurden in dieser Version behoben.


## <a name="issues-fixed-in-the-update-04"></a>In Update 0.4 behobene Probleme

Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in dieser Version behoben wurden:

| Nein. | Funktion | Problem |
| --- | --- | --- |
| 1 |Backupleistung|Bei früheren Versionen dauerten Backups mit einer großen Anzahl von Dateien sehr lange (einige Tage). In dieser Version wurde die Dauer für vollständige und inkrementelle Backups erheblich reduziert. |
| 2 |Supportpaket|Statistiken für Datenträger, CPU, Arbeitsspeicher, Netzwerk und Cloud werden jetzt im Supportprotokoll erfasst. Dadurch wird die Effizienz der Supportpakete bei der Behandlung von Geräteproblemen gesteigert.|
| 3 |Backup |In früheren Versionen konnten lange dauernde Backups zu Platzproblemen auf dem Gerät und letztendlich zu Backupfehlern führen. Dieses Problem wird in dieser Version behoben, da in der Warteschlange nicht mehr als fünf Backups gleichzeitig zugelassen werden.|
| 4 |iSCSI | In früheren Versionen betrug die lokale Reservierung für mehrstufige oder lokale Volumes 10 Prozent der Größe des bereitgestellten Volumes. In dieser Version ist die lokale Reservierung für alle iSCSI-Volumes (lokal oder mehrstufig) auf 10 Prozent und höchstens 200 GB (bei mehrstufigen Volumes mit mehr als 2 TB) begrenzt. Dadurch ist mehr Speicherplatz auf der lokalen Festplatte frei. Wir empfehlen eine Begrenzung der lokalen Volumes in dieser Version auf 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Bekannte Probleme in Update 0.4

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
| **15.** |Dateiserver |Symbolische Verknüpfungen werden nicht unterstützt. | |
| **16.** |Dateiserver |Mit dem verschlüsselnden Dateisystem von Windows (Encrypting File System, EFS) verschlüsselte Dateien bewirken beim Kopieren auf den oder Speichern auf dem StorSimple Virtual Array-Dateiserver eine nicht unterstützte Konfiguration.  | |

## <a name="next-step"></a>Nächster Schritt
[Installieren Sie Update 0.4](storsimple-virtual-array-install-update-04.md) für Ihr StorSimple Virtual Array.

## <a name="references"></a>References
Suchen Sie nach älteren Versionsanmerkungen? Wechseln Sie zu: 

* [Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Versionsanmerkungen zu Update 0.1 und Update 0.2 für das StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array – Version mit allgemeiner Verfügbarkeit – Versionsanmerkungen](./storsimple-virtual-array-update-06-release-notes.md)