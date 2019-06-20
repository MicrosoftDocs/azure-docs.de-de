---
title: Verwenden von Azure Data Factory zum inkrementellen Kopieren neuer Dateien nur basierend auf dem zeitpartitionierten Dateinamen | Microsoft-Dokumentation
description: Erstellen Sie eine Azure Data Factory, und verwenden Sie dann das Tool „Daten kopieren“, um neue Dateien nur basierend auf dem zeitpartitionierten Dateinamen zu laden.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: c89764d746f07e6100b1f250d4c107bb700fe014
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61098724"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Inkrementelles Kopieren neuer Dateien basierend auf dem zeitpartitionierten Dateinamen und mithilfe des Tools „Daten kopieren“

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend verwenden Sie das Tool „Daten kopieren“, um eine Pipeline zu erstellen, die neue Dateien basierend auf dem zeitpartitionierten Dateinamen aus einem Azure-Blobspeicher in einen anderen kopiert. 

> [!NOTE]
> Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure-Speicherkonto**: Verwenden Sie den Blobspeicher als _Quelldatenspeicher_ und als _Senkendatenspeicher_. Falls Sie noch nicht über ein Azure-Speicherkonto verfügen, finden Sie Anweisungen dazu unter [Erstellen eines Speicherkontos](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Erstellen von zwei Containern im Blobspeicher

Bereiten Sie Ihren Blobspeicher folgendermaßen auf das Tutorial vor.

1. Erstellen Sie einen Container mit dem Namen **source** (Quelle).  Erstellen Sie in Ihrem Container den Ordnerpfad **2019/02/26/14**. Erstellen Sie eine leere Textdatei, und nennen Sie sie **file1.txt**. Laden Sie die Datei „file1.txt“ in den Ordnerpfad (**source/2019/02/26/14**) in Ihrem Speicherkonto hoch.  Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).
    
    ![Hochladen von Dateien](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Passen Sie den Ordnernamen an Ihre UTC-Zeit an.  Wenn die aktuelle UTC-Zeit beispielsweise am 26. Februar 2019 14:03 Uhr ist, können Sie den Ordnerpfad als **source/2019/02/26/14/** gemäß der Regel **source/{Jahr}/{Monat}/{Tag}/{Stunde}/** erstellen.

2. Erstellen Sie einen Container mit dem Namen **destination** (Ziel). Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus: 
   
   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein. 
      
    ![Neue Data Factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
    
    Der Name der Data Factory muss _global eindeutig_ sein. Sie erhalten unter Umständen die folgende Fehlermeldung:
   
   ![Fehlermeldung zu neuer Data Factory](./media/tutorial-copy-data-tool/name-not-available-error.png)
   
   Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Verwenden Sie beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory**. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
3. Wählen Sie das **Azure-Abonnement** aus, in dem die neue Data Factory erstellt werden soll. 
4. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:
     
    a. Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

    b. Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein. 
         
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/resource-group-overview.md).

5. Wählen Sie unter **Version** die Option **V2**.
6. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich an anderen Standorten und in anderen Regionen befinden.
7. Wählen Sie die Option **An Dashboard anheften** aus. 
8. Klicken Sie auf **Erstellen**.
9. Auf dem Dashboard wird auf der Kachel **Deploying Data Factory** (Data Factory wird bereitgestellt...) der Prozessstatus angezeigt.

    ![Kachel „Deploying Data Factory“ (Data Factory wird bereitgestellt...)](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Nach Abschluss der Erstellung wird die Startseite von **Data Factory** angezeigt.
   
    ![Data Factory-Startseite](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Klicken Sie auf der Seite **Let's get started** (Erste Schritte) auf **Copy Data** (Daten kopieren), um das Tool zum Kopieren von Daten zu starten. 

   ![Kachel für das Tool zum Kopieren von Daten](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Gehen Sie auf der Seite **Properties** (Eigenschaften) wie folgt vor:

    a. Geben Sie unter **Task name** (Aufgabenname) **DeltaCopyFromBlobPipeline** ein.

    b. Wählen Sie unter **Task cadence or Task schedule** (Aufgabenhäufigkeit oder Aufgabenzeitplan) die Option **Run regularly on schedule** (Nach Zeitplan regelmäßig ausführen) aus.

    c. Wählen Sie unter **Trigger type** (Triggertyp) die Option **Tumbling Window** (Rollierendes Fenster) aus.
    
    d. Geben Sie unter **Recurrence** (Wiederholung) **1 Hour(s)** (1 Stunde[n]) ein. 
    
    e. Klicken Sie auf **Weiter**. 
    
    Über die Data Factory-Benutzeroberfläche wird eine Pipeline mit dem angegebenen Tasknamen erstellt. 

    ![Eigenschaftenseite](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

    a. Klicken Sie auf **+ Create new connection** (+ Neue Verbindung erstellen), um eine Verbindung hinzuzufügen.

    ![Seite „Quelldatenspeicher“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Wählen Sie im Katalog **Azure Blob Storage** aus, und klicken Sie dann auf **Continue** (Weiter).

    ![Seite „Quelldatenspeicher“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Wählen Sie auf der Seite **New Linked Service** (Neuer verknüpfter Dienst) in der Liste **Storage account name** (Speicherkontoname) Ihr Speicherkonto aus, und klicken Sie dann auf **Finish** (Fertig stellen).
    
    ![Seite „Quelldatenspeicher“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Wählen Sie den neu erstellten verknüpften Dienst aus, und klicken Sie dann auf **Next** (Weiter). 
    
   ![Seite „Quelldatenspeicher“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Führen Sie auf der Seite **Choose the input file or folder** (Eingabedatei oder -ordner auswählen) die folgenden Schritte aus:
    
    a. Suchen Sie nach dem Container **source**, wählen Sie ihn aus, und klicken Sie anschließend auf **Choose** (Wählen).
    
    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Wählen Sie unter **File loading behavior** (Dateiladeverhalten) die Option **Incremental load: time-partitioned folder/file names** (Inkrementell laden: zeitpartionierter Ordner/Dateinamen).
    
    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Schreiben Sie den dynamischen Ordnerpfad als **source/{Jahr}/{Monat}/{Tag}/{Stunde}/** , und ändern Sie das Format wie folgt:
    
    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Aktivieren Sie das Kontrollkästchen **Binary copy** (Binärkopie), und klicken Sie auf **Next** (Weiter).
    
    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Wählen Sie auf der Seite **Destination data store** (Zieldatenspeicher) **AzureBlobStorage** aus, also das gleiche Speicherkonto wie beim Datenquellenspeicher, und klicken Sie anschließend auf **Next** (Weiter).

    ![Seite „Zieldatenspeicher“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Führen Sie auf der Seite **Choose the output file or folder** (Ausgabedatei oder -ordner auswählen) die folgenden Schritte aus:
    
    a. Suchen Sie nach dem Ordner **destination** (Ziel), wählen Sie diesen aus, und klicken Sie anschließend auf **Choose** (Wählen).
    
    ![Auswählen der Ausgabedatei oder des Ausgabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Schreiben Sie den dynamischen Ordnerpfad als **source/{Jahr}/{Monat}/{Tag}/{Stunde}/** , und ändern Sie das Format wie folgt:
    
    ![Auswählen der Ausgabedatei oder des Ausgabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Klicken Sie auf **Weiter**.
    
    ![Auswählen der Ausgabedatei oder des Ausgabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**. 

    ![Seite "Einstellungen"](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **Weiter**.

    ![Seite „Zusammenfassung“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen.
    ![Bereitstellungsseite](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist.  Sie müssen auf die Ausführung der Pipeline warten, wenn sie (etwa nach einer Stunde) automatisch ausgelöst wird.  Wenn sie ausgeführt wird, werden in der Spalte **Actions** (Aktionen) Links zum Anzeigen von Aktivitätsausführungsdetails und zum erneuten Ausführen der Pipeline angezeigt. Wählen Sie **Refresh** (Aktualisieren) aus, um die Liste zu aktualisieren, und klicken Sie in der Spalte **Actions** (Aktionen) auf den Link **View Activity Runs** (Aktivitätsausführungen anzeigen). 

    ![Überwachen der Pipelineausführungen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Sie können sehen, dass die Quelldatei (file1.txt) mit dem gleichen Dateinamen von **source/2019/02/26/14/** nach **destination/2019/02/26/14/** kopiert wurde.  

    ![Überwachen der Pipelineausführungen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Sie können dies auch bestätigen, indem Sie den Azure Storage-Explorer (https://storageexplorer.com/) ) verwenden, um die Dateien zu überprüfen.
    
    ![Überwachen der Pipelineausführungen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Erstellen Sie eine weitere leere Textdatei, und nennen Sie sie **file2.txt**. Laden Sie die Datei „file2.txt“ in den Ordnerpfad (**source/2019/02/26/15**) in Ihrem Speicherkonto hoch.   Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).   
    
    ![Überwachen der Pipelineausführungen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Möglicherweise muss ein neuer Ordnerpfad erstellt werden. Passen Sie den Ordnernamen an Ihre UTC-Zeit an.  Wenn die aktuelle UTC-Zeit beispielsweise am 26. Februar 2019 15:20 Uhr ist, können Sie den Ordnerpfad als **source/2019/02/26/15/** gemäß der Regel **{Jahr}/{Monat}/{Tag}/{Stunde}/** erstellen.
    
13. Wählen Sie **All Pipelines Runs** (Alle Pipelineausführungen) aus, und warten Sie, bis die gleiche Pipeline nach einer Stunde erneut automatisch ausgelöst wird, um zur Anzeige **Pipeline Runs** (Pipelineausführungen) zurückzukehren.  

    ![Überwachen der Pipelineausführungen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Wählen Sie für die zweite Pipelineausführung **View Activity Run** (Aktivitätsausführung anzeigen) aus, wenn es soweit ist, und verfahren Sie ebenso, um Details anzuzeigen.  

    ![Überwachen der Pipelineausführungen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Sie können sehen, dass die Quelldatei (file2.txt) mit dem gleichen Dateinamen von **source/2019/02/26/15/** nach **destination/2019/02/26/15/** kopiert wurde.
    
    ![Überwachen der Pipelineausführungen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Sie können dies auch bestätigen, indem Sie den Azure Storage-Explorer (https://storageexplorer.com/) ) verwenden, um die Dateien im Container **destination** zu überprüfen.
    
    ![Überwachen der Pipelineausführungen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe eines Spark-Clusters in Azure Daten transformieren:

> [!div class="nextstepaction"]
>[Transform data using Spark cluster in cloud (Transformieren von Daten mit Spark-Cluster in der Cloud)](tutorial-transform-data-spark-portal.md).