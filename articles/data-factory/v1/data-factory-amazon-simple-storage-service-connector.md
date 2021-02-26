---
title: Verschieben von Daten aus Amazon Simple Storage Service mithilfe von Data Factory
description: Hier erfahren Sie, wie Sie mithilfe von Azure Data Factory Daten aus Amazon Simple Storage Service (S3) verschieben.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6d254a535b1db53478772b481bd029a8c4db6f3c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361345"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Verschieben von Daten aus Amazon Simple Storage Service mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Version 2 (aktuelle Version)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Amazon S3-Connector in V2](../connector-amazon-simple-storage-service.md).

Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Amazon Simple Storage Service (S3) zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Sie können Daten aus Amazon S3 in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory unterstützt derzeit nur das Verschieben von Daten aus Amazon S3 in andere Datenspeicher und nicht das Verschieben aus anderen Datenspeichern in Amazon S3.

## <a name="required-permissions"></a>Erforderliche Berechtigungen
Um Daten von Amazon S3 zu kopieren, müssen Sie sicherstellen, dass Ihnen die folgenden Berechtigungen erteilt wurden:

* `s3:GetObject` und `s3:GetObjectVersion` für Amazon S3-Objektvorgänge.
* `s3:ListBucket` für Amazon S3-Bucketvorgänge. Wenn Sie den Assistent zum Kopieren in Data Factory verwenden, ist außerdem `s3:ListAllMyBuckets` erforderlich.

Ausführliche Informationen zur vollständigen Liste der Amazon S3-Berechtigungen finden Sie unter [Specifying Permissions in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) (Angeben von Berechtigungen in einer Richtlinie).

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools oder APIs aus einer Amazon S3-Quelle verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Eine kurze exemplarische Vorgehensweise finden Sie unter [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md).

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlage**, **.NET-API** und **REST-API**. Eine detaillierte Anleitung zum Erstellen einer Pipeline mit einer Kopieraktivität finden Sie im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Zur Erstellung einer Pipeline, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt, müssen unabhängig von der Verwendung von Tools oder APIs folgende Schritte ausgeführt werden:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang.
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools oder APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einem Amazon S3-Datenspeicher verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten aus Amazon S3 in Azure Blob Storage](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) in diesem Artikel.

> [!NOTE]
> Ausführliche Informationen zu unterstützten Datei- und Komprimierungsformaten für eine Kopieraktivität finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren spezifischer Data Factory-Entitäten für Amazon S3 verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Ein verknüpfter Dienst verbindet einen Data Store mit einer Data Factory. Sie erstellen einen verknüpften Dienst vom Typ **AwsAccessKey**, um Ihren Amazon S3-Datenspeicher mit ihrer Data Factory zu verbinden. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Amazon S3 (AwsAccessKey) verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| accessKeyID |ID des geheimen Zugriffsschlüssels. |Zeichenfolge |Ja |
| secretAccessKey |Der geheime Zugriffsschlüssel selbst. |Verschlüsselte geheime Zeichenfolge |Ja |

>[!NOTE]
>Dieser Connector erfordert Zugriffsschlüssel für ein IAM-Konto zum Kopieren von Daten aus Amazon S3. [Temporäre Sicherheitsanmeldeinformationen](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) werden nicht unterstützt.
>

Beispiel:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Legen Sie die Typeigenschaft des Datasets auf **AmazonS3** fest, um ein Dataset zur Darstellung von Eingabedaten in Azure Blob Storage anzugeben. Legen Sie die **linkedServiceName**-Eigenschaft des Datasets auf den Namen des mit Amazon S3 verknüpften Diensts fest. Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Erstellen von Datasets](data-factory-create-datasets.md). 

