---
title: 'Tutorial: Zugriff auf Speicher durch die Web-App über verwaltete Identitäten | Azure'
description: In diesem Tutorial wird beschrieben, wie Sie für eine App mit verwalteten Identitäten auf Azure-Speicher zugreifen.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 72b1d4fe864c23c0ac065e47d96ab0c78866defa
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435840"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Tutorial: Zugreifen auf Azure-Speicher über eine Web-App

Es wird beschrieben, wie Sie für eine Web-App (kein angemeldeter Benutzer), die in Azure App Service ausgeführt wird, mit verwalteten Identitäten auf Azure-Speicher zugreifen.

:::image type="content" alt-text="Diagramm: Zugreifen auf Speicher" source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Sie möchten den Zugriff auf die Azure-Datenebene (Azure Storage, Azure SQL-Datenbank, Azure Key Vault oder andere Dienste) über Ihre Web-App hinzufügen. Sie können auch einen gemeinsam verwendeten Schlüssel nutzen. In diesem Fall müssen Sie sich aber Gedanken über den sicheren Betrieb machen – also darüber, wer das Geheimnis erstellen, bereitstellen und verwalten kann. Darüber hinaus kann es sein, dass der Schlüssel in GitHub eingecheckt wird – und Hacker wissen, wie man einen entsprechenden Scanvorgang zur Ermittlung durchführt. Eine sicherere Möglichkeit, wie Sie für Ihre Web-App den Zugriff auf Daten ermöglichen können, ist die Verwendung von [verwalteten Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

Mit einer verwalteten Identität über Azure Active Directory (Azure AD) können App Services per rollenbasierter Zugriffssteuerung (Role-Based Access Control, RBAC) auf Ressourcen zugreifen, ohne dass die App-Anmeldeinformationen benötigt werden. Nachdem Sie Ihrer Web-App eine verwaltete Identität zugewiesen haben, kümmert sich Azure um die Erstellung und Verteilung eines Zertifikats. Die Benutzer müssen sich keine Gedanken über die Verwaltung von Geheimnissen oder App-Anmeldeinformationen machen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen einer systemseitig zugewiesenen verwalteten Identität in einer Web-App
> * Erstellen eines Speicherkontos und eines Azure Blob Storage-Containers
> * Zugreifen auf Speicher über eine Web-App mit verwalteten Identitäten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Eine unter Azure App Service ausgeführte Webanwendung, für die das [App Service-Modul für die Authentifizierung/Autorisierung aktiviert ist](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-an-app"></a>Aktivieren einer verwalteten Identität in einer App

Wenn Sie Ihre Web-App mit Visual Studio erstellen und veröffentlichen, wird die verwaltete Identität für Sie in Ihrer App aktiviert. Wählen Sie auf Ihrer App Service-Instanz im linken Bereich die Option **Identität** und dann **Vom System zugewiesen** aus. Vergewissern Sie sich, dass der **Status** auf **Ein** festgelegt ist. Falls nicht: Wählen Sie **Speichern** und dann **Ja** aus, um die systemseitig zugewiesene verwaltete Identität zu aktivieren. Wenn die verwaltete Identität aktiviert ist, ist der Status auf **Ein** festgelegt, und die Objekt-ID ist verfügbar.

:::image type="content" alt-text="Screenshot: Option „Systemseitig zugewiesene Identität“" source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

In diesem Schritt wird eine neue Objekt-ID erstellt, die sich von der im Bereich **Authentifizierung/Autorisierung** erstellten App-ID unterscheidet. Kopieren Sie die Objekt-ID der systemseitig zugewiesenen verwalteten Identität. Sie benötigen die Information später.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Erstellen eines Speicherkontos und Blob Storage-Containers

Nun können Sie ein Speicherkonto und einen Blob Storage-Container erstellen.

Jedes Speicherkonto muss zu einer Azure-Ressourcengruppe gehören. Eine Ressourcengruppe ist ein logischer Container zur Gruppierung Ihrer Azure-Dienste. Beim Erstellen eines Speicherkontos haben Sie die Möglichkeit, eine neue Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden. In diesem Artikel wird gezeigt, wie Sie eine neue Ressourcengruppe erstellen.

Ein universelles v2-Speicherkonto bietet Zugriff auf sämtliche Azure Storage-Dienste: Blobs, Dateien, Warteschlangen, Tabellen und Datenträger. In den hier beschriebenen Schritten wird ein Speicherkonto vom Typ „Universell v2“ erstellt. Die Schritte für die Erstellung einer anderen Art von Speicherkonto sind jedoch ähnlich.

Blobs in Azure Storage sind in Containern organisiert. Bevor Sie später in diesem Tutorial ein Blob hochladen können, müssen Sie zunächst einen Container erstellen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, wenn Sie ein allgemeines Speicherkonto vom Typ „Universell V2“ über das Azure-Portal erstellen möchten.

1. Wählen Sie im Menü des Azure-Portals die Option **Alle Dienste** aus. Geben Sie in der Liste der Ressourcen **Speicherkonten** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Speicherkonten**.

1. Wählen Sie im angezeigten Fenster **Speicherkonten** die Option **Hinzufügen** aus.

1. Wählen Sie das Abonnement aus, in dem das Speicherkonto erstellt werden soll.

1. Wählen Sie unter dem Feld **Ressourcengruppe** im Dropdownmenü die Ressourcengruppe aus, die Ihre Web-App enthält.

1. Geben Sie als Nächstes einen Namen für Ihr Speicherkonto ein. Der gewählte Name muss innerhalb von Azure eindeutig sein. Der Name muss ebenfalls zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.

1. Wählen Sie einen Standort für Ihr Speicherkonto aus, oder verwenden Sie den Standardstandort.

1. Behalten Sie die Standardwerte für diese Felder bei:

    |Feld|Wert|
    |--|--|
    |Bereitstellungsmodell|Ressourcen-Manager|
    |Leistung|Standard|
    |Kontoart|StorageV2 (universell v2)|
    |Replikation|Georedundanter Speicher mit Lesezugriff (RA-GRS)|
    |Zugriffsebene|Hot|

1. Wählen Sie **Überprüfen + erstellen**, um die Speicherkontoeinstellungen zu überprüfen und das Konto zu erstellen.

1. Klicken Sie auf **Erstellen**.

Führen Sie die folgenden Schritte aus, um einen Blob Storage-Container in Azure Storage zu erstellen.

1. Navigieren Sie im Azure-Portal zu Ihrem neuen Speicherkonto.

1. Scrollen Sie im linken Menü für das Speicherkonto zum Abschnitt **Blob-Dienst**, und wählen Sie dann **Container** aus.

1. Wählen Sie die Schaltfläche **+ Container**.

1. Geben Sie einen Namen für den neuen Container ein. Der Containername muss klein geschrieben werden, mit einem Buchstaben oder einer Zahl beginnen und darf nur Buchstaben, Zahlen und Bindestriche (-) enthalten.

1. Legen Sie die öffentliche Zugriffsebene für den Container fest. Die Standardebene ist **Private (no anonymous access)** (Privat (kein anonymer Zugriff)).

1. Wählen Sie **OK** aus, um den Container zu erstellen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Führen Sie das folgende Skript aus, um ein Speicherkonto vom Typ „Universell V2“ und einen Blob Storage-Container zu erstellen. Geben Sie den Namen der Ressourcengruppe an, die Ihre Web-App enthält. Geben Sie einen Namen für Ihr Speicherkonto ein. Der gewählte Name muss innerhalb von Azure eindeutig sein. Der Name muss ebenfalls zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.

Geben Sie den Speicherort für Ihr Speicherkonto an. Führen Sie ```Get-AzLocation | select Location``` aus, um eine Liste mit den gültigen Speicherorten für Ihr Abonnement anzuzeigen. Der Containername muss klein geschrieben werden, mit einem Buchstaben oder einer Zahl beginnen und darf nur Buchstaben, Zahlen und Bindestriche (-) enthalten.

Denken Sie daran, die Platzhalterwerte in spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie das folgende Skript aus, um ein Speicherkonto vom Typ „Universell V2“ und einen Blob Storage-Container zu erstellen. Geben Sie den Namen der Ressourcengruppe an, die Ihre Web-App enthält. Geben Sie einen Namen für Ihr Speicherkonto ein. Der gewählte Name muss innerhalb von Azure eindeutig sein. Der Name muss ebenfalls zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten. 

Geben Sie den Speicherort für Ihr Speicherkonto an. Der Containername muss klein geschrieben werden, mit einem Buchstaben oder einer Zahl beginnen und darf nur Buchstaben, Zahlen und Bindestriche (-) enthalten.

Im folgenden Beispiel wird Ihr Azure AD-Konto genutzt, um den Vorgang zur Erstellung des Containers zu autorisieren. Weisen Sie sich vor der Erstellung des Containers selbst die Rolle Mitwirkender an Storage-Blobdaten zu. Selbst wenn Sie der Kontobesitzer sind, benötigen Sie explizite Berechtigungen, um Datenvorgänge in Ihrem Speicherkonto ausführen zu können.

Denken Sie daran, die Platzhalterwerte in spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Gewähren des Zugriffs auf das Speicherkonto

Sie müssen Ihrer Web-App Zugriff auf das Speicherkonto gewähren, bevor Sie Blobs erstellen, lesen oder löschen können. In einem vorherigen Schritt haben Sie die in App Service ausgeführte Web-App mit einer verwalteten Identität konfiguriert. Mit der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) können Sie der verwalteten Identität Zugriff auf eine andere Ressource gewähren (wie für jeden anderen Sicherheitsprinzipal). Mit der Rolle „Mitwirkender an Storage-Blobdaten“ wird der Web-App (repräsentiert durch die systemseitig zugewiesene verwaltete Identität) Lese-, Schreib- und Löschzugriff auf den Blobcontainer und die Daten gewährt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto, um für Ihre Web-App Zugriff zu gewähren. Wählen Sie im linken Bereich die Option **Zugriffssteuerung (IAM)** und dann **Rollenzuweisungen** aus. Es wird eine Liste mit den Benutzern angezeigt, die Zugriff auf das Speicherkonto haben. Nun möchten Sie eine Rollenzuweisung einem Roboter hinzufügen (der App Service-Instanz, die Zugriff auf das Speicherkonto benötigt). Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen**.

Wählen Sie unter **Rolle** die Option **Mitwirkender an Storage-Blobdaten** aus, um Ihrer Web-App Lesezugriff auf Speicherblobs zu gewähren. Wählen Sie unter **Zugriff zuweisen zu** die Option **App Service** aus. Wählen Sie unter **Abonnement** Ihr Abonnement aus. Wählen Sie anschließend den App-Dienst aus, für den Sie Zugriff gewähren möchten. Wählen Sie **Speichern** aus.

:::image type="content" alt-text="Screenshot: Bildschirm „Rollenzuweisung hinzufügen“" source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

Ihre Web-App verfügt jetzt über Zugriff auf Ihr Speicherkonto.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Führen Sie das folgende Skript aus, um Ihrer Web-App (repräsentiert durch eine systemseitig zugewiesene verwaltete Identität) in Ihrem Speicherkonto die Rolle „Mitwirkender an Storage-Blobdaten“ zuzuweisen.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie das folgende Skript aus, um Ihrer Web-App (repräsentiert durch eine systemseitig zugewiesene verwaltete Identität) in Ihrem Speicherkonto die Rolle „Mitwirkender an Storage-Blobdaten“ zuzuweisen.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Zugreifen auf Blob Storage (.NET)

Die Klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) wird zum Abrufen von Tokenanmeldeinformationen für Ihren Code verwendet, um Anforderungen für Azure Storage zu autorisieren. Erstellen Sie eine Instanz der Klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), bei der die verwaltete Identität zum Abrufen von Token verwendet wird, und fügen Sie diese dem Dienstclient hinzu. Im folgenden Codebeispiel werden die authentifizierten Tokenanmeldeinformationen abgerufen und zum Erstellen eines Dienstclientobjekts verwendet, mit dem ein neues Blob hochgeladen wird.

