---
title: 'Zählen der Status für Tasks und Knoten: Azure Batch | Microsoft-Dokumentation'
description: Zählen der Status von Azure Batch-Tasks und -Computeknoten zum Verwalten und Überwachen von Batch-Lösungen.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 574cdea61a474dda5d20254bfae9ff2f06044cca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775371"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Überwachen von Batch-Lösungen durch Zählen von Tasks und Knoten nach Status

Zum Überwachen und Verwalten umfangreicher Azure Batch-Lösungen benötigen Sie die genaue Anzahl von Ressourcen in verschiedenen Status. Azure Batch ermöglicht effiziente Vorgänge zum Abrufen dieser Werte für Batch-*Tasks* und *Computeknoten*. Verwenden Sie diese Vorgänge anstelle von potenziell zeitaufwendigen Listenabfragen, um ausführliche Informationen zu großen Sammlungen von Tasks oder Knoten zurückzugeben.

* [Get Task Counts][rest_get_task_counts] (Taskanzahl abrufen) ruft eine aggregierte Anzahl von aktiven, ausgeführten und abgeschlossenen Tasks in einem Auftrag und von erfolgreichen oder fehlgeschlagenen Tasks ab. 

  Dadurch, dass Sie die Anzahl von Tasks jedes Status zählen, können Sie für Benutzer leichter den Fortschrittsstatus des Auftrags anzeigen oder unerwartete Verzögerungen oder Fehler erkennen, die sich möglicherweise auf den Auftrag auswirken. Get Task Counts (Taskanzahl abrufen) ist ab Version 2017-06-01.5.1 der Batch-Dienst-API und verwandten SDKs und Tools verfügbar.

* [List Pool Node Counts][rest_get_node_counts] (Poolknotenanzahl auflisten): Ruft die Anzahl der dedizierten Computeknoten mit niedriger Priorität in jedem Pool ab, die sich in verschiedenen Status befinden: Erstellung, Im Leerlauf, Offline, Vorzeitig entfernt, Neustart wird ausgeführt, Reimaging, Wird gestartet usw. 

  Durch Zählen von Knoten in jedem Status können Sie festlegen, wann Sie über ausreichende Computeressourcen für die Ausführung Ihrer Aufträge besitzen und potentielle Probleme mit Ihren Pools identifizieren. List Pool Node Counts (Poolknotenanzahl auflisten) ist ab Version 2018-03-01.6.1 der Batch-Dienst-API und verwandten SDKs und Tools verfügbar.

