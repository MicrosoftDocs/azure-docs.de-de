---
title: Tutorial zum Kopieren von Daten über SMB auf die Azure Data Box | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Daten über SMB in Ihre Azure Data Box kopieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: alkohli
ms.openlocfilehash: 6b2a0655173405008e0bccf3e31a8db391da6127
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496288"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Tutorial: Kopieren von Daten in eine Azure Data Box über SMB

In diesem Tutorial wird beschrieben, wie Sie über die lokale Webbenutzeroberfläche eine Verbindung mit Ihrem Hostcomputer herstellen und Daten kopieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Voraussetzungen
> * Herstellen einer Verbindung mit der Data Box
> * Kopieren von Daten auf die Data Box


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben die Schritte unter [Tutorial: Einrichten der Azure Data Box](data-box-deploy-set-up.md) ausgeführt.
2. Sie haben Ihre Data Box erhalten, und die Bestellung wird im Portal mit dem Status **Übermittelt** angezeigt.
3. Sie haben einen Hostcomputer mit den Daten, die Sie auf die Data Box kopieren möchten. Für Ihren Hostcomputer müssen die folgenden Bedingungen erfüllt sein:
    - Es muss ein [unterstütztes Betriebssystem](data-box-system-requirements.md) ausgeführt werden.
    - Er muss mit einem Hochgeschwindigkeitsnetzwerk verbunden sein. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen. Falls keine 10-GbE-Verbindung verfügbar ist, verwenden Sie eine 1-GbE-Datenverbindung, die Geschwindigkeit der Kopiervorgänge wird dadurch jedoch beeinträchtigt.

## <a name="connect-to-data-box"></a>Herstellen einer Verbindung mit der Data Box

Je nach ausgewähltem Speicherkonto erstellt Data Box bis zu:
- Drei Freigaben für jedes verknüpfte Speicherkonto für GPv1 und GPv2
- Eine Freigabe für Storage Premium
- Eine Freigabe für das Blob Storage-Konto

Unter Blockblob- und Seitenblobfreigaben sind Entitäten der ersten Ebene Container, und Entitäten der zweiten Ebene sind Blobs. Unter Freigaben für Azure Files sind Entitäten erster Ebene Freigaben. Entitäten zweiter Ebene sind Dateien.

In der folgenden Tabelle sind der UNC-Pfad zu den Freigaben auf Ihrer Data Box und die Azure Storage-Pfad-URL aufgeführt, wohin die Daten hochgeladen werden. Die endgültige URL des Azure Storage-Pfads kann aus dem UNC-Freigabepfad abgeleitet werden.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-Blockblobs | <li>UNC-Pfad zu den Freigaben: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-Seitenblobs  | <li>UNC-Pfad zu den Freigaben: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-Pfad zu den Freigaben: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Wenn Sie einen Windows Server-Hostcomputer verwenden, führen Sie die folgenden Schritte aus, um eine Verbindung mit der Data Box herzustellen.

