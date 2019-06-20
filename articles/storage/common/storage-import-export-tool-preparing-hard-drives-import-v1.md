---
title: Vorbereiten von Festplatten für einen Importauftrag in Azure Import/Export (V1) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Festplatten mit dem WAImportExport V1-Tool zum Erstellen eines Importauftrags für den Azure Import/Export-Dienst vorbereiten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 03b504524b2f489f1ee042c6e825ccffe0a60bb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478469"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Vorbereiten von Festplatten für einen Importauftrag
Um eine oder mehrere Festplatten für einen Importauftrag vorzubereiten, gehen Sie in folgenden Schritten vor:

- Identifizieren der in den Blob-Dienst zu importierenden Daten

- Identifizieren der die virtuellen Zielverzeichnisse und Blobs im Blob-Dienst

- Bestimmen der Anzahl benötigter Laufwerke

- Kopieren der Daten auf jede der Festplatten

  Einen Beispielworkflow finden Sie unter [Sample Workflow to Prepare Hard Drives for an Import Job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md) (Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag).

## <a name="identify-the-data-to-be-imported"></a>Identifizieren der zu importierenden Daten
 Der erste Schritt beim Erstellen eines Importauftrags ist, dass Sie bestimmen, welche Verzeichnisse und Dateien Sie importieren möchten. Dies kann eine Liste von Verzeichnissen, eine Liste eindeutiger Dateien oder eine Kombination daraus sein. Wenn ein Verzeichnis einbezogen wird, werden alle Dateien im Verzeichnis und seinen Unterverzeichnissen Teil des Importauftrags.

> [!NOTE]
>  Da Unterverzeichnisse rekursiv eingeschlossen werden, wenn ein übergeordnetes Verzeichnis einbezogen wird, geben Sie nur das übergeordnete Verzeichnis an. Geben Sie darüber hinaus keines seiner Unterverzeichnisse an.
>
>  Gegenwärtig gilt für das Microsoft Azure Import/Export-Tool folgende Einschränkung: Wenn ein Verzeichnis mehr Daten enthält, als eine Festplatte aufnehmen kann, muss das Verzeichnis in kleinere Verzeichnisse unterteilt werden. Wenn ein Verzeichnis z.B. 2,5TB Daten enthält, und die Kapazität der Festplatte nur 2TB beträgt, müssen Sie das 2,5TB große Verzeichnis in kleinere Verzeichnisse unterteilen. Diese Einschränkung wird in zukünftigen Versionen des Tools behoben werden.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Ermitteln der Zielverzeichnisse im Blob-Dienst
 Für alle Verzeichnisse oder Dateien, die importiert werden sollen, müssen Sie ein virtuelles Zielverzeichnis oder ein Blob im Azure-Blob-Dienst identifizieren. Sie verwenden diese Ziele als Eingaben für das Azure Import/Export-Tool. Beachten Sie, dass Verzeichnisse mit dem Schrägstrich „/“ begrenzt werden sollen.

 Die folgende Tabelle zeigt einige Beispiele für Blobziele:

|Quelldatei- oder -verzeichnis|Zielblob oder virtuelles Verzeichnis|
|------------------------------|-------------------------------------------|
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Bestimmen der Anzahl benötigter Laufwerke
 Als Nächstes müssen Sie Folgendes bestimmen:

- Die Anzahl der Festplatten, die zum Speichern der Daten erforderlich sind.

- Die Verzeichnisse und/oder eigenständigen Dateien, die auf jede Festplatte kopiert werden sollen.

  Stellen Sie sicher, dass Sie über die Anzahl von Festplatten verfügen, die Sie benötigen, um die Daten zu speichern, die Sie übertragen.

## <a name="copy-data-to-your-hard-drive"></a>Kopieren von Daten auf Ihre Festplatte
 In diesem Abschnitt wird beschrieben, wie Sie das Azure Import/Export-Tool aufrufen, um Ihre Daten auf eine oder mehrere Festplatten zu kopieren. Jedes Mal, wenn Sie das Azure Import/Export-Tool aufrufen, erstellen Sie eine neue *Kopiersitzung*. Sie erstellen mindestens eine Kopiersitzung für jedes Laufwerk, auf das Sie Daten kopieren. In einigen Fällen benötigen Sie möglicherweise mehrere Kopiersitzungen, um alle Ihre Daten auf ein einzelnes Laufwerk zu kopieren. Hier sind einige Gründe, warum Sie möglicherweise mehrere Kopiersitzungen benötigen:

-   Sie müssen eine separate Kopiersitzung für jedes Laufwerk erstellen, auf das Sie kopieren.

