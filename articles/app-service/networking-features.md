---
title: Netzwerkfeatures
description: Erfahren Sie mehr über die Netzwerkfeatures in Azure App Service und darüber, welche Features Sie für Sicherheit oder andere Funktionalitäten benötigen.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5d950598e4a0af86ac37b53722e80eb4ef0a71a4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183055"
---
# <a name="app-service-networking-features"></a>App Service-Netzwerkfunktionen

Sie können Anwendungen in Azure App Service auf verschiedene Arten bereitstellen. Standardmäßig sind Apps, die in App Service gehostet werden, direkt über das Internet zugänglich und können nur Endpunkte erreichen, die im Internet gehostet werden. Für viele Anwendungen müssen Sie jedoch den ein- und ausgehenden Netzwerkdatenverkehr kontrollieren. Es gibt mehrere Funktionen in App Service, mit denen diese Anforderungen erfüllt werden können. Die Herausforderung besteht darin, zu wissen, welche Funktion zur Lösung eines bestimmten Problems verwendet werden sollte. Dieser Artikel hilft Ihnen, zu bestimmen, welche Funktion verwendet werden soll, basierend auf einigen Beispielanwendungsfällen.

Es gibt zwei Hauptbereitstellungsarten für Azure App Service: 
- Den mehrinstanzenfähigen öffentlichen Dienst, der App Service-Pläne in den Preis-SKUs (Tarifen) „Free“, „Shared“, „Basic“, „Standard“, „Premium“, „PremiumV2“ und „PremiumV3“ hostet. 
- Die App Service-Umgebung (App Service Environment, ASE) für einzelne Mandanten hostet App Service-Pläne für SKUs im Tarif „Isoliert“ direkt in Ihrem Azure Virtual Network. 

Sie verwenden unterschiedliche Funktionen abhängig davon, ob Sie sich im mehrinstanzenfähigen Dienst oder in einer ASE befinden. 

## <a name="multitenant-app-service-networking-features"></a>Netzwerkfunktionen für mehrinstanzenfähigen App Service 

Azure App Service ist ein verteiltes System. Die Rollen, mit denen eingehende HTTP/HTTPS-Anforderungen verarbeitet werden, werden als *Front-Ends* bezeichnet. Die Rollen, mit denen die Kundenworkload gehostet wird, werden als *Worker* bezeichnet. Alle Rollen in einer App Service-Bereitstellung sind in einem mehrinstanzenfähigen Netzwerk vorhanden. Da es viele verschiedene Kunden in derselben App Service-Skalierungseinheit gibt, können Sie das App Service-Netzwerk nicht direkt mit Ihrem Netzwerk verbinden. 

Statt die Netzwerke zu verbinden, sind Funktionen erforderlich, mit denen die verschiedenen Aspekte der Anwendungskommunikation verwaltet werden. Die Funktionen, mit denen Anforderungen *an* Ihre App verwaltet werden, können nicht dazu verwendet werden, Probleme zu lösen, wenn Sie Aufrufe *aus* Ihrer App tätigen. Umgekehrt können die Funktionen, die Probleme für Aufrufe aus Ihrer App lösen, nicht dazu verwendet werden, Probleme mit Anforderungen an Ihre App zu lösen.  

| Eingehende Funktionen | Ausgehende Funktionen |
|---------------------|-------------------|
| Von der App zugewiesene Adresse | Hybridverbindungen |
| Zugriffsbeschränkungen | Von einem Gateway abhängige VNet-Integration |
| Dienstendpunkte | VNET-Integration |
| Private Endpunkte ||

Abgesehen von den genannten Ausnahmen können Sie all diese Funktionen gleichzeitig verwenden. Sie können die Funktionen kombinieren, um Ihre Probleme zu lösen.

## <a name="use-cases-and-features"></a>Anwendungsfälle und Funktionen

Für jeden vorliegenden Anwendungsfall kann es einige Möglichkeiten geben, das Problem zu lösen. Die Auswahl der besten Funktion übersteigt manchmal den Anwendungsfall selbst. Anhand der folgenden Anwendungsfälle für eingehende Anforderungen wird vorgeschlagen, wie App Service-Netzwerkfunktionen verwendet werden können, um Probleme mit der Kontrolle des Datenverkehrs zu lösen, der an Ihre App gesendet wird:
 
