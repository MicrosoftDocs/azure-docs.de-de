---
title: Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag in Azure Import/Export | Microsoft-Dokumentation
description: Lernen Sie eine exemplarische Vorgehensweise für den vollständigen Prozess der Laufwerkvorbereitung für einen Importauftrag im Azure Import/Export-Dienst kennen.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 42da285fbb55df43959506996bcde9cf547c2a22
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320566"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag

Dieser Artikel führt Sie durch den vollständigen Prozess zur Vorbereitung von Laufwerken für einen Importauftrag.

## <a name="sample-data"></a>Beispieldaten

In diesem Beispiel werden die folgenden Daten in ein Azure-Speicherkonto namens `mystorageaccount` importiert:

|Location|BESCHREIBUNG|Datengröße|
|--------------|-----------------|-----|
|H:\Video\ |Eine Sammlung von Videos|12TB|
|H:\Photo\ |Eine Sammlung von Fotos|30 GB|
|K:\Temp\FavoriteMovie.ISO|Ein Blu-ray™-Datenträgerimage|25 GB|
|\\\bigshare\john\music\ |Eine Sammlung von Musikdateien auf einer Netzwerkfreigabe|10 GB|

## <a name="storage-account-destinations"></a>Speicherkontoziele

Der Importauftrag importiert diese Daten in die folgenden Ziele im Speicherkonto:

|`Source`|Virtuelles Zielverzeichnis oder Blob|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |photo/|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |music|

Mit dieser Zuordnung wird die Datei `H:\Video\Drama\GreatMovie.mov` in das Blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov` importiert.

## <a name="determine-hard-drive-requirements"></a>Ermitteln der Festplattenanforderungen

Berechnen Sie nun die Größe der Daten, um zu bestimmen, wie viele Festplatten erforderlich sind:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

In diesem Beispiel sollten zwei 8-TB-Festplatten ausreichen. Da das Quellverzeichnis `H:\Video` jedoch 12 TB Daten aufweist, und die Kapazität der einzelnen Festplatte nur 8 TB beträgt, können Sie dies folgendermaßen in der Datei **driveset.csv** angeben:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Das Tool verteilt die Daten optimiert auf zwei Festplatten.

## <a name="attach-drives-and-configure-the-job"></a>Anfügen von Laufwerken und Konfigurieren des Auftrags
Sie fügen beide Festplatten dem Computer an und erstellen Volumes. Dann erstellen Sie die Datei **dataset.csv**:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Darüber hinaus können Sie die folgenden Metadaten für alle Dateien festlegen:

* **UploadMethod:** Microsoft Azure Import/Export-Dienst
* **DataSetName:** SampleData
* **CreationDate:** 10.01.2013

Um Metadaten für die importierten Dateien festzulegen, erstellen Sie die Textdatei `c:\WAImportExport\SampleMetadata.txt` mit folgendem Inhalt:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Sie können auch bestimmte Eigenschaften für das Blob `FavoriteMovie.ISO` festlegen:

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

Um diese Eigenschaften festzulegen, erstellen Sie die Textdatei `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Ausführen des Azure Import/Export-Tools (WAImportExport.exe)

Jetzt können Sie das Azure Import/Export-Tool ausführen, um die beiden Festplatten vorzubereiten.

**Für die erste Sitzung:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Wenn weiteren Daten hinzugefügt werden müssen, erstellen Sie eine andere Datasetdatei (mit dem Format des anfänglichen Datasets).

**Für die zweite Sitzung:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Nach Abschluss der Kopiersitzungen können Sie die beiden Laufwerke vom Kopiercomputer trennen und sie an das entsprechende Azure-Rechenzentrum senden. Bei der Erstellung des Importauftrags im Azure-Portal laden Sie die beiden Journaldateien `<FirstDriveSerialNumber>.xml` und `<SecondDriveSerialNumber>.xml` hoch.

## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten von Festplatten für einen Importauftrag](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Kurzübersicht über häufig verwendete Befehle](../storage-import-export-tool-quick-reference.md)
