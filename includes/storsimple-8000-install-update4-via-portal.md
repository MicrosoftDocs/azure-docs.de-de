---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c3cfab78ab4bcc92b3e65b5e4fa04a29bf688de0
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376220"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>So installieren Sie ein Update über das Azure-Portal

1. Wählen Sie auf der Seite des StorSimple-Diensts Ihr Gerät aus.

    ![Gerät auswählen](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Navigieren Sie zu **Geräteeinstellungen** > **Geräteupdates**.

    ![Klicken Sie auf „Geräteupdates“.](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Eine Benachrichtigung wird angezeigt, wenn neue Updates verfügbar sind. Alternativ können Sie auf dem Blatt **Geräteupdates** auf **Updates scannen** klicken. Ein Auftrag für die Suche nach verfügbaren Updates wird erstellt. Sie werden benachrichtigt, wenn der Auftrag erfolgreich abgeschlossen wurde.

    ![Klicken auf „Geräteupdates“ 2](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Es empfiehlt sich, die Versionshinweise zu lesen, bevor Sie ein Update auf Ihr Gerät anwenden. Klicken Sie zum Anwenden von Updates auf **Updates installieren**. Überprüfen Sie auf dem Blatt **Reguläre Updates bestätigen** die Voraussetzungen, bevor Sie Updates anwenden. Aktivieren Sie das Kontrollkästchen, um anzugeben, dass Sie bereit sind, das Gerät zu aktualisieren, und klicken Sie dann auf **Installieren**.

    ![Klicken auf „Geräteupdates“ 3](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. Eine Reihe von Voraussetzungsüberprüfungen wird gestartet. Diese Prüfungen umfassen:
   
   * **Controller Integritätsprüfungen** , um sicherzustellen, dass die beiden Gerätecontroller fehlerfrei und online sind.
   * **Integritätsprüfungen für Hardware-Komponente** , um zu überprüfen, ob alle Hardwarekomponenten auf dem StorSimple-Gerät fehlerfrei sind.
   * **DATA 0-Prüfungen** , um sicherzustellen, dass DATA 0 auf Ihrem Gerät aktiviert ist. Wenn diese Schnittstelle nicht aktiviert ist, müssen Sie sie aktivieren, und es dann erneut versuchen.

     Das Update wird nur heruntergeladen und installiert, wenn alle Überprüfungen erfolgreich abgeschlossen wurden. Sie werden benachrichtigt, wenn die Prüfungen ausgeführt werden. Wenn bei den Vorüberprüfungen Fehler auftreten, werden die Fehlergründe angegeben. Beheben Sie diese Probleme, und wiederholen Sie den Vorgang anschließend. Möglicherweise müssen Sie den Microsoft Support kontaktieren, wenn Sie diese Probleme nicht selbst beheben können.

7. Wenn die Vorüberprüfungen erfolgreich abgeschlossen wurden, wird ein Updateauftrag erstellt. Sie werden benachrichtigt, wenn der Updateauftrag erfolgreich erstellt wurde.
   
    ![Erstellen eines Updateauftrags](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    Das Update wird dann auf Ihr Gerät angewendet.

9. Das Update dauert einige Stunden. Durch Auswählen des Updateauftrags und Klicken auf **Details** können Sie jederzeit die Details des Auftrags anzeigen.

    ![Erstellen eines Updateauftrags 2](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Sie können den Fortschritt des Updateauftrags auch über **Geräteeinstellungen > Aufträge** überwachen. Auf dem Blatt **Aufträge** sehen Sie den Updatestatus.

     ![Erstellen eines Updateauftrags 3](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. Nachdem der Auftrag abgeschlossen ist, navigieren Sie zu **Geräteeinstellungen > Geräteupdates**. Die Softwareversion sollte jetzt aktualisiert sein.

   ![Erstellen eines Updateauftrags 4](./media/storsimple-8000-install-update4-via-portal/update9.png)

