---
title: Versionsanmerkungen zu Update 0.2 und Update 0.1 für das StorSimple Virtual Array
description: Beschreibt wichtige offene Probleme und Lösungen für das StorSimple Virtual Array mit Update 0.2 und 0.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 67d3fafb9b4b93ae52bdb09d673bf65cad30b816
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966155"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Versionsanmerkungen zu Update 0.2 und Update 0.1 für das StorSimple Virtual Array
## <a name="overview"></a>Übersicht
Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays. (Microsoft Azure StorSimple Virtual Array wird auch als lokales virtuelles StorSimple-Gerät oder als virtuelles StorSimple-Gerät bezeichnet.) 

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres virtuellen StorSimple-Geräts die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 0.2 entspricht Softwareversion **10.0.10280.0**. Update 0.1 entspricht Version **10.0.10279.0**. In den folgenden Abschnitten werden die Änderungen jedes Updates aufgelistet. 

> [!NOTE]
> Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>In Update 0.2 behobene Probleme
Update 0.2 enthält alle Änderungen aus Update 0.1 sowie zusätzlich den in der folgenden Tabelle beschriebenen Fix:

| Funktion | Problem |
| --- | --- |
| Aktualisierungen |In der letzten Version wurden Updates im klassischen Azure-Portal nicht automatisch erkannt, und Sie mussten die lokale Webbenutzeroberfläche verwenden, um Updates zu installieren. Dieses Problem wurde in dieser Version behoben. Nach der Installation von Update 0.2 können Sie zukünftige Updates im klassischen Azure-Portal installieren. |

## <a name="whats-new-in-the-update-01"></a>Neuerungen in Update 0.1
Update 0.1 enthält folgende Fehlerbehebungen und Verbesserungen: 

* **Verbesserte Resilienz bei Cloudausfällen:** Diese Version enthält mehrere Fehlerbehebungen für Notfallwiederherstellung, Sicherung, Wiederherstellung und Tiering im Fall einer Unterbrechung der Cloudverbindung. 
* **Verbesserte Wiederherstellungsleistung:** Diese Version enthält Fehlerbehebungen, die die Ausführungsdauer der Wiederherstellungsaufträge erheblich verringern.
* **Optimierte automatisierte Speicherplatzrückgewinnung:** Wenn Daten auf Volumes mit schlanker Speicherzuweisung gelöscht werden, müssen die nicht verwendeten Speicherblöcke wieder freigegeben werden. In dieser Version wurde der Prozess zur Speicherplatzrückgewinnung in der Cloud verbessert, was dazu führt, dass nicht genutzter Speicherplatz im Vergleich mit den vorherigen Versionen schneller wieder verfügbar ist.
* **Neue Images für virtuelle Datenträger:** Im klassischen Azure-Portal sind neue VHD-, VHDX- und VMDK-Dateien verfügbar. Diese Images können zur Bereitstellung neuer Update 0.1-Geräte heruntergeladen werden.
* **Verbesserte Genauigkeit des Auftragsstatus im Portal:** In der früheren Version der Software wurde der Auftragsstatus im Portal nicht genau genug angegeben. Dieses Problem wurde in dieser Version behoben.
* **Domänenbeitritt:** Fehlerbehebungen im Zusammenhang mit Domänenbeitritt und Umbenennung des Geräts.

## <a name="issues-fixed-in-the-update-01"></a>In Update 0.1 behobene Probleme
Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in dieser Version behoben wurden:

| Nein. | Funktion | Problem |
| --- | --- | --- |
| 1 |VMDK |In bestimmten VMware-Versionen wurde der Betriebssystemdatenträger als spärlich betrachtet, was Warnungen sowie die Störung des regulären Betriebs zur Folge hatte. Dies wurde in dieser Version behoben. |
| 2 |iSCSI-Server |In der letzten Version musste der Benutzer für jede aktivierte Netzwerkschnittstelle des virtuellen StorSimple-Geräts ein Gateway angeben. Dieses Verhalten wurde in dieser Version geändert, sodass der Benutzer nun mindestens ein Gateway für alle aktivierten Netzwerkschnittstellen konfigurieren muss. |
| 3 |Supportpaket |In der früheren Version der Software trat bei der Supportpaketsammlung ein Fehler auf, wenn die Pakete größer als 1 GB waren. Dieses Problem wurde in dieser Version behoben. |
| 4 |Cloudzugriff |In der letzten Version lag folgendes Problem vor: Wenn StorSimple Virtual Array über keine Netzwerkkonnektivität verfügte und neu gestartet wurde, traten auf der lokalen Benutzeroberfläche Verbindungsprobleme auf. Dieses Problem wurde in dieser Version behoben. |
| 5 |Überwachungsdiagramme |In der vorherigen Version enthielten Cloudkapazitätsauslastungs-Diagramme im klassischen Azure-Portal nach einem Gerätefailover falsche Werte. Dies wurde in der aktuellen Version behoben. |

