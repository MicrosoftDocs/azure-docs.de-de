---
title: Verfügbarkeitszonenübergreifendes Erstellen eines Pools
description: Hier erfahren Sie, wie Sie zum Schutz vor Ausfällen einen Batch-Pool mit zonaler Richtlinie erstellen.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 98109e1b74106bc636eaa715575e4b30ab29f9e2
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056886"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Verfügbarkeitszonenübergreifendes Erstellen eines Azure Batch-Pools

Azure-Regionen, die [Verfügbarkeitszonen](https://azure.microsoft.com/global-infrastructure/availability-zones/) unterstützen, verfügen über mindestens drei getrennte Zonen mit jeweils eigener, unabhängiger Stromversorgung, eigenem Netzwerk und eigenem Kühlsystem. Wenn Sie mithilfe der VM-Konfiguration einen Azure Batch-Pool erstellen, haben Sie die Möglichkeit, den Batch-Pool verfügbarkeitszonenübergreifend bereitzustellen. Wenn Sie Ihren Pool mit dieser zonalen Richtlinie erstellen, können Sie Ihre Batch-Computeknoten besser vor Ausfällen auf Azure-Rechenzentrumsebene schützen.

So können Sie beispielsweise Ihren Pool mit zonaler Richtlinie in einer Azure-Region erstellen, die drei Verfügbarkeitszonen unterstützt. Sollte es in einer der Verfügbarkeitszonen bei einem Azure-Rechenzentrum zu einem Ausfall der Infrastruktur kommen, verfügt Ihr Batch-Pool immer noch über fehlerfreie Knoten in den beiden anderen Verfügbarkeitszonen, sodass der Pool weiterhin für die Aufgabenplanung zur Verfügung steht.

## <a name="regional-support-and-other-requirements"></a>Regionale Unterstützung und andere Anforderungen

Batch bietet die gleiche Unterstützung von Verfügbarkeitszonen wie Azure. Um die zonale Option verwenden zu können, muss Ihr Pool in einer [unterstützten Azure-Region](../availability-zones/az-region.md) erstellt werden.

Damit Ihr Batch-Pool verfügbarkeitszonenübergreifend zugeordnet wird, muss die Azure-Region, in der der Pool erstellt wird, die angeforderte VM-SKU in mehreren Zonen unterstützen. Zur Überprüfung dieser Anforderung können Sie die [API zum Auflisten von Ressourcen-SKUs](/rest/api/compute/resourceskus/list) aufrufen und sich das Feld **locationInfo** von [resourceSku](/rest/api/compute/resourceskus/list#resourcesku) ansehen. Achten Sie darauf, dass für die angeforderte VM-SKU mehrere Zonen unterstützt werden.

Vergewissern Sie sich bei [Batch-Konten im Modus „Benutzerabonnement“](accounts.md#batch-accounts), dass für das Abonnement, in dem Sie Ihren Pool erstellen, keine Zonenangebotseinschränkung für die angeforderte VM-SKU gilt. Rufen Sie zur Überprüfung dieser Anforderung die [API zum Auflisten von Ressourcen-SKUs](/rest/api/compute/resourceskus/list) auf, und sehen Sie sich [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions) an. Ist eine Zoneneinschränkung vorhanden, können Sie ein [Supportticket](../azure-portal/supportability/sku-series-unavailable.md) erstellen, um die Zoneneinschränkung zu entfernen.

Beachten Sie außerdem, dass Sie keinen Pool mit einer zonalen Richtlinie erstellen können, wenn die Kommunikation zwischen Knoten aktiviert ist und eine [VM-SKU mit InfiniBand-Unterstützung](../virtual-machines/workloads/hpc/enable-infiniband.md) verwendet wird.

## <a name="create-a-batch-pool-across-availability-zones"></a>Verfügbarkeitszonenübergreifendes Erstellen eines Batch-Pools

In den folgenden Beispielen wird die verfügbarkeitszonenübergreifende Erstellung eines Batch-Pools veranschaulicht.

> [!NOTE]
> Wenn Sie Ihren Pool mit einer zonalen Richtlinie erstellen, wird vom Batch-Dienst versucht, Ihren Pool in allen Verfügbarkeitszonen der ausgewählten Region zuzuordnen. Die Angabe einer bestimmten zonenübergreifenden Zuordnung ist nicht möglich.

### <a name="batch-management-client-net-sdk"></a>.NET SDK für den Batchverwaltungsclient

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Batch-REST-API

REST-API-URL

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Anforderungstext

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Erfahren Sie mehr über das [Erstellen eines Pools in einem Subnetz eines virtuellen Azure-Netzwerks](batch-virtual-network.md).
- Erfahren Sie mehr über das [Erstellen eines Azure Batch-Pools ohne öffentliche IP-Adressen](./batch-pool-no-public-ip-address.md).

