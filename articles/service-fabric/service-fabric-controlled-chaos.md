---
title: Auslösen von Chaos in Service Fabric-Clustern
description: Verwenden von Fault Injection und Cluster Analysis Service-APIs zum Verwalten von Chaostests im Cluster.
author: georgewallace
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: 727e0c8a738258434036652dd3df1f686f7433bb
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546379"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Auslösen von kontrolliertem Chaos in Service Fabric-Clustern
Große verteilte Systeme wie Cloudinfrastrukturen sind grundsätzlich unzuverlässig. Azure Service Fabric ermöglicht Entwicklern, aufbauend auf einer unzuverlässigen Infrastruktur zuverlässige verteilte Dienste zu erstellen. Um robuste, verteilte Dienste in einer unzuverlässigen Infrastruktur zu schreiben, müssen Entwickler in der Lage sein, die Stabilität ihrer Dienste zu testen, während die zugrunde liegende unzuverlässige Infrastruktur komplizierte Statusübergänge aufgrund von Fehlern durchläuft.

Der [Fault Injection and Cluster Analysis Service](./service-fabric-testability-overview.md) (auch als Fault Analysis Service (FAS) bezeichnet) gibt Entwicklern die Möglichkeit, Fehler einzuschleusen, um ihre Dienste zu testen. Solche zielgerichteten simulierten Fehler, wie z.B. das [Neustarten einer Partition](/powershell/module/servicefabric/start-servicefabricpartitionrestart), können dazu beitragen, die am häufigsten auftretenden Zustandsübergänge zu üben. Zielgerichtete simulierte Fehler sind jedoch definitionsgemäß nicht objektiv und können damit keine Fehler aufzeigen, die nur bei schwierig vorherzusagenden, langen und komplizierten Abfolgen von Zustandsübergängen auftreten. Für ausgewogene Tests können Sie Chaos verwenden.

Mithilfe von Chaos werden im Cluster über längere Zeiträume hinweg periodische verschachtelte Fehler simuliert (sowohl ordnungsgemäß als auch nicht ordnungsgemäß). Ein ordnungsgemäßer Fehler besteht aus einer Reihe von Aufrufen der Service Fabric-API. Beispielsweise ist der Fehler „Replikat neu starten“ ein ordnungsgemäßer Fehler, da hier auf das Schließen das Öffnen eines Replikats folgt. „Replikat entfernen“, „primäres Replikat verschieben“ und „sekundäres Replikat verschieben“ sind andere ordnungsgemäße Fehler, die von Chaos angewendet werden. Nicht ordnungsgemäße Fehler sind Prozessabbrüche wie „Knoten neu starten“ und „Codepaket neu starten“. 

Nachdem Sie Chaos mit der Rate und Art der Fehler konfiguriert haben, können Sie Chaos über die C#-, PowerShell- oder REST-API starten, um Fehler im Cluster und in Ihren Diensten zu generieren. Sie können Chaos für die Ausführung über einen angegebenen Zeitraum (z. B. für eine Stunde) konfigurieren. Chaos wird danach automatisch beendet. Sie können aber auch jederzeit die StopChaos-API (C#, PowerShell oder REST) aufrufen, um die Ausführung zu beenden.

> [!NOTE]
> In der derzeitigen Form löst Chaos nur sichere Fehler aus. Dies bedeutet, dass bei einem Nichtvorhandensein von externen Fehlern niemals ein Quorum- oder Datenverlust auftritt.
>