| Eingehender Anwendungsfall | Funktion |
|---------------------|-------------------|
| Unterstützung für IP-basiertes SSL für Ihre App benötigt | Von der App zugewiesene Adresse |
| Unterstützung einer nicht freigegebenen, dedizierten eingehenden Adresse für Ihre App | Von der App zugewiesene Adresse |
| Beschränken des Zugriffs auf Ihre App aus einem Satz klar definierter Adressen | Zugriffsbeschränkungen |
| Beschränken des Zugriffs auf Ihre App von Ressourcen in einem virtuellen Netzwerk | Dienstendpunkte </br> ILB ASE </br> Private Endpunkte |
| Verfügbarmachen Ihrer App unter einer privaten IP-Adresse in Ihrem virtuellen Netzwerk | ILB ASE </br> Private Endpunkte </br> Private IP-Adresse für eingehenden Datenverkehr an einer Application Gateway-Instanz mit Dienstendpunkten |
| Schützen Ihrer App mit einer Web Application Firewall (WAF) | Application Gateway und ILB-ASE </br> Application Gateway mit privaten Endpunkten </br> Application Gateway mit Dienstendpunkten </br> Azure Front Door mit Zugriffsbeschränkungen |
| Lastenausgleich für Datenverkehr zu Ihren Apps in verschiedenen Regionen | Azure Front Door mit Zugriffsbeschränkungen | 
| Lastenausgleich für Datenverkehr in derselben Region | [Application Gateway mit Dienstendpunkten][appgwserviceendpoints] | 

Anhand der folgenden Anwendungsfälle für ausgehenden Datenverkehr wird vorgeschlagen, wie App Service-Netzwerkfunktionen verwendet werden können, um Anforderungen zu lösen, die Ihre App hinsichtlich ausgehendem Zugriff stellt:

| Ausgehender Anwendungsfall | Funktion |
|---------------------|-------------------|
| Zugreifen auf Ressourcen in einem virtuellen Azure-Netzwerk in derselben Region | VNET-Integration </br> ASE |
| Zugreifen auf Ressourcen in einem virtuellen Azure-Netzwerk in einer anderen Region | Von einem Gateway abhängige VNet-Integration </br> ASE und Peering virtueller Netzwerke |
| Zugreifen auf Ressourcen, die mit Dienstendpunkten geschützt sind | VNET-Integration </br> ASE |
| Zugreifen auf Ressourcen in einem privaten Netzwerk, das nicht mit Azure verbunden ist | Hybridverbindungen |
| Zugreifen auf Ressourcen über Azure ExpressRoute-Verbindungen | VNET-Integration </br> ASE | 
| Sicherer ausgehender Datenverkehr von Ihrer Web-App | VNet-Integration und Netzwerksicherheitsgruppen </br> ASE | 
| Weiterleiten ausgehenden Datenverkehrs von Ihrer Web-App | VNet-Integration und Routingtabellen </br> ASE | 


### <a name="default-networking-behavior"></a>Standardmäßiges Netzwerkverhalten

Azure App Service-Skalierungseinheiten unterstützen viele Kunden in jeder Bereitstellung. Für die SKU-Pläne „Free“ und „Shared“ werden Kundenworkloads in mehrinstanzenfähigen Workern gehostet. Für den Plan „Basic“ und höhere Pläne werden Kundenworkloads gehostet, die nur einem App Service-Plan zugeordnet sind. Wenn Sie den App Service-Plan „Standard“ haben, werden alle Apps in diesem Plan im selben Worker ausgeführt. Wenn Sie den Worker aufskalieren, werden alle Apps in diesem App Service-Plan in einem neuen Worker für jede Instanz in Ihrem App Service-Plan repliziert. 

#### <a name="outbound-addresses"></a>Ausgehende Adressen

Die Worker-VMs werden größtenteils durch die App Service-Pläne aufgeschlüsselt. Die Pläne „Free“, „Shared“, „Basic“, „Standard“ und „Premium“ verwenden alle denselben Worker-VM-Typ. Der Plan „PremiumV2“ verwendet einen anderen VM-Typ. „PremiumV3“ verwendet noch einen anderen VM-Typ. Wenn Sie die VM-Familie wechseln, erhalten Sie einen anderen Satz ausgehender Adressen. Wenn Sie von „Standard“ auf „Premiumv2“ skalieren, ändern sich Ihre ausgehenden Adressen. Wenn Sie von „Premiumv2“ auf „Premiumv3“ skalieren, ändern sich Ihre ausgehenden Adressen. In einigen älteren Skalierungseinheiten ändern sich sowohl die eingehenden als auch die ausgehenden Adressen, wenn Sie von „Standard“ auf „PremiumV2“ skalieren. 

Es gibt eine Reihe von Adressen, die für ausgehende Aufrufe verwendet werden. Die von Ihrer App für ausgehende Aufrufe verwendeten ausgehenden Adressen werden in den Eigenschaften für Ihre App aufgeführt. Diese Adressen werden von allen Apps, die auf derselben Familie von Worker-VM ausgeführt werden, in der App Service-Bereitstellung gemeinsam genutzt. Wenn Sie alle Adressen anzeigen möchten, die Ihre App in einer Skalierungseinheit möglicherweise verwenden könnte, gibt es die Eigenschaft namens `possibleOutboundAddresses`, mit der sie aufgelistet werden. 

