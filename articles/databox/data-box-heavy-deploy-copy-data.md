---
title: 'Tutorial: Kopieren von Daten über SMB auf Azure Data Box Heavy | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Daten über SMB auf Ihr Azure Data Box Heavy-Gerät kopieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 8ee96f2e06071d60eb97596687387fd80ba14cc3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496275"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb-preview"></a>Tutorial: Kopieren von Daten auf Azure Data Box Heavy über SMB (Vorschauversion)

In diesem Tutorial wird beschrieben, wie Sie über die lokale Webbenutzeroberfläche eine Verbindung mit Ihrem Hostcomputer herstellen und Daten kopieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit Data Box Heavy
> * Kopieren von Daten auf Data Box Heavy


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben die Schritte unter [Tutorial: Verkabeln und Herstellen einer Verbindung mit der Azure Data Box](data-box-deploy-set-up.md) ausgeführt.
2. Sie haben Ihr Data Box Heavy-Gerät erhalten, und der Auftrag wird im Portal mit dem Status **Geliefert** angezeigt.
3. Sie verfügen über einen Hostcomputer mit den Daten, die Sie auf Data Box Heavy kopieren möchten. Für Ihren Hostcomputer müssen die folgenden Bedingungen erfüllt sein:
    - Es muss ein [unterstütztes Betriebssystem](data-box-system-requirements.md) ausgeführt werden.
    - Er muss mit einem Hochgeschwindigkeitsnetzwerk verbunden sein. Zur Erzielung der höchstmöglichen Kopiergeschwindigkeit können zwei 40-GbE-Verbindungen (jeweils eine pro Knoten) parallel genutzt werden. Falls Sie über keine 40-GbE-Verbindung verfügen, sollten Sie zwei Verbindungen mit mindestens 10 GbE verwenden (jeweils eine pro Knoten).

## <a name="connect-to-data-box-heavy-shares"></a>Herstellen einer Verbindung mit Data Box Heavy-Freigaben

Je nach ausgewähltem Speicherkonto erstellt Data Box Heavy bis zu:
- Drei Freigaben für jedes verknüpfte Speicherkonto für GPv1 und GPv2
- Eine Freigabe für Storage Premium
- Eine Freigabe für das Blob Storage-Konto

Diese Freigaben werden auf beiden Knoten des Geräts erstellt.

Unter Blockblob- und Seitenblobfreigaben:
- Entitäten der ersten Ebene sind Container.
- Entitäten der zweiten Ebene sind Blobs.

Unter Freigaben für Azure Files:
- Entitäten der ersten Ebene sind Freigaben.
- Entitäten der zweiten Ebene sind Dateien.

Die folgende Tabelle enthält den UNC-Pfad zu den Freigaben auf Ihrem Data Box Heavy-Gerät und die Azure Storage-Pfad-URL für den Datenupload. Die endgültige URL des Azure Storage-Pfads kann aus dem UNC-Freigabepfad abgeleitet werden.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-Blockblobs | <li>UNC-Pfad zu den Freigaben: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-Seitenblobs  | <li>UNC-Pfad zu den Freigaben: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-Pfad zu den Freigaben: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Die Schritte für die Verbindungsherstellung sind abhängig davon, ob Sie einen Windows- oder einen Linux-Client verwenden.

> [!NOTE]
> Führen Sie die gleichen Schritte aus, um parallel eine Verbindung mit beiden Knoten des Geräts herzustellen.

### <a name="connect-on-a-windows-system"></a>Herstellen einer Verbindung bei Verwendung eines Windows-Systems

Wenn Sie einen Windows Server-Hostcomputer verwenden, führen Sie die folgenden Schritte aus, um eine Verbindung mit Data Box Heavy herzustellen:

