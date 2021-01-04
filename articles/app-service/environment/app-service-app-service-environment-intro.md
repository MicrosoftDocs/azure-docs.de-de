---
title: Einführung in die ASE v1
description: Erfahren Sie etwas über die Features der App Service-Umgebung v1. Dieses Dokument wird nur für Kunden bereitgestellt, die die ASE-Legacyumgebung v1 verwenden.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 878cef39ade0487c03dba83c9e3631fe3c72088f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008622"
---
# <a name="introduction-to-app-service-environment-v1"></a>Einführung in die App Service-Umgebung v1

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v1 behandelt.  Für die App Service-Umgebung steht eine neuere Version zur Verfügung. Diese ist benutzerfreundlicher und basiert auf einer leistungsfähigeren Infrastruktur. Weitere Informationen zu dieser neuen Version finden Sie unter [Einführung in die App Service-Umgebung](intro.md).

## <a name="overview"></a>Übersicht

Eine App Service-Umgebung ist eine Option des [Premium][PremiumTier]-Tarifs von [Azure App Service](../overview.md), die eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung zahlreicher Azure App Service-Apps mit hoher Skalierung einschließlich Web-Apps, Mobile Apps und API-Apps bereitstellt.  

App Service-Umgebungen sind ideal für Anwendungsworkloads mit folgenden Anforderungen:

* Unterstützung sehr vieler Apps
* Isolierung und sicherer Netzwerkzugriff

Kunden können mehrere App Service-Umgebungen innerhalb einer einzelnen Azure-Region sowie über mehrere Azure-Regionen verteilt einrichten.  Dadurch eignen sich App Service-Umgebungen hervorragend für die horizontale Skalierung zustandsloser Anwendungsebenen zur Unterstützung hoher RPS-Workloads.

Aufgrund der Isolierung werden in App Service-Umgebungen nur Anwendungen eines einzelnen Kunden ausgeführt. Die Umgebungen werden zudem immer in einem virtuellen Netzwerk bereitgestellt.  Kunden haben eine genauere Kontrolle über den eingehenden und ausgehenden Anwendungs-Netzwerkdatenverkehr, und Anwendungen können über virtuelle Netzwerke sichere Hochgeschwindigkeitsverbindungen mit lokalen Unternehmensressourcen herstellen.

Eine Übersicht darüber, wie App Service-Umgebungen hochskalierbaren und sicheren Netzwerkzugriff ermöglichen, finden Sie in der [ausführlichen Betrachtung zu AzureCon][AzureConDeepDive] in App Service-Umgebungen.

Eine ausführliche Betrachtung von horizontaler Skalierung mit mehreren App Service-Umgebungen finden Sie im Artikel zum Einrichten einer [geografisch verteilten App][GeodistributedAppFootprint].

Informationen darüber, wie die in AzureCon Deep Dive gezeigte Sicherheitsarchitektur konfiguriert wurde, finden Sie im Artikel über das Implementieren einer [Mehrschicht-Sicherheitsarchitektur](app-service-app-service-environment-layered-security.md) in App Service-Umgebungen.

Der Zugriff von Apps für App-Umgebungen kann durch Upstreamgeräte wie z. B. Web Application Firewalls (WAF) abgegrenzt werden.  Im Artikel zum [Konfigurieren einer Web Application Firewall (WAF) für eine App Service-Umgebung](app-service-app-service-environment-web-application-firewall.md) wird dieses Szenario behandelt.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedizierte Serverressourcen

Alle Serverressourcen in einer App Service-Umgebung sind ausschließlich für ein einzelnes Abonnement reserviert. Eine App Service-Umgebung kann mit bis zu fünfzig (50) Serverressourcen konfiguriert werden, die ausschließlich von einer einzelnen Anwendung genutzt werden.

Eine App Service-Umgebung besteht aus einem Front-End-Serverressourcenpool sowie ein bis drei Worker-Serverressourcenpools.

Der Front-End-Pool enthält Serverressourcen, die für die TLS-Terminierung sowie für den automatischen Lastenausgleich von App-Anforderungen in einer App Service-Umgebung zuständig sind.

