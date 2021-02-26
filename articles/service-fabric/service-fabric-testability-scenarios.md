---
title: Erstellen von Chaos- und Failovertests für Azure Service Fabric
description: Mithilfe der Chaos- und Failovertestszenarien von Service Fabric können Sie Fehler herbeiführen und die Zuverlässigkeit Ihrer Dienste überprüfen.
author: georgewallace
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: 78e613e9d86c44ad7daf8778b35e6af49abb7d38
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546362"
---
# <a name="testability-scenarios"></a>Testability-Szenarien
Große verteilte Systeme, z. B. Cloudinfrastrukturen, sind häufig unzuverlässig. Mit Azure Service Fabric können Entwickler Dienste schreiben, die für unzuverlässige Infrastrukturen ausgeführt werden. Um hochwertige Dienste schreiben zu können, müssen Entwickler dazu in der Lage sein, diese unzuverlässigen Infrastrukturen auszulösen, um die Stabilität ihrer Dienste testen zu können.

Der Fehleranalysedienst gibt Entwicklern die Möglichkeit, Fehleraktionen auszulösen, um das Verhalten von Diensten beim Auftreten von Fehlern zu testen. Mit zielgerichteten simulierten Fehlern können Sie aber nicht alle Eventualitäten abdecken. Um den Testumfang zu erweitern, können Sie die Testszenarien in Service Fabric verwenden: einen Chaostest und einen Failovertest. Mit diesen Szenarien werden im Cluster über längere Zeiträume hinweg fortlaufende überlappende Fehler simuliert (sowohl ordnungsgemäß als auch nicht ordnungsgemäß). Nachdem die Rate und die Art der Fehler für einen Test konfiguriert wurde, kann er ausgeführt werden. Hierbei werden entweder die C#-APIs oder PowerShell genutzt, um Fehler im Cluster und in Ihrem Dienst zu generieren.

> [!WARNING]
> „ChaosTestScenario“ wird durch einen stabileren, dienstbasierten Chaostest ersetzt. Im neuen Artikel zum [kontrollierten Chaos](service-fabric-controlled-chaos.md) finden Sie weitere Details.
> 
> 

## <a name="chaos-test"></a>Chaostest
Beim Chaosszenario werden Fehler im gesamten Service Fabric-Cluster generiert. Fehler, die normalerweise in Zeiträumen von mehreren Monaten oder Jahren auftreten, werden auf wenige Stunden komprimiert. Bei dieser Kombination aus überlappenden Fehlern mit der hohen Fehlerrate werden auch Spezialfälle erkannt, die sonst nicht auffallen. Dies führt zu einer erheblichen Verbesserung bei der Codequalität des Diensts.

### <a name="faults-simulated-in-the-chaos-test"></a>Beim Chaostest simulierte Fehler
* Neustart eines Knotens
* Neustart eines bereitgestellten Codepakets
* Entfernung eines Replikats
* Neustart eines Replikats
* Verschiebung eines primären Replikats (optional)
* Verschiebung eines sekundären Replikats (optional)

Beim Chaostest werden im angegebenen Zeitraum mehrere Iterationen von Fehlern und Clusterüberprüfungen durchgeführt. Die Dauer der Stabilisierung des Clusters und des erfolgreichen Abschlusses der Überprüfung kann ebenfalls konfiguriert werden. Das Szenario ist nicht erfolgreich, wenn es bei der Clusterüberprüfung zu einem Einzelfehler kommt.

Nehmen wir beispielsweise an, ein Test soll eine Stunde lang ausgeführt werden und maximal drei gleichzeitige Fehler umfassen. Während des Tests werden drei Fehler ausgelöst, und anschließend wird die Clusterintegrität überprüft. Der Test durchläuft den vorherigen Schritt so lange, bis die Integrität des Clusters nicht mehr gewährleistet oder eine Stunde vergangen ist. Falls die Integrität des Clusters bei einer Iteration nicht mehr besteht, also keine Stabilisierung innerhalb eines konfigurierten Zeitraums möglich ist, ist der Test nicht erfolgreich, und es tritt eine Ausnahme auf. Mit der Ausnahme wird angegeben, dass ein Fehler aufgetreten ist, der weiter untersucht werden muss.

In seiner derzeitigen Form enthält die Fehlergenerierungs-Engine des Chaostests nur sichere Fehler. Dies bedeutet, dass bei einem Nichtvorhandensein von externen Fehlern niemals ein Quorum- oder Datenverlust auftritt.

