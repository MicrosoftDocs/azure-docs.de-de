---
title: Lesen oder Schreiben partitionierter Daten in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie partitionierte Daten in Azure Data Factory gelesen oder geschrieben werden.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: f2d780900a0cd24f2d70499573a4055dc836ae0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61466985"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Lesen oder Schreiben partitionierter Daten in Azure Data Factory

In Azure Data Factory Version 1 konnten Sie partitionierte Daten mit den Systemvariablen **SliceStart**, **SliceEnd**, **WindowStart** und **WindowEnd** lesen oder schreiben. In der aktuellen Version von Data Factory können Sie dieses Verhalten erreichen, indem Sie einen Pipelineparameter und die Startzeit oder geplante Zeit eines Triggers als Wert des Parameters verwenden. 

## <a name="use-a-pipeline-parameter"></a>Verwenden eines Pipelineparameters 

In Data Factory Version 1 konnten Sie die **partitionedBy**-Eigenschaft und die **SliceStart**-Systemvariable wie im folgenden Beispiel dargestellt verwenden: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Weitere Informationen zur Eigenschaft **partitonedBy** finden Sie unter [Kopieren von Daten mithilfe von Azure Data Factory in oder aus Azure Blob Storage](v1/data-factory-azure-blob-connector.md#dataset-properties). 

Um dieses Verhalten in der aktuellen Version von Data Factory zu erreichen, gehen Sie folgendermaßen vor: 

1. Definieren Sie einen *Pipelineparameter* vom Typ **Zeichenfolge**. Im folgenden Beispiel lautet der Name des Pipelineparameters **windowStartTime**. 
2. Legen Sie in der Datasetdefinition **folderPath** als Verweis auf den Wert des Pipelineparameters fest. 
3. Übergeben Sie den tatsächlichen Wert für den Parameter, wenn Sie die Pipeline bei Bedarf aufrufen. Sie können auch die Anfangszeit oder geplante Zeit eines Triggers dynamisch zur Laufzeit übergeben. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-a-value-from-a-trigger"></a>Übergeben eines Werts aus einem Trigger

In der folgenden Triggerdefinition mit rollierendem Fenster wird das Startzeitfenster des Triggers als Wert für den Pipelineparameter **windowStartTime** übergeben: 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>Beispiel

Es folgt ein Beispiel für eine Datasetdefinition:

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Pipelinedefinition: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Eine ausführliche exemplarische Vorgehensweise zum Erstellen einer Data Factory mit einer Pipeline finden Sie unter [Schnellstart: Erstellen einer Data Factory](quickstart-create-data-factory-powershell.md). 

