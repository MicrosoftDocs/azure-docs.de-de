---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95558873"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>So installieren Sie Updates über das Azure-Portal

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager, und wählen Sie **Devices** (Geräte) aus. Wählen Sie aus der Liste der mit dem Dienst verbundenen Geräte das Gerät aus, das Sie aktualisieren möchten, und klicken Sie darauf. 

    ![Unter „Recent“ (Zuletzt verwendet) ist der Geräte-Manager MySSDevManager hervorgehoben und ausgewählt, „Devices“ (Geräte) hervorgehoben und ausgewählt, und das Gerät MYSSIS1103 ist hervorgehoben und ausgewählt.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. Klicken Sie auf dem Blatt **Settings** (Einstellungen) auf **Device updates** (Geräteupdates). 

    ![Informationen zu MYSSIS1103 werden angezeigt. Unter „Settings“ (Einstellungen) ist „Device Updates“ (Geräteupdates) hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Wenn Softwareupdates verfügbar sind, wird eine Meldung angezeigt. Sie können auch auf **Scan** (Überprüfen) klicken, um nach Updates zu suchen.

    ![Im Bereich „Device Updates“ (Geräteupdates) ist die Schaltfläche „Scan“ (Überprüfen) hervorgehoben. Es wird eine vierzeilige Meldung angezeigt: Last scanned/Not scanned/Software version/10.0.10280.0 (Zuletzt überprüft/Nicht überprüft/Softwareversion/10.0.10280.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Sie werden jeweils benachrichtigt, wenn die Überprüfung beginnt und erfolgreich abgeschlossen wurde.

    ![Die Meldung lautet: „Scanning updates for device MySSIS1103.2:12 PM / Successfully completed the operation“ (Überprüfen auf Updates für das Gerät MySSIS1103.2:12/Der Vorgang wurde erfolgreich abgeschlossen).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Wenn die Updatesuche abgeschlossen wurde, klicken Sie auf **Download updates** (Updates herunterladen). 

    ![Die Meldungen im Bereich „Device updates“ (Geräteupdates) lauten: „New updates are available“ und „Last scanned/11/3/2016 2:12 PM/Software version/10.0.10280.0“ (Neue Updates verfügbar, Letzte Überprüfung/3.11.2016 2:12 Uhr/Softwareversion/10.0.10280.0). Die Version wird hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. Lesen Sie auf dem Blatt **New updates** (Neue Updates) die Hinweise, dass nach dem Download von Updates die Installation bestätigt werden muss. Klicken Sie auf **OK**.

    ![Im Bereich „New updates“ (Neue Updates) wird die folgende Meldung angezeigt: „After the updates are downloaded, you will need to confirm the installation“ (Nachdem die Updates heruntergeladen wurden, müssen Sie die Installation bestätigen). Die Schaltfläche „OK“ ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. Sie werden jeweils benachrichtigt, wenn das Hochladen beginnt und erfolgreich abgeschlossen wurde.

     ![Die Benachrichtigung lautet: „Downloading updates for device MySSIS… 2:13 PM“ (Updates für Gerät MySSIS werden heruntergeladen... 2:13 Uhr).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. Klicken Sie im Blatt **Device updates** auf **Install** (Installieren).

     ![Die Meldung im Bereich „Device updates“ (Geräteupdates) lautet: „Confirm installing updates“ (Installation der Updates bestätigen). Die Schaltfläche „Install“ (Installieren) ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. Auf dem Blatt **New updates** (Neue Updates) werden Sie gewarnt, dass das Update zu einer Unterbrechung führt. Da es sich bei Virtual Array um ein Gerät mit einem Einzelknoten handelt, wird das Gerät nach dem Update neu gestartet. Dadurch werden alle laufenden E/A-Vorgänge unterbrochen. Klicken Sie auf **OK**, um die Updates zu installieren. 

    ![Die Meldung im Bereich „New updates (Neue Updates) lautet: „Your device will have a downtime when these updates are installed“ (Für Ihr Gerät tritt bei der Installation dieser Updates eine Ausfallzeit auf). Die Schaltfläche „OK“ ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Sie werden benachrichtigt, wenn der Installationsauftrag beginnt. 

    ![Die Benachrichtigung lautet: „Installing updates for device MYSSIS1103. 2:15 PM (Updates für Gerät MYSSIS1103 werden installiert, 2:15 Uhr).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  Klicken Sie nach erfolgreichem Beendigen des Installationsauftrags im Blatt **Device updates** (Geräteupdates) auf den Link **View Job** (Auftrag anzeigen), um die Installation zu überwachen. 

    ![Im Bereich „Device updates“ (Geräteupdates) ist der Link „Installing updates. View Job“ (Updates werden installiert. Auftrag anzeigen) hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Dadurch gelangen Sie auf das Blatt **Install Updates** (Updates installieren). Hier finden Sie ausführliche Informationen zum Auftrag.

    ![Der Bereich „Install Updates“ (Updates installieren) enthält Installationsinformationen, einschließlich Gerät, Status, Dauer, Startzeit und Endzeit.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. Nachdem die Updates erfolgreich installiert wurden, wird eine Benachrichtigung diesbezüglich im Blatt „Device updates“ angezeigt. Die Softwareversion ändert sich auch auf **10.0.10288.0**. 

    ![Im Bereich „Device updates“ (Geräteupdates) wird eine Meldung angezeigt, dass Ihr Gerät auf dem neuesten Stand ist („Your device is up-to-date“), sowie die Softwareversion (10.0.10288.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
