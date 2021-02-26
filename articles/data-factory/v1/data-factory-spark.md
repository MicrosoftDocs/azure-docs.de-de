---
title: Aufrufen von Spark-Programmen aus Azure Data Factory
description: Erfahren Sie, wie Sie Spark-Programme mithilfe der MapReduce-Aktivität aus einer Azure Data Factory aufrufen.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a7fcccad039ff6d737e10cfc57ba97f914645a9d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382666"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Aufrufen von Spark-Programmen aus Azure Data Factory-Pipelines

> [!div class="op_single_selector" title1="Transformationsaktivitäten"]
> * [Hive-Aktivität](data-factory-hive-activity.md)
> * [Pig-Aktivität](data-factory-pig-activity.md)
> * [MapReduce-Aktivität](data-factory-map-reduce.md)
> * [Hadoop-Streamingaktivität](data-factory-hadoop-streaming-activity.md)
> * [Spark-Aktivität](data-factory-spark.md)
> * [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-batch-execution-activity.md)
> * [Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md)
> * [U-SQL-Aktivität für Data Lake Analytics](data-factory-usql-activity.md)
> * [Benutzerdefinierte .NET-Aktivität](data-factory-use-custom-activities.md)

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory, die allgemein verfügbar ist. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Transformieren von Daten mithilfe der Apache Spark-Aktivität in Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Einführung
Die Spark-Aktivität ist eine der [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md), die von Data Factory unterstützt werden. Diese Aktivität führt das angegebene Spark-Programm auf dem Spark-Cluster in Azure HDInsight aus.

> [!IMPORTANT]
> - Die Spark-Aktivität unterstützt keine HDInsight Spark-Cluster, die Azure Data Lake Store als primären Speicher verwenden.
> - Die Spark-Aktivität unterstützt nur vorhandene (Ihre eigenen) HDInsight Spark-Cluster. Bedarfsgesteuerte verknüpfte HDInsight-Dienste werden nicht unterstützt.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Exemplarische Vorgehensweise: Erstellen einer Pipeline mit einer Spark-Aktivität
Dies sind die typischen Schritte zum Erstellen einer Data Factory-Pipeline mit einer Spark-Aktivität:

* Erstellen einer Data Factory.
* Erstellen Sie einen verknüpften Azure Storage-Dienst, um Speicher, der Ihrem HDInsight Spark-Cluster zugeordnet ist, mit der Data Factory zu verknüpfen.
* Erstellen Sie einen verknüpften HDInsight-Dienst, um Ihren Spark-Cluster in HDInsight mit der Data Factory zu verknüpfen.
* Erstellen Sie ein Dataset, das auf den mit Storage verknüpften Dienst verweist. Derzeit müssen Sie ein Ausgabedataset für eine Aktivität angeben, auch wenn keine Ausgabe erzeugt wird.
* Erstellen Sie eine Pipeline mit Spark-Aktivität, die auf den erstellten verknüpften HDInsight-Dienst verweist. Die Aktivität wird mit dem Dataset, das Sie im vorherigen Schritt erstellt haben, als Ausgabedataset konfiguriert. Das Ausgabedataset stellt den Treiber des Zeitplans dar (stündlich, täglich). Daher müssen Sie das Augabedataset auch dann eingeben, wenn die Aktivität keine Ausgabe generiert.

### <a name="prerequisites"></a>Voraussetzungen
1. Erstellen Sie ein allgemeines Speicherkonto anhand der Anweisungen in der exemplarischen Vorgehensweise [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md).

