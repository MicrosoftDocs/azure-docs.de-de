---
title: 'Tutorial: Zugreifen auf Azure Storage mit SAS-Anmeldeinformationen – Linux – Azure AD'
description: Dieses Tutorial veranschaulicht, wie Sie mit einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers auf Azure Storage zugreifen und dabei SAS-Anmeldeinformationen (Shared Access Signature) anstelle eines Speicherkonto-Zugriffsschlüssels verwenden.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3edc63a1532bb6889fc490e400dbb57e7bce10d0
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360410"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-identity-to-access-azure-storage-via-a-sas-credential"></a>Tutorial: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Storage mithilfe von SAS-Anmeldeinformationen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Linux-Computer verwenden, um SAS-Anmeldeinformationen (Shared Access Signature) für Storage abzurufen. Im Speziellen geht es um [Anmeldeinformationen für eine Dienst-SAS](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

> [!NOTE]
> Der in diesem Tutorial generierte SAS-Schlüssel wird nicht eingeschränkt/an den virtuellen Computer gebunden.  

Eine Dienst-SAS erlaubt für einen begrenzten Zeitraum den eingeschränkten Zugriff auf Objekte in einem Speicherkonto für einen bestimmten Dienst (in unserem Fall der Blob-Dienst), ohne dass dabei der Zugriffsschlüssel für das Konto offengelegt wird. Sie können SAS-Anmeldeinformationen wie gewohnt bei Speichervorgängen verwenden, z.B. bei der Verwendung des Storage SDK. Für dieses Tutorial veranschaulichen wir das Hoch- und Herunterladen eines Blobs mithilfe der Azure Storage-Befehlszeilenschnittstelle. Sie lernen Folgendes:


> [!div class="checklist"]
> * Speicherkonto erstellen
> * Erstellen eines Blob-Containers im Speicherkonto
> * Gewähren des Zugriffs auf eine Speicherkonto-SAS für Ihren virtuellen Computer in Ressourcen-Manager 
> * Abrufen eines Zugriffstokens mithilfe der Identität Ihres virtuellen Computers und Verwenden dieses Zugriffstokens zum Abrufen der SAS von Ressourcen-Manager 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Speicherkonto erstellen 

Erstellen Sie ein Speicherkonto, sofern Sie über keines verfügen.  Sie können diesen Schritt auch überspringen und der systemseitig zugewiesenen verwalteten Identität Ihres virtuellen Computers Zugriff auf die Schlüssel eines vorhandenen Speicherkontos gewähren. 

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **+/Neuen Dienst erstellen**.
2. Klicken Sie auf **Speicher** , dann auf **Speicherkonto** , und anschließend wird ein neuer Bereich namens „Speicherkonto erstellen“ angezeigt.
3. Geben Sie einen **Namen** für das Speicherkonto ein, das Sie später verwenden werden.  
4. **Bereitstellungsmodell** und **Kontoart** sollten jeweils auf „Resource Manager“ und „Allgemein“ festgelegt werden. 
5. Stellen Sie sicher, dass **Abonnement** und **Ressourcengruppe** dem entsprechen, was Sie bei der Erstellung Ihrer VM im vorherigen Schritt angegeben haben.
6. Klicken Sie auf **Erstellen**.

    ![Erstellen eines neuen Speicherkontos](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Erstellen eines Blob-Containers im Speicherkonto

Später werden wir eine Datei in das neue Speicherkonto hoch- und daraus herunterladen. Da Dateien Blob-Speicher erfordern, müssen wir einen Blob-Container erstellen, in dem die Datei gespeichert wird.

1. Navigieren Sie zurück zum neu erstellten Speicherkonto.
2. Klicken Sie im linken Bereich unter „Blob-Dienst“ auf den Link **Container**.
3. Klicken Sie oben auf der Seite auf **+ Container** , und ein Bereich namens „Neuer Container“ wird geöffnet.
4. Geben Sie dem Container einen Namen, wählen Sie eine Zugriffsebene aus, und klicken Sie dann auf **OK**. Der angegebene Name wird später in diesem Tutorial verwendet. 

    ![Erstellen eines Speichercontainers](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Gewähren des Zugriffs für die systemseitig zugewiesene verwaltete Identität Ihres virtuellen Computers zur Verwendung einer Speicher-SAS

Azure Storage unterstützt die Azure AD-Authentifizierung nicht nativ.  Sie können die systemseitig zugewiesene verwaltete Identität des virtuellen Computers jedoch zum Abrufen einer Speicher-SAS von Resource Manager verwenden und mithilfe dieser SAS auf den Speicher zugreifen.  In diesem Schritt gewähren Sie der systemseitig zugewiesenen verwalteten Identität Ihres virtuellen Computers Zugriff auf die SAS des Speicherkontos.   

1. Navigieren Sie zurück zum neu erstellten Speicherkonto.
2. Klicken Sie im linken Bereich auf den Link **Zugriffssteuerung (IAM)** .  
3. Klicken Sie oben auf der Seite auf **+ Rollenzuweisung hinzufügen** , um dem virtuellen Computer eine neue Rollenzuweisung hinzuzufügen.
4. Legen Sie **Rolle** rechts auf der Seite auf „Speicherkontomitwirkender“ fest. 
5. Legen Sie in der nächsten Dropdownliste **Zugriff zuweisen zu** auf die Ressource „Virtueller Computer“ fest.  
6. Stellen Sie im nächsten Schritt sicher, dass das richtige Abonnement in der Dropdownliste **Abonnement** aufgeführt wird, und legen Sie dann **Ressourcengruppe** auf „Alle Ressourcengruppen“ fest.  
7. Wählen Sie schließlich unter **Auswählen** in der Dropdownliste Ihren virtuellen Linux-Computer aus, und klicken Sie dann auf **Speichern**.  

    ![Alternativer Bildtext](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Aufrufen von Azure Resource Manager

Für den Rest des Tutorials arbeiten wir von dem virtuellen Computer aus, den wir zuvor erstellt haben.

Zum Ausführen dieser Schritte benötigen Sie einen SSH-Client. Wenn Sie Windows verwenden, können Sie den SSH-Client im [Windows-Subsystem für Linux](/windows/wsl/install-win10) verwenden. Wenn Sie Hilfe beim Konfigurieren der SSH-Clientschlüssel benötigen, lesen Sie die Informationen unter [Vorgehensweise: Verwenden von SSH-Schlüsseln mit Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) oder [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Navigieren Sie im Azure-Portal zu **Virtuelle Computer** , wechseln Sie zu Ihrem virtuellen Linux-Computer, und klicken Sie dann oben auf der Seite **Übersicht** auf **Verbinden**. Kopieren Sie die Zeichenfolge, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. 
2. Stellen Sie mit Ihrem SSH-Client eine Verbindung mit Ihrem virtuellen Computer her.  
3. Als Nächstes werden Sie aufgefordert, Ihr **Kennwort** einzugeben, das Sie hinzugefügt haben, als Sie den **virtuellen Linux-Computer** erstellt haben. Anschließend sollten Sie erfolgreich angemeldet werden.  
4. Verwenden Sie CURL, um ein Zugriffstoken für Azure Resource Manager abzurufen.  

    Die CURL-Anforderung und -Antwort für das Zugriffstoken sehen Sie unten:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > In der Anforderung oben muss der Wert des resource-Parameters genau mit dem von Azure AD erwarteten Wert übereinstimmen. Wenn Sie die Azure Resource Manager-Ressourcen-ID verwenden, müssen Sie den nachgestellten Schrägstrich im URI verwenden.
    > In der folgenden Antwort wurde das access_token-Element aus Gründen der Übersichtlichkeit gekürzt.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Abrufen von SAS-Anmeldeinformationen von Azure Resource Manager für Speicheraufrufe

Nun rufen Sie mit cURL und dem Zugriffstoken, das im vorherigen Abschnitt abgerufen wurde, Resource Manager auf, um Anmeldeinformationen für eine Speicher-SAS zu erstellen. Sobald wir die SAS-Anmeldeinformationen haben, können wir Vorgänge zum Up- und Download von Speicher aufrufen.

Für diese Anforderung verwenden wir die folgenden HTTP-Anforderungsparameter, um die SAS-Anmeldeinformationen zu erstellen:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Diese Parameter sind im POST-Text der Anforderung für die SAS-Anmeldeinformationen enthalten. Weitere Informationen zu Parametern für das Erstellen von SAS-Anmeldeinformationen finden Sie in der [REST-Referenz zu „List Service SAS“](/rest/api/storagerp/storageaccounts/listservicesas).

Verwenden Sie die folgende cURL-Anforderung, um die SAS-Anmeldeinformationen abzurufen. Ersetzen Sie die Parameter`<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` und `<EXPIRATION TIME>` durch Ihre eigenen Werte. Ersetzen Sie den Wert `<ACCESS TOKEN>` durch das Zugriffstoken, das Sie zuvor abgerufen haben:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Für den Text in der URL oben wird zwischen Groß-/Kleinschreibung unterschieden. Wenn Sie also für Ihre Ressourcengruppen Groß-/Kleinschreibung verwenden, achten Sie darauf, die richtige Schreibweise zu verwenden. Darüber hinaus ist es wichtig zu wissen, dass dies eine POST-Anforderung ist, keine GET-Anforderung.

Die cURL-Antwort gibt die SAS-Anmeldeinformationen zurück:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Erstellen Sie eine Beispiel-Blob-Datei, die in den Blob-Speichercontainer hochgeladen wird. Dies ist auf einem virtuellen Linux-Computer mit dem folgenden Befehl möglich. 

```bash
echo "This is a test file." > test.txt
```

Im nächsten Schritt authentifizieren Sie sich mit dem CLI-Befehl `az storage` unter Verwendung der SAS-Anmeldeinformationen. Laden Sie die Datei dann in den Blobcontainer hoch. Für diesen Schritt müssen Sie auf Ihrem virtuellen Computer [die neueste Azure CLI installieren](/cli/azure/install-azure-cli), sofern dies noch nicht geschehen ist.

```azurecli
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Antwort: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Zudem können Sie die Datei mithilfe der Azure-Befehlszeilenschnittstelle herunterladen und mit den SAS-Anmeldeinformationen authentifizieren. 

Anforderung: 

```azurecli
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Antwort: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers verwenden, um mithilfe von SAS-Anmeldeinformationen auf Azure Storage zuzugreifen.  Weitere Informationen zu Azure Storage-SAS finden Sie hier:

> [!div class="nextstepaction"]
>[Verwenden von Shared Access Signatures (SAS)](../../storage/common/storage-sas-overview.md)
