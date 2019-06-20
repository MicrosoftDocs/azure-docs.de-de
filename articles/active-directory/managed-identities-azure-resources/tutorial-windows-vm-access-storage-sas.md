---
title: Verwenden einer vom Windows-VM-System zugewiesenen verwalteten Identität für den Zugriff auf Azure Storage mithilfe von SAS-Anmeldeinformationen
description: Dieses Tutorial veranschaulicht, wie Sie mit einer vom Windows-VM-System zugewiesenen verwalteten Identität auf Azure Storage zugreifen und dabei SAS-Anmeldeinformationen (Shared Access Signature) anstelle des Zugriffsschlüssels eines Speicherkontos verwenden.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23ec4d2a67beb9b5f903aa0b7f03196b47db3f78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226466"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Tutorial: Verwenden einer vom Windows-VM-System zugewiesenen verwalteten Identität für den Zugriff auf Azure Storage über SAS-Anmeldeinformationen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene Identität für einen virtuellen Windows-Computer verwenden, um SAS-Anmeldeinformationen (Shared Access Signature) für Storage abzurufen. Im Speziellen geht es um [Anmeldeinformationen für eine Dienst-SAS](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Eine Dienst-SAS erlaubt für einen begrenzten Zeitraum den eingeschränkten Zugriff auf Objekte in einem Speicherkonto für einen bestimmten Dienst (in unserem Fall der Blob-Dienst), ohne dass dabei der Zugriffsschlüssel für das Konto offengelegt wird. Sie können SAS-Anmeldeinformationen wie gewohnt bei Speichervorgängen verwenden, z.B. bei der Verwendung des Storage SDK. Für dieses Tutorial veranschaulichen wir das Hoch- und Herunterladen eines Blobs mithilfe von Azure Storage PowerShell. Sie lernen Folgendes:

> [!div class="checklist"]
> * Speicherkonto erstellen
> * Gewähren des Zugriffs auf eine Speicherkonto-SAS für Ihren virtuellen Computer in Ressourcen-Manager 
> * Abrufen eines Zugriffstokens mithilfe der Identität Ihres virtuellen Computers und Verwenden dieses Zugriffstokens zum Abrufen der SAS von Ressourcen-Manager 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Speicherkonto erstellen 

