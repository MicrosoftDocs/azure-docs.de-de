---
title: Erstellen einer schreibgeschützten Momentaufnahme eines Blobs in Azure Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Momentaufnahme eines Blobs erstellen, um Blobdaten zu einem angegebenen Zeitpunkt zu sichern. Sie erhalten grundlegende Informationen dazu, wie Momentaufnahmen in Rechnung gestellt und wie Sie mithilfe von Momentaufnahmen Ihre Kapazitätskosten minimieren können.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9c24f613de8bf26331f6fe328358aaf8a320d522
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794234"
---
# <a name="create-a-blob-snapshot"></a>Erstellen einer Momentaufnahme eines Blobs

Eine Momentaufnahme ist eine schreibgeschützte Version eines Blobs, die zu einem bestimmten Zeitpunkt erstellt wird. Momentaufnahmen sind nützlich, um Blobs zu sichern. Nach dem Erstellen einer Momentaufnahme kann sie gelesen, kopiert oder gelöscht, aber nicht mehr geändert werden.

Eine Momentaufnahme eines Blobs ist mit dem dazugehörigen Basisblob bis auf die Ausnahme identisch, dass an den Blob-URI ein **DateTime**-Wert angefügt ist. Hiermit wird der Zeitpunkt angegeben, zu dem die Momentaufnahme erstellt wurde. Wenn der Seitenblob-URI `http://storagesample.core.blob.windows.net/mydrives/myvhd` ist, lautet der Momentaufnahmen-URI z.B. in etwa `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Für alle Momentaufnahmen wird der URI des Basisblobs verwendet. Der einzige Unterschied zwischen dem Basisblob und der Momentaufnahme ist der angefügte **DateTime** -Wert.
>

Für ein Blob kann eine beliebige Anzahl von Momentaufnahmen vorhanden sein. Momentaufnahmen bleiben bestehen, bis sie explizit gelöscht werden, Eine Momentaufnahme kann das dazugehörige Basisblob nicht überleben. Sie können alle einem Basisblob zugeordneten Momentaufnahmen auflisten, um die aktuell vorhandenen Momentaufnahmen nachzuverfolgen.

Wenn Sie eine Momentaufnahme eines Blobs erstellen, werden seine Systemeigenschaften mit denselben Werten in die Momentaufnahme kopiert. Die Metadaten des Basisblobs werden auch in die Momentaufnahme kopiert, sofern Sie beim Erstellen keine separaten Metadaten für die Momentaufnahme angeben.

Dem Basis-Blob zugeordnete Leases wirken sich nicht auf die Momentaufnahme aus. Sie können für eine Momentaufnahme keine Lease abrufen.

Für das Speichern der aktuellen Informationen und des Status eines VM-Datenträgers wird eine VHD-Datei verwendet. Sie können einen Datenträger vom virtuellen Computer trennen oder die VM herunterfahren und dann eine Momentaufnahme der VHD-Datei erstellen. Sie können diese Momentaufnahmedatei später verwenden, um die VHD-Datei zu diesem Zeitpunkt abzurufen und den virtuellen Computer neu zu erstellen.

## <a name="create-a-snapshot"></a>Erstellen einer Momentaufnahme
Im folgenden Codebeispiel wird veranschaulicht, wie Sie eine Momentaufnahme mithilfe der [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage/client) erstellen. In diesem Beispiel werden zusätzliche Metadaten für die Momentaufnahme angegeben, wenn sie erstellt wird.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="copy-snapshots"></a>Kopieren von Momentaufnahmen
Für Kopiervorgänge, die Blobs und Momentaufnahmen betreffen, gelten folgende Regeln:

* Sie können eine Momentaufnahme über das zugehörige Basis-Blob kopieren. Indem Sie eine Momentaufnahme zu einem Basis-Blob heraufstufen, können Sie eine frühere Version eines Blobs wiederherstellen. Die Momentaufnahme bleibt erhalten, ihre Quelle wird jedoch durch eine Kopie überschrieben, die gelesen und geschrieben werden kann.
* Sie können eine Momentaufnahme in ein Ziel-Blob mit einem anderen Namen kopieren. Das resultierende Ziel-Blob ist ein beschreibbares Blob und keine Momentaufnahme.
* Wenn ein Quell-Blob kopiert wird, werden ggf. vorhandene Momentaufnahmen des Quell-Blobs nicht mit in das Ziel kopiert. Wenn ein Zielblob durch eine Kopie überschrieben wird, bleiben alle dem ursprünglichen Zielblob zugeordneten Momentaufnahmen erhalten.
* Wenn Sie eine Momentaufnahme eines Block-Blobs erstellen, wird die Liste der Blöcke mit ausgeführtem Commit für das Blob ebenfalls in die Momentaufnahme kopiert. Blöcke ohne ausgeführten Commit werden nicht kopiert.

## <a name="specify-an-access-condition"></a>Angeben einer Zugriffsbedingung
Beim Aufrufen von [CreateSnapshotAsync][dotnet_CreateSnapshotAsync] können Sie eine Zugriffsbedingung angeben, sodass die Momentaufnahme nur erstellt wird, wenn eine bestimmte Bedingung erfüllt ist. Zum Angeben einer Zugriffsbedingung verwenden Sie den [AccessCondition][dotnet_AccessCondition]-Parameter. Wenn die angegebene Bedingung nicht erfüllt ist, wird die Momentaufnahme nicht erstellt, und der Blob-Dienst gibt den Statuscode „[HTTPStatusCode][dotnet_HTTPStatusCode].PreconditionFailed“ zurück.

## <a name="delete-snapshots"></a>Löschen von Momentaufnahmen
Das Löschen eines Blobs mit Momentaufnahmen ist nur möglich, wenn auch die Momentaufnahmen gelöscht werden. Sie können eine Momentaufnahme einzeln löschen oder angeben, dass alle Momentaufnahmen gelöscht werden sollen, wenn das Quellblob gelöscht wird. Wenn Sie versuchen, ein Blob zu löschen, für das noch Momentaufnahmen vorhanden sind, tritt ein Fehler auf.

Im folgenden Codebeispiel wird veranschaulicht, wie Sie ein Blob und die dazugehörigen Momentaufnahmen in .NET löschen, wenn `blockBlob` ein Objekt vom Typ [CloudBlockBlob][dotnet_CloudBlockBlob] ist:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Momentaufnahmen mit Azure Premium-Speicher
Beim Verwenden von Momentaufnahmen mit Storage Premium gelten die folgenden Regeln:

* Die maximale Anzahl von Momentaufnahmen pro Seitenblob in einem Premium-Speicherkonto beträgt 100. Wenn diese Grenze überschritten wird, gibt der Momentaufnahmen-Blobvorgang Fehlercode 409 (`SnapshotCountExceeded`) zurück.
* Sie können unter einem Premium-Speicherkonto ca. alle zehn Minuten eine Momentaufnahme eines Seitenblobs erstellen. Wenn diese Rate überschritten wird, gibt der Momentaufnahmen-Blobvorgang Fehlercode 409 (`SnapshotOperationRateExceeded`) zurück.
* Zum Lesen einer Momentaufnahme können Sie eine Momentaufnahme mithilfe des Copy Blob-Vorgangs in ein anderes Seitenblob im Konto kopieren. Das Ziel-Blob für den Kopiervorgang darf keine vorhandenen Momentaufnahmen besitzen. Wenn das Zielblob Momentaufnahmen aufweist, gibt der Copy Blob-Vorgang den Fehlercode 409 (`SnapshotsPresent`) zurück.

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Zurückgeben des absoluten URI zu einer Momentaufnahme
Dieses C#-Codebeispiel erstellt eine Momentaufnahme und schreibt den absoluten URI für den primären Standort.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Grundlegendes zur Ermittlung der Gebühren für Momentaufnahmen
Durch das Erstellen einer Momentaufnahme, die eine schreibgeschützte Kopie eines BLOBs darstellt, können auf Ihrem Konto zusätzliche Gebühren für die Datenspeicherung anfallen. Wenn Sie beim Entwurf Ihrer Anwendung berücksichtigen, wie diese Gebühren auflaufen, können Sie die Kosten minimieren.

### <a name="important-billing-considerations"></a>Wichtige Überlegungen zur Abrechnung
Die folgende Liste enthält wichtige Punkte, die beim Erstellen einer Momentaufnahme zu berücksichtigen sind.

* Für Ihr Speicherkonto fallen Gebühren für eindeutige Blöcke oder Seiten an, die im Blob oder in der Momentaufnahme enthalten sind. Ihrem Konto werden erst dann zusätzliche Gebühren für Momentaufnahmen angerechnet, die einem BLOB zugeordnet sind, wenn Sie das BLOB aktualisieren, auf dem sie basieren. Sobald Sie das Basisblob aktualisieren, entstehen Abweichungen von den zugeordneten Momentaufnahmen. In diesem Fall werden für alle eindeutigen Blöcke oder Seiten in jedem Blob bzw. einer Momentaufnahme Gebühren berechnet.
* Wenn Sie einen Block innerhalb eines Block-BLOBs ersetzen, wird dieser Block anschließend als eindeutiger Block berechnet. Dies gilt auch, wenn der Block dieselbe Block-ID und dieselben Daten enthält wie in der Momentaufnahme. Nachdem ein erneuter Commit für den Block ausgeführt wurde, weicht er von seinem Pendant in den Momentaufnahmen ab, und Ihnen werden die Daten des Blocks berechnet. Gleiches gilt für eine Seite in einem Seitenblob, die mit identischen Daten aktualisiert wird.
* Wenn Sie ein Blockblob durch einen Aufruf der Methode [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] oder [UploadFromByteArray][dotnet_UploadFromByteArray] ersetzen, werden alle Blöcke im Blob ersetzt. Wenn dem Blob eine Momentaufnahme zugeordnet ist, weisen anschließend alle Blöcke im Basisblob und in der Momentaufnahme Abweichungen auf, und Ihnen werden Gebühren für alle Blöcke in beiden Blobs berechnet. Dies gilt auch, wenn die Daten im Basis-BLOB und in der Momentaufnahme identisch sind.
* Der Azure-Blob-Dienst kann nicht feststellen, ob zwei Blöcke identische Daten enthalten. Jeder hochgeladene Block, für den ein Commit ausgeführt wird, wird als eindeutig behandelt, selbst wenn die enthaltenen Daten und die Block-ID identisch sind. Da Gebühren jeweils für eindeutige Blöcke berechnet werden, ist zu berücksichtigen, dass beim Aktualisieren eines Blobs mit einer zugeordneten Momentaufnahme zusätzliche eindeutige Blöcke generiert werden, für die zusätzliche Gebühren entstehen.

### <a name="minimize-cost-with-snapshot-management"></a>Minimieren der Kosten durch Momentaufnahmenverwaltung

Es empfiehlt sich, Ihre Momentaufnahmen sorgfältig zu verwalten, um zusätzlich anfallende Gebühren zu vermeiden. Sie können die folgenden bewährten Methoden befolgen, um die beim Speichern von Momentaufnahmen anfallenden Kosten zu minimieren:

* Löschen und erstellen Sie zugehörige Momentaufnahmen für ein BLOB neu, wenn Sie das BLOB aktualisieren, selbst wenn Sie mit identischen Daten aktualisieren, es sei denn, der Anwendungsentwurf erfordert, dass die Momentaufnahmen beibehalten werden. Durch Löschen und Neuerstellen der Momentaufnahmen für ein Blob können Sie sicherstellen, dass das Blob und die Momentaufnahmen nicht voneinander abweichen.
* Wenn Sie Momentaufnahmen für ein Blob beibehalten, sollten Sie Aufrufe von [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] und [UploadFromByteArray][dotnet_UploadFromByteArray] zum Aktualisieren des Blobs vermeiden. Bei diesen Methoden werden alle Blöcke im Blob ersetzt, sodass Ihr Basisblob und seine Momentaufnahmen erheblich voneinander abweichen. Aktualisieren Sie stattdessen so wenig Blöcke wie möglich, indem Sie die [PutBlock][dotnet_PutBlock]-Methode und die [PutBlockList][dotnet_PutBlockList]-Methode aufrufen.

### <a name="snapshot-billing-scenarios"></a>Abrechnungsszenarien für Momentaufnahmen
Die folgenden Szenarien veranschaulichen, wie Gebühren für ein Block-BLOB und zugehörige Momentaufnahmen berechnet werden.

**Szenario 1**

In Szenario 1 wurde das Basis-Blob nicht aktualisiert, nachdem die Momentaufnahme erstellt wurde, sodass Gebühren nur für die eindeutigen Blöcke 1, 2 und 3 berechnet werden:

![Azure Storage-Ressourcen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Szenario 2**

In Szenario 2 wurde das Basisblob aktualisiert, die Momentaufnahme jedoch nicht. Block 3 wurde aktualisiert. Obwohl er die gleichen Daten und dieselbe ID enthält, ist er nicht identisch mit dem Block 3 der Momentaufnahme. Daher wird das Konto mit Gebühren für vier Blöcke belastet:

![Azure Storage-Ressourcen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Szenario 3**

In Szenario 3 wurde das Basisblob aktualisiert, die Momentaufnahme jedoch nicht. Block 3 wurde im Basis-BLOB durch Block 4 ersetzt, die Momentaufnahme enthält aber immer noch den Block 3. Daher wird das Konto mit Gebühren für vier Blöcke belastet:

![Azure Storage-Ressourcen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Szenario 4**

In Szenario 4 wurde das Basis-Blob vollständig aktualisiert und enthält keinen der ursprünglichen Blöcke. Daher wird das Konto für alle acht eindeutigen Blöcke belastet. Dieses Szenario kann auftreten, wenn Sie eine Updatemethode wie [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] oder [UploadFromByteArray][dotnet_UploadFromByteArray] verwenden, da diese Methoden alle Inhalte eines Blobs ersetzen.

![Azure Storage-Ressourcen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Arbeiten mit Datenträger-Momentaufnahmen für virtuelle Computer (VMs) finden Sie in [Sichern nicht verwalteter Azure-VM-Datenträger mithilfe inkrementeller Momentaufnahmen](../../virtual-machines/windows/incremental-snapshots.md)

* Weitere Codebeispiele zur Verwendung von Blob Storage finden Sie unter [Azure-Codebeispiele](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Sie können eine Beispielanwendung herunterladen und ausführen oder den Code auf GitHub durchsuchen.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.generatedblobs.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext