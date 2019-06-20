---
title: Format der Manifestdateien von Azure Import/Export | Microsoft Docs
description: Hier finden Sie Informationen zum Format der Laufwerkmanifestdatei, die in einem Import- oder Exportauftrag im Import/Export-Dienst die Zuordnung zwischen Blobs in Azure Blob Storage und Dateien auf einem Laufwerk beschreibt.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ee53cc3a639a79e1b29ac6cd537bfb04e05b1bca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478622"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Format der Manifestdateien des Azure Import/Export-Diensts
Die Laufwerksmanifestdatei beschreibt die Zuordnung zwischen Blobs in Azure Blob Storage und Dateien auf dem Laufwerk, aus denen ein Import- oder Exportauftrag besteht. Für einen Importvorgang wird die Manifestdatei im Rahmen des Prozesses der Laufwerksvorbereitung erstellt und auf dem Laufwerk gespeichert, bevor dieses an das Azure-Rechenzentrum gesendet wird. Während eines Exportvorgangs wird das Manifest vom Azure-Import-/Exportdienst erstellt und auf dem Laufwerk gespeichert.  
  
Sowohl für Import- als auch für Exportaufträge wird die Laufwerksmanifestdatei auf dem Import- oder Exportlaufwerk gespeichert. Sie wird nicht über einen API-Vorgang an den Dienst übertragen.  
  
Im Folgenden wird das allgemeine Format einer Laufwerksmanifestdatei beschrieben:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Elemente und Attribute des Manifest-XML-Formats

Die Datenelemente und Attribute des XML-Formats des Laufwerksmanifests sind in der folgenden Tabelle aufgeführt.  
  