Abschnitte wie „structure“, „availability“ und „policy“ sind bei allen Dataset-Typen (wie etwa SQL-Datenbank, Azure-Blob und Azure-Tabelle) ähnlich. Der Abschnitt **typeProperties** unterscheidet sich bei jeder Art von Dataset und enthält Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein Dataset vom Typ **AmazonS3** (mit dem Amazon S3-Dataset) hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| bucketName |Der Name des S3-Buckets. |String |Ja |
| Schlüssel |Der S3-Objektschlüssel. |String |Nein |
| prefix |Präfix für den S3-Objektschlüssel. Objekte, deren Schlüssel mit diesem Präfix beginnen, werden ausgewählt. Gilt nur, wenn der Schlüssel leer ist. |String |Nein |
| version |Die Version des S3-Objekts, wenn die S3-Versionsverwaltung aktiviert ist. |String |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), können Sie den Formatabschnitt in den Definitionen von Eingabe- und Ausgabedatasets überspringen. | |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Die unterstützten Typen sind: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Die folgenden Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |Nein |


> [!NOTE]
> **bucketName + key** gibt den Speicherort des S3-Objekts an, wobei „bucketName“ der Name des Stammcontainers für S3-Objekte und „key“ der vollständige Pfad zum S3-Objekt ist.

### <a name="sample-dataset-with-prefix"></a>Beispieldataset mit Präfix

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Beispieldataset (mit Version)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Dynamische Pfade für S3
Im vorherigen Beispiel werden für die Eigenschaften **key** und **bucketName** im Amazon S3-Dataset feste Werte verwendet.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Mithilfe von Systemvariablen wie „SliceStart“ können Sie diese Eigenschaften dynamisch zur Laufzeit durch Data Factory berechnen lassen.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Gleiches gilt für die Eigenschaft **prefix** eines Amazon S3-Datasets. Eine Liste mit unterstützten Funktionen und Variablen finden Sie unter [Data Factory – Funktionen und Systemvariablen](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinien sind für alle Arten von Aktivitäten verfügbar. Eigenschaften im Abschnitt **typeProperties** der Aktivität können je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken. Wenn es sich bei einer Quelle in der Kopieraktivität um eine Quelle vom Typ **FileSystemSource** handelt (zu dem Amazon S3 gehört), ist im Abschnitt **typeProperties** die folgende Eigenschaft verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob S3-Objekte rekursiv im Verzeichnis aufgelistet werden. |TRUE/FALSE |Nein |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-Beispiel: Kopieren von Daten aus Amazon S3 in Azure Blob Storage
Dieses Beispiel zeigt, wie Sie Daten aus Amazon S3 in Azure Blob Storage kopieren. Daten können jedoch mithilfe der Kopieraktivität in Data Factory direkt in eine [beliebige der unterstützten Senken](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopiert werden.

Das Beispiel enthält JSON-Definitionen für die folgenden Data Factory-Entitäten. Sie können diese Definitionen zur Erstellung einer Pipeline verwenden, um Daten aus Amazon S3 in Blob Storage zu kopieren (mithilfe von [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)).   

* Einen verknüpften Dienst des Typs [AwsAccessKey](#linked-service-properties).
* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AmazonS3](#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [FileSystemSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im Beispiel werden stündlich Daten aus Amazon S3 in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

### <a name="amazon-s3-linked-service"></a>Mit Amazon S3 verknüpfter Dienst

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst

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

### <a name="amazon-s3-input-dataset"></a>Amazon S3-Eingabedataset

Durch Festlegen von **"external": true** wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist. Legen Sie diese Eigenschaft für ein Eingabedataset, das nicht durch eine Aktivität in der Pipeline erstellt wird, auf „true“ fest.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Azure-Blob-Ausgabedataset

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Kopieraktivität in einer Pipeline mit einer Amazon S3-Quelle und einer Blobsenke

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **FileSystemSource** und der Typ **sink** auf **BlobSink** festgelegt.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Weitere Informationen zum Zuordnen von Spalten aus einem Quelldataset zu Spalten aus einem Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

* Im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) werden wichtige Faktoren beschrieben, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.

* Eine Schritt-für-Schritt-Anleitung zum Erstellen einer Pipeline mit einer Kopieraktivität finden Sie im [Kopieraktivität-Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
