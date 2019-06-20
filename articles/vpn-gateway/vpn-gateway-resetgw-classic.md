---
title: Zurücksetzen einer Azure VPN Gateway-Instazn zum erneuten Herstellen von IPsec-Tunnel | Microsoft-Dokumentation
description: In diesem Artikel werden die Schritte zum Zurücksetzen einer Azure VPN Gateway-Instanz zum erneuten Herstellen der IPsec-Tunnel beschrieben. Der Artikel bezieht sich auf VPN-Gateways im klassischen Bereitstellungsmodell und im Resource Manager-Bereitstellungsmodell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2019
ms.author: cherylmc
ms.openlocfilehash: 54b89b74017b8d5d6e4bd1b52c6b3986d2802702
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60457194"
---
# <a name="reset-a-vpn-gateway"></a>Zurücksetzen einer VPN Gateway-Instanz

Das Zurücksetzen von Azure VPN Gateway-Instanzen ist nützlich, wenn die standortübergreifende VPN-Verbindung bei mindestens einem Site-to-Site-VPN-Tunnel unterbrochen ist. In diesem Fall funktionieren Ihre lokalen VPN-Geräte ordnungsgemäß, können jedoch keine IPsec-Tunnelverbindungen mit Azure VPN Gateway-Instanzen herstellen. In diesem Artikel erfahren Sie, wie Sie Ihre VPN Gateway-Instanz zurücksetzen.

### <a name="what-happens-during-a-reset"></a>Was geschieht beim Zurücksetzen?

Eine VPN Gateway-Instanz umfasst zwei VM-Instanzen, die in einer Konfiguration mit aktivem Standbymodus ausgeführt werden. Beim Zurücksetzen des Gateways wird das Gateway neu gestartet, und die standortübergreifenden Konfigurationen werden erneut auf das Gateway angewendet. Die öffentliche IP-Adresse des Gateways bleibt unverändert. Die VPN-Routerkonfiguration muss also nicht mit einer neuen öffentlichen IP-Adresse für Azure VPN Gateway aktualisiert werden.

Wenn Sie den Befehl zum Zurücksetzen des Gateways ausführen, wird die aktuell aktive Azure VPN Gateway-Instanz umgehend neu gestartet. Während des Failovers von der aktiven Instanz (die Instanz, die neu gestartet wird) auf die Standbyinstanz kommt es zu einer kurzen Unterbrechung. Diese Unterbrechung sollte weniger als 1 Minute dauern.

Wenn die Verbindung nach dem ersten Neustart nicht wiederhergestellt wird, führen Sie denselben Befehl erneut aus, um die zweite VM-Instanz (das neue aktive Gateway) neu zu starten. Wenn die beiden Neustarts nacheinander angefordert werden, dauert der Neustart der beiden VM-Instanzen (aktive Instanz und Standbyinstanz) etwas länger. Der Neustart der beiden VMs kann zwei bis vier Minuten dauern, sodass die VPN-Konnektivität etwas länger unterbrochen ist.

Wenn nach den beiden Neustarts weiterhin standortübergreifende Konnektivitätsprobleme auftreten, erstellen Sie über das Azure-Portal ein Supportticket.

## <a name="before"></a>Voraussetzungen

Überprüfen Sie vor dem Zurücksetzen des Gateways die unten aufgeführten Elemente für die einzelnen IPsec-Site-to-Site-VPN-Tunnel (S2S). Treten bei diesen Elementen Konflikte auf, werden S2S-VPN-Tunnelverbindungen unterbrochen. Indem Sie die Konfigurationen für Ihre lokalen VPN-Gateways und Azure VPN Gateway-Instanzen überprüfen und korrigieren, werden nicht erforderliche Neustarts und Unterbrechungen anderer funktionierender Verbindungen der Gateways verhindert.

Überprüfen Sie die folgenden Elemente, bevor Sie Ihr Gateway zurücksetzen:

