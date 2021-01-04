---
title: Migrieren von Azure-Netzwerkressourcen von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Netzwerkressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli
ms.openlocfilehash: 1a10311974404a072feb535e5a4f562be2925504
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96002775"
---
# <a name="migrate-network-resources-to-global-azure"></a>Migrieren von Netzwerkressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Die meisten Netzwerkdienste unterstützen keine Migration von Azure Deutschland zu Azure weltweit. Allerdings können Sie Ihre Netzwerke in beiden Cloudumgebungen über ein Site-to-Site-VPN verbinden. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Die Schritte zum Einrichten eines Site-to-Site-VPNs zwischen Clouds sind mit den Schritten vergleichbar, die Sie zum Bereitstellen eines Site-to-Site-VPN zwischen Ihrem lokalen Netzwerk und Azure ausführen. Definieren Sie ein Gateway in beiden Clouds, und geben Sie dann an, wie die VPNs miteinander kommunizieren sollen. Im Artikel [Erstellen einer Site-to-Site-Verbindung im Azure-Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) werden die Schritte beschrieben, die Sie zum Bereitstellen eines Site-to-Site-VPNs ausführen müssen. Zusammenfassung der Schritte:

1. Definieren Sie ein virtuelles Netzwerk.
1. Definieren Sie den Adressraum.
1. Definieren Sie Subnetze.
1. Definieren Sie ein Gatewaysubnetz.
1. Definieren Sie ein Gateway für das virtuelle Netzwerk.
1. Definieren Sie ein Gateway für das lokale Netzwerk (Ihr lokales VPN-Gerät).
1. Konfigurieren Sie ein lokales VPN-Gerät.
1. Stellen Sie die Verbindung her.

So verbinden Sie virtuelle Netzwerke zwischen Azure weltweit und Azure Deutschland

1. Führen Sie die vorstehend genannten Schritte 1 bis 5 in Azure weltweit aus.
1. Führen Sie die Schritte 1 bis 5 in Azure Deutschland aus.
1. Führen Sie Schritt 6 in Azure weltweit aus:
   - Geben Sie die öffentliche IP-Adresse des VPN-Gateways in Azure Deutschland ein.
1. Führen Sie Schritt 6 in Azure Deutschland aus:
   - Geben Sie die öffentliche IP-Adresse des VPN Gateways in Azure weltweit ein.
1. Überspringen Sie Schritt 7.
1. Schließen Sie Schritt 8 ab.

## <a name="virtual-networks"></a>Virtuelle Netzwerke

Die Migration virtueller Netzwerke von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, neue virtuelle Netzwerke in der Zielregion zu erstellen und Ressourcen zu diesen virtuellen Netzwerken zu migrieren.

Weitere Informationen finden Sie unter:

- Aktualisieren Sie Ihre Kenntnisse, indem Sie die [Tutorials zu Azure Virtual Network](../virtual-network/index.yml) durcharbeiten.
- Im Artikel [Übersicht über virtuelle Netzwerke](../virtual-network/virtual-networks-overview.md) finden Sie weitere Informationen zu diesem Thema.
- Lesen Sie den Artikel [Planen virtueller Netzwerke](../virtual-network/virtual-network-vnet-plan-design-arm.md).

## <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Die Migration von Netzwerksicherheitsgruppen von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, neue Netzwerksicherheitsgruppen in der Zielregion zu erstellen und die Netzwerksicherheitsgruppenregeln auf die neue Anwendungsumgebung anzuwenden.

Die aktuelle Konfiguration einer beliebigen Netzwerksicherheitsgruppe können Sie über das Portal oder mit den folgenden PowerShell-Befehlen abrufen:

