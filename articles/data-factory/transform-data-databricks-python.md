---
title: Transformieren von Daten mit Python in Databricks
description: Erfahren Sie, wie Sie mit Python Daten in Databricks verarbeiten oder transformieren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
manager: anandsub
ms.custom: devx-track-python
ms.openlocfilehash: 7e80fad02a186173868a6aa78aedeac0801f199a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496890"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformieren von Daten durch Ausführen einer Python-Aktivität in Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Die Python-Aktivität in Azure Databricks in einer [Data Factory-Pipeline](concepts-pipelines-activities.md) führt eine Python-Datei in Ihrem Azure Databricks-Cluster aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.  Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark.

Das folgende Video enthält eine 11-minütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definition der Databricks-Python-Aktivität

Dies ist die JSON-Beispieldefinition der Python-Aktivität in Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Eigenschaften der Databricks-Python-Aktivität

Die folgende Tabelle beschreibt die JSON-Eigenschaften, die in der JSON-Definition verwendet werden:

|Eigenschaft|BESCHREIBUNG|Erforderlich|
|---|---|---|
|name|Der Name der Aktivität in der Pipeline.|Ja|
|description|Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.|Nein|
|type|Bei Python-Aktivitäten in Databricks lautet der Aktivitätstyp DatabricksSparkPython.|Ja|
|linkedServiceName|Der Name des verknüpften Databricks-Diensts, in dem die Python-Aktivität ausgeführt wird. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md).|Ja|
|pythonFile|Der URI der auszuführenden Python-Datei. Es werden nur DBFS-Pfade unterstützt.|Ja|
|parameters|Befehlszeilenparameter, die an die Python-Datei übergeben werden. Es handelt sich um einen Array von Zeichenfolgen.|Nein|
|libraries|Eine Liste der Bibliotheken, die in dem Cluster installiert werden, der den Auftrag ausführen wird. Es kann ein Array vom Typ <Zeichenfolge, Objekt> sein.|Nein|

## <a name="supported-libraries-for-databricks-activities"></a>Unterstützte Bibliotheken für Databricks-Aktivitäten

In der oben genannten Definition der Databricks-Aktivität geben Sie diese Bibliothekstypen an: *JAR*, *EGG*, *Maven*, *PyPI*,  *CRAN*.

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

## <a name="how-to-upload-a-library-in-databricks"></a>Hochladen einer Bibliothek in Databricks

#### <a name="using-databricks-workspace-ui"></a>[Benutzeroberfläche des Databricks-Arbeitsbereichs](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Sie können den DBFS-Pfad der hinzugefügten Bibliothek über die Benutzeroberfläche mithilfe der [Databricks-Benutzerzeilenschnittstelle (Installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) abrufen. 

JAR-Bibliotheken werden beim Verwenden der Benutzeroberfläche in der Regel unter dbfs:/FileStore/jars gespeichert. Sie können alle über die Benutzerzeilenschnittstelle auflisten: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopieren von Bibliotheken mit der Databricks-Benutzerzeilenschnittstelle](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Beispiel: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*.
