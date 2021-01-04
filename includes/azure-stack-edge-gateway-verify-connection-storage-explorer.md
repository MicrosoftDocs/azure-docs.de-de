---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 203c977fe9109cd8b2b6de561e975e20aacf700e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185541"
---
Falls Sie Storage-Explorer zum ersten Mal nutzen, müssen Sie die folgenden Schritte ausführen.

1. Navigieren Sie in der oberen Befehlsleiste zu **Bearbeiten > Azure Stack-APIs als Ziel verwenden**.

    ![Konfigurieren von Storage-Explorer](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Starten Sie Storage-Explorer neu, damit die Änderungen wirksam werden.


Führen Sie diese Schritte aus, um eine Verbindung mit dem Speicherkonto herzustellen und die Verbindung zu überprüfen.

1. Wählen Sie im Storage-Explorer Speicherkonten aus. Klicken Sie mit der rechten Maustaste, und wählen Sie **Verbindung mit Azure Storage herstellen** aus. 

    ![Konfigurieren von Storage-Explorer 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. Wählen Sie im Dialogfeld **Verbindung mit Azure-Speicher herstellen** die Option **Speicherkontoname und -schlüssel verwenden**.

    ![Konfigurieren von Storage-Explorer 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. Führen Sie im Dialogfeld **Verbindung mit Name und Schlüssel herstellen** die folgenden Schritte aus:

    1. Geben Sie für Ihr Edge-Speicherkonto einen Anzeigenamen ein. 
    2. Geben Sie den Namen des Edge-Speicherkontos an.
    3. Fügen Sie den Zugriffsschlüssel ein, den Sie aus den lokalen APIs des Geräts über Azure Resource Manager abgerufen haben.
    4. Wählen Sie für „Speicherdomäne“ **Andere (unten eingeben)** aus, und geben Sie dann das Suffix des Blob-Dienstendpunkts im folgenden Format ein: `<appliance name>.<DNSdomain>`. 
    5. Aktivieren Sie die Option **HTTP verwenden**, wenn die Übertragung per *http* erfolgt. 
    6. Wählen Sie **Weiter** aus.

    ![Konfigurieren von Storage-Explorer 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. Überprüfen Sie im Dialogfeld **Verbindungszusammenfassung** die angegebenen Informationen. Wählen Sie **Verbinden**.

    ![Konfigurieren von Storage-Explorer 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. Das Konto, das Sie erfolgreich hinzugefügt haben, wird im linken Bereich von Storage-Explorer mit dem Namenszusatz „(External, Other)“ ((Extern, Sonstiges)) angezeigt. Wählen Sie **Blobcontainer** aus, um den Container anzuzeigen.

    ![Blobcontainer anzeigen](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

Als Nächstes muss überprüft werden, ob die Datenübertragung über diese Verbindung tatsächlich einwandfrei funktioniert.

Führen Sie die folgenden Schritte aus, um Daten in Ihr Edge-Speicherkonto auf dem Gerät zu laden. Die Daten sollten automatisch in das zugeordnete Azure Storage-Konto eingeordnet werden.

1. Wählen Sie den Container aus, in den Sie die Daten in Ihrem Edge-Speicherkonto laden möchten. Wählen Sie **Hochladen** und dann **Dateien hochladen** aus.

    ![Überprüfen der Datenübertragung](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. Navigieren Sie im Dialogfeld **Dateien hochladen** zu den Dateien, die Sie hochladen möchten, und wählen Sie diese aus. Wählen Sie **Weiter** aus.

    ![Überprüfen der Datenübertragung 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Bestätigen Sie, dass die Dateien hochgeladen wurden. Die hochgeladenen Dateien werden im Container angezeigt.

    ![Überprüfen der Datenübertragung 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Als Nächstes stellen Sie eine Verbindung mit dem Azure Storage-Konto her, das diesem Edge-Speicherkonto zugeordnet wurde. Alle Daten, die in das Edge-Speicherkonto hochgeladen werden, sollten automatisch dem Azure Storage-Konto zugeordnet werden. 
    
    Um die Verbindungszeichenfolge für das Azure Storage-Konto zu erhalten, wechseln Sie zu **Azure Storage-Konto > Zugriffsschlüssel**, und kopieren Sie die Verbindungszeichenfolge.

    ![Überprüfen der Datenübertragung 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Führen Sie mithilfe der Verbindungszeichenfolge ein Anfügen an das Azure Storage-Konto durch.  

    ![Überprüfen der Datenübertragung 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. Überprüfen Sie im Dialogfeld **Verbindungszusammenfassung** die angegebenen Informationen. Wählen Sie **Verbinden**.

    ![Überprüfen der Datenübertragung 6](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Sie werden feststellen, dass die Dateien, die Sie in das Edge-Speicherkonto hochgeladen haben, in das Azure Storage-Konto übertragen wurden.

    ![Überprüfen der Datenübertragung 7](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)
