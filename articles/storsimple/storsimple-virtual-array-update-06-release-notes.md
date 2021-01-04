---
title: Versionsanmerkungen zu Update 0.6 für StorSimple Virtual Array | Microsoft-Dokumentation
description: Beschreibt wichtige offene Probleme und Lösungen für StorSimple Virtual Array mit Update 0.6.
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
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: 24bddb47a32a7b8ad4ea659d52c2d5b2730be9ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000911"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>Versionsanmerkungen zu Update 0.6 für StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres StorSimple Virtual Arrays die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 0.6 entspricht Softwareversion **10.0.10293.0**.

> [!IMPORTANT]
> - Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen. Ausführliche Anleitungen zum Anwenden des Updates finden Sie unter [Installieren von Update 0.6](storsimple-virtual-array-install-update-06.md).
>
> - Es wird dringend empfohlen, das Update 0.6 sofort zu installieren, da es wichtige Sicherheitsfixes enthält.


## <a name="whats-new-in-the-update-06"></a>Neuerungen in Update 0.6
Update 0.6 ist ein wichtiges Update und sollte sofort bereitgestellt werden. Dieses Update enthält die folgenden Fixes: 

- **Windows-Sicherheitsfixes:** Dieses Release enthält **wichtige Windows-Sicherheitsfixes**. In den folgenden Sicherheitsupdates erhalten Sie weitere Informationen zu Sicherheitsproblemen und den zugehörigen Fixes:
    - [Dezember 2016: Qualitätsupdate zu Sicherheitszwecken für Windows 8.1 und Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [März 2017: Qualitätsupdate zu Sicherheitszwecken für Windows 8.1 und Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9. Mai 2017: KB4019213 (nur Sicherheitsupdate)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Wiederherstellungsfix:** In früheren Versionen gab es einen Fehler, der den Abschluss der Wiederherstellung verhindert hat. Dieser Fehler wurde in dieser Version behoben.


## <a name="issues-fixed-in-the-update-06"></a>In Update 0.6 behobene Probleme

Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in dieser Version behoben wurden:

| Nein. | Funktion | Problem |
| --- | --- | --- |
| 1 |Sicherheit| Diese Version enthält wichtige Windows-Sicherheitsupdates. Es wird empfohlen, dieses Update sofort zu installieren.|
| 2 |Restore| Während einer Wiederherstellung trat eine Racebedingung ein, die verhinderte, dass der Wiederherstellungsauftrag abgeschlossen wurde. Die Fehlerkorrektur behandelt diese Racebedingung.|


## <a name="known-issues-in-the-update-06"></a>Bekannte Probleme in Update 0.6

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
| **8.** |Für Freigaben genutzte Kapazität |Möglicherweise wird eine Nutzung durch die Freigabe angezeigt, wenn diese keine Daten enthält. Diese Nutzung liegt daran, dass die für Freigaben genutzte Kapazität Metadaten umfasst. | |
| **9.** |Notfallwiederherstellung |Sie können die Notfallwiederherstellung eines Dateiservers nur in der Domäne des Quellgeräts ausführen. Die Notfallwiederherstellung auf einem Zielgerät in einer anderen Domäne wird in dieser Version nicht unterstützt. |Dies wird in einer späteren Version implementiert. Weitere Informationen finden Sie unter [Failover und Notfallwiederherstellung für StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md). |
| **10.** |Azure PowerShell |Die virtuellen StorSimple-Geräte können in dieser Version nicht mithilfe von Azure PowerShell verwaltet werden. |Die gesamte Verwaltung der virtuellen Geräte sollte über das Azure-Portal und die lokale Web-UI erfolgen. |
| **11.** |Kennwortänderung |Die Virtual Array-Gerätekonsole akzeptiert Eingaben nur über eine en-us-Tastatur. | |
| **12.** |CHAP |Einmal erstellte CHAP-Anmeldeinformationen können nicht wieder entfernt werden. Wenn Sie die CHAP-Anmeldeinformationen ändern, müssen Sie außerdem die Volumes offline und erneut online schalten, damit die Änderungen wirksam werden. |Dieses Problem wird in einer künftigen Version behoben. |
| **13.** |iSCSI-Server |Der für ein iSCSI-Volume „verwendete Speicher“ kann im StorSimple-Geräte-Manager-Dienst und im iSCSI-Host unterschiedlich angezeigt werden. |Der iSCSI-Host verwendet die Dateisystemansicht.<br></br>Dem Gerät werden die Blöcke angezeigt, die dem Volume bei maximaler Größe zugeordnet waren. |
| **14.** |Dateiserver |Wenn einer Datei in einem Ordner ein alternativer Datenstrom (Alternate Data Stream, ADS) zugeordnet ist, wird dieser nicht gesichert oder mittels Notfallwiederherstellung, Klonen oder Wiederherstellung auf Elementebene wiederhergestellt. | |
| **15.** |Dateiserver |Symbolische Verknüpfungen werden nicht unterstützt. | |
| **16.** |Dateiserver |Mit dem verschlüsselnden Dateisystem von Windows (Encrypting File System, EFS) verschlüsselte Dateien bewirken beim Kopieren auf den oder Speichern auf dem StorSimple Virtual Array-Dateiserver eine nicht unterstützte Konfiguration.  | |
| **17.** |Aktualisierungen |Wenn beim Installieren eines Hotfixes über die lokale Benutzeroberfläche der Fehlercode 2359302 (hex. 0x240006) angezeigt wird, zeigt dies an, dass der Hotfix bereits auf dem Gerät installiert ist.   | |

## <a name="next-step"></a>Nächster Schritt
[Installieren Sie Update 0.6](storsimple-virtual-array-install-update-06.md) in StorSimple Virtual Array.

## <a name="references"></a>References
Suchen Sie nach älteren Versionsanmerkungen? Wechseln Sie zu:

* [Versionsanmerkungen zu Update 0.5 für StorSimple Virtual Array](storsimple-virtual-array-update-05-release-notes.md)
* [Versionsanmerkungen zu Update 0.4 für StorSimple Virtual Array](storsimple-virtual-array-update-04-release-notes.md)
* [Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Versionsanmerkungen zu Update 0.1 und Update 0.2 für das StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array – Version mit allgemeiner Verfügbarkeit – Versionsanmerkungen]()