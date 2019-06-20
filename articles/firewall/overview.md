---
title: Was ist Azure Firewall?
description: Erfahren Sie mehr über Azure Firewall-Features.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 6/5/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 4b33174b20cdf42e29cdb5b4786122513d2c6080
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753738"
---
# <a name="what-is-azure-firewall"></a>Was ist Azure Firewall?

Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Es ist eine vollständig zustandsbehaftete Firewall-as-a-Service mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. 

![Firewallübersicht](media/overview/firewall-threat.png)

Sie können Richtlinien zur Anwendungs- und Netzwerkkonnektivität übergreifend für Abonnements und virtuelle Netzwerke zentral erstellen, erzwingen und protokollieren. Azure Firewall verwendet eine statische öffentliche IP-Adresse für Ihre virtuellen Netzwerkressourcen, die es außenstehenden Firewalls ermöglicht, Datenverkehr aus Ihrem virtuellen Netzwerk zu identifizieren.  Der Dienst ist für Protokollierung und Analyse vollständig in Azure Monitor integriert.

## <a name="features"></a>Features

Azure Firewall bietet die folgenden Features:

### <a name="built-in-high-availability"></a>Integrierte Hochverfügbarkeit

Hochverfügbarkeit ist integriert, sodass keine zusätzlichen Lastenausgleichsmodule erforderlich sind und Sie nichts konfigurieren müssen.

### <a name="unrestricted-cloud-scalability"></a>Uneingeschränkte Cloudskalierbarkeit

Azure Firewall kann entsprechend Ihren Anforderungen zentral hochskaliert werden, um einem sich ändernden Netzwerkdatenverkehr zu entsprechen, sodass Sie nicht für Ihre Spitzenlasten budgetieren müssen.

### <a name="application-fqdn-filtering-rules"></a>FQDN-Anwendungsfilterregeln

Sie können den ausgehenden HTTP/S-Datenverkehr auf eine angegebene Liste vollständig qualifizierter Domänennamen (FQDN) einschließlich Platzhalter beschränken. Dieses Feature erfordert keine SSL-Beendigung.

### <a name="network-traffic-filtering-rules"></a>Filterregeln für den Netzwerkdatenverkehr

Sie können Netzwerkfilterregeln zum *Zulassen* oder *Verweigern* nach Quell- und Ziel-IP-Adresse, Port und Protokoll zentral erstellen. Azure Firewall ist vollständig zustandsbehaftet, sodass zwischen legitimen Paketen für verschiedene Arten von Verbindungen unterschieden werden kann. Regeln werden übergreifend für mehrere Abonnements und virtuelle Netzwerke erzwungen und protokolliert.

### <a name="fqdn-tags"></a>FQDN-Tags

FQDN-Tags erleichtern es Ihnen, Netzwerkdatenverkehr bekannter Azure-Dienste durch die Firewall zuzulassen. Angenommen, Sie möchten Netzwerkdatenverkehr von Windows Update durch die Firewall zulassen. Sie erstellen eine entsprechende Anwendungsregel, und schließen das Windows Update-Tag ein. Jetzt kann der Netzwerkdatenverkehr von Windows Update durch Ihre Firewall fließen.

### <a name="service-tags"></a>Diensttags

Ein Diensttag steht für eine Gruppe von IP-Adressen und hat die Aufgabe, bei der Erstellung von Sicherheitsregeln die Komplexität zu verringern. Sie können weder ein eigenes Diensttag erstellen noch angeben, welche IP-Adressen in einem Tag enthalten sind. Microsoft verwaltet die Adresspräfixe, die mit dem Diensttag abgedeckt werden, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

### <a name="threat-intelligence"></a>Bedrohungsanalyse

Die Filterung auf der Grundlage der Bedrohungsanalyse kann für Ihre Firewall aktiviert werden, damit diese Sie bei Datenverkehr an bekannte schädliche und von bekannten schädlichen IP-Adressen oder Domänen warnt und diesen verweigert. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

### <a name="outbound-snat-support"></a>SNAT-Unterstützung für ausgehenden Datenverkehr

Alle IP-Adressen für ausgehenden Datenverkehr des virtuellen Netzwerks werden in die öffentliche IP-Adresse der Azure Firewall übersetzt (Source Network Address Translation). Sie können Datenverkehr aus Ihrem virtuellen Netzwerk an Remoteziele im Internet identifizieren und zulassen.

### <a name="inbound-dnat-support"></a>DNAT-Unterstützung für eingehenden Datenverkehr

Der eingehende Netzwerkdatenverkehr zur öffentlichen IP-Adresse Ihrer Firewall wird in die privaten IP-Adressen in Ihren virtuellen Netzwerken übersetzt (Destination Network Address Translation) und gefiltert.

### <a name="azure-monitor-logging"></a>Azure Monitor-Protokollierung

