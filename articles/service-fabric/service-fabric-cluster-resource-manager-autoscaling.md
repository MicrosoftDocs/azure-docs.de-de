---
title: Automatisches Skalieren von Diensten und Containern mit Azure Service Fabric | Microsoft-Dokumentation
description: Azure Service Fabric ermöglicht das Festlegen von Richtlinien für die automatische Skalierung von Diensten und Containern.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: nipuzovi
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 8e57c071c9fd93a8581d574aeec2b23b38b3ab95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844022"
---
# <a name="introduction-to-auto-scaling"></a>Einführung in die automatische Skalierung
Die automatische Skalierung ist eine weitere Funktion von Service Fabric, mit der Ihre Dienste basierend auf der Last, die von Diensten gemeldet wird, oder basierend auf ihrer Ressourcennutzung dynamisch skaliert werden können. Die automatische Skalierung bietet eine hervorragende Flexibilität und ermöglicht die Bereitstellung zusätzlicher Instanzen oder Partitionen Ihres Diensts bei Bedarf. Der gesamte Prozess der automatischen Skalierung ist automatisiert und transparent. Sobald Sie Ihre Richtlinien für einen Dienst eingerichtet haben, müssen Sie keine manuellen Skalierungsvorgänge auf Dienstebene mehr durchführen. Die automatische Skalierung kann während der Erstellung des Diensts oder zu einem beliebigen Zeitpunkt durch Aktualisieren des Diensts aktiviert werden.

Ein häufiges Szenario, in dem die automatische Skalierung hilfreich ist, ist, wenn die Last eines bestimmten Diensts mit der Zeit schwankt. Beispielsweise kann ein Dienst wie ein Gateway basierend auf dem Umfang an Ressourcen skaliert werden, der zum Verarbeiten eingehender Anforderungen erforderlich ist. Sehen wir uns ein Beispiel für diese Skalierungsregeln an:
* Wenn alle Instanzen des Gateways durchschnittlich mehr als zwei Kerne verwenden, skalieren Sie den Gatewaydienst horizontal hoch, indem Sie eine weitere Instanz hinzufügen. Tun Sie dies stündlich, die Instanzenanzahl sollte jedoch nie über sieben liegen.
* Wenn alle Instanzen des Gateways im Durchschnitt weniger als 0,5 Kerne verwenden, skalieren Sie den Dienst durch das Entfernen einer Instanz horizontal herunter. Tun Sie dies stündlich, die Instanzenanzahl sollte jedoch nie unter drei liegen.

Die automatische Skalierung wird für Container und für reguläre Service Fabric-Dienste unterstützt. Um die automatische Skalierung zu verwenden, müssen Sie mindestens Version 6.2 der Service Fabric-Runtime ausführen. 

Im weiteren Verlauf dieses Artikels werden die Skalierungsrichtlinien und Verfahren zum Aktivieren oder Deaktivieren der automatischen Skalierung beschrieben. Außerdem werden Beispiele zur Verwendung dieses Features aufgeführt.

## <a name="describing-auto-scaling"></a>Beschreibung der automatischen Skalierung
Richtlinien für die automatische Skalierung können für jeden Dienst in einem Service Fabric-Cluster definiert werden. Jede Skalierungsrichtlinie besteht aus zwei Teilen:
* Der **Skalierungstrigger** beschreibt, wann die Skalierung des Diensts ausgeführt wird. Bedingungen, die im Trigger definiert sind, werden regelmäßig überprüft, um festzustellen, ob ein Dienst skaliert werden muss.

* Der **Skalierungsmechanismus** beschreibt, wie die Skalierung ausgeführt wird, wenn sie ausgelöst wurde. Der Mechanismus wird nur angewendet, wenn die Bedingungen des Triggers erfüllt sind.

Alle Trigger, die derzeit unterstützt werden, arbeiten entweder mit [logischen Lastmetriken](service-fabric-cluster-resource-manager-metrics.md) oder mit physischen Metriken wie CPU-oder Arbeitsspeicherauslastung. In beiden Fällen überwacht Service Fabric die gemeldete Last für die Metrik und wertet den Trigger in regelmäßigen Abständen aus, um festzustellen, ob eine Skalierung erforderlich ist.

Es gibt zwei Mechanismen, die derzeit für die automatische Skalierung unterstützt werden. Der erste ist für zustandslose Dienste oder für Container vorgesehen, bei denen die automatische Skalierung ausgeführt wird, indem [Instanzen](service-fabric-concepts-replica-lifecycle.md) hinzugefügt oder entfernt werden. Für zustandsbehaftete und zustandslose Dienste kann die automatische Skalierung auch ausgeführt werden, indem benannte [Partitionen](service-fabric-concepts-partitioning.md) des Diensts hinzugefügt oder entfernt werden.

