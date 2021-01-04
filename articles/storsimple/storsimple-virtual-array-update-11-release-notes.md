---
title: Versionsanmerkungen zu Update 1.1 für StorSimple Virtual Array | Microsoft-Dokumentation
description: Dieser Artikel beschreibt wichtige offene Probleme und Lösungen für StorSimple Virtual Array mit Update 1.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: df4722a4232686b0d981c190bdc154f0d1bec62e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960203"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>Versionsanmerkungen zu Update 1.1 für StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres StorSimple Virtual Arrays die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 1.1 entspricht der Softwareversion **10.0.10307.0**.

> [!IMPORTANT]
> - Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen. Ausführliche Anleitungen zum Anwenden des Updates finden Sie unter [Installieren von Update 1.1](storsimple-virtual-array-install-update-11.md).
>
> - Update 1.1 ist über das Azure-Portal nur verfügbar, wenn auf Ihrem Gerät Update 1.0 ausgeführt wird.

## <a name="whats-new-in-update-11"></a>Neuerungen in Update 1.1

Dieses Update enthält folgende Verbesserungen und Fehlerbehebungen:

 - **Sicherungsfehler** wurden durch Erhöhen der Resilienz gegenüber Cloudausfällen und hoher CPU-Auslastung verringert.
 - **Protokollierung**: Die Protokollierung im ausführlichen Modus, wenn ein Gerät sich in einer Supportsitzung befindet, wurde geändert.


## <a name="issues-fixed-in-update-11"></a>Behobene Probleme in Update 1.1

Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in dieser Version behoben wurden:

| Nein. | Funktion | Problem |
| --- | --- | --- |
| 1 |Backups| Dieses Release enthält Änderungen, die die Resilienz gegenüber Cloudausfällen und hoher CPU-Auslastung erhöhen und so Sicherungsfehler reduzieren.|
| 2 |Protokollierung| Dieses Release enthält Änderungen an der Protokollierung im ausführlichen Modus, während ein Gerät sich in einer Supportsitzung befindet.|