Im [Beispiel auf GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity) können Sie sich diesen Code in einer Beispielanwendung ansehen.

### <a name="install-client-library-packages"></a>Installieren von Clientbibliothekspaketen

Installieren Sie das [Blob Storage-NuGet-Paket](https://www.nuget.org/packages/Azure.Storage.Blobs/), um Blob Storage zu nutzen, und das [NuGet-Paket mit der Azure Identity-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.Identity/), um die Authentifizierung mit Azure AD-Anmeldeinformationen durchzuführen. Installieren Sie die Clientbibliotheken über die .NET Core-Befehlszeilenschnittstelle oder die Paket-Manager-Konsole in Visual Studio.

# <a name="command-line"></a>[Befehlszeile](#tab/command-line)

Öffnen Sie eine Befehlszeile, und wechseln Sie zu dem Verzeichnis, in dem Ihre Projektdatei enthalten ist.

Führen Sie die Installationsbefehle aus.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Paket-Manager](#tab/package-manager)

Öffnen Sie das Projekt bzw. die Projektmappe in Visual Studio und dann die Konsole mit dem Befehl **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.

Führen Sie die Installationsbefehle aus.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Beispiel

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie dieses Tutorial abgeschlossen haben und die Web-App und die zugehörigen Ressourcen nicht mehr benötigen, sollten Sie [die von Ihnen erstellten Ressourcen bereinigen](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Erstellen einer systemseitig zugewiesenen verwalteten Identität
> * Erstellen eines Speicherkontos und Blob Storage-Containers
> * Zugreifen auf Speicher über eine Web-App mit verwalteten Identitäten

> [!div class="nextstepaction"]
> [App Service: Zugreifen auf Microsoft Graph im Namen des Benutzers](scenario-secure-app-access-microsoft-graph-as-user.md)