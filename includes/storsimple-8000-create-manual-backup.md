---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 89f7be31514f0b78c3bfb3efd6e6aca14658d5cd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376217"
---
#### <a name="to-create-a-manual-backup"></a>So erstellen Sie eine manuelle Sicherung

1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Geräte**. Wählen Sie Ihr Gerät in der Tabelle mit den Geräten aus. Navigieren Sie zu **Einstellungen > Verwalten > Sicherungsrichtlinien**.

2. Auf dem Blatt **Sicherungsrichtlinien** sind alle Sicherungsrichtlinien in einem Tabellenformat aufgelistet, darunter auch die Richtlinie für das zu sichernde Volume. Wählen Sie die Richtlinie, die dem zu sichernden Volume zugeordnet ist, und klicken Sie mit der rechten Maustaste, um das Kontextmenü aufzurufen. Wählen Sie in der Dropdownliste die Option **Jetzt sichern**.

    ![Erstellen einer manuellen Sicherung](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. Führen Sie auf dem Blatt **Jetzt sichern** die folgenden Schritte aus:

    1. Wählen Sie in der Dropdownliste den passenden **Momentaufnahmetyp** aus: **Lokal** oder **Cloud**. Wählen Sie für schnelle Sicherungen oder Wiederherstellungen die lokale Momentaufnahme, und wählen Sie die Cloudmomentaufnahme, um mehr Datenresilienz zu erzielen.

        ![Erstellen einer manuellen Sicherung 2](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Klicken Sie auf **OK**, um einen Auftrag zum Erstellen einer Momentaufnahme zu starten. Am oberen Rand der Seite wird eine Benachrichtigung angezeigt, nachdem der Auftrag erfolgreich erstellt wurde.

        ![Erstellen einer manuellen Sicherung 3](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Klicken Sie auf die Benachrichtigung, um den Auftrag zu überwachen. Sie gelangen auf das Blatt **Aufträge**, auf dem Sie den Auftragsstatus anzeigen können.


5. Nachdem der Sicherungsauftrag abgeschlossen wurde, navigieren Sie zur Registerkarte **Sicherungskatalog** .

6. Legen Sie die Filterauswahl auf das entsprechende Gerät, die Sicherungsrichtlinie und den Zeitraum fest. Die Sicherung sollte in der Liste der Sicherungssätze enthalten sein, die im Katalog angezeigt wird.

