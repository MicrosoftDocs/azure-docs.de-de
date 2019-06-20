---
title: 'Verbinden Ihres lokalen Netzwerks mit einem virtuellen Azure-Netzwerk: Site-to-Site-VPN (klassisch): Portal | Microsoft-Dokumentation'
description: Erstellen Sie über das öffentliche Internet eine IPsec-Verbindung zwischen Ihrem lokalen Netzwerk und dem klassischen virtuellen Azure-Netzwerk.
services: vpn-gateway
author: cherylmc
manager: jpconnock
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: a2d714cae187e4ebcf2eefd37c61484dc48495e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105943"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Erstellen einer Site-to-Site-Verbindung über das Azure-Portal (klassisch)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

In diesem Artikel wird beschrieben, wie Sie das Azure-Portal zum Erstellen einer Site-to-Site-VPN Gateway-Verbindung zwischen Ihrem lokalen Netzwerk und dem VNET verwenden. Die Schritte in diesem Artikel gelten für das klassische Bereitstellungsmodell. Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder -modell erstellen. Wählen Sie hierzu in der folgenden Liste eine andere Option:

> [!div class="op_single_selector"]
> * [Azure-Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Eine Site-to-Site-VPN-Gateway-Verbindung wird verwendet, um Ihr lokales Netzwerk über einen IPsec/IKE-VPN-Tunnel (IKEv1 oder IKEv2) mit einem virtuellen Azure-Netzwerk zu verbinden. Für diese Art von Verbindung wird ein lokales VPN-Gerät benötigt, dem eine extern zugängliche, öffentliche IP-Adresse zugewiesen ist. Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagramm für die standortübergreifende Site-to-Site-VPN Gateway-Verbindung](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen erfüllt sind bzw. Folgendes vorhanden ist:

* Vergewissern Sie sich, dass Sie das klassische Bereitstellungsmodell verwenden möchten. Wenn Sie das Resource Manager-Bereitstellungsmodell nutzen möchten, helfen Ihnen die Informationen unter [Erstellen einer Site-to-Site-Verbindung im Azure-Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md) weiter. Es wird empfohlen, nach Möglichkeit das Resource Manager-Bereitstellungsmodell zu verwenden.
* Achten Sie darauf, dass Sie ein kompatibles VPN-Gerät nutzen (und über eine Person verfügen, die es konfigurieren kann). Weitere Informationen zu kompatiblen VPN-Geräten und zur Gerätekonfiguration finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).
* Vergewissern Sie sich, dass Sie über eine externe öffentliche IPv4-Adresse für Ihr VPN-Gerät verfügen.
* Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, wenden Sie sich an eine Person, die Ihnen diese Informationen zur Verfügung stellen kann. Beim Erstellen dieser Konfiguration müssen Sie die Präfixe für die IP-Adressbereiche angeben, die Azure an Ihren lokalen Standort weiterleitet. Kein Subnetz Ihres lokalen Netzwerks darf sich mit den Subnetzen des virtuellen Netzwerks überschneiden, mit dem Sie eine Verbindung herstellen möchten.
* Derzeit muss in PowerShell der gemeinsam verwendete Schlüssel angegeben und die VPN Gateway-Verbindung erstellt werden. Installieren Sie die aktuelle Version der PowerShell-Cmdlets der Azure-Dienstverwaltung. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview). Bei der Verwendung von PowerShell für diese Konfiguration müssen Sie PowerShell als Administrator ausführen. 

### <a name="values"></a>Beispielkonfigurationswerte für diese Übung

In den Beispielen dieses Artikels werden die folgenden Werte verwendet. Sie können diese Werte zum Erstellen einer Testumgebung verwenden oder zum besseren Verständnis der Beispiele in diesem Artikel heranziehen.

* **VNet-Name:** TestVNet1
* **Adressraum:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (für diese Übung optional)
* **Subnetze:**
  * Front-End: 10.11.0.0/24
  * Back-End: 10.12.0.0/24 (für diese Übung optional)
* **GatewaySubnet:** 10.11.255.0/27
* **Ressourcengruppe:** TestRG1
* **Standort:** USA (Ost)
* **DNS-Server:** 10.11.0.3 (für diese Übung optional)
* **Name des lokalen Standorts:** Site2
* **Clientadressraum**: Der Adressraum an Ihrem lokalen Standort.

## <a name="CreatVNet"></a>1. Erstellen eines virtuellen Netzwerks