-   Eine Kopiersitzung kann entweder ein einzelnes Verzeichnis oder ein einzelnes Blob auf das Laufwerk kopieren. Wenn Sie mehrere Verzeichnisse, mehrere Blobs oder eine Kombination aus beidem kopieren, müssen Sie mehrere Kopiersitzungen erstellen.

-   Sie können Eigenschaften und Metadaten angeben, die für die Blobs festgelegt werden, die als Teil eines Importauftrags importiert werden. Die Eigenschaften oder Metadaten, die Sie für eine Kopiersitzung angeben, gelten für alle Blobs, die durch diese Kopiersitzung angegeben werden. Wenn Sie für einige Blobs unterschiedliche Eigenschaften oder Metadaten angeben möchten, müssen Sie eine separate Kopiersitzung erstellen. Weitere Informationen siehe [Festlegen von Eigenschaften und Metadaten im Rahmen des Importprozesses](storage-import-export-tool-setting-properties-metadata-import-v1.md).

> [!NOTE]
>  Wenn Sie über mehrere Computer verfügen, die die in [Einrichten des Azure Import/Export-Tools](storage-import-export-tool-setup-v1.md) beschriebenen Anforderungen erfüllen, können Sie Daten parallel auf mehrere Festplatten kopieren, indem Sie eine Instanz dieses Tools auf jedem Computer ausführen.

 Für jede Festplatte, die Sie mit dem Azure Import/Export-Tool vorbereiten, erstellt das Tool eine einzelne Journaldatei. Sie benötigen die Journaldateien von allen Ihren Laufwerken, um den Importauftrag zu erstellen. Die Journaldatei kann auch verwendet werden, um die Vorbereitung des Laufwerks nach Unterbrechung des Tools fortzusetzen.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Syntax des Azure Import/Export-Tools für einen Importauftrag
 Um Laufwerke für einen Importauftrag vorzubereiten, rufen Sie das Azure Import/Export-Tool mit dem Befehl **PrepImport** auf. Welche Parameter Sie verwenden, hängt davon ab, ob dies die erste oder eine nachfolgende Kopiersitzung ist.

 Die erste Kopiersitzung für ein Laufwerk erfordert einige zusätzliche Parameter für folgende Angaben: Speicherkontoschlüssel; Ziellaufwerkbuchstabe; ob das Laufwerk formatiert werden muss; ob das Laufwerk verschlüsselt werden muss – und wenn ja, des BitLocker-Schlüssels – und das Protokollverzeichnis. Hier ist die Syntax für eine erste Kopiersitzung zum Kopieren eines Verzeichnisses oder einer einzelnen Datei:

 **Erste Kopiersitzung zum Kopieren eines einzelnen Verzeichnisses**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Erste Kopiersitzung zum Kopieren einer einzelnen Datei**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 In nachfolgenden Kopiersitzungen müssen Sie nicht die ursprünglichen Parameter angeben. Hier ist die Syntax für eine nachfolgende Kopiersitzung zum Kopieren eines Verzeichnisses oder einer einzelnen Datei:

 **Nachfolgende Kopiersitzungen zum Kopieren eines einzelnen Verzeichnisses**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Nachfolgende Kopiersitzungen zum Kopieren einer einzelnen Datei**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parameter für die erste Kopiersitzung für ein Festplattenlaufwerk
 Bei jeder Ausführung des Azure Import/Export-Tools zum Kopieren von Dateien auf die Festplatte erstellt das Tool eine Kopiersitzung. Jede Kopiersitzung kopiert ein einzelnes Verzeichnis oder eine einzelne Datei auf eine Festplatte. Der Status der Kopiersitzung wird in die Journaldatei geschrieben. Wenn eine Kopiersitzung (z.B. aufgrund eines Systemausfalls) unterbrochen wird, können Sie sie fortsetzen, indem Sie das Tool erneut ausführen und die Journaldatei in der Befehlszeile angeben.

> [!WARNING]
>  Bei Angabe des **/format**-Parameters für die erste Kopiersitzung wird das Laufwerk formatiert, und alle Daten auf dem Laufwerk werden gelöscht. Sie sollten für Ihre Kopiersitzung nur leere Laufwerke verwenden.

 Der Befehl, der für die erste Kopiersitzung für jedes Laufwerk verwendet wird, erfordert andere Parameter als die Befehle für nachfolgende Kopiersitzungen. Die folgende Tabelle enthält die zusätzlichen Parameter, die für die erste Kopiersitzung verfügbar sind:

