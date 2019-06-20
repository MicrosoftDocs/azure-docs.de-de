---
title: 'Fehler: Azure SKU nicht verfügbar | Microsoft-Dokumentation'
description: Hier wird beschrieben, wie Sie den Fehler vom Typ „SKU nicht verfügbar“ während der Bereitstellung beheben.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: 1dd0532452c3558e53f0236998953d2055ed328c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390745"
---
# <a name="resolve-errors-for-sku-not-available"></a>Beheben des Fehlers vom Typ „SKU nicht verfügbar“

In diesem Artikel wird beschrieben, wie Sie den Fehler **SkuNotAvailable** beheben. Wenn Sie in dieser Region oder einer anderen Region keine geeignete SKU finden, die Ihre Geschäftsanforderungen erfüllt, übermitteln Sie eine [SKU-Anforderung](https://aka.ms/skurestriction) an den Azure-Support.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

Beim Bereitstellen einer Ressource (in der Regel ein virtueller Computer) werden der folgende Fehlercode und die folgende Fehlermeldung angezeigt:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Ursache

Sie erhalten diesen Fehler, wenn die ausgewählte Ressourcen-SKU (z.B. die Größe des virtuellen Computers) für den ausgewählten Standort nicht verfügbar ist.

## <a name="solution-1---powershell"></a>Lösung 1: PowerShell

Verwenden Sie den Befehl [Get AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku), um zu ermitteln, welche SKUs in einer Region verfügbar sind. Filtern Sie die Ergebnisse nach Standort. Für diesen Befehl benötigen Sie die aktuelle Version von PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Die Ergebnisse enthalten eine Liste von SKUs für den Standort und alle Einschränkungen für diese SKU. Beachten Sie, dass die SKU möglicherweise als `NotAvailableForSubscription` aufgeführt wird.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Lösung 2: Azure CLI

Verwenden Sie den Befehl `az vm list-skus`, um zu ermitteln, welche SKUs in einer Region verfügbar sind. Verwenden Sie den Parameter `--location`, um die Ausgabe auf den verwendeten Standort zu filtern. Verwenden Sie den Parameter `--size`, um nach dem Teil eines Namens für die Größe zu suchen.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Der Befehl gibt Ergebnisse ähnlich den folgenden zurück:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Lösung 3: Azure-Portal

Verwenden Sie das [Portal](https://portal.azure.com), um zu ermitteln, welche SKUs in einer Region verfügbar sind. Melden Sie sich beim Portal an, und fügen Sie über die Oberfläche eine Ressource hinzu. Beim Festlegen der Werte werden die verfügbaren SKUs für die Ressource angezeigt. Sie müssen die Bereitstellung nicht abschließen.

Starten Sie beispielsweise den Prozess zum Erstellen eines virtuellen Computers. Um andere verfügbare Größen anzuzeigen, wählen Sie **Größe ändern** aus.

![Erstellen eines virtuellen Computers](./media/resource-manager-sku-not-available-errors/create-vm.png)

Sie können die verfügbaren Größen filtern und durch sie scrollen.

![Verfügbare SKUs](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Lösung 4: REST

Verwenden Sie den Vorgang [Ressourcen-SKUs – Auflisten](/rest/api/compute/resourceskus/list), um zu ermitteln, welche SKUs in einer Region verfügbar sind.

Verfügbare SKUs und Regionen werden im folgenden Format zurückgegeben:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