Wenn Sie eine Version des Diensts verwenden, die die Zählvorgänge für Tasks und Knoten nicht unterstützt, verwenden Sie stattdessen eine Listenabfrage, um die Ressourcen zu zählen. Verwenden Sie auch eine Listenabfrage, um Informationen zu anderen Batch-Ressourcen, wie z.B. Anwendungen, Dateien und Aufträge, abzurufen. Weitere Informationen zum Anwenden von Filtern auf Listenabfragen finden Sie unter [Erstellen von Abfragen zum effizienten Auflisten von Batch-Ressourcen](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Taskstatuszähler

Mit dem Vorgang „Get Task Counts“ (Taskanzahl abrufen) werden Tasks nach den folgenden Status gezählt:

- **Aktiv**: Ein Task, der in die Warteschlange gestellt wurde und ausgeführt werden kann, derzeit aber keinem Computeknoten zugewiesen ist. Ein Task wird auch als `active` gezählt, wenn er von [einem übergeordneten Task](batch-task-dependencies.md) abhängig ist, der noch nicht abgeschlossen ist. 
- **Ausgeführt**: Ein Task, der einem Computeknoten zugewiesen, aber noch nicht abgeschlossen wurde. Ein Task wird als `running` gezählt, wenn sein Status entweder `preparing` oder `running` lautet. Dies wird mit dem Vorgang [Get information about a task][rest_get_task] (Informationen zu einem Task abrufen) angegeben.
- **Abgeschlossen**: Ein Task, der nicht mehr zur Ausführung berechtigt ist, da er entweder erfolgreich abgeschlossen wurde oder fehlgeschlagen ist und außerdem sein Wiederholungslimit ausgeschöpft hat. 
- **Erfolgreich**: Ein Task, dessen Taskausführungsergebnis `success` lautet. Batch bestimmt, ob ein Task erfolgreich oder fehlerhaft war, indem die Eigenschaft `TaskExecutionResult` der Eigenschaft [executionInfo][rest_get_exec_info] überprüft wird.
- **Fehler**: Ein Task, dessen Taskausführungsergebnis `failure` lautet.

Das folgende .NET-Codebeispiel zeigt, wie Sie die Taskanzahl nach dem Status abrufen: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Sie können ein ähnliches Muster für REST und andere unterstützte Sprachen verwenden, um die Taskanzahl für einen Auftrag zu erhalten. 

> [!NOTE]
> API-Versionen des Batch-Diensts vor 2018-08-01.7.0 geben auch eine `validationStatus`-Eigenschaft in der Antwort von „Get Task Counts“ zurück. Diese Eigenschaft gibt an, ob Batch die Zustandsanzahl auf Konsistenz mit den von der API zum Auflisten von Tasks angegebenen überprüft hat. Der Wert `validated` bedeutet lediglich, dass Batch mindestens einmal für den Auftrag eine Konsistenzüberprüfung durchgeführt hat. Der Wert der `validationStatus`-Eigenschaft gibt nicht an, ob die von „Get Task Counts“ zurückgegebenen Zahlen auf dem neuesten Stand sind.
>

## <a name="node-state-counts"></a>Knotenstatuszähler

Der Vorgang „List Pool Node Counts“ (Poolknotenanzahl auflisten) zählt Computeknoten nach den folgenden Status in jedem Pool. Separate aggregierte Zähler werden für dedizierte Knoten und Knoten mit niedriger Priorität in jedem Pool bereitgestellt.

- **Erstellung**: Ein Azure zugeordneter virtueller Computer, der noch nicht gestartet wurde, um einem Pool beizutreten.
- **Im Leerlauf**: Ein verfügbarer Computeknoten, der derzeit keinen Task ausführt.
- **Verlässt Pool**: Ein Knoten, der den Pool verlässt, da er ausdrücklich vom Benutzer entfernt wurde oder da die Größe des Pools geändert wird oder der Pool automatisch herunterskaliert wird.
- **Offline**: Ein Knoten, mit dem Batch keine neuen Tasks planen kann.
- **Vorzeitig entfernt**: Ein Knoten mit niedriger Priorität, der aus dem Pool entfernt wurde, da Azure den virtuellen Computer freigegeben hat. Ein Knoten mit Status `preempted` kann neu initialisiert werden, wenn VM-Kapazität mit niedriger Priorität als Ersatz verfügbar ist.
- **Neustart**: Ein Knoten, der neu gestartet wird.
- **Reimaging**: Ein Knoten, auf dem das Betriebssystem neu installiert wird.
- **Ausführung**: Ein Knoten, der auf dem mindestens ein Task (mit Ausnahme des Starttasks) ausgeführt wird.
- **Wird gestartet**: Ein Knoten, auf dem der Batch-Dienst gestartet wird. 
- **StartTaskFailed**: Ein Knoten, auf dem der [Starttask][rest_start_task] fehlgeschlagen ist und alle Wiederholungsversuche ausgeschöpft wurden und auf dem `waitForSuccess` für den Starttask festgelegt ist. Der Knoten kann nicht zum Ausführen von Tasks verwendet werden.
- **Unbekannt**: Ein Knoten, der die Verbindung mit dem Batch-Dienst verloren hat und dessen Status unbekannt ist.
- **Unbrauchbar**: Ein Knoten, der aufgrund von Fehlern nicht zur Taskausführung verwendet werden kann.
- **WaitingForStartTask**: Ein Knoten, auf dem die Ausführung des Starttasks gestartet wurde, aber `waitForSuccess` festgelegt ist und der Starttask noch nicht abgeschlossen wurde.

Der folgende C#-Codeausschnitt zeigt, wie Sie die Anzahl der Knoten für alle Pools im aktuellen Konto auflisten:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Der folgende C#-Codeausschnitt zeigt, wie Sie die Anzahl der Knoten für einen bestimmten Pool im aktuellen Konto auflisten.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Sie können ein ähnliches Muster für REST und andere unterstützte Sprachen verwenden, um die Knotenanzahl für Pools zu erhalten.
 
## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Konzepten und Features des Batch-Diensts finden Sie in der [Funktionsübersicht für Batch](batch-api-basics.md). In diesem Artikel werden die primären Batch-Ressourcen beschrieben, z.B. Pools, Computeknoten, Aufträge und Aufgaben. Außerdem enthält er eine Übersicht über die Features des Diensts.

* Informationen zum Anwenden von Filtern auf Abfragen, die Batch-Ressourcen auflisten, finden Sie unter [Erstellen von Abfragen zum effizienten Auflisten von Batch-Ressourcen](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

