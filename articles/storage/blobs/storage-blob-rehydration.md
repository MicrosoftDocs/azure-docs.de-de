---
title: Aktivieren von Blobdaten aus der Archivzugriffsebene
description: In diesem Artikel erfahren Sie, wie Sie Ihre Blobs aus dem Archivspeicher aktivieren, um auf die Blobdaten zugreifen zu können. Sie erfahren außerdem, wie Sie ein archiviertes Blob auf eine Onlinedienstebene kopieren.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: f74d4ffdd724039354a311234317dac889cd7cfe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545930"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Aktivieren von Blobdaten aus der Archivzugriffsebene

Während ein Blob sich auf der Archivzugriffsebene befindet, wird es als offline betrachtet und kann nicht gelesen oder geändert werden. Die Blobmetadaten bleiben online und verfügbar, sodass Sie das Blob und seine Eigenschaften auflisten können. Das Lesen und Ändern von Blobdaten ist nur auf Onlineebenen wie der heißen oder der kalten Zugriffsebene möglich. Es gibt zwei Optionen, um Daten aus der Archivzugriffsebene abzurufen und auf sie zuzugreifen.

1. [Aktivieren eines archivierten Blobs auf einer Onlineebene](#rehydrate-an-archived-blob-to-an-online-tier): Aktivieren Sie ein Archivblob auf der heißen oder kalten Zugriffsebene, indem Sie die Ebene mithilfe des Vorgangs [Blobebene festlegen](/rest/api/storageservices/set-blob-tier) ändern.
2. [Kopieren eines archivierten Blobs auf eine Onlineebene](#copy-an-archived-blob-to-an-online-tier): Erstellen Sie eine neue Kopie eines Archivblobs mithilfe des Vorgangs [Blob kopieren](/rest/api/storageservices/copy-blob). Geben Sie einen anderen Blobnamen und als Zielebene eine heiße oder kalte Zugriffsebene an.

 Weitere Informationen zu Ebenen finden Sie unter [Azure Blob Storage: Archivzugriffsebenen „Heiß“, „Kalt“ und „Archiv“](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Aktivieren eines archivierten Blobs auf einer Onlineebene

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopieren eines archivierten Blobs auf eine Onlineebene

Wenn Sie das Archivblob nicht aktivieren möchten, können Sie einen Vorgang vom Typ [Blob kopieren](/rest/api/storageservices/copy-blob) ausführen. Das ursprüngliche Blob bleibt unverändert im Archiv, während ein neues Blobs auf der heißen oder kalten Onlineebene erstellt wird, mit dem Sie arbeiten können. Beim Vorgang „Blob kopieren“ können Sie auch die optionale Eigenschaft *x-ms-rehydrate-priority* auf „Standard“ oder „Hoch“ festlegen, um die Priorität anzugeben, mit der die Blobkopie erstellt werden soll.

Das Kopieren eines Blobs aus dem Archiv kann je nach ausgewählter Aktivierungspriorität mehrere Stunden dauern. Im Hintergrund liest der Vorgang **Blob kopieren** das Archivblob, um ein neues Onlineblob auf der ausgewählten Zielebene zu erstellen. Das neue Blob ist beim Auflisten von Blobs möglicherweise sichtbar, aber die Daten sind erst verfügbar, wenn der Lesevorgang aus dem Quellarchivblob abgeschlossen ist und Daten in das neue Onlinezielblob geschrieben werden. Das neue Blob ist eine unabhängige Kopie, und jegliche Änderungen oder Löschungen wirken sich nicht auf das Quellarchivblob aus.

> [!IMPORTANT]
> Löschen Sie das Quellblob erst, nachdem der Kopiervorgang am Ziel erfolgreich abgeschlossen wurde. Wenn das Quellblob gelöscht wird, wird das Kopieren des Zielblobs möglicherweise nicht beendet, und das Blob ist leer. Sie können den *x-ms-copy-status* überprüfen, um den Status des Kopiervorgangs zu bestimmen.

Archivblobs können nur auf Onlinezielebenen innerhalb desselben Speicherkontos kopiert werden. Das Kopieren eines Archivblobs in ein anderes Archivblob wird nicht unterstützt. In der folgenden Tabelle sind die Funktionen von CopyBlob angegeben.

|                                           | **Quelle: Heiße Ebene**   | **Quelle: Kalte Ebene** | **Quelle: Archivebene**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Ziel: Heiße Ebene**                  | Unterstützt             | Unterstützt            | Innerhalb desselben Kontos unterstützt, Aktivierung steht aus               |
| **Ziel: Kalte Ebene**                 | Unterstützt             | Unterstützt            | Innerhalb desselben Kontos unterstützt, Aktivierung steht aus               |
| **Ziel: Archivebene**              | Unterstützt             | Unterstützt            | Nicht unterstützt         |

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Die Vorgänge zum Aktivieren von Blobs aus dem Archiv auf einer heißen oder kalten Zugriffsebene werden als Lese- und Datenabrufvorgänge abgerechnet. Vorgänge mit hoher Priorität verursachen höhere Vorgangs- und Datenabrufkosten als Vorgänge mit Standardpriorität. Aktivierungsvorgänge mit hoher Priorität werden auf Ihrer Rechnung als separater Posten ausgewiesen. Wenn eine Anforderung zum Abruf eines einige Gigabytes großen Archivblobs mit hoher Priorität über fünf Stunden dauert, werden Ihnen nicht die Gebühren für einen Abruf mit hoher Priorität berechnet. Die Standardgebühren für Abrufvorgänge gelten jedoch weiterhin, da die Aktivierung gegenüber anderen Anforderungen priorisiert wurde.

Die Vorgänge zum Kopieren von Blobs aus dem Archiv auf eine heiße oder kalte Zugriffsebene werden als Lese- und Datenabrufvorgänge abgerechnet. Für die Erstellung der neuen Blobkopie wird ein Schreibvorgang in Rechnung gestellt. Gebühren für frühes Löschen fallen beim Kopieren in ein Onlineblob nicht an, weil das Quellblob auf der Archivzugriffsebene unverändert bleibt. Gebühren für einen Abruf mit hoher Priorität gelten, wenn dieser ausgewählt ist.

Blobs auf Archivzugriffsebene müssen mindestens 180 Tage lang gespeichert werden. Wenn archivierte Blobs vor Ablauf von 180 Tagen gelöscht oder aktiviert werden, fallen Gebühren für frühes Löschen an.

> [!NOTE]
> Weitere Informationen zu den Preisen für Blockblobs und Datenaktivierung finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Weitere Informationen zu den Kosten für ausgehende Datenübertragungen finden Sie unter [Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Schnellstartszenarien

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Aktivieren eines Archivblobs auf einer Onlineebene
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im Azure-Portal nach **Alle Ressourcen**, und wählen Sie den Eintrag aus.

1. Wählen Sie dann Ihr Speicherkonto aus.

1. Wählen Sie Ihren Container und dann Ihr Blob aus.

1. Wählen Sie unter **Blob-Eigenschaften** die Option **Ebene ändern** aus.

1. Wählen Sie die Zugriffsebene **Heiß** oder **Kalt** aus. 

1. Wählen Sie eine Aktivierungspriorität von **Standard** oder **Hoch** aus.

1. Wählen Sie unten **Speichern** aus.

![Ändern der Speicherkontoebene](media/storage-tiers/blob-access-tier.png)
![Überprüfen des Aktivierungsstatus](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Das nachstehende PowerShell-Skript kann verwendet werden, um die Blobebene eines Archivblobs zu ändern. Die Variable `$rgName` muss mit Ihrem Ressourcengruppennamen initialisiert werden. Die Variable `$accountName` muss mit Ihrem Speicherkontonamen initialisiert werden. Die Variable `$containerName` muss mit Ihrem Containernamen initialisiert werden. Die Variable `$blobName` muss mit Ihrem Blobnamen initialisiert werden. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Kopieren eines Archivblobs in ein neues Blob mit einer Onlineebene
Das nachstehende PowerShell-Skript kann verwendet werden, um ein Archivblob in ein neues Blob innerhalb desselben Speicherkontos zu kopieren. Die Variable `$rgName` muss mit Ihrem Ressourcengruppennamen initialisiert werden. Die Variable `$accountName` muss mit Ihrem Speicherkontonamen initialisiert werden. Die Variablen `$srcContainerName` und `$destContainerName` müssen mit Ihren Containernamen initialisiert werden. Die Variablen `$srcBlobName` und `$destBlobName` müssen mit Ihren Blobnamen initialisiert werden. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zu Blobspeicherebenen](storage-blob-storage-tiers.md)
* Prüfen der Preise für „Heiß“, „Kalt“ und „Archiv“ in Blobspeicher- und GPv2-Konten nach Region unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Verwalten des Azure Blob Storage-Lebenszyklus](storage-lifecycle-management-concepts.md)
* [Überprüfen der Preise für Datenübertragungen unter Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/)