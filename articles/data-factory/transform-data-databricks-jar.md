---
title: Transformieren von Daten mit JAR in Databricks – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit JAR Daten in Databricks verarbeiten oder transformieren.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: d299a785d50657ef40c0c49cb2dce33b8939fd02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860984"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformieren von Daten durch Ausführen einer JAR-Aktivität in Azure Databricks

Die JAR-Aktivität in Azure Databricks in einer [Data Factory-Pipeline](concepts-pipelines-activities.md) führt eine Spark JAR-Datei in Ihrem Azure Databricks-Cluster aus. Dieser Artikel baut auf dem Artikel zu  [Datentransformationsaktivitäten](transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet. Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark.

Das folgende Video enthält eine 11-minütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definition der Databricks-JAR-Aktivität

Dies ist die JSON-Beispieldefinition der JAR-Aktivität in Databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Eigenschaften der Databricks-JAR-Aktivität

Die folgende Tabelle beschreibt die JSON-Eigenschaften, die in der JSON-Definition verwendet werden:

|Eigenschaft|BESCHREIBUNG|Erforderlich|
|:--|---|:-:|
|name|Der Name der Aktivität in der Pipeline.|Ja|
|description|Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.|Nein|
|type|Bei JAR-Aktivitäten in Databricks lautet der Aktivitätstyp DatabricksSparkJar.|Ja|
|linkedServiceName|Der Name des verknüpften Databricks-Diensts, in dem die JAR-Aktivität ausgeführt wird. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel  [Von Azure Data Factory unterstützte Compute-Umgebungen](compute-linked-services.md).|Ja|
|mainClassName|Der vollständige Name der Klasse, die die auszuführende Hauptmethode enthält. Diese Klasse muss in einer JAR-Datei enthalten sein, die als Bibliothek bereitgestellt wird.|Ja|
|parameters|Parameter, die an die Hauptmethode übergeben werden.  Es handelt sich um einen Array von Zeichenfolgen.|Nein|
|libraries|Eine Liste der Bibliotheken, die in dem Cluster installiert werden, der den Auftrag ausführen wird. Es kann ein Array vom Typ <Zeichenfolge, Objekt> sein.|Ja (mindestens eine mit der mainClassName-Methode)|

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

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Benutzeroberfläche des Databricks-Arbeitsbereichs](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Sie können den DBFS-Pfad der hinzugefügten Bibliothek über die Benutzeroberfläche mithilfe der [Databricks-Benutzerzeilenschnittstelle (Installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) abrufen. 

JAR-Bibliotheken werden beim Verwenden der Benutzeroberfläche in der Regel unter dbfs:/FileStore/jars gespeichert. Sie können alle über die Befehlszeilenschnittstelle auflisten: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopieren von Bibliotheken mit der Databricks-Benutzerzeilenschnittstelle](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Verwenden Sie die Databricks-Befehlszeilenschnittstelle [(Installationsschritte)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Beispiel – Kopieren von JAR in dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
