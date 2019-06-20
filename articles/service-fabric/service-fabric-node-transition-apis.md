---
title: Starten und Beenden von Clusterknoten zum Testen von Azure Service Fabric-Apps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Fault Injection zum Testen einer Service Fabric-Anwendung durch Starten und Beenden von Clusterknoten verwenden.
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: ''
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: df0e53736c08fd2c26c467def7328e85f2989f26
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60718137"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Ersetzen der APIs zum Starten und Beenden von Knoten durch die Knotenübergangs-API

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Was machen die APIs zum Starten und Beenden von Knoten?

Die API zum Beenden von Knoten (verwaltet: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) beendet einen Service Fabric-Knoten.  Ein Service Fabric-Knoten ist ein Prozess, d.h. keine VM bzw. kein Computer. Die VM bzw. der Computer wird weiterhin ausgeführt.  Im restlichen Dokument bedeutet „Knoten“ Service Fabric-Knoten.  Das Beenden eines Knotens versetzt ihn in den Zustand *Beendet*, in dem er kein Mitglied des Clusters ist und keine Dienste hosten kann, womit ein *ausgefallener* Knoten simuliert wird.  Dies ist hilfreich für das Einschleusen von Fehlern in das System zum Testen Ihrer Anwendung.  Die API zum Starten von Knoten (verwaltet: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) kehrt die Funktion der API zum Beenden von Knoten um, wodurch der Knoten auf seinen normalen Zustand zurückgesetzt wird.

## <a name="why-are-we-replacing-these"></a>Warum ersetzen wir diese APIs?

Wie oben beschrieben, ist ein *beendeter* Service Fabric-Knoten ein Knoten, auf den absichtlich die API zum Beenden von Knoten angewendet wurde.  Ein *ausgefallener* Knoten ist ein Knoten, der aus einem beliebigen anderen Grund außer Betrieb ist (z.B. wenn die VM oder der Computer deaktiviert ist).  Bei der API zum Beenden von Knoten macht das System keine Informationen verfügbar, um zwischen *beendeten* und *ausgefallenen* Knoten zu unterscheiden.

Darüber hinaus sind einige Fehler, die von diesen APIs zurückgegeben werden, nicht so beschreibend wie sie sein könnten.  Beim Aufrufen der API zum Beenden von Knoten für einen bereits *beendeten* Knoten wird der Fehler *InvalidAddress* zurückgegeben.  Dies kann verbessert werden.

Außerdem ist die Dauer, für die ein Knoten beendet ist, solange „unendlich“, bis die API zum Starten von Knoten aufgerufen wird.  Wir haben festgestellt, dass dies Probleme und Fehler verursachen kann.  Wir haben beispielsweise erlebt, dass ein Benutzer die API zum Beenden von Knoten aufgerufen und dies anschließend vergessen hat.  Später war unklar, ob der Knoten *ausgefallen* war oder *beendet* wurde.


## <a name="introducing-the-node-transition-apis"></a>Einführung in die Knotenübergang-APIs

Wir haben diese Probleme in einem neuen Satz von APIs behoben.  Die neue Knotenübergangs-API (verwaltet: [StartNodeTransitionAsync()][snt]) kann verwendet werden, um einen Service Fabric-Knoten in den Zustand *Beendet* zu versetzen oder um einen Knoten mit dem Zustand *Beendet* in einen normalen Zustand zu versetzen.  Beachten Sie, dass sich das „Start“ im Namen der API nicht auf das Starten eines Knotens bezieht.  Es bezieht sich auf einen asynchronen Vorgang, den das System ausführt, um den Übergang des Knotens in den Zustand *Beendet* oder „Gestartet“ einzuleiten.

**Verwendung**

Wenn die Knotenübergangs-API bei ihrem Aufruf keine Ausnahme auslöst, hat das System den asynchronen Vorgang akzeptiert und führt ihn aus.  Ein erfolgreicher Aufruf bedeutet nicht, dass der Vorgang bereits abgeschlossen ist.  Um Informationen zum aktuellen Status des Vorgangs zu erhalten, rufen Sie die Knotenübergangsstatus-API (verwaltet: [GetNodeTransitionProgressAsync()][gntp]) mit der GUID auf, die beim Aufrufen der Knotenübergangs-API für diesen Vorgang verwendet wurde.  Die Knotenübergangsstatus-API gibt ein „NodeTransitionProgress“-Objekt zurück.  Die „State“-Eigenschaft dieses Objekts gibt den aktuellen Status des Vorgangs an.  Wenn der Status „Running“ ist, wird der Vorgang ausgeführt.  Wenn er „Completed“ ist, wurde der Vorgang ohne Fehler abgeschlossen.  Wenn er „Faulted“ ist, gab es ein Problem bei der Ausführung des Vorgangs.  Die „Exception“-Eigenschaft der „Result“-Eigenschaft gibt an, welcher Fehler vorliegt.  Weitere Informationen zur State-Eigenschaft finden Sie unter https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate. Codebeispiele finden Sie unten im Abschnitt „Beispielverwendung“.