|Befehlszeilenparameter|BESCHREIBUNG|
|-----------------------------|-----------------|
|**/sk:** &lt;Speicherkontoschlüssel\>|`Optional.` Der Speicherkontoschlüssel für das Speicherkonto, in das die Daten importiert werden. Sie müssen entweder **/sk:** <Speicherkontoschlüssel\> oder **/csas:** <Container-SAS\> in den Befehl einschließen.|
|**/csas:** &lt;Container-SAS\>|`Optional`. Die Container-SAS zum Importieren von Daten in das Speicherkonto. Sie müssen entweder **/sk:** <Speicherkontoschlüssel\> oder **/csas:** <Container-SAS\> in den Befehl einschließen.<br /><br /> Der Wert für diesen Parameter muss mit dem Containernamen gefolgt von einem Fragezeichen (?) und dem SAS-Token beginnen. Beispiel:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Die Berechtigungen, ob in der URL oder in einer gespeicherten Zugriffsrichtlinie angegeben, umfassen Lesen, Schreiben und Löschen für Importaufträge und Lesen, Schreiben und Auflisten für Exportaufträge.<br /><br /> Wenn dieser Parameter angegeben wird, müssen alle Blobs, die importiert oder exportiert werden sollen, sich innerhalb des Containers befinden, der in der SAS angegeben wird.|
|/t:<**Ziellaufwerkbuchstabe**\>|`Required.` Der Laufwerkbuchstabe der Zielfestplatte für die aktuelle Kopiersitzung ohne nachgestellten Doppelpunkt.|
|**/format**|`Optional.` Geben Sie diesen Parameter an, wenn das Laufwerk formatiert werden muss. Andernfalls können Sie ihn auslassen. Bevor das Tool das Laufwerk formatiert, werden Sie von der Konsole aus zur Bestätigung aufgefordert. Um die Bestätigung zu unterdrücken, geben Sie den /silentmode-Parameter an.|
|**/silentmode**|`Optional.` Geben Sie diesen Parameter an, um die Bestätigung für die Formatierung des Ziellaufwerks zu unterdrücken.|
|**/encrypt**|`Optional.` Dieser Parameter wird angegeben, wenn das Laufwerk noch nicht mit BitLocker verschlüsselt wurde und vom Tool verschlüsselt werden muss. Wenn das Laufwerk bereits mit BitLocker verschlüsselt wurde, lassen Sie diesen Parameter aus, und geben Sie den `/bk`-Parameter an, in dem Sie den vorhandenen BitLocker-Schlüssel bereitstellen.<br /><br /> Wenn Sie den `/format`-Parameter angeben, müssen Sie auch den `/encrypt`-Parameter festlegen.|
|**/bk:** &lt;BitLocker-Schlüssel\>|`Optional.` Wenn `/encrypt` angegeben wird, lassen Sie diesen Parameter weg. Wenn `/encrypt` weggelassen wird, müssen Sie das Laufwerk bereits mit BitLocker verschlüsselt haben. Verwenden Sie diesen Parameter, um den BitLocker-Schlüssel anzugeben. Die BitLocker-Verschlüsselung ist für alle Festplatten für Importaufträge erforderlich.|
|**/logdir:** &lt;Protokollverzeichnis\>|`Optional.` Das Protokollverzeichnis ist ein Verzeichnis zum Speichern ausführlicher Protokolle als auch temporärer Manifestdateien. Ohne spezielle Angabe wird das aktuelle Verzeichnis als Protokollverzeichnis verwendet.|

### <a name="parameters-required-for-all-copy-sessions"></a>Für alle Kopiersitzungen erforderliche Parameter
 Die Journaldatei enthält den Status für alle Kopiersitzungen einer Festplatte. Sie enthält außerdem die benötigten Informationen zum Erstellen des Importauftrags. Wenn das Azure Import/Export-Tool für die Sitzung ausgeführt wird, müssen Sie sowohl eine Journaldatei als auch eine Kopiersitzungs-ID angeben:

|||
|-|-|
|Befehlszeilenparameter|BESCHREIBUNG|
|**/j:** &lt;Journaldatei\>|`Required.` Der Pfad zur Journaldatei. Jedes Laufwerk muss über genau eine Journaldatei verfügen. Beachten Sie, dass die Journaldatei sich nicht auf dem Ziellaufwerk befinden darf. Die Journaldatei hat die Erweiterung `.jrn`.|
|**/id:** &lt;Sitzungs-ID\>|`Required.` Die Sitzungs-ID identifiziert eine Kopiersitzung. Sie wird verwendet, um eine exakte Wiederherstellung einer unterbrochenen Kopiersitzung sicherzustellen. Die in einer Kopiersitzung kopierten Dateien werden in einem Verzeichnis gespeichert, das nach der Sitzungs-ID auf dem Ziellaufwerk benannt wird.|

