---
title: Netzwerkbereitstellungsfunktionen – Azure App Service | Microsoft-Dokumentation
description: Verwenden der verschiedenen App Service-Netzwerkfunktionen
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 666430a11fb95871eb601b2a38eb7b97ad16119f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498552"
---
# <a name="app-service-networking-features"></a>App Service-Netzwerkfunktionen

Anwendungen in Azure App Service können auf verschiedene Arten bereitgestellt werden. Für Apps, die von App Service gehostet werden, gilt standardmäßig, dass sie direkt über das Internet zugänglich sind und dass sie nur Endpunkte erreichen können, die im Internet gehostet werden. Viele Kundenanwendungen müssen jedoch den ein- und den ausgehenden Netzwerkdatenverkehr steuern. Es gibt mehrere Funktionen in App Service, mit denen diese Anforderungen erfüllt werden können. Die Herausforderung besteht darin, zu wissen, welche Funktion zur Lösung eines bestimmten Problems verwendet werden sollte. Dieses Dokument soll Kunden ermöglichen, anhand einiger exemplarischer Anwendungsfälle zu bestimmen, welche Funktion jeweils verwendet werden soll.

Es gibt zwei primäre Bereitstellungsarten für Azure App Service. Es gibt den mehrinstanzenfähigen öffentlichen Dienst, der App-Service-Pläne in den Preis-SKUs (Tarife) „Free“, „Shared“, „Basic“, „Standard“, „Premium“ und „Premiumv2“ hostet. Außerdem gibt es die App Service-Umgebung (App Service Environment, ASE) für einzelne Mandanten, in der App Service Pläne für isolierte SKUs direkt in Ihrem Azure Virtual Network (VNet) gehostet werden. Sie verwenden unterschiedliche Funktionen abhängig davon, ob Sie mit dem mehrinstanzenfähigen Dienst oder in einer ASE arbeiten. 

## <a name="multi-tenant-app-service-networking-features"></a>Netzwerkfunktionen für mehrinstanzenfähigen App Service 

Azure App Service ist ein verteiltes System. Die Rollen, mit denen eingehende HTTP/HTTPS-Anforderungen verarbeitet werden, werden als Front-Ends bezeichnet. Die Rollen, mit denen die Kundenworkload gehostet wird, werden als Worker bezeichnet. Alle Rollen in einer App Service-Bereitstellung sind in einem mehrinstanzenfähigen Netzwerk vorhanden. Da es viele verschiedene Kunden in derselben App Service-Skalierungseinheit gibt, können Sie das App Service-Netzwerk nicht direkt mit Ihrem Netzwerk verbinden. Statt die Netzwerke zu verbinden, sind Funktionen erforderlich, mit denen die verschiedenen Aspekte der Anwendungskommunikation verwaltet werden. Die Funktionen, mit denen Anforderungen AN Ihre App verwaltet werden, können nicht dazu verwendet werden, Probleme zu lösen, wenn Aufrufe AUS Ihrer App erfolgen. Umgekehrt können die Funktionen, die Probleme für Aufrufe AUS Ihrer App lösen, nicht dazu verwendet werden, Probleme mit Anforderungen AN Ihre App zu lösen.  

| Eingehende Funktionen | Ausgehende Funktionen |
|---------------------|-------------------|
| Von der App zugewiesene Adresse | Hybridverbindungen |
| Zugriffseinschränkungen | Gatewaygeforderte VNet-Integration |
| Dienstendpunkte | VNet-Integration (Vorschau) |

Sofern nicht anders angegeben ist, können alle Funktionen zusammen verwendet werden. Sie können die Funktionen kombinieren, um Ihre verschiedenen Probleme zu lösen.

## <a name="use-case-and-features"></a>Anwendungsfall und Funktionen

Für jeden vorliegenden Anwendungsfall kann es einige Möglichkeiten geben, das Problem zu lösen.  Die richtige zu verwendende Funktion hängt manchmal von Aspekten ab, die über den Anwendungsfall selbst hinausgehen. Anhand der folgenden Anwendungsfälle für eingehende Anforderungen wird vorgeschlagen, wie App Service-Netzwerkfunktionen verwendet werden können, um Probleme hinsichtlich des Steuerns von Datenverkehr zu lösen, der an Ihre App gesendet wird. 
 
