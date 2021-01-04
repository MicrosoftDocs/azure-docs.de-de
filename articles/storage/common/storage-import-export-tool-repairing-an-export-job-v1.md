---
title: Reparieren eines Exportauftrags in Azure Import/Export (V1) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Exportauftrag reparieren, der mithilfe des Azure Import/Export-Diensts erstellt und ausgeführt wurde.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2017
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 35738db5b7bd4a1ac7aaf94e2dc5f1d26a075cdf
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791833"
---
# <a name="repairing-an-export-job"></a>Reparieren eines Exportauftrags
Nach Abschluss eines Exportauftrags können Sie das Microsoft Azure Import/Export-Tool lokal ausführen, um folgende Vorgänge durchzuführen:  
  
1.  Herunterladen von Dateien, die der Azure Import/Export-Dienst nicht exportiert konnte.  
  
2.  Überprüfen, ob die Dateien auf dem Laufwerk ordnungsgemäß exportiert wurden.  
  
Sie müssen über eine Verbindung mit Azure Storage verfügen, um diese Funktion zu verwenden.  
  
Der Befehl zum Reparieren eines Exportauftrags lautet **RepairExport** .

## <a name="repairexport-parameters"></a>RepairExport-Parameter

Die folgenden Parameter können mit **RepairExport** angegeben werden:  
  
|Parameter|BESCHREIBUNG|  
|---------------|-----------------|  
|**/r:&lt;Reparaturdatei\>**|Erforderlich. Pfad zur Reparaturdatei, die den Status der Reparatur verfolgt und das Fortsetzen einer unterbrochenen Reparatur ermöglicht. Jedes Laufwerk muss über genau eine Reparaturdatei verfügen. Wenn Sie mit der Reparatur eines bestimmten Laufwerks beginnen, übergeben Sie den Pfad zu einer noch nicht vorhandenen Reparaturdatei. Zum Fortsetzen einer unterbrochenen Reparatur müssen Sie den Namen einer vorhandenen Reparaturdatei übergeben. Geben Sie immer die Reparaturdatei für das entsprechende Ziellaufwerk an.|  
|**/logdir:&lt;Protokollverzeichnis\>**|Optional. Das Protokollverzeichnis In dieses Verzeichnis werden ausführliche Protokolldateien geschrieben. Wird kein Protokollverzeichnis angegeben, wird das aktuelle Verzeichnis als Protokollverzeichnis verwendet.|  
|**/d:&lt;Zielverzeichnis\>**|Erforderlich. Das zu überprüfende und zu reparierende Verzeichnis. Dieses Verzeichnis ist normalerweise das Stammverzeichnis des Exportlaufwerks, könnte aber auch eine Netzwerkdateifreigabe sein, die eine Kopie der exportierten Dateien enthält.|  
|**/bk:&lt;BitLocker-Schlüssel\>**|Optional. Geben Sie den BitLocker-Schlüssel an, wenn das Tool ein verschlüsseltes Laufwerk entsperren soll, auf dem die exportierten Dateien gespeichert sind.|  
|**/sn:&lt;Speicherkontoname\>**|Erforderlich. Der Name des Speicherkontos für den Exportauftrag.|  
|**/sk:&lt;Speicherkontoschlüssel\>**|Nur **Erforderlich** , wenn keine Container-SAS angegeben wurde. Der Kontoschlüssel des Speicherkontos für den Exportauftrag.|  
|**/csas:&lt;Container-SAS\>**|Nur **Erforderlich** , wenn kein Speicherkontoschlüssel angegeben wurde. Die Container-SAS für den Zugriff auf die Blobs, die dem Exportauftrag zugeordnet sind.|  
|**/CopyLogFile:&lt;Laufwerk-Kopierprotokolldatei\>**|Erforderlich. Der Pfad zur Kopierprotokolldatei des Laufwerks. Die Datei wird vom Windows Azure Import/Export-Dienst generiert und kann aus dem Blobspeicher heruntergeladen werden, der dem Auftrag zugeordnet ist. Die Kopierprotokolldatei enthält Informationen zu fehlerhaften Blobs oder Dateien, die repariert werden müssen.|  
|**/ManifestFile:&lt;Laufwerkmanifestdatei\>**|Optional. Der Pfad zur Manifestdatei des Exportlaufwerks. Diese Datei wird vom Windows Azure Import/Export-Dienst erstellt und auf dem Exportlaufwerk gespeichert. Optional wird sie in einem Blob im Speicherkonto gespeichert, das dem Auftrag zugeordnet ist.<br /><br /> Der Inhalt der Dateien auf dem Exportlaufwerk wird mit den in dieser Datei enthaltenen MD5-Hashes überprüft. Alle beschädigten Dateien werden heruntergeladen und erneut in die Zielverzeichnisse geschrieben.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Verwenden des RepairExport-Modus zum Beheben fehlerhafter Exporte  
Sie können das Azure Import/Export-Tool zum Herunterladen von Dateien verwenden, die nicht exportiert werden konnten. Die Kopierprotokolldatei enthält eine Liste der Dateien, die nicht exportiert werden konnten.  
  