1. Zunächst müssen Sie sich authentifizieren und eine Sitzung starten. Navigieren Sie zu **Verbindung herstellen und Daten kopieren**. Klicken Sie auf **Get credentials** (Anmeldeinformationen abrufen), um die Anmeldeinformationen für den Zugriff auf die mit Ihrem Speicherkonto verknüpften Freigaben abzurufen. 

    ![Abrufen der Anmeldeinformationen für Freigaben 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Kopieren Sie im Dialogfeld „Auf Freigabe zugreifen und Daten kopieren“ den **Benutzernamen** und das **Kennwort** für die Freigabe. Klicken Sie auf **OK**.
    
    ![Abrufen der Anmeldeinformationen für Freigaben 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Um über Ihren Hostcomputer auf die Freigaben zuzugreifen, die mit Ihrem Speicherkonto (*devicemanagertest1* im folgenden Beispiel) verknüpft sind, öffnen Sie ein Befehlsfenster. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Abhängig von Ihrem Datenformat lauten die Freigabepfade wie folgt:
    - Azure-Blockblob: `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Azure-Seitenblob: `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Azure-Dateien: `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Geben Sie das Kennwort für die Freigabe ein, wenn Sie dazu aufgefordert werden. Das folgende Beispiel zeigt das Herstellen einer Verbindung mit einer Freigabe über den obigen Befehl.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Drücken Sie WINDOWS-TASTE+R. Geben Sie im Fenster **Ausführen** die `\\<device IP address>` an. Klicken Sie auf **OK**, um den Datei-Explorer zu öffnen.
    
    ![Herstellen einer Verbindung mit der Freigabe über den Datei-Explorer 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Die Freigaben sollten jetzt als Ordner angezeigt werden.
    
    ![Herstellen einer Verbindung mit der Freigabe über den Datei-Explorer 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Erstellen Sie immer einen Ordner für die Dateien, die Sie unter die Freigabe kopieren möchten, und kopieren Sie die Dateien dann in diesen Ordner**. Der Ordner, der unter der Blockblob- und der Seitenblob Freigabe erstellt wurde, entspricht einem Container, in den Daten als Blobs hochgeladen werden. Es ist nicht möglich, Dateien direkt in den *root*-Ordner im Speicherkonto zu kopieren.
    
Wenn Sie einen Linux-Client verwenden, stellen Sie mit folgendem Code die SMB-Freigabe bereit. Der unten angegebene Parameter „vers“ ist die Version des SMB, den Ihr Linux-Host unterstützt. Geben Sie die entsprechende Version in den folgenden Befehl ein. Von Data Box unterstützte SMB-Versionen finden Sie unter [Unterstützte Dateisysteme für Linux-Clients](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients). 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Kopieren von Daten auf die Data Box

Nachdem Sie eine Verbindung mit den Data Box-Freigaben hergestellt haben, kopieren Sie im nächsten Schritt Ihre Daten. Bevor Sie mit dem Kopieren der Daten beginnen, sollten Sie folgende Aspekte beachten:

- Stellen Sie sicher, dass Sie die Daten in Freigaben kopieren, die das richtige Datenformat aufweisen. Kopieren Sie beispielsweise die Blockblobdaten in die Freigabe für Blockblobs. Kopieren Sie die VHDs in einen Seitenblob. Wenn das Datenformat nicht mit dem entsprechenden Freigabetyp übereinstimmt, tritt später beim Hochladen der Daten in Azure ein Fehler auf.
-  Stellen Sie beim Kopieren der Daten sicher, dass für die Datengröße die Größenbeschränkungen eingehalten werden, die im Artikel zu den [Grenzwerten für Azure Storage und Data Box](data-box-limits.md) beschrieben sind.
- Falls von Data Box hochgeladene Daten gleichzeitig von anderen Anwendungen außerhalb von Data Box hochgeladen werden, kann dies zu Fehlern bei Uploadaufträgen und zu Datenbeschädigungen führen.
- Wir empfehlen Folgendes:
    - Sie verwenden SMB und NFS nicht gleichzeitig.
    - Kopieren Sie die gleichen Daten in das gleiche Endziel in Azure. 
     
  In diesen Fällen lässt sich das endgültige Ergebnis nicht im Vorhinein bestimmen.
- Erstellen Sie immer einen Ordner für die Dateien, die Sie unter die Freigabe kopieren möchten, und kopieren Sie die Dateien dann in diesen Ordner. Der Ordner, der unter der Blockblob- und der Seitenblob Freigabe erstellt wurde, entspricht einem Container, in den die Daten als Blobs hochgeladen werden. Es ist nicht möglich, Dateien direkt in den *root*-Ordner im Speicherkonto zu kopieren.

Nachdem Sie eine Verbindung mit der SMB-Freigabe hergestellt haben, beginnen Sie mit dem Kopieren der Daten. Sie können jedes SMB-kompatible Dateikopiertool (beispielsweise Robocopy) verwenden, um Ihre Daten zu kopieren. Mit Robocopy können mehrere Kopieraufträge initiiert werden. Verwenden Sie den folgenden Befehl:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Die Attribute werden in der folgenden Tabelle beschrieben.
    
|Attribut  |BESCHREIBUNG  |
|---------|---------|
|/e     |Kopiert Unterverzeichnisse, einschließlich der leeren Verzeichnisse.         |
|/r:     |Gibt die Anzahl von Wiederholungsversuchen für fehlerhafte Kopiervorgänge an.         |
|/w:     |Gibt die Wartezeit zwischen Wiederholungen in Sekunden an.         |
|/is     |Schließt die gleichen Dateien ein.         |
|/nfl     |Gibt an, dass Dateinamen nicht protokolliert werden.         |
|/ndl    |Gibt an, dass Verzeichnisnamen nicht protokolliert werden.        |
|/np     |Gibt an, dass der Status des Kopiervorgangs (die Anzahl bisher kopierter Dateien oder Verzeichnisse) nicht angezeigt wird. Die Anzeige des Status beeinträchtigt die Leistung erheblich.         |
|/MT     | Verwendet Multithreading (empfohlen werden 32 oder 64 Threads). Diese Option wird nicht mit verschlüsselten Dateien verwendet. Sie müssen verschlüsselte und unverschlüsselte Dateien möglicherweise voneinander trennen. Das Kopieren mit nur einem Thread beeinträchtigt die Leistung jedoch erheblich.           |
|/fft     | Reduziert die Zeitstempelgranularität für alle Dateisysteme.        |
|/b     | Kopiert Dateien im Sicherungsmodus.        |
|/z    | Kopiert Dateien im Neustartmodus. Verwenden Sie diese Option, wenn die Umgebung instabil ist. Diese Option reduziert den Durchsatz durch eine zusätzliche Protokollierung.      |
| /zb     | Verwendet den Neustartmodus. Wenn der Zugriff verweigert wird, wird der Sicherungsmodus verwendet. Diese Option reduziert den Durchsatz durch das Setzen von Prüfpunkten.         |
|/efsraw     | Kopiert alle verschlüsselten Dateien im EFS-Raw-Modus. Verwenden Sie diese Option nur mit verschlüsselten Dateien.         |
|log+:\<LogFile>| Fügt die Ausgabe an die vorhandene Protokolldatei an.|    
 
Das folgende Beispiel zeigt die Ausgabe des Robocopy-Befehls zum Kopieren von Dateien auf die Data Box.
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

Verwenden Sie zum Optimieren der Leistung die folgenden Robocopy-Parameter beim Kopieren der Daten.

|    Plattform    |    Überwiegend kleine Dateien < 512 KB                           |    Überwiegend mittelgroße Dateien 512 KB – 1 MB                      |    Überwiegend große Dateien > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 Robocopy-Sitzungen <br> 16 Threads pro Sitzung    |    3 Robocopy-Sitzungen <br> 16 Threads pro Sitzung    |    2 Robocopy-Sitzungen <br> 24 Threads pro Sitzung    |


Weitere Informationen zum Robocopy-Befehl finden Sie unter [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy und einige Beispiele).

Öffnen Sie den Zielordner, um die kopierten Dateien anzuzeigen und zu überprüfen. Falls während des Kopierprozesses Fehler auftreten, laden Sie zur Problembehandlung die Fehlerdateien herunter. Weitere Informationen finden Sie unter [Anzeigen von Fehlerprotokollen beim Kopieren von Daten auf die Data Box](data-box-logs.md#view-error-log-during-data-copy). Eine detaillierte Liste von Fehlern beim Datenkopiervorgang finden Sie unter [Behandeln von Problemen bei der Data Box](data-box-troubleshoot.md).

Um die Datenintegrität zu gewährleisten, wird inline eine Prüfsumme berechnet, während die Daten kopiert werden. Überprüfen Sie nach Abschluss des Kopiervorgangs den belegten Speicherplatz und den freien Speicherplatz auf Ihrem Gerät.
    
   ![Überprüfen des freien und belegten Speicherplatzes im Dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)



## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Voraussetzungen
> * Herstellen einer Verbindung mit der Data Box
> * Kopieren von Daten auf die Data Box


Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Data Box zurück an Microsoft senden.

> [!div class="nextstepaction"]
> [Zurücksenden der Azure Data Box an Microsoft](./data-box-deploy-picked-up.md)

