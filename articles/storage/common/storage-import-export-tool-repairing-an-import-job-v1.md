---
title: Reparieren eines Importauftrags in Azure Import/Export (V1) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Importauftrag reparieren, der mithilfe des Azure Import/Export-Diensts erstellt und ausgeführt wurde.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fda1d3d626c91ba984f08b96c79ab6a2fd2ec74b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477585"
---
# <a name="repairing-an-import-job"></a>Reparieren eines Importauftrags
Der Microsoft Azure Import/Export-Dienst kann möglicherweise einige Ihrer Dateien oder Teile einer Datei nicht in den Windows Azure-Blobdienst kopieren. Mögliche Fehlerursachen sind:  
  
-   Beschädigte Dateien  
  
-   Beschädigte Laufwerke  
  
-   Der Speicherkontoschlüssel wurde während der Übertragung der Datei geändert.  
  
Sie können das Microsoft Azure Import/Export-Tool mit den Kopierprotokolldateien des Importauftrags ausführen, und das Tool lädt die fehlenden Dateien (oder fehlenden Dateiteile) in Ihr Microsoft Azure Storage-Konto hoch, um den Importauftrag abzuschließen.  
  
## <a name="repairimport-parameters"></a>RepairImport-Parameter

Die folgenden Parameter können mit **RepairImport** angegeben werden: 
  
|||  
|-|-|  
|**/r:** &lt;Reparaturdatei\>|**Erforderlich.** Pfad zur Reparaturdatei, die den Status der Reparatur verfolgt und das Fortsetzen einer unterbrochenen Reparatur ermöglicht. Jedes Laufwerk muss über genau eine Reparaturdatei verfügen. Wenn Sie mit der Reparatur eines bestimmten Laufwerks beginnen, übergeben Sie den Pfad zu einer noch nicht vorhandenen Reparaturdatei. Zum Fortsetzen einer unterbrochenen Reparatur müssen Sie den Namen einer vorhandenen Reparaturdatei übergeben. Die Reparaturdatei für das Ziellaufwerk muss immer angegeben werden.|  
|**/logdir:** &lt;Protokollverzeichnis\>|**Optional.** Das Protokollverzeichnis. In dieses Verzeichnis werden ausführliche Protokolldateien geschrieben. Wird kein Protokollverzeichnis angegeben, wird das aktuelle Verzeichnis als Protokollverzeichnis verwendet.|  
|**/d:** &lt;Zielverzeichnisse\>|**Erforderlich.** Eine oder mehrere, durch Semikolons getrennte Verzeichnisse, die die ursprünglichen Dateien enthalten, die importiert wurden. Das Importlaufwerk kann auch verwendet werden, wird aber nicht benötigt, wenn alternative Speicherorte der ursprünglichen Dateien verfügbar sind.|  
|**/bk:** &lt;BitLocker-Schlüssel\>|**Optional.** Sie müssen den BitLocker-Schlüssel angeben, wenn das Tool ein verschlüsseltes Laufwerk entsperren soll, auf dem die ursprünglichen Dateien verfügbar sind.|  
|**/sn:** &lt;Speicherkontoname\>|**Erforderlich.** Der Name des Speicherkontos für den Importauftrag.|  
|**/sk:** &lt;Speicherkontoschlüssel\>|Nur **Erforderliche**, wenn keine Container-SAS angegeben wurde. Der Kontoschlüssel des Speicherkontos für den Importauftrag.|  
|**/csas:** &lt;Container-SAS\>|Nur **Erforderliche**, wenn kein Speicherkontoschlüssel angegeben wurde. Die Container-SAS für den Zugriff auf die Blobs, die dem Importauftrag zugeordnet sind.|  
|**/CopyLogFile:** &lt;Laufwerk-Kopierprotokolldatei\>|**Erforderlich.** Pfad zur Laufwerk-Kopierprotokolldatei (entweder ausführliches Protokoll oder Fehlerprotokoll). Die Datei wird vom Windows Azure Import/Export-Dienst generiert und kann aus dem Blobspeicher heruntergeladen werden, der dem Auftrag zugeordnet ist. Die Kopierprotokolldatei enthält Informationen zu fehlerhaften Blobs oder Dateien, die repariert werden müssen.|  
|**/PathMapFile:** &lt;Laufwerkpfad-Zuordnungsdatei\>|**Optional.** Pfad zu einer Textdatei, mit deren Hilfe Mehrdeutigkeiten aufgelöst werden können, wenn im selben Auftrag mehrere Dateien mit dem gleichen Namen importiert werden. Beim ersten Ausführen des Tools kann diese Datei mit allen mehrdeutigen Namen gefüllt werden. Bei nachfolgenden Ausführungen des Tools wird diese Datei verwendet, um die Mehrdeutigkeiten aufzulösen.|  
  
