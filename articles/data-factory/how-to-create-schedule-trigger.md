---
title: Erstellen von Zeitplantriggern in Azure Data Factory
description: Erfahren Sie, wie in Azure Data Factory ein Trigger erstellt wird, der eine Pipeline gemäß einem Zeitplan ausführt.
services: data-factory
documentationcenter: ''
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: a6f53d6ce41085b2348857ccb5b45c06132d6a99
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001982"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Erstellen eines Triggers zum Ausführen einer Pipeline gemäß einem Zeitplan
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Informationen zum Zeitplantrigger und den Schritten zum Erstellen, Starten und Überwachen eines Zeitplantriggers. Informationen zu anderen Triggertypen finden Sie unter [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md).

Geben Sie bei der Erstellung eines Zeitplantriggers einen Zeitplan (Startdatum, Wiederholung, Enddatum usw.) für den Trigger an, und ordnen Sie diesem eine Pipeline zu. Pipelines und Trigger haben eine m:m-Beziehung. Mehrere Trigger können eine einzelne Pipeline starten. Ein einzelnder Trigger kann mehrere Pipelines starten.

In den folgenden Abschnitten werden die Schritte zum Erstellen eines Zeitplantriggers durch unterschiedliche Methoden erläutert. 

## <a name="data-factory-ui"></a>Data Factory-Benutzeroberfläche
Sie können einen **Zeitplantrigger** erstellen, um eine regelmäßige Ausführung der Pipeline (stündlich, täglich usw.) festzulegen. 

> [!NOTE]
> Eine vollständige exemplarische Vorgehensweise zum Erstellen einer Pipeline und eines Zeitplantriggers, worin der Trigger der Pipeline zugeordnet sowie die Pipeline ausgeführt und überwacht wird, finden Sie im [Schnellstart: Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md).

