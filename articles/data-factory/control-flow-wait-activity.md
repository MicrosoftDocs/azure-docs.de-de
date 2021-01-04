---
title: Warteaktivität in Azure Data Factory
description: Die Warteaktivität hält die Ausführung der Pipeline für den angegebenen Zeitraum an.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 12c9027067c00a3db84e6610a0d73090cc011713
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485909"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Ausführen der Warteaktivität in Azure Data Factory
Wenn Sie eine Warteaktivität in einer Pipeline verwenden, wartet die Pipeline den angegebenen Zeitraum, bevor Sie die Ausführung nachfolgender Aktivitäten fortsetzt. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntax

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Der Name der `Wait`-Aktivität. | String | Ja
type | Muss auf **Wait** festgelegt werden. | String | Ja
waitTimeInSeconds | Die Anzahl von Sekunden, die die Pipeline wartet, bevor sie die Verarbeitung fortsetzt | Integer | Ja

## <a name="example"></a>Beispiel

> [!NOTE]
> Dieser Abschnitt enthält die JSON-Definitionen und PowerShell-Beispielbefehle zum Ausführen der Pipeline. Eine exemplarische Vorgehensweise mit einer ausführlichen Anleitung zum Erstellen einer Data Factory-Pipeline mithilfe von Azure PowerShell und JSON-Definitionen finden Sie unter [Erstellen einer Data Factory und Pipeline mithilfe von PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline mit Warteaktivität
In diesem Beispiel besitzt die Pipeline zwei Aktivitäten: **Until** und **Wait**. Für die Warteaktivität ist eine Wartezeit von einer Sekunde konfiguriert. Die Pipeline führt die Webaktivität in einer Schleife mit einer Sekunde Wartezeit zwischen jeder Ausführung aus. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
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
