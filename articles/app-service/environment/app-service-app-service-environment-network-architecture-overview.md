---
title: Übersicht über die Netzwerkarchitektur von App Service-Umgebungen – Azure
description: Übersicht über die Architektur der Netzwerktopologie von App Service-Umgebungen
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 0d7d4af46e54ad89e0d084cb15af13e56115e996
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60765277"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Übersicht über die Netzwerkarchitektur von App Service-Umgebungen
## <a name="introduction"></a>Einführung
App Service-Umgebungen werden stets in einem Subnetz eines [virtuellen Netzwerks][virtualnetwork] erstellt. Apps, die in einer App Service-Umgebung ausgeführt werden, können mit privaten Endpunkten kommunizieren, die sich in derselben virtuellen Netzwerktopologie befinden.  Da Kunden Teile ihrer virtuellen Netzwerkinfrastruktur sperren können, ist es wichtig, die Arten von Netzwerkkommunikationsabläufen zu kennen, die in einer App Service-Umgebung vorkommen.

## <a name="general-network-flow"></a>Allgemeiner Netzwerkdatenfluss
Wenn eine App Service-Umgebung (App Service Environment, ASE) eine öffentliche virtuelle IP-Adresse (VIP) für Apps verwendet, läuft der gesamte eingehende Datenverkehr über diese öffentliche VIP.  Dazu zählen der HTTP- und HTTPS-Datenverkehr für Apps sowie anderer Datenverkehr für FTP, Remotedebuggingfunktionalität und Azure-Verwaltungsvorgänge.  Eine vollständige Liste der spezifischen (erforderlichen und optionalen) Ports, die für die öffentliche VIP verfügbar sind, finden Sie im Artikel zum [Steuern des eingehenden Datenverkehrs][controllinginboundtraffic] in eine App Service-Umgebung. 

App Service-Umgebungen unterstützen auch die Ausführung von Apps, die nur an eine interne Adresse des virtuelles Netzwerks gebunden sind, auch als ILB-Adresse (Internal Load Balancer, interner Lastenausgleich) bezeichnet.  In einer ASE, in der ILB aktiviert ist, gehen sowohl HTTP- und HTTPS-Datenverkehr für Apps als auch Remotedebuggingaufrufe über die ILB-Adresse ein.  In den am häufigsten verwendeten ILB-ASE-Konfigurationen geht FTP/FTPS-Datenverkehr auch über die ILB-Adresse ein.  Azure-Verwaltungsvorgänge fließen jedoch immer noch über die Ports 454/455 der öffentlichen VIP-Adresse einer ASE, in der ILB aktiviert ist.

Das folgende Diagramm zeigt eine Übersicht der verschiedenen ein- und ausgehenden Netzwerkflüsse einer App Service-Umgebung, in der die Apps an eine öffentliche virtuelle IP-Adresse gebunden sind:

![Allgemeine Netzwerkdatenflüsse][GeneralNetworkFlows]

Eine App Service-Umgebung kann mit einer Vielzahl privater Kundenendpunkte kommunizieren.  Beispielsweise können Apps, die in der App Service-Umgebung ausgeführt werden, sich mit Datenbankservern auf virtuellen IaaS-Computern in der gleichen virtuellen Netzwerktopologie verbinden.

> [!IMPORTANT]
> Im Netzwerkdiagramm sehen Sie, dass „Weitere Compute-Ressourcen“ in einem anderen Subnetz als die App Service-Umgebung bereitgestellt werden. Durch Bereitstellung im gleichen Subnetz wie die App Service-Umgebung würde die Verbindung der Umgebung mit diesen Ressourcen blockiert (ausgenommen spezifische Routen innerhalb der Umgebung). Stellen Sie die Ressourcen daher in einem anderen Subnetz innerhalb des gleichen VNETs bereit. Dann kann die App Service-Umgebung eine Verbindung herstellen. Es ist keine zusätzliche Konfiguration erforderlich.
> 
> 

App Service-Umgebungen kommunizieren auch mit SQL-Datenbank- und Azure-Speicherressourcen, die für das Verwalten und Betreiben einer App Service-Umgebung erforderlich sind.  Einige SQL- und Speicherressourcen, mit denen eine App Service-Umgebung kommuniziert, befinden sich in der gleichen Region wie die App Service-Umgebung, während andere sich in anderen Azure-Regionen befinden.  Deshalb sind für den ordnungsgemäßen Betrieb einer App Service-Umgebung stets ausgehende Verbindungen erforderlich. 

Da eine App Service-Umgebung in einem Subnetz bereitgestellt wird, können Netzwerksicherheitsgruppen zum Steuern des in das Subnetz eingehenden Datenverkehrs verwendet werden.  Ausführliche Informationen zum Steuern des eingehenden Datenverkehrs in eine App Service-Umgebung finden Sie im folgenden [Artikel][controllinginboundtraffic].

Details zum Zulassen ausgehender Internetverbindungen aus einer App Service-Umgebung finden Sie im folgenden Artikel über das Arbeiten mit [ExpressRoute][ExpressRoute].  Der gleiche in diesem Artikel beschriebene Ansatz gilt beim Arbeiten mit Standort-zu-Standort-Verbindungen und Verwenden der Tunnelerzwingung.

## <a name="outbound-network-addresses"></a>Ausgehende Netzwerkadressen
Wenn eine App Service-Umgebung ausgehende Aufrufe ausführt, ist den ausgehenden Aufrufen stets eine IP-Adresse zugeordnet.  Die spezifische verwendete IP-Adresse hängt davon ab, ob sich der Endpunkt innerhalb oder außerhalb der virtuellen Netzwerktopologie befindet.

