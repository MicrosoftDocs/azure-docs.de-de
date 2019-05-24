---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 6f2ad6426c2c93fa595648af374df5566c45e57f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157780"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>So installieren Sie Updates über das Azure-Portal

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager, und wählen Sie **Devices** (Geräte) aus. Wählen Sie aus der Liste der mit dem Dienst verbundenen Geräte das Gerät aus, das Sie aktualisieren möchten, und klicken Sie darauf. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Klicken Sie auf dem Blatt **Settings** (Einstellungen) auf **Device updates** (Geräteupdates). 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Wenn Softwareupdates verfügbar sind, wird eine Meldung angezeigt. Sie können auch auf **Scan** (Überprüfen) klicken, um nach Updates zu suchen.

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    Sie werden jeweils benachrichtigt, wenn die Überprüfung beginnt und erfolgreich abgeschlossen wurde.

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. Wenn die Updatesuche abgeschlossen wurde, klicken Sie auf **Download updates** (Updates herunterladen). 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. Lesen Sie auf dem Blatt **New updates** (Neue Updates) die Hinweise, dass nach dem Download von Updates die Installation bestätigt werden muss. Klicken Sie auf **OK**.

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. Sie werden jeweils benachrichtigt, wenn das Hochladen beginnt und erfolgreich abgeschlossen wurde.

     ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. Klicken Sie im Blatt **Device updates** auf **Install** (Installieren).

     ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. Auf dem Blatt **New updates** (Neue Updates) werden Sie gewarnt, dass das Update zu einer Unterbrechung führt. Da es sich bei Virtual Array um ein Gerät mit einem Einzelknoten handelt, wird das Gerät nach dem Update neu gestartet. Dadurch werden alle laufenden E/A-Vorgänge unterbrochen. Klicken Sie auf **OK**, um die Updates zu installieren. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. Sie werden benachrichtigt, wenn der Installationsauftrag beginnt. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  Klicken Sie nach erfolgreichem Beendigen des Installationsauftrags im Blatt **Device updates** (Geräteupdates) auf den Link **View Job** (Auftrag anzeigen), um die Installation zu überwachen. 

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Dadurch gelangen Sie auf das Blatt **Install Updates** (Updates installieren). Hier finden Sie ausführliche Informationen zum Auftrag.

    ![Gerät aktualisieren](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. Nach erfolgreicher Installation der Updates wird auf dem Blatt **Geräteupdates** eine entsprechende Benachrichtigung angezeigt. 