### <a name="parameters-for-copying-a-single-directory"></a>Parameter zum Kopieren eines einzelnen Verzeichnisses
 Beim Kopieren eines einzelnen Verzeichnisses gelten die folgenden erforderlichen und optionalen Parameter:

|Befehlszeilenparameter|BESCHREIBUNG|
|----------------------------|-----------------|
|**/srcdir:** &lt;Quellverzeichnis\>|`Required.` Das Quellverzeichnis, das Dateien enthält, die auf das Ziellaufwerk kopiert werden. Der Verzeichnispfad muss ein absoluter Pfad sein (kein relativer Pfad).|
|**/dstdir:** &lt;virtuelles Zielblobverzeichnis\>|`Required.` Der Pfad zum virtuellen Zielverzeichnis in Ihrem Microsoft Azure-Speicherkonto. Das virtuelle Verzeichnis kann, muss jedoch noch nicht vorhanden sein.<br /><br /> Sie können einen Container oder ein Blobpräfix wie `music/70s/` angeben. Das Zielverzeichnis muss mit dem Containernamen beginnen, gefolgt von einem Schrägstrich „/“, und kann optional ein virtuelles Blobverzeichnis enthalten, das mit „/“ endet.<br /><br /> Wenn der Zielcontainer der Stammcontainer ist, müssen Sie explizit den Stammcontainer angeben – einschließlich des Schrägstrichs, wie `$root/`. Da die Namen von Blobs unter dem Stammcontainer nicht „/“ enthalten können, werden Unterverzeichnisse des Quellverzeichnisses nicht kopiert, wenn das Zielverzeichnis der Stammcontainer ist.<br /><br /> Achten Sie darauf, gültige Containernamen zu verwenden, wenn Sie virtuelle Zielverzeichnisse oder Blobs angeben. Containernamen müssen kleingeschrieben werden. Informationen zu den Benennungsregeln für Container finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).|
|**/Disposition:** <rename&#124;no-overwrite&#124;overwrite> (umbenennen, nicht überschreiben, überschreiben)|`Optional.` Gibt das Verhalten an, wenn ein Blob mit der angegebenen Adresse bereits vorhanden ist. Gültige Werte für diesen Parameter sind: `rename`, `no-overwrite` und `overwrite`. Beachten Sie, dass bei diesen Werten die Groß-/Kleinschreibung beachtet wird. Wenn kein Wert festgelegt ist, wird der Standardwert `rename` verwendet.<br /><br /> Der angegebene Wert für diesen Parameter wirkt sich auf alle Dateien im Verzeichnis aus, die vom `/srcdir`-Parameter angegeben werden.|
|**/BlobType:** <BlockBlob&#124;PageBlob>|`Optional.` Gibt den Blobtyp für die Zielblobs an. Gültige Werte sind `BlockBlob` und `PageBlob`. Beachten Sie, dass bei diesen Werten die Groß-/Kleinschreibung beachtet wird. Wenn kein Wert festgelegt ist, wird der Standardwert `BlockBlob` verwendet.<br /><br /> In den meisten Fällen wird `BlockBlob` empfohlen. Bei Angabe von `PageBlob` muss die Länge der einzelnen Dateien im Verzeichnis ein Vielfaches von 512 betragen, der Größe einer Seite für Seitenblobs.|
|**/PropertyFile:** &lt;Eigenschaftendatei\>|`Optional.` Pfad zur Eigenschaftendatei für die Zielblobs. Weitere Informationen finden Sie unter [Format der Metadaten- und Eigenschaftendatei des Import/Export-Diensts](../storage-import-export-file-format-metadata-and-properties.md).|
|**/MetadataFile:** &lt;Metadatendatei\>|`Optional.` Pfad zur Metadatendatei für die Zielblobs. Weitere Informationen finden Sie unter [Format der Metadaten- und Eigenschaftendatei des Import/Export-Diensts](../storage-import-export-file-format-metadata-and-properties.md).|

### <a name="parameters-for-copying-a-single-file"></a>Parameter zum Kopieren einer einzelnen Datei
 Beim Kopieren einer einzelnen Datei gelten die folgenden erforderlichen und optionalen Parameter:

