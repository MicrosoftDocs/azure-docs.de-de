---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 37d4c295fe8a89a8b32364f42e8fb4c5a4caf777
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178614"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>So installieren Sie Updates über das Azure-Portal

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager, und wählen Sie **Devices** (Geräte) aus. Wählen Sie aus der Liste der mit dem Dienst verbundenen Geräte das Gerät aus, das Sie aktualisieren möchten, und klicken Sie darauf. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. Klicken Sie auf dem Blatt **Settings** (Einstellungen) auf **Device updates** (Geräteupdates). 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Wenn Softwareupdates verfügbar sind, wird eine Meldung angezeigt. Sie können auch auf **Scan** (Überprüfen) klicken, um nach Updates zu suchen.

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Sie werden jeweils benachrichtigt, wenn die Überprüfung beginnt und erfolgreich abgeschlossen wurde.

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Wenn die Updatesuche abgeschlossen wurde, klicken Sie auf **Download updates** (Updates herunterladen). 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. Lesen Sie auf dem Blatt **New updates** (Neue Updates) die Hinweise, dass nach dem Download von Updates die Installation bestätigt werden muss. Klicken Sie auf **OK**.

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. Sie werden jeweils benachrichtigt, wenn das Hochladen beginnt und erfolgreich abgeschlossen wurde.

     ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. Klicken Sie im Blatt **Device updates** auf **Install** (Installieren).

     ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. Auf dem Blatt **New updates** (Neue Updates) werden Sie gewarnt, dass das Update zu einer Unterbrechung führt. Da es sich bei Virtual Array um ein Gerät mit einem Einzelknoten handelt, wird das Gerät nach dem Update neu gestartet. Dadurch werden alle laufenden E/A-Vorgänge unterbrochen. Klicken Sie auf **OK**, um die Updates zu installieren. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Sie werden benachrichtigt, wenn der Installationsauftrag beginnt. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  Klicken Sie nach erfolgreichem Beendigen des Installationsauftrags im Blatt **Device updates** (Geräteupdates) auf den Link **View Job** (Auftrag anzeigen), um die Installation zu überwachen. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Dadurch gelangen Sie auf das Blatt **Install Updates** (Updates installieren). Hier finden Sie ausführliche Informationen zum Auftrag.

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. Nachdem die Updates erfolgreich installiert wurden, wird eine Benachrichtigung diesbezüglich im Blatt „Device updates“ angezeigt. Die Softwareversion ändert sich auch auf **10.0.10288.0**. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)