---
title: Aktivieren von öffentlichem Lesezugriff auf Container und Blobs in Azure Blob Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Container und Blobs für den anonymen Zugriff verfügbar machen und darauf programmgesteuert zugreifen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148348"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Verwalten des anonymen Lesezugriffs auf Container und Blobs

Sie können anonymen öffentlichen Lesezugriff auf einen Container und dessen Blobs in Azure Blob Storage aktivieren. Auf diese Weise können Sie schreibgeschützten Zugriff auf diese Ressourcen gewähren, ohne Ihren Kontoschlüssel freizugeben und eine Shared Access Signature (SAS) zu erfordern.

Öffentlicher Lesezugriff ist am besten für Szenarien, in denen bestimmte Blobs stets für anonymen Lesezugriff zur Verfügung stehen sollen. Für eine feiner abgestufte Zugriffssteuerung können Sie eine Shared Access Signature erstellen. Shared Access Signatures ermöglichen es Ihnen, eingeschränkten Zugriff mit unterschiedlichen Berechtigungen für einen bestimmten Zeitraum zu gewähren. Weitere Informationen zum Erstellen von Shared Access Signatures finden Sie unter [Verwenden von Shared Access Signatures (SAS) in Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Erteilen von anonymen Benutzerberechtigungen für Container und Blobs

In der Standardeinstellung kann nur ein Benutzer mit den entsprechenden Berechtigungen auf einen Container und auf darin enthaltene Blobs zugreifen. Wenn anonyme Benutzer Lesezugriff auf den Container und auf die zugehörigen Blobs erhalten sollen, können Sie für den Container die öffentliche Zugriffsebene festlegen. Diese Benutzer müssen die Anforderung dann nicht autorisieren.

Sie können einen Container mit den folgenden Berechtigungen konfigurieren:

* **No public read access** (Kein öffentlicher Lesezugriff): Nur der Speicherkontobesitzer kann auf den Container und dessen Blobs zugreifen. Dies ist die Standardeinstellung für alle neuen Container.
* **Public read access for blobs only** (Öffentlicher Lesezugriff nur für Blobs): Blobs innerhalb des Containers können über anonyme Anforderungen gelesen werden, Containerdaten sind aber nicht verfügbar. Anonyme Clients können die Blobs im Container nicht aufzählen.
* **Public read access for container and its blobs** (Öffentlicher Lesezugriff für Container und zugehörige Blobs): Alle Container- und Blobdaten können über anonyme Anforderungen gelesen werden. Clients können Blobs im Container über anonyme Anforderungen aufzählen, können aber keine Container im Speicherkonto aufzählen.

Sie können Folgendes verwenden, um Containerberechtigungen festzulegen:

* [Azure-Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure-Befehlszeilenschnittstelle](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programmgesteuert, indem Sie eine der Speicherclientbibliotheken oder die REST-API verwenden

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Festlegen der öffentlichen Zugriffsebene für einen Container im Azure-Portal

Im [Azure-Portal](https://portal.azure.com) können Sie die öffentliche Zugriffsebene für einen oder mehrere Container ändern:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie auf dem Menüblatt unter **Blob-Dienst** die Option **Blobs** aus.
1. Wählen Sie die Container aus, für die Sie die öffentliche Zugriffsebene festlegen möchten.
1. Klicken Sie auf **Zugriffsebene ändern**, um die Einstellungen für den öffentlichen Zugriff aufzurufen.
1. Wählen Sie aus der Dropdownliste **Öffentliche Zugriffsebene** den gewünschten Eintrag aus, und klicken Sie auf „OK“, damit die Änderungen auf die ausgewählten Container angewendet werden.

Auf dem folgenden Screenshot wird gezeigt, wie Sie die öffentliche Zugriffsebene für die ausgewählten Container ändern.

![Screenshot: Festlegen der öffentlichen Zugriffsebene im Azure-Portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Sie können die öffentliche Zugriffsebene nicht für einzelne Blobs ändern, da diese ausschließlich auf Containerebene festgelegt wird.

### <a name="set-container-public-access-level-with-net"></a>Festlegen der öffentlichen Zugriffsebene für den Container mit .NET

Um Berechtigungen für einen Container mit C# und der Speicherclientbibliothek für .NET festzulegen, rufen Sie zunächst die vorhandenen Berechtigungen des Containers ab, indem Sie die **GetPermissions**-Methode aufrufen. Legen Sie dann die **PublicAccess**-Eigenschaft für das **BlobContainerPermissions**-Objekt fest, das von der **GetPermissions**-Methode zurückgegeben wird. Rufen Sie abschließend die **SetPermissions** -Methode mit den aktualisierten Berechtigungen auf.

Im folgenden Beispiel werden die Berechtigungen des Containers auf vollständigen öffentlichen Lesezugriff festgelegt. Legen Sie zum Festlegen von Berechtigungen auf den öffentlichen Lesezugriff nur für Blobs die **PublicAccess**-Eigenschaft auf **BlobContainerPublicAccessType.Blob** fest. Um alle Berechtigungen für anonyme Benutzer zu entfernen, legen Sie die Eigenschaft auf **BlobContainerPublicAccessType.Off**fest.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Anonymer Zugriff auf Container und Blobs

Ein Client, der auf Container und Blobs anonym zugreift, kann Konstruktoren verwenden, für die keine Anmeldeinformationen erforderlich sind. In den folgenden Beispielen werden verschiedene Möglichkeiten zum anonymen Verweisen auf Container und Blobs gezeigt.

### <a name="create-an-anonymous-client-object"></a>Erstellen eines anonymen Clientobjekts

Sie können ein neues Dienstclientobjekt für den anonymen Zugriff durch Bereitstellen des Blob Storage-Endpunkts für das Konto erstellen. Allerdings müssen Sie auch den Namen eines Containers in diesem Konto kennen, der für den anonymen Zugriff verfügbar ist.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Anonymes Verweisen auf einen Container

Wenn Sie über die URL zu einem Container verfügen, der anonym verfügbar ist, können Sie damit direkt auf den Container verweisen.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Anonymes Verweisen auf ein Blob

Wenn Sie über die URL zu einem Blob verfügen, das für den anonymen Zugriff verfügbar ist, können Sie damit über diese URL direkt auf das Blob verweisen:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Für anonyme Benutzer verfügbare Features

In der folgenden Tabelle wird dargestellt, welche Vorgänge anonym aufgerufen werden können, wenn der Container für den öffentlichen Zugriff konfiguriert ist.

| REST-Vorgang | Öffentlicher Lesezugriff auf Container | Nur öffentlicher Lesezugriff auf Blobs |
| --- | --- | --- |
| List Containers | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Create Container | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Get Container Properties | Anonyme Anforderungen zulässig | Nur autorisierte Anforderungen |
| Get Container Metadata | Anonyme Anforderungen zulässig | Nur autorisierte Anforderungen |
| Set Container Metadata | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Get Container ACL | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Set Container ACL | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Delete Container | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| List Blobs | Anonyme Anforderungen zulässig | Nur autorisierte Anforderungen |
| Put Blob | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Get Blob | Anonyme Anforderungen zulässig | Anonyme Anforderungen zulässig |
| Get Blob Properties | Anonyme Anforderungen zulässig | Anonyme Anforderungen zulässig |
| Set Blob Properties | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Get Blob Metadata | Anonyme Anforderungen zulässig | Anonyme Anforderungen zulässig |
| Set Blob Metadata | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Put Block | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Get Block List (nur Blöcke mit ausgeführtem Commit) | Anonyme Anforderungen zulässig | Anonyme Anforderungen zulässig |
| Get Block List (nur Blöcke ohne ausgeführten Commit oder alle Blöcke) | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Put Block List | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Delete Blob | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Kopieren von Blobs | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Snapshot Blob | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Lease Blob | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Put Page | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |
| Get Page Ranges | Anonyme Anforderungen zulässig | Anonyme Anforderungen zulässig |
| Blob anfügen | Nur autorisierte Anforderungen | Nur autorisierte Anforderungen |

## <a name="next-steps"></a>Nächste Schritte

* [Authorization for the Azure Storage Services (Autorisierung für Azure Storage-Dienste)](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Verwenden von Shared Access Signatures (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)