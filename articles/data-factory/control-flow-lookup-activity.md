---
title: Lookup-Aktivität in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie die Lookup-Aktivität verwendet wird, um einen Wert in einer externen Quelle zu suchen. Auf die Ausgabe kann durch nachfolgende Aktivitäten verwiesen werden.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 4f0662a71ee14af3c2c1aafee210641fc8b51f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60768655"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Lookup-Aktivität in Azure Data Factory

Mithilfe der Lookup-Aktivität kann ein Dataset aus einer der von Azure Data Factory unterstützten Datenquellen abgerufen werden. Verwenden Sie sie im folgenden Szenario:
- Es müssen Objekte dynamisch bestimmt werden, die in einer nachfolgenden Aktivität verarbeitet werden sollen, statt der Hartcodierung von Objektnamen. Beispiele für Objekte sind Dateien und Tabellen.

Die Lookup-Aktivität liest die Daten und gibt den Inhalt einer Konfigurationsdatei oder Tabelle zurück. Zudem gibt sie auch das Ergebnis einer ausgeführten Abfrage oder gespeicherten Prozedur zurück. Die Ausgabe der Lookup-Aktivität kann als Singleton-Wert in einer nachfolgenden Kopier- oder Transformationsaktivität verwendet werden. Wenn es sich um eine Gruppe von Attributen handelt, kann die Ausgabe in einer ForEach-Aktivität verwendet werden.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die folgenden Datenquellen werden für die Lookup-Aktivität unterstützt. Von der Lookup-Aktivität können höchstens 5.000 Zeilen mit einer Größe von bis zu 2 MB zurückgegeben werden. Derzeit beträgt die Höchstdauer für die Lookup-Aktivität vor dem Zeitlimit eine Stunde.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Typeigenschaften

NAME | BESCHREIBUNG | Type | Erforderlich?
---- | ----------- | ---- | --------
dataset | Enthält die Datasetreferenz für die Lookupaktivität. Details finden Sie in den entsprechenden Connectorartikeln im Abschnitt **Dataset-Eigenschaften**. | Schlüssel-Wert-Paar | Ja
source | Enthält spezifische Quelleneigenschaften für das Dataset, identisch mit der Quelle der Kopieraktivität. Details finden Sie in jedem entsprechenden Connectorartikel im Abschnitt **Eigenschaften der Kopieraktivität**. | Schlüssel-Wert-Paar | Ja
firstRowOnly | Gibt an, ob nur die erste Zeile oder alle Zeilen zurückgegeben werden sollen. | Boolean | Nein. Der Standardwert lautet `true`.

> [!NOTE]
> 
> * Quellspalten mit dem Typ **ByteArray** werden nicht unterstützt.
> * In der Datasetdefinition wird keine **Struktur** unterstützt. Bei Textformatdateien geben Sie den Spaltennamen in der Kopfzeile an.
> * Wenn es sich bei Ihrer Lookup-Quelle um eine JSON-Datei handelt, wird die `jsonPathDefinition`-Einstellung zur Neugestaltung des JSON-Objekts nicht unterstützt. Die gesamten Objekte werden abgerufen.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Verwenden des Ergebnisses der Lookup-Aktivität in einer nachfolgenden Aktivität

Das Lookupergebnis wird im Abschnitt `output` des Ergebnisses der Aktivitätsausführung zurückgegeben.

