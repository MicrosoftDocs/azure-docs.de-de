---
title: Verwenden von .NET zum Erstellen einer SAS für die Benutzerdelegierung für einen Container, ein Verzeichnis oder ein Blob
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie mithilfe der .NET-Clientbibliothek für Azure Storage eine SAS für die Benutzerdelegierung mit Azure Active Directory-Anmeldeinformationen erstellen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 13491735f73cb1696f3c36f3434cc781a1e2b739
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526804"
---
# <a name="create-a-user-delegation-sas-for-a-container-directory-or-blob-with-net"></a>Erstellen einer SAS für die Benutzerdelegierung für einen Container, ein Verzeichnis oder ein Blob mit .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In diesem Artikel erfahren Sie, wie Sie unter Verwendung von Azure AD-Anmeldeinformationen (Azure Active Directory) eine SAS für die Benutzerdelegierung für einen Container, ein Verzeichnis oder ein Blob mit der Azure Storage-Clientbibliothek für .NET Version 12 erstellen.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Zuweisen von Azure-Rollen für den Datenzugriff

Wenn ein Azure AD-Sicherheitsprinzipal versucht, auf Blobdaten zuzugreifen, muss dieser Sicherheitsprinzipal über Berechtigungen für die Ressource verfügen. Dem Sicherheitsprinzipal muss eine Azure-Rolle zugewiesen werden, die den Zugriff auf Blobdaten in Azure Storage ermöglicht. Dabei spielt es keine Rolle, ob es sich bei dem Sicherheitsprinzipal um eine verwaltete Identität in Azure oder um ein Azure AD-Benutzerkonto handelt, mit dem Code in der Entwicklungsumgebung ausgeführt wird. Informationen zur Zuweisung von Berechtigungen per Azure RBAC finden Sie unter **Autorisieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory** im Abschnitt [Zuweisen von Azure-Rollen für Zugriffsrechte](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Weitere Informationen zum Authentifizieren mit der Azure Identity-Clientbibliothek über Azure Storage finden Sie im Abschnitt **Authentifizieren mit der Azure Identity-Bibliothek** unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory und verwalteten Identitäten für Azure-Ressourcen](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="get-an-authenticated-token-credential"></a>Abrufen authentifizierter Tokenanmeldeinformationen

Erstellen Sie eine Instanz der Klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), um Tokenanmeldeinformationen abzurufen, die in Ihrem Code zum Autorisieren von Anforderungen für Azure Storage verwendet werden können.

Im folgenden Codeausschnitt sehen Sie, wie Sie die authentifizierten Tokenanmeldeinformationen abrufen und damit einen Dienstclient für Blob Storage erstellen:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Abrufen des Benutzerdelegierungsschlüssels

Jede SAS wird mit einem Schlüssel signiert. Zum Erstellen einer SAS für die Benutzerdelegierung müssen Sie zuerst einen Benutzerdelegierungsschlüssel anfordern, der dann zum Signieren der SAS verwendet wird. Der Benutzerdelegierungsschlüssel ist analog zum Kontoschlüssel, mit dem eine Dienst-SAS oder eine Konto-SAS signiert wird, mit der Ausnahme, dass er auf Ihren Azure AD-Anmeldeinformationen basiert. Wenn ein Client einen Benutzerdelegierungsschlüssel mithilfe eines OAuth 2.0-Tokens anfordert, gibt Azure Storage den Benutzerdelegierungsschlüssel im Namen des Benutzers zurück.

Nachdem Sie den Benutzerdelegierungsschlüssel abgerufen haben, können Sie mit diesem Schlüssel während seiner Lebensdauer eine beliebige Anzahl von Shared Access Signatures (SAS) für die Benutzerdelegierung erstellen. Der Benutzerdelegierungsschlüssel ist unabhängig vom OAuth 2.0-Token, das zum Abrufen des Schlüssels verwendet wird. Daher muss das Token nicht erneuert werden, solange der Schlüssel gültig ist. Sie können angeben, dass der Schlüssel für einen Zeitraum von bis zu 7 Tagen gültig ist.

Fordern Sie den Benutzerdelegierungsschlüssel mit einer der folgenden Methoden an:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Im folgenden Codeausschnitt werden der Benutzerdelegierungsschlüssel abgerufen und die zugehörigen Eigenschaften geschrieben:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Abrufen einer SAS für die Benutzerdelegierung für ein Blob

Im folgenden Codebeispiel ist der gesamte Code für die Authentifizierung des Dienstprinzipals und die Erstellung der SAS für die Benutzerdelegierung für ein Blob zu sehen:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

Im folgenden Beispiel wird die im vorherigen Beispiel erstellte SAS für die Benutzerdelegierung in einer simulierten Clientanwendung getestet. Wenn die SAS gültig ist, können die Inhalte des Blobs in der Clientanwendung gelesen werden. Wenn die SAS ungültig ist, z. B. weil sie abgelaufen ist, wird in Azure Storage der Fehlercode 403 (Verboten) zurückgegeben.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Abrufen einer SAS für die Benutzerdelegierung für einen Container

Im folgenden Codebeispiel wird gezeigt, wie Sie eine SAS für die Benutzerdelegierung für einen Container generieren:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

Im folgenden Beispiel wird die im vorherigen Beispiel erstellte SAS für die Benutzerdelegierung in einer simulierten Clientanwendung getestet. Wenn die SAS gültig ist, können die Inhalte des Blobs in der Clientanwendung gelesen werden. Wenn die SAS ungültig ist, z. B. weil sie abgelaufen ist, wird in Azure Storage der Fehlercode 403 (Verboten) zurückgegeben.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>Abrufen einer SAS für die Benutzerdelegierung für ein Verzeichnis

Im folgenden Codebeispiel wird gezeigt, wie Sie eine SAS für die Benutzerdelegierung für ein Verzeichnis generieren, wenn ein hierarchischer Namespace für das Speicherkonto aktiviert ist:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

Im folgenden Beispiel wird die im vorherigen Beispiel erstellte SAS für die Benutzerdelegierung in einer simulierten Clientanwendung getestet. Wenn die SAS gültig ist, kann die Clientanwendung Dateipfade in diesem Verzeichnis auflisten. Wenn die SAS ungültig ist, z. B. weil sie abgelaufen ist, wird in Azure Storage der Fehlercode 403 (Verboten) zurückgegeben.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Weitere Informationen

- [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../common/storage-sas-overview.md)
- [Vorgang zum Abrufen eines Benutzerdelegierungsschlüssels](/rest/api/storageservices/get-user-delegation-key)
- [Erstellen einer SAS für die Benutzerdelegierung (REST-API)](/rest/api/storageservices/create-user-delegation-sas)