Erstellen Sie ein Speicherkonto, sofern Sie über keines verfügen. Sie können diesen Schritt auch überspringen und der systemseitig zugewiesenen verwalteten Identität Ihres virtuellen Computers Zugriff auf die SAS-Anmeldeinformationen eines vorhandenen Speicherkontos gewähren. 

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **+/Neuen Dienst erstellen**.
2. Klicken Sie auf **Speicher**, dann auf **Speicherkonto**, und anschließend wird ein neuer Bereich namens „Speicherkonto erstellen“ angezeigt.
3. Geben Sie einen Namen für das Speicherkonto ein, das Sie später verwenden werden.  
4. **Bereitstellungsmodell** und **Kontoart** sollten jeweils auf „Resource Manager“ und „Allgemein“ festgelegt werden. 
5. Stellen Sie sicher, dass **Abonnement** und **Ressourcengruppe** dem entsprechen, was Sie bei der Erstellung Ihrer VM im vorherigen Schritt angegeben haben.
6. Klicken Sie auf **Create**.

    ![Erstellen eines neuen Speicherkontos](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Erstellen eines Blob-Containers im Speicherkonto

Später werden wir eine Datei in das neue Speicherkonto hoch- und daraus herunterladen. Da Dateien Blob-Speicher erfordern, müssen wir einen Blob-Container erstellen, in dem die Datei gespeichert wird.

1. Navigieren Sie zurück zum neu erstellten Speicherkonto.
2. Klicken Sie im linken Bereich unter „Blob-Dienst“ auf den Link **Container**.
3. Klicken Sie oben auf der Seite auf **+ Container**, und ein Bereich namens „Neuer Container“ wird geöffnet.
4. Geben Sie dem Container einen Namen, wählen Sie eine Zugriffsebene aus, und klicken Sie dann auf **OK**. Der angegebene Name wird später in diesem Tutorial verwendet. 

    ![Erstellen eines Speichercontainers](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Gewähren des Zugriffs für die systemseitig zugewiesene verwaltete Identität Ihres virtuellen Computers zur Verwendung einer Speicher-SAS 

Azure Storage unterstützt die Azure AD-Authentifizierung nicht nativ.  Sie können mit einer verwalteten Identität jedoch eine Speicher-SAS von Resource Manager abrufen und mit dieser SAS auf den Speicher zugreifen.  In diesem Schritt gewähren Sie der systemseitig zugewiesenen verwalteten Identität Ihres virtuellen Computers Zugriff auf die SAS des Speicherkontos.   

1. Navigieren Sie zurück zum neu erstellten Speicherkonto.   
2. Klicken Sie im linken Bereich auf den Link **Zugriffssteuerung (IAM)** .  
3. Klicken Sie oben auf der Seite auf **+ Rollenzuweisung hinzufügen**, um dem virtuellen Computer eine neue Rollenzuweisung hinzuzufügen.
4. Legen Sie **Rolle** rechts auf der Seite auf „Speicherkontomitwirkender“ fest.  
5. Legen Sie in der nächsten Dropdownliste **Zugriff zuweisen zu** auf die Ressource „Virtueller Computer“ fest.  
6. Stellen Sie im nächsten Schritt sicher, dass das richtige Abonnement in der Dropdownliste **Abonnement** aufgeführt wird, und legen Sie dann **Ressourcengruppe** auf „Alle Ressourcengruppen“ fest.  
7. Wählen Sie schließlich unter **Auswählen** in der Dropdownliste Ihren virtuellen Windows-Computer aus, und klicken Sie dann auf **Speichern**. 

    ![Alternativer Bildtext](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Aufrufen von Azure Resource Manager 

Für den Rest des Tutorials arbeiten wir von dem virtuellen Computer aus, den wir zuvor erstellt haben.

In diesem Abschnitt müssen Sie PowerShell-Cmdlets von Azure Resource Manager verwenden.  Wenn Sie das Programm nicht installiert haben, [laden Sie die neuesten Version herunter](https://docs.microsoft.com/powershell/azure/overview), bevor Sie fortfahren.

1. Navigieren Sie im Azure-Portal zu **Virtuelle Computer**, wechseln Sie zu Ihrem virtuellen Windows-Computer, und klicken Sie dann oben auf der Seite **Übersicht** auf **Verbinden**.
2. Geben Sie Ihren **Benutzernamen** und Ihr **Kennwort** ein, das Sie beim Erstellen des virtuellen Windows-Computers hinzugefügt haben. 
3. Sie haben nun eine **Remotedesktopverbindung** mit dem virtuellen Computer erstellt. Öffnen Sie jetzt PowerShell in der Remotesitzung. 
4. Erstellen Sie mithilfe des PowerShell-Befehls „Invoke-WebRequest“ eine Anforderung an den lokalen Endpunkt für die verwaltete Identität für Azure-Ressourcen, um ein Zugriffstoken für Azure Resource Manager zu erhalten.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Der Wert des „resource“-Parameters muss exakt mit dem von Azure AD erwarteten Wert übereinstimmen. Wenn Sie die Azure Resource Manager-Ressourcen-ID verwenden, müssen Sie den nachgestellten Schrägstrich im URI verwenden.
    
    Extrahieren Sie im nächsten Schritt das Element „Content“, das als JSON-formatierte (JavaScript Object Notation) Zeichenfolge im $response-Objekt gespeichert ist. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extrahieren Sie dann das Zugriffstoken aus der Antwort.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Abrufen von SAS-Anmeldeinformationen von Azure Resource Manager für Speicheraufrufe 

Nun rufen Sie mit PowerShell und dem Zugriffstoken, das im vorherigen Abschnitt abgerufen wurde, den Ressourcen-Manager auf, um Anmeldeinformationen für eine Speicher-SAS zu erstellen. Sobald wir die SAS-Anmeldeinformationen haben, können wir Speichervorgänge aufrufen.

Für diese Anforderung verwenden wir die folgenden HTTP-Anforderungsparameter, um die SAS-Anmeldeinformationen zu erstellen:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Diese Parameter sind im POST-Text der Anforderung für die SAS-Anmeldeinformationen enthalten. Weitere Informationen zu Parametern für das Erstellen von SAS-Anmeldeinformationen finden Sie in der [REST-Referenz zu „List Service SAS“](/rest/api/storagerp/storageaccounts/listservicesas).

Konvertieren Sie zuerst die Parameter in JSON, und rufen Sie dann den `listServiceSas`-Endpunkt des Speichers auf, um die SAS-Anmeldedaten zu erstellen:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Bei der URL wird die Groß-/Kleinschreibung beachtet. Achten Sie daher darauf, dieselbe Groß- und Kleinschreibung zu verwenden wie zuvor beim Benennen der Ressourcengruppe, einschließlich des Großbuchstaben „G“ in „resourceGroups“. 

Jetzt können wir die SAS-Anmeldeinformationen aus der Antwort extrahieren:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Wenn Sie die SAS-Anmeldeinformationen überprüfen, wird Ihnen Folgendes angezeigt:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Als Nächstes erstellen wir eine Datei namens „test.txt“. Führen Sie dann mithilfe der SAS-Anmeldeinformationen die Authentifizierung mit dem `New-AzStorageContent`-Cmdlet aus, und laden Sie die Datei in unseren Blob-Container hoch und anschließend wieder herunter.

```bash
echo "This is a test text file." > test.txt
```

Installieren Sie unbedingt zunächst die Azure Storage-Cmdlets mit `Install-Module Azure.Storage`. Laden Sie dann das Blob, das Sie soeben erstellt haben, mit dem PowerShell-Cmdlet `Set-AzStorageBlobContent` hoch:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Antwort:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Sie können das Blob, das Sie soeben erstellt haben, auch mit dem PowerShell-Cmdlet `Get-AzStorageBlobContent` herunterladen:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Antwort:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine vom Windows-VM-System zugewiesene verwaltete Identität für den Zugriff auf Azure Storage unter Verwendung von SAS-Anmeldeinformationen erstellen.  Weitere Informationen zu Azure Storage-SAS finden Sie hier:

> [!div class="nextstepaction"]
>[Verwenden von Shared Access Signatures (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


