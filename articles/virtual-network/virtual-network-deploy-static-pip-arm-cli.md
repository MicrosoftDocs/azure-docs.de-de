---
title: Erstellen einer VM mit einer statischen öffentlichen IP-Adresse – Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine VM mit einer statischen öffentlichen IP-Adresse mithilfe der Azure-Befehlszeilenschnittstelle (CLI) erstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 4b5b0453d4f9935eed9acd5059c0aa9741b25524
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730469"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Erstellen einer VM mit einer statischen öffentlichen IP-Adresse mithilfe der Azure CLI

Sie können einen virtuellen Computer (VM) mit einer statischen öffentlichen IP-Adresse erstellen. Mit einer öffentlichen IP-Adresse können Sie über das Internet mit einer VM kommunizieren. Um sicherzustellen, dass sich die Adresse niemals ändert, weisen Sie anstelle einer dynamischen Adresse eine statische öffentliche IP-Adresse zu. Lesen Sie im folgenden Artikel mehr über [statische öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Um eine öffentliche dynamische IP-Adresse, die einem vorhandenen virtuellen Computer zugewiesen ist, in eine statische Adresse zu ändern oder mit privaten IP-Adressen zu arbeiten, lesen Sie [Hinzufügen, Ändern oder Entfernen von IP-Adressen](virtual-network-network-interface-addresses.md). Für öffentliche IP-Adressen fällt eine [Schutzgebühr](https://azure.microsoft.com/pricing/details/ip-addresses) an, und für die Anzahl der öffentlichen IP-Adressen, die Sie pro Abonnement verwenden können, ist ein [Grenzwert](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) festgelegt.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Auf dem lokalen Computer oder mithilfe von Azure Cloud Shell können Sie die folgenden Schritte durchführen. [Azure PowerShell muss installiert](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) sein, damit Sie den lokalen Computer verwenden können. Wählen Sie oben rechts in einem beliebigen Befehlsfelds **Testen** aus, um Azure Cloud Shell zu verwenden. Cloud Shell meldet Sie in Azure an.

1. Wenn Sie Cloud Shell bereits verwenden, fahren Sie mit Schritt 2 fort. Öffnen Sie eine Befehlssitzung, und melden Sie sich mit `az login` in Azure an.
2. Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe in der Azure-Region „USA, Osten“ erstellt:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer. Die Option `--public-ip-address-allocation=static` weist dem virtuellen Computer eine statische öffentliche IP-Adresse zu. Das folgende Beispiel erstellt eine Ubuntu-VM mit einer statischen öffentlichen IP-Adresse mit der SKU „Basic“ mit dem Namen *myPublicIpAddress*:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Wenn die öffentliche IP-Adresse die SKU „Standard“ aufweisen muss, fügen Sie dem vorherigen Befehl `--public-ip-sku Standard` hinzu. [Hier erfahren Sie mehr über SKUs für öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#sku). Wenn die VM dem Back-End-Pool einer öffentlichen Azure Load Balancer-Instanz hinzugefügt wird, muss die SKU der öffentlichen IP-Adresse der VM der SKU der öffentlichen IP-Adresse des Lastenausgleichsmoduls entsprechen. Weitere Informationen finden Sie unter [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Zeigen Sie die zugewiesene öffentliche IP-Adresse an, und bestätigen Sie mit [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show), dass sie als statische Adresse mit der Basic-SKU erstellt wurde:

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure hat eine öffentliche IP-Adresse über Adressen in zugewiesen, die in der Region, in der der virtuelle Computer erstellt wurde, verwendet werden. Sie können die Liste von Bereichen (Präfixen) für die [öffentliche Azure-Cloud](https://www.microsoft.com/download/details.aspx?id=56519), die [Azure US Government-Cloud](https://www.microsoft.com/download/details.aspx?id=57063) sowie für die Azure-Cloud in [China](https://www.microsoft.com/download/details.aspx?id=57062) und [Deutschland](https://www.microsoft.com/download/details.aspx?id=57064) herunterladen.

> [!WARNING]
> Ändern Sie die IP-Adresseinstellungen nicht innerhalb des Betriebssystems des virtuellen Computers. Öffentliche Azure-IP-Adressen sind dem Betriebssystem nicht bekannt. Auch wenn Sie dem Betriebssystem Einstellungen für private IP-Adressen hinzufügen können, wird empfohlen, dies erst nach Lesen des Artikels [Hinzufügen einer privaten IP-Adresse zu einem Betriebssystem](virtual-network-network-interface-addresses.md#private) und bei absoluter Notwendigkeit zu tun.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure/group#az-group-delete) entfernen:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Lesen Sie mehr über [private IP-Adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) und das Zuweisen einer [statischen privaten IP-Adresse](virtual-network-network-interface-addresses.md#add-ip-addresses) zu einer Azure-VM.
- Erfahren Sie mehr über das Erstellen von VMs unter [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
