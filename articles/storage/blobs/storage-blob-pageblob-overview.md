---
title: Übersicht über Azure-Seitenblobs | Microsoft-Dokumentation
description: Eine Übersicht über Azure-Seitenblobs und deren Vorteile sowie Anwendungsfälle mit Beispielskripts.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 88bf81852a4501f4fc5807d865214d57dbc0aab3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794497"
---
# <a name="overview-of-azure-page-blobs"></a>Übersicht über Azure-Seitenblobs

Azure Storage bietet drei Typen von Blobs: Blockblobs, Seitenblobs und Anfügeblobs. Blockblobs bestehen aus Blöcken und eignen sich ideal für das Speichern von Text- oder Binärdateien und zum effizienten Hochladen großer Dateien. Anfügeblobs bestehen auch aus Blöcken, sind aber für Anfügevorgänge optimiert und damit ideal für Protokollierungsszenarien. Seitenblobs bestehen aus 512-Byte-Seiten, können insgesamt 8 TB groß sein und sind für häufige wahlfreie Lese-/Schreibzugriffe vorgesehen. Seitenblobs sind die Grundlage von Azure-IaaS-Datenträgern. In diesem Artikel werden die Features und Vorteile von Seitenblobs erläutert.

Seitenblobs sind eine Sammlung von 512-Byte-Seiten, die Lese-/Schreibzugriff auf beliebige Bytebereiche ermöglichen. Dadurch eignen sich Seitenblobs perfekt zum Speichern indexbasierter und platzsparender Datenstrukturen wie Betriebssystemdatenträger und sonstiger Datenträger für VMs und Datenbanken. Azure SQL-Datenbank verwendet Seitenblobs beispielsweise als zugrunde liegenden permanenten Speicher für seine Datenbanken. Darüber hinaus werden Seitenblobs auch häufig für Dateien mit bereichsbasierten Updates verwendet.  

Schlüsselfeatures von Azure-Seitenblobs sind die REST-Schnittstelle, die Dauerhaftigkeit des zugrunde liegenden Speichers und die Fähigkeit zur nahtlosen Migration zu Azure. Diese Features werden im nächsten Abschnitt ausführlicher besprochen. Darüber hinaus werden Azure-Seitenblobs derzeit von zwei Speichertypen unterstützt: Storage Premium und Storage Standard. Storage Premium wurde speziell für Workloads konzipiert, die konsistent hohe Leistung und geringe Wartezeit erfordern, sodass Premium-Seitenblobs für Hochleistungsspeicher-Szenarien ideal sind. Standardspeicherkonten sind kostengünstiger zur Ausführung von Workloads, bei denen Wartezeiten eine untergeordnete Rolle spielen.

## <a name="sample-use-cases"></a>Beispiele für Anwendungsfälle

