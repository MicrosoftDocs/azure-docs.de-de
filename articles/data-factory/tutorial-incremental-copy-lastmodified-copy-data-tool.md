---
title: Datentool zum schrittweisen Kopieren neuer und aktualisierter Dateien
description: Erstellen Sie eine Azure Data Factory, und verwenden Sie dann das Tool zum Kopieren von Daten, um neue Dateien auf Basis von „LastModifiedDate“ inkrementell zu laden.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: f2f0b3e452b39cb81f435dbee4a3b0f524b0213d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361144"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Inkrementelles Kopieren neuer und geänderter Dateien auf Basis von LastModifiedDate und mithilfe des Tools zum Kopieren von Daten

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend verwenden Sie das Tool zum Kopieren von Daten, um eine Pipeline zu erstellen, die neue und geänderte Dateien inkrementell von einem in den anderen Azure-Blobspeicher kopiert. Hierbei wird `LastModifiedDate` verwendet, um die zu kopierenden Dateien zu bestimmen.

Nachdem Sie die hier beschriebenen Schritte ausgeführt haben, überprüft Azure Data Factory alle Dateien im Quellspeicher, wendet den Filter über `LastModifiedDate` an und kopiert nur diejenigen Dateien in den Zielspeicher, die seit dem letzten Mal neu erstellt oder aktualisiert wurden. Beachten Sie, dass Data Factory zum Überprüfen einer großen Anzahl von Dateien dennoch sehr viel Zeit benötigt. Die Dateiüberprüfung ist sehr zeitintensiv – selbst dann, wenn die Menge der kopierten Daten verringert wird.

> [!NOTE]
> Falls Sie noch nicht mit Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

Dieses Tutorial umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Storage-Konto**: Verwenden Sie den Blobspeicher als Quell- und als Senkendatenspeicher. Falls Sie noch nicht über ein Azure Storage-Konto verfügen, finden Sie [hier](../storage/common/storage-account-create.md) eine Anleitung zum Erstellen eines Speicherkontos.

## <a name="create-two-containers-in-blob-storage"></a>Erstellen von zwei Containern im Blobspeicher

Bereiten Sie Ihren Blobspeicher folgendermaßen für das Tutorial vor:

1. Erstellen Sie einen Container mit dem Namen **source** (Quelle). Sie können für diese Aufgabe verschiedene Tools verwenden, z. B. [Azure Storage-Explorer](https://storageexplorer.com/).

2. Erstellen Sie einen Container mit dem Namen **destination** (Ziel).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im linken Bereich **Ressource erstellen**. Wählen Sie **Integration** > **Data Factory** aus:

   ![Data Factory auswählen](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

   Der Name der Data Factory muss global eindeutig sein. Möglicherweise wird diese Fehlermeldung angezeigt:

   ![Fehlermeldung: Name nicht verfügbar](./media/doc-common-process/name-not-available-error.png)

   Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Verwenden Sie beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory**. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
3. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, in dem Sie die neue Data Factory erstellen werden.
4. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    * Wählen Sie die Option **Vorhandene verwenden** und dann in der Liste eine vorhandene Ressourcengruppe aus.

    * Wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
         
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).

5. Wählen Sie unter **Version** die Option **V2**.
6. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Liste werden nur unterstützte Standorte angezeigt. Die von der Data Factory genutzten Datenspeicher (z. B. Azure Storage und Azure SQL-Datenbank) und Computeressourcen (z. B. Azure HDInsight) können sich an anderen Standorten und in anderen Regionen befinden.
8. Klicken Sie auf **Erstellen**.
9. Nachdem die Data Factory erstellt wurde, wird die Startseite für die Data Factory angezeigt.
10. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Azure Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu öffnen:

    ![Data Factory-Startseite](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Klicken Sie auf der Seite **Erste Schritte** auf die Kachel **Daten kopieren**, um das Tool zum Kopieren von Daten zu öffnen:

   ![Kachel „Daten kopieren“](./media/doc-common-process/get-started-page.png)

2. Gehen Sie auf der Seite **Eigenschaften** wie folgt vor:

    a. Geben Sie unter **Aufgabenname** den Wert **DeltaCopyFromBlobPipeline** ein.

    b. Wählen Sie unter **Aufgabenhäufigkeit oder Aufgabenzeitplan** die Option **Nach Zeitplan regelmäßig ausführen** aus.

    c. Wählen Sie unter **Triggertyp** die Option **Rollierendes Fenster** aus.

    d. Geben Sie unter **Wiederholung** den Wert **15 Minuten** ein.

    e. Wählen Sie **Weiter** aus.

    Über die Data Factory-Benutzeroberfläche wird eine Pipeline mit dem angegebenen Aufgabennamen erstellt.

    ![Bereich „Eigenschaften“ von „Daten kopieren“](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

    a. Wählen Sie **+ Neue Verbindung erstellen** aus, um eine Verbindung hinzuzufügen.

    b. Wählen Sie im Katalog **Azure Blob Storage** aus, und klicken Sie dann auf **Weiter**:

    ![Azure-Blobspeicher auswählen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Wählen Sie auf der Seite **Neuer verknüpfter Dienst (Azure Blob Storage)** in der Liste **Speicherkontoname** Ihr Speicherkonto aus. Testen Sie die Verbindung, und klicken Sie auf **Erstellen**.

    d. Wählen Sie den neu erstellten verknüpften Dienst aus, und klicken Sie dann auf **Weiter**:

   ![Auswählen des neuen verknüpften Diensts](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Führen Sie auf der Seite **Eingabedatei oder -ordner auswählen** die folgenden Schritte aus:

    a. Suchen Sie nach dem Ordner **source**, wählen Sie ihn aus, und klicken Sie anschließend auf **Auswählen**.

    ![Auswählen der Eingabedatei bzw. des Eingabeordners](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Wählen Sie unter **Dateiladeverhalten** die Option **Inkrementelles Laden: LastModifiedDate** aus.

    c. Aktivieren Sie das Kontrollkästchen **Binärkopie**, und klicken Sie auf **Weiter**:

     ![Seite „Eingabedatei oder -ordner auswählen“](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Wählen Sie auf der Seite **Zieldatenspeicher** den von Ihnen erstellten **AzureBlobStorage** aus. Dies ist dasselbe Speicherkonto wie der Quelldatenspeicher. Wählen Sie **Weiter** aus.

6. Führen Sie auf der Seite **Ausgabedatei oder -ordner auswählen** die folgenden Schritte aus:

    a. Suchen Sie nach dem Ordner **destination**, und klicken Sie anschließend auf **Auswählen**:

    ![Seite „Ausgabedatei oder -ordner auswählen“](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Wählen Sie **Weiter** aus.

7. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**.

8. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **Weiter**.

    ![Seite „Zusammenfassung“](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Aufgabe) zu überwachen.

    ![Bereitstellungsseite](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. Die Anwendung wechselt zur Registerkarte **Überwachen**. Dort wird der Status der Pipeline angezeigt. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. Klicken Sie auf den Link unter **PIPELINENAME**, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen.

    ![Aktualisieren der Liste und Anzeigen von Details zur Aktivitätsausführung](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. Da die Pipeline nur eine Aktivität (die Kopieraktivität) enthält, wird nur ein Eintrag angezeigt. Wenn Sie Details zum Kopiervorgang anzeigen möchten, wählen Sie in der Spalte **AKTIVITÄTSNAME** den Link **Details** (das Brillensymbol) aus. Einzelheiten zu den Eigenschaften finden Sie in der [Übersicht zur Kopieraktivität](copy-activity-overview.md).

    ![Kopieraktivität in der Pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Da sich im Quellcontainer in Ihrem Blobspeicherkonto keine Datei befindet, wird in Ihrem Blobspeicherkonto keine Datei angezeigt, die in den Zielcontainer kopiert wurde:

    ![Keine Dateien in Quell- oder Zielcontainer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Erstellen Sie eine leere Textdatei, und nennen Sie sie **file1.txt**. Laden Sie diese Textdatei in den Quellcontainer in Ihrem Speicherkonto hoch. Sie können für diese Aufgaben verschiedene Tools verwenden, beispielsweise [Azure Storage-Explorer](https://storageexplorer.com/).

    ![Erstellen von „file1.txt“ und Hochladen in den Quellcontainer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Wenn Sie zur Ansicht **Pipelineausführungen** zurückkehren möchten, wählen Sie **Alle Pipelineausführungen** aus, und warten Sie, bis dieselbe Pipeline erneut automatisch ausgelöst wird.  

14. Wenn die zweite Pipelineausführung abgeschlossen ist, führen Sie die zuvor genannten Schritte aus, um die Details zur Aktivitätsausführung zu überprüfen.  

    Sie werden sehen, dass eine Datei („file1.txt“) aus dem Quellcontainer in den Zielcontainer Ihres Blobspeicherkontos kopiert wurde:

    ![Datei „file1.txt“ wurde aus dem Quellcontainer in den Zielcontainer kopiert](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Erstellen Sie eine weitere leere Textdatei, und benennen Sie diese **file2.txt**. Laden Sie diese Textdatei in den Quellcontainer in Ihrem Blobspeicherkonto hoch.

16. Wiederholen Sie die Schritte 13 und 14 für die zweite Textdatei. Sie werden bemerken, dass nur die neue Datei („file2.txt“) bei der nächsten Pipelineausführung vom Quellcontainer in den Zielcontainer Ihres Speicherkontos kopiert wurde.  

    Sie können dies auch bestätigen, indem Sie die Dateien mit dem [Azure Storage-Explorer](https://storageexplorer.com/) überprüfen:

    ![Überprüfen von Dateien mit dem Azure Storage-Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe eines Apache Spark-Clusters in Azure Daten transformieren:

> [!div class="nextstepaction"]
>[Transformieren von Daten in der Cloud mithilfe von Apache Spark-Clustern](tutorial-transform-data-spark-portal.md)