Wenn sich der aufgerufene Endpunkt **außerhalb** der Topologie des virtuellen Netzwerks befindet, ist die ausgehende Adresse (d.h. die ausgehende NAT-Adresse), die verwendet wird, die öffentliche VIP der App Service-Umgebung.  Diese Adresse finden Sie auf der Portalbenutzeroberfläche der App Service-Umgebung auf dem Blatt "Eigenschaften".

![Ausgehende IP-Adresse][OutboundIPAddress]

Diese Adresse kann auch für ASEs ermittelt werden, die nur eine öffentliche VIP besitzen, indem in der App Service-Umgebung eine App erstellt und dann ein *nslookup* -Vorgang für die Adresse der App durchgeführt wird. Die resultierende IP-Adresse ist sowohl die öffentliche VIP als auch die ausgehende NAT-Adresse der App Service-Umgebung.

Wenn sich der aufgerufene Endpunkt **innerhalb** der Topologie des virtuellen Netzwerks befindet, ist die ausgehende Adresse der aufrufenden App die interne IP-Adresse der einzelnen Computeressourcen, auf denen die App ausgeführt wird.  Es gibt jedoch keine permanente Zuordnung interner IP-Adressen im virtuellen Netzwerk zu Apps.  Apps können sich auf verschiedenen Computeressourcen befinden, und der Pool verfügbarer Computeressourcen in einer App Service-Umgebung kann sich aufgrund von Skalierungsvorgängen ändern.

Da sich eine App Service-Umgebung immer in einem Subnetz befindet, wird jedoch garantiert, dass die interne IP-Adresse einer Computeressource, die zum Ausführen einer App dient, stets im CIDR-Bereich des Subnetzes liegt.  Wenn präzise Zugriffssteuerungslisten (ACLs) oder Netzwerksicherheitsgruppen zum Schützen des Zugriffs auf andere Endpunkte im virtuellen Netzwerk verwendet werden, muss dem Subnetzbereich mit der App Service-Umgebung Zugriff gewährt werden.

Das folgende Diagramm zeigt diese Konzepte ausführlicher:

![Ausgehende Netzwerkadressen][OutboundNetworkAddresses]

Für dieses Diagramm gilt:

* Da die öffentliche VIP-Adresse der App Service-Umgebung 192.23.1.2 lautet, ist dies die ausgehende IP-Adresse, die zum Aufrufen von Internetendpunkten verwendet wird.
* Der CIDR-Bereich des enthaltenden Subnetzes für die App Service-Umgebung ist 10.0.1.0/26.  Andere Endpunkte in derselben virtuellen Netzwerkinfrastruktur erkennen, dass Aufrufe von Apps aus diesem Adressbereich stammen.

## <a name="calls-between-app-service-environments"></a>Aufrufe zwischen App Service-Umgebungen
Ein komplexeres Szenario kann auftreten, wenn Sie mehrere App Service-Umgebungen im gleichen virtuellen Netzwerk bereitstellen und ausgehende Aufrufe von einer App Service-Umgebung in eine andere App Service-Umgebung ausführen.  Diese Art von Aufrufen zwischen App Service-Umgebungen wird auch wie "Internetaufrufe" behandelt.

Das folgende Diagramm zeigt ein Beispiel einer mehrschichtigen Architektur mit Apps in einer App Service-Umgebung (z. B. "Eingangstür"-Web-Apps), die Apps in einer zweiten App Service-Umgebung aufrufen (z. B. interne Back-End-API-Apps, die nicht aus dem Internet zugänglich sein sollen). 

![Aufrufe zwischen App Service-Umgebungen][CallsBetweenAppServiceEnvironments] 

Im obigen Beispiel hat die App Service-Umgebung "ASE One" die ausgehende IP-Adresse 192.23.1.2.  Wenn eine App, die in dieser App Service-Umgebung ausgeführt wird, einen ausgehenden Aufruf einer App durchführt, die in einer zweiten App Service-Umgebung ("ASE Two") im gleichen virtuellen Netzwerk ausgeführt wird, wird der ausgehende Aufruf als "Internetaufruf" behandelt.  Als Ergebnis wird für den in der zweiten App Service-Umgebung eingehenden Netzwerkverkehr die Ursprungs-IP-Adresse 192.23.1.2 angezeigt (d. h. nicht der Subnetzadressbereich der ersten App Service-Umgebung).

Zwar werden Aufrufe zwischen verschiedenen App Service-Umgebungen als "Internetaufrufe" behandelt, dennoch bleibt der Netzwerkverkehr im regionalen Azure-Netzwerk und fließt nicht physisch über das öffentliche Internet, wenn sich beide App Service-Umgebungen in derselben Azure-Region befinden.  Als Ergebnis können Sie eine Netzwerksicherheitsgruppe im Subnetz der zweiten App Service-Umgebung verwenden, um nur eingehende Aufrufe von der ersten App Service-Umgebung (mit der ausgehenden IP-Adresse 192.23.1.2) zuzulassen, und somit die sichere Kommunikation zwischen den App Service-Umgebungen ermöglichen.

## <a name="additional-links-and-information"></a>Zusätzliche Links und Informationen
Einzelheiten zu von App Service-Umgebungen verwendeten eingehenden Ports und zum Verwenden von Sicherheitsgruppen zum Steuern von eingehendem Datenverkehr finden Sie [hier][controllinginboundtraffic].

Details zur Verwendung von benutzerdefinierten Routen zum Gewähren des ausgehenden Internetzugriffs auf App Service-Umgebungen finden Sie in [diesem Artikel][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

