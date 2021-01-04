---
title: 'Erstellen einer öffentlichen IP-Adresse: PowerShell'
description: Erfahren Sie, wie Sie mit Azure PowerShell eine öffentliche IP-Adresse erstellen.
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 99e79e4d094fe6e93510d139d2f4d08f260102df
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010043"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Schnellstart: Erstellen einer öffentlichen IP-Adresse mit Azure PowerShell

In diesem Artikel wird gezeigt, wie Sie mithilfe von Azure PowerShell eine öffentliche IP-Adressressource erstellen. Weitere Informationen zu den Ressourcen, denen diese zugeordnet werden kann, zum Unterschied zwischen der SKU „Basic“ und „Standard“ und andere zugehörige Informationen finden Sie unter [Öffentliche IP-Adressen](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  In diesem Beispiel konzentrieren wir uns ausschließlich auf IPv4-Adressen. Weitere Informationen zu IPv6-Adressen finden Sie unter [IPv6 für Azure VNET](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Voraussetzungen

- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie eine Ressourcengruppe mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) namens **myResourceGroup** am Speicherort **eastus2**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Öffentliche IP-Adresse erstellen

---
# <a name="standard-sku---using-zones"></a>[**Standard-SKU: Verwenden von Zonen**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Der folgende Befehl funktioniert für die API-Version 2020-08-01 oder höher.  Weitere Informationen zur derzeit verwendeten API-Version finden Sie unter [Ressourcenanbieter und -typen](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine zonenredundante öffentliche IP-Standardadresse namens **myStandardZRPublicIP** in **myResourceGroup** zu erstellen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Führen Sie für Versionen der API, die älter als 2020-08-01 ist, den oben genannten Befehl aus, ohne einen Zonenparameter anzugeben, um eine zonenredundante IP-Adresse zu erstellen. 
>

Verwenden Sie den folgenden Befehl, um eine standardmäßige zonale öffentliche IP-Adresse in Zone 2 namens **myStandardZonalPublicIP** in **myResourceGroup** zu erstellen:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Beachten Sie, dass die oben genannten Optionen für Zonen nur eine gültige Auswahl in Regionen mit [Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) sind.

# <a name="standard-sku---no-zones"></a>[**Standard-SKU: Keine Zonen**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Der folgende Befehl funktioniert für die API-Version 2020-08-01 oder höher.  Weitere Informationen zur derzeit verwendeten API-Version finden Sie unter [Ressourcenanbieter und -typen](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine öffentliche IP-Standardadresse als nicht zonale Ressource namens **myStandardPublicIP** in **myResourceGroup** zu erstellen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Diese Auswahl ist in allen Regionen gültig und die Standardauswahl für öffentliche IP-Standardadressen in Regionen ohne [Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-create-public-ip-basic)

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine statische öffentliche IP-Adresse vom Typ „Basic“ namens **myBasicPublicIP** in **myResourceGroup** zu erstellen.  Öffentliche IP-Adressen des Typs „Basic“ verwenden nicht das Konzept von Verfügbarkeitszonen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann **dynamische** IP-Zuweisung ausgewählt werden, indem AllocationMethod in „Dynamic“ geändert wird.

---

## <a name="additional-information"></a>Zusätzliche Informationen 

Weitere Informationen zu den oben aufgeführten einzelnen Variablen finden Sie unter [Verwalten öffentlicher IP-Adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Nächste Schritte
- Zuordnen einer [öffentlichen IP-Adresse zu einem virtuellen Computer](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
