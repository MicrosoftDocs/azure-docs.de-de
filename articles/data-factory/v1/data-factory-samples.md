---
title: Azure Data Factory Editor – Beispiele
description: Bietet Informationen zu Beispielen, die zum Funktionsumfang des Azure Data Factory-Diensts gehören.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 82c871e8914d70dde7f2586b7dbf6556ebd2621f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387035"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory Editor – Beispiele
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Falls Sie die aktuelle Version des Data Factory-Diensts verwenden, lesen Sie [Azure PowerShell-Beispiele für Azure Data Factory](../samples-powershell.md) und [Azure-Codebeispiele](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Beispiele bei GitHub
Das [Azure-DataFactory-Repository auf GitHub](https://github.com/azure/azure-datafactory) enthält mehrere Beispiele, mit deren Hilfe Sie den Azure Data Factory-Dienst rasch in Betrieb nehmen können. Sie können die Skripts auch ändern, um sie in eigenen Anwendungen zu verwenden. Der Ordner "Samples\JSON" enthält JSON-Codeausschnitte für gängige Szenarien.

| Beispiel | BESCHREIBUNG |
|:--- |:--- |
| [ADF: Exemplarische Vorgehensweise](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Dieses Beispiel bietet eine umfassende exemplarische Vorgehensweise zur Verarbeitung von Protokolldateien mithilfe von Azure Data Factory, um sinnvolle Erkenntnisse aus den Protokolldateien zu gewinnen. <br/><br/>Bei dieser Vorgehensweise sammelt die Data Factory-Pipeline Beispielprotokolle, verarbeitet und ergänzt die Daten aus Protokollen durch Verweisdaten und transformiert die Daten zur Bewertung der Effektivität einer vor Kurzem gestarteten Marketingkampagne. |
| [JSON-Beispiele](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Dieses Beispiel enthält JSON-Beispiele für häufige Szenarien. |
| [Beispiel für ein Downloadprogramm für HTTP-Daten](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Dieses Beispiel demonstriert das Herunterladen von Daten von einem HTTP-Endpunkt nach Azure Blob Storage mithilfe einer benutzerdefinierten .NET-Aktivität. |
| [Beispiel für eine anwendungsdomänenübergreifende .NET-Aktivität](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Dieses Beispiel ermöglicht Ihnen das Erstellen einer .NET-Aktivität, die nicht auf die vom Azure Data Factory-Startprogramm verwendeten Assemblyversionen (z.B. WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x usw.) beschränkt ist. |
| [R-Skript ausführen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Dieses Beispiel enthält die benutzerdefinierte Data Factory-Aktivität, die verwendet werden kann, um „RScript.exe“ aufzurufen. Dieses Beispiel funktioniert nur mit Ihrem eigenen (nicht bedarfsgesteuerten) HDInsight-Cluster, auf dem R bereits installiert ist. |
| [Aufrufen von Spark-Aufträgen im HDInsight Hadoop-Cluster](../tutorial-transform-data-spark-portal.md) |Dieses Beispiel zeigt, wie Sie die MapReduce-Aktivität verwenden, um ein Spark-Programm aufzurufen. Das Spark-Programm kopiert ganz einfach Daten von einem Azure-Blobcontainer in einen anderen. |
| [Twitter-Analyse mit der Batchbewertungsaktivität von Azure Machine Learning Studio (klassisch)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Dieses Beispiel zeigt, wie Sie AzureMLBatchScoringActivity verwenden, um ein Azure Machine Learning-Modell aufzurufen, das Twitter-Stimmungsanalysen, -Bewertungen, -Vorhersagen usw. durchführt. |
| [Twitter-Analyse mithilfe der benutzerdefinierten Aktivität](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Dieses Beispiel zeigt, wie Sie eine benutzerdefinierte .NET-Aktivität verwenden, um ein Azure Machine Learning-Modell aufzurufen, das Twitter-Stimmungsanalysen, -Bewertungen, -Vorhersagen usw. durchführt. |
| [Parametrisierte Pipelines für Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Das Beispiel bietet einen End-to-End-C#-Code zum Bereitstellen von N-Pipelines, um jede durch einen anderen Regionsparameter zu bewerten und erneut zu trainieren. Die Liste der Regionen stammt dabei aus der Datei „parameters.txt“, die in diesem Beispiel enthalten ist. |
| [Aktualisierung von Verweisdaten für Azure Stream Analytics-Aufträge](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Dieses Beispiel zeigt, wie Sie Azure Data Factory und Azure Stream Analytics zusammen verwenden, um Abfragen mit Verweisdaten auszuführen und die Aktualisierung von Verweisdaten nach einem Zeitplan einzurichten. |
| [Hybride Pipeline mit lokalem Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Das Beispiel verwendet einen lokalen Hadoop-Cluster als Computeziel für Aufträge, die in Data Factory ausgeführt werden, genauso als ob Sie andere Computeziele wie einen HDInsight-basierten Hadoop-Cluster in der Cloud hinzufügen würden. |
| [JSON-Konvertierungstool](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Mit diesem Tool können Sie JSONs aus Versionen, die älter sind als die 2015-07-01-preview bis zur neuesten oder der 2015-07-01-preview (Standard) konvertieren. |
| [U-SQL-Beispieleingabedatei](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Diese Datei ist eine Beispieldatei, die von einer U-SQL-Aktivität verwendet wird. |
| [Löschen einer Blobdatei](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Dieses Beispiel zeigt eine C#-Datei, die im Rahmen der benutzerdefinierten .net-Aktivität von ADF verwendet werden kann, um Dateien aus dem Quellspeicherort in Azure Blob Storage zu löschen, nachdem die Dateien kopiert wurden.|

## <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen
Auf GitHub finden Sie die folgenden Azure Resource Manager-Vorlagen für Data Factory.

| Vorlage | BESCHREIBUNG |
| --- | --- |
| [Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Durch die Bereitstellung dieser Vorlage wird eine Azure Data Factory-Instanz mit einer Pipeline erstellt, die Daten aus der angegebenen Azure Blob Storage-Instanz in Azure SQL-Datenbank kopiert. |
| [Kopieren von Daten aus Salesforce in Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Durch die Bereitstellung dieser Vorlage wird eine Azure Data Factory-Instanz mit einer Pipeline erstellt, die Daten aus dem angegebenen Salesforce-Konto in die Azure Blob Storage-Instanz kopiert. |
| [Transformieren von Daten durch Ausführen eines Hive-Skripts in einem Azure HDInsight-Cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Durch die Bereitstellung dieser Vorlage wird eine Azure Data Factory-Instanz mit einer Pipeline erstellt, die Daten durch Ausführen des Hive-Beispielskripts in einem Azure HDInsight Hadoop-Cluster transformiert. |

## <a name="samples-in-azure-portal"></a>Beispiele im Azure-Portal
Sie können die Kachel mit den **Beispielpipelines** auf der Startseite Ihrer Data Factory zum Bereitstellen von Beispielpipelines mit den zugehörigen Entitäten (Datasets und verknüpfte Dienste) in Ihrer Data Factory verwenden.

1. Erstellen Sie eine Data Factory, oder öffnen Sie eine vorhandene Data Factory. Schritte zum Erstellen einer Data Factory werden unter [Kopieren von Daten aus Blob Storage in SQL-Datenbank mithilfe von Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) beschrieben.
2. Klicken Sie auf dem Blatt **DATA FACTORY** für die Data Factory auf die Kachel **Beispielpipelines**.

    ![Kachel "Beispielpipelines"](./media/data-factory-samples/SamplePipelinesTile.png)
3. Klicken Sie auf dem Blatt **Beispielpipelines** auf das **Beispiel**, das Sie bereitstellen möchten.

    ![Blatt "Beispielpipelines"](./media/data-factory-samples/SampleTile.png)
4. Geben Sie die Konfigurationseinstellungen für das Beispiel an. Hierzu zählen beispielsweise Ihr Azure Storage-Konto und Ihr Kontoschlüssel, der logische SQL-Servername, die Datenbank, die Benutzer-ID, das Kennwort usw.

    ![Blatt "Beispiel"](./media/data-factory-samples/SampleBlade.png)
5. Wenn Sie mit den Konfigurationseinstellungen fertig sind, klicken Sie auf **Erstellen**, um die Beispielpipelines und die von den Pipelines verwendeten verknüpften Dienste und Tabellen zu erstellen bzw. bereitzustellen.
6. Auf dem Blatt **Beispielpipelines** wird auf der Kachel für das Beispiel, auf die Sie zuvor geklickt haben, der Status der Bereitstellung angezeigt.

    ![Bereitstellungsstatus](./media/data-factory-samples/DeploymentStatus.png)
7. Wenn die Meldung **Bereitstellung erfolgreich** auf der Kachel für das Beispiel angezeigt wird, schließen Sie das Blatt **Beispielpipelines**.  
8. Auf dem Blatt **DATA FACTORY** können Sie sehen, dass Ihrer Data Factory verknüpfte Dienste, Datasets und Pipelines hinzugefügt wurden.  

    ![Blatt "Data Factory"](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Beispiele in Visual Studio
### <a name="prerequisites"></a>Voraussetzungen
Folgendes muss auf Ihrem Computer installiert sein:

* Visual Studio 2013 oder Visual Studio 2015
* Laden Sie das Azure-SDK für Visual Studio 2013 oder Visual Studio 2015 herunter. Navigieren Sie zur [Azure-Downloadseite](https://azure.microsoft.com/downloads/), und klicken Sie auf **VS 2013** oder **VS 2015** im Abschnitt **.NET**.
* Laden Sie das neueste Azure Data Factory-Plug-In für Visual Studio herunter: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) oder [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Wenn Sie Visual Studio 2013 verwenden, können Sie das Plug-In auch aktualisieren, indem Sie die folgenden Schritte ausführen: Klicken Sie im Menü auf **Tools** -> **Erweiterungen und Updates** -> **Online** -> **Visual Studio-Katalog** -> **Microsoft Azure Data Factory-Tools für Visual Studio** -> **Aktualisieren**.

### <a name="use-data-factory-templates"></a>Verwenden von Data Factory-Vorlagen
1. Klicken Sie im Menü auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**.
2. Führen Sie im Dialogfeld **Neues Projekt** die folgenden Schritte aus:

   1. Wählen Sie unter **Vorlagen** die Option **DataFactory** aus.
   2. Wählen Sie im rechten Bereich **Data Factory Templates** (Data Factory-Vorlagen) aus.
   3. Geben Sie einen **Namen** für das Projekt ein.
   4. Wählen Sie einen **Speicherort** für das Projekt aus.
   5. Klicken Sie auf **OK**.

      ![Dialogfeld "Neues Projekt"](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Wählen Sie im Dialogfeld **Data Factory-Vorlagen** die Beispielvorlage aus dem Abschnitt **Anwendungsfall-Vorlagen** aus, und klicken Sie auf **Weiter**. Die folgenden Schritte führen Sie durch die Verwendung der Vorlage **Kundenprofile** . Die Schritte sind bei den anderen Beispielen ähnlich.

    ![Dialogfeld „Data Factory-Vorlagen“](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Klicken Sie im Dialogfeld **Data Factory-Konfiguration** auf der Seite **Data Factory-Grundeinstellungen** auf **Weiter**.
5. Führen Sie auf der Seite **Data Factory konfigurieren** die folgenden Schritte aus:
   1. Wählen Sie **Neue Data Factory erstellen** aus. Sie können auch **Use existing data factory**(Vorhandene Data Factory verwenden) auswählen.
   2. Geben Sie einen **Namen** für die Data Factory ein.
   3. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
   4. Wählen Sie die **Ressourcengruppe** für die Data Factory aus.
   5. Wählen Sie **USA, Westen**, **USA, Osten** oder **Europa, Norden** als **Region** aus.
   6. Klicken Sie auf **Weiter**.
6. Geben Sie auf der Seite **Datenspeicher konfigurieren** eine vorhandene **Datenbank in Azure SQL-Datenbank** und ein **Azure Storage-Konto** an, oder erstellen Sie eine Datenbank/einen Speicher, und klicken Sie dann auf „Weiter“.
7. Übernehmen Sie auf der Seite **Compute konfigurieren** die Standardwerte, und klicken Sie auf **Weiter**.
8. Überprüfen Sie auf der Seite **Zusammenfassung** alle Einstellungen, und klicken Sie auf **Weiter**.
9. Warten Sie auf der Seite **Bereitstellungsstatus**, bis die Bereitstellung abgeschlossen ist, und klicken Sie auf **Fertig stellen**.
10. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen**.
11. Wenn das Dialogfeld **Melden Sie sich bei Ihrem Microsoft-Konto an** angezeigt wird, geben Sie Ihre Anmeldeinformationen für das Konto mit dem Azure-Abonnement ein, und klicken Sie auf **Anmelden**.
12. Das folgende Dialogfeld sollte angezeigt werden:

    ![Dialogfeld „Veröffentlichen“](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Führen Sie auf der Seite **Data Factory konfigurieren** die folgenden Schritte aus:

    1. Stellen Sie sicher, dass die Option **Use existing data factory** (Vorhandene Data Factory verwenden) aktiviert ist.
    2. Wählen Sie die **Data Factory** aus, die Sie für die Verwendung der Vorlage ausgewählt hatten.
    3. Klicken Sie auf **Weiter**, um zur Seite **Publish Items** zu wechseln. (Drücken Sie die **TAB-TASTE**, um das Feld „Name“ zu verlassen, wenn die Schaltfläche **Weiter** deaktiviert ist.)
14. Stellen Sie auf der Seite **Publish Items** sicher, dass alle Data Factory-Entitäten ausgewählt sind, und klicken Sie auf **Weiter**, um zur Seite **Zusammenfassung** zu wechseln.     
15. Prüfen Sie die Zusammenfassung, und klicken Sie auf **Weiter**, um den Bereitstellungsprozess zu starten und den **Bereitstellungsstatus** anzuzeigen.
16. Auf der Seite **Bereitstellungsstatus** sollte der Status des Bereitstellungsprozesses angezeigt werden. Klicken Sie auf „Fertig stellen“, nachdem die Bereitstellung abgeschlossen ist.

Einzelheiten zur Verwendung von Visual Studio zum Erstellen von Data Factory-Entitäten und ihrer Veröffentlichung in Azure finden Sie unter [Erstellen der ersten Azure Data Factory mit Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) .