---
title: Systemvariablen in Azure Data Factory
description: Dieser Artikel beschreibt die Systemvariablen, die von Azure Data Factory unterstützt werden. Sie können diese Variablen in Ausdrücken verwenden, wenn Sie Data Factory-Entitäten definieren.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 1780b4a64de349c1e272158fe6bfde9cab6f8369
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486044"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Von Azure Data Factory unterstützte Systemvariablen
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel beschreibt die Systemvariablen, die von Azure Data Factory unterstützt werden. Sie können diese Variablen in Ausdrücken verwenden, wenn Sie Data Factory-Entitäten definieren.

## <a name="pipeline-scope"></a>Bereich „Pipeline“
Auf diese Systemvariablen kann überall im Pipeline-JSON verwiesen werden.

| Variablenname | BESCHREIBUNG |
| --- | --- |
| @pipeline().DataFactory |Name der Data Factory, in der die Pipelineausführung ausgeführt wird |
| @pipeline().Pipeline |Name der Pipeline |
| @pipeline().RunId | ID der jeweiligen Pipelineausführung |
| @pipeline().TriggerType | Typ des Triggers, der die Pipeline aufgerufen hat (manuell, Planer) |
| @pipeline().TriggerId| ID des Triggers, der die Pipeline aufruft |
| @pipeline().TriggerName| Name des Triggers, der die Pipeline aufruft |
| @pipeline().TriggerTime| Zeit, zu der der Trigger die Pipeline aufgerufen hat. Die Triggerzeit ist die Zeit, zu der die Pipeline tatsächlich ausgelöst wurde, nicht die geplante Zeit. Beispiel: `13:20:08.0149599Z` wird anstelle von `13:20:00.00Z` zurückgegeben |

## <a name="schedule-trigger-scope"></a>Bereich „ScheduleTrigger“
Auf diese Systemvariablen kann überall im Trigger-JSON verwiesen werden, wenn der Trigger den Typ „ScheduleTrigger“ hat.

| Variablenname | BESCHREIBUNG |
| --- | --- |
| @trigger().scheduledTime |Zeit, zu der der Trigger die Pipelineausführung planmäßig aufrufen sollte. Beispiel: Für einen Trigger, der alle 5 Minuten ausgelöst wird, würde diese Variable `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z` bzw. `2017-06-01T22:30:00Z` zurückgeben.|
| @trigger().startTime |Zeit, zu der der Trigger **tatsächlich** ausgelöst wurde, um die Pipelineausführung aufzurufen. Beispiel: Für einen Trigger, der alle 5 Minuten ausgelöst wird, könnte diese Variable Werte wie `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z` bzw. `2017-06-01T22:30:00.9935483Z` zurückgeben. (Hinweis: Der Zeitstempel ist standardmäßig im ISO 8601-Format.)|

## <a name="tumbling-window-trigger-scope"></a>Bereich „TumblingWindowTrigger“
Auf diese Systemvariablen kann überall im Trigger-JSON verwiesen werden, wenn der Trigger den Typ „TumblingWindowTrigger“ hat.
(Hinweis: Der Zeitstempel ist standardmäßig im ISO 8601-Format.)

| Variablenname | BESCHREIBUNG |
| --- | --- |
| @trigger().outputs.windowStartTime |Start des Zeitfensters, zu dem der Trigger die Pipelineausführung planmäßig aufrufen sollte. Wenn der rollierende Fenstertrigger die Frequenz „hourly“ (stündlich) hat, ist dies die Uhrzeit am Anfang der Stunde.|
| @trigger().outputs.windowEndTime |Ende des Zeitfensters, zu dem der Trigger die Pipelineausführung planmäßig aufrufen sollte. Wenn der rollierende Fenstertrigger die Frequenz „hourly“ (stündlich) hat, ist dies die Uhrzeit am Ende der Stunde.|
## <a name="next-steps"></a>Nächste Schritte
Informationen darüber, wie diese Variablen in Ausdrücken verwendet werden, finden Sie unter [Ausdruckssprache und Funktionen](control-flow-expression-language-functions.md).
