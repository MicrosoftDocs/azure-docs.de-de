---
title: Inkrementelles Kopieren neuer Dateien basierend auf dem zeitpartitionierten Dateinamen
description: Erstellen Sie eine Azure Data Factory, und verwenden Sie dann das Tool „Daten kopieren“, um neue Dateien nur basierend auf dem zeitpartitionierten Dateinamen zu laden.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 8e68852434a4a8bea43b575523a60c9346b2a569
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384774"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Inkrementelles Kopieren neuer Dateien basierend auf dem zeitpartitionierten Dateinamen und mithilfe des Tools „Daten kopieren“

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

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
* **Azure-Speicherkonto**: Verwenden Sie den Blobspeicher als _Quelldatenspeicher_ und als _Senkendatenspeicher_. Falls Sie noch nicht über ein Azure-Speicherkonto verfügen, finden Sie Anweisungen dazu unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Erstellen von zwei Containern im Blobspeicher

Bereiten Sie Ihren Blobspeicher folgendermaßen auf das Tutorial vor.

1. Erstellen Sie einen Container mit dem Namen **source** (Quelle).  Erstellen Sie in Ihrem Container den Ordnerpfad **2020/03/17/03**. Erstellen Sie eine leere Textdatei, und nennen Sie sie **file1.txt**. Laden Sie die Datei „file1.txt“ in den Ordnerpfad (**source/2020/03/17/03**) in Ihrem Speicherkonto hoch.  Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

    ![Hochladen von Dateien](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Passen Sie den Ordnernamen an Ihre UTC-Zeit an.  Wenn die aktuelle UTC-Zeit beispielsweise „17. März 2020, 03:38 Uhr“ lautet, erstellen Sie gemäß dem Format **source/{Jahr}/{Monat}/{Tag}/{Stunde}/** den Ordnerpfad **source/2020/03/17/03/** .

2. Erstellen Sie einen Container mit dem Namen **destination** (Ziel). Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus:

   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

    Der Name der Data Factory muss _global eindeutig_ sein. Sie erhalten unter Umständen die folgende Fehlermeldung:

   ![Fehlermeldung zu neuer Data Factory](./media/doc-common-process/name-not-available-error.png)

   Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Verwenden Sie beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory**. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
3. Wählen Sie das **Azure-Abonnement** aus, in dem die neue Data Factory erstellt werden soll.
4. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    a. Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

    b. Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein. 
         
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).

5. Wählen Sie unter **Version** die Option **V2**.
6. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich an anderen Standorten und in anderen Regionen befinden.
7. Klicken Sie auf **Erstellen**.
8. Nach Abschluss der Erstellung wird die Startseite von **Data Factory** angezeigt.
9. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.

    ![Data Factory-Startseite](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Klicken Sie auf der Seite **Let's get started** (Erste Schritte) auf **Copy Data** (Daten kopieren), um das Tool zum Kopieren von Daten zu starten.

   ![Kachel für das Tool zum Kopieren von Daten](./media/doc-common-process/get-started-page.png)

2. Gehen Sie auf der Seite **Properties** (Eigenschaften) wie folgt vor:

    a. Geben Sie unter **Task name** (Aufgabenname) **DeltaCopyFromBlobPipeline** ein.

    b. Wählen Sie unter **Task cadence or Task schedule** (Aufgabenhäufigkeit oder Aufgabenzeitplan) die Option **Run regularly on schedule** (Nach Zeitplan regelmäßig ausführen) aus.

    c. Wählen Sie unter **Trigger type** (Triggertyp) die Option **Tumbling Window** (Rollierendes Fenster) aus.

    d. Geben Sie unter **Recurrence** (Wiederholung) **1 Hour(s)** (1 Stunde[n]) ein.

    e. Wählen Sie **Weiter** aus.

    Über die Data Factory-Benutzeroberfläche wird eine Pipeline mit dem angegebenen Tasknamen erstellt.

    ![Eigenschaftenseite](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

    a. Klicken Sie auf **+ Neue Verbindung erstellen**, um eine Verbindung hinzuzufügen.
    
    b. Wählen Sie im Katalog „Azure Blob Storage“ aus, und klicken Sie dann auf „Fortsetzen“.
    
    c. Geben Sie auf der Seite **New Linked Service (Azure Blob Storage)** (Neuer verknüpfter Dienst [Azure Blob Storage]) einen Namen für den verknüpften Dienst ein. Wählen Sie Ihr Azure-Abonnement und anschließend in der Liste **Speicherkontoname** Ihr Speicherkonto aus. Testen Sie die Verbindung, und wählen Sie **Fertig stellen** aus.

    ![Seite „Quelldatenspeicher“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Wählen Sie den neu erstellten verknüpften Dienst auf der Seite **Source data store** (Quelldatenspeicher) aus, und klicken Sie auf **Weiter**.

4. Führen Sie auf der Seite **Choose the input file or folder** (Eingabedatei oder -ordner auswählen) die folgenden Schritte aus:

    a. Suchen Sie nach dem Container **source**, wählen Sie ihn aus, und klicken Sie anschließend auf **Choose** (Wählen).

    ![Screenshot des Dialogfelds zum Auswählen der Eingabedatei bzw. des Eingabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. Wählen Sie unter **File loading behavior** (Dateiladeverhalten) die Option **Incremental load: time-partitioned folder/file names** (Inkrementell laden: zeitpartionierter Ordner/Dateinamen).

    c. Schreiben Sie den dynamischen Ordnerpfad im Format **source/{Jahr}/{Monat}/{Tag}/{Stunde}/** , und ändern Sie das Format wie im folgenden Screenshot. Aktivieren Sie das Kontrollkästchen **Binary copy** (Binärkopie), und klicken Sie auf **Next** (Weiter).

    ![Screenshot des Dialogfelds zum Auswählen der Eingabedatei bzw. des Eingabeordners mit ausgewähltem Ordner](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Wählen Sie auf der Seite **Destination data store** (Zieldatenspeicher) **AzureBlobStorage** aus, also das gleiche Speicherkonto wie beim Datenquellenspeicher, und klicken Sie anschließend auf **Next** (Weiter).

    ![Seite „Zieldatenspeicher“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Führen Sie auf der Seite **Choose the output file or folder** (Ausgabedatei oder -ordner auswählen) die folgenden Schritte aus:

    a. Suchen Sie nach dem Ordner **destination** (Ziel), wählen Sie diesen aus, und klicken Sie anschließend auf **Choose** (Wählen).

    b. Schreiben Sie den dynamischen Ordnerpfad als **destination/{Jahr}/{Monat}/{Tag}/{Stunde}/** , und ändern Sie das Format wie folgt:

    ![Screenshot des Dialogfelds zum Auswählen der Ausgabedatei bzw. des Ausgabeordners](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Klicken Sie auf **Weiter**.

    ![Screenshot des Dialogfelds zum Auswählen der Ausgabedatei bzw. des Ausgabeordners mit ausgewählter Option „Weiter“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**.

8. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **Weiter**.

    ![Seite „Zusammenfassung“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen.
    ![Bereitstellungsseite](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist.  Sie müssen auf die Ausführung der Pipeline warten, wenn sie (etwa nach einer Stunde) automatisch ausgelöst wird. Klicken Sie während der Ausführung auf den Link auf dem Pipelinenamen **DeltaCopyFromBlobPipeline**, um Details zur Aktivitätsausführung aufzurufen oder die Pipeline erneut auszuführen. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**.

    ![Screenshot des Bereichs „Pipelineausführungen“](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
11. Da die Pipeline nur eine Aktivität (Copy-Aktivität) enthält, wird nur ein Eintrag angezeigt. Passen Sie die Breite der Spalten **Quelle** und **Ziel** bei Bedarf an, um weitere Details aufzurufen. Ihnen wird angezeigt, dass die Quelldatei (file1.txt) mit demselben Dateinamen von *source/2020/03/17/03/* nach *destination/2020/03/17/03/* kopiert wurde. 

    ![Screenshot der Details zur Pipelineausführung](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Sie können dies auch bestätigen, indem Sie den Azure Storage-Explorer (https://storageexplorer.com/) ) verwenden, um die Dateien zu überprüfen.

    ![Screenshot der Details zur Pipelineausführung für das Ziel](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Erstellen Sie eine weitere leere Textdatei, und nennen Sie sie **file2.txt**. Laden Sie die Datei „file2.txt“ in den Ordnerpfad **source/2020/03/17/04** in Ihrem Speicherkonto hoch. Sie können für diese Aufgaben verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > Möglicherweise muss ein neuer Ordnerpfad erstellt werden. Passen Sie den Ordnernamen an Ihre UTC-Zeit an.  Wenn die aktuelle UTC-Zeit beispielsweise „17. März 2020, 04:20 Uhr“ lautet, können Sie gemäß dem Format **{Jahr}/{Monat}/{Tag}/{Stunde}/** den Ordnerpfad **source/2020/03/17/04/** erstellen.

13. Wählen Sie **All Pipelines runs** (Alle Pipelineausführungen) aus, und warten Sie, bis die gleiche Pipeline nach einer Stunde wiederholt automatisch ausgelöst wird, um zur Ansicht **Pipeline Runs** (Pipelineausführungen) zurückzukehren.  

    ![Screenshot des Links „Alle Pipelineausführungen“ für die Rückkehr zu dieser Seite](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Klicken Sie für die zweite Pipelineausführung auf den neuen Link **DeltaCopyFromBlobPipeline**, wenn es soweit ist, und tun Sie dasselbe, um Details anzuzeigen. Ihnen wird angezeigt, dass die Quelldatei (file2.txt) mit demselben Dateinamen von **source/2020/03/17/04/** nach **destination/2020/03/17/04/** kopiert wurde. Sie können dies auch überprüfen, indem Sie den Azure Storage-Explorer (https://storageexplorer.com/) ) verwenden, um die Dateien im Container **destination** zu überprüfen.


## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe eines Spark-Clusters in Azure Daten transformieren:

> [!div class="nextstepaction"]
>[Transform data using Spark cluster in cloud (Transformieren von Daten mit Spark-Cluster in der Cloud)](tutorial-transform-data-spark-portal.md).