| Eingehende Anwendungsfälle | Feature |
|---------------------|-------------------|
| Unterstützung für IP-basiertes SSL für Ihre App benötigt | Von der App zugewiesene Adresse |
| Nicht freigegeben, dedizierte eingehende Adresse für Ihre App | Von der App zugewiesene Adresse |
| Beschränken des Zugriffs auf Ihre App aus einem Satz klar definierter Adressen | Zugriffseinschränkungen |
| Verfügbarmachen meiner App über private IP-Adressen in meinem VNet | ILB ASE </br> Application Gateway mit Dienstendpunkten |
| Beschränken des Zugriffs auf meine App von Ressourcen in einem VNet | Dienstendpunkte </br> ILB ASE |
| Verfügbarmachen meiner App über eine private IP-Adresse in meinem VNet | ILB ASE </br> Private IP-Adresse für eingehenden Datenverkehr in Application Gateway mit Dienstendpunkten |
| Schützen meiner App mit einer Web Application Firewall (WAF) | Application Gateway + ILB ASE </br> Application Gateway mit Dienstendpunkten </br> Azure Front Door mit Zugriffsbeschränkungen |
| Lastenausgleich für Datenverkehr zu meinen Apps in verschiedenen Regionen | Azure Front Door mit Zugriffsbeschränkungen | 
| Lastenausgleich für Datenverkehr in derselben Region | Application Gateway mit Dienstendpunkten | 

Anhand der folgenden Anwendungsfälle für ausgehenden Datenverkehr wird vorgeschlagen, wie App Service-Netzwerkfunktionen verwendet werden können, um Anforderungen zu lösen, die Ihre App hinsichtlich ausgehendem Zugriff stellt. 

| Ausgehende Anwendungsfälle | Feature |
|---------------------|-------------------|
| Zugreifen auf Ressourcen in einem virtuellen Azure-Netzwerk in derselben Region | VNET-Integration </br> ASE |
| Zugreifen auf Ressourcen in einem virtuellen Azure-Netzwerk in einer anderen Region | Gatewaygeforderte VNet-Integration </br> ASE und VNet-Peering |
| Zugreifen auf Ressourcen, die mit Dienstendpunkten geschützt sind | VNET-Integration </br> ASE |
| Zugreifen auf Ressourcen in einem privaten Netzwerk, das nicht mit Azure verbunden ist | Hybridverbindungen |
| Zugreifen auf Ressourcen über ExpressRoute-Verbindungen | VNet-Integration (derzeit beschränkt auf RFC 1918-Adressen) </br> ASE | 


### <a name="default-networking-behavior"></a>Standardmäßiges Netzwerkverhalten

Die Azure App Service-Skalierungseinheiten unterstützen viele Kunden in jeder Bereitstellung. Für die SKU-Pläne „Free“ und „Shared“ werden Kundenworkloads in mehrinstanzenfähigen Workern gehostet. Für den Plan „Basic“ und höhere Pläne werden Kundenworkloads gehostet, die nur einem App Service-Plan (ASP) zugeordnet sind. Wenn Sie den App Service-Plan „Standard“ hatten, werden alle Apps in diesem Plan im selben Worker ausgeführt. Wenn Sie den Worker horizontal hochskalieren, werden alle Apps in diesem Plan in einem neuen Worker für jede Instanz in Ihrem ASP repliziert. Die Worker, die für „Premiumv2“ verwendet werden, unterscheiden sich von den Workern, für die anderen Pläne verwendet werden. Jede App Service-Bereitstellung hat eine IP-Adresse, die für den gesamten eingehenden Datenverkehr zu Apps in dieser App Service-Bereitstellung verwendet wird. Es gibt jedoch an jeder Stelle 4 bis 11 Adressen, die für ausgehende Aufrufe verwendet werden. Diese Adressen werden von allen Apps in dieser App Service-Bereitstellung gemeinsam genutzt. Die ausgehenden Adressen sind entsprechend den verschiedenen Workertypen unterschiedlich. Das heißt, dass sich die Adressen, die für die ASPs „Free“, „Shared“, „Basic“, „Standard“ und „Premium“ verwendet werden, von den Adressen unterscheiden, die für ausgehende Aufrufe aus den „Premiumv2“-ASPs verwendet werden. Wenn Sie sich die Eigenschaften Ihrer App ansehen, sehen Sie die Eingangs- und die Ausgangsadressen, die von Ihrer App verwendet werden. Wenn Sie eine Abhängigkeit mit einer IP-Zugriffssteuerungsliste sperren müssen, verwenden Sie die ausgehenden IP-Adressen (possibleOutboundAddresses). 

