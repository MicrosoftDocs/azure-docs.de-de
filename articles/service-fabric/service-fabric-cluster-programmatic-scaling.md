---
title: Azure Service Fabric – programmgesteuertes Skalieren
description: Programmgesteuertes horizontales Herunter- oder Hochskalieren eines Azure Service Fabric-Clusters gemäß benutzerdefinierten Triggern
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f39bd874c1f5a1be42ca1c88e6ea2fe8df22f87
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648207"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Programmgesteuertes Skalieren eines Service Fabric-Clusters 

Service Fabric-Cluster, die in Azure ausgeführt werden, basieren auf VM-Skalierungsgruppen.  Im Artikel zur [Clusterskalierung](./service-fabric-cluster-scale-in-out.md) wird beschrieben, wie Service Fabric-Cluster entweder manuell oder mit Regeln für die automatische Skalierung skaliert werden können. In diesem Artikel wird beschrieben, wie Sie Anmeldeinformationen verwalten und einen Cluster horizontal hoch- oder herunterskalieren, indem Sie das Azure Fluent-Compute-SDK verwenden. Dies ist ein anspruchsvolleres Szenario. Eine Übersicht hierzu finden Sie in dem Artikel, in dem die [programmgesteuerten Methoden zum Koordinieren von Azure-Skalierungsvorgängen](service-fabric-cluster-scaling.md#programmatic-scaling) beschrieben werden. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Verwalten von Anmeldeinformationen
Eine Herausforderung beim Schreiben von einem Dienst für die Skalierung besteht darin, dass der Dienst ohne eine interaktive Anmeldung Zugriff auf VM-Skalierungsgruppenressourcen haben muss. Der Zugriff auf den Service Fabric-Cluster ist einfach, wenn der Skalierungsdienst seine eigene Service Fabric-Anwendung ändert, für den Zugriff auf die Skalierungsgruppe jedoch Anmeldeinformationen erforderlich sind. Für die Anmeldung können Sie einen mit der [Azure CLI](https://github.com/azure/azure-cli) erstellten [Dienstprinzipal](/cli/azure/create-an-azure-service-principal-azure-cli) verwenden.

Ein Dienstprinzipal kann mit den folgenden Schritten erstellt werden:

1. Melden Sie sich bei der Azure CLI (`az login`) als Benutzer mit Zugriff auf die VM-Skalierungsgruppe an.
2. Erstellen Sie den Dienstprinzipal mit `az ad sp create-for-rbac`.
    1. Notieren Sie sich App-ID (an anderer Stelle als „Client-ID“ bezeichnet), Name, Kennwort und Mandant für die spätere Verwendung.
    2. Sie benötigen auch Ihre Abonnement-ID, die mit `az account list` angezeigt werden kann.

Die Fluent-Computebibliothek kann wie folgt die Anmeldung mit diesen Anmeldeinformationen durchführen (beachten Sie, dass sich wichtige Fluent-Azure-Typen wie `IAzure` im Paket [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) befinden):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Nach der Anmeldung kann die Anzahl von Skalierungsgruppeninstanzen über `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` abgefragt werden.

## <a name="scaling-out"></a>Horizontales Skalieren
Mithilfe des Azure Fluent-Compute-SDKs können der VM-Skalierungsgruppe mit nur wenigen Aufrufe Instanzen hinzugefügt werden:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternativ kann die Größe der VM-Skalierungsgruppe auch mit PowerShell-Cmdlets verwaltet werden. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) kann das Objekt der VM-Skalierungsgruppe abrufen. Die aktuelle Kapazität ist über die Eigenschaft `.sku.capacity` verfügbar. Nach der Änderung der Kapazität in den gewünschten Wert kann die VM-Skalierungsgruppe mit dem Befehl [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) in Azure aktualisiert werden.

Wie beim manuellen Hinzufügen eines Knotens sollte zum Starten eines neuen Service Fabric-Knotens nur das Hinzufügen einer Skalierungsgruppe erforderlich sein, da die Skalierungsgruppenvorlage Erweiterungen beinhaltet, um dem Service Fabric-Cluster automatisch neue Instanzen hinzuzufügen. 

## <a name="scaling-in"></a>Horizontales Herunterskalieren

Das horizontale Herunterskalieren ähnelt dem horizontalen Hochskalieren. Die tatsächlichen Änderungen an der VM-Skalierungsgruppe sind praktisch identisch. Aber wie zuvor erläutert bereinigt Service Fabric nur entfernte Knoten mit Dauerhaftigkeitsstufe „Gold“ oder „Silber“ automatisch. Im Falle einer horizontalen Herunterskalierung bei Dauerhaftigkeitsstufe „Bronze“ ist daher eine Interaktion mit dem Service Fabric-Cluster erforderlich, um den Knoten herunterzufahren, ihn zu entfernen und dann seinen Zustand zu entfernen.

Die Vorbereitung des Knotens auf das Herunterfahren umfasst das Suchen und Deaktivieren des zu entfernenden Knotens (dies ist die zuletzt hinzugefügte VM-Skalierungsgruppeninstanz). VM-Skalierungsgruppeninstanzen werden in der Reihenfolge nummeriert, in der sie hinzugefügt werden. Neuere Knoten lassen sich daher einfach auffinden, indem Sie die Nummernsuffixe der Knotennamen vergleichen (die Namen entsprechen den Namen der zugrunde liegenden VM-Skalierungsgruppeninstanzen). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Sobald der zu entfernende Knoten gefunden wurde, kann er mit denselben `FabricClient`- und `IAzure`-Instanzen wie zuvor deaktiviert und entfernt werden.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Wie beim horizontalen Hochskalieren können auch hier PowerShell-Cmdlets zum Ändern der Kapazität von VM-Skalierungsgruppen verwendet werden, wenn ein Skriptansatz bevorzugt wird. Sobald die virtuelle Computerinstanz entfernt wurde, kann der Zustand des Service Fabric-Knotens entfernt werden.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Nächste Schritte

Für die ersten Schritte mit der Implementierung Ihrer eigenen Logik für automatische Skalierung machen Sie sich mit den folgenden Konzepten und hilfreichen APIs vertraut:

- [Manuelles Skalieren oder mit automatischen Skalierungsregeln](./service-fabric-cluster-scale-in-out.md)
- [Azure-Verwaltungsbibliotheken für .NET](https://github.com/Azure/azure-libraries-for-net) (nützlich für die Interaktion mit den einem Service Fabric-Cluster zugrunde liegenden VM-Skalierungsgruppen)
- [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient) (nützlich für die Interaktion mit einem Service Fabric-Cluster und dessen Knoten)
