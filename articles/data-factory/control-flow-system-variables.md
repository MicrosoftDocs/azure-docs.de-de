---
title: Systemvariablen in Azure Data Factory
description: Dieser Artikel beschreibt die Systemvariablen, die von Azure Data Factory unterstützt werden. Sie können diese Variablen in Ausdrücken verwenden, wenn Sie Data Factory-Entitäten definieren.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: cb19b7e8c5271e1106e69c98d5bd17a1bcb822bf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385284"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Von Azure Data Factory unterstützte Systemvariablen
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel beschreibt die Systemvariablen, die von Azure Data Factory unterstützt werden. Sie können diese Variablen in Ausdrücken verwenden, wenn Sie Data Factory-Entitäten definieren.

## <a name="pipeline-scope"></a>Bereich „Pipeline“
Auf diese Systemvariablen kann überall im Pipeline-JSON verwiesen werden.

| Variablenname | BESCHREIBUNG |
| --- | --- |
| @pipeline().DataFactory |Name der Data Factory, in der die Pipelineausführung erfolgt |
| @pipeline().Pipeline |Name der Pipeline |
| @pipeline().RunId |ID der jeweiligen Pipelineausführung |
| @pipeline().TriggerType |Der Typ des Triggers, der die Pipeline aufgerufen hat (z. B. `ScheduleTrigger`, `BlobEventsTrigger`). Eine Liste der unterstützten Triggertypen finden Sie unter [Pipelineausführung und Trigger in Azure Data Factory](concepts-pipeline-execution-triggers.md). Der Triggertyp `Manual` gibt an, dass die Pipeline manuell ausgelöst wurde. |
| @pipeline().TriggerId|ID des Triggers, der die Pipeline aufgerufen hat |
| @pipeline().TriggerName|Name des Triggers, der die Pipeline aufgerufen hat |
| @pipeline().TriggerTime|Zeitpunkt der Triggerausführung, durch die die Pipeline aufgerufen wurde. Dies ist der Zeitpunkt, zu dem der Trigger zum Aufrufen der Pipelineausführung **tatsächlich** ausgelöst wurde. Er kann vom geplanten Zeitpunkt für den Trigger geringfügig abweichen.  |

>[!NOTE]
>Triggerbezogene Systemvariablen für Datum/Uhrzeit (in Pipeline- und Triggerbereichen) geben UTC-Datumsangaben im ISO 8601-Format zurück, z. B. `2017-06-01T22:20:00.4061448Z`.

## <a name="schedule-trigger-scope"></a>Bereich „Plantrigger“
Auf diese Systemvariablen kann überall im Trigger-JSON für Trigger vom Typ [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger) verwiesen werden.

| Variablenname | Beschreibung |
| --- | --- |
| @trigger().scheduledTime |Zeitpunkt, zu dem der Trigger die Pipelineausführung planmäßig aufrufen sollte. |
| @trigger().startTime |Zeitpunkt, zu dem der Trigger **tatsächlich** ausgelöst wurde, um die Pipelineausführung aufzurufen. Er kann vom geplanten Zeitpunkt für den Trigger geringfügig abweichen. |

## <a name="tumbling-window-trigger-scope"></a>Bereich „Trigger mit rollierendem Fenster“
Auf diese Systemvariablen kann überall im Trigger-JSON für Trigger vom Typ [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger) verwiesen werden.

| Variablenname | Beschreibung |
| --- | --- |
| @trigger().outputs.windowStartTime |Anfang des Fensters, das der Triggerausführung zugeordnet wurde. |
| @trigger().outputs.windowEndTime |Ende des Fensters, das der Triggerausführung zugeordnet wurde. |
| @trigger().scheduledTime |Zeitpunkt, zu dem der Trigger die Pipelineausführung planmäßig aufrufen sollte. |
| @trigger().startTime |Zeitpunkt, zu dem der Trigger **tatsächlich** ausgelöst wurde, um die Pipelineausführung aufzurufen. Er kann vom geplanten Zeitpunkt für den Trigger geringfügig abweichen. |

## <a name="event-based-trigger-scope"></a>Ereignisbasierter Triggerbereich
Auf diese Systemvariablen kann überall im Trigger-JSON für Trigger vom Typ [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger) verwiesen werden.

| Variablenname | Beschreibung |
| --- | --- |
| @triggerBody().fileName  |Name der Datei, deren Erstellung oder Löschung bewirkt hat, dass der Trigger ausgelöst wurde.   |
| @triggerBody().folderName  |Pfad zu dem Ordner, der die durch `@triggerBody().fileName`angegebene Datei enthält. Das erste Segment des Ordnerpfads ist der Name des Azure Blob Storage-Containers.  |
| @trigger().startTime |Zeitpunkt, zu dem der Trigger ausgelöst wurde, um die Pipelineausführung aufzurufen. |

## <a name="next-steps"></a>Nächste Schritte
Informationen darüber, wie diese Variablen in Ausdrücken verwendet werden, finden Sie unter [Ausdruckssprache und Funktionen](control-flow-expression-language-functions.md).
