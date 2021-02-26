---
title: Aktivität „Filter“ in Azure Data Factory
description: Die Aktivität „Filter“ filtert die Eingaben.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: 035ee5a5f1fe193fcb4579f1d46472ad9cd0b8b5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385590"
---
# <a name="filter-activity-in-azure-data-factory"></a>Aktivität „Filter“ in Azure Data Factory
Sie können eine Filter-Aktivität in einer Pipeline verwenden, um einen Filterausdruck auf ein Eingabearray anzuwenden. 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Der Name der `Filter`-Aktivität. | String | Ja
type | Muss auf **filter** festgelegt sein. | String | Ja
condition | Die Bedingung zum Filtern der Eingaben. | Ausdruck | Ja
items | Das Eingabearray, auf das der Filter angewendet werden soll. | Ausdruck | Ja

## <a name="example"></a>Beispiel

In diesem Beispiel enthält die Pipeline zwei Aktivitäten: **Filter** und **ForEach**. Die Aktivität „Filter“ wird so konfiguriert, dass das Eingabearray für Elemente mit einem Wert größer als 3 gefiltert wird. Die Aktivität ForEach durchläuft anschließend die gefilterten Werte und legt für die Variable **test** den aktuellen Wert fest.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
            "name": "MyForEach",
            "type": "ForEach",
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter anderen Ablaufsteuerungsaktivitäten, die von Data Factory unterstützt werden: 

- [Aktivität „If Condition“](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)
