---
title: Netzwerkarchitektur von SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Netzwerkarchitektur zur Bereitstellung von SAP HANA in Azure (große Instanzen).
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b18e1cd20a4b0a886258fd56003cd273d92381fa
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093977"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA-Netzwerkarchitektur (große Instanzen)

Die Architektur von Azure-Netzwerkdiensten ist eine wichtige Komponente der erfolgreichen Bereitstellung von SAP-Anwendungen unter HANA (große Instanz). In der Regel weisen Bereitstellungen von SAP HANA in Azure (große Instanzen) eine größere SAP-Landschaft mit mehreren verschiedenen SAP-Lösungen mit Datenbanken unterschiedlicher Größe sowie variierender CPU-Ressourcen- und Arbeitsspeichernutzung auf. Die Wahrscheinlichkeit ist hoch, dass nicht alle IT-Systeme bereits unter Azure angeordnet sind. Ihre SAP-Landschaft ist aus DBMS- und SAP-Anwendungssicht häufig eine hybride Landschaft, indem eine Mischung aus NetWeaver und S/4HANA/SAP HANA sowie anderen DBMS-Systemen verwendet wird. Azure verfügt über verschiedene Dienste, mit denen Sie die unterschiedlichen DBMS-, NetWeaver- und S/4HANA-System in Azure ausführen können. Darüber hinaus verfügt Azure über Netzwerktechnologie, damit Azure für Ihre lokalen Softwarebereitstellungen wie ein virtuelles Rechenzentrum aussieht.

Dies gilt nicht, wenn Ihre gesamten IT-Systeme in Azure gehostet werden. Die Azure-Netzwerkfunktionalität wird verwendet, um die lokale Umgebung mit Ihren Azure-Ressourcen zu verbinden, damit Azure wie eines Ihrer virtuellen Rechenzentren aussieht. Folgende Azure-Netzwerkfunktionalität wird verwendet: 

