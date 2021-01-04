---
title: Azure Cosmos DB-Anlagen
description: Dieser Artikel bietet eine Übersicht über Azure Cosmos DB-Anlagen.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: b6504c0521328edc356dea1c146fe9aeb6bde55f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092737"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB-Anlagen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB-Anlagen sind besondere Elemente, die Verweise auf zugeordnete Metadaten mit einem externen Blob oder einer externen Mediendatei enthalten.

Azure Cosmos DB unterstützt zwei Arten von Anlagen:

* **Nicht verwaltete Anlagen** sind ein Wrapper um einen URI-Verweis auf ein Blob, das in einem externen Dienst (z. B. Azure Storage, OneDrive usw.) gespeichert ist. Dieser Ansatz ähnelt dem Speichern einer URI-Eigenschaft in einem Azure Cosmos DB-Standardelement.
* **Verwaltete Anlagen** sind Blobspeicher, die von Azure Cosmos DB intern verwaltet und gespeichert sowie über einen vom System generierten mediaLink verfügbar gemacht werden.


> [!NOTE]
> „Anlage“ ist ein Legacyfeature. Seine Unterstützung ist auf die Bereitstellung fortgesetzter Funktionalität begrenzt, wenn Sie dieses Feature bereits verwenden.
> 
> Statt der Verwendung von Anlagen empfehlen wir, Azure Blob Storage als speziell zum Speichern von Blobdaten konzipierten Blobspeicherdienst zu verwenden. Sie können Metadaten zu Blobs, zusammen mit Verweis-URI-Links, in Azure Cosmos DB als Elementeigenschaften weiterhin speichern. Durch das Speichern dieser Daten in Azure Cosmos DB wird es möglich, Metadaten und Links zu in Azure Blob Storage gespeicherten Blobs abzufragen.
> 
> Microsoft ist bestrebt, vor der vollständigen Einstellung der Unterstützung von Anlagen mindestens 36 Monate vorher darauf hinzuweisen. Dies wird zu einem späteren Zeitpunkt angekündigt.

## <a name="known-limitations"></a>Bekannte Einschränkungen

Die verwalteten Anlagen von Azure Cosmos DB unterscheiden sich von deren Unterstützung für Standardelemente, für die sie unbegrenzte Skalierbarkeit, globale Verteilung und Integration in andere Azure-Dienste bietet.

- Anlagen werden nicht in allen Versionen der SDKs von Azure Cosmos DB unterstützt.
- Verwaltete Anlagen sind auf 2 GB Speicher pro Datenbankkonto begrenzt.
- Verwaltete Anhänge sind mit der globalen Verteilung von Azure Cosmos DB nicht kompatibel und werden nicht regionsübergreifend repliziert.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Migrieren von Anlagen zu Azure Blob Storage

Wir empfehlen, Azure Cosmos DB-Anlagen zu Azure Blob Storage zu migrieren, indem Sie die folgenden Schritte ausführen:

1. Kopieren Sie Anlagendaten aus Ihrem Azure Cosmos DB-Quellcontainer in Ihren Azure Blob Storage-Zielcontainer.
2. Überprüfen Sie die hochgeladenen Blobdaten im Azure Blob Storage-Zielcontainer.
3. Fügen Sie ggf. URI-Verweise auf die in Azure Blob Storage enthaltenen Blobs als Zeichenfolgeneigenschaften in Ihrem Azure Cosmos DB-Dataset hinzu.
4. Gestalten Sie Ihren Anwendungscode so um, das Blobs aus dem neuen Azure Blob Storage-Container gelesen und in ihn geschrieben werden.

Im folgenden DotNet-Codebeispiel wird gezeigt, wie Sie Anlagen aus Azure Cosmos DB im Rahmen eines Migrationsflusses mithilfe des .NET SDK v2 von Azure Cosmos DB und des Azure Blob Storage .NET SDK v12 in Azure Blob Storage kopieren. Sorgen Sie dafür, dass Sie die `<placeholder values>` für das Azure Cosmos DB-Quellkonto und den Azure Blob Storage-Zielcontainer ersetzen.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte mit [Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- Abrufen von Verweisen auf die Verwendung von Anlagen über das [.NET SDK von Azure Cosmos DB v2](/dotnet/api/microsoft.azure.documents.attachment?preserve-view=true&view=azure-dotnet)
- Abrufen von Verweisen auf die Verwendung von Anlagen über das [Java SDK von Azure Cosmos DB v2](/java/api/com.microsoft.azure.documentdb.attachment?preserve-view=true&view=azure-java-stable)
- Abrufen von Verweisen auf die Verwendung von Anlagen über die [REST-API von Azure Cosmos DB](/rest/api/cosmos-db/attachments)