* Die Internet-IP-Adressen (VIPs) der Azure VPN Gateway-Instanz und des lokalen VPN-Gateways sind sowohl in Azure als auch in den lokalen VPN-Richtlinien ordnungsgemäß konfiguriert.
* Der vorinstallierte Schlüssel muss sowohl bei der Azure VPN Gateway-Instanz als auch auf dem lokalen VPN-Gateway identisch sein.
* Wenn Sie eine bestimmte IPsec/IKE-Konfiguration – z.B. Verschlüsselung, Hashalgorithmus oder PFS (Perfect Forward Secrecy) – anwenden, müssen Sie sicherstellen, dass die Azure VPN Gateway-Instanz und das lokale VPN-Gateway dieselbe Konfiguration aufweisen.

## <a name="portal"></a>Azure-Portal

Sie können im Ressourcen-Manager-Bereitstellungsmodell ein VPN Gateway im Azure-Portal zurücksetzen. Wenn Sie ein klassisches Gateway zurücksetzen möchten, befolgen Sie die Schritte in [PowerShell](#resetclassic).

### <a name="resource-manager-deployment-model"></a>Ressourcen-Manager-Bereitstellungsmodell

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie in Resource Manager zu dem Gateway des virtuellen Netzwerks, das Sie zurücksetzen möchten.
2. Klicken Sie auf dem Blatt für das virtuelle Netzwerkgateway auf „Zurücksetzen“.

   ![VPN Gateway zurücksetzen](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Klicken Sie auf dem Blatt „Zurücksetzen“ auf die Schaltfläche **Zurücksetzen**.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Ressourcen-Manager-Bereitstellungsmodell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Das Cmdlet zum Zurücksetzen eines Gateways lautet **Reset-AzVirtualNetworkGateway**. Stellen Sie vor dem Zurücksetzen sicher, dass Sie die aktuelle Version der [PowerShell-Az-Cmdlets](https://docs.microsoft.com/powershell/module/az.network) installiert haben. Im folgenden Beispiel wird das Gateway des virtuellen Netzwerks mit dem Namen „VNet1GW“ in der Ressourcengruppe „TestRG1“ zurückgesetzt:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Ergebnis:

Wenn ein Ergebnis zurückgegeben wird, können Sie davon ausgehen, dass das Gateway erfolgreich zurückgesetzt wurde. Nichts im zurückgegebenen Ergebnis gibt jedoch explizit an, dass das Zurücksetzen erfolgreich war. Wenn Sie sich den Verlauf näher ansehen möchten, um zu prüfen, wann genau das Zurücksetzen des Gateways erfolgt ist, können Sie diese Informationen im [Azure-Portal](https://portal.azure.com) anzeigen. Wechseln Sie im Portal zu **GatewayName -> Resource Health**.

### <a name="resetclassic"></a> Klassisches Bereitstellungsmodell

Das Cmdlet zum Zurücksetzen eines Gateways ist **Reset-AzureVNetGateway**. Stellen Sie vor dem Zurücksetzen sicher, dass Sie die aktuelle Version der [Dienstverwaltungs-PowerShell-Cmdlets](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) installiert haben. Im folgenden Beispiel wird das Gateway für das virtuelle Netzwerk „ContosoVNet“ zurückgesetzt:

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

Ergebnis:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Azure CLI

Verwenden Sie zum Zurücksetzen des Gateways den Befehl [az network vnet-gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway). Im folgenden Beispiel wird das Gateway des virtuellen Netzwerks mit dem Namen „VNet5GW“ in der Ressourcengruppe „TestRG5“ zurückgesetzt:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Ergebnis:

Wenn ein Ergebnis zurückgegeben wird, können Sie davon ausgehen, dass das Gateway erfolgreich zurückgesetzt wurde. Nichts im zurückgegebenen Ergebnis gibt jedoch explizit an, dass das Zurücksetzen erfolgreich war. Wenn Sie sich den Verlauf näher ansehen möchten, um zu prüfen, wann genau das Zurücksetzen des Gateways erfolgt ist, können Sie diese Informationen im [Azure-Portal](https://portal.azure.com) anzeigen. Wechseln Sie im Portal zu **GatewayName -> Resource Health**.
