---
title: 'Erstellen einer Verbindung zwischen VNETs: klassisch: Azure-Portal | Microsoft-Dokumentation'
description: Stellen Sie mithilfe von PowerShell und dem Azure-Portal Verbindungen zwischen virtuellen Azure-Netzwerken her.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 48377f981e4d2c9ab480a1a734e6207a0246712a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60407910"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurieren einer VNet-zu-VNet-Verbindung (klassisch)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Dieser Artikel hilft Ihnen beim Erstellen einer VPN-Gatewayverbindung zwischen virtuellen Netzwerken. Die virtuellen Netzwerke können sich in derselben oder in unterschiedlichen Regionen befinden und aus demselben oder unterschiedlichen Abonnements stammen. Die Schritte in diesem Artikel gelten für das klassische Bereitstellungsmodell und Azure-Portal. Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder -modell erstellen. Wählen Sie hierzu in der folgenden Liste eine andere Option:

> [!div class="op_single_selector"]
> * [Azure-Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-Befehlszeilenschnittstelle](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinden von verschiedenen Bereitstellungsmodellen – Azure-Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinden von verschiedenen Bereitstellungsmodellen – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet-zu-VNet-Konnektivitätsdiagramm](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-vnet-to-vnet-connections"></a>Über VNet-zu-VNet-Verbindungen

Beim klassischen Bereitstellungsmodell ähnelt die Verbindung eines virtuellen Netzwerks mit einem anderen virtuellen Netzwerk (VNet-zu-VNet) mittels eines VPN-Gateways der Verbindung eines virtuellen Netzwerks mit einem lokalen Standort. Beide Verbindungstypen verwenden ein VPN-Gateway, um einen sicheren Tunnel mit IPsec/IKE bereitzustellen.

Die VNets, die Sie verbinden, können sich in verschiedenen Abonnements und Regionen befinden. Sie können die VNet-zu-VNet-Kommunikation mit Konfigurationen für mehrere Standorte kombinieren. Auf diese Weise können Sie Netzwerktopologien einrichten, die standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren.

![VNet-zu-VNet-Verbindungen](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Gründe für Verbindungen zwischen virtuellen Netzwerken

Aus den folgenden Gründen empfiehlt sich das Herstellen von Verbindungen zwischen virtuellen Netzwerken:

* **Regionsübergreifende Georedundanz und Geopräsenz**

  * Sie können Ihre eigene Georeplikation oder -synchronisierung mit sicheren Verbindungen einrichten, ohne Endpunkte mit Internetzugriff verwenden zu müssen.
  * Mit dem Azure Load Balancer und Clustertechnologie von Microsoft oder Drittanbietern können Sie Workloads mit hoher Verfügbarkeit mit Georedundanz über mehrere Azure-Regionen hinweg einrichten. Ein wichtiges Beispiel ist die Einrichtung von SQL Always On mit Verfügbarkeitsgruppen, die sich über mehrere Azure-Regionen erstrecken.
* **Regionale Anwendungen mit mehreren Ebenen mit starker Isolierungsgrenze**

  * Innerhalb der gleichen Region können Sie Anwendungen mit mehreren Ebenen mit mehreren verbundenen VNets einrichten, die starke Isolierung und sichere Kommunikation zwischen den Ebenen aufweisen.
* **Abonnementübergreifende Kommunikation zwischen Organisationen in Azure**

  * Wenn Sie über mehrere Azure-Abonnements verfügen, können Sie Workloads aus verschiedenen Abonnements sicher zwischen virtuellen Netzwerken verbinden.
  * Für Unternehmen und Dienstanbieter können Sie die organisationsübergreifende Kommunikation mit sicherer VPN-Technologie in Azure aktivieren.

Weitere Informationen zu VNet-zu-VNet-Verbindungen finden Sie am Ende dieses Artikels unter [Informationen zu VNet-zu-VNet-Verbindungen](#faq).

### <a name="before-you-begin"></a>Voraussetzungen

Ehe Sie diese Übung beginnen, laden Sie die aktuelle Version der PowerShell-Cmdlets der Azure-Dienstverwaltung herunter, und installieren Sie sie. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview). Wir verwenden für die meisten Schritte das Portal. Sie müssen jedoch PowerShell verwenden, um die Verbindungen zwischen den VNets herzustellen. Sie können die Verbindungen nicht im Azure-Portal erstellen.

## <a name="plan"></a>Schritt 1: Planen der IP-Adressbereiche

Eine wichtige Entscheidung betrifft die Bereiche, die Sie zum Konfigurieren Ihrer virtuellen Netzwerke verwenden. Bei dieser Konfiguration müssen Sie sicherstellen, dass sich die VNet-Bereiche weder untereinander überlappen, noch mit lokalen Netzwerken, mit denen sie eine Verbindung herstellen.

Die folgende Tabelle zeigt ein Beispiel zur Definition von VNets. Verwenden Sie die aufgeführten Bereiche nur als Richtlinie. Notieren Sie sich die Bereiche für Ihre virtuellen Netzwerke. Sie benötigen diese Informationen in den späteren Schritten.

**Beispiel**

| Virtual Network | Adressraum | Region | Verbindung mit dem lokalen Netzwerkstandort |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA (Ost) |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA (Westen) |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Schritt 2 – Erstellen der virtuellen Netzwerke

Erstellen Sie zwei virtuelle Netzwerke im [Azure-Portal](https://portal.azure.com). Informationen zu den Schritten, um klassische virtuelle Netzwerke zu erstellen, finden Sie unter [Erstellen eines klassischen virtuellen Netzwerks](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Wenn Sie das Portal verwenden, um ein klassisches virtuelles Netzwerk zu erstellen, müssen Sie mit den folgenden Schritten zur Seite „Virtuelles Netzwerk“ navigieren, da sonst die Option für das Erstellen eines klassischen virtuellen Netzwerks nicht angezeigt wird:

1. Klicken Sie auf „+“, um die Seite „Neu“ zu öffnen.
2. Geben Sie im Feld „Marketplace durchsuchen“ die Zeichenfolge „Virtuelles Netzwerk“ ein. Wenn Sie stattdessen „Netzwerk“ -> „Virtuelles Netzwerk“ auswählen, wird die Option für das Erstellen eines klassischen virtuellen Netzwerks nicht angezeigt.
3. Klicken Sie in der zurückgegebenen Liste auf „Virtuelles Netzwerk“, um die Seite „Virtuelles Netzwerk“ zu öffnen. 
4. Wählen Sie auf der Seite „Virtuelles Netzwerk“ die Option „Klassisch“ aus, um ein klassisches virtuelles Netzwerk zu erstellen. 

Sie können die folgenden Beispielwerte nutzen, wenn Sie diesen Artikel als Übung verwenden:

**Werte für TestVNet1**

Name: TestVNet1<br>
Adressraum: 10.11.0.0/16, 10.12.0.0/16 (optional)<br>
Subnetzname: Standard<br>
Subnetzadressbereich: 10.11.0.1/24<br>
Ressourcengruppe: ClassicRG<br>
Standort: USA (Ost)<br>
Gatewaysubnetz: 10.11.1.0/27

**Werte für TestVNet4**

Name: TestVNet4<br>
Adressraum: 10.41.0.0/16, 10.42.0.0/16 (optional)<br>
Subnetzname: Standard<br>
Subnetzadressbereich: 10.41.0.1/24<br>
Ressourcengruppe: ClassicRG<br>
Standort: USA (Westen)<br>
Gatewaysubnetz: 10.41.1.0/27

**Berücksichtigen Sie bei der Erstellung der VNETs folgende Einstellungen:**

* **Adressräume von Virtual Network** – Geben Sie auf der Seite „Adressräume von Virtual Network“ den Adressbereich an, der für das virtuelle Netzwerk verwendet werden soll. Dies sind die dynamischen IP-Adressen, die den VMs und anderen Rolleninstanzen zugewiesen werden, die Sie für dieses virtuelle Netzwerk bereitstellen.<br>Die Adressräume, die Sie auswählen, dürfen nicht die Adressräume von anderen VNETs oder lokalen Standorten überlappen, zu denen dieses VNET eine Verbindung herstellt.

* **Standort**: Wenn Sie ein virtuelles Netzwerk erstellen, ordnen Sie dieses einem Azure-Standort (einer Region) zu. Wenn Sie z. B. wünschen, dass Ihre virtuellen Computer, die Sie für Ihr virtuelles Netzwerk bereitgestellt haben, physisch in "USA West" vorhanden sind, wählen Sie diesen Standort aus. Sie können den Ihrem virtuellen Netzwerk zugeordnete Standort nach dem Erstellen nicht mehr ändern.

**Nach der Erstellung der VNETs können Sie folgende Einstellungen hinzufügen:**

* **Adressraum** – Für diese Konfiguration ist kein zusätzlicher Adressraum erforderlich, Sie können jedoch nach der Erstellung des VNET zusätzliche Adressräume hinzufügen.

* **Subnetze** – Für diese Konfiguration sind keine zusätzlichen Subnetze erforderlich, aber eventuell sollten Sie Ihre VMs in einem Subnetz, das von Ihren anderen Rolleninstanzen getrennt ist, verwalten.

* **DNS-Server** – Geben Sie den DNS-Servernamen und die IP-Adresse ein. Mit dieser Einstellung wird kein DNS-Server erstellt. Sie bietet die Möglichkeit, den DNS-Server anzugeben, den Sie zur Namensauflösung für dieses virtuelle Netzwerk verwenden möchten.

In diesem Abschnitt konfigurieren Sie den Verbindungstyp sowie den lokalen Standort und erstellen das Gateway.

## <a name="localsite"></a>Schritt 3 – Konfigurieren des lokalen Standorts

Azure verwendet die für die einzelnen lokalen Netzwerkstandorte angegebenen Einstellungen, um zu bestimmen, wie Datenverkehr zwischen den VNets weitergeleitet wird. Jedes VNet muss auf das entsprechende lokale Netzwerk verweisen, an das Sie Datenverkehr weiterleiten möchten. Sie legen den Namen fest, den Sie zum Verweisen auf die einzelnen lokalen Netzwerkstandorte verwenden möchten. Es wird empfohlen, einen aussagekräftigen Namen zu verwenden.

Beispielsweise stellt TestVNet1 eine Verbindung mit einem lokalen Netzwerkstandort her, den Sie mit dem Namen „VNet4Local“ erstellt haben. Die Einstellungen für VNet4Local enthalten die Adresspräfixe für TestVNet4.

Der lokale Standort für jedes VNET ist das andere VNET. Die folgenden Beispielwerte werden für diese Konfiguration verwendet:

| Virtual Network | Adressraum | Region | Verbindung mit dem lokalen Netzwerkstandort |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA (Ost) |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA (Westen) |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Suchen Sie im Azure-Portal nach TestVNet1. Klicken Sie im Abschnitt **VPN-Verbindungen** der Seite auf **Gateway**.

    ![Kein Gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Wählen Sie auf der Seite **Neue VPN-Verbindung** die Option **Standort-zu-Standort**.
3. Klicken Sie auf **Lokaler Standort**, um die Seite „Lokaler Standort“ zu öffnen und die Einstellungen zu konfigurieren.
4. Vergeben Sie auf der Seite **Lokaler Standort** einen Namen für Ihren lokalen Standort. In unserem Beispiel benennen wir den lokalen Standort in „VNet4Local“.
5. Für **IP-Adresse des VPN-Gateways** können Sie eine beliebige IP-Adresse verwenden; sie muss lediglich ein gültiges Format aufweisen. Normalerweise verwenden Sie die tatsächliche externe IP-Adresse für ein VPN-Gerät. Verwenden Sie für klassische VNet-zu-VNet-Konfigurationen die öffentliche IP-Adresse, die dem Gateway für Ihr VNET zugewiesen ist. Da Sie das Gateway für virtuelle Netzwerke noch nicht erstellt haben, geben Sie eine beliebige gültige öffentliche IP-Adresse als Platzhalter an.<br>Lassen Sie dieses Feld nicht leer, es ist für diese Konfiguration nicht optional. In einem späteren Schritt kehren Sie zu diesen Einstellungen zurück und konfigurieren sie dann mit den entsprechenden IP-Adressen des Gateways für virtuelle Netzwerke, nachdem Azure diese generiert hat.
6. Verwenden Sie für **Clientadressraum** den Adressraum des anderen VNET. Weitere Informationen finden Sie in Ihrem Planungsbeispiel. Klicken Sie auf **OK**, um Ihre Einstellungen zu speichern und zur Seite **Neue VPN-Verbindung** zurückzukehren.

    ![Lokaler Standort](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Schritt 4 – Erstellen des Gateways für virtuelle Netzwerke

Jedes virtuelle Netzwerk muss über ein Gateway für virtuelle Netzwerke verfügen. Das Gateway für virtuelle Netzwerke leitet den Datenverkehr weiter und verschlüsselt ihn.

1. Aktivieren Sie auf der Seite **Neue VPN-Verbindung** das Kontrollkästchen **Gateway sofort erstellen**.
2. Klicken Sie auf **Subnetz, Größe und Routingtyp**. Klicken Sie auf der Seite **Gatewaykonfiguration** auf **Subnetz**.
3. Der Name des Gatewaysubnetzes wird automatisch mit dem erforderlichen Namen „GatewaySubnet“ ausgefüllt. Der **Adressbereich** enthält die IP-Adressen, die den VPN-Gatewaydiensten zugeordnet sind. Einige Konfigurationen ermöglichen /29-Gatewaysubnetze, es wird jedoch empfohlen, /28- oder /27-Subnetze zu verwenden, um zukünftige Konfigurationen zu unterstützen, die möglicherweise weitere IP-Adressen für die Gatewaydienste erfordern. In diesen Beispieleinstellungen wird 10.11.1.0/27 verwendet. Passen Sie den Adressraum an und klicken Sie dann auf **OK**.
4. Konfigurieren Sie die **Gatewaygröße**. Diese Einstellung bezieht sich auf die [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurieren Sie den **Routingtyp**. Für diese Konfiguration muss der Routingtyp **Dynamisch** ausgewählt sein. Sie können den Routingtyp später nicht ändern, es sei denn, Sie löschen das Gateway und erstellen ein neues.
6. Klicken Sie auf **OK**.
7. Klicken Sie auf der Seite **Neue VPN-Verbindung** auf **OK**, um mit dem Erstellen des Gateways für virtuelle Netzwerke zu beginnen. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

## <a name="vnet4settings"></a>Schritt 5 – Konfigurieren von TestVNet4-Einstellungen

Wiederholen Sie die Schritte zum [Erstellen eines lokales Standorts](#localsite) und [Erstellen des Gateways für virtuelle Netzwerke](#gw), um TestVNet4 zu konfigurieren. Ersetzen Sie die Werte bei Bedarf. Wenn Sie diese Schritte als Übung durchführen, verwenden Sie die [Beispielwerte](#vnetvalues).

## <a name="updatelocal"></a>Schritt 6 – Aktualisieren der lokalen Standorte

Nachdem die Gateways für Ihre virtuellen Netzwerke für beide VNETs erstellt wurden, müssen Sie die Werte für **IP-Adresse des VPN-Gateways** der lokalen Standorte anpassen.

|VNet-Name|Verbundener Standort|Gateway-IP-Adresse|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|IP-Adresse des VPN-Gateways für TestVNet4|
|TestVNet4|VNet1Local|IP-Adresse des VPN-Gateways für TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Teil 1 – Abrufen der öffentlichen IP-Adresse des Gateways für virtuelle Netzwerke

1. Suchen Sie im Azure-Portal nach Ihrem virtuellen Netzwerk.
2. Klicken Sie darauf, um die Seite **Übersicht** des VNET zu öffnen. Sie können auf der Seite unter **VPN-Verbindungen** die IP-Adresse Ihres Gateways für virtuelle Netzwerke sehen.

   ![Öffentliche IP-Adresse](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Kopieren Sie die IP-Adresse. Sie werden sie im nächsten Abschnitt verwenden.
4. Wiederholen Sie diese Schritte für TestVNet4.

### <a name="part-2---modify-the-local-sites"></a>Teil 2 – Ändern der lokalen Standorte

1. Suchen Sie im Azure-Portal nach Ihrem virtuellen Netzwerk.
2. Klicken Sie auf der Seite **Übersicht** des VNET auf den lokalen Standort.

   ![Erstellter lokaler Standort](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Klicken Sie auf der Seite **Standort-zu-Standort-VPN-Verbindungen** auf den Namen des lokalen Standorts, den Sie ändern möchten.

   ![Öffnen des lokalen Standorts](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klicken Sie auf **Lokaler Standort**, der geändert werden soll.

   ![Ändern des Standorts](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Aktualisieren Sie die **IP-Adresse des VPN-Gateways** und klicken Sie auf **OK**, um die Einstellungen zu speichern.

   ![Gateway-IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Schließen Sie die anderen Seiten.
7. Wiederholen Sie diese Schritte für TestVNet4.

## <a name="getvalues"></a>Schritt 7 – Abrufen von Werten aus der Netzwerkkonfigurationsdatei

Beim Erstellen von klassischen VNETs im Azure-Portal ist der angezeigte Name nicht der vollständige Name, den Sie für PowerShell verwenden. Beispielsweise kann ein VNET, das im Azure-Portal als **TestVNet1** angezeigt wird, in der Netzwerkkonfigurationsdatei einen viel längeren Namen haben. Der Name sieht möglicherweise wie folgt aus: **Gruppe ClassicRG TestVNet1**. Bei der Erstellung Ihrer Verbindungen ist es wichtig, dass Sie die in der Netzwerkkonfigurationsdatei angezeigten Werte verwenden.

In den folgenden Schritten stellen Sie eine Verbindung zu Ihrem Azure-Konto her. Zudem laden Sie die Netzwerkkonfigurationsdatei herunter und zeigen diese an, um die für Ihre Verbindungen erforderlichen Werte abzurufen.

1. Laden Sie die aktuelle Version der PowerShell-Cmdlets der Azure-Dienstverwaltung herunter, und installieren Sie sie. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

2. Öffnen Sie die PowerShell-Konsole mit erhöhten Rechten, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

   ```powershell
   Connect-AzAccount
   ```

   Überprüfen Sie die Abonnements für das Konto.

   ```powershell
   Get-AzSubscription
   ```

   Wenn Sie über mehr als ein Abonnement verfügen, wählen Sie das Abonnement aus, das Sie verwenden möchten.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

   Verwenden Sie als nächstes das folgende Cmdlet, um PowerShell Ihr Azure-Abonnement für das klassische Bereitstellungsmodell hinzuzufügen.

   ```powershell
   Add-AzureAccount
   ```
3. Exportieren Sie die Netzwerkkonfigurationsdatei und zeigen Sie sie an. Erstellen Sie auf Ihrem Computer ein Verzeichnis, und exportieren Sie die Netzwerkkonfigurationsdatei in das Verzeichnis. In diesem Beispiel wird die Netzwerkkonfigurationsdatei in das Verzeichnis **C:\AzureNet** exportiert.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
4. Öffnen Sie die Datei mit einem Texteditor und zeigen Sie die Namen für Ihre VNETs und Standorte an. Dies sind die Namen, die Sie beim Erstellen Ihrer Verbindungen verwenden.<br>VNET-Namen werden unter **VirtualNetworkSite name =** aufgelistet.<br>Standortnamen werden unter **LocalNetworkSiteRef name =** aufgelistet.

## <a name="createconnections"></a>Schritt 8 – Erstellen von VPN-Gatewayverbindungen

Nach Abschluss aller zuvor beschriebenen Schritte können Sie die vorinstallierten IPSec/IKE-Schlüssel festlegen und die Verbindung erstellen. Bei dieser Gruppe von Schritten wird auf PowerShell zurückgegriffen. Im Azure-Portal können keine VNet-zu-VNet-Verbindungen für das klassische Bereitstellungsmodell konfiguriert werden.

Beachten Sie in den folgenden Beispielen, dass der Wert für „SharedKey“ identisch ist. Dieser Wert muss immer übereinstimmen. Achten Sie darauf, dass die Werte in diesen Beispielen durch die genauen Namen Ihrer VNETs und lokalen Netzwerkstandorte ersetzt werden.

1. Erstellen Sie die Verbindung zwischen TestVNet1 und TestVNet4.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. Erstellen Sie die Verbindung zwischen TestVNet4 und TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. Warten Sie, bis die Verbindung initialisiert wird. Sobald das Gateway initialisiert wurde, wechselt der Status zu „Erfolgreich“.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>Informationen zu VNet-zu-VNet-Verbindungen bei klassischen VNETs
* Die virtuellen Netzwerke können sich im gleichen Abonnement oder in verschiedenen Abonnements befinden.
* Die virtuellen Netzwerke können sich in der gleichen Azure-Region oder in verschiedenen Azure-Regionen (Standorte) befinden.
* Ein Clouddienst oder Endpunkt mit Lastenausgleich darf auch dann nicht mehrere virtuelle Netzwerke umfassen, wenn diese verbunden sind.
* Für das Verbinden mehrerer virtueller Netzwerke sind keine VPN-Geräte erforderlich.
* VNet-zu-VNet unterstützt das Verbinden virtueller Azure-Netzwerke. Nicht unterstützt werden hingegen Verbindungen virtueller Computer oder Clouddienste, die nicht in einem virtuellen Netzwerk bereitgestellt wurden.
* Für VNet-zu-VNet sind Gateways mit dynamischem Routing erforderlich. Azure-Gateways mit statischem Routing werden nicht unterstützt.
* Virtuelle Netzwerkverbindungen können simultan mit VPNs mit mehreren Standorten verwendet werden. Maximal 10 VPN-Tunnel können für ein VPN-Gateway des virtuellen Netzwerks verwendet werden, das Verbindungen mit anderen virtuellen Netzwerken oder lokalen Standorten herstellt.
* Die Adressräume der virtuellen Netzwerke und der lokalen Netzwerkstandorte dürfen sich nicht überschneiden. Wenn sich Adressbereiche überschneiden, tritt beim Erstellen eines virtuellen Netzwerks oder beim Hochladen von NETCFG-Konfigurationsdateien ein Fehler auf.
* Redundante Tunnel zwischen einem Paar virtueller Netzwerke werden nicht unterstützt.
* Alle VPN-Tunnel des VNet (einschließlich P2S-VPNs) verwenden die verfügbare Bandbreite für das VPN-Gateway und die gleiche SLA für die Verfügbarkeit des VPN-Gateways in Azure gemeinsam.
* VNet-zu-VNet-Datenverkehr wird über den Azure-Backbone übertragen.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie Ihre Verbindungen. Weitere Informationen finden Sie unter [Überprüfen einer VPN-Gatewayverbindung](vpn-gateway-verify-connection-resource-manager.md).
