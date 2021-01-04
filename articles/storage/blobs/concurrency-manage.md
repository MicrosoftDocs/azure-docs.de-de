---
title: Verwalten von Parallelität in Blob Storage
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie mehrere Writer für ein Blob verwalten, indem Sie optimistische oder pessimistische Parallelität in Ihrer Anwendung implementieren. Bei der optimistischen Parallelität wird der ETag-Wert für ein Blob überprüft und mit dem bereitgestellten ETag verglichen. Bei pessimistischer Parallelität wird das Blob mit einer exklusiven Lease für andere Writer gesperrt.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523302"
---
# <a name="managing-concurrency-in-blob-storage"></a>Verwalten der Parallelität im Blobspeicher

Moderne Anwendungen haben häufig mehrere Benutzer, die Daten gleichzeitig anzeigen und aktualisieren. Daher müssen Anwendungsentwickler sorgfältig überlegen, wie sie ihren Endbenutzern ein vorhersagbares Erlebnis gewährleisten können, insbesondere in Situationen, in denen mehrere Benutzer die gleichen Daten aktualisieren können. Von Entwicklern werden für gewöhnlich die drei folgenden Hauptstrategien für die Datenparallelität in Betracht gezogen:  

- **Optimistische Parallelität:** Eine Anwendung, die eine Aktualisierung ausführt, prüft während dieses Vorgangs, ob die Daten seit dem letzten Lesen geändert wurden. Wenn z. B. zwei Benutzer dieselbe Wiki-Seite aufrufen und aktualisieren, muss die Wiki-Plattform sicherstellen, dass die erste Aktualisierung nicht von der zweiten überschrieben wird. Außerdem muss sichergestellt werden, dass beide Benutzer wissen, ob ihre Aktualisierung erfolgreich war. Diese Strategie wird in Webanwendung sehr häufig verwendet.

- **Pessimistische Parallelität:** Eine Anwendung, die eine Aktualisierung ausführt, sperrt ein Objekt, sodass andere Benutzer die Daten erst aktualisieren können, wenn die Sperre aufgehoben wird. Bei einer Datenreplikation zwischen Primär- und Sekundärreplikat, bei der nur das Primärreplikat Aktualisierungen vornimmt, richtet dieses beispielsweise in der Regel für einen bestimmten Zeitraum eine Exklusivsperre der Daten ein, damit sie von keinem anderen Benutzer aktualisiert werden können.

- **Letzter Schreiber gewinnt:** Bei diesem Ansatz dürfen Aktualisierungsvorgänge fortgesetzt werden, ohne dass zunächst festgestellt wird, ob eine andere Anwendung die Daten seit dem Lesen aktualisiert hat. Diese Vorgehensweise wird normalerweise verwendet, wenn die Daten so partitioniert sind, dass nicht mehrere Benutzer gleichzeitig auf dieselben Daten zugreifen. Diese Strategie kann auch bei der Verarbeitung kurzlebiger Datenströme sinnvoll sein.

Azure Storage unterstützt alle drei Strategien, obwohl der Dienst besonders mit seiner Fähigkeit heraussticht, vollständige Unterstützung für optimistische und pessimistische Parallelität bereitzustellen. Azure Storage wurde anhand eines Modells mit starker Konsistenz entwickelt, mit dem sichergestellt wird, dass nach dem Ausführen eines Einfüge- oder Aktualisierungsvorgangs durch den Dienst nachfolgende Lesevorgänge die aktuellsten Daten zurückgeben.

Entwickler müssen nicht nur die entsprechende Parallelitätsstrategie auswählen, sondern auch wissen, wie eine Speicherplattform Änderungen isoliert, insbesondere Änderungen, die in mehreren Transaktionen an demselben Objekt vorgenommen wurden. Azure Storage verwendet die Momentaufnahmeisolation, damit Lesevorgänge innerhalb einer einzelnen Partition gleichzeitig mit Schreibvorgängen ausgeführt werden können. Die Momentaufnahmeisolation gewährleistet, dass alle Lesevorgänge eine konsistente Momentaufnahme der Daten zurückgeben, auch während Aktualisierungen durchgeführt werden.

Für die Verwaltung des Zugriffs auf Blobs und Container können Sie entweder das optimistische oder das pessimistische Parallelitätsmodell verwenden. Wenn Sie keine Strategie explizit festlegen, wird standardmäßig die Strategie „Letzter Schreiber gewinnt“ angewandt.  

## <a name="optimistic-concurrency"></a>Optimistische Parallelität

Azure Storage weist jedem gespeicherten Objekt einen Bezeichner zu. Dieser Bezeichner wird jedes Mal aktualisiert, wenn für ein Objekt ein Schreibvorgang ausgeführt wird. Der Bezeichner wird als Teil einer HTTP GET-Antwort an den Client zurückgegeben. Dabei wird der ETag-Header verwendet, der im HTTP-Protokoll definiert ist.

