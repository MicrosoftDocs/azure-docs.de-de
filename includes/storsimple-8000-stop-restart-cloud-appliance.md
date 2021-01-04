---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a01f91a81629800d3f03b907c65f05433b6163e6
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376199"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Beenden und starten eines Cloudgeräts

1. Zum Beenden eines Cloudgeräts wechseln Sie zum virtuellen Computer für Ihr Cloudgerät.
    ![StorSimple Cloud Appliance – virtueller Computer](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Klicken Sie auf der Befehlsleiste auf **Beenden**.

    ![StorSimple Cloud Appliance – virtueller Computer 2](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**.

    ![StorSimple Cloud Appliance – virtueller Computer 3](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Wenn Sie einen virtuellen Computer beenden, wird seine Zuordnung aufgehoben. Während das Cloudgerät beendet wird, weist es den Status **Zuordnung wird aufgehoben** auf. Nach dem Beenden des Cloudgeräts weist es den Status **Beendet (Zuordnung aufgehoben)** auf.

    ![StorSimple Cloud Appliance – virtueller Computer 4](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Sobald ein virtueller Computer beendet wurde, klicken Sie auf **Starten** (Schaltfläche wird verfügbar), um ihn zu starten. Nach dem Starten des Cloudgeräts weist des den Status **Gestartet** auf.

    ![StorSimple Cloud Appliance – virtueller Computer 5](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Verwenden Sie die folgenden Cmdlets zum Beenden und Starten eines Cloudgeräts.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>So starten Sie ein Cloudgerät neu

Zu Neustarten eines Cloudgeräts wechseln Sie zum virtuellen Computer für Ihr Cloudgerät. Klicken Sie auf der Befehlsleiste auf **Neu starten**. Bestätigen Sie den Neustart, wenn Sie dazu aufgefordert werden. Wenn das Cloudgerät zur Verwendung bereit ist, wird der Status **Wird ausgeführt** angezeigt.

![StorSimple Cloud Appliance – virtueller Computer 6](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Verwenden Sie die folgenden Cmdlets für den Neustart eines Cloudgeräts.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