|XML-Element|Type|BESCHREIBUNG|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Stammelement|Das Stammelement der Manifestdatei. Alle anderen Elemente in der Datei sind diesem Element untergeordnet.|  
|`Version`|Attribut, String|Die Version der Manifestdatei.|  
|`Drive`|Geschachteltes XML-Element|Enthält das Manifest für jedes Laufwerk.|  
|`DriveId`|string|Der eindeutige Laufwerksbezeichner für das Laufwerk. Der Laufwerksbezeichner lässt sich durch Abfrage des Laufwerks nach der Seriennummer ermitteln. Die Seriennummer des Laufwerks ist üblicherweise auch auf der Außenseite des Laufwerks zu finden. Das `DriveID`-Element muss vor allen `BlobList`-Elementen in der Manifestdatei angezeigt werden.|  
|`StorageAccountKey`|string|Ist für Importaufträge erforderlich und nur dann, wenn `ContainerSas` nicht angegeben ist. Der Kontoschlüssel für das Azure-Speicherkonto, das dem Auftrag zugeordnet ist.<br /><br /> Dieses Element wird bei Exportvorgängen vom Manifest ausgelassen.|  
|`ContainerSas`|string|Ist für Importaufträge erforderlich und nur dann, wenn `StorageAccountKey` nicht angegeben ist. Die Container-SAS für den Zugriff auf die Blobs, die dem Auftrag zugeordnet sind. Informationen finden Sie unter [Put Job](/rest/api/storageimportexport/jobs). Dieses Element wird bei Exportvorgängen vom Manifest ausgelassen.|  
|`ClientCreator`|string|Gibt den Client an, der die XML-Datei erstellt hat. Dieser Wert wird vom Import-/Exportdienst nicht interpretiert.|  
|`BlobList`|Geschachteltes XML-Element|Enthält eine Liste von Blobs, die zum Import- oder Exportauftrag gehören. Alle Blobs in einer Blobliste verwenden die gleichen Metadaten und Eigenschaften.|  
|`BlobList/MetadataPath`|string|Optional. Gibt auf dem Datenträger den relativen Pfad zu der Datei mit den Standardmetadaten an, die für die Blobs in der Blobliste für einen Importvorgang festgelegt werden. Diese Metadaten können optional für jedes einzelne Blob überschrieben werden.<br /><br /> Dieses Element wird bei Exportvorgängen vom Manifest ausgelassen.|  
|`BlobList/MetadataPath/@Hash`|Attribut, String|Gibt den Base16-codierten MD5-Hashwert für die Metadatendatei an.|  
|`BlobList/PropertiesPath`|string|Optional. Gibt auf dem Datenträger den relativen Pfad zu der Datei mit den Standardeigenschaften an, die für die Blobs in der Blobliste für einen Importvorgang festgelegt werden. Diese Eigenschaften können optional für jedes einzelne Blob überschrieben werden.<br /><br /> Dieses Element wird bei Exportvorgängen vom Manifest ausgelassen.|  
|`BlobList/PropertiesPath/@Hash`|Attribut, String|Gibt den Base16-codierten MD5-Hashwert für die Eigenschaftendatei an.|  
|`Blob`|Geschachteltes XML-Element|Enthält Informationen zu jedem Blob in allen Bloblisten.|  
|`Blob/BlobPath`|string|Der relative URI zum Blob, beginnend mit dem Containernamen. Wenn sich das Blob im Stammcontainer befindet, muss es mit `$root` beginnen.|  
|`Blob/FilePath`|string|Gibt den relativen Pfad zu der Datei auf dem Laufwerk an. Bei Exportaufträgen wird nach Möglichkeit der Blobpfad als Dateipfad verwendet, *z.B.* wird `pictures/bob/wild/desert.jpg` nach `\pictures\bob\wild\desert.jpg` exportiert. Aufgrund der Beschränkungen von NTFS-Namen kann ein Blob jedoch mit einem Pfad in eine Datei exportiert werden, der nicht dem Blobpfad entspricht.|  
|`Blob/ClientData`|string|Optional. Enthält Kommentare vom Kunden. Dieser Wert wird vom Import-/Exportdienst nicht interpretiert.|  
|`Blob/Snapshot`|DateTime|Optional für Exportaufträge. Gibt den Momentaufnahmebezeichner für eine exportierte Blobmomentaufnahme an.|  
|`Blob/Length`|Integer|Gibt die Gesamtlänge des Blobs in Bytes an. Der Wert kann bis zu 200 GB für ein Blockblob und bis zu 1 TB für ein Seitenblob betragen. Bei einem Seitenblob muss dieser Wert ein Vielfaches von 512 sein.|  
|`Blob/ImportDisposition`|string|Optional für Importaufträge, ausgelassen für Exportaufträge. Dieser Wert gibt an, wie der Import-/Exportdienst bei einem Importauftrag vorgehen soll, in dem bereits ein Blob mit dem gleichen Namen vorhanden ist. Wenn dieser Wert für das Importmanifest ausgelassen wird, lautet der Standardwert `rename`.<br /><br /> Die Werte für dieses Element umfassen Folgendes:<br /><br /> -   `no-overwrite`: Wenn bereits ein Zielblob mit dem gleichen Namen vorhanden ist, überspringt der Importvorgang den Import dieser Datei.<br />-   `overwrite`: Alle vorhandenen Zielblobs werden durch die neu importierte Datei vollständig überschrieben.<br />-   `rename`: Das neue Blob wird mit geändertem Namen hochgeladen.<br /><br /> Die Umbenennungsregel lautet wie folgt:<br /><br /> – Wenn der Blobname keinen Punkt enthält, wird ein durch Anfügen von `(2)` an den ursprünglichen Blobnamen ein neuer Name generiert. Wenn dieser neue Name ebenfalls einen Konflikt mit einem vorhandenen Blobnamen verursacht, wird `(3)` statt `(2)` angefügt usw.<br />– Wenn der Blobname einen Punkt enthält, wird der Teil des Namens nach dem letzten Punkt als Name der Erweiterung betrachtet. Ähnlich wie beim Vorgehen oben wird `(2)` vor den letzten Punkt eingefügt, um einen neuen Namen zu generieren. Wenn der neue Name weiterhin einen Konflikt mit einem vorhandenen Blobnamen verursacht, versucht der Dienst `(3)`, `(4)` usw., bis ein Name gefunden wird, der keinen Konflikt verursacht.<br /><br /> Hier einige Beispiele:<br /><br /> Das Blob `BlobNameWithoutDot` wird umbenannt in:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Das Blob `Seattle.jpg` wird umbenannt in:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Geschachteltes XML-Element|Für ein Seitenblob erforderlich.<br /><br /> Gibt bei einem Importvorgang eine Liste der Bytebereiche einer zu importierenden Datei an. Jeder Seitenbereich wird in der Quelldatei durch einen Offset und eine Länge sowie durch einen MD5-Hash der Region beschrieben. Das `Hash`-Attribut eines Seitenbereichs ist erforderlich. Der Dienst überprüft, ob der Hash der Daten im Blob mit dem berechneten MD5-Hash aus dem Seitenbereich übereinstimmt. Zum Beschreiben einer Datei für einen Import kann eine beliebige Anzahl von Seitenbereichen mit einer Gesamtgröße von bis zu 1 TB verwendet werden. Alle Seitenbereiche müssen nach Offset sortiert sein, Überschneidungen sind nicht zulässig.<br /><br /> Für einen Exportvorgang gibt dieses Element einen Satz von Bytebereichen eines Blobs an, das auf das Laufwerk exportiert wurde.<br /><br /> Alle Seitenbereiche zusammen decken möglicherweise nur Teilbereiche eines Blobs oder einer Datei ab.  Der verbleibende, durch keinen Seitenbereich abgedeckte Teil der Datei wird erwartet, und der Inhalte kann undefiniert sein.|  
|`PageRange`|XML-Element|Stellt einen Seitenbereich dar.|  
|`PageRange/@Offset`|Attribut, Integer|Gibt den Offset in der Übertragungsdatei und im Blob an, an dem der angegebene Seitenbereich beginnt. Dieser Wert muss ein Vielfaches von 512 sein.|  
|`PageRange/@Length`|Attribut, Integer|Gibt die Länge des Seitenbereichs an. Dieser Wert muss ein Vielfaches von 512 sein und darf 4 MB nicht überschreiten.|  
|`PageRange/@Hash`|Attribut, String|Gibt den Base16-codierten MD5-Hashwert für den Seitenbereich an.|  
|`BlockList`|Geschachteltes XML-Element|Erforderlich für einen Blockblob mit benannten Blöcken.<br /><br /> Bei einem Importvorgang gibt die Blockliste einen Satz von Blöcken an, die in Azure Storage importiert werden. Bei einem Exportvorgang gibt die Blockliste an, wo jeder Block in der Datei auf dem Exportdatenträger gespeichert war. Jeder Block wird durch einen Offset in der Datei und eine Blocklänge beschrieben. Darüber hinaus wird jeder Block durch ein Block-ID-Attribut benannt und enthält einen MD5-Hash für den Block. Zum Beschreiben eines Blobs können bis zu 50.000 Blöcke verwendet werden.  Alle Blöcke müssen nach Offset sortiert sein und zusammen den gesamten Bereich der Datei abdecken, *d.h.* , zwischen den Blöcken dürfen keine Lücken vorhanden sein. Wenn das Blob nicht mehr als 64 MB umfasst, müssen alle Block-IDs für jeden Block entweder vollständig vorhanden oder nicht vorhanden sein. Block-IDs müssen Base64-codierte Zeichenfolgen sein. Weitere Anforderungen für Block-IDs finden Sie unter [Put Block](/rest/api/storageservices/put-block).|  
|`Block`|XML-Element|Stellt einen Block dar.|  
|`Block/@Offset`|Attribut, Integer|Gibt den Offset an, an dem der angegebene Block beginnt.|  
|`Block/@Length`|Attribut, Integer|Gibt die Anzahl von Bytes im Block an. Dieser Wert darf 4 MB nicht überschreiten.|  
|`Block/@Id`|Attribut, String|Gibt eine Zeichenfolge an, die die Block-ID für den Block darstellt.|  
|`Block/@Hash`|Attribut, String|Gibt den Base16-codierten MD5-Hash des Blocks an.|  
|`Blob/MetadataPath`|string|Optional. Gibt den relativen Pfad einer Metadatendatei an. Während eines Importvorgangs sind die Metadaten auf das Zielblob festgelegt. Während eines Exportvorgangs werden die Metadaten des Blobs in der Metadatendatei auf dem Laufwerk gespeichert.|  
|`Blob/MetadataPath/@Hash`|Attribut, String|Gibt den Base16-codierten MD5-Hash der Metadatendatei des Blobs an.|  
|`Blob/PropertiesPath`|string|Optional. Gibt den relativen Pfad einer Eigenschaftendatei an. Während eines Importvorgangs sind die Eigenschaften auf das Zielblob festgelegt. Während eines Exportvorgangs werden die Eigenschaften des Blobs in der Eigenschaftendatei auf dem Laufwerk gespeichert.|  
|`Blob/PropertiesPath/@Hash`|Attribut, String|Gibt den Base16-codierten MD5-Hash der Eigenschaftendatei des Blobs an.|  
  
## <a name="next-steps"></a>Nächste Schritte
 
* [Storage Import/Export-REST-API](/rest/api/storageimportexport/)
