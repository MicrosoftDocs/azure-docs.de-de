---
title: 'Azure ExpressRoute: Konfigurieren von Global Reach'
description: Dieser Artikel hilft Ihnen, ExpressRoute-Leitungen miteinander zu verbinden, um ein privates Netzwerk zwischen Ihren lokalen Netzwerken aufzubauen und Global Reach zu aktivieren.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.openlocfilehash: 2365317d83a4c11fa17cb5c449a25b70da17c2eb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368365"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurieren von ExpressRoute Global Reach

Dieser Artikel unterstützt Sie bei der Konfiguration von ExpressRoute Global Reach mit PowerShell. Weitere Informationen finden Sie unter [ExpressRoute Global Reach (Vorschau)](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie mit der Konfiguration beginnen:

* Sie sind mit den [Workflows](expressroute-workflows.md) zur Bereitstellung von ExpressRoute-Leitungen vertraut.
* Ihre ExpressRoute-Leitungen weisen den Status „Bereitgestellt“ auf.
* Für Ihre ExpressRoute-Leitungen ist privates Azure-Peering konfiguriert.
* Wenn Sie PowerShell lokal ausführen möchten, stellen Sie sicher, dass die neueste Version von Azure PowerShell auf Ihrem Computer installiert ist.

### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identifizieren von Leitungen

1. Um mit der Konfiguration zu beginnen, melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie das zu verwendende Abonnement aus.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifizieren Sie die ExpressRoute-Leitungen, die Sie verwenden möchten. Sie können ExpressRoute Global Reach zwischen dem privaten Peering von zwei beliebigen ExpressRoute-Leitungen aktivieren, solange sich diese in den unterstützten Ländern/Regionen befinden und an verschiedenen Peeringstandorten erstellt wurden. 

   * Wenn beide Leitungen Ihrem Abonnement zugewiesen sind, können Sie eine der beiden Leitungen auswählen, um die Konfiguration in den folgenden Abschnitten auszuführen.
   * Wenn sich die beiden Leitungen in unterschiedlichen Azure-Abonnements befinden, benötigen Sie die Autorisierung eines der Azure-Abonnements. Den Autorisierungsschlüssel übergeben Sie, wenn Sie den Konfigurationsbefehl im anderen Azure-Abonnement ausführen.

## <a name="enable-connectivity"></a>Herstellen von Konnektivität

Aktivieren Sie die Konnektivität zwischen Ihren lokalen Netzwerken. Es gibt getrennte Anweisungen für Leitungen, die sich im gleichen Azure-Abonnement befinden, und für Leitungen in unterschiedlichen Abonnements.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-Leitungen im gleichen Azure-Abonnement

1. Verwenden Sie folgende Befehle zum Abrufen von Leitung 1 und Leitung 2. Die beiden Leitungen befinden sich im selben Abonnement.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Führen Sie den folgenden Befehl für Leitung 1 aus, und übergeben Sie die ID für das private Peering von Leitung 2. Beachten Sie dabei Folgendes:

   * Die ID für das private Peering sieht in etwa wie folgt aus: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* muss ein IPv4-Subnetz vom Typ „/29“ sein (Beispiel: 10.0.0.0/29). Wir verwenden IP-Adressen in diesem Subnetz, um eine Verbindung zwischen den beiden ExpressRoute-Leitungen herzustellen. Die Adressen in diesem Subnetz dürfen nicht in Ihren virtuellen Azure-Netzwerken oder in Ihrem lokalen Netzwerk verwendet werden.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Speichern Sie die Konfiguration für Leitung 1 wie folgt:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Wenn der vorherige Vorgang abgeschlossen ist, verfügen Sie dank der beiden ExpressRoute-Leitungen auf beiden Seiten über Konnektivität zwischen Ihren lokalen Netzwerken.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-Leitungen in verschiedenen Azure-Abonnements

Wenn sich die beiden Leitungen nicht im gleichen Azure-Abonnement befinden, benötigen Sie eine Autorisierung. Bei der folgenden Konfiguration wird die Autorisierung im Abonnement von Leitung 2 generiert und der Autorisierungsschlüssel an Leitung 1 übergeben.

1. Generieren Sie einen Autorisierungsschlüssel.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Notieren Sie sich die ID für das private Peering von Leitung 2 sowie den Autorisierungsschlüssel.
2. Führen Sie den folgenden Befehl für Leitung 1 aus. Übergeben Sie die ID für das private Peering von Leitung 2 und den Autorisierungsschlüssel.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Speichern Sie die Konfiguration für Leitung 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Wenn der vorherige Vorgang abgeschlossen ist, verfügen Sie dank der beiden ExpressRoute-Leitungen auf beiden Seiten über Konnektivität zwischen Ihren lokalen Netzwerken.

## <a name="verify-the-configuration"></a>Überprüfen der Konfiguration

Verwenden Sie den folgenden Befehl, um die Konfiguration für die Leitung zu überprüfen, für die die Konfiguration vorgenommen wurde (Leitung 1 im obigen Beispiel).
```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Wenn Sie einfach *$ckt_1* in PowerShell ausführen, wird *CircuitConnectionStatus* in der Ausgabe angezeigt. So können Sie ermitteln, ob die Verbindung hergestellt wurde. Mögliche Werte: „Connected“ (Verbunden) und „Disconnected“ (Getrennt). 

## <a name="disable-connectivity"></a>Deaktivieren der Konnektivität

Um die Konnektivität zwischen Ihren lokalen Netzwerken zu deaktivieren, führen Sie die Befehle für die Leitung aus, in der die Konfiguration vorgenommen wurde (z.B. Leitung 1 im vorherigen Beispiel).

```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Sie können den Vorgang „Get“ ausführen, um den Status zu überprüfen.

Nach Abschluss des obigen Vorgangs besteht zwischen Ihren lokalen Netzwerken keine Verbindung mehr über Ihre ExpressRoute-Leitungen.

## <a name="next-steps"></a>Nächste Schritte
1. [Weitere Informationen zu ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Überprüfen der ExpressRoute-Konnektivität](expressroute-troubleshooting-expressroute-overview.md)
3. [Verknüpfen einer ExpressRoute-Leitung mit einem virtuellen Azure-Netzwerk](expressroute-howto-linkvnet-arm.md)
