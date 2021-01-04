---
title: 'Konfigurieren der ExpressRoute-Verschlüsselung: IPsec über ExpressRoute für Azure Virtual WAN'
description: Erfahren Sie, wie Sie Azure Virtual WAN verwenden, um eine Site-to-Site-VPN-Verbindung über privates ExpressRoute-Peering zu erstellen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8dde3ed76587e2343edaec8626287853ec6ef9b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487506"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute-Verschlüsselung: IPsec über ExpressRoute für Virtual WAN

In diesem Artikel erfahren Sie, wie Sie mit Azure Virtual WAN über das private Peering einer Azure ExpressRoute-Leitung eine IPsec/IKE-VPN-Verbindung von Ihrem lokalen Netzwerk zu Azure herstellen. Mit dieser Methode kann eine verschlüsselte Übertragung zwischen den lokalen Netzwerken und virtuellen Azure-Netzwerken über ExpressRoute ermöglicht werden, ohne dass die Übertragung über das öffentliche Internet erfolgt oder öffentliche IP-Adressen verwendet werden.

## <a name="topology-and-routing"></a>Topologie und Routing

Das folgende Diagramm zeigt ein Beispiel für eine VPN-Verbindung über privates ExpressRoute-Peering:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="VPN über ExpressRoute":::

Das Diagramm zeigt ein Netzwerk innerhalb des lokalen Netzwerks, das über privates ExpressRoute-Peering mit dem VPN-Gateway des Azure-Hubs verbunden ist. Die Einrichtung de Verbindung ist unkompliziert:

1. Richten Sie eine ExpressRoute-Verbindung mit einer ExpressRoute-Leitung und privatem Peering ein.
2. Richten Sie die VPN-Verbindung wie in diesem Artikel beschrieben ein.

Ein wichtiger Aspekt dieser Konfiguration ist das Routing zwischen den lokalen Netzwerken und Azure über die ExpressRoute- und VPN-Pfade.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Datenverkehr von lokalen Netzwerken zu Azure

Für den Datenverkehr von lokalen Netzwerken zu Azure werden die Azure-Präfixe (einschließlich des virtuellen Hubs und aller mit dem Hub verbundenen virtuellen Netzwerke) über das BGP für das private ExpressRoute-Peering und über das VPN-BGP angekündigt. Dies führt zu zwei Netzwerkrouten (Pfaden) zu Azure aus den lokalen Netzwerken:

- Eine über den durch IPSec geschützten Pfad.
- Eine direkt über ExpressRoute *ohne* IPSec-Schutz. 

Um die Kommunikation zu verschlüsseln, muss für das VPN-verbundene Netzwerk im Diagramm sichergestellt werden, dass die Azure-Routen über das lokale VPN-Gateway dem direkten ExpressRoute-Pfad gegenüber bevorzugt werden.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Datenverkehr von Azure zu lokalen Netzwerken

Die gleiche Anforderung gilt für den Datenverkehr von Azure zu lokalen Netzwerken. Damit der IPsec-Pfad dem direkten ExpressRoute-Pfad (ohne IPsec) gegenüber bevorzugt wird, haben Sie zwei Möglichkeiten:

- Kündigen Sie in der VPN-BGP-Sitzung für das VPN-verbundene Netzwerk spezifischere Präfixe an. Sie können einen größeren Bereich, der das VPN-verbundene Netzwerk über privates ExpressRoute-Peering umfasst, und dann spezifischere Bereiche in der VPN-BGP-Sitzung ankündigen. Kündigen Sie z. B. 10.0.0.0/16 über ExpressRoute und 10.0.1.0/24 über das VPN an.

- Kündigen Sie getrennte Präfixe für VPN und ExpressRoute an. Wenn die Bereiche für das VPN-verbundene Netzwerk von anderen ExpressRoute-verbundenen Netzwerken getrennt sind, können Sie die Präfixe in den VPN- bzw. ExpressRoute-BGP-Sitzungen ankündigen. Kündigen Sie z. B. 10.0.0.0/24 über ExpressRoute und 10.0.1.0/24 über das VPN an.

In diesen beiden Beispielen sendet Azure den Datenverkehr über die VPN-Verbindung und nicht ohne VPN-Schutz direkt über ExpressRoute an 10.0.1.0/24.

