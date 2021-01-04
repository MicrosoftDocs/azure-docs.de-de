---
title: Versionsanmerkungen zu Update 1.0 für StorSimple Virtual Array
description: Beschreibt wichtige offene Probleme und Lösungen für StorSimple Virtual Array mit Update 1.0.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 2ec88e4e97d45b27c0226198491b3adec6448496
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000894"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Versionsanmerkungen zu Update 1.0 für StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres StorSimple Virtual Arrays die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 1.0 entspricht der Softwareversion **10.0.10296.0**.

> [!IMPORTANT]
> - Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen. Ausführliche Anleitungen zum Anwenden des Updates finden Sie unter [Installieren von Update 1.0](storsimple-virtual-array-install-update-1.md).
>
> - Update 1 ist nur über das Azure-Portal verfügbar, wenn auf Ihrem Gerät Update 0.6 ausgeführt wird.

## <a name="whats-new-in-update-10"></a>Neuerungen in Update 1.0

**Update 1.0 enthält Änderungen für die Authentifizierung des StorSimple-Geräte-Manager-Diensts und muss zeitnah bereitgestellt werden.** Dieses Update enthält folgende Verbesserungen und Fehlerbehebungen:

 - **Verwenden von Azure Active Directory (AAD) für die Authentifizierung beim StorSimple-Geräte-Manager-Dienst:** Ab Update 1.0 wird für die Authentifizierung beim StorSimple-Geräte-Manager-Dienst Azure Active Directory verwendet. Der alte Authentifizierungsmechanismus wird ab Dezember 2017 als veraltet markiert. Alle Benutzer müssen die neuen Authentifizierungs-URLs in ihre Firewallregeln aufnehmen. Weitere Informationen finden Sie unter den Authentifizierungs-URLs, die in den [Netzwerkanforderungen für StorSimple Virtual Array](storsimple-ova-system-requirements.md) aufgeführt sind.
 
    Wenn die Authentifizierungs-URL nicht in den Firewallregeln enthalten ist, wird Benutzern eine kritische Warnung darüber angezeigt, dass ihr StorSimple-Gerät sich nicht beim Dienst authentifizieren konnte. Wenn die Benutzer diese Warnung erhalten, müssen sie die neue Authentifizierungs-URL aufnehmen. Weitere Informationen finden Sie unter [StorSimple-Netzwerkwarnungen](storsimple-virtual-array-manage-alerts.md).

 - **Verbesserte Leistung:** Zur Verbesserung der Geschwindigkeit von Cloudlesevorgängen sowie von ein- und ausgehenden Tieringvorgängen wurden mehrere Fehler behoben. Dadurch hat sich die Leistung beim Sichern und Wiederherstellen für iSCSI- und Dateiservergeräte verbessert.

 - **Verbesserte Garbage Collection:** Diese Version enthält Fehlerbehebungen, die die Leistung des Garbage Collection-Zyklus verbessern, wenn sich das Geräte- und das Speicherkonto in zwei entfernten Regionen befinden.

 - **Verbesserte Protokollierung:** Diese Version enthält Verbesserungen für die Protokollierung im Zusammenhang mit Garbage Collection und E/A-Pfad.


## <a name="issues-fixed-in-update-10"></a>Behobene Probleme in Update 1.0

Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in dieser Version behoben wurden:

| Nein. | Funktion | Problem |
| --- | --- | --- |
| 1 |AAD-basierte Authentifizierung| Diese Version enthält Änderungen, die die AAD-Authentifizierung beim StorSimple-Geräte-Manager ermöglichen.|
| 2 |Garbage Collection| Dieses Problem trat an einem Kundenstandort auf, bei dem sich die Geräte- und Speicherkonten in unterschiedlichen Regionen befinden. Der Kunde hat von vorübergehenden Netzwerkfehlern und einer dadurch bedingten Beeinträchtigung der Abrechnung berichtet. Das Problem wurde in dieser Version behoben. |
| 3 |Leistung| Diese Version enthält Änderungen zur Verbesserung der Leistung von Wiederherstellungs- und Cloudlesevorgängen sowie von Tier-In- und Tier-Out-Vorgängen.|
| 4 |Aktualisieren| Die vorherige Version enthielt ein Aktualisierungsproblem, das am Kundenstandort zu Sicherungsfehlern führte. Dieses Problem wurde in dieser Version behoben.|

## <a name="known-issues-in-update-10"></a>Bekannte Probleme in Update 1.0

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
[Installieren Sie Update 1.0](storsimple-virtual-array-install-update-1.md) für Ihr StorSimple Virtual Array.

## <a name="references"></a>References
Suchen Sie nach älteren Versionsanmerkungen? Wechseln Sie zu:
*  [Versionsanmerkungen zu Update 0.6 für StorSimple Virtual Array](storsimple-virtual-array-update-06-release-notes.md)
* [Versionsanmerkungen zu Update 0.5 für StorSimple Virtual Array](storsimple-virtual-array-update-05-release-notes.md)
* [Versionsanmerkungen zu Update 0.4 für StorSimple Virtual Array](storsimple-virtual-array-update-04-release-notes.md)
* [Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Versionsanmerkungen zu Update 0.1 und Update 0.2 für das StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array – Version mit allgemeiner Verfügbarkeit – Versionsanmerkungen](./storsimple-virtual-array-update-06-release-notes.md)