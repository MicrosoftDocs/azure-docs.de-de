---
title: StorSimple Snapshot Manager-Sicherungsaufträge | Microsoft Docs
description: Beschreibt, wie das MMC-Snap-in von StorSimple Snapshot Manager zum Anzeigen und Verwalten von geplanten, aktuell ausgeführten und abgeschlossenen Sicherungsaufträge verwendet wird.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998191"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Verwenden von StorSimple Snapshot Manager zum Anzeigen und Verwalten von Sicherungsaufträgen

## <a name="overview"></a>Übersicht
Der Knoten **Aufträge** im Fensterbereich **Bereich** zeigt Sicherungsaufträge in den Phasen **Geplant**, **Letzte 24 Stunden** und **Wird ausgeführt**, die Sie interaktiv oder durch eine konfigurierte Richtlinie initiiert haben. 

In diesem Tutorial wird erklärt, wie Sie den Knoten **Aufträge** verwenden können, um Informationen über geplante, aktuelle und aktuell ausgeführte Sicherungsaufträge anzuzeigen. (Die Liste der Aufträge und die entsprechenden Informationen werden im Bereich **Ergebnisse** angezeigt.) Darüber hinaus können Sie mit der rechten Maustaste auf einen aufgelisteten Auftrag klicken, sodass ein Kontextmenü mit verfügbaren Aktionen erscheint.

## <a name="view-scheduled-jobs"></a>Anzeigen geplanter Aufträge
Verwenden Sie das folgende Verfahren, um geplante Sicherungsaufträge anzuzeigen.

#### <a name="to-view-scheduled-jobs"></a>So zeigen Sie geplante Aufträge an:
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten. 
2. Erweitern Sie im Fenster **Bereich** den Knoten **Aufträge**, und klicken Sie auf **Geplant**. Die folgenden Informationen werden im **Ergebnisbereich** angezeigt:
   
   * **Name** – der Name der geplanten Momentaufnahme
   * **Nächste Ausführung** – das Datum und die Uhrzeit der nächsten geplanten Momentaufnahme
   * **Letzte Ausführung** – das Datum und die Uhrzeit der letzten geplanten Momentaufnahme
     
     > [!NOTE]
     > Bei einmaligen Momentaufnahmen sind die Informationen **Nächste Ausführung** und **Letzte Ausführung** identisch.
     
     ![Geplante Sicherungsaufträge](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Um zusätzliche Aktionen für einen bestimmten Auftrag durchzuführen, klicken Sie mit der rechten Maustaste auf den Namen des Auftrags im Bereich **Ergebnisse** , und wählen Sie aus den Menüoptionen.

## <a name="view-recent-jobs"></a>Anzeigen kürzlich ausgeführter Aufträge
Verwenden Sie das folgende Verfahren zum Anzeigen von Sicherungs- und Wiederherstellungsaufträgen, die in den letzten 24 Stunden abgeschlossen wurden.

#### <a name="to-view-recent-jobs"></a>So zeigen Sie kürzlich ausgeführte Aufträge an:
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Erweitern Sie im Fenster **Bereich** den Knoten **Aufträge**, und klicken Sie auf **Letzte 24 Stunden**. Der Bereich **Ergebnisse** zeigt Sicherungsaufträge für die letzten 24 Stunden an (bis maximal 64 Aufträge). Die folgenden Informationen erscheinen im Bereich **Ergebnisse**, abhängig von den unter **Ansicht** angegebenen Optionen:
   
   * **Name** – der Name der geplanten Momentaufnahme.
   * **Gestartet** – das Datum und die Startzeit der Momentaufnahme.
   * **Beendet**: das Datum und die Uhrzeit, zu der die Momentaufnahme abgeschlossen oder beendet wurde.
   * **Verstrichen**: die Zeitspanne zwischen den Zeiten **Gestartet** und **Beendet**.
   * **Status**: der Status des zuletzt abgeschlossenen Auftrags. **Erfolgreich**: Gibt an, dass die Sicherung erfolgreich erstellt wurde. **Fehler**: Gibt an, dass der Auftrag nicht erfolgreich ausgeführt wurde.
   * **Informationen**: die Ursache für den Fehler.
   * **Verarbeitete Bytes (MB)** : die Menge der Daten aus der Volumegruppe, die (in MB) verarbeitet wurde. 
     
     ![Aufträge, die in den letzten 24 Stunden ausgeführt wurden](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Um zusätzliche Aktionen für einen bestimmten Auftrag durchzuführen, klicken Sie mit der rechten Maustaste auf den Namen des Auftrags im Bereich **Ergebnisse** , und wählen Sie aus den Menüoptionen.
   
    ![Löschen eines Auftrags](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Anzeigen aktuell ausgeführter Aufträge
Verwenden Sie das folgende Verfahren zum Anzeigen von Aufträgen, die derzeit ausgeführt werden.

#### <a name="to-view-currently-running-jobs"></a>So zeigen Sie aktuell ausgeführte Aufträge an:
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Erweitern Sie im Fenster **Bereich** den Knoten **Aufträge**, und klicken Sie auf **Wird ausgeführt**. Abhängig von den unter **Ansicht** angegebenen Optionen werden im Bereich **Ergebnisse** die folgenden Informationen angezeigt:
   
   * **Name** – der Name der geplanten Momentaufnahme.
   * **Gestartet** – das Datum und die Startzeit der Momentaufnahme.
   * **Prüfpunkt** – die aktuelle Sicherungsaktion.
   * **Status** – der Prozentsatz der Fertigstellung.
   * **Verstrichene** – die Zeitspanne, die seit dem Start der Sicherung vergangen ist. 
   * **Durchschnittlicher Durchsatz (MB)** – Verhältnis der gesamten verarbeiteten Datenbytes zur gesamten Verarbeitungszeit (in MB).
   * **Verarbeitete Bytes (MB)** – gesamte verarbeitete Datenbytes (in MB).
   * **Geschriebene Bytes (MB)** – gesamte geschriebene Datenbytes (in MB). Dieser Wert umfasst die Daten sowie die Metadaten und ist daher normalerweise größer als der Wert von „Verarbeitete Bytes (MB)“.
     
     ![Aufträge, die derzeit ausgeführt werden](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Um zusätzliche Aktionen für einen bestimmten Auftrag durchzuführen, klicken Sie mit der rechten Maustaste auf den Namen des Auftrags im Bereich **Ergebnisse** , und wählen Sie aus den Menüoptionen.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung](storsimple-snapshot-manager-admin.md).
* Informationen zum [Verwenden von StorSimple Snapshot Manager zum Verwalten des Sicherungkatalogs](storsimple-snapshot-manager-manage-backup-catalog.md).