![App-Eigenschaften](media/networking-features/app-properties.png)

App Service hat einige Endpunkte, die zur Verwaltung des Diensts verwendet werden.  Diese Adressen werden in einem gesonderten Dokument veröffentlicht und sind auch im IP-Diensttag „AppServiceManagement“ enthalten. Das Tag „AppServiceManagement“ wird nur mit einer App Service-Umgebung (App Service Environment, ASE) verwendet, in der Sie solchen Datenverkehr zuzulassen müssen. Der eingehenden Adressen für App Service werden im IP-Diensttag „AppService“ nachverfolgt. Es gibt kein IP-Diensttag, das die ausgehenden Adressen enthält, die von App Service verwendet werden. 

![Diagramm für eingehenden und ausgehenden Datenverkehr für App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Von der App zugewiesene Adresse 

Die Funktion „von der App zugewiesene Adresse“ ist ein Ableger der IP-basierten SSL-Fähigkeit, und auf sie wird zugegriffen, indem SSL für Ihre App eingerichtet wird. Diese Funktion kann für IP-basierte SSL-Anrufe verwendet werden, aber sie kann auch verwendet werden, um Ihrer App eine Adresse zu geben, die nur sie hat. 

![Diagramm zu von der App zugewiesene Adresse](media/networking-features/app-assigned-address.png)

Wenn Sie eine von der App zugewiesene Adresse verwenden, durchläuft Ihr Datenverkehr weiterhin genau die Front-End-Rollen, die den gesamten Datenverkehr verarbeiten, der in der App Service-Skalierungseinheit eingeht. Die Adresse, die Ihrer App zugewiesen ist, wird jedoch nur von Ihrer App verwendet. Die Anwendungsfälle für diese Funktion sehen wie folgt aus:

* Unterstützung für IP-basiertes SSL für Ihre App benötigt
* Festlegen einer dedizierten Adresse für Ihre App, die mit keinem anderen Element gemeinsam genutzt wird

Informationen, wie Sie eine Adresse für Ihre App festlegen, finden Sie im Tutorial zu [Konfigurieren von IP-basiertem SSL][appassignedaddress]. 

### <a name="access-restrictions"></a>Zugriffseinschränkungen 

Über die Funktionalität „Zugriffsbeschränkungen“ können Sie **eingehende** Anforderungen anhand der Ursprungs-IP-Adresse filtern. Die Filteraktion wird mit den Front-End-Rollen ausgeführt, die den Workerrollen vorgelagert sind, mit denen Ihre Apps ausgeführt werden. Da die Front-End-Rollen den Workern vorgelagert sind, kann die Funktionalität „Zugriffsbeschränkungen“ als Schutz auf Netzwerkebene für Ihre Apps angesehen werden. Die Funktion ermöglicht es Ihnen, eine Liste von zulässigen und abgelehnten Adressblöcken zu erstellen, die in der Reihenfolge ihrer Priorität ausgewertet werden. Diese Funktion ist mit der Netzwerksicherheitsgruppe-Funktion (NSG-Funktion) vergleichbar, die in Azure-Netzwerken vorhanden ist.  Sie können diese Funktion in einer App Service-Umgebung (ASE) oder im mehrinstanzenfähigen Dienst verwenden. Bei Verwendung mit einer ILB ASE können Sie den Zugriff von privaten Adressblöcken beschränken.

![Zugriffseinschränkungen](media/networking-features/access-restrictions.png)

Die „Zugriffsbeschränkungen“-Funktion ist in den Fällen nützlich, in denen Sie die IP-Adressen einschränken möchten, über die Ihre App erreicht werden kann. Zu den Anwendungsfällen für diese Funktion gehören:

* Beschränken des Zugriffs auf Ihre App aus einem Satz klar definierter Adressen 
* Beschränken des Zugriffs so, dass er über einen Lastenausgleichsdienst erfolgt, z. B. über Azure Front Door. Wenn Sie Ihren eingehenden Datenverkehr an Azure Front Door sperren möchten, erstellen Sie Regeln, um Datenverkehr von 147.243.0.0/16 und 2a01:111:2050::/44 zuzulassen. 

![Zugriffsbeschränkungen mit Azure Front Door](media/networking-features/access-restrictions-afd.png)

Wenn Sie den Zugriff auf Ihre App sperren möchten, sodass sie nur von Ressourcen in Ihrem virtuellen Azure-Netzwerk (VNet) erreicht werden kann, benötigen Sie eine statische öffentliche Adresse für jede Komponente, die in Ihrem VNet als Quelle fungiert. Haben die Ressourcen keine öffentliche Adresse, sollten Sie stattdessen die Funktion „Dienstendpunkte“ verwenden. Informationen, wie Sie diese Funktion aktivieren, finden Sie im Tutorial zu [Azure App Service – Statische Zugriffseinschränkungen][iprestrictions].

### <a name="service-endpoints"></a>Dienstendpunkte

Mit Dienstendpunkten können Sie **eingehenden** Zugriff auf Ihre App so sperren, dass die Quelladresse aus einem Satz von Subnetzen stammen muss, die Sie auswählen. Diese Funktion funktioniert zusammen mit den IP-Zugriffseinschränkungen. Dienstendpunkte werden in derselben Benutzeroberfläche wie die IP-Zugriffseinschränkungen festgelegt. Sie können eine Zulassungs-/Verweigerungsliste mit Zugriffsregeln erstellen, die sowohl öffentliche Adressen als auch Subnetze in Ihren VNets umfassen. Diese Funktion unterstützt Szenarien wie die folgenden:

![Dienstendpunkte](media/networking-features/service-endpoints.png)

* Einrichten eines Anwendungsgateways mit Ihrer App, um eingehenden Datenverkehr an Ihre App zu sperren
* Einschränken von Zugriff auf Ihre App auf Ressourcen in Ihrem VNet. Dies kann virtuelle Computer, ASEs oder sogar andere Apps umfassen, für die VNet-Integration verwendet wird. 

![Dienstendpunkte mit Anwendungsgateway](media/networking-features/service-endpoints-appgw.png)

Weitere Informationen zum Konfigurieren von Dienstendpunkten mit Ihrer App finden Sie im Tutorial zu [Azure App Service – Statische Zugriffseinschränkungen][serviceendpoints].
 
### <a name="hybrid-connections"></a>Hybridverbindungen

Mit App Service-Hybridverbindungen wird es ermöglicht, in Ihren Apps **ausgehende** Aufrufe an bestimmte TCP-Endpunkte auszuführen. Der jeweilige Endpunkt kann sich an einer lokalen Stelle, in einem VNet oder an einer beliebigen Stelle befinden, die ausgehenden Datenverkehr an Azure über Port 443 zulässt. Für die Funktion muss ein Relay-Agent, der als Hybridverbindungs-Manager (Hybrid Connection Manager, HCM) bezeichnet wird, auf einem Host mit Windows Server 2012 oder höher installiert sein. Der HCM muss in der Lage sein, Azure Relay über Port 443 erreichen zu können. Der HCM kann im Portal über die Benutzeroberfläche für App Service-Hybridverbindungen heruntergeladen werden. 

![Netzwerkdatenfluss für Hybridverbindungen](media/networking-features/hybrid-connections.png)

Die Funktion „App Service-Hybridverbindungen“ setzt auf der Funktionalität für Azure Relay-Hybridverbindungen auf. In App Service wird eine spezielle Form der Funktion verwendet, die es nur unterstützt, dass ausgehende Aufrufe aus Ihrer App an einen TCP-Host und -Port ausgeführt werden. Dieser Host und Port müssen nur auf dem Host aufgelöst werden, auf dem der HCM installiert ist. Wenn die App in App Service eine DNS-Suche (DNS-Lookup) für den Host und den Port ausführt, die in Ihrer Hybridverbindung definiert sind, wird der Datenverkehr automatisch umgeleitet, sodass er über die Hybridverbindung und den Hybridverbindungs-Manager nach außen gesendet wird. Weitere Informationen über Hybridverbindungen finden Sie in der Dokumentation zu [Azure App Service-Hybridverbindungen][hybridconn].

Diese Funktion wird üblicherweise für Folgendes verwendet:

* Zugreifen auf Ressourcen in privaten Netzwerken, die nicht mit Azure verbunden sind, über ein VPN oder über ExpressRoute
* Unterstützen von Lift & Shift von lokalen Anwendungen zu App Service, ohne dass auch unterstützende Datenbanken verschoben werden müssen  
* Sicheres Bereitstellen von Zugriff auf einen einzelnen Host und Port pro Hybridverbindung. Die meisten Netzwerkfunktionen öffnen Zugriff auf ein Netzwerk, und mit Hybridverbindungen haben Sie nur den einzigen Host und Port, den Sie erreichen können.
* Abdecken von Szenarios, die von anderen Methoden für ausgehende Konnektivität nicht abgedeckt sind
* Entwickeln in App Service, wo für die Apps problemlos lokale Ressourcen genutzt werden können 

Da die Funktion Zugriff auf lokale Ressourcen ohne eine eingehende Firewalllücke ermöglicht, ist sie bei Entwicklern beliebt. Die anderen Funktionen für ausgehenden App Service-Netzwerkdatenverkehr beziehen sich im Wesentlichen auf virtuelle Azure-Netzwerke. Für Hybridverbindungen gibt es keine Abhängigkeit hinsichtlich des Durchlaufens eines VNets, sodass sie für eine größere Bandbreite von Netzwerkanforderungen verwendet werden können. Es ist wichtig zu beachten, dass die Funktion „App Service-Hybridverbindungen“ weder beachtet noch weiß, was über sie erledigt wird. Das heißt, Sie können sie verwenden, um auf eine Datenbank, einen Webdienst oder einen beliebigen TCP-Socket auf einem Mainframe zugreifen. Die Funktion tunnelt im Wesentlichen TCP-Pakete. 

Hybridverbindungen sind nicht für die Entwicklung beliebt, sondern werden auch in zahlreichen Produktionsanwendungen verwendet. Sie eignen sich hervorragend für den Zugriff auf einen Webdienst oder eine Datenbank, sind aber nicht für Situationen geeignet, in denen sehr viele Verbindungen zu erstellen sind. 

### <a name="gateway-required-vnet-integration"></a>Gatewaygeforderte VNet-Integration 

Die Funktion „Gatewaygeforderte App Service-VNet-Integration“ ermöglicht, dass in Ihrer App **ausgehende** Anforderungen an ein virtuelles Azure-Netzwerk gesendet werden. Mit der Funktion wird der Host, auf dem Ihre App ausgeführt wird, über einen Punkt-zu-Standort-VPN mit einem virtuellen Netzwerkgateway in Ihrem VNet verbunden. Wenn Sie die Funktion konfigurieren, erhält Ihre App eine der Punkt-zu-Standort-Adressen, die jeder Instanz zugewiesen sind. Über diese Funktion können Sie auf Ressourcen zugreifen, die sich in einer beliebigen Region in klassischen oder in Resource Manager-VNets befinden. 

![Gatewaygeforderte VNet-Integration](media/networking-features/gw-vnet-integration.png)

Diese Funktion löst das Problem des Zugreifens auf Ressourcen in anderen VNets und kann sogar genutzt werden, über ein VNet Verbindungen mit anderen VNets oder sogar lokal herzustellen. Die Funktion kann nicht mit VNets, die über ExpressRoute verbunden sind, kann aber mit Netzwerken verwendet werden, die über ein Site-to-Site-VPN (Standort-zu-Standort-VPN) verbunden sind. Es ist normalerweise nicht sinnvoll, diese Funktion aus einer App in einer App Service-Umgebung (ASE) zu verwenden, da sich die ASE bereits in Ihrem VNet befindet. Diese Funktion löst folgende Anwendungsfälle:

* Zugreifen auf Ressourcen über private IP-Adressen in Ihren virtuellen Azure-Netzwerken 
* Zugreifen auf lokale Ressourcen, wenn ein Site-to-Site-VPN vorhanden ist 
* Zugreifen auf Ressourcen in VNets mit Peering 

Wenn diese Funktion aktiviert ist, wird in Ihrer App der DNS-Server verwenden, mit dem das Ziel-VNet konfiguriert ist. Weitere Informationen zu dieser Funktion finden Sie in der Dokumentation zu [Integrieren Ihrer App in ein Azure Virtual Network][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNET-Integration

Die Funktion „Gatewaygeforderte VNet-Integration“ ist sehr nützlich, löst aber nicht das Problem des Zugreifens auf Ressourcen über ExpressRoute. Zusätzlich zur Notwendigkeit der Erreichbarkeit über ExpressRoute-Verbindungen ist es für Apps erforderlich, dass sie Aufrufe an Dienste senden können, die über Dienstendpunkte geschützt sind. Um diese beiden zusätzlichen Anforderungen erfüllen zu können, wurde eine weitere VNet-Integrationsfunktion hinzugefügt. Die neue VNet-Integrationsfunktion ermöglicht es Ihnen, das Back-End Ihrer App in einem Subnetz in einem Resource Manager-VNet in derselben Region zu platzieren. Diese Funktion ist nicht aus einer App Service-Umgebung verfügbar, die sich bereits in einem VNet befindet. Diese Funktion ermöglicht Folgendes:

* Zugreifen auf Ressourcen in Resource Manager-VNets in derselben Region
* Zugreifen auf Ressourcen, die mit Dienstendpunkten geschützt sind 
* Zugreifen auf Ressourcen, auf die über ExpressRoute- oder VPN-Verbindungen zugegriffen werden kann

![VNET-Integration](media/networking-features/vnet-integration.png)

Diese Funktion befindet sich in der Vorschauphase und darf nicht für Produktionsworkloads verwendet werden. Wenn Sie mehr zu dieser Funktion erfahren möchten, lesen Sie den Artikel [Integrieren Ihrer App in ein Azure Virtual Network][vnetintegration].

## <a name="app-service-environment"></a>App Service-Umgebung 

Eine App Service-Umgebung (ASE) ist eine Bereitstellung von Azure App Service für einen einzelnen Mandanten, die in Ihrem VNet ausgeführt wird. Die ASE ermöglicht Anwendungsfälle wie die folgenden:

* Zugriff auf Ressourcen in Ihrem VNet
* Zugriff auf Ressourcen über ExpressRoute
* Verfügbarmachen Ihrer Apps mit einer privaten Adresse in Ihrem VNet 
* Zugriff auf Ressourcen über Dienstendpunkte 

Mit einer ASE müssen Sie keine Funktionen wie „VNet-Integration“ oder „Dienstendpunkte“ verwenden, da sich die ASE bereits in Ihrem VNet befindet. Wenn Sie über Dienstendpunkte auf Ressourcen wie SQL oder Storage zugreifen möchten, aktivieren Sie Dienstendpunkte im ASE-Subnetz. Wenn Sie auf Ressourcen im VNet zugreifen möchten, ist keine zusätzliche Konfiguration erforderlich.  Wenn Sie über ExpressRoute auf Ressourcen zugreifen möchten, befinden Sie sich bereits im VNet und müssen nichts in der ASE oder in den ihr enthaltenen Apps konfigurieren. 

Da die Apps in einer ILB ASE über eine private IP-Adresse verfügbar gemacht werden können, können Sie einfach Web Application Firewall-Geräte hinzufügen, um nur die gewünschten Apps für das Internet verfügbar zu machen und die restlichen Apps geschützt zu belassen. Dies führt immanent zur einfachen Entwicklung von Anwendungen mit mehreren Ebenen. 

Es gibt einige Dinge, die aus dem mehrinstanzenfähigen Dienst noch nicht, aber aus einer ASE möglich sind. Dazu gehören folgende Dinge:

* Verfügbarmachen Ihrer Apps unter einer privaten IP-Adresse
* Schützen des gesamten ausgehenden Datenverkehrs mit Netzwerksteuerelementen, die nicht Bestandteil Ihrer App sind 
* Hosten Ihrer Apps in einem Dienst für einen einzelnen Mandanten 
* Zentrales Hochskalieren auf sehr viel mehr Instanzen als im mehrinstanzenfähigen Dienst möglich sind 
* Laden von privaten Zertifizierungsstellen-Clientzertifikaten zur Verwendung durch Ihre Apps mit privaten Endpunkten, die über eine Zertifizierungsstelle geschützt sind 
* Erzwingen von TLS 1.1 für alle Apps, die im System gehostet werden, ohne irgendeine Möglichkeit, dies auf App-Ebene zu deaktivieren 
* Bereitstellen einer dedizierten ausgehenden Adresse für alle Apps in Ihrer ASE, die für keinen Kunden freigegeben ist 

![ASE in einem VNet](media/networking-features/app-service-environment.png)

Die ASE bietet die beste Lösung rund um isoliertes und dediziertes App-Hosting, bringt aber einige Verwaltungsherausforderungen mit sich. Folgende Punkte sind einige der Punkte, die vor dem Verwenden einer operativen ASE zu beachten sind:
 
 * Eine ASE wird in Ihrem VNet ausgeführt, hat aber Abhängigkeiten außerhalb des VNet. Diese Abhängigkeiten müssen zulässig sein. Weitere Informationen finden Sie unter [Überlegungen zum Netzwerkbetrieb in einer App Service-Umgebung][networkinfo].
 * Eine ASE lässt sich nicht unmittelbar skalieren wie der mehrinstanzenfähige Dienst. Sie müssen die Skalierungsanforderungen vorausplanen, statt reagierend zu skalieren. 
 * Eine ASE hat höhere mit ihr verbundene Vorlaufkosten. Um Ihre ASE optimal zu nutzen, sollten Sie so planen, dass viele Workloads in einer ASE platziert werden, statt sie für kleine Kapazitäten zu nutzen.
 * Für die Apps in einer ASE ist es nicht möglich, Zugriff auf einige Apps in der ASE zu beschränken, auf andere dagegen nicht.
 * Die ASE befindet sich in einem Subnetz, und Netzwerkregeln gelten für den gesamten Datenverkehr an diese und aus dieser ASE. Wenn Sie nur für eine App Regeln für eingehenden Datenverkehr zuweisen möchten, verwenden Sie Zugriffseinschränkungen. 

## <a name="combining-features"></a>Kombinieren von Funktionen 

Die Funktionen, die für den mehrinstanzenfähigen Dienst notiert sind, können zusammen verwendet werden, um komplexere Anwendungsfälle zu lösen. Zwei der häufigsten Anwendungsfälle sind hier beschrieben, aber sie sind nur Beispiele. Sobald Sie verstanden haben, was die verschiedenen Funktionen tun, können Sie nahezu alle Ihrer Systemarchitekturanforderungen lösen.

### <a name="inject-app-into-a-vnet"></a>Einfügen einer App in ein VNet

Eine häufige Anforderung besteht darin, wie Sie Ihre App in einem VNet platzieren. Ein Platzieren Ihrer App in einem VNet bedeutet, dass sich die eingehenden und ausgehenden Endpunkte für die App in einem VNet befinden. Die ASE bietet die beste Lösung für dieses Problem, aber Sie können das beste Ergebnis hinsichtlich der Erfordernisse im mehrinstanzenfähigen Dienst erzielen, indem Sie Funktionen kombinieren. Beispielsweise können Sie reine Intranetanwendungen mit privaten ein- und ausgehenden Adressen hosten durch:

* Erstellen eines Anwendungsgateways mit privaten eingehenden und ausgehenden Adressen
* Schützen von eingehendem Datenverkehr an Ihre App mit Dienstendpunkten 
* Verwenden der neuen VNet-Integration, sodass sich das Back-End Ihrer App nicht in Ihrem VNet befindet 

Diese Art der Bereitstellung bringt Ihnen weder eine dedizierte Adresse für ausgehenden Datenverkehr in das Internet noch die Möglichkeit, den gesamten ausgehenden Datenverkehr aus Ihrer App zu sperren.  Diese Art der Bereitstellung eröffnet Ihnen viele der Möglichkeiten, die Sie andernfalls nur mit einer ASE erhalten. 

### <a name="create-multi-tier-applications"></a>Erstellen von Anwendungen mit mehreren Ebenen

Eine Anwendung mit mehreren Ebene ist eine Anwendung, in der nur über die Front-End-Ebene auf die API-Back-End-Apps zugegriffen werden kann. Um eine Anwendung mit mehreren Ebenen zu erstellen, können Sie wie folgt vorgehen:

* Verwenden von VNet-Integration, um das Back-End Ihrer Front-End-Web-App mit einem Subnetz in einem VNet zu verbinden
* Verwenden von Dienstendpunkten, um eingehenden Datenverkehr an Ihre API-App dadurch zu schützen, dass er nur aus dem Subnetz stammt, das für Ihre Front-End-Web-App verwendet wird

![App mit mehreren Ebenen](media/networking-features/multi-tier-app.png)

Sie können erzwingen, dass mehrere Front-End-Apps dieselbe API-App verwenden, indem Sie VNet-Integration von den anderen Front-End-Apps und Dienstendpunkten über die API-App mit ihren Subnetzen verwenden.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
