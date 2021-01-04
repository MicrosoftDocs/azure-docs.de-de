---
title: Autorisieren des Zugriffs auf Daten mit einer verwalteten Identität
titleSuffix: Azure Storage
description: Mithilfe von verwalteten Identitäten für Azure-Ressourcen können Sie den Zugriff auf Blob- und Warteschlangendaten aus Anwendungen autorisieren, die auf virtuellen Azure-Computern, in Funktions-Apps und anderen ausgeführt werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: ccc545b15f16879582c671b082cab40f6b11aa08
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778970"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorisieren des Zugriffs auf Blob- und Warteschlangendaten mit verwalteten Identitäten für Azure-Ressourcen

Azure Blob Storage und Azure Queue Storage unterstützen die Azure Active Directory-Authentifizierung (Azure AD-Authentifizierung) mit [verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md). Sie können verwaltete Identitäten für Azure-Ressourcen verwenden, um den Zugriff auf Blob- und Warteschlangendaten mithilfe von Azure AD-Anmeldeinformationen über Anwendungen zu autorisieren, die auf virtuellen Azure-Computern, in Funktions-Apps, in VM-Skalierungsgruppen und anderen Diensten ausgeführt werden. Durch Verwendung von verwalteten Identitäten für Azure-Ressourcen zusammen mit der Azure AD-Authentifizierung können Sie vermeiden, dass Anmeldeinformationen mit den in der Cloud ausgeführten Anwendungen gespeichert werden.  

In diesem Artikel wird die Autorisierung des Zugriffs auf Blob- oder Warteschlangendaten über einen virtuellen Azure-Computer unter Verwendung verwalteter Identitäten erläutert. Darüber hinaus erfahren Sie, wie Sie Ihren Code in der Entwicklungsumgebung testen.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivieren von verwalteten Identitäten auf einem virtuellen Computer

