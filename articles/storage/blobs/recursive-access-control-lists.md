---
title: Rekursives Festlegen von ACLs für Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Sie können Zugriffssteuerungslisten für vorhandene untergeordnete Elemente eines übergeordneten Verzeichnisses rekursiv hinzufügen, aktualisieren und entfernen.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2544711054678ec1bb5c43d40e4497eec9af2941
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600335"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Rekursives Festlegen von Zugriffssteuerungslisten (ACLs) für Azure Data Lake Storage Gen2

Die Vererbung von Zugriffssteuerungslisten (ACLs) ist für neue untergeordnete Elemente, die unter einem übergeordneten Verzeichnis erstellt werden, bereits verfügbar. Sie können ACLs nun auch rekursiv für die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses hinzufügen, aktualisieren und entfernen, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen.

[Bibliotheken](#libraries) | [Beispiele](#code-samples) | [Bewährte Methoden](#best-practice-guidelines)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](create-data-lake-storage-account.md) für die Erstellung.

- Die richtigen Berechtigungen für die Ausführung rekursiver ACL-Prozesse. Die richtigen Berechtigungen umfassen eine der folgenden Optionen: 

  - Ein bereitgestellter [Sicherheitsprinzipal](../../role-based-access-control/overview.md#security-principal) für Azure Active Directory (AD), dem die Rolle [Speicherblob-Datenbesitzer](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) im Bereich des Zielcontainers, der übergeordneten Ressourcengruppe oder des Abonnements zugewiesen wurde   

  - Der Benutzer muss Besitzer des Zielcontainers oder -verzeichnisses sein, in dem Sie den rekursiven ACL-Prozess anwenden möchten. Dies schließt alle untergeordneten Elemente im Zielcontainer oder -verzeichnis ein. 

- Verständnis der Anwendung von Zugriffssteuerungslisten auf Verzeichnisse und Dateien. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Weitere Informationen zur Installation mit PowerShell, dem .NET SDK und dem Python-SDK finden Sie in diesem Artikel im Abschnitt **Einrichten Ihres Projekts**.

## <a name="set-up-your-project"></a>Einrichten des Projekts

Installieren Sie die erforderlichen Bibliotheken.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Überprüfen Sie mit dem folgenden Befehl, ob PowerShell Version `5.1` oder höher installiert ist.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Informationen zum Aktualisieren der PowerShell-Version finden Sie unter [Aktualisieren einer vorhandenen Windows PowerShell-Version](/powershell/scripting/install/installing-windows-powershell).
    
2. Installieren Sie das Modul **Az.Storage**.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Weitere Informationen zum Installieren von PowerShell-Modulen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Öffnen Sie [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-Befehlszeilenschnittstelle lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Überprüfen Sie mit dem folgenden Befehl, ob die installierte Version der Azure-Befehlszeilenschnittstelle `2.14.0` oder höher ist.

   ```azurecli
    az --version
   ```
   Wenn Ihre Version der Azure-Befehlszeilenschnittstelle kleiner als `2.14.0` ist, dann installieren Sie eine neuere Version. Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Öffnen Sie ein Befehlsfenster (z. B. Windows PowerShell).

2. Installieren Sie das Vorschaupaket „Azure.Storage.Files.DataLake“ aus Ihrem Projektverzeichnis mit dem Befehl `dotnet add package`.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Fügen Sie die folgenden using-Anweisungen am Anfang Ihrer Codedatei ein.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

Öffnen Sie zunächst [diese Seite](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake), und suchen Sie die neueste Version der Java-Bibliothek. Öffnen Sie dann die Datei *pom.xml* in Ihrem Text-Editor. Fügen Sie ein Abhängigkeitselement hinzu, das auf diese Version verweist.

Wenn Sie beabsichtigen, die Clientanwendung mithilfe von Azure Active Directory (AD) zu authentifizieren, fügen Sie der Azure-Geheimnisclientbibliothek eine Abhängigkeit hinzu. Informationen dazu finden Sie unter [Hinzufügen des Pakets für die Geheimnisclientbibliothek zu Ihrem Projekt](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Fügen Sie als Nächstes Ihrer Codedatei die folgenden Import-Anweisungen hinzu.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Laden Sie die [Azure Data Lake Storage-Clientbibliothek für Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D) herunter.

2. Installieren Sie die heruntergeladene Bibliothek mit [pip](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Fügen Sie diese Importanweisungen am Anfang Ihrer Codedatei hinzu.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>Herstellen einer Verbindung mit Ihrem Konto

Sie können die Verbindung mit Azure Active Directory (AD) oder einem Kontoschlüssel herstellen. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Öffnen Sie ein Windows PowerShell-Befehlsfenster, melden Sie sich dann mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, das Sie erstellen und in dem Sie Verzeichnisse verwalten möchten. In diesem Beispiel ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Wählen Sie als Nächstes aus, wie Ihre Befehle die Autorisierung für das Speicherkonto abrufen sollen. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Option 1: Abrufen der Autorisierung mithilfe von Azure Active Directory (AD)

Bei diesem Ansatz stellt das System sicher, dass Ihr Benutzerkonto über die entsprechenden Azure RBAC-Zuweisungen (Azure Role-Based Access Control, rollenbasierte Zugriffssteuerung von Azure) und ACL-Berechtigungen verfügt. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

In der folgenden Tabelle werden die einzelnen unterstützten Rollen und ihre Einstellungsfunktionen für Zugriffssteuerungslisten aufgeführt.

|Role|ACL-Einstellungsfunktion|
|--|--|
|[Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle Verzeichnisse und Dateien im Konto|
|[Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Nur Verzeichnisse und Dateien im Besitz des Sicherheitsprinzipals|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Option 2: Abrufen der Autorisierung mithilfe des Speicherkontoschlüssels

Bei diesem Ansatz überprüft das System nicht die Azure RBAC- oder ACL-Berechtigungen.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Wenn Sie die Azure-Befehlszeilenschnittstelle lokal verwenden, führen Sie den Anmeldebefehl aus.

   ```azurecli
   az login
   ```

   Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.

   Öffnen Sie andernfalls die Browserseite [https://aka.ms/devicelogin](https://aka.ms/devicelogin), und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein. Melden Sie sich dann im Browser mit Ihren Anmeldeinformationen an.

   Weitere Informationen zu verschiedenen Authentifizierungsmethoden finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure CLI](./authorize-data-operations-cli.md).

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, auf dem Ihre statische Website gehostet wird.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

> [!NOTE]
> Das in diesem Artikel dargestellte Beispiel zeigt die Azure Active Directory (AD)-Autorisierung. Weitere Informationen zu Autorisierungsmethoden finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure CLI](./authorize-data-operations-cli.md).

### <a name="net"></a>[.NET](#tab/dotnet)

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz erstellen, die das Speicherkonto darstellt.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (AD)

Sie können die [Azure-Identitätsclientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

Nachdem Sie das Paket installiert haben, fügen Sie am Anfang der Codedatei diese using-Anweisung hinzu.

```csharp
using Azure.Identity;
```

Rufen Sie die Client-ID, ein Clientgeheimnis und die Mandanten-ID ab. Informationen zur Vorgehensweise finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md). Im Rahmen dieses Prozesses müssen Sie dem Sicherheitsprinzipal eine der folgenden [Azure RBAC-Rollen](../../role-based-access-control/overview.md) (Azure Role-Based Access Control) zuweisen. 

|Role|ACL-Einstellungsfunktion|
|--|--|
|[Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle Verzeichnisse und Dateien im Konto|
|[Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Nur Verzeichnisse und Dateien im Besitz des Sicherheitsprinzipals|

In diesem Beispiel wird eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dieser Ansatz stellt die einfachste Möglichkeit dar, eine Verbindung mit einem Konto herzustellen. 

In diesem Beispiel wird eine [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)-Instanz mithilfe eines Kontoschlüssels erstellt.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

### <a name="java"></a>[Java](#tab/java)

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine **DataLakeServiceClient**-Instanz erstellen, die das Speicherkonto darstellt. 

#### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen. 

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe eines Kontoschlüssels erstellt.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (Azure AD)

Sie können die [Azure-Identitätsclientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.  Informationen zum Abrufen dieser Werte finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

### <a name="python"></a>[Python](#tab/python)

Wenn Sie die Codeausschnitte in diesem Artikel verwenden möchten, müssen Sie eine **DataLakeServiceClient**-Instanz erstellen, die das Speicherkonto darstellt. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Herstellen einer Verbindung mit Azure Active Directory (AD)

Sie können die [Azure-Identitätsclientbibliothek für Python](https://pypi.org/project/azure-identity/) verwenden, um Ihre Anwendung bei Azure AD zu authentifizieren.

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe einer Client-ID, eines Clientgeheimnisses und einer Mandanten-ID erstellt.  Informationen zum Abrufen dieser Werte finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md). Im Rahmen dieses Prozesses müssen Sie dem Sicherheitsprinzipal eine der folgenden [Azure RBAC-Rollen](../../role-based-access-control/overview.md) (Azure Role-Based Access Control) zuweisen. 

|Role|ACL-Einstellungsfunktion|
|--|--|
|[Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle Verzeichnisse und Dateien im Konto|
|[Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Nur Verzeichnisse und Dateien im Besitz des Sicherheitsprinzipals|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Weitere Beispiele finden Sie in der Dokumentation zur [Azure-Identitätsclientbibliothek für Python](https://pypi.org/project/azure-identity/).

### <a name="connect-by-using-an-account-key"></a>Herstellen einer Verbindung per Kontoschlüssel

Dies ist die einfachste Möglichkeit, eine Verbindung mit einem Konto herzustellen. 

In diesem Beispiel wird eine **DataLakeServiceClient**-Instanz mithilfe eines Kontoschlüssels erstellt.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Ersetzen Sie den Platzhalterwert `storage_account_name` durch den Namen Ihres Speicherkontos.

- Ersetzen Sie den Platzhalterwert `storage_account_key` durch den Zugriffsschlüssel Ihres Speicherkontos.

---

## <a name="set-an-acl-recursively"></a>Rekursives Festlegen einer Zugriffssteuerungsliste

Wenn Sie eine Zugriffssteuerungsliste *festlegen*, **ersetzen** Sie die gesamte Zugriffssteuerungsliste einschließlich aller Einträge. Wenn Sie die Berechtigungsebene eines Sicherheitsprinzipals ändern oder der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen möchten, ohne dass sich dies auf bereits vorhandene Einträge auswirkt, sollten Sie die Zugriffssteuerungsliste stattdessen *aktualisieren*. Informationen dazu, wie eine Zugriffssteuerungsliste aktualisiert wird, anstatt sie zu ersetzen, finden Sie im Abschnitt [Rekursives Aktualisieren einer Zugriffssteuerungsliste](#update-an-acl-recursively) in diesem Artikel.  

Wenn Sie die Zugriffssteuerungsliste *festlegen*, müssen Sie einen Eintrag für den besitzenden Benutzer, einen Eintrag für die besitzende Gruppe und einen Eintrag für alle anderen Benutzer hinzufügen. Weitere Informationen zum besitzenden Benutzer, zur besitzenden Gruppe und zu allen anderen Benutzern finden Sie unter [Benutzer und Identitäten](data-lake-storage-access-control.md#users-and-identities). 

Dieser Abschnitt enthält Beispiele für die Festlegung einer Zugriffssteuerungsliste.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zum rekursiven Festlegen einer Zugriffssteuerungsliste verwenden Sie das Cmdlet **Set-AzDataLakeGen2AclRecursive**.

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Mit diesen Einträgen werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag festlegen möchten, verwenden Sie den Parameter **-DefaultScope**, wenn Sie den Befehl **Set-AzDataLakeGen2ItemAclObject** ausführen. Beispiel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im Referenzartikel zu [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Zum rekursiven Festlegen einer Zugriffssteuerungsliste verwenden Sie den Befehl [az storage fs access set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive).

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Mit diesen Einträgen werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag festlegen möchten, fügen Sie jedem Eintrag das Präfix `default:` hinzu. Zum Beispiel: `default:user::rwx` oder `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Sie legen eine Zugriffssteuerungsliste rekursiv fest, indem Sie die **DataLakeDirectoryClient.SetAccessControlRecursiveAsync**-Methode aufrufen. Übergeben Sie dieser Methode eine [Liste](/dotnet/api/system.collections.generic.list-1) der [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Jedes [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-Element definiert einen ACL-Eintrag. 

Wenn Sie einen **standardmäßigen** ACL-Eintrag festlegen möchten, dann können Sie die Eigenschaft [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) der Eigenschaft [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) auf **true** festlegen. 

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob die Standard-ACL festgelegt werden soll. Dieser Parameter wird im Konstruktor von [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) verwendet. Mit den Einträgen der ACL werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Legen Sie eine Zugriffssteuerungsliste rekursiv fest, indem Sie die Methode **DataLakeDirectoryClient.setAccessControlRecursive** aufrufen. Übergeben Sie dieser Methode eine [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Objekte. Jedes [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Element definiert einen ACL-Eintrag. 

Wenn Sie einen **ACL-Standardeintrag** festlegen möchten, rufen Sie die **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse auf und übergeben den Wert **true**. 

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob die Standard-ACL festgelegt werden soll. Dieser Parameter wird bei jedem Aufruf der **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse verwendet. Mit den Einträgen der ACL werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

```java
static public void SetACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){
    
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create owner entry.
    PathAccessControlEntry ownerEntry = new PathAccessControlEntry();

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    ownerEntry.setDefaultScope(isDefaultScope);
    ownerEntry.setAccessControlType(AccessControlType.USER);
    ownerEntry.setPermissions(ownerPermission);

    pathAccessControlEntries.add(ownerEntry);

    // Create group entry.
    PathAccessControlEntry groupEntry = new PathAccessControlEntry();

    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    groupEntry.setDefaultScope(isDefaultScope);
    groupEntry.setAccessControlType(AccessControlType.GROUP);
    groupEntry.setPermissions(groupPermission);

    pathAccessControlEntries.add(groupEntry);

    // Create other entry.
    PathAccessControlEntry otherEntry = new PathAccessControlEntry();

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setExecutePermission(false).setReadPermission(false).setWritePermission(false);

    otherEntry.setDefaultScope(isDefaultScope);
    otherEntry.setAccessControlType(AccessControlType.OTHER);
    otherEntry.setPermissions(otherPermission);

    pathAccessControlEntries.add(otherEntry);

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.setAccessControlRecursive(pathAccessControlEntries);        

}
```

### <a name="python"></a>[Python](#tab/python)

Sie legen eine Zugriffssteuerungsliste rekursiv fest, indem Sie die **DataLakeDirectoryClient.set_access_control_recursive**-Methode aufrufen.

Wenn Sie einen **standardmäßigen** ACL-Eintrag festlegen möchten, fügen Sie die Zeichenfolge `default:` an den Anfang jeder ACL-Eintragszeichenfolge an. 

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. 

Diese Methode akzeptiert einen booleschen Parameter namens `is_default_scope`, der angibt, ob die Standard-ACL festgelegt werden soll. wenn dieser Parameter `True` ist, wird der Liste der ACL-Einträge die Zeichenfolge `default:` vorangestellt. 

Mit den Einträgen der ACL werden dem zuständigen Benutzer Lese-, Schreib- und Ausführungsberechtigungen und der zuständigen Gruppe nur Lese- und Ausführungsberechtigungen gewährt, während allen anderen kein Zugriff gewährt wird. Der letzte ACL-Eintrag in diesem Beispiel gibt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen. Diese Einträge erteilen dem besitzenden Benutzer Lese-, Schreib- und Ausführungsberechtigungen, der besitzenden Gruppe nur Lese- und Ausführungsberechtigungen und allen anderen keinen Zugriff. Der letzte ACL-Eintrag in diesem Beispiel gewährt einem bestimmten Benutzer mit der Objekt-ID „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ Lese- und Ausführungsberechtigungen.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches verarbeitet, finden Sie im [Python-Beispiel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="update-an-acl-recursively"></a>Rekursives Aktualisieren einer Zugriffssteuerungsliste

Wenn Sie eine Zugriffssteuerungsliste *aktualisieren*, ändern Sie die Zugriffssteuerungsliste, anstatt sie zu ersetzen. Beispielsweise können Sie der Zugriffssteuerungsliste einen neuen Sicherheitsprinzipal hinzufügen, ohne dass sich dies auf andere Sicherheitsprinzipale auswirkt, die in der Zugriffssteuerungsliste aufgelistet sind.  Informationen dazu, wie eine Zugriffssteuerungsliste ersetzt wird, anstatt sie zu aktualisieren, finden Sie im Abschnitt [Rekursives Festlegen einer Zugriffssteuerungsliste](#set-an-acl-recursively) in diesem Artikel. 

Um eine Zugriffssteuerungsliste zu aktualisieren, erstellen Sie ein neues Zugriffssteuerungsliste-Objekt mit dem Zugriffssteuerungslisteneintrag, den Sie aktualisieren möchten, und verwenden Sie dieses Objekt dann im Aktualisierungsvorgang der Zugriffssteuerungsliste. Rufen Sie nicht die vorhandene Zugriffssteuerungsliste ab, stellen Sie lediglich die zu aktualisierenden Zugriffssteuerungslisteneinträge bereit.

Dieser Abschnitt enthält Beispiele, wie eine Zugriffssteuerungsliste aktualisiert wird.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sie aktualisieren eine Zugriffssteuerungsliste rekursiv mithilfe des Cmdlets **Update-AzDataLakeGen2AclRecursive**. 

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag aktualisieren möchten, verwenden Sie den Parameter **-DefaultScope**, wenn Sie den Befehl **Set-AzDataLakeGen2ItemAclObject** ausführen. Beispiel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches aktualisiert, finden Sie im Referenzartikel zu [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Zum rekursiver Aktualisieren einer Zugriffssteuerungsliste verwenden Sie den Befehl [az storage fs access update-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive). 

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag aktualisieren möchten, fügen Sie jedem Eintrag das Präfix `default:` hinzu. Beispiel: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

Sie aktualisieren eine Zugriffssteuerungsliste rekursiv, indem Sie die **DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync**-Methode aufrufen.  Übergeben Sie dieser Methode eine [Liste](/dotnet/api/system.collections.generic.list-1) der [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Jedes [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-Element definiert einen ACL-Eintrag. 

Wenn Sie einen **standardmäßigen** ACL-Eintrag aktualisieren möchten, dann können Sie die Eigenschaft [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) der Eigenschaft [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) auf **true** festlegen. 

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob die Standard-ACL aktualisiert werden soll. Dieser Parameter wird im Konstruktor von [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) verwendet.

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches aktualisiert, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Aktualisieren Sie eine Zugriffssteuerungsliste rekursiv, indem Sie die Methode **DataLakeDirectoryClient.updateAccessControlRecursive** aufrufen.  Übergeben Sie dieser Methode eine [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Objekte. Jedes [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Element definiert einen ACL-Eintrag. 

Wenn Sie einen **ACL-Standardeintrag** aktualisieren möchten, können Sie die **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse aufrufen und den Wert **true** übergeben. 

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob die Standard-ACL aktualisiert werden soll. Dieser Parameter wird beim Aufruf der **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse verwendet. 

```java
static public void UpdateACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.updateAccessControlRecursive(pathAccessControlEntries);          
}
```

### <a name="python"></a>[Python](#tab/python)

Sie aktualisieren eine Zugriffssteuerungsliste rekursiv, indem Sie die **DataLakeDirectoryClient.update_access_control_recursive**-Methode aufrufen. Wenn Sie einen **standardmäßigen** ACL-Eintrag aktualisieren möchten, fügen Sie die Zeichenfolge `default:` an den Anfang jeder ACL-Eintragszeichenfolge an. 

Dieses Beispiel aktualisiert einen ACL-Eintrag mit Schreibberechtigung.

In diesem Beispiel wird die ACL des Verzeichnisses `my-parent-directory` festgelegt. Diese Methode akzeptiert einen booleschen Parameter namens `is_default_scope`, der angibt, ob die Standard-ACL aktualisiert werden soll. Wenn dieser Parameter `True` ist, wird dem aktualisierten ACL-Eintrag die Zeichenfolge `default:` vorangestellt.  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches verarbeitet, finden Sie im [Python-Beispiel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="remove-acl-entries-recursively"></a>Rekursives Entfernen von ACL-Einträgen

Sie können einen oder mehrere ACL-Einträge rekursiv entfernen. Um einen ACL-Eintrag zu entfernen, erstellen Sie ein neues ACL-Objekt für den zu entfernenden ACL-Eintrag, und verwenden Sie dieses Objekt dann im Entfernungsvorgang der ACL. Rufen Sie nicht die vorhandene ACL ab, stellen Sie lediglich die zu entfernenden ACL-Einträge bereit. 

Dieser Abschnitt enthält Beispiele, wie eine ACL entfernt wird. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sie entfernen ACL-Einträge mithilfe des Cmdlets **Remove-AzDataLakeGen2AclRecursive**. 

In diesem Beispiel wird ein ACL-Eintrag aus dem Stammverzeichnis des Containers entfernt.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag entfernen möchten, verwenden Sie den Parameter **-DefaultScope**, wenn Sie den Befehl **Set-AzDataLakeGen2ItemAclObject** ausführen. Beispiel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches entfernt, finden Sie im Referenzartikel zu [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Zum Entfernen von ACL-Einträgen verwenden Sie den Befehl [az storage fs access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive). 

In diesem Beispiel wird ein ACL-Eintrag aus dem Stammverzeichnis des Containers entfernt.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Wenn Sie einen **standardmäßigen** ACL-Eintrag entfernen möchten, fügen Sie jedem Eintrag das Präfix `default:` hinzu. Beispiel: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

Sie entfernen ACL-Einträge, indem Sie die **DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync**-Methode aufrufen. Übergeben Sie dieser Methode eine [Liste](/dotnet/api/system.collections.generic.list-1) der [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Jedes [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-Element definiert einen ACL-Eintrag. 

Wenn Sie einen **standardmäßigen** ACL-Eintrag entfernen möchten, dann können Sie die Eigenschaft [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) der Eigenschaft [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) auf **true** festlegen. 

In diesem Beispiel wird ein ACL-Eintrag aus der Zugriffssteuerungsliste des Verzeichnisses `my-parent-directory` entfernt. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob der Eintrag aus der Standard-ACL entfernt werden soll. Dieser Parameter wird im Konstruktor von [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) verwendet.

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches entfernt, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Entfernen Sie ACL-Einträge, indem Sie die Methode **DataLakeDirectoryClient.removeAccessControlRecursive** aufrufen. Übergeben Sie dieser Methode eine [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Objekte. Jedes [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Element definiert einen ACL-Eintrag. 

Wenn Sie einen **ACL-Standardeintrag** entfernen möchten, rufen Sie die **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse auf und übergeben den Wert **true**.  

In diesem Beispiel wird ein ACL-Eintrag aus der Zugriffssteuerungsliste des Verzeichnisses `my-parent-directory` entfernt. Diese Methode akzeptiert einen booleschen Parameter namens `isDefaultScope`, der angibt, ob der Eintrag aus der Standard-ACL entfernt werden soll. Dieser Parameter wird beim Aufruf der **setDefaultScope**-Methode der [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Klasse verwendet.


```java
static public void RemoveACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathRemoveAccessControlEntry> pathRemoveAccessControlEntries = 
        new ArrayList<PathRemoveAccessControlEntry>();

    // Create named user entry.
    PathRemoveAccessControlEntry userEntry = new PathRemoveAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"); 
    
    pathRemoveAccessControlEntries.add(userEntry);
    
    directoryClient.removeAccessControlRecursive(pathRemoveAccessControlEntries);      

}
```

### <a name="python"></a>[Python](#tab/python)

Sie entfernen ACL-Einträge, indem Sie die **DataLakeDirectoryClient.remove_access_control_recursive**-Methode aufrufen. Wenn Sie einen **standardmäßigen** ACL-Eintrag entfernen möchten, fügen Sie die Zeichenfolge `default:` an den Anfang der ACL-Eintragszeichenfolge an. 

In diesem Beispiel wird ein ACL-Eintrag aus der Zugriffssteuerungsliste des Verzeichnisses `my-parent-directory` entfernt. Diese Methode akzeptiert einen booleschen Parameter namens `is_default_scope`, der angibt, ob der Eintrag aus der Standard-ACL entfernt werden soll. Wenn dieser Parameter `True` ist, wird dem aktualisierten ACL-Eintrag die Zeichenfolge `default:` vorangestellt. 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches verarbeitet, finden Sie im [Python-Beispiel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="recover-from-failures"></a>Wiederherstellen nach Fehlern

Es können Laufzeit- oder Berechtigungsfehler auftreten. Starten Sie den Prozess bei Laufzeitfehlern vom Anfang neu. Berechtigungsfehler können auftreten, wenn der Sicherheitsprinzipal nicht über ausreichende Berechtigungen verfügt, um die Zugriffssteuerungsliste eines Verzeichnisses oder einer Datei zu ändern, die sich in der geänderten Verzeichnishierarchie befindet. Beheben Sie das Berechtigungsproblem, und setzen Sie dann den Prozess ab dem Zeitpunkt des Fehlers mithilfe eines Fortsetzungstokens fort, oder starten Sie den Prozess vom Anfang neu. Sie müssen das Fortsetzungstoken nicht verwenden, wenn Sie vom Anfang neu starten. Sie können ACL-Einträge ohne negative Auswirkung erneut anwenden.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Dieses Beispiel gibt Ergebnisse an die Variable zurück und leitet fehlerhafte Einträge dann an eine formatierte Tabelle weiter.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Basierend auf der Ausgabe der Tabelle können Sie alle Berechtigungsfehler beheben und die Ausführung dann mit dem Fortsetzungstoken fortsetzen.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im Referenzartikel zu [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Im Fall eines Fehlers können Sie ein Fortsetzungstoken zurückgeben, indem Sie den Parameter `--continue-on-failure` auf `false` festlegen. Nachdem Sie die Fehler behoben haben, können Sie den Prozess ab dem Fehlerzeitpunkt fortsetzen. Dazu führen Sie den Befehl erneut aus, und legen Sie dann den Parameter `--continuation` auf das Fortsetzungstoken fest. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

In diesem Beispiel wird im Fall eines Fehlers ein Fortsetzungstoken zurückgegeben. Die Anwendung kann diese Beispielmethode erneut aufrufen, nachdem der Fehler behoben wurde, und das Fortsetzungstoken übergeben. Wenn diese Beispielmethode zum ersten Mal aufgerufen wird, kann die Anwendung den Wert `null` für den Fortsetzungstokenparameter übergeben. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

In diesem Beispiel wird im Fall eines Fehlers ein Fortsetzungstoken zurückgegeben. Die Anwendung kann diese Beispielmethode erneut aufrufen, nachdem der Fehler behoben wurde, und das Fortsetzungstoken übergeben. Wenn diese Beispielmethode zum ersten Mal aufgerufen wird, kann die Anwendung den Wert `null` für den Fortsetzungstokenparameter übergeben. 

```java
static public String ResumeSetACLRecursively(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList, 
String continuationToken){

    try{
        PathSetAccessControlRecursiveOptions options = new PathSetAccessControlRecursiveOptions(accessControlList);
        
        options.setContinuationToken(continuationToken);
    
        Response<AccessControlChangeResult> accessControlChangeResult =  
            directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

        if (accessControlChangeResult.getValue().getCounters().getFailedChangesCount() > 0)
        {
            continuationToken =
                accessControlChangeResult.getValue().getContinuationToken();
        }
    
        return continuationToken;

    }
    catch(Exception ex){
    
        System.out.println(ex.toString());
        return continuationToken;
    }
}
```

### <a name="python"></a>[Python](#tab/python)

In diesem Beispiel wird im Fall eines Fehlers ein Fortsetzungstoken zurückgegeben. Die Anwendung kann diese Beispielmethode erneut aufrufen, nachdem der Fehler behoben wurde, und das Fortsetzungstoken übergeben. Wenn diese Beispielmethode zum ersten Mal aufgerufen wird, kann die Anwendung den Wert ``None`` für den Fortsetzungstokenparameter übergeben. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches verarbeitet, finden Sie im [Python-Beispiel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

Wenn Sie möchten, dass der Prozess ohne Unterbrechung durch Berechtigungsfehler durchgeführt wird, können Sie dies angeben.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Dieses Beispiel verwendet den Parameter `ContinueOnFailure`, sodass die Ausführung auch bei einem Berechtigungsfehler im Vorgang fortgesetzt wird. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im Referenzartikel zu [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um sicherzustellen, dass der Prozess ohne Unterbrechung durchgeführt wird, legen Sie den Parameter `--continue-on-failure` auf `true` fest. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

Um sicherzustellen, dass der Prozess ohne Unterbrechung durchgeführt wird, übergeben Sie ein **AccessControlChangedOptions**-Objekt, und legen Sie die Eigenschaft **ContinueOnFailure** dieses Objekts auf ``true`` fest.

In diesem Beispiel werden ACL-Einträge rekursiv festgelegt. Wenn dieser Code auf einen Berechtigungsfehler stößt, wird dieser Fehler aufgezeichnet und die Ausführung fortgesetzt. In diesem Beispiel wird die Anzahl der Fehler an die Konsole ausgegeben. 

```cs
public async Task ContinueOnFailureAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient, 
    List<PathAccessControlItem> accessControlList)
{
    var accessControlChangeResult = 
        await directoryClient.SetAccessControlRecursiveAsync(
            accessControlList, null, new AccessControlChangeOptions() 
            { ContinueOnFailure = true });

    var counters = accessControlChangeResult.Value.Counters;

    Console.WriteLine("Number of directories changed: " +
        counters.ChangedDirectoriesCount.ToString());

    Console.WriteLine("Number of files changed: " +
        counters.ChangedFilesCount.ToString());

    Console.WriteLine("Number of failures: " +
        counters.FailedChangesCount.ToString());
}
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches festlegt, finden Sie im [.NET-Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Um sicherzustellen, dass der Prozess ohne Unterbrechung durchgeführt wird, rufen Sie die **setContinueOnFailure**-Methode eines [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)-Objekts auf, und übergeben Sie den Wert **true**.

In diesem Beispiel werden ACL-Einträge rekursiv festgelegt. Wenn dieser Code auf einen Berechtigungsfehler stößt, wird dieser Fehler aufgezeichnet und die Ausführung fortgesetzt. In diesem Beispiel wird die Anzahl der Fehler an die Konsole ausgegeben. 

```java
static public void ContinueOnFailure(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList){
    
    PathSetAccessControlRecursiveOptions options = 
        new PathSetAccessControlRecursiveOptions(accessControlList);
        
    options.setContinueOnFailure(true);
    
    Response<AccessControlChangeResult> accessControlChangeResult =  
        directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

    AccessControlChangeCounters counters = accessControlChangeResult.getValue().getCounters();

    System.out.println("Number of directories changes: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of files changed: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of failures: " + 
        counters.getChangedDirectoriesCount());
}
```

### <a name="python"></a>[Python](#tab/python)

Um sicherzustellen, dass der Prozess ohne Unterbrechung durchgeführt wird, übergeben Sie kein Fortsetzungstoken an die **DataLakeDirectoryClient.set_access_control_recursive**-Methode.

In diesem Beispiel werden ACL-Einträge rekursiv festgelegt. Wenn dieser Code auf einen Berechtigungsfehler stößt, wird dieser Fehler aufgezeichnet und die Ausführung fortgesetzt. In diesem Beispiel wird die Anzahl der Fehler an die Konsole ausgegeben. 

```python
def continue_on_failure():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl)

        print("Summary: {} directories and {} files were updated successfully, {} failures were counted."
          .format(acl_change_result.counters.directories_successful, acl_change_result.counters.files_successful,
                  acl_change_result.counters.failure_count))
        
    except Exception as e:
     print(e)
```

Ein Beispiel, das Zugriffssteuerungslisten durch Angabe einer Batchgröße rekursiv in Batches verarbeitet, finden Sie im [Python-Beispiel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="resources"></a>Ressourcen

Dieser Abschnitt enthält Links zu Bibliotheken und Codebeispielen.

#### <a name="libraries"></a>Bibliotheken

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)
- [REST](/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>Codebeispiele

- Mit PowerShell: [Info](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D) | [Beispiel](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- Azure CLI: [Beispiel](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- .NET: [Info](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%2fSc2aDVW3De4A%2fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0) | [Beispiel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Info](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0) | [Beispiel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>Richtlinien zu bewährten Methoden

Dieser Abschnitt enthält einige Richtlinien zu bewährten Methoden für das rekursive Festlegen von Zugriffssteuerungslisten. 

#### <a name="handling-runtime-errors"></a>Behandeln von Laufzeitfehlern

Ein Laufzeitfehler kann aus vielen Gründen auftreten (z. B. bei einem Ausfall oder einem Clientkonnektivitätsproblem). Wenn ein Laufzeitfehler auftritt, starten Sie den rekursiven ACL-Prozess neu. Zugriffssteuerungslisten können ohne negative Auswirkungen erneut auf Elemente angewandt werden. 

#### <a name="handling-permission-errors-403"></a>Behandeln von Berechtigungsfehlern (403)

Wenn beim Ausführen eines rekursiven ACL-Prozesses eine Zugriffssteuerungsausnahme auftritt, verfügt Ihr AD-[Sicherheitsprinzipal](../../role-based-access-control/overview.md#security-principal) möglicherweise nicht über ausreichende Berechtigungen zum Anwenden einer Zugriffssteuerungsliste auf eines der untergeordneten Elemente in der Verzeichnishierarchie. Wenn ein Berechtigungsfehler auftritt, wird der Prozess angehalten, und es wird ein Fortsetzungstoken bereitgestellt. Beheben Sie das Berechtigungsproblem, und verwenden Sie dann das Fortsetzungstoken, um das restliche Dataset zu verarbeiten. Die Verzeichnisse und Dateien, die bereits erfolgreich verarbeitet wurden, müssen nicht erneut verarbeitet werden. Sie können den rekursiven ACL-Prozess auch neu starten. Zugriffssteuerungslisten können ohne negative Auswirkungen erneut auf Elemente angewandt werden. 

#### <a name="credentials"></a>Anmeldeinformationen 

Es wird empfohlen, einen Azure AD-Sicherheitsprinzipal bereitzustellen, dem die Rolle [Speicherblob-Datenbesitzer](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) im Bereich des Zielspeicherkontos oder -containers zugewiesen wurde. 

#### <a name="performance"></a>Leistung 

Um die Latenz zu verringern, empfiehlt es sich, den rekursiven ACL-Prozess auf einem virtuellen Azure-Computer (VM) auszuführen, der sich in derselben Region wie das Speicherkonto befindet. 

#### <a name="acl-limits"></a>Einschränkungen bei Zugriffssteuerungslisten

Die maximale Anzahl von Zugriffssteuerungslisten, die Sie auf ein Verzeichnis oder eine Datei anwenden können, beträgt 32 ACLs für den Zugriff und 32 Standard-ACLs. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

## <a name="see-also"></a>Weitere Informationen

- [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)
- [Bekannte Probleme](data-lake-storage-known-issues.md)