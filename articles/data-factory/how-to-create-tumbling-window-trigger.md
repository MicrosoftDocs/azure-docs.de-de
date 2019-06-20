---
title: Erstellen von Triggern für rollierende Fenster in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie in Azure Data Factory ein Trigger erstellt wird, der eine Pipeline gemäß einem rollierenden Fenster ausführt.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: shlo
ms.openlocfilehash: 6fbdee71ab1123c258a5191a78e38f51eb41cbab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152933"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Erstellen eines Triggers zum Ausführen einer Pipeline für ein rollierendes Fenster
Dieser Artikel enthält die Schritte zum Erstellen, Starten und Überwachen eines Triggers für rollierende Fenster. Allgemeine Informationen zu Triggern und unterstützten Triggertypen finden Sie unter [Pipelineausführung und Trigger in Azure Data Factory](concepts-pipeline-execution-triggers.md).

Trigger für ein rollierendes Fenster werden ab einem angegebenen Startzeitpunkt in regelmäßigen Zeitintervallen ausgelöst, während der Zustand beibehalten wird. Bei rollierenden Fenstern handelt es sich um eine Reihe von nicht überlappenden, aneinandergrenzenden Zeitintervallen mit einer festen Größe. Ein Trigger für ein rollierendes Fenster hat eine 1:1-Beziehung zu einer Pipeline und kann nur auf eine einzelne Pipeline verweisen.

## <a name="data-factory-ui"></a>Data Factory-Benutzeroberfläche

Wählen Sie zum Erstellen eines Triggers für rollierende Fenster im Azure-Portal **Trigger > Rollierendes Fenster > Weiter** aus, und konfigurieren Sie dann die Eigenschaften, um das rollierende Fenster zu definieren.

