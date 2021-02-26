---
title: Ausführen eines Upgrades für öffentliche IP-Adressen
titleSuffix: Azure Virtual Network
description: Aktualisieren Sie öffentliche IP-Adressen von Basic zu Standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 93d5d422709fad3d4f732b3b232f67d60d79b507
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493718"
---
# <a name="upgrade-public-ip-addresses"></a>Ausführen eines Upgrades für öffentliche IP-Adressen

Öffentliche IP-Adressen in Azure werden mit einer SKU erstellt („Basic“ oder „Standard“), die Aspekte ihrer Funktionalität bestimmt (einschließlich der Zuordnungsmethode, der Unterstützung von Funktionen und der Ressourcen, denen sie zugeordnet werden können). 

Folgende Szenarien werden in diesem Artikel behandelt:
* Upgraden einer öffentlichen IP-Adresse mit Basic-SKU auf eine öffentliche IP-Adresse mit Standard-SKU (über PowerShell oder die Befehlszeilenschnittstelle)
* Migrieren eines klassischen reservierten IP in Azure zu einer öffentlichen IP-Adresse in Azure Resource Manager mit Basic-SKU

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Upgraden einer öffentlichen IP-Adresse von der Basic-SKU auf die Standard-SKU

Damit für eine öffentliche IP-Adresse ein Upgrade durchgeführt werden kann, darf sie keiner Ressource zugeordnet sein. (Weitere Informationen zum Aufheben der Zuordnung öffentlicher IP-Adressen finden Sie auf [dieser Seite](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).)