|Befehlszeilenparameter|BESCHREIBUNG|
|----------------------------|-----------------|
|**/srcfile:** &lt;Quelldatei\>|`Required.` Der vollständige Pfad zu der Datei, die kopiert werden soll. Der Verzeichnispfad muss ein absoluter Pfad sein (kein relativer Pfad).|
|**/dstblob:** &lt;Zielblobpfad&gt;\>|`Required.` Der Pfad zum Zielblob in Ihrem Microsoft Azure-Speicherkonto. Das Blob kann, muss aber noch nicht vorhanden sein.<br /><br /> Geben Sie den Blobnamen beginnend mit dem Containernamen an. Der Blobname darf nicht mit „/“ oder dem Namen des Speicherkontos beginnen. Informationen zu den Benennungsregeln für Blobs finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).<br /><br /> Wenn der Zielcontainer der Stammcontainer ist, müssen Sie explizit `$root` als Container angeben, wie `$root/sample.txt`. Beachten Sie, dass Namen von Blobs unter dem Stammcontainer nicht „/“ enthalten dürfen.|
|**/Disposition:** &lt;rename&#124;no-overwrite&#124;overwrite&gt; (umbenennen, nicht überschreiben, überschreiben)|`Optional.` Gibt das Verhalten an, wenn ein Blob mit der angegebenen Adresse bereits vorhanden ist. Gültige Werte für diesen Parameter sind: `rename`, `no-overwrite` und `overwrite`. Beachten Sie, dass bei diesen Werten die Groß-/Kleinschreibung beachtet wird. Wenn kein Wert festgelegt ist, wird der Standardwert `rename` verwendet.|
|**/BlobType:** <BlockBlob&#124;PageBlob>|`Optional.` Gibt den Blobtyp für die Zielblobs an. Gültige Werte sind `BlockBlob` und `PageBlob`. Beachten Sie, dass bei diesen Werten die Groß-/Kleinschreibung beachtet wird. Wenn kein Wert festgelegt ist, wird der Standardwert `BlockBlob` verwendet.<br /><br /> In den meisten Fällen wird `BlockBlob` empfohlen. Bei Angabe von `PageBlob` muss die Länge der einzelnen Dateien im Verzeichnis ein Vielfaches von 512 betragen, der Größe einer Seite für Seitenblobs.|
|**/PropertyFile:** &lt;Eigenschaftendatei\>|`Optional.` Pfad zur Eigenschaftendatei für die Zielblobs. Weitere Informationen finden Sie unter [Format der Metadaten- und Eigenschaftendatei des Import/Export-Diensts](../storage-import-export-file-format-metadata-and-properties.md).|
|**/MetadataFile:** &lt;Metadatendatei\>|`Optional.` Pfad zur Metadatendatei für die Zielblobs. Weitere Informationen finden Sie unter [Format der Metadaten- und Eigenschaftendatei des Import/Export-Diensts](../storage-import-export-file-format-metadata-and-properties.md).|

### <a name="resuming-an-interrupted-copy-session"></a>Fortsetzen einer unterbrochenen Kopiersitzung
 Wenn eine Kopiersitzung aus beliebigem Grund unterbrochen wird, können Sie sie fortsetzen, indem Sie das Tool nur unter Angabe der Journaldatei ausführen:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Nur die jeweils letzte Kopiersitzung kann fortgesetzt werden, wenn sie fehlerbedingt beendet wurde.

> [!IMPORTANT]
>  Wenn Sie eine Kopiersitzung fortsetzen, ändern Sie nicht die Quelldatendateien und -verzeichnisse durch Hinzufügen oder Entfernen von Dateien.

### <a name="aborting-an-interrupted-copy-session"></a>Abbruch einer unterbrochenen Kopiersitzung
 Wenn eine Kopiersitzung unterbrochen wird, und keine Fortsetzung möglich ist (wenn beispielsweise kein Zugriff auf ein Quellverzeichnis besteht), müssen Sie die aktuelle Sitzung abbrechen, sodass ein Rollback ausgeführt werden kann und neue Kopiersitzungen gestartet werden können:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Nur die letzte Kopiersitzung kann abgebrochen werden, wenn sie fehlerbedingt beendet wurde. Beachten Sie, dass Sie die erste Kopiersitzung für ein Laufwerk nicht abbrechen können. Stattdessen müssen Sie die Kopiersitzung mit einer neuen Journaldatei neu starten.

## <a name="next-steps"></a>Nächste Schritte

* [Einrichten des Azure Import/Export-Tools](storage-import-export-tool-setup-v1.md)
* [Festlegen von Eigenschaften und Metadaten im Rahmen des Importprozesses](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Kurzübersicht über häufig verwendete Befehle](storage-import-export-tool-quick-reference-v1.md) 
* [Überprüfen des Auftragsstatus mit Protokollkopiedateien](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparieren eines Importauftrags](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparieren eines Exportauftrags](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Behandeln von Problemen mit dem Azure Import/Export-Tool](storage-import-export-tool-troubleshooting-v1.md)
