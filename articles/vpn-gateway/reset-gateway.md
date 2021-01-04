---
title: Zurücksetzen einer Azure VPN Gateway-Instanz zum erneuten Herstellen des IPsec-Tunnels
description: Setzen Sie die Azure VPN Gateway-Instanz zurück, um IPSec-Tunnel für VPN-Gateways im klassischen und im Resource Manager-Bereitstellungsmodell wiederherzustellen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: cd25c7638bd7e178cdb963ba528cccefde6b9eca
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646490"
---
# <a name="reset-a-vpn-gateway"></a>Zurücksetzen einer VPN Gateway-Instanz

Das Zurücksetzen von Azure VPN Gateway-Instanzen ist nützlich, wenn die standortübergreifende VPN-Verbindung bei mindestens einem Site-to-Site-VPN-Tunnel unterbrochen ist. In diesem Fall funktionieren Ihre lokalen VPN-Geräte ordnungsgemäß, können jedoch keine IPsec-Tunnelverbindungen mit Azure VPN Gateway-Instanzen herstellen. In diesem Artikel erfahren Sie, wie Sie Ihre VPN Gateway-Instanz zurücksetzen.

### <a name="what-happens-during-a-reset"></a>Was geschieht beim Zurücksetzen?

Eine VPN Gateway-Instanz umfasst zwei VM-Instanzen, die in einer Konfiguration mit aktivem Standbymodus ausgeführt werden. Beim Zurücksetzen des Gateways wird das Gateway neu gestartet, und die standortübergreifenden Konfigurationen werden erneut auf das Gateway angewendet. Die öffentliche IP-Adresse des Gateways bleibt unverändert. Die VPN-Routerkonfiguration muss also nicht mit einer neuen öffentlichen IP-Adresse für Azure VPN Gateway aktualisiert werden.

Wenn Sie den Befehl zum Zurücksetzen des Gateways ausführen, wird die aktuell aktive Azure VPN Gateway-Instanz umgehend neu gestartet. Während des Failovers von der aktiven Instanz (die Instanz, die neu gestartet wird) auf die Standbyinstanz kommt es zu einer kurzen Unterbrechung. Diese Unterbrechung sollte weniger als 1 Minute dauern.

Wenn die Verbindung nach dem ersten Neustart nicht wiederhergestellt wird, führen Sie denselben Befehl erneut aus, um die zweite VM-Instanz (das neue aktive Gateway) neu zu starten. Wenn die beiden Neustarts nacheinander angefordert werden, dauert der Neustart der beiden VM-Instanzen (aktive Instanz und Standbyinstanz) etwas länger. Der Neustart der beiden VMs kann 30 bis 45 Minuten dauern, sodass die VPN-Konnektivität etwas länger unterbrochen ist.

Wenn nach den beiden Neustarts weiterhin standortübergreifende Konnektivitätsprobleme auftreten, erstellen Sie über das Azure-Portal ein Supportticket.

## <a name="before-you-begin"></a><a name="before"></a>Voraussetzungen

Überprüfen Sie vor dem Zurücksetzen des Gateways die unten aufgeführten Elemente für die einzelnen IPsec-Site-to-Site-VPN-Tunnel (S2S). Treten bei diesen Elementen Konflikte auf, werden S2S-VPN-Tunnelverbindungen unterbrochen. Indem Sie die Konfigurationen für Ihre lokalen VPN-Gateways und Azure VPN Gateway-Instanzen überprüfen und korrigieren, werden nicht erforderliche Neustarts und Unterbrechungen anderer funktionierender Verbindungen der Gateways verhindert.

Überprüfen Sie die folgenden Elemente, bevor Sie Ihr Gateway zurücksetzen:

* Die Internet-IP-Adressen (VIPs) der Azure VPN Gateway-Instanz und des lokalen VPN-Gateways sind sowohl in Azure als auch in den lokalen VPN-Richtlinien ordnungsgemäß konfiguriert.
* Der vorinstallierte Schlüssel muss sowohl bei der Azure VPN Gateway-Instanz als auch auf dem lokalen VPN-Gateway identisch sein.
* Wenn Sie eine bestimmte IPsec/IKE-Konfiguration – z.B. Verschlüsselung, Hashalgorithmus oder PFS (Perfect Forward Secrecy) – anwenden, müssen Sie sicherstellen, dass die Azure VPN Gateway-Instanz und das lokale VPN-Gateway dieselbe Konfiguration aufweisen.

