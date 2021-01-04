---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553092"
---
Führen Sie diese Schritte aus, um eine Verbindung mit dem Speicherkonto herzustellen und die Verbindung zu überprüfen.

1. Öffnen Sie in Storage-Explorer das Dialogfeld **Verbindung mit Azure-Speicher herstellen**. Wählen Sie im Dialogfeld **Verbindung mit Azure-Speicher herstellen** die Option **Speicherkontoname und -schlüssel verwenden**.

    ![Screenshot: Dialogfeld „Verbindung mit Azure-Speicher herstellen“, in dem die Option „Speicherkontoname und -schlüssel verwenden“ ausgewählt ist](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Fügen Sie Ihren **Kontonamen** und **Kontoschlüssel** (Wert von Schlüssel 1 auf der Seite **Verbindung herstellen und Daten kopieren** der lokalen Webbenutzeroberfläche) ein. Wählen Sie für die Domäne der Speicherendpunkte die Option **Other (enter below)** (Andere (unten eingeben)), und geben Sie anschließend wie unten gezeigt den Blob-Dienstendpunkt an. Aktivieren Sie die Option **HTTP verwenden** nur, wenn die Übertragung per *http* erfolgt. Lassen Sie diese Option bei Verwendung von *https* deaktiviert. Wählen Sie **Weiter** aus.

    ![Screenshot: Dialogfeld „Verbindung mit Name und Schlüssel herstellen“ mit eingegebenen Werten](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Überprüfen Sie im Dialogfeld **Verbindungszusammenfassung** die angegebenen Informationen. Wählen Sie **Verbinden**.

    ![Screenshot: Dialogfeld „Verbindungszusammenfassung“, in dem „Verbinden“ ausgewählt ist](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Das Konto, das Sie erfolgreich hinzugefügt haben, wird im linken Bereich von Storage-Explorer mit dem Namenszusatz „(External, Other)“ ((Extern, Sonstiges)) angezeigt. Klicken Sie auf **Blobcontainer**, um den Container anzuzeigen.

    ![Screenshot: Explorer-Menü, in dem „Blobcontainer“ ausgewählt ist](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
