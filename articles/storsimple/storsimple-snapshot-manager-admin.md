---
title: StorSimple Snapshot Manager-Verwaltung | Microsoft Docs
description: Bietet eine Übersicht und Links zu weiteren Informationen zu Verwaltungsaufgaben und -workflows in der StorSimple Snapshot Manager-Lösung.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: bc72da98800ef85ef14be0882ba856fbf01386b9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630018"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung

## <a name="overview"></a>Übersicht
StorSimple Snapshot Manager ist ein Microsoft Management Console (MMC)-Snap-In, das den Datenschutz und die Sicherungsverwaltung in einer Microsoft Azure StorSimple-Umgebung vereinfacht. Mit dem StorSimple Snapshot Manager können Sie Microsoft Azure StorSimple-Daten im Rechenzentrum und in der Cloud als eine einzelne integrierte Speicherlösung verwalten und somit die Sicherungsprozesse vereinfachen und die Kosten senken.

Mit der zentralen Verwaltungskonsole von StorSimple Snapshot Manager können Sie konsistente, zeitpunktbezogene Sicherungskopien von lokalen und Clouddaten erstellen. Sie können die Konsole z. B. für Folgendes verwenden:

* Konfigurieren, Sichern und Löschen von Volumes
* Konfigurieren von Volumegruppen, um sicherzustellen, dass gesicherte Daten anwendungskonsistent sind
* Verwalten von Sicherungsrichtlinien, damit die Daten nach einem vordefinierten Zeitplan gesichert werden.
* Erstellen unabhängiger Kopien von Daten, die in der Cloud gespeichert und für die Notfallwiederherstellung verwendet werden können.

Dieser Artikel enthält Links zu Tutorials, in denen der StorSimple Snapshot Manager sowie dessen Verwendung zum Durchführen von Aufgaben der Systemverwaltung und Workflows beschrieben wird.

* Weitere Informationen zu StorSimple Snapshot Manager-Komponenten und zur Architektur finden Sie unter [Was ist der StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Um den StorSimple Snapshot Manager herunterzuladen, besuchen Sie [die StorSimple Snapshot Manager-Downloadseite](https://www.microsoft.com/download/details.aspx?id=44220).
* Informationen zu StorSimple Snapshot Manager-Bereitstellungsverfahren finden Sie unter [Bereitstellung von StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> StorSimple Snapshot Manager eignet sich nicht zur Verwaltung von Microsoft Azure StorSimple Virtual Arrays (auch bekannt als lokale virtuelle StorSimple-Geräte).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager-Aufgaben und -Workflows
Sie können StorSimple Snapshot Manager zum Überwachen und Verwalten bevorstehender, aktueller und abgeschlossener Sicherungsaufträge verwenden. Darüber hinaus stellt StorSimple Snapshot Manager einen Katalog von bis zu 64 abgeschlossenen Sicherungen bereit. Sie können in diesem Katalog nach Volumes oder einzelnen Dateien suchen und diese wiederherstellen. 

| WENN SIE DIES TUN MÖCHTEN... | VERWENDEN SIE DIESES TUTORIAL... |
|:--- |:--- |
| Erfahren Sie mehr über StorSimple Snapshot Manager |[Was ist StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Installieren des StorSimple Snapshot Managers<br>Erneutes Installieren des StorSimple Snapshot Managers<br>Entfernen des StorSimple Snapshot Managers |[Bereitstellung von StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Verwenden von StorSimple Snapshot Manager-Menüs und -Funktionen:<ul><li>Menüleiste</li><li>Symbolleiste</li><li>Bereichsfenster</li><li>Ergebnisbereich</li><li>Bereich "Aktionen"</li><li>Tastaturnavigation und Tastenkombinationen</li></ul> |[Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md) |
| Verwenden Sie die allgemeinen MMC-Funktionen im StorSimple Snapshot Manager:<ul><li>Sicht</li><li>Neues Fenster hier öffnen</li><li>Aktualisieren</li><li>Liste exportieren</li><li>Hilfe</li></ul> |[Verwenden der MMC-Menüaktionen in StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Hinzufügen oder Ersetzen eines Geräts<br>Verbinden eines Geräts<br>Überprüfen importierter Volumegruppen<br>Aktualisieren verbundener Geräte<br>Authentifizieren eines Geräts<br>Anzeigen von Gerätedetails<br>Löschen einer Gerätekonfiguration<br>Ändern eines Gerätekennworts<br>Ersetzen eines ausgefallenen Geräts<br> |[Verwenden von StorSimple Snapshot Manager zum Verbinden und Verwalten von StorSimple-Geräten](storsimple-snapshot-manager-manage-devices.md) |
| Bereitstellen von Volumes<br>Anzeigen von Informationen zu Volumes<br>Löschen von Volumes<br>Volumes erneut einlesen<br>Konfigurieren und Sichern eines Basisvolumes<br>Konfigurieren und Sichern eines dynamischen gespiegelten Volumes |[Verwenden des StorSimple Snapshot Managers zum Anzeigen und Verwalten von Volumes](storsimple-snapshot-manager-manage-volumes.md) |
| Anzeigen von Volumegruppen<br>Erstellen einer Volumegruppe<br>Sichern einer Volumegruppe<br>Bearbeiten einer Volumegruppe<br>Löschen einer Volumegruppe |[Verwenden des StorSimple Snapshot Managers zum Erstellen und Verwalten von Volumegruppen](storsimple-snapshot-manager-manage-volume-groups.md) |
| Erstellen einer Sicherungsrichtlinie <br>Bearbeiten einer Sicherungsrichtlinie<br>Löschen einer Sicherungsrichtlinie |[Verwenden des StorSimple Snapshot Managers zum Erstellen und Verwalten von Sicherungsrichtlinien](storsimple-snapshot-manager-manage-backup-policies.md) |
| Anzeigen und Verwalten der geplanten Sicherungsaufträge<br>Anzeigen und Verwalten kürzlich ausgeführter Sicherungsaufträge<br>Anzeigen und Verwalten von derzeit ausgeführten Sicherungsaufträgen |[Verwenden von StorSimple Snapshot Manager zum Anzeigen und Verwalten von Sicherungsaufträgen](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Wiederherstellen eines Volumes<br>Klonen eines Volumes oder einer Volumegruppe<br>Löschen einer Sicherung<br>Wiederherstellen einer Datei<br>Wiederherstellen der Storsimple Snapshot Manager-Datenbank |[Verwenden Sie StorSimple Snapshot Manager zum Verwalten des Sicherungskatalogs](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Nächste Schritte
[StorSimple Snapshot Manager herunterladen](https://www.microsoft.com/download/details.aspx?id=44220).

