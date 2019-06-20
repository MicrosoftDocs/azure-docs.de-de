---
title: Verwenden von Azure Import/Export zum Exportieren von Daten aus Azure-Blob | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Exportaufträge im Azure-Portal erstellen und Daten aus Azure-Blobs übertragen.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e542ad59f6fd64b52aef9438ed0f646e9e36fc4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209625"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Verwenden des Azure Import/Export-Diensts zum Exportieren von Daten aus Azure Blob Storage
Dieser Artikel enthält ausführliche Anweisungen zum Verwenden des Azure Import/Export-Diensts, um große Datenmengen sicher aus Azure Blob Storage zu exportieren. Für diesen Dienst müssen Sie leere Laufwerke an das Azure-Rechenzentrum senden. Der Dienst exportiert Daten aus Ihrem Speicherkonto auf die Laufwerke und versendet diese dann zurück.

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Erstellen eines Exportauftrags zum Übertragen von Daten aus Azure Blob Storage überprüfen Sie sorgfältig anhand der folgenden Liste, ob alle Voraussetzungen für diesen Dienst erfüllt sind. Die Voraussetzungen lauten wie folgt:

- Ein aktives Azure-Abonnement, das für den Import/Export-Dienst verwendet werden kann
- Mindestens ein Azure Storage-Konto Hier finden Sie die Liste der [für den Import/Export-Dienst unterstützten Speicherkonten und Speichertypen](storage-import-export-requirements.md). Weitere Informationen zum Erstellen eines neuen Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](storage-quickstart-create-account.md).
- Eine angemessene Anzahl von Datenträgern der [unterstützten Typen](storage-import-export-requirements.md#supported-disks)
- Sie benötigen ein FedEx/DHL-Konto. Wenn Sie einen anderen Spediteur als FedEx/DHL verwenden möchten, wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team. 
    - Das Konto muss gültig sein, es muss Guthaben vorhanden sein und es muss der Rückversand aktiviert sein.
    - Generieren Sie eine Nachverfolgungsnummer für den Exportauftrag.
    - Jeder Auftrag benötigt eine separate Nachverfolgungsnummer. Mehrere Aufträge mit derselben Nachverfolgungsnummer werden nicht unterstützt. 
    - Wenn Sie kein Spediteurskonto haben, wechseln Sie zu:
        - [Erstellen eines FedEx-Kontos](https://www.fedex.com/en-us/create-account.html) oder 
        - [Erstellen eines DHL-Kontos](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Schritt 1: Erstellen eines Exportauftrags

Führen Sie die folgenden Schritte aus, um im Azure-Portal einen Exportauftrag zu erstellen.

1. Melden Sie sich bei https://portal.azure.com/ an.
2. Wechseln Sie zu **Alle Dienste > Speicher > Import-/Exportaufträge**. 

    ![Wechseln zu „Import/Exportaufträge“](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Klicken Sie auf **Import-/Exportauftrag erstellen**.

    ![Klicken auf „Import-/Exportauftrag“](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. Gehen Sie unter **Grundlegende Einstellungen** wie folgt vor:
    
    - Wählen Sie **Export aus Azure** aus. 
    - Geben Sie einen beschreibenden Namen für den Exportauftrag ein. Verwenden Sie den gewählten Namen, um den Fortschritt Ihrer Aufträge zu verfolgen. 
        - Der Name darf nur Kleinbuchstaben, Zahlen, Bindestriche und Unterstriche enthalten.
        - Der Name muss mit einem Buchstaben beginnen und darf keine Leerzeichen enthalten. 
    - Wählen Sie ein Abonnement aus.
    - Wählen Sie eine Ressourcengruppe aus, oder geben Sie eine Gruppe ein.

        ![Grundlagen](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. Gehen Sie unter **Auftragsdetails** wie folgt vor:

    - Wählen Sie das Speicherkonto aus, das die zu exportierenden Daten enthält. Verwenden Sie ein Speicherkonto in der Nähe Ihres Standorts.
    - Der Ablageort wird automatisch basierend auf der Region des ausgewählten Speicherkontos mit Daten aufgefüllt. 
    - Geben Sie die Blobdaten an, die Sie von Ihrem Speicherkonto auf Ihr leeres Laufwerk bzw. Ihre leeren Laufwerke exportieren möchten. 
    - Wählen Sie für die Blobdaten im Speicherkonto **Alle exportieren** aus.
    
         ![Alle exportieren](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Sie können angeben, welche Container und Blobs exportiert werden sollen.
        - **So geben Sie ein zu exportierendes Blob an**: Verwenden Sie den Selektor **Gleich**. Geben Sie den relativen Pfad zum Blob, beginnend mit dem Containernamen, an. Verwenden Sie *$root* , um den Stammcontainer festzulegen.
        - **So geben Sie alle Blobs an, die mit einem Präfix beginnen**: Verwenden Sie den Selektor **Beginnt mit**. Geben Sie das Präfix beginnend mit einem Schrägstrich „/“ an. Bei dem Präfix kann es sich um das Präfix des Containernamens, den vollständigen Containernamen oder den vollständigen Containernamen gefolgt vom Präfix des Blob-Namens handeln. Sie müssen die Blobpfade in einem gültigen Format angeben, um Fehler während der Verarbeitung zu vermeiden. Dies ist in diesem Screenshot dargestellt. Weitere Informationen finden Sie unter [Beispiele für gültige Blobpfade](#examples-of-valid-blob-paths). 
   
           ![Exportieren ausgewählter Container und Blobs](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Sie können den Export aus der Bloblistendatei ausführen.

        ![Exportieren aus der Bloblistendatei](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Wenn das zu exportierende Blob während des Kopierens der Daten verwendet wird, erstellt der Azure Import/Export-Dienst eine Momentaufnahme des Blobs und kopiert die Momentaufnahme.
 

4. Gehen Sie unter **Informationen für Rücksendung** wie folgt vor:

    - Wählen Sie den Spediteur in der Dropdownliste aus. Wenn Sie einen anderen Spediteur als FedEx/DHL beauftragen möchten, wählen Sie eine der Optionen in der Dropdownliste aus. Wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team, und informieren Sie es über den von Ihnen vorgesehenen Spediteur.
    - Geben Sie eine gültige Spediteurkontonummer ein, die Sie mit diesem Spediteur erstellt haben. Microsoft verwendet dieses Konto, um die Laufwerke nach Abschluss des Exportauftrags an Sie zurückzuschicken. 
    - Geben Sie vollständige und gültige Kontaktdaten an: Name, Telefonnummer, E-Mail-Adresse, Straße, Stadt, PLZ, Bundesstaat/Provinz und Land/Region.

        > [!TIP] 
        > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.
   
5. Gehen Sie in **Zusammenfassung** wie folgt vor:

    - Überprüfen Sie die Details zum Auftrag.
    - Notieren Sie den Auftragsnamen und die angegebene Lieferadresse des Azure-Rechenzentrums für das Senden der Datenträger an Azure. 

        > [!NOTE] 
        > Senden Sie die Datenträger immer an das im Azure-Portal angegebene Rechenzentrum. Wenn die Datenträger an das falsche Rechenzentrum versendet werden, wird der Auftrag nicht verarbeitet.

    - Klicken Sie auf **OK**, um das Erstellen des Exportauftrags abzuschließen.

## <a name="step-2-ship-the-drives"></a>Schritt 2: Versenden der Laufwerke

Wenn Sie die Anzahl der benötigten Laufwerke nicht kennen, fahren Sie mit [Überprüfen der Anzahl der Laufwerke](#check-the-number-of-drives) fort. Wenn Sie die Anzahl der Laufwerke kennen, fahren Sie mit dem Versenden der Laufwerke fort.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Schritt 3: Aktualisieren des Auftrags mit Nachverfolgungsinformationen

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Schritt 4: Empfangen der Datenträger
Wenn im Dashboard angegeben wird, dass der Auftrag abgeschlossen wurde, werden die Laufwerke an Sie versendet, und die Nachverfolgungsnummer für die Lieferung ist im Portal verfügbar.

1. Wenn Sie die Laufwerke mit exportierten Daten erhalten haben, benötigen Sie die BitLocker-Schlüssel, um die Laufwerke zu entsperren. Wechseln Sie im Azure-Portal zum Exportauftrag. Klicken auf die Registerkarte **Import/Export**. 
2. Wählen Sie Ihren Exportauftrag in der Liste aus, und klicken Sie darauf. Wechseln Sie zu **BitLocker-Schlüssel**, und kopieren Sie die Schlüssel.
   
   ![BitLocker-Schlüssel für einen Exportauftrag anzeigen](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Verwenden Sie die BitLocker-Schlüssel zum Entsperren der Datenträger.

Der Export ist abgeschlossen. Zu diesem Zeitpunkt können Sie den Auftrag löschen. Nach 90 Tagen wird er automatisch gelöscht.


## <a name="check-the-number-of-drives"></a>Überprüfen der Anzahl der Laufwerke

Mit diesem *optionalen* Schritt können Sie die Anzahl der Laufwerke, die für den Exportauftrag erforderlich ist, ermitteln. Führen Sie diesen Schritt auf einem Windows-System aus, auf dem eine [unterstützte Betriebssystemversion](storage-import-export-requirements.md#supported-operating-systems) ausgeführt wird.

1. [Laden Sie Version 1 von WAImportExport](https://aka.ms/waiev1) auf das Windows-System herunter. 
2. Entzippen Sie die Dateien in den Standardordner `waimportexportv1`. Beispiel: `C:\WaImportExportV1`.
3. Öffnen Sie ein PowerShell- oder Befehlszeilenfenster mit Administratorrechten. Um das Verzeichnis in den Ordner mit den entzippten Daten zu ändern, führen Sie den folgenden Befehl aus:
    
    `cd C:\WaImportExportV1`

4. Um die erforderliche Anzahl von Datenträgern für die ausgewählten Blobs zu überprüfen, führen Sie den folgenden Befehl aus:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Die Parameter werden in der folgenden Tabelle beschrieben:
    
    |Befehlszeilenparameter|BESCHREIBUNG|  
    |--------------------------|-----------------|  
    |**/logdir:**|Optional. Das Protokollverzeichnis. In dieses Verzeichnis werden ausführliche Protokolldateien geschrieben. Ohne spezielle Angabe wird das aktuelle Verzeichnis als Protokollverzeichnis verwendet.|  
    |**/sn:**|Erforderlich. Der Name des Speicherkontos für den Exportauftrag.|  
    |**/sk:**|Nur erforderlich, wenn keine Container-SAS angegeben wurde. Der Kontoschlüssel des Speicherkontos für den Exportauftrag.|  
    |**/csas:**|Nur erforderlich, wenn kein Speicherkontoschlüssel angegeben wurde. Die Container-SAS zum Auflisten der Blobs, die im Exportauftrag exportiert werden sollen.|  
    |**/ExportBlobListFile:**|Erforderlich. Pfad zur XML-Datei mit der Liste der Blobpfade oder Blobpfadpräfixe für die zu exportierenden Blobs. Das Dateiformat entspricht dem Format, das im Element `BlobListBlobPath` im [Put Job](/rest/api/storageimportexport/jobs)-Vorgang der REST-API des Import/Export-Diensts verwendet wird.|  
    |**/DriveSize:**|Erforderlich. Die Größe der Laufwerke, die für einen Exportauftrag verwendet werden sollen, *beispielsweise* 500 GB, 1,5 TB.|  

    Weitere Informationen finden Sie unter [Beispiel für den Befehl PreviewExport](#example-of-previewexport-command).
 
5. Überprüfen Sie, ob Sie Lese-/Schreibzugriff auf die Laufwerke haben, die für den Exportauftrag versendet werden sollen.

### <a name="example-of-previewexport-command"></a>Beispiel für den Befehl PreviewExport

Das folgende Beispiel veranschaulicht die Verwendung des Befehls `PreviewExport`:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Die Datei mit der Blobliste für den Export kann wie hier gezeigt Blobnamen und Blobpräfixe enthalten:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Das Azure Import/Export-Tool listet alle zu exportierenden Blobs auf, berechnet unter Berücksichtigung des notwendigen Mehraufwands, wie diese in Laufwerke der angegebenen Größe gepackt werden, und schätzt dann die erforderliche Anzahl von Laufwerken für die Blobs sowie Informationen zur Laufwerknutzung.  
  
Hier sehen Sie ein Beispiel der Ausgabe ohne Informationsprotokolle:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>Beispiele für gültige Blobpfade

Die folgende Tabelle enthält Beispiele für gültige Blob-Pfade:
   
   | Auswahl | Blob-Pfad | BESCHREIBUNG |
   | --- | --- | --- |
   | Starts With |/ |Exportiert alle Blobs im Speicherkonto |
   | Starts With |/$root/ |Exportiert alle Blobs im Stammcontainer |
   | Starts With |/book |Exportiert alle Blobs in allen Containern mit dem Präfix **book** |
   | Starts With |/music/ |Exportiert alle Blobs im Container **music** |
   | Starts With |/music/love |Exportiert alle Blobs im Container **music**, die mit dem Präfix **love** beginnen |
   | Equal To |$root/logo.bmp |Exportiert das Blob **logo.bmp** im Stammcontainer |
   | Equal To |videos/story.mp4 |Exportiert das Blob **story.mp4** im Container **videos** |

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen von Auftrags- und Laufwerkstatus](storage-import-export-view-drive-status.md)
* [Überprüfen der Import/Export-Anforderungen](storage-import-export-requirements.md)


