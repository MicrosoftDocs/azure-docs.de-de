---
title: Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag in Azure Import/Export (V1) | Microsoft-Dokumentation
description: Lernen Sie eine exemplarische Vorgehensweise für den vollständigen Prozess der Laufwerkvorbereitung für einen Importauftrag im Azure Import/Export-Dienst kennen.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: b80ba1cbe168270ec591bdd38859408eae387bbf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320583"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag
Dieses Thema führt Sie durch den vollständigen Prozess zur Vorbereitung von Laufwerken für einen Importauftrag.  
  
In diesem Beispiel werden die folgenden Daten in ein Microsoft Azure-Speicherkonto namens `mystorageaccount` importiert:  
  
|Location|BESCHREIBUNG|  
|--------------|-----------------|  
|H:\Video|Eine Sammlung von Videos, insgesamt 5TB.|  
|H:\Photo|Eine Sammlung von Fotos, insgesamt 30GB.|  
|K:\Temp\FavoriteMovie.ISO|Ein Blu-ray™-Datenträgerimage, 25GB.|  
|\\\bigshare\john\music|Eine Sammlung von Musikdateien auf einer Netzwerkfreigabe, insgesamt 10GB.|  
  
Der Importauftrag importiert diese Daten in die folgenden Ziele im Speicherkonto:  
  
|`Source`|Virtuelles Zielverzeichnis oder Blob|  
|------------|-------------------------------------------|  
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|  
  
Mit dieser Zuordnung wird die Datei `H:\Video\Drama\GreatMovie.mov` in das Blob „https:\//mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov“ importiert.  
  
Berechnen Sie nun die Größe der Daten, um zu bestimmen, wie viele Festplatten erforderlich sind:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
In diesem Beispiel sollten zwei 3-TB-Festplatten ausreichen. Da das Quellverzeichnis `H:\Video` jedoch 5 TB an Daten enthält und die Kapazität der einzelnen Festplatte nur 3 TB beträgt, muss `H:\Video` vor dem Ausführen des Microsoft Azure Import/Export-Tools in zwei kleinere Verzeichnisse aufgeteilt werden: `H:\Video1` und `H:\Video2`. Aus diesem Schritt resultieren die folgenden Quellverzeichnisse:  
  
|Location|Größe|Virtuelles Zielverzeichnis oder Blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 TB|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https:\//mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https:\//mystorageaccount.blob.core.windows.net/music|  
  
 Das Verzeichnis `H:\Video` wurde zwar in zwei Verzeichnisse unterteilt, doch beide zeigen auf das gleiche virtuelle Zielverzeichnis im Speicherkonto. Auf diese Weise werden alle Videodateien innerhalb eines einzelnen Containers `video` im Speicherkonto verwaltet.  
  
 Als Nächstes werden die oben genannten Quellverzeichnisse gleichmäßig auf die zwei Festplatten verteilt:  
  
||||  
|-|-|-|  
|Festplatte|Quellverzeichnisse|Gesamtgröße|  
|Erste Festplatte|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Zweite Festplatte|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Darüber hinaus können Sie die folgenden Metadaten für alle Dateien festlegen:  
  
-   **UploadMethod:** Microsoft Azure Import/Export-Dienst  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10.01.2013  
  
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
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
Um diese Eigenschaften festzulegen, erstellen Sie die Textdatei `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Jetzt können Sie das Azure Import/Export-Tool ausführen, um die beiden Festplatten vorzubereiten. Beachten Sie Folgendes:  
  
-   Das erste Laufwerk wird als Laufwerk X bereitgestellt.  
  
-   Das zweite Laufwerk wird als Laufwerk Y bereitgestellt.  
  
-   Der Schlüssel für das Speicherkonto `mystorageaccount` ist `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Vorbereiten des Datenträgers für den Import, wenn Daten vorab geladen werden
 
 Wenn die zu importierenden Daten bereits auf dem Datenträger vorhanden sind, verwenden Sie das Flag „/skipwrite“. Die Werte von „/t“ und „/srcdir“ sollten auf den Datenträger verweisen, der für den Import vorbereitet wird. Wenn nicht alle zu importierenden Daten im gleichen virtuellen Zielverzeichnis bzw. im Stamm des Speicherkontos abgelegt werden, führen Sie den gleichen Befehl für jedes Zielverzeichnis separat aus, und behalten Sie den Wert von „/id“ in allen Ausführungen bei.

>[!NOTE] 
>Geben Sie nicht „/format“ an, weil damit die Daten auf dem Datenträger gelöscht werden. Je nachdem, ob der Datenträger bereits verschlüsselt ist oder nicht, können Sie „/encrypt“ oder „/bk“ angeben. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Kopiersitzungen – Erstes Laufwerk

Führen Sie für das erste Laufwerk das Azure Import/Export-Tool zweimal aus, um die beiden Quellverzeichnisse zu kopieren:  

**Erste Kopiersitzung**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Zweite Kopiersitzung**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Kopiersitzungen – Zweites Laufwerk
 
Führen Sie für das zweite Laufwerk das Azure Import/Export-Tool dreimal aus, einmal für jedes Quellverzeichnis und einmal für die eigenständige Blu-Ray™-Bilddatei:  
  
**Erste Kopiersitzung** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Zweite Kopiersitzung**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Dritte Kopiersitzung**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Abschluss der Kopiersitzungen

Nach Abschluss der Kopiersitzungen können Sie die beiden Laufwerke vom Kopiercomputer trennen und sie an das entsprechende Microsoft Azure-Rechenzentrum senden. Laden Sie beim Erstellen des Importauftrags im [Azure-Portal](https://portal.azure.com) die beiden Journaldateien `FirstDrive.jrn` und `SecondDrive.jrn` hoch.  
  
## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten von Festplatten für einen Importauftrag](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kurzübersicht über häufig verwendete Befehle](../storage-import-export-tool-quick-reference-v1.md) 
