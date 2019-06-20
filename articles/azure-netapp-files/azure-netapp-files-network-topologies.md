---
title: Richtlinien für die Azure NetApp Files-Netzwerkplanung | Microsoft-Dokumentation
description: Hier finden Sie Anleitungen zum Entwerfen einer effektiven Netzwerkarchitektur mithilfe von Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: fa2de14ada5d24531dfecc7f2f709a87f39ea6cb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826438"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Richtlinien für die Azure NetApp Files-Netzwerkplanung

Die Planung der Netzwerkarchitektur ist ein Schlüsselelement beim Entwerfen von Anwendungsinfrastrukturen. Dieser Artikel hilft Ihnen, eine effektive Netzwerkarchitektur für Ihre Workloads zu entwickeln, um von den umfangreichen Möglichkeiten von Azure NetApp Files zu profitieren.

Azure NetApp Files-Volumes sind so konzipiert, dass sie in einem speziellen Subnetz namens [Delegiertes Subnetz](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) innerhalb Ihrer Azure Virtual Network-Instanz enthalten sind. Daher können Sie bei Bedarf direkt aus Ihrem VNET, auf mit Peering verknüpften VNETs in der gleichen Region oder an lokalen Standorten über ein Gateway von Virtual Network (ExpressRoute oder VPN Gateway) auf die Volumes zugreifen. Das Subnetz ist Azure NetApp Files dediziert zugeordnet, ohne dass eine Verbindung mit anderen Azure-Diensten oder dem Internet besteht.

## <a name="considerations"></a>Überlegungen  

Zum Planen eines Azure NetApp Files-Netzwerks müssen Sie zunächst verschiedene Aspekte verstehen.

### <a name="constraints"></a>Einschränkungen

Azure NetApp Files unterstützt derzeit die folgenden Features nicht: 

* Netzwerksicherheitsgruppen (NSGs) im Subnetz
* Benutzerdefinierte Routen (UDRs) mit dem nächsten Hop als Subnetz für Azure NetApp Files
* Azure-Richtlinien (z.B. benutzerdefinierte Benennungsrichtlinien) für die Azure NetApp Files-Schnittstelle
* Lastenausgleichsmodule für den Azure NetApp Files-Datenverkehr

Für Azure NetApp Files gelten die folgenden Netzwerkeinschränkungen:

* Die Anzahl der VMs, die sich mit einem Volume verbinden können (mit einem VNET oder mit Peering verknüpften VNETs), darf 1.000 nicht überschreiten.
* In jedem Azure Virtual Network (VNET) kann nur ein Subnetz an Azure NetApp Files delegiert werden.


### <a name="supported-network-topologies"></a>Unterstützte Netzwerktopologien

In der folgenden Tabelle werden die von Azure NetApp Files unterstützten Netzwerktopologien beschrieben.  Es werden auch die Problemumgehungen für die nicht unterstützten Topologien beschrieben. 

|    Topologien    |    Unterstützt    |     Problemumgehung    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Verbindung mit Volume in einem lokalen VNET    |    Ja    |         |
|    Verbindung mit Volume in einem mittels Peering verknüpften VNET (in derselben Region)    |    Ja    |         |
|    Verbindung mit Volume in einem mittels Peering verknüpften VNET (regionsübergreifend oder globales Peering)    |    Nein    |    Keine    |
|    Verbindung mit einem Volume über ExpressRoute-Gateway    |    Ja    |         |
|    Verbindung zwischen lokalem Standort mit einem Volume in einem Spoke-VNET über ExpressRoute-Gateway und VNET-Peering mit Gatewaytransit    |    Nein    |    Erstellen eines delegierten Subnetzes im Hub-VNET (Azure-VNET mit Gateway)    |
|    Verbindung zwischen lokalem Standort mit einem Volume in einem Spoke-VNET über VPN-Gateway    |    Ja    |         |
|    Verbindung zwischen lokalem Standort mit einem Volume in einem Spoke-VNET über VPN-Gateway und VNET-Peering mit Gatewaytransit    |    Ja    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuelles Netzwerk für Azure NetApp Files-Volumes

