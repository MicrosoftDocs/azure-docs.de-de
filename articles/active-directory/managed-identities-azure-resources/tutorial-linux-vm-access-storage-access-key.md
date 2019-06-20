---
title: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Storage
description: In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers verwenden, um auf Azure Storage zuzugreifen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d53f625cec6aee980155711ddeb4d1e1761b91d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66236148"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Tutorial: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Storage über Zugriffsschlüssel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Linux-Computer verwenden, um Speicherkonto-Zugriffsschlüssel abzurufen. Sie können einen Speicherzugriffsschlüssel wie gewohnt bei Speichervorgängen verwenden, z.B. bei der Verwendung des Storage SDK. Für dieses Tutorial werden Blobs mithilfe der Azure-CLI hoch- und heruntergeladen. Sie lernen Folgendes:

> [!div class="checklist"]
> * Gewähren des Zugriffs auf Speicherkonto-Zugriffsschlüssel für Ihren virtuellen Computer in Resource Manager 
> * Abrufen eines Zugriffstokens mithilfe der Identität Ihres virtuellen Computers und Verwenden dieses Zugriffstokens zum Abrufen von Speicherzugriffsschlüsseln aus Resource Manager  

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Speicherkonto erstellen 

Erstellen Sie ein Speicherkonto, sofern Sie über keines verfügen.  Sie können diesen Schritt auch überspringen und der systemseitig zugewiesenen verwalteten Identität Ihres virtuellen Computers Zugriff auf die Schlüssel eines vorhandenen Speicherkontos gewähren. 

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **+/Neuen Dienst erstellen**.
2. Klicken Sie auf **Speicher**, dann auf **Speicherkonto**, und anschließend wird ein neuer Bereich namens „Speicherkonto erstellen“ angezeigt.
3. Geben Sie einen **Namen** für das Speicherkonto ein, das Sie später verwenden werden.  
4. **Bereitstellungsmodell** und **Kontoart** sollten jeweils auf „Resource Manager“ und „Allgemein“ festgelegt werden. 
5. Stellen Sie sicher, dass **Abonnement** und **Ressourcengruppe** dem entsprechen, was Sie bei der Erstellung Ihrer VM im vorherigen Schritt angegeben haben.
6. Klicken Sie auf **Create**.

    ![Erstellen eines neuen Speicherkontos](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Erstellen eines Blob-Containers im Speicherkonto

Später werden wir eine Datei in das neue Speicherkonto hoch- und daraus herunterladen. Da Dateien Blob-Speicher erfordern, müssen wir einen Blob-Container erstellen, in dem die Datei gespeichert wird.

1. Navigieren Sie zurück zum neu erstellten Speicherkonto.
2. Klicken Sie auf der linken Seite unter „Blob-Dienst“ auf den Link **Container**.
3. Klicken Sie oben auf der Seite auf **+ Container**, und ein Bereich namens „Neuer Container“ wird geöffnet.
4. Geben Sie dem Container einen Namen, wählen Sie eine Zugriffsebene aus, und klicken Sie dann auf **OK**. Der angegebene Name wird später in diesem Tutorial verwendet. 

    ![Erstellen eines Speichercontainers](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Gewähren des Zugriffs für die systemseitig zugewiesene verwaltete Identität Ihres virtuellen Computers zur Verwendung von Speicherkonto-Zugriffsschlüsseln

In diesem Schritt gewähren Sie der systemseitig zugewiesenen verwalteten Identität Ihres virtuellen Computers Zugriff auf die Schlüssel des Speicherkontos.   

1. Navigieren Sie zurück zum neu erstellten Speicherkonto.
2. Klicken Sie im linken Bereich auf den Link **Zugriffssteuerung (IAM)** .  
3. Klicken Sie oben auf der Seite auf **+ Rollenzuweisung hinzufügen**, um dem virtuellen Computer eine neue Rollenzuweisung hinzuzufügen.
4. Legen Sie **Rolle** rechts auf der Seite auf „Dienstrolle Speicherkonto-Schlüsseloperator“ fest. 
5. Legen Sie in der nächsten Dropdownliste **Zugriff zuweisen zu** auf die Ressource „Virtueller Computer“ fest.  
6. Stellen Sie im nächsten Schritt sicher, dass das richtige Abonnement in der Dropdownliste **Abonnement** aufgeführt wird, und legen Sie dann **Ressourcengruppe** auf „Alle Ressourcengruppen“ fest.  
7. Wählen Sie schließlich unter **Auswählen** in der Dropdownliste Ihren virtuellen Linux-Computer aus, und klicken Sie dann auf **Speichern**. 

    ![Alternativer Bildtext](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Aufrufen von Azure Resource Manager

Für den Rest des Tutorials arbeiten wir von dem virtuellen Computer aus, den wir zuvor erstellt haben.

Zum Ausführen dieser Schritte benötigen Sie einen SSH-Client. Wenn Sie Windows verwenden, können Sie den SSH-Client im [Windows-Subsystem für Linux](https://msdn.microsoft.com/commandline/wsl/install_guide) verwenden. Wenn Sie Hilfe beim Konfigurieren der SSH-Clientschlüssel benötigen, lesen Sie die Informationen unter [Vorgehensweise: Verwenden von SSH-Schlüsseln mit Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) oder [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Navigieren Sie im Azure-Portal zu **Virtuelle Computer**, wechseln Sie zu Ihrem virtuellen Linux-Computer, und klicken Sie dann oben auf der Seite **Übersicht** auf **Verbinden**. Kopieren Sie die Zeichenfolge, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. 
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
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Abrufen von Speicherkonto-Zugriffsschlüsseln aus Azure Resource Manager, um Speicheraufrufe auszuführen  

Nun rufen Sie mit CURL und dem Zugriffstoken, das im vorherigen Abschnitt abgerufen wurde, Resource Manager auf, um den Speicherzugriffsschlüssel abzurufen. Sobald wir den Speicherzugriffsschlüssel haben, können wir Vorgänge zum Up- und Download von Speicher aufrufen. Ersetzen Sie die Parameter `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, und `<STORAGE ACCOUNT NAME>` durch Ihre eigenen Werte. Ersetzen Sie den Wert `<ACCESS TOKEN>` durch das Zugriffstoken, das Sie zuvor abgerufen haben:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Für den Text in der URL oben wird zwischen Groß-/Kleinschreibung unterschieden. Wenn Sie also für Ihre Ressourcengruppen Groß-/Kleinschreibung verwenden, achten Sie darauf, die richtige Schreibweise zu verwenden. Wichtig ist darüber hinaus die Tatsache, dass es sich um eine POST-Anforderung und nicht um eine GET-Anforderung handelt. Stellen Sie sicher, dass Sie mit -d einen Wert zum Erfassen einer Längenbeschränkung übergeben, der NULL sein kann.  

Die CURL-Antwort stellt die Liste der Schlüssel bereit:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Erstellen Sie eine Beispiel-Blob-Datei, die in den Blob-Speichercontainer hochgeladen wird. Dies ist auf einem virtuellen Linux-Computer mit dem folgenden Befehl möglich. 

```bash
echo "This is a test file." > test.txt
```

Im nächsten Schritt authentifizieren Sie sich mit dem CLI-Befehl `az storage` unter Verwendung des Speicherzugriffsschlüssels. Laden Sie die Datei dann in den BLOB-Container hoch. Für diesen Schritt müssen Sie auf Ihrem virtuellen Computer [die neueste Azure CLI installieren](https://docs.microsoft.com/cli/azure/install-azure-cli), sofern dies noch nicht geschehen ist.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Antwort: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Zudem können Sie die Datei mithilfe der Azure CLI herunterladen und mit dem Speicherzugriffsschlüssel authentifizieren. 

Anforderung: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Antwort: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Storage unter Verwendung eines Zugriffsschlüssels erstellen.  Weitere Informationen zu Azure Storage-Zugriffsschlüsseln finden Sie hier:

> [!div class="nextstepaction"]
>[Verwalten von Speicherzugriffsschlüsseln](/azure/storage/common/storage-create-storage-account)
