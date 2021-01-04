---
title: 'Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung: Azure-Portal'
description: Erstellen Sie eine VPN Gateway-Verbindung zwischen VNets unter Verwendung von Resource Manager und Azure-Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.openlocfilehash: fe0280e302882fd5e50830950b531ea9ca169618
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660541"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie zwischen virtuellen Netzwerken (VNETs) eine VNET-zu-VNET-Verbindung herstellen. Virtuelle Netzwerke können sich in verschiedenen Regionen befinden und zu verschiedenen Abonnements gehören. Beim Verbinden von VNETs aus unterschiedlichen Abonnements müssen die Abonnements nicht demselben Active Directory-Mandanten zugeordnet sein. 

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet-vnet-diagram.png" alt-text="VNET-zu-VNET-Diagramm":::

Die Schritte in diesem Artikel gelten für das Azure Resource Manager-Bereitstellungsmodell und verwenden das Azure-Portal. Sie können diese Konfiguration mit einem anderen Bereitstellungstool oder Modell erstellen, indem Sie Optionen verwenden, die in den folgenden Artikeln beschrieben werden:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-Befehlszeilenschnittstelle](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinden von verschiedenen Bereitstellungsmodellen – Azure-Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinden von virtuellen Netzwerken aus verschiedenen Bereitstellungsmodellen mit PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Informationen zum Verbinden von VNETs

In den folgenden Abschnitten werden die unterschiedlichen Verbindungsmethoden für virtuelle Netzwerke beschrieben.

### <a name="vnet-to-vnet"></a>VNet-zu-VNet

Durch Konfigurieren einer VNET-zu-VNet-Verbindung können Sie sehr einfach eine Verbindung zwischen VNets herstellen. Wenn Sie eine VNET-zu-VNET-Verbindung (VNET2VNET) zwischen zwei virtuellen Netzwerken herstellen, ist dies vergleichbar mit dem Herstellen einer Site-to-Site-IPsec-Verbindung mit einem lokalen Standort. Bei beiden Verbindungstypen wird ein VPN-Gateway verwendet, um per IPsec/IKE einen sicheren Tunnel zu erstellen, und auch die Kommunikation läuft jeweils gleich ab. Jedoch unterscheiden sie sich in der Art, in der das lokale Netzwerkgateway konfiguriert wird. 

Beim Herstellen einer VNET-zu-VNET-Verbindung wird der Adressraum des lokalen Netzwerkgateways automatisch erstellt und gefüllt. Wenn Sie den Adressraum für ein VNET aktualisieren, leitet das andere VNET den Datenverkehr automatisch an den aktualisierten Adressraum weiter. Es ist in der Regel schneller und einfacher, eine VNET-zu-VNET-Verbindung herzustellen, als eine Site-to-Site-Verbindung.

### <a name="site-to-site-ipsec"></a>Site-to-Site (IPsec)

Wenn Sie mit einer komplizierten Netzwerkkonfiguration arbeiten, empfiehlt es sich unter Umständen, die Verbindung zwischen Ihren VNETs stattdessen mit einer [Site-to-Site-Verbindung](vpn-gateway-howto-site-to-site-resource-manager-portal.md) herzustellen. Bei Verwendung der Site-to-Site-Schritte (IPsec) erstellen und konfigurieren Sie die lokalen Netzwerkgateways manuell. Das lokale Netzwerkgateway für die einzelnen VNets behandelt das andere VNet als lokalen Standort. Mit diesen Schritten können Sie zusätzliche Adressräume für das lokale Netzwerkgateway angeben, um Datenverkehr weiterzuleiten. Wenn sich der Adressraum für ein VNET ändert, müssen Sie das dazugehörige lokale Netzwerkgateway manuell aktualisieren.

### <a name="vnet-peering"></a>VNet-Peering

Sie können Ihre VNETs auch per VNET-Peering verbinden. Beim VNET-Peering wird kein VPN-Gateway verwendet, und es gelten andere Einschränkungen. Außerdem werden die [Preise für VNet-Peering](https://azure.microsoft.com/pricing/details/virtual-network) anders berechnet als die [Preise für VPN Gateway (VNet-zu-VNet)](https://azure.microsoft.com/pricing/details/vpn-gateway). Weitere Informationen finden Sie unter [VNet-Peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Gründe für das Herstellen einer VNET-zu-VNET-Verbindung

Folgende Gründe sprechen für das Herstellen einer VNET-zu-VNET-Verbindung zwischen virtuellen Netzwerken:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Regionsübergreifende Georedundanz und Geopräsenz

* Sie können Ihre eigene Georeplikation oder -synchronisierung mit sicheren Verbindungen einrichten, ohne Endpunkte mit Internetzugriff verwenden zu müssen.
* Mit Azure Traffic Manager und Azure Load Balancer können Sie eine hoch verfügbare Workload mit Georedundanz über mehrere Azure-Regionen hinweg einrichten. Sie können z.B. SQL Server Always On-Verfügbarkeitsgruppen über mehrere Azure-Regionen hinweg einrichten.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionale Anwendungen mit mehreren Ebenen und Isolation oder Verwaltungsgrenzen

* In derselben Region können Sie Anwendungen mit mehreren Ebenen und mehreren virtuellen Netzwerken einrichten, die aufgrund von Isolations- oder Verwaltungsanforderungen miteinander verbunden sind.

Die VNET-zu-VNET-Kommunikation kann mit Konfigurationen für mehrere Standorte kombiniert werden. Mit diesen Konfigurationen können Sie Netzwerktopologien einrichten, die wie in der folgenden Abbildung dargestellt standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren:

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections-diagram.png" alt-text="Diagramm zu VNET-Verbindungen":::

In diesem Artikel wird beschrieben, wie Sie mit dem Verbindungstyp „VNET-zu-VNET“ VNETs verbinden. Sie können die folgenden Beispieleinstellungswerte nutzen, wenn Sie diese Schritte als Übung verwenden. Im Beispiel befinden sich die virtuellen Netzwerke im selben Abonnement, aber in verschiedenen Ressourcengruppen. Wenn sich Ihre VNets in unterschiedlichen Abonnements befinden, können Sie die Verbindung nicht im Portal herstellen. Sie können [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) oder stattdessen die [CLI](vpn-gateway-howto-vnet-vnet-cli.md) verwenden. Weitere Informationen zu VNET-zu-VNET-Verbindungen finden Sie unter [Häufig gestellte Fragen zu VNET-zu-VNET-Verbindungen](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Beispieleinstellungen

**Werte für VNet1:**

* **Einstellungen für das virtuelle Netzwerk**
  * **Name**: VNet1
  * **Adressraum:** 10.1.0.0/16
  * **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten.
  * **Ressourcengruppe**: TestRG1
  * **Standort**: East US
  * **Subnetz**
    * **Name**: FrontEnd
    * **Adressbereich**: 10.1.0.0/24

* **Einstellungen des Gateways für virtuelle Netzwerke**
  * **Name**: VNet1GW
  * **Ressourcengruppe**: East US
  * **Generation:** Generation 1
  * **Gatewaytyp**: Wählen Sie **VPN** aus.
  * **VPN-Typ:** Wählen Sie **Routenbasiert** aus.
  * **SKU**: VpnGw1
  * **Virtuelles Netzwerk:** VNet1
  * **Adressbereich für Gatewaysubnetz**: 10.1.255.0/27
  * **Öffentliche IP-Adresse:** Neu erstellen
  * **Öffentliche IP-Adresse**: VNet1GWpip

* **Connection**
  * **Name**: VNet1toVNet4
  * **Gemeinsam verwendeter Schlüssel**: Sie können den gemeinsam verwendeten Schlüssel selbst erstellen. Wenn Sie die Verbindung zwischen den VNETs herstellen, müssen die Werte übereinstimmen. Verwenden Sie für diese Übung abc123.

**Werte für VNet4:**

* **Einstellungen für das virtuelle Netzwerk**
  * **Name**: VNet4
  * **Adressraum:** 10.41.0.0/16
  * **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten.
  * **Ressourcengruppe**: TestRG4
  * **Standort**: USA (Westen)
  * **Subnetz**
  * **Name**: FrontEnd
  * **Adressbereich**: 10.41.0.0/24

* **Einstellungen des Gateways für virtuelle Netzwerke**
  * **Name**: VNet4GW
  * **Ressourcengruppe**: USA (Westen)
  * **Generation:** Generation 1
  * **Gatewaytyp**: Wählen Sie **VPN** aus.
  * **VPN-Typ:** Wählen Sie **Routenbasiert** aus.
  * **SKU**: VpnGw1
  * **Virtuelles Netzwerk:** VNet4
  * **Adressbereich für Gatewaysubnetz**: 10.41.255.0/27
  * **Öffentliche IP-Adresse:** Neu erstellen
  * **Öffentliche IP-Adresse**: VNet4GWpip

* **Connection**
  * **Name**: VNet4toVNet1
  * **Gemeinsam verwendeter Schlüssel**: Sie können den gemeinsam verwendeten Schlüssel selbst erstellen. Wenn Sie die Verbindung zwischen den VNETs herstellen, müssen die Werte übereinstimmen. Verwenden Sie für diese Übung abc123.

## <a name="create-and-configure-vnet1"></a>Erstellen und Konfigurieren von VNet1

Wenn Sie bereits über ein VNet verfügen, sollten Sie überprüfen, ob die Einstellungen mit Ihrem Entwurf des VPN Gateways kompatibel sind. Achten Sie besonders auf Subnetze, die sich unter Umständen mit anderen Netzwerken überlappen. Bei überlappenden Subnetzen funktioniert die Verbindung nicht einwandfrei.

### <a name="to-create-a-virtual-network"></a>So erstellen Sie ein virtuelles Netzwerk

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Erstellen des Net1-Gateways

In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern. Falls Sie diese Konfiguration zu Übungszwecken erstellen, können Sie die [Beispieleinstellungen](#example-settings) verwenden.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>So erstellen Sie ein Gateway für das virtuelle Netzwerk

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Erstellen und Konfigurieren von VNet4

Nachdem Sie VNet1 konfiguriert haben, erstellen Sie VNet4 und das VNet4-Gateway, indem Sie die vorherigen Schritte wiederholen und die Werte durch VNet4-Werte ersetzen. Sie können mit dem Konfigurieren von VNet4 beginnen, auch wenn die Erstellung des virtuellen Netzwerkgateways für VNet1 noch nicht abgeschlossen ist. Achten Sie bei Verwendung eigener Werte darauf, dass sich die Adressräume nicht mit anderen VNETs überschneiden, mit denen Sie eine Verbindung herstellen möchten.

## <a name="configure-the-vnet1-gateway-connection"></a>Konfigurieren der VNet1-Gatewayverbindung

Nach Abschluss der Vorgänge für die virtuellen Netzwerkgateways für VNet1 und VNet4 können Sie die Verbindungen für das virtuelle Netzwerkgateway erstellen. In diesem Abschnitt erstellen Sie eine Verbindung von „VNet1“ zu „VNet4“. Diese Schritte gelten nur für VNets in demselben Abonnement. Wenn sich Ihre VNETs in unterschiedlichen Abonnements befinden, müssen Sie [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) zum Herstellen der Verbindung verwenden. Wenn Ihre VNETs in verschiedenen Ressourcengruppen in demselben Abonnement enthalten sind, können Sie sie jedoch mithilfe des Portals verbinden.

1. Wählen Sie im Azure-Portal **Alle Ressourcen** aus, geben Sie *Gateway für virtuelle Netzwerke* in das Suchfeld ein, und navigieren Sie dann zum Gateway für virtuelle Netzwerke für Ihr VNET. Beispiel: **VNet1GW**. Wählen Sie das Gateway aus, um die Seite **Gateway für virtuelle Netzwerke** zu öffnen.
1. Auf der Gatewayseite wechseln Sie zu **Einstellungen ->Verbindungen**. Wählen Sie anschließend **+Hinzufügen** aus.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png" alt-text="Seite „Verbindungen“":::
1. Die Seite **Verbindung hinzufügen** wird geöffnet.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4.png" alt-text="Verbindung hinzufügen":::

   Geben Sie auf der Seite **Verbindung hinzufügen** die Werte zum Herstellen der Verbindung ein:

   * **Name**: Geben Sie einen Namen für Ihre Verbindung ein. Beispiel: *VNet1toVNet4*.

   * **Verbindungstyp**: Wählen Sie **VNET-zu-VNET** in der Dropdownliste aus.

   * **Erstes Gateway für virtuelle Netzwerke**: Der Wert des Felds wird automatisch ausgefüllt, da Sie diese Verbindung über das angegebene virtuelle Netzwerkgateway herstellen.

   * **Zweites Gateway für virtuelle Netzwerke**: Dieses Feld enthält das virtuelle Netzwerkgateway des VNET, mit dem Sie eine Verbindung herstellen möchten. Wählen Sie **Ein weiteres virtuelles Netzwerkgateway auswählen** aus, um die Seite **Ein Gateway für virtuelle Netzwerke auswählen** zu öffnen.

      :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/choose.png" alt-text="Gateway auswählen":::

     * Sehen Sie sich die auf der Seite aufgeführten virtuellen Netzwerkgateways an. Beachten Sie, dass nur virtuelle Netzwerkgateways aus Ihrem Abonnement aufgeführt werden. Falls Sie eine Verbindung mit einem Gateway für virtuelle Netzwerke herstellen möchten, das nicht in Ihrem Abonnement enthalten ist, verwenden Sie [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     * Wählen Sie das Gateway für virtuelle Netzwerke aus, mit dem Sie eine Verbindung herstellen möchten.

   * **Gemeinsam verwendeter Schlüssel (PSK)** : Geben Sie in diesem Feld einen gemeinsam verwendeten Schlüssel für die Verbindung ein. Diesen Schlüssel können Sie selbst generieren oder erstellen. Bei einer Site-to-Site-Verbindung wird für Ihr lokales Gerät und für Ihre Verbindung mit dem Gateway für virtuelle Netzwerke der gleiche Schlüssel verwendet. Das Konzept ist hier ähnlich, nur dass diesmal keine Verbindung mit einem VPN-Gerät hergestellt wird, sondern mit einem anderen Gateway für virtuelle Netzwerke.
1. Klicken Sie zum Speichern der Änderungen auf **OK** .

## <a name="configure-the-vnet4-gateway-connection"></a>Konfigurieren der VNet4-Gatewayverbindung

Erstellen Sie als Nächstes eine Verbindung zwischen VNet4 und VNet1. Ermitteln Sie im Portal das mit VNet4 verbundene virtuelle Netzwerkgateway. Führen Sie die Schritte im vorherigen Abschnitt aus, und ersetzen Sie dabei die Werte, um eine Verbindung zwischen VNet4 und VNet1 zu erstellen. Verwenden Sie den gleichen gemeinsam verwendeten Schlüssel.

## <a name="verify-your-connections"></a>Überprüfen der Verbindungen

1. Suchen Sie im Azure-Portal nach dem Gateway für virtuelle Netzwerke. 
1. Wählen Sie auf der Seite **Gateway für virtuelle Netzwerke** **Verbindungen** aus, um die Seite **Verbindungen** für das Gateway für virtuelle Netzwerke anzuzeigen. Nach dem Verbindungsaufbau sehen Sie, dass sich die Werte für **Status** in **Verbunden** ändern.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png" alt-text="Überprüfen von Verbindungen":::
1. Wählen Sie unter der Spalte **Name** eine der Verbindungen aus, um weitere Informationen anzuzeigen. Sobald Daten übertragen werden, werden Werte für **Eingehende Daten** und **Ausgehende Daten** angezeigt.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png" alt-text="Der Screenshot zeigt eine Ressourcengruppe mit Werten für „Eingehende Daten“ und „Ausgehende Daten“.":::

## <a name="add-additional-connections"></a>Hinzufügen zusätzlicher Verbindungen

Wenn Sie zusätzliche Verbindungen hinzufügen möchten, navigieren Sie zum Gateway für virtuelle Netzwerke, über das Sie die Verbindung herstellen möchten, und wählen Sie **Verbindungen** aus. Sie können eine weitere VNET-to-VNET-Verbindung oder eine IPsec-S2S-Verbindung mit einem lokalen Standort erstellen. Denken Sie daran, den **Verbindungstyp** entsprechend dem gewünschten Verbindungstyp einzustellen. Stellen Sie vor dem Erstellen weiterer Verbindungen sicher, dass sich der Adressraum für Ihr virtuelles Netzwerk nicht mit den Adressräumen überlappt, mit denen Sie eine Verbindung herstellen möchten. Anweisungen zum Erstellen einer S2S-Verbindung finden Sie unter [Erstellen einer Site-to-Site-Verbindung](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>FAQs zu VNet-zu-VNet

Zeigen Sie die Details zu den häufig gestellten Fragen an, um zusätzliche Informationen zu VNet-zu-VNet-Verbindungen zu erhalten.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Beschränken des Netzwerkdatenverkehrs auf Ressourcen in einem virtuellen Netzwerk finden Sie unter [Netzwerksicherheit](../virtual-network/network-security-groups-overview.md).

* Informationen zur Weiterleitung von Datenverkehr zwischen Azure, lokalen Speicherorten und Internetressourcen finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md).