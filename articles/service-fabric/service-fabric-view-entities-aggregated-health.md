---
title: Anzeigen der aggregierten Integrität für Azure Service Fabric-Entitäten | Microsoft Docs
description: Beschreibt, wie Sie die aggregierte Integrität von Azure Service Fabric-Entitäten abfragen, anzeigen und auswerten, indem Sie Integritätsabfragen und allgemeine Abfragen nutzen.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: e4edcc0aecfbf03aff7cf9bee764522bb1c489f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60716376"
---
# <a name="view-service-fabric-health-reports"></a>Anzeigen von Service Fabric-Integritätsberichten
Mit Azure Service Fabric wird ein [Integritätsmodell](service-fabric-health-introduction.md) eingeführt, das Integritätsentitäten enthält, auf denen Systemkomponenten und Watchdogs die von ihnen überwachten lokalen Bedingungen melden können. Im so genannten [Integritätsspeicher](service-fabric-health-introduction.md#health-store) werden alle Integritätsdaten zusammengefasst, um zu ermitteln, ob die Entitäten fehlerfrei sind.

Der Cluster wird automatisch mit von den Systemkomponenten gesendeten Integritätsberichten aufgefüllt. Weitere Informationen erhalten Sie unter [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric bietet Ihnen mehrere Möglichkeiten, den aggregierten Integritätsstatus der Entitäten abzurufen:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) oder andere Visualisierungstools
* Integritätsabfragen (über PowerShell, API oder REST)
* Allgemeine Abfragen, die eine Liste mit Entitäten zurückgeben, von denen eine der Eigenschaften die Integrität ist (über PowerShell, API oder REST)

Um diese Optionen zu demonstrieren, verwenden wir einen lokalen Cluster mit fünf Knoten und der Anwendung [fabric:/WordCount](https://aka.ms/servicefabric-wordcountapp). Die Anwendung **fabric:/WordCount** enthält zwei Standarddienste: einen zustandsbehafteten Dienst vom Typ `WordCountServiceType` und einen zustandslosen Dienst vom Typ `WordCountWebServiceType`. Ich habe `ApplicationManifest.xml` dahin gehend geändert, dass sieben Zielreplikate für den zustandsbehafteten Dienst und eine Partition erforderlich sind. Da nur fünf Knoten im Cluster vorhanden sind, melden die Systemkomponenten eine Warnung zur Dienstpartition, da sie die Zielanzahl unterschreitet.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Integrität im Service Fabric Explorer
Der Service Fabric Explorer bietet eine grafische Ansicht des Clusters. In der unten gezeigten Abbildung ist Folgendes dargestellt:

* Die Anwendung **fabric:/WordCount** ist rot dargestellt (Fehler), weil **MyWatchdog** für die Eigenschaft **Availability** ein Fehlerereignis gemeldet hat.
* Einer der Dienste (**fabric:/WordCount/WordCountService**) ist gelb gekennzeichnet (Warnung). Der Dienst ist mit sieben Replikaten konfiguriert, und der Cluster verfügt über fünf Knoten, daher können zwei Replikate nicht platziert werden. Hier ist es zwar nicht zu sehen, aber die Dienstpartition ist aufgrund eines Systemberichts von `System.FM` gelb gekennzeichnet, der angibt: `Partition is below target replica or instance count`. Die gelb dargestellte Partition löst den gelb dargestellten Dienst aus.
* Der Cluster ist aufgrund der rot gezeigten Anwendung ebenfalls rot dargestellt.

Die Auswertung verwendet Standardrichtlinien aus dem Cluster- und dem Anwendungsmanifest. Hierbei handelt es sich um strenge Richtlinien, die keine Fehler tolerieren.

Ansicht des Clusters mit dem Service Fabric Explorer:

![Ansicht des Clusters mit dem Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Erfahren Sie mehr über den [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Integritätsabfragen
Service Fabric macht für jeden unterstützten [Entitätstyp](service-fabric-health-introduction.md#health-entities-and-hierarchy)Integritätsabfragen verfügbar. Es kann über die API (mit Methoden von [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet)), mit PowerShell-Cmdlets und mit REST darauf zugegriffen werden. Mit diesen Abfragen werden vollständige Integritätsinformationen zur Entität zurückgegeben: der zusammengefasste Integritätsstatus, Integritätsereignisse der Entität, untergeordnete Integritätsstatus (falls verfügbar), Fehlerauswertungen (sofern Fehler für die Entität auftreten) und Statistiken zur Integrität untergeordneter Elemente (sofern zutreffend).

> [!NOTE]
> Eine Integritätsentität wird zurückgegeben, wenn sie im Integritätsspeicher vollständig aufgefüllt wurde. Die Entität muss aktiv sein (nicht gelöscht) und einen Systembericht aufweisen. Außerdem müssen für ihre übergeordneten Entitäten in der Hierarchiekette ebenfalls Systemberichte vorliegen. Falls eine dieser Bedingungen nicht erfüllt ist, geben die Integritätsabfragen eine [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) mit [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` zurück, der zeigt, warum die Entität nicht zurückgegeben wurde.
>
>

Die Integritätsabfragen müssen den Entitätsbezeichner übergeben, der sich nach dem Entitätstyp richtet. Die Abfragen akzeptieren optionale Integritätsrichtlinienparameter. Wenn keine Integritätsrichtlinien angegeben werden, werden die [Integritätsrichtlinien](service-fabric-health-introduction.md#health-policies) aus dem Cluster- oder Anwendungsmanifest für die Auswertung verwendet. Wenn die Manifeste keine Definition für Integritätsrichtlinien enthalten, werden die Standardintegritätsrichtlinien für die Auswertung verwendet. In den Standardintegritätsrichtlinien werden keine Fehler toleriert. Außerdem akzeptieren die Abfragen auch Filter für die Teilrückgabe von untergeordneten Elementen oder Ereignissen, sofern die angegebenen Filter jeweils berücksichtigt werden. Ein weiterer Filter ermöglicht das Ausschließen der Statistiken zu untergeordneten Elementen.

> [!NOTE]
> Die Ausgabefilter werden auf der Serverseite angewendet, damit die Antwortgröße der Nachricht reduziert wird. Es wird empfohlen, die Ausgabefilter zum Eingrenzen der zurückgegebenen Daten zu verwenden, anstatt Filter auf der Clientseite anzuwenden.
>
>

Die Integrität einer Entität enthält Folgendes:

* Den zusammengefassten Integritätsstatus der Entität. Dieser wird vom Integritätsspeicher anhand der Berichte zur Entitätsintegrität, anhand des Integritätsstatus der untergeordneten Elemente (falls zutreffend) und anhand der Integritätsrichtlinien berechnet. Erfahren Sie mehr über die [Auswertung der Entitätsintegrität](service-fabric-health-introduction.md#health-evaluation).  
* Die Integritätsereignisse der Entität
* Die Integritätsstatusauflistung für alle untergeordneten Elemente für die Entitäten, denen Elemente untergeordnet sein können. Die Integritätsstatusangaben enthalten Entitätsbezeichner und den aggregierten Integritätsstatus. Rufen Sie zum Erhalten der vollständigen Integrität für ein untergeordnetes Element die Abfrageintegrität für den untergeordneten Entitätstyp auf, und übergeben Sie den Bezeichner des untergeordneten Elements.
* Wenn die Entität nicht fehlerfrei ist, die Fehlerauswertungen in Verbindung mit dem Bericht, über den der Status der Entität ausgelöst wurde. Die Auswertungen sind rekursiv und enthalten die Auswertungen der Integrität untergeordneter Elemente, die den aktuellen Integritätszustand ausgelöst haben. Beispiel: Ein Watchdog hat einen Fehler für ein Replikat ausgegeben. Die Auswertung der Anwendungsintegrität wird als fehlerhaft aufgrund eines fehlerhaften Diensts angezeigt. Der Dienst ist aufgrund einer fehlerhaften Partition fehlerhaft. Die Partition ist aufgrund eines fehlerhaften Replikats fehlerhaft. Das Replikat ist aufgrund des Fehlerberichts des Watchdogs fehlerhaft.
* Die Integritätsstatistiken gelten für die Typen aller untergeordneten Elemente von Entitäten, die untergeordnete Elemente aufweisen. Beispiel: Die Clusterintegrität zeigt die Gesamtanzahl der Anwendungen, Dienste, Partitionen, Replikate und bereitgestellten Entitäten im Cluster an. Die Dienstintegrität zeigt die Gesamtanzahl der Partitionen und Replikate unter dem angegebenen Dienst an.

## <a name="get-cluster-health"></a>Abrufen der Clusterintegrität
Gibt die Integrität der Clusterentität zurück und enthält die Integritätsstatusangaben von Anwendungen und Knoten (untergeordnete Elemente des Clusters). Eingabe:

* [Optional] Die Clusterintegritätsrichtlinie zum Auswerten der Knoten und der Clusterereignisse.
* [Optional] Die Zuordnung der Anwendungsintegritätsrichtlinien mit den Integritätsrichtlinien zum Außerkraftsetzen der Richtlinien für das Anwendungsmanifest.
* [Optional] Filter für Ereignisse, Knoten und Anwendungen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Alle Ereignisse, Knoten und Anwendungen – unabhängig vom Filter – werden zum Auswerten der aggregierten Entitätsintegrität verwendet.
* [Optional:] Filter zum Ausschließen der Integritätsstatistiken.
* [Optional:] Filter zum Einschließen der Integritätsstatistiken von fabric:/System in der Integritätsstatistik. Nur zutreffend, wenn die Integritätsstatistiken nicht ausgeschlossen werden. Standardmäßig enthalten die Integritätsstatistiken nur Statistiken für Benutzeranwendungen und nicht für Systemanwendungen.

### <a name="api"></a>API
Erstellen Sie zum Abrufen der Clusterintegrität ein Element vom Typ `FabricClient` , und rufen Sie die Methode [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) für das dazugehörige Element vom Typ **HealthManager**auf.

Mit dem folgenden Aufruf wird die Clusterintegrität abgerufen:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Mit dem folgenden Code wird die Clusterintegrität abgerufen, indem eine benutzerdefinierte Clusterintegritätsrichtlinie und Filter für Knoten und Anwendungen verwendet werden. Er gibt an, dass die Integritätsstatistiken die Statistiken für fabric:/System enthalten. Das [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription)-Element mit den Eingabedaten wird erstellt.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Clusterintegrität wird das Cmdlet [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her.

Der Status des Clusters umfasst fünf Knoten, die Systemanwendung und „fabric:/WordCount“ in der beschriebenen Konfiguration.

Mit dem folgenden Cmdlet wird die Clusterintegrität mit standardmäßigen Integritätsrichtlinien abgerufen. Der zusammengefasste Integritätsstatus lautet „Warnung“, da die Anwendung fabric:/WordCount den Status „Warnung“ aufweist. Beachten Sie, dass die Fehlerauswertungen mit Details zu den Bedingungen angezeigt werden, durch die die aggregierte Integrität ausgelöst wurde.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Mit dem folgenden PowerShell-Cmdlet wird die Integrität des Clusters anhand einer benutzerdefinierten Anwendungsrichtlinie abgerufen. Die Ergebnisse werden gefiltert, sodass nur Anwendungen und Knoten mit dem Status „Fehler“ oder „Warnung“ abgerufen werden. Aus diesem Grund werden keine Knoten zurückgegeben, da sie alle fehlerfrei sind. Nur die Anwendung „fabric:/WordCount“ berücksichtigt den Anwendungsfilter. Da mit der benutzerdefinierten Richtlinie festgelegt wird, dass Warnungen für die Anwendung „fabric:/WordCount“ als Fehler anzusehen sind, wird die Anwendung auf den Status „Fehler“ heraufgestuft. Dasselbe gilt auch für den Cluster.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
Sie können die Clusterintegrität mit einer [GET-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) abrufen, die im Text beschriebene Integritätsrichtlinien enthält.

## <a name="get-node-health"></a>Abrufen der Integrität von Knoten
Gibt die Integrität einer Knotenentität zurück und enthält die auf dem Knoten gemeldeten Integritätsereignisse. Eingabe:

* [Erforderlich] Der Knotenname, mit dem der Knoten identifiziert wird.
* [Optional] Einstellungen zur Clusterintegritätsrichtlinie zum Auswerten der Integrität.
* [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Alle Ereignisse – unabhängig vom Filter – werden zum Auswerten der aggregierten Entitätsintegrität verwendet.

### <a name="api"></a>API
Erstellen Sie zum Abrufen der Knotenintegrität über die API ein Element vom Typ `FabricClient` , und rufen Sie die [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) -Methode für das dazugehörige HealthManager-Element auf.

Mit dem folgenden Code wird die Knotenintegrität für den angegebenen Knotennamen abgerufen:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Mit dem folgenden Code wird die Knotenintegrität für den angegebenen Knotennamen abgerufen, und der Ereignisfilter und die benutzerdefinierte Richtlinie werden mit [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription)übergeben:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Knotenintegrität wird das Cmdlet [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her.
Mit dem folgenden Cmdlet wird die Knotenintegrität mit standardmäßigen Integritätsrichtlinien abgerufen:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Mit dem folgenden Cmdlet wird die Integrität aller Knoten im Cluster abgerufen:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Sie können die Knotenintegrität mit einer [GET-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) abrufen, die im Text beschriebene Integritätsrichtlinien enthält.

## <a name="get-application-health"></a>Abrufen der Anwendungsintegrität
Gibt die Integrität einer Anwendungsentität zurück. Enthält die Integritätsstatusangaben der bereitgestellten Anwendung und der untergeordneten Elemente des Diensts. Eingabe:

* [Erforderlich] Der Anwendungsname (URI) zum Identifizieren der Anwendung.
* [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinien.
* [Optional] Filter für Ereignisse, Dienste und bereitgestellte Anwendungen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Alle Ereignisse, Dienste und bereitgestellten Anwendungen werden – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet.
* [Optional:] Filter zum Ausschließen der Integritätsstatistiken. Wenn er nicht angegeben wird, schließen die Integritätsstatistiken die Anzahl der Zustände „OK“, „Warnung“ und „Fehler“ für alle untergeordneten Elemente von Anwendungen ein: Dienste, Partitionen, Replikate, bereitgestellte Anwendungen und bereitgestellte Dienstpakete.

### <a name="api"></a>API
Erstellen Sie zum Abrufen der Anwendungsintegrität ein Element vom Typ `FabricClient` , und rufen Sie die [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) -Methode für das dazugehörige HealthManager-Element auf.

Mit dem folgenden Code wird die Anwendungsintegrität für den angegebenen Anwendungsnamen (URI) abgerufen:

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Mit dem folgenden Code wird die Anwendungsintegrität für den angegebenen Anwendungsnamen (URI) abgerufen. Filter und die benutzerdefinierte Richtlinien werden mit [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription) angegeben.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Anwendungsintegrität wird das Cmdlet [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Cmdlet wird die Integrität der Anwendung **fabric:/WordCount** zurückgegeben:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Das folgende PowerShell-Cmdlet übergibt benutzerdefinierte Richtlinien. Außerdem filtert es Elemente und Ereignisse.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Sie können die Anwendungsintegrität mit einer [GET-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) abrufen, die im Text beschriebene Integritätsrichtlinien enthält.

## <a name="get-service-health"></a>Abrufen der Dienstintegrität
Gibt die Integrität einer Dienstentität zurück. Enthält die Integritätsstatusangaben der Partition. Eingabe:

* [Erforderlich] Der Dienstname (URI) zum Identifizieren des Diensts.
* [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.
* [Optional] Filter für Ereignisse und Partitionen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Alle Ereignisse und Partitionen werden – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet.
* [Optional:] Filter zum Ausschließen der Integritätsstatistiken. Wenn er nicht angegeben wird, schließen die Integritätsstatistiken die Anzahl der Zustände „OK“, „Warnung“ und „Fehler“ für alle Partitionen und Replikate des Diensts ein.

### <a name="api"></a>API
Erstellen Sie zum Abrufen der Dienstintegrität über die API ein Element vom Typ `FabricClient` , und rufen Sie die [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) -Methode für das dazugehörige HealthManager-Element auf.

Im folgenden Beispiel wird die Integrität eines Diensts mit dem angegebenen Dienstnamen (URI) abgerufen:

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Mit dem folgenden Code wird die Dienstintegrität für den angegebenen Dienstnamen (URI) abgerufen. Filter und benutzerdefinierte Richtlinie werden mit [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription) angegeben:

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Dienstintegrität wird das Cmdlet [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Cmdlet wird die Dienstintegrität anhand von standardmäßigen Integritätsrichtlinien abgerufen.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Sie können die Dienstintegrität mit einer [GET-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) abrufen, die im Text beschriebene Integritätsrichtlinien enthält.

## <a name="get-partition-health"></a>Abrufen der Partitionsintegrität
Gibt die Integrität einer Partitionsentität zurück. Enthält die Angaben zum Replikatintegritätsstatus. Eingabe:

* [Erforderlich] Die Partitions-ID (GUID) zur Identifizierung der Partition.
* [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.
* [Optional] Filter für Ereignisse und Replikate, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Alle Ereignisse und Replikate werden – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet.
* [Optional:] Filter zum Ausschließen der Integritätsstatistiken. Wenn er nicht angegeben wird, wird in den Integritätsstatistiken angezeigt, wie viele Replikate die Zustände „OK“, „Warnung“ und „Fehler“ aufweisen.

### <a name="api"></a>API
Erstellen Sie zum Abrufen der Partitionsintegrität über die API ein Element vom Typ `FabricClient` , und rufen Sie die [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) -Methode für das dazugehörige HealthManager-Element auf. Erstellen Sie zum Angeben optionaler Parameter das Element [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Partitionsintegrität wird das Cmdlet [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Cmdlet wird die Integrität für alle Partitionen des Diensts **fabric:/WordCount/WordCountService** abgerufen, und die Integritätszustände von Replikaten werden ausgefiltert:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Sie können die Partitionsintegrität mit einer [GET-Anforderung](/rest/api/servicefabric/sfclient-api-getpartitionhealth) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) abrufen, die im Text beschriebene Integritätsrichtlinien enthält.

## <a name="get-replica-health"></a>Abrufen der Replikatintegrität
Gibt die Integrität eines zustandsbehafteten Dienstreplikats oder einer zustandslosen Dienstinstanz zurück. Eingabe:

* [Erforderlich] Die Partitions-ID (GUID) und die Replikat-ID zum Identifizieren des Replikats.
* [Optional] Die Anwendungsintegritätsrichtlinien-Parameter zum Überschreiben der Anwendungsmanifestrichtlinien.
* [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Alle Ereignisse – unabhängig vom Filter – werden zum Auswerten der aggregierten Entitätsintegrität verwendet.

### <a name="api"></a>API
Erstellen Sie zum Abrufen der Replikatintegrität über die API ein Element vom Typ `FabricClient` , und rufen Sie die [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) -Methode für das dazugehörige HealthManager-Element auf. Erweiterte Parameter können mit [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription)angegeben werden.

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Replikatintegrität wird das Cmdlet [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Cmdlet wird die Integrität des primären Replikats für alle Partitionen des Diensts abgerufen:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Sie können die Replikatintegrität mit einer [GET-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) abrufen, die im Text beschriebene Integritätsrichtlinien enthält.

## <a name="get-deployed-application-health"></a>Abrufen der Integrität der bereitgestellten Anwendung
Gibt die Integrität einer Anwendung zurück, die auf einer Knotenentität bereitgestellt wird. Enthält die Integritätsstatusangaben der bereitgestellten Dienstpakete. Eingabe:

* [Erforderlich] Der Anwendungsname (URI) und der Knotenname (Zeichenfolge) zum Identifizieren der bereitgestellten Anwendung.
* [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinien.
* [Optional] Filter für Ereignisse und bereitgestellte Dienstpakete, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Alle Ereignisse und bereitgestellten Dienste werden – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet.
* [Optional:] Filter zum Ausschließen der Integritätsstatistiken. Wenn er nicht angegeben wird, schließen die Integritätsstatistiken die Anzahl der Zustände „OK“, „Warnung“ und „Fehler“ für bereitgestellte Dienstpakete ein.

### <a name="api"></a>API
Um die Integrität für eine auf einem Knoten bereitgestellte Anwendung über die API abzurufen, erstellen Sie ein Element vom Typ `FabricClient` , und rufen Sie die [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) -Methode für das dazugehörige HealthManager-Element auf. Optionale Parameter können mit [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription)angegeben werden.

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Integrität von bereitgestellten Anwendungen wird das Cmdlet [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her. Führen Sie [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) aus, und ermitteln Sie anhand der untergeordneten Elemente der bereitgestellten Anwendung, wo eine Anwendung bereitgestellt wird.

Mit dem folgenden Cmdlet wird die Integrität der Anwendung **fabric:/WordCount** abgerufen, die auf **_Node_2** bereitgestellt ist.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Sie können die Integrität der bereitgestellten Anwendung mit einer [GET-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) abrufen, die im Text beschriebene Integritätsrichtlinien enthält.

## <a name="get-deployed-service-package-health"></a>Abrufen der Integrität von bereitgestellten Dienstpaketen
Gibt die Integrität einer bereitgestellten Dienstpaketentität zurück. Eingabe:

* [Erforderlich] Der Anwendungsname (URI), der Knotenname (Zeichenfolge) und der Dienstmanifestname (Zeichenfolge) zum Identifizieren des bereitgestellten Dienstpakets.
* [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.
* [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Alle Ereignisse – unabhängig vom Filter – werden zum Auswerten der aggregierten Entitätsintegrität verwendet.

### <a name="api"></a>API
Erstellen Sie zum Abrufen der Integrität eines bereitgestellten Dienstpakets über die API ein Element vom Typ `FabricClient` , und rufen Sie die [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) -Methode für das dazugehörige HealthManager-Element auf. Optionale Parameter können mit [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription)angegeben werden.

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Integrität bereitgestellter Dienstpakete wird das Cmdlet [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her. Führen Sie [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) aus, und ermitteln Sie anhand der bereitgestellten Anwendungen, wo eine Anwendung bereitgestellt wird. Sehen Sie sich die untergeordneten Elemente der bereitgestellten Dienstpakete in der Ausgabe von [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) an, um zu ermitteln, welche Dienstpakete in einer Anwendung enthalten sind.

Mit dem folgenden Cmdlet wird die Integrität des Dienstpakets **WordCountServicePkg** der Anwendung **fabric:/WordCount** abgerufen, die auf **_Node_2** bereitgestellt ist. Die Entität verfügt über **System.Hosting** -Berichte für die erfolgreiche Aktivierung von Dienstpaketen und Einstiegspunkten und die erfolgreiche Registrierung von Diensttypen.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Sie können die Integrität des bereitgestellten Dienstpakets mit einer [GET-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) abrufen, die im Text beschriebene Integritätsrichtlinien enthält.

## <a name="health-chunk-queries"></a>Integritätsblockabfragen
Integritätsblockabfragen können pro Eingabefilter rekursiv mehrstufige untergeordnete Clusterelemente zurückgeben. Sie unterstützen erweiterte Filter, mit denen ein hohes Maß an Flexibilität bei der Auswahl der zurückgegebenen untergeordneten Elemente erreicht werden kann. Die Filter können untergeordnete Elemente anhand ihres eindeutigen Bezeichners oder anderer Gruppen-IDs und/oder Integritätsstatus angeben. Im Gegensatz zu Integritätsbefehlen, bei denen immer untergeordnete Elemente der ersten Ebene einbezogen werden, werden hier standardmäßig keine untergeordneten Elemente einbezogen.

Die [Integritätsabfragen](service-fabric-view-entities-aggregated-health.md#health-queries) geben pro erforderlichem Filter nur untergeordnete Elemente der ersten Ebene der angegebenen Entität zurück. Zum Abrufen der untergeordneten Elemente weiterer Ebenen müssen Sie für die betreffenden Entitäten zusätzliche Integritäts-APIs aufrufen. Analog dazu müssen Sie jeweils eine API pro gewünschter Entität aufrufen, um die Integrität bestimmter Entitäten abzurufen. Mit der erweiterten Filterung der Blockabfrage können Sie mehrere Elemente in einer einzelnen Abfrage anfordern und so die Größe und Anzahl von Nachrichten verringern.

Der Vorteil der Blockabfrage besteht darin, dass Sie mit nur einem Aufruf den Integritätszustand mehrerer Clusterentitäten (potenziell sogar aller Clusterentitäten am erforderlichen Stamm) abrufen können. Dies ermöglicht komplexe Integritätsabfragen, die beispielsweise Folgendes bewirken:

* Es werden nur fehlerhafte Anwendungen zurückgegeben. Für diese Anwendungen werden alle Dienste mit den Zuständen „Warnung“ oder „Fehler“ eingeschlossen. Bei den zurückgegebenen Diensten werden alle Partitionen eingeschlossen.
* Es wird nur die Integrität von vier namentlich angegebenen Anwendungen zurückgegeben.
* Es wird nur die Integrität von Anwendungen eines bestimmten Anwendungstyps zurückgegeben.
* Es werden alle auf einem Knoten bereitgestellten Entitäten zurückgegeben. Gibt alle Anwendungen, alle auf dem angegebenen Knoten bereitgestellten Anwendungen und alle auf dem Knoten bereitgestellten Dienstpakete zurück.
* Es werden alle Replikate mit einem Fehlerstatus zurückgegeben. Dabei werden alle Anwendungen, Dienste, Partitionen und nur Replikate mit Fehlerstatus zurückgegeben.
* Es werden alle Anwendungen zurückgegeben. Für einen angegebenen Dienst werden alle Partitionen einbezogen.

Momentan wird die Integritätsblockabfrage nur für die Clusterentität verfügbar gemacht. Sie gibt einen Clusterintegritätsblock mit folgendem Inhalt zurück:

* Aggregierter Integritätszustand des Clusters.
* Integritätszustandsblock-Liste der Knoten, die Eingabefilter berücksichtigen.
* Integritätszustandsblock-Liste der Anwendungen, die Eingabefilter berücksichtigen. Jeder Anwendungs-Integritätszustandsblock enthält eine Blockliste mit allen Diensten, die Eingabefilter berücksichtigen, und eine Blockliste mit allen bereitgestellten Anwendungen, die die Filter berücksichtigen. Gleiches gilt für die untergeordneten Elemente von Diensten und bereitgestellten Anwendung. Dadurch können potenziell alle Entitäten im Cluster auf Anforderung hierarchisch zurückgegeben werden.

### <a name="cluster-health-chunk-query"></a>Cluster-Integritätsblockabfrage
Gibt die Integrität der Clusterentität zurück (einschließlich der hierarchischen Integritätszustandsblöcke der erforderlichen untergeordneten Elemente). Eingabe:

* [Optional] Die Clusterintegritätsrichtlinie zum Auswerten der Knoten und der Clusterereignisse.
* [Optional] Die Zuordnung der Anwendungsintegritätsrichtlinien mit den Integritätsrichtlinien zum Außerkraftsetzen der Richtlinien für das Anwendungsmanifest.
* [Optional] Filter für Knoten und Anwendungen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen. Die Filter gelten speziell für eine Entität/Entitätsgruppe oder für alle Entitäten auf dieser Ebene. Die Liste mit den Filtern kann einen allgemeinen Filter und/oder Filter für bestimmte Bezeichner enthalten, um die von der Abfrage zurückgegebenen Entitäten zu differenzieren. Ohne Angabe werden die untergeordneten Elemente standardmäßig nicht zurückgegeben.
  Weitere Informationen zu den Filtern finden Sie unter [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) sowie unter [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Mit den Anwendungsfiltern können rekursiv erweiterte Filter für untergeordnete Elemente angegeben werden.

Das Blockergebnis enthält die untergeordneten Elemente, die die Filter berücksichtigen.

Derzeit werden von der Blockabfrage keine fehlerhaften Auswertungen oder Entitätsereignisse zurückgegeben. Diese zusätzlichen Informationen können mit der vorhandenen Clusterintegritätsabfrage abgerufen werden.

### <a name="api"></a>API
Erstellen Sie zum Abrufen des Clusterintegritätsblocks ein Element vom Typ `FabricClient` , und rufen Sie die [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) -Methode für das dazugehörige Element vom Typ **HealthManager**auf. Integritätsrichtlinien und erweiterte Filter können durch Übergeben von [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) definiert werden.

Im folgenden Code wird ein Clusterintegritätsblock mit erweiterten Filtern abgerufen:

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Zum Abrufen der Clusterintegrität wird das Cmdlet [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk)verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Code werden Knoten nur dann abgerufen, wenn sie einen Fehlerstatus besitzen – mit Ausnahme eines bestimmten Knotens, der immer zurückgegeben werden soll:

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Das folgende Cmdlet ruft den Clusterblock mit Anwendungsfiltern ab:

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Das folgende Cmdlet gibt alle bereitgestellten Entitäten auf einem Knoten zurück:

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Sie können den Clusterintegritätsblock mit einer [GET-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) oder einer [POST-Anforderung](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) abrufen, die im Text beschriebene Integritätsrichtlinien und erweiterte Filter enthält.

## <a name="general-queries"></a>Allgemeine Abfragen
Mit den allgemeinen Abfragen wird die Liste der Service Fabric-Entitäten des angegebenen Typs zurückgegeben. Sie werden über die API (über die Methoden unter **FabricClient.QueryManager**), über PowerShell-Cmdlets und über REST verfügbar gemacht. Mit diesen Abfragen werden Unterabfragen aus mehreren Komponenten zusammengefasst. Eine davon ist der [Integritätsspeicher](service-fabric-health-introduction.md#health-store), in dem der aggregierte Integritätszustand für jedes Abfrageergebnis aufgefüllt wird.  

> [!NOTE]
> Mit den allgemeinen Abfragen wird der aggregierte Integritätsstatus der Entität zurückgegeben. Sie enthalten keine umfassenden Integritätsdaten. Wenn eine Entität fehlerhaft ist, können Sie mit Integritätsabfragen alle Informationen zur Integrität abrufen, z. B. Ereignisse, Integritätsstatusangaben untergeordneter Elemente und Fehlerauswertungen.
>
>

Wenn allgemeine Abfragen einen unbekannten Integritätsstatus für eine Entität zurückgeben, ist es möglich, dass der Integritätsspeicher keine vollständigen Daten über die Entität besitzt. Es ist auch möglich, dass eine Unterabfrage an den Integritätsspeicher nicht erfolgreich war (z. B. ein Kommunikationsfehler aufgetreten ist oder der Integritätsspeicher gedrosselt wurde). Verwenden Sie danach eine Integritätsabfrage für die Entität. Wenn bei der Unterabfrage vorübergehende Fehler, beispielsweise Netzwerkfehler, aufgetreten sind, ist diese nachfolgende Abfrage möglicherweise erfolgreich. Sie kann Ihnen auch weitere Details aus dem Integritätsspeicher zu den Gründen bereitstellen, aus denen die Entität nicht verfügbar gemacht wird.

Die Abfragen, die **HealthState** für Entitäten enthalten, lauten:

* Node list: Gibt die (auf Seiten aufgeteilte) Liste mit den Knoten im Cluster zurück.
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Application list: Gibt die (auf Seiten aufgeteilte) Liste mit den Anwendungen im Cluster zurück.
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Service list: Gibt die (auf Seiten aufgeteilte) Liste mit den Diensten einer Anwendung zurück.
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partition list: Gibt die (auf Seiten aufgeteilte) Liste mit den Partitionen eines Diensts zurück.
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Replica list: Gibt die (auf Seiten aufgeteilte) Liste mit den Replikaten in einer Partition zurück.
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Deployed application list: Gibt die Liste mit den bereitgestellten Anwendungen eines Knotens zurück.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Deployed service package list: Gibt die Liste mit den Dienstpaketen einer bereitgestellten Anwendung zurück.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Einige Abfragen geben unter Umständen auf Seiten aufgeteilte Ergebnisse zurück. Die Rückgabe dieser Abfragen ist eine von [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1) abgeleitete Liste. Falls die Ergebnisse nicht in eine Nachricht passen, wird nur eine Seite zurückgegeben, und mit einem ContinuationToken (Fortsetzungstoken) wird nachverfolgt, wo die Enumeration beendet wurde. Wiederholen Sie die gleiche Abfrage, und übergeben Sie dabei das Fortsetzungstoken aus der vorherigen Abfrage, um weitere Ergebnisse zu erhalten.

### <a name="examples"></a>Beispiele
Mit dem folgenden Code werden die fehlerhaften Anwendungen im Cluster abgerufen:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Mit dem folgenden Cmdlet werden die Anwendungsdetails für die Anwendung „fabric:/WordCount“ abgerufen. Beachten Sie, dass der Integritätsstatus „Warnung“ lautet.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Mit dem folgenden Cmdlet werden die Dienste mit dem Integritätsstatus „Fehler“ abgerufen:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Cluster- und Anwendungsupgrades
Während eines überwachten Upgrades von Cluster und Anwendung wird von Service Fabric die Integrität überprüft, um sicherzustellen, dass alles fehlerfrei bleibt. Wenn eine Entität von den konfigurierten Integritätsrichtlinien als fehlerhaft ausgewertet wird, wendet das Upgrade spezifische Richtlinien an, um die nächste Aktion festzulegen. Das Upgrade kann angehalten werden, um eine Benutzerinteraktion (z. B. das Beheben von Fehlerbedingungen oder das Ändern von Richtlinien) zu ermöglichen, oder es kann automatisch einen Rollback auf die vorherige, einwandfreie Version durchführen.

Während des Upgrades eines *Clusters* können Sie den Clusterupgradestatus abrufen. Zum Upgradestatus gehören auch Fehlerauswertungen, die anzeigen, was im Cluster fehlerhaft ist. Falls für das Upgrade aufgrund von Integritätsproblemen ein Rollback durchgeführt wird, werden im Upgradestatus die letzten Gründe für Fehler beibehalten und können abgerufen werden. Mit diesen Informationen können Administratoren untersuchen, was falsch gelaufen ist, nachdem das Upgrade zurückgesetzt oder angehalten wurde.

Ebenso enthält der Anwendungsupgradestatus beim Upgrade einer *Anwendung* die entsprechenden Fehlerauswertungen.

Im folgenden Beispiel wird der Anwendungsupgradestatus für die geänderte Anwendung „fabric:/WordCount“ veranschaulicht. Ein Watchdog-Element hat für eines der Replikate einen Fehler gemeldet. Für das Upgrade wird ein Rollback durchgeführt, weil die Integritätsüberprüfungen nicht berücksichtigt wurden.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Erfahren Sie mehr über das [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Verwenden von Integritätsauswertungen zur Problembehandlung
Wenn im Cluster oder in einer Anwendung ein Problem vorliegt, können Sie sich die Integrität des Clusters oder der Anwendung ansehen, um die Ursache zu ermitteln. In den Fehlerauswertungen werden Details zur Ursache bereitgestellt, durch die der aktuelle Fehlerstatus ausgelöst wurde. Nach Bedarf können Sie ein Detailinformationen zu den fehlerhaften untergeordneten Entitäten anzeigen, um die Ursache zu ermitteln.

Betrachten Sie beispielsweise eine Anwendung als fehlerhaft, da ein Fehlerbericht zu einem ihrer Replikate vorhanden ist. Das folgende PowerShell-Cmdlet zeigt die Fehlerauswertungen an:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Sie können das Replikat untersuchen, um weitere Informationen zu erhalten:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> Die Fehlerauswertungen geben Aufschluss über den ersten Grund für den aktuellen Integritätszustand der Entität. Der Zustand ist unter Umständen auf mehrere andere Ereignisse zurückzuführen, aber diese werden bei der Auswertung nicht berücksichtigt. Sehen Sie sich die Detailinformationen der Integritätsentitäten an, um mehr Informationen zu erhalten und alle Fehlerberichte im Cluster zu ermitteln.
>
>

## <a name="next-steps"></a>Nächste Schritte
[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten](service-fabric-report-health.md)

[Melden und Überprüfen der Dienstintegrität](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)
