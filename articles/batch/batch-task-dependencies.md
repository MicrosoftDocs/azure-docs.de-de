---
title: Verwenden von Taskabhängigkeiten zum Ausführen von Tasks basierend auf dem Abschluss anderer Tasks – Azure Batch | Microsoft Docs
description: Erstellen Sie Tasks, die vom Abschluss anderer Tasks abhängig sind, um Vorgänge vom MapReduce-Typ und ähnliche Big Data-Workloads in Azure Batch zu verarbeiten.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca6918b809a9b4ede3fffb151c7fa5183ae03b47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550384"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Erstellen von Taskabhängigkeiten zum Ausführen von Tasks, die von anderen Tasks abhängen

Sie können Taskabhängigkeiten definieren, um einen Task oder eine Gruppe von Tasks erst auszuführen, nachdem ein übergeordneter Task abgeschlossen wurde. Einige Szenarios, in denen Taskabhängigkeiten nützlich sind:

* Workloads vom Typ MapReduce in der Cloud.
* Aufträge, deren Datenverarbeitungstasks als gerichteter azyklischer Graph (DAG) ausgedrückt werden können.
* Prozesse vor und nach dem Rendern, bei denen jeder Task abgeschlossen sein muss, bevor der nächste Task beginnen kann.
* Alle anderen Aufträge, in denen nachgelagerte Tasks von der Ausgabe vorgelagerter Tasks abhängen.

Mit Taskabhängigkeiten in Batch können Sie Tasks erstellen, deren Ausführung auf Computeknoten erst nach dem Abschluss von mindestens einem weiteren übergeordneten Task geplant wird. Beispielsweise können Sie einen Auftrag erstellen, der jedes Einzelbild eines 3D-Films mit separaten, parallelen Tasks rendert. Der letzte Task – der „Zusammenführungstask“ – fügt die gerenderten Einzelbilder erst dann zu einem vollständigen Film zusammen, wenn alle Einzelbilder erfolgreich gerendert wurden.

