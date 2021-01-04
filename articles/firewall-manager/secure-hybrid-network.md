---
title: 'Tutorial: Schützen Ihres virtuellen Hubnetzwerks per Azure Firewall Manager'
description: In diesem Tutorial erfahren Sie, wie Sie Ihr virtuelles Netzwerk mit Azure Firewall Manager über das Azure-Portal schützen.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 3d4d1e65c2200aee178abefb46d3e330acbd3108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023685"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Tutorial: Schützen Ihres virtuellen Hubnetzwerks per Azure Firewall Manager

Wenn Sie Ihr lokales Netzwerk mit einem virtuellen Azure-Netzwerk verbinden, um ein Hybridnetzwerk zu erstellen, ist das Steuern des Zugriffs auf Ihre Azure-Netzwerkressourcen ein wichtiger Bestandteil des globalen Sicherheitsplans.

Mit Azure Firewall Manager können Sie ein virtuelles Hubnetzwerk erstellen, um den für private IP-Adressen, für Azure-PaaS und für das Internet bestimmten Datenverkehr Ihres Hybridnetzwerks zu schützen. Mit Azure Firewall Manager können Sie den Netzwerkzugriff in einem Hybridnetzwerk mithilfe von Richtlinien steuern, die den zulässigen und unzulässigen Netzwerkdatenverkehr definieren.

Für Firewall Manager wird auch eine Architektur mit einem geschützten virtuellen Hub unterstützt. Einen Vergleich der Architekturtypen für geschützte virtuelle Hubs und virtuelle Hubnetzwerke finden Sie unter [Welche Architekturoptionen gibt es für Azure Firewall Manager?](vhubs-and-vnets.md).

In diesem Tutorial erstellen Sie drei virtuelle Netzwerke:

- **VNet-Hub**: In diesem virtuellen Netzwerk befindet sich die Firewall.
- **VNet-Spoke**: Das virtuelle Spoke-Netzwerk stellt die Workload in Azure dar.
- **VNet-Onprem**: Das lokale virtuelle Netzwerk stellt ein lokales Netzwerk dar. Bei einer tatsächlichen Bereitstellung kann die Verbindung dafür entweder mit einer VPN- oder einer ExpressRoute-Verbindung hergestellt werden. Der Einfachheit halber wird in diesem Tutorial eine VPN-Gatewayverbindung genutzt, und ein virtuelles Netzwerk in Azure wird als lokales Netzwerk verwendet.

