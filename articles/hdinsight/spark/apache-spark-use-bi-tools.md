---
title: 'Tutorial: Analysieren von Apache Spark-Daten mithilfe von Power BI in Azure HDInsight '
description: Verwenden von Microsoft Power BI zum Visualisieren von Apache Spark-Daten, die in HDInsight-Clustern gespeichert sind
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: bf70abd2b3119a97af5ad1d4c56274f8e575fd3e
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860972"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analysieren von Apache Spark-Daten mithilfe von Power BI in HDInsight

In diesem Tutorial erfahren Sie, wie Sie mit [Microsoft Power BI](https://powerbi.microsoft.com/) Daten in einem Apache Spark-Cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) visualisieren.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Visualisieren von Spark-Daten mithilfe von Power BI

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Lesen Sie den Artikel [Tutorial: Laden von Daten und Ausführen von Abfragen auf einem Apache Spark-Cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Optional: [Power BI-Testabonnement](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Überprüfen der Daten

Das im [vorherigen Tutorial](apache-spark-load-data-run-query.md) erstellte [Jupyter Notebook](https://jupyter.org/) enthält Code zum Erstellen einer `hvac`-Tabelle. Diese Tabelle basiert auf der CSV-Datei, die in allen HDInsight Spark-Clustern unter `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` verfügbar ist. Gehen Sie folgendermaßen vor, um die Daten zu überprüfen.

1. Fügen Sie im Jupyter Notebook den folgenden Code ein, und drücken Sie **UMSCHALT+EINGABETASTE**. Der Code überprüft das Vorhandensein der Tabellen.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Die Ausgabe sieht wie folgt aus:

    ![Anzeigen von Tabellen in Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Wenn Sie das Notebook vor dem Starten dieses Tutorials geschlossen haben, wird `hvactemptable` bereinigt und ist daher nicht in der Ausgabe enthalten.  Nur Hive-Tabellen, die im Metastore gespeichert werden (angegeben durch **False** in der Spalte **isTemporary**), sind für die BI-Tools zugänglich. In diesem Tutorial stellen Sie eine Verbindung mit der erstellten Tabelle **hvac** her.

2. Fügen Sie den folgenden Code in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**. Der Code überprüft die Daten in der Tabelle.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Die Ausgabe sieht wie folgt aus:

    ![Anzeigen von Zeilen aus der hvac-Tabelle in Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Wählen Sie im Menü **Datei** des Notebooks die Option **Schließen und Anhalten** aus. Fahren Sie das Notebook herunter, um die Ressourcen freizugeben.

## <a name="visualize-the-data"></a>Visualisieren der Daten

In diesem Abschnitt verwenden Sie Power BI, um Visualisierungen, Berichte und Dashboards aus den Daten im Spark-Cluster zu erstellen.

### <a name="create-a-report-in-power-bi-desktop"></a>Erstellen eines Berichts in Power BI Desktop

Die ersten Schritte bei der Verwendung von Spark bestehen darin, eine Verbindung mit dem Cluster in Power BI Desktop herzustellen, Daten aus dem Cluster zu laden und eine grundlegende Visualisierung auf der Grundlage dieser Daten zu erstellen.

> [!NOTE]  
> Der in diesem Artikel vorgestellte Connector befindet sich derzeit in der Vorschau. Wenn Sie Feedback abgeben möchten, ist dies über die [Power BI-Communitywebsite](https://community.powerbi.com/) und [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) möglich.

1. Öffnen Sie Power BI Desktop. Schließen Sie den Begrüßungsbildschirm, wenn er geöffnet ist.

2. Navigieren Sie auf der Registerkarte **Home** zu **Daten abrufen** > **Mehr..** .

    ![Abrufen von Daten in Power BI Desktop aus HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Abrufen von Daten in Power BI aus Apache Spark BI")

3. Geben Sie `Spark` in das Suchfeld ein, wählen Sie **Azure HDInsight Spark** aus, und wählen Sie dann **Verbinden** aus.

    ![Einlesen von Daten in Power BI aus Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Einlesen von Daten in Power BI aus Apache Spark BI")

4. Geben Sie Ihre Cluster-URL (in der Form `mysparkcluster.azurehdinsight.net`) in das Textfeld **Server** ein.

5. Wählen Sie unter **Datenverbindungsmodus:** die Option **DirectQuery** aus. Wählen Sie dann **OK**aus.

    Sie können mit Spark beide Datenkonnektivitätsmodi verwenden. Wenn Sie „DirectQuery“ verwenden, werden Änderungen in Berichten ohne Aktualisierung des gesamten Datasets wiedergegeben. Wenn Sie Daten importieren, müssen Sie das Dataset aktualisieren, um die Änderungen zu sehen. Weitere Informationen dazu, wie und wann Sie „DirectQuery“ verwenden, finden Sie unter [Verwenden von DirectQuery mit Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Geben Sie die Kontoinformationen für die HDInsight-Anmeldung ein, und wählen Sie dann **Verbinden** aus. Der Standardkontoname lautet *admin*.

7. Wählen Sie die Tabelle `hvac` aus, warten Sie, um eine Vorschau der Daten anzuzeigen, und wählen Sie dann **Laden** aus.

    ![Benutzername und Kennwort des Spark-Clusters](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Benutzername und Kennwort des Spark-Clusters")

    Power BI Desktop verfügt nun über alle Informationen, die zum Herstellen einer Verbindung mit dem Spark-Cluster und zum Laden von Daten aus der Tabelle `hvac` erforderlich sind. Die Tabelle und ihre Spalten werden im Bereich **Felder** angezeigt.

8. Visualisieren Sie die Abweichung zwischen Zieltemperatur und Ist-Temperatur für jedes Gebäude:

    1. Wählen Sie im Bereich **VISUALISIERUNGEN** die Option **Flächendiagramm** aus.

    2. Ziehen Sie das Feld **BuildingID** unter **Achse** und die Felder **ActualTemp** und **TargetTemp** unter **Wert**.

        ![Erstellen von Spark-Datenvisualisierungen mithilfe von Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Erstellen von Spark-Datenvisualisierungen mithilfe von Apache Spark BI")

        Das Diagramm sieht wie folgt aus:

        ![Erstellen von Spark-Datenvisualisierungen mithilfe von Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Erstellen von Spark-Datenvisualisierungen mithilfe von Apache Spark BI")

        Standardmäßig werden in der Visualisierung die Summen für **ActualTemp** und **TargetTemp** angezeigt. Wählen Sie den Pfeil nach unten neben **ActualTemp** und **TargetTemp** im Bereich „Visualisierungen“ aus. Sie sehen, dass **Summe** ausgewählt ist.

    3. Wählen Sie den Pfeil nach unten neben **ActualTemp** und **TargetTemp** im Bereich „Visualisierungen“ aus, wählen Sie **Durchschnitt** aus, um den Durchschnittswert zwischen tatsächlicher und Zieltemperatur für jedes Gebäude zu erhalten.

        ![Erstellen von Spark-Datenvisualisierungen mithilfe von Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Erstellen von Spark-Datenvisualisierungen mithilfe von Apache Spark BI")

        Ihre Datenvisualisierung sollte ähnlich dem Screenshot aussehen. Bewegen Sie den Cursor über die Visualisierung, um QuickInfos mit relevanten Daten abzurufen.

        ![Erstellen von Spark-Datenvisualisierungen mithilfe von Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Erstellen von Spark-Datenvisualisierungen mithilfe von Apache Spark BI")

9. Navigieren Sie zu **Datei** > **Speichern**, geben Sie den Namen `BuildingTemperature` für die Datei ein, und wählen Sie dann **Speichern** aus.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Veröffentlichen des Berichts im Power BI-Dienst (optional)

Mit dem Power BI-Dienst können Sie Berichte und Dashboards in Ihrer Organisation freigeben. In diesem Abschnitt veröffentlichen Sie zunächst das DataSet und den Bericht. Dann heften Sie den Bericht an ein Dashboard an. Dashboards werden üblicherweise dazu verwendet, sich auf eine Teilmenge der Daten in einem Bericht zu konzentrieren. Ihr Bericht enthält zwar nur eine Visualisierung, es ist aber dennoch hilfreich, die Schritte durchzugehen.

1. Öffnen Sie Power BI Desktop.
2. Klicken Sie auf der Registerkarte **Start** auf **Veröffentlichen**.

    ![Veröffentlichen über Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Veröffentlichen über Power BI Desktop")

2. Wählen Sie einen Arbeitsbereich für die Veröffentlichung des Datasets und des Berichts aus, und klicken Sie dann auf **Auswählen**. In der folgenden Abbildung wird die Standardoption **My Workspace** (Mein Arbeitsbereich) ausgewählt.

    ![Auswählen eines Arbeitsbereichs für die Veröffentlichung des Datasets und des Berichts](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Auswählen eines Arbeitsbereichs für die Veröffentlichung des Datasets und des Berichts") 

3. Nachdem die Veröffentlichung abgeschlossen ist, klicken Sie auf **Open „BuildingTemperature.pbix“ in Power BI** („BuildingTemperature.pbix“ in Power BI öffnen).

    ![Veröffentlichung erfolgreich, zur Eingabe von Anmeldeinformationen klicken](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Veröffentlichung erfolgreich, zur Eingabe von Anmeldeinformationen klicken") 

4. Klicken Sie im Power BI-Dienst auf **Anmeldeinformationen eingeben**.

    ![Eingeben von Anmeldeinformationen im Power BI-Dienst](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Eingeben von Anmeldeinformationen im Power BI-Dienst")

5. Klicken Sie auf **Anmeldeinformationen bearbeiten**.

    ![Bearbeiten von Anmeldeinformationen im Power BI-Dienst](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Bearbeiten von Anmeldeinformationen im Power BI-Dienst")

6. Geben Sie die Kontoinformationen für die HDInsight-Anmeldung ein, und klicken Sie auf **Anmelden**. Der Standardkontoname lautet *admin*.

    ![Anmelden beim Spark-Cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Anmelden beim Spark-Cluster")

7. Navigieren Sie im linken Bereich zu **Arbeitsbereiche** > **Mein Arbeitsbereich** > **BERICHTE**, und klicken Sie auf **BuildingTemperature**.

    ![Bericht unter „Berichte“ im linken Bereich](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Bericht unter „Berichte“ im linken Bereich")

    Im linken Bereich sollte unter **DATASETS** außerdem **BuildingTemperature** aufgeführt sein.

    Das in Power BI Desktop erstellte visuelle Element ist nun im Power BI-Dienst verfügbar. 

8. Zeigen Sie mit der Maus auf die Visualisierung, und klicken Sie dann auf das Symbol zum Anheften in der rechten oberen Ecke.

    ![Bericht im Power BI-Dienst](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Bericht im Power BI-Dienst")

9. Wählen Sie „Neues Dashboard“, geben Sie den Namen `Building temperature` ein, und klicken Sie dann auf **Anheften**.

    ![An neues Dashboard anheften](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "An neues Dashboard anheften")

10. Klicken Sie im Bericht auf **Zum Dashboard wechseln**. 

Ihr visuelles Element wird an das Dashboard angeheftet. Sie können weitere visuelle Elemente zum Bericht hinzufügen und sie ans gleiche Dashboard anheften. Weitere Informationen zu Berichten und Dashboards finden Sie unter [Berichte in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) und [Einführung in Dashboards für Power BI-Designer](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss des Tutorials kann es ratsam sein, den Cluster zu löschen. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Informationen zum Löschen eines Clusters finden Sie unter [Löschen eines HDInsight-Clusters mit Ihrem Browser, PowerShell oder der Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie mit [Microsoft Power BI](https://powerbi.microsoft.com/) Daten in einem Apache Spark-Cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) visualisieren. Fahren Sie mit dem nächsten Artikel fort, um festzustellen, wie die Daten, die Sie in Spark registriert haben, in ein BI-Analyse-Tool wie Power BI gezogen werden können.

> [!div class="nextstepaction"]
> [Tutorial: Verarbeiten von Tweets mit Azure Event Hubs und Spark in HDInsight](apache-spark-eventhub-streaming.md)