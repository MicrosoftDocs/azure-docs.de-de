---
title: Abfragen nach Clusterereignissen mithilfe von EventStore-APIs
description: Es wird beschrieben, wie Sie die Azure Service Fabric-EventStore-APIs verwenden, um Plattformereignisse abzufragen.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 286c59938413a912c005181fca12f6698a7bc55e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042893"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Abfragen von EventStore-APIs nach Clusterereignissen

In diesem Artikel wird beschrieben, wie Sie die EventStore-APIs abfragen, die in Service Fabric Version 6.2 und höher verfügbar sind. Weitere Informationen zum EventStore-Dienst finden Sie unter [Übersicht über den EventStore-Dienst](service-fabric-diagnostics-eventstore.md). Derzeit kann der EventStore-Dienst nur auf Daten der letzten sieben Tage zugreifen (basiert auf der Diagnosedaten-Aufbewahrungsrichtlinie Ihres Clusters).

>[!NOTE]
>Die EventStore-APIs sind ab Service Fabric-Version 6.4 für reine Windows-Cluster auf Azure allgemein verfügbar.

Auf die EventStore-APIs kann direkt über einen REST-Endpunkt oder programmgesteuert zugegriffen werden. Je nach Abfrage können mehrere Parameter verwendet werden, die zum Sammeln der richtigen Daten erforderlich sind. Diese Parameter schließen in der Regel Folgendes ein:
* `api-version`: Die Version der von Ihnen verwendeten EventStore-APIs.
* `StartTimeUtc`: Definiert den Beginn des Zeitraums, den Sie sich ansehen möchten.
* `EndTimeUtc`: Definiert das Ende des Zeitraums.

Zusätzlich zu diesen Parametern sind optionale Parameter verfügbar, darunter:
* `timeout`: Bewirkt die Außerkraftsetzung des Standardzeitlimits von 60 Sekunden zur Durchführung des Anforderungsvorgangs.
* `eventstypesfilter`: Ermöglicht das Filtern nach bestimmten Ereignistypen.
* `ExcludeAnalysisEvents`: Verhindert die Rückgabe von „Analysis“-Ereignissen (Analyseereignissen). Standardmäßig werden EventStore-Abfragen mit Analyseereignissen zurückgegeben, sofern dies möglich ist. Analyseereignisse sind umfassendere Betriebskanalereignisse, die zusätzlichen Kontext oder Informationen über ein reguläres Service Fabric-Ereignis hinaus enthalten und mehr in die Tiefe gehen.
* `SkipCorrelationLookup`: Verhindert das Suchen nach potenziellen korrelierten Ereignissen im Cluster. Standardmäßig versucht die EventStore-Komponente, Ereignisse eines Clusters zu korrelieren und Ihre Ereignisse nach Möglichkeit zu verknüpfen. 

Jede Entität eines Clusters kann nach Ereignissen abgefragt werden. Außerdem können Sie für alle Entitäten eines Typs Ereignisse abfragen. Beispielsweise können Sie Ereignisse für einen bestimmten Knoten oder für alle Knoten Ihres Clusters abfragen. Dies ist der aktuelle Satz mit Entitäten, für die Sie Ereignisse abfragen können (mit Strukturierung der Abfrage):
* Cluster: `/EventsStore/Cluster/Events`
* Nodes: `/EventsStore/Nodes/Events`
* Node: `/EventsStore/Nodes/<NodeName>/$/Events`
* Applications: `/EventsStore/Applications/Events`
* Application: `/EventsStore/Applications/<AppName>/$/Events`
* Services: `/EventsStore/Services/Events`
* Service: `/EventsStore/Services/<ServiceName>/$/Events`
* Partitions: `/EventsStore/Partitions/Events`
* Partition: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Replicas: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replica: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Beim Verweisen auf einen Anwendungs- oder Dienstnamen muss die Abfrage das Präfix „fabric:/“ nicht unbedingt enthalten. Beachten Sie außerdem Folgendes: Falls Ihre Anwendungs- oder Dienstnamen einen Schrägstrich („/“) enthalten, sollten Sie diesen durch „~“ ersetzen, um die Funktionsweise der Abfrage sicherzustellen. Wenn Ihre Anwendung beispielsweise als „fabric:/App1/FrontendApp“ angezeigt wird, wären Ihre App-spezifischen Abfragen wie folgt strukturiert: `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Darüber hinaus werden Integritätsberichte für Dienste derzeit unter der entsprechenden Anwendung angezeigt. Sie fragen also `DeployedServiceHealthReportCreated`-Ereignisse für die richtige Anwendungsentität ab. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Abfragen der EventStore-Komponente über REST-API-Endpunkte

Sie können die EventStore-Komponente direkt über einen REST-Endpunkt abfragen, indem Sie `GET`-Anforderungen an folgendes Ziel richten: `<your cluster address>/EventsStore/<entity>/Events/`.

Ihre Anforderung sieht beispielsweise wie folgt aus, um alle Clusterereignisse zwischen `2018-04-03T18:00:00Z` und `2018-04-04T18:00:00Z` abzufragen:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Es werden entweder keine Ereignisse oder die in JSON zurückgegebene Liste der Ereignisse zurückgegeben:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Hier ist zu sehen, dass zwischen `2018-04-03T18:00:00Z` und `2018-04-04T18:00:00Z` das erste Upgrade für diesen Cluster nach seiner Einrichtung erfolgreich abgeschlossen wurde (von `"CurrentClusterVersion": "0.0.0.0:"` auf `"TargetClusterVersion": "6.2:1.0"` in `"OverallUpgradeElapsedTimeInMs": "120196.5212"`).

## <a name="query-the-eventstore-programmatically"></a>Programmgesteuertes Abfragen der EventStore-Komponente

Sie können die EventStore-Komponente auch programmgesteuert über die [Service Fabric-Clientbibliothek](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library) abfragen.

Nachdem Sie den Service Fabric-Client eingerichtet haben, können Sie Ereignisse abfragen, indem Sie wie folgt auf die EventStore-Komponente zugreifen: `sfhttpClient.EventStore.<request>`.

Hier ist eine Beispielanforderung für alle Clusterereignisse zwischen `2018-04-03T18:00:00Z` und `2018-04-04T18:00:00Z` über die Funktion `GetClusterEventListAsync` angegeben.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Hier ein weiteres Beispiel, das die Clusterintegrität und alle Knotenereignisse im September 2018 abfragt. Die Ergebnisse werden anschließend ausgegeben.

```csharp
  const int timeoutSecs = 60;
  var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
  var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

  var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
  Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());

  
  Console.WriteLine("Querying for node events...");
  var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
      "2018-09-01T00:00:00Z",
      "2018-09-30T23:59:59Z",
      timeoutSecs,
      "NodeDown,NodeUp")
      .GetAwaiter()
      .GetResult()
      .ToList();
  Console.WriteLine("Result Count: {0}", nodesEvents.Count());

  foreach (var nodeEvent in nodesEvents)
  {
      Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
      if (nodeEvent is NodeDownEvent)
      {
          var nodeDownEvent = nodeEvent as NodeDownEvent;
          Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
      }
      else if (nodeEvent is NodeUpEvent)
      {
          var nodeUpEvent = nodeEvent as NodeUpEvent;
          Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
      }
  }
