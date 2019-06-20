---
title: Format der Protokolldateien von Azure Import/Export | Microsoft Docs
description: Erfahren Sie mehr über das Format der Protokolldateien, die erstellt werden, wenn Schritte für einen Auftrag des Import/Export-Diensts ausgeführt werden.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 00e226134039d29efd744290c4bc63abd50adc89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478605"
---
# <a name="azure-importexport-service-log-file-format"></a>Format der Protokolldateien des Azure Import/Export-Diensts
Wenn der Microsoft Azure Import/Export-Dienst als Teil eines Import- oder Exportauftrags eine Aktion auf einem Laufwerk durchführt, werden Protokolle in Blockblobs in dem Speicherkonto geschrieben, das dem Auftrag zugeordnet ist.  
  
Es gibt zwei Protokolle, die vom Import/Export-Dienst geschrieben werden können:  
  
-   Das Fehlerprotokoll wird bei einem Fehler immer generiert.  
  
-   Das ausführliche Protokoll ist standardmäßig nicht aktiviert, kann jedoch aktiviert werden, indem Sie die Eigenschaft `EnableVerboseLog` für einen [Put Job](/rest/api/storageimportexport/jobs)- oder [Update Job Properties](/rest/api/storageimportexport/jobs)-Vorgang festlegen.  
  
## <a name="log-file-location"></a>Speicherort der Protokolldatei  
Protokolle werden in Blockblobs in dem Container oder virtuellen Verzeichnis geschrieben, dass mit der Einstellung `ImportExportStatesPath` angegeben wird, die Sie für einen `Put Job`-Vorgang festlegen können. Der Speicherort, in den die Protokolle geschrieben werden, hängt davon ab, wie die Authentifizierung für den Auftrag angegeben ist und welcher Wert für `ImportExportStatesPath` festgelegt wurde. Die Authentifizierung für den Auftrag kann über einen Speicherkontoschlüssel oder eine Container-SAS (Shared Access Signature) angegeben werden.  
  
Der Name des Containers oder des virtuellen Verzeichnisses kann entweder der Standardname `waimportexport` oder der von Ihnen angegebene Name eines anderen Containers oder virtuellen Verzeichnisses sein.  
  
In der folgenden Tabelle sind die möglichen Optionen dargestellt:  
  
