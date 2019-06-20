---
title: Includedatei
description: Includedatei
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150807"
---
Sie können zum schnellen Erstellen eines VNET in diesem Artikel auf die Option zum Ausprobieren klicken, um eine PowerShell-Konsole zu öffnen. Passen Sie die Werte an, und kopieren Sie die Befehle dann in das Konsolenfenster. Weitere Informationen zum neuen Az-Modul und zur Kompatibilität mit AzureRM finden Sie unter [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az) (Einführung in das neue Az-Modul von Azure PowerShell). Anweisungen zur Installation des Az-Moduls finden Sie unter [Install Azure PowerShell](/powershell/azure/install-az-ps) (Installieren von Azure PowerShell).

Stellen Sie sicher, dass sich der Adressraum für das von Ihnen erstellte VNET nicht mit den Adressbereichen anderer VNETs, mit denen Sie eine Verbindung herstellen möchten, oder mit Ihren Adressräumen des lokalen Netzwerks überlappt.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Wenn Sie nicht bereits über eine Ressourcengruppe verfügen, erstellen Sie eine neue. Passen Sie die PowerShell-Befehle gemäß dem gewünschten Ressourcengruppennamen an, und führen Sie dann das folgende Cmdlet aus:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Erstellen eines VNET

Passen Sie die PowerShell-Befehle an, um ein VNET zu erstellen, das für Ihre Umgebung kompatibel ist.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```