---
title: 'Tutorial: Failover Ihres physischen StorSimple-Geräts auf dasselbe Gerät'
description: Lernen Sie die erforderlichen Schritte für ein Failover bei einem Notfall von einem physischen Gerät der StorSimple 8000-Serie auf das Gerät selbst kennen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: 17c116194aa52a82246bcee9114824e8a918ebbd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017099"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Failover Ihres physischen StorSimple-Geräts auf dasselbe Gerät

## <a name="overview"></a>Übersicht

In diesem Tutorial werden die erforderlichen Schritte für ein Failover bei einem Notfall von einem physischen Gerät der StorSimple 8000-Serie auf das Gerät selbst beschrieben. StorSimple nutzt die Funktion des Gerätefailovers zum Migrieren von Daten von einem physischen Quellgerät im Rechenzentrum auf ein anderes physisches Gerät. Die Anleitung in diesem Tutorial gilt für physische Geräte der StorSimple 8000-Serie, auf denen Softwareversionen mit Update 3 und höher ausgeführt werden.

Weitere Informationen zum Gerätefailover und seiner Nutzung für die Wiederherstellung nach einem Notfall finden Sie unter [Failover und Notfallwiederherstellung für Geräte der StorSimple 8000-Serie](storsimple-8000-device-failover-disaster-recovery.md).

Informationen zum Failover eines physischen Geräts auf ein anderes physisches Gerät finden Sie unter [Failover auf dasselbe physische StorSimple-Gerät](storsimple-8000-device-failover-physical-device.md). Informationen zum Failover eines physischen Geräts auf eine StorSimple Cloud Appliance finden Sie unter [Failover auf eine StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass Sie die Überlegungen zum Gerätefailover geprüft haben. Weitere Informationen finden Sie unter [Allgemeine Überlegungen zum Gerätefailover](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Schritte für ein Failover auf dasselbe Gerät

Führen Sie die folgenden Schritte aus, wenn ein Failover auf dasselbe Gerät erfolgen soll.

1. Erstellen Sie Cloudmomentaufnahmen aller Volumes auf Ihrem Gerät. Weitere Informationen finden Sie unter [Verwenden des StorSimple-Geräte-Manager-Diensts zum Erstellen von Sicherungen](storsimple-8000-manage-backup-policies-u2.md).
2. Setzen Sie das Gerät auf die Werkseinstellungen zurück. Befolgen Sie die ausführlichen Anleitungen unter [Zurücksetzen des Geräts auf die Standardwerkseinstellungen](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Geräte**. Auf dem Blatt **Geräte** sollte das alte Gerät als **Offline** angezeigt werden.

    ![Quellgerät offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurieren Sie das Gerät, und registrieren Sie es erneut im StorSimple-Geräte-Manager-Dienst. Das neu registrierte Gerät sollte als **Für Einrichtung bereit** angezeigt werden. Der Gerätename des neuen Geräts ist identisch mit dem des alten Geräts. An ihn wurde jedoch eine Ziffer angefügt, um anzugeben, dass das Gerät auf die Werkseinstellungen zurückgesetzt und erneut registriert wurde.

    ![Neu registriertes Gerät zur Einrichtung bereit](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Führen Sie für das neue Gerät die Einrichtung durch. Weitere Informationen finden Sie unter [Schritt 4: Abschließen der minimalen Gerätekonfiguration](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Auf dem Blatt **Geräte** ändert sich der Gerätestatus in **Online**.

   > [!IMPORTANT]
   > **Schließen Sie zuerst die Mindestkonfiguration ab, weil andernfalls die Notfallwiederherstellung misslingen kann.**

    ![Neu registriertes Gerät online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Wählen Sie auf der Befehlsleiste das alte Gerät (mit dem Status „Offline“) aus, und klicken Sie auf **Failover ausführen**. Wählen Sie auf dem Blatt **Failover ausführen** das alte Gerät als Quelle aus, und geben Sie das Zielgerät als neu registriertes Gerät an.

    ![Übersicht über das Failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Ausführliche Anweisungen finden Sie unter „Failover auf ein anderes physisches Gerät“.

7. Es wird ein Gerätewiederherstellungsauftrag erstellt, den Sie auf dem Blatt **Aufträge** überwachen können.

8. Greifen Sie, nachdem der Auftrag erfolgreich abgeschlossen wurde, auf das neue Gerät zu, und navigieren Sie zum Blatt **Volumecontainer**. Prüfen Sie, ob alle Volumecontainer vom alten Gerät auf das neue Gerät migriert wurden.

   ![Migrierte Volumecontainer](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Nach Abschluss des Failovers können Sie das alte Gerät im Portal deaktivieren und löschen. Wählen Sie das alte Gerät (mit dem Status „Offline“) aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Deaktivieren** aus. Nachdem das Gerät deaktiviert wurde, wird der Status des Geräts aktualisiert.

     ![Quellgerät deaktiviert](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Wählen Sie das deaktivierte Gerät aus, klicken Sie mit der rechten Maustaste darauf, und klicken Sie dann auf **Löschen**. Dadurch wird das Gerät aus der Liste der Geräte gelöscht.

    ![Quellgerät gelöscht](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Nächste Schritte

* Nach einem Failover müssen Sie unter Umständen Ihr [StorSimple-Gerät deaktivieren oder löschen](storsimple-8000-deactivate-and-delete-device.md).
* Weitere Informationen zum Verwenden des StorSimple-Geräte-Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-GeräteManager-Diensts](storsimple-8000-manager-service-administration.md).