>[!IMPORTANT]
>Auch nach dem Upgrade von der Basic-SKU auf die Standard-SKU haben die öffentlichen IP-Adressen keine [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  Dies bedeutet, dass sie keiner Azure-Ressource zugeordnet werden können, die zonenredundant oder in Regionen, in denen dies angeboten wird, an eine vordefinierte Zone gebunden ist.

---
# <a name="basic-to-standard---powershell"></a>[**Basic zu Standard: PowerShell**](#tab/option-upgrade-powershell)

Im folgenden Beispiel wird vorausgesetzt, dass eine öffentliche IP-Adresse mit Basic-SKU erstellt und dabei das Beispiel auf [dieser Seite](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) mit der öffentlichen IP-Adresse **myBasicPublicIP** mit Basic-SKU in **myResourceGroup** verwendet wurde.

Führen Sie zum Upgraden der IP-Adresse einfach die folgenden Befehle mithilfe von PowerShell aus.  Beachten Sie, dass dieser Abschnitt übersprungen werden kann, wenn die IP-Adresse bereits statisch zugeordnet ist.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Basic zu Standard: Befehlszeilenschnittstelle**](#tab/option-upgrade-cli)

Im folgenden Beispiel wird vorausgesetzt, dass eine öffentliche IP-Adresse mit Basic-SKU erstellt und dabei das Beispiel auf [dieser Seite](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) mit der öffentlichen IP-Adresse **myBasicPublicIP** mit Basic-SKU in **myResourceGroup** verwendet wurde.

Führen Sie zum Upgraden der IP-Adresse einfach die folgenden Befehle mithilfe der Azure-Befehlszeilenschnittstelle aus.  Beachten Sie, dass dieser Abschnitt übersprungen werden kann, wenn die IP-Adresse bereits statisch zugeordnet ist.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Aktualisieren (Migrieren) einer klassischen reservierten IP auf eine statische öffentliche IP-Adresse

Wenn Sie die neuen Funktionen in Azure Resource Manager nutzen möchten, können Sie vorhandene öffentliche statische IP-Adressen (reservierte IPs) vom klassischen Modell zum modernen Azure Resource Manager-Modell migrieren.  Die migrierte öffentliche IP-Adresse hat dann den SKU-Typ „Basic“.


---

# <a name="reserved-to-basic---powershell"></a>[**Reservierte IP zu Basic: PowerShell**](#tab/option-migrate-powershell)

Im folgenden Beispiel wird davon ausgegangen, dass die klassische reservierte Azure-IP **myReservedIP** in **myResourceGroup** erstellt wurde. Außerdem müssen Sie vor der Migration sicherstellen, dass das Azure Resource Manager-Abonnement für die Migration registriert ist. Dies wird ausführlich in den Schritten 3 und 4 auf [dieser Seite](../virtual-machines/migration-classic-resource-manager-ps.md) behandelt.

Führen Sie die folgenden Befehle mithilfe von PowerShell aus, um die reservierte IP zu migrieren.  Falls die IP-Adresse keinem Dienst zugeordnet ist (im Beispiel gibt es einen Dienst namens **myService**), kann dieser Schritt übersprungen werden.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Der vorhergehende Befehl zeigt ggf. Warnungen und Fehler an, die die Migration blockieren. Wenn die Validierung erfolgreich ist, können Sie mit den folgenden Schritten für das Vorbereiten und Committen der Migration fortfahren:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Es wird eine neue Ressourcengruppe in Azure Resource Manager mit dem Namen der migrierten reservierten IP erstellt (im obigen Beispiel heißt die Ressourcengruppe dann **myReservedIP-Migrated**).

# <a name="reserved-to-basic---cli"></a>[**Reservierte IP zu Basic: Befehlszeilenschnittstelle**](#tab/option-migrate-cli)

Im folgenden Beispiel wird davon ausgegangen, dass die klassische reservierte Azure-IP **myReservedIP** in **myResourceGroup** erstellt wurde. Außerdem müssen Sie vor der Migration sicherstellen, dass das Azure Resource Manager-Abonnement für die Migration registriert ist. Dies wird ausführlich in den Schritten 3 und 4 auf [dieser Seite](../virtual-machines/migration-classic-resource-manager-cli.md) behandelt.

Führen Sie die folgenden Befehle mithilfe der Azure-Befehlszeilenschnittstelle aus, um die reservierte IP zu migrieren.  Falls die IP-Adresse keinem Dienst zugeordnet ist (im Beispiel gibt es einen Dienst namens **myService** sowie die Bereitstellung **myDeployment**), kann dieser Schritt übersprungen werden.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Der vorhergehende Befehl zeigt ggf. Warnungen und Fehler an, die die Migration blockieren. Wenn die Validierung erfolgreich ist, können Sie mit den folgenden Schritten für das Vorbereiten und Committen der Migration fortfahren:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Es wird eine neue Ressourcengruppe in Azure Resource Manager mit dem Namen der migrierten reservierten IP erstellt (im obigen Beispiel heißt die Ressourcengruppe dann **myReservedIP-Migrated**).

---

## <a name="limitations"></a>Einschränkungen

* Damit ein Upgrade der öffentlichen IP-Adresse mit Basic-SKU durchgeführt werden kann, darf diese keiner Azure-Ressource zugeordnet sein.  Weitere Informationen zum Aufheben der Zuordnung von öffentlichen IP-Adressen finden Sie auf [dieser Seite](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).  Ebenso darf eine reservierte IP, die migriert werden soll, keinem Clouddienst zugeordnet sein.  Weitere Informationen zum Aufheben der Zuordnung von reservierten IPs finden Sie auf [dieser Seite](./remove-public-ip-address-vm.md).  
* Öffentliche IP-Adressen, für die ein Upgrade von der Basic-SKU auf die Standard-SKU durchgeführt wird, haben weiterhin keine [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) und können daher keinen Azure-Ressourcen zugeordnet werden, die zonenredundant oder zonal sind.  Beachten Sie, dass dies nur für Regionen gilt, in denen Verfügbarkeitszonen angeboten werden.
* Sie können keine Herabstufung von „Standard“ auf „Basic“ durchführen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [öffentliche IP-Adressen](./public-ip-addresses.md#public-ip-addresses) in Azure, einschließlich der Unterschiede zwischen den SKU-Typen, sowie über [Einstellungen für öffentliche IP-Adresse](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Erfahren Sie mehr über das [Upgraden einer öffentlichen Azure Load Balancer-Instanz von der Basic- auf die Standard-SKU](../load-balancer/upgrade-basic-standard.md).
- Informieren Sie sich über [klassische reservierte Azure-IPs](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) und die [Migration klassischer Ressourcen zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).
