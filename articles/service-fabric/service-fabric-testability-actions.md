---
title: Simulieren von Fehlern in Azure-Microservices
description: Dieser Artikel befasst sich mit den Prüfbarkeitsaktionen von Microsoft Azure Service Fabric.
author: georgewallace
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: aea918f75ad9c8aa58b5201d2fdd967794085438
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547076"
---
# <a name="testability-actions"></a>Testability-Aktionen
Zur Simulierung einer unzuverlässigen Infrastruktur bietet Azure Service Fabric Ihnen, dem Entwickler, die Möglichkeit, verschiedene Ausfälle und Statusübergänge aus der Praxis zu simulieren. Diese werden als Testability-Aktionen verfügbar gemacht. Bei den Aktionen handelt es sich um Low-Level-APIs, die eine bestimmte Fault Injection, einen Statusübergang oder eine Überprüfung bewirken. Diese Aktionen können von Ihnen zu umfassenden Testszenarien für Ihre Dienste kombiniert werden.

Auf der Grundlage dieser Aktionen stellt Service Fabric einige allgemeine Testszenarien bereit. Es wird dringend empfohlen, diese integrierten Szenarien zu verwenden. Sie wurden sorgfältig ausgewählt, um häufig vorkommende Statusübergänge und Ausfallsituationen zu testen. Aktionen können aber auch zum Erstellen von benutzerdefinierten Testszenarien verwendet werden, wenn Sie Fälle abdecken möchten, die noch nicht in den integrierten Testszenarien enthalten oder speziell auf Ihre Anwendung zugeschnitten sind.

Die C#-Implementierungen der Aktionen befinden sich in der Assembly „System.Fabric.dll“. Das PowerShell-Modul für System Fabric befindet sich in der Assembly „Microsoft.ServiceFabric.Powershell.dll“. Im Rahmen der Laufzeitinstallation wird das PowerShell-Modul „ServiceFabric“ installiert, um eine einfache Nutzung zu ermöglichen.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Ordnungsgemäße und nicht ordnungsgemäße Fehleraktionen
Testability-Aktionen sind nach zwei Hauptbereichen (Buckets) klassifiziert:

* Nicht ordnungsgemäße Fehler: Mit diesen Fehlern werden Fehler wie Neustarts von Computern und Abstürze von Prozessen simuliert. Bei diesen Fehlern wird der Ausführungskontext von Prozessen abrupt beendet. Dies bedeutet, dass keine Bereinigung des Status ausgeführt werden kann, bevor die Anwendung neu gestartet wird.
* Ordnungsgemäße Fehler: Mit diesen Fehlern werden ordnungsgemäße Aktionen simuliert, z. B. Verschiebungen von Replikaten und per Lastenausgleich ausgelöst Ablegevorgänge. In diesen Fällen wird der Dienst über das Schließen informiert und kann vor dem Beenden den Status bereinigen.

Führen Sie zur besseren Überprüfung der Qualität die Dienst- und Unternehmensworkload aus, während Sie verschiedene ordnungsgemäße und nicht ordnungsgemäße Fehler auslösen. Bei nicht ordnungsgemäßen Fehlern werden Szenarien simuliert, bei denen der Dienstprozess mitten in einem Workflow abrupt beendet wird. Auf diese Weise wird der Wiederherstellungspfad getestet, nachdem das Dienstreplikat von Service Fabric wiederhergestellt wurde. Dies ist eine Hilfe beim Testen der Datenkonsistenz und der richtigen Beibehaltung des Dienstzustands nach Ausfällen. Bei den anderen (ordnungsgemäßen) Fehlern wird getestet, ob der Dienst richtig auf Replikate reagiert, die von Service Fabric verschoben werden. Hierbei wird auch die Behandlung des Abbruchs in der RunAsync-Methode getestet. Der Dienst muss überprüfen, ob das Abbruchtoken festgelegt ist, den Status korrekt speichern und die RunAsync-Methode beenden.