## <a name="azure-portal"></a><a name="portal"></a>Azure-Portal

Sie können im Ressourcen-Manager-Bereitstellungsmodell ein VPN Gateway im Azure-Portal zurücksetzen. Wenn Sie ein klassisches Gateway zurücksetzen möchten, lesen Sie die PowerShell-Schritte für das [klassische Bereitstellungsmodell](#resetclassic).

### <a name="resource-manager-deployment-model"></a>Ressourcen-Manager-Bereitstellungsmodell

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Ressourcen-Manager-Bereitstellungsmodell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Das Cmdlet zum Zurücksetzen eines Gateways lautet **Reset-AzVirtualNetworkGateway**. Stellen Sie vor dem Zurücksetzen sicher, dass Sie die aktuelle Version der [PowerShell-Az-Cmdlets](/powershell/module/az.network) installiert haben. Im folgenden Beispiel wird das Gateway des virtuellen Netzwerks mit dem Namen „VNet1GW“ in der Ressourcengruppe „TestRG1“ zurückgesetzt:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Ergebnis:

Wenn ein Ergebnis zurückgegeben wird, können Sie davon ausgehen, dass das Gateway erfolgreich zurückgesetzt wurde. Nichts im zurückgegebenen Ergebnis gibt jedoch explizit an, dass das Zurücksetzen erfolgreich war. Wenn Sie sich den Verlauf näher ansehen möchten, um zu prüfen, wann genau das Zurücksetzen des Gateways erfolgt ist, können Sie diese Informationen im [Azure-Portal](https://portal.azure.com) anzeigen. Wechseln Sie im Portal zu **GatewayName -> Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klassisches Bereitstellungsmodell

Das Cmdlet zum Zurücksetzen eines Gateways ist **Reset-AzureVNetGateway**. Die Azure PowerShell-Cmdlets für die Dienstverwaltung müssen lokal auf Ihrem Desktop installiert sein. Azure Cloud Shell können Sie nicht verwenden. Stellen Sie vor dem Zurücksetzen sicher, dass Sie die aktuelle Version der [Dienstverwaltungs-PowerShell-Cmdlets](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets) installiert haben. Wenn Sie diesen Befehl verwenden, müssen Sie den vollständigen Namen des virtuellen Netzwerks verwenden. Ein klassisches virtuelles Netzwerk, das über das Portal erstellt wurde, hat einen langen Namen, der für PowerShell erforderlich ist. Sie können den langen Namen anzeigen, indem Sie „Get-AzureVNetConfig -ExportToFile C:\MeinOrdnername\NetworkConfig.xml“ verwenden.

Im folgenden Beispiel wird das Gateway für ein virtuelles Netzwerk namens „Group TestRG1 TestVNet1“ (das im Portal einfach als „TestVNet1“ angezeigt wird) zurückgesetzt:

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
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

## <a name="azure-cli"></a><a name="cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie zum Zurücksetzen des Gateways den Befehl [az network vnet-gateway reset](/cli/azure/network/vnet-gateway). Im folgenden Beispiel wird das Gateway des virtuellen Netzwerks mit dem Namen „VNet5GW“ in der Ressourcengruppe „TestRG5“ zurückgesetzt:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Ergebnis:

Wenn ein Ergebnis zurückgegeben wird, können Sie davon ausgehen, dass das Gateway erfolgreich zurückgesetzt wurde. Nichts im zurückgegebenen Ergebnis gibt jedoch explizit an, dass das Zurücksetzen erfolgreich war. Wenn Sie sich den Verlauf näher ansehen möchten, um zu prüfen, wann genau das Zurücksetzen des Gateways erfolgt ist, können Sie diese Informationen im [Azure-Portal](https://portal.azure.com) anzeigen. Wechseln Sie im Portal zu **GatewayName -> Resource Health**.