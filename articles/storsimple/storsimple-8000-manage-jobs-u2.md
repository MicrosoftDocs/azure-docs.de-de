---
title: Anzeigen und Verwalten von Aufträgen für die StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt das Blatt „Aufträge“ des StorSimple-Geräte-Manager-Diensts und seine Verwendung zum Nachverfolgen zuletzt ausgeführter, aktueller und geplanter Sicherungsaufträge.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 462f8dafdffa7ee01e6ccf7945a1abfdff90db42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720692"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Anzeigen und Verwalten von Aufträgen mithilfe des StorSimple-Geräte-Manager-Diensts (Update 3 und höher)

## <a name="overview"></a>Übersicht
Das Blatt **Aufträge** stellt ein zentrales Portal zum Anzeigen und Verwalten von Aufträgen bereit, die für Geräte gestartet wurden, die mit dem StorSimple-Geräte-Manager-Dienst verbunden sind. Sie können geplante, aktive, abgeschlossene, abgebrochene und fehlerhafte Aufträge für mehrere Geräte anzeigen. Die jeweiligen Ergebnisse werden in einem Tabellenformat angezeigt.

![Blatt „Aufträge“](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Sie können die Aufträge, an denen Sie interessiert sind, schnell finden, indem Sie nach folgenden Feldern filtern:

* **Status** – Aufträge können die folgenden Status haben: „Wird ausgeführt“, „Erfolgreich“, „Abgebrochen“, „Fehler“, „Wird abgebrochen“ oder „Erfolgreich mit Fehlern“.
* **Zeitbereich** – Aufträge können entsprechend einem Datums- und Zeitbereich gefiltert werden. Die möglichen Bereich sind „Letzte Stunde“, „Letzte 24 Stunden“, „Letzte 7 Tage“, „Letzte 30 Tage“, „Letztes Jahr“ und „Benutzerdefiniertes Datum“.
* **Typ** – Es gibt die folgenden Auftragstypen: „Geplante Sicherung“, „Manuelle Sicherung“, „Sicherung wiederherstellen“, „Volume klonen“, „Failover für Volumecontainer“, „Lokales Volume erstellen“, „Volume ändern“, „Updates installieren“, „Supportprotokolle erfassen“ und „Cloudgerät erstellen“.
* **Geräte** – Aufträge werden für ein bestimmtes Gerät ausgelöst, das mit Ihrem Dienst verbunden ist.
  
Die gefilterten Aufträge werden dann anhand der folgenden Attribute tabellarisch aufgelistet:
  
* **Name** – „Geplante Sicherung“, „Manuelle Sicherung“, „Sicherung wiederherstellen“, „Volume klonen“, „Failover für Volumecontainer“, „Lokales Volume erstellen“, „Volume ändern“, „Updates installieren“, „Supportprotokolle erfassen“ und „Cloudgerät erstellen“.
* **Status** – „Wird ausgeführt“, „Fehler“, „Abgeschlossen“, „Wird abgebrochen“, „Abgebrochen“ oder „Mit Fehlern abgeschlossen“.
* **Entität** – Die Aufträge können einem Volume, einer Sicherungsrichtlinie oder einem Gerät zugeordnet sein. Ein Klonauftrag ist beispielsweise einem Volume zugeordnet, während ein geplanter Sicherungsauftrag einer Sicherungsrichtlinie zugeordnet ist. Ein Geräteauftrag wird aufgrund eines Notfallwiederherstellungs- oder eines Wiederherstellungsvorgangs erstellt.
* **Gerät** – Der Name des Geräts, für den der Auftrag gestartet wurde.
* **Gestartet am** – der Zeitpunkt, zu dem der Auftrag gestartet wurde.
* **Dauer** – Der Zeitaufwand für den Auftrag.

Die Liste der Aufträge wird alle 30 Sekunden aktualisiert.

Sie können auf dieser Seite die folgenden auftragsbezogenen Aktionen ausführen:

* Anzeigen von Auftragsdetails
* Abbrechen eines Auftrags

## <a name="view-job-details"></a>Anzeigen von Auftragsdetails
Führen Sie die folgenden Schritte aus, um die Details eines Auftrags anzuzeigen.

#### <a name="to-view-job-details"></a>So zeigen Sie Auftragsdetails an
1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Aufträge**.

2. Zeigen Sie auf dem Blatt **Aufträge** die für Sie interessanten Aufträge an, indem Sie eine Abfrage mit entsprechenden Filtern ausführen. Sie können nach abgeschlossenen, ausführenden oder abgebrochenen Aufträgen suchen.

    ![Blatt „Auftrag“](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Wählen Sie einen Auftrag aus, und klicken Sie darauf.

    ![Blatt „Auftrag“](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. Auf dem Blatt „Auftragsdetails“ können Sie den Status, die Details, Zeitstatistiken und Datenstatistiken sehen.
   
    ![Auftragsdetails](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Abbrechen eines Auftrags
Führen Sie die folgenden Schritte aus, um einen Auftrag abzubrechen, der momentan ausgeführt wird.

> [!NOTE]
> Einige Aufträge, wie z. B. das Ändern eines Volumetyps oder das Erweitern eines Volumes, können nicht abgebrochen werden.


### <a name="to-cancel-a-job"></a>So brechen Sie einen Auftrag ab
1. Zeigen Sie auf der Seite **Aufträge** die Aufträge an, die momentan ausgeführt werden und die Sie abbrechen möchten. Führen Sie dazu eine Abfrage mit entsprechenden Filtern aus. Wählen Sie den Auftrag aus.

2. Klicken Sie mit der rechten Maustaste, um für den ausgewählten Auftrag das Kontextmenü aufzurufen. Klicken Sie dann auf **Abbrechen**.

    ![Auftragsdetails](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Dieser Auftrag wird nun abgebrochen.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwalten von StorSimple-Sicherungsrichtlinien](storsimple-8000-manage-backup-policies-u2.md).
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts verwalten](storsimple-8000-manager-service-administration.md).

