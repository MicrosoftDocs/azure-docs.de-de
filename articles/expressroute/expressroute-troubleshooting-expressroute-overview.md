---
title: 'Azure ExpressRoute: Überprüfen der Konnektivität – Handbuch zur Problembehandlung'
description: Diese Seite enthält Anleitungen für die Problembehandlung und Überprüfung der lückenlosen Konnektivität einer ExpressRoute-Verbindung.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: af4ef156cccded6afe2db09628446a6ffe1ad53a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "92204638"
---
# <a name="verifying-expressroute-connectivity"></a>Überprüfen der ExpressRoute-Konnektivität
Dieser Artikel hilft Ihnen beim Sicherstellen der ExpressRoute-Konnektivität und beim Beheben von Problemen. ExpressRoute erweitert Ihr lokales Netzwerk über eine private Verbindung, die meistens von einem Konnektivitätsanbieter bereitgestellt wird, in die Microsoft Cloud. Die ExpressRoute-Konnektivität umfasst in der Regel drei verschiedene Netzwerkzonen:

-   Kundennetzwerk
-   Anbieternetzwerk
-   Microsoft-Datencenter

> [!NOTE]
> Über das direkte ExpressRoute-Konnektivitätsmodell (mit einer Bandbreite von 10/100 GBit/s) können Kunden eine direkte Verbindung mit dem Port des Microsoft Enterprise Edge-Routers (MSEE) herstellen. Daher umfasst das direkte Konnektivitätsmodell nur Netzwerkzonen für den Kunden und für Microsoft.
>


Anhand dieses Dokuments kann der Benutzer feststellen, ob und wo ein Konnektivitätsproblem aufgetreten ist. Dabei kann er sich auch an das zuständige Team wenden, um Unterstützung zur Behebung eines Problems zu erhalten. Wenn Sie zum Lösen eines Problems Unterstützung von Microsoft benötigt wird, können Sie ein Supportticket beim [Microsoft-Support][Support] erstellen.

> [!IMPORTANT]
> Dieses Dokument soll Ihnen als Hilfe beim Diagnostizieren und Beheben einfacher Probleme dienen. Es dient nicht als Ersatz für Microsoft Support. Erstellen Sie ein Supportticket beim [Microsoft-Support][Support], wenn Sie Ihr Problem nicht anhand der angegebenen Anleitung beheben können.
>
>

## <a name="overview"></a>Übersicht
Das folgende Diagramm zeigt die logische Verbindung eines Kundennetzwerks mit einem Microsoft-Netzwerk per ExpressRoute.
[![1]][1]

Im obigen Diagramm stehen die Zahlen für wichtige Netzwerkpunkte. Auf die Netzwerkpunkte wird in diesem Artikel an einigen Stellen anhand der zugeordneten Nummer verwiesen. Je nach ExpressRoute-Konnektivitätsmodell (Cloud Exchange Co-Location, Point-to-Point-Ethernet-Verbindung oder Any-to-Any (IPVPN)) können die Netzwerkpunkte 3 und 4 Switches (Layer-2-Geräte) oder Router (Layer-3-Geräte) sein. Im direkten Konnektivitätsmodell sind die Netzwerkpunkte 3 und 4 nicht vorhanden. Stattdessen sind CEs (2) über Dark Fiber direkt mit MSEEs verbunden. Die wichtigen dargestellten Netzwerkpunkte sind:

1.  Computegerät des Kunden (z.B. ein Server oder PC)
2.  CEs: Edge-Router des Kunden 
3.  PEs (mit CE-Verbindung): Edge-Router/-Switches des Anbieters mit Verbindung mit Edge-Routern des Kunden. Sie werden in diesem Dokument als „PE-CEs“ bezeichnet.
4.  PEs (mit MSEE-Verbindung): Edge-Router/-Switches des Anbieters mit Verbindung mit MSEEs. Sie werden in diesem Dokument als „PE-MSEEs“ bezeichnet.
5.  MSEEs: MSEE-ExpressRoute-Router (Microsoft Enterprise Edge)
6.  Virtual Network (VNet) Gateway
7.  Computegerät im Azure VNet

