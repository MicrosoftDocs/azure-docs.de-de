---
title: 'Verbinden Ihres lokalen Netzwerks mit einem virtuellen Azure-Netzwerk: Site-to-Site-VPN (klassisch): Portal | Microsoft-Dokumentation'
description: Erstellen Sie über das öffentliche Internet eine IPsec-Verbindung zwischen Ihrem lokalen Netzwerk und dem klassischen virtuellen Azure-Netzwerk.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 346536f5797841a850ef97dc4667110eafa721a4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656955"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Erstellen einer Site-to-Site-Verbindung über das Azure-Portal (klassisch)

In diesem Artikel wird beschrieben, wie Sie das Azure-Portal zum Erstellen einer Site-to-Site-VPN-Gateway-Verbindung zwischen Ihrem lokalen Netzwerk und dem VNET verwenden. Die Schritte in diesem Artikel gelten für das klassische Bereitstellungsmodell und gelten nicht für das aktuelle Bereitstellungsmodell, Resource Manager. Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder -modell erstellen. Wählen Sie hierzu in der folgenden Liste eine andere Option:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

Eine Site-to-Site-VPN-Gateway-Verbindung wird verwendet, um Ihr lokales Netzwerk über einen IPsec/IKE-VPN-Tunnel (IKEv1 oder IKEv2) mit einem virtuellen Azure-Netzwerk zu verbinden. Für diese Art von Verbindung wird ein lokales VPN-Gerät benötigt, dem eine extern zugängliche, öffentliche IP-Adresse zugewiesen ist. Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagramm für die standortübergreifende Site-to-Site-VPN Gateway-Verbindung](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen erfüllt sind bzw. Folgendes vorhanden ist:

* Vergewissern Sie sich, dass Sie das klassische Bereitstellungsmodell verwenden möchten. Wenn Sie das Resource Manager-Bereitstellungsmodell nutzen möchten, helfen Ihnen die Informationen unter [Erstellen einer Site-to-Site-Verbindung im Azure-Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md) weiter. Es wird empfohlen, das Resource Manager-Bereitstellungsmodell zu verwenden, da das klassische Modell veraltet ist.
* Achten Sie darauf, dass Sie ein kompatibles VPN-Gerät nutzen (und über eine Person verfügen, die es konfigurieren kann). Weitere Informationen zu kompatiblen VPN-Geräten und zur Gerätekonfiguration finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).
* Vergewissern Sie sich, dass Sie über eine externe öffentliche IPv4-Adresse für Ihr VPN-Gerät verfügen.
* Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, wenden Sie sich an eine Person, die Ihnen diese Informationen zur Verfügung stellen kann. Beim Erstellen dieser Konfiguration müssen Sie die Präfixe für die IP-Adressbereiche angeben, die Azure an Ihren lokalen Standort weiterleitet. Kein Subnetz Ihres lokalen Netzwerks darf sich mit den Subnetzen des virtuellen Netzwerks überschneiden, mit dem Sie eine Verbindung herstellen möchten.
* PowerShell ist erforderlich, um den gemeinsam verwendeten Schlüssel anzugeben und die VPN Gateway-Verbindung zu erstellen. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Beispielkonfigurationswerte für diese Übung

In den Beispielen dieses Artikels werden die folgenden Werte verwendet. Sie können diese Werte zum Erstellen einer Testumgebung verwenden oder zum besseren Verständnis der Beispiele in diesem Artikel heranziehen. Bei Verwendung von IP-Adresswerten für den Adressraum sollten Sie sich in der Regel mit Ihrem Netzwerkadministrator absprechen, um überlappende Adressräume zu vermeiden, die sich auf das Routing auswirken können. Ersetzen Sie in diesem Fall die IP-Adresswerte durch ihre eigenen Werte, wenn Sie eine funktionierende Verbindung herstellen möchten.

* **Ressourcengruppe:** TestRG1
* **VNet-Name:** TestVNet1
* **Adressraum:** 10.11.0.0/16
* **Subnetzname:** FrontEnd
* **Subnetzadressbereich:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Region:** (USA) USA, Osten
* **Name des lokalen Standorts:** Site2
* **Clientadressraum**: Der Adressraum an Ihrem lokalen Standort.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Erstellen eines virtuellen Netzwerks

Wenn Sie ein virtuelles Netzwerk für eine S2S-Verbindung erstellen, müssen Sie Folgendes sicherstellen: Die von Ihnen angegebenen Adressräume dürfen sich nicht mit den Clientadressräumen für die lokalen Standorte überlappen, mit denen Sie eine Verbindung herstellen möchten. Bei überlappenden Subnetzen funktioniert die Verbindung nicht einwandfrei.

