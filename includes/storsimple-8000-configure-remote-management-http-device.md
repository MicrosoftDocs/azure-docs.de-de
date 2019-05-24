---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159656"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>So konfigurieren Sie die Remoteverwaltung auf dem Cloudgerät

1. Klicken Sie in Ihrem StorSimple-Geräte-Manager-Dienst auf **Geräte**. Wählen Sie in der Liste der mit dem Dienst verbundenen Geräte Ihr Cloudgerät aus, und klicken Sie darauf.
    ![StorSimple – Cloudgerät auswählen](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Wechseln Sie zu **Einstellungen > Sicherheit**, um das Blatt **Sicherheitseinstellungen** zu öffnen.

     ![StorSimple-Sicherheitseinstellungen](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Navigieren Sie zum Abschnitt **Remoteverwaltung** . Klicken Sie auf das Feld **Remoteverwaltung**.
     ![StorSimple-Remoteverwaltung](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. Auf dem Blatt **Remoteverwaltung**:

    1. Stellen Sie sicher, dass **Remoteverwaltung aktivieren** aktiviert ist.
    2. Die Standardeinstellung ist eine HTTPS-Verbindung. Sie können eine Verbindung über HTTP herstellen. Verbindungen über HTTP sind nur in vertrauenswürdigen Netzwerken zulässig. Stellen Sie sicher, dass HTTP aktiviert ist.
    3. Klicken Sie auf der Befehlsleiste am oberen Rand des Blatts auf **... Mehr**, und klicken Sie dann auf **Zertifikat herunterladen**, um ein Remoteverwaltungszertifikat herunterzuladen. Sie können einen Speicherort für diese Datei angeben. Dieses Zertifikat muss auf dem Client oder dem Hostcomputer installiert werden, der für die Verbindung mit dem Cloudgerät verwendet wird.

        ![Blatt „Remoteverwaltung“](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Klicken Sie auf **Speichern**, und bestätigen Sie die Änderungen, wenn Sie dazu aufgefordert werden.