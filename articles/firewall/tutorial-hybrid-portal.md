---
title: 'Tutorial: Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk über das Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Firewall über das Azure-Portal bereitstellen und konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 78e934a90b8d4e8feccf18a5cada3ec4920e1642
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734451"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Tutorial: Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk über das Azure-Portal

Wenn Sie Ihr lokales Netzwerk mit einem virtuellen Azure-Netzwerk verbinden, um ein Hybridnetzwerk zu erstellen, ist das Steuern des Zugriffs auf Ihre Azure-Netzwerkressourcen ein wichtiger Bestandteil des globalen Sicherheitsplans.

Mit Azure Firewall können Sie den Netzwerkzugriff in einem Hybridnetzwerk mithilfe von Regeln steuern, die den zulässigen und verweigerten Netzwerkdatenverkehr definieren.

In diesem Tutorial erstellen Sie drei virtuelle Netzwerke:

- **VNet-Hub**: In diesem virtuellen Netzwerk befindet sich die Firewall.
- **VNet-Spoke**: Das virtuelle Spoke-Netzwerk stellt die Workload in Azure dar.
- **VNet-Onprem**: Das lokale virtuelle Netzwerk stellt ein lokales Netzwerk dar. Bei einer tatsächlichen Bereitstellung kann die Verbindung dafür entweder mit einer VPN- oder einer ExpressRoute-Verbindung hergestellt werden. Der Einfachheit halber wird in diesem Tutorial eine VPN-Gatewayverbindung genutzt, und ein virtuelles Netzwerk in Azure wird als lokales Netzwerk verwendet.