Exportfehler können folgende Ursachen haben:  
  
-   Beschädigte Laufwerke  
  
-   Der Speicherkontoschlüssel wurde während des Übertragungsprozesses geändert  
  
Zum Ausführen des Tools im **RepairExport** -Modus müssen Sie zuerst das Laufwerk, das die exportierten Dateien enthält, an den Computer anschließen. Anschließend führen Sie das Azure Import/Export-Tool aus und geben den Pfad zu diesem Laufwerk mit dem Parameter `/d` an. Sie müssen auch den Pfad zur heruntergeladenen Kopierprotokolldatei des Laufwerks angeben. Das folgende Befehlszeilenbeispiel führt das Tool aus, um alle Dateien zu reparieren, die nicht exportiert werden konnten:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Das folgende Beispiel ist eine Kopierprotokolldatei, die zeigt, dass ein Block im Blob nicht exportiert werden konnte:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Die Kopierprotokolldatei gibt an, dass während des Herunterladens eines der Blöcke des Blobs in die Datei auf dem Exportlaufwerk durch den Windows Azure Import/Export-Dienst ein Fehler aufgetreten ist. Die anderen Komponenten der Datei wurden erfolgreich heruntergeladen, und die Dateilänge wurde ordnungsgemäß festgelegt. In diesem Fall öffnet das Tool die Datei auf dem Laufwerk, lädt den Block aus dem Speicherkonto herunter und schreibt ihn in den Dateibereich ab Offset 65536 mit der Länge 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Verwenden von „RepairExport“ zum Überprüfen der Laufwerkinhalte  
Sie können das Azure-Import-/Exporttool auch zusammen mit der Option **RepairExport** verwenden, um den Inhalt des Laufwerks auf seine Richtigkeit zu überprüfen. Die Manifestdatei auf den einzelnen Exportlaufwerken enthält MD5s für den Inhalt des Laufwerks.  
  
Der Azure Import/Export-Dienst kann die Manifestdateien auch während des Exportvorgangs in ein Speicherkonto speichern. Der Speicherort der Manifestdateien ist nach Abschluss des Auftrags über den [Get Job](/rest/api/storageimportexport/jobs)-Vorgang verfügbar. Weitere Informationen zum Format der Manifestdatei eines Laufwerks finden Sie unter [Format der Manifestdatei des Import/Export-Diensts](/previous-versions/azure/storage/common/storage-import-export-file-format-metadata-and-properties).  
  
Im folgenden Beispiel wird gezeigt, wie Sie das Azure Import/Export-Tool mit den Parametern **/ManifestFile** und **/CopyLogFile** ausführen:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Im folgenden Beispiel ist eine Manifestdatei dargestellt:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Nach Abschluss des Reparaturvorgangs liest das Tool alle Dateien, auf die in der Manifestdatei verwiesen wird, und überprüft die Integrität der Datei mit den MD5-Hashes. Für das oben stehende Manifest werden die folgenden Komponenten durchlaufen.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Alle Komponenten, die die Überprüfung nicht bestehen, werden vom Tool heruntergeladen und erneut in dieselbe Datei auf dem Laufwerk geschrieben.  
  
## <a name="next-steps"></a>Nächste Schritte
 
* [Einrichten des Azure Import/Export-Tools](storage-import-export-tool-setup-v1.md)   
* [Vorbereiten von Festplatten für einen Importauftrag](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import-v1)   
* [Überprüfen des Auftragsstatus mit Protokollkopiedateien](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparieren eines Importauftrags](storage-import-export-tool-repairing-an-import-job-v1.md)