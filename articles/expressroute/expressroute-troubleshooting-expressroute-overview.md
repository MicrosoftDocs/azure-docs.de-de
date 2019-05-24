---
title: 'Überprüfen der Konnektivität – ExpressRoute-Handbuch zur Problembehandlung: Azure | Microsoft-Dokumentation'
description: Diese Seite enthält Anleitungen für die Problembehandlung und Überprüfung der lückenlosen Konnektivität einer ExpressRoute-Verbindung.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 888f4dedf2fda0f54297d42a5f813abf73ded748
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117875"
---
# <a name="verifying-expressroute-connectivity"></a>Überprüfen der ExpressRoute-Konnektivität
Dieser Artikel hilft Ihnen beim Sicherstellen der ExpressRoute-Konnektivität und beim Beheben von Problemen. Mit ExpressRoute wird ein lokales Netzwerk über eine private Verbindung, die über einen Konnektivitätsanbieter bereitgestellt wird, in die Microsoft Cloud erweitert. ExpressRoute umfasst die drei folgenden separaten Netzwerkzonen:

-   Kundennetzwerk
-   Anbieternetzwerk
-   Microsoft-Datencenter

Der Zweck dieses Dokuments besteht darin, Benutzer bei der Ermittlung, wo (bzw. ob) und in welcher Zone ein Konnektivitätsproblem besteht, zu unterstützen. Die Benutzer können dann Hilfe vom zuständigen Team anfordern, um das Problem zu lösen. Wenn Sie zum Lösen eines Problems Unterstützung von Microsoft benötigt wird, können Sie ein Supportticket beim [Microsoft Support][Support] erstellen.

> [!IMPORTANT]
> Dieses Dokument soll Ihnen als Hilfe beim Diagnostizieren und Beheben einfacher Probleme dienen. Es dient nicht als Ersatz für Microsoft Support. Erstellen Sie ein Supportticket beim [Microsoft Support][Support], wenn Sie Ihr Problem nicht anhand der angegebenen Anleitung beheben können.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht
Das folgende Diagramm zeigt die logische Verbindung eines Kundennetzwerks mit einem Microsoft-Netzwerk per ExpressRoute.
[![1]][1]

Im obigen Diagramm stehen die Zahlen für wichtige Netzwerkpunkte. Auf die Netzwerkpunkte wird in diesem Artikel häufig anhand der zugeordneten Nummer verwiesen.

Je nach ExpressRoute-Konnektivitätsmodell (Cloud Exchange Co-Location, Point-to-Point-Ethernet-Verbindung oder Any-to-Any (IPVPN)) können die Netzwerkpunkte 3 und 4 Switches (Layer 2-Geräte) sein. Die wichtigen dargestellten Netzwerkpunkte sind:

1.  Computegerät des Kunden (z.B. ein Server oder PC)
2.  CEs: Edge-Router des Kunden 
3.  PEs (mit CE-Verbindung): Edge-Router/-Switches des Anbieters mit Verbindung mit Edge-Routern des Kunden. Sie werden in diesem Dokument als „PE-CEs“ bezeichnet.
4.  PEs (mit MSEE-Verbindung): Edge-Router/-Switches des Anbieters mit Verbindung mit MSEEs. Sie werden in diesem Dokument als „PE-MSEEs“ bezeichnet.
5.  MSEEs: MSEE-ExpressRoute-Router (Microsoft Enterprise Edge)
6.  Virtual Network (VNet) Gateway
7.  Computegerät im Azure VNet

Wenn die Konnektivitätsmodelle „Cloud Exchange Co-Location“ oder „Point-to-Point-Ethernet-Verbindung“ verwendet werden, richtet der Edge-Router des Kunden (2) ein BGP-Peering mit MSEEs (5) ein. Die Netzwerkpunkte 3 und 4 wären weiterhin vorhanden, aber als Layer 2-Geräte gewissermaßen transparent.

