---
title: Simulieren von Fehlern in Azure Service Fabric-Apps | Microsoft-Dokumentation
description: Informationen zum Absichern Ihrer Dienste bei ordnungsgemäßen und nicht ordnungsgemäßen Ausfällen.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: ''
ms.assetid: 44af01f0-ed73-4c31-8ac0-d9d65b4ad2d6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: ceb6ad1a6a1182d78c473b8b0387c365eb660065
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60865271"
---
# <a name="simulate-failures-during-service-workloads"></a>Simulieren von Ausfällen während der Bearbeitung von Dienstworkloads
Dank der in Azure Service Fabric enthaltenen Testability-Szenarien müssen sich Entwickler keine Sorgen über den Umgang mit einzelnen Fehlern machen. Es gibt aber Szenarien, bei denen unter Umständen eine explizite Überlappung von Clientworkload und Fehlern erforderlich ist. Mit dem Überlappen von Clientworkload und Fehlern wird sichergestellt, dass der Dienst wirklich eine Aktion ausführt, wenn ein Fehler auftritt. Aufgrund des hohen Kontrollgrads, den die Testability bietet, ist dies an präzisen Punkten der Workloadausführung möglich. Diese Auslösung von Fehlern bei unterschiedlichen Zuständen in der Anwendung kann zur Ermittlung von Fehlern und einer Verbesserung der Qualität führen.

## <a name="sample-custom-scenario"></a>Benutzerdefiniertes Beispielszenario
Mit diesem Test wird ein Szenario veranschaulicht, bei dem eine Überlappung der Geschäftsworkload mit [ordnungsgemäßen und nicht ordnungsgemäßen Fehlern](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions)erfolgt. Die Fehler sollten in der Mitte von Dienstvorgängen oder bei der Berechnung der besten Ergebnisse ausgelöst werden.

Sehen wir uns ein Beispiel für einen Dienst an, der vier Workloads bereitstellt: A, B, C und D. Jede Workload entspricht einer Reihe von Workflows und kann ein Compute- bzw. Speichervorgang oder eine Mischung aus beidem sein. Der Einfachheit halber werden die Workloads im Beispiel abstrahiert. Die folgenden unterschiedlichen Ausfälle werden die in diesem Beispiel ausgeführt:

* RestartNode: Ein nicht ordnungsgemäßer Ausfall zur Simulation eines Neustarts des Computers.
* RestartDeployedCodePackage: Ein nicht ordnungsgemäßer Ausfall zur Simulation von Abstürzen des Diensthostprozesses.
* RemoveReplica: Ein ordnungsgemäßer Ausfall zur Simulation des Entfernens eines Replikats.
* MovePrimary: Ein ordnungsgemäßer Ausfall zur Simulation von Replikatverschiebungen, die durch Service Fabric Load Balancer ausgelöst werden.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.ServiceManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.ClusterManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.ApplicationManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.ServiceManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.ServiceManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
