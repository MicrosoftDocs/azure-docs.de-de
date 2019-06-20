---
title: 'StorSimple: Failover und Notfallwiederherstellung für eine StorSimple Cloud Appliance | Microsoft-Dokumentation'
description: Erfahren Sie, wie für Ihr physisches Gerät der StorSimple 8000-Serie ein Failover auf ein Cloudgerät erfolgt.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 45c521fd044fa258b8052a3f0de48784cf4160e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584394"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Failover auf Ihre StorSimple Cloud Appliance

## <a name="overview"></a>Übersicht

In diesem Tutorial werden die erforderlichen Schritte für ein Failover bei einem Notfall von einem physischen Gerät der StorSimple 8000-Serie auf eine StorSimple Cloud Appliance beschrieben. StorSimple nutzt die Funktion des Gerätefailovers zum Migrieren von Daten von einem physischen Quellgerät im Rechenzentrum auf ein in Azure ausgeführtes Cloudgerät. Die Anleitung in diesem Tutorial gilt für physische Geräte der StorSimple 8000-Serie und Cloudgeräte, auf denen Softwareversionen mit Update 3 und höher ausgeführt werden.

Weitere Informationen zum Gerätefailover und seiner Nutzung für die Wiederherstellung nach einem Notfall finden Sie unter [Failover und Notfallwiederherstellung für Geräte der StorSimple 8000-Serie](storsimple-8000-device-failover-disaster-recovery.md).

Informationen zum Failover eines physischen StorSimple-Geräts auf ein anderes physisches Gerät finden Sie unter [Failover auf ein physisches StorSimple-Gerät](storsimple-8000-device-failover-physical-device.md). Informationen für ein Failover eines Geräts auf sich selbst finden Sie unter [Failover auf dasselbe physische StorSimple-Gerät](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass Sie die Überlegungen zum Gerätefailover geprüft haben. Weitere Informationen finden Sie unter [Allgemeine Überlegungen zum Gerätefailover](storsimple-8000-device-failover-disaster-recovery.md).

- Bevor Sie dieses Verfahren ausführen können, müssen Sie eine StorSimple Cloud Appliance erstellen und konfigurieren. Bei Ausführung der Softwareversion mit Update 3 und höher erwägen Sie ein Cloudgerät der 8020-Serie für die Notfallwiederherstellung. Das Modell 8020 bietet 64 TB Speicherplatz und nutzt Storage Premium. Weitere Informationen finden Sie unter [Bereitstellen und Verwalten einer StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Schritte beim Failover auf ein Cloudgerät

Führen Sie die folgenden Schritte aus, um Ihr Gerät auf einer StorSimple Cloud Appliance wiederherzustellen.

1.  Stellen Sie sicher, dass der Volumecontainer für das Failover über zugeordnete Cloudmomentaufnahmen verfügt. Weitere Informationen finden Sie unter [Verwenden des StorSimple-Geräte-Manager-Diensts zum Erstellen von Sicherungen](storsimple-8000-manage-backup-policies-u2.md).
2. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**. Wechseln Sie auf dem Blatt **Geräte** zur Liste der Geräte, die mit Ihrem Dienst verbunden sind.
    ![Gerät auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Klicken Sie auf das gewünschte Quellgerät. Das Quellgerät weist die Volumecontainer auf, für die ein Failover erfolgen soll. Wechseln Sie zu **Einstellungen > Volumecontainer**.

    ![Gerät auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Wählen Sie einen Volumecontainer für das Failover auf einem anderen Gerät aus. Klicken Sie auf den Volumecontainer, um die Liste der Volumes innerhalb dieses Containers anzuzeigen. Wählen Sie ein Volume aus. Klicken Sie mit der rechten Maustaste, und klicken Sie auf **Offline schalten**, um das Volume offline zu schalten.

    ![Gerät auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Wiederholen Sie diesen Vorgang für alle Volumes im Volumecontainer.

     ![Gerät auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Wiederholen Sie den vorherigen Schritt für alle Volumecontainer, für die auf dem anderen Gerät ein Failover durchgeführt werden soll.

7. Navigieren Sie zurück zum Blatt **Geräte**. Klicken Sie auf der Befehlsleiste auf **Failover ausführen**.

    ![Klicken Sie auf „Failover ausführen“](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Führen Sie auf dem Blatt **Failover ausführen** die folgenden Schritte durch:
   
    1. Klicken Sie auf **Quelle**. Wählen Sie die Volumecontainer aus, für die das Failover erfolgen soll. **Nur die Volumecontainer mit zugeordneten Cloudmomentaufnahmen und Offlinevolumes werden angezeigt.**
        ![Quelle auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klicken Sie auf **Ziel**. Wählen Sie in der Dropdownliste mit den verfügbaren Geräten ein Zielcloudgerät aus. **In der Liste werden nur die Geräte mit ausreichender Kapazität zum Aufnehmen der Quellvolumecontainer angezeigt.**

        ![Ziel auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Überprüfen Sie unter **Zusammenfassung** die Failovereinstellungen, und aktivieren Sie das Kontrollkästchen, das angibt, dass die Volumes in ausgewählten Volumecontainern offline sind. 

        ![Überprüfen der Failovereinstellungen](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Ein Failoverauftrag wird erstellt. Klicken Sie auf die Auftragsbenachrichtigung, um den Failoverauftrag zu überwachen.

    ![Überwachen des Failoverauftrags](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Wechseln Sie nach Abschluss des Failovers zurück zum Blatt **Geräte**.

    1. Wählen Sie das Gerät aus, das Sie als Ziel für das Failover verwendet haben.

       ![Gerät auswählen](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klicken Sie auf **Volumecontainer**. Es sollten alle Volumecontainer und die Volumes des alten Geräts aufgeführt werden.

       Wenn der Volumecontainer, für den Sie das Failover ausgeführt haben, über lokale Volumes verfügt, erfolgt für diese Volumes ein Failover als mehrstufige Volumes. Lokale Volumes werden von StorSimple Cloud Appliances derzeit nicht unterstützt.

       ![Anzeigen von Zielvolumecontainern](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Nächste Schritte

* Nach einem Failover müssen Sie unter Umständen Ihr [StorSimple-Gerät deaktivieren oder löschen](storsimple-8000-deactivate-and-delete-device.md).

* Weitere Informationen zum Verwenden des StorSimple-Geräte-Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-GeräteManager-Diensts](storsimple-8000-manager-service-administration.md).

