---
title: Vorläufiges Löschen für Azure Storage-Blobs | Microsoft-Dokumentation
description: Azure Storage ermöglicht jetzt das vorläufige Löschen für Blobobjekte, sodass Sie Ihre Daten leichter wiederherstellen können, wenn sie irrtümlich von einer Anwendung oder einem anderen Benutzer des Speicherkontos geändert oder gelöscht wurden.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 8c23e429966cf9a1e93ac46ea3ecd11744761872
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148624"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Vorläufiges Löschen für Azure Storage-Blobs
Azure Storage ermöglicht jetzt das vorläufige Löschen für Blobobjekte, sodass Sie Ihre Daten leichter wiederherstellen können, wenn sie irrtümlich von einer Anwendung oder einem anderen Benutzer des Speicherkontos geändert oder gelöscht wurden.

## <a name="how-does-it-work"></a>Wie funktioniert dies?
Wenn diese Option aktiviert ist, ermöglicht Ihnen das vorläufige Löschen das Speichern und Wiederherstellen Ihrer Daten, wenn Blobs oder Blobmomentaufnahmen gelöscht wurden. Dieser Schutz erstreckt sich auch auf Blobdaten, die durch Überschreiben gelöscht wurden.

Wenn Daten gelöscht werden, gehen sie in einen vorläufig gelöschten Zustand über, anstatt dauerhaft gelöscht zu werden. Wenn das vorläufige Löschen aktiviert ist und Sie Daten überschreiben, wird eine vorläufig gelöschte Momentaufnahme generiert, um den Zustand der überschriebenen Daten zu speichern. Vorläufig gelöschte Objekte sind nur sichtbar, wenn sie explizit aufgelistet werden. Sie können die Zeitspanne konfigurieren, die vorläufig gelöschte Daten wiederhergestellt werden können, bevor sie dauerhaft ablaufen.