## <a name="known-issues-in-the-update-01"></a>Bekannte Probleme in Update 0.1
Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme für StorSimple Virtual Array sowie die Probleme aus früheren Versionsinformationen. **Die Probleme aus den aktuellen Versionshinweisen sind mit einem Sternchen gekennzeichnet. Nahezu alle Probleme in dieser Liste stammen aus der GA-Version von StorSimple Virtual Array.**

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
| **1.** |Aktualisierungen |In der Vorabversion erstellte virtuellen Geräte können nicht auf eine unterstützte Version für allgemeine Verfügbarkeit aktualisiert werden. |Für diese virtuellen Geräte muss mithilfe eines Notfallwiederherstellungs-Workflows ein Failover für die allgemein verfügbare Version ausgeführt werden. |
| **2.** |Bereitgestellter Datenträger |Nachdem Sie einen Datenträger mit einer bestimmten angegebenen Größe bereitgestellt und das entsprechende virtuelle StorSimple-Gerät erstellt haben, darf der Datenträger nicht erweitert oder verkleinert werden. Ein entsprechender Versuch führt zum Verlust aller Daten auf den lokalen Ebenen des Geräts. | |
| **3.** |Gruppenrichtlinie |Wenn ein Gerät Mitglied einer Domäne ist, kann das Anwenden einer Gruppenrichtlinie den Gerätebetrieb beeinträchtigen. |Stellen Sie sicher, dass sich Ihr virtuelles Array in einer eigenen Organisationseinheit (OU) für Active Directory befindet und keine Gruppenrichtlinienobjekte (GPO) darauf angewendet werden. |
| **4.** |Lokale Web-UI |Wenn in Internet Explorer (IE ESC) erweiterten Sicherheitsfeatures aktiviert sind, funktionieren einige lokale Web-UI-Seiten wie "Problembehandlung" oder "Wartung" möglicherweise nicht ordnungsgemäß. Außerdem funktionieren Schaltflächen auf diesen Seiten möglicherweise nicht. |Deaktivieren Sie erweiterte Sicherheitsfeatures in Internet Explorer. |
| **5.** |Lokale Web-UI |Auf einer virtuellen Hyper-V-Maschine werden die Netzwerkschnittstellen in der Web-UI als 10-Gbit/s-Schnittstellen angezeigt. |Dieses Verhalten ist eine Spiegelung von Hyper-V. Hyper-V zeigt immer 10 Gbit/s für virtuelle Netzwerkadapter an. |
| **6.** |Mehrstufige Volumes oder Freigaben |Bytebereichssperren für Anwendungen, die mit den mehrstufigen StorSimple-Volumes arbeiten, werden nicht unterstützt. Wenn Bytebereichsperren aktiviert sind, funktioniert die StorSimple-Staffelung nicht. |Empfohlene Maßnahmen: <br></br>Deaktivieren Sie Bytebereichsperren in der Anwendungslogik.<br></br>Platzieren Sie die Daten für diese Anwendung nicht in mehrstufigen Volumes, sondern in lokalen Volumes.<br></br>*Nachteil*: Wenn lokale Volumes verwendet werden und Bytebereichssperren aktiviert sind, beachten Sie, dass das lokale Volume online sein kann, bevor die Wiederherstellung abgeschlossen ist. Wenn in solchen Fällen eine Wiederherstellung ausgeführt wird, müssen Sie auf den Abschluss des Wiederherstellungsvorgangs warten. |
| **7.** |Mehrstufige Freigaben |Das Arbeiten mit großen Dateien kann zu einer langsamen Abstufung führen. |Bei der Arbeit mit großen Dateien sollte die größte Datei nach Möglichkeit kleiner als 3 % der Größe der Dateifreigabe sein. |
| **8.** |Für Freigaben genutzte Kapazität |Möglicherweise wird eine Nutzung durch die Freigabe angezeigt, auch wenn diese keine Daten enthält. Dies liegt daran, dass die für Freigaben genutzte Kapazität Metadaten umfasst. | |
| **9.** |Notfallwiederherstellung |Sie können die Notfallwiederherstellung eines Dateiservers nur in der Domäne des Quellgeräts ausführen. Die Notfallwiederherstellung auf einem Zielgerät in einer anderen Domäne wird in dieser Version nicht unterstützt. |Dies wird in einer späteren Version implementiert werden. |
| **10.** |Azure PowerShell |Die virtuellen StorSimple-Geräte können in dieser Version nicht mithilfe von Azure PowerShell verwaltet werden. |Die gesamte Verwaltung der virtuellen Geräte sollte über das klassische Azure-Portal und die lokale Web-UI erfolgen. |
| **11.** |Kennwortänderung |Die Virtual Array-Gerätekonsole akzeptiert Eingaben nur über eine En-US-Tastatur. | |
| **12.** |CHAP |Einmal erstellte CHAP-Anmeldeinformationen können nicht wieder entfernt werden. Wenn Sie die CHAP-Anmeldeinformationen ändern, müssen Sie außerdem die Volumes offline und erneut online schalten, damit die Änderungen wirksam werden. |Dies wird in einer zukünftigen Version behoben. |
| **13.** |iSCSI-Server |Der für ein iSCSI-Volume „verwendete Speicher“ kann im StorSimple Manager-Dienst und im iSCSI-Host unterschiedlich angezeigt werden. |Der iSCSI-Host umfasst die Dateisystemansicht.<br></br>Dem Gerät werden die Blöcke angezeigt, die dem Volume bei maximaler Größe zugeordnet waren. |
| **14.** |Dateiserver* |Wenn einer Datei in einem Ordner ein alternativer Datenstrom (Alternate Data Stream, ADS) zugeordnet ist, wird dieser nicht gesichert oder mittels Notfallwiederherstellung, Klonen oder Wiederherstellung auf Elementebene wiederhergestellt. | |

## <a name="next-step"></a>Nächster Schritt
[Installieren Sie Updates](./storsimple-virtual-array-install-update-06.md) auf Ihrem StorSimple Virtual Array.