Ein Client, der eine Aktualisierung ausführt, kann das ursprüngliche ETag zusammen mit einem bedingten Header senden, um sicherzustellen, dass die Aktualisierung nur dann erfolgt, wenn eine bestimmte Bedingung erfüllt ist. Wenn beispielsweise der **If-Match**-Header angegeben ist, überprüft Azure Storage, ob der Wert des in der Aktualisierungsanforderung angegebenen ETags mit dem ETag des zu aktualisierenden Objekts übereinstimmt. Weitere Informationen zu bedingten Headern finden Sie unter [Angeben von bedingten Headern für Vorgänge des Blob-Diensts](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

Dieser Prozess ist folgendermaßen gegliedert:  

1. Es wird ein Blob aus Azure Storage abgerufen. Die Antwort enthält einen HTTP-ETag-Headerwert, der die aktuelle Version des Objekts bezeichnet.
1. Wenn Sie das Blob aktualisieren, fügen Sie den in Schritt 1 erhaltenen ETag-Wert in den bedingten **If-Match**-Header der Schreibanforderung ein. Azure Storage vergleicht den ETag-Wert in der Anforderung mit dem aktuellen ETag-Wert des Blobs.
1. Wenn sich der aktuelle ETag-Wert des Blobs von dem im bedingten **If-Match**-Header der Anforderung unterscheidet, gibt Azure Storage den HTTP-Statuscode 412 (Vorbedingung nicht erfüllt) zurück. Dieser Fehler teilt dem Client mit, dass das Blob von einem anderen Prozess geändert wurde, nachdem es vom Client das erste Mal abgerufen wurde.
1. Wenn der aktuelle ETag-Wert des Blobs dieselbe Version wie das ETag im bedingten **If-Match**-Header der Anforderung aufweist, führt Azure Storage den angeforderten Vorgang aus und aktualisiert den aktuellen ETag-Wert des Blobs.  

In den folgenden Codebeispielen wird gezeigt, wie Sie eine **If-Match**-Bedingung für die Schreibanforderung erstellen, die den ETag-Wert für ein Blob überprüft. Azure Storage überprüft, ob das aktuelle ETag des Blobs mit dem ETag übereinstimmt, das in der Anforderung bereitgestellt wurde, und führt den Schreibvorgang nur aus, wenn die beiden ETag-Werte übereinstimmen. Falls das Blob in der Zwischenzeit von einem anderen Prozess aktualisiert wurde, gibt Azure Storage die Statusmeldung HTTP 412 (Vorbedingung nicht erfüllt) zurück.  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure Storage unterstützt auch andere bedingte Header, einschließlich **If-Modified-Since**, **If-Unmodified-Since** und **If-None-Match**. Weitere Informationen finden Sie unter [Specifying Conditional Headers for Blob Service Operations](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations) (Angeben von bedingten Headern für Vorgänge des Blob-Diensts).  

## <a name="pessimistic-concurrency-for-blobs"></a>Pessimistische Nebenläufigkeit für Blobs

Um ein Blob für die exklusive Verwendung zu sperren, können Sie dafür eine Lease abrufen. Wenn Sie die Lease abrufen, geben Sie die Dauer der Lease an. Eine zeitlich begrenzte Lease kann zwischen 15 und 60 Sekunden gültig sein. Eine Lease kann auch unendlich gültig sein und wird dann zu einer Exklusivsperre. Sie können eine begrenzte Lease erneuern, um sie zu verlängern, und Sie können eine Lease freigeben, wenn sie nicht mehr benötigt wird. Azure Storage gibt begrenzte Leases automatisch frei, sobald sie ablaufen.  

Leases ermöglichen die Unterstützung verschiedener Synchronisierungsstrategien, einschließlich exklusiver Schreib-/gemeinsamer Lesevorgänge, exklusiver Schreib-/exklusiver Lesevorgänge und gemeinsamer Schreib-/exklusiver Lesevorgänge. Wenn eine Lease vorhanden ist, erzwingt Azure Storage den exklusiven Zugriff auf Schreibvorgänge für den Leaseinhaber. Um die Exklusivität für Lesevorgänge zu gewährleisten, muss der Entwickler jedoch sicherstellen, dass alle Clientanwendungen eine Lease-ID verwenden und dass jederzeit nur ein Client über eine gültige Lease-ID verfügt. Lesevorgänge, die keine Lease-ID enthalten, führen zu gemeinsamen Lesevorgängen.  

In den folgenden Codebeispielen wird veranschaulicht, wie Sie eine exklusive Lease für ein Blob abrufen, den Inhalt des Blobs aktualisieren, indem Sie die Lease-ID angeben, und dann die Lease freigeben. Wenn die Lease aktiv ist und die Lease-ID für eine Schreibanforderung nicht angegeben ist, führt der Schreibvorgang zum Fehlercode 412 (Vorbedingung nicht erfüllt).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Pessimistische Nebenläufigkeit für Container

Leases für Container ermöglichen dieselben Synchronisierungsstrategien wie für Blobs, einschließlich exklusiver Schreib-/gemeinsamer Lesevorgänge, exklusiver Schreib-/exklusiver Lesevorgänge und gemeinsamer Schreib-/exklusiver Lesevorgänge. Bei Containern wird die exklusive Sperre jedoch nur bei Löschvorgängen erzwungen. Um einen Container mit einer aktiven Lease zu löschen, muss ein Client die ID der aktiven Lease bei der Löschanforderung angeben. Alle anderen Containervorgänge werden für einen geleasten Container auch ohne Angabe der Lease-ID erfolgreich ausgeführt.  

## <a name="next-steps"></a>Nächste Schritte

* [Angeben von bedingten Headern für Vorgänge des Blob-Diensts](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Lease Container](/rest/api/storageservices/lease-container)
* [Lease Blob](/rest/api/storageservices/lease-blob)