![Firewall in einem Hybridnetzwerk](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Deklarieren der Variablen
> * Erstellen des virtuellen Firewall-Hub-Netzwerks
> * Erstellen des virtuellen Spoke-Netzwerks
> * Erstellen des lokalen virtuellen Netzwerks
> * Konfigurieren und Bereitstellen der Firewall
> * Erstellen und Verbinden der VPN-Gateways
> * Durchführen des Peerings für die virtuellen Hub- und Spoke-Netzwerke
> * Erstellen der Routen
> * Erstellen der virtuellen Computer
> * Testen der Firewall

Falls Sie anstelle dieser Prozedur Azure PowerShell verwenden möchten, wechseln Sie zu [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk mit Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Voraussetzungen

Ein Hybridnetzwerk nutzt das Modell der Hub-and-Spoke-Architektur zur Weiterleitung von Datenverkehr zwischen Azure-VNETs und lokalen Netzwerken. Für die Hub-and-Spoke-Architektur gelten die folgenden Anforderungen:

- Legen Sie für das Peering von VNET-Hub mit VNET-Spoke **AllowGatewayTransit** fest. In der Hub-and-Spoke-Netzwerkarchitektur ermöglicht der Gatewaytransit die gemeinsame Nutzung des VPN-Gateways im Hub, anstatt VPN-Gateways in jedem virtuellen Spoke-Netzwerk bereitzustellen. 

   Darüber hinaus werden Routen zu den mit dem Gateway verbundenen virtuellen Netzwerken oder lokalen Netzwerken über den Gatewaytransit automatisch an die Routingtabellen für die virtuellen Netzwerke mit Peeringbeziehung verteilt. Weitere Informationen finden Sie unter [Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Legen Sie für das Peering von VNET-Spoke mit VNET-Hub **UseRemoteGateways** fest. Wenn **UseRemoteGateways** und für das Remotepeering **AllowGatewayTransit** festgelegt ist, verwendet das virtuelle Spoke-Netzwerk Gateways des virtuellen Remotenetzwerks für den Transit.
- Zum Weiterleiten des Spoke-Subnetzdatenverkehrs durch die Hub-Firewall können Sie eine benutzerdefinierte Route (User Defined Route, UDR) verwenden, die auf die Firewall verweist, während die Option **Routenverteilung des Gateways für virtuelle Netzwerke** deaktiviert ist. Wenn die Option **Routenverteilung des Gateways für virtuelle Netzwerke** deaktiviert ist, wird die Routenverteilung für die Spoke-Subnetze verhindert. Dadurch wird verhindert, dass erlernte Routen mit Ihrer UDR in Konflikt stehen. Wenn Sie **Routenverteilung des Gateways für virtuelle Netzwerke** aktiviert lassen möchten, definieren Sie unbedingt spezifische Routen für die Firewall, um die Routen außer Kraft zu setzen, die aus einer lokalen Umgebung über BGP veröffentlicht werden.
- Konfigurieren Sie eine UDR im Hub-Gatewaysubnetz, die auf die Firewall-IP-Adresse als nächsten Hop auf dem Weg zu den Spoke-Netzwerken verweist. Für das Azure Firewall-Subnetz ist keine UDR erforderlich, da es die Routen über BGP erlernt.

Informationen zur Erstellung dieser Routen finden Sie in diesem Tutorial im Abschnitt [Erstellen von Routen](#create-the-routes).

>[!NOTE]
>Azure Firewall muss über eine direkte Internetverbindung verfügen. Wenn Ihr Subnetz „AzureFirewallSubnet“ eine Standardroute zu Ihrem lokalen Netzwerk über BGP erfasst, müssen Sie diese mit der benutzerdefinierten Route 0.0.0.0/0 überschreiben. Legen Sie dabei den Wert **NextHopType** auf **Internet** fest, um die direkte Internetkonnektivität beizubehalten.
>
>Azure Firewall kann so konfiguriert werden, dass die Tunnelerzwingung unterstützt wird. Weitere Informationen finden Sie unter [Azure Firewall-Tunnelerzwingung](forced-tunneling.md).

>[!NOTE]
>Der Datenverkehr zwischen per direktem Peering verbundenen VNETs wird direkt weitergeleitet, auch wenn eine UDR auf Azure Firewall als Standardgateway verweist. Um in diesem Szenario Subnetz-zu-Subnetz-Datenverkehr an die Firewall zu senden, muss eine UDR explizit das Zielsubnetzwerk-Präfix in beiden Subnetzen enthalten.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-the-firewall-hub-virtual-network"></a>Erstellen des virtuellen Firewall-Hub-Netzwerks

Erstellen Sie zunächst die Ressourcengruppe für die in diesem Tutorial verwendeten Ressourcen:

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Wählen Sie auf der Startseite des Azure-Portals **Ressourcengruppen** > **Hinzufügen** aus.
3. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
1. Geben Sie unter **Ressourcengruppenname** die Zeichenfolge **FW-Hybrid-Test** ein.
2. Wählen Sie unter **Region** die Option **(USA) USA, Osten** aus. Alle später erstellten Ressourcen müssen sich am gleichen Standort befinden.
3. Klicken Sie auf **Überprüfen + erstellen**.
4. Klicken Sie auf **Erstellen**.

Erstellen Sie nun das VNET:

> [!NOTE]
> Die Größe des Subnetzes „AzureFirewallSubnet“ beträgt /26. Weitere Informationen zur Subnetzgröße finden Sie unter [Azure Firewall – Häufig gestellte Fragen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Netzwerk** die Option **Virtuelles Netzwerk** aus.
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe **FW-Hybrid-Test** aus.
1. Geben Sie unter **Name** den Namen **VNet-hub** ein.
1. Klicken Sie auf **Weiter: IP-Adressen**.
1. Löschen Sie unter **IPv4-Adressraum** die Standardadresse, und geben Sie **10.5.0.0/16** ein.
1. Wählen Sie unter **Subnetzname** die Einstellung **Subnetz hinzufügen** aus.
1. Geben Sie unter **Subnetz** die Zeichenfolge **AzureFirewallSubnet** ein. Die Firewall befindet sich diesem Subnetz, und der Subnetzname **muss** „AzureFirewallSubnet“ lauten.
1. Geben Sie unter **Subnetzadressbereich** den Bereich **10.5.0.0/26** ein. 
1. Wählen Sie **Hinzufügen**.
1. Klicken Sie auf **Überprüfen und erstellen**.
1. Klicken Sie auf **Erstellen**.

## <a name="create-the-spoke-virtual-network"></a>Erstellen des virtuellen Spoke-Netzwerks

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Netzwerk** die Option **Virtuelles Netzwerk** aus.
7. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe **FW-Hybrid-Test** aus.
1. Geben Sie unter **Name** den Namen **VNet-Spoke** ein.
2. Wählen Sie unter **Region** die Option **(USA) USA, Osten** aus.
3. Klicken Sie auf **Weiter: IP-Adressen**.
4. Löschen Sie unter **IPv4-Adressraum** die Standardadresse, und geben Sie **10.6.0.0/16** ein.
6. Wählen Sie unter **Subnetzname** die Einstellung **Subnetz hinzufügen** aus.
7. Geben Sie unter **Subnetzname** die Zeichenfolge **SN-Workload** ein.
8. Geben Sie unter **Subnetzadressbereich** den Bereich **10.6.0.0/24** ein. 
9. Wählen Sie **Hinzufügen**.
10. Klicken Sie auf **Überprüfen und erstellen**.
11. Klicken Sie auf **Erstellen**.

## <a name="create-the-on-premises-virtual-network"></a>Erstellen des lokalen virtuellen Netzwerks

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Netzwerk** die Option **Virtuelles Netzwerk** aus.
7. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe **FW-Hybrid-Test** aus.
1. Geben Sie unter **Name** den Namen **VN-OnPrem** ein.
2. Wählen Sie unter **Region** die Option **(USA) USA, Osten** aus.
3. Wählen Sie **Weiter: IP-Adressen** aus.
4. Löschen Sie unter **IPv4-Adressraum** die Standardadresse, und geben Sie **192.168.0.0/16** ein.
5. Wählen Sie unter **Subnetzname** die Einstellung **Subnetz hinzufügen** aus.
7. Geben Sie unter **Subnetzname** den Namen **SN-Corp** ein.
8. Geben Sie unter **Subnetzadressbereich** den Bereich **192.168.1.0/24** ein. 
9. Wählen Sie **Hinzufügen**.
10. Klicken Sie auf **Überprüfen und erstellen**.
11. Klicken Sie auf **Erstellen**.

Erstellen Sie nun ein zweites Subnetz für das Gateway.

1. Wählen Sie auf der Seite **VNet-Onprem** die Option **Subnetze** aus.
2. Wählen Sie **+ Subnetz** aus.
3. Geben Sie unter **Name** den Namen **GatewaySubnet** ein.
4. Geben Sie unter **Subnetzadressbereich** den Bereich **192.168.2.0/24** ein.
5. Klicken Sie auf **OK**.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurieren und Bereitstellen der Firewall

Stellen Sie nun die Firewall im virtuellen Firewall-Hub-Netzwerk bereit.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie in der linken Spalte die Option **Netzwerk** aus. Suchen Sie nach **Firewall**, und wählen Sie in den Suchergebnissen dann „Firewall“ aus.
4. Konfigurieren Sie die Firewall auf der Seite **Firewall erstellen** anhand der folgenden Tabelle:

   |Einstellung  |Wert  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |**FW-Hybrid-Test** |
   |Name     |**AzFW01**|
   |Region     |**USA, Osten**|
   |Virtuelles Netzwerk auswählen     |**Vorhandene verwenden**:<br> **VNet-hub**|
   |Öffentliche IP-Adresse     |Neu hinzufügen: <br>**fw-pip**. |

5. Klicken Sie auf **Überprüfen + erstellen**.
6. Überprüfen Sie die Zusammenfassung, und wählen Sie dann **Erstellen** aus, um die Firewall zu erstellen.

   Die Bereitstellung dauert einige Minuten.
7. Navigieren Sie nach Abschluss der Bereitstellung zur Ressourcengruppe **FW-Hybrid-Test**, und wählen Sie die Firewall **AzFW01** aus.
8. Notieren Sie sich die private IP-Adresse. Diese wird später für die Erstellung der Standardroute benötigt.

### <a name="configure-network-rules"></a>Konfigurieren von Netzwerkregeln

Fügen Sie zunächst eine Netzwerkregel hinzu, um Webdatenverkehr zuzulassen.

1. Wählen Sie auf der Seite **AzFW01** die Option **Regeln** aus.
2. Klicken Sie auf die Registerkarte **Netzwerkregelsammlung**.
3. Wählen Sie **Netzwerkregelsammlung hinzufügen** aus.
4. Geben Sie unter **Name** den Namen **RCNet01** ein.
5. Geben Sie unter **Priorität** den Wert **100** ein.
6. Wählen Sie für **Aktion** die Option **Zulassen** aus.
6. Geben Sie unter **Regeln** für **Name** die Zeichenfolge **AllowWeb** ein.
7. Wählen Sie für **Protokoll** die Option **TCP** aus.
8. Wählen Sie unter **Quelltyp** die Option **IP-Adresse** aus.
9. Geben Sie unter **Quelle** die Adresse **192.168.1.0/24** ein.
10. Wählen Sie unter **Zieltyp** die Option **IP-Adresse** aus.
11. Geben Sie unter **Zieladresse** die Zeichenfolge **10.6.0.0/16** ein.
12. Geben Sie unter **Zielports** den Wert **80** ein.

Fügen Sie nun eine Regel hinzu, um RDP-Datenverkehr zuzulassen.

Geben Sie in der zweiten Regel Zeile die folgenden Informationen ein:

1. Geben Sie unter **Name** den Namen **AllowRDP** ein.
2. Wählen Sie für **Protokoll** die Option **TCP** aus.
3. Wählen Sie unter **Quelltyp** die Option **IP-Adresse** aus.
4. Geben Sie unter **Quelle** die Adresse **192.168.1.0/24** ein.
5. Wählen Sie unter **Zieltyp** die Option **IP-Adresse** aus.
6. Geben Sie unter **Zieladresse** die Zeichenfolge **10.6.0.0/16** ein.
7. Geben Sie unter **Zielports** den Wert **3389** ein.
8. Wählen Sie **Hinzufügen**.

## <a name="create-and-connect-the-vpn-gateways"></a>Erstellen und Verbinden der VPN-Gateways

Das virtuelle Hub-Netzwerk und das lokale virtuelle Netzwerk werden über VPN-Gateways verbunden.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Erstellen eines VPN-Gateways für das virtuelle Hub-Netzwerk

Erstellen Sie nun das VPN-Gateway für das virtuelle Hub-Netzwerk. Netzwerk-zu-Netzwerk-Konfigurationen erfordern einen routenbasierten VPN-Typ. Häufig kann die Erstellung eines VPN-Gateways je nach ausgewählter VPN-Gateway-SKU mindestens 45 Minuten dauern.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Geben Sie im Textfeld für die Suche **Gateway für virtuelle Netzwerke** ein.
3. Wählen Sie **Gateway für virtuelle Netzwerke** und anschließend **Erstellen** aus.
4. Geben Sie unter **Name** den Namen **GW-hub** ein.
5. Wählen Sie unter **Region** die gleiche Region aus wie zuvor.
6. Wählen Sie unter **Gatewaytyp** die Option **VPN** aus.
7. Wählen Sie unter **VPN-Typ** die Option **Routenbasiert** aus.
8. Wählen Sie unter **SKU** die Option **Basic** aus.
9. Wählen Sie unter **Virtuelles Netzwerk** die Option **VNet-hub** aus.
10. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu erstellen** aus, und geben Sie als Name die Zeichenfolge **VNet-hub-GW-pip** ein.
11. Übernehmen Sie für die restlichen Optionen die Standardwerte, und klicken Sie auf **Überprüfen + erstellen**.
12. Überprüfen Sie die Konfiguration, und klicken Sie auf **Erstellen**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Erstellen eines VPN-Gateways für das lokale virtuelle Netzwerk

Erstellen Sie nun das VPN-Gateway für das lokale virtuelle Netzwerk. Netzwerk-zu-Netzwerk-Konfigurationen erfordern einen routenbasierten VPN-Typ. Häufig kann die Erstellung eines VPN-Gateways je nach ausgewählter VPN-Gateway-SKU mindestens 45 Minuten dauern.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Geben Sie **Gateway für virtuelle Netzwerke** in das Textfeld für die Suche ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie **Gateway für virtuelle Netzwerke** und anschließend **Erstellen** aus.
4. Geben Sie unter **Name** den Namen **GW-Onprem** ein.
5. Wählen Sie unter **Region** die gleiche Region aus wie zuvor.
6. Wählen Sie unter **Gatewaytyp** die Option **VPN** aus.
7. Wählen Sie unter **VPN-Typ** die Option **Routenbasiert** aus.
8. Wählen Sie unter **SKU** die Option **Basic** aus.
9. Wählen Sie unter **Virtuelles Netzwerk** die Option **VNet-Onprem** aus.
10. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu erstellen** aus, und geben Sie als Name die Zeichenfolge **VNet-Onprem-GW-pip** ein.
11. Übernehmen Sie für die restlichen Optionen die Standardwerte, und klicken Sie auf **Überprüfen + erstellen**.
12. Überprüfen Sie die Konfiguration, und klicken Sie auf **Erstellen**.

### <a name="create-the-vpn-connections"></a>Erstellen der VPN-Verbindungen

Nun können Sie die VPN-Verbindungen zwischen dem Hub-Gateway und dem lokalen Gateway erstellen.

In diesem Schritt erstellen Sie die Verbindung vom virtuellen Hub-Netzwerk zum lokalen virtuellen Netzwerk. In den Beispielen wird auf einen gemeinsam verwendeten Schlüssel verwiesen. Sie können eigene Werte für den gemeinsam verwendeten Schlüssel verwenden. Wichtig ist dabei, dass der gemeinsam verwendete Schlüssel für beide Verbindungen übereinstimmen muss. Das Erstellen einer Verbindung kann etwas dauern.

1. Öffnen Sie die Ressourcengruppe **FW-Hybrid-Test**, und wählen Sie das Gateway **GW-hub** aus.
2. Wählen Sie in der linken Spalte die Option **Verbindungen** aus.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie als Verbindungsname die Zeichenfolge **Hub-to-Onprem** ein.
5. Wählen Sie unter **Verbindungstyp** die Option **VNet-zu-VNet** aus.
6. Wählen Sie für das **zweite Gateway für virtuelle Netzwerke** die Option **GW-Onprem** aus.
7. Geben Sie unter **Gemeinsam verwendeter Schlüssel (PSK)** die Zeichenfolge **AzureA1b2C3** ein.
8. Klicken Sie auf **OK**.

Erstellen Sie die Verbindung vom lokalen virtuellen Netzwerk zum virtuellen Hub-Netzwerk. Dieser Schritt ähnelt dem vorherigen Schritt, hier erstellen Sie jedoch die Verbindung vom lokalen virtuellen Netzwerk zum virtuellen Hub-Netzwerk. Stellen Sie sicher, dass die freigegebenen Schlüssel übereinstimmen. Nach einigen Minuten wird die Verbindung hergestellt.

1. Öffnen Sie die Ressourcengruppe **FW-Hybrid-Test**, und wählen Sie das Gateway **GW-Onprem** aus.
2. Wählen Sie in der linken Spalte die Option **Verbindungen** aus.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie als Verbindungsnamen den Namen **Onprem-to-Hub** ein.
5. Wählen Sie unter **Verbindungstyp** die Option **VNet-zu-VNet** aus.
6. Wählen Sie für das **zweite Gateway für virtuelle Netzwerke** die Option **GW-hub** aus.
7. Geben Sie unter **Gemeinsam verwendeter Schlüssel (PSK)** die Zeichenfolge **AzureA1b2C3** ein.
8. Klicken Sie auf **OK**.


#### <a name="verify-the-connection"></a>Überprüfen der Verbindung

Nach etwa fünf Minuten sollte der Status beider Verbindungen **Verbunden** lauten.

![Gatewayverbindungen](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Durchführen des Peerings für die virtuellen Hub- und Spoke-Netzwerke

Führen Sie nun das Peering für die virtuellen Hub- und Spoke-Netzwerke durch.

1. Öffnen Sie die Ressourcengruppe **FW-Hybrid-Test**, und wählen Sie das virtuelle Netzwerk **VNet-hub** aus.
2. Wählen Sie in der linken Spalte **Peerings** aus.
3. Wählen Sie **Hinzufügen**.
4. Unter **Dieses virtuelle Netzwerk**:
 
   
   |Einstellungsname  |Wert  |
   |---------|---------|
   |Name des Peeringlinks| HubtoSpoke|
   |Datenverkehr zum virtuellen Remotenetzwerk|   Zulassen (Standard)      |
   |Traffic forwarded from remote virtual network (Vom virtuellen Remotenetzwerk weitergeleiteter Datenverkehr)    |   Zulassen (Standard)      |
   |Gateway des virtuellen Netzwerks     |  Gateway dieses virtuellen Netzwerks verwenden       |
    
5. Unter **Virtuelles Remotenetzwerk**:

   |Einstellungsname  |Wert  |
   |---------|---------|
   |Name des Peeringlinks | SpoketoHub|
   |Bereitstellungsmodell für das virtuelle Netzwerk| Resource Manager|
   |Subscription|\<your subscription\>|
   |Virtuelles Netzwerk| VNet-Spoke
   |Datenverkehr zum virtuellen Remotenetzwerk     |   Zulassen (Standard)      |
   |Traffic forwarded from remote virtual network (Vom virtuellen Remotenetzwerk weitergeleiteter Datenverkehr)    |   Zulassen (Standard)      |
   |Gateway des virtuellen Netzwerks     |  Gateway des virtuellen Remotenetzwerks verwenden       |

5. Wählen Sie **Hinzufügen**.

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="VNET-Peering":::

## <a name="create-the-routes"></a>Erstellen der Routen

Erstellen Sie als Nächstes zwei Routen:

- Eine Route vom Hub-Gatewaysubnetz zum Spoke-Subnetz über die Firewall-IP-Adresse
- Eine Standardroute vom Spoke-Subnetz über die Firewall-IP-Adresse

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Geben Sie **Routingtabelle** in das Textfeld für die Suche ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie **Routingtabelle** aus.
4. Klicken Sie auf **Erstellen**.
6. Wählen Sie die Ressourcengruppe **FW-Hybrid-Test** aus.
8. Wählen Sie unter **Region** denselben Standort aus wie zuvor.
1. Geben Sie als Name die Zeichenfolge **UDR-Hub-Spoke** ein.
9. Klicken Sie auf **Überprüfen + erstellen**.
10. Klicken Sie auf **Erstellen**.
11. Wählen Sie die erstellte Routingtabelle aus, um die Routingtabellenseite zu öffnen.
12. Wählen Sie in der linken Spalte die Option **Routen** aus.
13. Wählen Sie **Hinzufügen**.
14. Geben Sie als Routenname die Zeichenfolge **ToSpoke** ein.
15. Geben als Adresspräfix die Zeichenfolge **10.6.0.0/16** ein.
16. Wählen Sie als Typ des nächsten Hops die Option **Virtuelles Gerät** aus.
17. Geben Sie als Adresse des nächsten Hops die private IP-Adresse der Firewall ein, die Sie sich zuvor notiert haben.
18. Klicken Sie auf **OK**.

Ordnen Sie nun die Routingtabelle dem Subnetz zu.

1. Wählen Sie auf der Seite **UDR-Hub-Spoke – Routen** die Option **Subnetze** aus.
2. Wählen Sie **Zuordnen** aus.
3. Wählen Sie unter **Virtuelles Netzwerk** die Option **VNet-hub** aus.
1. Wählen Sie unter **Subnetz** die Option **GatewaySubnet** aus.
2. Klicken Sie auf **OK**.

Erstellen Sie nun die Standardroute aus dem Spoke-Subnetz.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Geben Sie **Routingtabelle** in das Textfeld für die Suche ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie **Routingtabelle** aus.
5. Klicken Sie auf **Erstellen**.
7. Wählen Sie die Ressourcengruppe **FW-Hybrid-Test** aus.
8. Wählen Sie unter **Region** denselben Standort aus wie zuvor.
1. Geben Sie als Name die Zeichenfolge **UDR-DG** ein.
4. Wählen Sie unter **Gatewayrouten verteilen** die Option **Nein** aus.
5. Klicken Sie auf **Überprüfen + erstellen**.
6. Klicken Sie auf **Erstellen**.
7. Wählen Sie die erstellte Routingtabelle aus, um die Routingtabellenseite zu öffnen.
8. Wählen Sie in der linken Spalte die Option **Routen** aus.
9. Wählen Sie **Hinzufügen**.
10. Geben Sie als Routenname die Zeichenfolge **ToHub** ein.
11. Geben Sie für das Adresspräfix **0.0.0.0/0** ein.
12. Wählen Sie als Typ des nächsten Hops die Option **Virtuelles Gerät** aus.
13. Geben Sie als Adresse des nächsten Hops die private IP-Adresse der Firewall ein, die Sie sich zuvor notiert haben.
14. Klicken Sie auf **OK**.

Ordnen Sie nun die Routingtabelle dem Subnetz zu.

1. Wählen Sie auf der Seite **UDR-DG – Routen** die Option **Subnetze** aus.
2. Wählen Sie **Zuordnen** aus.
3. Wählen Sie unter **Virtuelles Netzwerk** die Option **VNet-spoke** aus.
1. Wählen Sie unter **Subnetz** die Option **SN-Workload** aus.
2. Klicken Sie auf **OK**.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie nun die VM für die Spoke-Workload und die lokale VM, und platzieren Sie sie in den entsprechenden Subnetzen.

### <a name="create-the-workload-virtual-machine"></a>Erstellen des virtuellen Workloadcomputers

Erstellen Sie im virtuellen Spoke-Netzwerk einen virtuellen Computer ohne öffentliche IP-Adresse, auf dem IIS ausgeführt wird.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Beliebt** die Option **Windows Server 2016 Datacenter** aus.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:
    - **Ressourcengruppe**: Wählen Sie **FW-Hybrid-Test** aus.
    - **Name des virtuellen Computers**: *VM-Spoke-01*
    - **Region**: Die gleiche Region, die Sie auch zuvor verwendet haben.
    - **Benutzername**: \<type a user name\>.
    - **Kennwort**: \<type a password\>
4. Wählen Sie unter **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** und anschließend **HTTP (80)** und **RDP (3389)** aus.
4. Wählen Sie **Weiter: Datenträger**.
5. Übernehmen Sie die Standardeinstellungen, und wählen Sie **Weiter: Netzwerk** aus.
6. Wählen für das virtuelle Netzwerk die Option **VNet-Spoke** und für das Subnetz die Option **SN-Workload** aus.
7. Wählen Sie unter **Öffentliche IP** die Option **Keine** aus. 
9. Wählen Sie **Weiter: Verwaltung** aus.
10. Wählen Sie unter **Startdiagnose** die Option **Deaktivieren** aus.
11. Wählen Sie **Überprüfen + erstellen** aus, überprüfen Sie die Einstellungen auf der Zusammenfassungsseite, und wählen Sie anschließend **Erstellen** aus.

### <a name="install-iis"></a>Installieren von IIS

1. Öffnen Sie im Azure-Portal die Cloud Shell, und vergewissern Sie sich, dass **PowerShell** festgelegt ist.
2. Führen Sie den folgenden Befehl aus, um IIS auf dem virtuellen Computer zu installieren und ggf. den Speicherort zu ändern:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Erstellen der lokalen VM

Mit diesem virtuellen Computer wird eine Remotedesktopverbindung mit der öffentlichen IP-Adresse hergestellt. Von dort aus stellen Sie dann über die Firewall eine Verbindung mit dem lokalen Server her.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Beliebt** die Option **Windows Server 2016 Datacenter** aus.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:
    - **Ressourcengruppe**: Wählen Sie „Vorhandene verwenden“ und anschließend **FW-Hybrid-Test** aus.
    - **Name des virtuellen Computers** - *VM-Onprem*.
    - **Region**: Die gleiche Region, die Sie auch zuvor verwendet haben.
    - **Benutzername**: \<type a user name\>.
    - **Kennwort**: \<type a user password\>.
7. Wählen Sie unter **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** und anschließend **RDP (3389)** aus.
4. Wählen Sie **Weiter: Datenträger**.
5. Übernehmen Sie die Standardeinstellungen, und wählen Sie **Weiter: Netzwerk** aus.
6. Wählen Sie als virtuelles Netzwerk die Option **VNet-Onprem** und als Subnetz die Option **SN-Corp** aus.
8. Wählen Sie **Weiter: Verwaltung** aus.
10. Wählen Sie unter **Startdiagnose** die Option **Deaktivieren** aus.
10. Wählen Sie **Überprüfen + erstellen** aus, überprüfen Sie die Einstellungen auf der Zusammenfassungsseite, und wählen Sie anschließend **Erstellen** aus.

## <a name="test-the-firewall"></a>Testen der Firewall

1. Notieren Sie sich zuerst die private IP-Adresse für den virtuellen Computer **VM-spoke-01**.

2. Stellen Sie über das Azure-Portal eine Verbindung mit dem virtuellen Computer **VM-Onprem** her.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Öffnen Sie auf **VM-Onprem** einen Webbrowser, und navigieren Sie zu „http://\<VM-spoke-01 private IP\>“.

   Daraufhin sollte die Webseite **VM-spoke-01** angezeigt werden: ![Webseite „VM-spoke-01“](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Öffnen Sie auf dem virtuellen Computer **VM-Onprem** eine Remotedesktopverbindung mit **VM-spoke-01** an der privaten IP-Adresse.

   Die Verbindungsherstellung sollte erfolgreich sein, und Sie sollten sich anmelden können.

Damit haben Sie sich vergewissert, dass die Firewallregeln funktionieren:

<!---- You can ping the server on the spoke VNet.--->
- Sie können den Webserver im virtuellen Spoke-Netzwerk durchsuchen.
- Sie können per Remotedesktopprotokoll (RDP) eine Verbindung mit dem Server im virtuellen Spoke-Netzwerk herstellen.

Ändern Sie als Nächstes die Aktion zum Sammeln von Firewallnetzwerkregeln in **Verweigern**, um zu überprüfen, ob die Firewallregeln wie erwartet funktionieren.

1. Wählen Sie die Firewall **AzFW01** aus.
2. Wählen Sie **Regeln** aus.
3. Wählen Sie die Registerkarte **Netzwerkregelsammlung** und anschließend die Regelsammlung **RCNet01** aus.
4. Wählen Sie unter **Aktion** die Option **Verweigern** aus.
5. Wählen Sie **Speichern** aus.

Schließen Sie alle vorhandenen Remotedesktops, bevor Sie die geänderten Regeln testen. Führen Sie die Tests nun erneut durch. Dieses Mal sollten alle Tests fehlschlagen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **FW-Hybrid-Test** löschen, wenn Sie sie nicht mehr benötigen. Hierdurch werden alle firewallbezogenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie die Azure Firewall-Protokolle überwachen.

> [!div class="nextstepaction"]
> [Tutorial: Überwachen von Azure Firewall-Protokollen](./firewall-diagnostics.md)