Vorläufiges Löschen ist abwärtskompatibel. Sie müssen keine Änderungen an Ihren Anwendungen vornehmen, um die Schutzvorteile dieses Features zu nutzen. Allerdings wird mit der [Datenwiederherstellung](#recovery) eine neue API zum **Wiederherstellen von Blobs** eingeführt.

### <a name="configuration-settings"></a>Konfigurationseinstellungen
Wenn Sie ein neues Konto erstellen, ist vorläufiges Löschen standardmäßig deaktiviert. Vorläufiges Löschen ist auch für vorhandene Speicherkonten standardmäßig deaktiviert. Sie können das Feature während der Lebensdauer eines Speicherkontos jederzeit aktivieren bzw. deaktivieren.

Sie können weiterhin auf vorläufig gelöschte Daten zugreifen und diese wiederherstellen, wenn das Feature deaktiviert ist, vorausgesetzt, dass vorläufig gelöschte Daten gespeichert wurden, als das Feature zuvor aktiviert war. Wenn Sie vorläufiges Löschen aktivieren, müssen Sie auch die Beibehaltungsdauer konfigurieren.

Die Beibehaltungsdauer gibt die Zeitspanne an, für die vorläufig gelöschte Daten gespeichert werden und für die Wiederherstellung verfügbar sind. Für Blobs und Blobmomentaufnahmen, die explizit gelöscht werden, beginnt die Beibehaltungsdauer, wenn die Daten gelöscht werden. Für vorläufig gelöschte Momentaufnahmen, die durch das Feature zum vorläufigen Löschen generiert werden, wenn Daten überschrieben werden, beginnt die Beibehaltungsdauer beim Generieren der Momentaufnahme. Zurzeit können Sie vorläufig gelöschte Daten zwischen einem Tag und 365 Tagen aufbewahren.

Sie können die Beibehaltungsdauer beim vorläufigen Löschen jederzeit ändern. Eine aktualisierte Beibehaltungsdauer gilt nur für neu gelöschten Daten. Zuvor gelöschte Daten laufen basierend auf der Beibehaltungsdauer ab, die konfiguriert war, als diese Daten gelöscht wurden. Der Versuch, ein vorläufig gelöschtes Objekt zu löschen, wirkt sich nicht auf dessen Ablaufzeit aus.

### <a name="saving-deleted-data"></a>Speichern gelöschter Daten
Vorläufiges Löschen behält Ihre Daten in vielen Fällen bei, in denen Blobs oder Blobmomentaufnahmen gelöscht oder überschrieben werden.

Wenn ein Blob mit **Put Blob**, **Put Block**, **Put Block List** oder **Copy Blob** überschrieben wird, wird automatisch eine Momentaufnahme des Blobzustands vor dem Schreibvorgang generiert. Diese Momentaufnahme ist eine vorläufig gelöschten Momentaufnahme. Sie ist nur dann sichtbar, wenn vorläufig gelöschte Objekte explizit aufgelistet werden. Informationen zum Auflisten vorläufig gelöschte Objekte finden Sie im Abschnitt [Wiederherstellung](#recovery).

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Vorläufig gelöschte Daten werden grau angezeigt, während aktive Daten blau sind. Die zuletzt geschriebenen Daten werden unter den älteren Daten angezeigt. Wenn B0 mit B1 überschrieben wird, wird eine vorläufig gelöschte Momentaufnahme von B0 generiert. Wenn B1 mit B2 überschrieben wird, wird eine vorläufig gelöschte Momentaufnahme von B1 generiert.*

> [!NOTE]  
> Vorläufiges Löschen bietet nur dann einen Überschreibschutz für Kopiervorgänge, wenn diese Option für das Konto des Zielblobs aktiviert ist.

> [!NOTE]  
> Vorläufiges Löschen bietet keinen Überschreibschutz für Blobs in der Archivebene. Wenn ein Blob im Archiv durch ein neues Blob in einer beliebigen Ebene überschrieben wird, wird das überschriebene Blob dauerhaft gelöscht.

Wenn **Delete Blob** für eine Momentaufnahme aufgerufen wird, wird diese Momentaufnahme als „vorläufig gelöscht“ markiert. Eine neue Momentaufnahme wird nicht generiert.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Vorläufig gelöschte Daten werden grau angezeigt, während aktive Daten blau sind. Die zuletzt geschriebenen Daten werden unter den älteren Daten angezeigt. Wenn **Snapshot Blob** aufgerufen wird, wird B0 zu einer Momentaufnahme, und B1 ist der aktive Zustand des Blobs. Wenn die B0-Momentaufnahme gelöscht wird, wird sie als vorläufig gelöscht markiert.*

Wenn **Delete Blob** für ein Basisblob (alle Blobs, die nicht selbst eine Momentaufnahme sind) aufgerufen wird, wird dieses Blob als vorläufig gelöscht markiert. Der Aufruf von **Delete Blob** für ein Blob mit aktiven Momentaufnahmen gibt einen Fehler zurück. Dies ist mit dem vorherigen Verhalten konsistent. Der Aufruf von **Delete Blob** für ein Blob mit vorläufig gelöschten Momentaufnahmen gibt keinen Fehler zurück. Sie können ein Blob und alle zugehörigen Momentaufnahmen weiterhin in einem einzigen Vorgang löschen, wenn vorläufiges Löschen aktiviert ist. Das Basisblob und die Momentaufnahmen werden dann als vorläufig gelöscht markiert.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Vorläufig gelöschte Daten werden grau angezeigt, während aktive Daten blau sind. Die zuletzt geschriebenen Daten werden unter den älteren Daten angezeigt. Hier wird ein Aufruf **Delete Blob** vorgenommen, um B2 und alle zugehörigen Momentaufnahmen zu löschen. Das aktive Blob (B2) und alle zugehörigen Momentaufnahmen werden als vorläufig gelöscht markiert.*

> [!NOTE]  
> Wenn ein vorläufig gelöschtes Blob überschrieben wird, wird automatisch eine vorläufig gelöschte Momentaufnahme des Blobzustands vor dem Schreibvorgang generiert. Das neue Blob erbt die Ebene des überschriebenen Blobs.

Vorläufiges Löschen speichert Ihre Daten nicht, wenn Container oder Konten gelöscht oder Blobmetadaten und Blobeigenschaften überschrieben werden. Um ein Speicherkonto vor versehentlichem Löschen zu schützen, können Sie mit Azure Resource Manager eine Sperre konfigurieren. Weitere Informationen finden Sie im Azure Resource Manager-Artikel [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../../azure-resource-manager/resource-group-lock-resources.md).

Die folgende Tabelle beschreibt das erwartete Verhalten, wenn vorläufiges Löschen aktiviert ist:

| REST-API-Vorgang | Ressourcentyp | BESCHREIBUNG | Änderung im Verhalten |
|--------------------|---------------|-------------|--------------------|
| [Löschen](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Löscht das Speicherkonto einschließlich aller Container und Blobs, die darin enthalten sind.                           | Keine Änderung. Container und Blobs im gelöschten Konto können nicht wiederhergestellt werden. |
| [Delete Container](/rest/api/storageservices/delete-container) | Container | Löscht den Container einschließlich aller darin enthaltenen Blobs. | Keine Änderung. Blobs im gelöschten Container können nicht wiederhergestellt werden. |
| [Put Blob](/rest/api/storageservices/put-blob) | Block-, Anfüge- und Seitenblobs | Erstellt ein neues Blob oder ersetzt ein vorhandenes Blob innerhalb eines Containers. | Wenn dieser Aufruf verwendet wird, um ein vorhandenes Blob zu ersetzen, wird automatisch eine Momentaufnahme des Blobzustands vor dem Aufruf generiert. Dies gilt auch für ein zuvor vorläufig gelöschtes Blob, aber nur dann, wenn es durch ein Blob des gleichen Typs (Block-, Anfüge- oder Seitenblob) ersetzt wird. Wenn es durch ein Blob eines anderen Typs ersetzt wird, werden alle vorhandenen vorläufig gelöschten Daten dauerhaft gelöscht. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Block-, Anfüge- und Seitenblobs | Kennzeichnet ein Blob oder eine Blobmomentaufnahme für die Löschung. Das Blob oder die Momentaufnahme wird später während der Garbage Collection gelöscht. | Wenn der Aufruf zum Löschen einer Blobmomentaufnahme erfolgt, wird diese Momentaufnahme als „vorläufig gelöscht“ markiert. Wenn der Aufruf zum Löschen eines Blobs erfolgt, wird dieses Blob als „vorläufig gelöscht“ markiert. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Block-, Anfüge- und Seitenblobs | Kopiert ein Quellblob in ein Zielblob im gleichen Speicherkonto oder in einem anderen Speicherkonto. | Wenn dieser Aufruf verwendet wird, um ein vorhandenes Blob zu ersetzen, wird automatisch eine Momentaufnahme des Blobzustands vor dem Aufruf generiert. Dies gilt auch für ein zuvor vorläufig gelöschtes Blob, aber nur dann, wenn es durch ein Blob des gleichen Typs (Block-, Anfüge- oder Seitenblob) ersetzt wird. Wenn es durch ein Blob eines anderen Typs ersetzt wird, werden alle vorhandenen vorläufig gelöschten Daten dauerhaft gelöscht. |
| [Put Block](/rest/api/storageservices/put-block) | Blockblobs | Erstellt einen neuen Block, der als Teil eines Blockblobs committet werden soll. | Wenn der Aufruf verwendet wird, um einen Block an ein Blob zu committen, das aktiv ist, erfolgt keine Änderung. Wenn der Aufruf verwendet wird, um einen Block an ein Blob zu committen, das vorläufig gelöscht ist, wird ein neues Blob erstellt, und es wird automatisch eine Momentaufnahme generiert, um den Zustand des vorläufig gelöschten Blobs zu erfassen. |
| [Put Block List](/rest/api/storageservices/put-block-list) | Blockblobs | Committet ein Blob, indem die Sammlung der Block-IDs angegeben wird, aus denen ein Blockblob besteht. | Wenn dieser Aufruf verwendet wird, um ein vorhandenes Blob zu ersetzen, wird automatisch eine Momentaufnahme des Blobzustands vor dem Aufruf generiert. Dies gilt auch für ein zuvor vorläufig gelöschtes Blob, aber nur, wenn es sich um ein Blockblob handelt. Wenn es durch ein Blob eines anderen Typs ersetzt wird, werden alle vorhandenen vorläufig gelöschten Daten dauerhaft gelöscht. |
| [Put Page](/rest/api/storageservices/put-page) | Seitenblobs | Schreibt einen Bereich von Seiten in ein Seitenblob. | Keine Änderung. Seitenblobdaten, die mit diesem Vorgang überschrieben oder gelöscht werden, werden nicht gespeichert und können nicht wiederhergestellt werden. |
| [Append Block](/rest/api/storageservices/append-block) | Anfügeblobs | Schreibt einen Block von Daten an das Ende eines Anfügeblobs. | Keine Änderung. |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | Block-, Anfüge- und Seitenblobs | Legt Werte für Systemeigenschaften fest, die für ein Blob definiert sind. | Keine Änderung. Überschriebene Blobeigenschaften können nicht wiederhergestellt werden. |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | Block-, Anfüge- und Seitenblobs | Legt benutzerdefinierte Metadaten für das angegebene Blob als mindestens ein Name-Wert-Paar fest. | Keine Änderung. Überschriebene Blobmetadaten können nicht wiederhergestellt werden. |

Es ist wichtig zu beachten, dass beim Aufrufen von „Put Page“ zum Überschreiben oder Löschen von Bereichen eines Seitenblobs nicht automatisch Momentaufnahmen generiert werden. Datenträger virtueller Computer werden durch Seitenblobs gesichert und verwenden **Put Page** zum Schreiben von Daten.

### <a name="recovery"></a>Wiederherstellen
Um das Wiederherstellen gelöschter Daten zu erleichtern, haben wir eine neue API zum Wiederherstellen von Blobs (Undelete Blob) eingeführt. Durch den Aufruf der Wiederherstellungs-API für ein vorläufig gelöschtes Basisblob werden dieses sowie alle zugehörigen vorläufig gelöschten Momentaufnahmen als aktiv wiederhergestellt. Durch den Aufruf der Wiederherstellungs-API für ein aktives Basisblob werden alle zugehörigen vorläufig gelöschten Momentaufnahmen als aktiv wiederhergestellt. Wenn Momentaufnahmen als aktiv wiederhergestellt werden, sehen sie wie vom Benutzer generierte Momentaufnahmen aus. Sie überschreiben das Basisblob nicht.

Um ein Blob in einer bestimmten vorläufig gelöschten Momentaufnahme wiederherzustellen, können Sie **Undelete Blob** für das Basisblob aufrufen. Anschließend können Sie die Momentaufnahme über das jetzt aktive Blob kopieren. Sie können die Momentaufnahme auch in ein neues Blob kopieren.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Vorläufig gelöschte Daten werden grau angezeigt, während aktive Daten blau sind. Die zuletzt geschriebenen Daten werden unter den älteren Daten angezeigt. Hier wird **Undelete Blob** für Blob B, aufgerufen, um das Basisblob (B1) und alle zugehörigen Momentaufnahmen (hier nur B0) als aktiv wiederherzustellen. Im zweiten Schritt wird B0 über das Basisblob kopiert. Dieser Kopiervorgang generiert eine vorläufig gelöschte Momentaufnahme von B1.*

Zum Anzeigen von vorläufig gelöschten Blobs und Blobmomentaufnahmen können Sie gelöschte Daten wahlweise in **List Blobs** einschließen. Sie können nur vorläufig gelöschte Basisblobs anzeigen oder auch vorläufig gelöschte Blobmomentaufnahmen einschließen. Für alle vorläufig gelöschten Daten können Sie die Zeit anzeigen, zu der die Daten gelöscht wurden, sowie die Anzahl der Tage, bevor die Daten dauerhaft gelöscht werden.

### <a name="example"></a>Beispiel
Das folgende Beispiel zeigt die Konsolenausgabe eines .NET-Skripts, mit dem das Blob „HelloWorld“ hochgeladen, überschrieben, eine Momentaufnahme dafür erstellt und es gelöscht und wiederhergestellt wird, wenn vorläufiges Löschen aktiviert ist:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Im Abschnitt [Nächste Schritte](#next-steps) finden Sie einen Hinweis auf die Anwendung, die diese Ausgabe generiert.

## <a name="pricing-and-billing"></a>Preise und Abrechnung
Alle vorläufig gelöschten Daten werden mit dem gleichen Tarif wie aktive Daten in Rechnung gestellt. Für Daten, die nach Ablauf der konfigurierten Beibehaltungsdauer dauerhaft gelöscht werden, fallen keine Kosten an. Ausführlichere Informationen zu Momentaufnahmen und den dafür anfallenden Kosten finden Sie unter [Understanding how snapshots accrue charges](storage-blob-snapshots.md) (Informationen zu den Kosten von Momentaufnahmen).

Ihnen werden keine Transaktionen im Zusammenhang mit der automatischen Generierung von Momentaufnahmen in Rechnung gestellt. Ihnen werden **Undelete Blob**-Transaktionen zum Tarif für „Schreibvorgänge“ in Rechnung gestellt.

Weitere Informationen zu Preisen für Azure Blob Storage im Allgemeinen finden Sie auf der Seite [Preise für Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Wenn Sie erstmals vorläufiges Löschen aktivieren, wird empfohlen, eine kurze Beibehaltungsdauer zu verwenden, um besser zu verstehen, wie sich das Feature auf Ihre Rechnung auswirkt.

## <a name="quickstart"></a>Schnellstart
### <a name="azure-portal"></a>Azure-Portal
Navigieren Sie zum Aktivieren von vorläufigem Löschen unter **Blobdienst** zur Option **Vorläufiges Löschen**. Klicken Sie dann auf **Aktiviert**, und geben Sie die Anzahl der Tage ein, die vorläufig gelöschte Daten beibehalten werden sollen.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Aktivieren Sie zum Anzeigen von vorläufig gelöschten Blobs das Kontrollkästchen **Gelöschte Blobs anzeigen**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Um vorläufig gelöschte Momentaufnahmen für ein bestimmtes Blob anzuzeigen, wählen Sie das Blob aus, und klicken Sie dann auf **Momentaufnahmen anzeigen**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Stellen Sie sicher, dass das Kontrollkästchen **Gelöschte Momentaufnahmen anzeigen** aktiviert ist.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Beachten Sie beim Klicken auf ein vorläufig gelöschtes Blob oder eine Momentaufnahme die neuen Blobeigenschaften. Sie geben an, wann das Objekt gelöscht wurde und wie viele Tage verbleiben, bis das Blob oder die Blobmomentaufnahme dauerhaft gelöscht wird. Wenn das vorläufig gelöschte Objekt keine Momentaufnahme ist, ist auch eine Option verfügbar, den Löschvorgang rückgängig zu machen.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Denken Sie daran, dass bei der Wiederherstellung eines Blob auch alle zugehörige Momentaufnahmen wiederhergestellt werden. Um vorläufig gelöschte Momentaufnahmen für ein aktives Blob wiederherzustellen, klicken Sie auf das Blob, und wählen Sie dann **Alle Momentaufnahmen wiederherstellen** aus.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Sobald Sie die Momentaufnahmen eines Blobs wiederhergestellt haben, können Sie auf **Höher stufen** klicken, um eine Momentaufnahme über das Stammblob zu kopieren und so das Blob in der Momentaufnahme wiederherzustellen.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aktualisieren Sie zum Aktivieren des vorläufigen Löschens die Diensteigenschaften eines Blobclients. Im folgenden Beispiel wird das vorläufige Löschen für eine Teilmenge der Konten in einem Abonnement aktiviert:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Mithilfe des folgenden Befehls können Sie überprüfen, ob vorläufiges Löschen aktiviert wurde:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Rufen Sie zum Wiederherstellen von Blobs, die versehentlich gelöscht wurden, „Undelete“ für diese Blobs auf. Denken Sie daran, dass der Aufruf von **Undelete Blob** (für aktive und vorläufig gelöschte Blobs) alle zugeordneten vorläufig gelöschten Momentaufnahmen als aktiv wiederherstellt. Im folgende Beispiel wird „Undelete“ für alle vorläufig gelöschten und aktiven Blobs in einem Container aufgerufen:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Mithilfe des folgenden Befehls können Sie die aktuelle Aufbewahrungsrichtlinie für vorläufiges Löschen suchen:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle 
Aktualisieren Sie zum Aktivieren des vorläufigen Löschens die Diensteigenschaften eines Blobclients:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Verwenden Sie den folgenden Befehl, um sicherzustellen, dass das vorläufige Löschen aktiviert ist: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Python-Clientbibliothek
Aktualisieren Sie zum Aktivieren des vorläufigen Löschens die Diensteigenschaften eines Blobclients:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>.NET-Clientbibliothek
Aktualisieren Sie zum Aktivieren des vorläufigen Löschens die Diensteigenschaften eines Blobclients:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Rufen Sie zum Wiederherstellen von Blobs, die versehentlich gelöscht wurden, „Undelete“ für diese Blobs auf. Denken Sie daran, dass der Aufruf von **Undelete Blob** (für aktive und vorläufig gelöschte Blobs) alle zugeordneten vorläufig gelöschten Momentaufnahmen als aktiv wiederherstellt. Im folgende Beispiel wird „Undelete“ für alle vorläufig gelöschten und aktiven Blobs in einem Container aufgerufen:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Um eine bestimmte Blobversion wiederherzustellen, rufen Sie zuerst „Undelete“ für ein Blob auf, und kopieren Sie dann die gewünschte Momentaufnahme über das Blob. Im folgenden Beispiel wird die zuletzt generierte Momentaufnahme eines Blockblobs wiederhergestellt:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

## <a name="should-i-use-soft-delete"></a>Sollte ich vorläufiges Löschen verwenden?
Wenn die Möglichkeit besteht, dass Ihre Daten versehentlich geändert oder von einer Anwendung oder einem anderen Benutzer des Speicherkontos gelöscht werden, wird empfohlen, vorläufiges Löschen zu aktivieren. Vorläufiges Löschen ist ein Teil einer Strategie für den Datenschutz und kann verhindern, dass es zu unbeabsichtigten Datenverlusten kommt.

## <a name="faq"></a>Häufig gestellte Fragen
**Gelten für das Verwenden des vorläufigen Löschens besondere Überlegungen?**  
Das Aktivieren des vorläufigen Löschens für häufig überschriebene Daten kann zu höheren Gebühren für die Speicherkapazität und einer höheren Latenz beim Auflisten der Blobs führen. Sie können dies umgehen, indem Sie häufig überschriebene Daten in einem separaten Speicherkonto speichern, in dem das vorläufige Löschen deaktiviert ist. 

**Für welche Speichertypen kann ich vorläufiges Löschen verwenden?**  
Zurzeit ist vorläufiges Löschen nur für Blobspeicher (Objektspeicher) verfügbar.

**Ist vorläufiges Löschen für alle Speicherkontotypen verfügbar?**  
Ja, vorläufiges Löschen ist für Blobspeicherkonten sowie für Blobs in allgemeinen Speicherkonten (sowohl GPv1 als auch GPv2) verfügbar. Dies gilt für Standard- und Premium-Konten. Vorläufiges Löschen ist für verwaltete Datenträger nicht verfügbar.

**Ist vorläufiges Löschen für alle Speicherebenen verfügbar?**  
Ja, vorläufiges Löschen ist für alle Speicherebenen einschließlich „heiß“, „kalt“ und „Archiv“ verfügbar. Vorläufiges Löschen bietet jedoch keinen Überschreibschutz für Blobs in der Archivebene.

**Kann ich die API zum Festlegen des Blobtarifs für Blobs mit vorläufig gelöschten Momentaufnahmen verwenden?**  
Ja. Die vorläufig gelöschten Momentaufnahmen verbleiben im ursprünglichen Tarif, aber das Basisblob wird in den neuen Tarif verschoben. 

**Storage Premium-Konten weisen eine Momentaufnahmengrenze von 100 pro Blob auf. Werden vorläufig gelöschte Momentaufnahmen für diesen Grenzwert berücksichtigt?**  
Nein, vorläufig gelöschte Momentaufnahmen werden für diesen Grenzwert nicht berücksichtigt.

**Kann ich vorläufiges Löschen für vorhandene Speicherkonten aktivieren?**  
Ja, vorläufiges Löschen kann für vorhandene und neue Speicherkonten konfiguriert werden.

**Wenn ich ein gesamtes Konto oder einen Container lösche, wenn vorläufiges Löschen aktiviert ist, werden dann alle zugehörigen Blobs gesichert?**  
Nein, wenn Sie ein gesamtes Konto oder einen Container löschen, werden alle zugehörigen Blobs dauerhaft gelöscht. Um zu erfahren, wie Sie ein Speicherkonto vor versehentlichem Löschen schützen können, lesen Sie den Azure Resource Manager-Artikel [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../../azure-resource-manager/resource-group-lock-resources.md).

**Kann ich Kapazitätsmetriken für gelöschte Daten anzeigen?**  
Vorläufig gelöschte Daten sind als Teil der Gesamtkapazität Ihres Speicherkontos enthalten. Weitere Informationen zum Nachverfolgen und Überwachen der Speicherkapazität finden im Artikel zu [Storage Analytics](../common/storage-analytics.md).

**Wenn ich vorläufiges Löschen deaktiviere, kann ich dann trotzdem auf vorläufig gelöschte Daten zugreifen?**  
Ja, Sie können weiterhin auf nicht abgelaufene vorläufig gelöschte Daten zugreifen und diese wiederherstellen, wenn vorläufiges Löschen deaktiviert ist.

**Kann ich vorläufig gelöschte Momentaufnahmen meines Blobs lesen und daraus kopieren?**  
Ja, aber Sie müssen zuerst „Undelete“ für das Blob aufrufen.

**Ist vorläufiges Löschen für alle Blobtypen verfügbar?**  
Ja, vorläufiges Löschen ist für Blockblobs, Anfügeblobs und Seitenblobs verfügbar.

**Ist vorläufiges Löschen für die Datenträger virtueller Computer verfügbar?**  
Vorläufiges Löschen steht für nicht verwaltete Premium- und Standard-Datenträger zur Verfügung. Vorläufiges Löschen kann Sie nur beim Wiederherstellen von Daten unterstützen, die mit **Delete Blob**, **Put Blob**, **Put Block List**, **Put Block** und **Copy Blob** gelöscht wurden. Daten, die durch einen Aufruf von **Put Page** überschrieben wurden, können nicht wiederhergestellt werden.

**Muss ich zur Verwendung von vorläufigem Löschen meine vorhandenen Anwendungen ändern?**  
Es ist möglich, vorläufiges Löschen unabhängig von der API-Version zu nutzen, die Sie verwenden. Allerdings müssen Sie zum Auflisten und Wiederherstellen vorläufig gelöschter Blobs und Blobmomentaufnahmen die Version vom 29.07.2017 der [Speicherdienste-REST-API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) oder eine höhere Version verwenden. Unabhängig von der Nutzung dieses Features empfehlen wir grundsätzlich die Verwendung der neuesten Version.

## <a name="next-steps"></a>Nächste Schritte
* [.NET-Beispielcode](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [REST-API des Blob-Diensts](/rest/api/storageservices/blob-service-rest-api)
* [Azure Storage-Replikation](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Entwerfen hochverfügbarer Anwendungen mithilfe von RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Notfallwiederherstellung und Speicherkontofailover (Vorschau) in Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
