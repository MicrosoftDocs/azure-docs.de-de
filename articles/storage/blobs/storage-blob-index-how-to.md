---
title: Verwenden von Blobindextags zum Verwalten und Suchen von Daten in Azure Blob Storage
description: Hier finden Sie Beispiele für die Verwendung von Blobindextags zum Kategorisieren, Verwalten und Abfragen von Daten für Blobobjekte.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 411815ca2f947c47b8dfb0d2e5d61f8ea18f3545
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95541248"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Verwenden von Blobindextags (Vorschau) zum Verwalten und Suchen von Daten in Azure Blob Storage

Blobindextags kategorisieren Daten in Ihrem Speicherkonto mithilfe von Schlüssel-Wert-Tagattributen. Diese Tags werden automatisch indiziert und als durchsuchbarer mehrdimensionaler Index verfügbar gemacht, um Daten einfach finden zu können. In diesem Artikel wird erörtert, wie Sie Daten mithilfe von Blobindextags festlegen, abrufen und suchen.

> [!NOTE]
> Der Blobindex befindet sich in der öffentlichen Vorschauphase und ist in den Regionen **Kanada, Mitte**, **Kanada, Osten**, **Frankreich, Mitte** und **Frankreich, Süden** verfügbar. Weitere Informationen zu diesem Feature sowie zu bekannten Problemen und Einschränkungen finden Sie unter [Verwalten und Suchen von Daten in Azure Blob Storage mit Blobindextags (Vorschau)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/azure-portal)