## <a name="known-issues-in-update-11"></a>Bekannte Probleme in Update 1.1

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme für StorSimple Virtual Array sowie die Probleme aus früheren Versionsinformationen.

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
| **1.** |Aktualisierungen |In der Vorschauversion erstellte virtuelle Arrays können nicht auf eine unterstützte allgemein verfügbare Version werden. |Für diese virtuellen Arrays muss mithilfe eines Notfallwiederherstellungsworkflows ein Failover für die allgemein verfügbare Version ausgeführt werden. |
| **2.** |Bereitgestellter Datenträger |Nachdem Sie einen Datenträger mit einer bestimmten angegebenen Größe bereitgestellt und das entsprechende StorSimple Virtual Array erstellt haben, darf der Datenträger nicht erweitert oder verkleinert werden. Ein entsprechender Versuch führt zum Verlust aller Daten auf den lokalen Ebenen des Geräts. | |
| **3.** |Gruppenrichtlinie |Wenn ein Gerät Mitglied einer Domäne ist, kann das Anwenden einer Gruppenrichtlinie den Gerätebetrieb beeinträchtigen. |Stellen Sie sicher, dass sich Ihr virtuelles Array in einer eigenen Organisationseinheit (OU) für Active Directory befindet und keine Gruppenrichtlinienobjekte (GPO) darauf angewendet werden. |
| **4.** |Lokale Web-UI |Wenn in Internet Explorer (IE ESC) erweiterten Sicherheitsfeatures aktiviert sind, funktionieren einige lokale Web-UI-Seiten wie "Problembehandlung" oder "Wartung" möglicherweise nicht ordnungsgemäß. Außerdem funktionieren Schaltflächen auf diesen Seiten möglicherweise nicht. |Deaktivieren Sie erweiterte Sicherheitsfeatures in Internet Explorer. |
| **5.** |Lokale Web-UI |Auf einer virtuellen Hyper-V-Maschine werden die Netzwerkschnittstellen in der Web-UI als 10-Gbit/s-Schnittstellen angezeigt. |Dieses Verhalten ist eine Spiegelung von Hyper-V. Hyper-V zeigt immer 10 Gbit/s für virtuelle Netzwerkadapter an. |
| **6.** |Mehrstufige Volumes oder Freigaben |Bytebereichssperren für Anwendungen, die mit den mehrstufigen StorSimple-Volumes arbeiten, werden nicht unterstützt. Wenn Bytebereichssperren aktiviert sind, funktioniert die StorSimple-Staffelung nicht. |Empfohlene Maßnahmen: <br></br>Deaktivieren Sie Bytebereichsperren in der Anwendungslogik.<br></br>Platzieren Sie die Daten für diese Anwendung nicht in mehrstufigen Volumes, sondern in lokalen Volumes.<br></br>*Nachteil*: Wenn lokale Volumes verwendet werden und Bytebereichssperren aktiviert sind, beachten Sie, dass das lokale Volume online sein kann, bevor die Wiederherstellung abgeschlossen ist. Wenn in solchen Fällen eine Wiederherstellung ausgeführt wird, müssen Sie auf den Abschluss des Wiederherstellungsvorgangs warten. |
| **7.** |Mehrstufige Freigaben |Das Arbeiten mit großen Dateien kann zu einer langsamen Abstufung führen. |Bei der Arbeit mit großen Dateien sollte die größte Datei nach Möglichkeit kleiner als 3 % der Größe der Dateifreigabe sein. |
| **8.** |Für Freigaben genutzte Kapazität |Möglicherweise wird eine Nutzung durch die Freigabe angezeigt, wenn diese keine Daten enthält. Diese Nutzung liegt daran, dass die für Freigaben genutzte Kapazität Metadaten umfasst. | |
| **9.** |Notfallwiederherstellung |Sie können die Notfallwiederherstellung eines Dateiservers nur in der Domäne des Quellgeräts ausführen. Die Notfallwiederherstellung auf einem Zielgerät in einer anderen Domäne wird in dieser Version nicht unterstützt. |Dies wird in einer späteren Version implementiert. Weitere Informationen finden Sie unter [Failover und Notfallwiederherstellung für StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md). |
| **10.** |Azure PowerShell |Die StorSimple Virtual Arrays können in dieser Version nicht mithilfe von Azure PowerShell verwaltet werden. |Die gesamte Verwaltung der virtuellen Geräte sollte über das Azure-Portal und die lokale Web-UI erfolgen. |
| **11.** |Kennwortänderung |Die Virtual Array-Gerätekonsole akzeptiert Eingaben nur über eine en-us-Tastatur. | |
| **12.** |CHAP |Einmal erstellte CHAP-Anmeldeinformationen können nicht wieder entfernt werden. Wenn Sie die CHAP-Anmeldeinformationen ändern, müssen Sie außerdem die Volumes offline und erneut online schalten, damit die Änderungen wirksam werden. |Dieses Problem wird in einer künftigen Version behoben. |
| **13.** |iSCSI-Server |Der für ein iSCSI-Volume „verwendete Speicher“ kann im StorSimple-Geräte-Manager-Dienst und im iSCSI-Host unterschiedlich angezeigt werden. |Der iSCSI-Host verwendet die Dateisystemansicht.<br></br>Dem Gerät werden die Blöcke angezeigt, die dem Volume bei maximaler Größe zugeordnet waren. |
| **14.** |Dateiserver |Wenn einer Datei in einem Ordner ein alternativer Datenstrom (Alternate Data Stream, ADS) zugeordnet ist, wird dieser nicht gesichert oder mittels Notfallwiederherstellung, Klonen oder Wiederherstellung auf Elementebene wiederhergestellt. | |
| **15.** |Dateiserver |Symbolische Verknüpfungen werden nicht unterstützt. | |
| **16.** |Dateiserver |Mit dem verschlüsselnden Dateisystem von Windows (Encrypting File System, EFS) verschlüsselte Dateien bewirken beim Kopieren auf den oder Speichern auf dem StorSimple Virtual Array-Dateiserver eine nicht unterstützte Konfiguration.  | |
| **17.** |Aktualisierungen |Wenn beim Installieren eines Hotfixes über die lokale Benutzeroberfläche der Fehlercode 2359302 (hex. 0x240006) angezeigt wird, zeigt dies an, dass der Hotfix bereits auf dem Gerät installiert ist.   | |
| **18.** |Aktualisierungen |Wenn Sie Update 1 über die lokale Weboberfläche auf Ihrem virtuellen Array installieren möchten, vergewissern Sie sich, dass Update 0.6 ausgeführt wird. Andernfalls müssen Sie zuerst Update 0.6 installieren, um Update 1 anwenden zu können. Wenn Sie eine Version vor Update 0.6 verwenden und direkt Update 1.0 installieren, stehen einige Updates nicht zur Verfügung, und die Überwachungsdiagramme funktionieren nicht.   | |


## <a name="next-steps"></a>Nächste Schritte
[Installieren Sie Update 1.1](storsimple-virtual-array-install-update-11.md) für Ihr StorSimple Virtual Array.

## <a name="references"></a>References
Suchen Sie nach älteren Versionsanmerkungen? Wechseln Sie zu:
* [Versionsanmerkungen zu Update 1.0 für StorSimple Virtual Array](storsimple-virtual-array-update-1-release-notes.md)
* [Versionsanmerkungen zu Update 0.6 für StorSimple Virtual Array](storsimple-virtual-array-update-06-release-notes.md)
* [Versionsanmerkungen zu Update 0.5 für StorSimple Virtual Array](storsimple-virtual-array-update-05-release-notes.md)
* [Versionsanmerkungen zu Update 0.4 für StorSimple Virtual Array](storsimple-virtual-array-update-04-release-notes.md)
* [Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Versionsanmerkungen zu Update 0.1 und Update 0.2 für das StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array – Version mit allgemeiner Verfügbarkeit – Versionsanmerkungen](./storsimple-virtual-array-update-06-release-notes.md)