---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553179"
---
1. Melden Sie sich am Data Box-Gerät an. Stellen Sie sicher, dass es nicht gesperrt ist.

    ![Screenshot: Dashboard, auf dem das Gerät als nicht gesperrt angezeigt wird](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Navigieren Sie zu **Netzwerkschnittstellen festlegen**. Notieren Sie sich die IP-Adresse des Geräts für die Netzwerkschnittstelle, die zum Herstellen der Verbindung mit dem Client verwendet wird.

    ![Screenshot: Netzwerkeinstellungen, in denen Sie die IP-Adresse anzeigen können](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Navigieren Sie zu **Verbindung herstellen und Daten kopieren**, und klicken Sie auf **REST**.

    ![Screenshot: Bereich „Verbindung herstellen und Daten kopieren“, in dem Sie „REST“ als Zugriffseinstellung auswählen können](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Kopieren Sie im Dialogfeld **Auf Speicherkonto zugreifen und Daten hochladen** den **Blob-Dienstendpunkt**.

    ![Screenshot: Dialogfeld „Auf Speicherkonto zugreifen und Daten hochladen“, in dem Sie den Blob-Dienstendpunkt kopieren können](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Starten Sie **Editor** als Administrator, und öffnen Sie die Datei **hosts** unter `C:\Windows\System32\Drivers\etc`.
6. Fügen Sie Ihrer **hosts**-Datei den folgenden Eintrag hinzu: `<device IP address> <Blob service endpoint>`.
7. Verwenden Sie die folgende Abbildung als Referenz. Speichern Sie die Datei **hosts**.

    ![Screenshot: Editor-Dokument, in dem die IP-Adresse und der Blob-Dienstendpunkt hinzugefügt werden](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
