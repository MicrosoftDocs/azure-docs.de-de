---
title: ExpressRoute für Cloudlösungsanbieter | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen für Cloudlösungsanbieter, die Azure-Dienste und ExpressRoute in ihre Angebote integrieren möchten.
services: expressroute
author: richcar
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: richcar
ms.custom: seodec18
ms.openlocfilehash: a03ab7bbdadad2728f54127583583c22bd2ec07a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367545"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute für Cloudlösungsanbieter (Cloud Solution Providers, CSPs)
Mit den von Microsoft bereitgestellten Diensten mit Hyperskalierung können traditionelle Wiederverkäufer und Distributoren (CSPs) schnell neue Dienste und Lösungen für Ihre Kunden bereitstellen, ohne in die Entwicklung dieser neuen Dienste investieren zu müssen. Microsoft stellt zudem Programme und APIs zur direkten Verwaltung dieser neuen Dienste bereit, sodass der Cloudlösungsanbieter (Cloud Solution Provider, CSP) Microsoft Azure-Ressourcen im Auftrag Ihrer Kunden verwalten kann. Eine dieser Ressourcen ist ExpressRoute. Mit ExpressRoute kann der CSP vorhandene Kundenressourcen mit Azure-Diensten verknüpfen. ExpressRoute ist eine private Hochgeschwindigkeitsverbindung für die Kommunikation mit Diensten in Azure. 

Sie besteht aus einem Verbindungspaar für Hochverfügbarkeit, das den Abonnements einzelner Kunden zugeordnet ist und nicht von mehreren Kunden gemeinsam genutzt werden kann. Jede Verbindung muss in einem anderen Router enden, um Hochverfügbarkeit zu gewährleisten.

> [!NOTE]
> Bandbreite und Verbindung sind bei ExpressRoute gedeckelt. Bei umfangreichen/komplexen Implementierungen werden daher mehrere ExpressRoute-Verbindungen für einen einzelnen Kunden benötigt.
> 
> 

Microsoft Azure stellt eine immer größere Anzahl von Diensten bereit, die Sie Ihren Kunden anbieten können. ExpressRoute bietet Zugriff auf die Microsoft Azure-Umgebung mit hoher Geschwindigkeit und geringer Wartezeit, sodass Sie und Ihre Kunden von diesen Diensten profitieren können.

