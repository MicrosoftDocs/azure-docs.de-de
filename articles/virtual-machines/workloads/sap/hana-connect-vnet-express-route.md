---
title: Einrichten der Konnektivität zwischen einem virtuellen Netzwerk und SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Einrichten der Konnektivität zwischen einem virtuellen Netzwerk und SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c5a8a8157721f34abf7761a85febc7bcea3abb88
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967838"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Verbinden eines virtuellen Netzwerks mit großen HANA-Instanzen

Nachdem Sie ein virtuelles Azure-Netzwerk erstellt haben, können Sie dieses Netzwerk mit großen SAP HANA-Instanzen in Azure verbinden. Erstellen Sie ein Azure ExpressRoute-Gateway im virtuellen Netzwerk. Mit diesem Gateway können Sie das virtuelle Netzwerk mit der ExpressRoute-Leitung verknüpfen, für die eine Verbindung mit dem Kundenmandanten im Umfeld von HANA (große Instanz) hergestellt wird.

> [!NOTE] 
> Die vollständige Ausführung dieses Schritts kann bis zu 30 Minuten dauern. Das neue Gateway wird im vorgesehenen Azure-Abonnement erstellt und dann mit dem angegebenen virtuellen Azure-Netzwerk verbunden.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Wenn bereits ein Gateway vorhanden ist, überprüfen Sie, ob es sich dabei um ein ExpressRoute-Gateway handelt. Falls es sich nicht um ein ExpressRoute-Gateway handelt, sollten Sie das Gateway löschen und als ExpressRoute-Gateway neu erstellen. Wenn bereits ein ExpressRoute-Gateway eingerichtet ist, lesen Sie den Abschnitt „Verknüpfen virtueller Netzwerke“ in diesem Artikel. 

