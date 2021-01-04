---
title: 'Problembehandlung bei Azure-Site-to-Site-VPN-Verbindungen, die nicht hergestellt werden können titleSuffix: Azure VPN Gateway'
description: In diesem Artikel erfahren Sie, was Sie tun können, wenn die Site-to-Site-VPN-Verbindung plötzlich getrennt wird und nicht mehr hergestellt werden kann.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 674b8ab8266921a4eef8bbf212f3c556b1e587d7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658348"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Problembehandlung: Azure-Site-to-Site-VPN-Verbindung kann nicht hergestellt werden und reagiert nicht mehr

Nach dem Konfigurieren einer Site-to-Site-VPN-Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk wird plötzlich die Verbindung getrennt und kann nicht wieder hergestellt werden. Dieser Artikel enthält Schritte, mit denen Sie dieses Problem beheben können. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Um das Problem zu beheben, versuchen Sie zunächst [das VPN-Gateway von Azure ](./reset-gateway.md) und den Tunnel des lokalen VPN-Geräts zurückzusetzen. Sollte das Problem weiterhin bestehen, gehen Sie folgendermaßen vor, um die Ursache des Problems zu identifizieren.

### <a name="prerequisite-step"></a>Voraussetzung

Überprüfen Sie den Typ des Azure-VPN-Gateways.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Überprüfen Sie auf der **Übersichtsseite** des VPN-Gateways die Typinformationen.
    
    ![Übersicht über das Gateway](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Schritt 1: Überprüfen, ob das lokale VPN-Gerät validiert wurde

1. Überprüfen Sie, ob [das VPN-Gerät und die Version des Betriebssystems validiert wurden](vpn-gateway-about-vpn-devices.md#devicetable). Wenn es sich bei dem Gerät nicht um ein überprüftes VPN-Gerät handelt, müssen Sie sich möglicherweise beim Gerätehersteller erkundigen, ob ein Kompatibilitätsproblem vorliegt.

2. Stellen Sie sicher, dass das VPN-Gerät ordnungsgemäß konfiguriert ist. Weitere Informationen finden Sie unter [Bearbeiten der Gerätekonfigurationsvorlagen](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Schritt 2: Überprüfen des gemeinsam verwendeten Schlüssels

Vergleichen Sie den gemeinsam verwendeten Schlüssel des lokalen VPN-Geräts und des Azure Virtual Network-VPNs, um sicherzustellen, dass die Schlüssel übereinstimmen. 

Verwenden Sie zum Anzeigen des gemeinsamen Schlüssels für die Azure-VPN-Verbindung eine der folgenden Methoden:

**Azure portal**

1. Gehen Sie zu der von Ihnen erstellen VPN-Gateway-Site-to-Site-Verbindung.

2. Klicken Sie im Abschnitt **Einstellungen** auf **Gemeinsam verwendeter Schlüssel**.
    
    ![Gemeinsam verwendeter Schlüssel](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Für das Azure Resource Manager-Bereitstellungsmodell:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

Für das klassische Bereitstellungsmodell:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>Schritt 3: Überprüfen Sie die VPN-Peer-IP-Adressen

-   Die IP-Definition im Objekt **Lokales Netzwerkgateway** in Azure muss mit der IP-Adresse des lokalen Geräts übereinstimmen.
-   Die Azure-Gateway-IP-Definition, die auf dem lokalen Gerät festgelegt ist, muss der Azure-Gateway-IP-Adresse entsprechen.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Schritt 4. Überprüfen Sie die UDR und die NSG im Gatewaysubnetz

Suchen und Entfernen Sie benutzerdefiniertes routing (UDR) oder Netzwerksicherheitsgruppen (NSG) auf dem Gatewaysubnetz, und testen Sie dann das Ergebnis. Wenn das Problem behoben wurde, überprüfen Sie die vom UDR oder den NSG angewendeten Einstellungen.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Schritt 5: Überprüfen der Adresse der externen Schnittstelle des lokalen VPN-Geräts

- Wenn die IP-Adresse des VPN-Geräts mit Internetzugriff in der Definition **Lokales Netzwerk** in Azure enthalten ist, kann es vereinzelt zur Trennung der Verbindung kommen.
- Die externe Schnittstelle des Geräts muss sich direkt im Internet befinden. Es darf sich keine Netzwerkadressübersetzung oder Firewall zwischen dem Internet und dem Gerät befinden.
- Um Firewallclustering konfigurieren, um über eine virtuelle IP-Adresse zu verfügen, müssen Sie den Cluster unterbrechen und das VPN-Gerät direkt an eine öffentliche Schnittstelle anschließen, mit der das Gateway kompatibel ist.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Schritt 6: Überprüfen, ob die Subnetze genau übereinstimmen (Auf Azure-Richtlinien basierende Gateways)

-   Stellen Sie sicher, dass der Adressraum bzw. die Adressräume des virtuellen Netzwerks zwischen dem virtuellen Netzwerk in Azure und den lokalen Definitionen genau übereinstimmen.
-   Stellen Sie sicher, dass die Subnetze zwischen dem **lokalen Netzwerkgateway** und den lokalen Definitionen für das lokale Netzwerk genau übereinstimmen.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Schritt 7. Überprüfen des Azure-Gatewayintegritätstests

1. Öffnen Sie den Integritätstest, indem Sie zu der folgenden URL wechseln:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Klicken Sie sich durch die Zertifikatswarnung.
3. Wenn Sie eine Antwort erhalten, wird das VPN-Gateway als integer betrachtet. Wenn Sie keine Antwort erhalten, ist das Gateway möglicherweise nicht fehlerfrei, oder auf dem Gatewaysubnetz ist eine NSG vorhanden, die das Problem verursacht. Der folgende Text ist eine Beispielantwort:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Schritt 8: Überprüfen, ob beim lokalen VPN-Gerät die Funktion Perfect Forward Secrecy aktiviert ist

Die Funktion Perfect Forward Secrecy kann die Probleme mit der Trennung der Verbindung verursachen. Wenn beim VPN-Gerät Perfect Forward Secrecy aktiviert ist, sollten Sie diese Funktion deaktivieren. Aktualisieren Sie dann die VPN-Gateway-IPsec-Richtlinie.

## <a name="next-steps"></a>Nächste Schritte

-   [Erstellen einer Site-to-Site-Verbindung im Azure-Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurieren einer IPsec/IKE-Richtlinie für Site-to-Site-VPN-Verbindungen](vpn-gateway-ipsecikepolicy-rm-powershell.md)