**Unterscheidung zwischen einem beendeten Knoten und einem ausgefallenen Knoten**: Wenn ein Knoten mithilfe der Knotenübergangs-API *beendet* wurde, wird in der Ausgabe einer Knotenabfrage (verwaltet: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) angezeigt, dass bei diesem Knoten der Wert der *IsStopped*-Eigenschaft „true“ ist.  Beachten Sie, dass sich dies vom Wert der *NodeStatus*-Eigenschaft unterscheidet, der *Down* lautet.  Wenn die *NodeStatus*-Eigenschaft den Wert *Down* aufweist, aber *IsStopped* FALSE ist, wurde der Knoten nicht mithilfe der Knotenübergangs-API beendet, sondern weist aus einem anderen Grund den Status *Down* auf.  Wenn die *IsStopped*-Eigenschaft „true“ ist und die *NodeStatus*-Eigenschaft den Wert *Down* hat, wurde der Knoten mithilfe der Knotenübergangs-API beendet.

Durch Starten eines *beendeten* Knotens mithilfe der Knotenübergangs-API wird der Knoten wieder zum normalen Mitglied des Clusters.  Die Ausgabe der Knotenabfrage-API zeigt *IsStopped* als FALSE und *NodeStatus* als nicht „Down“ an (sondern z.B. „Up“).


**Begrenzte Dauer** Bei Verwenden der Knotenübergangs-API zum Beenden eines Knotens stellt einer der Pflichtparameter (*stopNodeDurationInSeconds*) die Dauer in Sekunden dar, die der Knoten *beendet* bleibt.  Dieser Wert muss im zulässigen Bereich von 600 bis 14.400 liegen.  Nach Ablauf dieser Zeit startet sich der Knoten selbst mit dem Status „Up“ automatisch neu.  In Beispiel 1 unten finden Sie ein Beispiel der Nutzung.

> [!WARNING]
> Vermeiden Sie das Kombinieren der APIs zum Starten und Beenden von Knoten mit Knotenübergangs-API.  Es wird empfohlen, nur die Knotenübergangs-API zu verwenden.  Wenn ein Knoten bereits mithilfe der API zum Beenden von Knoten beendet wurde, muss er mit der API zum Starten von Knoten gestartet werden, ehe die Knotenübergangs-API verwendet wird.

> [!WARNING]
> Für denselben Knoten dürfen nicht parallel mehrere Aufrufe der Knotenübergangs-API erfolgen.  In einem solchen Fall wird von der Knotenübergangs-API eine FabricException mit dem „ErrorCode“-Eigenschaftswert „NodeTransitionInProgress“ ausgelöst.  Sobald ein Knotenübergang auf einem bestimmten Knoten gestartet wurde, warten Sie bis der Knoten einen Abschlusszustand (Completed, Faulted oder ForceCancelled) erreicht, ehe Sie einen neuen Übergang auf demselben Knoten starten.  Aufrufe paralleler Knotenübergänge auf verschiedenen Knoten sind zulässig.


#### <a name="sample-usage"></a>Beispielverwendung


**Beispiel 1**: Im folgenden Beispiel wird die Knotenübergangs-API verwendet, um einen Knoten zu beenden.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Beispiel 2**: Im folgenden Beispiel wird ein *beendeter* Knoten gestartet.  Einige Hilfsmethoden aus dem ersten Beispiel werden verwendet.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Beispiel 3**: Das folgende Beispiel zeigt eine falsche Verwendung.  Diese Verwendung ist falsch, da *stopDurationInSeconds* größer als der zulässige Bereich ist.  Da „StartNodeTransitionAsync()“ mit einem schwerwiegenden Fehler misslingt, wurde der Vorgang nicht akzeptiert, und die Status-API muss nicht aufgerufen werden.  Einige Hilfsmethoden aus dem ersten Beispiel werden bei diesem Beispiel verwendet.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Beispiel 4**: Das folgende Beispiel zeigt die Fehlerinformationen, die von der Knotenübergangsstatus-API zurückgegeben werden, wenn der von der Knotenübergangs-API eingeleitete Vorgang akzeptiert wurde, aber später bei der Ausführung misslingt.  In diesem Fall schlägt sie fehl, da die Knotenübergangs-API versucht, einen Knoten zu starten, der nicht vorhanden ist.  Einige Hilfsmethoden aus dem ersten Beispiel werden bei diesem Beispiel verwendet.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
