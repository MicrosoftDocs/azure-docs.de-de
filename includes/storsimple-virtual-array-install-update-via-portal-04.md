---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026794"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>So installieren Sie Updates über das Azure-Portal

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager, und wählen Sie **Devices** (Geräte) aus. Wählen Sie aus der Liste der mit dem Dienst verbundenen Geräte das Gerät aus, das Sie aktualisieren möchten, und klicken Sie darauf. 

    ![Unter „Recent“ (Zuletzt verwendet) ist der Geräte-Manager MySSDevManager hervorgehoben und ausgewählt, „Devices“ (Geräte) hervorgehoben und ausgewählt, und das Gerät MYSSIS1103 ist hervorgehoben und ausgewählt.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Klicken Sie auf dem Blatt **Settings** (Einstellungen) auf **Device updates** (Geräteupdates). 

    ![Informationen zu MYSSIS1103 werden angezeigt. Unter „Settings“ (Einstellungen) ist „Device Updates“ (Geräteupdates) hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Wenn Softwareupdates verfügbar sind, wird eine Meldung angezeigt. Sie können auch auf **Scan** (Überprüfen) klicken, um nach Updates zu suchen.

    ![Die Meldungen im Bereich „Device updates“ (Geräteupdates) lauten: „New updates are available“ (Neue Updates verfügbar) und „Last scanned / 18/01/2017 2:42 PM / Software version / 10.0.10288.0“ (Letzte Überprüfung/18.01.2017 14:42 Uhr/Softwareversion/10.0.10288.0). Die Version wird hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    Sie werden jeweils benachrichtigt, wenn die Überprüfung beginnt und erfolgreich abgeschlossen wurde.

    ![Die Benachrichtigung lautet: „Scanning updates for device MySSIS1103. 2:12 PM / Successfully complete the operation.“ (Updates für Gerät „MySSIS1103“ werden überprüft. 14:12 Uhr/Der Vorgang wurde erfolgreich abgeschlossen.)](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. Wenn die Updatesuche abgeschlossen wurde, klicken Sie auf **Download updates** (Updates herunterladen). 

    ![Im Bereich „The Device updates“ (Das Gerät wird aktualisiert) wird die Meldung „New updates are available“ (Neue Updates sind verfügbar) angezeigt. Die Schaltfläche „Download updates“ (Updates herunterladen) wird hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. Lesen Sie auf dem Blatt **New updates** (Neue Updates) die Hinweise, dass nach dem Download von Updates die Installation bestätigt werden muss. Klicken Sie auf **OK**.

    ![Im Bereich „New updates“ (Neue Updates) wird die folgende Meldung angezeigt: „After the updates are downloaded, you will need to confirm the installation“ (Nachdem die Updates heruntergeladen wurden, müssen Sie die Installation bestätigen). Die Schaltfläche „OK“ ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. Sie werden jeweils benachrichtigt, wenn das Hochladen beginnt und erfolgreich abgeschlossen wurde.

     ![Die Benachrichtigung lautet: „Downloading updates for device MySSIS1… 2:13 PM“ (Updates für Gerät MySSIS werden heruntergeladen... 2:13 Uhr).](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. Klicken Sie im Blatt **Device updates** auf **Install** (Installieren).

     ![Unter „Geräteupdates“ wird „Installieren von Updates bestätigen“ angezeigt. Es gibt eine Schaltfläche zum Installieren.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. Auf dem Blatt **New updates** (Neue Updates) werden Sie gewarnt, dass das Update zu einer Unterbrechung führt. Da es sich bei Virtual Array um ein Gerät mit einem Einzelknoten handelt, wird das Gerät nach dem Update neu gestartet. Dadurch werden alle laufenden E/A-Vorgänge unterbrochen. Klicken Sie auf **OK**, um die Updates zu installieren. 

    ![Die Meldung im Bereich „New updates (Neue Updates) lautet: „Your device will have a downtime when these updates are installed“ (Für Ihr Gerät tritt bei der Installation dieser Updates eine Ausfallzeit auf). Die Schaltfläche „OK“ ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. Sie werden benachrichtigt, wenn der Installationsauftrag beginnt. 

    ![Die Benachrichtigung lautet: „Installing updates for device MySSIS1103. 2:15 PM“ (Updates für Gerät MySSIS1103 werden installiert. 14:15 Uhr).](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  Klicken Sie nach erfolgreichem Beendigen des Installationsauftrags im Blatt **Device updates** (Geräteupdates) auf den Link **View Job** (Auftrag anzeigen), um die Installation zu überwachen. 

    ![Der Bereich „Device updates“ (Geräteupdates) enthält den Link „Installing updates. View Job“ (Updates werden installiert. Auftrag anzeigen).](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Dadurch gelangen Sie auf das Blatt **Install Updates** (Updates installieren). Hier finden Sie ausführliche Informationen zum Auftrag.

    ![Der Bereich „Install Updates“ (Updates installieren) enthält Installationsinformationen, einschließlich Gerät, Status, Dauer, Startzeit und Endzeit.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. Nach erfolgreicher Installation der Updates wird auf dem Blatt **Geräteupdates** eine entsprechende Benachrichtigung angezeigt. 