|Authentifizierungsmethode|Wert des `ImportExportStatesPath`-Elements|Speicherort des Protokollblobs|  
|---------------------------|----------------------------------------------|---------------------------|  
|Speicherkontoschlüssel|Standardwert|Ein Container namens `waimportexport`, dies ist der Standardcontainer. Beispiel:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Speicherkontoschlüssel|Vom Benutzer angegebener Wert|Ein vom Benutzer benannter Container. Beispiel:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Container-SAS|Standardwert|Ein virtuelles Verzeichnis namens `waimportexport` (der Standardname) unter dem Container, der in der SAS angegeben ist.<br /><br /> Beispiel: Wenn die für den Auftrag angegebene SAS `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue` ist, wäre der Protokollspeicherort `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`.|  
|Container-SAS|Vom Benutzer angegebener Wert|Ein virtuelles vom Benutzer benanntes Verzeichnis unter dem Container, der in der SAS angegeben ist.<br /><br /> Beispiel: Wenn die für den Auftrag angegebene SAS `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue` ist und das angegebene virtuelle Verzeichnis `mylogblobs` heißt, wäre der Protokollspeicherort `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Sie können die URL für das Fehlerprotokoll und das ausführliche Protokoll durch Aufrufen des [Get Job](/rest/api/storageimportexport/jobs)-Vorgangs abrufen. Die Protokolle sind verfügbar, nachdem die Verarbeitung des Laufwerks abgeschlossen wurde.  
  
## <a name="log-file-format"></a>Protokolldateiformat  
Das Format für beide Protokolle ist identisch: ein Blob mit XML-Beschreibungen der Ereignisse, die beim Kopieren von Blobs zwischen der Festplatte und dem Kundenkonto aufgetreten sind.  
  
Das ausführliche Protokoll enthält vollständige Informationen zum Status des Kopiervorgangs für jedes Blob (bei einem Importauftrag) oder jede Datei (bei einem Exportauftrag). Das Fehlerprotokoll enthält dagegen nur die Informationen für Blobs oder Dateien, bei denen während des Import- oder Exportauftrags Fehler aufgetreten sind.  
  
Das Format des ausführlichen Protokolls ist unten dargestellt. Das Fehlerprotokoll hat die gleiche Struktur, allerdings werden erfolgreiche Vorgänge herausgefiltert.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

Die folgende Tabelle beschreibt die Elemente der Protokolldatei.  
  
|XML-Element|Type|BESCHREIBUNG|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML-Element|Stellt ein Laufwerkprotokoll dar.|  
|`Version`|Attribut, String|Die Version des Protokollformats.|  
|`DriveId`|string|Der Hardwareseriennummer des Laufwerks.|  
|`Status`|string|Status der Laufwerksverarbeitung. Weitere Informationen finden Sie weiter unten in der Tabelle `Drive Status Codes`.|  
|`Blob`|Geschachteltes XML-Element|Stellt ein Blob dar.|  
|`Blob/BlobPath`|string|Der URI des Blobs.|  
|`Blob/FilePath`|string|Der relative Pfad zur Datei auf dem Laufwerk.|  
|`Blob/Snapshot`|DateTime|Die Momentaufnahmeversion des Blobs (nur für einen Exportauftrag).|  
|`Blob/Length`|Integer|Die Gesamtlänge des Blobs in Bytes.|  
|`Blob/LastModified`|DateTime|Datum/Uhrzeit der letzten Änderung des Blobs (nur für einen Exportauftrag).|  
|`Blob/ImportDisposition`|string|Die Importdisposition des Blobs (nur für einen Importauftrag).|  
|`Blob/ImportDisposition/@Status`|Attribut, String|Der Status der Importdisposition.|  
|`PageRangeList`|Geschachteltes XML-Element|Stellt eine Liste von Seitenbereichen für ein Seitenblob dar.|  
|`PageRange`|XML-Element|Stellt einen Seitenbereich dar.|  
|`PageRange/@Offset`|Attribut, Integer|Der Startoffset des Seitenbereichs im Blob.|  
|`PageRange/@Length`|Attribut, ganze Zahl|Länge des Seitenbereichs in Bytes.|  
|`PageRange/@Hash`|Attribut, String|Base16-codierter MD5-Hash des Seitenbereichs.|  
|`PageRange/@Status`|Attribut, String|Status der Verarbeitung des Seitenbereichs.|  
|`BlockList`|Geschachteltes XML-Element|Stellt eine Liste von Blöcken für ein Blockblob dar.|  
|`Block`|XML-Element|Stellt einen Block dar.|  
|`Block/@Offset`|Attribut, Integer|Der Startoffset des Blocks im Blob.|  
|`Block/@Length`|Attribut, Integer|Länge des Blocks in Bytes.|  
|`Block/@Id`|Attribut, String|Die Block-ID.|  
|`Block/@Hash`|Attribut, String|Base16-codierter MD5-Hash des Blocks.|  
|`Block/@Status`|Attribut, String|Status der Verarbeitung des Blocks.|  
|`Metadata`|Geschachteltes XML-Element|Stellt die Metadaten des Blobs dar.|  
|`Metadata/@Status`|Attribut, Zeichenfolge|Status der Verarbeitung der Blobmetadaten.|  
|`Metadata/GlobalPath`|string|Relativer Pfad zur globalen Metadatendatei.|  
|`Metadata/GlobalPath/@Hash`|Attribut, String|Base16-codierter MD5-Hash der globalen Metadatendatei.|  
|`Metadata/Path`|string|Relativer Pfad zur Metadatendatei.|  
|`Metadata/Path/@Hash`|Attribut, String|Base16-codierter MD5-Hash der Metadatendatei.|  
|`Properties`|Geschachteltes XML-Element|Stellt die Blobeigenschaften dar.|  
|`Properties/@Status`|Attribut, String|Status der Verarbeitung der Blobeigenschaften, z.B. Datei wurde nicht gefunden, abgeschlossen.|  
|`Properties/GlobalPath`|string|Relativer Pfad zur globalen Eigenschaftendatei.|  
|`Properties/GlobalPath/@Hash`|Attribut, String|Base16-codierter MD5-Hash der globalen Eigenschaftendatei.|  
|`Properties/Path`|string|Relativer Pfad zur Eigenschaftendatei.|  
|`Properties/Path/@Hash`|Attribut, String|Base16-codierter MD5-Hash der Eigenschaftendatei.|  
|`Blob/Status`|string|Status der Verarbeitung des Blobs.|  
  
## <a name="drive-status-codes"></a>Statuscodes für Laufwerke  
In der folgenden Tabelle sind die Statuscodes für die Verarbeitung eines Laufwerks aufgeführt.  
  
|Statuscode|BESCHREIBUNG|  
|-----------------|-----------------|  
|`Completed`|Die Verarbeitung des Laufwerks wurde ohne Fehler abgeschlossen.|  
|`CompletedWithWarnings`|Die Verarbeitung des Laufwerks wurde mit Warnungen in einem oder mehreren Blobs abgeschlossen, gemäß den für die Blobs angegebenen Importdispositionen.|  
|`CompletedWithErrors`|Das Laufwerk wurde mit Fehlern in einem oder mehreren Blobs oder Abschnitten abgeschlossen.|  
|`DiskNotFound`|Auf dem Laufwerk wurde kein Datenträger gefunden.|  
|`VolumeNotNtfs`|Das erste Datenvolume auf dem Datenträger hat nicht das NTFS-Format.|  
|`DiskOperationFailed`|Ein unbekannter Fehler ist beim Ausführen von Vorgängen auf dem Laufwerk aufgetreten.|  
|`BitLockerVolumeNotFound`|Es wurde kein mit BitLocker verschlüsselbares Volume gefunden.|  
|`BitLockerNotActivated`|BitLocker ist auf dem Volume nicht aktiviert.|  
|`BitLockerProtectorNotFound`|Die numerische Kennwortschlüssel-Schutzvorrichtung ist auf dem Volume nicht vorhanden.|  
|`BitLockerKeyInvalid`|Das bereitgestellte numerische Kennwort kann das Volume nicht entsperren.|  
|`BitLockerUnlockVolumeFailed`|Ein unbekannter Fehler ist beim Versuch, das Volume zu entsperren, aufgetreten.|  
|`BitLockerFailed`|Ein unbekannter Fehler ist beim Ausführen von BitLocker-Vorgängen aufgetreten.|  
|`ManifestNameInvalid`|Der Name der Manifestdatei ist ungültig.|  
|`ManifestNameTooLong`|Der Name der Manifestdatei ist zu lang.|  
|`ManifestNotFound`|Die Manifestdatei wurde nicht gefunden.|  
|`ManifestAccessDenied`|Zugriff auf die Manifestdatei wurde verweigert.|  
|`ManifestCorrupted`|Die Manifestdatei ist beschädigt (der Inhalt stimmt nicht mit dem Hash überein).|  
|`ManifestFormatInvalid`|Der Inhalt des Manifests entspricht nicht dem erforderlichen Format.|  
|`ManifestDriveIdMismatch`|Die Laufwerks-ID in der Manifestdatei stimmt nicht mit der auf dem Laufwerk gelesenen ID überein.|  
|`ReadManifestFailed`|Beim Lesen im Manifest ist ein Datenträger-E/A-Fehler aufgetreten.|  
|`BlobListFormatInvalid`|Das Listenblob des Exportblobs entspricht nicht dem erforderlichen Format.|  
|`BlobRequestForbidden`|Zugriff auf die Blobs im Speicherkonto ist nicht zulässig. Die Ursache kann ein ungültiger Speicherkontoschlüssel oder eine ungültige Container-SAS sein.|  
|`InternalError`|Beim Verarbeiten des Laufwerks ist ein interner Fehler aufgetreten.|  
  
## <a name="blob-status-codes"></a>Statuscodes für Blobs  
In der folgenden Tabelle sind die Statuscodes für die Verarbeitung eines Blobs aufgeführt.  
  
|Statuscode|BESCHREIBUNG|  
|-----------------|-----------------|  
|`Completed`|Die Verarbeitung des Blobs wurde ohne Fehler abgeschlossen.|  
|`CompletedWithErrors`|Die Verarbeitung des Blobs wurde mit Fehlern in einem oder mehren Seitenbereichen oder Blöcken, Metadaten oder Eigenschaften abgeschlossen.|  
|`FileNameInvalid`|Der Dateiname ist ungültig.|  
|`FileNameTooLong`|Der Dateiname ist zu lang.|  
|`FileNotFound`|Die Datei wurde nicht gefunden.|  
|`FileAccessDenied`|Zugriff auf die Datei wurde verweigert.|  
|`BlobRequestFailed`|Die Blob-Dienstanforderung für den Zugriff auf das Blob ist fehlgeschlagen.|  
|`BlobRequestForbidden`|Die Blob-Dienstanforderung für den Zugriff auf das Blob ist nicht zulässig. Die Ursache kann ein ungültiger Speicherkontoschlüssel oder eine ungültige Container-SAS sein.|  
|`RenameFailed`|Beim Umbenennen des Blobs (für einen Importauftrag) oder der Datei (für einen Exportauftrag) ist ein Fehler aufgetreten.|  
|`BlobUnexpectedChange`|Im Blob (für einen Exportauftrag) ist eine unerwartete Änderung aufgetreten.|  
|`LeasePresent`|Eine Lease ist für das Blob vorhanden.|  
|`IOFailed`|Beim Verarbeiten des Blobs ist ein Datenträger- oder Netzwerk-E/A-Fehler aufgetreten.|  
|`Failed`|Beim Verarbeiten des Blobs ist ein unbekannter Fehler aufgetreten.|  
  
## <a name="import-disposition-status-codes"></a>Statuscodes für Importdispositionen  
In der folgenden Tabelle sind die Statuscodes zum Auflösen einer Importdisposition aufgeführt.  
  
|Statuscode|BESCHREIBUNG|  
|-----------------|-----------------|  
|`Created`|Das Blob wurde erstellt.|  
|`Renamed`|Das Blob wurde gemäß der entsprechenden Importdisposition umbenannt. Das `Blob/BlobPath`-Element enthält den URI für das umbenannte Blob.|  
|`Skipped`|Das Blob wurde gemäß `no-overwrite`-Importdisposition übersprungen.|  
|`Overwritten`|Das Blob hat gemäß `overwrite`-Importdisposition ein vorhandenes Blob überschrieben.|  
|`Cancelled`|Ein vorheriger Fehler hat die weitere Verarbeitung der Importdisposition beendet.|  
  
## <a name="page-rangeblock-status-codes"></a>Statuscodes für Seitenbereiche/Blöcke  
In der folgenden Tabelle sind die Statuscodes für die Verarbeitung eines Seitenbereichs oder Blocks aufgeführt.  
  
|Statuscode|BESCHREIBUNG|  
|-----------------|-----------------|  
|`Completed`|Die Verarbeitung des Seitenbereichs oder Blocks wurde ohne Fehler abgeschlossen.|  
|`Committed`|Für den Block wurde ein Commit ausgeführt, jedoch nicht in der vollständigen Blockliste, weil für andere Blöcke Fehler aufgetreten sind oder die Übergabe der vollständigen Blockliste selbst fehlgeschlagen ist.|  
|`Uncommitted`|Der Block wird hochgeladen, aber es wird kein Commit ausgeführt.|  
|`Corrupted`|Der Seitenbereich oder Block ist beschädigt (der Inhalt stimmt nicht mit dem Hash überein).|  
|`FileUnexpectedEnd`|Ein unerwartetes Dateiende wurde festgestellt.|  
|`BlobUnexpectedEnd`|Ein unerwartetes Blobende wurde festgestellt.|  
|`BlobRequestFailed`|Die Blob-Dienstanforderung für den Zugriff auf den Seitenbereich oder Block ist fehlgeschlagen.|  
|`IOFailed`|Beim Verarbeiten des Seitenbereichs oder Blocks ist ein Datenträger- oder Netzwerk-E/A-Fehler aufgetreten.|  
|`Failed`|Beim Verarbeiten des Seitenbereichs oder Blocks ist ein unbekannter Fehler aufgetreten.|  
|`Cancelled`|Ein vorheriger Fehler hat die weitere Verarbeitung des Seitenbereichs oder Blocks beendet.|  
  
## <a name="metadata-status-codes"></a>Statuscodes für Metadaten  
In der folgenden Tabelle sind die Statuscodes für die Verarbeitung von Blobmetadaten aufgeführt.  
  
|Statuscode|BESCHREIBUNG|  
|-----------------|-----------------|  
|`Completed`|Die Verarbeitung der Metadaten wurde ohne Fehler abgeschlossen.|  
|`FileNameInvalid`|Der Name der Metadatendatei ist ungültig.|  
|`FileNameTooLong`|Der Name der Metadatendatei ist zu lang.|  
|`FileNotFound`|Die Metadatendatei wurde nicht gefunden.|  
|`FileAccessDenied`|Zugriff auf die Metadatendatei wurde verweigert.|  
|`Corrupted`|Die Metadatendatei ist beschädigt (der Inhalt stimmt nicht mit dem Hash überein).|  
|`XmlReadFailed`|Der Metadateninhalt entspricht nicht dem erforderlichen Format.|  
|`XmlWriteFailed`|Das Schreiben der Metadaten-XML ist fehlgeschlagen.|  
|`BlobRequestFailed`|Die Blob-Dienstanforderung für den Zugriff auf die Metadaten ist fehlgeschlagen.|  
|`IOFailed`|Beim Verarbeiten der Metadaten ist ein Datenträger- oder Netzwerk-E/A-Fehler aufgetreten.|  
|`Failed`|Beim Verarbeiten der Metadaten ist ein unbekannter Fehler aufgetreten.|  
|`Cancelled`|Ein vorheriger Fehler hat die weitere Verarbeitung der Metadaten beendet.|  
  
## <a name="properties-status-codes"></a>Statuscodes für Eigenschaften  
In der folgenden Tabelle sind die Statuscodes für die Verarbeitung von Blobeigenschaften aufgeführt.  
  
|Statuscode|BESCHREIBUNG|  
|-----------------|-----------------|  
|`Completed`|Die Verarbeitung der Eigenschaften wurde ohne Fehler abgeschlossen.|  
|`FileNameInvalid`|Der Name der Eigenschaftendatei ist ungültig.|  
|`FileNameTooLong`|Der Name der Eigenschaftendatei ist zu lang.|  
|`FileNotFound`|Die Eigenschaftendatei wurde nicht gefunden.|  
|`FileAccessDenied`|Zugriff auf die Eigenschaftendatei wurde verweigert.|  
|`Corrupted`|Die Eigenschaftendatei ist beschädigt (der Inhalt stimmt nicht mit dem Hash überein).|  
|`XmlReadFailed`|Der Inhalt der Eigenschaften entspricht nicht dem erforderlichen Format.|  
|`XmlWriteFailed`|Das Schreiben der Eigenschaften-XML ist fehlgeschlagen.|  
|`BlobRequestFailed`|Die Blob-Dienstanforderung für den Zugriff auf die Eigenschaften ist fehlgeschlagen.|  
|`IOFailed`|Beim Verarbeiten der Eigenschaften ist ein Datenträger- oder Netzwerk-E/A-Fehler aufgetreten.|  
|`Failed`|Beim Verarbeiten der Eigenschaften ist ein unbekannter Fehler aufgetreten.|  
|`Cancelled`|Ein vorheriger Fehler hat die weitere Verarbeitung der Eigenschaften beendet.|  
  
## <a name="sample-logs"></a>Beispielprotokolle  
Im Folgenden finden Sie ein Beispiel für das ausführliche Protokoll.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Das entsprechende Fehlerprotokoll ist unten dargestellt.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 Das folgende Fehlerprotokoll für einen Importauftrag enthält einen Fehler aufgrund einer Datei, die auf dem Importlaufwerk nicht gefunden wurde. Beachten Sie, dass der Status der nachfolgenden Komponenten `Cancelled` ist.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

Das folgende Fehlerprotokoll für einen Exportauftrag gibt an, dass der Blobinhalt erfolgreich auf das Laufwerk geschrieben wurde, aber beim Exportieren der Eigenschaften des Blobs ist ein Fehler aufgetreten.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Nächste Schritte
 
* [Storage Import/Export-REST-API](/rest/api/storageimportexport/)
