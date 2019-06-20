---
title: Entwickeln effizienter Listenabfragen – Azure Batch | Microsoft-Dokumentation
description: Steigern Sie durch Filtern von Abfragen die Leistung beim Anfordern von Informationen zu Batch-Ressourcen wie Pools, Aufträgen, Aufgaben und Computeknoten.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: eddafdc651eb2cd0fbdf400f7f7e933a91021faf
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808152"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Erstellen von Abfragen zum effizienten Auflisten von Batch-Ressourcen

Hier erfahren Sie, wie Sie die Leistung Ihrer Azure Batch-Anwendung steigern, indem Sie die Datenmenge verringern, die vom Dienst zurückgegeben wird, wenn Sie Aufträge, Tasks, Computeknoten und andere Ressourcen mit der [Batch .NET][api_net]-Bibliothek abfragen.

Nahezu alle Batch-Anwendungen führen eine Überwachung oder eine andere Art von Vorgang aus, die den Batch-Dienst (häufig in regelmäßigen Abständen) abfragt. Wenn Sie etwa bestimmen möchten, ob bei einem Auftrag noch Aufgaben in der Warteschlange vorhanden sind, müssen Daten zu allen Aufgaben des Auftrags abgerufen werden. Wenn Sie den Status von Knoten im Pool ermitteln möchten, müssen Daten zu jedem Knoten im Pool abgerufen werden. In diesem Artikel wird erläutert, wie diese Abfragen auf möglichst effiziente Weise ausgeführt werden.