## <a name="testability-actions-list"></a>Liste mit Testability-Aktionen
| Action | BESCHREIBUNG | Verwaltete API | PowerShell-Cmdlet | Ordnungsgemäße und nicht ordnungsgemäße Fehler |
| --- | --- | --- | --- | --- |
| CleanTestState |Entfernt alle Testzustände aus dem Cluster, falls es zu einem fehlerhaften Herunterfahren des Testtreibers kommt. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nicht verfügbar |
| InvokeDataLoss |Verursacht einen Datenverlust für eine Dienstpartition. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Ordnungsgemäß |
| InvokeQuorumLoss |Versetzt eine bestimmte zustandsbehaftete Dienstpartition in den Zustand eines Quorumverlusts. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Ordnungsgemäß |
| MovePrimary |Verschiebt das angegebene primäre Replikat eines zustandsbehafteten Diensts auf den angegebenen Clusterknoten. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Ordnungsgemäß |
| MoveSecondary |Verschiebt das aktuelle sekundäre Replikat des zustandsbehafteten Diensts auf einen anderen Clusterknoten. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Ordnungsgemäß |
| RemoveReplica |Simuliert einen Replikatausfall, indem ein Replikat aus einem Cluster entfernt wird. Das Replikat wird geschlossen, und es wird der Übergang zur Rolle „Keine“ durchgeführt, sodass alle dazugehörigen Zustände aus dem Cluster entfernt werden. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Ordnungsgemäß |
| RestartDeployedCodePackage |Simuliert den Ausfall eines Codepaketprozesses, indem ein Codepaket neu gestartet wird, das auf einem Knoten in einem Cluster bereitgestellt wurde. Dadurch wird der Codepaketprozess abgebrochen, und alle unter dem Prozess gehosteten Benutzerdienstreplikate werden neu gestartet. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Nicht ordnungsgemäß |
| RestartNode |Simuliert den Ausfall eines Service Fabric-Clusterknotens, indem ein Knoten neu gestartet wird. |RestartNodeAsync |Restart-ServiceFabricNode |Nicht ordnungsgemäß |
| RestartPartition |Simuliert einen Stromausfall in einem Rechenzentrum oder Cluster, indem einige oder alle Replikate einer Partition neu gestartet werden. |RestartPartitionAsync |Restart-ServiceFabricPartition |Ordnungsgemäß |
| RestartReplica |Simuliert den Ausfall eines Replikats, indem ein dauerhaftes Replikat in einem Cluster neu gestartet und das Replikat dann geschlossen und wieder geöffnet wird. |RestartReplicaAsync |Restart-ServiceFabricReplica |Ordnungsgemäß |
| StartNode |Startet einen Knoten in einem bereits beendeten Cluster. |StartNodeAsync |Start-ServiceFabricNode |Nicht verfügbar |
| StopNode |Simuliert den Ausfall eines Knotens, indem ein Knoten in einem Cluster beendet wird. Der Knoten bleibt so lange heruntergefahren, bis StartNode aufgerufen wird. |StopNodeAsync |Stop-ServiceFabricNode |Nicht ordnungsgemäß |
| ValidateApplication |Überprüft die Verfügbarkeit und Integrität aller Service Fabric-Dienste innerhalb einer Anwendung. Dies erfolgt in der Regel nach dem Auslösen eines Fehlers im System. |ValidateApplicationAsync |Test-ServiceFabricApplication |Nicht verfügbar |
| ValidateService |Überprüft die Verfügbarkeit und Integrität eines Service Fabric-Diensts. Dies erfolgt in der Regel nach dem Auslösen eines Fehlers im System. |ValidateServiceAsync |Test-ServiceFabricService |Nicht verfügbar |

## <a name="running-a-testability-action-using-powershell"></a>Ausführen einer Testability-Aktion mit PowerShell
Dieses Tutorial zeigt, wie Sie mit PowerShell eine Testability-Aktion ausführen. Sie erfahren, wie Sie eine Testability-Aktion für einen lokalen Cluster (One-Box) oder für einen Azure-Cluster ausführen. „Microsoft.Fabric.Powershell.dll“ (das PowerShell-Modul für Service Fabric) wird automatisch installiert, wenn Sie den MSI für Microsoft Service Fabric installieren. Das Modul wird beim Öffnen einer PowerShell-Eingabeaufforderung automatisch geladen.

Abschnitte des Tutorials:

