---
title: Erstellen von Datenpipelines für die Vorhersage mithilfe von Azure Data Factory
description: Dieser Artikel beschreibt das Erstellen von Vorhersagepipelines mithilfe von Azure Data Factory und Azure Machine Learning Studio (klassisch).
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: a7042a67081475311341ad6d56e35b2a71b8b12c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379334"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Erstellen einer Vorhersagepipeline mithilfe von Azure Machine Learning Studio (klassisch) und Azure Data Factory

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

## <a name="introduction"></a>Einführung
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Transformieren von Daten mithilfe von maschinellem Lernen in Data Factory](../transform-data-using-machine-learning.md).

### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klassisch)
[Azure Machine Learning Studio (Classic)](https://azure.microsoft.com/documentation/services/machine-learning/) ermöglicht es Ihnen, Predictive Analytics-Lösungen zu erstellen, zu testen und bereitzustellen. Allgemein betrachtet, geschieht dies in drei Schritten:

1. **Erstellen eines Trainingsexperiments**. Dieser Schritt wird mithilfe von Azure Machine Learning Studio (klassisch) ausgeführt. Azure Machine Learning Studio (klassisch) ist eine visuelle Entwicklungsumgebung für die Zusammenarbeit, mit der Sie ein Predictive Analytics-Modell mithilfe von Trainingsdaten trainieren und testen können.
2. **Konvertierten in ein Vorhersageexperiment**. Sobald Ihr Modell mit vorhandenen Daten trainiert wurde, können Sie es verwenden, um neue Daten zu bewerten. Sie bereiten das Experiment auf die Bewertung vor und optimieren es.
3. **Bereitstellen des Experiments als Webdienst**. Sie können das Bewertungsexperiment als Azure-Webdienst veröffentlichen. Sie können Daten über diesen Webdienstendpunkt an Ihr Modell senden und Ergebnisvorhersagen vom Modell empfangen.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory ist ein cloudbasierter Daten-Integrationsdienst, der das **Verschieben** und **Transformieren** von Daten organisiert und automatisiert. Mit Azure Data Factory können Sie Datenintegrationslösungen erstellen, die Daten aus verschiedenen Datenspeichern erfassen, die Daten transformieren/verarbeiten und anschließend die Ergebnisdaten in den Datenspeichern veröffentlichen.

Der Data Factory-Dienst ermöglicht die Erstellung von Datenpipelines zum Verschieben und Transformieren von Daten und die Ausführung dieser Pipelines nach einem festgelegten Zeitplan (stündlich, täglich, wöchentlich usw.). Darüber hinaus stehen umfangreiche Visualisierungen zur Verfügung, um Herkunft und Abhängigkeiten zwischen Ihren Datenpipelines anzuzeigen und alle Ihre Datenpipelines über eine zentrale, einheitliche Ansicht zu überwachen. Dadurch können Sie nicht nur mühelos Probleme lokalisieren, sondern auch Überwachungswarnungen einrichten.

In den Artikeln [Einführung in Azure Data Factory](data-factory-introduction.md) und [Erstellen der ersten Pipeline](data-factory-build-your-first-pipeline.md) finden Sie Informationen für den schnellen Einstieg in den Azure Data Factory-Dienst.

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory und Machine Learning Studio (klassisch)
Azure Data Factory ermöglicht die einfache Erstellung von Pipelines, die einen veröffentlichten [Azure Machine Learning Studio (Classic)][azure-machine-learning]-Webdienst für Predictive Analytics nutzen. Mithilfe der **Batchausführungsaktivität** in einer Azure Data Factory-Pipeline können Sie einen Webdienst von Azure Machine Learning Studio (klassisch) aufrufen, um Vorhersagen zu den Daten in Batches zu treffen. Ausführliche Informationen finden Sie im Abschnitt „Aufrufen eines Webdiensts von Azure Machine Learning Studio (klassisch) mit der Batchausführungsaktivität“.

Im Laufe der Zeit müssen die Vorhersagemodelle in den Bewertungsexperimenten von Azure Machine Learning Studio (klassisch) mit neuen Eingabedatasets neu trainiert werden. Sie können ein Azure Machine Learning Studio-Modell (klassisch) über eine Data Factory-Pipeline neu trainieren, indem Sie die folgenden Schritte ausführen:

1. Veröffentlichen Sie das Trainingsexperiment (nicht das Vorhersageexperiment) als Webdienst. Für diesen Schritt können Sie Azure Machine Learning Studio (klassisch) verwenden, wie Sie es beim Veröffentlichen des Vorhersageexperiments als Webdienst im vorherigen Szenario getan haben.
2. Verwenden Sie die Batchausführungsaktivität von Azure Machine Learning Studio (klassisch), um den Webdienst für das Trainingsexperiment aufzurufen. Grundsätzlich können Sie die Batchausführungsaktivität von Azure Machine Learning Studio (klassisch) verwenden, um den Trainingswebdienst und den Bewertungswebdienst aufzurufen.

Nachdem Sie das erneute Training abgeschlossen haben, aktualisieren Sie den Bewertungswebdienst (das Vorhersageexperiment, das als Webdienst verfügbar gemacht wurde) mithilfe der **Aktivität zur Ressourcenaktualisierung von Azure Machine Learning Studio (klassisch)**  mit dem neu trainierten Modell. Einzelheiten finden Sie im Artikel [Aktualisieren von Modellen mithilfe der Ressourcenaktualisierungsaktivität](data-factory-azure-ml-update-resource-activity.md).

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Aufrufen eines Webdiensts mit der Batchausführungsaktivität
Mit Azure Data Factory können Sie die Verschiebung und Verarbeitung von Daten orchestrieren und anschließend eine Batchausführung mithilfe von Azure Machine Learning Studio (klassisch) vornehmen. Dies sind die Schritte der obersten Ebene:

1. Erstellen Sie einen verknüpften Dienst von Azure Machine Learning Studio (klassisch). Sie benötigen folgende Werte:

   1. **Anforderungs-URI** für die Batchausführungs-API ab. Sie erhalten den Anforderungs-URI, indem Sie auf der Webdiensteseite auf den Link **BATCHAUSFÜHRUNG** klicken.
   2. Den **API-Schlüssel** für den veröffentlichten Webdienst von Azure Machine Learning Studio (klassisch). Den API-Schlüssel erhalten Sie durch Klicken auf den Webdienst, den Sie veröffentlicht haben.
   3. Verwenden Sie die Aktivität **AzureMLBatchExecution** .

      ![Dashboard von Machine Learning Studio (klassisch)](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch-URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Szenario: Experimente mit Eingaben/Ausgaben für den Webdienst, die auf Daten in Azure Blob Storage verweisen
In diesem Szenario werden Vorhersagen mit dem Azure Machine Learning Studio-Webdienst (klassisch) anhand der Daten aus einer Datei in Azure Blob Storage erstellt und die Vorhersageergebnisse in Blob Storage gespeichert. Das folgende JSON-Skript definiert eine Data Factory-Pipeline mit einer AzureMLBatchExecution-Aktivität. Die Aktivität enthält das Dataset **DecisionTreeInputBlob** als Eingabe und **DecisionTreeResultBlob** als Ausgabe. **DecisionTreeInputBlob** wird als Eingabe an den Webdienst übergeben, indem die **webServiceInput**-JSON-Eigenschaft verwendet wird. **DecisionTreeResultBlob** wird als Ausgabe an den Webdienst übergeben, indem die **webServiceOutputs**-JSON-Eigenschaft verwendet wird.

> [!IMPORTANT]
> Wenn der Webdienst mehrere Eingaben akzeptiert, verwenden Sie die Eigenschaft **webServiceInputs** anstatt **webServiceInput**. Im Abschnitt [Webdienst erfordert mehrere Eingaben](#web-service-requires-multiple-inputs) finden Sie ein Beispiel für die Verwendung der webServiceInputs-Eigenschaft.
>
> Datasets, auf die die Eigenschaften **webServiceInput**/**webServiceInputs** und **webServiceOutputs** (in **typeProperties**) verweisen, müssen auch in den Aktivitäten **inputs** und **outputs** enthalten sein.
>
> In Ihrem Azure Machine Learning Studio-Experiment (klassisch) haben Eingabe- und Ausgabeports von Webdiensten und globale Parameter Standardnamen („input1“, „input2“), die Sie anpassen können. Die Namen, die Sie für die Einstellungen webServiceInputs, webServiceOutputs und globalParameters verwenden, müssen den Namen in den Experimenten genau entsprechen. Sie können die Beispiel-Anforderungsnutzlast auf der Hilfeseite für die Batchausführung für Ihren Azure Machine Learning Studio-Endpunkt (klassisch) anzeigen, um die erwartete Zuordnung zu überprüfen.
>
>

```json
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

> [!NOTE]
> Nur Eingaben und Ausgaben der AzureMLBatchExecution-Aktivität können als Parameter an den Webdienst übergeben werden. Im JSON-Codeausschnitt oben ist beispielsweise "DecisionTreeInputBlob" eine Eingabe für die AzureMLBatchExecution-Aktivität, die über den webServiceInput-Parameter als Eingabe an den Webdienst übergeben wird.
>
>

### <a name="example"></a>Beispiel
Dieses Beispiel verwendet Azure Storage zum Speichern der Ein- und Ausgabedaten.

Wir empfehlen Ihnen, das Tutorial zum [Erstellen der ersten Pipeline mit Data Factory][adf-build-1st-pipeline] durchzuarbeiten, bevor Sie sich dieses Beispiel ansehen. Verwenden Sie den Data Factory-Editor zum Erstellen von Data Factory-Artefakten (verknüpfte Dienste, Datasets, Pipeline) in diesem Beispiel.

1. Erstellen Sie einen **verknüpften Dienst** für **Azure Storage**. Wenn sich die Ein- und Ausgabedateien in verschiedenen Speicherkonten befinden, benötigen Sie zwei verknüpfte Dienste. Hier folgt ein JSON-Beispiel:

   ```json
   {
     "name": "StorageLinkedService",
     "properties": {
       "type": "AzureStorage",
       "typeProperties": {
         "connectionString": "DefaultEndpointsProtocol=https;AccountName= [acctName];AccountKey=[acctKey]"
       }
     }
   }
   ```

2. Erstellen Sie das **Eingabe** **dataset** für Azure Data Factory. Im Gegensatz zu einigen anderen Data Factory-Datasets müssen diese Datasets die beiden Werte **folderPath** und **fileName** enthalten. Sie können die Partitionierung verwenden, damit jede Batchausführung (jeder Datenslice) eindeutige Ein- und Ausgabedateien verarbeitet oder erzeugt. Sie müssen unter Umständen einige vorgeschaltete Aktivitäten einbeziehen, um die Eingabe in das CSV-Dateiformat umzuwandeln und sie im Speicherkonto für die einzelnen Datenslices abzulegen. In diesem Fall würden Sie die im folgenden Beispiel gezeigten Einstellungen **external** und **externalData** nicht einbeziehen, und Ihr „DecisionTreeInputBlob“ würde dem Ausgabedataset einer anderen Aktivität entsprechen.

   ```json
   {
     "name": "DecisionTreeInputBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/input",
         "fileName": "in.csv",
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "external": true,
       "availability": {
         "frequency": "Day",
         "interval": 1
       },
       "policy": {
         "externalData": {
           "retryInterval": "00:01:00",
           "retryTimeout": "00:10:00",
           "maximumRetry": 3
         }
       }
     }
   }
   ```

   Die CSV-Eingabedatei muss die Spaltenkopfzeile enthalten. Bei Verwendung der **Kopieraktivität** zum Erstellen/Verschieben der CSV-Datei in den Blobspeicher müssen Sie die Senkeneigenschaft **blobWriterAddHeader** auf **true** festlegen. Beispiel:

   ```json
   sink:
   {
     "type": "BlobSink",
     "blobWriterAddHeader": true
     }
   ```

   Wenn die CSV-Datei nicht die Kopfzeile enthält, wird möglicherweise folgender Fehler angezeigt: **Fehler in Aktivität: Fehler beim Lesen der Zeichenfolge. Unerwartetes Token: StartObject. Pfad '', Zeile 1, Position 1**.

3. Erstellen Sie das **Ausgabe** **dataset** für Azure Data Factory. In diesem Beispiel wird die Partitionierung verwendet, um für die Ausführung der einzelnen Datenslices einen eindeutigen Ausgabepfad zu erstellen. Ohne die Partitionierung würde die Aktivität die Datei überschreiben.

   ```json
   {
     "name": "DecisionTreeResultBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/scored/{folderpart}/",
         "fileName": "{filepart}result.csv",
         "partitionedBy": [
           {
             "name": "folderpart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "yyyyMMdd"
             }
           },
           {
             "name": "filepart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "HHmmss"
             }
           }
         ],
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "availability": {
         "frequency": "Day",
         "interval": 15
       }
     }
   }
   ```

4. Erstellen Sie einen **verknüpften Dienst** vom Typ **AzureMLLinkedService**, der den API-Schlüssel und die URL für die Modellbatchausführung bereitstellt.

   ```json
   {
     "name": "MyAzureMLLinkedService",
     "properties": {
       "type": "AzureML",
       "typeProperties": {
         "mlEndpoint": "https://[batch execution endpoint]/jobs",
         "apiKey": "[apikey]"
       }
     }
   }
   ```

5. Erstellen Sie abschließend eine Pipeline, die eine **AzureMLBatchExecution** -Aktivität enthält. Zur Laufzeit führt die Pipeline die folgenden Schritte aus:

   1. Abrufen des Speicherorts der Eingabedatei aus Ihren Eingabedatasets
   2. Aufrufen der Batchausführungs-API von Azure Machine Learning Studio (klassisch)
   3. Kopieren der Ausgabe der Batchausführung in das Blob, das in Ihrem Ausgabedataset angegeben ist

      > [!NOTE]
      > Die AzureMLBatchExecution-Aktivität kann über keine oder mehrere Eingaben sowie über eine oder mehrere Ausgaben verfügen.
      >
      >

      ```json
      {
        "name": "PredictivePipeline",
        "properties": {
          "description": "use AzureML model",
          "activities": [
            {
              "name": "MLActivity",
              "type": "AzureMLBatchExecution",
              "description": "prediction analysis on batch input",
              "inputs": [
                {
                  "name": "DecisionTreeInputBlob"
                }
                ],
              "outputs": [
                {
                  "name": "DecisionTreeResultBlob"
                }
                ],
              "linkedServiceName": "MyAzureMLLinkedService",
              "typeProperties":
                {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                  "output1": "DecisionTreeResultBlob"
                }
                },
              "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
              }
            }
          ],
          "start": "2016-02-13T00:00:00Z",
          "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      Die **Start**- und **Endzeit** von Datums-/Uhrzeitangaben müssen im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeit für **end** ist optional. Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an. Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](/previous-versions/azure/dn835050(v=azure.100)) .

      > [!NOTE]
      > Das Festlegen der Eingaben für die AzureMLBatchExecution-Aktivität erfolgt optional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Szenario: Experimente mit Reader- und Writer-Modulen zum Verweisen auf Daten in verschiedenen Speichern
Ein weiteres gängiges Szenario beim Erstellen von Azure Machine Learning Studio-Experimenten (klassisch) ist die Verwendung von Reader- und Writer-Modulen. Das Reader-Modul wird verwendet, um Daten in einem Experiment zu laden, während mit dem Writer-Modul Daten aus den Experimenten gespeichert werden. Ausführliche Informationen zu Reader- und Writer-Modulen finden Sie in den Themen [Reader](/azure/machine-learning/studio-module-reference/import-data) und [Writer](/azure/machine-learning/studio-module-reference/export-data) in der MSDN Library.

Bei Verwendung der Reader- und Writer-Module empfiehlt es sich, einen Webdienstparameter für jede Eigenschaft dieser Module zu verwenden. Durch diese Webparameter können Sie die Werte zur Laufzeit konfigurieren. Sie können z.B. ein Experiment mit einem Reader-Modul erstellen, das eine Azure SQL-Datenbank mit dem Namen „XXX.database.windows.net“ verwendet. Nach der Bereitstellung des Webdiensts sollen die Nutzer des Webdiensts einen weiteren logischen SQL Server mit dem Namen „YYY.database.windows.net“ angeben können. Durch Verwendung eines Webdienstparameters wird ermöglicht, dass dieser Wert konfiguriert werden kann.

> [!NOTE]
> Eingaben und Ausgaben für den Webdienst unterscheiden sich von Webdienstparametern. Im ersten Szenario haben Sie gesehen, wie Eingaben und Ausgaben für einen Azure Machine Learning Studio-Webdienst (klassisch) angegeben werden können. In diesem Szenario übergeben Sie Parameter für einen Webdienst, die Eigenschaften von Reader- und Writer-Modulen entsprechen.
>
>

Betrachten wir nun ein Szenario für die Verwendung von Webdienstparametern. Sie haben einen Azure Machine Learning Studio-Webdienst (klassisch) bereitgestellt, bei dem Daten mithilfe eines Reader-Moduls aus einer von Azure Machine Learning Studio (klassisch) unterstützten Datenquelle (z. B. Azure SQL-Datenbank) gelesen werden. Nach der Batchausführung werden die Ergebnisse mit einem Writer-Modul (Azure SQL-Datenbank) geschrieben.  In den Experimenten sind keine Eingaben und Ausgaben für den Webdienst definiert. In diesem Fall wird empfohlen, die relevanten Webdienstparameter für das Reader- und das Writer-Modul zu konfigurieren. Bei dieser Konfiguration können das Reader- und Writer-Modul bei Verwendung der AzureMLBatchExecution-Aktivität konfiguriert werden. Sie geben die Webdienstparameter wie folgt im Abschnitt **globalParameters** im JSON-Code der Aktivität an.

```json
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Sie können auch [Data Factory-Funktionen](data-factory-functions-variables.md) zum Übergeben von Werten für die Webdienstparameter verwenden, wie im folgenden Beispiel gezeigt:

```json
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Bei Webdienstparametern wird Groß-/Kleinschreibung unterschieden, weshalb Sie sicherstellen müssen, dass die Namen, die Sie im JSON-Code der Aktivität angeben, denjenigen entsprechen, die vom Webdienst verfügbar gemacht werden.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Lesen von Daten aus mehreren Dateien im Azure-Blob mithilfe eines Reader-Moduls
Große Datenpipelines mit Aktivitäten wie Pig und Hive können eine oder mehrere Ausgabedateien ohne Erweiterungen produzieren. Wenn Sie beispielsweise eine externe Hive-Tabelle angeben, können die Daten für die externe Hive-Tabelle in Azure Blob Storage unter dem Namen „000000_0“ gespeichert werden. Mithilfe des Reader-Moduls in einem Experiment können Sie mehrere Dateien lesen und für Vorhersagen verwenden.

Wenn Sie das Reader-Modul in einem Azure Machine Learning Studio-Experiment (klassisch) verwenden, können Sie das Azure-Blob als Eingabe angeben. Bei den Dateien in Azure Blob Storage kann es sich um die Ausgabedateien (Beispiel: 000000_0) handeln, die von einem Pig- und Hive-Skript unter HDInsight erstellt werden. Mit dem Reader-Modul können Sie Dateien lesen (ohne Erweiterungen), indem Sie **Path to container, directory/blob**(Pfad zum Container, Verzeichnis/Blob) konfigurieren. **Path to container** verweist auf den Container, und **directory/blob** verweist auf den Ordner, der die Dateien enthält, wie in der folgenden Abbildung dargestellt. Das Sternchen (\*) **gibt an, dass alle Dateien im Container/Ordner (d.h. „data/aggregateddata/year=2014/month=6/\*“)** als Teil des Experiments gelesen werden.

![Azure-Blobeigenschaften](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Beispiel
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline mit "AzureMLBatchExecution"-Aktivität mit Webdienstparametern

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

Im obigen JSON-Beispiel:

* Der bereitgestellte Azure Machine Learning Studio-Webdienst (klassisch) verwendet ein Reader- und ein Writer-Modul zum Lesen/Schreiben von Daten aus/an eine Azure SQL-Datenbank-Instanz. Dieser Webdienst stellt die vier folgenden Parameter bereit:  „Database server name“, „Database name“, „Server user account name“ und „Server user account password“.
* Die **Start**- und **Endzeit** von Datums-/Uhrzeitangaben müssen im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeit für **end** ist optional. Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an. Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](/previous-versions/azure/dn835050(v=azure.100)) .

### <a name="other-scenarios"></a>Andere Szenarien
#### <a name="web-service-requires-multiple-inputs"></a>Webdienst erfordert mehrere Eingaben
Wenn der Webdienst mehrere Eingaben akzeptiert, verwenden Sie die Eigenschaft **webServiceInputs** anstatt **webServiceInput**. Datasets, auf die **webServiceInputs** verweist, müssen auch in der Aktivität **inputs** enthalten sein.

In Ihrem Experiment für Azure Machine Learning Studio (klassisch) haben Eingabe- und Ausgabeports von Webdiensten und globale Parameter Standardnamen („input1“, „input2“), die Sie anpassen können. Die Namen, die Sie für die Einstellungen webServiceInputs, webServiceOutputs und globalParameters verwenden, müssen den Namen in den Experimenten genau entsprechen. Sie können die Beispiel-Anforderungsnutzlast auf der Hilfeseite für die Batchausführung für Ihren Azure Machine Learning Studio-Endpunkt (klassisch) anzeigen, um die erwartete Zuordnung zu überprüfen.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Webdienst erfordert keine Eingabe
Mit Webdiensten für die Batchausführung von Azure Machine Learning Studio (klassisch) können beliebige Workflows (beispielsweise R- oder Python-Skripts) ausgeführt werden, die ggf. keine Eingaben erfordern. Das Experiment könnte auch mit einem Reader-Modul konfiguriert werden, das keine GlobalParameters verfügbar macht. In diesem Fall würde die AzureMLBatchExecution-Aktivität wie folgt konfiguriert werden:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webdienst erfordert keine Ein-/Ausgabe
Für den Webdienst für die Batchausführung von Azure Machine Learning Studio (klassisch) ist möglicherweise keine Webdienstausgabe konfiguriert. In diesem Beispiel ist weder eine Webdienst-Ein-/Ausgabe, noch sind GlobalParameters konfiguriert. In der Aktivität selbst ist immer noch eine Ausgabe konfiguriert, aber nicht als „webServiceOutput“.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Der Webdienst verwendet Reader und Writer, und die Aktivität wird nur dann ausgeführt, wenn andere Aktivitäten erfolgreich ausgeführt wurden.
Die Reader- und Writer-Module des Webdiensts von Azure Machine Learning Studio (klassisch) könnten für die Ausführung mit oder ohne globale Parameter konfiguriert werden. Es kann aber ratsam sein, Dienstaufrufe in eine Pipeline einzubetten, bei der Datasetabhängigkeiten erst dann zum Aufrufen des Diensts verwendet werden, wenn einige vorgelagerte Verarbeitungsschritte durchgeführt wurden. Mit diesem Ansatz können Sie auch eine andere Aktion auslösen, nachdem die Batchausführung abgeschlossen wurde. In diesem Fall können Sie die Abhängigkeiten mit Eingaben und Ausgaben der Aktivität ausdrücken, ohne diese als Webdienstein- oder -ausgaben zu benennen.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Es ergeben sich folgende **Erkenntnisse** :

* Wenn Ihr Experimentendpunkt einen „webServiceInput“ verwendet, wird er durch ein Blobdataset dargestellt und ist in den Aktivitätseingaben und in der webServiceInput-Eigenschaft enthalten. Andernfalls wird die webServiceInput-Eigenschaft weggelassen.
* Wenn Ihr Experimentendpunkt „webServiceOutput(s)“ verwendet, werden diese durch Blobdatasets dargestellt und in die Aktivitätsausgaben und in die webServiceOutputs-Eigenschaft eingebunden. Die Aktivitätsausgaben und webServiceOutputs werden nach dem Namen der einzelnen Ausgaben im Experiment zugeordnet. Andernfalls wird die webServiceOutputs-Eigenschaft weggelassen.
* Wenn Ihr Experimentendpunkt „globalParameter(s)“ verfügbar macht, werden sie in der globalParameters-Eigenschaft der Aktivität als Schlüsselwertpaare angegeben. Andernfalls wird die globalParameters-Eigenschaft weggelassen. Bei Schlüsseln wird Groß-/Kleinschreibung unterschieden. [Azure Data Factory-Funktionen](data-factory-functions-variables.md) können in den Werten verwendet werden.
* Weitere Datasets können in den Eigenschaften „inputs“ und „outputs“ der Aktivität enthalten sein, ohne das in „typeProperties“ der Aktivität darauf verwiesen wird. Diese Datasets bestimmen die Ausführung mit Slice-Abhängigkeiten, werden aber von der Aktivität „AzureMLBatchExecution“ ignoriert.


## <a name="updating-models-using-update-resource-activity"></a>Aktualisieren von Modellen mithilfe der Ressourcenaktualisierungsaktivität
Nachdem Sie das erneute Training abgeschlossen haben, aktualisieren Sie den Bewertungswebdienst (das Vorhersageexperiment, das als Webdienst verfügbar gemacht wurde) mithilfe der **Aktivität zur Ressourcenaktualisierung von Azure Machine Learning Studio (klassisch)**  mit dem neu trainierten Modell. Einzelheiten finden Sie im Artikel [Aktualisieren von Modellen mithilfe der Ressourcenaktualisierungsaktivität](data-factory-azure-ml-update-resource-activity.md).

### <a name="reader-and-writer-modules"></a>Die Module "Reader" und "Writer"
Ein häufiges Szenario für Webdienstparameter ist die Verwendung der Azure SQL-Module "Reader" und "Writer". Das Reader-Modul wird zum Laden von Daten in ein Experiment aus Datenverwaltungsdiensten außerhalb von Azure Machine Learning Studio (klassisch) verwendet. Das Writer-Modul dient zum Speichern von Daten aus Ihren Experimenten in Datenverwaltungsdiensten außerhalb von Azure Machine Learning Studio (klassisch).

Ausführliche Informationen zu Azure-Blob/Azure SQL Reader/Writer finden Sie in den Themen [Reader](/azure/machine-learning/studio-module-reference/import-data) und [Writer](/azure/machine-learning/studio-module-reference/export-data) in der MSDN Library. Im Beispiel im vorherigen Abschnitt wurden die Azure-Blobmodule "Reader" und "Writer" verwendet. In diesem Abschnitt werden die Azure SQL-Module "Reader" und "Writer" erläutert.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
**F:** Ich verfüge über mehrere Dateien, die von meinen Big Data-Pipelines erstellt werden. Kann ich die AzureMLBatchExecution-Aktivität zum Bearbeiten aller Dateien verwenden?

**A:** Ja. Ausführliche Informationen dazu finden Sie im Abschnitt **Lesen von Daten aus mehreren Dateien im Azure-Blob mithilfe eines Reader-Moduls** .

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Batchbewertungsaktivität von Azure Machine Learning Studio (klassisch)
Wenn Sie derzeit die Aktivität **AzureMLBatchScoring** für die Integration in Azure Machine Learning Studio (klassisch) verwenden, empfiehlt es sich, die neueste **AzureMLBatchExecution**-Aktivität zu verwenden.

Die AzureMLBatchExecution-Aktivität wird in den Azure SDK- und Azure PowerShell-Versionen von August 2015 eingeführt.

Wenn Sie weiterhin die AzureMLBatchScoring-Aktivität verwenden möchten, lesen Sie weiterhin diesen Abschnitt.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Batchbewertungsaktivität von Azure Machine Learning Studio (klassisch) mit Azure Storage für Ein-/Ausgabe

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Webdienstparameter
Fügen Sie den Abschnitt **typeProperties** dem Abschnitt **AzureMLBatchScoringActivity** im JSON-Code der Pipeline hinzu, um Werte für Webdienstparameter anzugeben. Dies wird im folgenden Beispiel veranschaulicht:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Sie können auch [Data Factory-Funktionen](data-factory-functions-variables.md) zum Übergeben von Werten für die Webdienstparameter verwenden, wie im folgenden Beispiel gezeigt:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Bei Webdienstparametern wird Groß-/Kleinschreibung unterschieden, weshalb Sie sicherstellen müssen, dass die Namen, die Sie im JSON-Code der Aktivität angeben, denjenigen entsprechen, die vom Webdienst verfügbar gemacht werden.
>
>

## <a name="see-also"></a>Weitere Informationen
* [Azure-Blogbeitrag: Erste Schritte mit Azure Data Factory und Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/