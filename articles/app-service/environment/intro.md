---
title: Einführung
description: Hier erfahren Sie, wie Azure App Service-Umgebungen Ihnen helfen, Ihre Apps in einer vollständig isolierten und dedizierten Umgebung zu skalieren, zu schützen und zu optimieren.
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 6fff19498e9ca70991d3190165df70a48136f502
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631137"
---
# <a name="introduction-to-the-app-service-environments"></a>Einführung in die App Service-Umgebungen #
## <a name="overview"></a>Übersicht ##

Die Azure App Service-Umgebung ist ein Feature von Azure App Service, das eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung von App Service-Apps mit umfangreicher Skalierung bereitstellt. Über diese Funktion können folgende Elemente gehostet werden:

* Windows-Web-Apps
* Linux-Web-Apps 
* Docker-Container
* Mobile Apps
* Functions

App Service-Umgebungen (App Service Environments, ASEs) sind ideal geeignet für Anwendungsworkloads mit folgenden Anforderungen:

* Unterstützung sehr vieler Apps
* Isolierung und sicherer Netzwerkzugriff
* Hohe Speicherauslastung

Kunden können mehrere ASEs innerhalb einer einzelnen Azure-Region oder über mehrere Azure-Regionen verteilt einrichten. Durch diese Flexibilität eignen sich ASEs hervorragend für die horizontale Skalierung zustandsloser Anwendungsebenen zur Unterstützung von Workloads mit hohen Anforderungen pro Sekunde (Requests per Second, RPS).

ASEs hosten Anwendungen von nur einem Kunden in einem seiner VNETs. Kunden haben präzise Kontrolle über den eingehenden und ausgehenden Netzwerkdatenverkehr der Anwendung. Anwendungen können schnelle, sichere Verbindungen über VPNs mit lokalen Unternehmensressourcen einrichten.

* Für ASE gilt ein eigener Tarif. Informieren Sie sich darüber, wie das [isolierte Angebot](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) zur Hyperskalierung und Sicherheit beiträgt.
* [App Service-Umgebungen v2](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) bieten eine Umgebung zum Schutz Ihrer Apps in einem Subnetz Ihres Netzwerks sowie eine eigene private Azure App Service-Bereitstellung.
* Für die horizontale Skalierung können mehrere ASEs verwendet werden. Weitere Informationen finden Sie unter [Vorgehensweise zum Einrichten einer geografisch verteilten App-Umgebung](app-service-app-service-environment-geo-distributed-scale.md).
* ASEs können verwendet werden, um eine Sicherheitsarchitektur zu konfigurieren, wie in AzureCon Deep Dive dargestellt wird. Informationen darüber, wie die in AzureCon Deep Dive gezeigte Sicherheitsarchitektur konfiguriert wurde, finden Sie im Artikel zum [Implementieren einer Sicherheitsarchitektur mit Ebenen](app-service-app-service-environment-layered-security.md) in App Service-Umgebungen.
* Der Zugriff von Apps in ASEs kann durch Upstreamgeräte wie z.B. Web Application Firewalls (WAFs) abgegrenzt werden. Weitere Informationen finden Sie unter [Web Application Firewall (WAF)][AppGW].
* App Service-Umgebungen können durch Anheften von Zonen in Verfügbarkeitszonen bereitgestellt werden.  Ausführlichere Informationen finden Sie unter [Unterstützung der App Service-Umgebung für Verfügbarkeitszonen][ASEAZ].

## <a name="dedicated-environment"></a>Dedizierte Umgebung ##

Eine ASE wird dediziert für ein einzelnes Abonnement verwendet und kann 100 App Service-Planinstanzen hosten. Dabei kann es sich um 100 Instanzen in einem einzelnen App Service-Plan oder auch um 100 App Service-Einzelinstanzpläne und alles dazwischen handeln.

Eine ASE besteht aus Front-Ends und Worker. Front-Ends sind für die HTTP/HTTPS-Beendigung und den automatischen Lastenausgleich von App-Anforderungen in einer ASE zuständig. Front-Ends werden automatisch hinzugefügt, wenn die App Service-Pläne in der ASE horizontal hochskaliert werden.

Worker sind Rollen, die Kunden-Apps hosten. Worker sind in drei festen Größen verfügbar:

* Eine vCPU/3,5 GB RAM
* Zwei vCPUs/7 GB RAM
* Vier vCPUs/14 GB RAM

