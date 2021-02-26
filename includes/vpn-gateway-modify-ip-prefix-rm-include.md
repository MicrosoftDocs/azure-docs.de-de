---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7dd255e9767309c7b273dccfa0ee5675eed18568
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380485"
---
So fügen Sie weitere Adresspräfixe hinzu:

1. Legen Sie die Variable für das lokale Netzwerkgateway (LocalNetworkGateway) fest.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
1. Ändern Sie die Präfixe.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

So entfernen Sie Adresspräfixe:

  Lassen Sie die Präfixe weg, die Sie nicht mehr benötigen. In diesem Beispiel wird das Präfix „10.101.2.0/24“ (aus dem vorherigen Beispiel) nicht mehr benötigt. Daher wird das Gateway für lokale Netzwerke aktualisiert und das Präfix weggelassen.

1. Legen Sie die Variable für das lokale Netzwerkgateway (LocalNetworkGateway) fest.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
1. Legen Sie das Gateway mit den aktualisierten Präfixen fest.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
