---
title: Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung über das Azure-Portal | Microsoft-Dokumentation
description: Erstellen Sie eine VPN Gateway-Verbindung zwischen VNets unter Verwendung von Resource Manager und Azure-Portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 94b32595cf2c884ccfd1362f6c8d03f542aabfc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128380"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie zwischen virtuellen Netzwerken (VNETs) eine VNET-zu-VNET-Verbindung herstellen. Virtuelle Netzwerke können sich in verschiedenen Regionen befinden und zu verschiedenen Abonnements gehören. Beim Verbinden von VNETs aus unterschiedlichen Abonnements müssen die Abonnements nicht demselben Active Directory-Mandanten zugeordnet sein. 

![v2v-Diagramm](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

Die Schritte in diesem Artikel gelten für das Azure Resource Manager-Bereitstellungsmodell und verwenden das Azure-Portal. Sie können diese Konfiguration mit einem anderen Bereitstellungstool oder Modell erstellen, indem Sie Optionen verwenden, die in den folgenden Artikeln beschrieben werden:

> [!div class="op_single_selector"]
> * [Azure-Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
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

![Informationen zu Verbindungen](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informationen zu Verbindungen")

In diesem Artikel wird beschrieben, wie Sie mit dem Verbindungstyp „VNET-zu-VNET“ VNETs verbinden. Sie können die folgenden Beispieleinstellungswerte nutzen, wenn Sie diese Schritte als Übung verwenden. Im Beispiel befinden sich die virtuellen Netzwerke im selben Abonnement, aber in verschiedenen Ressourcengruppen. Wenn sich Ihre VNets in unterschiedlichen Abonnements befinden, können Sie die Verbindung nicht im Portal herstellen. Sie können [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) oder stattdessen die [CLI](vpn-gateway-howto-vnet-vnet-cli.md) verwenden. Weitere Informationen zu VNET-zu-VNET-Verbindungen finden Sie unter [Häufig gestellte Fragen zu VNET-zu-VNET-Verbindungen](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Beispieleinstellungen

**Werte für TestVNet1:**

- **Einstellungen für das virtuelle Netzwerk**
    - **Name**: Geben Sie *TestVNet1* ein.
    - **Adressraum**: Geben Sie *10.11.0.0/16* ein.
    - **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten.
    - **Ressourcengruppe**: Geben Sie *TestRG1* ein.
    - **Standort**: Wählen Sie **USA, Osten** aus.
    - **Subnetz**
        - **Name**: Geben Sie *FrontEnd* ein.
        - **Adressbereich**: Geben Sie *10.11.0.0/24* ein.
    - **Gatewaysubnetz**:
        - **Name**: *GatewaySubnet* wird automatisch eingetragen.
        - **Adressbereich**: Geben Sie *10.11.255.0/27* ein.
    - **DNS-Server**: Wählen Sie **Benutzerdefiniert** aus, und geben Sie die IP-Adresse Ihres DNS-Servers ein.

- **Einstellungen des Gateways für virtuelle Netzwerke** 
    - **Name**: Geben Sie *TestVNet1GW* ein.
    - **Gatewaytyp**: Wählen Sie **VPN** aus.
    - **VPN-Typ:** Wählen Sie **Routenbasiert** aus.
    - **SKU**: Wählen Sie die gewünschte Gateway-SKU aus.
    - **Öffentliche IP-Adresse**: Geben Sie *TestVNet1GWIP* ein.
    - **Connection** 
       - **Name**: Geben Sie *TestVNet1toTestVNet4* ein.
       - **Gemeinsam verwendeter Schlüssel**: Geben Sie *abc123* ein. Sie können den gemeinsam verwendeten Schlüssel selbst erstellen. Wenn Sie die Verbindung zwischen den VNETs herstellen, müssen die Werte übereinstimmen.

**Werte für TestVNet4:**

- **Einstellungen für das virtuelle Netzwerk**
   - **Name**: Geben Sie *TestVNet4*.
   - **Adressraum**: Geben Sie *10.41.0.0/16* ein.
   - **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten.
   - **Ressourcengruppe**: Geben Sie *TestRG4* ein.
   - **Standort**: Wählen Sie **USA, Westen** aus.
   - **Subnetz** 
      - **Name**: Geben Sie *FrontEnd* ein.
      - **Adressbereich**: Geben Sie *10.41.0.0/24* ein.
   - **GatewaySubnet** 
      - **Name**: *GatewaySubnet* wird automatisch eingetragen.
      - **Adressbereich**: Geben Sie *10.41.255.0/27* ein.
   - **DNS-Server**: Wählen Sie **Benutzerdefiniert** aus, und geben Sie die IP-Adresse Ihres DNS-Servers ein.

- **Einstellungen des Gateways für virtuelle Netzwerke** 
    - **Name**: Geben Sie *TestVNet4GW* ein.
    - **Gatewaytyp**: Wählen Sie **VPN** aus.
    - **VPN-Typ:** Wählen Sie **Routenbasiert** aus.
    - **SKU**: Wählen Sie die gewünschte Gateway-SKU aus.
    - **Öffentliche IP-Adresse**: Geben Sie *TestVNet4GWIP* ein.
    - **Connection** 
       - **Name**: Geben Sie *TestVNet4toTestVNet1* ein.
       - **Gemeinsam verwendeter Schlüssel**: Geben Sie *abc123* ein. Sie können den gemeinsam verwendeten Schlüssel selbst erstellen. Wenn Sie die Verbindung zwischen den VNETs herstellen, müssen die Werte übereinstimmen.

## <a name="create-and-configure-testvnet1"></a>Erstellen und Konfigurieren von „TestVNet1“
Wenn Sie bereits über ein VNet verfügen, sollten Sie überprüfen, ob die Einstellungen mit Ihrem Entwurf des VPN Gateways kompatibel sind. Achten Sie besonders auf Subnetze, die sich unter Umständen mit anderen Netzwerken überlappen. Bei überlappenden Subnetzen funktioniert die Verbindung nicht einwandfrei. Wenn Ihr VNET mit den richtigen Einstellungen konfiguriert wurde, können Sie mit den Schritten im Abschnitt „Angeben eines DNS-Servers“ beginnen.

### <a name="to-create-a-virtual-network"></a>So erstellen Sie ein virtuelles Netzwerk
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>Hinzufügen weiterer Adressräume und Erstellen von Subnetzen
Sie können dem VNet nach dem Erstellen weitere Adressräume hinzufügen und Subnetze erstellen.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-gateway-subnet"></a>Erstellen eines Gatewaysubnetzes
Bevor Sie ein Gateway für Ihr virtuelles Netzwerk erstellen, müssen Sie zunächst das Gatewaysubnetz erstellen. Das Gatewaysubnetz enthält die IP-Adressen, die vom Gateway des virtuellen Netzwerks verwendet werden. Erstellen Sie nach Möglichkeit ein Gatewaysubnetz mit einem CIDR-Block vom Typ „/28“ oder „/27“, damit genügend IP-Adressen für zukünftige zusätzliche Konfigurationsanforderungen zur Verfügung stehen.

Wenn Sie diese Konfiguration als Übung erstellen, können Sie beim Erstellen des Gatewaysubnetzes diese [Beispieleinstellungen](#example-settings) verwenden.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>So erstellen Sie ein Gatewaysubnetz
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="specify-a-dns-server-optional"></a>Angeben eines DNS-Servers (optional)
DNS ist für VNET-zu-VNET-Verbindungen nicht erforderlich. Wenn Sie für Ressourcen, die in Ihren virtuellen Netzwerken bereitgestellt werden, die Namensauflösung verwenden möchten, müssen Sie aber einen DNS-Server angeben. Diese Einstellung bietet die Möglichkeit, den DNS-Server anzugeben, den Sie zur Namensauflösung für dieses virtuelle Netzwerk verwenden möchten. Mit dieser Einstellung wird kein DNS-Server erstellt.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="create-a-virtual-network-gateway"></a>Erstellen eines Gateways für das virtuelle Netzwerk
In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern. Falls Sie diese Konfiguration zu Übungszwecken erstellen, können Sie die [Beispieleinstellungen](#example-settings) verwenden.

### <a name="to-create-a-virtual-network-gateway"></a>So erstellen Sie ein Gateway für das virtuelle Netzwerk
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="create-and-configure-testvnet4"></a>Erstellen und Konfigurieren von „TestVNet4“
Nachdem Sie „TestVNet1“ konfiguriert haben, erstellen Sie „TestVNet4“. Wiederholen Sie hierzu die vorherigen Schritte, und ersetzen Sie die Werte mit den Werten von „TestVNet4“. Sie können mit dem Konfigurieren von „TestVNet4“ beginnen, auch wenn die Erstellung des virtuellen Netzwerkgateways für „TestVNet1“ noch nicht abgeschlossen ist. Achten Sie bei Verwendung eigener Werte darauf, dass sich die Adressräume nicht mit anderen VNETs überschneiden, mit denen Sie eine Verbindung herstellen möchten.

## <a name="configure-the-testvnet1-gateway-connection"></a>Konfigurieren der TestVNet1-Gatewayverbindung
Nach Abschluss der Vorgänge für die virtuellen Netzwerkgateways für „TestVNet1“ und „TestVNet4“ können Sie die Verbindungen für das virtuelle Netzwerkgateway erstellen. In diesem Abschnitt erstellen Sie eine Verbindung von „VNet1“ zu „VNet4“. Diese Schritte gelten nur für VNets in demselben Abonnement. Wenn sich Ihre VNETs in unterschiedlichen Abonnements befinden, müssen Sie [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) zum Herstellen der Verbindung verwenden. Wenn Ihre VNETs in verschiedenen Ressourcengruppen in demselben Abonnement enthalten sind, können Sie sie jedoch mithilfe des Portals verbinden.

1. Wählen Sie im Azure-Portal **Alle Ressourcen** aus, geben Sie *Gateway für virtuelle Netzwerke* in das Suchfeld ein, und navigieren Sie dann zum Gateway für virtuelle Netzwerke für Ihr VNET. Beispiel: **TestVNet1GW**. Wählen Sie dies aus, um die Seite **Gateway für virtuelle Netzwerke** zu öffnen.

   ![Seite „Verbindungen“](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Seite „Verbindungen“")
2. Wählen Sie unter **Einstellungen** **Verbindungen** und dann **Hinzufügen** aus, um die Seite **Verbindung hinzufügen** zu öffnen.

   ![Hinzufügen der Verbindung](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Hinzufügen der Verbindung")
3. Geben Sie auf der Seite **Verbindung hinzufügen** die Werte zum Herstellen der Verbindung ein:

   - **Name**: Geben Sie einen Namen für Ihre Verbindung ein. Beispiel: *TestVNet1toTestVNet4*.

   - **Verbindungstyp**: Wählen Sie **VNET-zu-VNET** in der Dropdownliste aus.

   - **Erstes Gateway für virtuelle Netzwerke**: Der Wert des Felds wird automatisch ausgefüllt, da Sie diese Verbindung über das angegebene virtuelle Netzwerkgateway herstellen.

   - **Zweites Gateway für virtuelle Netzwerke**: Dieses Feld enthält das virtuelle Netzwerkgateway des VNET, mit dem Sie eine Verbindung herstellen möchten. Wählen Sie **Ein weiteres virtuelles Netzwerkgateway auswählen** aus, um die Seite **Ein Gateway für virtuelle Netzwerke auswählen** zu öffnen.

     - Sehen Sie sich die auf der Seite aufgeführten virtuellen Netzwerkgateways an. Beachten Sie, dass nur virtuelle Netzwerkgateways aus Ihrem Abonnement aufgeführt werden. Falls Sie eine Verbindung mit einem Gateway für virtuelle Netzwerke herstellen möchten, das nicht in Ihrem Abonnement enthalten ist, verwenden Sie [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Wählen Sie das Gateway für virtuelle Netzwerke aus, mit dem Sie eine Verbindung herstellen möchten.

     - **Gemeinsam verwendeter Schlüssel (PSK)** : Geben Sie in diesem Feld einen gemeinsam verwendeten Schlüssel für die Verbindung ein. Diesen Schlüssel können Sie selbst generieren oder erstellen. Bei einer Site-to-Site-Verbindung wird für Ihr lokales Gerät und für Ihre Verbindung mit dem Gateway für virtuelle Netzwerke der gleiche Schlüssel verwendet. Das Konzept ist hier ähnlich, nur dass diesmal keine Verbindung mit einem VPN-Gerät hergestellt wird, sondern mit einem anderen Gateway für virtuelle Netzwerke.
    
4. Klicken Sie zum Speichern der Änderungen auf **OK** .

## <a name="configure-the-testvnet4-gateway-connection"></a>Konfigurieren der TestVNet4-Gatewayverbindung
Als Nächstes erstellen Sie eine Verbindung von „TestVNet4“ zu „TestVNet1“. Suchen Sie im Portal nach dem virtuellen Netzwerkgateway, das TestVNet4 zugeordnet ist. Führen Sie die Schritte im vorherigen Abschnitt aus, und ersetzen Sie dabei die Werte, um eine Verbindung von „TestVNet4“ zu „TestVNet1“ zu erstellen. Verwenden Sie den gleichen gemeinsam verwendeten Schlüssel.

## <a name="verify-your-connections"></a>Überprüfen der Verbindungen

Suchen Sie im Azure-Portal nach dem Gateway für virtuelle Netzwerke. Wählen Sie auf der Seite **Gateway für virtuelle Netzwerke** **Verbindungen** aus, um die Seite **Verbindungen** für das Gateway für virtuelle Netzwerke anzuzeigen. Sobald die Verbindung hergestellt wurde, ändern sich die **Statuswerte** in **Erfolgreich** und **Verbunden**. Wählen Sie eine Verbindung aus, um die Seite **Zusammenfassung** zu öffnen und weitere Informationen anzuzeigen.

![Erfolgreich](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Erfolgreich")

Sobald Daten übertragen werden, werden Werte für **Eingehende Daten** und **Ausgehende Daten** angezeigt.

![Zusammenfassung](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Zusammenfassung")

## <a name="add-additional-connections"></a>Hinzufügen zusätzlicher Verbindungen

Wenn Sie zusätzliche Verbindungen hinzufügen möchten, navigieren Sie zum Gateway für virtuelle Netzwerke, über das Sie die Verbindung herstellen möchten, und wählen Sie **Verbindungen** aus. Sie können eine weitere VNET-to-VNET-Verbindung oder eine IPsec-S2S-Verbindung mit einem lokalen Standort erstellen. Denken Sie daran, den **Verbindungstyp** entsprechend dem gewünschten Verbindungstyp einzustellen. Stellen Sie vor dem Erstellen weiterer Verbindungen sicher, dass sich der Adressraum für Ihr virtuelles Netzwerk nicht mit den Adressräumen überlappt, mit denen Sie eine Verbindung herstellen möchten. Anweisungen zum Erstellen einer S2S-Verbindung finden Sie unter [Erstellen einer Site-to-Site-Verbindung](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>FAQs zu VNet-zu-VNet
Zeigen Sie die Details zu den häufig gestellten Fragen an, um zusätzliche Informationen zu VNet-zu-VNet-Verbindungen zu erhalten.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Beschränken des Netzwerkdatenverkehrs auf Ressourcen in einem virtuellen Netzwerk finden Sie unter [Netzwerksicherheit](../virtual-network/security-overview.md).

Informationen zur Weiterleitung von Datenverkehr zwischen Azure, lokalen Speicherorten und Internetressourcen finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md).