```powershell
$nsg=Get-AzNetworkSecurityGroup -ResourceName <nsg-name> -ResourceGroupName <resourcegroupname>
Get-AzNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg
```

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre [Kenntnisse zu Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#network-security-groups) auf.
- Lesen Sie auch den Artikel [Übersicht über die Netzwerksicherheit](../virtual-network/network-security-groups-overview.md).
- Im Artikel [Befehle zum Verwalten von Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md) finden Sie weitere Informationen zu diesem Thema.

## <a name="expressroute"></a>ExpressRoute

Das Migrieren einer Azure ExpressRoute-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, in Azure weltweit neue ExpressRoute-Verbindungen und ein neues ExpressRoute-Gateway zu erstellen.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu ExpressRoute](../expressroute/index.yml) durcharbeiten.
- Im Artikel [Erstellen eines neuen ExpressRoute-Gateways](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) finden Sie weitere Informationen zu diesem Thema.
- Lesen Sie den Artikel [ExpressRoute-Standorte und -Dienstanbieter](../expressroute/expressroute-locations.md).
- Lesen Sie auch den Artikel [Gateways für virtuelle Netzwerke für ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="vpn-gateway"></a>VPN Gateway

Das Migrieren einer Azure VPN Gateway-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, in Azure weltweit eine neue VPN Gateway-Instanz zu erstellen und zu konfigurieren.

Informationen zu Ihrer aktuellen VPN Gateway-Konfiguration können Sie über das Portal oder mit PowerShell abrufen. Verwenden Sie in PowerShell einen Satz von Cmdlets, die mit `Get-AzVirtualNetworkGateway*` beginnen.

Stellen Sie sicher, dass Sie Ihre lokale Konfiguration aktualisieren. Löschen Sie auch alle vorhandenen Regeln für die alten IP-Adressbereiche, nachdem Sie Ihre Azure-Netzwerkumgebung aktualisiert haben.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu VPN Gateway](../vpn-gateway/index.yml) durcharbeiten.
- Im Artikel [Herstellen einer Site-to-Site-Verbindung](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) finden Sie weitere Informationen zu diesem Thema.
- Lesen Sie auch den Artikel über die PowerShell-Cmdlets [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway).
- Lesen Sie den Blogbeitrag [Herstellen einer Site-to-Site-Verbindung](/archive/blogs/ralfwi/connecting-clouds).
 
## <a name="application-gateway"></a>Application Gateway

Das Migrieren einer Azure Application Gateway-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, in Azure weltweit ein neues Gateway zu erstellen und zu konfigurieren.

Informationen zu Ihrer aktuellen Gatewaykonfiguration können Sie über das Portal oder mit PowerShell abrufen. Verwenden Sie in PowerShell einen Satz von Cmdlets, die mit `Get-AzApplicationGateway*` beginnen.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Application Gateway](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md) durcharbeiten.
- Im Artikel [Erstellen eines Application Gateways](../application-gateway/quick-create-portal.md) finden Sie weitere Informationen zu diesem Thema.
- Lesen Sie auch den Artikel über die PowerShell-Cmdlets [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway).

## <a name="dns"></a>DNS

Um Ihre Azure DNS-Konfiguration von Azure Deutschland zu Azure weltweit zu migrieren, exportieren Sie die DNS-Zonendatei und importieren sie dann unter dem neuen Abonnement. Derzeit können Sie die Zonendatei nur über die Azure CLI exportieren.

Wenn Sie sich in Azure Deutschland bei Ihrem Quellabonnement angemeldet haben, konfigurieren Sie die Azure CLI für die Verwendung des Resource Manager-Modus. Exportieren Sie die Zone mit dem folgenden Befehl:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Beispiel:

```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```

Dieser Befehl ruft den Azure DNS-Dienst zum Exportieren der Zone `contoso.com` in der Ressourcengruppe `myresourcegroup` auf. Die Ausgabe wird als eine mit BIND kompatible Zonendatei in „contoso.com.txt“ im aktuellen Ordner gespeichert.

Wenn der Export abgeschlossen ist, löschen Sie die NS-Einträge aus der Zonendatei. Neue NS-Einträge werden für die neue Region und dasselbe Abonnement erstellt.

Melden Sie sich nun in Ihrer Zielumgebung an, erstellen Sie eine neue Ressourcengruppe (oder wählen Sie eine vorhandene aus), und importieren Sie dann die Zonendatei:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Nachdem die Zone importiert wurde, müssen Sie die Zone überprüfen, indem Sie den folgenden Befehl ausführen:

```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```

Wenden Sie sich nach Abschluss der Überprüfung an Ihre Domänenregistrierungsstelle, und delegieren Sie die NS-Einträge erneut. Rufen Sie Informationen zum NS-Eintrag ab, indem Sie den folgenden Befehl ausführen:

```azurecli
az network dns record-set ns list -g <resource group> -z --output json
```

Weitere Informationen finden Sie unter:

- Aktualisieren Sie Ihre Kenntnisse, indem Sie die [Tutorials zu Azure DNS](../dns/index.yml) durcharbeiten.
- Im Artikel [Übersicht über Azure DNS](../dns/dns-overview.md) finden Sie weitere Informationen zu diesem Thema.
- Erfahren Sie mehr über [Azure DNS-Import und -Export](../dns/dns-import-export.md).

## <a name="network-watcher"></a>Network Watcher

Das Migrieren einer Azure Network Watcher-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, eine neue Network Watcher-Instanz in Azure weltweit zu erstellen und zu konfigurieren. Vergleichen Sie anschließend die Ergebnisse zwischen der alten und der neuen Umgebung.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Network Watcher-Tutorials](../network-watcher/index.yml) durcharbeiten.
- Im Artikel [Übersicht über Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) finden Sie weitere Informationen zu diesem Thema.
- Erfahren Sie mehr über [Datenflussprotokolle für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-portal.md).
- Erfahren Sie mehr über [Verbindungsmonitor](../network-watcher/connection-monitor.md).

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager kann Sie beim Ausführen einer reibungslosen Migration unterstützen. E ist aber nicht möglich, Traffic Manager-Profile, die Sie in Azure Deutschland erstellt haben, nach Azure weltweit zu migrieren. (Während einer Migration migrieren Sie Traffic Manager-Endpunkte zur Zielumgebung, so dass Sie das Traffic Manager-Profil ohnehin aktualisieren müssen.)

Sie können zusätzliche Endpunkte in der Zielumgebung definieren, indem Sie den Traffic Manager verwenden, während er noch in der Quellumgebung ausgeführt wird. Wenn Traffic Manager in der neuen Umgebung ausgeführt wird, können Sie in der Quellumgebung weiterhin Endpunkte definieren, die Sie noch nicht migriert haben. Dieses Szenario wird als das [Blau-Grün-Szenario](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/) bezeichnet. Dieses Szenario umfasst die folgenden Schritte:

1. Erstellen Sie ein neues Traffic Manager-Profil in Azure weltweit.
1. Definieren Sie die Endpunkte in Azure Deutschland.
1. Ändern Sie Ihren DNS CNAME-Eintrag in das neue Traffic Manager-Profil.
1. Deaktivieren Sie das alte Traffic Manager-Profil.
1. Migrieren und konfigurieren Sie Endpunkte. Für jeden Endpunkt in Azure Deutschland:
   1. Migrieren Sie den Endpunkt zu Azure weltweit.
   1. Ändern Sie das Traffic Manager-Profil, um den neuen Endpunkt zu verwenden.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Traffic Manager-Tutorials](../traffic-manager/index.yml) durcharbeiten.
- Lesen Sie [Was ist Traffic Manager?](../traffic-manager/traffic-manager-overview.md)
- Informieren Sie sich über das [Erstellen eines Traffic Manager-Profils](../traffic-manager/quickstart-create-traffic-manager-profile.md).

## <a name="load-balancer"></a>Load Balancer

Das Migrieren einer Azure Load Balancer-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, in Azure weltweit eine neue Load Balancer-Instanz zu erstellen und zu konfigurieren.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Load Balancer](../load-balancer/index.yml) durcharbeiten.
- Lesen Sie auch den Artikel [Übersicht über Azure Load Balancer](../load-balancer/load-balancer-overview.md).
- Im Artikel [Erstellen eines neuen Load Balancers](../load-balancer/quickstart-load-balancer-standard-public-portal.md) finden Sie weitere Informationen zu diesem Thema.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)