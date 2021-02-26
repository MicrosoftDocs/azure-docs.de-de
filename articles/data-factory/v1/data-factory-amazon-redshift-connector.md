---
title: Verschieben von Daten von Amazon Redshift mithilfe von Azure Data Factory
description: Erfahren Sie, wie Sie mithilfe der Kopieraktivität von Azure Data Factory Daten aus Amazon Redshift verschieben.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 025250f47bf0630be5ae988140a5feeecfd0eaf0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377549"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Verschieben von Daten mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-amazon-redshift-connector.md)
> * [Version 2 (aktuelle Version)](../connector-amazon-redshift.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Bei Verwendung der aktuellen Version des Data Factory-Diensts finden Sie weitere Informationen unter [Amazon Redshift connector in V2](../connector-amazon-redshift.md) (Amazon Redshift-Connector in V2).

Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Amazon Redshift zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Data Factory unterstützt derzeit nur das Verschieben von Daten aus Amazon Redshift in eine [unterstützte Datenspeichersenke](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Das Verschieben von Daten aus anderen Datenspeichern nach Amazon Redshift wird nicht unterstützt.

> [!TIP]
> Um beim Kopieren großer Datenmengen aus Amazon Redshift eine optimale Leistung zu erzielen, empfiehlt sich die Verwendung des integrierten Redshift-Befehls **UNLOAD** über Amazon Simple Storage Service (Amazon S3). Ausführliche Informationen finden Sie im Abschnitt [Verwenden von UNLOAD zum Kopieren von Daten aus Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Voraussetzungen
* Wenn Sie Daten in einen einem lokalen Datenspeicher verschieben möchten, installieren Sie das [Datenverwaltungsgateway](data-factory-data-management-gateway.md) auf dem lokalen Computer. Gewähren Sie einem Gateway Zugriff auf den Amazon Redshift-Cluster mit der IP-Adresse des lokalen Computers. Anweisungen finden Sie unter [Authorize access to the cluster (Autorisieren des Zugriffs auf den Cluster)](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Informationen zum Verschieben von Daten in ein Azure-Datenspeicher finden Sie unter [Compute IP address and SQL ranges that are used by the Microsoft Azure Datacenters (Server-IP-Adresse und SQL-Bereiche, die von den Microsoft Azure-Datencentern verwendet werden)](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools und APIs aus einer Amazon Redshift-Quelle verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem Kopier-Assistenten von Azure Data Factory. Eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Kopier-Assistenten finden Sie im [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md).

Sie können auch Visual Studio, Azure PowerShell oder andere Tools verwenden, um eine Pipeline zu erstellen. Azure Resource Manager-Vorlagen, die .NET API oder die REST-API können ebenfalls zur Erstellung der Pipeline verwendet werden. Eine ausführliche Anleitung zum Erstellen einer Pipeline mit einer Kopieraktivität finden Sie im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen Sie verknüpfte Dienste zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen Sie DataSets zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang.
3. Erstellen Sie eine Pipeline mit einer Kopieraktivität, die ein DataSet als Eingabe und ein DataSet als Ausgabe akzeptiert.

Wenn Sie den Kopier-Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten erstellt. Bei Verwendung von Tools oder APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Das JSON-Beispiel Kopieren von Daten aus Amazon Redshift in Azure Blob Storage zeigt die JSON-Definitionen für die Data Factory-Entitäten, die zum Kopieren von Daten aus einem Amazon Redshift-Datenspeicher verwendet werden.

Die folgenden Abschnitte beschreiben die JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten für Amazon Redshift verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Die folgende Tabelle enthält Beschreibungen der JSON-Elemente, die für einen mit Amazon Redshift verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| **type** |Diese Eigenschaft muss auf **AmazonRedshift** festgelegt sein. |Ja |
| **server** |Die IP-Adresse oder der Hostname des Amazon Redshift-Servers. |Ja |
| **port** |Die Nummer des TCP-Ports, den der Amazon Redshift-Server verwendet, um auf Clientverbindungen zu lauschen. |Nein (der Standardwert lautet 5439) |
| **database** |Der Name der Amazon Redshift-Datenbank. |Ja |
| **username** |Der Name des Benutzers, der Zugriff auf die Datenbank hat. |Ja |
| **password** |Das Kennwort für das Benutzerkonto |Ja |

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von DataSets zur Verfügung stehen, finden Sie im Artikel [Erstellen von DataSets](data-factory-create-datasets.md). Abschnitte wie **structure**, **availability** und **policy** sind bei allen dataset-Typen ähnlich. Beispiele für dataset-Typen sind Azure SQL, Azure Blob Storage und Azure Table Storage.

Der Abschnitt **typeProperties** unterscheidet sich bei jeder Art von Dataset und enthält Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein Dataset vom Typ **RelationalTable**, zu dem das Amazon Redshift-Dataset gehört, weist die folgenden Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| **tableName** |Der Name der Tabelle in der Amazon Redshift-Datenbank, auf die der verknüpfte Dienst verweist. |Nein (wenn die **query**-Eigenschaft einer Kopieraktivität vom Typ **RelationalSource** angegeben wurde) |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Die Eigenschaften für **name**, **description**, **inputs**-Tabelle, **outputs**-Tabelle und **policy** sind für alle Arten von Aktivitäten verfügbar. Die Eigenschaften im Abschnitt **typeProperties** können je nach Art der Aktivität variieren. Für die Kopieraktivität variieren die Eigenschaften des Typs je nach Art der Datenquellen und Senken.

Wenn bei einer Kopieraktivität die Quelle den Typ **AmazonRedshiftSource** aufweist, sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| **query** | Verwendet die benutzerdefinierte Abfrage zum Lesen der Daten. |Nein (wenn die **tableName**-Eigenschaft eines DataSets angegeben wurde) |
| **redshiftUnloadSettings** | Enthält bei Verwendung des Redshift-Befehls **UNLOAD** die Eigenschaftsgruppe. | Nein |
| **s3LinkedServiceName** | Der Amazon S3-Speicher, der als vorläufiger Speicher verwendet werden soll. Der verknüpfte Dienst wird mithilfe eines Azure Data Factory-Namens des Typs **AwsAccessKey** angegeben. | Erforderlich, wenn Sie die **RedshiftUnloadSettings**-Eigenschaft verwenden |
| **bucketName** | Gibt den zu verwendenden Amazon S3-Bucket zum Speichern der vorläufigen Daten an. Bei fehlender Angabe dieser Eigenschaft generiert die Kopieraktivität automatisch einen Bucket. | Erforderlich, wenn Sie die **RedshiftUnloadSettings**-Eigenschaft verwenden |

Alternativ können Sie auch den Typ **RelationalSource**, der Amazon Redshift enthält, mit der folgenden Eigenschaft im Abschnitt **typeProperties** verwenden. Beachten Sie, dass dieser Quelltyp den Redshift-Befehl **UNLOAD** nicht unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| **query** |Verwendet die benutzerdefinierte Abfrage zum Lesen der Daten. | Nein (wenn die **tableName**-Eigenschaft eines DataSets angegeben wurde) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Verwenden von UNLOAD zum Kopieren von Daten aus Amazon Redshift

Der Amazon Redshift-Befehl [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) wird die Ergebnisse einer Abfrage auf eine oder mehrere Dateien auf Amazon S3 entladen. Dieser Befehl wird von Amazon zum Kopieren großer Datasets aus Redshift empfohlen.

**Beispiel: Kopieren von Daten aus Amazon Redshift zu Azure Synapse Analytics**

Dieses Beispiel kopiert Daten aus Amazon Redshift in Azure Synapse Analytics. Das Beispiel verwendet den Redshift-Befehl **UNLOAD**, gestaffelte Kopierdaten und Microsoft PolyBase.

Für diesen Anwendungsfall entlädt die Kopieraktivität zuerst die Daten aus Amazon Redshift nach Amazon S3, entsprechend der Konfiguration der **redshiftUnloadSettings**-Option. Als Nächstes werden die Daten von Amazon S3 nach Azure Blob Storage kopiert, gemäß der **stagingSettings**-Option. Abschließend lädt PolyBase die Daten in Azure Synapse Analytics. Sämtliche vorläufigen Formate werden von der Kopieraktivität verarbeitet.

![Workflow des Kopierens von Amazon Redshift zu Azure Synapse Analytics](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-Beispiel: Kopieren von Daten aus Amazon Redshift nach Azure Blob Storage
Dieses Beispiel zeigt, wie Sie Daten aus einer Amazon Redshift-Datenbank nach Azure Blob Storage kopieren. Mithilfe der Kopieraktivität können Daten direkt in eine [unterstützte Senke](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopiert werden.

Das Beispiel enthält die folgenden Data Factory-Entitäten:

* Einen verknüpften Dienst des Typs [AmazonRedshift](#linked-service-properties)
* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein Eingabe-[DataSet](data-factory-create-datasets.md) vom Typ [RelationalTable](#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) vom Typ [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit einer Kopieraktivität, die die Eigenschaften [RelationalSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Das Beispiel kopiert stündlich Daten aus einem Abfrageergebnis in Amazon Redshift in ein Azure-Blob. Die im Beispiel verwendeten JSON-Eigenschaften werden in den Abschnitten nach den Entitätsdefinitionen beschrieben.

**Mit Amazon Redshift verknüpfter Dienst**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Mit Azure Blob Storage verknüpfter Dienst**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Amazon Redshift-Eingabedataset**

Durch Festlegen der **external**-Eigenschaft auf „TRUE“, wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist. Diese Eigenschaftseinstellung gibt an, dass das Dataset nicht von einer Aktivität in der Data Factory erzeugt wird. Legen Sie diese Eigenschaft für ein Eingabedataset, das nicht durch eine Aktivität in der Pipeline erstellt wird, auf „TRUE“ fest.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben, indem Sie die **frequency**-Eigenschaft auf „Hour“ und die **interval**-Eigenschaft auf „1“ festlegen. Die **folderPath**-Eigenschaft für das Blob wird dynamisch ausgewertet. Der Eigenschaftswert basiert auf der Startzeit des Slices, der verarbeitet wird. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopieraktivität in einer Pipeline mit einer Azure Redshift-Quelle (des Typs RelationalSource) und einer Azure-Blobsenke**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets konfiguriert ist. Für die Pipeline ist die Ausführung einmal pro Stunde geplant. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **query**-Eigenschaft angegebene SQL-Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Typzuordnung für Amazon Redshift
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen durch. Die Typen werden anhand eines Ansatzes in zwei Schritten konvertiert:

1. Konvertieren von nativen Quelltypen in .NET-Typen
2. Konvertieren von .NET-Typen in native Senkentypen

Die folgenden Zuordnungen werden angewendet, wenn die Kopieraktivität Daten aus einem Amazon Redshift-Typ in einen .NET-Datentyp konvertiert:

| Amazon Redshift-Typ | .NET-Typ |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| bigint |Int64 |
| DECIMAL |Decimal |
| real |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |Datetime |
| timestamp |Datetime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quell-DataSet zu Spalten im Senken-DataSet finden Sie unter [Zuordnen von DataSet-Spalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen
Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch die **Wiederholungsrichtlinie** für ein Dataset konfigurieren, um einen Slice erneut auszuführen, wenn ein Fehler auftritt. Stellen Sie sicher, dass dieselben Daten gelesen werden, egal, wie oft der Slice erneut ausgeführt wird. Stellen Sie außerdem sicher, dass dieselben Daten gelesen werden, unabhängig davon, auf welche Weise der Slice erneut ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) werden wichtige Faktoren beschrieben, die sich auf die Leistung der Kopieraktivität auswirken, sowie Möglichkeiten zur Leistungsoptimierung.

## <a name="next-steps"></a>Nächste Schritte
Eine ausführliche Anleitung zum Erstellen einer Pipeline mit einer Kopieraktivität finden Sie im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