1. Wechseln Sie zur Registerkarte **Bearbeiten**, die mit einem Stiftsymbol angezeigt wird. 

    ![Wechseln zur Registerkarte „Bearbeiten“](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. Wählen Sie im Menü **Trigger** und dann **Neu/Bearbeiten** aus. 

    ![Menü „Neuer Trigger“](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. Wählen Sie auf der Seite **Trigger hinzufügen** die Option **Trigger auswählen...** und dann **+Neu** aus. 

    ![Hinzufügen von Triggern – neuer Trigger](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. Führen Sie auf der Seite **Neuer Trigger** die folgenden Schritte aus: 

    1. Überprüfen Sie, ob **Zeitplan** als **Typ** ausgewählt ist.
    1. Geben Sie als **Startdatum** den Startzeitpunkt des Triggers an. Dieser Wert ist standardmäßig auf den aktuellen datetime-Wert in UTC (koordinierte Weltzeit) festgelegt.
    1. Geben Sie die Zeitzone an, in der der Trigger erstellt wird. Die Zeitzoneneinstellung gilt für **Startdatum**, **Enddatum** und **Ausführungszeiten planen** in den erweiterten Wiederholungsoptionen. Durch eine Änderung der Zeitzoneneinstellung wird das Startdatum nicht automatisch geändert. Stellen Sie sicher, dass das Startdatum in der angegebenen Zeitzone korrekt ist. Beachten Sie, dass vorausgesetzt wird, dass die geplante Ausführungszeit des Auslösers nach dem Startdatum liegt (stellen Sie sicher, dass das Startdatum mindestens eine Minute vor der Ausführungszeit liegt, sonst wird die Pipeline bei der nächsten Wiederholung ausgelöst). 

        > [!NOTE]
        > Bei Zeitzonen mit Umstellung zwischen Sommerzeit und Winterzeit wird die Triggerzeit automatisch zweimal im Jahr angepasst. Um die Anpassung an Sommerzeit/Winterzeit zu deaktivieren, wählen Sie eine Zeitzone aus, in der diese Umstellung nicht erfolgt, z. B. UTC.

    1. Legen Sie für den Trigger **Wiederholen** fest. Wählen Sie einen Wert aus der Dropdownliste aus („Minütlich“, „Stündlich“, „Täglich“, „Wöchentlich“ und „Monatlich“). Geben Sie den Multiplikator in das Textfeld ein. Beispiel: Wenn der Trigger einmal alle 15 Minuten ausgeführt werden soll, wählen Sie **Minütlich** aus, und geben Sie **15** in das Textfeld ein. 
    1. Zum Angeben eines Endzeitpunkts wählen Sie **Enddatum festlegen** aus, legen einen Wert für _Endet am_ fest und klicken dann auf **OK**. Für jede Pipelineausführung fallen Gebühren an. Wenn Sie Tests durchführen, sollten Sie darauf achten, dass die Pipeline nur ein paar Mal ausgelöst wird. Stellen Sie jedoch sicher, dass zwischen Veröffentlichungszeit und Endzeit ausreichend Zeit für die Pipelineausführung bleibt. Der Trigger wird erst wirksam, nachdem Sie die Lösung in Data Factory veröffentlicht haben, nicht beim Speichern des Triggers auf der Benutzeroberfläche.

        ![Triggereinstellungen](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![Triggereinstellungen für Enddatum](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. Wählen Sie im Fenster **Neuer Trigger** in der Option **Aktiviert** die Schaltfläche **Ja** und dann **OK** aus. Über dieses Kontrollkästchen können Sie den Trigger später deaktivieren. 

    ![Triggereinstellungen – Schaltfläche „Weiter“](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. Überprüfen Sie im Fenster **Neuer Auslöser** die Warnmeldung, und wählen Sie **OK** aus.

    ![Triggereinstellungen – Schaltfläche „Fertig stellen“](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. Wählen Sie **Alle veröffentlichen** aus, um die Änderungen in Data Factory zu veröffentlichen. Der Trigger startet die Pipelineausführung erst, wenn die Änderungen in Data Factory veröffentlicht wurden. 

    ![Schaltfläche "Veröffentlichen"](./media/how-to-create-schedule-trigger/publish-2.png)

1. Wechseln Sie auf der linken Seite zur Registerkarte **Pipelineausführung**, und wählen Sie **Aktualisieren** aus, um die Liste zu aktualisieren. Dann werden die Pipelineausführungen angezeigt, die vom geplanten Trigger ausgelöst wurden. Beachten Sie die Werte in der Spalte **Ausgelöst durch**. Wenn Sie die Option **Jetzt auslösen** verwenden, wird die manuelle Triggerausführung in der Liste angezeigt. 

    ![Überwachen ausgelöster Ausführungen](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. Wechseln Sie zur Ansicht **Triggerausführungen** \ **Zeitplan**. 

    ![Überwachen von Triggerausführungen](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In diesem Abschnitt erfahren Sie, wie Sie mit Azure PowerShell einen Zeitplantrigger erstellen, starten und überwachen. Damit dieses Beispiel funktioniert, lesen Sie zuerst [Schnellstart: Erstellen einer Data Factory mit Azure PowerShell](quickstart-create-data-factory-powershell.md). Fügen Sie anschließend den folgenden Code zur „main“-Methode hinzu, der einen Zeitplantrigger erstellt und startet, der alle 15 Minuten ausgeführt wird. Der Trigger ist einer Pipeline namens **Adfv2QuickStartPipeline** zugeordnet, die Sie als Teil des Schnellstarts erstellen.

1. Erstellen Sie im Ordner „C:\ADFv2QuickStartPSH“ eine JSON-Datei mit dem Namen **MyTrigger.json** und dem folgenden Inhalt:

    > [!IMPORTANT]
    > Legen Sie vor dem Speichern der JSON-Datei den Wert des **startTime**-Elements auf die aktuelle UTC-Zeit fest. Legen Sie den Wert des **endTime**-Elements auf eine Stunde nach der aktuellen UTC-Zeit fest.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    Im JSON-Codeausschnitt:
    - Das **type**-Element des Triggers wird auf „ScheduleTrigger“ festgelegt.
    - Das **frequency**-Element wird auf „Minute“ und das **interval**-Element auf „15“ festgelegt. So führt der Trigger die Pipeline zwischen dem Start- und Endzeitpunkt alle 15 Minuten aus.
    - Das **timeZone**-Element gibt die Zeitzone an, in der der Trigger erstellt wird. Diese Einstellung betrifft sowohl **startTime** als auch **endTime**.
    - Das **endTime**-Element ist auf eine Stunde nach dem Wert des **startTime**-Elements festgelegt. So führt der Trigger die Pipeline 15 Minuten, 30 Minuten und 45 Minuten nach der Startzeit aus. Denken Sie daran, die Startzeit in die aktuelle UTC-Zeit und die Endzeit in eine Stunde nach der Startzeit zu ändern. 

        > [!IMPORTANT]
        > In der Zeitzone UTC müssen startTime und endTime das Format „yyyy-MM-ddTHH:mm:ss **Z**“ aufweisen, in anderen Zeitzonen ist das Format „yyyy-MM-ddTHH:mm:ss“ erforderlich. 
        > 
        > Gemäß ISO 8601-Norm markiert das Suffix _Z_ des Zeitstempels den datetime-Wert für die Zeitzone UTC, und damit ist das timeZone-Feld nutzlos. Wenn das Suffix _Z_ für UTC fehlt, tritt bei der _Aktivierung_ des Triggers ein Fehler auf.

    - Der Trigger ist der Pipeline **Adfv2QuickStartPipeline** zugeordnet. Um einen Trigger mehreren Pipelines zuzuordnen, fügen Sie weitere **pipelineReference**-Abschnitte hinzu.
    - Die Pipeline im Schnellstart akzeptiert zwei **parameter**-Werte: **inputPath** und **outputPath**. Sie übergeben Werte für diese Parameter aus dem Trigger.

1. Erstellen Sie mit dem Cmdlet **Set-AzDataFactoryV2Trigger** einen Trigger:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. Vergewissern Sie sich, dass der Status des Triggers **Beendet** lautet, indem Sie das Cmdlet **Get-AzDataFactoryV2Trigger** verwenden:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Starten Sie den Trigger mithilfe des Cmdlets **Start-AzDataFactoryV2Trigger**:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Vergewissern Sie sich, dass der Status des Triggers **Gestartet** lautet, indem Sie das Cmdlet **Get-AzDataFactoryV2Trigger** verwenden:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  Rufen Sie Triggerausführungen in Azure PowerShell mit dem Cmdlet **Get-AzDataFactoryV2TriggerRun** ab. Führen Sie in regelmäßigen Abständen den folgenden Befehl aus, um die Informationen zu den Triggerausführungen abzurufen. Aktualisieren Sie die Werte **TriggerRunStartedAfter** und **TriggerRunStartedBefore** entsprechend den Werten in Ihrer Triggerdefinition:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > Die Auslösezeiten für Zeitplantrigger sind im UTC-Zeitstempel angegeben. _TriggerRunStartedAfter_ und _TriggerRunStartedBefore_ erwarten ebenfalls einen UTC-Zeitstempel.

    Informationen zum Überwachen von Trigger- bzw. Pipelineausführungen im Azure-Portal finden Sie unter [Überwachen der Pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).



## <a name="net-sdk"></a>.NET SDK
In diesem Abschnitt erfahren Sie, wie Sie mit dem .NET SDK einen Trigger erstellen, starten und überwachen. Damit dieses Beispiel funktioniert, lesen Sie zuerst [Schnellstart: Erstellen einer Data Factory mit dem .NET SDK](quickstart-create-data-factory-dot-net.md). Fügen Sie anschließend den folgenden Code zur „main“-Methode hinzu, der einen Zeitplantrigger erstellt und startet, der alle 15 Minuten ausgeführt wird. Der Trigger ist einer Pipeline namens **Adfv2QuickStartPipeline** zugeordnet, die Sie als Teil des Schnellstarts erstellen.

Um einen Zeitplantrigger zu erstellen und zu starten, der alle 15 Minuten ausgeführt wird, fügen Sie den folgenden Code zur main-Methode hinzu:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Zum Erstellen von Triggern in einer anderen Zeitzone als UTC sind folgende Einstellungen erforderlich:
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

Fügen Sie zum Überwachen einer Triggerausführung im Beispiel den folgenden Code vor der letzten `Console.WriteLine`-Anweisung hinzu:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Informationen zum Überwachen von Trigger- bzw. Pipelineausführungen im Azure-Portal finden Sie unter [Überwachen der Pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
In diesem Abschnitt erfahren Sie, wie Sie mit dem Python-SDK einen Trigger erstellen, starten und überwachen. Damit dieses Beispiel funktioniert, lesen Sie zuerst [Schnellstart: Erstellen einer Data Factory mit dem Python SDK](quickstart-create-data-factory-python.md). Fügen Sie dann im Python-Skript den folgenden Codeblock nach dem Codeblock „monitor the pipeline run“ hinzu. Dieser Code erzeugt einen Zeitplantrigger, der alle 15 Minuten zwischen den angegebenen Start- und Endzeiten ausgeführt wird. Ändern Sie die Variable **start_time** in die aktuelle UTC-Zeit und die Variable **end_time** in eine Stunde nach der aktuellen UTC-Zeit.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Informationen zum Überwachen von Trigger- bzw. Pipelineausführungen im Azure-Portal finden Sie unter [Überwachen der Pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage
Sie können mit einer Azure Resource Manager-Vorlage einen Trigger erstellen. Detaillierte Anweisungen finden Sie unter [Tutorial: Erstellen einer Azure Data Factory mit einer Azure Resource Manager-Vorlage](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Übergeben der Startzeit des Triggers an eine Pipeline
In Version 1 unterstützt Azure Data Factory das Lesen oder Schreiben von partitionierten Daten mithilfe dieser Systemvariablen: **SliceStart**, **SliceEnd**, **WindowStart** und **WindowEnd**. In der aktuellen Version von Azure Data Factory können Sie dieses Verhalten anhand eines Pipelineparameters erreichen. Die Startzeit und die geplante Zeit für den Trigger werden als Wert für den Pipelineparameter festgelegt. Im folgenden Beispiel wird die geplante Zeit für den Trigger als Wert an den Pipelineparameter **scheduledRunTime** übergeben:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON-Schema
Die folgende JSON-Definition zeigt, wie Sie einen Zeitplantrigger mit Planung und Wiederholung erstellen können:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  Die **parameters**-Eigenschaft ist eine erforderliche Eigenschaft des **pipelines**-Elements. Wenn für Ihre Pipeline keine Parameter verwendet werden, müssen Sie eine leere JSON-Definition für die **parameters**-Eigenschaft einfügen.


### <a name="schema-overview"></a>Schemaübersicht
Die folgende Tabelle enthält eine allgemeine Übersicht über die wichtigsten Schemaelemente im Zusammenhang mit der Wiederholung und Zeitplanung eines Triggers:

| JSON-Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| **startTime** | Ein Datums-/Uhrzeitwert. Bei einfachen Zeitplänen gilt der Wert der **startTime**-Eigenschaft für das erste Vorkommen. Bei komplexen Zeitplänen wird der Trigger frühestens beim festgelegten **startTime**-Wert gestartet. <br> Für UTC lautet das Format `'yyyy-MM-ddTHH:mm:ssZ'`, für andere Zeitzonen `'yyyy-MM-ddTHH:mm:ss'`. |
| **endTime** | Enddatum und -uhrzeit für den Trigger. Der Trigger wird am angegebenen Enddatum und der Enduhrzeit beendet. Der Wert für die Eigenschaft darf nicht in der Vergangenheit liegen. Diese Eigenschaft ist optional.  <br> Für UTC lautet das Format `'yyyy-MM-ddTHH:mm:ssZ'`, für andere Zeitzonen `'yyyy-MM-ddTHH:mm:ss'`. |
| **timeZone** | Die Zeitzone, in der der Trigger erstellt wird. Diese Einstellung wirkt sich auf **startTime**, **endTime** und **Zeitplan** aus. Mehr dazu erfahren Sie in der [Liste mit unterstützten Zeitzonen](#time-zone-option). |
| **recurrence** | Ein recurrence-Objekt, das die Wiederholungsregeln für den Trigger angibt. Das recurrence-Objekt unterstützt die Elemente **frequency**, **interval**, **endTime**, **count** und **schedule**. Wenn ein recurrence-Objekt definiert ist, ist das **frequency**-Element erforderlich. Die anderen Elemente des recurrence-Objekts sind optional. |
| **frequency** | Die Einheit der Häufigkeit, mit welcher der Trigger wiederholt wird. Zu den unterstützten Werten gehören „minute“, „hour“, „day“, „week“ und „month“. |
| **interval** | Eine positive ganze Zahl, die das Intervall für den **frequency**-Wert angibt, der bestimmt, wie oft der Trigger ausgeführt wird. Ist **interval** also beispielsweise auf „3“ und **frequency** auf „week“ festgelegt, wird der Trigger alle drei Wochen ausgeführt. |
| **schedule** | Der Wiederholungszeitplan für den Trigger. Die Wiederholung eines Triggers mit einem festgelegten **frequency**-Wert wird auf der Grundlage eines Wiederholungszeitplans angepasst. Die **schedule**-Eigenschaft enthält Anpassungen für die Wiederholung auf der Grundlage von Minuten, Stunden, Wochentagen, Monatstagen und Wochennummer.

> [!IMPORTANT]
> In der Zeitzone UTC müssen startTime und endTime das Format „yyyy-MM-ddTHH:mm:ss **Z**“ aufweisen, in anderen Zeitzonen ist das Format „yyyy-MM-ddTHH:mm:ss“ erforderlich. 
> 
> Gemäß ISO 8601-Norm markiert das Suffix _Z_ des Zeitstempels den datetime-Wert für die Zeitzone UTC, und damit ist das timeZone-Feld nutzlos. Wenn das Suffix _Z_ für UTC fehlt, tritt bei der _Aktivierung_ des Triggers ein Fehler auf.

### <a name="schema-defaults-limits-and-examples"></a>Schemastandards, Einschränkungen und Beispiele

| JSON-Eigenschaft | type | Erforderlich | Standardwert | Gültige Werte | Beispiel |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | String | Ja | Keine | Datum/Uhrzeit (nach ISO 8601) | Für die Zeitzone UTC: `"startTime" : "2013-01-09T09:30:00-08:00Z"` <br> Für andere Zeitzonen: `"2013-01-09T09:30:00-08:00"` |
| **timeZone** | String | Ja | Keine | [Werte für Zeitzonen](#time-zone-option)  | `"UTC"` |
| **recurrence** | Object | Ja | Keine | Wiederholungsobjekt | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Number | Nein | 1 | 1 bis 1.000 | `"interval":10` |
| **endTime** | String | Ja | Keine | Ein Datums-/Uhrzeitwert, der eine Zeit in der Zukunft darstellt. | Für die Zeitzone UTC: `"endTime" : "2013-02-09T09:30:00-08:00Z"` <br> Für andere Zeitzonen: `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **schedule** | Object | Nein | Keine | Zeitplanobjekt | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>Zeitzonenoption

Im Folgenden finden Sie einige der für Zeitplantrigger unterstützte Zeitzonen:

| Zeitzone | Unterschied zu UTC (keine Sommerzeit) | timeZone-Wert | Beachtung der Sommerzeit | Format für Zeitstempel |
| :--- | :--- | :--- | :--- | :--- |
| Koordinierte Weltzeit (UTC) | 0 | `UTC` | Nein  | `'yyyy-MM-ddTHH:mm:ssZ'`|
| Pacific Time (PT) | -8 | `Pacific Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Central Time (CT) | –6 | `Central Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Eastern Time (ET) | -5 | `Eastern Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Greenwich Mean Time (GMT) | 0 | `GMT Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Mitteleuropäische Zeit | +1 | `W. Europe Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| India Standard Time (IST) | +5:30 | `India Standard Time` | Nein  | `'yyyy-MM-ddTHH:mm:ss'` |
| China Normalzeit | +8 | `China Standard Time` | Nein  | `'yyyy-MM-ddTHH:mm:ss'` |

Diese Liste ist unvollständig. Eine vollständige Liste der Zeitzonenoptionen finden Sie im Data Factory-Portal auf der Seite zur [Triggererstellung](#data-factory-ui).

### <a name="starttime-property"></a>startTime-Eigenschaft
Die folgende Tabelle zeigt, wie die **startTime**-Eigenschaft eine Triggerausführung steuert:

| startTime-Wert | Wiederholung ohne Zeitplan | Wiederholung mit Zeitplan |
|:--- |:--- |:--- |
| Startzeit in der Vergangenheit | Berechnet die erste zukünftige Ausführungszeit nach der Startzeit und nimmt die Ausführung zu diesem Zeitpunkt vor.<br/><br/>Berechnet weitere Ausführungen auf Grundlage der letzten Ausführungszeit.<br/><br/>Betrachten Sie das Beispiel nach dieser Tabelle. | Der Trigger startet _frühestens_ zur angegebenen Startzeit. Das erste Vorkommen basiert auf dem Zeitplan, der auf der Grundlage der Startzeit berechnet wird.<br/><br/>Berechnet weitere Ausführungen auf Grundlage des Wiederholungszeitplans. |
| Startzeit in der Zukunft oder Gegenwart | Wird einmalig zur angegebenen Startzeit ausgeführt.<br/><br/>Berechnet weitere Ausführungen auf Grundlage der letzten Ausführungszeit. | Der Trigger startet _frühestens_ zur angegebenen Startzeit. Das erste Vorkommen basiert auf dem Zeitplan, der auf der Grundlage der Startzeit berechnet wird.<br/><br/>Berechnet weitere Ausführungen auf Grundlage des Wiederholungszeitplans. |

Das folgende Beispiel veranschaulicht, was passiert, wenn die Startzeit in der Vergangenheit liegt und nur eine Wiederholung, aber kein Zeitplan angegeben ist. Nehmen Sie beispielsweise an, dass die aktuelle Uhrzeit `2017-04-08 13:00` ist, die Startzeit `2017-04-07 14:00` ist und die Wiederholung alle zwei Tage erfolgt. (Der **recurrence**-Wert wird definiert, indem die **frequency**-Eigenschaft auf „day“ und die **interval**-Eigenschaft auf „2“ festgelegt wird.) Beachten Sie, dass der **startTime**-Wert in der Vergangenheit liegt.

Unter diesen Umständen erfolgt die erste Ausführung am `2017-04-09` um `14:00`. Die Scheduler-Engine berechnet die Ausführungen auf Grundlage der Startzeit. In der Vergangenheit liegende Instanzen werden verworfen. Die Engine verwendet die nächste in der Zukunft liegende Instanz. In diesem Szenario ist die Startzeit auf den `2017-04-07` um `2:00pm` festgelegt. Die nächste Instanz folgt zwei Tage nach diesem Zeitpunkt, also am `2017-04-09` um `2:00pm`.

Die erste Ausführungszeit ist auch dieselbe, wenn der **startTime**-Wert `2017-04-05 14:00` oder `2017-04-01 14:00` ist. Nach der ersten Ausführung werden nachfolgende Ausführungen anhand des Zeitplans berechnet. Daher erfolgen die weiteren Ausführungen am `2017-04-11` um `2:00pm`, dann am `2017-04-13` um `2:00pm`, dann am `2017-04-15` um `2:00pm` und so weiter.

Wenn die Stunden oder Minuten im Zeitplan für einen Trigger nicht festgelegt sind, werden die Stunden oder Minuten der ersten Ausführung als Standardwerte verwendet.

### <a name="schedule-property"></a>schedule-Eigenschaft
Mithilfe eines Zeitplans lässt sich einerseits die Anzahl der Triggerausführungen begrenzen. Beispiel: Wenn für einen Trigger mit einer monatlichen Häufigkeit nur die Ausführung am 31. Tag geplant ist, wird der Trigger nur in Monaten mit 31 Tagen ausgeführt.

Ein Zeitplan kann dagegen auch zum Erweitern der Anzahl von Triggerausführungen verwendet werden. Beispiel: Ein Trigger mit einem monatlichen Intervall, dessen Ausführung für die Monatstage 1 und 2 geplant ist, wird an den 1. und 2. Tagen des Monats anstatt einmal im Monat ausgeführt.

Bei Angabe mehrerer **schedule**-Elemente werden diese in absteigender Reihenfolge der Zeitplaneinstellungen ausgewertet. Die Auswertung beginnt mit der Wochennummer und wird mit dem Monatstag, dem Wochentag, der Stunde und schließlich der Minute fortgesetzt.

Die folgende Tabelle enthält eine ausführliche Beschreibung der **schedule**-Elemente:


| JSON-Element | BESCHREIBUNG | Gültige Werte |
|:--- |:--- |:--- |
| **minutes** | Minuten der Stunde, zu denen der Trigger ausgeführt wird | <ul><li>Integer</li><li>Array mit ganzen Zahlen</li></ul>
| **hours** | Stunden des Tages, zu denen der Trigger ausgeführt wird | <ul><li>Integer</li><li>Array mit ganzen Zahlen</li></ul> |
| **weekDays** | Tage der Woche, an denen der Trigger ausgeführt wird. Der Wert kann nur bei wöchentlicher Häufigkeit angegeben werden. | <ul><li>Montag, Dienstag, Mittwoch, Donnerstag, Freitag, Samstag, Sonntag</li><li>Array von Tageswerten (die maximale Arraygröße ist 7)</li><li>Bei Tageswerten wird nicht zwischen Groß- und Kleinschreibung unterschieden.</li></ul> |
| **monthlyOccurrences** | Tage des Monats, an denen der Trigger ausgeführt wird. Der Wert kann nur bei monatlicher Häufigkeit angegeben werden. | <ul><li>Array aus **monthlyOccurrence**-Objekten: `{ "day": day,  "occurrence": occurrence }`.</li><li>Das **day**-Attribut ist der Tag der Woche, an dem der Trigger ausgeführt wird. Beispiel: Eine **monthlyOccurrences**-Eigenschaft mit dem **day**-Wert `{Sunday}` bedeutet jeden Sonntag des Monats. Das **day**-Attribut ist erforderlich.</li><li>Das **occurrence**-Attribut ist das Vorkommen des angegebenen **day**-Attributs innerhalb des Monats. Beispiel: Eine **monthlyOccurrences**-Eigenschaft mit dem **day**- und **occurrence**-Wert `{Sunday, -1}` bedeutet den letzten Sonntag des Monats. Das **occurrence**-Attribut ist optional.</li></ul> |
| **monthDays** | Tag des Monats, an dem der Trigger ausgeführt wird. Der Wert kann nur bei monatlicher Häufigkeit angegeben werden. | <ul><li>Beliebiger Wert, für den Folgendes gilt: <= -1 und >= -31</li><li>Beliebiger Wert, für den Folgendes gilt: >= 1 und <= 31</li><li>Array von Werten</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Beispiele für Wiederholungszeitpläne von Triggern
Dieser Abschnitt enthält verschiedene Beispiele für Wiederholungszeitpläne, bei denen jeweils das **schedule**-Objekt und die dazugehörigen Elemente im Mittelpunkt stehen.

In den Beispielen wird angenommen, dass der **interval**-Wert „1“ festgelegt ist, und der **frequency**-Wert gemäß der Zeitplandefinition richtig ist. Beispielsweise können nicht gleichzeitig der **frequency**-Wert „day“ und die Änderung „monthDays“ im **schedule**-Objekt angegeben werden. Solche Einschränkungen werden in der Tabelle im vorhergehenden Abschnitt erwähnt.

| Beispiel | BESCHREIBUNG |
|:--- |:--- |
| `{"hours":[5]}` | Ausführung täglich um 05:00 Uhr. |
| `{"minutes":[15], "hours":[5]}` | Ausführung täglich um 05:15 Uhr. |
| `{"minutes":[15], "hours":[5,17]}` | Ausführung täglich um 05:15 und 17:15 Uhr. |
| `{"minutes":[15,45], "hours":[5,17]}` | Ausführung täglich um 05:15 Uhr, 05:45 Uhr, 17:15 Uhr und 17:45 Uhr. |
| `{"minutes":[0,15,30,45]}` | Ausführung alle 15 Minuten. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Stündliche Ausführung. Dieser Trigger wird stündlich ausgeführt. Die Minuten werden vom **startTime**-Wert gesteuert, wenn ein Wert angegeben ist. Wenn kein Wert angegeben ist, werden die Minuten von der Erstellungszeit gesteuert. Lautet die Start- oder Erstellungszeit also beispielsweise 12:25 Uhr, wird der Trigger um 00:25 Uhr, 01:25 Uhr, 02:25 Uhr etc. und um 23:25 Uhr ausgeführt.<br/><br/>Dieser Zeitplan entspricht einem Trigger mit dem **frequency**-Wert „hour“, dem **interval**-Wert „1“ und ohne **schedule**-Wert.  Dieser Zeitplan kann mit anderen Werten für **frequency** und **interval** auch zur Erstellung anderer Trigger verwendet werden. Beispiel: Mit dem **frequency**-Wert „month“ wird der Zeitplan nur einmal im Monat ausgeführt statt wie beim **frequency**-Wert „day“ jeden Tag. |
| `{"minutes":[0]}` | Ausführung jeweils zur vollen Stunde. Dieser Trigger wird immer zur vollen Stunde ab 00:00 Uhr (dann 01:00 Uhr, 02:00 Uhr und so weiter) ausgeführt.<br/><br/>Dieser Zeitplan entspricht einem Trigger mit dem **frequency**-Wert „hour“ und einem **startTime**-Wert von null Minuten bzw. ohne **schedule**-Wert, aber mit dem **frequency**-Wert „day“. Wenn der **frequency**-Wert „week“ oder „month“ ist, wird der Zeitplan nur an einem Tag in der Woche bzw. im Monat ausgeführt. |
| `{"minutes":[15]}` | Stündliche Ausführung jeweils 15 Minuten nach der vollen Stunde. Dieser Trigger wird stündlich immer 15 Minuten nach der vollen Stunde ab 00:15 Uhr, dann 01:15 Uhr, 02:15 Uhr und so weiter ausgeführt und endet um 23:15 Uhr. |
| `{"hours":[17], "weekDays":["saturday"]}` | Wöchentliche Ausführung, immer samstags um 17:00 Uhr. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 17:00 Uhr. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 17:15 und 17:45 Uhr. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Ausführung an Wochentagen im 15-Minuten-Takt. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Ausführung an Wochentagen zwischen 09:00 Uhr und 16:45 Uhr im 15-Minuten-Takt. |
| `{"weekDays":["tuesday", "thursday"]}` | Ausführung jeweils dienstags und donnerstags zur festgelegten Startzeit. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Ausführung um 06:00 Uhr am 28. Tag des Monats (beim **frequency**-Wert „month“). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Ausführung um 06:00 Uhr am letzten Tag des Monats. Zur Ausführung eines Triggers am letzten Tag eines Monats verwenden Sie „-1“ anstatt Tag 28, 29, 30 oder 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Ausführung jeweils am ersten und letzten Tag jedes Monats um 06:00 Uhr. |
| `{monthDays":[1,14]}` | Ausführung jeweils am ersten und 14. Tag jedes Monats zur festgelegten Startzeit. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Ausführung am ersten Freitag jedes Monats um 05:00 Uhr. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Ausführung am ersten Freitag jedes Monats zur festgelegten Startzeit. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Monatliche Ausführung am dritten Freitag ab Monatsende zur festgelegten Startzeit. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Ausführung am ersten und letzten Freitag jedes Monats um 05:15 Uhr. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Ausführung am ersten und letzten Freitag jedes Monats zur festgelegten Startzeit. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Ausführung am fünften Freitag jedes Monats zur festgelegten Startzeit. Wenn in einem Monat kein fünfter Freitag vorhanden ist, wird die Pipeline nicht ausgeführt, da die Ausführung ausschließlich für den fünften Freitag geplant ist. Zur Ausführung des Triggers am letzten Freitag des Monats empfiehlt sich u.U. die Verwendung von „-1“ anstelle von „5“ als **occurrence**-Wert. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Ausführung im 15-Minuten-Takt am letzten Freitag des Monats. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Ausführung um 05:15, 05:45, 17:15 und 17:45 Uhr am dritten Mittwoch jedes Monats. |


## <a name="next-steps"></a>Nächste Schritte
Detaillierte Informationen zu Triggern finden Sie unter [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md#trigger-execution).
