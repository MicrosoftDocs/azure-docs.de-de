---
title: Vorbereiten von Festplatten für einen Azure Import/Export-Auftrag | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Festplatten mit dem WAImportExport-Tool zum Erstellen eines Importauftrags für den Azure Import/Export-Dienst vorbereiten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 777e0aac46dbffb1e491874b5889667a888aadf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478510"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Vorbereiten von Festplatten für einen Importauftrag

Das WAImportExport-Tool ist das Tool für die Laufwerkvorbereitung und Reparatur, das Sie für den [Microsoft Azure Import/Export-Dienst](../storage-import-export-service.md) verwenden können. Sie können dieses Tool zum Kopieren von Daten auf die Laufwerke verwenden, die Sie an ein Azure-Rechenzentrum senden. Nach Abschluss eines Importauftrags können Sie dieses Tool zum Reparieren von Blobs verwenden, die beschädigt sind, fehlen oder in Konflikt mit anderen Blobs stehen. Nachdem Sie die Laufwerke für einen abgeschlossenen Exportauftrag erhalten haben, können Sie dieses Tool nutzen, um Dateien zu reparieren, die beschädigt waren oder auf den Laufwerken fehlten. In diesem Artikel behandeln wir die Verwendung dieses Tools.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="requirements-for-waimportexportexe"></a>Anforderungen zum Ausführen von „WAImportExport.exe“

