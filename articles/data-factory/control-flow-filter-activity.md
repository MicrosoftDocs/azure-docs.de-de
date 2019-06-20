---
title: Aktivität „Filter“ in Azure Data Factory | Microsoft-Dokumentation
description: Die Aktivität „Filter“ filtert die Eingaben.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: shlo
ms.openlocfilehash: 787c9393e2700bd7ed349b501e70abc4a0687b9c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60554846"
---
# <a name="filter-activity-in-azure-data-factory"></a>Aktivität „Filter“ in Azure Data Factory
Sie können eine Filter-Aktivität in einer Pipeline verwenden, um einen Filterausdruck auf ein Eingabearray anzuwenden. 

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
name | Der Name der `Filter`-Aktivität. | string | Ja
type | Muss auf **filter** festgelegt sein. | string | Ja
condition | Die Bedingung zum Filtern der Eingaben. | Ausdruck | Ja
items | Das Eingabearray, auf das der Filter angewendet werden soll. | Ausdruck | Ja

## <a name="example"></a>Beispiel

In diesem Beispiel enthält die Pipeline zwei Aktivitäten: **Filter** und **ForEach**. Die Aktivität „Filter“ wird so konfiguriert, dass das Eingabearray für Elemente mit einem Wert größer als 3 gefiltert wird. Die Aktivität „ForEach“ durchläuft anschließend die gefilterten Werte und wartet die vom aktuellen Wert angegebene Dauer in Sekunden.

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
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
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
