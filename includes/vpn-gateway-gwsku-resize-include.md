---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6fd4039614dbd7c1a2b2c6ba8403502a6420fe3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159379"
---
Wenn Sie für die aktuellen SKUs („VpnGw1“, „VpnGw2“ und „VPNGW3“) die Größe Ihrer Gateway-SKU anpassen möchten, um ein Upgrade auf eine leistungsfähigere SKU durchzuführen, können Sie das PowerShell-Cmdlet `Resize-AzVirtualNetworkGateway` verwenden. Sie können mit diesem Cmdlet auch ein Downgrade der Gateway-SKU-Größe durchführen. Verwenden Sie bei Verwendung der Basic-Gateway-SKU stattdessen [diese Anweisungen](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize), um die Größe Ihres Gateways anzupassen.

Im folgenden PowerShell-Beispiel wird das Ändern der Größe einer Gateway-SKU in „VpnGw2“ veranschaulicht.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Sie können die Größe eines Gateways auch über das Azure-Portal anpassen. Wählen Sie hierzu auf der Seite **Konfiguration** für Ihr Gateway für virtuelle Netzwerke in der Dropdownliste eine andere SKU aus.