![Erstellen eines Triggers für ein rollierendes Fenster im Azure-Portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Triggertypeigenschaften eines rollierenden Fensters
Ein rollierendes Fenster weist die folgenden Triggertypeigenschaften auf:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

Die folgende Tabelle enthält eine allgemeine Übersicht über die wichtigsten JSON-Elemente im Zusammenhang mit der Wiederholung und Zeitplanung eines Triggers für rollierende Fenster:

| JSON-Element | BESCHREIBUNG | Type | Zulässige Werte | Erforderlich |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Typ des Triggers. Der Typ ist der feste Wert „TumblingWindowTrigger“. | String | „TumblingWindowTrigger“ | Ja |
| **runtimeState** | Der aktuelle Status der Triggerausführungszeit.<br/>**Hinweis**: Dieses Element ist \<readOnly>. | String | „Started“, „Stopped“, „Disabled“ | Ja |
| **frequency** | Eine Zeichenfolge für die Einheit der Häufigkeit (Minuten oder Stunden), mit der der Trigger wiederholt wird. Wenn die **startTime**-Datumswerte granularer sind als der **frequency**-Wert, werden die **startTime**-Datumsangaben bei der Berechnung der Fenstergrenzen berücksichtigt. Beispiel: Wenn der **frequency**-Wert stündlich ist und der **startTime**-Wert „2017-09-01T10:10:10Z“ lautet, ist das erste Fenster „(2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z)“. | String | „minute“, „hour“  | Ja |
| **interval** | Eine positive ganze Zahl, die das Intervall für den **frequency**-Wert angibt, der bestimmt, wie oft der Trigger ausgeführt wird. Ist **interval** also beispielsweise auf „3“ und **frequency** auf „hour“ festgelegt, wird der Trigger alle drei Stunden ausgeführt. | Integer | Eine positive ganze Zahl | Ja |
| **startTime**| Das erste Vorkommen, das in der Vergangenheit liegen kann. Das erste Triggerintervall ist (**startTime**, **startTime** + **interval**). | DateTime | Ein DateTime-Wert | Ja |
| **endTime**| Das letzte Vorkommen, das in der Vergangenheit liegen kann. | DateTime | Ein DateTime-Wert | Ja |
| **delay** | Der Zeitraum, in dem der Beginn der Datenverarbeitung für das Fenster verzögert wird. Die Ausführung der Pipeline wird nach der erwarteten Ausführungszeit sowie dem **delay**-Wert gestartet. **delay** legt fest, wie lange der Trigger nach Ablauf der fälligen Zeit wartet, bevor er eine neue Ausführung auslöst. Bei **delay** wird nicht das Fenster **startTime** geändert. Ein **delay**-Wert von 00:10:00 impliziert beispielsweise eine Verzögerung von 10 Minuten. | Timespan<br/>(hh:mm:ss)  | Ein Zeitraumwert, wobei der Standardwert 00:00:00 ist. | Nein |
| **maxConcurrency** | Die Anzahl der gleichzeitigen Triggerausführungen, die für bereite Fenster ausgelöst werden. Dies gilt beispielsweise für das Abgleichen stündlicher Ausführungen für die gestrigen Ergebnisse in 24 Fenstern. Wenn **maxConcurrency** = 10, werden Triggerereignisse nur für die ersten 10 Fenster ausgelöst (00:00-01:00 – 09:00-10:00). Nachdem die ersten 10 ausgelösten Pipelineausführungen erfolgt sind, werden Triggerausführungen für die nächsten 10 Fenster (10:00-11:00 – 19:00-20:00) ausgelöst. Wenn Sie mit dem Beispiel **maxConcurrency** = 10 fortfahren, erfolgen 10 Pipelineausführungen, sobald 10 Fenster bereit sind. Wenn nur ein Fenster bereit ist, erfolgt nur eine Pipelineausführung. | Integer | Eine ganze Zahl zwischen 1 und 50. | Ja |
| **retryPolicy: Count** | Die Anzahl der Wiederholungen, bevor die Ausführung der Pipeline als „Failed“ markiert wird  | Integer | Eine ganze Zahl, wobei der Standardwert 0 ist (keine Wiederholungen) | Nein |
| **retryPolicy: intervalInSeconds** | Die Verzögerung zwischen den in Sekunden angegebenen Wiederholungsversuchen | Integer | Die Anzahl der Sekunden, wobei der Standardwert 30 ist | Nein |

### <a name="windowstart-and-windowend-system-variables"></a>Systemvariablen „WindowStart“ und „WindowEnd“

Sie können die Systemvariablen **WindowStart** und **WindowEnd** des Triggers für rollierende Fenster in Ihrer **Pipeline**-Definition (d.h. für einen Teil einer Abfrage) verwenden. Übergeben Sie die Systemvariablen als Parameter an Ihre Pipeline in der **Trigger**-Definition. Im folgenden Beispiel wird gezeigt, wie diese Variablen als Parameter übergeben werden:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Um die Systemvariablen **WindowStart** und **WindowEnd** in der Pipelinedefinition zu nutzen, verwenden Sie die Parameter „MyWindowStart“ und „MyWindowEnd“ entsprechend.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ausführungsreihenfolge von Fenstern in einem Abgleichsszenario
Wenn mehrere Fenster zur Ausführung anstehen (insbesondere in einem Abgleichsszenario), ist die Reihenfolge der Ausführung von Fenstern deterministisch (von den ältesten bis zu den neuesten Intervallen). Dieses Verhalten kann derzeit nicht geändert werden.

### <a name="existing-triggerresource-elements"></a>Vorhandene TriggerResource-Elemente
Die folgenden Punkte gelten für vorhandene **TriggerResource**-Elemente:

* Wenn der Wert für das **frequency**-Element (oder die Fenstergröße) des Triggers geändert wird, wird der Status der bereits verarbeiteten Fenster *nicht* zurückgesetzt. Der Trigger setzt das Auslösen für die Fenster ab dem letzten ausgeführten Fenster mit der neuen Fenstergröße fort.
* Wenn der Wert für das **endTime**-Element des Triggers geändert wird (durch Hinzufügen oder Aktualisieren), wird der Status der bereits verarbeiteten Fenster *nicht* zurückgesetzt. Der Trigger berücksichtigt den neuen **endTime**-Wert. Wenn der neue **endTime**-Wert vor den bereits ausgeführten Fenstern liegt, wird der Trigger angehalten. Andernfalls wird der Trigger angehalten, sobald der neue **endTime**-Wert erreicht wird.

## <a name="sample-for-azure-powershell"></a>Beispiel für Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In diesem Abschnitt erfahren Sie, wie Sie mit Azure PowerShell einen Trigger erstellen, starten und überwachen.

1. Erstellen Sie im Ordner „C:\ADFv2QuickStartPSH“ eine JSON-Datei mit dem Namen **MyTrigger.json** und dem folgenden Inhalt:

    > [!IMPORTANT]
    > Legen Sie vor dem Speichern der JSON-Datei den Wert des **startTime**-Elements auf die aktuelle UTC-Zeit fest. Legen Sie den Wert des **endTime**-Elements auf eine Stunde nach der aktuellen UTC-Zeit fest.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Erstellen Sie mit dem Cmdlet **Set-AzDataFactoryV2Trigger** einen Trigger:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Vergewissern Sie sich, dass der Status des Triggers **Beendet** lautet, indem Sie das Cmdlet **Get-AzDataFactoryV2Trigger** verwenden:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Starten Sie den Trigger mithilfe des Cmdlets **Start-AzDataFactoryV2Trigger**:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Vergewissern Sie sich, dass der Status des Triggers **Gestartet** lautet, indem Sie das Cmdlet **Get-AzDataFactoryV2Trigger** verwenden:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Rufen Sie Triggerausführungen in Azure PowerShell mit dem Cmdlet **Get-AzDataFactoryV2TriggerRun** ab. Führen Sie in regelmäßigen Abständen den folgenden Befehl aus, um die Informationen zu den Triggerausführungen abzurufen. Aktualisieren Sie die Werte **TriggerRunStartedAfter** und **TriggerRunStartedBefore** entsprechend den Werten in Ihrer Triggerdefinition:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Informationen zum Überwachen von Trigger- bzw. Pipelineausführungen im Azure-Portal finden Sie unter [Überwachen der Pipelineausführungen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Nächste Schritte
Detaillierte Informationen zu Triggern finden Sie unter [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md#triggers).