* [Ausführen einer Aktion für einen One-Box-Cluster](#run-an-action-against-a-one-box-cluster)
* [Ausführen einer Aktion für einen Azure-Cluster](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Ausführen einer Aktion für einen One-Box-Cluster
Um eine Testability-Aktion für einen lokalen Cluster ausführen zu können, müssen Sie zuerst eine Verbindung mit dem Cluster herstellen und die PowerShell-Eingabeaufforderung im Administratormodus öffnen. Wir sehen uns jetzt die Aktion **Restart-ServiceFabricNode** an.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Hier wird die Aktion **Restart-ServiceFabricNode** für einen Knoten namens „Node1“ ausgeführt. Für den Beendigungsmodus ist festgelegt, dass nicht überprüft werden soll, ob die Aktion zum Neustarten des Knotens erfolgreich war. Bei Verwendung des Beendigungsmodus „Verify“wird überprüft, ob die Neustartaktion erfolgreich war. Anstatt den Knoten direkt anhand des Namens anzugeben, können Sie ihn auch wie folgt per Partitionsschlüssel und Art des Replikats angeben:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** sollte verwendet werden, um einen Service Fabric-Knoten in einem Cluster neu zu starten. Dadurch wird der Prozess „Fabric.exe“ beendet, und alle Systemdienst- und Benutzerdienstreplikate auf dem Knoten werden neu gestartet. Die Verwendung dieser API zum Testen Ihres Diensts dient Ihnen als Hilfe beim Entdecken von Fehlern in den Failover-Wiederherstellungspfaden. Damit können Ausfälle von Knoten im Cluster besser simuliert werden.

Der folgende Screenshot zeigt den Testability-Befehl **Restart-ServiceFabricNode** in Aktion.

![Screenshot: Ausführen des Befehls „Restart-ServiceFabricNode“ in PowerShell](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Die Ausgabe der ersten Instanz von **Get-ServiceFabricNode** (ein Cmdlet aus dem Service Fabric-PowerShell-Modul) zeigt, dass der lokale Cluster über fünf Knoten verfügt: „Node.1“ bis „Node.5“. Nach Ausführung der Testability-Aktion **Restart-ServiceFabricNode** (Cmdlet) für „Node.4“ ist zu sehen, dass die Betriebszeit des Knotens zurückgesetzt wurde.

### <a name="run-an-action-against-an-azure-cluster"></a>Ausführen einer Aktion für einen Azure-Cluster
Eine Testability-Aktion kann für einen Azure-Cluster (mit PowerShell) auf ähnliche Weise ausgeführt werden wie für einen lokalen Cluster. Der einzige Unterschied besteht darin, dass vor dem Ausführen der Aktion keine Verbindung mit dem lokalen Cluster hergestellt werden muss, sondern mit dem Azure-Cluster.

## <a name="running-a-testability-action-using-c35"></a>Ausführen einer Testability-Aktion mit C&#35;
Zum Ausführen einer Testability-Aktion mit C# müssen Sie zuerst per FabricClient eine Verbindung mit dem Cluster herstellen. Anschließend rufen Sie die Parameter ab, die Sie zum Ausführen der Aktion benötigen. Es können verschiedene Parameter verwendet werden, um die gleiche Aktion auszuführen.
Eine Möglichkeit zum Ausführen der Aktion „RestartServiceFabricNode“ besteht in der Verwendung der Knoteninformationen (Knotenname und Knoteninstanz-ID) im Cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Beschreibung der Parameter:

* **CompleteMode** ) ist festgelegt, dass nicht überprüft werden soll, ob die Neustartaktion erfolgreich war. Bei Verwendung des Beendigungsmodus „Verify“wird überprüft, ob die Neustartaktion erfolgreich war.  
* **OperationTimeout** : Legt den Zeitraum fest, in dem der Vorgang abgeschlossen werden muss, bevor eine TimeoutException-Ausnahme ausgelöst wird.
* **CancellationToken** : Ermöglicht das Abbrechen eines anstehenden Aufrufs.

Anstatt den Knoten direkt anhand seines Namens anzugeben, können Sie ihn als Partitionsschlüssel und Art des Replikats angeben.

Weitere Informationen finden Sie unter „Partitionsselektor und Replikatselektor“.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>Partitionsselektor und Replikatselektor
### <a name="partitionselector"></a>Partitionsselektor
Der Partitionsselektor (PartitionSelector) ist eine Hilfsklasse, die im Testability-Modul verfügbar gemacht wird. Sie dient zum Auswählen einer bestimmten Partition, für die Testability-Aktionen ausgeführt werden sollen. Sie können damit eine bestimmte Partition auswählen, wenn die Partitions-ID im Voraus bekannt ist. Alternativ dazu können Sie den Partitionsschlüssel angeben. Der Vorgang löst die Partitions-ID dann intern auf. Sie haben auch die Möglichkeit, eine zufällige Partition auszuwählen.

Erstellen Sie zur Verwendung dieses Hilfsprogramms das PartitionSelector-Objekt, und wählen Sie die Partition mithilfe einer der Select*-Methoden aus. Übergeben Sie dann das PartitionSelector-Objekt an die API, die es benötigt. Ist keine Option ausgewählt, wird standardmäßig eine zufällige Partition verwendet.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>Replikatselektor
Der Replikatselektor (ReplicaSelector) ist eine Hilfsklasse, die im Testability-Modul verfügbar gemacht wird. Sie dient zum Auswählen eines Replikats, auf dem Testability-Aktionen ausgeführt werden sollen. Sie können damit ein bestimmtes Replikat auswählen, sofern die Replikat-ID im Voraus bekannt ist. Außerdem haben Sie die Möglichkeit, ein primäres Replikat oder auch ein zufälliges sekundäres Replikat auszuwählen. „ReplicaSelector“ wird von „PartitionSelector“ abgeleitet. Sie müssen also sowohl das Replikat als auch die Partition auswählen, für das bzw. die der Testability-Vorgang durchgeführt werden soll.

Erstellen Sie zur Verwendung dieses Hilfsprogramms ein ReplicaSelector-Objekt, und legen Sie fest, wie Sie das Replikat und die Partition auswählen möchten. Anschließend können Sie es an die API übergeben, für die es erforderlich ist. Ist keine Option ausgewählt, werden standardmäßig ein zufälliges Replikat und eine zufällige Partition verwendet.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Nächste Schritte
* [Testability-Szenarien](service-fabric-testability-scenarios.md)
* Gewusst wie: Testen Ihres Diensts
  * [Simulieren von Ausfällen während der Bearbeitung von Dienstworkloads](service-fabric-testability-workload-tests.md)
  * [Ausfälle bei der Kommunikation von Dienst zu Dienst](service-fabric-testability-scenarios-service-communication.md)

