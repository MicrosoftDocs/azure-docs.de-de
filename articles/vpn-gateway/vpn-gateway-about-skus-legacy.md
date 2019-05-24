---
title: Ältere SKUs für virtuelle Azure-VPN-Netzwerkgateways | Microsoft-Dokumentation
description: So arbeiten Sie mit den alten SKUs „Basic“, „Standard“ und „HighPerformance“ für virtuelle Netzwerkgateways.
services: vpn-gateway
documentationcenter: na
author: WenJason
manager: digimobile
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/20/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 5a9e3f63a484069bf8cd39f8a545d7c37f05c63c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159272"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Arbeiten mit SKUs für virtuelle Netzwerkgateways (Legacy-SKUs)

Dieser Artikel enthält Informationen über die Legacy-SKUs (alte SKUs) für virtuelle Netzwerkgateways. Die Legacy-SKUs sind weiterhin in beiden Bereitstellungsmodellen für VPN-Gateways funktionsfähig, die bereits erstellt wurden. Klassische VPN-Gateways verwenden weiterhin die Legacy-SKUs für vorhandene Gateways und für neue Gateways. Wenn Sie neue Resource Manager-VPN-Gateways erstellen, verwenden Sie die neuen Gateway-SKUs. Informationen zu den neuen SKUs finden Sie unter [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Gateway-SKUs

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Voraussichtlicher aggregierter Durchsatz nach SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Unterstützte Konfigurationen nach SKU und VPN-Typ

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Ändern der Größe eines Gateways

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können die Größe Ihres Gateways in eine Gateway-SKU innerhalb einer SKU-Familie ändern. Wenn Sie z.B. eine Standard-SKU haben, können Sie die Größe in eine HighPerformance-SKU ändern. Die Größe Ihrer VPN-Gateways kann zwischen den alten SKUs und den neuen SKU-Familien jedoch nicht geändert werden. Beispielsweise können Sie nicht von einer Standard-SKU zu einer VpnGw2-SKU wechseln, oder von einer Basic-SKU zu VpnGw1.

Um die Größe eines Gateways für das klassische Bereitstellungsmodell zu ändern, verwenden Sie den folgenden Befehl:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Um die Größe eines Gateways mithilfe von PowerShell für das Resource Manager-Bereitstellungsmodell zu ändern, verwenden Sie den folgenden Befehl:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Sie können die Größe eines Gateways auch im Azure-Portal ändern.

## <a name="change"></a>Wechsel zu den neuen Gateway-SKUs

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den neuen Gateway-SKUs finden Sie unter [Gateway-SKUs](vpn-gateway-about-vpngateways.md#gwsku).

Weitere Informationen zu Konfigurationseinstellungen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md).

<!--Update_Description: wording update-->