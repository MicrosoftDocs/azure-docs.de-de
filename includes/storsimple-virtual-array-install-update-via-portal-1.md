---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005814"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>So installieren Sie Updates über das Azure-Portal

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager, und wählen Sie **Devices** (Geräte) aus. Wählen Sie aus der Liste der mit dem Dienst verbundenen Geräte das Gerät aus, das Sie aktualisieren möchten, und klicken Sie darauf.

    ![Unter „Recent“ (Zuletzt verwendet) ist der Geräte-Manager MySSDevManager hervorgehoben und ausgewählt, „Devices“ (Geräte) hervorgehoben und ausgewählt, und das Gerät MYSSIS1103 ist hervorgehoben und ausgewählt.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Klicken Sie auf dem Blatt **Settings** (Einstellungen) auf **Device updates** (Geräteupdates).

    ![Informationen zu MYSSIS1103 werden angezeigt. Unter „Settings“ (Einstellungen) ist „Device Updates“ (Geräteupdates) hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Wenn Softwareupdates verfügbar sind, wird eine Meldung angezeigt. Sie können auch auf **Scan** (Überprüfen) klicken, um nach Updates zu suchen. Notieren Sie sich die Softwareversion, die Sie ausführen. 

    ![Die Meldungen im Bereich „Device updates“ (Geräteupdates) lauten: „New updates are available“ und „Last scanned/11/01/2017 10:56 PM/Software version/10.0.10289.0“ (Neue Updates verfügbar, Letzte Überprüfung/1.11.2017 10:56 Uhr/Softwareversion/10.0.10289.0). Die Version wird hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Sie werden jeweils benachrichtigt, wenn die Überprüfung beginnt und erfolgreich abgeschlossen wurde.

    ![Die Benachrichtigung lautet: „Scanning updates for device MySVAFS110… 10:57 AM / The operation is in progress.“ (Updates für Gerät MySVAFS110 werden überprüft...10:57 Uhr / Der Vorgang wird ausgeführt.“)](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Wenn die Updatesuche abgeschlossen wurde, klicken Sie auf **Download updates** (Updates herunterladen).

    ![Im Bereich „The Device updates“ (Das Gerät wird aktualisiert) wird die Meldung „New updates are available“ (Neue Updates sind verfügbar) angezeigt. Die Schaltfläche „Download updates“ (Updates herunterladen) wird hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Im Blatt **Neue Updates**, überprüfen Sie die Anmerkungen zu dieser Version. Beachten Sie, dass Sie, nachdem die Updates heruntergeladen wurden, die Installation bestätigen müssen. Klicken Sie auf **OK**.

    ![Im Bereich „New updates“ (Neue Updates) wird die folgende Meldung angezeigt: „After the updates are downloaded, you will need to confirm the installation“ (Nachdem die Updates heruntergeladen wurden, müssen Sie die Installation bestätigen). Die Schaltfläche „OK“ ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Sie werden jeweils benachrichtigt, wenn das Hochladen beginnt und erfolgreich abgeschlossen wurde.

     ![Die Benachrichtigung lautet: „Downloading updates for device MySSIS… 11:07 AM“ (Updates für Gerät MySSIS werden heruntergeladen...11:07 Uhr).](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Klicken Sie im Blatt **Device updates** auf **Install** (Installieren).

     ![Auf „Device updates“ (Geräteupdates) wird „Confirm installing updates“ (Installation von Updates bestätigen) angezeigt. Die Schaltfläche „Install“ (Installieren) ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Auf dem Blatt **New updates** (Neue Updates) werden Sie gewarnt, dass das Update zu einer Unterbrechung führt. Da es sich bei Virtual Array um ein Gerät mit einem Einzelknoten handelt, wird das Gerät nach dem Update neu gestartet. Dadurch werden alle laufenden E/A-Vorgänge unterbrochen. Klicken Sie auf **OK**, um die Updates zu installieren.

    ![Die Meldung im Bereich „New updates“ (Neue Updates) lautet: „Your device will have a downtime when these updates are installed“ (Für Ihr Gerät tritt bei der Installation dieser Updates eine Ausfallzeit auf). Die Schaltfläche „OK“ ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Sie werden benachrichtigt, wenn der Installationsauftrag beginnt.

    ![Die Benachrichtigung lautet: „Installing updates for device MySVAFS110... 11:09 AM“ (Updates für Gerät MySVAFS110 werden installiert...11:09 Uhr).](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Klicken Sie nach erfolgreichem Beendigen des Installationsauftrags im Blatt **Device updates** (Geräteupdates) auf den Link **View Job** (Auftrag anzeigen), um die Installation zu überwachen. 

    ![Der Bereich „Device updates“ (Geräteupdates) enthält den Link „Installing updates. View Job“ (Updates werden installiert. Auftrag anzeigen). Die Schaltfläche „Install“ (Installieren) ist hervorgehoben.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Durch diese Aktion gelangen Sie auf das Blatt **Install Updates** (Updates installieren). Hier finden Sie ausführliche Informationen zum Auftrag.

    ![Der Bereich „Install Updates“ (Updates installieren) enthält Installationsinformationen, einschließlich Gerät, Status, Dauer, Startzeit und Endzeit.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Wenn Sie mit einem virtuellen Array mit Softwareversion Update 0.6 (10.0.10293.0) begonnen haben, führen Sie jetzt Update 1 aus, danach sind Sie fertig. Sie können die verbleibenden Schritte überspringen. Wenn Sie mit einem virtuellen Array mit einer älteren Softwareversion als Update 0.6 (10.0.10293.0) gestartet sind, werden Sie jetzt auf Update 0.6 aktualisiert und sind fertig. Daraufhin wird eine andere Meldung angezeigt, die angibt, dass Updates verfügbar sind. Wiederholen Sie die Schritte 4 bis 8, um Update 1 zu installieren.

    ![Die Meldungen im Bereich „Device updates“ (Geräteupdates) lauten: „New updates are available“ und „Last scanned/11/1/2017 10:56 PM/Software version/10.0.10293.0“ (Neue Updates verfügbar, Letzte Überprüfung/1.11.2017 10:56 Uhr/Softwareversion/10.0.10293.0).](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

