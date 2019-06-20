---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150724"
---
Führen Sie diese Schritte aus, um eine Verbindung mit dem Speicherkonto herzustellen und die Verbindung zu überprüfen.

1. Öffnen Sie in Storage-Explorer das Dialogfeld **Verbindung mit Azure-Speicher herstellen**. Wählen Sie im Dialogfeld **Verbindung mit Azure-Speicher herstellen** die Option **Speicherkontoname und -schlüssel verwenden**.

    ![Data Box-Dashboard](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Fügen Sie Ihren **Kontonamen** und **Kontoschlüssel** (Wert von Schlüssel 1 auf der Seite **Verbindung herstellen und Daten kopieren** der lokalen Webbenutzeroberfläche) ein. Wählen Sie für die Domäne der Speicherendpunkte die Option **Other (enter below)** (Andere (unten eingeben)), und geben Sie anschließend wie unten gezeigt den Blob-Dienstendpunkt an. Aktivieren Sie die Option **HTTP verwenden** nur, wenn die Übertragung per *http* erfolgt. Lassen Sie diese Option bei Verwendung von *https* deaktiviert. Klicken Sie auf **Weiter**.

    ![Data Box-Dashboard](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Überprüfen Sie im Dialogfeld **Verbindungszusammenfassung** die angegebenen Informationen. Wählen Sie **Verbinden**aus.

    ![Data Box-Dashboard](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Das Konto, das Sie erfolgreich hinzugefügt haben, wird im linken Bereich von Storage-Explorer mit dem Namenszusatz „(External, Other)“ ((Extern, Sonstiges)) angezeigt. Klicken Sie auf **Blobcontainer**, um den Container anzuzeigen.

    ![Data Box-Dashboard](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
