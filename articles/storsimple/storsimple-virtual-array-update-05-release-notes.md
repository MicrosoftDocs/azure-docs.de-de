---
title: Versionshinweise zu Update 0.5 für StorSimple Virtual Array | Microsoft-Dokumentation
description: Beschreibt wichtige offene Probleme und Lösungen für StorSimple Virtual Array mit Update 0.5.
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
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: f70cf96baed274eaf260832bd83ab0e2c91f3683
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967039"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>Versionsanmerkungen zu Update 0.5 für StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres StorSimple Virtual Arrays die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 0.5 entspricht Softwareversion **10.0.10290.0**.

> [!NOTE]
> Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen. Ausführliche Anleitungen zum Anwenden des Updates finden Sie unter [Installieren von Update 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Neuerungen in Update 0.5
Update 0.5 ist in erster Linie ein Build zur Fehlerkorrektur. Die wichtigsten Verbesserungen und Fehlerbehebungen:

- **Verbesserungen der Sicherungsresilienz**: Diese Version enthält Fixes, die die Sicherungsresilienz verbessern. In den früheren Versionen wurden Sicherungen nur für bestimmte Ausnahmen wiederholt. Diese Version führt Neuversuche für alle Ausnahmen aus und macht Sicherungen dadurch ausfallsicherer.

- **Updates für die Überwachung der Speichernutzung**: Ab dem 30. Juni 2017 wird die Überwachung der Speichernutzung für die virtuelle StorSimple-Geräteserie eingestellt. Dies gilt für die Überwachungsdiagramme für alle virtuellen Arrays mit Update 0.4 oder niedriger. Dieses Update enthält die erforderlichen Änderungen, um die Überwachung Speichernutzung im Azure-Portal weiterhin verwenden zu können. Installieren Sie dieses wichtige Update vor dem 30. Juni 2017, um das Überwachungsfeature weiterhin verwenden zu können.


## <a name="issues-fixed-in-the-update-05"></a>In Update 0.5 behobene Probleme

Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in dieser Version behoben wurden:

| Nein. | Funktion | Problem |
| --- | --- | --- |
| 1 |Sicherungsresilienz| In den früheren Versionen wurden Sicherungen nur für bestimmte Ausnahmen wiederholt. Diese Version enthält einen Fix, der Sicherungen ausfallsicherer macht, indem Sicherungen für alle Ausnahmen erneut versucht werden.|
| 2 |Überwachung| Die Überwachung der Speichernutzung für die virtuelle StorSimple-Geräteserie wird am 30. Juni 2017 eingestellt. Diese Aktion wirkt sich auf die Überwachungsdiagramme im StorSimple-Geräte-Manager-Dienst aus, der auf StorSimple Virtual Arrays (Modell 1200) ausgeführt wird. Diese Version enthält Updates, die dem Benutzer ermöglichen, die Speichernutzung für virtuelle Arrays auch nach dem 30. Juni 2017 weiterhin überwachen zu können.|
| 3 |Dateiserver| In früheren Versionen war es Benutzern möglich, verschlüsselte Dateien in den virtuellen Array kopieren. Diese Version enthält einen Fix, der das Kopieren verschlüsselter Dateien in den virtuellen Array unterbindet. Wenn auf Ihrem Gerät verschlüsselte Dateien vorhanden sind, die von vor dem Update stammen, schlagen Sicherungen weiterhin fehl, bis die verschlüsselten Dateien aus dem System gelöscht wurden. |


## <a name="known-issues-in-the-update-05"></a>Bekannte Probleme in Update 0.5

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

## <a name="next-step"></a>Nächster Schritt
[Installieren Sie Update 0.5](storsimple-virtual-array-install-update-05.md) für Ihr StorSimple Virtual Array.

## <a name="references"></a>References
Suchen Sie nach älteren Versionsanmerkungen? Wechseln Sie zu:

* [Versionsanmerkungen zu Update 0.4 für StorSimple Virtual Array](storsimple-virtual-array-update-04-release-notes.md)
* [Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Versionsanmerkungen zu Update 0.1 und Update 0.2 für das StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array – Version mit allgemeiner Verfügbarkeit – Versionsanmerkungen](./storsimple-virtual-array-update-06-release-notes.md)