## <a name="using-the-repairimport-command"></a>Verwenden des RepairImport-Befehls  
Um Importdaten durch das Streamen der Daten über das Netzwerk zu reparieren, müssen Sie die Verzeichnisse angeben, die die ursprünglichen Dateien enthalten, die Sie mithilfe des Parameters `/d` importiert haben. Sie müssen außerdem die Kopierprotokolldatei angeben, die Sie aus Ihrem Speicherkonto heruntergeladen haben. Eine typische Befehlszeile zum Reparieren eines Importauftrags mit Teilfehlern sieht wie folgt aus:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
Im folgenden Beispiel für eine Kopierprotokolldatei wurde ein 64 KB großer Teil einer für den Importauftrag gesendeten Datei auf dem Laufwerk beschädigt. Da dies der einzige angegebene Fehler ist, wurden die restlichen Blobs des Auftrags erfolgreich importiert.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Wenn dieses Kopierprotokoll an das Azure Import/Export-Tool übergeben wird, versucht das Tool, den Importvorgang für diese Datei durch Kopieren der fehlenden Inhalte über das Netzwerk abzuschließen. Entsprechend dem oben stehenden Beispiel sucht das Tool nach der ursprünglichen Datei `\animals\koala.jpg` in den beiden Verzeichnissen `C:\Users\bob\Pictures` und `X:\BobBackup\photos`. Wenn die Datei `C:\Users\bob\Pictures\animals\koala.jpg` vorhanden ist, kopiert das Azure Import/Export-Tool den fehlenden Datenbereich in das entsprechende Blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Lösen von Konflikten bei der Verwendung von „RepairImport“  
In einigen Situationen kann das Tool möglicherweise die erforderliche Datei aus einem der folgenden Gründe nicht finden oder öffnen: Die Datei wurde nicht gefunden, auf die Datei kann nicht zugegriffen werden, der Dateiname ist mehrdeutig oder der Inhalt der Datei ist nicht mehr richtig.  
  
Ein Mehrdeutigkeitsfehler kann auftreten, wenn das Tool `\animals\koala.jpg` sucht und eine Datei mit diesem Namen in beiden Verzeichnissen (`C:\Users\bob\pictures` und `X:\BobBackup\photos`) findet. Das bedeutet, auf den Importauftragslaufwerken sind die Dateien `C:\Users\bob\pictures\animals\koala.jpg` und `X:\BobBackup\photos\animals\koala.jpg` vorhanden.  
  
Mit der Option `/PathMapFile` können Sie diese Fehler beheben. Sie können den Namen einer Datei angeben, die die Liste der Dateien enthält, die das Tool nicht ordnungsgemäß identifizieren konnte. Das folgende Befehlszeilenbeispiel füllt `9WM35C2V_pathmap.txt` aus:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Das Tool schreibt dann die problematischen Dateipfade in `9WM35C2V_pathmap.txt`, und zwar je einen Pfad pro Zeile. Die Datei kann nach dem Ausführen des Befehls z.B. die folgenden Einträge enthalten:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Für jede Datei in der Liste sollten Sie versuchen, die Datei zu finden und zu öffnen, um sicherzustellen, dass sie für das Tool verfügbar ist. Wenn Sie dem Tool explizit mitteilen möchten, wo sich eine Datei befindet, können Sie die Pfadzuordnungsdatei bearbeiten und den Pfad zu jeder Datei in der gleichen Zeile, durch ein Tabstoppzeichen getrennt, hinzufügen:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Nachdem Sie die erforderlichen Dateien für das Tool zur Verfügung gestellt oder die Pfadzuordnungsdatei aktualisiert haben, können Sie das Tool erneut ausführen, um den Importvorgang abzuschließen.  
  
## <a name="next-steps"></a>Nächste Schritte
 
* [Einrichten des Azure Import/Export-Tools](storage-import-export-tool-setup-v1.md)   
* [Vorbereiten von Festplatten für einen Importauftrag](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Überprüfen des Auftragsstatus mit Protokollkopiedateien](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparieren eines Exportauftrags](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Behandeln von Problemen mit dem Azure Import/Export-Tool](storage-import-export-tool-troubleshooting-v1.md)