Während dieses Chaostests, werden verschiedene Ereignisse erzeugt, die den Status der Ausführung im jeweiligen Moment erfassen. Beispielsweise enthält ein ExecutingFaultsEvent alle Fehler, die Chaos im Rahmen der Iteration ausführt. Ein ValidationFailedEvent enthält die Details eines Überprüfungsfehlers (Integritäts- oder Stabilitätsprobleme), der während der Überprüfung des Clusters gefunden wurde. Sie können die GetChaosReport-API (C#, PowerShell oder REST) aufrufen, um einen Bericht zu Chaos-Ausführungen zu erhalten. Diese Ereignisse werden in ein [Reliable Dictionary](./service-fabric-reliable-services-reliable-collections.md) übernommen, für das eine Kürzungsrichtlinie mit zwei Konfigurationen gilt: **MaxStoredChaosEventCount** (der Standardwert ist 25.000) und **StoredActionCleanupIntervalInSeconds** (der Standardwert ist 3.600). Alle *StoredActionCleanupIntervalInSeconds* führt Chaos eine Überprüfung durch und entfernt alle Ereignisse bis auf die letzten *MaxStoredChaosEventCount* Ereignisse aus dem Reliable Dictionary dauerhaft.

## <a name="faults-induced-in-chaos"></a>Im Chaostest ausgelöste Fehler
Der Chaostest generiert Fehler im gesamten Service Fabric-Cluster und komprimiert in Monaten oder Jahren auftretende Fehler zu einigen wenigen Stunden. Bei dieser Kombination aus verschachtelten Fehlern mit der hohen Fehlerrate werden auch Spezialfälle erkannt, die sonst nicht auffallen. Dieser Chaostest führt zu einer erheblichen Verbesserung der Codequalität des Diensts.

Der Chaostest verursacht Fehler in den folgenden Kategorien:

* Neustart eines Knotens
* Neustart eines bereitgestellten Codepakets
* Entfernung eines Replikats
* Neustart eines Replikats
* Verschiebung eines primären Replikats (konfigurierbar)
* Verschiebung eines sekundären Replikats (konfigurierbar)

Chaos wird in mehreren Iterationen ausgeführt. Jede Iteration besteht aus Fehlern und der Clusterüberprüfung für den angegebenen Zeitraum. Sie können die Dauer der Stabilisierung des Clusters und des erfolgreichen Abschlusses der Überprüfung konfigurieren. Wenn bei der Clusterüberprüfung ein Fehler gefunden wird, wird vom Chaostest ein „ValidationFailedEvent“-Ereignis mit dem UTC-Zeitstempel und den Fehlerdetails generiert und gespeichert. Nehmen wir beispielsweise an, ein Chaostest soll eine Stunde lang ausgeführt werden und maximal drei gleichzeitige Fehler umfassen. Beim Chaostest werden drei Fehler eingeschleust, und anschließend wird die Clusterintegrität überprüft. Es erfolgt eine Iteration durch den vorherigen Schritt, bis dieser explizit mithilfe der StopChaosAsync-API beendet wird oder eine Stunde vergangen ist. Wenn der Cluster bei einer der Iterationen fehlerhaft wird (also sich nicht innerhalb des mit MaxClusterStabilizationTimeout übergebenen Zeitraums stabilisiert oder fehlerfrei wird), generiert Chaos ein Ereignis vom Typ „ValidationFailedEvent“. Dieses Ereignis bedeutet, dass etwas schiefgelaufen ist und ggf. eine nähere Untersuchung erforderlich ist.

Wenn Sie herausfinden möchten, welche Fehler Chaos induziert hat, können Sie die GetChaosReport-API (PowerShell, C# oder REST) verwenden. Die API ruft das jeweils nächste Segment des Chaos-Berichts basierend auf dem übergebenen Fortsetzungstoken oder dem übergebenen Zeitraum ab. Sie können entweder das ContinuationToken zum Abrufen des nächsten Segments des Chaos-Berichts angeben oder den Zeitraum mit StartTimeUtc und EndTimeUtc festlegen. Sie können jedoch nicht im gleichen Aufruf das ContinuationToken und den Zeitraum angeben. Wenn mehr als 100 Chaos-Ereignisse vorhanden sind, wird der Chaos-Bericht in Segmenten zurückgegeben, die jeweils nicht mehr als 100 Chaos-Ereignisse enthalten.

## <a name="important-configuration-options"></a>Wichtige Konfigurationsoptionen
* **TimeToRun**: Gesamtdauer der Ausführung des Chaostests, bevor er erfolgreich abgeschlossen wird. Sie können den Chaostest mit der StopChaos-API vor Ablauf des Zeitraums von „TimeToRun“ beenden.

* **MaxClusterStabilizationTimeout:** maximale Dauer für das Warten auf die Wiederherstellung der Integrität des Clusters, bevor ein ValidationFailedEvent ausgelöst wird. Mit diesem Wartezeitraum soll die Auslastung des Clusters während der Wiederherstellung reduziert werden. Die folgenden Prüfungen erfolgen:
  * Ob die Clusterintegrität in Ordnung ist
  * Ob die Dienstintegrität in Ordnung ist
  * Ob die Größe der Zielreplikatgruppe für die Dienstpartition erreicht wurde
  * Ob keine „InBuild“-Replikate vorhanden sind
* **MaxConcurrentFaults**: Maximale Anzahl von gleichzeitigen Fehlern, die bei jeder Iteration ausgelöst werden. Je höher die Zahl, desto aggressiver geht Chaos vor und desto komplexer sind auch die Failover und die Status-Übergang-Kombinationen, die der Cluster durchläuft. 

> [!NOTE]
> Der Chaos-Test garantiert unabhängig von der Höhe des Werts von *MaxConcurrentFaults*, dass es bei Nichtauftreten externer Fehler nicht zu einem Quorum- oder Datenverlust kommt.
>

* **EnableMoveReplicaFaults**: Aktiviert oder deaktiviert die Fehler, die zur Verschiebung der primären oder sekundären Replikate führen. Diese Fehler sind standardmäßig aktiviert.
* **WaitTimeBetweenIterations:** die Zeitspanne, die zwischen Iterationen gewartet wird. Dies ist also die Zeitspanne, die Chaos nach einem Fehlerdurchlauf und den zugehörigen Überprüfungen der Integrität des Clusters unterbrochen wird. Je höher der Wert, desto niedriger ist die durchschnittliche Fault Injection-Rate.
* **WaitTimeBetweenFaults:** Wartezeit zwischen zwei aufeinanderfolgenden Fehlern in einer Iteration. Je höher der Wert, desto niedriger ist die Parallelität (oder Überlappung) der Fehler.
* **ClusterHealthPolicy:** Die Clusterintegritätsrichtlinie wird verwendet, um die Integrität des Clusters zwischen Iterationen von Chaos zu überprüfen. Wenn die Clusterintegrität beeinträchtigt ist oder bei der Fehlerausführung eine unerwartete Ausnahme auftritt, wartet Chaos 30 Minuten bis zur nächsten Integritätsüberprüfung, damit der Cluster sich normalisieren kann.
* **Context:** eine Sammlung von Schlüssel-Wert-Paaren vom Typ (string, string). Die Zuordnung kann verwendet werden, um Informationen zur Ausführung von Chaos aufzuzeichnen. Es kann nicht mehr als 100 solcher Paare geben, und jede Zeichenfolge (Schlüssel oder Wert) darf höchstens 4.095 Zeichen lang sein. Diese Zuordnung wird beim Start der Chaos-Ausführung festgelegt, um optional den Kontext der jeweiligen Ausführung zu speichern.
* **ChaosTargetFilter**: Dieser Filter kann verwendet werden, um Chaos-Fehler nur auf bestimmte Knotentypen oder nur auf bestimmte Anwendungsinstanzen auszurichten. Wenn „ChaosTargetFilter“ nicht verwendet wird, stört Chaos alle Clusterentitäten. Wenn „ChaosTargetFilter“ verwendet wird, stört Chaos nur die Entitäten, die der ChaosTargetFilter-Spezifikation entsprechen. „NodeTypeInclusionList“ und „ApplicationInclusionList“ gestatten lediglich die Vereinigungssemantik. Mit anderen Worten, es ist nicht möglich, eine Schnittmenge von „NodeTypeInclusionList“ und „ApplicationInclusionList“ anzugeben. Es ist z. B. nicht möglich, Folgendes anzugeben: „Diese Anwendung nur bemängeln, wenn sie sich auf diesem Knotentyp befindet“. Sobald eine Entität entweder in „NodeTypeInclusionList“ oder „ApplicationInclusionList“ enthalten ist, kann diese Entität nicht mit „ChaosTargetFilter“ ausgeschlossen werden. Selbst wenn „applicationX“ nicht in „ApplicationInclusionList“ enthalten ist, kann „applicationX“ in manchen Chaos-Iterationen fehlerhaft sein, da es sich anscheinend auf einem Knoten von „nodeTypeY“ befindet, der in „NodeTypeInclusionList“ enthalten ist. Wenn sowohl „NodeTypeInclusionList“ als auch „ApplicationInclusionList“ NULL oder leer sind, wird „ArgumentException“ ausgelöst.
    * **NodeTypeInclusionList**: Eine Liste von Knotentypen, die in Chaos-Fehler einbezogen werden sollen. Für die Knoten dieser Knotentypen sind alle Arten von Fehlern („Knoten neu starten“, „Codepaket neu starten“, „Replikat entfernen“, „Replikat neu starten“, „primäres Replikat verschieben“ und „sekundäres Replikat verschieben“) aktiviert. Wenn ein Knotentyp (z. B. „NodeTypeX“) nicht in „NodeTypeInclusionList“ enthalten ist, dann werden Fehler auf Knotenebene (z. B. „NodeRestart“) niemals für die Knoten von „NodeTypeX“ aktiviert, aber Codepaket- und Replikatfehler können für „NodeTypeX“ weiterhin aktiviert werden, wenn sich eine Anwendung in „ApplicationInclusionList“ zufällig auf einem Knoten von „NodeTypeX“ befindet. Es können maximal 100 Knotentypnamen in diese Liste aufgenommen werden. Um diese Zahl zu erhöhen, ist ein Upgrade für die MaxNumberOfNodeTypesInChaosTargetFilter-Konfiguration erforderlich.
    * **ApplicationInclusionList**: Eine Liste der Anwendungs-URIs, die in Chaos-Fehler einbezogen werden sollen. Alle Replikate, die zu den Diensten dieser Anwendungen gehören, sind durch Chaos für Replikatfehler anfällig („Replikat neu starten“, „Replikat entfernen“, „primäres Replikat verschieben“ und „sekundäres Replikat verschieben“). Chaos startet ein Codepaket möglicherweise nur neu, wenn das Codepaket nur Replikate dieser Anwendungen hostet. Wenn eine Anwendung nicht in dieser Liste enthalten ist, kann sie dennoch in einer Chaos-Iteration gestört werden, wenn die Anwendung auf einem Knoten eines Knotentyps landet, der in NodeTypeInclusionList enthalten ist. Wenn „applicationX“ jedoch durch Platzierungseinschränkungen an „nodeTypeY“ gebunden ist und „applicationX“ in „ApplicationInclusionList“ fehlt und „nodeTypeY“ in „NodeTypeInclusionList“ fehlt, dann wird „applicationX“ niemals fehlerhaft sein. Es können maximal 1000 Anwendungsnamen in diese Liste aufgenommen werden. Um diese Zahl zu erhöhen, ist ein Upgrade für die MaxNumberOfApplicationsInChaosTargetFilter-Konfiguration erforderlich.

## <a name="how-to-run-chaos"></a>Ausführen des Chaostests

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
