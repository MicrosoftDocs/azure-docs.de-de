---
title: Tutorial zur Microsoft Azure StorSimple Virtual Array-Sicherung | Microsoft Docs
description: Erstellen geplanter und manueller Sicherungen für Microsoft Azure StorSimple Virtual Array und Ausführen der Wiederherstellung auf Elementebene, um eine gelöschte Datei auf Ihrem virtuellen Array wiederherzustellen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99c7f4ab92e4cce3937a933fec97008c281a0eae
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018289"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Sichern von Freigaben oder Volumes in Ihrem StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Das StorSimple Virtual Array ist ein lokales virtuelles Cloudspeichergerät, das als Dateiserver oder iSCSI-Server konfiguriert werden kann. Das Virtual Array ermöglicht dem Benutzer, geplante und manuelle Sicherungen aller Freigaben oder Volumes auf dem Gerät zu erstellen. Wenn als Dateiserver konfiguriert ist, ermöglicht es auch die Wiederherstellung auf Elementebene. In diesem Tutorial wird beschrieben, wie geplante und manuelle Sicherungen erstellt werden können, und die Wiederherstellung auf Elementebene durchgeführt werden kann, um eine gelöschte Datei auf Ihrem virtuellen Array wiederherzustellen.

Dieses Tutorial gilt nur für StorSimple Virtual Array. Informationen zur 8000-Serie finden Sie unter [Erstellen eines Backups für Geräte der 8000-Serie](./storsimple-8000-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Sichern von Freigaben und Volumes

Sicherungen stellen Zeitpunktschutz zur Verfügung und verbessern die Wiederherstellbarkeit bei gleichzeitiger Minimierung der Wiederherstellungszeiten für Freigaben und Volumes. Für das Sichern einer Freigabe oder eines Volumes auf dem StorSimple-Gerät sind zwei Methoden möglich: **geplant** oder **manuell**. Diese Methoden werden in den folgenden Abschnitten erläutert.

## <a name="change-the-backup-start-time"></a>Ändern der Startzeit der Sicherung

> [!NOTE]
> In dieser Version werden geplante Sicherungen durch eine Standardrichtlinie erstellt, die täglich zu einer bestimmten Uhrzeit ausgeführt wird und alle Freigaben oder Volumes auf dem Gerät sichert. Derzeit können für geplante Sicherungen keine benutzerdefinierten Richtlinien erstellt werden.


Ihr StorSimple Virtual Array verfügt über eine Standard-Sicherungsrichtlinie, die zu einer angegebenen Uhrzeit (22:30) beginnt, und alle Freigaben oder Volumes auf dem Gerät einmal pro Tag sichert. Sie können die Zeit ändern, zu der die Sicherung gestartet wird, die Häufigkeit und Vermerkdauer (Anzahl der beizubehaltenden Sicherungen) können nicht geändert werden. Während dieser Sicherung wird das gesamte virtuelle Gerät gesichert. Dies kann möglicherweise Auswirkungen auf die Leistung des Geräts haben, und die auf diesem Gerät bereitgestellten Workloads betreffen. Aus diesem Grund empfehlen wir, deren Ausführung außerhalb der Spitzenzeiten einzuplanen.

 Führen Sie die folgenden Schritte im [Azure-Portal](https://portal.azure.com/) aus, um die Standardstartzeit für die Sicherung zu ändern.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>So ändern Sie die Startzeit für die Standard-Sicherungsrichtlinie

1. Wechseln Sie zu **Geräte**. Daraufhin wird die Liste der Geräte, die bei Ihrem StorSimple-Geräte-Manager-Dienst registriert sind, angezeigt. 
   
    ![navigieren zu Geräte](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Wählen Sie Ihr Gerät aus, und klicken Sie darauf. Das Blatt **Einstellungen** wird angezeigt. Gehen Sie zu **Verwalten > Sicherungsrichtlinien**.
   
    ![Auswahl Ihres Geräts](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Auf dem Blatt **Sicherungsrichtlinien**, ist die angegebene Standardstartzeit 22:30 Uhr. Sie können in „Zeitzone des Geräts“ eine neue Startzeit für den täglichen Zeitplan angeben.
   
    ![Navigieren zu Sicherungsrichtlinien](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klicken Sie auf **Speichern**.

### <a name="take-a-manual-backup"></a>Erstellen einer manuellen Sicherung

Neben geplanten Sicherungen können Sie jederzeit eine manuelle (bedarfsgesteuerte) Sicherung von Gerätedaten vornehmen.

#### <a name="to-create-a-manual-backup"></a>So erstellen Sie eine manuelle Sicherung

1. Wechseln Sie zu **Geräte**. Wählen Sie Ihr Gerät aus, und klicken Sie mit der rechten Maustaste auf der rechten Seite der ausgewählten Reihe auf **...** . Wählen Sie im Kontextmenü **Sicherung anlegen** aus.
   
    ![Navigieren zu Sicherung anlegen](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Klicken Sie auf dem Blatt **Sicherung anlegen** auf **Sicherung anlegen**. Dadurch werden alle Freigaben auf dem Dateiserver oder alle Volumes auf Ihrem iSCSI-Server gesichert. 
   
    ![Sicherung wird gestartet](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Eine On-Demand-Sicherung wird gestartet. Dadurch sehen Sie, dass ein Sicherungsauftrag gestartet wurde.
   
    ![Sicherung wird gestartet 2](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Sobald der Auftrag erfolgreich abgeschlossen wurde, erhalten Sie erneut eine Benachrichtigung. Anschließend Startet der Sicherungsvorgang.
   
    ![Sicherungsauftrag erstellt](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Klicken Sie zum Überwachen des Fortschritts der Sicherung und zum Anzeigen der Auftragsdetails auf die Benachrichtigung. Dadurch gelangen Sie zu **Auftragsdetails**.
   
     ![Auftragsdetails zur Sicherung](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Nachdem die Sicherung abgeschlossen ist, gehen Sie zu **Verwaltung > Sicherungskatalog**. Es wird eine Cloud-Momentaufnahme aller Freigaben (oder Volumes) auf Ihrem Gerät angezeigt.
   
    ![Sicherung abgeschlossen](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Anzeigen vorhandener Sicherungen
Führen Sie die folgenden Schritte im Azure-Portal aus, um vorhandene Sicherungen anzuzeigen.

#### <a name="to-view-existing-backups"></a>So zeigen Sie vorhandene Sicherungen an

1. Wechseln Sie auf das Blatt **Geräte**. Wählen Sie Ihr Gerät aus, und klicken Sie darauf. Gehen Sie auf dem Blatt **Einstellungen** zu **Verwaltung > Sicherungskatalog**.
   
    ![Navigieren zum Sicherungskatalog](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Geben Sie die folgenden Kriterien an, die für die Filterung verwendet werden sollen:
   
   - **Zeitbereich** – kann auf **Letzte Stunde**, **Letzte 24 Stunden**, **Letzte 7 Tage**, **Letzte 30 Tage**,**Letztes Jahr** und **Benutzerdefinierter Datumsbereich** festgelegt werden.
    
   - **Geräte** – Wählen Sie aus der Liste der Dateiserver oder iSCSI-Server aus, die bei Ihrem StorSimple-Geräte-Manager-Dienst registriert sind.
   
   - **Initiiert** – kann automatisch auf **Geplant** (durch eine Sicherungsrichtlinie) oder **Manuell** initiiert werden (durch Sie).
   
     ![Filtern von Sicherungen](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klicken Sie auf **Anwenden**. Die gefilterte Liste der Sicherungen wird auf dem Blatt **Sicherungskatalog** angezeigt. Beachten Sie, dass nur 100 Sicherungselemente zu einem bestimmten Zeitpunkt angezeigt werden können.
   
    ![Aktualisierter Sicherungskatalog](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).