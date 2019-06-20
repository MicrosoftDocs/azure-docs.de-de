---
title: Azure Service Fabric – bewährte Methoden für die Kapazitätsplanung und Skalierung | Microsoft-Dokumentation
description: Bewährte Methoden für die Planung und Skalierung von Service Fabric-Clustern und -Anwendungen.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 9bddb6552b11dd506ee3e2c1c416c15da11048b7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258754"
---
# <a name="capacity-planning-and-scaling"></a>Kapazitätsplanung und Skalierung

Vor der Erstellung eines Azure Service Fabric-Clusters oder der Skalierung von Computeressourcen, die den Cluster hosten, ist es wichtig, die Kapazität zu planen. Weitere Informationen zur Planung der Kapazität finden Sie unter [Planen der Service Fabric-Clusterkapazität](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Weitere bewährte Methoden zur Skalierbarkeit von Clustern finden Sie unter [Überlegungen zur Service Fabric-Skalierbarkeit](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Neben der Berücksichtigung der Merkmale von Knotentypen und Clustern sollten Sie einplanen, dass Skalierungsvorgänge für eine Produktionsumgebung länger als eine Stunde dauern, unabhängig von der Anzahl der hinzugefügten virtuellen Computer.

## <a name="auto-scaling"></a>Automatische Skalierung
Skalierungsvorgänge sollten über die Bereitstellung von Azure Resource Manager-Vorlagen erfolgen, da es sich bewährt hat, [Ressourcenkonfigurationen als Code]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) zu behandeln, und die Verwendung der automatischen Skalierung von VM-Skalierungsgruppen dazu führt, dass in der Resource Manager-Vorlage mit Versionsangabe die Instanzanzahl der VM-Skalierungsgruppen ungenau definiert wird, sodass sich das Risiko erhöht, dass zukünftige Bereitstellungen zu unbeabsichtigten Skalierungsvorgängen führen. Im Allgemeinen sollten Sie die automatische Skalierung in folgenden Fällen verwenden:

* Die Bereitstellung Ihrer Resource Manager-Vorlagen mit der deklarierten geeigneten Kapazität unterstützt Ihren Anwendungsfall nicht.
  * Zusätzlich zur manuellen Skalierung können Sie eine [Pipeline für Continuous Integration und Continuous Delivery in Azure DevOps Services mit Bereitstellungsprojekten für Azure-Ressourcengruppen](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts) konfigurieren. Diese wird im Allgemeinen durch eine Logik-App ausgelöst, die Leistungsmetriken für virtuelle Computer nutzt, die über die [Azure Monitor-REST-API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) abgefragt werden, sodass die automatische Skalierung basierend auf beliebig festgelegten Metriken durchgeführt wird und gleichzeitig der Azure Resource Manager-Mehrwert optimiert wird.
* Sie müssen jeweils nur 1 Knoten einer VM-Skalierungsgruppe horizontal skalieren.
  * Für das horizontale Hochskalieren um jeweils 3 oder mehr Knoten sollten Sie [einen Service Fabric-Cluster durch Hinzufügen einer VM-Skalierungsgruppe skalieren](https://docs.microsoft.com/azure service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), und es ist am sichersten, VM-Skalierungsgruppen jeweils um 1 Knoten horizontal herunter- und hochzuskalieren.
* Für den Service Fabric-Cluster ist die Zuverlässigkeitsstufe „Silber“ oder höher und die Dauerhaftigkeitsstufe „Silber“ oder höher für alle Skalierungsgruppen festgelegt, für die Sie Regeln für die automatische Skalierung konfigurieren.
  * Die mit den Regeln für die automatische Skalierung festgelegte minimale Kapazität muss größer oder gleich fünf VM-Instanzen und größer oder gleich der minimalen Zuverlässigkeitsstufe für den primären Knotentyp sein.

> [!NOTE]
> Der zustandsbehaftete Azure Service Fabric-Dienst „fabric:/System/InfastructureService/<NODE_TYPE_NAME>“ wird auf jedem Knotentyp mit der Dauerhaftigkeitsstufe „Silber“ oder einer höheren Dauerhaftigkeitsstufe ausgeführt. Dies ist der einzige Systemdienst, dessen Ausführung in Azure auf allen Knotentypen Ihres Clusters unterstützt wird.

## <a name="vertical-scaling-considerations"></a>Überlegungen zur vertikalen Skalierung

Die [vertikale Skalierung](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) eines Knotentyps in Azure Service Fabric erfordert mehrere Schritte und Überlegungen. Beispiel:

* Der Cluster muss vor der Skalierung fehlerfrei sein. Andernfalls wird der Cluster nur weiter destabilisiert.
* Für alle Knotentypen des Service Fabric-Clusters, die zustandsbehaftete Dienste hosten, ist die **Dauerhaftigkeitsstufe „Silber“ oder höher** erforderlich.

> [!NOTE]
> Der primäre Knotentyp, der zustandsbehaftete Service Fabric-Systemdienste hostet, muss mindestens die Dauerhaftigkeitsstufe „Silber“ aufweisen. Nachdem Sie die Dauerhaftigkeitsstufe „Silber“ aktiviert haben, werden Clustervorgänge wie das Aktualisieren, Hinzufügen oder Entfernen von Knoten langsamer durchgeführt, da im System die Optimierung der Datensicherheit Vorrang vor der Geschwindigkeit von Vorgängen hat.

Die vertikale Skalierung einer VM-Skalierungsgruppe ist ein destruktiver Vorgang. Skalieren Sie den Cluster stattdessen horizontal durch Hinzufügen einer neuen Skalierungsgruppe mit der gewünschten SKU, und migrieren Sie die Dienste zu der gewünschten SKU, um den vertikalen Skalierungsvorgang sicher abzuschließen. Die Änderung der SKU einer VM-Skalierungsgruppenressource ist ein destruktiver Vorgang, da für die Hosts ein Reimaging durchgeführt wird, bei dem alle lokal persistenten Zustände entfernt werden.

Für Ihren Cluster werden [Knoteneigenschaften und Platzierungseinschränkungen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) in Service Fabric verwendet, um festzulegen, wo die Anwendungsdienste gehostet werden. Deklarieren Sie bei der vertikalen Skalierung des primären Knotentyps identische Eigenschaftswerte für das Element `"nodeTypeRef"`, das sich in der Service Fabric-Erweiterung für VM-Skalierungsgruppen befindet. Der folgende Codeausschnitt der Resource Manager-Vorlage zeigt die Eigenschaften, die Sie deklarieren, mit dem gleichen Wert für die neu bereitgestellten Skalierungsgruppen, für die die Skalierung durchgeführt wird, und wird nur als vorübergehend zustandsbehaftet für den Cluster unterstützt:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Führen Sie den Cluster nicht länger als für den Abschluss eines erfolgreichen vertikalen Skalierungsvorgangs erforderlich mit mehreren Skalierungsgruppen aus, die den gleichen Eigenschaftswert für `nodeTypeRef` verwenden.
> Überprüfen Sie Vorgänge immer in Testumgebungen, bevor Sie Änderungen in der Produktionsumgebung vornehmen. Standardmäßig weisen die Systemdienste des Service Fabric-Clusters nur für den primären Zielknotentyp eine Platzierungseinschränkung auf.

Führen Sie nach dem Deklarieren der Knoteneigenschaften und Platzierungseinschränkungen die folgenden Schritte für jeweils eine VM-Instanz aus. Dies ermöglicht, dass die Systemdienste (und Ihre zustandsbehafteten Dienste) ordnungsgemäß auf der VM-Instanz heruntergefahren werden, die Sie entfernen, und gleichzeitig neue Replikate an anderer Stelle erstellt werden.

1. Führen Sie in PowerShell `Disable-ServiceFabricNode` mit der Absicht „RemoveNode“ aus, um den Knoten zu deaktivieren, der entfernt werden soll. Entfernen Sie den Knotentyp mit der höchsten Zahl. Entfernen Sie bei einem Cluster mit sechs Knoten beispielsweise die VM-Instanz „MyNodeType_5“.
2. Führen Sie `Get-ServiceFabricNode` aus, um sicherzustellen, dass der Status des Knotens tatsächlich in „Deaktiviert“ geändert wurde. Falls nicht, warten Sie, bis der Knoten deaktiviert ist. Dies kann für jeden Knoten einige Stunden dauern. Fahren Sie erst fort, nachdem der Status des Knotens in „Deaktiviert“ geändert wurde.
3. Verringern Sie die Anzahl der virtuellen Computer in diesem Knotentyp um 1. Damit wird die höchste VM-Instanz entfernt.
4. Wiederholen Sie den Anforderungen entsprechend die Schritte 1 bis 3. Skalieren Sie allerdings auf keinen Fall die Anzahl der Instanzen auf den primären Knotentypen auf einen Wert herunter, der unter dem liegt, den die Zuverlässigkeitsstufe verlangt. Eine Liste der empfohlenen Instanzen finden Sie unter [Planen der Service Fabric-Clusterkapazität ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

> [!NOTE]
> Ein unterstütztes Szenario für die Durchführung einer vertikalen Skalierung ist: Ich kann meinen Service Fabric-Cluster und meine -Anwendung ohne Ausfallzeiten der Anwendung von „Nicht verwalteter Datenträger“ zu „Verwalteter Datenträger“ migrieren. Durch die Bereitstellung einer neuen VM-Skalierungsgruppe mit verwalteten Datenträgern und die Durchführung eines Anwendungsupgrades mit Platzierungseinschränkungen, die auf die bereitgestellte Kapazität ausgerichtet sind, kann Ihr Service Fabric-Cluster die Workload gemäß der Kapazität der bereitgestellten Clusterknoten planen, die von der Upgradedomäne ohne Ausfallzeiten der Anwendung eingeführt wird. [Azure Load Balancers Basic-SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus): Back-End-Poolendpunkte können virtuelle Computer in einer einzelnen Verfügbarkeitsgruppe oder VM-Skalierungsgruppe sein. Das bedeutet, dass Sie keinen Load Balancer mit Basic-SKU verwenden können, wenn Sie Ihre Service Fabric Systems-Anwendung zwischen den Skalierungsgruppen verschieben, ohne eine temporäre Nichterreichbarkeit Ihres Endpunkts für die Service Fabric-Clusterverwaltung zu verursachen, obwohl der Cluster und seine Anwendung noch ausgeführt werden. In der Regel stellen Sie einen Load Balancer mit Standard-SKU zur Verfügung, wenn Sie einen Wechsel der der virtuellen IP-Adresse (VIP) zwischen Load Balancer-Ressourcen mit Basic-SKU und Load Balancer-Ressourcen mit Standard-SKU durchführen, um die zukünftig für den VIP-Wechsel erforderlichen 30 Sekunden (ungefähre Angabe) der Nichterreichbarkeit zu verringern.

## <a name="horizontal-scaling"></a>Horizontale Skalierung

Die horizontale Skalierung in Service Fabric kann [manuell](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) oder [programmgesteuert](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling) erfolgen.

> [!NOTE]
> Wenn Sie einen Knotentyp mit der Dauerhaftigkeitsstufe „Silber“ oder „Gold“ skalieren, wird der Skalierungsvorgang langsam durchgeführt.

### <a name="scaling-out"></a>Horizontales Skalieren

Skalieren Sie einen Service Fabric-Cluster horizontal hoch, indem Sie die Anzahl der Instanzen für eine bestimmte VM-Skalierungsgruppe erhöhen. Sie können das horizontale Hochskalieren programmgesteuert durchführen, indem Sie mithilfe des Azure-Clients und der ID für die gewünschte Skalierungsgruppe die Kapazität erhöhen.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Für ein manuelles horizontales Hochskalieren aktualisieren Sie die Kapazität in der SKU-Eigenschaft der gewünschten [VM-Skalierungsgruppe](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile).
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Horizontales Herunterskalieren

Beim horizontalen Herunterskalieren müssen mehr Aspekte berücksichtigt werden als beim horizontalen Hochskalieren. Beispiel:

* Service Fabric-Systemdienste werden auf dem primären Knotentyp in Ihrem Cluster ausgeführt. Sie dürfen also niemals die Anzahl der Instanzen für diesen Knotentyp herunterfahren oder so herunterskalieren, dass weniger Instanzen vorhanden sind, als durch die Zuverlässigkeitsstufe vorgegeben ist. 
* Für einen zustandsbehafteten Dienst muss eine bestimmte Anzahl von Knoten stets aktiv sein, um die Verfügbarkeit sicherzustellen und den Zustand des Diensts beizubehalten. Sie benötigen mindestens eine Anzahl von Knoten, die der Anzahl der Zielreplikatgruppen der Partition oder des Diensts entspricht.

Beim manuellen Herunterskalieren gehen Sie folgendermaßen vor:

1. Führen Sie in PowerShell `Disable-ServiceFabricNode` mit der Absicht „RemoveNode“ aus, um den Knoten zu deaktivieren, der entfernt werden soll. Entfernen Sie den Knotentyp mit der höchsten Zahl. Entfernen Sie bei einem Cluster mit sechs Knoten beispielsweise die VM-Instanz „MyNodeType_5“.
2. Führen Sie `Get-ServiceFabricNode` aus, um sicherzustellen, dass der Status des Knotens tatsächlich in „Deaktiviert“ geändert wurde. Falls nicht, warten Sie, bis der Knoten deaktiviert ist. Dies kann für jeden Knoten einige Stunden dauern. Fahren Sie erst fort, nachdem der Status des Knotens in „Deaktiviert“ geändert wurde.
3. Verringern Sie die Anzahl der virtuellen Computer in diesem Knotentyp um 1. Damit wird die höchste VM-Instanz entfernt.
4. Wiederholen Sie den Anforderungen entsprechend die Schritte 1 bis 3. Skalieren Sie allerdings auf keinen Fall die Anzahl der Instanzen auf den primären Knotentypen auf einen Wert herunter, der unter dem liegt, den die Zuverlässigkeitsstufe verlangt. Eine Liste der empfohlenen Instanzen finden Sie unter [Planen der Service Fabric-Clusterkapazität ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Um manuell horizontal herunterzuskalieren, aktualisieren Sie die Kapazität in der SKU-Eigenschaft der gewünschten [VM-Skalierungsgruppe](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile).

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Wiederholen Sie die Schritte 1 bis 3, bis die gewünschte Kapazität bereitgestellt wird. Skalieren Sie auf keinen Fall die Anzahl der Instanzen des primären Knotentyps auf einen Wert herunter, der unter dem von der Zuverlässigkeitsstufe vorgegebenen liegt. Ausführliche Informationen zu Zuverlässigkeitsstufen und der jeweils erforderlichen Anzahl von Instanzen finden Sie unter [Planen der Service Fabric-Clusterkapazität](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Damit das horizontale Herunterskalieren programmgesteuert durchgeführt werden kann, müssen Sie den Knoten auf das Herunterfahren vorbereiten. Dies umfasst das Suchen und Deaktivieren des zu entfernenden Knotens, d.h. des Knotens der höchsten Instanz. Beispiel:

```c#
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

Nachdem Sie den zu entfernenden Knoten identifiziert haben, deaktivieren und entfernen Sie ihn, und verwenden Sie dabei die gleiche `FabricClient`-Instanz (in diesem Fall `client`) und dem gleichen Namen der Knoteninstanz (in diesem Fall `instanceIdString`), die Sie im Code oben verwendet haben:

```c#
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

> [!NOTE]
> Wenn Sie einen Cluster herunterskalieren, wird der entfernte Knoten bzw. die entfernte VM-Instanz im Service Fabric Explorer in einem fehlerhaften Zustand angezeigt. Eine Erklärung dieses Verhaltens finden Sie unter [Verhaltensweisen von Service Fabric Explorer, die Sie möglicherweise beobachten](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Ihre Möglichkeiten:
> * Rufen Sie den Befehl [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) mit dem entsprechenden Knotennamen auf.
> * Stellen Sie in Ihrem Cluster eine [service-fabric-autoscale-helper-Anwendung](https://github.com/Azure/service-fabric-autoscale-helper/) bereit, die sicherstellt, dass herunterskalierte Knoten aus dem Service Fabric Explorer gelöscht werden.

## <a name="reliability-levels"></a>Zuverlässigkeitsstufen

Die [Zuverlässigkeitsstufe](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) ist eine Eigenschaft der Service Fabric-Clusterressource und kann für einzelne Knotentypen nicht unterschiedlich konfiguriert werden. Sie steuert den Replikationsfaktor der Systemdienste für den Cluster und ist eine Einstellung auf Ebene der Clusterressource. Die Zuverlässigkeitsstufe bestimmt die Mindestanzahl von Knoten, über die Ihr primärer Knotentyp verfügen muss. Für die Zuverlässigkeitsstufe können folgende Werte festgelegt werden:

* Platin: Die Systemdienste werden mit der Replikatgruppen-Zielanzahl 7 und neun Startknoten ausgeführt.
* Gold: Die Systemdienste werden mit der Replikatgruppen-Zielanzahl 7 und sieben Startknoten ausgeführt.
* Silber: Die Systemdienste werden mit der Replikatgruppen-Zielanzahl 5 und fünf Startknoten ausgeführt.
* Bronze: Die Systemdienste werden mit der Replikatgruppen-Zielanzahl 3 und drei Startknoten ausgeführt.

Die minimale empfohlene Zuverlässigkeitsstufe ist „Silber“.

Die Zuverlässigkeitsstufe wird im Abschnitt der Eigenschaften der Ressource [Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) wie folgt festgelegt:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Dauerhaftigkeitsstufen

> [!WARNING]
> Knotentypen, die mit der Dauerhaftigkeitsstufe „Bronze“ ausgeführt werden, erhalten _keine Berechtigungen_. Das bedeutet, dass die Infrastrukturaufträge, die sich auf die zustandslosen Workloads auswirken, nicht angehalten oder verzögert werden. Dies kann sich auf Ihre Workloads auswirken. Verwenden Sie die Dauerhaftigkeitsstufe „Bronze“ nur für Knotentypen, die zustandslose Workloads ausführen. Verwenden Sie für Produktionsworkloads mindestens die Dauerhaftigkeitsstufe „Silber“, um Zustandskonsistenz sicherzustellen. Wählen Sie die entsprechende Zuverlässigkeitsstufe basierend auf den Anweisungen in der [Dokumentation zur Kapazitätsplanung](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) aus.

Die Dauerhaftigkeitsstufe muss in zwei Ressourcen festgelegt werden: Im Erweiterungsprofil der [VM-Skalierungsgruppenressource](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Und unter `nodeTypes` in der Ressource [Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

* Erstellen eines Clusters auf virtuellen Computern oder Computern mit Windows Server: [Erstellen eines Service Fabric-Clusters für Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Erstellen eines Clusters auf virtuellen Computern oder Computern mit Linux: [Erstellen eines Linux-Clusters](service-fabric-cluster-creation-via-portal.md)
* Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