- Verwenden Sie entweder das [Azure-Portal](https://portal.azure.com/) oder PowerShell, um ein mit Ihrem virtuellen Netzwerk verbundenes ExpressRoute-VPN-Gateway zu erstellen.
  - Wenn Sie das Azure-Portal verwenden, fügen Sie ein neues **virtuelles Netzwerkgateway** hinzu, und wählen Sie dann **ExpressRoute** als Gatewaytyp aus.
  - Bei Verwendung von PowerShell laden Sie zuerst das aktuelle [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) herunter, und verwenden Sie dieses. 
 
Über die folgenden Befehle erstellen Sie ein ExpressRoute-Gateway. Die Texte, denen _$_ vorangestellt ist, sind benutzerdefinierte Variablen, die mit Ihren spezifischen Informationen aktualisiert werden müssen.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

In diesem Beispiel wurde die Gateway-SKU „HighPerformance“ verwendet. Ihre Optionen sind „HighPerformance“ oder „UltraPerformance“ als einzige Gateway-SKUs, die für SAP HANA in Azure (große Instanzen) unterstützt werden.

> [!IMPORTANT]
> Für große HANA-Instanzen von Typ-II-SKUs müssen Sie die UltraPerformance-Gateway-SKU verwenden.

## <a name="link-virtual-networks"></a>Verknüpfen virtueller Netzwerke

Das virtuelle Azure-Netzwerk verfügt jetzt über ein ExpressRoute-Gateway. Verwenden Sie die von Microsoft bereitgestellten Autorisierungsinformationen, um das ExpressRoute-Gateway mit der ExpressRoute-Leitung von SAP HANA (große Instanzen) zu verbinden. Sie können die Verbindung im Azure-Portal oder mit PowerShell herstellen. Die PowerShell-Anleitung lautet wie folgt: 

Führen Sie die folgenden Befehle für jedes ExpressRoute-Gateway aus, und verwenden Sie dabei für jede Verbindung eine andere AuthGUID. Die ersten beiden Einträge im folgenden Skript stammen aus den von Microsoft bereitgestellten Informationen. Darüber hinaus ist die AuthGUID spezifisch für jedes virtuelle Netzwerk und das zugehörige Gateway. Wenn Sie ein weiteres virtuelles Azure-Netzwerk hinzufügen möchten, müssen Sie eine weitere AuthID für Ihre ExpressRoute-Leitung abrufen, die große SAP HANA-Instanzen in Azure von Microsoft verbindet. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Der letzte Parameter **ExpressRouteGatewayBypass** im Befehl „New-AzVirtualNetworkGatewayConnection“ ist neu und dient zum Aktivieren von ExpressRoute Fast Path. Dies ist eine Funktionalität, mit der die Netzwerklatenz zwischen Ihren Einheiten von HANA (große Instanzen) und Azure-VMs reduziert wird. Die Funktionalität wurde im Mai 2019 hinzugefügt. Weitere Informationen finden Sie im Artikel [SAP HANA-Netzwerkarchitektur (große Instanzen)](./hana-network-architecture.md). Stellen Sie sicher, dass Sie die aktuelle Version von PowerShell-Cmdlets ausführen, bevor Sie die Befehle ausführen.

Wenn Sie das Gateway mit mehreren ExpressRoute-Leitungen verbinden möchten, die Ihrem Abonnement zugeordnet sind, müssen Sie diesen Schritt unter Umständen mehrmals ausführen. Beispielsweise ist die Wahrscheinlichkeit hoch, dass Sie das virtuelle Netzwerkgateway, das das virtuelle Netzwerk mit Ihrem lokalen Netzwerk verbindet, mit der ExpressRoute-Leitung verbinden.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Anwenden von ExpressRoute Fast Path auf vorhandene ExpressRoute-Leitungen von HANA (große Instanz)
In der Dokumentation wurde bisher beschrieben, wie Sie die Verbindung für eine neue ExpressRoute-Leitung herstellen, die per Bereitstellung von HANA (große Instanz) auf einem Azure ExpressRoute-Gateway in einem Ihrer virtuellen Azure-Netzwerke erstellt wurde. Viele Kunden haben aber bereits eigene ExpressRoute-Leitungen eingerichtet und für ihre virtuellen Netzwerke eine Verbindung mit HANA (große Instanzen) hergestellt. Da mit der neuen Anwendung ExpressRoute Fast Path die Netzwerklatenz verringert wird, empfehlen wir Ihnen, zur Nutzung dieser Funktion die Änderung durchzuführen. Die Befehle zum Herstellen einer Verbindung für eine neue ExpressRoute-Leitung und zum Ändern einer vorhandenen ExpressRoute-Leitung sind identisch. Sie müssen also diese Folge von PowerShell-Befehlen ausführen, um eine vorhandene Leitung zu ändern und für die Nutzung festzulegen. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Es ist wichtig, dass Sie den letzten Parameter wie oben gezeigt hinzufügen, um die Funktionalität von ExpressRoute Fast Path zu aktivieren.


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Sie möchten Global Reach für eines der folgenden Szenarien aktivieren (oder auch für beide):

 - HANA-Systemreplikation ohne zusätzliche Proxys oder Firewalls
 - Kopieren von Sicherungen zwischen HANA-Einheiten (große Instanzen) in zwei unterschiedlichen Regionen zur Durchführung von Systemkopien oder -aktualisierungen

Hierfür müssen Sie Folgendes berücksichtigen:

- Sie müssen einen Adressraumbereich eines Adressraums vom Typ „/29“ angeben. Dieser Adressbereich darf sich nicht mit anderen Adressraumbereichen überschneiden, die Sie bisher für die Herstellung der Verbindung für HANA (große Instanzen) mit Azure verwendet haben, und auch nicht mit Ihren IP-Adressbereichen, die Sie in Azure oder lokal anderweitig genutzt haben.
- Es gilt eine Einschränkung in Bezug auf die ASNs (Autonomous System Number), die genutzt werden können, um Ihre lokalen Routen für HANA (große Instanzen) anzukündigen. Für Ihre lokale Umgebung dürfen keine Routen mit privaten ASNs im Bereich 65.000 bis 65.020 oder 65.515 angekündigt werden. 
- Für das Szenario, bei dem für die lokale Umgebung der direkte Zugriff auf HANA (große Instanzen) ermöglicht wird, müssen Sie eine Gebühr für die Leitung berechnen, die für die Verbindung mit Azure verwendet wird. Informationen zu den Preisen erhalten Sie auf der Seite mit den Preisangaben für das [Global Reach-Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Erstellen Sie wie unter [Öffnen einer Supportanfrage für HANA (große Instanzen)](./hana-li-portal.md#open-a-support-request-for-hana-large-instances) beschrieben eine Supportanfrage, um ein oder beide Szenarien auf Ihre Bereitstellung anzuwenden.

Die benötigten Daten und Schlüsselwörter, die Sie verwenden müssen, damit Microsoft Ihre Anfrage weiterleiten und Maßnahmen ergreifen kann, lauten:

- Dienst: Große SAP HANA-Instanz
- Problemtyp: Konfiguration und Setup
- Problemuntertyp: Mein Problem ist oben nicht aufgeführt
- Betreff „Netzwerk ändern: Global Reach hinzufügen“
- Details: „Hinzufügen von Global Reach zu HANA (große Instanz) für HANA-Mandanten (große Instanz)“ oder „Hinzufügen von Global Reach zur lokalen Umgebung für HANA-Mandanten (große Instanz)“.
- Weitere Details zum Fall „HANA (große Instanz) für HANA-Mandanten (große Instanz)“: Sie müssen die **beiden Azure-Regionen** definieren, in denen sich die beiden zu verbindenden Mandanten befinden, **UND** Sie müssen den **IP-Adressbereich vom Typ „/29“** übermitteln.
- Weitere Details zum Fall „Lokale Umgebung für HANA-Mandanten (große Instanz)“: Sie müssen die **Azure-Region** definieren, in der der HANA-Mandant (große Instanz) bereitgestellt wird, mit dem Sie eine direkte Verbindung herstellen möchten. Außerdem müssen Sie die **GUID für die Authentifizierung** und die **Leitungspeer-ID** angeben, die Sie erhalten haben, als Sie Ihre ExpressRoute-Leitung zwischen Ihrer lokalen Umgebung und Azure eingerichtet haben. Darüber hinaus müssen Sie Ihre **ASN** benennen. Die letzte Angabe ist ein **IP-Adressbereich vom Typ „/29“** für ExpressRoute Global Reach.

> [!NOTE]
> Falls beide Fälle verarbeitet werden sollen, müssen Sie zwei unterschiedliche IP-Adressbereiche vom Typ „/29“ angeben, die sich nicht mit anderen bisher genutzten IP-Adressbereichen überschneiden. 




## <a name="next-steps"></a>Nächste Schritte

- [Zusätzliche Netzwerkanforderungen für HLI](hana-additional-network-requirements.md)
