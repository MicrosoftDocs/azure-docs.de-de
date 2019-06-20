---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150729"
---
1. Melden Sie sich am Data Box-Gerät an. Stellen Sie sicher, dass es nicht gesperrt ist.

    ![Data Box-Dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Navigieren Sie zu **Netzwerkschnittstellen festlegen**. Notieren Sie sich die IP-Adresse des Geräts für die Netzwerkschnittstelle, die zum Herstellen der Verbindung mit dem Client verwendet wird.

    ![Data Box-Dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Navigieren Sie zu **Verbindung herstellen und Daten kopieren**, und klicken Sie auf **REST**.

    ![Data Box-Dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Kopieren Sie im Dialogfeld **Auf Speicherkonto zugreifen und Daten hochladen** den **Blob-Dienstendpunkt**.

    ![Data Box-Dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Starten Sie **Editor** als Administrator, und öffnen Sie die Datei **hosts** unter `C:\Windows\System32\Drivers\etc`.
6. Fügen Sie Ihrer **hosts**-Datei den folgenden Eintrag hinzu: `<device IP address> <Blob service endpoint>`.
7. Verwenden Sie die folgende Abbildung als Referenz. Speichern Sie die Datei **hosts**.

    ![Data Box-Dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