> [!NOTE]
> Der Batch-Dienst bietet spezielle API-Unterstützung für allgemeine Szenarios, in denen Tasks in einem Auftrag und Compute-Knoten in einem Batch-Pool gezählt werden. Statt dafür eine Listenabfrage zu verwenden, können Sie den Vorgänge [Get Task Counts][rest_get_task_counts] (Taskanzahl abrufen) und [List Pool Node Counts][rest_get_node_counts] (Poolknotenanzahl auflisten) aufrufen. Diese Vorgänge sind effizienter als eine Listenabfrage, geben jedoch eingeschränkte Informationen zurück. Weitere Informationen finden Sie unter [Monitor Batch solutions by counting tasks and nodes by state (Überwachen von Batch-Lösungen durch das Zählen von Tasks und Knoten nach Bundesstaat)](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>DetailLevel
In einer Batch-Produktionsanwendung kann die Anzahl von Entitäten wie Aufträgen, Aufgaben und Computeknoten leicht in die Tausende gehen. Beim Anfordern von Informationen zu diesen Ressourcen muss häufig für jede Abfrage eine größere Menge von Daten den Weg vom Batch-Dienst zu Ihrer Anwendung zurücklegen. Durch Einschränken der Anzahl von Elementen und der Art der zurückgegebenen Informationen können Sie die Geschwindigkeit Ihrer Abfragen und damit die Leistung der Anwendung steigern.

Der folgende [Batch .NET][api_net]-API-Codeausschnitt listet *alle* Aufgaben im Zusammenhang mit einem Auftrag sowie *alle* Eigenschaften der einzelnen Aufgaben auf:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Durch Anwenden einer Detailebene auf die Abfrage lässt sich die Effizienz von Listenabfragen deutlich erhöhen. Dazu übergeben Sie ein [ODATADetailLevel][odata]-Objekt an die [JobOperations.ListTasks][net_list_tasks]-Methode. Dieser Codeausschnitt gibt nur die ID-, Befehlszeilen- und Computeknoteneigenschaften abgeschlossener Aufgaben zurück:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Wenn der Auftrag wie in diesem Beispielszenario Tausende von Aufgaben umfasst, werden die Ergebnisse der zweiten Abfrage meist viel schneller als die der ersten zurückgegeben. Weitere Informationen zum Verwenden von ODATADetailLevel beim Auflisten von Elementen mit der Batch .NET-API finden Sie [weiter unten](#efficient-querying-in-batch-net)in diesem Dokument.

> [!IMPORTANT]
> Es wird dringend empfohlen, *immer* ein ODATADetailLevel-Objekt für Ihre .NET-API-Listenaufrufe zu verwenden, um eine maximale Effizienz und optimale Leistung der Anwendung zu gewährleisten. Das Angeben einer Detailebene hilft dem Batch-Dienst beim Verkürzen von Antwortzeiten, Verbessern der Netzwerkauslastung und Minimieren der Speicherbelegung von Clientanwendungen.
> 
> 

## <a name="filter-select-and-expand"></a>Filtern, Auswählen und Erweitern
Die [Batch .NET][api_net]- und [Batch REST][api_rest]-APIs bieten die Möglichkeit, sowohl die Anzahl von Elementen, die in einer Liste zurückgegeben werden, als auch die Menge von Informationen für jedes Element zu reduzieren. Dazu geben Sie beim Ausführen von Listenabfragen Zeichenfolgen zum **Filtern**, **Auswählen** und **Erweitern** an.

### <a name="filter"></a>Filter
Die Filterzeichenfolge ist ein Ausdruck, der die Anzahl der zurückgegebenen Elemente reduziert. Sie können beispielsweise die derzeit ausgeführten Aufgaben für einen Auftrag oder nur Computeknoten auflisten, die zum Ausführen von Aufgaben bereit sind.

* Eine Filterzeichenfolge besteht aus mindestens einem Ausdruck, wobei ein Ausdruck aus einem Eigenschaftsnamen, einem Operator und einem Wert besteht. Die Eigenschaften, die angegeben werden können, sind spezifisch für jeden Entitätstyp, den Sie abfragen. Dies gilt auch für die für jede Eigenschaft unterstützten Operatoren.
* Mehrere Ausdrücke können mithilfe der logischen Operatoren `and` und `or` kombiniert werden.
* Das folgende Beispiel für eine Filterzeichenfolge listet nur die aktuell ausgeführten Renderaufgaben auf: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Select
Die Auswählzeichenfolge begrenzt die Eigenschaftswerte, die für jedes Element zurückgegeben werden. Sie geben eine Liste mit Eigenschaftsnamen an, woraufhin nur diese Eigenschaftswerte für die Elemente in den Abfrageergebnissen zurückgegeben werden.

* Die Auswählzeichenfolge besteht aus einer durch Trennzeichen getrennte Liste von Eigenschaftsnamen. Sie können beliebige Eigenschaften für den Entitätstyp angeben, den Sie abfragen möchten.
* Das folgende Beispiel für eine Auswählzeichenfolge gibt an, dass für jede Aufgabe nur drei Eigenschaftswerte zurückgegeben werden sollen: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expand
Die Erweiterungszeichenfolge verringert die Anzahl der API-Aufrufe, die zum Abrufen bestimmter Informationen erforderlich sind. Wenn Sie eine Erweiterungszeichenfolge verwenden, können mit einem einzigen API-Aufruf weitere Informationen zu den einzelnen Elementen abgerufen werden. Anstatt zunächst die Liste der Entitäten abzurufen und dann Informationen zu den einzelnen Elementen in der Liste anzufordern, können Sie dieselben Informationen mithilfe einer Erweiterungszeichenfolge mit einem einzigen API-Aufruf abrufen. Je weniger API-Aufrufe, desto besser die Leistung.

* Ähnlich wie die Auswählzeichenfolge steuert die Erweiterungszeichenfolge, ob bestimmte Daten in die Listenabfrageergebnisse einbezogen werden.
* Die Erweiterungszeichenfolge wird nur beim Auflisten von Aufträgen, Auftragszeitplänen, Aufgaben und Pools unterstützt. Derzeit bietet sie nur Unterstützung für Statistikinformationen.
* Wenn alle Eigenschaften erforderlich sind und keine Auswählzeichenfolge angegeben wurde, *muss* die Erweiterungszeichenfolge zum Abrufen von Statistikinformationen verwendet werden. Wenn eine Auswählzeichenfolge zum Abrufen einer Teilmenge der Eigenschaften verwendet wird, kann `stats` in der Auswählzeichenfolge angegeben werden, und die Erweiterungszeichenfolge muss nicht angegeben werden.
* Das folgende Beispiel für eine Erweiterungszeichenfolge gibt an, dass für jedes Element in der Liste Statistikinformationen zurückgegeben werden sollen: `stats`.

> [!NOTE]
> Beim Erstellen dieser drei Typen von Abfragezeichenfolgen (Filtern, Auswählen, Erweitern) müssen Sie sicherstellen, dass die Namen und die Groß-/Kleinschreibung der Eigenschaften mit den entsprechenden REST-API-Elementen übereinstimmen. Wenn Sie beispielsweise mit der .NET-Klasse [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask#Microsoft_Azure_Batch_CloudTask) arbeiten, müssen Sie **state** anstelle von **State** angeben, obwohl die .NET-Eigenschaft [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State) lautet. In den folgenden Tabellen finden Sie Eigenschaftszuordnungen zwischen der .NET- und der REST-API.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regeln für Filter-, Auswähl- und Erweiterungszeichenfolgen
* Eigenschaftsnamen in Filter-, Auswähl- und Erweiterungszeichenfolgen müssen den Eigenschaftsnamen in der [Batch REST][api_rest]-API entsprechen. Dies gilt auch bei Verwendung von [Batch .NET][api_net] oder von einem der anderen Batch-SDKs.
* Bei allen Eigenschaftsnamen muss die Groß- und Kleinschreibung beachtet werden, bei Eigenschaftswerten dagegen nicht.
* Datums-/Uhrzeitzeichenfolgen können eines von zwei Formaten besitzen, und ihnen muss `DateTime`vorangestellt sein.
  
  * Beispiel für das W3C-DTF-Format: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Beispiel für das RFC 1123-Format: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Boolesche Zeichenfolgen sind entweder `true` oder `false`.
* Bei Angabe einer ungültigen Eigenschaft oder eines ungültigen Operators tritt der Fehler `400 (Bad Request)` auf.

## <a name="efficient-querying-in-batch-net"></a>Effizientes Abfragen in Batch .NET
Innerhalb der [Batch .NET][api_net]-API wird die [ODATADetailLevel][odata]-Klasse zum Angeben von Filter-, Auswähl- und Erweiterungszeichenfolgen für Listenvorgänge verwendet. Die „ODataDetailLevel“-Klasse verfügt über drei öffentliche Zeichenfolgeneigenschaften, die im Konstruktor angegeben oder direkt für das Objekt festgelegt werden können. Das ODataDetailLevel-Objekt wird dann als Parameter an die verschiedenen Listenvorgänge wie [ListPools][net_list_pools], [ListJobs][net_list_jobs] oder [ListTasks][net_list_tasks] übergeben.

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: Begrenzt die Anzahl der zurückgegebenen Elemente.
* [ODATADetailLevel][odata].[SelectClause][odata_select]: Gibt an, welche Eigenschaftswerte mit jedem Element zurückgegeben werden sollen.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: Ruft Daten für alle Elemente in einem einzigen API-Aufruf statt mit separaten Aufrufen für jedes Element ab.

Im folgenden Codeausschnitt wird die Batch .NET-API verwendet, um den Batch-Dienst effizient auf die Statistik zu einer bestimmten Gruppe von Pools abzufragen. In diesem Szenario verfügt der Batch-Benutzer über Test- und Produktionspools. Den Testpool-IDs ist das Präfix „test“ vorangestellt, und den Produktionspool-IDs ist das Präfix „prod“ vorangestellt. Im Codeausschnitt ist *myBatchClient* eine ordnungsgemäß initialisierte Instanz der [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) -Klasse.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Eine mit Select- und Expand-Klauseln konfigurierte Instanz von [ODATADetailLevel][odata] kann auch an geeignete Get-Methoden (etwa [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)) übergeben werden, um die Menge der zurückgegebenen Daten einzuschränken.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Zuordnungen zwischen der Batch REST-API und .NET-API
Eigenschaftsnamen in Filter-, Auswähl- und Erweiterungszeichenfolgen *müssen* ihren Gegenstücken in der REST-API hinsichtlich Name und Groß-/Kleinschreibung entsprechen. Die folgenden Tabellen enthalten die Zuordnungen zwischen den .NET-APIs und ihren REST-API-Entsprechungen.

### <a name="mappings-for-filter-strings"></a>Zuordnungen für Filterzeichenfolgen
* **.NET-Listenmethoden**: Jede der .NET-API-Methoden in dieser Spalte akzeptiert ein [ODATADetailLevel][odata]-Objekt als Parameter.
* **REST-Listenanforderungen**: Jede mit dieser Spalte verknüpfte REST-API-Seite enthält eine Tabelle mit den Eigenschaften und Vorgängen, die in *Filterzeichenfolgen* zulässig sind. Diese Eigenschaftsnamen und Vorgänge werden verwendet, wenn Sie eine [ODATADetailLevel.FilterClause][odata_filter]-Zeichenfolge erstellen.

| .NET-Listenmethoden | REST-Listenanforderungen |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Auflisten der Zertifikate in einem Konto][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Auflisten der einer Aufgabe zugeordneten Dateien][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Auflisten des Status der Aufgaben zur Auftragsvorbereitung und -freigabe für einen Auftrag][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Auflisten der Aufträge in einem Konto][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Auflisten der Dateien auf einem Knoten][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Auflisten der einem Auftrag zugeordneten Aufgaben][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Auflisten der Auftragszeitpläne in einem Konto][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Auflisten der einem Auftragszeitplan zugeordneten Aufträge][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Auflisten der Computeknoten in einem Pool][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Auflisten der Pools in einem Konto][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Zuordnungen für Auswählzeichenfolgen
* **Batch .NET-Typen**: Batch .NET-API-Typen.
* **REST-API-Entitäten**: Jede Seite in dieser Spalte enthält mindestens eine Tabelle mit den Namen der REST-API-Eigenschaften für den Typ. Diese Eigenschaftsnamen werden beim Erstellen von *Auswählzeichenfolgen* verwendet. Die gleichen Eigenschaftsnamen werden verwendet, wenn Sie eine [ODATADetailLevel.SelectClause][odata_select]-Zeichenfolge erstellen.

| Batch .NET-Typen | REST-API-Entitäten |
| --- | --- |
| [Certificate][net_cert] |[Abrufen von Informationen zu einem Zertifikat][rest_get_cert] |
| [CloudJob][net_job] |[Abrufen von Informationen zu einem Auftrag][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Abrufen von Informationen zu einem Auftragszeitplan][rest_get_schedule] |
| [ComputeNode][net_node] |[Abrufen von Informationen zu einem Knoten][rest_get_node] |
| [CloudPool][net_pool] |[Abrufen von Informationen zu einem Pool][rest_get_pool] |
| [CloudTask][net_task] |[Abrufen von Informationen zu einer Aufgabe][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Beispiel: Erstellen einer Filterzeichenfolge
Ermitteln Sie beim Erstellen einer Filterzeichenfolge für [ODATADetailLevel.FilterClause][odata_filter] in der obigen Tabelle unter „Zuordnungen für Filterzeichenfolgen“ die passende REST-API-Dokumentationsseite für den gewünschten Listenvorgang. Sie finden die filterbaren Eigenschaften und die jeweils unterstützten Operatoren in der ersten mehrzeiligen Tabelle auf der entsprechenden Seite. Wenn Sie beispielsweise alle Aufgaben abrufen möchten, deren Exitcode ungleich Null war, gibt die Zeile zu [Auflisten der einem Auftrag zugeordneten Aufgaben][rest_list_tasks] die entsprechende Eigenschaftszeichenfolge und zulässigen Operatoren an:

| Eigenschaft | Zulässige Vorgänge | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Daher lautet die Filterzeichenfolge zum Auflisten aller Aufgaben mit einem Exitcode ungleich 0 wie folgt:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Beispiel: Erstellen einer Auswählzeichenfolge
Ziehen Sie beim Erstellen von [ODATADetailLevel.SelectClause][odata_select] die obige Tabelle unter „Zuordnungen für Auswählzeichenfolgen“ heran, und navigieren Sie zu der REST-API-Seite, die der Art der aufzulistenden Entität entspricht. Sie finden die auswählbaren Eigenschaften und die jeweils unterstützten Operatoren in der ersten mehrzeiligen Tabelle auf der entsprechenden Seite. Wenn Sie z.B. nur die ID und Befehlszeile für jede Aufgabe in einer Liste abrufen möchten, finden Sie diese Zeilen in der entsprechenden Tabelle unter [Abrufen von Informationen zu einer Aufgabe][rest_get_task]:

| Eigenschaft | Type | Notizen |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Die Auswählzeichenfolge zum Einbeziehen von ausschließlich der ID und Befehlszeile für jede aufgeführte Aufgabe lautet dann wie folgt:

`id, commandLine`

## <a name="code-samples"></a>Codebeispiele
### <a name="efficient-list-queries-code-sample"></a>Codebeispiel für effiziente Listenabfragen
Probieren Sie das Beispielprojekt [EfficientListQueries][efficient_query_sample] auf GitHub aus, um zu sehen, wie sich effiziente Listenabfragen in einer Anwendung auf die Leistung auswirken können. Diese C#-Konsolenanwendung erstellt eine große Anzahl von Aufgaben und fügt sie einem Auftrag hinzu. Anschließend führt sie mehrere Aufrufe für die [JobOperations.ListTasks][net_list_tasks]-Methode aus und übergibt [ODATADetailLevel][odata]-Objekte, die mit verschiedenen Eigenschaftswerten konfiguriert sind, um unterschiedliche Mengen von Rückgabedaten zu erhalten. Sie erzeugt eine Ausgabe ähnlich der Folgenden:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Wie anhand der verstrichenen Zeit zu sehen, können Sie durch eine Begrenzung der Eigenschaften und der Anzahl von zurückgegebenen Elementen die Antwortzeiten für Abfragen erheblich verkürzen. Sie finden dieses Beispielprojekt und weitere Beispielprojekte im [azure-batch-samples][github_samples]-Repository auf GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics-Bibliothek und Codebeispiel
Zusätzlich zum obigen EfficientListQueries-Codebeispiel steht im GitHub-Repository [azure-batch-samples][github_samples] das Projekt [BatchMetrics][batch_metrics] zur Verfügung. Das BatchMetrics-Beispielprojekt veranschaulicht die effiziente Überwachung des Azure Batch-Auftragsstatus mithilfe der Batch-API.

Das [BatchMetrics][batch_metrics]-Beispiel enthält ein .NET-Klassenbibliotheksprojekt, das Sie in Ihre eigenen Projekte integrieren können, sowie ein einfaches Befehlszeilenprogramm zum Testen und Veranschaulichen der Verwendung der Bibliothek.

Die Beispielanwendung innerhalb des Projekts veranschaulicht folgende Vorgänge:

1. Auswählen bestimmter Attribute, um nur die benötigten Eigenschaften herunterzuladen
2. Filtern nach Statusübergangszeiten, um nur Änderungen seit der letzten Abfrage herunterzuladen

Die BatchMetrics-Bibliothek enthält beispielsweise die folgende Methode. Diese gibt ein ODATADetailLevel-Objekt zurück, mit dem angegeben wird, dass für die abgefragten Entitäten nur die Eigenschaften `id` und `state` abgerufen werden sollen. Außerdem gibt es an, dass nur Entitäten zurückgegeben werden sollen, deren Status sich seit dem angegebenen `DateTime` -Parameter geändert hat.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Nächste Schritte
### <a name="parallel-node-tasks"></a>Parallele Knotenaufgaben
[Maximieren der Azure Batch Compute-Ressourcenauslastung mit parallelen Knotenaufgaben](batch-parallel-node-tasks.md) ist ein weiterer Artikel zur Leistung von Batch-Anwendungen. Einige Arten von Workloads profitieren von der Ausführung paralleler Aufgaben auf größeren (und dafür weniger) Computeknoten. Ausführlichere Informationen zu einem solchen Szenario finden Sie im [Beispielszenario](batch-parallel-node-tasks.md#example-scenario) des Artikels.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