> [!NOTE]
> Derzeit wird nur eine Skalierungsrichtlinie pro Dienst und nur ein Skalierungsauslöser pro Skalierungsrichtlinie unterstützt.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Trigger für die durchschnittliche Partitionslast mit instanzbasierter Skalierung
Der erste Triggertyp basiert auf der Last von Instanzen in einer zustandslosen Dienstpartition. Metriklasten werden zuerst geglättet, um die Last für jede Instanz einer Partition zu erhalten. Dann wird der Mittelwert dieser Werte über alle Instanzen der Partition gebildet. Es gibt drei Faktoren, die bestimmen, wann der Dienst skaliert wird:

* Der _untere Lastschwellenwert_ ist ein Wert, der bestimmt, wann der Dienst **horizontal herunterskaliert wird**. Wenn die durchschnittliche Last aller Instanzen der Partitionen niedriger als dieser Wert ist, wird der Dienst horizontal herunterskaliert.
* Der _obere Lastschwellenwert_ ist ein Wert, der bestimmt, wann der Dienst **horizontal hochskaliert wird**. Wenn die durchschnittliche Last aller Instanzen der Partition höher als dieser Wert ist, wird der Dienst horizontal hochskaliert.
* Das _Skalierungsintervall_ bestimmt, wie oft der Trigger überprüft wird. Sobald der Trigger überprüft wurde, wird der Mechanismus angewendet, wenn eine Skalierung erforderlich ist. Wenn keine Skalierung erforderlich ist, wird keine Aktion ausgeführt. In beiden Fällen wird der Trigger erst wieder überprüft, wenn das Skalierungsintervall erneut abläuft.

Dieser Trigger kann nur für zustandslose Dienste (zustandslose Container oder Service Fabric-Dienste) verwendet werden. Wenn ein Dienst über mehrere Partitionen verfügt, wird der Trigger separat für jede Partition ausgewertet, und auf jede Partition wird der angegebene Mechanismus unabhängig angewendet. In diesem Fall ist es daher möglich, dass gleichzeitig einige Partitionen des Diensts basierend auf ihrer Last horizontal hochskaliert werden, einige horizontal herunterskaliert werden und einige überhaupt nicht skaliert werden.

Der einzige Mechanismus, der mit diesem Trigger verwendet werden kann, ist „PartitionInstanceCountScaleMechanism“. Es gibt drei Faktoren, die bestimmen, wie dieser Mechanismus angewendet wird:
* Das _Skalierungsinkrement_ bestimmt, wie viele Instanzen hinzugefügt oder entfernt werden, wenn der Mechanismus ausgelöst wird.
* Die _maximale Anzahl von Instanzen_ definiert die Obergrenze für die Skalierung. Wenn die Anzahl von Instanzen der Partition diesen Grenzwert erreicht, wird der Dienst unabhängig von der Last nicht horizontal hochskaliert. Es ist möglich, diesen Grenzwert durch die Angabe des Werts „-1“ zu übergehen. In diesem Fall wird der Dienst so weit wie möglich horizontal hochskaliert (der Grenzwert ist die Anzahl von Knoten, die im Cluster verfügbar sind).
* Die _minimale Anzahl von Instanzen_ definiert die Untergrenze für die Skalierung. Wenn die Anzahl von Instanzen der Partition diesen Grenzwert erreicht, wird der Dienst unabhängig von der Last nicht horizontal herunterskaliert.

## <a name="setting-auto-scaling-policy"></a>Festlegen der Richtlinie für die automatische Skalierung

### <a name="using-application-manifest"></a>Verwenden des Anwendungsmanifests
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Verwenden von C#-APIs
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Verwenden von PowerShell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Trigger für die durchschnittliche Dienstlast mit partitionsbasierter Skalierung
Der zweite Trigger basiert auf der Last aller Partitionen eines Diensts. Metriklasten werden zuerst geglättet, um die Last für jedes Replikat oder jede Instanz einer Partition zu erhalten. Bei zustandsbehafteten Diensten wird die Last der Partition als die Last des primären Replikats angesehen. Bei zustandslosen Diensten ist dagegen die Last der Partition die durchschnittliche Last aller Instanzen der Partition. Diese Werte werden für alle Partitionen des Diensts gemittelt, und dieser Wert wird verwendet, um die automatische Skalierung auszulösen. Wie beim vorherigen Mechanismus gibt es drei Faktoren, die bestimmen, wann der Dienst skaliert wird:

* Der _untere Lastschwellenwert_ ist ein Wert, der bestimmt, wann der Dienst **horizontal herunterskaliert wird**. Wenn die durchschnittliche Last aller Partitionen des Diensts niedriger als dieser Wert ist, wird der Dienst horizontal herunterskaliert.
* Der _obere Lastschwellenwert_ ist ein Wert, der bestimmt, wann der Dienst **horizontal hochskaliert wird**. Wenn die durchschnittliche Last aller Partitionen des Diensts höher als dieser Wert ist, wird der Dienst horizontal hochskaliert.
* Das _Skalierungsintervall_ bestimmt, wie oft der Trigger überprüft wird. Sobald der Trigger überprüft wurde, wird der Mechanismus angewendet, wenn eine Skalierung erforderlich ist. Wenn keine Skalierung erforderlich ist, wird keine Aktion ausgeführt. In beiden Fällen wird der Trigger erst wieder überprüft, wenn das Skalierungsintervall erneut abläuft.

