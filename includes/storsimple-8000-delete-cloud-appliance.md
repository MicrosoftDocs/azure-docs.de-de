---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ac708eb2ac79a74b8f4e09a7306a42665b3aca94
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178148"
---
#### <a name="to-delete-a-cloud-appliance"></a>So löschen Sie ein Cloudgerät

1. Melden Sie sich beim Azure-Portal an.
2. Sie können nur deaktivierte Geräte löschen, die keine Daten enthalten. Löschen Sie zuerst die Daten auf dem Gerät. Alternativ können Sie ein [Failover der Daten](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md) in Volumecontainern auf ein anderes Gerät ausführen. Nach dem Löschen der Daten können Sie das Gerät deaktivieren.
3. Klicken Sie auf der Seite des StorSimple-Geräte-Managers auf **Geräte**, und wählen Sie anschließend das Gerät aus. Klicken Sie mit der rechten Maustaste, und wählen Sie **Deaktivieren** aus.
4. Klicken Sie nach dem Deaktivieren des Geräts mit der rechten Maustaste auf das Gerät, und wählen Sie **Löschen** aus.

    ![Wählen Sie das deaktivierte Gerät aus, und klicken Sie auf „Löschen“.](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. Geben Sie den Gerätenamen ein, um den Löschvorgang zu bestätigen. Nach dem Löschen des Geräts wird die Geräteliste aktualisiert.

    ![Löschvorgang bestätigen](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. Sie werden benachrichtigt, nachdem das Gerät gelöscht wurde.

    ![Benachrichtigung nach erfolgreichem Löschen des Geräts](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. Die Geräteliste wird aktualisiert, und das gelöschte Gerät wird nicht mehr angezeigt.

    ![Aktualisierte Geräteliste](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