Wenn die Konnektivitätsmodelle „Cloud Exchange Co-Location“, „Point-to-Point-Ethernet“ oder „Direct“ verwendet werden, richten die CEs (2) BGP-Peering mit MSEEs (5) ein. 

Bei Verwendung des Any-to-Any-Konnektivitätsmodells (IPVPN) richten die PE-MSEEs (4) BGP-Peering mit MSEEs (5) ein. Von Microsoft empfangene Routen werden dann von PE-MSEEs über das Netzwerk des IPVPN-Dienstanbieters an das Kundennetzwerk zurückgegeben.

> [!NOTE]
>Um Hochverfügbarkeit zu gewährleisten, stellt Microsoft eine vollständig redundante parallele Konnektivität zwischen MSEE (5)- und PE-MSEE (4)-Paaren her. Es empfiehlt sich auch, zwischen dem Kundennetzwerk- und PE-CE-Paar einen vollständig redundanten parallelen Netzwerkpfad einzurichten. Weitere Informationen zur Hochverfügbarkeit finden Sie im Artikel [Entwurf für Hochverfügbarkeit mit ExpressRoute][HA]
>
>

Im Folgenden sind die logischen Schritte zur Problembehandlung der ExpressRoute-Verbindung aufgeführt:

* [Überprüfen von Verbindungsbereitstellung und -zustand](#verify-circuit-provisioning-and-state)
  
* [Überprüfen der Peeringkonfiguration](#validate-peering-configuration)
  
* [Überprüfen von ARP](#validate-arp)
  
* [Überprüfen von BGP und Routen auf dem MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Bestätigen des Datenverkehrsflusses](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Überprüfen von Verbindungsbereitstellung und -zustand
Beim Bereitstellen einer ExpressRoute-Verbindung werden redundante Layer-2-Verbindungen zwischen CEs/PE-MSEEs (2)/(4) und MSEEs (5) hergestellt. Weitere Informationen dazu, wie Sie eine ExpressRoute-Verbindung erstellen, ändern, bereitstellen und überprüfen, finden Sie im Artikel [Erstellen und Ändern einer ExpressRoute-Verbindung][CreateCircuit].

>[!TIP]
>Mit einem Dienstschlüssel wird eine ExpressRoute-Verbindung eindeutig identifiziert. Falls Sie von Microsoft oder von einem ExpressRoute-Partnerunternehmen Hilfe zur Behebung eines ExpressRoute-Problems benötigen, sollten Sie den Dienstschlüssel angeben, um die Verbindung problemlos zu identifizieren.
>
>

### <a name="verification-via-the-azure-portal"></a>Überprüfung über das Azure-Portal
Öffnen Sie im Azure-Portal das Blatt der ExpressRoute-Verbindung. In Abschnitt ![3][3] des Blatts sind die wichtigsten ExpressRoute-Informationen aufgeführt, wie im folgenden Screenshot dargestellt:

![4][4]    

Auf dem ExpressRoute-Blatt „Zusammenfassung“ ist unter *Schaltkreisstatus* der Status der Verbindung auf Microsoft-Seite angegeben. Unter *Anbieterstatus* ist angegeben, ob für die Verbindung auf Service Provider-Seite *Bereitgestellt/Nicht bereitgestellt* gilt. 

Damit eine ExpressRoute-Verbindung betriebsbereit ist, muss *Schaltkreisstatus* auf *Aktiviert* und *Anbieterstatus* auf *Bereitgestellt* festgelegt sein.

> [!NOTE]
> Wenn *Leitungsstatus* nach der Konfiguration einer ExpressRoute-Verbindung immer noch den Status „Nicht aktiviert“ aufweist, wenden Sie sich bitte an den [Microsoft-Support][Support]. Weist dagegen *Anbieterstatus* immer noch den Status „Nicht bereitgestellt“ auf, wenden Sie sich bitte an Ihren Dienstanbieter.
>
>

### <a name="verification-via-powershell"></a>Überprüfung per PowerShell
Verwenden Sie den folgenden Befehl, um alle ExpressRoute-Verbindungen in einer Ressourcengruppe aufzulisten:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>Wenn Sie den Namen einer Ressourcengruppe suchen, können Sie ihn durch Auflisten aller Ressourcengruppen in Ihrem Abonnement ermitteln. Verwenden Sie dazu den Befehl *Get-AzResourceGroup*.
>


Verwenden Sie den folgenden Befehl, um eine bestimmte ExpressRoute-Verbindung in einer Ressourcengruppe auszuwählen:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

Eine Beispielantwort lautet:

```output
Name                             : Test-ER-Ckt
ResourceGroupName                : Test-ER-RG
Location                         : westus2
Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_UnlimitedData",
                                    "Tier": "Standard",
                                    "Family": "UnlimitedData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : {
                                    "ServiceProviderName": "****",
                                    "PeeringLocation": "******",
                                    "BandwidthInMbps": 100
                                    }
ServiceKey                       : **************************************
Peerings                         : []
Authorizations                   : []
```

Achten Sie besonders auf die folgenden Felder, um zu überprüfen, ob eine ExpressRoute-Verbindung betriebsbereit ist:

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> Wenn *Leitungsstatus* nach der Konfiguration einer ExpressRoute-Verbindung immer noch den Status „Nicht aktiviert“ aufweist, wenden Sie sich bitte an den [Microsoft-Support][Support]. Weist dagegen *Anbieterstatus* immer noch den Status „Nicht bereitgestellt“ auf, wenden Sie sich bitte an Ihren Dienstanbieter.
>
>

## <a name="validate-peering-configuration"></a>Überprüfen der Peeringkonfiguration
Nachdem der Dienstanbieter die Bereitstellung der ExpressRoute-Verbindung abgeschlossen hat, können über die ExpressRoute-Verbindung zwischen CEs/MSEE-PEs (2)/(4) und MSEEs (5) mehrere eBGP-basierte Routingkonfigurationen erstellt werden. Jede ExpressRoute-Verbindung kann zwei Zustände aufweisen: Privates Azure-Peering (Datenverkehr zu privaten virtuellen Netzwerken in Azure) und/oder (Datenverkehr zu öffentlichen PaaS- und SaaS-Endpunkten). Weitere Informationen dazu, wie Sie die Routingkonfiguration erstellen und ändern, finden Sie im Artikel [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Überprüfung über das Azure-Portal

> [!NOTE]
> Beim IPVPN-Konnektivitätsmodell übernehmen Dienstanbieter die Verantwortung für die Konfiguration der Peeringvorgänge (Layer-3-Dienste). Nachdem der Dienstanbieter das Peering konfiguriert hat und das Peering im Portal leer ist, sollten Sie bei einem solchen Modell versuchen, die Verbindungskonfiguration über die Schaltfläche „Aktualisieren“ im Portal zu aktualisieren. Durch diesen Vorgang wird von Ihrer Verbindung die aktuelle Routingkonfiguration abgerufen. 
>

Im Azure-Portal können Sie den Peeringstatus einer ExpressRoute-Verbindung auf dem Blatt der ExpressRoute-Verbindung überprüfen. In Abschnitt ![3][3] des Blatts sind die ExpressRoute-Peerings aufgeführt, wie im folgenden Screenshot dargestellt:

![5][5]

Im vorherigen Beispiel wurde wie angegeben das private Azure-Peering bereitgestellt, während öffentliche Azure-Peerings und Microsoft-Peerings nicht bereitgestellt werden. Für einen erfolgreich bereitgestellten Peeringkontext wären auch die primären und sekundären Point-to-Point-Subnetze aufgelistet. Die /30-Subnetze werden für die Schnittstellen-IP-Adresse der MSEEs und CEs/PE-MSEEs verwendet. Für die bereitgestellten Peerings gibt die Liste auch an, wer die Konfiguration zuletzt geändert hat. 

> [!NOTE]
> Wenn ein Peering nicht aktiviert wird, sollten Sie überprüfen, ob die zugewiesenen primären und sekundären Subnetze mit der Konfiguration auf dem verknüpften CE/PE-MSEE übereinstimmen. Überprüfen Sie auch, ob die Angaben für *VlanId*, *AzureASN* und *PeerASN* auf den MSEEs stimmen und ob diese Werte zu den Werten auf dem verknüpften CE/PE-MSEE passen. Wenn MD5-Hashing gewählt wird, sollte der gemeinsam verwendete Schlüssel für das MSEE- und PE-MSEE/CE-Paar gleich sein. Der zuvor konfigurierte gemeinsam verwendete Schlüssel wird aus Sicherheitsgründen nicht angezeigt. Wenn Sie die Konfiguration auf MSEE-Routern ändern müssen, helfen Ihnen die Informationen unter [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering] weiter.  
>

> [!NOTE]
> Bei einem/30-Subnetz, das der Schnittstelle zugewiesen wird, wählt Microsoft die zweite verwendbare IP-Adresse des Subnetzes für die MSEE-Schnittstelle aus. Stellen Sie daher sicher, dass die erste verwendbare IP-Adresse des Subnetzes auf dem Peer-CE/PE-MSEE zugewiesen wurde.
>


### <a name="verification-via-powershell"></a>Überprüfung per PowerShell
Verwenden Sie die folgenden Befehle, um die Konfigurationsdetails für das private Azure-Peering zu erhalten:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

Eine Beispielantwort für ein erfolgreich konfiguriertes privates Peering lautet:

```output
Name                       : AzurePrivatePeering
Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
Etag                       : W/"################################"
PeeringType                : AzurePrivatePeering
AzureASN                   : 12076
PeerASN                    : 123##
PrimaryPeerAddressPrefix   : 172.16.0.0/30
SecondaryPeerAddressPrefix : 172.16.0.4/30
PrimaryAzurePort           : 
SecondaryAzurePort         : 
SharedKey                  : 
VlanId                     : 200
MicrosoftPeeringConfig     : null
ProvisioningState          : Succeeded
```

 Für einen erfolgreich aktivierten Peeringkontext wären die primären und sekundären Adresspräfixe aufgeführt. Die /30-Subnetze werden für die Schnittstellen-IP-Adresse der MSEEs und CEs/PE-MSEEs verwendet.

Verwenden Sie die folgenden Befehle, um die Konfigurationsdetails für das öffentliche Azure-Peering zu erhalten:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

Verwenden Sie die folgenden Befehle, um die Konfigurationsdetails für das Microsoft-Peering zu erhalten:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

Wenn ein Peering nicht konfiguriert ist, wird eine Fehlermeldung angezeigt. Eine Beispielantwort für den Fall, in dem das angegebene Peering (hier öffentliches Azure-Peering) nicht für die Verbindung konfiguriert ist, lautet:

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> Wenn ein Peering nicht aktiviert wird, sollten Sie überprüfen, ob die zugewiesenen primären und sekundären Subnetze mit der Konfiguration auf dem verknüpften CE/PE-MSEE übereinstimmen. Überprüfen Sie auch, ob die Angaben für *VlanId*, *AzureASN* und *PeerASN* auf den MSEEs stimmen und ob diese Werte zu den Werten auf dem verknüpften CE/PE-MSEE passen. Wenn MD5-Hashing gewählt wird, sollte der gemeinsam verwendete Schlüssel für das MSEE- und PE-MSEE/CE-Paar gleich sein. Der zuvor konfigurierte gemeinsam verwendete Schlüssel wird aus Sicherheitsgründen nicht angezeigt. Wenn Sie die Konfiguration auf MSEE-Routern ändern müssen, helfen Ihnen die Informationen unter [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering] weiter.  
>
>

> [!NOTE]
> Bei einem/30-Subnetz, das der Schnittstelle zugewiesen wird, wählt Microsoft die zweite verwendbare IP-Adresse des Subnetzes für die MSEE-Schnittstelle aus. Stellen Sie daher sicher, dass die erste verwendbare IP-Adresse des Subnetzes auf dem Peer-CE/PE-MSEE zugewiesen wurde.
>

## <a name="validate-arp"></a>Überprüfen von ARP

Die ARP-Tabelle ermöglicht eine Zuordnung der IP-Adresse und MAC-Adresse für ein bestimmtes Peering. Die ARP-Tabelle für ein ExpressRoute-Verbindungspeering enthält die folgenden Informationen für jede (primäre und sekundäre) Schnittstelle:
* Zuordnung der IP-Adresse der lokalen Routerschnittstelle zur MAC-Adresse
* Zuordnung der IP-Adresse der ExpressRoute-Routerschnittstelle zur MAC-Adresse
* Das Alter der ARP-Zuordnungstabellen kann dazu beitragen, die Layer-2-Konfiguration zu überprüfen und Layer-2-Konnektivitätsprobleme zu beheben.


Informationen zum Anzeigen der ARP-Tabelle eines ExpressRoute-Peerings und Informationen zur Behandlung von Layer-2-Konnektivitätsproblemen finden Sie im Dokument [Abrufen von ARP-Tabellen im Resource Manager-Bereitstellungsmodell][ARP].


## <a name="validate-bgp-and-routes-on-the-msee"></a>Überprüfen von BGP und Routen auf dem MSEE

Verwenden Sie den folgenden Befehl, um die Routingtabelle vom MSEE unter dem *primären* Pfad für den *privaten* Routingkontext zu erhalten:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

Eine Beispielantwort lautet:

```output
Network : 10.1.0.0/16
NextHop : 10.17.17.141
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.1.0.0/16
NextHop : 10.17.17.140*
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.2.20.0/25
NextHop : 172.16.0.1
LocPrf  : 
Weight  : 0
Path    : 123##
```

> [!NOTE]
> Wenn der Status eines eBGP-Peerings zwischen einem MSEE und einem CE/PE-MSEE „Aktiv“ oder „Im Leerlauf“ lautet, sollten Sie überprüfen, ob die zugewiesenen primären und sekundären Peersubnetze mit der Konfiguration auf dem verknüpften CE/PE-MSEE übereinstimmen. Überprüfen Sie auch, ob die Angaben für *VlanId*, *AzureASN* und *PeerASN* auf den MSEEs stimmen und ob diese Werte zu den Werten auf dem verknüpften PE-MSEE/CE passen. Wenn MD5-Hashing gewählt wird, sollte der gemeinsam verwendete Schlüssel für das MSEE- und CE/PE-MSEE-Paar gleich sein. Wenn Sie die Konfiguration auf MSEE-Routern ändern müssen, helfen Ihnen die Informationen unter [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering] weiter.
>


> [!NOTE]
> Falls bestimmte Ziele über ein Peering nicht erreichbar sind, sollten Sie die Routentabelle der MSEEs überprüfen, die zum jeweiligen Peeringkontext gehören. Wenn in der Routingtabelle ein übereinstimmendes Präfix (z. B. IP mit NAT) enthalten ist, sollten Sie überprüfen, ob für den Pfad Firewalls/NSG/ACLs vorhanden sind, die den Datenverkehr blockieren.
>


Das folgende Beispiel zeigt die Reaktion auf den Befehl für den Fall, dass kein Peering vorhanden ist:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>Bestätigen des Datenverkehrsflusses
Verwenden Sie den folgenden Befehl, um die Datenverkehrsstatistiken für den primären und sekundären Pfad – eingehende und ausgehende Byte – eines Peeringkontexts zu erhalten:

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

Eine Beispielausgabe des Befehls lautet:

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

Eine Beispielausgabe des Befehls für ein nicht vorhandenes Peering lautet:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen oder Hilfe finden Sie unter den folgenden Links:

- [Microsoft-Support][Support]
- [Erstellen und Ändern einer ExpressRoute-Verbindung][CreateCircuit]
- [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Logische ExpressRoute-Konnektivität"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Symbol „Alle Ressourcen“"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Symbol „Übersicht“"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute-Beispielscreenshot: „Zusammenfassung“"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute-Beispielscreenshot: „Zusammenfassung“"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ARP]: ./expressroute-troubleshooting-arp-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[DR-Pvt]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md