Dieser Trigger kann für zustandsbehaftete und zustandslose Dienste verwendet werden. Der einzige Mechanismus, der mit diesem Trigger verwendet werden kann, ist „AddRemoveIncrementalNamedPartitionScalingMechanism“. Wenn der Dienst horizontal hochskaliert wird, wird eine neue Partition hinzugefügt, und wenn der Dienst horizontal herunterskaliert wird, wird eine vorhandene Partition entfernt. Es gibt Einschränkungen, die überprüft werden, wenn der Dienst erstellt oder aktualisiert wird, und die Erstellung/Aktualisierung des Diensts schlägt fehl, wenn diese Bedingungen nicht erfüllt sind:
* Ein benanntes Partitionsschema muss für den Dienst verwendet werden.
* Partitionsnamen müssen aufeinanderfolgende ganze Zahlen sein, z.B. „0“, „1“, ...
* Der Name der ersten Partition muss „0“ sein.

Beispiel: Wenn ein Dienst anfänglich mit drei Partitionen erstellt wird, ist die einzige gültige Möglichkeit für Partitionsnamen „0“, „1“ und „2“.

Bei der eigentlichen automatischen Skalierung, die ausgeführt wird, wird dieses Benennungsschema ebenfalls berücksichtigt:
* Wenn die aktuellen Partitionen des Diensts „0“, „1“ und „2“ genannt wurden, wird die Partition, die beim horizontalen Hochskalieren hinzugefügt wird, „3“ genannt.
* Wenn die aktuellen Partitionen des Diensts „0“, „1“ und „2“ genannt wurden, ist die Partition, die beim horizontalen Herunterskalieren entfernt wird, die Partition mit dem Namen „2“.

Wie beim Mechanismus, der die Skalierung durch Hinzufügen oder Entfernen von Instanzen ausführt, gibt es drei Parameter, die bestimmen, wie dieser Mechanismus angewendet wird:
* Das _Skalierungsinkrement_ bestimmt, wie viele Partitionen hinzugefügt oder entfernt werden, wenn der Mechanismus ausgelöst wird.
* Die _maximale Anzahl von Partitionen_ definiert die Obergrenze für die Skalierung. Wenn die Anzahl von Partitionen des Diensts diesen Grenzwert erreicht, wird der Dienst unabhängig von der Last nicht horizontal hochskaliert. Es ist möglich, diesen Grenzwert durch die Angabe des Werts „-1“ zu übergehen. In diesem Fall wird der Dienst so weit wie möglich horizontal hochskaliert (der Grenzwert ist die tatsächliche Kapazität des Clusters).
* Die _minimale Anzahl von Instanzen_ definiert die Untergrenze für die Skalierung. Wenn die Anzahl von Partitionen des Diensts diesen Grenzwert erreicht, wird der Dienst unabhängig von der Last nicht horizontal herunterskaliert.

> [!WARNING] 
> Bei Verwendung von „AddRemoveIncrementalNamedPartitionScalingMechanism“ mit „stateful“-Diensten wird Service Fabric Partitionen **ohne Benachrichtigung oder Warnung** hinzufügen oder entfernen. Die Neupartitionierung von Daten wird nicht durchgeführt, wenn ein Skalierungsmechanismus ausgelöst wird. Beim zentralen Hochskalieren sind die neuen Partitionen leer, und beim zentralen Herunterskalieren wird die **Partition zusammen mit allen darin enthaltenen Daten gelöscht**.

## <a name="setting-auto-scaling-policy"></a>Festlegen der Richtlinie für die automatische Skalierung

### <a name="using-application-manifest"></a>Verwenden des Anwendungsmanifests
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Verwenden von C#-APIs
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Verwenden von PowerShell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatische Skalierung basierend auf Ressourcen

Damit der Ressourcenmonitor basierend auf tatsächliche Ressourcen skalieren kann

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Es gibt zwei Metriken, die tatsächliche physische Ressourcen darstellen. Eine davon ist „servicefabric:/_CpuCores“, die die tatsächliche CPU-Auslastung darstellt (0,5 entspricht also der Hälfte eines Kerns), und die andere ist „servicefabric:/_MemoryInMB“, die die Arbeitsspeicherauslastung in MB darstellt.
„ResourceMonitorService“ ist verantwortlich für das Nachverfolgen der CPU- und Arbeitsspeicherauslastung von Benutzerdiensten. Dieser Dienst wendet einen gewichteten gleitenden Durchschnitt an, um potenzielle kurzlebige Spitzen zu berücksichtigen. Die Ressourcenüberwachung wird für Containeranwendungen und Nicht-Containeranwendungen unter Windows und für Containeranwendungen unter Linux unterstützt. Die automatische Skalierung von Ressourcen ist nur für Dienste aktiviert, die im [Modell mit einem exklusiven Prozess](service-fabric-hosting-model.md#exclusive-process-model) aktiviert sind.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die [Skalierbarkeit von Anwendungen](service-fabric-concepts-scalability.md).