- Ein Azure-Abonnement ist registriert und wurde für den Zugriff auf die Blobindex-Vorschauversion genehmigt.
- Zugriff auf das [Azure-Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

Da sich der Blobindex in der Vorschauphase befindet, wird das .NET-Speicherpaket im NuGet-Vorschaufeed veröffentlicht. Diese Bibliothek kann während der Vorschauphase geändert werden.

1. Richten Sie Ihr Visual Studio-Projekt für die ersten Schritte mit der Azure Blob Storage-Clientbibliothek v12 für .NET ein. Weitere Informationen finden Sie unter [Schnellstart: Azure Blob Storage-Clientbibliothek v12 für .NET](storage-quickstart-blobs-dotnet.md).

2. Suchen Sie im NuGet-Paket-Manager nach dem Paket **Azure.Storage.Blobs**, und installieren Sie Version **12.7.0-preview.1** oder höher in Ihrem Projekt. Sie können auch den PowerShell-Befehl `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1` ausführen.

   Weitere Informationen finden Sie unter [Suchen und Installieren eines Pakets](/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Fügen Sie die folgenden using-Anweisungen am Anfang Ihrer Codedatei ein.

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>Hochladen eines neuen Blobs mit Indextags

Diese Aufgabe kann von einem [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) oder einem Sicherheitsprinzipal ausgeführt werden, dem über eine benutzerdefinierte Azure-Rolle die Berechtigung für den [Azure-Ressourcenanbietervorgang](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` erteilt wurde.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Speicherkonto aus. 

2. Navigieren Sie unter **Blob-Dienst** zur Option **Container**, und wählen Sie Ihren Container aus.

3. Wählen Sie die Schaltfläche **Hochladen** aus, und navigieren Sie in Ihrem lokalen Dateisystem zu einer Datei, um sie als Blockblob hochzuladen.

4. Erweitern Sie das Dropdownmenü **Erweitert**, und wechseln Sie zum Abschnitt **Blobindextags**.

5. Geben Sie die Schlüssel-Wert-Blobindextags ein, die auf Ihre Daten angewendet werden sollen.

6. Wählen Sie die Schaltfläche **Hochladen** aus, um das Blob hochzuladen.

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Screenshot des Azure-Portals beim Hochladen eines Blobs mit Indextags":::

# <a name="net"></a>[.NET](#tab/net)

Im folgenden Beispiel wird das Erstellen eines Anfügeblobs gezeigt, bei dem Tags während der Erstellung festgelegt wurden.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Abrufen, Festlegen und Aktualisieren von Blobindextags

Das Abrufen von Blobindextags kann von einem [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) oder einem Sicherheitsprinzipal durchgeführt werden, dem über eine benutzerdefinierte Azure-Rolle die Berechtigung für den [Azure-Ressourcenanbietervorgang](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` erteilt wurde.

Das Festlegen und Aktualisieren von Blobindextags kann von einem [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) oder einem Sicherheitsprinzipal durchgeführt werden, dem über eine benutzerdefinierte Azure-Rolle die Berechtigung für den [Azure-Ressourcenanbietervorgang](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` erteilt wurde.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Speicherkonto aus. 

2. Navigieren Sie unter **Blob-Dienst** zur Option **Container**, und wählen Sie Ihren Container aus.

3. Wählen Sie das Blob aus der Liste der Blobs innerhalb des ausgewählten Containers aus.

4. Auf der Registerkarte „Übersicht“ für das Blob werden die Eigenschaften Ihres Blobs einschließlich der **Blobindextags** angezeigt.

5. Sie können alle Schlüssel-Wert-Indextags für Ihr Blob abrufen, festlegen, ändern oder löschen.

6. Wählen Sie die Schaltfläche **Speichern** aus, um die an Ihrem Blob vorgenommenen Aktualisierungen zu bestätigen.

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Screenshot des Azure-Portals beim Abrufen, Festlegen, Aktualisieren und Löschen von Indextags für Blobs":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtern und Suchen von Daten mit Blobindextags

Diese Aufgabe kann von einem [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) oder einem Sicherheitsprinzipal ausgeführt werden, dem über eine benutzerdefinierte Azure-Rolle die Berechtigung für den [Azure-Ressourcenanbietervorgang](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` erteilt wurde.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Im Azure-Portal wendet der Filter für Blobindextags den `@container`-Parameter automatisch an, um den Bereich auf Ihren ausgewählten Container festzulegen. Wenn Sie mit Tags versehene Daten in Ihrem gesamten Speicherkonto filtern und suchen möchten, verwenden Sie unsere REST-API, SDKs oder Tools.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Speicherkonto aus. 

2. Navigieren Sie unter **Blob-Dienst** zur Option **Container**, und wählen Sie Ihren Container aus.

3. Wählen Sie die Schaltfläche **Filter für Blobindextags** aus, um innerhalb des ausgewählten Containers zu filtern.

4. Geben Sie einen Tagschlüssel und einen Tagwert für den Blobindex ein.

5. Wählen Sie die Schaltfläche **Filter für Blobindextags** aus, um weitere (bis zu 10) Tagfilter hinzuzufügen.

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Screenshot des Azure-Portals beim Filtern und Suchen von Blobs mit Indextags":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Lebenszyklusverwaltung mit Filtern für Blobindextags

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Speicherkonto aus. 

2. Navigieren Sie unter **Blob-Dienst** zur Option **Lebenszyklusverwaltung**.

3. Wählen Sie *Regel hinzufügen* aus, und füllen Sie dann die Felder des Formulars „Aktionssatz“ aus.

4. Wählen Sie **Filtersatz** aus, um optionale Filter für die Präfixübereinstimmung und Blobindexübereinstimmung hinzuzufügen.

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Screenshot des Azure-Portals beim Hinzufügen von Indextags für die Lebenszyklusverwaltung":::

5. Wählen Sie **Überprüfen + hinzufügen** aus, um die Regeleinstellungen zu überprüfen.

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Screenshot des Azure-Portals mit einer Regel für die Lebenszyklusverwaltung und einem Filterbeispiel mit Blobindextags":::

6. Wählen Sie **Hinzufügen** aus, um die neue Regel auf die Richtlinie für die Lebenszyklusverwaltung anzuwenden.

# <a name="net"></a>[.NET](#tab/net)

Richtlinien für die [Lebenszyklusverwaltung](storage-lifecycle-management-concepts.md) werden für jedes Speicherkonto auf der Steuerungsebene angewendet. Installieren Sie für .NET die [Microsoft Azure Management Storage Library](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) ab Version 16.0.0.

---

## <a name="next-steps"></a>Nächste Schritte

 - Weitere Informationen zu Blobindextags finden Sie unter [Verwalten und Suchen von Daten in Azure Blob Storage mit Blobindextags (Vorschau)](storage-manage-find-blobs.md ).
 - Weitere Informationen zur Lebenszyklusverwaltung finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](storage-lifecycle-management-concepts.md).