Alle Ereignisse sind in Azure Monitor integriert, sodass Sie Protokolle in einem Speicherkonto archivieren sowie Ereignisse an Ihren Event Hub streamen oder an Azure Monitor-Protokolle senden können.

## <a name="known-issues"></a>Bekannte Probleme

Azure Firewall weist die folgenden bekannten Probleme auf:

|Problem  |BESCHREIBUNG  |Lösung  |
|---------|---------|---------|
|Konflikt mit Just-in-Time (JIT)-Funktion von Azure Security Center (ASC)|Wenn auf einen virtuellen Computer mithilfe von JIT zugegriffen wird und sich dieser in einem Subnetz mit einer benutzerdefinierten Route befindet, die auf Azure Firewall als ein Standardgateway verweist, funktioniert ASC JIT nicht. Dies ist eine Folge des asymmetrischen Routings: Ein Paket trifft über die öffentliche IP-Adresse des virtuellen Computers ein (JIT hat den Zugriff geöffnet), aber der Rückgabepfad verläuft über die Firewall, die das Paket verwirft, da keine Sitzung in der Firewall erstellt wurde.|Um dieses Problem zu umgehen, platzieren Sie die virtuellen JIT-Computer in einem separaten Subnetz, das keine benutzerdefinierte Route zur Firewall aufweist.|
Netzwerkfilterregeln für andere Protokolle als TCP/UDP (z.B. ICMP) funktionieren nicht für den Internetdatenverkehr|Netzwerkfilterregeln für andere Protokolle als TCP/UDP funktionieren nicht mit SNAT für Ihre öffentliche IP-Adresse. Nicht-TCP/UDP-Protokolle werden zwischen Spoke-Subnetzen und VNets unterstützt.|Azure Firewall verwendet Standard Load Balancer, [das SNAT für IP-Protokolle derzeit nicht unterstützt](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Wir prüfen Möglichkeiten, um dieses Szenario in einer zukünftigen Version zu unterstützen.|
|Fehlende PowerShell- und CLI-Unterstützung für ICMP|Azure PowerShell und CLI weisen keine Unterstützung von ICMP als gültiges Protokoll in Netzwerkregeln auf.|Es ist weiterhin möglich, ICMP über das Portal und die REST-API als Protokoll zu verwenden. Wir arbeiten daran, ICMP in Kürze in PowerShell und CLI hinzuzufügen.|
|Für FQDN-Tags muss ein Protokoll/Port festgelegt werden|Für Anwendungsregeln mit FQDN-Tags ist eine „Port: Protokoll“-Definition erforderlich.|Sie können **https** als port:-Protokollwert verwenden. Wir arbeiten daran, dieses Feld optional zu machen, wenn FQDN-Tags verwendet werden.|
|Das Verlagern einer Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement wird nicht unterstützt|Das Verlagern einer Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement wird nicht unterstützt.|Die Unterstützung dieser Funktionalität ist Teil unserer Roadmap. Um eine Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement zu verschieben, müssen Sie die aktuelle Instanz löschen und in der neuen Ressourcengruppe bzw. im Abonnement neu erstellen.|
|Portbereich in Netzwerk- und Anwendungsregeln|Ports sind auf eine Anzahl von 64.000 begrenzt, da der hohe Portbereich für Verwaltung und Integritätstests reserviert sind. |Wir arbeiten daran, diese Beschränkung zu lockern.|
|Threat Intelligence-Warnungen sind möglicherweise maskiert.|Netzwerkregeln mit Ziel 80/443 für ausgehende Filterung maskieren Threat Intelligence-Warnungen, wenn diese für den Modus „Alert only“ (Nur Warnen) konfiguriert sind.|Erstellen Sie die ausgehende Filterung für 80/443 mithilfe von Anwendungsregeln. Oder ändern Sie den Threat Intelligence-Modus zu **Alert and Deny** (Warnen und Verweigern).|
|Azure Firewall verwendet Azure DNS nur für die Namensauflösung.|Azure Firewall löst FQDNs nur mit Azure DNS auf. Ein benutzerdefinierter DNS-Server wird nicht unterstützt. Dies hat keine Auswirkungen auf die DNS-Auflösung in anderen Subnetzen.|Wir arbeiten daran, diese Beschränkung zu lockern.|
|Azure Firewall-SNAT/DNAT funktioniert für private IP-Ziele nicht|Die Azure Firewall-SNAT/DNAT-Unterstützung ist auf eingehenden/ausgehenden Internetdatenverkehr beschränkt. SNAT/DNAT funktioniert derzeit für private IP-Ziele nicht. Beispiel: Spoke zu Spoke.|Dies ist Teil der Roadmap für ein künftiges Update.
## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](tutorial-firewall-deploy-portal.md)
- [Bereitstellen von Azure Firewall mit einer Vorlage](deploy-template.md)
- [Erstellen einer Azure Firewall-Testumgebung](scripts/sample-create-firewall-test.md)