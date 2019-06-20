---
title: Verwenden des Azure Import/Export-Diensts zum Übertragen von Daten in Azure Blob Storage | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Import- und Exportaufträge im Azure-Portal erstellen und Daten von und in Azure Blob Storage übertragen.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ddaead7a0e616b3138dca0b18a58d64e38a46f9e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356426"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Verwenden des Azure Import/Export-Diensts zum Importieren von Daten in Azure Blob Storage

Dieser Artikel enthält schrittweise Anweisungen zum Verwenden des Azure Import/Export-Diensts, um große Datenmengen sicher in Azure Blob Storage zu importieren. Wenn Sie Daten in Azure Blob Storage importieren möchten, müssen Sie als Dienstvoraussetzung verschlüsselte Datenträger mit den Daten an ein Azure-Rechenzentrum senden.  

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Erstellen eines Importauftrags zum Übertragen von Daten in Azure Blob Storage überprüfen Sie sorgfältig die folgende Liste, ob alle Voraussetzungen für diesen Dienst erfüllt sind. Die Voraussetzungen lauten wie folgt:

- Ein aktives Azure-Abonnement, das für den Import/Export-Dienst verwendet werden kann
- Mindestens ein Azure Storage-Konto mit einem Speichercontainer. Hier finden Sie die Liste der [für den Import/Export-Dienst unterstützten Speicherkonten und Speichertypen](storage-import-export-requirements.md). 
    - Weitere Informationen zum Erstellen eines neuen Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](storage-quickstart-create-account.md). 
    - Informationen zu Speichercontainern finden Sie unter [Erstellen eines Speichercontainers](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Eine angemessene Anzahl von Datenträgern der [unterstützten Typen](storage-import-export-requirements.md#supported-disks). 
- Ein Windows-System, auf dem eine [unterstützte Betriebssystemversion](storage-import-export-requirements.md#supported-operating-systems) ausgeführt wird. 
- Aktivierte BitLocker-Verschlüsselung auf dem Windows-System. Lesen Sie hierzu die [Schrittweise Anleitung zur Windows BitLocker-Laufwerkverschlüsselung](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Laden Sie Version 1 von WAImportExport](https://aka.ms/waiev1) auf das Windows-System herunter. Entzippen Sie die Dateien in den Standardordner `waimportexportv1`. Beispiel: `C:\WaImportExportV1`.
- Sie benötigen ein FedEx/DHL-Konto. Wenn Sie einen anderen Spediteur als FedEx/DHL verwenden möchten, wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team.  
    - Das Konto muss gültig sein, es muss Guthaben vorhanden sein und es muss der Rückversand aktiviert sein.
    - Generieren Sie eine Nachverfolgungsnummer für den Exportauftrag.
    - Jeder Auftrag benötigt eine separate Nachverfolgungsnummer. Mehrere Aufträge mit derselben Nachverfolgungsnummer werden nicht unterstützt.
    - Wenn Sie kein Spediteurskonto haben, wechseln Sie zu:
        - [Erstellen eines FedEx-Kontos](https://www.fedex.com/en-us/create-account.html) oder 
        - [Erstellen eines DHL-Kontos](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Schritt 1: Vorbereiten der Laufwerke

Dieser Schritt generiert eine Journaldatei. In der Journaldatei werden grundlegende Informationen wie Laufwerksseriennummer, Verschlüsselungsschlüssel und Speicherkontendetails gespeichert. 

Führen Sie zum Vorbereiten der Laufwerke die folgenden Schritte aus.

1.  Stellen Sie die Verbindung Ihrer Laufwerke mit dem Windows-System über SATA-Anschlüsse her.
1.  Erstellen Sie ein einzelnes NTFS-Volume auf jedem Laufwerk. Weisen Sie dem Volume einen Laufwerkbuchstaben zu. Verwenden Sie keine Bereitstellungspunkte.
2.  Aktivieren Sie die BitLocker-Verschlüsselung auf dem NTFS-Volume. Wenn Sie ein Windows Server-System verwenden, lesen Sie die Anweisungen unter [Aktivieren von BitLocker auf Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopieren Sie Daten in das verschlüsselte Volume. Verwenden Sie Drag & Drop oder Robocopy oder ein ähnliches Kopiertool.
4.  Öffnen Sie ein PowerShell- oder Befehlszeilenfenster mit Administratorrechten. Um das Verzeichnis in den Ordner mit den entzippten Daten zu ändern, führen Sie den folgenden Befehl aus:
    
    `cd C:\WaImportExportV1`
5.  Um den BitLocker-Schlüssel des Laufwerks abzurufen, führen Sie den folgenden Befehl aus:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Um den Datenträger vorzubereiten, führen Sie den folgenden Befehl aus: **Dies kann je nach Größe der Daten mehrere Stunden bis Tage dauern.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite /enablecontentmd5 
    ```
    Eine Journaldatei wird in demselben Ordner erstellt, in dem Sie das Tool ausgeführt haben. Es werden noch zwei weitere Dateien erstellt: eine *XML*-Datei (im Ordner, in dem Sie das Tool ausführen) und die Datei *LaufwerkManifest.xml* (im Ordner, in dem sich die Daten befinden).
    
    Die verwendeten Parameter werden in der folgenden Tabelle beschrieben:

    |Option  |BESCHREIBUNG  |
    |---------|---------|
    |/j:     |Der Name der Journaldatei mit der Erweiterung „.jrn“. Eine Journaldatei wird pro Laufwerk generiert. Es wird empfohlen, die Seriennummer des Datenträgers als Journaldateinamen zu verwenden.         |
    |/id:     |Die Sitzungs-ID. Verwenden Sie für jede Instanz des Befehls eine eindeutige Sitzungsnummer.      |
    |/t:     |Der Laufwerksbuchstabe des auszuliefernden Datenträgers. Beispiel: Laufwerk `D`         |
    |/bk:     |Der BitLocker-Schlüssel für das Laufwerk Sein numerisches Kennwort aus der Ausgabe von `manage-bde -protectors -get D:`      |
    |/srcdir:     |Der Laufwerksbuchstabe des auszuliefernden Datenträgers gefolgt von `:\`. Beispiel: `D:\`.         |
    |/dstdir:     |Der Name des Zielcontainers in Azure Storage.         |
    |/skipwrite:     |Option, die angibt, dass keine neuen Daten kopiert werden müssen und vorhandene Daten auf dem Datenträger vorbereitet werden sollen.          |
    |/enablecontentmd5:     |Wird die Option aktiviert, wird damit sichergestellt, dass MD5 während des Hochladens von Blockblobs in Azure berechnet wird.          |
7. Wiederholen Sie den vorherigen Schritt für jeden Datenträger, der versendet werden muss. Für jede Ausführung an der Befehlszeile wird eine Journaldatei mit dem bereitgestellten Namen erstellt.
    
    > [!IMPORTANT]
    > - Zusammen mit der Journaldatei wird auch eine `<Journal file name>_DriveInfo_<Drive serial ID>.xml`-Datei erstellt, und zwar im gleichen Ordner, in dem sich das Tool befindet. Die XML-Datei wird beim Erstellen eines Auftrags anstelle der Journaldatei verwendet, wenn die Journaldatei zu groß ist. 

## <a name="step-2-create-an-import-job"></a>Schritt 2: Erstellen eines Importauftrags

Führen Sie die folgenden Schritte aus, um einen Importauftrag im Azure-Portal zu erstellen.

1. Melden Sie sich bei https://portal.azure.com/ an.
2. Wechseln Sie zu **Alle Dienste > Speicher > Import-/Exportaufträge**. 
    
    ![Wechseln zu „Import/Exportaufträge“](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicken Sie auf **Import-/Exportauftrag erstellen**.

    ![Auf „Import-/Exportauftrag erstellen“ klicken](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Gehen Sie unter **Grundlegende Einstellungen** wie folgt vor:

   - Wählen Sie **Import in Azure** aus.
   - Geben Sie einen aussagekräftigen Namen für den Importauftrag ein. Verwenden Sie den Namen, um den Fortschritt Ihrer Aufträge zu verfolgen.
       - Der Name darf nur Kleinbuchstaben, Ziffern und Bindestriche enthalten.
       - Der Name muss mit einem Buchstaben beginnen und darf keine Leerzeichen enthalten.
   - Wählen Sie ein Abonnement aus.
   - Wählen Sie eine Ressourcengruppe aus, oder geben Sie eine Gruppe ein.  

     ![Importauftrag erstellen – Schritt 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Gehen Sie unter **Auftragsdetails** wie folgt vor:

    - Laden Sie die Laufwerkjournaldateien hoch, die Sie während der Vorbereitung des Laufwerks erhalten haben. Bei Verwendung von `waimportexport.exe version1` müssen Sie pro vorbereitetem Laufwerk eine Datei hochladen. Wenn die Größe der Journaldatei 2 MB überschreitet, können Sie die `<Journal file name>_DriveInfo_<Drive serial ID>.xml`-Datei verwenden, die zusammen mit der Journaldatei erstellt wurde. 
    - Wählen Sie das Zielspeicherkonto aus, in dem Daten gespeichert werden sollen. 
    - Der Ablageort wird automatisch basierend auf der Region des ausgewählten Speicherkontos mit Daten aufgefüllt.
   
   ![Importauftrag erstellen – Schritt 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Gehen Sie unter **Informationen für Rücksendung** wie folgt vor:

   - Wählen Sie den Spediteur in der Dropdownliste aus. Wenn Sie einen anderen Spediteur als FedEx/DHL beauftragen möchten, wählen Sie eine der Optionen in der Dropdownliste aus. Wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team, und informieren Sie es über den von Ihnen vorgesehenen Spediteur.
   - Geben Sie eine gültige Spediteurkontonummer ein, die Sie mit diesem Spediteur erstellt haben. Microsoft verwendet dieses Konto, um die Laufwerke nach Abschluss des Importauftrags an Sie zurückzuschicken. Wenn Sie keine Kontonummer haben, erstellen Sie ein [FedEx](https://www.fedex.com/us/oadr/)- oder [DHL](https://www.dhl.com/)-Spediteurkonto.
   - Geben Sie vollständige und gültige Kontaktdaten an: Name, Telefonnummer, E-Mail-Adresse, Straße, Stadt, PLZ, Bundesstaat/Provinz und Land/Region. 
        
       > [!TIP] 
       > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.

     ![Importauftrag erstellen – Schritt 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. Gehen Sie unter **Zusammenfassung** wie folgt vor:

   - Überprüfen Sie die in der Zusammenfassung bereitgestellten Informationen zum Auftrag. Notieren Sie sich den Namen des Auftrags und die Versandadresse des Azure-Rechenzentrums, damit Sie Datenträger an Azure zurücksenden können. Diese Informationen werden später auf dem Adressetikett verwendet.
   - Klicken Sie auf **OK**, um den Importauftrag zu erstellen.

     ![Importauftrag erstellen – Schritt 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Schritt 3: Versenden der Laufwerke 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Schritt 4: Aktualisieren des Auftrags mit Nachverfolgungsinformationen

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Schritt 5: Überprüfen des Datenuploads in Azure

Überwachen Sie den Auftrag bis zu seinem Abschluss. Sobald der Auftrag abgeschlossen ist, überprüfen Sie, ob Ihre Daten in Azure hochgeladen wurden. Löschen Sie die lokalen Daten erst, wenn Sie überprüft haben, dass die Daten erfolgreich hochgeladen wurden.

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen von Auftrags- und Laufwerkstatus](storage-import-export-view-drive-status.md)
* [Überprüfen der Import/Export-Anforderungen](storage-import-export-requirements.md)