Standardmäßig werden abhängige Tasks erst für die Ausführung eingeplant, nachdem der übergeordnete Task erfolgreich abgeschlossen wurde. Sie können eine Abhängigkeitsaktion bestimmen, um das Standardverhalten zu überschreiben und Tasks auszuführen, wenn im übergeordneten Task ein Fehler auftritt. Weitere Informationen finden Sie im Abschnitt [Abhängigkeitsaktionen](#dependency-actions).  

Sie können Tasks erstellen, die in einer 1:1- oder 1:n-Beziehung von anderen Tasks abhängen. Sie können auch eine Bereichsabhängigkeit erstellen, bei der ein Task vom Abschluss einer Gruppe von Tasks innerhalb eines bestimmten Task-ID-Bereichs abhängig ist. Sie können diese drei grundlegenden Szenarien auch kombinieren, um m:n-Beziehungen zu erstellen.

## <a name="task-dependencies-with-batch-net"></a>Taskabhängigkeiten bei Batch .NET
In diesem Artikel wird beschrieben, wie Sie Taskabhängigkeiten mit der [.NET-Bibliothek für Batch][net_msdn] konfigurieren. Zuerst zeigen wir Ihnen, wie Sie die [Taskabhängigkeit in Ihren Aufträgen aktivieren](#enable-task-dependencies), danach erläutern wir, wie Sie [einen Task mit Abhängigkeiten konfigurieren](#create-dependent-tasks). Es wird auch beschrieben, wie Sie eine Abhängigkeitsaktion bestimmen, um abhängige Tasks auszuführen, wenn im übergeordneten Task ein Fehler auftritt. Zuletzt geht es um die von Batch unterstützten [Abhängigkeitsszenarien](#dependency-scenarios) .

## <a name="enable-task-dependencies"></a>Aktivieren von Taskabhängigkeiten
Um Taskabhängigkeiten in der Batch-Anwendung verwenden zu können, müssen Sie zuerst den Auftrag für die Verwendung von Taskabhängigkeiten konfigurieren. Führen Sie die Aktivierung in Batch .NET für Ihr [CloudJob][net_cloudjob]-Objekt durch, indem Sie die dazugehörige [UsesTaskDependencies][net_usestaskdependencies]-Eigenschaft auf `true` festlegen:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Im obigen Codeausschnitt ist „batchClient“ eine Instanz der [BatchClient][net_batchclient]-Klasse.

## <a name="create-dependent-tasks"></a>Erstellen von abhängigen Tasks
Zum Erstellen eines Tasks, der vom Abschluss eines oder mehrerer übergeordneter Tasks abhängig ist, können Sie angeben, dass der Task von anderen Tasks abhängig ist („depends on“). Konfigurieren Sie in Batch .NET die [CloudTask][net_cloudtask].[DependsOn][net_dependson]-Eigenschaft mit einer Instanz der [TaskDependencies][net_taskdependencies]-Klasse:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Dieser Codeausschnitt erstellt einen abhängigen Task mit der Task-ID „Flowers“. Der Task „Flowers“ ist von den Tasks „Rain“ und „Sun“ abhängig. Der Task „Flowers“ wird erst für die Ausführung auf einem Computeknoten eingeplant, nachdem die Tasks „Rain“ und „Sun“ erfolgreich abgeschlossen wurden.

> [!NOTE]
> Ein Task wird als erfolgreich abgeschlossen angesehen, wenn er den Zustand **abgeschlossen** aufweist und der **Exitcode** den Wert `0` hat. In Batch .NET bedeutet dies, dass der [CloudTask][net_cloudtask].[State][net_taskstate]-Eigenschaftswert `Completed` und der [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode]-Eigenschaftswert von „CloudTask“ `0` lautet. Informationen zum Ändern dieses Verhaltens finden Sie im Abschnitt [Abhängigkeitsaktionen](#dependency-actions).
> 
> 

## <a name="dependency-scenarios"></a>Abhängigkeitsszenarien
Es gibt drei grundlegende Szenarien für Abhängigkeiten von Tasks, die Sie in Azure Batch verwenden können: 1:1, 1:n und Task-ID-Bereich. Diese können Sie kombinieren, um ein viertes Szenario zu schaffen, und zwar „m:n“.

| Szenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Beispiel |  |
|:---:| --- | --- |
|  [1:1](#one-to-one) |*TaskB* hängt von *TaskA* ab. <p/> *TaskB* wird erst ausgeführt, nachdem *TaskA* erfolgreich abgeschlossen wurde |![Diagramm: 1:1-Abhängigkeit von Tasks][1] |
|  [1:n](#one-to-many) |*TaskC* hängt sowohl von *TaskA* als auch von *TaskB* ab. <p/> *TaskC* wird erst ausgeführt, nachdem *TaskA* und *TaskB* erfolgreich abgeschlossen wurden. |![Diagramm: 1:n-Abhängigkeit von Tasks][2] |
|  [Task-ID-Bereich](#task-id-range) |*TaskD* hängt von einem Taskbereich ab. <p/> *TaskD* wird erst ausgeführt, nachdem die Tasks mit den IDs *1* bis *10* erfolgreich abgeschlossen wurden. |![Diagramm: Abhängigkeit vom Task-ID-Bereich][3] |

> [!TIP]
> Sie können **m:n**-Beziehungen erstellen, bei denen beispielsweise die Tasks C, D, E und F jeweils von den Tasks A und B abhängen. Dies ist beispielsweise für Fälle mit parallelisierter Vorverarbeitung nützlich, in denen Ihre nachgelagerten Tasks von der Ausgabe mehrerer vorgelagerter Tasks abhängig sind.
> 
> In den Beispielen in diesem Abschnitt wird ein abhängiger Task erst ausgeführt, nachdem die übergeordneten Tasks erfolgreich abgeschlossen wurden. Dieses Verhalten ist das Standardverhalten für einen abhängigen Task. Sie können einen abhängigen Task ausführen, wenn im übergeordneten Task ein Fehler auftritt, indem Sie eine Abhängigkeitsaktion angeben, um das Standardverhalten zu überschreiben. Weitere Informationen finden Sie im Abschnitt [Abhängigkeitsaktionen](#dependency-actions).

### <a name="one-to-one"></a>1:1
In einer 1 : 1-Beziehung hängt ein Task vom erfolgreichen Abschluss eines übergeordneten Tasks ab. Um die Abhängigkeit zu erstellen, geben Sie eine einzelne Task-ID für die statische [TaskDependencies][net_taskdependencies].[ OnId][net_onid]-Methode an, wenn Sie die [DependsOn][net_dependson]-Eigenschaft von [CloudTask][net_cloudtask] ausfüllen.

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>1:n
In einer 1 : n-Beziehung hängt ein Task vom Abschluss mehrerer übergeordneter Tasks ab. Um die Abhängigkeit zu erstellen, geben Sie mehrere Task-IDs für die statische [TaskDependencies][net_taskdependencies].[ OnIds][net_onids]-Methode an, wenn Sie die [DependsOn][net_dependson]-Eigenschaft von [CloudTask][net_cloudtask] ausfüllen.

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Task-ID-Bereich
Bei einer Abhängigkeit von einem Bereich von übergeordneten Tasks hängt ein Task vom Abschluss von Tasks ab, deren IDs innerhalb eines Bereichs liegen.
Um die Abhängigkeit zu erstellen, geben Sie die IDs des ersten und letzten Tasks in dem Bereich für die statische [TaskDependencies][net_taskdependencies].[ OnIdRange][net_onidrange]-Methode an, wenn Sie die [DependsOn][net_dependson]-Eigenschaft von [CloudTask][net_cloudtask] ausfüllen.

> [!IMPORTANT]
> Wenn Sie Task-ID-Bereiche für Ihre Abhängigkeiten verwenden, werden nur Tasks mit IDs, die Integerwerte darstellen, durch den Bereich ausgewählt. Der Bereich `1..10` wählt somit die Aufgaben `3` und `7` aus, aber nicht `5flamingoes`. 
> 
> Führende Nullen haben für die Auswertung von Bereichsabhängigkeiten keine Bedeutung. Tasks mit den Zeichenfolgenbezeichnern `4`, `04` und `004` befinden sich daher alle *im* Bereich und werden alle als Task `4` behandelt, sodass der erste abgeschlossene Task die Abhängigkeit erfüllt.
> 
> Jeder Task in dem Bereich muss die Abhängigkeit erfüllen. Dies kann durch erfolgreichen Abschluss geschehen oder durch Abschließen mit einem Fehler, der einer Abhängigkeitsaktion zugeordnet ist, die auf **Satisfy** (Erfüllen) festgelegt ist. Weitere Informationen finden Sie im Abschnitt [Abhängigkeitsaktionen](#dependency-actions).
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Abhängigkeitsaktionen

Standardmäßig werden ein abhängiger Task oder eine Gruppe von Tasks erst ausgeführt, nachdem ein übergeordneter Task erfolgreich abgeschlossen wurde. In einigen Szenarios möchten Sie abhängige Tasks eventuell auch ausführen, wenn im übergeordneten Task ein Fehler auftritt. Sie können das Standardverhalten überschreiben, indem Sie eine Abhängigkeitsaktion angeben. Eine Abhängigkeitsaktion gibt an, ob ein abhängiger Task basierend auf Erfolg oder Misserfolg des übergeordneten Tasks ausgeführt werden darf. 

Nehmen wir beispielsweise an, dass ein abhängiger Task auf Daten vom Abschluss des Upstreamtasks wartet. Wenn der Upstreamtask fehlschlägt, kann der abhängige Task möglicherweise mit älteren Daten ausgeführt werden. In diesem Fall kann eine Abhängigkeitsaktion angeben, dass der abhängige Task berechtigt ist, trotz des Fehlers im übergeordneten Task ausgeführt zu werden.

Eine Abhängigkeitsaktion basiert auf einer Beendigungsbedingung für den übergeordneten Task. Sie können eine Abhängigkeitsaktion für jede der folgenden Beendigungsbedingungen angeben (Informationen zu .NET finden Sie unter der [ExitConditions][net_exitconditions]-Klasse):

- Wenn bei der Vorverarbeitung ein Fehler auftritt.
- Wenn beim Dateiupload ein Fehler auftritt. Wenn der Task mit einem Exitcode beendet wird, der über **ExitCodes** oder **ExitCodeRanges** angegeben wurde und dann ein Fehler beim Dateiupload auftritt, hat die durch den Exitcode angegebene Aktion Vorrang.
- Wenn der Task mit einem Exitcode beendet wird, der durch die **ExitCodes**-Eigenschaft definiert ist.
- Wenn der Task mit einem Exitcode aus einem Bereich beendet wird, der durch die **ExitCodeRanges**-Eigenschaft angegeben wird.
- Der Standardfall, wenn der Task einem nicht in **ExitCodes** oder **ExitCodeRanges** definierten Exitcode beendet wird oder wenn der Task mit einem Fehler bei der Vorverarbeitung beendet wird und die **PreProcessingError**-Eigenschaft nicht festgelegt ist oder wenn für den Task ein Fehler beim Dateiupload auftritt und die **FileUploadError**-Eigenschaft nicht festgelegt ist. 

Legen Sie zum Angeben einer Abhängigkeitsaktion in .NET die [ExitOptions][net_exitoptions].[DependencyAction][net_dependencyaction]-Eigenschaft für die Beendigungsbedingung fest. Die **DependencyAction**-Eigenschaft nimmt einen von zwei Werten an:

- Das Festlegen der **DependencyAction**-Eigenschaft auf **Satisfy** (Erfüllen) gibt an, dass abhängige Tasks ausgeführt werden dürfen, wenn der übergeordnete Task mit einem angegebenen Fehler beendet wird.
- Das Festlegen der **DependencyAction**-Eigenschaft auf **Block** (Blockieren) gibt an, dass abhängige Tasks nicht ausgeführt werden dürfen.

Die Standardeinstellung für die **DependencyAction**-Eigenschaft ist **Satisfy** (Erfüllen) für den Exitcode 0 und **Block** (Blockieren) für alle anderen Beendigungsbedingungen.

Der folgende Codeausschnitt legt die **DependencyAction**-Eigenschaft für einen übergeordneten Task fest. Wenn der übergeordnete Task mit einem Fehler bei der Vorverarbeitung oder den angegebenen Fehlercodes beendet wird, wird der abhängige Task blockiert. Wenn der übergeordnete Task mit einem anderen Fehler ungleich 0 beendet wird, darf der abhängige Task ausgeführt werden.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Codebeispiel
Das Beispielprojekt [TaskDependencies][github_taskdependencies] ist eines der [Azure Batch-Codebeispiele][github_samples] auf GitHub. Diese Visual Studio-Lösung veranschaulicht:

- Wie Sie Taskabhängigkeit für einen Auftrag aktivieren
- Wie Sie Tasks erstellen, die von anderen Tasks abhängen
- So führen Sie diese Tasks in einem Pool von Computeknoten aus

## <a name="next-steps"></a>Nächste Schritte
### <a name="application-deployment"></a>Anwendungsbereitstellung
Das Batch-Feature [Anwendungspakete](batch-application-packages.md) bietet eine einfache Möglichkeit zum Bereitstellen und Versionieren der Anwendungen, die von Ihren Tasks auf Computeknoten ausgeführt werden.

### <a name="installing-applications-and-staging-data"></a>Installieren von Anwendungen und Bereitstellen von Daten (Staging)
Der Beitrag [Installing applications and staging data on Batch compute nodes (Installieren von Anwendungen und Bereitstellen von Daten auf Batch-Computeknoten)][forum_post] im Azure Batch-Forum bietet einen Überblick über die Methoden, mit denen Knoten für die Ausführung von Tasks vorbereitet werden können. Der Beitrag wurde von einem Mitglied des Azure Batch-Teams verfasst und enthält alle relevanten Informationen, um sich mit den verschiedenen Vorgehensweisen vertraut zu machen, mit denen sich Anwendungen, Taskeingabedaten und andere Dateien auf Computeknoten kopieren lassen.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagramm: 1:1-Abhängigkeit"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagramm: 1:n-Abhängigkeit"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagramm: Abhängigkeit vom Task-ID-Bereich"
