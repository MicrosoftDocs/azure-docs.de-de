---
title: Tutorial zum Kopieren von Daten auf Azure Data Box Disk | Microsoft-Dokumentation
description: Verwenden Sie dieses Tutorial, um zu erfahren, wie Sie Daten auf Ihren Azure Data Box-Datenträger kopieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: f5c498fc3f3fe051070b3565041e506bc40fceda
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925169"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Tutorial: Kopieren von Daten auf die Azure Data Box Disk und Durchführen der Überprüfung

In diesem Tutorial wird beschrieben, wie Sie Daten von Ihrem Hostcomputer kopieren und anschließend Prüfsummen generieren, um die Datenintegrität zu überprüfen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Kopieren von Daten auf den Data Box-Datenträger
> * Überprüfen der Daten

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:
- Sie haben die Schritte unter [Tutorial: Entpacken, Verbinden und Entsperren des Azure Data Box-Datenträgers](data-box-disk-deploy-set-up.md) ausgeführt.
- Ihre Datenträger sind entsperrt und mit einem Clientcomputer verbunden.
- Auf Ihrem Clientcomputer, der zum Kopieren von Daten auf die Datenträger verwendet wird, muss ein [unterstütztes Betriebssystem](data-box-disk-system-requirements.md##supported-operating-systems-for-clients) ausgeführt werden.
- Stellen Sie sicher, dass der gewünschte Speichertyp für Ihre Daten den [unterstützten Datentypen](data-box-disk-system-requirements.md#supported-storage-types-for-upload) entspricht.
- Informationen zu Grenzwerten für verwaltete Datenträger finden Sie unter [Größenbeschränkungen für das Azure-Objekt](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Kopieren von Daten auf Datenträger

Beachten Sie Folgendes, bevor Sie die Daten auf die Datenträger kopieren:

- Sie sind dafür verantwortlich, die Daten in Ordner zu kopieren, die das richtige Datenformat aufweisen. Kopieren Sie beispielsweise die Blockblobdaten in den Ordner für Blockblobs. Falls das Datenformat nicht mit dem entsprechenden Ordner (Speichertyp) übereinstimmt, tritt für den Datenupload in Azure während eines späteren Schritts ein Fehler auf.
- Stellen Sie beim Kopieren der Daten sicher, dass für die Datengröße die Größenbeschränkungen eingehalten werden, die im Artikel zu den [Grenzwerten für Azure Storage und Data Box-Datenträger](data-box-disk-limits.md) beschrieben sind.
- Falls vom Data Box-Datenträger hochgeladene Daten gleichzeitig von anderen Anwendungen außerhalb des Data Box-Datenträgers hochgeladen werden, kann dies zu Fehlern bei Uploadaufträgen und zu Datenbeschädigungen führen.

   > [!IMPORTANT]
   >  Wenn Sie beim Aufgeben der Bestellung verwaltete Datenträger als eines der Speicherziele angegeben haben, gelten die Informationen im folgenden Abschnitt.

- Der Name eines verwalteten Datenträgers muss in einer Ressourcengruppe in allen vorab erstellten Ordner und der gesamten Data Box Disk-Instanz eindeutig sein. Die VHDs, die in die vorab erstellten Ordner hochgeladen werden, müssen also jeweils einen eindeutigen Namen aufweisen. Achten Sie darauf, dass der Name nicht mit dem Namen eines bereits vorhandenen verwalteten Datenträgers in einer Ressourcengruppe identisch ist. Wenn VHDs denselben Namen haben, wird nur eine VHD in einen verwalteten Datenträger mit diesem Namen konvertiert. Die anderen VHDs werden als Seitenblobs in das Stagingspeicherkonto hochgeladen.
- Kopieren Sie die VHDs immer in einen der vorab erstellten Ordner. Wenn Sie die VHDs an einen Ort außerhalb dieser Ordner oder in einen selbst erstellten Ordner kopieren, werden sie nicht als verwaltete Datenträger, sondern als Seitenblobs in das Azure Storage-Konto hochgeladen.
- Zur Erstellung von verwalteten Datenträgern können nur feste VHDs hochgeladen werden. Dynamische VHDs, differenzierende VHDs oder VHDX-Dateien werden nicht unterstützt.


Führen Sie die folgenden Schritte aus, um eine Verbindung herzustellen und Daten von Ihrem Computer auf den Data Box-Datenträger zu kopieren.

1. Zeigen Sie den Inhalt des entsperrten Laufwerks an. Die Liste der vorab erstellten Ordner und Unterordner im Laufwerk unterscheidet sich je nach den bei der Data Box Disk-Bestellung ausgewählten Optionen.

    |Ausgewähltes Speicherziel  |Speicherkontotyp|Typ des Stagingspeicherkontos |Ordner und Unterordner  |
    |---------|---------|---------|------------------|
    |Speicherkonto     |GPv1 oder GPv2                 | Nicht verfügbar | BlockBlob <br> PageBlob <br> AzureFile        |
    |Speicherkonto     |Blob Storage-Konto         | Nicht verfügbar | BlockBlob        |
    |Verwaltete Datenträger     |Nicht verfügbar | GPv1 oder GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Speicherkonto <br> Verwaltete Datenträger     |GPv1 oder GPv2 | GPv1 oder GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Speicherkonto <br> Verwaltete Datenträger    |Blob Storage-Konto | GPv1 oder GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Nachfolgend ist ein Beispielscreenshot einer Bestellung abgebildet, bei der ein GPv2-Speicherkonto angegeben wurde:

    ![Inhalt des Laufwerks](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Kopieren Sie die als Blockblobs zu importierenden Daten in den Ordner *BlockBlob*. Kopieren Sie entsprechend Daten z. B. von VHD/VHDX-Dateien in den Ordner *PageBlob* und Daten in den Ordner *AzureFile*.

    Im Azure-Speicherkonto wird für jeden Unterordner der Ordner „BlockBlob“ und „PageBlob“ ein Container erstellt. Alle Dateien in den Ordnern „BlockBlob“ und „PageBlob“ werden in den Standardcontainer `$root` unter dem Azure Storage-Konto kopiert. Alle Dateien im Container `$root` werden immer als Blockblobs hochgeladen.

   Kopieren Sie Dateien in einen Ordner innerhalb des Ordners *AzureFile*. Durch einen Unterordner innerhalb des Ordners *AzureFile* wird eine Dateifreigabe erstellt. Bei direkt in den Ordner *AzureFile* kopierten Dateien tritt ein Fehler auf, und die Dateien werden als Blockblobs hochgeladen.

    Falls im Stammverzeichnis Dateien und Ordner vorhanden sind, müssen Sie diese in einen anderen Ordner verschieben, bevor Sie mit dem Kopieren von Daten beginnen.

    > [!IMPORTANT]
    > Für alle Container, Blobs und Dateinamen sollten die [Azure-Namenskonventionen](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) eingehalten werden. Wenn diese Regeln nicht befolgt werden, tritt beim Datenupload in Azure ein Fehler auf.

3. Stellen Sie beim Kopieren von Dateien sicher, dass Dateien für Blockblobs eine Größe von ca. 4,7 TiB, für Seitenblobs eine Größe von ca. 8 TiB und für Azure Files eine Größe von ca. 1 TiB nicht überschreiten. 
4. Sie können zum Kopieren der Daten für den Datei-Explorer Drag & Drop verwenden. Außerdem können Sie jedes SMB-kompatible Dateikopiertool, z.B. Robocopy, verwenden, um die Daten zu kopieren. Mehrere Kopieraufträge können mit dem folgenden Robocopy-Befehl initiiert werden:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Die Parameter und Optionen für den Befehl werden wie folgt in Tabellenform angezeigt:
    
    |Parameter/Optionen  |BESCHREIBUNG |
    |--------------------|------------|
    |`Source`            | Gibt den Pfad zum Quellverzeichnis an.        |
    |Ziel       | Gibt den Pfad zum Zielverzeichnis an.        |
    |/E                  | Kopiert Unterverzeichnisse, einschließlich der leeren Verzeichnisse. |
    |/MT[:N]             | Erstellt Multithread-Kopien mit N Threads, wobei N hier für eine ganze Zahl zwischen 1 und 128 steht. <br>Der Standardwert für N ist „8“.        |
    |/R: \<N>             | Gibt die Anzahl von Wiederholungsversuchen für fehlerhafte Kopiervorgänge an. Der Standardwert von N ist „1.000.000“ (eine Million Wiederholungen).        |
    |/W: \<N>             | Gibt die Wartezeit zwischen Wiederholungen in Sekunden an. Der Standardwert von N ist „30“ (Wartezeit von 30 Sekunden).        |
    |/NFL                | Gibt an, dass Dateinamen nicht protokolliert werden sollen.        |
    |/NDL                | Gibt an, dass Verzeichnisnamen nicht protokolliert werden sollen.        |
    |/FFT                | Setzt FAT-Dateizeitangaben voraus (Zwei-Sekunden-Genauigkeit).        |
    |/Log:\<Log File>     | Schreibt die Statusausgabe in die Protokolldatei (vorhandene Protokolldatei wird überschrieben).         |

    Mehrere Datenträger können parallel genutzt werden, wobei auf jedem Datenträger mehrere Aufträge ausgeführt werden.

6. Überprüfen Sie den Kopierstatus, während sich der Auftrag in Bearbeitung befindet. Das folgende Beispiel enthält die Ausgabe des Robocopy-Befehls zum Kopieren von Dateien auf den Data Box-Datenträger.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Verwenden Sie zum Optimieren der Leistung die folgenden Robocopy-Parameter beim Kopieren der Daten.

    |    Plattform    |    Überwiegend kleine Dateien < 512 KB                           |    Überwiegend mittelgroße Dateien 512 KB – 1 MB                      |    Überwiegend große Dateien > 1 MB                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 Robocopy-Sitzungen* <br> 16 Threads pro Sitzung    |    2 Robocopy-Sitzungen* <br> 16 Threads pro Sitzung    |    2 Robocopy-Sitzungen* <br> 16 Threads pro Sitzung    |
    
    **Jede Robocopy-Sitzung kann maximal 7.000 Verzeichnisse und 150 Millionen Dateien aufweisen.*
    
    >[!NOTE]
    > Die oben vorgeschlagenen Parameter basieren auf der Umgebung, die bei internen Tests verwendet wird.
    
    Weitere Informationen zum Robocopy-Befehl finden Sie unter [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy und einige Beispiele).

6. Öffnen Sie den Zielordner, um die kopierten Dateien anzuzeigen und zu überprüfen. Laden Sie die Protokolldateien herunter, falls während des Kopierprozesses Fehler auftreten, um die Problembehandlung durchzuführen. Die Protokolldateien befinden sich an den Speicherorten, die für den Robocopy-Befehl angegeben wurden.
 
### <a name="split-and-copy-data-to-disks"></a>Aufteilen und Kopieren von Daten auf Datenträger

Dieses optionale Verfahren kann verwendet werden, wenn Sie mehrere Datenträger verwenden und über ein umfangreiches Dataset verfügen, das aufgeteilt und auf alle Datenträger kopiert werden muss. Das Data Box-Tool zum Aufteilen/Kopieren unterstützt Sie beim Aufteilen und Kopieren der Daten auf einem Windows-Computer.

>[!IMPORTANT]
> Mit dem Data Box-Tool zum Aufteilen/Kopieren werden Ihre Daten auch überprüft. Wenn Sie das Data Box-Tool zum Aufteilen/Kopieren zum Kopieren von Daten verwenden, können Sie den [Überprüfungsschritt](#validate-data) überspringen.
> Das Tool zum Aufteilen/Kopieren wird für verwaltete Datenträger nicht unterstützt.

1. Vergewissern Sie sich auf Ihrem Windows-Computer, dass Sie das Data Box-Tool zum Aufteilen/Kopieren heruntergeladen und in einem lokalen Ordner extrahiert haben. Dieses Tool wurde zusammen mit dem Data Box Disk-Toolset für Windows heruntergeladen.
2. Öffnen Sie den Datei-Explorer. Notieren Sie sich das Datenquelllaufwerk und die Laufwerkbuchstaben für Data Box Disk. 

     ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identifizieren Sie die zu kopierenden Quelldaten. In diesem Beispiel:
    - Folgende Blockblobdaten wurden identifiziert:

         ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Folgende Seitenblobdaten wurden identifiziert:

         ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Wechseln Sie zu dem Ordner, in dem die Software extrahiert wurde. Suchen Sie in diesem Ordner nach der Datei `SampleConfig.json`. Hierbei handelt es sich um eine schreibgeschützte Datei, die Sie ändern und speichern können.

   ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Ändern Sie die Datei `SampleConfig.json`.
 
   - Geben Sie einen Auftragsnamen an. Dadurch wird in der Data Box Disk-Ressource ein Ordner erstellt, der letztendlich als Container in dem Azure-Speicherkonto fungiert, das den Datenträgern zugeordnet ist. Der Auftragsname muss den Benennungskonventionen für Azure-Container entsprechen. 
   - Geben Sie einen Quellpfad an, und beachten Sie das Pfadformat in der Datei `SampleConfigFile.json`. 
   - Geben Sie die Laufwerkbuchstaben für die Zieldatenträger ein. Die Daten werden aus dem Quellpfad abgerufen und auf mehrere Datenträger kopiert.
   - Geben Sie einen Pfad für die Protokolldateien an. Standardmäßig wird als Ziel das aktuelle Verzeichnis verwendet, in dem sich auch die `.exe`-Datei befindet.

     ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Navigieren Sie zur Überprüfung des Dateiformats zu `JSONlint`. Speichern Sie die Datei als `ConfigFile.json`. 

     ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Öffnen Sie ein Eingabeaufforderungsfenster. 

8. Führen Sie die Datei `DataBoxDiskSplitCopy.exe` aus. Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Drücken Sie die EINGABETASTE, um das Skript fortzusetzen.

    ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Nach dem Aufteilen und Kopieren des Datasets wird eine Zusammenfassung des Tools zum Aufteilen/Kopieren für die Kopiersitzung angezeigt. Nachfolgend sehen Sie eine Beispielausgabe.

    ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Vergewissern Sie sich, dass die Daten auf die Zieldatenträger verteilt wurden. 
 
    ![Aufteilen/Kopieren von Daten ](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Bei näherer Betrachtung des Inhalts von Laufwerk `n:` sehen Sie, dass für Daten im Blockblob- und Seitenblobformat zwei entsprechende Unterordner erstellt wurden.
    
     ![Aufteilen/Kopieren von Daten](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Sollte bei der Kopiersitzung ein Fehler aufgetreten sein, führen Sie den folgenden Befehl aus, um den Vorgang fortzusetzen:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Nachdem der Datenkopiervorgang abgeschlossen ist, können Sie mit der Überprüfung Ihrer Daten fortfahren. Wenn Sie das Aufteilen/Kopieren-Tool verwendet haben, können Sie die Überprüfung überspringen (mit dem Aufteilen/Kopieren-Tool wird auch eine Überprüfung durchgeführt) und mit dem nächsten Tutorial fortfahren.


## <a name="validate-data"></a>Überprüfen der Daten

Falls Sie das Aufteilen/Kopieren-Tool nicht zum Kopieren der Daten verwendet haben, müssen Sie Ihre Daten überprüfen. Führen Sie die folgenden Schritte aus, um die Daten zu überprüfen.

1. Führen Sie die Datei `DataBoxDiskValidation.cmd` für die Überprüfung der Prüfsumme im Ordner *DataBoxDiskImport* Ihres Laufwerks aus.
    
    ![Ausgabe des Data Box Disk-Überprüfungstools](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Wählen Sie die geeignete Option. **Es wird empfohlen, die Dateien immer zu überprüfen und Prüfsummen zu generieren, indem Option 2 gewählt wird**. Je nach Größe Ihrer Daten kann dieser Schritt eine Weile dauern. Nachdem das Skript abgeschlossen wurde, können Sie das Befehlsfenster beenden. Falls bei der Überprüfung und Generierung der Prüfsumme Fehler auftreten, werden Sie benachrichtigt und erhalten einen Link zu den Fehlerprotokollen.

    ![Ausgabe der Prüfsumme](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Setzen Sie das Tool zwischen zwei Ausführungen zurück.
    > - Verwenden Sie Option 1, wenn Sie ein großes Dataset mit kleinen Dateien (KB-Bereich) verwenden. Mit dieser Option werden die Dateien nur überprüft, da die Generierung der Prüfsumme ggf. sehr lange dauern und die Leistung deutlich verlangsamt sein kann.

3. Führen Sie den Befehl für jeden Datenträger einzeln aus, wenn Sie mehrere Datenträger verwenden möchten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Datenträgern erhalten, z.B.:

> [!div class="checklist"]
> * Kopieren von Daten auf den Data Box-Datenträger
> * Überprüfen der Integrität von Daten

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie den Data Box-Datenträger zurücksenden und den Datenupload in Azure überprüfen.

> [!div class="nextstepaction"]
> [Zurücksenden Ihres Azure Data Box-Datenträgers an Microsoft](./data-box-disk-deploy-picked-up.md)
