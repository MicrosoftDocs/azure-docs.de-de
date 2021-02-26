---
title: 'Verbinden von klassischen virtuellen Netzwerken mit Azure Resource Manager-VNETs: Portal | Microsoft-Dokumentation'
description: Schritte zum Verbinden klassischer VNETs mit Resource Manager-VNETs mithilfe von VPN Gateway und des Portals
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 9d31bcaad01b9b762e57bd619d45c1f53ffb201e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376801"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Verbinden von virtuellen Netzwerken aus unterschiedlichen Bereitstellungsmodellen über das Portal

In diesem Artikel wird erläutert, wie Sie klassische VNets mit Resource Manager-VNets verbinden, damit die Ressourcen in den separaten Bereitstellungsmodellen miteinander kommunizieren können. Die Schritte in diesem Artikel werden in erster Linie im Azure-Portal ausgeführt. Sie können diese Konfiguration aber auch mithilfe von PowerShell durch Auswählen des Artikels in dieser Liste erstellen.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Das Verbinden eines klassischen VNet mit einem Resource Manager-VNet ähnelt dem Verbinden eines VNet mit einem lokalen Standort. Beide Verbindungstypen verwenden ein VPN-Gateway, um einen sicheren Tunnel mit IPsec/IKE bereitzustellen. Sie können auch eine Verbindung zwischen VNets in unterschiedlichen Abonnements und Regionen erstellen. Es ist auch möglich, VNets zu verbinden, die bereits über Verbindungen mit lokalen Netzwerken verfügen, sofern sie mit einem dynamischen oder routenbasierten Gateway konfiguriert wurden. Weitere Informationen zu VNet-zu-VNet-Verbindungen finden Sie am Ende dieses Artikels unter [Häufig gestellte Fragen zu VNet-zu-VNet-Verbindungen](#faq) . 

Falls Sie noch kein Gateway für virtuelle Netzwerke besitzen und keins erstellen möchten, können Sie stattdessen die VNETS mittels VNET-Peering verbinden. Beim VNET-Peering wird kein VPN-Gateway verwendet. Weitere Informationen finden Sie unter [VNet-Peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Voraussetzungen



* Bei den folgenden Schritten wird davon ausgegangen, dass beide VNETs bereits erstellt wurden. Wenn Sie diesen Artikel als Übung verwenden und Sie keine VNETs besitzen, finden Sie über die in den Schritten angegebenen Links Informationen zu ihrer Erstellung.
* Stellen Sie sicher, dass sich die Adressbereiche für die VNETs weder einander überlappen noch die Bereiche für andere Verbindungen, mit denen die Gateways verbunden werden können.
* Installieren Sie die neuesten PowerShell-Cmdlets für den Resource Manager und die Dienstverwaltung (klassisch). In diesem Artikel verwenden wir sowohl das Azure-Portal als auch PowerShell. Für die Verbindung zwischen dem klassischen VNET und dem Resource Manager-VNET ist PowerShell erforderlich. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/). 

### <a name="example-settings"></a><a name="values"></a>Beispieleinstellungen

Sie können diese Werte zum Erstellen einer Testumgebung verwenden oder zum besseren Verständnis der Beispiele in diesem Artikel heranziehen.

**Klassisches VNET**

VNET-Name = ClassicVNet <br>
Adressraum = 10.0.0.0/24 <br>
Subnetzname = Subnet-1 <br>
Subnetzadressbereich = 10.0.0.0/27 <br>
Abonnement = das gewünschte Abonnement <br>
Ressourcengruppe = ClassicRG <br>
Standort = USA, Westen <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokaler Standort = RMVNetLocal <br>

**Resource Manager-VNET**

VNET-Name = RMVNet <br>
Adressraum = 192.168.0.0/16 <br>
Ressourcengruppe = RG1 <br>
Standort = USA, Osten <br>
Subnetzname = Subnet-1 <br>
Adressbereich = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Name des Gateways für virtuelle Netzwerke = RMGateway <br>
Gatewaytyp = VPN <br>
VPN-Typ = Routenbasiert <br>
SKU = VpnGw1 <br>
Standort = USA, Osten <br>
Virtuelles Netzwerk = RMVNet <br> (Ordnen Sie das VPN-Gateway diesem VNET zu.) Erste IP-Konfiguration = rmgwpip <br> (Öffentliche IP-Adresse des Gateways.) Lokales Netzwerkgateway = ClassicVNetLocal <br>
Verbindungsname = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Verbindungsübersicht

Für diese Konfiguration erstellen Sie eine VPN-Gatewayverbindung über einen IPsec/IKE-VPN-Tunnel zwischen den virtuellen Netzwerken. Stellen Sie sicher, dass sich die VNET-Bereiche weder untereinander überlappen noch mit lokalen Netzwerken, mit denen sie eine Verbindung herstellen.

Die folgende Tabelle enthält ein Beispiel dafür, wie die Beispiel-VNETs und lokalen Standorte definiert werden:

| Virtual Network | Adressraum | Region | Verbindung mit dem lokalen Netzwerkstandort |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA (Westen) | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Abschnitt 1 – Konfigurieren der Einstellungen für klassische VNETs

In diesem Abschnitt erstellen Sie das klassische VNET, das lokale Netzwerk (den lokalen Standort) und das Gateway des virtuellen Netzwerks. Die Screenshots dienen lediglich zur Veranschaulichung. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen oder die [Beispielwerte](#values) verwenden.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"></a>Erstellen eines klassischen VNET

Wenn Sie nicht über ein klassisches VNET verfügen und diese Schritte als Übung ausführen, können Sie mithilfe der Informationen [dieses Artikels](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) und den oben genannten [Beispieleinstellungswerten](#values) ein VNET erstellen.

Wenn Sie bereits ein VNET mit einem VPN-Gateway besitzen, stellen Sie sicher, dass das Gateway dynamisch ist. Wenn es statisch ist, müssen Sie das VPN-Gateway zunächst löschen, bevor Sie mit dem [Konfigurieren des lokalen Standorts](#local) fortfahren.

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com), und melden Sie sich mit Ihrem Azure-Konto an.
2. Klicken Sie auf **+ Ressource erstellen**, um die Seite „Neu“ zu öffnen.
3. Geben Sie im Feld „Marketplace durchsuchen“ die Zeichenfolge „Virtuelles Netzwerk“ ein. Wenn Sie stattdessen „Netzwerk“ -> „Virtuelles Netzwerk“ auswählen, wird die Option für das Erstellen eines klassischen virtuellen Netzwerks nicht angezeigt.
4. Klicken Sie in der zurückgegebenen Liste auf „Virtuelles Netzwerk“, um die Seite „Virtuelles Netzwerk“ zu öffnen. 
5. Wählen Sie auf der Seite „Virtuelles Netzwerk“ die Option „Klassisch“ aus, um ein klassisches virtuelles Netzwerk zu erstellen. Wenn Sie hier die Standardeinstellung übernehmen, erhalten Sie stattdessen ein Resource Manager-VNET.

### <a name="2-configure-the-local-site"></a>2. <a name="local"></a>Konfigurieren des lokalen Standorts

1. Navigieren Sie zu **Alle Ressourcen** und suchen Sie in der Liste nach **ClassicVNet**.
2. Klicken Sie im Abschnitt **Einstellungen** des Menüs auf **Gateway**, und klicken Sie dann auf das Banner, um ein Gateway zu erstellen.
  ![Konfigurieren eines VPN-Gateways](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Konfigurieren eines VPN-Gateways")
3. Wählen Sie auf die Seite **Neue VPN-Verbindung** unter **Verbindungstyp** die Option **Standort-zu-Standort** aus.
4. Klicken Sie unter **Lokaler Standort** auf **Erforderliche Einstellungen konfigurieren**. Daraufhin wird die Seite **Lokaler Standort** geöffnet.
5. Erstellen Sie auf die Seite **Lokaler Standort** einen Namen, um auf das Ressourcen-Manager-VNET zu verweisen. Beispiel: „RMVNetLocal“.
6. Wenn das VPN-Gateway für das Resource Manager-VNET bereits eine öffentliche IP-Adresse aufweist, verwenden Sie diesen Wert für das Feld **IP-Adresse des VPN-Gateways**. Wenn Sie diese Schritte als Übung ausführen oder Sie noch kein Gateway für virtuelle Netzwerke für Ihr Resource Manager-VNET besitzen, können Sie eine Platzhalter-IP-Adresse erstellen. Stellen Sie sicher, dass die Platzhalter-IP-Adresse ein gültiges Format aufweist. Später ersetzen Sie die Platzhalter-IP-Adresse durch die öffentliche IP-Adresse des Gateways für das virtuelle Resource Manager-Netzwerk.
7. Verwenden Sie für **Clientadressraum** die [Werte](#connectoverview) für die IP-Adressräume des virtuellen Netzwerks für das Resource Manager-VNET. Diese Einstellung wird verwendet, um die Adressräume für die Weiterleitung an das virtuelle Resource Manager-Netzwerk anzugeben. Im Beispiel verwenden wir 192.168.0.0/16, den Adressbereich für das RMVNet.
8. Klicken Sie auf **OK**, um die Werte zu speichern und zur Seite **Neue VPN-Verbindung** zurückzukehren.

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. Erstellen des Gateways für das lokale Netzwerk

1. Aktivieren Sie auf der Seite **Neue VPN-Verbindung** das Kontrollkästchen **Gateway sofort erstellen**.
2. Klicken Sie auf **Optionale Gatewaykonfiguration**, um die Seite **Gatewaykonfiguration** zu öffnen.

   ![Öffnen der Seite „Gatewaykonfiguration“](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Öffnen der Seite „Gatewaykonfiguration“")
3. Klicken Sie auf **Subnetz – Erforderliche Einstellungen konfigurieren**, um die Seite **Subnetz hinzufügen** zu öffnen. Der **Name** ist bereits mit dem erforderlichen Wert konfiguriert: **GatewaySubnet**.
4. **Adressbereich** bezieht sich auf den Bereich für das Gatewaysubnetz. Obwohl Sie ein Gatewaysubnetz mit einem /29-Adressbereich (3 Adressen) erstellen können, wird empfohlen, ein Gatewaysubnetz mit einer höheren Anzahl von IP-Adressen zu erstellen. Dadurch werden zukünftige Konfigurationen unterstützt, die möglicherweise eine höhere Anzahl von verfügbaren IP-Adressen erfordern. Verwenden Sie möglichst /27 oder /28. Sie können die [Beispielwerte](#values) nutzen, wenn Sie diese Schritte als Übung verwenden. In diesem Beispiel verwenden wir 10.0.0.32/28. Klicken Sie auf **OK**, um das Gatewaysubnetz zu erstellen.
5. Auf der Seite **Gatewaykonfiguration** bezieht sich **Größe** auf die Gateway-SKU. Wählen Sie die Gateway-SKU für das VPN-Gateway aus.
6. Vergewissern Sie sich, dass für **Routingtyp** der Wert **Dynamisch** festgelegt wurde, und klicken Sie auf **OK**, um zur Seite **Neue VPN-Verbindung** zurückzukehren.
7. Klicken Sie auf der Seite **Neue VPN-Verbindung** auf **OK**, um mit dem Erstellen des VPN-Gateways zu beginnen. Die Erstellung eines VPN-Gateways kann bis zu 45 Minuten dauern.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Kopieren der öffentlichen IP-Adresse für das Gateway für virtuelle Netzwerke

Nachdem das Gateway für das virtuelle Netzwerk erstellt wurde, können Sie die Gateway-IP-Adresse anzeigen. 

1. Navigieren Sie zum klassischen VNET, und klicken Sie auf **Übersicht**.
2. Klicken Sie auf **VPN-Verbindungen**, um die Seite „VPN-Verbindungen“ zu öffnen. Auf der Seite „VPN-Verbindungen“ können Sie die öffentliche IP-Adresse anzeigen. Dies ist die öffentliche IP-Adresse, die dem Gateway Ihres virtuellen Netzwerks zugewiesen ist. Notieren Sie sich die IP-Adresse. Sie verwenden sie in späteren Schritten bei der Arbeit mit den Konfigurationseinstellungen für das Gateway für das lokale Resource Manager-Netzwerk. 
3. Sie können den Status Ihrer Gatewayverbindungen anzeigen. Beachten Sie, dass der lokale Netzwerkstandort als „Verbindung wird hergestellt“ aufgeführt wird. Der Status ändert sich nach dem Erstellen der Verbindungen. Wenn Sie den Status hinreichend angezeigt haben, können Sie diese Seite schließen.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Abschnitt 2 – Konfigurieren der Einstellungen für das Resource Manager-VNET

In diesem Abschnitt erstellen Sie das Gateway für das virtuelle Netzwerk und das lokale Netzwerkgateway für Ihr Resource Manager-VNET. Die Screenshots dienen lediglich zur Veranschaulichung. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen oder die [Beispielwerte](#values) verwenden.

### <a name="1-create-a-virtual-network"></a>1. Erstellen eines virtuellen Netzwerks

**Beispielwerte:**

* VNET-Name = RMVNet <br>
* Adressraum = 192.168.0.0/16 <br>
* Ressourcengruppe = RG1 <br>
* Standort = USA, Osten <br>
* Subnetzname = Subnet-1 <br>
* Adressbereich = 192.168.1.0/24 <br>

Wenn Sie nicht über ein Resource Manager-VNET verfügen und diese Schritte als Übung ausführen, erstellen Sie mit den Schritten in [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md) und den Beispielwerten ein virtuelles Netzwerk.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Erstellen eines Gateways für das virtuelle Netzwerk

In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Beispielwerte:**

* Name des Gateways für virtuelle Netzwerke = RMGateway <br>
* Gatewaytyp = VPN <br>
* VPN-Typ = Routenbasiert <br>
* SKU = VpnGw1 <br>
* Standort = USA, Osten <br>
* Virtuelles Netzwerk = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Erste IP-Konfiguration = rmgwpip <br>

[!INCLUDE [Add gateway](../../includes/vpn-gateway-add-gw-rm-portal-empty.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Erstellen eines Gateways für das lokale Netzwerk

**Beispielwerte:** Lokales Netzwerkgateway = ClassicVNetLocal

| Virtual Network | Adressraum | Region | Verbindung mit dem lokalen Netzwerkstandort |Öffentliche IP-Adresse des Gateways|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA (Westen) | RMVNetLocal (192.168.0.0/16) |Die öffentliche IP-Adresse, die dem Gateway ClassicVNet zugewiesen ist|
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |Die öffentliche IP-Adresse, die dem Gateway RMVNet zugewiesen ist|

Das lokale Netzwerkgateway legt den Adressbereich und die öffentliche IP-Adresse fest, die Ihrem klassischen VNET und dem Gateway des zugehörigen virtuellen Netzwerks zugeordnet sind. Wenn Sie diese Schritte als Übung durchführen, sehen Sie bei den Beispielwerten nach.

[!INCLUDE [Add local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-ip-empty.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Abschnitt 3 – Ändern der Einstellungen für den lokalen Standort des klassischen VNET

In diesem Abschnitt ersetzen Sie die Platzhalter-IP-Adresse, die Sie beim Festlegen der Einstellungen für den lokalen Standort verwendet haben, durch die IP-Adresse des Resource Manager-VPN-Gateways. In diesem Abschnitt werden die klassischen PowerShell-Cmdlets (SM) verwendet.

1. Navigieren Sie im Azure-Portal zum klassischen virtuellen Netzwerk.
2. Klicken Sie auf der Seite für Ihr virtuelles Netzwerk auf **Übersicht**.
3. Klicken Sie unter **VPN-Verbindungen** auf den Namen des lokalen Standorts in der Abbildung.

   ![VPN-connections](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN Connections")
4. Klicken Sie auf der Seite **Standort-zu-Standort-VPN-Verbindungen** auf den Namen des Standorts.

   ![Site-name](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Name des lokalen Standorts")
5. Klicken Sie auf der Seite „Verbindung“ für den lokalen Standort auf den Namen des lokalen Standorts, um die Seite **Lokaler Standort** zu öffnen.

   ![Open-local-site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Öffnen des lokalen Standorts")
6. Ersetzen Sie auf der Seite **Lokaler Standort** die **IP-Adresse des VPN-Gateways** durch die IP-Adresse des Ressourcen-Manager-Gateways.

   ![Gateway-ip-address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Gateway-IP-Adresse")
7. Klicken Sie auf **OK**, um die IP-Adresse zu aktualisieren.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Abschnitt 4 – Erstellen der Verbindung zwischen dem Resource Manager-VNET und dem klassischen VNET

In den folgenden Schritten konfigurieren Sie mithilfe des Azure-Portals die Verbindung zwischen dem Resource Manager-VNET und dem klassischen VNET.

1. Suchen Sie unter **Alle Ressourcen** nach dem lokalen Netzwerkgateway. In unserem Beispiel handelt es sich beim lokalen Netzwerkgateway um **ClassicVNetLocal**.
2. Klicken Sie auf **Konfiguration** und stellen Sie sicher, dass es sich beim IP-Adresswert um das VPN-Gateway für das klassische VNET handelt. Aktualisieren Sie diesen bei Bedarf und klicken Sie dann auf **Speichern**. Schließen Sie die Seite.
3. Klicken Sie unter **Alle Ressourcen** auf das lokale Netzwerkgateway.
4. Klicken Sie auf **Verbindungen**, um die Seite „Verbindungen“ zu öffnen.
5. Klicken Sie auf der Seite **Verbindungen** auf **+** , um eine Verbindung hinzuzufügen.
6. Geben Sie auf der Seite **Verbindung hinzufügen** einen Namen für die Verbindung ein. Beispiel: „RMtoClassic“.
7. **Standort-zu-Standort-** ist bereits auf dieser Seite ausgewählt.
8. Wählen Sie das Gateway des virtuellen Netzwerks aus, das Sie diesem Standort zuweisen möchten.
9. Erstellen Sie einen **gemeinsam verwendeten Schlüssel**. Dieser Schlüssel wird auch bei der Verbindung verwendet, die Sie zwischen dem klassischen VNET und dem Resource Manager-VNET erstellen. Sie können den Schlüssel generieren oder einen solchen erstellen. In unserem Beispiel wird „abc123“ verwendet, aber Sie können (und sollten) einen komplexeren Wert verwenden.
10. Klicken Sie auf **OK**, um die Verbindung zu erstellen.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Abschnitt 5 – Erstellen der Verbindung zwischen dem klassischen VNET und dem Resource Manager-VNET

In den folgenden Schritten konfigurieren Sie die Verbindung zwischen dem klassischen VNET und dem Resource Manager-VNET. Für diese Schritte ist PowerShell erforderlich. Diese Verbindung kann nicht im Portal erstellt werden. Stellen Sie sicher, dass Sie sowohl die SM-PowerShell-Cmdlets (klassisch) als auch die RM-PowerShell-Cmdlets (Resource Manager) heruntergeladen und installiert haben.

### <a name="1-connect-to-your-azure-account"></a>1. Herstellen einer Verbindung mit Ihrem Azure-Konto

Öffnen Sie die PowerShell-Konsole mit erhöhten Rechten, und melden Sie sich bei Ihrem Azure-Konto an. Nachdem Sie sich angemeldet haben, werden Ihre Kontoeinstellungen heruntergeladen, sodass sie in Azure PowerShell verfügbar sind. Das folgende Cmdlet fordert Sie zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto für das Resource Manager-Bereitstellungsmodell auf:

```powershell
Connect-AzAccount
```

Rufen Sie eine Liste Ihrer Azure-Abonnements ab.

```powershell
Get-AzSubscription
```

Wenn Sie über mehrere Abonnements verfügen, geben Sie das Abonnement an, das Sie verwenden möchten.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Melden Sie sich anschließend an, um die klassischen PowerShell-Cmdlets zu verwenden (Dienstverwaltung). Verwenden Sie den folgenden Befehl, um Ihr Azure-Konto für das klassische Bereitstellungsmodell hinzuzufügen:

```powershell
Add-AzureAccount
```

Rufen Sie eine Liste Ihrer Abonnements ab. Dieser Schritt kann je nach Azure-Modulinstallation beim Hinzufügen der Dienstverwaltungs-Cmdlets notwendig sein.

```powershell
Get-AzureSubscription
```

Wenn Sie über mehrere Abonnements verfügen, geben Sie das Abonnement an, das Sie verwenden möchten.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Anzeigen der Werte der Netzwerkkonfigurationsdatei

Wenn Sie im Azure-Portal ein VNet erstellen, ist der vollständige von Azure verwendet Name im Azure-Portal nicht zu sehen. Beispielsweise kann ein VNET, das im Azure-Portal als „ClassicVNet“ angezeigt wird, in der Netzwerkkonfigurationsdatei einen viel längeren Namen haben. Der Name sieht möglicherweise wie folgt aus: 'Gruppe ClassicRG ClassicVNet'. In den folgenden Schritten laden Sie die Netzwerkkonfigurationsdatei herunter und zeigen die Werte an.

Erstellen Sie auf Ihrem Computer ein Verzeichnis, und exportieren Sie die Netzwerkkonfigurationsdatei in das Verzeichnis. In diesem Beispiel wird die Netzwerkkonfigurationsdatei in das Verzeichnis „C:\AzureNet“ exportiert.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öffnen Sie die Datei in einem Text-Editor, und zeigen Sie den Namen für Ihr klassisches VNet an. Verwenden Sie zum Ausführen von PowerShell-Cmdlets die Namen in der Netzwerkkonfigurationsdatei.

- VNET-Namen werden unter **VirtualNetworkSite name =** aufgelistet.
- Standortnamen werden unter **LocalNetworkSite name=** aufgelistet.

### <a name="3-create-the-connection"></a>3. Erstellen der Verbindung

Legen Sie den gemeinsam verwendeten Schlüssel fest und erstellen Sie die Verbindung zwischen dem klassischen VNET und dem Resource Manager-VNET. Der gemeinsam verwendete Schlüssel kann nicht im Portal festgelegt werden. Stellen Sie sicher, dass Sie die folgenden Schritte ausführen, während Sie zum Verwenden der klassischen Version der PowerShell-Cmdlets angemeldet sind. Verwenden Sie hierzu **Add-AzureAccount**. Andernfalls können Sie „-AzureVNetGatewayKey“ nicht festlegen.

- In diesem Beispiel ist **-VNetName** der Name des klassischen VNET, der in Ihrer Netzwerkkonfigurationsdatei enthalten ist. 
- **-LocalNetworkSiteName** ist der Name, den Sie für den lokalen Standort festgelegt haben und der in Ihrer Netzwerkkonfigurationsdatei enthalten ist.
- **-SharedKey** ist ein Wert, den Sie generieren und angeben. In diesem Beispiel wurde *abc123* verwendet, aber Sie können etwas Komplexeres generieren. Entscheidend ist Folgendes: Der Wert, den Sie hier angeben, muss demselben Wert entsprechen, den Sie beim Erstellen der Verbindung zwischen Ihrem Resource Manager-VNET und dem klassischen VNET angegeben haben.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Abschnitt 6 – Überprüfen der Verbindungen

Sie können die Verbindung im Azure-Portal oder mit PowerShell überprüfen. Beim Überprüfen müssen Sie möglicherweise eine Minute warten, während die Verbindung erstellt wird. Wenn eine Verbindung erfolgreich erstellt wurde, wechselt der Verbindungsstatus von „Verbindung wird hergestellt“ zu „Verbunden“.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>So überprüfen Sie die Verbindung des klassischen VNet mit dem Resource Manager-VNet

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>So überprüfen Sie die Verbindung des Resource Manager-VNet mit dem klassischen VNet

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Häufig gestellte Fragen zu VNet-zu-VNet-Verbindungen

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