Bei Verwendung des Any-to-Any-Konnektivitätsmodells (IPVPN) richten die PEs (mit MSEE-Verbindung) (4) ein BGP-Peering mit MSEEs (5) ein. Routen werden dann über das IPVPN-Netzwerk des Service Providers zurück an das Kundennetzwerk weitergegeben.

> [!NOTE]
>Zur Erzielung der Hochverfügbarkeit von ExpressRoute benötigt Microsoft ein redundantes Paar von BGP-Sitzungen zwischen MSEEs (5) und PE-MSEEs (4). Es ist auch ratsam, zwischen dem Kundennetzwerk und PE-CEs ein redundantes Paar von Netzwerkpfaden einzurichten. Beim Any-to-Any-Verbindungsmodell (IPVPN) ist aber ggf. ein einzelnes CE-Gerät (2) mit mindestens einem PE (3) verbunden.
>
>

Zum Überprüfen einer ExpressRoute-Verbindung gehören die folgenden Schritte (der Netzwerkpunkt wird durch die zugeordnete Zahl angegeben):
1. [Überprüfen der Verbindungsbereitstellung bzw. des -zustands (5)](#validate-circuit-provisioning-and-state)
2. [Überprüfen, ob mindestens ein ExpressRoute-Peering konfiguriert ist (5)](#validate-peering-configuration)
3. [Überprüfen von ARP zwischen Microsoft und dem Service Provider (Link zwischen 4 und 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Überprüfen von BGP und Routen auf dem MSEE (BGP zwischen 4 und 5 sowie 5 und 6, wenn eine Verbindung mit einem VNet besteht)](#validate-bgp-and-routes-on-the-msee)
5. [Überprüfen der Datenverkehrsstatistiken (Datenverkehr über 5)](#check-the-traffic-statistics)

Es werden ständig weitere Überprüfungen hinzugefügt. Also lohnt es sich, diese Seite jeden Monat einmal aufzurufen!

## <a name="validate-circuit-provisioning-and-state"></a>Überprüfen der Verbindungsbereitstellung bzw. des -zustands
Unabhängig vom Konnektivitätsmodell müssen eine ExpressRoute-Verbindung erstellt und ein entsprechender Dienstschlüssel für die Verbindungsbereitstellung generiert werden. Beim Bereitstellen einer ExpressRoute-Verbindung werden redundante Layer 2-Verbindungen zwischen PE-MSEEs (4) und MSEEs (5) hergestellt. Weitere Informationen dazu, wie Sie eine ExpressRoute-Verbindung erstellen, ändern, bereitstellen und überprüfen, finden Sie im Artikel [Erstellen und Ändern einer ExpressRoute-Verbindung][CreateCircuit].

>[!TIP]
>Mit einem Dienstschlüssel wird eine ExpressRoute-Verbindung eindeutig identifiziert. Dieser Schlüssel ist für die meisten PowerShell-Befehle erforderlich, die in diesem Dokument beschrieben werden. Falls Sie von Microsoft oder von einem ExpressRoute-Partnerunternehmen Hilfe beim Lösen eines ExpressRoute-Problems benötigen, sollten Sie außerdem den Dienstschlüssel angeben, um die Verbindung eindeutig zu identifizieren.
>
>

### <a name="verification-via-the-azure-portal"></a>Überprüfung über das Azure-Portal
Im Azure-Portal können Sie den Status einer ExpressRoute-Verbindung überprüfen, indem Sie im Seitenleistenmenü auf der linken Seite ![2][2] und dann die ExpressRoute-Verbindung wählen. Wenn Sie eine ExpressRoute-Verbindung auswählen, die unter „Alle Ressourcen“ aufgeführt ist, wird das Blatt für die ExpressRoute-Verbindung geöffnet. In Abschnitt ![3][3] des Blatts sind die wichtigsten ExpressRoute-Informationen aufgeführt, wie im folgenden Screenshot dargestellt:

![4][4]    

Auf dem ExpressRoute-Blatt „Zusammenfassung“ ist unter *Schaltkreisstatus* der Status der Verbindung auf Microsoft-Seite angegeben. Unter *Anbieterstatus* ist angegeben, ob für die Verbindung auf Service Provider-Seite *Bereitgestellt/Nicht bereitgestellt* gilt. 

Damit eine ExpressRoute-Verbindung betriebsbereit ist, muss *Schaltkreisstatus* auf *Aktiviert* und *Anbieterstatus* auf *Bereitgestellt* festgelegt sein.

> [!NOTE]
> Wenden Sie sich an den [Microsoft Support][Support], wenn die Option *Schaltkreisstatus* nicht auf „Aktiviert“ festgelegt ist. Wenden Sie sich an Ihren Service Provider, wenn *Anbieterstatus* nicht „Bereitgestellt“ lautet.
>
>

### <a name="verification-via-powershell"></a>Überprüfung per PowerShell
Verwenden Sie den folgenden Befehl, um alle ExpressRoute-Verbindungen in einer Ressourcengruppe aufzulisten:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Sie können Ihren Ressourcengruppennamen über das Azure-Portal ermitteln. Lesen Sie sich den vorherigen Unterabschnitt dieses Dokuments durch. Sie sehen, dass der Ressourcengruppenname im Beispielscreenshot aufgeführt ist.
>
>

Verwenden Sie den folgenden Befehl, um eine bestimmte ExpressRoute-Verbindung in einer Ressourcengruppe auszuwählen:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Eine Beispielantwort lautet:

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

Achten Sie besonders auf die folgenden Felder, um zu überprüfen, ob eine ExpressRoute-Verbindung betriebsbereit ist:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Wenden Sie sich an den [Microsoft Support][Support], wenn *CircuitProvisioningState* nicht auf „Enabled“ festgelegt ist. Wenden Sie sich an Ihren Service Provider, wenn *ServiceProviderProvisioningState* nicht auf „Provisioned“ festgelegt ist.
>
>

### <a name="verification-via-powershell-classic"></a>Überprüfung per PowerShell (klassisch)
Verwenden Sie den folgenden Befehl, um alle ExpressRoute-Verbindungen eines Abonnements aufzulisten:

    Get-AzureDedicatedCircuit

Verwenden Sie den folgenden Befehl, um eine bestimmte ExpressRoute-Verbindung auszuwählen:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Eine Beispielantwort lautet:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Achten Sie besonders auf die folgenden Felder, um zu überprüfen, ob eine ExpressRoute-Verbindung betriebsbereit ist: ServiceProviderProvisioningState: Status der Bereitstellung                           : Enabled

> [!NOTE]
> Wenden Sie sich an den [Microsoft Support][Support], wenn die Option *Status* nicht auf „Enabled“ festgelegt ist. Wenden Sie sich an Ihren Service Provider, wenn *ServiceProviderProvisioningState* nicht auf „Provisioned“ festgelegt ist.
>
>

## <a name="validate-peering-configuration"></a>Überprüfen der Peeringkonfiguration
Nachdem der Service Provider die Bereitstellung der ExpressRoute-Verbindung abgeschlossen hat, kann über die ExpressRoute-Verbindung zwischen MSEE-PRs (4) und MSEEs (5) eine Routingkonfiguration erstellt werden. Für jede ExpressRoute-Verbindung können ein, zwei oder drei Routingkontexte aktiviert werden: privates Azure-Peering (Datenverkehr zu privaten virtuellen Netzwerken in Azure), öffentliches Azure-Peering (Datenverkehr zu öffentlichen IP-Adressen in Azure) und Microsoft-Peering (Datenverkehr zu Office 365 und Dynamics 365). Weitere Informationen dazu, wie Sie die Routingkonfiguration erstellen und ändern, finden Sie im Artikel [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Überprüfung über das Azure-Portal

> [!NOTE]
> Wenn vom Dienstanbieter Layer 3 bereitgestellt wird und die Peerings im Portal leer sind, aktualisieren Sie die Verbindungskonfiguration über die Schaltfläche „Aktualisieren“ im Portal. Mit diesem Vorgang wird für Ihre Verbindung die richtige Routingkonfiguration angewendet. 
>
>

Im Azure-Portal können Sie den Status einer ExpressRoute-Verbindung überprüfen, indem Sie im Seitenleistenmenü auf der linken Seite ![2][2] und dann die ExpressRoute-Verbindung wählen. Wenn Sie eine ExpressRoute-Verbindung auswählen, die unter „Alle Ressourcen“ aufgeführt ist, wird das Blatt für die ExpressRoute-Verbindung geöffnet. In Abschnitt ![3][3] des Blatts sind die wichtigsten ExpressRoute-Informationen aufgeführt, wie im folgenden Screenshot dargestellt:

![5][5]

Im vorherigen Beispiel ist der Routingkontext für das private Azure-Peering aktiviert, während die Routingkontexte für das öffentliche Azure-Peering und das Microsoft-Peering nicht aktiviert sind. Für einen erfolgreich aktivierten Peeringkontext wären auch die primären und sekundären Point-to-Point-Subnetze (für BGP erforderlich) aufgelistet. Die /30-Subnetze werden für die Schnittstellen-IP-Adresse der MSEEs und PE-MSEEs verwendet. 

> [!NOTE]
> Wenn ein Peering nicht aktiviert ist, sollten Sie überprüfen, ob die zugewiesenen primären und sekundären Subnetze mit der Konfiguration auf PE-MSEEs übereinstimmen. Andernfalls helfen Ihnen die Informationen unter [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering] weiter, wenn Sie die Konfiguration auf MSEE-Routern ändern möchten.
>
>

### <a name="verification-via-powershell"></a>Überprüfung per PowerShell
Verwenden Sie die folgenden Befehle, um die Konfigurationsdetails für das private Azure-Peering zu erhalten:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Eine Beispielantwort für ein erfolgreich konfiguriertes privates Peering lautet:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Für einen erfolgreich aktivierten Peeringkontext wären die primären und sekundären Adresspräfixe aufgeführt. Die /30-Subnetze werden für die Schnittstellen-IP-Adresse der MSEEs und PE-MSEEs verwendet.

Verwenden Sie die folgenden Befehle, um die Konfigurationsdetails für das öffentliche Azure-Peering zu erhalten:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Verwenden Sie die folgenden Befehle, um die Konfigurationsdetails für das Microsoft-Peering zu erhalten:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Wenn ein Peering nicht konfiguriert ist, wird eine Fehlermeldung angezeigt. Eine Beispielantwort für den Fall, in dem das angegebene Peering (hier öffentliches Azure-Peering) nicht für die Verbindung konfiguriert ist, lautet:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Wenn ein Peering nicht aktiviert ist, sollten Sie überprüfen, ob die zugewiesenen primären und sekundären Subnetze mit der Konfiguration auf dem verknüpften PE-MSEE übereinstimmt. Überprüfen Sie auch, ob die richtige Angaben für *VlanId*, *AzureASN* und *PeerASN* auf den MSEEs verwendet werden und ob diese Werte zu den Werten auf dem verknüpften PE-MSEE passen. Wenn MD5-Hashing gewählt wird, sollte der gemeinsam verwendete Schlüssel für das MSEE- und PE-MSEE-Paar gleich sein. Wenn Sie die Konfiguration auf den MSEE-Routern ändern möchten, helfen Ihnen die Informationen unter [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering] weiter.  
>
>

### <a name="verification-via-powershell-classic"></a>Überprüfung per PowerShell (klassisch)
Verwenden Sie den folgenden Befehl, um die Konfigurationsdetails für das private Azure-Peering zu erhalten:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Eine Beispielantwort für ein erfolgreich konfiguriertes privates Peering lautet:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Für einen erfolgreich aktivierten Peeringkontext wären die primären und sekundären Peersubnetze aufgeführt. Die /30-Subnetze werden für die Schnittstellen-IP-Adresse der MSEEs und PE-MSEEs verwendet.

Verwenden Sie die folgenden Befehle, um die Konfigurationsdetails für das öffentliche Azure-Peering zu erhalten:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Verwenden Sie die folgenden Befehle, um die Konfigurationsdetails für das Microsoft-Peering zu erhalten:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> Falls vom Service Provider Layer 3-Peerings festgelegt werden, werden die Service Provider-Einstellungen durch das Festlegen der ExpressRoute-Peerings über das Portal oder PowerShell überschrieben. Für das Zurücksetzen der Peeringeinstellungen auf Anbieterseite ist die Unterstützung des Service Providers erforderlich. Ändern Sie die ExpressRoute-Peerings nur, wenn sicher ist, dass der Service Provider nur Layer 2-Dienste bereitstellt!
>
>

> [!NOTE]
> Wenn ein Peering nicht aktiviert ist, sollten Sie überprüfen, ob die zugewiesenen primären und sekundären Peersubnetze mit der Konfiguration auf dem verknüpften PE-MSEE übereinstimmen. Überprüfen Sie auch, ob die richtige Angaben für *VlanId*, *AzureASN* und *PeerASN* auf den MSEEs verwendet werden und ob diese Werte zu den Werten auf dem verknüpften PE-MSEE passen. Wenn Sie die Konfiguration auf den MSEE-Routern ändern möchten, helfen Ihnen die Informationen unter [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering] weiter.
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Überprüfen von ARP zwischen Microsoft und dem Service Provider
In diesem Abschnitt werden PowerShell-Befehle (klassisch) verwendet. Stellen Sie bei der Verwendung von PowerShell-Befehlen mit Azure Resource Manager sicher, dass Sie über einen Administrator- bzw. Co-Administratorzugriff auf das Abonnement verfügen. Informationen zur Problembehandlung mithilfe von Azure Resource Manager-Befehlen finden Sie im Dokument [Abrufen von ARP-Tabellen im Resource Manager-Bereitstellungsmodell][ARP].

> [!NOTE]
>Zum Abrufen von ARP-Daten können Sie sowohl das Azure-Portal als auch Azure Resource Manager-PowerShell-Befehle verwenden. Falls für die Azure Resource Manager-PowerShell-Befehle Fehler auftreten, sollte es mit klassischen PowerShell-Befehlen funktionieren. Die klassischen PowerShell-Befehle können auch für Azure Resource Manager-ExpressRoute-Verbindungen verwendet werden.
>
>

Verwenden Sie den folgenden Befehl, um die ARP-Tabelle vom primären MSEE-Router für das private Peering abzurufen:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Beispielantwort für den Befehl im Erfolgsfall:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Ebenso können Sie die ARP-Tabelle aus dem MSEE im *primären*/*sekundären* Pfad für *private*/*öffentliche*/*Microsoft*-Peerings überprüfen.

Das folgende Beispiel zeigt die Antwort auf den Befehl für den Fall, dass kein Peering vorhanden ist.

    ARP Info:
       
> [!NOTE]
> Sehen Sie sich die folgenden Informationen an, wenn für die ARP-Tabelle keine IP-Adressen der Schnittstellen, die MAC-Adressen zugeordnet sind, vorhanden sind:
>1. Wenn die erste IP-Adresse des /30-Subnetzes, die für den Link zwischen dem MSEE-PR und MSEE zugewiesen ist, für die Schnittstelle des MSEE-PR verwendet wird. Azure verwendet immer die zweite IP-Adresse für MSEEs.
>2. Überprüfen Sie, ob die VLAN-Tags für den Kunden (C-Tag) und den Dienst (S-Tag) für das MSEE-PR- und das MSEE-Paar übereinstimmen.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Überprüfen von BGP und Routen auf dem MSEE
In diesem Abschnitt werden PowerShell-Befehle (klassisch) verwendet. Stellen Sie bei der Verwendung von PowerShell-Befehlen mit Azure Resource Manager sicher, dass Sie über einen Administrator- bzw. Co-Administratorzugriff auf das Abonnement verfügen.

> [!NOTE]
>Zum Abrufen von BGP-Informationen können Sie sowohl das Azure-Portal als auch Azure Resource Manager-PowerShell-Befehle verwenden. Falls für die Azure Resource Manager-PowerShell-Befehle Fehler auftreten, sollte es mit klassischen PowerShell-Befehlen funktionieren. Die klassischen PowerShell-Befehle können auch für Azure Resource Manager-ExpressRoute-Verbindungen verwendet werden.
>
>

Verwenden Sie den folgenden Befehl, um die Routingtabelle (BGP-Nachbar) für einen bestimmten Routingkontext zu erhalten:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Eine Beispielantwort lautet:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Wie im vorherigen Beispiel gezeigt, lässt sich mit dem Befehl gut ermitteln, wie lange der Routingkontext eingerichtet war. Außerdem wird damit die Anzahl von Routenpräfixen angegeben, die vom Peeringrouter angekündigt werden.

> [!NOTE]
> Wenn der Status „Aktiv“ oder „Im Leerlauf“ lautet, sollten Sie überprüfen, ob die zugewiesenen primären und sekundären Peersubnetze mit der Konfiguration auf dem verknüpften PE-MSEE übereinstimmen. Überprüfen Sie auch, ob die richtige Angaben für *VlanId*, *AzureASN* und *PeerASN* auf den MSEEs verwendet werden und ob diese Werte zu den Werten auf dem verknüpften PE-MSEE passen. Wenn MD5-Hashing gewählt wird, sollte der gemeinsam verwendete Schlüssel für das MSEE- und PE-MSEE-Paar gleich sein. Wenn Sie die Konfiguration auf den MSEE-Routern ändern möchten, helfen Ihnen die Informationen unter [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering] weiter.
>
>

> [!NOTE]
> Falls bestimmte Ziele über ein bestimmtes Peering nicht erreichbar sind, sollten Sie die Routentabelle der MSEEs überprüfen, die zum jeweiligen Peeringkontext gehören. Wenn in der Routingtabelle ein übereinstimmendes Präfix (z.B. IP mit NAT) vorhanden ist, sollten Sie überprüfen, ob für den Pfad Firewalls/NSG/ACLs vorhanden sind und ob Datenverkehr zugelassen ist.
>
>

Verwenden Sie den folgenden Befehl, um die vollständige Routingtabelle vom MSEE auf dem *primären* Pfad für den jeweiligen *privaten* Routingkontext zu erhalten:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ein Beispiel für den Erfolgsfall für diesen Befehl lautet:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Ebenso können Sie die Routingtabelle aus dem MSEE im *primären*/*sekundären* Pfad für den Kontext eines *privaten*/*öffentlichen*/*Microsoft*-Peerings überprüfen.

Das folgende Beispiel zeigt die Antwort auf den Befehl für den Fall, dass kein Peering vorhanden ist:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Überprüfen der Datenverkehrsstatistiken
Verwenden Sie den folgenden Befehl, um die Datenverkehrsstatistiken für den primären und sekundären Pfad – eingehende und ausgehende Byte – eines Peeringkontexts zu erhalten:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Eine Beispielausgabe des Befehls lautet:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Eine Beispielausgabe des Befehls für ein nicht vorhandenes Peering lautet:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen oder Hilfe finden Sie unter den folgenden Links:

- [Microsoft Support][Support]
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
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






