---
title: Failover, Notfallwiederherstellung auf ein anderes StorSimple 8000-Gerät
description: Erfahren Sie, wie für Ihr physisches Gerät der StorSimple 8000-Serie ein Failover auf ein anderes physisches Gerät erfolgt.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 67d6fb1f3cc359288ed942d915e186542a62b0fc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017133"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Failover auf ein physisches Gerät der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht

In diesem Tutorial werden die erforderlichen Schritte für ein Failover bei einem Notfall von einem physischen Gerät der StorSimple 8000-Serie auf ein anderes physisches StorSimple-Gerät beschrieben. StorSimple nutzt die Funktion des Gerätefailovers zum Migrieren von Daten von einem physischen Quellgerät im Rechenzentrum auf ein anderes physisches Gerät. Die Anleitung in diesem Tutorial gilt für physische Geräte der StorSimple 8000-Serie, auf denen Softwareversionen mit Update 3 und höher ausgeführt werden.

Weitere Informationen zum Gerätefailover und seiner Nutzung für die Wiederherstellung nach einem Notfall finden Sie unter [Failover und Notfallwiederherstellung für Geräte der StorSimple 8000-Serie](storsimple-8000-device-failover-disaster-recovery.md).

Informationen zum Failover eines physischen Geräts auf eine StorSimple Cloud Appliance finden Sie unter [Failover auf eine StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Informationen für ein Failover eines physischen Geräts auf sich selbst finden Sie unter [Failover auf demselben physischen StorSimple-Gerät](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass Sie die Überlegungen zum Gerätefailover geprüft haben. Weitere Informationen finden Sie unter [Allgemeine Überlegungen zum Gerätefailover](storsimple-8000-device-failover-disaster-recovery.md).

- Sie benötigen ein physisches Gerät der StorSimple 8000-Serie, das im Rechenzentrum bereitgestellt sein muss. Auf dem Gerät muss die Softwareversion mit Update 3 oder höher ausgeführt werden. Weitere Informationen finden Sie unter [Bereitstellen des lokalen StorSimple-Geräts](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Schritte für ein Failover auf ein anderes physisches Gerät

Führen Sie die folgenden Schritte aus, um Ihr Gerät auf einem physischen Zielgerät wiederherzustellen.

1. Stellen Sie sicher, dass der Volumecontainer für das Failover über zugeordnete Cloudmomentaufnahmen verfügt. Weitere Informationen finden Sie unter [Verwenden des StorSimple-Geräte-Manager-Diensts zum Erstellen von Sicherungen](storsimple-8000-manage-backup-policies-u2.md).
2. Navigieren Sie zu Ihrem StorSimple-Geräte-Manager, und klicken Sie dann auf **Geräte**. Wechseln Sie auf dem Blatt **Geräte** zur Liste der Geräte, die mit Ihrem Dienst verbunden sind.
    ![Gerät auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Klicken Sie auf das gewünschte Quellgerät. Das Quellgerät weist die Volumecontainer auf, für die ein Failover erfolgen soll. Wechseln Sie zu **Einstellungen > Volumecontainer**.
4. Wählen Sie einen Volumecontainer für das Failover auf einem anderen Gerät aus. Klicken Sie auf den Volumecontainer, um die Liste der Volumes innerhalb dieses Containers anzuzeigen. Wählen Sie ein Volume aus. Klicken Sie mit der rechten Maustaste, und klicken Sie auf **Offline schalten**, um das Volume offline zu schalten. Wiederholen Sie diesen Vorgang für alle Volumes im Volumecontainer.
5. Wiederholen Sie den vorherigen Schritt für alle Volumecontainer, für die auf dem anderen Gerät ein Failover durchgeführt werden soll.
6. Navigieren Sie zurück zum Blatt **Geräte**. Klicken Sie auf der Befehlsleiste auf **Failover ausführen**.
    Klicken Sie auf ![Failover ausführen](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png).
    
7. Führen Sie auf dem Blatt **Failover ausführen** die folgenden Schritte durch:
   
   1. Klicken Sie auf **Quelle**. Die Volumecontainer mit zugeordneten Cloudmomentaufnahmen werden angezeigt. Nur die angezeigten Container sind für ein Failover geeignet. Wählen Sie in der Liste der Volumecontainer die Volumecontainer aus, für die ein Failover durchgeführt werden soll. **Nur die Volumecontainer mit zugeordneten Cloudmomentaufnahmen und Offlinevolumes werden angezeigt.**

       ![Quelle auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klicken Sie auf **Ziel**. Wählen Sie für die im vorherigen Schritt ausgewählten Volumecontainer in der Dropdownliste mit den verfügbaren Zielgeräten ein Zielgerät aus. In der Liste werden nur die Geräte mit ausreichender Kapazität zum Aufnehmen der Quellvolumecontainer angezeigt.

        ![Ziel auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Überprüfen Sie abschließend unter **Zusammenfassung** die Failovereinstellungen. Nachdem Sie die Einstellungen überprüft haben, aktivieren Sie das Kontrollkästchen, das angibt, dass die Volumes in ausgewählten Volumecontainern offline sind. Klicken Sie auf **OK**.

       ![Überprüfen der Failovereinstellungen](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple erstellt einen Failoverauftrag. Klicken Sie auf dem Blatt **Aufträge** auf die Auftragsbenachrichtigung, um den Failoverauftrag zu überwachen.

    Wenn der Volumecontainer, für den Sie ein Failover durchgeführt haben, lokale Volumes enthält, werden einzelne Wiederherstellungsaufträge für jedes lokale Volume im Container angezeigt (nicht für mehrstufige Volumes). Diese Wiederherstellungsaufträge können einige Zeit in Anspruch nehmen. Der Failoverauftrag wird wahrscheinlich vorher abgeschlossen. Diese Volumes verfügen erst nach Abschluss der Wiederherstellungsaufträge über lokale Garantien.

    ![Überwachen des Failoverauftrags](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Wechseln Sie nach Abschluss des Failovers zum Blatt **Geräte**.
   
   1. Wählen Sie das Gerät aus, das Sie als Zielgerät für den Failovervorgang verwendet haben.

       ![Auswählen von Gerät 2](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Wechseln Sie zum Blatt **Volumecontainer**. Es sollten alle Volumecontainer und die Volumes des alten Geräts aufgeführt werden.

       ![Anzeigen von Zielvolumecontainern](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Nächste Schritte

* Nach einem Failover müssen Sie unter Umständen Ihr [StorSimple-Gerät deaktivieren oder löschen](storsimple-8000-deactivate-and-delete-device.md).
* Weitere Informationen zum Verwenden des StorSimple-Geräte-Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-GeräteManager-Diensts](storsimple-8000-manager-service-administration.md).

