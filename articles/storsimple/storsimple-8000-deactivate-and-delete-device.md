---
title: Deaktivieren und Löschen eines Geräts der StorSimple 8000-Serie| Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein StorSimple-Gerät deaktivieren und löschen, das mit einem StorSimple-Geräte-Manager-Dienst verbunden ist.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 07d108306fdca9bfe8f793b61660550e43151d71
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017184"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Deaktivieren und Löschen eines StorSimple-Geräts

## <a name="overview"></a>Übersicht

Dieser Artikel beschreibt die Vorgehensweise zum Deaktivieren und Löschen eines StorSimple-Geräts, das mit einem StorSimple-Geräte-Manager-Dienst verbunden ist. Die Anleitung in diesem Artikel gilt nur für Geräte der StorSimple 8000-Serie, einschließlich StorSimple Cloud Appliance. Wenn Sie ein StorSimple Virtual Array verwenden, fahren Sie mit [Deaktivieren und Löschen eines StorSimple Virtual Arrays](storsimple-virtual-array-deactivate-and-delete-device.md) fort.

Das Deaktivieren trennt die Verbindung zwischen dem Gerät und dem entsprechenden StorSimple-Geräte-Manager-Dienst. Möglicherweise möchten Sie ein StorSimple-Gerät außer Betrieb nehmen (z. B. weil Sie es ersetzen oder aufrüsten möchten oder weil Sie StorSimple nicht mehr verwenden). Wenn dies der Fall ist, müssen Sie das Gerät deaktivieren, bevor Sie es löschen können.

Wenn Sie ein Gerät deaktivieren, sind alle Daten, die lokal auf dem Gerät gespeichert wurden, nicht mehr zugänglich. Nur Daten auf dem Gerät, die in der Cloud gespeichert wurden, können wiederhergestellt werden.

> [!WARNING]
> Die Deaktivierung ist ein ENDGÜLTIGER Vorgang und kann nicht rückgängig gemacht werden. Ein deaktiviertes Gerät kann nicht beim StorSimple Manager-Dienst registriert werden, es sei denn, es wird auf die Werkseinstellungen zurückgesetzt.
>
> Das Zurücksetzen auf die Werkseinstellungen löscht alle Daten, die lokal auf Ihrem Gerät gespeichert wurden. Daher müssen Sie von allen Daten eine Momentaufnahme in der Cloud erstellen, ehe Sie ein Gerät deaktivieren. Mithilfe einer solchen Cloudmomentaufnahme können Sie alle Daten zu einem späteren Zeitpunkt wiederherstellen.

> [!NOTE]
>
> - Bevor Sie ein physisches StorSimple-Gerät oder eine Cloud Appliance deaktivieren, stellen Sie sicher, dass die Daten im gelöschten Volumecontainer auch tatsächlich vom Gerät gelöscht werden. Sie können die Diagramme für die Cloudnutzung überwachen, und wenn Sie feststellen, dass die Nutzung aufgrund der gelöschten Sicherungen rapide sinkt, können Sie das Gerät deaktivieren. Wenn Sie das Gerät deaktivieren, bevor dieses Absinken auftritt, verbleiben die Daten im Speicherkonto und verursachen Gebühren.
>
> - Vor dem Deaktivieren eines physischen StorSimple-Geräts oder Cloudgeräts müssen alle von diesem Gerät abhängigen Clients und Hosts beendet oder entfernt werden.
>
> - Wenn Speicherkonten oder Container im Speicherkonto, die Volumecontainern zugeordnet sind, bereits vor dem Löschen der Daten vom Gerät gelöscht wurden, erhalten Sie eine Fehlermeldung und können die Daten möglicherweise nicht löschen. Es empfiehlt sich, die Daten auf dem Gerät zu löschen, bevor Sie das Speicherkonto oder darin enthaltene Container löschen. Allerdings müssen Sie in dieser Situation mit der Deaktivierung und Löschung von Geräten in der Annahme fortfahren, dass die Daten bereits aus dem Speicherkonto entfernt wurden.

In diesem Tutorial lernen Sie Folgendes:

- Deaktivieren eines Geräts und Löschen der Daten
- Deaktivieren eines Geräts und Beibehalten der Daten

## <a name="deactivate-and-delete-data"></a>Deaktivieren und Löschen von Daten

Wenn Sie das Gerät vollständig löschen und die Daten auf dem Gerät nicht beibehalten möchten, führen Sie die folgenden Schritte aus:

### <a name="to-deactivate-the-device-and-delete-the-data"></a>So deaktivieren Sie ein Gerät und löschen die Daten

1. Vor dem Deaktivieren eines Geräts müssen Sie alle Volumecontainer (und die Volumes) löschen, die dem Gerät zugeordnet sind. Sie können Volumecontainer nur löschen, nachdem Sie die zugeordneten Sicherungen gelöscht haben. Lesen Sie den Hinweis in der Übersicht oben, bevor Sie ein physisches StorSimple-Gerät oder eine Cloud Appliance deaktivieren.

