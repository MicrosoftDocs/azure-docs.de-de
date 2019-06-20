---
title: Festlegen und Abrufen von Objekteigenschaften und Metadaten in Azure Storage | Microsoft-Dokumentation
description: Speichern Sie benutzerdefinierten Metadaten für Objekte in Azure Storage, legen Sie Systemeigenschaften fest, und rufen Sie diese ab.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: e8a85319a12f04a11e3914716d9ff84cdb6de8d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787861"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Festlegen und Abrufen von Eigenschaften und Metadaten

Objekte in Azure Storage unterstützen zusätzlich zu den Daten, die sie enthalten, Systemeigenschaften und benutzerdefinierte Metadaten. In diesem Artikel wird das Verwalten von Systemeigenschaften und benutzerdefinierten Metadaten mithilfe der [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage/client) beschrieben.

* **Systemeigenschaften**:  Systemeigenschaften sind in jeder Speicherressource vorhanden. Einige davon können gelesen oder festgelegt werden, während andere schreibgeschützt sind. Darüber hinaus entsprechen einige Systemeigenschaften bestimmten HTTP-Standardheadern. Diese Eigenschaften werden für Sie von den Azure Storage-Clientbibliotheken verwaltet.

* **Benutzerdefinierte Metadaten**: Benutzerdefinierte Metadaten bestehen aus einem oder mehreren Name-Wert-Paaren, die Sie für eine Azure Storage-Ressource angeben. Sie können Metadaten verwenden, um weitere Werte zusammen mit einer Ressource zu speichern. Metadatenwerte sind nur für Ihre eigenen Zwecke bestimmt und wirken sich nicht auf das Verhalten der Ressource aus.

Das Abrufen von Eigenschafts- und Metadatenwerten einer Speicherressource ist ein zweistufiger Prozess. Bevor Sie diese Werte lesen können, müssen Sie sie explizit durch Aufrufen der **FetchAttributesAsync** oder **FetchAttributesAsync**-Methode abrufen. Eine Ausnahme ist, wenn Sie die **Exists**- oder **ExistsAsync**-Methode für eine Ressource aufrufen. Wenn Sie eine der dieser Methoden aufrufen, ruft Azure Storage im Hintergrund als Teil des Aufrufs der **Exists**-Methode die entsprechende **FetchAttributes**-Methode auf.

> [!IMPORTANT]
> Wenn Sie feststellen, dass Eigenschaften- oder Metadatenwerte für eine Speicherressource nicht ausgefüllt wurden, überprüfen Sie, ob Ihr Code die **FetchAttributes**- oder **FetchAttributesAsync**-Methode aufruft.
>
> Name-Wert-Paare für Metadaten sind gültige HTTP-Header und sollten daher allen Einschränkungen für HTTP-Header entsprechen. Metadatennamen müssen gültige HTTP-Headernamen und gültige C#-Bezeichner sein, dürfen nur ASCII-Zeichen enthalten und sollten als „Keine Beachtung von Groß-/Kleinschreibung“ behandelt werden. Metadatenwerte mit Nicht-ASCII-Zeichen sollten Base64- oder URL-codiert werden.

## <a name="setting-and-retrieving-properties"></a>Festlegen und Abrufen von Eigenschaften
Zum Abrufen von Eigenschaftswerten rufen Sie die **FetchAttributesAsync**-Methode für das Blob oder den Container auf, um die Eigenschaften aufzufüllen, und lesen anschließend die Werte.

Um Eigenschaften für ein Objekt festzulegen, geben Sie den Eigenschaftswert an und rufen dann die **SetProperties** -Methode auf.

Das folgende Codebeispiel erstellt einen Container und schreibt dann einige der zugehörigen Eigenschaftswerte in ein Konsolenfenster.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Festlegen und Abrufen von Metadaten
Sie können Metadaten als ein oder mehrere Name-Wert-Paare für eine Blob- oder Containerressource angeben. Fügen Sie zum Festlegen von Metadaten Name-Wert-Paare zur **Metadaten**-Sammlung der Ressource hinzu, und rufen Sie dann die **SetMetadata** oder **SetMetadataAsync**-Methode auf, um die Werte für den Dienst zu speichern.

> [!NOTE]
> Der Name der Metadaten muss den Benennungskonventionen für C#-Bezeichner entsprechen.
>
>

Das folgende Codebeispiel legt die Metadaten für einen Container fest. Mittels der **Hinzufügen** -Methode der Sammlung wird ein Wert festgelegt. Der andere Wert wird mit der impliziten Schlüssel-Wert-Syntax festgelegt. Beide eignen sich hierzu.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Zum Abrufen von Metadaten rufen Sie die **FetchAttributes**- oder **FetchAttributesAsync**-Methode für das Blob oder den Container auf, um die **Metadaten**-Sammlung zu füllen, und lesen anschließend die Werte wie im unten stehenden Beispiel gezeigt.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
* [Azure Storage-Clientbibliothek für .NET-Referenz](/dotnet/api/?term=Microsoft.Azure.Storage)
* [Azure Blob Storage-Clientbibliothek für .NET-Paket](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [Azure Queue Storage-Clientbibliothek für .NET-Paket](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Azure File Storage-Clientbibliothek für .NET-Paket](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

