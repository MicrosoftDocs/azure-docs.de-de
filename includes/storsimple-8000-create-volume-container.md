---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4ca02b3d6f4dec805c6ce58d4470c4c8e5f3454f
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376067"
---
#### <a name="to-create-a-volume-container"></a>So erstellen Sie einen Volumecontainer
1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**. Wählen Sie aus der tabellarischen Auflistung von Geräten ein Gerät aus, und klicken Sie darauf. 

    ![Blatt „Volumecontainer“](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. Klicken Sie im Gerätedashboard auf **+ Volumecontainer hinzufügen**

    ![Blatt „Volumecontainer“ 2](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. Auf dem Blatt **Volumecontainer hinzufügen**:
   
   1. Das Gerät wird automatisch ausgewählt.
   2. Geben Sie einen **Namen** für den Volumecontainer an. Der Name muss 3 bis 32 Zeichen lang sein. Sie können einen Volumecontainer nach der Erstellung nicht mehr umbenennen.
   3. Wählen Sie **Cloud-Speicherverschlüsselung aktivieren** aus, um die Verschlüsselung der Daten zu aktivieren, die vom Gerät in die Cloud gesendet werden.
   4. Stellen Sie einen **Verschlüsselungsschlüssel für Cloud-Speicher** bereit, der 8 bis 32 Zeichen lang ist, und bestätigen Sie diesen. Dieser Schlüssel wird vom Gerät für den Zugriff auf verschlüsselte Daten verwendet.
   5. Wählen Sie ein **Speicherkonto** aus, das diesem Volumecontainer zugeordnet werden soll. Sie können ein vorhandenes Speicherkonto oder das Standardkonto auswählen, das beim Erstellen des Diensts generiert wird. Sie können auch die Option **Neu hinzufügen** verwenden, um ein Speicherkonto anzugeben, das nicht mit diesem Dienstabonnement verknüpft ist.
   6. Wählen Sie in der Dropdownliste **Bandbreite angeben** die Option **Unbegrenzt** aus, wenn Sie die gesamte verfügbare Bandbreite nutzen möchten. Sie können diese Option auch auf **Benutzerdefiniert** festlegen, um die Bandbreite zu steuern und einen Wert zwischen 1 und 1.000 MBit/s anzugeben.
      Wenn Ihnen Informationen zur Bandbreitenauslastung vorliegen, können Sie möglicherweise Bandbreite nach einem Zeitplan zuordnen, indem Sie **Bandbreitenvorlage auswählen** angeben. Eine detaillierte Anleitung finden Sie unter [Hinzufügen einer Bandbreitenvorlage](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Blatt „Volumecontainer“ 3](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Klicken Sie auf **Erstellen**.

        ![Blatt „Volumecontainer“ 4](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       Sie werden benachrichtigt, wenn der Volumecontainer erfolgreich erstellt wurde.

       ![Benachrichtigung über Erstellung des Volumecontainers](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   Der neu erstellte Volumecontainer wird in der Liste der Volumecontainer für Ihr Gerät aufgeführt.

   ![Blatt „Volumecontainer hinzufügen“](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