* **Wenn `firstRowOnly` auf `true` (Standardwert) gesetzt ist**, wird das Ausgabeformat wie im folgenden Code gezeigt. Das Lookupergebnis befindet sich unter dem festen Schlüssel `firstRow`. Um das Ergebnis in nachfolgenden Aktivitäten zu nutzen, verwenden Sie das Muster `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Wenn `firstRowOnly` auf `false` festgelegt ist**, wird das Ausgabeformat wie im folgenden Code gezeigt. Ein Feld `count` gibt an, wie viele Datensätze zurückgegeben werden. Detaillierte Werte werden unter einem festen `value`-Array angezeigt. In diesem Fall folgt auf die Lookup-Aktivität eine [ForEach-Aktivität](control-flow-for-each-activity.md). Um das Array `value` an das Feld `items` der ForEach-Aktivität zu übergeben, verwenden Sie das Muster `@activity('MyLookupActivity').output.value`. Für den Zugriff auf Elemente in Array `value` verwenden Sie die folgende Syntax: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Ein Beispiel ist `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Beispiel für Kopieraktivität
In diesem Beispiel kopieren Sie mit der Kopieraktivität Daten aus einer SQL-Tabelle in Ihrer Azure SQL-Datenbank-Instanz nach Azure Blob Storage. Der Name der SQL-Tabelle wird in einer JSON-Datei in Blob Storage gespeichert. Die Lookup-Aktivität sucht den Tabellennamen zur Runtime. Durch diesen Ansatz wird die JSON-Datei dynamisch geändert. Pipelines oder Datasets müssen nicht erneut bereitgestellt werden. 

Dieses Beispiel zeigt nur das Lookup für die erste Zeile. Wenn Sie weitere Informationen zum Lookup für alle Zeilen und dem Verketten von Ergebnissen mit der ForEach-Aktivität benötigen, lesen Sie die Beispiele in [Massenkopieren von mehreren Tabellen mithilfe von Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Diese Pipeline enthält zwei Aktivitäten: Lookup und Copy. 

- Die Lookup-Aktivität ist für die Verwendung von **LookupDataset** konfiguriert, das auf einen Speicherort in Azure Blob Storage verweist. Der Name der SQL-Tabelle wird von der Lookup-Aktivität aus einer JSON-Datei an diesem Speicherort gelesen. 
- Die Kopieraktivität verwendet die Ausgabe der Lookup-Aktivität, also den Namen der SQL-Tabelle. Die **tableName**-Eigenschaft in **SourceDataset** ist für die Verwendung der Ausgabe der Lookup-Aktivität konfiguriert. Die Kopieraktivität kopiert Daten aus der SQL-Tabelle an einen Speicherort in Azure Blob Storage. Der Speicherort wird durch die **SinkDataset**-Eigenschaft festgelegt. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Suchdataset
Das **Lookup**-Dataset verweist auf die Datei **sourcetable.json** im Lookup-Ordner von Azure Storage, der durch den **AzureStorageLinkedService**-Typ angegeben wird. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**source**-Dataset für die Kopieraktivität
Das **source**-Dataset verwendet die Ausgabe der Lookup-Aktivität, also den Namen der SQL-Tabelle. Die Kopieraktivität kopiert Daten aus dieser SQL-Tabelle an einen Speicherort in Azure Blob Storage. Der Speicherort wird durch das **sink**-Dataset festgelegt. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**sink**-Dataset für die Kopieraktivität
Die Kopieraktivität kopiert Daten aus der SQL-Tabelle in die Datei **filebylookup.csv** im Ordner **csv** in Azure Storage. Die Datei wird durch die **AzureStorageLinkedService**-Eigenschaft festgelegt. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst
Dieses Speicherkonto enthält die JSON-Datei mit den Namen der SQL-Tabellen. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Mit Azure SQL-Datenbank verknüpfter Dienst
Diese Azure SQL-Datenbankinstanz enthält die Daten, die in Blob Storage kopiert werden sollen. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Satz von Objekten

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Array von Objekten

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Einschränkungen und Problemumgehungen

Hier werden einige Einschränkungen der Lookup-Aktivität und empfohlene Problemumgehungen aufgeführt.

| Einschränkung | Problemumgehung |
|---|---|
| Die Grenze einer Lookup-Aktivität liegt bei maximal 5.000 Zeilen und einer maximalen Größe von 2MB. | Entwerfen Sie eine Pipeline mit zwei Ebenen, wobei die äußere Pipeline eine innere Pipeline durchläuft, die Daten abruft, die die maximale Zeilenanzahl oder Größe nicht überschreiten. |
| | |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter anderen Ablaufsteuerungsaktivitäten, die von Data Factory unterstützt werden: 

- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Webaktivität](control-flow-web-activity.md)
