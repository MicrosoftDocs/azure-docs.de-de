---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010803"
---
Sie können das PowerShell-Cmdlet `Resize-AzVirtualNetworkGateway` zum Aktualisieren oder Herabstufen einer Generation1- oder Generation2-SKU verwenden (die Größe aller VpnGw-SKUs kann mit Ausnahme von Basic-SKUs geändert werden). Verwenden Sie bei Verwendung der Basic-Gateway-SKU stattdessen [diese Anweisungen](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize), um die Größe Ihres Gateways anzupassen.

Im folgenden PowerShell-Beispiel wird das Ändern der Größe einer Gateway-SKU in „VpnGw2“ veranschaulicht.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```