In diesem Abschnitt werden einige Anwendungsfälle für Seitenblobs erläutert. Den Anfang machen Azure-IaaS-Datenträger. Azure-Seitenblobs bilden das Rückgrat der Plattform für virtuelle Datenträger für Azure-IaaS. Sowohl Azure-Datenträger für das Betriebssystem als auch für die zu speichernden Daten werden als virtuelle Datenträger implementiert, wo Daten auf der Azure Storage-Plattform persistent gespeichert und anschließend für optimale Leistung an die virtuellen Computer übermittelt werden. Azure-Datenträger liegen im [VHD-Format](https://technet.microsoft.com/library/dd979539.aspx) von Hyper-V vor und werden als [Seitenblob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) in Azure Storage gespeichert. Zusätzlich zur Verwendung virtueller Datenträger für Azure-IaaS-VMs ermöglichen Seitenblobs auch PaaS- und DBaaS-Szenarien. Ein Beispiel wäre etwa der Azure SQL-DB-Dienst, der derzeit Seitenblobs zum Speichern von SQL-Daten verwendet, um schnelle wahlfreie Lese-/Schreibzugriffe für die Datenbank zu ermöglichen. Ein weiteres Beispiel: Bei einem PaaS-Dienst für den Zugriff auf freigegebene Medien für Anwendungen für gemeinsame Videobearbeitung ermöglichen Seitenblobs schnellen Zugriff auf zufällige Positionen in den Medien. Außerdem ermöglichen sie schnelles und effizientes Bearbeiten und Zusammenführen derselben Medien durch mehrere Benutzer. 

Für Microsoft-Erstanbieterdienste wie Azure Site Recovery und Azure Backup sowie von vielen Drittanbieterentwicklern wurden branchenführende Innovationen mithilfe der REST-Schnittstelle des Seitenblobs implementiert. Zu den einzigartigen in Azure implementierten Szenarien zählen: 

* Anwendungsorientierte inkrementelle Momentaufnahmeverwaltung: Anwendungen können Momentaufnahmen von Seitenblobs und REST-APIs nutzen, um die Anwendungsprüfpunkte ohne kostspielige Datenduplizierung zu speichern. Azure Storage unterstützt lokale Momentaufnahmen für Seitenblobs, für die nicht das Kopieren des gesamten Blobs erforderlich ist. Diese öffentlichen Momentaufnahmen-APIs ermöglichen auch das Zugreifen auf und Kopieren von Deltas zwischen Momentaufnahmen.
* Livemigration der Anwendung und Daten aus der lokalen Umgebung zur Cloud: Kopieren Sie die lokalen Daten, und schreiben Sie mithilfe von REST-APIs direkt in ein Azure-Seitenblob, während die lokale VM weiterhin ausgeführt wird. Sobald das Ziel erreicht ist, können Sie schnell ein Failover zu der Azure-VM ausführen, die die Daten verwendet. Dies ermöglicht die Migration Ihrer virtuellen Computer und virtuellen Datenträger vom lokalen Standort zur Cloud mit minimaler Ausfallzeit, da die Datenmigration im Hintergrund durchgeführt wird, während Sie weiterhin den virtuellen Computer verwenden. Die für das Failover erforderliche Ausfallzeit ist kurz (wenige Minuten).
* [SAS-basierter](../common/storage-dotnet-shared-access-signature-part-1.md) freigegebener Zugriff, der Szenarien wie z.B. mehrere Lese- und einzelne Schreibvorgänge mit Unterstützung für Gleichzeitigkeitssteuerung ermöglicht.

## <a name="page-blob-features"></a>Features für Seitenblobs

### <a name="rest-api"></a>REST-API

Machen Sie sich anhand des folgenden Dokuments mit der [Entwicklung mithilfe von Seitenblobs](storage-dotnet-how-to-use-blobs.md) vertraut. Sehen Sie sich als Beispiel den Zugriff auf Seitenblobs mit der Storage-Clientbibliothek für .NET an. 

Das folgende Diagramm beschreibt die allgemeinen Beziehungen zwischen Konto, Containern und Seitenblobs.

![Screenshot der Beziehungen zwischen Konto, Containern und Seitenblobs](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Erstellen eines leeren Seitenblobs mit einer bestimmten Größe

Zur Erstellung eines Seitenblobs erstellen wir zunächst ein **CloudBlobClient**-Objekt mit dem Basis-URI für den Zugriff auf den Blobspeicher für Ihr Speicherkonto (*pbaccount* in Abbildung 1) sowie das **StorageCredentialsAccountAndKey**-Objekt, wie im folgenden Beispiel zu sehen. Das Beispiel zeigt anschließend das Erstellen eines Verweises auf ein **CloudBlobContainer**-Objekt und dann das Erstellen des Containers (*testvhds*), wenn dieser nicht bereits vorhanden ist. Dann können Sie mit dem **CloudBlobContainer**-Objekt durch Angabe des Namens des Seitenblobs („os4.vhd“), auf das Sie zugreifen möchten, einen Verweis auf ein **CloudPageBlob**-Objekt erstellen. Zum Erstellen des Seitenblobs rufen Sie [CloudPageBlob.Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create) auf und übergeben die maximale Größe für das Blob, das Sie erstellen möchten. Der Wert von *blobSize* muss ein Vielfaches von 512 Byte sein.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Ändern der Größe eines Seitenblobs

Verwenden Sie zum Ändern der Größe eines Seitenblobs nach der Erstellung die [Resize](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize)-Methode. Die angeforderte Größe sollte ein Vielfaches von 512 Bytes sein.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Schreiben von Seiten in ein Seitenblob

Verwenden Sie zum Schreiben von Seiten die [CloudPageBlob.WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages)-Methode.  So können Sie eine sequenzielle Reihe von Seiten bis zu 4 MB schreiben. Der Offset, in den geschrieben wird, muss an einer 512-Byte-Begrenzung beginnen (startingOffset % 512 == 0) und an einer 512-Begrenzung -1 enden.  Im folgenden Codebeispiel wird das Aufrufen von **WritePages** für ein Blob veranschaulicht:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Sobald eine Schreibanforderung für eine sequenzielle Reihe von Seiten im Blobdienst erfolgreich ist und für Dauerhaftigkeit und Resilienz repliziert wird, wird der Schreibvorgang committet, und der Client erhält eine Erfolgsmeldung.  

Das folgende Diagramm zeigt 2 separate Schreibvorgänge:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Einen Schreibvorgang mit der Länge 1.024 Bytes, der am Offset 0 beginnt 
2.  Einen Schreibvorgang mit der Länge 1.024 Bytes, der am Offset 4096 beginnt 

#### <a name="reading-pages-from-a-page-blob"></a>Lesen von Seiten aus einem Seitenblob

Verwenden Sie zum Lesen von Seiten die [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray)-Methode, um einen Bereich von Bytes aus dem Seitenblob zu lesen. So können Sie das vollständige Blob oder einen Bereich von Bytes, der an einem beliebigen Offset im Blob beginnt, herunterladen. Beim Lesen muss der Offset nicht bei einem Vielfachen von 512 beginnen. Beim Lesen von Bytes aus einer NULL-Seite gibt der Dienst 0 (null) Bytes zurück.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

Die folgende Abbildung zeigt einen Lesevorgang mit einem Offset von 256 und einer Bereichsgröße von 4352. Die zurückgegebenen Daten sind in Orange hervorgehoben. Nullen werden für NULL-Seiten zurückgegeben.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Bei einem platzsparend gefüllten Blob sollten Sie nur die gültigen Seitenbereiche herunterladen, um nicht für ausgehende 0 (null) Bytes zu zahlen und die Downloadwartezeit zu verringern.  Bestimmen Sie mit [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges), welche Seiten Daten enthalten. Sie können dann die zurückgegebenen Bereiche aufzählen und die Daten in jedem Bereich herunterladen. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

#### <a name="leasing-a-page-blob"></a>Leasen eines Seitenblobs

Das Leasen eines Blobs richtet eine Sperre für Schreib- und Löschvorgänge für ein Blob ein und verwaltet sie. Dieser Vorgang ist nützlich in Szenarien, in denen mehrere Clients auf ein Seitenblob zugreifen, um sicherzustellen, dass jeweils nur ein einziger Client in das Blob schreiben kann. Azure-Datenträger nutzen diesen Leasemechanismus z.B., um sicherzustellen, dass der Datenträger nur von einem einzelnen virtuellen Computer verwaltet wird. Die Sperrdauer kann 15 bis 60 Sekunden betragen oder unendlich sein. Weitere Informationen finden Sie [hier](/rest/api/storageservices/lease-blob).

Zusätzlich zu umfangreichen REST-APIs bieten Seitenblobs auch freigegebenen Zugriff, Dauerhaftigkeit und verbesserte Sicherheit. Wir werden diese Vorteile ausführlicher in den nächsten Abschnitten behandeln. 

### <a name="concurrent-access"></a>Paralleler Zugriff

Mit der Seitenblob-REST-API und ihrem Leasingmechanismus können Anwendungen von mehreren Clients aus auf das Seitenblob zugreifen. Stellen Sie sich beispielsweise vor, Sie müssten einen verteilten Clouddienst erstellen, der mehreren Benutzern die gemeinsame Nutzung von Speicherobjekten ermöglicht. Dies könnte eine Webanwendung sein, die verschiedenen Benutzern eine umfangreiche Sammlung von Bildern zur Verfügung stellt. Eine Möglichkeit, dies zu implementieren, ist die Verwendung eines virtuellen Computers mit angefügten Datenträgern. Dies hat folgende Nachteile: (i) Die Einschränkung, dass ein Datenträger nur einem einzelnen virtuellen Computer angehängt werden kann, was Skalierbarkeit und Flexibilität einschränkt und die Risiken steigert. Wenn ein Problem mit dem virtuellen Computer oder dem Dienst, der auf dem virtuellen Computer ausgeführt wird, auftritt, kann aufgrund der Lease nicht auf das Image zugegriffen werden, bis die Lease abläuft oder unterbrochen wird; und (ii) die zusätzlichen Kosten einer IaaS-VM. 

Eine andere Möglichkeit ist die direkte Verwendung der Seitenblobs über REST-APIs von Azure Storage. Bei dieser Option entfällt die Notwendigkeit teurer IaaS-VMs. Sie bietet die vollständige Flexibilität des direkten Zugriffs von mehreren Clients aus, sie vereinfacht die Bereitstellung mit dem klassischen Bereitstellungsmodell, da die Notwendigkeit entfällt, Datenträger anzuhängen/zu trennen, und sie eliminiert das Risiko des Auftretens von Problemen auf dem virtuellen Computer. Außerdem entspricht das Leistungsniveau bei wahlfreien Lese-/Schreibzugriffen dem bei gleichen Zugriffen auf einen Datenträger.

### <a name="durability-and-high-availability"></a>Dauerhaftigkeit und Hochverfügbarkeit

Sowohl Standardspeicher als auch Storage Premium sind permanente Speicher, in denen die Seitenblobdaten immer repliziert werden, um Dauerhaftigkeit und hohe Verfügbarkeit zu gewährleisten. Weitere Informationen zur Azure Storage-Redundanz finden Sie in dieser [Dokumentation](../common/storage-redundancy.md). Azure konnte für IaaS-Datenträger und Seitenblobs durchgängig eine Dauerhaftigkeit auf Unternehmensniveau bereitstellen, mit einer branchenweit führenden [auf das Jahr umgerechneten Fehlerrate](https://en.wikipedia.org/wiki/Annualized_failure_rate) von null Prozent.

### <a name="seamless-migration-to-azure"></a>Nahtlose Migration zu Azure

Für Kunden und Entwickler, die am Implementieren ihrer eigenen benutzerdefinierten Sicherungslösung interessiert sind, bietet Azure auch inkrementelle Momentaufnahmen, die nur die Deltas enthalten. Dieses Feature vermeidet die Kosten für die erste vollständige Kopie, was die Sicherungskosten erheblich reduziert. Zusammen mit der Fähigkeit, Differenzdaten effizient zu lesen und zu kopieren, ist dies eine weitere leistungsstarke Funktion, die Entwicklern ermöglicht, noch innovativer zu sein, und Sicherung und Notfallwiederherstellung (Disaster Recovery, DR) in Azure mit einer herausragenden Benutzererfahrung verbindet. Sie können Ihre eigene Sicherungs- oder DR-Lösung für Ihre virtuellen Computer in Azure mithilfe von [Blobmomentaufnahmen](/rest/api/storageservices/snapshot-blob) zusammen mit der API zum [Abrufen von Seitenbereichen](/rest/api/storageservices/get-page-ranges) und der API zum [inkrementellen Kopieren von Blobs](/rest/api/storageservices/incremental-copy-blob) einrichten, die Sie zum mühelosen Kopieren der inkrementellen Daten für die Notfallwiederherstellung verwenden können. 

Darüber hinaus werden kritische Workloads vieler Unternehmen bereits in lokalen Rechenzentren ausgeführt. Hauptaspekte bei der Migration der Workload zur Cloud sind die Länge der Ausfallzeit, die beim Kopieren der Daten anfällt, und das Risiko unvorhergesehener Probleme nach dem Wechsel. In vielen Fällen kann die Ausfallzeit für die Migration zur Cloud ein K.O.-Kriterium sein. Die Seitenblob-REST-API von Azure löst dieses Problem, indem sie die Migration kritischer Workloads zur Cloud mit minimaler Unterbrechung ermöglicht. 

Beispiele zum Erstellen einer Momentaufnahme und Wiederherstellen eines Seitenblobs aus einer Momentaufnahme finden Sie im Artikel [Sichern nicht verwalteter Azure-VM-Datenträger mithilfe inkrementeller Momentaufnahmen](../../virtual-machines/windows/incremental-snapshots.md).