- Virtuelle Azure-Netzwerke werden mit der [ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Leitung verbunden, die als Verbindung mit Ihren lokalen Netzwerkressourcen dient.
- Eine ExpressRoute-Leitung, die lokale Systeme mit Azure verbindet, sollte über eine [Mindestbandbreite von 1 GBit/s](https://azure.microsoft.com/pricing/details/expressroute/) verfügen. Diese minimale Bandbreite ist ausreichend für die Übertragung von Daten zwischen lokalen Systemen und auf VMs ausgeführten Systemen. Sie bietet auch eine angemessene Bandbreite für die Verbindung von lokalen Benutzern mit Azure-Systemen.
- Alle SAP-Systeme in Azure werden in virtuellen Netzwerken eingerichtet, um miteinander kommunizieren zu können.
- Die lokal gehostete Active Directory-Instanz und der lokale DNS-Dienst werden über ExpressRoute von der lokalen Umgebung auf Azure ausgedehnt oder vollständig in Azure ausgeführt.

Für den Sonderfall, in dem HANA (große Instanzen) in das Netzwerkfabric des Azure-Rechenzentrums integriert wird, wird auch Azure ExpressRoute-Technologie genutzt.


> [!NOTE] 
> Ein Azure-Abonnement kann nur mit einem einzelnen Mandanten in einem Umfeld von HANA (große Instanz) in einer bestimmten Azure-Region verknüpft werden. Umgekehrt kann ein einziger Mandant im Umfeld von HANA (große Instanz) nur mit einem Azure-Abonnement verknüpft werden. Diese Anforderung gilt auch für andere abrechenbare Objekte in Azure.

Wenn SAP HANA in Azure (große Instanzen) in mehreren verschiedenen Azure-Regionen bereitgestellt wird, wird ein separater Mandant im Umfeld von HANA (große Instanz) bereitgestellt. Sie können beide Mandanten im gleichen Azure-Abonnement ausführen, solange diese Instanzen zur gleichen SAP-Landschaft gehören. 

> [!IMPORTANT] 
> Für SAP HANA in Azure (große Instanzen) wird nur die Azure Resource Manager-Bereitstellungsmethode unterstützt.

 

## <a name="additional-virtual-network-information"></a>Zusätzliche Informationen zum virtuellen Netzwerk

Zum Verbinden eines virtuellen Netzwerks mit ExpressRoute muss ein Azure ExpressRoute-Gateway erstellt werden. Weitere Informationen finden Sie unter [Informationen zu Gateways für virtuelle Netzwerke für ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Ein Azure ExpressRoute-Gateway wird mit einer ExpressRoute-Verbindung mit einer Infrastruktur außerhalb von Azure oder mit einem Azure-Umfeld der großen Instanz verwendet. Sie können das Azure ExpressRoute-Gateway mit bis zu vier verschiedenen ExpressRoute-Leitungen verbinden, solange diese von unterschiedlichen Microsoft-Unternehmensedgeroutern stammen. Weitere Informationen finden Sie unter [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Der maximale Durchsatz, den Sie über eine ExpressRoute-Verbindung mit einem ExpressRoute-Gateway erreichen können, beträgt 10 GBit/s. Beim Kopieren von Dateien zwischen einer VM in einem virtuellen Netzwerk und einem lokalen System (als einzelner Kopierdatenstrom) wird nicht der volle Durchsatz der anderen Gateway-SKUs erzielt. Verwenden Sie mehrere Datenströme, um die vollständige Bandbreite des ExpressRoute-Gateways zu nutzen. Andernfalls müssen Sie verschiedene Dateien in parallelen Datenströmen einer einzelnen Datei kopieren.


## <a name="networking-architecture-for-hana-large-instance"></a>Netzwerkarchitektur für HANA (große Instanz)
Die Netzwerkarchitektur für HANA (große Instanz) kann in vier verschiedene Teile unterteilt werden:

- Lokales Netzwerk und ExpressRoute-Verbindung mit Azure. Dieser Teil ist die Domäne des Kunden und wird über ExpressRoute mit Azure verbunden. Für diese ExpressRoute-Leitung übernehmen Sie als Kunde die gesamten Kosten. Die Bandbreite sollte hoch genug sein, um den Netzwerkdatenverkehr zwischen Ihren lokalen Ressourcen und der Azure-Region, für die Sie die Verbindung herstellen, verarbeiten zu können. Er ist unten rechts in der folgenden Abbildung dargestellt.
- Azure-Netzwerkdienste (wie zuvor erläutert) mit virtuellen Netzwerken, denen wiederum ExpressRoute-Gateways hinzugefügt werden müssen. Dieser Teil ist ein Bereich, für den Sie die geeigneten Entwürfe für Ihre Anwendungs-, Sicherheits- und Konformitätsanforderungen finden müssen. Ob Sie HANA (große Instanz) verwenden, ist ein weiterer Aspekt, der im Hinblick auf die Auswahl der Anzahl virtueller Netzwerke und Azure-Gateway-SKUs zu berücksichtigen ist. Dieser Teil ist oben rechts in der Abbildung dargestellt.
- Konnektivität von HANA (große Instanz) über ExpressRoute-Technologie in Azure. Diesem Teil wird von Microsoft bereitgestellt und gehandhabt. Sie müssen lediglich nach der Bereitstellung Ihrer Ressourcen in HANA (große Instanz) einige IP-Adressbereiche angeben, um die ExpressRoute-Leitung mit den virtuellen Netzwerken zu verbinden. Weitere Informationen finden Sie unter [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Für Sie als Kunde fällt keine zusätzliche Gebühr für die Konnektivität zwischen dem Netzwerkfabric des Azure-Rechenzentrums und HANA-Einheiten (große Instanzen) an.
- Netzwerk im Umfeld von HANA (große Instanz), das für Sie weitestgehend transparent ist.

![Mit SAP HANA in Azure (große Instanzen) und lokal verbundenes virtuelles Netzwerk](./media/hana-overview-architecture/image1-architecture.png)

Die Anforderung, dass Ihre lokalen Ressourcen über ExpressRoute mit Azure verbunden werden müssen, ändert sich durch die Verwendung von HANA (große Instanz) nicht. Die Notwendigkeit von mindestens einem virtuellen Netzwerk zur Ausführung der VMs, die die Anwendungsschicht hosten, die mit den in Einheiten von HANA (große Instanz) gehosteten HANA-Instanzen verbunden sind, ändert sich ebenfalls nicht. 

Für SAP-Bereitstellungen in Azure gelten folgende Unterschiede:

- Die Einheiten von HANA (große Instanz) Ihres Kundenmandanten sind über eine andere ExpressRoute-Leitung mit Ihren virtuellen Netzwerken verbunden. Zur Trennung der Ladebedingungen werden für die ExpressRoute-Leitung von der lokalen Umgebung zum virtuellen Azure-Netzwerk und für die Leitungen zwischen virtuellen Azure-Netzwerken und HANA (große Instanzen) nicht dieselben Router gemeinsam genutzt.
- Das Workloadprofil zwischen der SAP-Anwendungsschicht und HANA (große Instanz) unterscheidet sich insofern, dass viele kleine Anforderungen und Bursts wie Datenübertragungen (Resultsets) von SAP HANA an die Anwendungsschicht gesendet werden.
- Die SAP-Anwendungsarchitektur ist empfindlicher gegenüber der Netzwerklatenz als typische Szenarien, in denen Daten zwischen lokalen Systemen und Azure ausgetauscht werden.
- Das Azure ExpressRoute-Gateway verfügt mindestens über zwei ExpressRoute-Verbindungen. Eine Leitung, die als Verbindung für die lokale Umgebung fungiert, und eine Leitung für die Verbindung mit HANA (große Instanzen). Daher ist nur noch Platz für zwei weitere Leitungen von unterschiedlichen MSEEs, um eine Verbindung mit einem ExpressRoute-Gateway herzustellen. Diese Einschränkung gilt unabhängig von der Nutzung von ExpressRoute Fast Path. Für alle verbundenen Leitungen wird die maximale Bandbreite für eingehende Daten des ExpressRoute-Gateways gemeinsam genutzt.

Mit Revision 3 der Stamps von SAP HANA (große Instanzen) kann die Netzwerklatenz zwischen VMs und Einheiten von SAP HANA (große Instanzen) höher sein als die typische Roundtriplatenz zwischen VMs in einem Netzwerk. Je nach Azure-Region können die gemessenen Werte die Roundtriplatenz von 0,7 ms übersteigen, die im [SAP-Hinweis 1100926 – Häufig gestellte Fragen: Netzwerkleistung](https://launchpad.support.sap.com/#/notes/1100926/E) als unterdurchschnittlich klassifiziert ist. Je nach Azure-Region und Tool zum Messen der Netzwerkroundtrip-Wartezeit zwischen einem virtuellen Azure-Computer und einer HANA-Einheit (große Instanzen) kann die gemessene Wartezeit bis zu ca. 2 Millisekunden betragen. Trotzdem stellen Kunden SAP HANA-basierte SAP-Produktionsanwendungen erfolgreich unter SAP HANA (große Instanz) bereit. Sie sollten Ihre Geschäftsprozesse unbedingt gründlich in SAP HANA in Azure (große Instanzen) testen. Mit der neuen Funktionalität „ExpressRoute Fast Path“ kann die Netzwerklatenz zwischen HANA (große Instanzen) und VMs auf der Anwendungsschicht in Azure erheblich reduziert werden (siehe unten). 

Mit Revision 4 der Stamps von SAP HANA (große Instanzen) kann die Netzwerklatenz zwischen Azure-VMs, die in der Nähe des Stamps von SAP HANA (große Instanzen) bereitgestellt werden, der durchschnittlichen Klassifizierung entsprechen oder besser sein, wie in [SAP Note #1100926 – FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (SAP-Hinweis 1100926 – FAQ: Netzwerkleistung) dokumentiert, wenn der Azure ExpressRoute Fast Path konfiguriert ist (siehe unten). Um Azure-VMs in unmittelbarer Nähe zu Einheiten von SAP HANA (große Instanzen) der Revision 4 bereitzustellen, müssen Sie [Azure-Näherungsplatzierungsgruppen](../../linux/co-location.md) nutzen. Die Art und Weise, wie Näherungsplatzierungsgruppen verwendet werden können, um die SAP-Anwendungsschicht im gleichen Azure-Rechenzentrum wie die als Revision 4 gehosteten Einheiten von SAP HANA (große Instanzen) unterzubringen, ist in [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](sap-proximity-placement-scenarios.md) beschrieben.

Die Auswahl der ExpressRoute-Gateway-SKU ist entscheidend, um eine deterministische Netzwerklatenz zwischen VMs und HANA (große Instanz) bereitzustellen. Im Gegensatz zu den Datenverkehrsmustern zwischen lokalen Systemen und VMs kann das Datenverkehrsmuster zwischen VMs und HANA (große Instanz) kleine, aber hohe Bursts von zu übertragenden Anforderungen und Datenvolumen hervorbringen. Damit solche Bursts korrekt verarbeitet werden, empfehlen wir die Verwendung der UltraPerformance-Gateway-SKU. Für die Typ-II-Klasse-SKUs von HANA (große Instanz) muss die UltraPerformance-Gateway-SKU als ExpressRoute-Gateway verwendet werden.

> [!IMPORTANT] 
> Beim gesamten Netzwerkdatenverkehr zwischen den SAP-Anwendungs- und Datenbankschichten werden zum Herstellen einer Verbindung mit SAP HANA in Azure (große Instanzen) nur die HighPerformance- oder UltraPerformance-Gateway-SKUs für virtuelle Netzwerke unterstützt. Für Typ II-SKUs von HANA (große Instanzen) wird nur die UltraPerformance-Gateway-SKU als ExpressRoute-Gateway unterstützt. Ausnahmen gelten bei Verwendung von ExpressRoute Fast Path (siehe unten).

### <a name="expressroute-fast-path"></a>ExpressRoute Fast Path
Um eine Reduzierung der Latenz zu erzielen, wurde ExpressRoute Fast Path im Mai 2019 eingeführt und veröffentlicht. Diese Funktionalität ist für die spezifische Konnektivität von HANA (große Instanzen) mit virtuellen Azure-Netzwerken bestimmt, die die VMs von SAP-Anwendungen hosten. Der Hauptunterschied zur bisherigen Lösung besteht darin, dass die Datenflüsse zwischen VMs und HANA (große Instanzen) nicht mehr über das ExpressRoute-Gateway geleitet werden. Stattdessen kommunizieren die VMs, die in den Subnetzen des virtuellen Azure-Netzwerks zugewiesen werden, direkt mit dem dedizierten Unternehmensedgerouter. 

> [!IMPORTANT] 
> Für die Funktionalität von ExpressRoute Fast Path ist es erforderlich, dass sich die Subnetze, in denen die VMs der SAP-Anwendungen ausgeführt werden, in demselben virtuellen Azure-Netzwerk befinden, für das eine Verbindung mit HANA (große Instanzen) hergestellt wurde. VMs in virtuellen Azure-Netzwerken, für die ein Peering mit dem virtuellen Azure-Netzwerk besteht, das direkt mit den HANA-Einheiten (große Instanz) verbunden ist, profitieren nicht von ExpressRoute Fast Path. Für typische virtuelle Hub-and-Spoke-Netzwerke, in denen die ExpressRoute-Leitungen eine Verbindung mit einem virtuellen Hub-Netzwerk herstellen und für virtuelle Netzwerke mit der SAP-Anwendungsschicht ein Peering eingerichtet wird, funktioniert die Optimierung mit ExpressRoute Fast Path daher nicht. Darüber hinaus unterstützt ExpressRoute Fast Path derzeit auch keine Regeln für benutzerdefiniertes Routing. Weitere Informationen finden Sie unter [ExpressRoute-Gateway für virtuelle Netzwerke und FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md). 


Ausführlichere Informationen zur Konfiguration von ExpressRoute Fast Path finden Sie im Dokument [Verbinden eines virtuellen Netzwerks mit großen HANA-Instanzen](./hana-connect-vnet-express-route.md).    

> [!NOTE]
> Ein ExpressRoute-Gateway vom Typ „UltraPerformance“ ist erforderlich, damit ExpressRoute Fast Path funktioniert.


## <a name="single-sap-system"></a>Einzelnes SAP-System

Die zuvor gezeigte lokale Infrastruktur ist über ExpressRoute mit Azure verbunden. Die ExpressRoute-Leitung ist mit einem Microsoft-Unternehmensedgerouter (Microsoft Enterprise Edge Router, MSEE) verbunden. Weitere Informationen finden Sie in der [technischen Übersicht über ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nachdem die Route eingerichtet wurde, wird eine Verbindung mit dem Azure-Backbone hergestellt.

> [!NOTE] 
> Stellen Sie zum Ausführen von SAP-Landschaften in Azure eine Verbindung mit dem Unternehmensedgerouter her, der der Azure-Region in der SAP-Landschaft am nächsten ist. Umfelder von HANA (große Instanz) werden über dedizierte Unternehmensedgerouter-Geräte verbunden, um die Netzwerklatenz zwischen VMs in Azure-IaaS und HANA-Umfeldern (große Instanz) zu minimieren.

Das ExpressRoute-Gateway für die VMs, auf denen SAP-Anwendungsinstanzen gehostet werden, ist mit einer ExpressRoute-Leitung verbunden, über die eine Verbindung mit der lokalen Umgebung hergestellt wird. Dasselbe virtuelle Netzwerk ist mit einem separaten Unternehmensedgerouter verbunden, der zum Herstellen einer Verbindung mit Umfeldern der großen Instanz vorgesehen ist. Bei Verwendung von ExpressRoute Fast Path fließen die Daten von SAP HANA (große Instanzen) zu den VMs der SAP-Anwendungsschicht. Da sie nicht mehr über das ExpressRoute-Gateway geleitet werden, wird die Latenz in Bezug auf Netzwerkroundtrips reduziert.

Dieses System ist ein einfaches Beispiel für ein einzelnes SAP-System. Die SAP-Anwendungsschicht wird in Azure gehostet. Die SAP HANA-Datenbank wird in SAP HANA in Azure (große Instanzen) ausgeführt. Hierbei wird davon ausgegangen, dass die Bandbreite des ExpressRoute-Gateways mit einem Durchsatz von 2 GBit/s oder 10 GBit/s keinen Engpass darstellt.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Mehrere SAP-Systeme oder große SAP-Systeme

Wenn mehrere SAP-Systeme oder große SAP-Systeme für die Verbindung mit SAP HANA in Azure (große Instanzen) bereitgestellt werden, kann sich der Durchsatz des ExpressRoute-Gateways als Engpass erweisen. Es kann auch sein, dass Sie Produktionssysteme und andere Systeme in unterschiedlichen virtuellen Azure-Netzwerken isolieren möchten. Teilen Sie in diesem Fall die Anwendungsschichten auf mehrere virtuelle Netzwerke auf. In Fällen wie den folgenden können Sie auch ein spezielles virtuelles Netzwerk erstellen, das mit HANA (große Instanz) verbunden wird:

- Direktes Ausführen von Sicherungen über die HANA-Instanzen in HANA (große Instanz) auf einer VM, die NFS-Freigaben hostet.
- Kopieren großer Sicherungen oder anderer Dateien aus Einheiten von HANA (große Instanzen) auf Festplattenspeicher, der in Azure verwaltet wird.

Verwenden Sie ein separates virtuelles Netzwerk zum Hosten von VMs, mit denen die Speicherung für die Massenübertragung von Daten zwischen HANA (große Instanzen) und Azure verwaltet wird. Hierdurch vermeiden Sie, dass sich große Datei- oder Datenübertragungen von HANA (große Instanz) an Azure auf das ExpressRoute-Gateway für die VMs auswirken, die die SAP-Anwendungsschicht ausführen. 

Gehen Sie für eine stärker skalierbare Netzwerkarchitektur folgendermaßen vor:

- Nutzen Sie mehrere virtuelle Netzwerke für eine einzelne, größere SAP-Anwendungsschicht.
- Stellen Sie für jedes bereitgestellte SAP-System ein separates virtuelles Netzwerk bereit, anstatt diese SAP-Systeme in separaten Subnetzen unter demselben virtuellen Netzwerk zu kombinieren.

  Eine stärker skalierbare Netzwerkarchitektur für SAP HANA in Azure (große Instanzen):

![Bereitstellen einer SAP-Anwendungsschicht über mehrere virtuelle Netzwerke](./media/hana-overview-architecture/image4-networking-architecture.png)

Je nach den Regeln und Einschränkungen, die Sie zwischen den verschiedenen virtuellen Netzwerken, in denen VMs unterschiedlicher SAP-Systeme gehostet werden, anwenden möchten, sollten Sie für diese virtuellen Netzwerke ein Peering einrichten. Weitere Informationen zum Peering virtueller Netzwerke finden Sie unter [Peering in virtuellen Netzwerken](../../../virtual-network/virtual-network-peering-overview.md).


## <a name="routing-in-azure"></a>Routing in Azure

Basierend auf der Standardbereitstellung gibt es drei wichtige Überlegungen zum Netzwerkrouting für SAP HANA in Azure (große Instanzen):

* SAP HANA in Azure (große Instanzen) ist nur über Azure-VMs und die dedizierte ExpressRoute-Verbindung und nicht direkt vom lokalen System aus zugänglich. Der direkte Zugriff vom lokalen System auf die Einheiten von HANA (große Instanz) ist in der von Microsoft bereitgestellten Konfiguration nicht sofort möglich. Die Einschränkungen beim transitiven Routing sind auf die aktuelle Azure-Netzwerkarchitektur zurückzuführen, die für SAP HANA (große Instanz) verwendet wird. Manche Verwaltungsclients und alle Anwendungen, die direkten Zugriff erfordern (beispielsweise eine lokale Instanz von SAP Solution Manager), können keine Verbindung mit der SAP HANA-Datenbank herstellen. Informationen zu Ausnahmen finden Sie im Abschnitt „Direktes Routing zu HANA (große Instanzen)“.

* Wenn Sie für die Notfallwiederherstellung Einheiten von HANA (große Instanz) in zwei verschiedenen Azure-Regionen bereitgestellt haben, haben bisher dieselben vorübergehenden Routingeinschränkungen gegolten. Anders ausgedrückt: Die IP-Adressen einer Einheit von HANA (große Instanz) in einer Region (z. B. „USA, Westen“) wurden nicht an eine Einheit von HANA (große Instanz) in einer anderen Region (z. B. „USA, Osten“) weitergeleitet. Diese Einschränkung galt unabhängig von der Verwendung des Azure-Netzwerkpeerings zwischen Regionen oder der Querverbindung von ExpressRoute-Leitungen, mit denen Einheiten von HANA (große Instanz) mit virtuellen Netzwerken verbunden werden. Eine grafische Darstellung finden Sie in der Abbildung im Abschnitt „Verwenden von Einheiten von HANA (große Instanz) in mehreren Regionen“. Diese Einschränkung, die auf die bereitgestellte Architektur zurückzuführen war, verhinderte die unmittelbare Verwendung der HANA-Systemreplikation als Funktionalität zur Notfallwiederherstellung. Informationen zu kürzlich durchgeführten Änderungen finden Sie im Abschnitt „Verwenden von Einheiten von HANA (große Instanz) in mehreren Regionen“. 

* Einheiten von SAP HANA in Azure (große Instanzen) verfügen über eine zugewiesene IP-Adresse aus dem IP-Adressbereich des Serverpools, den Sie beim Anfordern der Bereitstellung von HANA (große Instanz) angegeben haben. Weitere Informationen finden Sie unter [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Der Zugriff auf diese IP-Adresse ist über die Azure-Abonnements und die Leitung möglich, über die virtuelle Azure-Netzwerke mit HANA (große Instanzen) verbunden werden. Die zugewiesene IP-Adresse aus dem IP-Adressbereich des Serverpools wird der Hardwareeinheit direkt zugewiesen. Anders als bei den ersten Bereitstellungen dieser Lösung wird sie *nicht* mehr über NAT zugewiesen. 

### <a name="direct-routing-to-hana-large-instances"></a>Direktes Routing zu HANA (große Instanzen)

Standardmäßig funktioniert das transitive Routing in folgenden Szenarien nicht:

* Zwischen HANA-Einheiten (große Instanz) und einer lokalen Bereitstellung

* Zwischen HANA-Routing (große Instanz), das in zwei verschiedenen Regionen bereitgestellt wird

Es gibt drei Möglichkeiten, um transitives Routing in diesen Szenarien zu aktivieren:

- Ein Reverseproxy zum Weiterleiten von Daten in beide Richtungen. Beispiel: F5 BIG-IP, NGINX mit einer Bereitstellung von Traffic Manager im virtuellen Azure-Netzwerk, über das eine Verbindung mit HANA (große Instanzen) und mit der lokalen Umgebung als Lösung für eine virtuelle Firewall oder für das Datenverkehrsrouting hergestellt wird.
- IPTables-Regeln auf einer Linux-VM, um das Routing zwischen lokalen Standorten und Einheiten von HANA (große Instanz) oder zwischen Einheiten von HANA (große Instanz) in unterschiedlichen Regionen zu ermöglichen. Die VM, auf der IPTables ausgeführt wird, muss im virtuellen Azure-Netzwerk bereitgestellt werden, über das eine Verbindung mit HANA (große Instanzen) und der lokalen Umgebung hergestellt wird. Die Größe der VM muss entsprechend angepasst werden, damit der Netzwerkdurchsatz der VM für den erwarteten Netzwerkdatenverkehr ausreicht. Ausführlichere Informationen zur VM-Netzwerkbandbreite finden Sie im Artikel [Größen für virtuelle Linux-Computer in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) ist eine weitere Lösung zur Aktivierung von direktem Datenverkehr zwischen der lokalen Umgebung und HANA-Einheiten (große Instanz). 

Der gesamte Datenverkehr dieser Lösungen wird über ein virtuelles Azure-Netzwerk geleitet. Auf diese Weise kann der Datenverkehr durch die verwendeten softwarebasierten Appliances oder mit Netzwerksicherheitsgruppen zusätzlich eingeschränkt werden. Bestimmte IP-Adressen oder IP-Adressbereiche der lokalen Umgebung können blockiert werden, oder der Zugriff auf HANA (große Instanzen) kann explizit zugelassen werden. 

> [!NOTE]  
> Beachten Sie, dass Microsoft keine Implementierung und keinen Support für benutzerdefinierte Lösungen mit Netzwerkgeräten von Drittanbietern oder IPTables bietet. Der Support muss durch den Hersteller der verwendeten Komponente oder den Integrator bereitgestellt werden. 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft hat eine neue Funktionalität mit dem Namen [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) eingeführt. Global Reach kann für HANA (große Instanzen) in zwei Szenarien verwendet werden:

- Ermöglichen des direkten Zugriffs aus der lokalen Umgebung auf Ihre HANA-Einheiten (große Instanz) in unterschiedlichen Regionen
- Ermöglichen der direkten Kommunikation zwischen Ihren HANA-Einheiten (große Instanz) in unterschiedlichen Regionen


##### <a name="direct-access-from-on-premises"></a>Direktzugriff aus der lokalen Umgebung
In den Azure-Regionen, in denen Global Reach angeboten wird, können Sie die Aktivierung der Global Reach-Funktionalität für Ihre ExpressRoute-Leitung anfordern, mit der Ihr lokales Netzwerk mit dem virtuellen Azure-Netzwerk verbunden wird (über das auch eine Verbindung mit Ihren HANA-Einheiten (große Instanz) hergestellt wird). Für die lokale Seite Ihrer ExpressRoute-Leitung wirkt sich dies auf die Kosten aus. Informationen zu den Preisen erhalten Sie auf der Seite mit den Preisangaben für das [Global Reach-Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Für Sie fallen keine zusätzlichen Kosten für die Leitung an, mit der eine Verbindung mit HANA-Einheiten (große Instanz) mit Azure hergestellt wird. 

> [!IMPORTANT]  
> Bei der Nutzung von Global Reach zur Aktivierung des Direktzugriffs zwischen Ihren HANA-Einheiten (große Instanz) und lokalen Ressourcen werden die Netzwerkdaten und die Ablaufsteuerung **nicht über virtuelle Azure-Netzwerke** geleitet, sondern direkt zwischen den Microsoft-Unternehmensedgeroutern. Dies führt dazu, dass alle NSG- oder ASG-Regeln und alle Arten von Firewall, NVA oder Proxy, die Sie in einem virtuellen Azure-Netzwerk bereitgestellt haben, nicht genutzt werden. **Wenn Sie ExpressRoute Global Reach zum Ermöglichen des Direktzugriffs aus der lokalen Umgebung auf HANA-Einheiten (große Instanz) verwenden, müssen Einschränkungen und Berechtigungen zum Zugreifen auf HANA-Einheiten (große Instanz) in Firewalls auf lokaler Seite definiert werden**. 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Verbinden von HANA (große Instanzen) in unterschiedlichen Azure-Regionen
ExpressRoute Global Reach kann nicht nur zum Herstellen einer Verbindung der lokalen Umgebung mit HANA-Einheiten (große Instanz) verwendet werden, sondern auch zum Verbinden zweier HANA-Mandanten (große Instanz), die für Sie in zwei unterschiedlichen Regionen bereitgestellt werden. Die Isolation erfolgt über die ExpressRoute-Leitungen, die von Ihren HANA-Mandanten (große Instanz) verwendet werden, um in beiden Regionen eine Verbindung mit Azure herzustellen. Es werden keine zusätzlichen Gebühren berechnet, um zwei HANA-Mandanten (große Instanz) zu verbinden, die in zwei unterschiedlichen Regionen bereitgestellt werden. 

> [!IMPORTANT]  
> Der Datenfluss und die Ablaufsteuerung des Netzwerkdatenverkehrs zwischen den verschiedenen HANA-Mandanten (große Instanz) verlaufen nicht über Azure-Netzwerke. Sie können daher keine Azure-Funktionalität oder NVAs nutzen, um zwischen Ihren beiden HANA-Mandanten (große Instanzen) Einschränkungen der Kommunikation zu erzwingen. 

Ausführlichere Informationen zur Aktivierung von ExpressRoute Global Reach finden Sie im Dokument [Verbinden eines virtuellen Netzwerks mit großen HANA-Instanzen](./hana-connect-vnet-express-route.md).


## <a name="internet-connectivity-of-hana-large-instance"></a>Internetkonnektivität von HANA (große Instanz)
HANA (große Instanz) verfügt *nicht* über direkte Internetkonnektivität. Daher können Sie das Betriebssystemimage möglicherweise nicht direkt beim Hersteller des Betriebssystems registrieren. Gegebenenfalls müssen Sie dazu den lokalen SUSE Linux Enterprise Server Subscription Management Tool-Server oder Red Hat Enterprise Linux Subscription Manager verwenden.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Datenverschlüsselung zwischen VMs und HANA (große Instanz)
Zwischen HANA (große Instanz) und VMs übertragene Daten werden nicht verschlüsselt. Sie können jedoch für den Austausch zwischen der HANA DBMS-Seite und JDBC-/ODBC-basierten Anwendungen die Verschlüsselung des Datenverkehrs aktivieren. Weitere Informationen finden Sie in [dieser Dokumentation von SAP](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Verwenden von Einheiten von HANA (große Instanz) in mehreren Regionen

Zur Einrichtung der Notfallwiederherstellung müssen Sie über HANA-Einheiten (große Instanz) in mehreren Azure-Regionen verfügen. Auch bei Verwendung des [globalen VNET-Peerings] von Azure funktioniert das transitive Routing zwischen HANA-Mandanten (große Instanz) in zwei unterschiedlichen Regionen nicht. Mit Global Reach werden aber die Kommunikationspfade zwischen den HANA-Einheiten (große Instanz) geöffnet, die Sie in zwei unterschiedlichen Regionen bereitgestellt haben. Dieses Nutzungsszenario für ExpressRoute Global Reach ermöglicht Folgendes:

 - HANA-Systemreplikation ohne zusätzliche Proxys oder Firewalls
 - Kopieren von Sicherungen zwischen HANA-Einheiten (große Instanzen) in zwei unterschiedlichen Regionen zur Durchführung von Systemkopien oder -aktualisierungen


![Mit Azure-Umfeldern der großen Instanz in verschiedenen Azure-Regionen verbundene virtuelle Netzwerke](./media/hana-overview-architecture/image8-multiple-regions.png)

Die Abbildung zeigt, wie die verschiedenen virtuellen Netzwerke in beiden Regionen mit zwei verschiedenen ExpressRoute-Leitungen verbunden sind, die zum Herstellen einer Verbindung mit SAP HANA in Azure (große Instanzen) in beiden Azure-Regionen verwendet werden (graue Linien). Der Grund für diese beiden Querverbindungen ist, dass ein Schutz vor dem Ausfall der MSEEs auf beiden Seiten vorhanden sein soll. Für den Kommunikationsfluss zwischen den beiden virtuellen Netzwerken in den beiden Azure-Regionen wird erwartet, dass er über das [globale Peering](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) der beiden virtuellen Netzwerke in den beiden unterschiedlichen Regionen verarbeitet wird (blaue gepunktete Linie). Mit der dicken roten Linie wird die ExpressRoute Global Reach-Verbindung angegeben, über die die HANA-Einheiten (große Instanz) Ihrer Mandanten in zwei unterschiedlichen Regionen miteinander kommunizieren können. 

> [!IMPORTANT] 
> Falls Sie mehrere ExpressRoute-Leitungen verwendet haben, sollten das Voranstellen von AS Path und lokale BGP-Einstellungen verwendet werden, um ein korrektes Routing des Datenverkehrs sicherzustellen.

**Nächste Schritte**
- Siehe [SAP HANA-Speicherarchitektur (große Instanzen)](hana-storage-architecture.md)