Wenn Sie ein virtuelles Netzwerk für eine S2S-Verbindung erstellen, müssen Sie Folgendes sicherstellen: Die von Ihnen angegebenen Adressräume dürfen sich nicht mit den Clientadressräumen für die lokalen Standorte überlappen, mit denen Sie eine Verbindung herstellen möchten. Bei überlappenden Subnetzen funktioniert die Verbindung nicht einwandfrei.

* Wenn Sie bereits über ein VNet verfügen, sollten Sie überprüfen, ob die Einstellungen mit Ihrem Entwurf des VPN Gateways kompatibel sind. Achten Sie besonders auf Subnetze, die sich unter Umständen mit anderen Netzwerken überlappen. 

* Falls Sie noch nicht über ein virtuelles Netzwerk verfügen, erstellen Sie eines. Die Screenshots dienen lediglich zur Veranschaulichung. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen.

### <a name="to-create-a-virtual-network"></a>So erstellen Sie ein virtuelles Netzwerk

1. Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie unten auf der Seite auf **+** . Geben Sie im Feld **Marketplace durchsuchen** die Zeichenfolge „Virtual Network“ ein. Klicken Sie in der zurückgegebenen Liste auf **Virtual Network**, um die Seite **Virtual Network** zu öffnen.

   ![Seite für die Suche nach einem virtuellen Netzwerk](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. Wählen Sie im unteren Bereich der Seite „Virtual Network“ in der Dropdownliste **Bereitstellungsmodell auswählen** die Option **Klassisch** aus, und klicken Sie dann auf **Erstellen**.

   ![Bereitstellungsmodell auswählen](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. Konfigurieren Sie auf der Seite **Virtuelles Netzwerk erstellen (klassisch)** die VNET-Einstellungen. Auf dieser Seite fügen Sie Ihren ersten Adressraum und einen Adressbereich des Subnetzes hinzu. Nachdem Sie die Erstellung des VNet abgeschlossen haben, können Sie zurückgehen und weitere Subnetze und Adressräume hinzufügen.

   ![Seite „Virtuelles Netzwerk erstellen“](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Seite „Virtuelles Netzwerk erstellen“")
5. Überprüfen Sie, ob es sich um das richtige **Abonnement** handelt. Das Abonnement kann über die Dropdownliste geändert werden.
6. Klicken Sie auf **Ressourcengruppe**, und wählen Sie entweder eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue, indem Sie einen Namen eingeben. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Wählen Sie als Nächstes für das VNet die Einstellungen für **Standort** aus. Der Standort gibt an, wo sich die in diesem VNet bereitgestellten Ressourcen befinden.
8. Wenn das VNET komfortabel auf dem Dashboard zur Verfügung stehen soll, wählen Sie **An Dashboard anheften** aus. Klicken Sie auf **Erstellen**, um das VNET zu erstellen.

   ![An Dashboard anheften](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "An Dashboard anheften")
9. Nach dem Klicken auf „Erstellen“ wird im Dashboard eine Kachel angezeigt, die den Status für Ihr VNet anzeigt. Die Kachel verändert sich, wenn das VNet erstellt wird.

   ![Kachel „Virtuelles Netzwerk wird erstellt“](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Virtuelles Netzwerk wird erstellt")

## <a name="additionaladdress"></a>2. Hinzufügen weiterer Adressräume

Nach dem Erstellen Ihres virtuellen Netzwerks können Sie weitere Adressräume hinzufügen. Das Hinzufügen von zusätzlichen Adressräumen ist für eine S2S-Konfiguration nicht obligatorisch, aber Sie können die folgenden Schritte ausführen, falls Sie mehrere Adressräume benötigen:

1. Suchen Sie im Portal nach den virtuellen Netzwerken.
2. Klicken Sie auf der Seite für Ihr virtuelles Netzwerk im Abschnitt **Einstellungen** auf **Adressraum**.
3. Klicken Sie auf der Seite „Adressraum“ auf **+Hinzufügen**, und geben Sie weitere Adressräume ein.

## <a name="dns"></a>3. Angeben eines DNS-Servers

Die DNS-Einstellungen sind für eine S2S-Konfiguration nicht obligatorisch, aber das DNS wird benötigt, wenn Sie die Namensauflösung nutzen möchten. Wenn ein Wert angegeben wird, wird kein neuer DNS-Server erstellt. Die IP-Adresse des angegebenen DNS-Servers muss dazu in der Lage sein, die Namen für die Ressourcen aufzulösen, mit denen Sie eine Verbindung herstellen möchten. Für die Beispieleinstellungen wurde eine private IP-Adresse verwendet. Die verwendete IP-Adresse ist wahrscheinlich nicht die IP-Adresse Ihres DNS-Servers. Achten Sie darauf, dass Sie Ihre eigenen Werte verwenden.

Nach Erstellung des virtuellen Netzwerks können Sie für die Namensauflösung die IP-Adresse eines DNS-Servers hinzufügen. Öffnen Sie die Einstellungen für Ihr virtuelles Netzwerk, klicken Sie auf „DNS-Server“, und fügen Sie die IP-Adresse des gewünschten DNS-Servers hinzu, den Sie für die Namensauflösung verwenden möchten.

1. Suchen Sie im Portal nach den virtuellen Netzwerken.
2. Klicken Sie auf der Seite für Ihr virtuelles Netzwerk im Abschnitt **Einstellungen** auf **DNS-Server**.
3. Fügen Sie einen DNS-Server hinzu.
4. Klicken Sie oben auf der Seite auf **Speichern**, um Ihre Einstellungen zu speichern.

## <a name="localsite"></a>4. Konfigurieren des lokalen Standorts

Mit dem lokalen Standort ist in der Regel Ihr lokaler Standort gemeint. Er enthält die IP-Adresse des VPN-Geräts, mit dem Sie eine Verbindung herstellen, und die IP-Adressbereiche, die über das VPN-Gateway an das VPN-Gerät weitergeleitet werden.

1. Navigieren Sie im Portal zu dem virtuellen Netzwerk, für das Sie ein Gateway erstellen möchten.
2. Klicken Sie auf der Seite für Ihr virtuelles Netzwerk auf der Seite **Übersicht** im Abschnitt mit den VPN-Verbindungen auf **Gateway**, um die Seite **Neue VPN-Verbindung** zu öffnen.

   ![Klicken zum Konfigurieren der Gatewayeinstellungen](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Klicken zum Konfigurieren der Gatewayeinstellungen")
3. Wählen Sie auf der Seite **Neue VPN-Verbindung** die Option **Standort-zu-Standort**.
4. Klicken Sie auf **Lokaler Standort – Erforderliche Einstellungen konfigurieren**, um die Seite **Lokaler Standort** zu öffnen. Konfigurieren Sie die Einstellungen, und klicken Sie dann auf **OK**, um die Einstellungen zu speichern.
   - **Name:** Erstellen Sie einen Namen für Ihren lokalen Standort, damit Sie ihn leicht identifizieren können.
   - **IP-Adresse des VPN-Gateways:** Dies ist die öffentliche IP-Adresse des VPN-Geräts für Ihr lokales Netzwerk. Für das VPN-Gerät wird eine öffentliche IPv4-IP-Adresse benötigt. Geben Sie eine gültige öffentliche IP-Adresse für das VPN-Gerät an, mit dem Sie eine Verbindung herstellen möchten. Es muss in Azure erreichbar sein. Falls Sie die IP-Adresse Ihres VPN-Geräts nicht kennen, können Sie einfach einen Platzhalterwert (im Format einer gültigen öffentlichen IP-Adresse) angeben und den Wert später ändern.
   - **Clientadressraum**: Listen Sie die IP-Adressbereiche auf, die über dieses Gateway an das lokale Netzwerk weitergeleitet werden sollen. Sie können mehrere Adressraumbereiche hinzufügen. Stellen Sie sicher, dass sich die hier angegebenen Bereiche nicht mit den Bereichen anderer Netzwerke, mit denen für Ihr virtuelles Netzwerk Verbindungen hergestellt werden, oder mit den Adressbereichen des virtuellen Netzwerks selbst überlappen.

   ![Lokaler Standort](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Konfigurieren des lokalen Standorts")

## <a name="gatewaysubnet"></a>5. Konfigurieren des Gatewaysubnetzes

Sie müssen für Ihr VPN-Gateway ein Gatewaysubnetz erstellen. Das Gatewaysubnetz enthält die IP-Adressen, die von den Diensten des VPN-Gateways verwendet werden.

1. Aktivieren Sie auf der Seite **Neue VPN-Verbindung** das Kontrollkästchen **Gateway sofort erstellen**. Die Seite „Optionale Gatewaykonfiguration“ wird angezeigt. Wenn Sie das Kontrollkästchen nicht aktivieren, wird die Seite zum Konfigurieren des Gatewaysubnetzes nicht angezeigt.

   ![Gatewaykonfiguration – Subnetz, Größe, Routingtyp](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Gatewaykonfiguration – Subnetz, Größe, Routingtyp")
2. Um die Seite **Gatewaykonfiguration** zu öffnen, klicken Sie auf **Optionale Gatewaykonfiguration – Subnetz, Größe und Routingtyp**.
3. Klicken Sie auf der Seite **Gatewaykonfiguration** auf **Subnetz – Erforderliche Einstellungen konfigurieren**, um die Seite **Subnetz hinzufügen** zu öffnen.

   ![Gatewaykonfiguration – Gatewaysubnetz](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Gatewaykonfiguration – Gatewaysubnetz")
4. Fügen Sie auf der Seite **Subnetz hinzufügen** das Gatewaysubnetz hinzu. Die Größe des von Ihnen angegebenen Gatewaysubnetzes richtet sich nach der VPN-Gatewaykonfiguration, die Sie erstellen möchten. Obwohl es möglich ist, ein Gatewaysubnetz mit einer Größe von nur /29 zu erstellen, wird /27 oder /28 empfohlen. Dadurch wird ein größeres Subnetz erstellt, das mehr Adressen enthält. Die Verwendung eines größeren Gatewaysubnetzes ermöglicht die Vergabe einer ausreichenden Zahl von IP-Adressen für potenzielle zukünftige Konfigurationen.

   ![Hinzufügen eines Gatewaysubnetzes](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Hinzufügen eines Gatewaysubnetzes")

## <a name="sku"></a>6. Angeben der SKU und des VPN-Typs

1. Wählen Sie die **Gatewaygröße**aus. Hierbei handelt es sich um die Gateway-SKU, die Sie bei der Erstellung Ihres Gateways für virtuelle Netzwerke verwenden. Im Portal ist standardmäßig die SKU **Basic** ausgewählt. Klassische VPN-Gateways verwenden die alten Gateway-SKUs. Weitere Informationen zu den alten Gateway-SKUs finden Sie unter [Arbeiten mit SKUs für virtuelle Netzwerkgateways (alte SKUs)](vpn-gateway-about-skus-legacy.md).

   ![Auswählen der SKU und des VPN-Typs](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Auswählen der SKU und des VPN-Typs")
2. Wählen Sie den **Routingtyp** für Ihr Gateway aus. Dies wird auch als VPN-Typ bezeichnet. Es ist wichtig, den richtigen Gatewaytyp auszuwählen, weil es nicht möglich ist, das Gateway in einen anderen Typ zu konvertieren. Ihr VPN-Gerät muss mit dem Routingtyp kompatibel sein, den Sie auswählen. Weitere Informationen zum VPN-Typ finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Es kann sein, dass in Artikeln die VPN-Typen „RouteBased“ und „PolicyBased“ vorkommen. „Dynamisch“ entspricht „RouteBased“, und „Statisch“ entspricht „PolicyBased“.
3. Klicken Sie auf **OK** , um die Einstellungen zu speichern.
4. Klicken Sie am unteren Rand der Seite **Neue VPN-Verbindung** auf **OK**, um mit der Erstellung Ihres Gateways für virtuelle Netzwerke zu beginnen. Abhängig von der ausgewählten SKU kann die Erstellung eines Gateways für virtuelle Netzwerke bis zu 45 Minuten dauern.

## <a name="vpndevice"></a>7. Konfigurieren des VPN-Geräts

Für Site-to-Site-Verbindungen mit einem lokalen Netzwerk ist ein VPN-Gerät erforderlich. In diesem Schritt konfigurieren Sie Ihr VPN-Gerät. Beim Konfigurieren des VPN-Geräts benötigen Sie Folgendes:

- Einen gemeinsam verwendeten Schlüssel. Dies ist derselbe gemeinsame Schlüssel, den Sie beim Erstellen Ihrer Site-to-Site-VPN-Verbindung angeben. In unseren Beispielen verwenden wir einen einfachen gemeinsamen Schlüssel. Es wird empfohlen, einen komplexeren Schlüssel zu generieren.
- Die öffentliche IP-Adresse Ihres Gateways für virtuelle Netzwerke. Sie können die öffentliche IP-Adresse mit dem Azure-Portal, mit PowerShell oder mit der CLI anzeigen.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. Erstellen der Verbindung
In diesem Schritt legen Sie den gemeinsam verwendeten Schlüssel fest und erstellen die Verbindung. Der von Ihnen festgelegte Schlüssel muss der gleiche Schlüssel sein, der in Ihrer VPN-Gerätekonfiguration verwendet wurde.

> [!NOTE]
> Derzeit ist dieser Schritt im Azure-Portal nicht verfügbar. Sie müssen die Dienstverwaltungsversion der Azure PowerShell-Cmdlets verwenden.
>

### <a name="step-1-connect-to-your-azure-account"></a>Schritt 1: Herstellen einer Verbindung mit Ihrem Azure-Konto

1. Öffnen Sie die PowerShell-Konsole mit erhöhten Rechten, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

   ```powershell
   Add-AzureAccount
   ```
2. Überprüfen Sie die Abonnements für das Konto.

   ```powershell
   Get-AzureSubscription
   ```
3. Wenn Sie über mehr als ein Abonnement verfügen, wählen Sie das Abonnement aus, das Sie verwenden möchten.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Schritt 2: Festlegen des gemeinsam verwendeten Schlüssels und Erstellen der Verbindung

Beim Verwenden von PowerShell und des klassischen Bereitstellungsmodells sind die Namen der Ressourcen im Portal in einigen Fällen nicht die Namen, die Azure in Bezug auf die Nutzung von PowerShell erwartet. Mit den folgenden Schritten können Sie die Netzwerkkonfigurationsdatei exportieren, um die genauen Werte für die Namen zu erhalten.

1. Erstellen Sie auf Ihrem Computer ein Verzeichnis, und exportieren Sie die Netzwerkkonfigurationsdatei in das Verzeichnis. In diesem Beispiel wird die Netzwerkkonfigurationsdatei in das Verzeichnis „C:\AzureNet“ exportiert.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Öffnen Sie die Netzwerkkonfigurationsdatei mit einem XML-Editor, und überprüfen Sie die Werte für „LocalNetworkSite name“ und „VirtualNetworkSite name“. Ändern Sie das Beispiel, um die erforderlichen Werte zu nutzen. Setzen Sie den Wert in einfache Anführungszeichen, wenn ein Name Leerzeichen enthält.

3. Legen Sie den gemeinsam verwendeten Schlüssel fest, und erstellen Sie die Verbindung. „-SharedKey“ ist ein Wert, den Sie generieren und angeben. Im Beispiel wurde „abc123“ verwendet, aber Sie können (und sollten) einen komplexeren Wert generieren und verwenden. Entscheidend ist Folgendes: Der Wert, den Sie hier angeben, muss dem Wert entsprechen, den Sie beim Konfigurieren Ihres VPN-Geräts angegeben haben.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   Wenn die Verbindung erstellt wird, lautet das Ergebnis: **Status: Erfolgreich** zurückgegeben werden.

## <a name="verify"></a>9. Überprüfen der Verbindung

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Sollten bei der Verbindungsherstellung Probleme auftreten, wechseln Sie über das Inhaltsverzeichnis auf der linken Seite zum Abschnitt **Problembehandlung**.

## <a name="reset"></a>Zurücksetzen einer VPN Gateway-Instanz

Das Zurücksetzen von Azure VPN Gateway-Instanzen ist nützlich, wenn die standortübergreifende VPN-Verbindung bei mindestens einem Site-to-Site-VPN-Tunnel unterbrochen ist. In diesem Fall funktionieren Ihre lokalen VPN-Geräte ordnungsgemäß, können jedoch keine IPsec-Tunnelverbindungen mit Azure VPN Gateway-Instanzen herstellen. Eine entsprechende Anleitung finden Sie unter [Zurücksetzen einer VPN Gateway-Instanz](vpn-gateway-resetgw-classic.md).

## <a name="changesku"></a>Ändern einer Gateway-SKU

Eine Anleitung zum Ändern einer Gateway-SKU finden Sie unter [Arbeiten mit SKUs für virtuelle Netzwerkgateways (alte SKUs)](vpn-gateway-about-SKUS-legacy.md).

## <a name="next-steps"></a>Nächste Schritte

* Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](https://docs.microsoft.com/azure/) .
* Weitere Informationen zur Tunnelerzwingung finden Sie unter [Informationen zur Tunnelerzwingung](vpn-gateway-about-forced-tunneling.md).