- **Konfiguration des Computers**
  - Windows 7, Windows Server 2008 R2 oder ein neueres Windows-Betriebssystem
  - .NET Framework 4 muss installiert sein. Unter [Häufig gestellte Fragen](#faq) erfahren Sie, wie Sie überprüfen können, ob .NET Framework auf dem Computer installiert ist.
- **Speicherkontoschlüssel**: Sie benötigen mindestens einen der Kontoschlüssel für das Speicherkonto.

### <a name="preparing-disk-for-import-job"></a>Vorbereiten des Datenträgers für den Importauftrag

- **BitLocker**: BitLocker muss auf dem Computer aktiviert sein, auf dem das WAImportExport-Tool ausgeführt wird. Unter [Häufig gestellte Fragen](#faq) erfahren Sie, wie Sie BitLocker aktivieren.
- **Datenträger**, auf die der Computer zugreifen kann, auf dem das WAImportExport-Tool ausgeführt wird. Unter [Häufig gestellte Fragen](#faq) können Sie sich über die Datenträgerspezifikation informieren.
- **Quelldateien**: Die zu importierenden Dateien müssen vom Kopiercomputer aus zugänglich sein. Dabei spielt es keine Rolle, ob sie sich auf einer Netzwerkfreigabe oder einer lokalen Festplatte befinden.

### <a name="repairing-a-partially-failed-import-job"></a>Reparieren eines teilweise fehlgeschlagenen Importauftrags

- **Kopieren Sie die Protokolldatei**, die generiert wird, wenn der Azure Import/Export-Dienst Daten zwischen Speicherkonto und Festplatte kopiert. Die Datei befindet sich in Ihrem Zielspeicherkonto.

### <a name="repairing-a-partially-failed-export-job"></a>Reparieren eines teilweise fehlgeschlagenen Exportauftrags

- **Kopieren Sie die Protokolldatei**, die generiert wird, wenn der Azure Import/Export-Dienst Daten zwischen Speicherkonto und Festplatte kopiert. Die Datei befindet sich in Ihrem Quellspeicherkonto.
- **Manifestdatei** [optional]: Die Datei befindet sich auf dem exportierten Laufwerk, das von Microsoft zurückgegeben wurde.

## <a name="download-and-install-waimportexport"></a>Herunterladen und Installieren von WAImportExport

Laden Sie die [neueste Version von WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280) herunter. Extrahieren Sie den ZIP-Inhalt in ein Verzeichnis auf Ihrem Computer.

Die nächste Aufgabe besteht darin, CSV-Dateien zu erstellen.

## <a name="prepare-the-dataset-csv-file"></a>Vorbereiten der Dataset-CSV-Datei

### <a name="what-is-dataset-csv"></a>Was ist die Dataset-CSV-Datei?

Die Dataset-CSV-Datei ist der Wert des /DataSet-Flags – eine CSV-Datei, die eine Liste von Verzeichnissen enthält und/oder eine Liste von Dateien, die auf Ziellaufwerke kopiert werden sollen. Der erste Schritt beim Erstellen eines Importauftrags ist, dass Sie bestimmen, welche Verzeichnisse und Dateien Sie importieren möchten. Dies kann eine Liste von Verzeichnissen, eine Liste eindeutiger Dateien oder eine Kombination daraus sein. Wenn ein Verzeichnis einbezogen wird, werden alle Dateien im Verzeichnis und seinen Unterverzeichnissen Teil des Importauftrags.

Für alle Verzeichnisse oder Dateien, die importiert werden sollen, müssen Sie ein virtuelles Zielverzeichnis oder ein Blob im Azure-Blob-Dienst identifizieren. Sie verwenden diese Ziele als Eingaben für das WAImportExport-Tool. Verzeichnisse mit dem Schrägstrich „/“ sollen begrenzt werden.

Die folgende Tabelle zeigt einige Beispiele für Blobziele:

| Quelldatei- oder -verzeichnis | Zielblob oder virtuelles Verzeichnis |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Beispiel für dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Felder der Dataset-CSV-Datei

| Feld | BESCHREIBUNG |
| --- | --- |
| BasePath | **[Erforderlich]**<br/>Der Wert dieses Parameters stellt die Quelle dar, in der sich die zu importierenden Daten befinden. Das Tool kopiert rekursiv alle Daten, die sich unter diesem Pfad befinden.<br><br/>**Zulässige Werte**: Dies muss ein gültiger Pfad auf dem lokalen Computer oder ein gültiger Freigabepfad sein, und der Benutzer sollte darauf zugreifen können. Der Verzeichnispfad muss ein absoluter Pfad sein (kein relativer Pfad). Wenn der Pfad mit „\\“ endet, stellt er ein Verzeichnis woanders in einem Pfad dar, der ohne „\\“ endet und eine Datei darstellt.<br/>In diesem Feld sind keine regulären Ausdrücke zulässig. Wenn der Pfad Leerzeichen enthält, setzen Sie sie in "".<br><br/>**Beispiel**: „c:\Directory\c\Directory\File.txt“<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Erforderlich]**<br/> Der Pfad zum virtuellen Zielverzeichnis in Ihrem Microsoft Azure-Speicherkonto. Das virtuelle Verzeichnis kann, muss jedoch noch nicht vorhanden sein. Wenn es nicht vorhanden ist, wird es vom Import/Export-Dienst erstellt.<br/><br/>Achten Sie darauf, gültige Containernamen zu verwenden, wenn Sie virtuelle Zielverzeichnisse oder Blobs angeben. Containernamen müssen kleingeschrieben werden. Informationen zu den Benennungsregeln für Container finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese). Wenn nur der Stamm angegeben wird, wird die Verzeichnisstruktur der Quelle im Zielblobcontainer repliziert. Für den Fall, das eine andere Verzeichnisstruktur als diejenige in der Quelle gewünscht ist, stehen in der CSV-Datei mehrere Zeilen zur Zuordnung zur Verfügung.<br/><br/>Sie können einen Container oder ein Blobpräfix angeben, wie etwa „music/70s/“. Das Zielverzeichnis muss mit dem Containernamen beginnen, gefolgt von einem Schrägstrich „/“, und kann optional ein virtuelles Blobverzeichnis enthalten, das mit „/“ endet.<br/><br/>Wenn der Zielcontainer der Stammcontainer ist, müssen Sie explizit den Stammcontainer angeben – einschließlich des Schrägstrichs, wie „$root/“. Da die Namen von Blobs unter dem Stammcontainer nicht „/“ enthalten können, werden Unterverzeichnisse des Quellverzeichnisses nicht kopiert, wenn das Zielverzeichnis der Stammcontainer ist.<br/><br/>**Beispiel**<br/>Wenn der Zielblobpfad https://mystorageaccount.blob.core.windows.net/video ist, kann der Wert dieses Felds „video/“ sein.  |
| BlobType | **[Optional]** block &#124; page<br/>Gegenwärtig unterstützt der Import/Export-Dienst 2 Arten von Blobs. Seitenblobs und Blockblobs – standardmäßig werden alle Dateien als Blockblobs importiert. \*.vhd und \*.vhdx werden als Seitenblobs importiert. Die zulässige Größe von Blockblobs und Seitenblobs ist begrenzt. Weitere Informationen finden Sie unter [Skalierbarkeitsziele für Blobs, Warteschlangen, Tabellen und Dateien](storage-scalability-targets.md).  |
| Disposition | **[Optional]** rename &#124; no-overwrite &#124; overwrite (umbenennen, nicht überschreiben, überschreiben) <br/> Dieses Feld gibt das Kopierverhalten beim Import an, Das bedeutet, wenn Daten vom Datenträger in das Speicherkonto geladen werden. Verfügbare Optionen sind: rename&#124;overwite&#124;no-overwrite. Der Standardwert ist „rename“, wenn nichts angegeben ist. <br/><br/>**Rename**: Wenn ein Objekt mit dem gleichen Namen vorhanden ist, wird eine Kopie im Ziel erstellt.<br/>Overwrite: Die Datei wird mit der neueren Dateiversion überschrieben. Die Version mit der letzten Änderung überschreibt die ältere.<br/>**No-overwrite**: Wenn die Datei bereits vorhanden ist, wird sie nicht überschrieben.|
| MetadataFile | **[Optional]** <br/>Der Wert dieses Felds ist die Metadatendatei, die bereitgestellt werden kann, wenn die Metadaten der Objekte beibehalten werden müssen, oder um benutzerdefinierte Metadaten bereitzustellen. Pfad zur Metadatendatei für die Zielblobs. Weitere Informationen finden Sie unter [Format der Metadaten- und Eigenschaftendatei des Import/Export-Diensts](../storage-import-export-file-format-metadata-and-properties.md). |
| PropertiesFile | **[Optional]** <br/>Pfad zur Eigenschaftendatei für die Zielblobs. Weitere Informationen finden Sie unter [Format der Metadaten- und Eigenschaftendatei des Import/Export-Diensts](../storage-import-export-file-format-metadata-and-properties.md). |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Vorbereiten von InitialDriveSet- oder AdditionalDriveSet-CSV-Datei

### <a name="what-is-driveset-csv"></a>Was ist die Driveset-CSV-Datei?

Der Wert des „/InitialDriveSet“- bzw. „/AdditionalDriveSet“-Flags ist eine CSV-Datei, die die Liste der Datenträger enthält, denen die Laufwerkbuchstaben zugeordnet sind, damit das Tool richtig die Liste der vorzubereitenden Datenträger auswählen kann. Wenn die Größe der Daten die Größe eines einzelnen Datenträgers überschreitet, verteilt das WAImportExport-Tool die Daten, die in der CSV-Datei eingetragen sind, auf optimierte Weise auf mehrere Datenträger.

Es gibt keine Beschränkung für die Anzahl der Datenträger, auf die die Daten in einer einzelnen Sitzung geschrieben werden können. Das Tool verteilt die Daten auf Grundlage der Datenträger- und Ordnergröße. Es wählt den Datenträger aus, der für die Größe des Objekts am meisten optimiert ist. Beim Hochladen in das Speicherkonto werden die Daten wieder in die Verzeichnisstruktur konvergiert, die in der Dataset-Datei angegeben wurde. Um eine Driveset-CSV-Datei zu erstellen, führen Sie die folgenden Schritte aus.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Erstellen eines Basisvolumes und Zuweisen eines Laufwerkbuchstaben

Um ein Basisvolume zu erstellen und einen Laufwerkbuchstaben zuzuweisen, befolgen Sie die Anweisungen für „Simpler partition creation“ (Einfachere Partitionserstellung) unter [Overview of Disk Management](https://technet.microsoft.com/library/cc754936.aspx) (Übersicht über die Datenträgerverwaltung).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>InitialDriveSet- und AdditionalDriveSet-CSV-Beispieldatei

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Felder der Driveset-CSV-Datei

| Felder | Wert |
| --- | --- |
| DriveLetter | **[Erforderlich]**<br/> Auf jedem Laufwerk, das dem Tool als Ziel bereitgestellt wird, muss ein einfaches NTFS-Volume vorhanden sein, und ihm muss ein Laufwerkbuchstabe zugewiesen sein.<br/> <br/>**Beispiel**: R oder r |
| FormatOption | **[Required]** Format &#124; AlreadyFormatted<br/><br/> **Format**: Bei dieser Angabe werden alle Daten auf dem Datenträger formatiert. <br/>**AlreadyFormatted**: Bei dieser Angabe überspringt das Tool die Formatierung. |
| SilentOrPromptOnFormat | **[Erforderlich]** SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: Bei dieser Angabe kann der Benutzer das Tool im unbeaufsichtigten Modus ausführen. <br/>**PromptOnFormat**: Das Tool fordert den Benutzer bei jeder Formatierung auf, zu bestätigen, ob die Aktion wirklich vorgesehen ist.<br/><br/>Wenn kein Wert festgelegt ist, wird der Befehl abgebrochen und eine Fehlermeldung angezeigt: „Incorrect value for SilentOrPromptOnFormat: none“ (Falscher Wert für SilentOrPromptOnFormat: keiner). |
| Verschlüsselung | **[Erforderlich]** Encrypt &#124; AlreadyEncrypted<br/> Der Wert dieses Felds entscheidet, welcher Datenträger verschlüsselt wird und welcher nicht. <br/><br/>**Encrypt**: Das Tool formatiert das Laufwerk. Wenn der Wert des Felds „FormatOption“ „Format“ ist, muss dieser Wert „Encrypt“ sein. Wenn in diesem Fall „AlreadyEncrypted“ angegeben wird, führt dies zu der Fehlermeldung: „When Format is specified, Encrypt must also be specified“ (Bei Angabe von ‚Format‘ muss auch ‚Encrypt‘ angegeben werden).<br/>**AlreadyEncrypted**: Das Tool entschlüsselt das Laufwerk mit dem im Feld „ExistingBitLockerKey“ angegebenen BitLocker-Schlüssel. Wenn der Wert des Felds „FormatOption“ „AlreadyFormatted“ ist, kann dieser Wert entweder „Encrypt“ oder „AlreadyEncrypted“ sein. |
| ExistingBitLockerKey | **[Erforderlich]** Wenn das Feld „Encryption“ den Wert „AlreadyEncrypted“ hat.<br/> Der Wert dieses Felds ist der BitLocker-Schlüssel, der dem bestimmten Datenträger zugeordnet ist. <br/><br/>Dieses Feld sollte leer gelassen werden, wenn das Feld „Encryption“ den Wert „Encrypt“ hat.  Wenn der BitLocker-Schlüssel in diesem Fall angegeben wird, führt dies zu der Fehlermeldung „Bitlocker Key should not be specified“ (BitLocker-Schlüssel sollte nicht angegeben werden).<br/>  **Beispiel**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Vorbereiten des Datenträgers für den Importauftrag

Um Laufwerke für einen Importauftrag vorzubereiten, rufen Sie das WAImportExport-Tool mit dem **PrepImport**-Befehl auf. Welche Parameter Sie verwenden, hängt davon ab, ob dies die erste oder eine nachfolgende Kopiersitzung ist.

### <a name="first-session"></a>Erste Sitzung

Erste Kopiersitzung zum Kopieren eines einzelnen Verzeichnisses/mehrerer Verzeichnisses auf einen einzelnen/mehrere Datenträger (je nachdem, was in der CSV-Datei angegeben wird). Der PrepImport-Befehl des WAImportExport-Tools für die erste Kopiersitzung zum Kopieren von Verzeichnissen und/oder Dateien mit einer neuen Kopiersitzung lautet:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] /DataSet:<dataset.csv>
```

**Beispiel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Hinzufügen von Daten in einer nachfolgenden Sitzung

In nachfolgenden Kopiersitzungen müssen Sie nicht die ursprünglichen Parameter angeben. Sie müssen die gleiche Journaldatei verwenden, damit das Tool weiß, an welchem Punkt es in der vorherigen Sitzung ausgestiegen ist. Der Status der Kopiersitzung wird in die Journaldatei geschrieben. Hier ist die Syntax für eine nachfolgende Kopiersitzung zum Kopieren zusätzlicher Verzeichnisse und/oder Dateien:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Beispiel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Hinzufügen von Laufwerken zur aktuellen Sitzung

Wenn die Daten nicht in die in InitialDriveset angegebenen Laufwerke passen, können Sie mit dem Tool der gleichen Kopiersitzung zusätzliche Laufwerke hinzufügen. 

> [!NOTE]
> Die Sitzungs-ID sollte mit der vorherigen Sitzungs-ID übereinstimmen. Die Journaldatei sollte mit der Journaldatei übereinstimmen, die in der vorherigen Sitzung angegeben wurde.
> 
> ```
> WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
> ```

**Beispiel:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Abbruch der aktuellen Sitzung:

Wenn eine Kopiersitzung unterbrochen wird, und keine Fortsetzung möglich ist (wenn beispielsweise kein Zugriff auf ein Quellverzeichnis besteht), müssen Sie die aktuelle Sitzung abbrechen, sodass ein Rollback ausgeführt werden kann und neue Kopiersitzungen gestartet werden können:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Beispiel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Nur die letzte Kopiersitzung kann abgebrochen werden, wenn sie fehlerbedingt beendet wurde. Beachten Sie, dass Sie die erste Kopiersitzung für ein Laufwerk nicht abbrechen können. Stattdessen müssen Sie die Kopiersitzung mit einer neuen Journaldatei neu starten.

### <a name="resume-a-latest-interrupted-session"></a>Fortsetzen der aktuellen unterbrochenen Sitzung

Wenn eine Kopiersitzung aus beliebigem Grund unterbrochen wird, können Sie sie fortsetzen, indem Sie das Tool nur unter Angabe der Journaldatei ausführen:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Beispiel:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Wenn Sie eine Kopiersitzung fortsetzen, ändern Sie nicht die Quelldatendateien und -verzeichnisse durch Hinzufügen oder Entfernen von Dateien.

## <a name="waimportexport-parameters"></a>WAImportExport-Parameter

| Parameter | BESCHREIBUNG |
| --- | --- |
|     /j:&lt;Journaldatei&gt;  | **Erforderlich**<br/> Der Pfad zur Journaldatei. Eine Journaldatei verfolgt einen Satz von Laufwerken und zeichnet den Status der Vorbereitung dieser Laufwerke auf. Die Journaldatei muss immer angegeben werden.  |
|     /LogDir:&lt;Protokollverzeichnis&gt;  | **Optional**. Das Protokollverzeichnis.<br/> In dieses Verzeichnis werden sowohl ausführliche Protokolldateien als auch Temporärdateien geschrieben. Ohne spezielle Angabe wird das aktuelle Verzeichnis als Protokollverzeichnis verwendet. Das Protokollverzeichnis kann nur einmal für die gleiche Journaldatei angegeben werden.  |
|     /id:&lt;Sitzungs-ID&gt;  | **Erforderlich**<br/> Der Sitzungs-ID wird zum Identifizieren einer Kopiersitzung verwendet. Sie wird verwendet, um eine exakte Wiederherstellung einer unterbrochenen Kopiersitzung sicherzustellen.  |
|     /ResumeSession  | Optional. Wenn die letzte Kopiersitzung fehlerbedingt beendet wurde, kann dieser Parameter angegeben werden, um die Sitzung fortzusetzen.   |
|     /AbortSession  | Optional. Wenn die letzte Kopiersitzung fehlerbedingt beendet wurde, kann dieser Parameter angegeben werden, um die Sitzung abzubrechen.  |
|     /sn:&lt;Speicherkontoname&gt;  | **Erforderlich**<br/> Gilt nur für RepairImport und RepairExport. Der Name des Speicherkontos.  |
|     /sk:&lt;Speicherkontoschlüssel&gt;  | **Erforderlich**<br/> Der Schlüssel des Speicherkontos. |
|     /InitialDriveSet:&lt;driveset.csv&gt;  | **Erforderlich** Beim Ausführen der ersten Kopiersitzung.<br/> Eine CSV-Datei, die eine Liste der vorzubereitenden Laufwerke enthält.  |
|     /AdditionalDriveSet:&lt;driveset.csv&gt; | **Erforderlich**. Wenn Sie der aktuellen Kopiersitzung Laufwerke hinzufügen. <br/> Eine CSV-Datei, die eine Liste zusätzlich hinzuzufügender Laufwerke enthält.  |
|      /r:&lt;Reparaturdatei&gt; | **Erforderlich** Gilt nur für RepairImport und RepairExport.<br/> Der Pfad zur Datei für die Verfolgung des Reparaturstatus. Jedes Laufwerk muss über genau eine Reparaturdatei verfügen.  |
|     /d:&lt;Zielverzeichnisse&gt; | **Erforderlich**. Gilt nur für RepairImport und RepairExport. Für RepairImport ein oder mehrere durch Semikolons getrennte zu reparierende Verzeichnisse; für RepairExport ein zu reparierendes Verzeichnis, z.B. das Stammverzeichnis des Laufwerks.  |
|     /CopyLogFile:&lt;Laufwerk-Kopierprotokolldatei&gt; | **Erforderlich** Gilt nur für RepairImport und RepairExport. Pfad zur Kopierprotokolldatei des Laufwerks (ausführlich oder Fehler).  |
|     /ManifestFile:&lt;Laufwerkmanifestdatei&gt; | **Erforderlich** Gilt nur für RepairExport.<br/> Pfad der Laufwerkmanifestdatei.  |
|     /PathMapFile:&lt;Laufwerkpfad-Zuordnungsdatei&gt; | **Optional**. Gilt nur für RepairImport.<br/> Pfad zur Datei, die relative Zuordnungen von Dateipfaden zu Speicherorten der eigentlichen Dateien zum Laufwerksstamm enthält (mit Tabstopptrennzeichen). Bei der ersten Angabe wird sie mit Dateipfaden mit leeren Zielen aufgefüllt, was bedeutet, das sie entweder nicht in den Zielverzeichnissen gefunden, der Zugriff verweigert wurde, ihre Namen ungültig oder sie in mehreren Verzeichnissen vorhanden sind. Die Pfadzuordnungsdatei kann manuell bearbeitet werden, sodass sie die richtigen Zielpfade enthält, und erneut angegeben werden, damit das Tool die Dateipfade ordnungsgemäß auflöst.  |
|     /ExportBlobListFile:&lt;Exportbloblisten-Datei&gt; | **Erforderlich**. Gilt nur für PreviewExport.<br/> Pfad zur XML-Datei mit der Liste der Blobpfade oder Blobpfadpräfixe für die zu exportierenden Blobs. Das Dateiformat ist identisch mit dem Bloblisten-Blobformat im „Put Job“-Vorgang der REST-API des Import/Export-Diensts.  |
|     /DriveSize:&lt;Laufwerkgröße&gt; | **Erforderlich**. Gilt nur für PreviewExport.<br/>  Die Größe der Laufwerke, die für den Export verwendet werden. Beispielsweise 500 GB, 1,5 TB. Hinweis: 1GB = 1.000.000.000 Bytes, 1TB = 1.000.000.000.000 Bytes  |
|     /DataSet:&lt;dataset.csv&gt; | **Erforderlich**<br/> Eine CSV-Datei, die eine Liste von Verzeichnissen enthält und/oder eine Liste von Dateien, die auf Ziellaufwerke kopiert werden sollen.  |
|     /silentmode  | **Optional**.<br/> Ohne Angabe werden Sie an Laufwerkanforderungen erinnert, und Ihre Bestätigung ist erforderlich, um fortzufahren.  |

## <a name="tool-output"></a>Ausgabe des Tools

### <a name="sample-drive-manifest-file"></a>Beispiel einer Laufwerkmanifestdatei

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Beispiel einer Journaldatei (XML) für jedes Laufwerk

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Beispiel einer Sitzungsjournaldatei (JRN), das die Spur der Sitzungen aufzeichnet

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="general"></a>Allgemein

#### <a name="what-is-waimportexport-tool"></a>Was ist das WAImportExport-Tool?

Das WAImportExport-Tool ist das Tool für die Laufwerkvorbereitung und Reparatur, das Sie für den Microsoft Azure Import/Export-Dienst verwenden können. Sie können dieses Tool zum Kopieren von Daten auf die Laufwerke verwenden, die Sie an ein Azure-Rechenzentrum senden. Nach Abschluss eines Importauftrags können Sie dieses Tool zum Reparieren von Blobs verwenden, die beschädigt sind, fehlen oder in Konflikt mit anderen Blobs stehen. Nachdem Sie die Laufwerke für einen abgeschlossenen Exportauftrag erhalten haben, können Sie dieses Tool nutzen, um Dateien zu reparieren, die beschädigt waren oder auf den Laufwerken fehlten.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Wie funktioniert das WAImportExport-Tool bei mehreren Quellverzeichnissen und Datenträgern?

Wenn die Größe der Daten die Größe des Datenträgers überschreitet, verteilt das WAImportExport-Tool die Daten auf optimierte Weise auf mehrere Datenträger. Das Kopieren der Daten auf mehrere Datenträger kann parallel oder sequenziell ausgeführt werden. Es gibt keine Beschränkung für die Anzahl der Datenträger, auf die die Daten simultan geschrieben werden können. Das Tool verteilt die Daten auf Grundlage der Datenträger- und Ordnergröße. Es wählt den Datenträger aus, der für die Größe des Objekts am meisten optimiert ist. Beim Hochladen in das Speicherkonto werden die Daten wieder in die angegebene Verzeichnisstruktur konvergiert.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Wo finde ich die Vorgängerversion des WAImportExport-Tools?

Das WAImportExport-Tool verfügt über alle Funktionen, die das WAImportExport V1-Tool hatte. Das WAImportExport-Tool ermöglicht Benutzern, mehrere Quellen anzugeben und auf mehrere Laufwerke zu schreiben. Darüber hinaus lassen sich problemlos mehrere Quellspeicherorte, aus denen die Daten kopiert werden müssen, in einer einzigen CSV-Datei verwalten. Falls Sie jedoch SAS-Unterstützung benötigen oder eine einzelne Datenquelle auf einen einzelnen Datenträger kopieren möchten, können Sie das [WAImportExport V1-Tool herunterladen](https://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409). In der [Referenz zu WAImportExport V1](storage-import-export-tool-how-to-v1.md) finden Sie Hilfe zur Verwendung von WAImportExport V1 finden.

#### <a name="what-is-a-session-id"></a>Was ist eine Sitzungs-ID?

Das Tool erwartet, dass der Kopiersitzungsparameter (/id) identisch ist, wenn die Daten auf mehrere Datenträger verteilt werden sollen. Wenn der Name der Kopiersitzung beibehalten wird, kann der Benutzer Daten aus einem oder mehreren Speicherorten in ein oder mehrere Zieldatenträger/-verzeichnisse kopieren. Wenn die gleiche Sitzungs-ID beibehalten wird, kann das Tool die Kopie der Dateien dort abholen, wo sie sich beim letzten Mal befand.

Allerdings können Daten nicht in der gleichen Kopiersitzung in verschiedene Speicherkonten importiert werden.

Wenn der Name der Kopiersitzung in mehreren Durchläufen des Tools identisch ist, wird erwartet, dass Protokolldatei (/logdir) und Speicherkontoschlüssel (/sk) ebenfalls identisch sind.

Die Sitzungs-ID kann aus Buchstaben, 0~9, Unterstrich (\_), Bindestrich (-) oder Hash (#) bestehen, und die Länge muss 3~30 betragen.

Beispiel: „session-1“, „session#1“ oder „session\_1“

#### <a name="what-is-a-journal-file"></a>Was ist eine Journaldatei?

Bei jeder Ausführung des WAImportExport-Tools zum Kopieren von Dateien auf die Festplatte erstellt das Tool eine Kopiersitzung. Der Status der Kopiersitzung wird in die Journaldatei geschrieben. Wenn eine Kopiersitzung (z.B. aufgrund eines Systemausfalls) unterbrochen wird, können Sie sie fortsetzen, indem Sie das Tool erneut ausführen und die Journaldatei in der Befehlszeile angeben.

Für jede Festplatte, die Sie mit dem Azure Import/Export-Tool vorbereiten, erstellt das Tool eine einzelne Journaldatei mit dem Namen „&lt;DriveID&gt;.xml“, wobei DriveID die Seriennummer des Laufwerks ist, die das Tool aus dem Datenträger liest. Sie benötigen die Journaldateien von allen Ihren Laufwerken, um den Importauftrag zu erstellen. Die Journaldatei kann auch verwendet werden, um die Vorbereitung des Laufwerks nach Unterbrechung des Tools fortzusetzen.

#### <a name="what-is-a-log-directory"></a>Was ist ein Protokollverzeichnis?

Das Protokollverzeichnis ist ein Verzeichnis zum Speichern ausführlicher Protokolle als auch temporärer Manifestdateien. Ohne spezielle Angabe wird das aktuelle Verzeichnis als Protokollverzeichnis verwendet. Die Protokolle sind ausführliche Protokolle.

### <a name="prerequisites"></a>Voraussetzungen

#### <a name="what-are-the-specifications-of-my-disk"></a>Welche Spezifikationen weist mein Datenträger auf?

Mindestens ein leeres, an den Kopiercomputer angeschlossenes 2,5-Zoll- oder 3,5-Zoll-SATA-II-, -III- oder -SSD-Festplattenlaufwerk.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Wie kann ich BitLocker auf meinem Computer aktivieren?

Überprüfen Sie dies einfach, indem Sie mit der rechten Maustaste auf das Systemlaufwerk klicken. Es werden Optionen für BitLocker angezeigt, wenn die Funktion aktiviert ist. Wenn sie deaktiviert ist, werden keine Optionen angezeigt.

![Überprüfen von BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Hier ist eine [Schrittweise Anleitung zur Windows BitLocker-Laufwerkverschlüsselung](https://technet.microsoft.com/library/cc766295.aspx).

Es ist möglich, dass Ihr Computer nicht über einen TPM-Chip verfügt. Wenn Sie mit tpm.msc keine Ausgabe erhalten, lesen Sie die nächste häufig gestellte Frage.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Wie deaktiviere ich Trusted Platform Module (TPM) in BitLocker?
> [!NOTE]
> Sie müssen die TPM-Richtlinie nur deaktivieren, wenn TPM auf ihren Servern nicht vorhanden ist. Es ist nicht notwendig, TPM zu deaktivieren, wenn ein vertrauenswürdiger TPM im Server des Benutzers vorhanden ist. 
> 

Um TPM in BitLocker zu deaktivieren, führen Sie die folgenden Schritte aus:<br/>
1. Starten Sie den **Gruppenrichtlinien-Editor** durch Eingabe von „gpedit.msc“ in eine Eingabeaufforderung. Wenn der **Gruppenrichtlinien-Editor** nicht verfügbar zu sein scheint, aktivieren Sie zuerst BitLocker. Siehe vorherige häufig gestellte Frage.
2. Öffnen Sie **Lokale Computerrichtlinie &gt; Computerkonfiguration &gt; Administrative Vorlagen &gt; Windows-Komponenten &gt; BitLocker-Laufwerkverschlüsselung &gt; Betriebssystemlaufwerke**.
3. Bearbeiten Sie die Richtlinie **Zusätzliche Authentifizierung beim Start anfordern**.
4. Legen Sie die Richtlinie auf **Aktiviert** fest, und stellen Sie sicher, dass **BitLocker ohne kompatibles TPM zulassen** aktiviert ist.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Wie kann ich überprüfen, ob .NET 4 oder eine höhere Version auf meinem Computer installiert ist?

Alle Microsoft .NET Framework-Versionen werden im folgenden Verzeichnis installiert: „%windir%\Microsoft.NET\Framework\“.

Navigieren Sie zu dem oben erwähnten Verzeichnis Ihres Zielcomputers, auf dem das Tool ausgeführt werden soll. Suchen Sie nach einem Ordnernamen, der mit „v4“ beginnt. Wenn ein solches Verzeichnis nicht vorhanden ist, ist .NET 4 nicht auf dem Computer installiert. Sie können .NET 4 mit [Microsoft .NET Framework 4 (Webinstaller)](https://www.microsoft.com/download/details.aspx?id=17851) auf Ihren Computer herunterladen.

### <a name="limits"></a>Einschränkungen

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Wie viele Laufwerke kann ich zur gleichen Zeit vorbereiten/senden?

Es gibt keine Beschränkung für die Anzahl der Datenträger, die das Tool vorbereiten kann. Allerdings erwartet das Tool Laufwerkbuchstaben als Eingabe. Damit gilt die Einschränkung, dass 25 Datenträger gleichzeitig vorbereitet werden können. Ein einzelner Auftrag kann maximal 10 Datenträger behandeln. Wenn Sie mehr als 10 Datenträger für das gleiche Speicherkonto vorbereiten, können die Datenträger auf mehrere Aufträge verteilt werden.

#### <a name="can-i-target-more-than-one-storage-account"></a>Kann ich mehrere Speicherkonten angeben?

Pro Auftrag und in einer einzelnen Kopiersitzung kann nur ein einziges Speicherkonto angegeben werden.

### <a name="capabilities"></a>Funktionen

#### <a name="does-waimportexportexe-encrypt-my-data"></a>Verschlüsselt WAImportExport.exe meine Daten?

Ja. Die BitLocker-Verschlüsselung ist aktiviert und für diesen Vorgang erforderlich.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Welche Hierarchie haben meine Daten im Speicherkonto?

Trotz Verteilung auf mehrere Datenträger werden die Daten beim Hochladen in das Speicherkonto wieder in die Verzeichnisstruktur konvergiert, die in der Dataset-CSV-Datei angegeben wurde.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Bei wie vielen der Eingabedatenträger ist beim Kopiervorgang E/A parallel aktiv?

Das Tool verteilt Daten gemäß der Größe der Eingabedateien auf die Eingabedatenträger. Dies bedeutet, dass die Anzahl der parallel aktiven Datenträger vollständig von der Art der Eingabedaten abhängt. Abhängig von der Größe der einzelnen Dateien im Eingabedataset kann E/A bei ein oder mehreren Datenträgern parallel aktiv sein. Nächste Frage für weitere Details anzeigen

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Wie verteilt das Tool die Dateien auf die Datenträger?

Das WAImportExport-Tool liest und schreibt Dateien Batch für Batch, wobei ein Batch höchstens 100.000 Dateien enthält. Dies bedeutet, dass max. 100.000 Dateien parallel geschrieben werden können. Auf mehrere Datenträger wird gleichzeitig geschrieben, wenn diese 100.000 Dateien auf mehrere Laufwerke verteilt werden. Ob das Tool auf mehrere Datenträger gleichzeitig oder einen einzelnen Datenträger schreibt, hängt jedoch von der Gesamtgröße des Batches ab. Wenn z.B. im Falle kleinerer Dateien alle 100.000 Dateien auf ein einziges Laufwerk passen, schreibt das Tool während der Verarbeitung dieses Batches nur auf einen Datenträger.

### <a name="waimportexport-output"></a>WAImportExport-Ausgabe

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Es gibt zwei Journaldateien, welche davon soll ich in das Azure-Portal hochladen?

**XML**: Für jede Festplatte, die Sie mit dem WAImportExport-Tool vorbereiten, erstellt das Tool eine einzelne Journaldatei mit dem Namen `<DriveID>.xml`, wobei DriveID die Seriennummer des Laufwerks ist, die das Tool aus dem Datenträger liest. Sie benötigen die Journaldateien von allen Ihren Laufwerken, um den Importauftrag im Azure-Portal zu erstellen. Diese Journaldatei kann auch verwendet werden, um die Vorbereitung des Laufwerks nach Unterbrechung des Tools fortzusetzen.

**JRN**: Die Journaldatei mit dem Suffix `.jrn` enthält den Status für alle Kopiersitzungen einer Festplatte. Sie enthält außerdem die benötigten Informationen zum Erstellen des Importauftrags. Wenn das WAImportExport-Tool für die Sitzung ausgeführt wird, müssen Sie sowohl eine Journaldatei als auch eine Kopiersitzungs-ID angeben.

## <a name="next-steps"></a>Nächste Schritte

* [Einrichten des Azure Import/Export-Tools](storage-import-export-tool-setup.md)
* [Festlegen von Eigenschaften und Metadaten im Rahmen des Importprozesses](storage-import-export-tool-setting-properties-metadata-import.md)
* [Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Kurzübersicht über häufig verwendete Befehle](storage-import-export-tool-quick-reference.md) 
* [Überprüfen des Auftragsstatus mit Protokollkopiedateien](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparieren eines Importauftrags](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparieren eines Exportauftrags](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Behandeln von Problemen mit dem Azure Import/Export-Tool](storage-import-export-tool-troubleshooting-v1.md)