1. Erstellen Sie einen Spark-Cluster in Azure HDInsight anhand der Anweisungen im Tutorial [Erstellen eines Spark-Clusters in HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Ordnen Sie das in Schritt 1 erstellte Speicherkonto diesem Cluster zu.

1. Laden Sie die Python-Skriptdatei **test.py** herunter, die sich unter [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py) befindet.

1. Laden Sie **test.py** in den Ordner **pyFiles** im Container **adfspark** in Blob Storage hoch. Erstellen Sie den Container und den Ordner, wenn sie nicht vorhanden sind.

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory
Führen Sie die folgenden Schritte aus, um eine Data Factory zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Klicken Sie auf **Neu** > **Daten + Analysen** > **Data Factory**.

1. Geben Sie auf dem Blatt **Neue Data Factory** unter **Name** den Namen **SparkDF** ein.

   > [!IMPORTANT]
   > Der Name der Azure Data Factory muss global eindeutig sein. Wenn die Fehlermeldung „Data Factory-Name SparkDF nicht verfügbar“ angezeigt wird, ändern Sie den Namen der Data Factory. Verwenden Sie z.B. „IhrNameSparkDFdate“, und erstellen Sie die Data Factory erneut. Weitere Informationen zu Benennungsregeln finden Sie unter [Data Factory: Benennungsregeln](data-factory-naming-rules.md).

1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, in dem die Data Factory erstellt werden soll.

1. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine Azure-Ressourcengruppe.

1. Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**.

1. Klicken Sie auf **Erstellen**.

   > [!IMPORTANT]
   > Zum Erstellen von Data Factory-Instanzen müssen Sie Mitglied der Rolle [Data Factory-Mitwirkender](../../role-based-access-control/built-in-roles.md#data-factory-contributor) auf Abonnement- bzw. Ressourcengruppenebene sein.

1. Im Dashboard des Azure-Portals sehen Sie, dass die Data Factory erstellt wird.

1. Nachdem die Data Factory erstellt wurde, wird die Seite **Data Factory** mit dem Inhalt der Data Factory angezeigt. Wenn die Seite **Data Factory** nicht angezeigt wird, klicken Sie auf dem Dashboard auf die Kachel für Ihre Data Factory.

    ![Blatt "Data Factory"](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
In diesem Schritt erstellen Sie zwei verknüpfte Dienste. Ein Dienst verknüpft Ihren Spark-Cluster mit der Data Factory, und der andere Dienst verknüpft Ihren Speicher mit der Data Factory.

#### <a name="create-a-storage-linked-service"></a>Erstellen eines verknüpften Speicherdiensts
In diesem Schritt verknüpfen Sie Ihr Speicherkonto mit Ihrer Data Factory. Ein Dataset, das Sie in einem späteren Schritt in dieser exemplarischen Vorgehensweise erstellen, verweist auf diesen verknüpften Dienst. Der verknüpfte HDInsight-Dienst, den Sie im nächsten Schritt definieren, verweist ebenfalls auf diesen verknüpften Dienst.

1. Wählen Sie auf dem Blatt **Data Factory** die Option **Verfassen und bereitstellen** aus. Der Data Factory-Editor wird eingeblendet.

1. Klicken Sie auf **Neuer Datenspeicher** und dann auf **Azure Storage**.

   ![Neuer Datenspeicher](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Das JSON-Skript, mit dem Sie einen mit Storage verknüpften Dienst erstellen, wird im Editor angezeigt.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Ersetzen Sie **Kontoname** und **Kontoschlüssel** durch den Namen und den Zugriffsschlüssel Ihres Speicherkontos. Weitere Informationen zum Abrufen der Speicherzugriffsschlüssel finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md).

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen. Nachdem der verknüpfte Dienst erfolgreich bereitgestellt wurde, wird das Fenster „Draft-1“ geschlossen. **AzureStorageLinkedService** wird in der Strukturansicht links angezeigt.

#### <a name="create-an-hdinsight-linked-service"></a>Erstellen eines verknüpften HDInsight-Diensts
In diesem Schritt erstellen Sie einen verknüpften HDInsight-Dienst, um Ihren HDInsight Spark-Cluster mit der Data Factory zu verknüpfen. Der HDInsight-Cluster wird verwendet, um das Spark-Programm auszuführen, das in der Spark-Aktivität der Pipeline in diesem Beispiel angegeben ist.

1. Wählen Sie im Data Factory-Editor **Weitere** > **Neu berechnen** > **HDInsight-Cluster** aus.

    ![Erstellen eines verknüpften HDInsight-Diensts](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in das Fenster „Draft-1“ ein. Gehen Sie im JSON-Editor folgendermaßen vor:

    1. Geben Sie den URI für den HDInsight Spark-Cluster an. Beispiel: `https://<sparkclustername>.azurehdinsight.net/`.

    1. Geben Sie den Namen des Benutzers an, der auf den Spark-Cluster zugreifen darf.

    1. Geben Sie das Kennwort für den Benutzer an.

    1. Geben Sie den mit Storage verknüpften Dienst an, der dem HDInsight Spark-Cluster zugeordnet ist. In diesem Beispiel ist dies „AzureStorageLinkedService“.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Die Spark-Aktivität unterstützt keine HDInsight Spark-Cluster, die Azure Data Lake Store als primären Speicher verwenden.
    > - Die Spark-Aktivität unterstützt nur vorhandene (Ihre eigenen) HDInsight Spark-Cluster. Bedarfsgesteuerte verknüpfte HDInsight-Dienste werden nicht unterstützt.

    Weitere Informationen zum verknüpften HDInsight-Dienst finden Sie unter [Verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

### <a name="create-the-output-dataset"></a>Erstellen des Ausgabedatasets
Das Ausgabedataset stellt den Treiber des Zeitplans dar (stündlich, täglich). Daher müssen Sie das Augabedataset für die Spark-Aktivität in der Pipeline auch dann angeben, wenn die Aktivität keine Ausgabe generiert. Das Angeben eines Eingabedatasets für die Aktivität ist optional.

1. Wählen Sie im Data Factory-Editor **Weitere** > **Neues Dataset** > **Azure Blob Storage** aus.

1. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in das Fenster „Draft-1“ ein. Der JSON-Codeausschnitt definiert ein Dataset mit dem Namen **OutputDataset**. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer **adfspark** und im Ordner **pyFiles/output** gespeichert werden. Wie bereits erwähnt, ist dieses Dataset ein Dummydataset. Das Spark-Programm in diesem Beispiel generiert keine Ausgabe. Der Abschnitt **availability** gibt an, dass das Ausgabedataset täglich erzeugt wird.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Wählen Sie auf der Befehlsleiste **Bereitstellen** aus, um das Dataset bereitzustellen.


### <a name="create-a-pipeline"></a>Erstellen einer Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer HDInsightSpark-Aktivität. Zurzeit steuert das Ausgabedataset den Zeitplan, sodass Sie auch dann ein Ausgabedataset erstellen müssen, wenn die Aktivität keine Ausgabe generiert. Wenn die Aktivität keine Eingabe akzeptiert, können Sie das Erstellen des Eingabedatasets überspringen. Aus diesem Grund wird in diesem Beispiel kein Eingabedataset angegeben.

1. Klicken Sie im Data Factory-Editor auf **Weitere** > **Neue Pipeline**.

1. Ersetzen Sie das Skript im Fenster „Entwurf-1“ durch das folgende Skript:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```

    Beachten Sie folgende Punkte:

    1. Die **type**-Eigenschaft ist auf **HDInsightSpark** festgelegt.

    1. Die Eigenschaft **rootPath** ist auf **adfspark\\pyFiles** festgelegt, wobei „adfspark“ den Blobcontainer und „pyFiles“ einen Dateiordner in diesem Container darstellt. In diesem Beispiel ist Blob Storage der dem Spark-Cluster zugeordnete Speicher. Sie können die Datei in ein anderes Speicherkonto hochladen. Erstellen Sie in diesem Fall einen mit Storage verknüpften Dienst, um dieses Speicherkonto mit der Data Factory zu verknüpfen. Geben Sie dann den Namen des verknüpften Diensts als Wert für die Eigenschaft **sparkJobLinkedService** an. Weitere Informationen zu dieser und anderen von der Spark-Aktivität unterstützten Eigenschaften finden Sie unter [Eigenschaften von Spark-Aktivitäten](#spark-activity-properties).

    1. Die Eigenschaft **entryFilePath** ist auf **test.py** festgelegt. Dies ist die Python-Datei.

    1. Die **getDebugInfo**-Eigenschaft ist auf **Always** festgelegt, das heißt, Protokolldateien werden in jedem Fall (Erfolg oder Fehler) erstellt.

       > [!IMPORTANT]
       > Es wird empfohlen, diese Eigenschaft in einer Produktionsumgebung nicht auf `Always` festzulegen, es sei denn, dies dient der Problembehandlung.

    1. Der Abschnitt **outputs** weist ein Ausgabedataset auf. Sie müssen in jedem Fall ein Ausgabedataset angeben, selbst wenn das Spark-Programm keine Ausgabe generiert. Das Ausgabedataset bestimmt den Zeitplan für die Pipeline (stündlich, täglich).

    Weitere Informationen zu den von der Spark-Aktivität unterstützten Eigenschaften finden Sie unter [Eigenschaften von Spark-Aktivitäten](#spark-activity-properties).

1. Wählen Sie in der Befehlsleiste **Bereitstellen** aus, um die Pipeline bereitzustellen.

### <a name="monitor-a-pipeline"></a>Überwachen einer Pipeline
1. Wählen Sie auf dem Blatt **Data Factory** die Option **Überwachung und Verwaltung** aus, um die Überwachungsanwendung auf einer anderen Registerkarte zu starten.

    ![Kachel „Überwachung und Verwaltung“](media/data-factory-spark/monitor-and-manage-tile.png)

1. Ändern Sie den Filter **Startzeit** oben in **01.02.2017**, und wählen Sie dann **Übernehmen** aus.

1. Es wird nur ein Aktivitätsfenster angezeigt, da nur ein Tag zwischen der Startzeit (01.02.2017) und der Endzeit (02.02.2017) der Pipeline liegt. Vergewissern Sie sich, dass der Datenslice den Zustand **Bereit** aufweist.

    ![Überwachen der Pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. Wählen Sie in der Liste **Aktivitätsfenster** eine Aktivitätsausführung aus, um die Details dazu anzuzeigen. Wenn ein Fehler aufgetreten ist, sehen Sie die Details dazu im rechten Bereich.

### <a name="verify-the-results"></a>Überprüfen der Ergebnisse

1. Starten Sie Jupyter Notebook für Ihren HDInsight Spark-Cluster, indem Sie zu `https://CLUSTERNAME.azurehdinsight.net/jupyter` wechseln. Sie können auch ein Clusterdashboard für Ihren HDInsight Spark-Cluster und dann Jupyter Notebook starten.

1. Wählen Sie auf **Neu** > **PySpark** aus, um ein neues Notebook zu starten.

    ![Neues Jupyter Notebook](media/data-factory-spark/jupyter-new-book.png)

1. Führen Sie den folgenden Befehl aus, indem Sie den Text kopieren und am Ende der zweiten Anweisung einfügen und UMSCHALT+EINGABE drücken:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Überprüfen Sie, ob die Daten aus der hvac-Tabelle angezeigt werden.

    ![Jupyter-Abfrageergebnisse](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Ausführliche Anweisungen finden Sie im Abschnitt [Ausführen einer Spark SQL-Abfrage](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Problembehandlung
Da Sie „getDebugInfo“ auf **Always** festgelegt haben, finden Sie einen Unterordner „log“ im Ordner „pyFiles im Blobcontainer. Die Protokolldatei im Ordner „log“ enthält weitere Informationen. Diese Protokolldatei ist besonders nützlich, wenn ein Fehler aufgetreten ist. In einer Produktionsumgebung sollten Sie sie auf **Failure** festlegen.

Führen Sie zur zusätzlichen Problembehandlung die folgenden Schritte aus:


1. Gehe zu `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![YARN-UI-Anwendung](media/data-factory-spark/yarnui-application.png)

1. Wählen Sie für einen der Ausführungsversuche **Protokolle** aus.

    ![Anwendungsseite](media/data-factory-spark/yarn-applications.png)

1. Die folgenden zusätzlichen Fehlerinformationen sollten auf der Protokollseite angezeigt werden:

    ![Protokollfehler](media/data-factory-spark/yarnui-application-error.png)

Die folgenden Abschnitte enthalten Informationen zu Data Factory-Entitäten zur Verwendung von Spark-Clustern und Spark-Aktivitäten in Ihrer Data Factory.

## <a name="spark-activity-properties"></a>Eigenschaften von Spark-Aktivitäten
Dies ist eine JSON-Beispieldefinition einer Pipeline mit einer Spark-Aktivität:

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

Die folgende Tabelle beschreibt die JSON-Eigenschaften, die in der JSON-Definition verwendet werden.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| name | Der Name der Aktivität in der Pipeline. | Ja |
| description | Text, der beschreibt, welche Aktion die Aktivität ausführt. | Nein  |
| type | Diese Eigenschaft muss auf „HDInsightSpark“ festgelegt werden. | Ja |
| linkedServiceName | Name des mit HDInsight verknüpften Diensts, in dem das Spark-Programm ausgeführt wird. | Ja |
| rootPath | Der Blobcontainer und -ordner mit der Spark-Datei. Für den Dateinamen wird zwischen Groß- und Kleinschreibung unterschieden. | Ja |
| entryFilePath | Der relative Pfad zum Stammordner des Spark-Codes bzw. -Pakets. | Ja |
| className | Die Java-/Spark-Hauptklasse der Anwendung. | Nein  |
| Argumente | Eine Liste der Befehlszeilenargumente für das Spark-Programm. | Nein |
| proxyUser | Das Benutzerkonto, dessen Identität angenommen werden soll, um das Spark-Programm auszuführen. | Nein  |
| sparkConfig | Geben Sie Werte für die Spark-Konfigurationseigenschaften an, die unter [Spark-Konfiguration: Anwendungseigenschaften](https://spark.apache.org/docs/latest/configuration.html#available-properties) aufgeführt sind. | Nein  |
| getDebugInfo | Gibt an, ob die Spark-Protokolldateien in den Speicher kopiert werden, der vom HDInsight-Cluster verwendet oder von „sparkJobLinkedService“ angegeben wird. Zulässige Werte sind “Keine“, „Immer“ oder „Fehler“. Der Standardwert lautet „Keine“. | Nein  |
| sparkJobLinkedService | Der verknüpfte Speicherdienst, der die Datei sowie die Abhängigkeiten und Protokolle für den Spark-Auftrag enthält. Wenn Sie für diese Eigenschaft keinen Wert angeben, wird der Speicher verwendet, der dem HDInsight-Cluster zugeordnet ist. | Nein  |

## <a name="folder-structure"></a>Ordnerstruktur
Die Spark-Aktivität unterstützt im Gegensatz zu Pig- und Hive-Aktivitäten keine Inlineskripts. Spark-Aufträge lassen sich zudem besser erweitern als Pig- oder Hive-Aufträge. Bei Spark-Aufträgen können Sie mehrere Abhängigkeiten wie z.B. JAR-Pakete (im Java-CLASSPATH platziert), Python-Dateien (im PYTHONPATH platziert) sowie beliebige andere Dateien bereitstellen.

Erstellen Sie folgende Ordnerstruktur in dem Blobspeicher, auf den der verknüpfte HDInsight-Dienst verweist. Laden Sie dann abhängige Dateien in die entsprechenden Unterordner in dem Stammordner hoch, der durch **entryFilePath** dargestellt wird. Python-Dateien werden beispielsweise in den Unterordner „pyFiles“ und JAR-Dateien in den Unterordner „jars“ des Stammordners hochgeladen. Zur Laufzeit erwartet der Data Factory-Dienst die folgende Ordnerstruktur im Blobspeicher:

| `Path` | Beschreibung | Erforderlich | type |
| ---- | ----------- | -------- | ---- |
| . | Der Stammpfad des Spark-Auftrags im verknüpften Speicherdienst. | Ja | Ordner |
| &lt;benutzerdefiniert&gt; | Der Pfad, der auf die Eingabedatei des Spark-Auftrags zeigt. | Ja | Datei |
| ./jars | Alle Dateien in diesem Ordner werden hochgeladen und im Java-CLASSPATH des Clusters platziert. | Nein  | Ordner |
| ./pyFiles | Alle Dateien in diesem Ordner werden hochgeladen und im PYTHONPATH des Clusters platziert. | Nein  | Ordner |
| ./files | Alle Dateien in diesem Ordner werden hochgeladen und im Executor-Arbeitsverzeichnis platziert. | Nein  | Ordner |
| ./archives | Alle Dateien in diesem Ordner sind nicht komprimiert. | Nein  | Ordner |
| ./logs | Der Ordner, in dem Protokolle aus dem Spark-Cluster gespeichert werden.| Nein  | Ordner |

Hier finden Sie ein Beispiel für einen Speicher mit zwei Spark-Auftragsdateien in dem Azure-Blobspeicher, auf den der verknüpfte HDInsight-Dienst verweist:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