1. Zunächst müssen Sie sich authentifizieren und eine Sitzung starten. Navigieren Sie zu **Verbindung herstellen und Daten kopieren**. Klicken Sie auf **Get credentials** (Anmeldeinformationen abrufen), um die Anmeldeinformationen für den Zugriff auf die mit Ihrem Speicherkonto verknüpften Freigaben abzurufen.

    ![Abrufen der Anmeldeinformationen für Freigaben 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Kopieren Sie im Dialogfeld „Auf Freigabe zugreifen und Daten kopieren“ den **Benutzernamen** und das **Kennwort** für die Freigabe. Klicken Sie auf **OK**.
    
    ![Abrufen der Anmeldeinformationen für Freigaben 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Öffnen Sie ein Befehlsfenster, um über Ihren Hostcomputer auf die Freigaben zuzugreifen, die mit Ihrem Speicherkonto (*databoxe2etest* im folgenden Beispiel) verknüpft sind. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Abhängig von Ihrem Datenformat lauten die Freigabepfade wie folgt:
    - Azure-Blockblob: `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure-Seitenblob: `\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure-Dateien: `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Geben Sie das Kennwort für die Freigabe ein, wenn Sie dazu aufgefordert werden. Das folgende Beispiel zeigt das Herstellen einer Verbindung mit einer Freigabe über den obigen Befehl.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Drücken Sie WINDOWS-TASTE+R. Geben Sie im Fenster **Ausführen** die `\\<device IP address>` an. Klicken Sie auf **OK**, um den Datei-Explorer zu öffnen.
    
    ![Herstellen einer Verbindung mit der Freigabe über den Datei-Explorer 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Die Freigaben sollten jetzt als Ordner angezeigt werden.
    
    ![Herstellen einer Verbindung mit der Freigabe über den Datei-Explorer 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Erstellen Sie immer einen Ordner für die Dateien, die Sie unter die Freigabe kopieren möchten, und kopieren Sie die Dateien dann in diesen Ordner**. Der Ordner, der unter der Blockblob- und der Seitenblob Freigabe erstellt wurde, entspricht einem Container, in den Daten als Blobs hochgeladen werden. Es ist nicht möglich, Dateien direkt in den *root*-Ordner im Speicherkonto zu kopieren.
    
### <a name="connect-on-a-linux-system"></a>Herstellen einer Verbindung bei Verwendung eines Linux-Systems

Wenn Sie einen Linux-Client verwenden, stellen Sie mit folgendem Code die SMB-Freigabe bereit.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

Der Parameter `vers` ist die von Ihrem Linux-Host unterstützte SMB-Version. Fügen Sie die entsprechende Version in den obigen Befehl ein.

Von Data Box Heavy unterstützte SMB-Versionen finden Sie unter [Unterstützte Dateisysteme für Linux-Clients](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopieren von Daten auf Data Box Heavy

Nachdem Sie eine Verbindung mit den Data Box Heavy-Freigaben hergestellt haben, kopieren Sie im nächsten Schritt Ihre Daten.

### <a name="copy-considerations"></a>Überlegungen beim Kopieren

Bevor Sie mit dem Kopieren der Daten beginnen, sollten Sie folgende Aspekte beachten:

- Stellen Sie sicher, dass Sie die Daten in Freigaben kopieren, die das richtige Datenformat aufweisen. Kopieren Sie beispielsweise die Blockblobdaten in die Freigabe für Blockblobs. Kopieren Sie die VHDs in einen Seitenblob.

    Wenn das Datenformat nicht mit dem entsprechenden Freigabetyp übereinstimmt, tritt später beim Hochladen der Daten in Azure ein Fehler auf.
-  Achten Sie beim Kopieren von Daten darauf, dass die unter [Azure Data Box Heavy limits (preview)](data-box-heavy-limits.md) (Grenzwerte für Azure Data Box Heavy (Vorschauversion)) beschriebenen Größenbeschränkungen für die Datengröße eingehalten werden.
- Falls von Data Box Heavy hochgeladene Daten gleichzeitig von anderen Anwendungen außerhalb von Data Box Heavy hochgeladen werden, kann dies zu Fehlern bei Uploadaufträgen und zu Datenbeschädigungen führen.
- Wir empfehlen Folgendes:
    - Sie verwenden SMB und NFS nicht gleichzeitig.
    - Kopieren Sie die gleichen Daten in das gleiche Endziel in Azure.
     
  In diesen Fällen lässt sich das endgültige Ergebnis nicht im Vorhinein bestimmen.
- Erstellen Sie immer einen Ordner für die Dateien, die Sie unter die Freigabe kopieren möchten, und kopieren Sie die Dateien dann in diesen Ordner. Der Ordner, der unter der Blockblob- und der Seitenblob Freigabe erstellt wurde, entspricht einem Container, in den die Daten als Blobs hochgeladen werden. Es ist nicht möglich, Dateien direkt in den *root*-Ordner im Speicherkonto zu kopieren.

Nachdem Sie eine Verbindung mit der SMB-Freigabe hergestellt haben, beginnen Sie mit dem Kopieren der Daten.

1. Sie können jedes SMB-kompatible Dateikopiertool (beispielsweise Robocopy) verwenden, um Ihre Daten zu kopieren. Mit Robocopy können mehrere Kopieraufträge initiiert werden. Verwenden Sie den folgenden Befehl:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Die Attribute werden in der folgenden Tabelle beschrieben.
    
    |Attribut  |BESCHREIBUNG  |
    |---------|---------|
    |/e      |Kopiert Unterverzeichnisse, einschließlich der leeren Verzeichnisse.         |
    |/r:     |Gibt die Anzahl von Wiederholungsversuchen für fehlerhafte Kopiervorgänge an.         |
    |/w:     |Gibt die Wartezeit zwischen Wiederholungen in Sekunden an.         |
    |/is     |Schließt die gleichen Dateien ein.         |
    |/nfl    |Gibt an, dass Dateinamen nicht protokolliert werden.         |
    |/ndl    |Gibt an, dass Verzeichnisnamen nicht protokolliert werden.        |
    |/np     |Gibt an, dass der Status des Kopiervorgangs (die Anzahl bisher kopierter Dateien oder Verzeichnisse) nicht angezeigt wird. Die Anzeige des Status beeinträchtigt die Leistung erheblich.         |
    |/MT     | Verwendet Multithreading (empfohlen werden 32 oder 64 Threads). Diese Option wird nicht mit verschlüsselten Dateien verwendet. Sie müssen verschlüsselte und unverschlüsselte Dateien möglicherweise voneinander trennen. Das Kopieren mit nur einem Thread beeinträchtigt die Leistung jedoch erheblich.           |
    |/fft    | Reduziert die Zeitstempelgranularität für alle Dateisysteme.        |
    |/b      | Kopiert Dateien im Sicherungsmodus.        |
    |/z      | Kopiert Dateien im Neustartmodus. Verwenden Sie diese Option, wenn die Umgebung instabil ist. Diese Option reduziert den Durchsatz durch eine zusätzliche Protokollierung.      |
    | /zb    | Verwendet den Neustartmodus. Wenn der Zugriff verweigert wird, wird der Sicherungsmodus verwendet. Diese Option reduziert den Durchsatz durch das Setzen von Prüfpunkten.         |
    |/efsraw | Kopiert alle verschlüsselten Dateien im EFS-Raw-Modus. Verwenden Sie diese Option nur mit verschlüsselten Dateien.         |
    |log+:\<LogFile>| Fügt die Ausgabe an die vorhandene Protokolldatei an.|
    
 
    Das folgende Beispiel enthält die Ausgabe des Robocopy-Befehls zum Kopieren von Dateien auf Data Box Heavy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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
    ```       

2. Verwenden Sie zum Optimieren der Leistung die folgenden Robocopy-Parameter beim Kopieren der Daten. (Die folgenden Zahlen stellen jeweils den Idealfall dar.)

    | Plattform    | Überwiegend kleine Dateien (< 512 KB)    | Überwiegend mittelgroße Dateien (512 KB bis 1 MB)  | Überwiegend große Dateien (> 1 MB)                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | Sechs Robocopy-Sitzungen <br> 24 Threads pro Sitzung | Sechs Robocopy-Sitzungen <br> 16 Threads pro Sitzung | Sechs Robocopy-Sitzungen <br> 16 Threads pro Sitzung |


    Weitere Informationen zum Robocopy-Befehl finden Sie unter [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy und einige Beispiele).

3. Öffnen Sie den Zielordner, um die kopierten Dateien anzuzeigen und zu überprüfen.

    ![Anzeigen der kopierten Dateien](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Vorgänge während des Datenkopiervorgangs:

    - Die Dateinamen, Größen und das Format werden überprüft, um sicherzustellen, dass die Grenzwerte für Azure-Objekte und -Speicher sowie die Benennungskonventionen für Azure-Dateien und -Container eingehalten werden.
    - Zur Gewährleistung der Datenintegrität wird inline auch eine Prüfsumme berechnet.

    Falls während des Kopierprozesses Fehler auftreten, laden Sie zur Problembehandlung die Fehlerdateien herunter. Wählen Sie das Pfeilsymbol aus, um die Fehlerdateien herunterzuladen.

    ![Herunterladen von Fehlerdateien](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Weitere Informationen finden Sie unter [View error log during data copy](data-box-logs.md#view-error-log-during-data-copy) (Anzeigen des Fehlerprotokolls beim Kopieren von Daten). Eine detaillierte Liste mit Fehlern beim Kopieren von Dateien finden Sie unter [Troubleshoot issues related to Azure Data Box and Azure Data Box Heavy](data-box-troubleshoot.md) (Behandeln von Problemen mit Azure Data Box und Azure Data Box Heavy).

5. Öffnen Sie die Fehlerdatei im Editor. Der folgenden Fehlerdatei können Sie entnehmen, dass die Daten nicht ordnungsgemäß abgestimmt wurden:

    ![Geöffnete Fehlerdatei](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Bei einem Seitenblob müssen die Daten einem ganzzahligen Vielfachen von 512 Bytes entsprechen. Nach dem Entfernen dieser Daten ist der Fehler behoben, wie im folgenden Screenshot zu sehen:

    ![Fehler behoben](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Navigieren Sie nach Abschluss des Kopiervorgangs zur Seite **Dashboard anzeigen**. Überprüfen Sie den beanspruchten und den freien Speicherplatz auf Ihrem Gerät.
    
    ![Überprüfen des freien und belegten Speicherplatzes im Dashboard](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Wiederholen Sie die obigen Schritte, um Daten auf den zweiten Knoten des Geräts zu kopieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Informationen zu Azure Data Box Heavy vermittelt:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit Data Box Heavy
> * Kopieren von Daten auf Data Box Heavy


Im nächsten Tutorial erfahren Sie, wie Sie Data Box Heavy an Microsoft zurücksenden.

> [!div class="nextstepaction"]
> [Zurücksenden von Azure Data Box Heavy und Überprüfen des Datenuploads in Azure (Vorschauversion)](./data-box-heavy-deploy-picked-up.md)

