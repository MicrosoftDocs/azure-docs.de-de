---
title: Entfernen eines Knotentyps in Azure Service Fabric | Microsoft-Dokumentation
description: Erfahren Sie, wie ein Knotentyp aus einem in Azure ausgeführten Service Fabric-Cluster entfernt wird.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 193a24aebff8f7de60752e53bbc1b18dd5c54f33
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482197"
---
# <a name="remove-a-service-fabric-node-type"></a>Entfernen eines Service Fabric-Knotentyps
In diesem Artikel wird beschrieben, wie Sie einen Azure Service Fabric-Cluster skalieren, indem Sie einen vorhandenen Knotentyp aus einem Cluster entfernen. Ein Service Fabric-Cluster enthält eine per Netzwerk verbundene Gruppe von virtuellen oder physischen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Ein physischer oder virtueller Computer, der Teil eines Clusters ist, wird als Knoten bezeichnet. VM-Skalierungsgruppen sind eine Azure-Computeressource, mit der Sie eine Sammlung von virtuellen Computern als Gruppe bereitstellen und verwalten können. Jeder Knotentyp, der in einem Azure-Cluster definiert ist, wird [als separate Skalierungsgruppe eingerichtet](service-fabric-cluster-nodetypes.md). Jeder Knotentyp kann dann separat verwaltet werden. Nachdem Sie einen Service Fabric-Cluster erstellt haben, können Sie einen Cluster horizontal skalieren, indem Sie einen Knotentyp (VM-Skalierungsgruppe) und alle seine Knoten entfernen.  Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster.  Wenn der Cluster skaliert wird, werden Ihre Anwendungen ebenfalls automatisch skaliert.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verwenden Sie [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype), um einen Service Fabric-Knotentyp zu entfernen.

Die folgenden drei Vorgänge treten beim Aufruf von „Remove-AzServiceFabricNodeType“ auf:
1.  Die VM-Skalierungsgruppe hinter dem Knotentyp wird gelöscht.
2.  Der Knotentyp wird aus dem Cluster entfernt.
3.  Für alle Knoten in diesen Knotentyp wird der gesamte Zustand für diesen Knoten aus dem System entfernt. Wenn Dienste auf diesem Knoten vorhanden sind, werden sie zuerst auf einen anderen Knoten verschoben. Wenn der Cluster-Manager keinen Knoten für das Replikat bzw. den Dienst finden kann, wird der Vorgang verzögert/blockiert.

> [!WARNING]
> Es wird nicht empfohlen, regelmäßig „Remove-AzServiceFabricNodeType“ zum Entfernen eines Knotentyps aus einem Produktionscluster zu verwenden. Es handelt sich um einen sehr gefährlichen Befehl, da er die VM-Skalierungsgruppenressource hinter dem Knotentyp löscht. 

## <a name="durability-characteristics"></a>Dauerhaftigkeitsmerkmale
Bei der Verwendung von „Remove-AzServiceFabricNodeType“ wird die Sicherheit über die Geschwindigkeit gestellt. Der Knotentyp muss aus den folgenden Gründen die [Dauerhaftigkeitsstufe](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) „Silber“ oder „Gold“ aufweisen:
- „Bronze“ gibt Ihnen keine Garantie für die Speicherung von Zustandsinformationen.
- Die Dauerhaftigkeit von Typ „Silber“ und „Gold“ fängt alle Änderungen an der Skalierungsgruppe ab.
- Mit „Gold“ erhalten Sie außerdem Kontrolle über die Azure-Updates, die im Hintergrund der Skalierungsgruppe erfolgen.

Service Fabric „orchestriert“ die zugrunde liegenden Änderungen und Updates, damit keine Daten verloren gehen. Wenn Sie einen Knoten mit der Dauerhaftigkeit „Bronze“ entfernen, können Sie jedoch Zustandsinformationen verlieren. Wenn Sie einen primären Knotentyp entfernen und Ihre Anwendung zustandslos ist, ist „Bronze“ akzeptabel. Wenn Sie zustandsbehaftete Workloads in der Produktion ausführen, sollte die Mindestkonfiguration „Silber“ sein. Analog dazu sollte für Produktionsszenarien der primären Knotentyp immer „Silber“ oder „Gold“ sein.

### <a name="more-about-bronze-durability"></a>Weitere Informationen zur Dauerhaftigkeitsstufe „Bronze“

Wenn Sie einen Knotentyp „Bronze“ entfernen, fallen alle Knoten im Knotentyp sofort aus. Service Fabric fängt keine Knotenupdates von VM-Skalierungsgruppen vom Typ „Bronze“ ab, daher fallen alle virtuellen Computer sofort aus. Wenn zustandsbehaftete Elemente auf diesen Knoten vorhanden waren, gehen die Daten verloren. Selbst als zustandslose Elemente nehmen alle Knoten in Service Fabric am Ring teil, sodass eine ganze Umgebung verloren gehen kann, was den Cluster selbst destabilisieren könnte.

## <a name="recommended-node-type-removal-process"></a>Empfohlener Knotentypen-Entfernungsvorgang

Führen Sie das Cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) aus, um den Knotentyp zu entfernen.  Die Ausführung des Cmdlets nimmt einige Zeit in Anspruch.  Führen Sie dann [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) auf allen Knoten aus, die entfernt werden sollen.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über die [Dauerhaftigkeitsmerkmale](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) eines Clusters.
- Erfahren Sie mehr über [Knotentypen und VM-Skalierungsgruppen](service-fabric-cluster-nodetypes.md).
- Erfahren Sie mehr über [Service Fabric-Clusterskalierung](service-fabric-cluster-scaling.md).