![Screenshot, der App-Eigenschaften zeigt.](media/networking-features/app-properties.png)

App Service hat einige Endpunkte, die zur Verwaltung des Diensts verwendet werden.  Diese Adressen werden in einem gesonderten Dokument veröffentlicht und sind auch im IP-Diensttag `AppServiceManagement` enthalten. Das Tag `AppServiceManagement` wird nur in App Service-Umgebungen verwendet, in denen Sie solchen Datenverkehr zuzulassen müssen. Der eingehenden Adressen für App Service werden im IP-Diensttag `AppService` nachverfolgt. Es gibt kein IP-Diensttag, das die ausgehenden Adressen enthält, die von App Service verwendet werden. 

![Diagramm, das eingehenden und ausgehenden Datenverkehr für App Service zeigt.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Von der App zugewiesene Adresse 

Die Funktion „von der App zugewiesene Adresse“ ist ein Ableger der IP-basierten SSL-Funktion. Sie greifen darauf zu, indem Sie SSL für Ihre App einrichten. Sie können diese Funktion für IP-basierte SSL-Aufrufe verwenden. Sie können sie auch verwenden, um Ihrer App eine Adresse zuzuteilen, die nur diese hat. 

![Diagramm, das eine von der App zugewiesene Adresse veranschaulicht.](media/networking-features/app-assigned-address.png)

Wenn Sie eine von der App zugewiesene Adresse verwenden, durchläuft Ihr Datenverkehr weiterhin genau die Front-End-Rollen, die den gesamten Datenverkehr verarbeiten, der in der App Service-Skalierungseinheit eingeht. Die Adresse, die Ihrer App zugewiesen ist, wird aber nur von Ihrer App verwendet. Anwendungsfälle für diese Funktion:

* Unterstützung IP-basierter SSL-Anforderungen für Ihre App.
* Festlegen einer dedizierten Adresse für Ihre App, die nicht gemeinsam genutzt wird.

Informationen, wie Sie eine Adresse für Ihre App festlegen, finden Sie unter [Hinzufügen eines TLS/SSL-Zertifikats in Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Zugriffsbeschränkungen 

Mithilfe von Zugriffsbeschränkungen können Sie *eingehende* Anforderungen filtern. Die Filteraktion wird mit den Front-End-Rollen ausgeführt, die den Workerrollen vorgelagert sind, mit denen Ihre Apps ausgeführt werden. Da die Front-End-Rollen den Workern vorgelagert sind, können Sie Zugriffsbeschränkungen als Schutz auf Netzwerkebene für Ihre Apps angesehen werden. 

Diese Funktion ermöglicht Ihnen, eine Liste von zulässigen und abgelehnten Regeln zu erstellen, die in der Reihenfolge ihrer Priorität ausgewertet werden. Sie ähnelt der Netzwerksicherheitsgruppen-Funktion (NSG), die in Azure-Netzwerken vorhanden ist. Sie können diese Funktion in einer ASE oder im mehrinstanzenfähigen Dienst verwenden. Bei Verwendung mit einer ILB-ASE oder einem privaten Endpunkt können Sie den Zugriff von privaten Adressblöcken beschränken.
> [!NOTE]
> Pro App können bis zu 512 Regeln zur Zugriffseinschränkung konfiguriert werden. 

![Diagramm, das Zugriffseinschränkungen veranschaulicht.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>IP-basierte Zugriffseinschränkungsregeln

Das Feature zur IP-basierten Zugriffseinschränkung ist nützlich, wenn Sie die IP-Adressen einschränken möchten, über die Ihre App erreicht werden kann. Sowohl IPv4 als auch IPv6 werden unterstützt. Einige Anwendungsfälle für diese Funktion:
* Beschränken des Zugriffs auf Ihre App aus einem Satz klar definierter Adressen. 
* Beschränken des Zugriffs auf Datenverkehr, der über einen Lastenausgleichsdienst eingeht, z. B. über Azure Front Door. Wenn Sie Ihren eingehenden Datenverkehr an Azure Front Door sperren möchten, erstellen Sie Regeln, um Datenverkehr von 147.243.0.0/16 und 2a01:111:2050::/44 zuzulassen. 

Informationen, wie Sie diese Funktion aktivieren, finden Sie unter [Konfigurieren von Zugriffseinschränkungen][iprestrictions].

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Auf Dienstendpunkten basierende Zugriffseinschränkungsregeln 

Mit Dienstendpunkten können Sie *eingehenden* Zugriff auf Ihre App so sperren, dass die Quelladresse aus einem Satz von Subnetzen stammen muss, die Sie auswählen. Diese Funktion funktioniert zusammen mit IP-Zugriffseinschränkungen. Dienstendpunkte sind mit Remotedebugging nicht kompatibel. Wenn Sie Remotedebuggen mit Ihrer App verwenden möchten, darf sich Ihr Client nicht in einem Subnetz mit aktivierten Dienstendpunkten befinden. Der Vorgang zum Festlegen von Dienstendpunkten ähnelt dem Verfahren zum Festlegen von IP-Zugriffseinschränkungen. Sie können eine Zulassungs-/Verweigerungsliste mit Zugriffsregeln erstellen, die öffentliche Adressen und Subnetze in Ihren virtuellen Netzwerken umfasst. 

Einige Anwendungsfälle für diese Funktion:

* Einrichten eines Anwendungsgateways mit Ihrer App, um eingehenden Datenverkehr an Ihre App zu sperren.
* Beschränken des Zugriffs auf Ihre App auf Ressourcen in Ihrem virtuellen Netzwerk. Diese Ressourcen können virtuelle Computer, ASEs oder sogar andere Apps umfassen, die VNet-Integration verwenden. 

![Diagramm, das die Verwendung von Dienstendpunkten mit Application Gateway veranschaulicht.](media/networking-features/service-endpoints-appgw.png)

Weitere Informationen zum Konfigurieren von Dienstendpunkten mit Ihrer App finden Sie unter [Azure App Service-Zugriffseinschränkungen][serviceendpoints].

### <a name="private-endpoint"></a>Privater Endpunkt

Ein privater Endpunkt ist eine Netzwerkschnittstelle, die Sie über eine private und sichere Verbindung mit Azure Private Link mit Ihrer Web-App verbindet. Ein privater Endpunkt verwendet eine private IP-Adresse aus Ihrem virtuellen Netzwerk und bindet die Web-App dadurch in Ihr virtuelles Netzwerk ein. Diese Funktion gilt nur für bei Ihrer Web-App *eingehenden* Flows.
Weitere Informationen finden Sie unter [Verwenden privater Endpunkte für eine Azure-Web-App][privateendpoints].

Einige Anwendungsfälle für diese Funktion:

* Beschränken des Zugriffs auf Ihre App von Ressourcen in einem virtuellen Netzwerk. 
* Verfügbarmachen Ihrer App unter einer privaten IP-Adresse in Ihrem virtuellen Netzwerk. 
* Schützen Ihrer App mit einer WAF.

Private Endpunkte verhindern Datenexfiltration, weil Sie über den privaten Endpunkt ausschließlich Zugriff auf die App erhalten, die damit konfiguriert ist. 
 
### <a name="hybrid-connections"></a>Hybridverbindungen

Mit App Service-Hybridverbindungen wird es ermöglicht, in Ihren Apps *ausgehende* Aufrufe an bestimmte TCP-Endpunkte auszuführen. Der jeweilige Endpunkt kann sich an einer lokalen Stelle, in einem virtuellen Netzwerk oder an einer beliebigen Stelle befinden, die ausgehenden Datenverkehr an Azure über Port 443 zulässt. Um die Funktion zu nutzen, müssen Sie einen Relay-Agent, der als Hybridverbindungs-Manager bezeichnet wird, auf einem Host mit Windows Server 2012 oder höher installieren. Der Hybridverbindungs-Manager muss in der Lage sein, Azure Relay über Port 443 zu erreichen. Sie können den Hybridverbindungs-Manager im Portal über die Benutzeroberfläche für App Service-Hybridverbindungen herunterladen. 

![Diagramm, das den Netzwerkflow für Hybridverbindungen zeigt.](media/networking-features/hybrid-connections.png)

App Service-Hybridverbindungen setzen auf der Funktionalität für Azure Relay-Hybridverbindungen auf. In App Service wird eine spezielle Form der Funktion verwendet, die es nur unterstützt, dass ausgehende Aufrufe aus Ihrer App an einen TCP-Host und -Port ausgeführt werden. Dieser Host und Port müssen nur auf dem Host aufgelöst werden, auf dem der Hybridverbindungs-Manager installiert ist. 

Wenn die App in App Service eine DNS-Suche (DNS-Lookup) für den Host und den Port ausführt, die in Ihrer Hybridverbindung definiert sind, wird der Datenverkehr automatisch umgeleitet, sodass er über die Hybridverbindung und den Hybridverbindungs-Manager nach außen gesendet wird. Weitere Informationen finden Sie unter [App Service-Hybridverbindungen][hybridconn].

Diese Funktion wird üblicherweise für Folgendes verwendet:

* Zugreifen auf Ressourcen in privaten Netzwerken, die nicht mit Azure verbunden sind, über ein VPN oder über ExpressRoute.
* Unterstützen der Migration von lokalen Apps zu App Service, ohne die unterstützenden Datenbanken verschieben zu müssen.  
* Bereitstellen von Zugriff mit erhöhter Sicherheit auf einen einzelnen Host und Port pro Hybridverbindung. Die meisten Netzwerkfeatures öffnen den Zugriff auf ein Netzwerk. Mit Hybridverbindungen können Sie nur den einzelnen Host und Port erreichen.
* Abdecken von Szenarios, die von anderen Methoden für ausgehende Konnektivität nicht abgedeckt sind.
* Entwickeln in App Service auf eine Weise, die es den Apps erlaubt, problemlos lokale Ressourcen zu nutzen. 

Da diese Funktion Zugriff auf lokale Ressourcen ohne eine eingehende Firewalllücke ermöglicht, ist sie bei Entwicklern beliebt. Die anderen Funktionen für ausgehenden App Service-Netzwerkdatenverkehr beziehen sich auf virtuelle Azure-Netzwerke. Hybridverbindungen sind nicht vom Durchlaufen eines virtuellen Netzwerks abhängig. Sie können für eine größere Vielfalt von Netzwerkanforderungen verwendet werden. 

Beachten Sie, dass App Service-Hybridverbindungen nicht erkennen, was Sie darauf aufsetzend tun. So können Sie sie verwenden, um auf eine Datenbank, einen Webdienst oder einen beliebigen TCP-Socket auf einem Mainframe zugreifen. Die Funktion tunnelt im Wesentlichen TCP-Pakete. 

Hybridverbindungen sind in der Entwicklung beliebt, werden aber auch in Produktionsanwendungen verwendet. Sie eignen sich hervorragend für den Zugriff auf einen Webdienst oder eine Datenbank, sind aber nicht für Situationen geeignet, in denen sehr viele Verbindungen zu erstellen sind. 

### <a name="gateway-required-vnet-integration"></a>Von einem Gateway abhängige VNet-Integration 

Die von einem Gateway abhängige VNet-Integration in App Service ermöglicht, dass in Ihrer App *ausgehende* Anforderungen an ein virtuelles Azure-Netzwerk gesendet werden. Mit der Funktion wird der Host, auf dem Ihre App ausgeführt wird, über ein Point-to-Site-VPN mit einem Virtual Network-Gateway in Ihrem virtuellen Netzwerk verbunden. Wenn Sie die Funktion konfigurieren, erhält Ihre App eine der Punkt-zu-Standort-Adressen, die jeder Instanz zugewiesen sind. Über diese Funktion können Sie auf Ressourcen zugreifen, die sich in einer beliebigen Region in klassischen oder in virtuellen Azure Resource Manager-Netzwerken befinden. 

![Diagramm, das die von einem Gateway abhängige VNet-Integration veranschaulicht.](media/networking-features/gw-vnet-integration.png)

Mit dieser Funktion wird das Problem des Zugriffs auf Ressourcen in anderen virtuellen Netzwerken gelöst. Sie kann sogar verwendet werden, um eine Verbindung über ein virtuelles Netzwerk mit anderen virtuellen Netzwerken oder lokal herzustellen. Die Funktion kann nicht mit virtuellen Netzwerken, die über ExpressRoute verbunden sind, kann aber mit Netzwerken verwendet werden, die über ein Site-to-Site-VPN verbunden sind. Es ist normalerweise nicht sinnvoll, diese Funktion aus einer App in einer App Service-Umgebung (ASE) zu verwenden, da sich die ASE bereits in Ihrem virtuellen Netzwerk befindet. Anwendungsfälle für diese Funktion:

* Zugreifen auf Ressourcen über private IP-Adressen in Ihren virtuellen Azure-Netzwerken. 
* Zugreifen auf lokale Ressourcen, wenn ein Site-to-Site-VPN vorhanden ist. 
* Zugreifen auf Ressourcen in virtuellen Netzwerken mit Peering. 

Wenn diese Funktion aktiviert ist, wird in Ihrer App der DNS-Server verwenden, mit dem das virtuelle Zielnetzwerk konfiguriert ist. Weitere Informationen zu dieser Funktion finden Sie unter [App Service-VNet-Integration][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNET-Integration

Die von einem Gateway abhängige VNet-Integration ist nützlich, löst aber nicht das Problem des Zugreifens auf Ressourcen über ExpressRoute. Zusätzlich zur Notwendigkeit der Erreichbarkeit über ExpressRoute-Verbindungen ist es für Apps erforderlich, dass sie Aufrufe an Dienste senden können, die durch Dienstendpunkte geschützt sind. Eine andere VNet-Integrationsfunktion kann diese Anforderungen erfüllen. 

Die neue VNet-Integrationsfunktion ermöglicht es Ihnen, das Back-End Ihrer App in einem Subnetz in einem virtuellen Resource Manager-Netzwerk in derselben Region wie Ihre App zu platzieren. Diese Funktion ist nicht aus einer App Service-Umgebung verfügbar, die sich bereits in einem virtuellen Netzwerk befindet. Anwendungsfälle für diese Funktion:

* Zugreifen auf Ressourcen in virtuellen Resource Manager-Netzwerken in derselben Region.
* Zugreifen auf Ressourcen, die mit Dienstendpunkten geschützt sind. 
* Zugreifen auf Ressourcen, auf die über ExpressRoute- oder VPN-Verbindungen zugegriffen werden kann.
* Hilfe beim Sichern des gesamten ausgehenden Datenverkehrs. 
* Erzwingen von Tunnelung für den gesamten ausgehenden Datenverkehr. 

![Diagramm, das VNet-Integration veranschaulicht.](media/networking-features/vnet-integration.png)

Weitere Informationen finden Sie unter [App Service-VNet-Integration][vnetintegration].

### <a name="app-service-environment"></a>App Service-Umgebung 

Eine App Service-Umgebung (ASE) ist eine Bereitstellung von Azure App Service für einen einzelnen Mandanten, die in Ihrem virtuellen Netzwerk ausgeführt wird. Einige Fälle für diese Funktion:

* Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk.
* Zugriff auf Ressourcen über ExpressRoute.
* Verfügbarmachen Ihrer Apps mit einer privaten Adresse in Ihrem virtuellen Netzwerk. 
* Zugriff auf Ressourcen über Dienstendpunkte. 

Mit einer ASE müssen Sie keine Funktionen wie „VNet-Integration“ oder „Dienstendpunkte“ verwenden, da sich die ASE bereits in Ihrem virtuellen Netzwerk befindet. Wenn Sie über Dienstendpunkte auf Ressourcen wie SQL oder Azure Storage zugreifen möchten, aktivieren Sie Dienstendpunkte im ASE-Subnetz. Wenn Sie auf Ressourcen im virtuellen Netzwerk zugreifen möchten, ist keine zusätzliche Konfiguration erforderlich. Wenn Sie über ExpressRoute auf Ressourcen zugreifen möchten, befinden Sie sich bereits im virtuellen Netzwerk und müssen nichts an der ASE oder den in ihr enthaltenen Apps konfigurieren. 

Da die Apps in einer ILB ASE über eine private IP-Adresse verfügbar gemacht werden können, können Sie einfach Web Application Firewall-Geräte hinzufügen, um nur die gewünschten Apps für das Internet verfügbar zu machen und die restlichen Apps geschützt zu belassen. Diese Funktion kann dazu beitragen, die Entwicklung von Anwendungen mit mehreren Ebenen zu vereinfachen. 

Einige Dinge sind zurzeit aus dem mehrinstanzenfähigen Dienst nicht möglich, können aber aus einer ASE ausgeführt werden. Hier einige Beispiele:

* Verfügbarmachen Ihrer Apps unter einer privaten IP-Adresse.
* Hilfe beim Schützen des gesamten ausgehenden Datenverkehrs mit Netzwerksteuerelementen, die nicht Bestandteil Ihrer App sind.
* Hosten Ihrer Apps in einem Dienst für einen einzelnen Mandanten. 
* Hochskalieren auf sehr viel mehr Instanzen als im mehrinstanzenfähigen Dienst möglich sind. 
* Laden von privaten Zertifizierungsstellen-Clientzertifikaten zur Verwendung durch Ihre Apps mit privaten Endpunkten, die über eine Zertifizierungsstelle geschützt sind.
* Erzwingen von TLS 1.1 für alle Apps, die im System gehostet werden, ohne irgendeine Möglichkeit, dies auf App-Ebene zu deaktivieren. 
* Bereitstellen einer dedizierten ausgehenden Adresse für alle Apps in Ihrer ASE, die für keinen Kunden freigegeben sind. 

![Diagramm, das eine ASE in einem virtuellen Netzwerk veranschaulicht.](media/networking-features/app-service-environment.png)

Die ASE bietet die beste Lösung rund um isoliertes und dediziertes App-Hosting, bringt aber einige Verwaltungsherausforderungen mit sich. Einige Punkte, die vor dem Verwenden einer operativen ASE zu beachten sind:
 
 * Eine ASE wird in Ihrem virtuellen Netzwerk ausgeführt, hat aber Abhängigkeiten außerhalb des virtuellen Netzwerks. Diese Abhängigkeiten müssen zulässig sein. Weitere Informationen finden Sie unter [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung][networkinfo].
 * Eine ASE lässt sich nicht unmittelbar skalieren wie der mehrinstanzenfähige Dienst. Sie müssen die Skalierungsanforderungen vorausplanen, statt reagierend zu skalieren. 
 * Eine ASE hat höhere Vorlaufkosten. Um Ihre ASE optimal zu nutzen, sollten Sie so planen, dass viele Workloads in einer ASE platziert werden, statt sie für kleine Kapazitäten zu nutzen.
 * Für die Apps in einer ASE ist es nicht möglich, Zugriff selektiv auf einige Apps in der ASE zu beschränken, auf andere dagegen nicht.
 * Eine ASE befindet sich in einem Subnetz, und Netzwerkregeln gelten für den gesamten Datenverkehr in und aus dieser ASE. Wenn Sie nur für eine App Regeln für eingehenden Datenverkehr zuweisen möchten, verwenden Sie Zugriffseinschränkungen. 

## <a name="combining-features"></a>Kombinieren von Funktionen 

Die Funktionen, die für den mehrinstanzenfähigen Dienst notiert sind, können zusammen verwendet werden, um komplexere Anwendungsfälle zu lösen. Zwei der häufigsten Anwendungsfälle sind hier beschrieben, aber sie sind nur Beispiele. Sobald Sie verstanden haben, was die verschiedenen Funktionen tun, können Sie nahezu alle Ihrer Systemarchitekturanforderungen erfüllen.

### <a name="place-an-app-into-a-virtual-network"></a>Platzieren einer App in einem virtuellen Netzwerk

Sie fragen sich vielleicht, wie Sie eine App in einem virtuellen Netzwerk platzieren können. Wenn Sie Ihre App in einem virtuellen Netzwerk platzieren, befinden sich die ein- und ausgehenden Endpunkte für die App innerhalb des virtuellen Netzwerks. Eine ASE ist die beste Möglichkeit, um dieses Problem zu beheben. Allerdings können Sie die meisten Ihrer Anforderungen innerhalb des mehrinstanzenfähigen Diensts erfüllen, indem Sie Features kombinieren. Beispielsweise können Sie reine Intranetanwendungen mit privaten ein- und ausgehenden Adressen hosten durch:

* Erstellen eines Anwendungsgateways mit privaten ein- und ausgehenden Adressen.
* Schützen von eingehendem Datenverkehr an Ihre App mit Dienstendpunkten. 
* Verwenden der neuen VNet-Integrationsfunktion, sodass sich das Back-End Ihrer App nicht in Ihrem virtuellen Netzwerk befindet. 

Diese Art der Bereitstellung bringt Ihnen weder eine dedizierte Adresse für ausgehenden Datenverkehr in das Internet noch die Möglichkeit, den gesamten ausgehenden Datenverkehr aus Ihrer App zu sperren. Sie eröffnet Ihnen viele der Möglichkeiten, die Sie andernfalls nur mit einer ASE erhalten. 

### <a name="create-multitier-applications"></a>Erstellen von Anwendungen mit mehreren Ebenen

Eine Anwendung mit mehreren Ebene ist eine Anwendung, in der nur über die Front-End-Ebene auf die API-Back-End-Apps zugegriffen werden kann. Eine App mit mehreren Ebenen kann auf zwei Arten erstellt werden. Beide beginnen mit der Verwendung von VNet-Integration, um Ihre Front-End-Web-App mit einem Subnetz in einem virtuellen Netzwerk zu verbinden. Dies ermöglicht Ihrer Web-App das Ausführen von Aufrufen in Ihr virtuelle Netzwerk. Nachdem Ihre Front-End-App mit dem virtuellen Netzwerk verbunden ist, müssen Sie entscheiden, wie Sie den Zugriff auf Ihre API-Anwendung sperren möchten. Ihre Möglichkeiten:

* Hosten sowohl der Front-End- als auch der API-App in derselben ILB-ASE und Verfügbarmachen der Front-End-App im Internet durch Verwendung eines Anwendungsgateways.
* Hosten des Front-Ends im mehrinstanzenfähigen Dienst und des Back-Ends in einer ILB-ASE.
* Hosten sowohl der Front-End- als auch der API-App im mehrinstanzenfähigen Dienst.

Wenn Sie sowohl die Front-End- als auch die API-App für eine Anwendung mit mehreren Ebenen hosten, haben Sie folgende Möglichkeiten:

- Verfügbarmachen Ihrer API-Anwendung mithilfe privater Endpunkte in Ihrem virtuellen Netzwerk:

  ![Diagramm, das die Verwendung privater Endpunkte in einer App mit zwei Ebenen veranschaulicht.](media/networking-features/multi-tier-app-private-endpoint.png)

- Verwenden von Dienstendpunkten, um sicherzustellen, dass eingehender Datenverkehr an Ihre API-App nur aus dem Subnetz stammt, das von Ihrer Front-End-Web-App verwendet wird:

  ![Diagramm, das die Verwendung von Dienstendpunkten zur Sicherung einer App veranschaulicht.](media/networking-features/multi-tier-app.png)

Im Folgenden finden Sie einige Erwägungen, die Ihnen bei der Wahl der geeigneten Methode helfen sollen:

* Wenn Sie Dienstendpunkte verwenden, müssen Sie nur den Datenverkehr zu Ihrer API-App in das Integrationssubnetz schützen. Dies hilft, die API-App zu sichern, aber es bestünde immer noch die Möglichkeit zur Datenexfiltration aus Ihrer Front-End-App in andere Apps im App Service.
* Wenn Sie private Endpunkte verwenden, verfügen Sie über zwei aktive Subnetze, was die Komplexität erhöht. Außerdem ist der private Endpunkt eine Ressource der obersten Ebene, was den Verwaltungsaufwand erhöht. Der Vorteil bei der Verwendung privater Endpunkte besteht darin, dass keine Möglichkeit der Datenexfiltration besteht. 

Beide Methoden funktionieren mit mehreren Front-Ends. In kleineren Szenarien lassen sich Dienstendpunkte wesentlich einfacher verwenden, weil Sie Dienstendpunkte einfach für die API-App im Front-End-Integrationssubnetz aktivieren. Wenn Sie weitere Front-End-Apps hinzufügen, müssen Sie jede API-App so anpassen, dass sie Dienstendpunkte in das Integrationssubnetz aufnimmt. Wenn Sie private Endpunkte verwenden, haben Sie eine höhere Komplexität, müssen aber nichts bei Ihren API-Apps ändern, nachdem Sie einen privaten Endpunkt festgelegt haben. 

### <a name="line-of-business-applications"></a>Branchenanwendungen

Branchenanwendungen (Line-of-Business, LOB) sind interne Anwendungen, die in der Regel nicht für den Zugriff über das Internet verfügbar gemacht werden. Diese Anwendungen werden aus Unternehmensnetzwerken heraus aufgerufen, in denen sich der Zugriff streng kontrollieren lässt. Wenn Sie eine ILB-ASE verwenden, lassen sich Ihre Branchenanwendungen einfach hosten. Wenn Sie den mehrinstanzenfähigen Dienst verwenden, können Sie entweder private Endpunkte oder Dienstendpunkte in Kombination mit einem Anwendungsgateway verwenden. Es gibt zwei Gründe, um ein Anwendungsgateway mit Dienstendpunkten anstelle privater Endpunkte zu verwenden:
* Sie benötigen WAF-Schutz für Ihre Branchenanwendungen.
* Sie möchten einen Lastenausgleich für mehrere Instanzen Ihrer Branchenanwendungen ausführen.

Wenn nichts davon zutrifft, ist es besser, private Endpunkte zu verwenden. Wenn private Endpunkte in App Service verfügbar sind, können Sie Ihre Apps über private Adressen in Ihrem virtuellen Netzwerk verfügbar machen. Der private Endpunkt, den Sie in Ihrem virtuellen Netzwerk platzieren, kann über ExpressRoute- und VPN-Verbindungen erreicht werden. 

Das Konfigurieren privater Endpunkte macht Ihre Apps über eine private Adresse verfügbar, aber Sie müssen das DNS so konfigurieren, dass diese Adresse von lokalen Standorten aus erreichbar ist. Damit diese Konfiguration funktioniert, müssen Sie die private Azure DNS-Zone, die Ihre privaten Endpunkten enthält, an Ihre lokalen DNS-Server weiterleiten. Private Azure DNS-Zonen unterstützen keine Zonenweiterleitung, aber Sie können Zonenweiterleitung unterstützen, indem Sie einen DNS-Server zu diesem Zweck verwenden. Die Vorlage [DNS-Weiterleitung](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) vereinfacht das Weiterleiten Ihrer privaten Azure DNS-Zone an Ihre lokalen DNS-Server.

## <a name="app-service-ports"></a>App Service-Ports

Wenn Sie App Service überprüfen, finden Sie mehrere Ports, die für eingehende Verbindungen verfügbar gemacht sind. Es gibt keine Möglichkeit, den Zugriff auf diese Ports im mehrinstanzenfähigen Dienst zu blockieren oder zu kontrollieren. Im Folgenden finden Sie eine Liste der verfügbar gemachten Ports:

| Zweck | Port oder Ports |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Verwaltung | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Remotedebuggen in Visual Studio  |  4020, 4022, 4024 |
|  Web Deploy-Dienst | 8172 |
|  Infrastrukturverwendung | 7654, 1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md