* Wenn Sie bereits über ein VNet verfügen, sollten Sie überprüfen, ob die Einstellungen mit Ihrem Entwurf des VPN Gateways kompatibel sind. Achten Sie besonders auf Subnetze, die sich unter Umständen mit anderen Netzwerken überlappen.

* Falls Sie noch nicht über ein virtuelles Netzwerk verfügen, erstellen Sie eines. Die Screenshots dienen lediglich zur Veranschaulichung. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen.

### <a name="to-create-a-virtual-network"></a>So erstellen Sie ein virtuelles Netzwerk

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>Konfigurieren von Standort und Gateway

### <a name="to-configure-the-site"></a>So konfigurieren Sie den Standort

Mit dem lokalen Standort ist in der Regel Ihr lokaler Standort gemeint. Er enthält die IP-Adresse des VPN-Geräts, mit dem Sie eine Verbindung herstellen, und die IP-Adressbereiche, die über das VPN-Gateway an das VPN-Gerät weitergeleitet werden.

1. Wählen Sie auf der Seite für Ihr VNET unter **Einstellungen** die Option **Site-to-Site-Verbindungen** aus.
1. Wählen Sie auf der Seite „Site-to-Site-Verbindungen“ **+ Hinzufügen** aus.
1. Lassen Sie auf der Seite **VPN-Verbindung und Gateway konfigurieren** für **Verbindungstyp** die Option **Site-to-Site** ausgewählt. Für diese Übung müssen Sie eine Kombination aus [Beispielwerten](#values) und eigenen Werten verwenden.

   * **IP-Adresse des VPN-Gateways:** Dies ist die öffentliche IP-Adresse des VPN-Geräts für Ihr lokales Netzwerk. Für das VPN-Gerät wird eine öffentliche IPv4-IP-Adresse benötigt. Geben Sie eine gültige öffentliche IP-Adresse für das VPN-Gerät an, mit dem Sie eine Verbindung herstellen möchten. Es muss in Azure erreichbar sein. Falls Sie die IP-Adresse Ihres VPN-Geräts nicht kennen, können Sie einfach einen Platzhalterwert (im Format einer gültigen öffentlichen IP-Adresse) angeben und den Wert später ändern.

   * **Clientadressraum**: Listen Sie die IP-Adressbereiche auf, die über dieses Gateway an das lokale Netzwerk weitergeleitet werden sollen. Sie können mehrere Adressraumbereiche hinzufügen. Stellen Sie sicher, dass sich die hier angegebenen Bereiche nicht mit den Bereichen anderer Netzwerke, mit denen für Ihr virtuelles Netzwerk Verbindungen hergestellt werden, oder mit den Adressbereichen des virtuellen Netzwerks selbst überlappen.
1. Wählen Sie unten auf der Seite NICHT die Option „Überprüfen + erstellen“ aus. Wählen Sie stattdessen **Weiter: Gateway >** aus.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>So konfigurieren Sie das Gateway des virtuellen Netzwerks

1. Wählen Sie auf der Seite **Gateway** die folgenden Werte aus:

   * **Size:** Hierbei handelt es sich um die Gateway-SKU, die Sie bei der Erstellung Ihres Gateways für virtuelle Netzwerke verwenden. Klassische VPN-Gateways verwenden die alten Gateway-SKUs. Weitere Informationen zu den alten Gateway-SKUs finden Sie unter [Arbeiten mit SKUs für virtuelle Netzwerkgateways (alte SKUs)](vpn-gateway-about-skus-legacy.md). Für diese Übung können Sie **Standard** auswählen.

   * **Routingtyp**: Wählen Sie den Routingtyp für Ihr Gateway aus. Dies wird auch als VPN-Typ bezeichnet. Es ist wichtig, den richtigen Typ auszuwählen, weil es nicht möglich ist, das Gateway in einen anderen Typ zu konvertieren. Ihr VPN-Gerät muss mit dem Routingtyp kompatibel sein, den Sie auswählen. Weitere Informationen zum Routingtyp finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Es kann sein, dass in Artikeln die VPN-Typen „RouteBased“ und „PolicyBased“ vorkommen. „Dynamisch“ entspricht „RouteBased“, und „Statisch“ entspricht „PolicyBased“. In der Regel sollten Sie dynamisches Routing verwenden.

   * **Gatewaysubnetz**: Die Größe des von Ihnen angegebenen Gatewaysubnetzes richtet sich nach der VPN-Gatewaykonfiguration, die Sie erstellen möchten. Obwohl es möglich ist, ein Gatewaysubnetz mit einer Größe von nur /29 zu erstellen, wird /27 oder /28 empfohlen. Dadurch wird ein größeres Subnetz erstellt, das mehr Adressen enthält. Die Verwendung eines größeren Gatewaysubnetzes ermöglicht die Vergabe einer ausreichenden Zahl von IP-Adressen für potenzielle zukünftige Konfigurationen.

1. Wählen Sie unten auf der Seite die Option **Überprüfen + erstellen** aus, um die Einstellungen zu überprüfen. Klicken Sie auf **Erstellen**, um die Bereitstellung durchzuführen. Abhängig von der ausgewählten SKU kann die Erstellung eines Gateways für virtuelle Netzwerke bis zu 45 Minuten dauern.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>Konfigurieren des VPN-Geräts

Für Site-to-Site-Verbindungen mit einem lokalen Netzwerk ist ein VPN-Gerät erforderlich. In diesem Schritt konfigurieren Sie Ihr VPN-Gerät. Beim Konfigurieren des VPN-Geräts benötigen Sie die folgenden Werte:

* Einen gemeinsam verwendeten Schlüssel. Dies ist derselbe gemeinsame Schlüssel, den Sie beim Erstellen Ihrer Site-to-Site-VPN-Verbindung angeben. In unseren Beispielen verwenden wir einen einfachen gemeinsamen Schlüssel. Es wird empfohlen, einen komplexeren Schlüssel zu generieren.
* Die öffentliche IP-Adresse Ihres Gateways für virtuelle Netzwerke. Sie können die öffentliche IP-Adresse mit dem Azure-Portal, mit PowerShell oder mit der CLI anzeigen.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Abrufen der Werte

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>Erstellen der Verbindung

> [!NOTE]
> Beim klassischen Bereitstellungsmodell ist dieser Schritt im Azure-Portal oder über Azure Cloud Shell nicht verfügbar. Sie müssen die Dienstverwaltungsversion der Azure PowerShell-Cmdlets lokal auf Ihrem Desktop verwenden.
>

In diesem Schritt legen Sie den gemeinsam verwendeten Schlüssel unter Verwendung der Werte aus dem vorherigen Schritt fest und erstellen die Verbindung. Der von Ihnen festgelegte Schlüssel muss der gleiche Schlüssel sein, der in Ihrer VPN-Gerätekonfiguration verwendet wurde.

1. Legen Sie den gemeinsam verwendeten Schlüssel fest, und erstellen Sie die Verbindung.

   * Ändern Sie den Wert von „-VNetName“ und „-LocalNetworkSiteName“. Setzen Sie den Wert in einfache Anführungszeichen, wenn ein Name Leerzeichen enthält.
   * „-SharedKey“ ist ein Wert, den Sie generieren und dann angeben. Im Beispiel wurde „abc123“ verwendet, aber Sie können (und sollten) einen komplexeren Wert generieren. Entscheidend ist Folgendes: Der Wert, den Sie hier angeben, muss dem Wert entsprechen, den Sie beim Konfigurieren Ihres VPN-Geräts angegeben haben.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Wenn die Verbindung erstellt wird, lautet das Ergebnis: **Status: Erfolgreich** zurückgegeben werden.

## <a name="verify-your-connection"></a><a name="verify"></a>Überprüfen der Verbindung

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Sollten bei der Verbindungsherstellung Probleme auftreten, wechseln Sie über das Inhaltsverzeichnis auf der linken Seite zum Abschnitt **Problembehandlung**.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Zurücksetzen einer VPN Gateway-Instanz

Das Zurücksetzen von Azure VPN Gateway-Instanzen ist nützlich, wenn die standortübergreifende VPN-Verbindung bei mindestens einem Site-to-Site-VPN-Tunnel unterbrochen ist. In diesem Fall funktionieren Ihre lokalen VPN-Geräte ordnungsgemäß, können jedoch keine IPsec-Tunnelverbindungen mit Azure VPN Gateway-Instanzen herstellen. Eine entsprechende Anleitung finden Sie unter [Zurücksetzen einer VPN Gateway-Instanz](./reset-gateway.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Ändern einer Gateway-SKU

Eine Anleitung zum Ändern einer Gateway-SKU finden Sie unter [Ändern der Größe einer Gateway-SKU](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Nächste Schritte

* Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](../index.yml) .
* Weitere Informationen zur Tunnelerzwingung finden Sie unter [Informationen zur Tunnelerzwingung](vpn-gateway-about-forced-tunneling.md).