2. Deaktivieren Sie das Gerät wie folgt:

   1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**. Klicken Sie auf dem Blatt **Geräte** auf das Gerät, das Sie deaktivieren möchten. Klicken Sie mit der rechten Maustaste, und klicken Sie dann auf **Deaktivieren**.

        ![Deaktivieren des StorSimple-Geräts](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Geben Sie auf dem Blatt **Deaktivieren** den zu bestätigenden Gerätenamen ein, und klicken Sie anschließend auf **Deaktivieren**. Der Deaktivierungsvorgang beginnt und kann einige Minuten in Anspruch nehmen.

        ![Deaktivieren des StorSimple-Geräts 2](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Sie können nach der Deaktivierung das Gerät vollständig löschen. Das Löschen eines Geräts entfernt es aus der Liste der mit dem Dienst verbundenen Geräte. Der Dienst kann das gelöschte Gerät dann nicht mehr länger verwalten. Führen Sie die folgenden Schritte aus, um das Gerät zu löschen:
   
   1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**. Wählen Sie auf dem Blatt **Geräte** das deaktivierte Gerät aus, das Sie löschen möchten. Klicken Sie mit der rechten Maustaste, und klicken Sie dann auf **Löschen**.

        ![Deaktivieren des StorSimple-Geräts 3](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Geben Sie auf dem Blatt **Löschen** den zu bestätigenden Gerätenamen ein, und klicken Sie anschließend auf **Löschen**. Der Löschvorgang kann einige Minuten dauern.

        ![Deaktivieren des StorSimple-Geräts 4](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Nachdem der Löschvorgang erfolgreich abgeschlossen wurde, werden Sie benachrichtigt. Die Geräteliste wird dem Löschvorgang entsprechend aktualisiert.

## <a name="deactivate-and-retain-data"></a>Deaktivieren und Beibehalten von Daten

Wenn Sie das Gerät löschen, aber die Daten beibehalten möchten, führen Sie die folgenden Schritte aus:

### <a name="to-deactivate-a-device-and-retain-the-data"></a>So deaktivieren Sie ein Gerät und behalten die Daten bei

1. Deaktivieren Sie das Gerät. Alle Volumecontainer und die Momentaufnahmen des Geräts bleiben erhalten.
   
   1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**. Klicken Sie auf dem Blatt **Geräte** auf das Gerät, das Sie deaktivieren möchten. Klicken Sie mit der rechten Maustaste, und klicken Sie dann auf **Deaktivieren**.

         ![Deaktivieren des StorSimple-Geräts 5](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Geben Sie auf dem Blatt **Deaktivieren** den zu bestätigenden Gerätenamen ein, und klicken Sie anschließend auf **Deaktivieren**. Der Deaktivierungsvorgang beginnt und kann einige Minuten in Anspruch nehmen.

         ![Deaktivieren des StorSimple-Geräts 6](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Sie können nun ein Failover der Volumecontainer und der zugehörigen Momentaufnahmen durchführen. Informationen zu den Verfahren finden Sie unter [Ausführen eines Failovers und einer Notfallwiederherstellung für das StorSimple-Gerät](storsimple-8000-device-failover-disaster-recovery.md).
3. Sie können nach der Deaktivierung und der Ausführung des Failovers das Gerät vollständig löschen. Das Löschen eines Geräts entfernt es aus der Liste der mit dem Dienst verbundenen Geräte. Der Dienst kann das gelöschte Gerät dann nicht mehr länger verwalten. Führen Sie die folgenden Schritte aus, um das Gerät zu löschen:
   
   1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**. Wählen Sie auf dem Blatt **Geräte** das deaktivierte Gerät aus, das Sie löschen möchten. Klicken Sie mit der rechten Maustaste, und klicken Sie dann auf **Löschen**.

       ![Deaktivieren des StorSimple-Geräts 7](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Geben Sie auf dem Blatt **Löschen** den zu bestätigenden Gerätenamen ein, und klicken Sie anschließend auf **Löschen**. Der Löschvorgang kann einige Minuten dauern.

       ![Deaktivieren des StorSimple-Geräts 8](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Nachdem der Löschvorgang erfolgreich abgeschlossen wurde, werden Sie benachrichtigt. Die Geräteliste wird dem Löschvorgang entsprechend aktualisiert.

## <a name="deactivate-and-delete-a-cloud-appliance"></a>Deaktivieren und Löschen eines Cloudgeräts

Bei einer StorSimple Cloud Appliance erfolgt bei einer Deaktivierung im Portal das Aufheben der Zuordnung und Löschen des virtuellen Computers und der Ressourcen, die bei seiner Bereitstellung erstellt wurden. Nach der Deaktivierung kann das Cloudgerät nicht im vorherigen Zustand wiederhergestellt werden.

![Deaktivieren einer StorSimple Cloud Appliance](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Beim Deaktivieren werden die folgenden Aktionen ausgeführt:

* Die StorSimple Cloud Appliance wird aus dem Dienst entfernt.
* Der virtuelle Computer der StorSimple-Appliance Cloud wird gelöscht.
* Der für die StorSimple-Cloudappliance erstellte Betriebssystemdatenträger und die Datenträger für Daten werden entfernt. Wenn diese Entitäten nicht verwendet werden, sollten sie manuell gelöscht werden.
* Der bei der Bereitstellung erstellte gehostete Dienst und das virtuelle Netzwerk bleiben erhalten. Wenn diese Entitäten nicht verwendet werden, sollten sie manuell gelöscht werden.
* Von der StorSimple Cloud Appliance erstellte Cloudmomentaufnahmen werden beibehalten.

Nachdem das Cloudgerät deaktiviert wurde, können Sie es aus der Liste der Geräte löschen. Wählen Sie das deaktivierte Gerät aus, klicken Sie mit der rechten Maustaste darauf, und klicken Sie dann auf **Löschen**. StorSimple benachrichtigt Sie, sobald das Gerät gelöscht wurde. Die Liste der Geräte wird entsprechend aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

* Um das deaktivierte Gerät mit den Werkseinstellungen wiederherzustellen, wechseln Sie zu [Zurücksetzen des Geräts auf die Standardwerkseinstellungen](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Um technische Unterstützung zu erhalten, [kontaktieren Sie den Microsoft-Support](storsimple-8000-contact-microsoft-support.md).
* Weitere Informationen zum Verwenden des StorSimple-Geräte-Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-GeräteManager-Diensts](storsimple-8000-manager-service-administration.md).