In diesem Abschnitt werden Konzepte erläutert, die Ihnen bei der Planung virtueller Netzwerke helfen.

### <a name="azure-virtual-networks"></a>Virtuelle Azure-Netzwerke

Bevor Sie ein Azure NetApp Files-Volume bereitstellen, müssen Sie ein virtuelles Azure-Netzwerk (VNET) erstellen, oder verwenden Sie eines, das bereits in Ihrem Abonnement vorhanden ist. Das VNET definiert die Netzwerkgrenze des Volumes.  Weitere Informationen zum Erstellen virtueller Netzwerke finden Sie in der [Azure Virtual Network-Dokumentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Subnetze

Subnetze unterteilen das virtuelle Netzwerk in getrennte Adressräume, die von den darin enthaltenen Azure-Ressourcen verwendet werden können.  Azure NetApp Files-Volumes sind in einem speziellen Subnetz namens [Delegiertes Subnetz](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) enthalten. 

Bei der Subnetzdelegierung erhält der Azure NetApp Files-Dienst explizite Berechtigungen, um dienstspezifische Ressourcen im Subnetz zu erstellen.  Bei der Bereitstellung des Diensts wird ein eindeutiger Bezeichner verwendet. In diesem Fall wird eine Netzwerkschnittstelle erstellt, um die Verbindung mit Azure NetApp Files zu ermöglichen.

Wenn Sie ein neues VNET verwenden, können Sie ein Subnetz erstellen und das Subnetz an Azure NetApp Files delegieren, indem Sie den Anweisungen unter [Delegieren eines Subnetz an Azure NetApp Files](azure-netapp-files-delegate-subnet.md) folgen. Es kann eine Delegierung an ein bestehendes leeres Subnetz erfolgen, das nicht bereits an andere Dienste delegiert ist.

Wenn das VNET per Peering mit einem anderen VNET verknüpft ist, können Sie den VNET-Adressraum nicht erweitern. Aus diesem Grund muss das neue delegierte Subnetz innerhalb des VNET-Adressraums eingerichtet werden. Wenn Sie den Adressraum erweitern möchten, müssen Sie das VNET-Peering löschen, ehe Sie den Adressraum erweitern.

### <a name="udrs-and-nsgs"></a>Benutzerdefinierte Routen und Netzwerksicherheitsgruppen

Netzwerksicherheitsgruppen (NSGs) mit einem nächsten Hop können nicht als delegierte Subnetze für Azure NetApp Files verwendet werden. Benutzerdefinierte Routen (User-Defined Routes, UDRs) werden ebenfalls nicht unterstützt. 

Als Umgehungsmaßnahme können Sie NSGs anderen Subnetzen zuordnen, die den Datenverkehr zu und von dem von Azure NetApp Files delegierten Subnetz zulassen oder verweigern.  

## <a name="azure-native-environments"></a>Native Azure-Umgebungen

Das folgende Diagramm veranschaulicht eine native Azure-Umgebung:

![Native Azure-Netzwerkumgebung](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Lokales VNET

Ein einfaches Szenario ist das Erstellen oder Verbinden mit einem Azure NetApp Files-Volume auf einem virtuellen Computer (VM) im selben VNET. Für VNET 2 im obigen Diagramm wird Volume 1 in einem delegierten Subnetz erstellt und kann auf VM 1 im Standardsubnetz eingebunden werden.

### <a name="vnet-peering"></a>VNet-Peering

Wenn Sie zusätzliche VNETs in derselben Region haben, die Zugriff auf die Ressourcen des jeweils anderen benötigen, können die VNETs über [VNET-Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) verbunden werden, um über die Azure-Infrastruktur eine sichere Verbindung zu ermöglichen. 

Sehen Sie sich VNET 2 und VNET 3 im obigen Diagramm an. Wenn VM 1 eine Verbindung mit VM 2 und Volume 2 herstellen muss oder VM 2 eine Verbindung mit VM 1 oder Volume 1 herstellen muss, müssen Sie VNET-Peering zwischen VNET 2 und VNET 3 aktivieren. 

Denken Sie zudem an ein Szenario, bei dem VNET 1 per Peering mit VNET 2 und VNET 2 per Peering mit VNET 3 in der gleichen Region verknüpft ist. Die Ressourcen in VNET 1 können sich mit Ressourcen in VNET 2 verbinden, aber sie können sich nicht mit Ressourcen in VNET 3 verbinden, es sei denn, VNET 1 und VNET 3 werden mittels Peering verknüpft. 

Im obigen Diagramm kann VM 3 zwar eine Verbindung mit Volume 1 herstellen, VM 4 jedoch keine Verbindung mit Volume 2.  Der Grund dafür ist, dass die Spoke-VNETs nicht per Peering verknüpft sind und _Transitrouting über VNET-Peering_ nicht unterstützt wird.

## <a name="hybrid-environments"></a>Hybridumgebungen

Das folgende Diagramm veranschaulicht eine Hybridumgebung: 

![Hybridnetzwerkumgebung](../media/azure-netapp-files/azure-netapp-files-networ-hybrid-environment.png)

Im Hybridszenario benötigen Anwendungen in lokalen Rechenzentren Zugriff auf die Ressourcen in Azure.  Dies ist der Fall, wenn Sie Ihr Rechenzentrum auf Azure ausweiten oder für die Notfallwiederherstellung native Azure-Dienste nutzen möchten. Unter [Planungsoptionen für VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) erfahren Sie, wie Sie mehrere lokale Ressourcen über ein Site-to-Site-VPN oder ExpressRoute mit Ressourcen in Azure verbinden.

In einer hybriden Hub-Spoke-Topologie fungiert das Hub-VNET in Azure als zentraler Verbindungspunkt für Ihr lokales Netzwerk. Die Spokes sind VNETs, die per Peering mit dem Hub verknüpft sind und zur Isolierung von Workloads verwendet werden können.

Das ist abhängig von der Konfiguration. Sie können lokale Ressourcen mit Ressourcen im Hub und auf den Spokes verbinden.

In der oben dargestellten Topologie ist das lokale Netzwerk mit einem Hub-VNET in Azure verbunden, und es gibt 2 Spoke-VNETs, die per Peering mit dem Hub-VNET verbunden sind.  In diesem Szenario werden folgende Verbindungsoptionen für Azure NetApp Files-Volumes unterstützt:

* Lokale Ressourcen auf VM 1 und VM 2 können sich über ein Site-to-Site-VPN oder ExpressRoute mit Volume 1 im Hub verbinden. 
* Lokale Ressourcen in VM 1 und VM 2 können sich mit Volume 2 oder Volume 3 verbinden.
* VM 3 im Hub-VNET kann sich mit Volume 2 in Spoke-VNET 1 und Volume 3 in Spoke-VNET 2 verbinden.
* VM 4 in Spoke-VNET 1 und VM 5 in Spoke-VNET 2 können sich mit Volume 1 im Hub-VNET verbinden.

VM 4 in Spoke-VNET 1 kann sich nicht mit Volume 3 in Spoke-VNET 2 verbinden. Auch VM 5 in Spoke-VNET 2 kann sich nicht mit Volume 2 in Spoke-VNET 1 verbinden. Der Grund dafür ist, dass die Spoke-VNETs nicht per Peering verknüpft sind und _Transitrouting über VNET-Peering_ nicht unterstützt wird.

## <a name="next-steps"></a>Nächste Schritte

[Delegieren eines Subnetzes für Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
