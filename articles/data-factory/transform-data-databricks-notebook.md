---
title: Transformieren von Daten mit einem Databricks-Notebook
description: Erfahren Sie, wie Sie Daten verarbeiten oder transformieren, indem Sie ein Databricks-Notebook ausführen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018544"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformieren von Daten durch Ausführen eines Databricks-Notebooks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die Azure Databricks-Notebook-Aktivität in einer [Data Factory-Pipeline](concepts-pipelines-activities.md) führt ein Databricks-Notebook in Ihrem Azure Databricks-Arbeitsbereich aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.  Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definition der Databricks-Notebook-Aktivität

Dies ist die JSON-Beispieldefinition der Databricks-Notebook-Aktivität:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Eigenschaften der Databricks-Notebook-Aktivität

Die folgende Tabelle beschreibt die JSON-Eigenschaften, die in der JSON-Definition verwendet werden:

|Eigenschaft|BESCHREIBUNG|Erforderlich|
|---|---|---|
|name|Der Name der Aktivität in der Pipeline.|Ja|
|description|Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.|Nein|
|type|Bei Databricks-Notebook-Aktivitäten lautet der Aktivitätstyp DatabricksNotebook.|Ja|
|linkedServiceName|Der Name des verknüpften Databricks-Diensts, in dem das Databricks-Notebook ausgeführt wird. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md).|Ja|
|notebookPath|Der absolute Pfad des Notebooks, das im Databricks-Arbeitsbereich ausgeführt werden soll. Dieser Pfad muss mit einem Schrägstrich beginnen.|Ja|
|baseParameters|Ein Array aus Schlüssel-Wert-Paaren. Für jede Aktivitätsausführung können Basisparameter verwendet werden. Wenn das Notebook einen nicht spezifizierten Parameter akzeptiert, wird der Standardwert des Notebooks verwendet. Erfahren Sie mehr über Parameter in [Databricks-Notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nein|
|libraries|Eine Liste der Bibliotheken, die in dem Cluster installiert werden, der den Auftrag ausführen wird. Es kann ein \<string, object>-Array sein.|Nein|


## <a name="supported-libraries-for-databricks-activities"></a>Unterstützte Bibliotheken für Databricks-Aktivitäten

In der oben genannten Definition der Databricks-Aktivität geben Sie die folgenden Bibliothekstypen an: *JAR*, *EGG*, *WHL*, *Maven*, *PyPI*, *CRAN*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Weitere Informationen zu Bibliothekstypen finden Sie in der [Databricks-Dokumentation](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary).

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Übergeben von Parametern zwischen Notebooks und Data Factory

Mithilfe der Eigenschaft *baseParameters* in der Databricks-Aktivität können Sie Data Factory-Parameter an Notebooks übergeben. 

In bestimmten Fällen müssen Sie möglicherweise bestimmte Werte aus dem Notebook an Data Factory zurückgeben, die für die Ablaufsteuerung (Bedingungsüberprüfungen) in Data Factory verwendet oder von Downstreamaktivitäten (Größenbeschränkung ist 2 MB) genutzt werden können. 

1. Sie können in Ihrem Notebook [dbutils.notebook.exit("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) aufrufen, und der entsprechende Rückgabewert (returnValue) wird an Data Factory zurückgeben.

2. Mit einem Ausdruck wie `'@activity('databricks notebook activity name').output.runOutput'` können Sie die Ausgabe in Data Factory verwenden. 

   > [!IMPORTANT]
   > Wenn Sie ein JSON-Objekt übergeben, können Sie Werte abrufen, indem Sie Eigenschaftsnamen anhängen. Beispiel: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Hochladen einer Bibliothek in Databricks

#### <a name="using-databricks-workspace-ui"></a>[Benutzeroberfläche des Databricks-Arbeitsbereichs](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Sie können den DBFS-Pfad der hinzugefügten Bibliothek über die Benutzeroberfläche mithilfe der [Databricks-Benutzerzeilenschnittstelle (Installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) abrufen. 

JAR-Bibliotheken werden beim Verwenden der Benutzeroberfläche in der Regel unter dbfs:/FileStore/jars gespeichert. Sie können alle über die Benutzerzeilenschnittstelle auflisten: *databricks fs ls dbfs:/FileStore/jars*



#### <a name="copy-library-using-databricks-cli"></a>[Kopieren von Bibliotheken mit der Databricks-Benutzerzeilenschnittstelle](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Beispiel: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*.