Jeder Workerpool enthält Computeressourcen, die [App Service-Plänen][AppServicePlan] zugeordnet sind, die wiederum eine oder mehrere Azure App Service-Apps enthalten.  Da in einer App Service-Umgebung bis zu drei verschiedenen Workerpools vorhanden sein können, können Sie flexibel verschiedene Serverressourcen für jeden Workerpool auswählen.  

Beispielsweise können Sie einen Workerpool mit weniger leistungsfähigen Computeressourcen für App Service-Pläne erstellen, die für Entwicklungs- oder Test-Apps vorgesehen sind.  Ein zweiter (oder sogar dritter) Workerpool kann leistungsfähigere Serverressourcen für App Service-Pläne nutzen, die für Produktions-Apps ausgeführt werden.

Ausführliche Informationen zu der Menge von Computeressourcen, die den Front-End- und Workerpools zur Verfügung stehen, finden Sie unter [Konfigurieren einer App Service-Umgebung][HowToConfigureanAppServiceEnvironment].  

Weitere Informationen zu den verfügbaren Computeressourcengrößen, die in einer App Service-Umgebung unterstützt werden, finden Sie auf der Seite [App Service – Preise][AppServicePricing]. Sehen Sie sich die verfügbaren Optionen für App Service-Umgebungen im Premium-Tarif an.

## <a name="virtual-network-support"></a>Unterstützung für virtuelle Netzwerke

Eine App Service-Umgebung kann **entweder** in einem virtuellen Netzwerk von Azure Resource Manager **oder** einem virtuellen Netzwerk eines klassischen Bereitstellungsmodells ([weitere Informationen zu virtuellen Netzwerken][MoreInfoOnVirtualNetworks]) erstellt werden.  Da eine App Service-Umgebung sich immer in einem virtuellen Netzwerk, genauer gesagt, in einem Subnetz eines virtuellen Netzwerks befindet, können Sie die Sicherheitsfunktionen virtueller Netzwerke zum Steuern sowohl der eingehenden als auch der ausgehenden Netzwerkkommunikation nutzen.  

Eine App Service-Umgebung kann entweder für Internetzugriff mit einer öffentlichen IP-Adresse oder für die ausschließliche interne Verwendung mit einer Azure ILB-Adresse (Internal Load Balancer) eingerichtet werden.

Mithilfe von [Netzwerksicherheitsgruppen][NetworkSecurityGroups] können Sie die eingehende Netzwerkkommunikation mit dem Subnetz einschränken, das eine App Service-Umgebung enthält.  Dadurch können Sie Apps hinter Upstreamgeräten und -diensten ausführen wie z. B. Web Application Firewalls und Netzwerk-SaaS-Anbietern.

Apps müssen häufig auch auf Unternehmensressourcen wie interne Datenbanken und Webdienste zugreifen.  Ein gängiger Ansatz besteht darin, diese Endpunkte nur für internen Netzwerkdatenverkehr verfügbar zu machen, der innerhalb eines virtuellen Azure-Netzwerks fließt.  Sobald eine App Service-Umgebung in dasselbe virtuelle Netzwerk eingebunden wird wie die internen Dienste, können die in der Umgebung ausgeführten Apps auf diese zugreifen. Dies gilt auch für Endpunkte, die über [Site-to-Site][SiteToSite]- und [Azure ExpressRoute][ExpressRoute]-Verbindungen erreichbar sind.

Weitere Details zur Funktionsweise der App Service-Umgebungen mit virtuellen und lokalen Netzwerken finden Sie in den folgenden Artikeln: [Netzwerkarchitektur][NetworkArchitectureOverview], [Steuern von eingehendem Datenverkehr][ControllingInboundTraffic] und [Sicheres Verbinden mit Back-End-Ressourcen][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Erstellen einer App Service-Umgebung][HowToCreateAnAppServiceEnvironment]

Eine Übersicht über die Netzwerkarchitektur der App Service-Umgebung finden Sie im Artikel [Übersicht über die Netzwerkarchitektur][NetworkArchitectureOverview].

Informationen zur Verwendung einer App Service-Umgebung mit ExpressRoute finden Sie im Artikel [ExpressRoute und App Service-Umgebungen][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: ../../virtual-network/virtual-networks-faq.md
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: ../../logic-apps/logic-apps-overview.md
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->