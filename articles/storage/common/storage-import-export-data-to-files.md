---
title: Verwenden des Azure Import/Export-Diensts zum Übertragen von Daten in Azure Files | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Import- und Exportaufträge im Azure-Portal erstellen, um Daten in Azure Files zu übertragen.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 28026a429643c62434ddfd7591126169857a7371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478992"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Verwenden des Azure Import/Export-Diensts zum Importieren von Daten in Azure Files

Dieser Artikel enthält schrittweise Anweisungen zur Verwendung des Azure Import/Export-Diensts, um große Datenmengen sicher in Azure Files zu importieren. Zum Importieren von Daten erfordert der Dienst, dass Sie unterstützte Datenträger versenden, die die Daten enthalten, die im Azure-Rechenzentrum gespeichert werden sollen.  

Der Import/Export-Dienst unterstützt nur den Import von Azure Files zu Azure Storage. Das Exportieren von Azure Files wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Erstellen eines Importauftrags zum Übertragen von Daten in Azure Files überprüfen Sie sorgfältig die folgende Liste der Voraussetzungen, und führen Sie sie aus. Die Voraussetzungen lauten wie folgt:

- Ein aktives Azure-Abonnement zur Verwendung mit dem Import/Export-Dienst.
- Mindestens ein Azure Storage-Konto. Hier finden Sie die Liste der [für den Import/Export-Dienst unterstützten Speicherkonten und Speichertypen](storage-import-export-requirements.md). Weitere Informationen zum Erstellen eines neuen Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](storage-quickstart-create-account.md).
- Eine angemessene Anzahl von Datenträgern der [unterstützten Typen](storage-import-export-requirements.md#supported-disks) 
- Ein Windows-System, auf dem eine [unterstützte Betriebssystemversion](storage-import-export-requirements.md#supported-operating-systems) ausgeführt wird.
- [Laden Sie Version 2 von WAImportExport](https://aka.ms/waiev2) auf das Windows-System herunter. Entzippen Sie die Dateien in den Standardordner `waimportexport`. Beispiel: `C:\WaImportExport`.
- Sie benötigen ein FedEx/DHL-Konto. Wenn Sie einen anderen Spediteur als FedEx/DHL verwenden möchten, wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team.  
    - Das Konto muss gültig sein, es muss Guthaben vorhanden sein und es muss der Rückversand aktiviert sein.
    - Generieren Sie eine Nachverfolgungsnummer für den Exportauftrag.
    - Jeder Auftrag benötigt eine separate Nachverfolgungsnummer. Mehrere Aufträge mit derselben Nachverfolgungsnummer werden nicht unterstützt.
    - Wenn Sie kein Spediteurskonto haben, wechseln Sie zu:
        - [Erstellen eines FedEx-Kontos](https://www.fedex.com/en-us/create-account.html) oder 
        - [Erstellen eines DHL-Kontos](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>Schritt 1: Vorbereiten der Laufwerke

Dieser Schritt generiert eine Journaldatei. In der Journaldatei werden grundlegende Informationen wie Laufwerksseriennummer, Verschlüsselungsschlüssel und Speicherkontendetails gespeichert.

Führen Sie die folgenden Schritte zum Vorbereiten der Laufwerke aus.

1. Stellen Sie die Verbindung unserer Laufwerke mit dem Windows-System über SATA-Anschlüsse her.
2. Erstellen Sie ein einzelnes NTFS-Volume auf jedem Laufwerk. Weisen Sie dem Volume einen Laufwerkbuchstaben zu. Verwenden Sie keine Bereitstellungspunkte.
3. Ändern Sie die Datei *dataset.csv* im Stammverzeichnis, in dem sich das Tool befindet. Je nachdem, ob Sie eine Datei, einen Ordner oder beides importieren möchten, fügen Sie der *dataset.csv*-Datei Einträge wie in den folgenden Beispielen hinzu.  

   - **So importieren Sie eine Datei**: Im folgenden Beispiel befinden sich die zu kopierenden Daten auf dem Laufwerk „C:“. Die Datei *MyFile1.txt* wird auf das Stammverzeichnis von *MyAzureFileshare1* kopiert. Wenn *MyAzureFileshare1* nicht vorhanden ist, wird es im Azure Storage-Konto erstellt. Die Ordnerstruktur wird beibehalten.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
       ```
   - **So importieren Sie einen Ordner**: Alle Dateien und Ordner in *MyFolder2* werden rekursiv in die Dateifreigabe kopiert. Die Ordnerstruktur wird beibehalten.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
       ```
     Mehrere Einträge können gemäß importierter Ordner oder Dateien in derselben Datei vorgenommen werden. 

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
       ```
     Erfahren Sie mehr über das [Vorbereiten der Dataset-CSV-Datei](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Ändern Sie die Datei *driveset.csv* im Stammverzeichnis, in dem sich das Tool befindet. Fügen Sie Einträge in der Datei *driveset.csv* wie in den folgenden Beispielen hinzu. Die Driveset-Datei enthält die Liste der Datenträger und die entsprechenden Laufwerkbuchstaben, damit das Tool die Liste der vorzubereitenden Datenträger richtig auswählen kann.

    In diesem Beispiel wird vorausgesetzt, dass zwei Festplatten angeschlossen sind und die grundlegenden NTFS-Volumes G:\ und H:\ erstellt werden. H:\ wird nicht verschlüsselt, während G:\ bereits verschlüsselt ist. Das Tool formatiert und verschlüsselt nur den Datenträger, der H:\ hostet (und nicht G:\).

   - **Für einen Datenträger, die nicht verschlüsselt ist**: Geben Sie *Encrypt* zum Aktivieren der BitLocker-Verschlüsselung auf dem Datenträger an.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```
    
   - **Für einen Datenträger, der bereits verschlüsselt ist**: Geben Sie *AlreadyEncrypted* und den BitLocker-Schlüssel an.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Mehrere Einträge können gemäß mehrerer Laufwerke in derselben Datei vorgenommen werden. Erfahren Sie mehr über das [Vorbereiten der Driveset-CSV-Datei](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5. Verwenden der Option `PrepImport` zum Kopieren und Vorbereiten von Daten auf dem Datenträger. Führen Sie für die erste Kopiersitzung zum Kopieren von Verzeichnissen und/oder Dateien mit einer neuen Kopiersitzung folgenden Befehl aus:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Ein Importbeispiel ist nachfolgend dargestellt.
  
       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```
 
6. Für jede Ausführung der Befehlszeile wird eine Journaldatei mit dem von Ihnen angegebenen Namen mit Parameter `/j:` erstellt. Jedes Laufwerk, das Sie vorbereiten, hat eine Journaldatei, die hochgeladen werden muss, wenn Sie den Importauftrag erstellen. Laufwerke ohne Journaldateien werden nicht verarbeitet.

    > [!IMPORTANT]
    > - Ändern Sie die Daten auf den Datenträgern oder die Journaldatei nicht mehr, nachdem Sie die Festplattenvorbereitung abgeschlossen haben.

Zusätzliche Beispiele finden Sie unter [Beispiele für Journaldateien](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Schritt 2: Erstellen eines Importauftrags 

Führen Sie die folgenden Schritte aus, um einen Importauftrag im Azure-Portal zu erstellen.
1. Melden Sie sich bei https://portal.azure.com/ an.
2. Wechseln Sie zu **Alle Dienste > Speicher > Import-/Exportaufträge**. 

    ![Zu „Import/Export“ wechseln](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicken Sie auf **Import-/Exportauftrag erstellen**.

    ![Klicken auf „Import-/Exportauftrag“](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Gehen Sie unter **Grundlegende Einstellungen** wie folgt vor:

    - Wählen Sie **Import in Azure** aus.
    - Geben Sie einen aussagekräftigen Namen für den Importauftrag ein. Verwenden Sie diesen Namen, um Ihre Aufträge während und nach der Bearbeitung nachzuverfolgen.
        -  Dieser Name darf nur Kleinbuchstaben, Zahlen, Bindestriche und Unterstriche enthalten.
        -  Der Name muss mit einem Buchstaben beginnen und darf keine Leerzeichen enthalten. 
    - Wählen Sie ein Abonnement aus.
    - Wählen Sie eine Ressourcengruppe aus. 

        ![Importauftrag erstellen – Schritt 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Gehen Sie unter **Auftragsdetails** wie folgt vor:
    
    - Laden Sie die Journaldateien hoch, die Sie im vorhergehenden [Schritt 1: Vorbereiten der Laufwerke](#step-1-prepare-the-drives). 
    - Wählen Sie das Speicherkonto aus, in das die Daten importiert werden. 
    - Der Ablageort wird automatisch basierend auf der Region des ausgewählten Speicherkontos mit Daten aufgefüllt.
   
       ![Importauftrag erstellen – Schritt 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. In **Informationen für Rücksendung**:

    - Wählen Sie den Spediteur in der Dropdownliste aus. Wenn Sie einen anderen Spediteur als FedEx/DHL beauftragen möchten, wählen Sie eine der Optionen in der Dropdownliste aus. Wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team, und informieren Sie es über den von Ihnen vorgesehenen Spediteur.
    - Geben Sie eine gültige Spediteurkontonummer ein, die Sie mit diesem Spediteur erstellt haben. Microsoft verwendet dieses Konto, um die Laufwerke nach Abschluss des Importauftrags an Sie zurückzuschicken. 
    - Geben Sie vollständige und gültige Kontaktdaten an: Name, Telefonnummer, E-Mail-Adresse, Straße, Stadt, PLZ, Bundesstaat/Provinz und Land/Region.

        > [!TIP] 
        > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.

       ![Importauftrag erstellen – Schritt 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. Gehen Sie unter **Zusammenfassung** wie folgt vor:

    - Geben Sie die Lieferadresse des Azure-Rechenzentrums für die Rücksendung der Datenträger an Azure an. Stellen Sie sicher, dass der Auftragsname und die vollständige Adresse auf dem Adressetikett angegeben sind.
    - Klicken Sie auf **OK**, um das Erstellen des Importauftrags abzuschließen.

        ![Importauftrag erstellen – Schritt 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Schritt 3: Versenden der Laufwerke an das Azure-Datencenter 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Schritt 4: Aktualisieren des Auftrags mit Nachverfolgungsinformationen

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Schritt 5: Überprüfen des Datenuploads in Azure

Überwachen Sie den Auftrag bis zu seinem Abschluss. Sobald der Auftrag abgeschlossen ist, überprüfen Sie, ob Ihre Daten in Azure hochgeladen wurden. Löschen Sie die lokalen Daten erst, wenn Sie überprüft haben, dass die Daten erfolgreich hochgeladen wurden.

## <a name="samples-for-journal-files"></a>Beispiele für Journaldateien

Um **weitere Laufwerke hinzuzufügen**, erstellen Sie eine neue Driveset-Datei, und führen Sie den Befehl wie unten beschrieben aus. 

Geben Sie für nachfolgende Kopiersitzungen mit anderen Laufwerken als den in der Datei *InitialDriveset.csv* angegebenen eine neue Driveset-*CSV*-Datei an, und legen Sie sie als Wert für den Parameter `AdditionalDriveSet` fest. Verwenden Sie den **gleichen Journaldateinamen**, und geben Sie eine **neue Sitzungs-ID** an. Das Format der AdditionalDriveset-CSV-Datei ist mit dem InitialDriveSet-Format identisch.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Ein Importbeispiel ist nachfolgend dargestellt.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Um demselben Driveset zusätzliche Daten hinzuzufügen, verwenden Sie den PrepImport-Befehl für die nachfolgenden Kopiersitzungen, um zusätzliche Dateien/Verzeichnisse zu kopieren.

Geben Sie für nachfolgende Kopiersitzungen für Kopiervorgänge auf dieselben in der Datei *InitialDriveset.csv* angegebenen Festplatten den **gleichen Journaldateinamen** und eine **neue Sitzungs-ID** an. Es ist nicht erforderlich, den Speicherkontoschlüssel anzugeben.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Ein Importbeispiel ist nachfolgend dargestellt.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen von Auftrags- und Laufwerkstatus](storage-import-export-view-drive-status.md)
* [Überprüfen der Import/Export-Anforderungen](storage-import-export-requirements.md)


