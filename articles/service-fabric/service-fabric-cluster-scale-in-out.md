---
title: Horizontales Herunter- oder Hochskalieren eines Service Fabric-Clusters
description: Skalieren Sie ein Service Fabric-Cluster bedarfsgesteuert horizontal herunter oder hoch, indem Sie die Regeln für das automatische Skalierung für jeden Knotentyp bzw. jede VM-Skalierungsgruppe festlegen. Hinzufügen oder Entfernen von Knoten für einen Service Fabric-Cluster
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409959"
---
# <a name="scale-a-cluster-in-or-out"></a>Horizontales Herunter- oder Hochskalieren eines Clusters

> [!WARNING]
> Lesen Sie vor dem Skalieren diesen Abschnitt.

Die Skalierung von Computeressourcen zum Ermitteln der Arbeitsauslastung Ihrer Anwendung erfordert eine absichtliche Planung, dauert fast immer länger als eine Stunde für eine Produktionsumgebung und setzt voraus, dass Sie Ihre Arbeitsauslastung und Ihren Geschäftskontext verstehen. Wenn Sie diese Aktivität noch nie zuvor ausgeführt haben, wird empfohlen, dass Sie zunächst die [Überlegungen zur Kapazitätsplanung von Service Fabric-Clustern](service-fabric-cluster-capacity.md) lesen und verstehen, bevor Sie sich mit dem Rest dieses Dokuments beschäftigen. Diese Empfehlung dient dazu, unbeabsichtigte LiveSite-Probleme zu vermeiden. Es wird außerdem empfohlen, die Vorgänge, die Sie für eine Nichtproduktionsumgebung ausführen möchten, erfolgreich zu testen. Sie können jederzeit [Produktionsprobleme melden oder bezahlte Unterstützung für Azure anfordern](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Für Engineers, die für die Ausführung dieser Vorgänge vorgesehen sind und den entsprechendem Kontext kennen, werden in diesem Artikel Skalierungsvorgänge beschrieben. Sie müssen aber entscheiden und verstehen, welche Vorgänge für Ihren Anwendungsfall geeignet sind, z.B. welche Ressourcen skaliert werden sollen (CPU, Speicher, Arbeitsspeicher), in welche Richtung skaliert werden soll (vertikal oder horizontal) und welche Vorgänge ausgeführt werden sollen (Bereitstellung von Ressourcenvorlagen, Portal, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Zentrales Hoch- oder Herunterskalieren eines Service Fabric-Clusters mithilfe von Regeln für die automatische Skalierung oder eines manuellen Verfahrens
VM-Skalierungsgruppen sind eine Azure-Computeressource, mit der Sie eine Sammlung von virtuellen Computern bereitstellen und verwalten können. Jeder Knotentyp, der in einem Service Fabric-Cluster definiert ist, wird als separate Skalierungsgruppe eines virtuellen Computers eingerichtet. Jeden Knotentyp kann dann unabhängig zentral hoch- oder herunterskaliert werden, bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen können verschiedene Kapazitätsmetriken aufweisen. Weitere Informationen finden Sie im Dokument über die [Service Fabric-Knotentypen](service-fabric-cluster-nodetypes.md). Da die Service Fabric-Knotentypen in Ihrem Cluster am Back-End aus VM-Skalierungsgruppen bestehen, müssen Sie für jeden Knotentyp bzw. jede VM-Skalierungsgruppe Regeln für die automatische Skalierung einrichten.

> [!NOTE]
> Ihr Abonnement muss ausreichend Kerne zum Hinzufügen der neuen virtuellen Computer umfassen, aus denen dieser Cluster besteht. Gegenwärtig erfolgt keine Modellvalidierung. Daher wird für die Bereitstellungszeit ein Fehler ausgegeben, wenn eine der Kontingentgrenzen erreicht wird. Ein einzelner Knotentyp kann außerdem nicht einfach 100 Knoten pro VM-Skalierungsgruppe überschreiten. Möglicherweise müssen Sie VM-Skalierungsgruppen hinzufügen, um die gewünschte Skalierung zu erreichen. Die automatische Skalierung kann nicht automatisch VM-Skalierungsgruppen hinzufügen. Das Hinzufügen von VM-Skalierungsgruppen zu einem aktiven Cluster ist eine anspruchsvolle Aufgabe, die in der Regel dazu führt, dass Benutzer neue Cluster mit den entsprechenden Knotentypen bereitstellen, die zum Zeitpunkt der Erstellung bereitgestellt werden. [Planen Sie die Clusterkapazität](./service-fabric-cluster-capacity.md) entsprechend. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Auswählen des zu skalierenden Knotentyps bzw. der VM-Skalierungsgruppe
Zurzeit können Sie über das Portal keine Regeln für die automatische Skalierung für VM-Skalierungsgruppen angeben, um einen Service Fabric-Cluster zu erstellen. Daher wird Azure PowerShell (1.0 und höher) verwendet, um die Knotentypen aufzulisten und anschließend automatische Skalierungsregeln hinzuzufügen.

Führen Sie die folgenden Cmdlets aus, um eine Liste mit den VM-Skalierungsgruppen abzurufen, aus denen Ihr Cluster besteht:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Festlegen von Regeln für die automatische Skalierung für Knotentypen bzw. VM-Skalierungsgruppen
Wenn Ihr Cluster mehrere Knotentypen enthält, müssen Sie diesen Vorgang für jeden der Knotentypen bzw. jede der VM-Skalierungsgruppen durchführen, die Sie horizontal hoch- oder herunterskalieren möchten. Sie sollten dabei die Anzahl der Knoten berücksichtigen, die Sie benötigen, bevor Sie die automatische Skalierung einrichten. Die minimale Anzahl von Knoten, die für den primären Knotentyp benötigt wird, wird von der Zuverlässigkeitsstufe gesteuert, die Sie ausgewählt haben. Erfahren Sie mehr über [Zuverlässigkeitsstufen](service-fabric-cluster-capacity.md).

> [!NOTE]
> Wenn Sie den primären Knotentyp auf eine geringere Anzahl als das Minimum skalieren, wird der Cluster instabil oder ggf. sogar abgeschaltet. Dies könnte für Ihre Anwendung und die Systemdienste zu Datenverlust führen.
> 
> 

Die automatische Skalierungsfunktion wird derzeit nicht durch die Lasten gesteuert, die Ihre Anwendung an Service Fabric meldet. Die bereitgestellte automatische Skalierung wird derzeit ausschließlich durch Leistungsindikatoren gesteuert, die von jeder Instanz der VM-Skalierungsgruppen ausgegeben werden.  

Befolgen Sie diese Anweisungen, um eine [automatische Skalierung für jede VM-Skalierungsgruppe einzurichten](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> In einem Abskalierungsszenario müssen Sie das Cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) mit dem Namen des entsprechenden Knotens aufrufen, es sei denn, Ihr Knotentyp besitzt die [Dauerhaftigkeitsstufe][durability] „Gold“ oder „Silber“. Für die Dauerhaftigkeitsstufe „Bronze“wird nicht empfohlen, mehrere Knoten gleichzeitig abzuskalieren.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Manuelles Hinzufügen von virtuellen Computern zu einem Knotentyp oder einer VM-Skalierungsgruppe

Beim Aufskalieren werden der Skalierungsgruppe mehr VM-Instanzen hinzugefügt. Diese Instanzen werden zu den Knoten, die Service Fabric verwendet. Service Fabric erkennt, wenn der Skalierungsgruppe durch horizontales Hochskalieren weitere Instanzen hinzugefügt werden, und reagiert automatisch. 

> [!NOTE]
> Das Hinzufügen virtueller Computer ist zeitaufwendig. Rechnen Sie also nicht damit, dass das Hinzufügen unmittelbar erfolgt. Planen Sie das Hinzufügen von Kapazität vorausschauend, räumen Sie mehr als 10 Minuten ein, bis die VM-Kapazität für die Platzierung der Replikate und Dienstinstanzen verfügbar ist.
> 

### <a name="add-vms-using-a-template"></a>Hinzufügen von VMs mithilfe einer Vorlage
Befolgen Sie das Beispiel bzw. die Anweisungen im [Vorlagenkatalog für den Schnellstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing), um die Anzahl der VMs in jedem Knotentyp zu ändern. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Hinzufügen von virtuellen Computern mithilfe von PowerShell- oder CLI-Befehlen
Der folgende Code ruft eine Skalierungsgruppe anhand ihres Namens ab und erhöht die **Kapazität** der Skalierungsgruppe um 1:

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Der folgende Code legt die Kapazität auf 6 fest:

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Manuelles Entfernen von virtuellen Computern aus einem Knotentyp oder einer VM-Skalierungsgruppe
Wenn Sie in einem Knotentyp abskalieren, entfernen Sie damit VM-Instanzen aus der Skalierungsgruppe. Wenn der Knotentyp die Dauerhaftigkeitsstufe „Bronze“ aufweist, weiß Service Fabric nicht, was passiert ist, und meldet, dass ein Knoten verloren gegangen ist. Daraufhin meldet Service Fabric einen fehlerhaften Zustand für den Cluster. Um diesen Zustand zu verhindern, müssen Sie den Knoten explizit aus dem Cluster entfernen und den Knotenstatus korrigieren.

Die Service Fabric-Systemdienste werden auf dem primären Knotentyp in Ihrem Cluster ausgeführt. Skalieren Sie beim Abskalieren des primären Knotentyps auf keinen Fall die Anzahl der Instanzen auf einen Wert ab, der unter dem von der [Zuverlässigkeitsstufe](service-fabric-cluster-capacity.md) vorgegebenen liegt. 
 
Für einen zustandsbehafteten Dienst muss eine bestimmte Anzahl von Knoten stets aktiv sein, um Verfügbarkeit sicherzustellen und den Status Ihres Diensts beizubehalten. Sie benötigen als äußerstes Minimum eine Anzahl von Knoten, die der Anzahl der Zielreplikatgruppen der Partition oder des Diensts entspricht.

### <a name="remove-the-service-fabric-node"></a>Entfernen des Service Fabric-Knotens

Die Schritte für das manuelle Korrigieren des Knotenstatus gelten nur für Knotentypen mit der Dauerhaftigkeitsstufe *Bronze*.  Für die Dauerhaftigkeitsstufen *Silber* und *Gold* werden diese Schritte automatisch von der Plattform ausgeführt. Weitere Informationen zur Dauerhaftigkeit finden Sie unter [Überlegungen zur Kapazitätsplanung für Service Fabric-Cluster][durability].

>[!NOTE]
> Verwalten Sie mindestens fünf Knoten für alle VM-Skalierungsgruppen, für die die Dauerhaftigkeitsstufen „Gold“ oder „Silber“ aktiviert wurden. Der Cluster wechselt in den Fehlerzustand, wenn Sie ihn auf einen Wert unterhalb dieses Schwellenwerts abskalieren, und Sie müssen die entfernten Knoten manuell bereinigen.

Der zuletzt erstellte Knoten sollte zuerst entfernt werden, damit die Knoten des Clusters über die Upgrade- und Fehlerdomänen gleichmäßig verteilt bleiben und die gleichmäßige Nutzung sichergestellt ist. Anders ausgedrückt: Die Knoten sollten in der umgekehrten Reihenfolge ihrer Erstellung entfernt werden. Der zuletzt erstellte Knoten verfügt über den höchsten `virtual machine scale set InstanceId`-Eigenschaftswert. In den unten angegebenen Codebeispielen wird der zuletzt erstellte Knoten zurückgegeben.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Der Service Fabric-Cluster muss wissen, dass dieser Knoten entfernt wird. Hierzu sind drei Schritte erforderlich:

1. Deaktivieren Sie den Knoten, sodass es sich dabei nicht mehr um ein Replikat für Daten handelt.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Beenden Sie den Knoten, damit die Service Fabric-Laufzeit richtig heruntergefahren wird und Ihre App eine Beendigungsanforderung erhält.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Entfernen Sie den Knoten aus dem Cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Nachdem diese drei Schritte für den Knoten ausgeführt wurden, kann er aus der Skalierungsgruppe entfernt werden. Bei Verwendung einer anderen Dauerhaftigkeitsstufe als [Bronze][durability] werden diese Schritte automatisch ausgeführt, wenn die Skalierungsgruppeninstanz entfernt wird.

Der folgende Codeblock ruft den zuletzt erstellten Knoten ab und deaktiviert, beendet und entfernt ihn aus dem Cluster.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

Unten im **sfctl**-Code wird der folgende Befehl verwendet, um den Wert **node-name** des zuletzt erstellten Knotens abzurufen: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Verwenden Sie die folgenden **sfctl**-Abfragen, um den Status der einzelnen Schritte zu überprüfen:
>
> **Überprüfen des Deaktivierungsstatus**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Überprüfen des Beendigungsstatus**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Abskalieren der Skalierungsgruppe

Nachdem der Service Fabric-Knoten aus dem Cluster entfernt wurde, kann die VM-Skalierungsgruppe horizontal herunterskaliert werden. Im folgenden Beispiel wird die Kapazität der Skalierungsgruppe um 1 verringert:

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Der folgende Code legt die Kapazität auf 5 fest:

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Verhaltensweisen von Service Fabric Explorer, die Sie möglicherweise beobachten
Wenn Sie einen Cluster aufskalieren, zeigt Service Fabric Explorer die Knotenanzahl (Instanzen der VM-Skalierungsgruppen) an, die zu dem Cluster gehört.  Wenn Sie einen Cluster jedoch abskalieren, wird der entfernte Knoten bzw. die entfernte VM-Instanz in einem fehlerhaften Zustand angezeigt, es sei denn, Sie rufen das Cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) mit dem entsprechenden Knotennamen auf.   

Dieses Verhalten erklärt sich wie folgt:

Die in Service Fabric Explorer aufgeführten Knoten sind ein Abbild der Informationen, die die Service Fabric-Systemdienste (besonders FM) über die derzeitige und frühere Anzahl der Knoten des Clusters besitzen. Wenn Sie eine VM-Skalierungsgruppe abskalieren, wird die VM gelöscht. Der FM-Systemdienst geht aber weiterhin davon aus, dass der Knoten, der dem gelöschten virtuellen Computer zugeordnet war, dem Cluster wieder zur Verfügung stehen wird. Service Fabric Explorer zeigt diesen Knoten daher weiterhin an (der Integritätszustand kann „fehlerhaft“ oder „unbekannt“ lauten).

Sie verfügen über zwei Optionen, um sicherzustellen, dass ein Knoten beim Entfernen eines virtuellen Computers entfernt wird:

1. Wählen Sie die Dauerhaftigkeitsstufe „Gold“ oder „Silber“ für die Knotentypen in Ihrem Cluster aus, die die Infrastrukturintegration für Sie bereitstellt. Wenn Sie abskalieren, werden so automatisch die Knoten aus den Systemdiensten (FM) entfernt.
[hier mit den Details der Dauerhaftigkeitsstufen](service-fabric-cluster-capacity.md)

> [!NOTE]
> Verwalten Sie mindestens fünf Knoten für alle VM-Skalierungsgruppen, für die die Dauerhaftigkeitsstufen „Gold“ oder „Silber“ aktiviert wurden. Der Cluster wechselt in den Fehlerzustand, wenn Sie ihn auf einen Wert unterhalb dieses Schwellenwerts abskalieren, und Sie müssen die entfernten Knoten manuell bereinigen.

2. Sobald die VM-Instanz abskaliert wurde, müssen Sie das Cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) aufrufen.

> [!NOTE]
> Um Verfügbarkeit sicherzustellen und den Zustand beizubehalten, muss eine bestimmte Anzahl von Knoten in einem Service Fabric-Cluster stets in Betrieb sein. Dies wird auch als „Aufrechterhalten eines Quorums“ bezeichnet. Daher ist es für gewöhnlich nicht sicher, alle Computer innerhalb des Clusters herunterzufahren, sofern Sie nicht zuvor eine [vollständige Sicherung des Zustands](service-fabric-reliable-services-backup-restore.md) durchgeführt haben.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel, um mehr über die Kapazitätsplanung von Clustern, das Upgraden von Clusters und das Partitionieren von Diensten zu erfahren:

* [Planen Sie Ihre Clusterkapazität](service-fabric-cluster-capacity.md)
* [Clusterupgrades](service-fabric-cluster-upgrade.md)
* [Die Partitionierung statusbehafteter Dienste für maximale Skalierung](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
