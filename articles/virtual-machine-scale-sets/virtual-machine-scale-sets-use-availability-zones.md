---
title: Erstellen einer Azure-Skalierungsgruppe, die Verfügbarkeitszonen verwendet
description: Erfahren Sie, wie Skalierungsgruppen für virtuelle Azure Computer erstellt werden, die Verfügbarkeitszonen verwenden, um bessere Redundanz bei Ausfällen bereitzustellen.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c5ddd5846be91e9fc99a251d6ad45ade8bde2937
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016657"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Erstellen einer VM-Skalierungsgruppe, die Verfügbarkeitszonen verwendet

Um Ihre VM-Skalierungsgruppen vor Fehlern auf Rechenzentrumsebene zu schützen, können Sie eine Skalierungsgruppe über mehrere Verfügbarkeitszonen erstellen. Azure-Regionen, die Verfügbarkeitszonen unterstützen, weisen mindestens drei getrennte Zonen auf, mit je eigener, unabhängiger Stromquelle, Netzwerk und Kühlung. Weitere Informationen finden Sie in der [Overview of Availability Zones (Übersicht über Verfügbarkeitszonen)](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Überlegungen zur Verfügbarkeit

Wenn Sie eine regionale (nicht zonale) Skalierungsgruppe in einer oder mehreren Zonen gemäß API-Version *2017-12-01* bereitstellen, haben Sie die folgenden Verfügbarkeitsoptionen:
- Maximale Zuweisung (platformFaultDomainCount = 1)
- Statische festgelegte Zuweisung (platformFaultDomainCount = 5)
- an Fehlerdomänen des Speicherdatenträgers ausgerichtete Zuweisung (platforFaultDomainCount = 2 oder 3)

Bei der maximalen Zuweisung verteilt die Skalierungsgruppe Ihre VMs in jeder Zone auf so viele Fehlerdomänen wie möglich. Diese Zuweisung kann für mehr oder weniger als fünf Fehlerdomänen pro Zone erfolgen. Bei der statischen festgelegten Zuweisung verteilt die Skalierungsgruppe Ihre VMs exakt auf fünf Fehlerdomänen pro Zone. Wenn die Skalierungsgruppe nicht fünf einzelne Fehlerdomänen pro Zone finden kann, um die Zuordnungsanforderung zu erfüllen, ist die Anforderung nicht erfolgreich.

**Wir empfehlen die Bereitstellung mit maximaler Zuweisung für die meisten Workloads**, da diese Vorgehensweise normalerweise die bestmögliche Zuweisung ergibt. Falls bei Ihnen Replikate auf einzelne Einheiten für die Hardwareisolation verteilt werden müssen, raten wir Ihnen zum Verteilen auf Verfügbarkeitszonen und zur Verwendung der maximalen Zuweisung in jeder Zone.

> [!NOTE]
> Bei der maximalen Zuweisung ist in der VM-Instanzansicht für Skalierungsgruppen und in den Instanzmetadaten nur eine Fehlerdomäne zu sehen. Dies gilt unabhängig davon, auf wie viele Fehlerdomänen die VMs verteilt werden. Die Zuweisung in einer Zone erfolgt jeweils implizit.

### <a name="placement-groups"></a>Platzierungsgruppen

Bei der Bereitstellung einer Skalierungsgruppe haben Sie auch die Möglichkeit, diese mit einer einzelnen oder mit mehreren [Platzierungsgruppen](./virtual-machine-scale-sets-placement-groups.md) pro Verfügbarkeitszone durchzuführen. Bei regionalen (nicht zonalen) Skalierungsgruppen kann zwischen einer einzelnen Platzierungsgruppe oder mehreren Platzierungsgruppen in der Region gewählt werden. Für die meisten Workloads empfehlen wir mehrere Platzierungsgruppen, um eine bessere Skalierbarkeit zu erzielen. In der API-Version *2017-12-01* werden für Skalierungsgruppen standardmäßig mehrere Platzierungsgruppen für einzelne und mehrere Zonen verwendet. Für regionale (nicht zonale) Skalierungsgruppen wird dagegen standardmäßig nur eine einzelne Platzierungsgruppe genutzt.

> [!NOTE]
> Bei der maximalen Zuweisung müssen Sie mehrere Platzierungsgruppen verwenden.

### <a name="zone-balancing"></a>Sicherstellen eines Zonengleichgewichts

Für Skalierungsgruppen, die über mehrere Zonen hinweg bereitgestellt werden, können Sie auch „bestmögliches Zonengleichgewicht“ oder „strenges Zonengleichgewicht“ wählen. Eine Skalierungsgruppe befindet sich „im Gleichgewicht“, wenn die gleiche Anzahl von VMs oder +\\– 1 VM in allen anderen Zonen der Skalierungsgruppe liegt. Beispiel:

- Eine Skalierungsgruppe wird mit zwei VMs in Zone 1, drei VMs in Zone 2 und drei VMs in Zone 3 als im Gleichgewicht befindlich angesehen. Es ist nur eine Zone mit einer unterschiedlichen VM-Anzahl vorhanden, und diese Anzahl ist nur 1 geringer als die Anzahl der VMs in anderen Zonen. 
- Eine Skalierungsgruppe wird mit einer VM in Zone 1, drei VMs in Zone 2 und drei VMs in Zone 3 als nicht im Gleichgewicht befindlich angesehen. Zone 1 verfügt über zwei VMs weniger als Zone 2 und 3.

Es ist möglich, dass VMs in der Skalierungsgruppe erfolgreich erstellt werden, dies ist für die Bereitstellung von Erweiterungen dieser VMs jedoch nicht der Fall. Diese VMs mit Erweiterungsfehlern werden trotzdem mitgezählt, wenn ermittelt wird, ob sich eine Skalierungsgruppe im Gleichgewicht befindet. Eine Skalierungsgruppe mit drei VMs in Zone 1, drei VMs in Zone 2 und drei VMs in Zone 3 wird beispielsweise auch dann als im Gleichgewicht befindlich angesehen, wenn alle Erweiterungen in Zone 1 nicht erfolgreich und alle Erweiterungen in den Zonen 2 und 3 erfolgreich waren.

Beim bestmöglichen Zonengleichgewicht versucht die Skalierungsgruppe, das horizontale Herunter- und Hochskalieren durchzuführen, während das Gleichgewicht beibehalten wird. Falls dies aus bestimmten Gründen nicht möglich ist (wenn beispielsweise eine Zone ausfällt und die Skalierungsgruppe in dieser Zone keine neue VM erstellen kann), lässt die Skalierungsgruppe ein vorübergehendes Ungleichgewicht zu, um das erfolgreiche Ab- und Aufskalieren zu ermöglichen. Bei den nachfolgenden Versuchen zur horizontalen Skalierung fügt die Skalierungsgruppe den Zonen VMs hinzu, die mehr VMs benötigen, damit sich die Skalierungsgruppe im Gleichgewicht befindet. Entsprechend entfernt die Skalierungsgruppe bei nachfolgenden Versuchen, das horizontale Herunterskalieren durchzuführen, VMs aus den Zonen, die weniger VMs benötigen, damit sich die Skalierungsgruppe im Gleichgewicht befindet. Beim „strengen Zonengleichgewicht“ enden für die Skalierungsgruppe alle Versuche zum horizontalen Herunter- oder Hochskalieren mit einem Fehler, falls dies zu einem Ungleichgewicht führen würde.

Legen Sie *zoneBalance* auf *false* fest, um das bestmögliche Zonengleichgewicht zu verwenden. Dies ist die Standardeinstellung in API-Version *2017-12-01*. Legen Sie *zoneBalance* auf *true* fest, um das strenge Zonengleichgewicht zu verwenden.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Skalierungsgruppen mit einer Zone und zonenredundante Skalierungsgruppen

Wenn Sie eine VM-Skalierungsgruppe bereitstellen, können eine einzelne Verfügbarkeitszone in einer Region oder mehrere Zonen verwenden.

Wenn Sie eine Skalierungsgruppe in einer einzelnen Zone erstellen, können Sie steuern, in welcher Zone alle diese VM-Instanzen ausgeführt werden. Die Skalierungsgruppe wird dann nur innerhalb dieser Zone verwaltet und automatisch skaliert. Durch eine zonenredundante Skalierungsgruppe können Sie eine einzelne Skalierungsgruppe erstellen, die sich über mehrere Zonen erstreckt. Während der Erstellung werden VM-Instanzen standardmäßig gleichmäßig auf Zonen verteilt. Wenn in einer der Zonen eine Unterbrechung auftritt, wird eine Skalierungsgruppe nicht automatisch horizontal hochskaliert, um die Kapazität zu erhöhen. Eine bewährte Methode besteht darin, Regeln für die automatische Skalierung anhand der CPU- oder Arbeitsspeicherauslastung zu konfigurieren. Über diese Regeln für automatische Skalierung könnte die Skalierungsgruppe auf den Verlust der VM-Instanzen in dieser einen Zone reagieren, indem neue Instanzen in den verbleibenden betriebsbereiten Zonen horizontal hochskaliert werden.

Damit Verfügbarkeitszonen verwendet werden können, muss Ihre Skalierungsgruppe in einer [unterstützten Azure-Region](../availability-zones/az-region.md) erstellt werden. Sie können mit einer der folgenden Methoden eine Skalierungsgruppe erstellen, die Verfügbarkeitszonen verwendet:

- [Azure portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure-Ressourcen-Manager-Vorlagen](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Der Erstellungsprozess einer Skalierungsgruppe, die eine Verfügbarkeitszone verwendet, ist der gleiche, der im [Artikel „Erste Schritte“](quick-create-portal.md) ausführlich beschrieben wird. Wenn Sie eine unterstützte Azure-Region auswählen, können Sie eine Skalierungsgruppe in einer oder mehreren Verfügbarkeitszonen erstellen, wie im folgenden Beispiel gezeigt:

![Erstellen einer Skalierungsgruppe in einer einzelnen Verfügbarkeitszone](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Die Skalierungsgruppe und die unterstützenden Ressourcen, wie etwa der Azure Load Balancer und die öffentliche IP-Adresse, werden in der von Ihnen angegebenen einzelnen Zone erstellt.

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

Der Erstellungsprozess einer Skalierungsgruppe, die eine Verfügbarkeitszone verwendet, ist der gleiche, der im [Artikel „Erste Schritte“](quick-create-cli.md) ausführlich beschrieben wird. Damit Verfügbarkeitszonen verwendet werden können, müssen Sie Ihre Skalierungsgruppe in einer unterstützten Azure-Region erstellen.

Fügen Sie dem [az vmss create](/cli/azure/vmss)-Befehl den `--zones`-Parameter hinzu, und geben Sie die zu verwendende Zone an (z. B. Zone *1*, *2* oder *3*). Im folgenden Beispiel wird eine Skalierungsgruppe mit einer Zone namens *myScaleSet* in Zone *1* erstellt:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Ein vollständiges Beispiel einer Skalierungsgruppe mit einer einzelnen Zone und Netzwerkressourcen finden Sie in [diesem Beispielskript für die Befehlszeilenschnittstelle](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh).

### <a name="zone-redundant-scale-set"></a>Zonenredundante Skalierungsgruppe

Um eine zonenredundante Skalierungsgruppe zu erstellen, verwenden Sie die öffentliche IP-Adresse einer *Standard*-SKU und einen Lastenausgleich. Für eine verbesserte Redundanz erstellt die *Standard*-SKU zonenredundante Netzwerkressourcen. Weitere Informationen finden Sie unter [Übersicht: Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) und [Load Balancer Standard und Verfügbarkeitszonen](../load-balancer/load-balancer-standard-availability-zones.md).

Geben Sie zum Erstellen einer zonenredundanten Skalierungsgruppe mehrere Zonen mit dem Parameter `--zones` an. Im folgenden Beispiel wird eine zonenredundante Skalierungsgruppe namens *myScaleSet* in den Zonen *1, 2 und 3* erstellt:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe in den von Ihnen angegebenen Zonen dauert einige Minuten. Ein vollständiges Beispiel einer zonenredundanten Skalierungsgruppe mit Netzwerkressourcen finden Sie in [diesem Beispielskript für die Befehlszeilenschnittstelle](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh).

## <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

Damit Verfügbarkeitszonen verwendet werden können, müssen Sie Ihre Skalierungsgruppe in einer unterstützten Azure-Region erstellen. Fügen Sie dem Befehl [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) den Parameter `-Zone` hinzu, und geben Sie die zu verwendende Zone an (z.B. Zone *1*, *2* oder *3*).

Im folgenden Beispiel wird eine Skalierungsgruppe mit einer einzelnen Zone namens *myScaleSet* in Zone *1* von *USA, Osten 2* erstellt: Die Azure-Netzwerkressourcen für virtuelles Netzwerk, öffentliche IP-Adresse und Lastenausgleich werden automatisch erstellt. Geben Sie Ihre gewünschten Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe an, wenn Sie dazu aufgefordert werden:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zonenredundante Skalierungsgruppe

Geben Sie zum Erstellen einer zonenredundanten Skalierungsgruppe mehrere Zonen mit dem Parameter `-Zone` an. Im folgenden Beispiel wird eine zonenredundante Skalierungsgruppe namens *myScaleSet* in den Zonen *1, 2 und 3* von *USA, Osten 2* erstellt: Die zonenredundanten Azure-Netzwerkressourcen für virtuelles Netzwerk, öffentliche IP-Adresse und Lastenausgleich werden automatisch erstellt. Geben Sie Ihre gewünschten Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe an, wenn Sie dazu aufgefordert werden:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Der Erstellungsprozess einer Skalierungsgruppe, die eine Verfügbarkeitszone verwendet, ist der gleiche, der im Artikel „Erste Schritte“ für [Linux](quick-create-template-linux.md) oder [Windows](quick-create-template-windows.md) ausführlich beschrieben wird. Damit Verfügbarkeitszonen verwendet werden können, müssen Sie Ihre Skalierungsgruppe in einer unterstützten Azure-Region erstellen. Fügen Sie in Ihrer Vorlage dem Ressourcentyp *Microsoft.Compute/virtualMachineScaleSets* die Eigenschaft `zones` hinzu, und geben Sie die zu verwendende Zone an (z. B. Zone *1*, *2* oder *3*).

Im folgenden Beispiel wird eine Linux-Skalierungsgruppe mit einer einzelnen Zone namens *myScaleSet* in Zone *1* von *USA, Osten 2* erstellt:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Ein vollständiges Beispiel einer Skalierungsgruppe mit einer einzelnen Zone und Netzwerkressourcen finden Sie in [dieser Resource Manager-Beispielvorlage](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json).

### <a name="zone-redundant-scale-set"></a>Zonenredundante Skalierungsgruppe

Um eine zonenredundante Skalierungsgruppe zu erstellen, geben Sie in der `zones`-Eigenschaft für den Ressourcentyp *Microsoft.Compute/virtualMachineScaleSets* mehrere Werte an. Im folgenden Beispiel wird eine zonenredundante Skalierungsgruppe namens *myScaleSet* in den Zonen *1, 2 und 3* von *USA, Osten 2* erstellt:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Wenn Sie eine öffentliche IP-Adresse oder einen Lastenausgleich erstellen, geben Sie die Eigenschaft *"sku": { "name": "Standard" }"* an, um zonenredundante Netzwerkressourcen zu erstellenden. Sie müssen außerdem eine Netzwerksicherheitsgruppe und Regeln zum Zulassen von Datenverkehr erstellen. Weitere Informationen finden Sie unter [Übersicht: Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) und [Load Balancer Standard und Verfügbarkeitszonen](../load-balancer/load-balancer-standard-availability-zones.md).

Ein vollständiges Beispiel einer zonenredundanten Skalierungsgruppe mit Netzwerkressourcen finden Sie in [dieser Resource Manager-Beispielvorlage](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt eine Skalierungsgruppe in einer Verfügbarkeitszone erstellt haben, können Sie lernen, wie das [Bereitstellen von Anwendungen auf VM-Skalierungsgruppen](tutorial-install-apps-cli.md) oder das [Verwenden von automatischer Skalierung bei VM-Skalierungsgruppen](tutorial-autoscale-cli.md) funktioniert.
