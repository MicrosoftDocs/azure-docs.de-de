---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d6bad1ec584b28fee77bc654eba8a2d0c7b5df30
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66118323"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>So fügen Sie Anmeldeinformationen für ein Speicherkonto hinzu, das zum gleichen Azure-Abonnement wie der StorSimple-Geräte-Manager-Dienst gehört

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst. Klicken Sie im Abschnitt **Konfiguration** auf **Anmeldeinformationen des Speicherkontos**.

    ![Anmeldeinformationen des Speicherkontos](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. Klicken Sie auf dem Blatt **Anmeldeinformationen des Speicherkontos** auf **+ Hinzufügen**.

    ![Hinzufügen von Anmeldeinformationen eines Speicherkontos](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. Führen Sie auf dem Blatt **Anmeldeinformationen des Speicherkontos hinzufügen** folgende Schritte aus:

    1. Da Sie Anmeldeinformationen für ein Speicherkonto im gleichen Azure-Abonnement wie Ihr Dienst hinzufügen, stellen Sie sicher, dass **Aktuell** ausgewählt ist.

    2. Wählen Sie in der Dropdownliste **Speicherkonto** ein vorhandenes Speicherkonto aus.

    3. Basierend auf dem ausgewählten Speicherkonto wird der **Speicherort** angezeigt (abgeblendet und kann hier nicht geändert werden).

    4. Aktivieren Sie **SSL-Modus aktivieren** , um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem Gerät und der Cloud zu erstellen. Deaktivieren Sie **SSL aktivieren** nur dann, wenn Sie in einer Private Cloud arbeiten.

        ![Hinzufügen des Blatts „Anmeldeinformationen des Speicherkontos“](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. Klicken Sie auf **Hinzufügen**, um die Auftragserstellung für die Anmeldeinformationen des Speicherkontos zu starten. Sie werden benachrichtigt, nachdem die Anmeldeinformationen des Speicherkontos erfolgreich erstellt wurden.

        ![Erfolgsbenachrichtigung für Anmeldeinformationen des Speicherkontos](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

Die neu erstellten Anmeldeinformationen des Speicherkontos werden unter der Liste der **Anmeldeinformationen für Speicherkonten** angezeigt.

![Liste der Anmeldeinformationen für Speicherkonten](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

