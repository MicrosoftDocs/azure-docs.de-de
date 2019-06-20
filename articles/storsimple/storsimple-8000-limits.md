---
title: Systembegrenzungen der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt Systembegrenzungen und empfohlene Größen für StorSimple-Komponenten und Verbindungen der 8000 Serie.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0053f950b36351b06d08630cbf9977f53f2ed47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721693"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Welche Systemeinschränkungen gelten für die StorSimple 8000-Serie?

## <a name="overview"></a>Übersicht

StorSimple bietet skalierbaren und flexiblen Speicher für Ihr Rechenzentrum. Es gibt jedoch einige Einschränkungen, die Sie bei Planung, Bereitstellung und Betrieb Ihrer StorSimple-Lösung beachten sollten. Die folgende Tabelle enthält eine Beschreibung dieser Einschränkungen sowie einige Empfehlungen, damit Sie Ihre StorSimple-Lösung optimal nutzen können.

| Grenzwertbezeichner | Begrenzung | Kommentare |
| --- | --- | --- |
| Maximale Anzahl von Anmeldeinformationen für das Speicherkonto |64 | |
| Maximale Anzahl von Volumecontainern |64 | |
| Maximale Anzahl von Volumes |255 | |
| Maximale Anzahl von lokalen Volumes |32 | |
| Maximale Anzahl von Zeitplänen pro Bandbreitenvorlage |168 |Einen Zeitplan für jede Stunde, jeden Tag der Woche (24 * 7). |
| Maximale Größe eines mehrstufigen Volumes auf physischen Geräten |64 TB für 8100 und 8600 |8100 und 8600 sind physische Geräte. |
| Maximale Größe eines mehrstufigen Volumes auf virtuellen Geräten in Azure |30 TB für 8010 <br></br> 64 TB für 8020 |8010 und 8020 sind virtuelle Geräte in Azure, die Standardspeicher und Storage Premium verwenden. |
| Maximale Größe eines lokal fixierten Volumes auf physischen Geräten |8,5 TB für 8100 <br></br> 22,5 TB für 8600 |8100 und 8600 sind physische Geräte. |
| Maximale Anzahl von iSCSI-Verbindungen |512 | |
| Maximale Anzahl von iSCSI-Verbindungen von Initiatoren |512 | |
| Maximale Anzahl von Zugriffssteuerungsdatensätzen pro Gerät |64 | |
| Maximale Anzahl von Volumes pro Sicherungsrichtlinie |20 | |
| Maximale Anzahl von Sicherungen, die pro Zeitplan (in einer Sicherungsrichtlinie) beibehalten werden |64 | |
| Maximale Anzahl von Zeitplänen pro Sicherungsrichtlinie |10 | |
| Maximale Anzahl von Momentaufnahmen beliebigen Typs, die pro Volume beibehalten werden können |256 |Dies umfasst lokale Momentaufnahmen und Cloudmomentaufnahmen. |
| Maximale Anzahl von Momentaufnahmen, die in einem Gerät vorhanden sein können |10\.000 | |
| Maximale Anzahl von Volumes, die für Sicherung, Wiederherstellung oder Klonen parallel verarbeitet werden können |16 |<ul><li>Wenn mehr als 16 Volumes vorhanden sind, werden sie nacheinander verarbeitet, sobald Verarbeitungsslots verfügbar sind.</li><li>Neue Sicherungen eines geklonten oder wiederhergestellten Volumes können erst stattfinden, nachdem der Vorgang abgeschlossen wurde. Allerdings sind für ein lokales Volume Sicherungen zulässig, nachdem das Volume online geschaltet wurde.</li></ul> |
| Wiederherstellungszeit für das Wiederherstellen und Klonen mehrstufiger Volumes |< 2 Minuten |<ul><li>Das Volume wird nach einem Wiederherstellungs- oder Klonvorgang unabhängig von der Volumegröße innerhalb von zwei Minuten verfügbar gemacht.</li><li>Die Volumeleistung kann am Anfang noch etwas beeinträchtigt sein, da sich die meisten Daten und Metadaten noch in der Cloud befinden. Die Leistung kann sich verbessern, wenn die Daten nach und nach aus der Cloud auf das StorSimple-Gerät übertragen werden.</li><li>Die Gesamtzeit, die zum Herunterladen der Metadaten benötigt wird, hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite der Cloudverbindung beeinflusst werden.</li><li>Der Wiederherstellungs- oder Klonvorgang ist abgeschlossen, wenn sich alle Metadaten auf dem Gerät befinden.</li><li>Sicherungsvorgänge können erst nach Abschluss des Wiederherstellungs- oder Klonvorgangs ausgeführt werden. |
| Wiederherstellungszeit für das Wiederherstellen lokal fixierter Volumes |< 2 Minuten |<ul><li>Das Volume wird nach dem Wiederherstellungsvorgang unabhängig von der Volumegröße innerhalb von zwei Minuten verfügbar gemacht.</li><li>Die Volumeleistung kann am Anfang noch etwas beeinträchtigt sein, da sich die meisten Daten und Metadaten noch in der Cloud befinden. Die Leistung kann sich verbessern, wenn die Daten nach und nach aus der Cloud auf das StorSimple-Gerät übertragen werden.</li><li>Die Gesamtzeit, die zum Herunterladen der Metadaten benötigt wird, hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite der Cloudverbindung beeinflusst werden.</li><li>Im Gegensatz zu mehrschichtigen Volumes werden bei lokalen Volumes die Volumedaten auch lokal auf das Gerät heruntergeladen. Die Wiederherstellung ist abgeschlossen, wenn alle Daten des Datenträgers auf das Gerät geladen wurden.</li><li>Die Wiederherstellungsvorgänge können lange dauern. Die Gesamtzeit zum Abschließen des Wiederherstellungsvorgangs hängt von der Größe des bereitgestellten lokalen Volumes, der Internetbandbreite und den auf dem Gerät vorhandenen Daten ab. Sicherungsvorgänge auf lokal fixierten Volumes sind zulässig, während die Wiederherstellung ausgeführt wird. |
| Verarbeitungsrate für Cloudmomentaufnahmen |15 Minuten/TB |<ul><li>Die Mindestzeit zum Vorbereiten einer Cloudmomentaufnahme für das Hochladen. Diese gilt pro TB an zugewiesenen Volumedaten in der Sicherung. </li><li> Die Gesamtzeit für eine Cloudmomentaufnahme wird berechnet, indem diese Zeit zur Hochladezeit für die Momentaufnahme addiert wird. Letztere wird von der Internetbandbreite der Cloudverbindung beeinflusst. |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von SSD-Ebene aus bereitgestellt)* |920/720 MB/s mit einer einzelnen 10-GbE-Netzwerkschnittstelle |Bis zu 2x mit MPIO und zwei Netzwerkschnittstellen. |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von HDD-Ebene aus bereitgestellt)* |120/250 MB/s | |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von Cloud-Ebene aus bereitgestellt)* ab Update 3 und höher** |40/60 MB/s für mehrstufige Volumes<br><br>60/80 MB/s für mehrstufige Volumes, bei denen im Rahmen der Volumeerstellung die Archivierungsoption ausgewählt wurde |Lesedurchsatz hängt von Clients ab, die genügend E/A-Warteschlangentiefe generieren und verwalten müssen. <br><br>Die erreichbare Geschwindigkeit hängt von der Geschwindigkeit des zugrunde liegenden Speicherkontos ab. |

\* Der maximale Durchsatz pro E/A-Typ wurde mit 100 %-Lese- und 100 %-Schreibszenarien gemessen. Der tatsächliche Durchsatz kann geringer sein und hängt von der E/A-Mischung und den Netzwerkbedingungen ab.

\*\* Leistungswerte vor Update 3 können niedriger sein.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie die [StorSimple-Systemanforderungen](storsimple-8000-system-requirements.md).