```

## <a name="sample-scenarios-and-queries"></a>Beispielszenarien und -abfragen

Hier sind einige Beispiele dafür angegeben, wie Sie die EventStore-REST-APIs aufrufen können, um Informationen zum Status Ihres Clusters zu erhalten.

*Clusterupgrades:*

Um anzuzeigen, wann für Ihren Cluster während der letzten Woche zum letzten Mal erfolgreich ein Upgrade durchgeführt wurde (oder ein entsprechender Versuch unternommen wurde), können Sie die APIs nach zuletzt abgeschlossenen Upgrades für den Cluster abfragen. Fragen Sie hierzu die ClusterUpgradeComplete-Ereignisse in der EventStore-Komponente ab: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`.

*Probleme mit Clusterupgrades:*

Falls bei einem kürzlich durchgeführten Clusterupgrade Probleme aufgetreten sind, können Sie alle Ereignisse für die Clusterentität abfragen. Es werden verschiedene Ereignisse angezeigt, einschließlich der Initiierung von Upgrades und aller Upgradedomänen, für die das Upgrade erfolgreich abgeschlossen wurde. Außerdem werden Ereignisse für den Punkt, an dem der Rollback gestartet wurde, und die entsprechenden Integritätsereignisse angezeigt. Hier ist die Abfrage angegeben, die Sie hierfür verwenden: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`.

*Änderung des Knotenstatus:*

Verwenden Sie die folgende Abfrage, um die Änderungen des Knotenstatus für die letzten Tage anzuzeigen – das Hoch- oder Herunterfahren von Knoten bzw. deren Aktivierung oder Deaktivierung (entweder über die Plattform, den Chaosdienst oder per Benutzereingabe): `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`.

*Anwendungsereignisse:*

Sie können auch Ihre zuletzt durchgeführten Anwendungsbereitstellungen und -upgrades nachverfolgen. Verwenden Sie die folgende Abfrage, um alle Anwendungsereignisse in Ihrem Cluster anzuzeigen: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`.

*Integritätsverlauf für eine Anwendung:*

Es kann sein, dass Sie nicht nur Ereignisse zum Anwendungslebenszyklus anzeigen möchten, sondern auch die Verlaufsdaten zur Integrität einer bestimmten Anwendung. Dies erreichen Sie, indem Sie den Namen der Anwendung angeben, für die Sie die Daten sammeln möchten. Verwenden Sie diese Abfrage, um alle Ereignisse zur Integrität der Anwendung abzurufen: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Falls Sie auch Integritätsereignisse einbeziehen möchten, die ggf. abgelaufen sind (Ende der Gültigkeitsdauer), können Sie am Ende der Abfrage `,ApplicationHealthReportExpired` hinzufügen, um nach zwei Arten von Ereignissen zu filtern.

*Integritätsverlauf für alle Dienste in „myApp“:*

Derzeit werden Integritätsberichtsereignisse für Dienste unter der entsprechenden Anwendungsentität als `DeployedServicePackageNewHealthReport`-Ereignisse angezeigt. Verwenden Sie die folgende Abfrage, um den Verlauf für Ihre Dienste für „App1“ anzuzeigen: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`.

*Neukonfiguration der Partition:*

Fragen Sie das `PartitionReconfigured`-Ereignis ab, um alle Partitionsbewegungen des Clusters anzuzeigen. Auf diese Weise können Sie ermitteln, welche Workloads zu bestimmten Zeiten auf welchem Knoten ausgeführt wurden, wenn Sie Probleme in Ihrem Cluster diagnostizieren. Hier ist eine Beispielabfrage angegeben, die diesen Zweck erfüllt: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`.

*Chaosdienst:*

Es ist ein Ereignis für den Start bzw. die Beendigung des Chaosdiensts vorhanden, das auf der Clusterebene verfügbar gemacht wird. Verwenden Sie die folgende Abfrage, um Ihre letzte Nutzung des Chaosdiensts anzuzeigen: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`.