![Hybrides Netzwerk](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Firewallrichtlinie
> * Erstellen der virtuellen Netzwerke
> * Konfigurieren und Bereitstellen der Firewall
> * Erstellen und Verbinden der VPN-Gateways
> * Durchführen des Peerings für die virtuellen Hub- und Spoke-Netzwerke
> * Erstellen der Routen
> * Erstellen der virtuellen Computer
> * Testen der Firewall


## <a name="prerequisites"></a>Voraussetzungen

Ein Hybridnetzwerk nutzt das Modell der Hub-and-Spoke-Architektur zur Weiterleitung von Datenverkehr zwischen Azure-VNETs und lokalen Netzwerken. Für die Hub-and-Spoke-Architektur gelten die folgenden Anforderungen:

- Legen Sie für das Peering von VNET-Hub mit VNET-Spoke **AllowGatewayTransit** fest. In der Hub-and-Spoke-Netzwerkarchitektur ermöglicht der Gatewaytransit die gemeinsame Nutzung des VPN-Gateways im Hub, anstatt VPN-Gateways in jedem virtuellen Spoke-Netzwerk bereitzustellen. 

   Darüber hinaus werden Routen zu den mit dem Gateway verbundenen virtuellen Netzwerken oder lokalen Netzwerken über den Gatewaytransit automatisch an die Routingtabellen für die virtuellen Netzwerke mit Peeringbeziehung verteilt. Weitere Informationen finden Sie unter [Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Legen Sie für das Peering von VNET-Spoke mit VNET-Hub **UseRemoteGateways** fest. Wenn **UseRemoteGateways** und für das Remotepeering **AllowGatewayTransit** festgelegt ist, verwendet das virtuelle Spoke-Netzwerk Gateways des virtuellen Remotenetzwerks für den Transit.
- Zum Weiterleiten des Spoke-Subnetzdatenverkehrs durch die Hub-Firewall benötigen Sie eine benutzerdefinierte Route (User Defined Route, UDR), die auf die Firewall verweist, während die Einstellung **Routenverteilung des Gateways für virtuelle Netzwerke** deaktiviert ist. Diese Option verhindert die Routenverteilung an die Spoke-Subnetze. Dadurch wird verhindert, dass erlernte Routen mit Ihrer UDR in Konflikt stehen.
- Konfigurieren Sie eine UDR im Hub-Gatewaysubnetz, die auf die Firewall-IP-Adresse als nächsten Hop auf dem Weg zu den Spoke-Netzwerken verweist. Für das Azure Firewall-Subnetz ist keine UDR erforderlich, da es die Routen über BGP erlernt.

Informationen zur Erstellung dieser Routen finden Sie in diesem Tutorial im Abschnitt [Erstellen von Routen](#create-the-routes).

>[!NOTE]
>Azure Firewall muss über eine direkte Internetverbindung verfügen. Wenn Ihr Subnetz „AzureFirewallSubnet“ eine Standardroute zu Ihrem lokalen Netzwerk über BGP erfasst, müssen Sie diese mit der benutzerdefinierten Route 0.0.0.0/0 überschreiben. Legen Sie dabei den Wert **NextHopType** auf **Internet** fest, um die direkte Internetkonnektivität beizubehalten.
>
>Azure Firewall kann so konfiguriert werden, dass die Tunnelerzwingung unterstützt wird. Weitere Informationen finden Sie unter [Azure Firewall-Tunnelerzwingung](../firewall/forced-tunneling.md).

>[!NOTE]
>Der Datenverkehr zwischen per direktem Peering verbundenen VNETs wird direkt weitergeleitet, auch wenn eine UDR auf Azure Firewall als Standardgateway verweist. Um in diesem Szenario Subnetz-zu-Subnetz-Datenverkehr an die Firewall zu senden, muss eine UDR explizit das Zielsubnetzwerk-Präfix in beiden Subnetzen enthalten.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-firewall-policy"></a>Erstellen einer Firewallrichtlinie

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Geben Sie in der Suchleiste im Azure-Portal den Suchbegriff **Firewall Manager** ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie auf der Azure Firewall Manager-Seite die Option **View Azure firewall policies** (Azure Firewall-Richtlinien anzeigen) aus.

   ![Firewallrichtlinie](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Wählen Sie die Option **Azure-Firewallrichtlinie erstellen** aus.
1. Wählen Sie Ihr Abonnement aus. Wählen Sie anschließend unter „Ressourcengruppe“ die Option **Neu erstellen** aus, und erstellen Sie eine Ressourcengruppe mit dem Namen **FW-Hybrid-Test**.
2. Geben Sie **Pol-Net01** als Name der Richtlinie ein.
3. Wählen Sie **USA, Osten** als Region aus.
4. Wählen Sie **Weiter: Regeln** aus.
5. Wählen Sie **Regelsammlung hinzufügen** aus.
6. Geben Sie unter **Name** den Namen **RCNet01** ein.
7. Wählen Sie unter **Regelsammlungstyp** die Option **Netzwerk** aus.
8. Geben Sie unter **Priorität** den Wert **100** ein.
9. Wählen Sie für **Aktion** die Option **Zulassen** aus.
10. Geben Sie unter **Regeln** für **Name** die Zeichenfolge **AllowWeb** ein.
11. Geben Sie unter **Quelladressen** die Zeichenfolge **192.168.1.0/24** ein.
12. Wählen Sie für **Protokoll** die Option **TCP** aus.
13. Geben Sie unter **Zielports** den Wert **80** ein.
14. Wählen Sie unter **Zieltyp** die Option **IP-Adresse** aus.
15. Geben Sie unter **Ziel** die Adresse **10.6.0.0/16** ein.
16. Geben Sie in der nächsten Regelzeile die folgenden Informationen ein:
 
    Name: Geben Sie **AllowRDP** ein.<br>
    Quell-IP-Adresse: Geben Sie **192.168.1.0/24** ein.<br>
    Protokoll: Wählen Sie **TCP** aus.<br>
    Zielports: Geben Sie **3389** ein.<br>
    Zieltyp: Wählen Sie **IP-Adresse** aus.<br>
    Geben Sie als Ziel die Adresse **10.6.0.0/16** ein.

1. Wählen Sie **Hinzufügen**.
2. Klicken Sie auf **Überprüfen + erstellen**.
3. Überprüfen Sie die Angaben, und wählen Sie dann **Erstellen** aus.

## <a name="create-the-firewall-hub-virtual-network"></a>Erstellen des virtuellen Firewall-Hub-Netzwerks

> [!NOTE]
> Die Größe des Subnetzes „AzureFirewallSubnet“ beträgt /26. Weitere Informationen zur Subnetzgröße finden Sie unter [Azure Firewall – Häufig gestellte Fragen](../firewall/firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Netzwerk** die Option **Virtuelles Netzwerk** aus.
4. Geben Sie unter **Name** den Namen **VNet-hub** ein.
5. Geben Sie unter **Adressraum** den Adressraum **10.5.0.0/16** ein.
6. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
7. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe **FW-Hybrid-Test** aus.
8. Wählen Sie unter **Standort** die Option **USA, Osten** aus.
9. Geben Sie unter **Subnetz** als **Name** die Zeichenfolge **AzureFirewallSubnet** ein. Die Firewall befindet sich diesem Subnetz, und der Subnetzname **muss** „AzureFirewallSubnet“ lauten.
10. Geben Sie unter **Adressbereich** den Adressbereich **10.5.0.0/26** ein. 
11. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und wählen Sie dann **Erstellen** aus.

## <a name="create-the-spoke-virtual-network"></a>Erstellen des virtuellen Spoke-Netzwerks

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Netzwerk** die Option **Virtuelles Netzwerk** aus.
4. Geben Sie unter **Name** den Namen **VNet-Spoke** ein.
5. Geben Sie unter **Adressraum** den Adressraum **10.6.0.0/16** ein.
6. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
7. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe **FW-Hybrid-Test** aus.
8. Wählen Sie unter **Standort** den gleichen Standort aus wie zuvor.
9. Geben Sie unter **Subnetz** für **Name** den Namen **SN-Workload** ein.
10. Geben Sie unter **Adressbereich** den Adressbereich **10.6.0.0/24** ein.
11. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und wählen Sie dann **Erstellen** aus.

## <a name="create-the-on-premises-virtual-network"></a>Erstellen des lokalen virtuellen Netzwerks

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Netzwerk** die Option **Virtuelles Netzwerk** aus.
4. Geben Sie unter **Name** den Namen **VN-OnPrem** ein.
5. Geben Sie unter **Adressraum** die Zeichenfolge **192.168.0.0/16** ein.
6. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
7. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe **FW-Hybrid-Test** aus.
8. Wählen Sie unter **Standort** den gleichen Standort aus wie zuvor.
9. Geben Sie unter **Subnetz** für **Name** den Namen **SN-Corp** ein.
10. Geben Sie unter **Adressbereich** die Zeichenfolge **192.168.1.0/24** ein.
11. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und wählen Sie dann **Erstellen** aus.

Erstellen Sie nach der Bereitstellung des virtuellen Netzwerks ein zweites Subnetz für das Gateway.

1. Wählen Sie auf der Seite **VNet-Onprem** die Option **Subnetze** aus.
2. Wählen Sie **+ Subnetz** aus.
3. Geben Sie unter **Name** den Namen **GatewaySubnet** ein.
4. Geben Sie unter **Adressbereich (CIDR-Block)** den Adressbereich **192.168.2.0/24** ein.
5. Klicken Sie auf **OK**.

### <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Dies ist die öffentliche IP-Adresse für das lokale Gateway.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Geben Sie **öffentliche IP-Adresse** in das Textfeld für die Suche ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie **Öffentliche IP-Adresse** und anschließend **Erstellen** aus.
4. Geben als Name die Zeichenfolge **VNet-Onprem-GW-pip** ein.
5. Geben Sie als Ressourcengruppe die Zeichenfolge **FW-Hybrid-Test** ein.
6. Wählen Sie unter **Standort** die Option **USA, Osten** aus.
7. Übernehmen Sie für die anderen Optionen die Standardwerte, und wählen Sie **Erstellen** aus.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurieren und Bereitstellen der Firewall

Wenn Sicherheitsrichtlinien einem Hub zugeordnet werden, wird dies als *virtuelles Hubnetzwerk* bezeichnet.

Wandeln Sie das virtuelle Netzwerk **VNet-Hub** in ein *virtuelles Hubnetzwerk* um, und schützen Sie es per Azure Firewall.

1. Geben Sie in der Suchleiste im Azure-Portal den Suchbegriff **Firewall Manager** ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie auf der Azure Firewall Manager-Seite unter **Add security to virtual networks** (Sicherheit virtueller Netzwerke erhöhen) die Option **View hub virtual networks** (Virtuelle Hubnetzwerke anzeigen) aus.
4. Wählen Sie **Convert virtual networks** (Virtuelle Netzwerke konvertieren) aus.
5. Wählen Sie **VNet-hub** und dann **Weiter: Azure Firewall** aus.
6. Wählen Sie unter **Firewallrichtlinie** die Option **Pol-Net01** aus.
7. Wählen Sie **Weiter: Überprüfen und bestätigen** aus.
8. Überprüfen Sie die Angaben, und wählen Sie dann **Bestätigen** aus.


   Die Bereitstellung dauert einige Minuten.
7. Navigieren Sie nach Abschluss der Bereitstellung zur Ressourcengruppe **FW-Hybrid-Test**, und wählen Sie die Firewall aus.
9. Notieren Sie sich auf der Seite **Übersicht** die **Private Firewall-IP-Adresse**. Diese wird später für die Erstellung der Standardroute benötigt.

## <a name="create-and-connect-the-vpn-gateways"></a>Erstellen und Verbinden der VPN-Gateways

Das virtuelle Hub-Netzwerk und das lokale virtuelle Netzwerk werden über VPN-Gateways verbunden.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Erstellen eines VPN-Gateways für das virtuelle Hub-Netzwerk

Erstellen Sie nun das VPN-Gateway für das virtuelle Hub-Netzwerk. Netzwerk-zu-Netzwerk-Konfigurationen erfordern einen routenbasierten VPN-Typ. Häufig kann die Erstellung eines VPN-Gateways je nach ausgewählter VPN-Gateway-SKU mindestens 45 Minuten dauern.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Geben Sie **Gateway für virtuelle Netzwerke** in das Textfeld für die Suche ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie **Gateway für virtuelle Netzwerke** und anschließend **Erstellen** aus.
4. Geben Sie unter **Name** den Namen **GW-hub** ein.
5. Wählen Sie unter **Region** die Option **(USA) USA, Osten** aus.
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
5. Wählen Sie unter **Region** die Option **(USA) USA, Osten** aus.
6. Wählen Sie unter **Gatewaytyp** die Option **VPN** aus.
7. Wählen Sie unter **VPN-Typ** die Option **Routenbasiert** aus.
8. Wählen Sie unter **SKU** die Option **Basic** aus.
9. Wählen Sie unter **Virtuelles Netzwerk** die Option **VNet-Onprem** aus.
10. Wählen Sie unter **Öffentliche IP-Adresse** die Option *Vorhandenes verwenden* und **VNet-Onprem-GW-pip** als Name aus.
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
4. Geben Sie als Verbindungsname die Zeichenfolge **Onprem-to-Hub** ein.
5. Wählen Sie unter **Verbindungstyp** die Option **VNet-zu-VNet** aus.
6. Wählen Sie für das **zweite Gateway für virtuelle Netzwerke** die Option **GW-hub** aus.
7. Geben Sie unter **Gemeinsam verwendeter Schlüssel (PSK)** die Zeichenfolge **AzureA1b2C3** ein.
8. Klicken Sie auf **OK**.


#### <a name="verify-the-connection"></a>Überprüfen der Verbindung

Nach etwa fünf Minuten sollte der Status beider Verbindungen **Verbunden** lauten.

![Gatewayverbindungen](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Durchführen des Peerings für die virtuellen Hub- und Spoke-Netzwerke

Führen Sie nun das Peering für die virtuellen Hub- und Spoke-Netzwerke durch.

1. Öffnen Sie die Ressourcengruppe **FW-Hybrid-Test**, und wählen Sie das virtuelle Netzwerk **VNet-hub** aus.
2. Wählen Sie in der linken Spalte **Peerings** aus.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie unter **Name** den Namen **HubtoSpoke** ein.
5. Wählen Sie unter **Virtuelles Netzwerk** die Option **VNet-spoke** aus.
6. Geben Sie als Name für das Peering von „VNetSpoke“ zu „VNet-hub“ die Zeichenfolge **SpoketoHub** ein.
7. Aktivieren Sie **Gatewaytransit zulassen**.
8. Klicken Sie auf **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Konfigurieren zusätzlicher Einstellungen für das Peering „SpoketoHub“

Für das Peering „SpoketoHub“ muss die Einstellung **Weitergeleiteten Datenverkehr zulassen** aktiviert werden.

1. Öffnen Sie die Ressourcengruppe **FW-Hybrid-Test**, und wählen Sie das virtuelle Netzwerk **VNet-Spoke** aus.
2. Wählen Sie in der linken Spalte **Peerings** aus.
3. Wählen Sie das Peering **SpoketoHub** aus.
4. Wählen Sie unter **Weitergeleiteten Datenverkehr von "VNet-hub" nach "VNet-Spoke" zulassen** die Option **Aktiviert** aus.
5. Wählen Sie **Speichern** aus.

## <a name="create-the-routes"></a>Erstellen der Routen

Erstellen Sie als Nächstes zwei Routen:

- Eine Route vom Hub-Gatewaysubnetz zum Spoke-Subnetz über die Firewall-IP-Adresse
- Eine Standardroute vom Spoke-Subnetz über die Firewall-IP-Adresse

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Geben Sie **Routingtabelle** in das Textfeld für die Suche ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie **Routingtabelle** aus.
4. Klicken Sie auf **Erstellen**.
5. Geben Sie als Name die Zeichenfolge **UDR-Hub-Spoke** ein.
6. Wählen Sie die Ressourcengruppe **FW-Hybrid-Test** aus.
8. Wählen Sie unter **Standort** die Option **(USA) USA, Osten** aus.
9. Klicken Sie auf **Erstellen**.
10. Wählen Sie die erstellte Routingtabelle aus, um die Routingtabellenseite zu öffnen.
11. Wählen Sie in der linken Spalte die Option **Routen** aus.
12. Wählen Sie **Hinzufügen**.
13. Geben Sie als Routenname die Zeichenfolge **ToSpoke** ein.
14. Geben als Adresspräfix die Zeichenfolge **10.6.0.0/16** ein.
15. Wählen Sie als Typ des nächsten Hops die Option **Virtuelles Gerät** aus.
16. Geben Sie als Adresse des nächsten Hops die private IP-Adresse der Firewall ein, die Sie sich zuvor notiert haben.
17. Klicken Sie auf **OK**.

Ordnen Sie nun die Routingtabelle dem Subnetz zu.

1. Wählen Sie auf der Seite **UDR-Hub-Spoke – Routen** die Option **Subnetze** aus.
2. Wählen Sie **Zuordnen** aus.
4. Wählen Sie unter **Virtuelles Netzwerk** die Option **VNet-hub** aus.
5. Wählen Sie unter **Subnetz** die Option **GatewaySubnet** aus.
6. Klicken Sie auf **OK**.

Erstellen Sie nun die Standardroute aus dem Spoke-Subnetz.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Geben Sie **Routingtabelle** in das Textfeld für die Suche ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie **Routingtabelle** aus.
5. Klicken Sie auf **Erstellen**.
6. Geben Sie als Name die Zeichenfolge **UDR-DG** ein.
7. Wählen Sie die Ressourcengruppe **FW-Hybrid-Test** aus.
8. Wählen Sie unter **Standort** die Option **(USA) USA, Osten** aus.
4. Wählen Sie für **Routenverteilung des Gateways für virtuelle Netzwerke** die Option **Deaktiviert** aus.
1. Klicken Sie auf **Erstellen**.
2. Wählen Sie die erstellte Routingtabelle aus, um die Routingtabellenseite zu öffnen.
3. Wählen Sie in der linken Spalte die Option **Routen** aus.
4. Wählen Sie **Hinzufügen**.
5. Geben Sie als Routenname die Zeichenfolge **ToHub** ein.
6. Geben Sie für das Adresspräfix **0.0.0.0/0** ein.
7. Wählen Sie als Typ des nächsten Hops die Option **Virtuelles Gerät** aus.
8. Geben Sie als Adresse des nächsten Hops die private IP-Adresse der Firewall ein, die Sie sich zuvor notiert haben.
9. Klicken Sie auf **OK**.

Ordnen Sie nun die Routingtabelle dem Subnetz zu.

1. Wählen Sie auf der Seite **UDR-DG – Routen** die Option **Subnetze** aus.
2. Wählen Sie **Zuordnen** aus.
4. Wählen Sie unter **Virtuelles Netzwerk** die Option **VNet-spoke** aus.
5. Wählen Sie unter **Subnetz** die Option **SN-Workload** aus.
6. Klicken Sie auf **OK**.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie nun die VM für die Spoke-Workload und die lokale VM, und platzieren Sie sie in den entsprechenden Subnetzen.

### <a name="create-the-workload-virtual-machine"></a>Erstellen des virtuellen Workloadcomputers

Erstellen Sie im virtuellen Spoke-Netzwerk einen virtuellen Computer ohne öffentliche IP-Adresse, auf dem IIS ausgeführt wird.

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
2. Wählen Sie unter **Beliebt** die Option **Windows Server 2016 Datacenter** aus.
3. Geben Sie die folgenden Werte für den virtuellen Computer ein:
    - **Ressourcengruppe**: Wählen Sie **FW-Hybrid-Test** aus.
    - **Name des virtuellen Computers**: *VM-Spoke-01*
    - **Region** -  *(USA) USA, Osten*.
    - **Benutzername**: *azureuser*
    - **Kennwort**: Geben Sie Ihr Kennwort ein.

4. Wählen Sie **Weiter: Datenträger**.
5. Übernehmen Sie die Standardeinstellungen, und wählen Sie **Weiter: Netzwerk** aus.
6. Wählen für das virtuelle Netzwerk die Option **VNet-Spoke** und für das Subnetz die Option **SN-Workload** aus.
7. Wählen Sie unter **Öffentliche IP** die Option **Keine** aus.
8. Wählen Sie unter **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** und anschließend **HTTP (80)** und **RDP (3389)** aus.
9. Wählen Sie **Weiter: Verwaltung** aus.
10. Wählen Sie unter **Startdiagnose** die Option **Aus** aus.
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
    - **Region** -  *(USA) USA, Osten*.
    - **Benutzername**: *azureuser*
    - **Kennwort**: Geben Sie Ihr Kennwort ein.

4. Wählen Sie **Weiter: Datenträger**.
5. Übernehmen Sie die Standardeinstellungen, und wählen Sie **Weiter: Netzwerk** aus.
6. Wählen Sie als virtuelles Netzwerk die Option **VNet-Onprem** aus, und vergewissern Sie sich, dass **SN-Corp** als Subnetz angegeben ist.
7. Wählen Sie unter **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** und anschließend **RDP (3389)** aus.
8. Wählen Sie **Weiter: Verwaltung** aus.
9. Wählen Sie unter **Startdiagnose** die Option **Aus** aus.
10. Wählen Sie **Überprüfen + erstellen** aus, überprüfen Sie die Einstellungen auf der Zusammenfassungsseite, und wählen Sie anschließend **Erstellen** aus.

## <a name="test-the-firewall"></a>Testen der Firewall

1. Notieren Sie sich zunächst auf der Seite mit der Übersicht für „VM-Spoke-01“ die private IP-Adresse für den virtuellen Computer „VM-Spoke-01“.

2. Stellen Sie über das Azure-Portal eine Verbindung mit dem virtuellen Computer **VM-Onprem** her.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Öffnen Sie auf **VM-Onprem** einen Webbrowser, und navigieren Sie zu „http://\<VM-spoke-01 private IP\>“.

   Daraufhin sollte die Webseite **VM-spoke-01** angezeigt werden: ![Webseite „VM-spoke-01“](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Öffnen Sie auf dem virtuellen Computer **VM-Onprem** eine Remotedesktopverbindung mit **VM-spoke-01** an der privaten IP-Adresse.

   Die Verbindungsherstellung sollte erfolgreich sein, und Sie sollten sich anmelden können.

Damit haben Sie sich vergewissert, dass die Firewallregeln funktionieren:

<!---- You can ping the server on the spoke VNet.--->
- Sie können den Webserver im virtuellen Spoke-Netzwerk durchsuchen.
- Sie können per Remotedesktopprotokoll (RDP) eine Verbindung mit dem Server im virtuellen Spoke-Netzwerk herstellen.

Ändern Sie als Nächstes die Aktion zum Sammeln von Firewallnetzwerkregeln in **Verweigern**, um zu überprüfen, ob die Firewallregeln wie erwartet funktionieren.

1. Öffnen Sie die Ressourcengruppe **FW-Hybrid-Test**, und wählen Sie die Firewallrichtlinie **Pol-Net01** aus.
2. Wählen Sie unter **Einstellungen** die Option **Regeln** aus.
3. Wählen Sie unter **Netzwerkregeln** die Regelsammlung **RCNet01**, die Auslassungspunkte (...) und dann die Option **Bearbeiten** aus.
4. Wählen Sie unter **Regelsammlungsaktion** die Option **Verweigern** aus.
5. Wählen Sie **Speichern** aus.

Schließen Sie alle vorhandenen Remotedesktops und Browser auf **VM-Onprem**, bevor Sie die geänderten Regeln testen. Führen Sie die Tests nach Abschluss des Updates der Regelsammlung erneut durch. Dieses Mal sollten alle Tests fehlschlagen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **FW-Hybrid-Test** löschen, wenn Sie sie nicht mehr benötigen. Hierdurch werden alle firewallbezogenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Schützen Ihres virtuellen WAN mithilfe von Azure Firewall Manager](secure-cloud-network.md)