### <a name="important-configuration-options"></a>Wichtige Konfigurationsoptionen
* **TimeToRun**: Gesamtdauer der Ausführung des Tests, bevor er erfolgreich abgeschlossen wird. Beim Auftreten eines Überprüfungsfehlers kann der Test auch früher abgeschlossen werden.
* **MaxClusterStabilizationTimeout**: Maximaler Zeitraum, wie lange auf die Wiederherstellung der Integrität des Clusters gewartet wird, bevor der Test als nicht erfolgreich gewertet wird. Folgende Überprüfungen werden durchgeführt: ob Clusterintegrität besteht, ob Dienstintegrität besteht, ob für die Dienstpartition die Größe der Zielreplikatgruppe erreicht wird und ob keine InBuild-Replikate vorhanden sind.
* **MaxConcurrentFaults**: Maximale Anzahl von gleichzeitigen Fehlern, die bei jeder Iteration ausgelöst werden. Je höher die Anzahl ist, desto aggressiver ist der Test. Dies führt zu komplexeren Failovern und Übergangskombinationen. Mit dem Test wird garantiert, dass es bei einem Nichtvorhandensein von externen Fehlern nicht zu einem Quorum- oder Datenverlust kommt. Dabei spielt es keine Rolle, wie hoch der Wert für diese Konfiguration gewählt wurde.
* **EnableMoveReplicaFaults**: Aktiviert oder deaktiviert die Fehler, die zur Verschiebung der primären oder sekundären Replikate führen. Diese Fehler sind standardmäßig deaktiviert.
* **WaitTimeBetweenIterations**: Gibt an, wie lange zwischen Iterationen gewartet wird (also nach einer Fehlerrunde und der entsprechenden Überprüfung).

### <a name="how-to-run-the-chaos-test"></a>Ausführen des Chaostests
C#-Beispiel

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

Das Service Fabric PowerShell-Modul bietet zwei Möglichkeiten zum Start eines Chaos-Szenarios. Da `Invoke-ServiceFabricChaosTestScenario` clientbasiert ist, werden keine weiteren Fehler eingefügt, falls der Client-Computer während des Tests heruntergefahren wird. Ein alternativer Befehlssatz ist vorhanden, der den Test weiterlaufen lässt, falls der Computer heruntergefahren wird. `Start-ServiceFabricChaos` nutzt einen zustandsbehafteten und zuverlässigen Systemdienst namens FaultAnalysisService, um zu gewährleisten, dass bis zum Ablauf der TimeToRun weitere Fehler eingefügt werden. `Stop-ServiceFabricChaos` kann zum manuellen Abbruch des Szenarios verwendet werden, und `Get-ServiceFabricChaosReport` generiert einen Bericht. Weitere Informationen finden Sie unter [Azure Service Fabric PowerShell-Referenz](/powershell/module/servicefabric/) und [In Microsoft Azure Service Fabric-Clustern kontrolliertes Chaos erzeugen](service-fabric-controlled-chaos.md).

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Failovertest
Das Failovertestszenario ist eine Version des Chaostestszenarios für eine bestimmte Dienstpartition. Damit werden die Auswirkungen eines Failovers auf eine bestimmte Dienstpartition getestet, während die anderen Dienste nicht beeinträchtigt werden. Nach der Konfiguration mit den Informationen zur Zielpartition und anderen Parametern wird es entweder mit C#-APIs oder PowerShell als clientseitiges Tool ausgeführt, um Fehler für eine Dienstpartition zu generieren. Bei diesem Szenario werden eine Sequenz von simulierten Fehlern und die Dienstüberprüfung durchlaufen, während Ihre Geschäftslogik im Hintergrund ausgeführt wird, damit eine Workload vorhanden ist. Ein Fehler bei der Dienstüberprüfung weist auf ein Problem hin, das genauer untersucht werden muss.

### <a name="faults-simulated-in-the-failover-test"></a>Beim Failovertest simulierte Fehler
* Neustart eines bereitgestellten Codepakets, mit dem die Partition gehostet wird
* Entfernung eines primären bzw. sekundären Replikats oder einer zustandslosen Instanz
* Neustart eines primären bzw. sekundären Replikats (bei einem dauerhaften Dienst)
* Verschiebung eines primären Replikats
* Verschiebung eines sekundären Replikats
* Neustart der Partition

Beim Failovertest wird ein ausgewählter Fehler ausgelöst, und anschließend wird die Überprüfung für den Dienst durchgeführt, um dessen Stabilität sicherzustellen. Beim Failovertest wird nur jeweils ein Fehler ausgelöst, während beim Chaostest mehrere Fehler ausgelöst werden können. Wenn die Dienstpartition nach jedem Fehler nicht innerhalb des konfigurierten Zeitraums stabilisiert wird, ist der Test nicht erfolgreich. Beim Test werden nur sichere Fehler ausgelöst. Dies bedeutet, dass bei einem Nichtvorhandensein von externen Fehlern kein Quorum- oder Datenverlust auftritt.

### <a name="important-configuration-options"></a>Wichtige Konfigurationsoptionen
* **PartitionSelector**: Selektorobjekt zum Angeben der gewünschten Partition.
* **TimeToRun**: Gesamtdauer der Ausführung des Tests, bevor er abgeschlossen wird.
* **MaxServiceStabilizationTimeout**: Maximaler Zeitraum, wie lange auf die Wiederherstellung der Integrität des Clusters gewartet wird, bevor der Test als nicht erfolgreich gewertet wird. Folgende Überprüfungen werden durchgeführt: ob Dienstintegrität besteht, ob für die Dienstpartition die Größe der Zielreplikatgruppe erreicht wird und ob keine InBuild-Replikate vorhanden sind.
* **WaitTimeBetweenFaults**: Gibt an, wie lange zwischen den einzelnen Fehler- und Überprüfungszyklen gewartet wird.

### <a name="how-to-run-the-failover-test"></a>Ausführen des Failovertests
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
