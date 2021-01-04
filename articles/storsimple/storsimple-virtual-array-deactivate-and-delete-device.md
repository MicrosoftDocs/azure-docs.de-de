---
title: Deaktivieren und Löschen eines Microsoft Azure StorSimple Virtual Array | Microsoft Docs
description: Beschreibt, wie ein StorSimple-Gerät aus dem Dienst entfernt wird, indem es zunächst deaktiviert und anschließend gelöscht wird.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 84fd1a2d3d0dc4ce6960469ea3212b8ca1d4d07c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023022"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Deaktivieren und Löschen eines StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Wenn Sie ein StorSimple Virtual Array deaktivieren, trennen Sie die Verbindung zwischen dem Gerät und dem entsprechenden StorSimple-Geräte-Manager-Dienst. In diesem Tutorial werden folgende Punkte erläutert:

* Deaktivieren eines Geräts 
* Löschen eines deaktivierten Geräts

Die in diesem Artikel veröffentlichten Informationen gelten nur für StorSimple Virtual Arrays. Informationen zur 8000-Serie finden Sie unter [Deaktivieren und Löschen eines Geräts](./storsimple-8000-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Wann sollte deaktiviert werden?

Die Deaktivierung ist ein ENDGÜLTIGER Vorgang und kann nicht rückgängig gemacht werden. Ein deaktiviertes Gerät kann nicht erneut für den StorSimple-Geräte-Manager-Dienst registriert werden. In den folgenden Fällen müssen Sie ein StorSimple Virtual Array möglicherweise deaktivieren und löschen:

* **Geplantes Failover**: Ihr Gerät ist online, und Sie planen das Failover für dieses Gerät. Dies kann erforderlich sein, wenn Sie ein Upgrade auf ein größeres Gerät planen. Nachdem die Dateneigentümerschaft übertragen wurde und das Failover abgeschlossen ist, wird das Quellgerät automatisch gelöscht.
* **Ungeplantes Failover**: Ihr Gerät ist offline, und Sie planen das Failover für dieses Gerät. Dieses Szenario kann während eines Notfalls eintreten, wenn es zu einem Ausfall im Datencenter kommt und Ihr primäres Gerät nicht verfügbar ist. Sie planen, das Failover über das Gerät auf ein sekundäres Gerät durchzuführen. Nachdem die Dateneigentümerschaft übertragen wurde und das Failover abgeschlossen ist, wird das Quellgerät automatisch gelöscht.
* **Außer Betrieb setzen**: Sie möchten das Gerät außer Betrieb setzen. Dazu müssen Sie das Gerät zunächst deaktivieren und anschließend löschen. Wenn Sie ein Gerät deaktivieren, sind die lokal gespeicherten Daten nicht mehr zugänglich. Sie können nur auf Daten zugreifen und sie wiederherstellen, die in der Cloud gespeichert sind. Wenn Sie planen, die Gerätedaten nach der Deaktivierung aufzubewahren, sollten Sie eine Cloudmomentaufnahme aller Daten erstellen, bevor Sie ein Gerät deaktivieren. Mithilfe einer solchen Cloudmomentaufnahme können Sie alle Daten zu einem späteren Zeitpunkt wiederherstellen.

## <a name="deactivate-a-device"></a>Deaktivieren eines Geräts

Führen Sie die folgenden Schritte aus, um Ihr Gerät zu deaktivieren.

#### <a name="to-deactivate-the-device"></a>So deaktivieren Sie das Gerät

1. Wechseln Sie in Ihrem Dienst zu **Management > Geräte**. Klicken Sie auf dem Blatt **Geräte** auf das Gerät, das Sie deaktivieren möchten, und wählen Sie es aus.
   
    ![Zu deaktivierendes Gerät auswählen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Klicken Sie auf Ihrem Blatt **Gerätedashboard** auf **... More** (... Weitere), und wählen Sie aus der Liste **Deaktivieren** aus.
   
    ![Auf „Deaktivieren“ klicken](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Geben Sie auf dem Blatt **Deaktivieren** den Gerätenamen ein, und klicken Sie anschließend auf **Deaktivieren**. 
   
    ![Deaktivierung bestätigen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Der Deaktivierungsvorgang beginnt und kann einige Minuten in Anspruch nehmen.
   
    ![Deaktivierung wird durchgeführt](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Nach der Deaktivierung wird die Liste mit den Geräten aktualisiert.
   
    ![Vollständig deaktivieren](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Sie können das Gerät jetzt löschen.

## <a name="delete-the-device"></a>Löschen des Geräts

Ein Gerät muss zuerst deaktiviert werden, bevor es gelöscht werden kann. Das Löschen eines Geräts entfernt es aus der Liste der mit dem Dienst verbundenen Geräte. Der Dienst kann das gelöschte Gerät dann nicht mehr länger verwalten. Die Daten, die dem Gerät zugeordnet sind, verbleiben jedoch in der Cloud. Für diese Daten fallen dann Gebühren an.

Führen Sie die folgenden Schritte aus, um das Gerät zu löschen.

#### <a name="to-delete-the-device"></a>So löschen Sie das Gerät

1. Wechseln Sie in Ihrem StorSimple-Geräte-Manager-Dienst zu **Management > Geräte**. Wählen Sie auf dem Blatt **Geräte** ein deaktiviertes Gerät aus, das Sie löschen möchten.
2. Klicken Sie auf dem Blatt **Gerätedashboard** auf **… More** (... Weitere), und klicken Sie anschließend auf **Löschen**.
   
   ![Zu löschendes Gerät auswählen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Geben Sie auf dem Blatt **Löschen** den Namen Ihres Geräts ein, um den Löschvorgang zu bestätigen, und klicken Sie anschließend auf **Löschen**. Beim Löschen des Geräts werden die Clouddaten, die dem Gerät zugeordnet sind, nicht gelöscht. 
   
   ![Bestätigen des Löschens](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Der Löschvorgang beginnt und kann einige Minuten in Anspruch nehmen.
   
   ![Löschvorgang wird durchgeführt](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Nachdem das Gerät gelöscht wurde, können Sie die aktualisierte Liste der Geräte anzeigen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Failover finden Sie unter [Notfallwiederherstellung und Gerätefailover für das StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md).

* Weitere Informationen zum Verwenden des StorSimple-Geräte-Manager-Diensts finden Sie unter [Use the StorSimple Device Manager service to administer your StorSimple Virtual Array (Verwalten des StorSimple Virtual Array mithilfe des StorSimple Manager-Diensts)](storsimple-virtual-array-manager-service-administration.md).