Damit Sie verwaltete Identitäten für Azure-Ressourcen zum Autorisieren des Zugriffs auf Blobs und Warteschlangen über Ihren virtuellen Computer verwenden können, müssen Sie zunächst verwaltete Identitäten für Ressourcen auf dem virtuellen Computer aktivieren. Informationen zum Aktivieren von verwalteten Identitäten für Azure-Ressourcen finden Sie in diesen Artikeln:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-Vorlage](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-Clientbibliotheken](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Authentifizieren mit der Azure Identity-Bibliothek

Mit der Azure Identity-Clientbibliothek wird die Azure AD-Tokenauthentifizierung für das [Azure SDK](https://github.com/Azure/azure-sdk) unterstützt. Die neuesten Versionen der Azure Storage-Clientbibliotheken für .NET, Java, Python und JavaScript sind in die Azure Identity-Bibliothek integriert und stellen so eine einfache und sichere Möglichkeit dar, ein OAuth 2.0-Token für die Autorisierung von Azure Storage-Anforderungen abzurufen.

Ein Vorteil der Azure Identity-Clientbibliothek besteht darin, dass Sie den gleichen Code verwenden können, um zu authentifizieren, ob Ihre Anwendung in der Entwicklungsumgebung oder in Azure läuft. Die Azure Identity-Clientbibliothek für .NET dient zur Authentifizierung eines Sicherheitsprinzipals. Wenn Ihr Code in Azure ausgeführt wird, ist der Sicherheitsprinzipal eine verwaltete Identität für Azure-Ressourcen. In der Entwicklungsumgebung ist die verwaltete Identität nicht vorhanden, sodass die Clientbibliothek für Testzwecke entweder den Benutzer oder einen Dienstprinzipal authentifiziert.

Nach der Authentifizierung erhält die Azure Identity-Clientbibliothek Tokenanmeldeinformationen. Diese Tokenanmeldeinformationen werden im Dienstclientobjekt gekapselt, das Sie erstellen, um Vorgänge für Azure Storage auszuführen. Die Bibliothek führt diesen Schritt automatisch für Sie durch und ruft die entsprechenden Tokenanmeldeinformationen ab.

Weitere Informationen zur Azure Identity-Clientbibliothek für .NET finden Sie unter [Azure Identity-Clientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Die Referenzdokumentation zur Azure Identity-Clientbibliothek finden Sie unter [Azure.Identity-Namespace](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Zuweisen von Azure-Rollen für den Zugriff auf Daten

Wenn ein Azure AD-Sicherheitsprinzipal versucht, auf Blob- oder Warteschlangendaten zuzugreifen, muss dieser Sicherheitsprinzipal über Berechtigungen für die Ressource verfügen. Dem Sicherheitsprinzipal muss eine Azure-Rolle zugewiesen werden, die den Zugriff auf Blob- oder Warteschlangendaten in Azure Storage ermöglicht. Dabei spielt es keine Rolle, ob es sich beim Sicherheitsprinzipal um eine verwaltete Identität in Azure oder um ein Azure AD-Benutzerkonto handelt, mit der bzw. dem Code in der Entwicklungsumgebung ausgeführt wird. Informationen zur Zuweisung von Berechtigungen per Azure RBAC finden Sie unter **Autorisieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory** im Abschnitt [Zuweisen von Azure-Rollen für Zugriffsrechte](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

> [!NOTE]
> Wenn Sie ein Azure Storage-Konto erstellen, erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten über Azure AD. Sie müssen sich selbst explizit eine Azure-Rolle für Azure Storage zuweisen. Sie können sie auf Ebene Ihres Abonnements, einer Ressourcengruppe, eines Speicherkontos oder eines Containers oder einer Warteschlange zuordnen.
>
> Bevor Sie sich eine Rolle für den Datenzugriff zuweisen, können Sie bereits über das Azure-Portal auf Daten in Ihrem Speicherkonto zugreifen, da das Azure-Portal auch den Kontoschlüssel für den Datenzugriff nutzen kann. Weitere Informationen finden Sie unter [Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal](../blobs/authorize-data-operations-portal.md).

### <a name="authenticate-the-user-in-the-development-environment"></a>Authentifizieren des Benutzers in der Entwicklungsumgebung

Wenn Ihr Code in der Entwicklungsumgebung ausgeführt wird, erfolgt die Authentifizierung ggf. automatisch. Abhängig von den verwendeten Tools kann aber auch eine Browseranmeldung erforderlich sein. Microsoft Visual Studio unterstützt beispielsweise einmaliges Anmelden (SSO), sodass automatisch das aktive Azure AD-Benutzerkonto für die Authentifizierung verwendet wird. Weitere Informationen zu SSO finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../../active-directory/manage-apps/what-is-single-sign-on.md).

Bei anderen Entwicklungstools ist ggf. eine Anmeldung über einen Webbrowser erforderlich.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Authentifizieren des Dienstprinzipals in der Entwicklungsumgebung

Wenn Ihre Entwicklungsumgebung kein einmaliges Anmelden bzw. keine Anmeldung über einen Webbrowser unterstützt, können Sie für die Authentifizierung aus der Entwicklungsumgebung einen Dienstprinzipal verwenden.

#### <a name="create-the-service-principal"></a>Erstellen des Dienstprinzipals

Wenn Sie einen Dienstprinzipal über die Azure-Befehlszeilenschnittstelle erstellen und eine Azure-Rolle zuweisen möchten, rufen Sie den Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) auf. Geben Sie eine Azure Storage-Datenzugriffsrolle an, die dem neuen Dienstprinzipal zugewiesen werden soll. Geben Sie außerdem den Bereich für die Rollenzuweisung an. Weitere Informationen zu den für Azure Storage bereitgestellten integrierten Rollen finden Sie unter [In Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md).

Wenn Sie nicht über ausreichende Berechtigungen zum Zuweisen einer Rolle zum Dienstprinzipal verfügen, müssen Sie möglicherweise den Kontobesitzer oder den Administrator bitten, die Rollenzuweisung vorzunehmen.

Im folgenden Beispiel wird ein neuer Dienstprinzipal über die Azure-Befehlszeilenschnittstelle erstellt. Außerdem wird ihm die Rolle **Storage Blob Data Reader** im Kontobereich zugewiesen.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Mit dem Befehl `az ad sp create-for-rbac` wird eine Liste der Dienstprinzipaleigenschaften im JSON-Format zurückgegeben. Kopieren Sie diese Werte, damit Sie sie zum Erstellen der erforderlichen Umgebungsvariablen im nächsten Schritt verwenden können.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Die Azure-Rollenzuweisungen können einige Minuten dauern.

#### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Über die Azure Identity-Clientbibliothek werden zur Laufzeit Werte aus drei Umgebungsvariablen gelesen, um den Dienstprinzipal zu authentifizieren. In der folgenden Tabelle sind die für die einzelnen Umgebungsvariablen festzulegenden Werte beschrieben.

|Umgebungsvariable|value
|-|-
|`AZURE_CLIENT_ID`|Die App-ID für den Dienstprinzipal
|`AZURE_TENANT_ID`|Die Azure AD-Mandanten-ID des Dienstprinzipals
|`AZURE_CLIENT_SECRET`|Das für den Dienstprinzipal generierte Kennwort

> [!IMPORTANT]
> Nachdem Sie die Umgebungsvariablen festgelegt haben, schließen Sie das Konsolenfenster, und öffnen Sie es dann erneut. Wenn Sie Visual Studio oder eine andere Entwicklungsumgebung verwenden, müssen Sie möglicherweise die Entwicklungsumgebung neu starten, damit die neuen Umgebungsvariablen registriert werden.

Weitere Informationen finden Sie unter [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET Codebeispiel: Erstellen eines Blockblobs

Fügen Sie dem Code die folgenden `using`-Anweisungen hinzu, um die Azure Identity- und Azure Storage-Clientbibliotheken zu verwenden.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Erstellen Sie eine Instanz der Klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), um Tokenanmeldeinformationen abzurufen, die in Ihrem Code zum Autorisieren von Anforderungen für Azure Storage verwendet werden können. Das folgende Codebeispiel zeigt, wie Sie die authentifizierten Tokenanmeldeinformationen abrufen, mit ihnen ein Dienstclientobjekt erstellen und anschließend unter Verwendung des Dienstclients ein neues Blob hochladen:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
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
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Zum Autorisieren von Anforderungen für Blob- oder Warteschlangendaten mit Azure AD müssen Sie für die Anforderungen HTTPS verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Rechten für den Zugriff auf Speicherdaten mithilfe der Azure RBAC](./storage-auth-aad-rbac-portal.md)
- [Verwenden von Azure AD mit Speicheranwendungen](storage-auth-aad-app.md)
- [Ausführen von PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Blobdaten](../blobs/authorize-data-operations-powershell.md)