Kunden müssen keine Front-Ends und Worker verwalten. Die gesamte Infrastruktur wird automatisch hinzugefügt, wenn Kunden ihre App Service-Pläne aufskalieren. Wenn App Service-Pläne erstellt oder in einer ASE skaliert werden, wird die erforderliche Infrastruktur nach Bedarf hinzugefügt bzw. entfernt.

Es gibt eine monatliche Pauschalgebühr für eine ASE, mit der die Infrastruktur abgedeckt ist. Dies ändert sich nicht mit der Größe der ASE. Darüber hinaus fallen Kosten pro vCPU-App Service-Plan an. Alle in einer ASE gehosteten Apps befinden sich in der isolierten Preis-SKU. Um Informationen zu den Preisen für eine ASE zu erhalten, lesen Sie die Seite [App Service-Preise][Pricing], und überprüfen Sie die verfügbaren Optionen für ASEs.

## <a name="virtual-network-support"></a>Unterstützung für virtuelle Netzwerke ##

Das ASE-Feature ist eine Bereitstellung von Azure App Service direkt im virtuellen Azure Resource Manager-Netzwerk eines Kunden. Weitere Informationen zu virtuellen Azure-Netzwerken finden Sie unter [Virtuelle Azure-Netzwerke – FAQs](../../virtual-network/virtual-networks-faq.md). Eine ASE befindet sich immer in einem virtuellen Netzwerk, genauer gesagt, in einem Subnetz eines virtuellen Netzwerks. Mithilfe der Sicherheitsfunktionen virtueller Netzwerke können Sie ein- und ausgehende Netzwerkkommunikation für Ihre Apps steuern.

Eine ASE kann entweder für Internetzugriff mit einer öffentlichen IP-Adresse oder für die ausschließliche interne Verwendung mit einer Azure-ILB-Adresse (Internal Load Balancer) eingerichtet werden.

Mithilfe von [Netzwerksicherheitsgruppen][NSGs] können Sie die eingehende Netzwerkkommunikation mit dem Subnetz einschränken, das eine ASE enthält. Durch NSGs können Sie Apps hinter Upstreamgeräten und -diensten ausführen, wie WAFs und Netzwerk-SaaS-Anbietern.

Apps müssen häufig auch auf Unternehmensressourcen wie interne Datenbanken und Webdienste zugreifen. Wenn Sie die ASE in einem virtuellen Netzwerk bereitstellen, das über eine VPN-Verbindung mit dem lokalen Netzwerk verfügt, können die Apps in der ASE auf die lokalen Ressourcen zugreifen. Diese Funktion besteht unabhängig davon, ob es sich um ein [Site-to-Site-VPN](../../vpn-gateway/vpn-gateway-multi-site.md) oder ein [Azure ExpressRoute-VPN](https://azure.microsoft.com/services/expressroute/) handelt.

Weitere Informationen zur Funktionsweise von ASEs mit virtuellen Netzwerken und lokalen Netzwerken finden Sie unter [Überlegungen zu Netzwerken mit einer App Service-Umgebung][ASENetwork].

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service-Umgebung v1 ##

Es gibt zwei Versionen der App Service-Umgebung: ASEv1 und ASEv2. Die oben aufgeführten Informationen basieren auf ASEv2. In diesem Abschnitt werden die Unterschiede zwischen ASEv1 und ASEv2 aufgezeigt. 

In ASEv1 müssen Sie alle Ressourcen manuell verwalten. Dies schließt Front-Ends, Worker und IP-Adressen ein, die für IP-basiertes SSL verwendet werden. Bevor Sie Ihren App Service-Plan aufskalieren können, müssen Sie zunächst den Workerpool aufskalieren, den Sie zum Hosten verwenden möchten.

ASEv1 verwendet ein anderes Preismodell als ASEv2. In ASEv1 bezahlen Sie jede zugewiesene vCPU. Dazu gehören vCPUs für Front-Ends oder Worker, die keine Workloads hosten. In ASEv1 beträgt die maximale Standardskalierungsgröße einer ASE insgesamt 55 Hosts. Dazu gehören Worker und Front-Ends. ASEv1 hat den Vorteil, dass die Bereitstellung in einem klassischen virtuellen Netzwerk sowie in einem virtuellen Resource Manager-Netzwerk möglich ist. Weitere Informationen zu ASEv1 finden Sie unter [Einführung in die App Service-Umgebung v1][ASEv1Intro].

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html