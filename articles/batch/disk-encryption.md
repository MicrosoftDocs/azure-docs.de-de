---
title: Erstellen eines Pools mit aktivierter Datenträgerverschlüsselung
description: Erfahren Sie, wie Sie die Disk Encryption-Konfiguration verwenden, um Knoten mit einem plattformseitig verwalteten Schlüssel zu verschlüsseln.
author: pkshultz
ms.topic: how-to
ms.date: 01/27/2021
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 41fc827459b454e2bcb120a925cdab8fcd46e310
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055313"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Erstellen eines Pools mit aktivierter Datenträgerverschlüsselung

Wenn Sie einen Azure Batch-Pool mithilfe einer [VM-Konfiguration](nodes-and-pools.md#virtual-machine-configuration) erstellen, können Sie Computeknoten im Pool mit einem plattformseitig verwalteten Schlüssel verschlüsseln, indem Sie die Datenträgerverschlüsselungskonfiguration angeben.

In diesem Artikel wird erläutert, wie ein Batch-Pool mit aktivierter Datenträgerverschlüsselung erstellt wird.

> [!IMPORTANT]
> Die Verschlüsselung auf dem Host mithilfe eines plattformseitig verwalteten Schlüssels in Azure Batch ist aktuell in der öffentlichen Vorschau für die Regionen „USA, Osten“, „USA, Westen 2“, „USA, Süden-Mitte“, „US Gov Virginia“ und „US Gov Arizona“ verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Gründe für die Verwendung eines Pools mit Disk Encryption-Konfiguration

Wenn Sie über einen Batch-Pool verfügen, können Sie Daten auf den Betriebssystem- und temporären Datenträgern des Computeknotens speichern und dort auf die Daten zugreifen. Diese Daten lassen sich mit geringem Aufwand schützen, indem Sie den serverseitigen Datenträger mit einem plattformseitig verwalteten Schlüssel verschlüsseln.

Batch wendet basierend auf der Poolkonfiguration und der regionalen Unterstützung eine dieser Datenträgerverschlüsselungstechnologien auf Computeknoten an.

- [Verschlüsselung von verwalteten Datenträgern im Ruhezustand mit plattformseitig verwalteten Schlüsseln](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Verschlüsselung auf dem Host mithilfe eines plattformseitig verwalteten Schlüssels](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure-Datenträgerverschlüsselung](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Sie können nicht angeben, welche Verschlüsselungsmethode auf die Knoten in Ihrem Pool angewendet wird. Stattdessen geben Sie die Zieldatenträger an, die auf den Knoten verschlüsselt werden sollen. Batch wählt dann die geeignete Verschlüsselungsmethode aus und stellt sicher, dass die angegebenen Datenträger auf dem Computeknoten verschlüsselt werden.

> [!IMPORTANT]
> Wenn Sie den Pool mit einem [benutzerdefinierten Image](batch-sig-images.md) erstellen, können Sie die Datenträgerverschlüsselung nur aktivieren, wenn Sie Windows-VMs verwenden.

## <a name="azure-portal"></a>Azure-Portal

Wählen Sie beim Erstellen eines Batch-Pools im Azure-Portal unter **Disk Encryption-Konfiguration** entweder **TemporaryDisk** oder **OsAndTemporaryDisk** aus.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Screenshot der Disk Encryption-Konfigurationsoption im Azure-Portal.":::

Nachdem der Pool erstellt wurde, werden die Disk Encryption-Konfigurationsziele im Abschnitt **Eigenschaften** des Pools angezeigt.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Screenshot mit den Disk Encryption-Konfigurationszielen im Azure-Portal.":::

## <a name="examples"></a>Beispiele

Die folgenden Beispiele zeigen, wie Sie die Betriebssystem- und temporären Datenträger in einem Batch-Pool mithilfe des Batch .NET SDK, der Batch-REST-API und der Azure CLI verschlüsseln.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch-REST-API

REST-API-URL:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Anforderungstext:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [serverseitige Verschlüsselung von Azure Disk Storage](../virtual-machines/disk-encryption.md).
- Eine ausführliche Übersicht über Batch finden Sie unter [Workflow und Ressourcen des Batch-Diensts](batch-service-workflow-features.md).