## <a name="microsoft-azure-management"></a>Verwaltung von Microsoft Azure
Microsoft stellt Cloudlösungsanbietern APIs zur Verfügung, die sich programmatisch in Ihre eigenen Dienstverwaltungssysteme integrieren lassen und so die Verwaltung der Azure-Kundenabonnements ermöglichen. Die unterstützten Verwaltungsfunktionen finden Sie [hier](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Verwaltung von Microsoft Azure-Ressourcen
Die Verwaltung des Abonnements wird durch den Vertrag geregelt, den Sie mit Ihrem Kunden geschlossen haben. Der Cloud-Lösungsanbieter kann die Erstellung und Verwaltung von Ressourcen direkt verwalten, oder der Kunde kann die Kontrolle über das Microsoft Azure-Abonnement erhalten und die Azure-Ressourcen nach Bedarf erstellen. Wenn der Kunde die Erstellung von Ressourcen unter seinem Microsoft Azure-Abonnement selbst verwaltet, verwendet er eines von zwei Modellen: „*Durchleitungsmodell*“ oder „*Direktverbindungsmodell*“. Diese Modelle werden im Anschluss ausführlich beschrieben.  

### <a name="connect-through-model"></a>Durchleitungsmodell
![alt text](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

Beim Durchleitungsmodell stellt der CSP eine direkte Verbindung zwischen Ihrem Datencenter und dem Azure-Abonnement Ihres Kunden her. Die direkte Verbindung wird unter Verwendung von ExpressRoute hergestellt und verbindet Ihr Netzwerk mit Azure. Anschließend stellt Ihr Kunde eine Verbindung mit Ihrem Netzwerk her. In diesem Szenario muss der Kunde durch das CSP-Netzwerk geschleust werden, um auf die Azure-Dienste zugreifen zu können. 

Falls der Kunde über weitere Azure-Abonnements verfügt, die nicht von Ihnen verwaltet werden, verwendet er das öffentliche Internet oder eine eigene private Verbindung, um eine Verbindung mit diesen Diensten herzustellen, die nicht im Rahmen des CSP-Abonnements bereitgestellt werden. 

Für durch den CSP verwaltete Azure-Dienste wird vorausgesetzt, dass der CSP über einen zuvor eingerichteten Kundenidentitätsspeicher verfügt. Dieser Identitätsspeicher wird dann in Azure Active Directory repliziert, um die Verwaltung des CSP-Abonnements per AOBO (Administrate-On-Behalf-Of) zu ermöglichen. Zentrale Faktoren für dieses Szenario sind etwa, dass ein bestimmter Partner oder Dienstanbieter eine Beziehung mit dem Kunden etabliert hat, der Kunde aktuell Dienste des Anbieters nutzt oder der Partner eine Kombination aus vom Anbieter gehosteten und von Azure gehosteten Lösungen anbieten möchte, um für Flexibilität zu sorgen und Kundenherausforderungen zu bewältigen, die der CSP nicht alleine bewältigen kann. Dieses Modell wird in der **Abbildung** weiter unten veranschaulicht.

![alt text](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Direktverbindungsmodell
![alt text](./media/expressroute-for-cloud-solution-providers/connect-to.png)

Beim Direktverbindungsmodell stellt der Dienstanbieter mithilfe von ExpressRoute über das Kundennetzwerk eine direkte Verbindung zwischen dem Datencenter seines Kunden und dem vom CSP bereitgestellten Azure-Abonnement her.

> [!NOTE]
> Dazu muss der Kunde die ExpressRoute-Verbindung erstellen und verwalten.  
> 
> 

Für dieses Verbindungsszenario ist es erforderlich, dass der Kunde die Verbindung direkt über ein Kundennetzwerk herstellt, um auf das vom CSP verwaltete Azure-Abonnement zuzugreifen. Die hierfür verwendete direkte Netzwerkverbindung wird entweder vollständig oder teilweise vom Kunden erstellt, besessen und verwaltet. Für diese Kunden wird angenommen, dass beim Anbieter derzeit kein Kundenidentitätsspeicher eingerichtet wurde. Der Anbieter unterstützt den Kunden beim Replizieren des aktuellen Identitätsspeichers in Azure Active Directory für die Verwaltung des Abonnements per AOBO. Zentrale Faktoren für dieses Szenario sind etwa, dass ein bestimmter Partner oder Dienstanbieter eine Beziehung mit dem Kunden etabliert hat, der Kunde aktuell Dienste des Anbieters nutzt oder der Partner Dienste bereitstellen möchte, die ausschließlich auf von Azure gehosteten Lösungen basieren, sodass kein Anbieterdatencenter und keine Infrastruktur benötigt werden.

![alt text](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Die Entscheidung für eine dieser beiden Optionen hängt von den Anforderungen Ihres Kunden sowie davon ab, ob Sie Azure-Dienste bereitstellen müssen. Details zu diesen Modellen sowie zur dazugehörigen rollenbasierten Zugriffssteuerung, zum Netzwerk und zu Identitätsentwurfsmustern finden Sie unter folgenden Links:

* **Rollenbasierte Zugriffssteuerung** (Role Based Access Control, RBAC): RBAC basiert auf Azure Active Directory.  Weitere Informationen zu Azure RBAC finden Sie [hier](../role-based-access-control/role-assignments-portal.md).
* **Netzwerk** : Behandelt die verschiedenen Netzwerkthemen in Microsoft Azure.
* **Azure Active Directory (Azure AD)** : Azure AD stellt die Identitätsverwaltung für Microsoft Azure und SaaS-Drittanbieteranwendungen bereit. Weitere Informationen zu Azure AD finden Sie [hier](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Netzwerkgeschwindigkeiten
ExpressRoute unterstützt Netzwerkgeschwindigkeiten zwischen 50 Mbit/s und 10 Gbit/s. Dadurch können Kunden eine für ihre individuelle Umgebung benötigte Netzwerkbandbreite erwerben.

> [!NOTE]
> Die Netzwerkbandbreite kann bedarfsgerecht ohne Unterbrechung der Kommunikation erhöht werden. Zur Verringerung der Netzwerkgeschwindigkeit muss die Verbindung dagegen getrennt und mit der geringeren Netzwerkgeschwindigkeit erneut hergestellt werden.  
> 
> 

ExpressRoute unterstützt die Verknüpfung mehrerer virtueller Netzwerke zu einer einzelnen ExpressRoute-Verbindung, um die Nutzung der schnelleren Verbindungen zu optimieren. Eine einzelne ExpressRoute-Verbindung kann von mehreren Azure-Abonnements des gleichen Kunden genutzt werden.

## <a name="configuring-expressroute"></a>Konfigurieren von ExpressRoute
ExpressRoute kann so konfiguriert werden, dass über eine einzelne ExpressRoute-Verbindung drei Arten von Datenverkehr ([Routingdomänen](#expressroute-routing-domains)) abgewickelt werden können. Dieser Datenverkehr wird in Microsoft-Peering, öffentliches Azure-Peering und privates Peering unterteilt. Sie können entweder einzelne oder alle Arten von Datenverkehr über eine einzelne ExpressRoute-Verbindung senden oder mehrere ExpressRoute-Verbindungen verwenden. Diese Entscheidung ist abhängig vom Umfang der ExpressRoute-Verbindung und der vom Kunden benötigten Isolation. Bei bestimmten Kunden dürfen öffentlicher und privater Datenverkehr aus Sicherheitsgründen nicht über die gleiche Verbindung abgewickelt werden.

### <a name="connect-through-model"></a>Durchleitungsmodell
Bei einer Durchleitungskonfiguration sind Sie für das gesamte Netzwerkfundament zuständig, das erforderlich ist, um die Datencenterressourcen Ihrer Kunden mit den in Azure gehosteten Abonnements zu verbinden. Jeder Kunde, der Azure-Funktionen verwenden möchte, benötigt eine eigene, von Ihnen verwaltete ExpressRoute-Verbindung. Sie verwenden die gleichen Methoden, mit denen auch der Kunde die ExpressRoute-Verbindung bereitstellen würde. Sie führen die Schritte aus, die im Artikel [ExpressRoute-Workflows](expressroute-workflows.md) für die Verbindungsbereitstellung und Verbindungszustände erläutert werden. Anschließend konfigurieren Sie die Routen des Border Gateway-Protokolls (BGP), um den Datenverkehrsfluss zwischen dem lokalen Netzwerk und dem virtuellen Azure-Netzwerk zu steuern.

### <a name="connect-to-model"></a>Direktverbindungsmodell
Bei einer Direktverbindungskonfiguration verfügt Ihr Kunde bereits über eine Verbindung mit Azure oder initiiert eine Verbindung mit dem Internetdienstanbieter, der ExpressRoute direkt über das Datencenter Ihres Kunden mit Azure (anstatt mit Ihrem Datencenter) verknüpft. Zur Einleitung des Bereitstellungsprozesses führt Ihr Kunde die gleichen Schritte durch wie beim oben beschriebenen Durchleitungsmodell. Nach Einrichtung der Verbindung muss Ihr Kunde die lokalen Router konfigurieren, um sowohl auf Ihr Netzwerk als auch auf virtuelle Azure-Netzwerke zugreifen zu können.

Sie können den Kunden beim Einrichten der Verbindung und beim Konfigurieren der Routen unterstützen, um den Ressourcen in Ihren Datencentern die Kommunikation mit den Kundenressourcen in Ihrem Datencenter (oder mit den in Azure gehosteten Ressourcen) zu ermöglichen.

## <a name="expressroute-routing-domains"></a>ExpressRoute-Routingdomänen
ExpressRoute bietet drei Routingdomänen: öffentliches Peering, privates Peering und Microsoft-Peering. Jede der Routingdomänen ist mit identischen Routern in einer Aktiv/Aktiv-Konfiguration für Hochverfügbarkeit konfiguriert. Ausführlichere Informationen zu ExpressRoute-Routingdomänen finden Sie [hier](expressroute-circuit-peerings.md).

Sie können benutzerdefinierte Routenfilter definieren, um nur die gewünschten oder benötigten Routen zuzulassen. Um weitere Informationen zu erhalten oder zu erfahren, wie Sie diese Änderungen durchführen, lesen Sie diesen Artikel: [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung mit PowerShell](expressroute-howto-routing-classic.md) mit weiteren Details zu Routingfiltern.

> [!NOTE]
> Microsoft-Peering und öffentliches Peering müssen über eine öffentliche IP-Adresse des Kunden oder CSPs erfolgen und sämtlichen definierten Regeln entsprechen. Weitere Informationen finden Sie auf der Seite [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md) .  
> 
> 

## <a name="routing"></a>Routing
ExpressRoute stellt über das Gateway des virtuellen Azure-Netzwerks eine Verbindung mit den Azure-Netzwerken her. Netzwerkgateways bieten Routing für virtuelle Azure-Netzwerke.

Bei der Erstellung eines virtuellen Azure-Netzwerks wird auch eine Standardroutingtabelle für das virtuelle Netzwerk erstellt, um Datenverkehr an Subnetze des virtuellen Netzwerks bzw. aus Subnetzen des virtuellen Netzwerks weiterzuleiten. Sollte die Standardroutingtabelle für die Lösung nicht ausreichen, können benutzerdefinierte Routen zur Weiterleitung von ausgehendem Datenverkehr an benutzerdefinierte Geräte oder zur Blockierung von Routen zu bestimmten Subnetzen oder externen Netzwerken erstellt werden.

### <a name="default-routing"></a>Standardrouting
Die Standardroutingtabelle enthält die folgenden Routen:

* Routing innerhalb eines Subnetzes
* Subnetz zu Subnetz innerhalb des virtuellen Netzwerks
* An das Internet
* Virtuelles Netzwerk zu virtuellem Netzwerk mittels VPN-Gateway
* Virtuelles Netzwerk zu lokalem Netzwerk mittels VPN- oder ExpressRoute-Gateway

![alt text](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Benutzerdefiniertes Routing
Benutzerdefinierte Routen ermöglichen die Steuerung des ausgehenden Datenverkehrs aus dem zugewiesenen Subnetz an andere Subnetze im virtuellen Netzwerk oder über eines der anderen vordefinierten Gateways (ExpressRoute, Internet oder VPN). Die Standardroutingtabelle des Systems kann durch eine benutzerdefinierte Routingtabelle ersetzt werden, um die Standardrouten durch benutzerdefinierte Routen zu ersetzen. Mithilfe von benutzerdefinierten Routen können Kunden spezifische Routen zu Geräten (etwa Firewall- oder Angriffserkennungsgeräte) erstellen oder den Zugriff auf bestimmte Subnetze aus dem Subnetz blockieren, das die benutzerdefinierte Route hostet. Eine Übersicht über benutzerdefinierte Routen finden Sie [hier](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Sicherheit
Je nach verwendetem Modell (Direktverbindung oder Durchleitung) definiert Ihr Kunde die Sicherheitsrichtlinien in seinem virtuellen Netzwerk selbst oder teilt dem CSP seine Sicherheitsanforderungen mit, damit dieser seine virtuellen Netzwerke einrichten kann. Folgende Sicherheitskriterien können definiert werden:

1. **Kundenisolation** : Die Azure-Plattform sorgt für Kundenisolation, indem Kunden-ID und Informationen zum virtuellen Netzwerk in einer sicheren Datenbank gespeichert und zur Kapselung des jeweiligen Datenverkehrs eines Kunden in einem GRE-Tunnel verwendet werden.
2. **Netzwerksicherheitsgruppen-Regeln** dienen zum Definieren des zulässigen ein- und ausgehenden Datenverkehrs für die Subnetze innerhalb virtueller Azure-Netzwerke. Standardmäßig enthält die NSG Blockierungsregeln, die eingehenden Datenverkehr aus dem Internet blockieren, und Zulassungsregeln für Datenverkehr innerhalb eines virtuellen Netzwerks. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie [hier](https://azure.microsoft.com/blog/network-security-groups/).
3. **Tunnelerzwingung** : Dient dazu, für das Internet bestimmten Datenverkehr aus Azure über die ExpressRoute-Verbindung an das lokale Datencenter umzuleiten. Weitere Informationen zur Tunnelerzwingung finden Sie [hier](expressroute-routing.md#advertising-default-routes).  
4. **Verschlüsselung**: ExpressRoute-Verbindungen werden zwar nur für einen bestimmten Kunden verwendet, aber es besteht die Möglichkeit, dass die Sicherheitsmaßnahmen des Netzwerkanbieters überwunden werden, sodass ein Eindringling Zugang zum Paketdatenverkehr erhält. Als Gegenmaßnahme kann ein Kunde oder CSP den über die Verbindung abgewickelten Datenverkehr durch Definieren von IPSec-Tunnelmodusrichtlinien für den gesamten Datenverkehr zwischen den lokalen Ressourcen und Azure verschlüsseln (siehe optionale Tunnelmodus-IPSec für Kunde 1 in Abbildung 5: ExpressRoute-Sicherheit, oben). Alternativ kann an jedem Endpunkt der ExpressRoute-Verbindung ein Firewallgerät verwendet werden. Hierzu müssen zur Verschlüsselung des Datenverkehrs über die ExpressRoute-Verbindung an beiden Enden zusätzliche Firewall-VMs/-geräte von Drittanbietern installiert werden.

![alt text](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Nächste Schritte
Der Cloud Solution Provider-Dienst ermöglicht es Ihnen, Ihren Nutzen für Kunden ohne Anschaffung teurer Infrastruktur und Funktionen zu erhöhen und dabei gleichzeitig Ihre Position als primärer Outsourcinganbieter zu festigen. Zur nahtlosen Integration von Microsoft Azure steht die CSP-API zur Verfügung, mit der Sie die Verwaltung von Microsoft Azure in Ihre vorhandenen Verwaltungsframeworks integrieren können.  

Weitere Informationen finden Sie unter den folgenden Links:

[Azure im Cloud Solution Provider-Programm](https://docs.microsoft.com/azure/cloud-solution-provider)  
[Vorbereitung auf Transaktionen als Cloud Solution Provider](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch)  
[Microsoft Cloud Solution Provider – Ressourcen](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources)