> [!WARNING]
> Wenn Sie die *gleichen* Präfixe über ExpressRoute- und VPN-Verbindungen ankündigen, verwendet Azure den ExpressRoute-Pfad direkt ohne VPN-Schutz.
>

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Erstellen eines virtuellen WAN und Hubs mit Gateways

Damit Sie fortfahren können, müssen die folgenden Azure-Ressourcen und die entsprechenden lokalen Konfigurationen vorhanden sein:

- Ein Azure Virtual WAN
- Ein Virtual WAN-Hub mit einem [ExpressRoute-Gateway](virtual-wan-expressroute-portal.md) und einem [VPN-Gateway](virtual-wan-site-to-site-portal.md)

Informationen zu den Schritten zum Erstellen eines Azure Virtual WAN und Hubs mit einer ExpressRoute-Zuordnung finden Sie unter [Erstellen einer ExpressRoute-Zuordnung per Azure Virtual WAN](virtual-wan-expressroute-portal.md). Informationen zu den Schritten zum Erstellen eines VPN-Gateways in dem virtuellen WAN finden Sie unter [Erstellen einer Site-to-Site-Verbindung per Azure Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Erstellen eines Standorts für das lokale Netzwerk

Die Standortressource stimmt mit den Nicht-ExpressRoute-VPN-Standorten für ein virtuelles WAN überein. Die IP-Adresse des lokalen VPN-Geräts kann nun entweder eine private IP-Adresse oder eine öffentliche IP-Adresse im lokalen Netzwerk sein, das über das private ExpressRoute-Peering erreichbar ist, das in Schritt 1 erstellt wurde.

> [!NOTE]
> Die IP-Adresse des lokalen VPN-Geräts *muss* Teil der Adresspräfixe sein, die dem Virtual WAN-Hub über das private Azure ExpressRoute-Peering angekündigt wurden.
>

1. Navigieren Sie in Ihrem Browser zum Azure-Portal. 
1. Wählen Sie den Hub aus, den Sie erstellt haben. Wählen Sie auf der Seite des virtuellen WAN-Hubs unter **Konnektivität** die Option **VPN-Standorte** aus.
1. Wählen Sie auf der Seite **VPN-Standorte** die Option **+Standort erstellen** aus.
1. Füllen Sie auf der Seite **Create site** (Standort erstellen) die folgenden Felder aus:
   * **Abonnement**: Überprüfen Sie das Abonnement.
   * **Ressourcengruppe**: Wählen Sie die Ressourcengruppe aus, die Sie verwenden möchten, oder erstellen Sie sie.
   * **Region**: Geben Sie die Azure-Region für die VPN-Standortressource ein.
   * **Name**: Geben Sie den Namen ein, den Sie Ihrem lokalen Standort geben möchten.
   * **Gerätehersteller**: Geben Sie den Hersteller des lokalen VPN-Geräts ein.
   * **Border Gateway Protocol**: Wählen Sie „Aktivieren“ aus, wenn Ihr lokales Netzwerk BGP verwendet.
   * **Privater Adressraum**: Geben Sie den IP-Adressraum ein, der sich an Ihrem lokalen Standort befindet. Der für diesen Adressraum bestimmte Datenverkehr wird über das VPN-Gateway an das lokale Netzwerk weitergeleitet.
   * **Hubs**: Wählen Sie mindestens einen Hub für die Verbindung mit diesem VPN-Standort aus. Für die ausgewählten Hubs müssen bereits VPN-Gateways erstellt worden sein.
1. Klicken Sie auf **Weiter: Links >** , um die VPN-Verbindungseinstellungen anzuzeigen:
   * **Linkname**: Der Name, den Sie für diese Verbindung verwenden möchten.
   * **Anbietername**: Der Name des Internetdienstanbieters für diesen Standort. Bei einem lokalen ExpressRoute-Netzwerk ist dies der Name des ExpressRoute-Dienstanbieters.
   * **Geschwindigkeit**: Die Geschwindigkeit der Internetdienstverbindung oder der ExpressRoute-Leitung.
   * **IP-Adresse**: Die öffentliche IP-Adresse des VPN-Geräts, das sich an Ihrem lokalen Standort befindet. Oder bei einer lokalen ExpressRoute ist dies die private IP-Adresse des VPN-Geräts über ExpressRoute.

   Wenn BGP aktiviert ist, wird BGP für alle Verbindungen verwendet, die für diesen Standort in Azure erstellt werden. Die Vorgehensweise zum Konfigurieren von BGP für eine Virtual WAN-Instanz ist dieselbe wie beim Konfigurieren von BGP für ein Azure-VPN-Gateway. 
   
   Die lokale BGP-Peeradresse darf *nicht* mit der IP-Adresse Ihres VPN-Geräts oder dem Adressraum des virtuellen Netzwerks des VPN-Standorts übereinstimmen. Verwenden Sie als BGP-Peer-IP-Adresse eine andere IP-Adresse für das VPN-Gerät. Dabei kann es sich um eine Adresse handeln, die der Loopback-Schnittstelle des Geräts zugewiesen ist. Es kann aber auch *keine* APIPA-Adresse (169.254.*x*.*x*) sein. Geben Sie diese Adresse im entsprechenden VPN-Standort an, der den Speicherort darstellt. Informationen zu den Voraussetzungen für BGP finden Sie unter [Übersicht über BGP mit Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Klicken Sie auf **Weiter: Überprüfen + Erstellen >** , um die festgelegten Werte zu überprüfen und die VPN-Site zu erstellen. Wenn Sie **Hubs** für die Verbindung ausgewählt haben, wird die Verbindung zwischen dem lokalen Netzwerk und dem VPN-Gateway des Hubs hergestellt.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Aktualisieren der VPN-Verbindungseinstellung für die Verwendung von ExpressRoute

Nachdem Sie den VPN-Standort erstellt und die Verbindung mit dem Hub hergestellt haben, führen Sie die folgenden Schritte aus, um die Verbindung für das private ExpressRoute-Peering zu konfigurieren:

1. Kehren Sie zur Virtual WAN-Ressourcenseite zurück und wählen Sie die Hubressource aus. Oder navigieren Sie vom VPN-Standort zum verbundenen Hub.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="Hub auswählen":::
1. Wählen Sie unter **Konnektivität** die Option **VPN (Site-to-Site)** aus.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="Auswählen des VPN (Site-to-Site)":::
1. Wählen Sie das Auslassungszeichen (**...**) neben dem VPN-Standort über ExpressRoute aus, und wählen Sie **VPN-Verbindung mit diesem Hub bearbeiten** aus.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="Menü „Konfiguration eingeben“":::
1. Wählen Sie für **Private Azure-IP-Adresse verwenden** den Wert **Ja** aus. Mit dieser Einstellung wird das Hub-VPN-Gateway so konfiguriert, dass für diese Verbindung anstelle der öffentlichen IP-Adressen private IP-Adressen innerhalb des Hubadressbereichs auf dem Gateway verwendet werden. Dadurch wird sichergestellt, dass der Datenverkehr aus dem lokalen Netzwerk die privaten ExpressRoute-Peeringpfade durchläuft und nicht das öffentliche Internet für diese VPN-Verbindung verwendet wird. Der folgende Screenshot zeigt die Einstellung:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="Einstellung für die Verwendung einer privaten IP-Adresse für die VPN-Verbindung" border="false":::
1. Wählen Sie **Speichern** aus.

Nachdem Sie Ihre Änderungen gespeichert haben, verwendet das VPN-Gateway des Hubs die privaten IP-Adressen des VPN-Gateways, um die IPsec/IKE-Verbindungen mit dem lokalen VPN-Gerät über ExpressRoute herzustellen.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Ermitteln der privaten IP-Adressen für das VPN-Gateway des Hubs

Laden Sie die VPN-Gerätekonfiguration herunter, um die privaten IP-Adressen des Hub-VPN-Gateways zu ermitteln. Sie benötigen diese Adressen, um das lokale VPN-Gerät zu konfigurieren.

1. Wählen Sie auf der Seite für Ihren Hub unter **Konnektivität** die Option **VPN (Site-to-Site)** aus.
1. Wählen Sie oben auf der Seite **Übersicht** die Option **VPN-Konfiguration herunterladen** aus. 

   Azure erstellt ein Speicherkonto in der Ressourcengruppe „microsoft-network-[Standort]“, wobei *Standort* für den WAN-Standort steht. Nachdem Sie die Konfiguration auf Ihre VPN-Geräte angewendet haben, können Sie dieses Speicherkonto löschen.
1. Nachdem die Datei erstellt wurde, wählen Sie den Link aus, um sie herunterzuladen.
1. Wenden Sie die Konfiguration auf Ihr VPN-Gerät an.

### <a name="vpn-device-configuration-file"></a>VPN-Gerätekonfigurationsdatei

Die Gerätekonfigurationsdatei enthält die Einstellungen, die Sie beim Konfigurieren Ihrer lokalen VPN-Geräte verwenden. Beachten Sie beim Anzeigen dieser Datei die folgenden Informationen:

* **vpnSiteConfiguration**: In diesem Abschnitt sind die Gerätedetails für die Einrichtung einer Site angegeben, für die eine Verbindung mit dem virtuellen WAN hergestellt wird. Hierzu gehören der Name und die öffentliche IP-Adresse des Zweigstellengeräts.
* **vpnSiteConnections**: Dieser Abschnitt enthält die folgenden Einstellungen:

    * Adressraum des virtuellen Netzwerks des virtuellen Hubs.<br/>Beispiel:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Adressraum der virtuellen Netzwerke, die mit dem Hub verbunden sind.<br>Beispiel:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * IP-Adressen des VPN-Gateways des virtuellen Hubs. Da jede Verbindung des VPN-Gateways aus zwei Tunneln mit Aktiv-Aktiv-Konfiguration besteht, werden in dieser Datei beide IP-Adressen aufgelistet. In diesem Beispiel werden `Instance0` und `Instance1` für jeden Standort angezeigt. Dabei handelt sich um private IP-Adressen und nicht um öffentliche IP-Adressen.<br>Beispiel:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Details zur Konfiguration der VPN-Gatewayverbindung, z. B. BGP und vorinstallierter Schlüssel. Der vorinstallierte Schlüssel wird automatisch für Sie erstellt. Sie können die Verbindung für einen benutzerdefinierten vorinstallierten Schlüssel jederzeit auf der Seite **Übersicht** bearbeiten.
  
### <a name="example-device-configuration-file"></a>Beispiel für Gerätekonfigurationsdatei

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Konfigurieren Ihres VPN-Geräts

Falls Sie eine Anleitung für die Konfiguration Ihres Geräts benötigen, können Sie die Anleitung auf der [Seite mit den Schritten für die VPN-Gerätekonfiguration](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) verwenden. Beachten Sie hierbei aber die folgenden Einschränkungen:

* Die Anweisungen auf der Seite des VPN-Geräts sind nicht für Virtual WAN gedacht. Sie können aber die Virtual WAN-Werte aus der Konfigurationsdatei verwenden, um Ihr VPN-Gerät manuell zu konfigurieren. 
* Die herunterladbaren Skripts für die Gerätekonfiguration, die für das VPN-Gateway bestimmt sind, funktionieren nicht für Virtual WAN, da sich die Konfiguration unterscheidet.
* Eine neue Virtual WAN-Instanz kann sowohl IKEv1 als auch IKEv2 unterstützen.
* Virtual WAN kann nur routenbasierte VPN-Geräte und die entsprechenden Geräteanweisungen verwenden.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Anzeigen Ihrer Virtual WAN-Instanz

1. Wechseln Sie zum virtuellen WAN.
1. Auf der Seite **Übersicht** steht jeder Punkt auf der Karte für einen Hub.
1. Im Abschnitt mit den **Hubs und Verbindungen** können Sie den Hub, die Site, die Region und den Status der VPN-Verbindung anzeigen. Sie können auch die Anzahl der ein- und ausgehenden Bytes anzeigen.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. Überwachen einer Verbindung

Erstellen Sie eine Verbindung, um die Kommunikation zwischen einem virtuellen Azure-Computer (VM) und einem Remotestandort zu überwachen. Weitere Informationen zum Einrichten einer Verbindungsüberwachung finden Sie unter [Überwachen der Netzwerkkommunikation](~/articles/network-watcher/connection-monitor.md). Das Quellfeld ist die VM-IP in Azure, und die Ziel-IP ist die Standort-IP.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. Bereinigen von Ressourcen

Wenn Sie diese Ressourcen nicht mehr benötigen, können Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) verwenden, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu entfernen. Führen Sie den folgenden PowerShell-Befehl aus, und ersetzen Sie `myResourceGroup` durch den Namen Ihrer Ressourcengruppe:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel hilft Ihnen beim Erstellen einer VPN-Verbindung über privates ExpressRoute-Peering mithilfe von Virtual WAN. Weitere Informationen zu Virtual WAN und zugehörigen Funktionen finden Sie unter [Übersicht über Virtual WAN](virtual-wan-about.md).
