---
title: Häufig gestellte Fragen zu Azure Application Gateway
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: 25779cd62f70df41a7734d2f8097504923dcb3a3
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430872"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Häufig gestellte Fragen zu Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Im Folgenden finden Sie häufig gestellte Fragen zu Azure Application Gateway.

## <a name="general"></a>Allgemein

### <a name="what-is-application-gateway"></a>Was ist Application Gateway?

Azure Application Gateway stellt einen Application Deliver Controller (ADC) als Dienst bereit. Er bietet verschiedene Layer-7-Lastenausgleichsfunktionen für Ihre Anwendungen. Dieser Dienst ist hoch verfügbar und skalierbar und wird vollständig von Azure verwaltet.

### <a name="what-features-does-application-gateway-support"></a>Welche Funktionen werden von Application Gateway unterstützt?

Application Gateway unterstützt automatische Skalierung, SSL-Abladung und End-to-End-SSL, Web Application Firewall (WAF), cookiebasierte Sitzungsaffinität, Routing auf URL-Pfadbasis, Hosting für mehrere Standorte und vieles mehr. Eine vollständige Liste der unterstützten Funktionen finden Sie unter [Einführung in Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Was ist der Unterschied zwischen Application Gateway und Azure Load Balancer?

Application Gateway ist ein Layer-7-Lastenausgleich und somit nur für Webdatenverkehr (HTTP/HTTPS/WebSocket und HTTP/2) geeignet. Er unterstützt Funktionen wie SSL-Beendigung, cookiebasierte Sitzungsaffinität und Roundrobin für den Lastenausgleich von Datenverkehr. Load Balancer nimmt einen Lastausgleich des Datenverkehrs auf Schicht 4 (TCP/UDP) vor.

### <a name="what-protocols-does-application-gateway-support"></a>Welche Protokolle werden von Application Gateway unterstützt?

Application Gateway unterstützt HTTP, HTTPS, HTTP/2 und WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Wie unterstützt Application Gateway HTTP/2?

Siehe [Application Gateway – Konfigurationsübersicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Welche Ressourcen werden als Teil eines Back-End-Pools unterstützt?

Siehe [Application Gateway-Komponenten](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool)

### <a name="in-what-regions-is-application-gateway-available"></a>In welchen Regionen ist Application Gateway verfügbar?

Application Gateway ist in allen Regionen des globalen Azure verfügbar. Der Dienst ist auch in [Azure China 21Vianet](https://www.azure.cn/) und [Azure Government](https://azure.microsoft.com/overview/clouds/government/) verfügbar.

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Ist diese Bereitstellung für mein Abonnement dediziert, oder wird sie zur gemeinsamen Nutzung für Kunden freigegeben?

Application Gateway ist eine dedizierte Bereitstellung in Ihrem virtuellen Netzwerk.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Unterstützt Application Gateway HTTP-zu-HTTPS-Umleitung?

Die Umleitung wird unterstützt. Weitere Informationen finden Sie unter [Übersicht über die Umleitung in Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In welcher Reihenfolge werden Listener verarbeitet?

Informationen hierzu finden Sie unter [Application Gateway – Konfigurationsübersicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Wo finde ich IP und DNS von Application Gateway?

Wenn Sie eine öffentliche IP-Adresse als Endpunkt verwenden, finden Sie die Informationen zu IP und DNS in der Ressource der öffentlichen IP-Adresse. Sie können sie auch im Portal auf der Übersichtsseite für das Anwendungsgateway abrufen. Bei Verwendung von internen IP-Adressen finden Sie die Informationen auf der Übersichtsseite.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Welche Einstellungen werden für das Keep-Alive-Timeout und das TCP-Leerlauftimeout verwendet?

 In der Application Gateway v1-SKU beträgt das Keep-Alive-Timeout 120 Sekunden. Das Keep-Alive-Timeout für die v2-SKU beträgt 75 Sekunden. Das TCP-Leerlauftimeout beträgt standardmäßig vier Minuten für die virtuelle IP (Virtual IP, VIP) des Front-Ends von Application Gateway.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Ändert sich die IP-Adresse oder der DNS-Name während der Lebensdauer des Anwendungsgateways?

Die VIP kann sich ändern, wenn Sie das Anwendungsgateway beenden und starten. Der zugeordnete DNS-Name des Anwendungsgateways ändert sich während der Lebensdauer des Gateways nicht. Daher wird empfohlen, einen CNAME-Alias zu verwenden und damit auf die DNS-Adresse des Anwendungsgateways zu verweisen.

### <a name="does-application-gateway-support-static-ip"></a>Unterstützt Application Gateway statische IP-Adressen?

Ja, die Application Gateway v2-SKU unterstützt statische öffentliche IP-Adressen. Die v1-SKU unterstützt statische interne IP-Adressen.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Unterstützt Application Gateway mehrere öffentliche IP-Adressen auf dem Gateway?

Ein Anwendungsgateway unterstützt nur eine öffentliche IP-Adresse.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Wie groß soll ich mein Subnetz für Application Gateway auslegen?

Siehe [Application Gateway – Konfigurationsübersicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kann ich mehrere Application Gateway-Ressourcen in einem einzelnen Subnetz bereitstellen?

Ja. Sie können nicht nur mehrere Instanzen einer bestimmten Application Gateway-Bereitstellung besitzen, sondern auch eine weitere eindeutige Application Gateway-Ressource zu einem vorhandenen Subnetz hinzufügen, das eine andere Application Gateway-Ressource enthält.

Ein einzelnes Subnetz kann entweder eine Application Gateway-Instanz vom Typ „Standard“ oder eine Application Gateway-Instanz vom Typ „Standard_v2“ unterstützen, aber nicht beide gleichzeitig.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Werden X-Forwarded-For-Header von Application Gateway unterstützt?

Ja. Entsprechende Informationen finden Sie unter [Funktionsweise von Application Gateway](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Wie lange dauert das Bereitstellen eines Anwendungsgateways? Funktioniert das Anwendungsgateway während einer Aktualisierung?

Die Ausführung neuer Bereitstellungen der Application Gateway v1-SKU kann bis zu 20 Minuten dauern. Beim Vornehmen von Änderungen der Instanzgröße oder -anzahl wird der Betrieb nicht unterbrochen, und das Gateway bleibt währenddessen aktiv.

Die Bereitstellung von Instanzen, die die v2-SKU nutzen, kann bis zu sechs Minuten dauern.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kann ich Exchange Server als Back-End mit Application Gateway verwenden?

Nein. Application Gateway unterstützt keine E-Mail-Protokolle wie SMTP, IMAP oder POP3. 

## <a name="performance"></a>Leistung

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Wie unterstützt Application Gateway Hochverfügbarkeit und Skalierbarkeit?

Die Application Gateway v1-SKU unterstützt Szenarien mit Hochverfügbarkeit, wenn zwei oder mehr Instanzen bereitgestellt wurden. Azure verteilt diese Instanzen auf Update- und Fehlerdomänen, um sicherzustellen, dass nicht alle Instanzen gleichzeitig ausfallen. Die v1-SKU unterstützt Skalierbarkeit durch Hinzufügen mehrerer Instanzen des gleichen Gateways, um die Last zu teilen.

Die v2-SKU stellt automatisch sicher, dass neue Instanzen über Fehlerdomänen und Updatedomänen hinweg verteilt werden. Haben Sie Zonenredundanz gewählt, werden die neuesten Instanzen darüber hinaus über Verfügbarkeitszonen hinweg verteilt, um Resilienz gegen Zonenausfälle zu erreichen.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Wie erziele ich mit Application Gateway ein rechenzentrumsübergreifendes DR-Szenario?

Verteilen Sie Datenverkehr mithilfe von Traffic Manager auf mehrere Anwendungsgateways in verschiedenen Rechenzentren.

### <a name="does-application-gateway-support-autoscaling"></a>Unterstützt Application Gateway automatische Skalierung?

Ja, die Application Gateway v2-SKU unterstützt automatische Skalierung. Weitere Informationen finden Sie unter [Automatische Skalierung und zonenredundantes Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Werden durch das zentrale Hoch- oder Herunterskalieren Ausfallzeiten verursacht?

Nein. Instanzen sind auf Upgrade- und Fehlerdomänen verteilt.

### <a name="does-application-gateway-support-connection-draining"></a>Unterstützt Application Gateway den Verbindungsausgleich?

Ja. Sie können den Verbindungsausgleich einrichten, um die Mitglieder in einem Back-End-Pool ohne Unterbrechung zu ändern. Mit dieser Einrichtung können vorhandene Verbindungen weiterhin an ihr vorheriges Ziel gesendet werden, bis entweder diese Verbindung geschlossen wird oder ein konfigurierbares Zeitlimit abläuft. Der Verbindungsausgleich wartet nur auf die Beendigung der aktuellen aktiven Verbindungen. Application Gateway kennt den Status der Anwendungssitzung nicht.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kann ich Instanzgröße ohne Unterbrechung von mittel zu groß ändern?

Ja. Azure verteilt Instanzen auf Update- und Fehlerdomänen, um sicherzustellen, dass nicht alle Instanzen gleichzeitig ausfallen. Application Gateway unterstützt Skalierung durch Hinzufügen mehrerer Instanzen desselben Gateways, um die Last zu teilen.

## <a name="configuration"></a>Konfiguration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Wird Application Gateway immer in einem virtuellen Netzwerk bereitgestellt?

Ja. Application Gateway wird immer in einem Subnetz des virtuellen Netzwerks bereitgestellt. Dieses Subnetz kann nur Anwendungsgateways enthalten. Weitere Informationen finden Sie unter [Application Gateway – Konfigurationsübersicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kann Application Gateway mit Instanzen außerhalb des eigenen virtuellen Netzwerks oder des eigenen Abonnements kommunizieren?

Application Gateway kann mit Instanzen außerhalb des eigenen virtuellen Netzwerks kommunizieren, sofern eine IP-Verbindung besteht. Application Gateway kann auch mit Instanzen außerhalb des eigenen Abonnements kommunizieren. Wenn Sie die Verwendung interner IP-Adressen als Back-End-Pool-Mitglieder planen, ist ein [Peering virtueller Netzwerke](../virtual-network/virtual-network-peering-overview.md) oder [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) erforderlich.

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kann ich im Application Gateway-Subnetz noch etwas anderes bereitstellen?

Nein. Aber Sie können weitere Application Gateway-Instanzen im Subnetz bereitstellen.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Werden Netzwerksicherheitsgruppen im Application Gateway-Subnetz unterstützt?

Siehe [Application Gateway – Konfigurationsübersicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Unterstützt das Application Gateway-Subnetz benutzerdefinierte Routen?

Siehe [Application Gateway – Konfigurationsübersicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Was sind die Grenzwerte für Application Gateway? Kann ich diese Grenzwerte erhöhen?

Siehe [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md#application-gateway-limits)

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Kann ich Application Gateway gleichzeitig für externen und internen Datenverkehr verwenden?

Ja. Application Gateway unterstützt pro Instanz eine interne und eine externe IP-Adresse.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Unterstützt Application Gateway das Peering virtueller Netzwerke?

Ja. Das Peering virtueller Netzwerke ermöglicht den Lastenausgleich von Datenverkehr in anderen virtuellen Netzwerken.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Kann ich mit lokalen Servern kommunizieren, wenn sie über ExpressRoute- oder VPN-Tunnel verbunden sind?

Ja, sofern Datenverkehr zugelassen wird.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Kann ein Back-End-Pool für das Bereitstellen mehrerer Anwendungen an unterschiedlichen Ports verwendet werden?

Microservice-Architektur wird unterstützt. Für das Testen an unterschiedlichen Ports müssen Sie mehrere HTTP-Einstellungen konfigurieren.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Unterstützen benutzerdefinierte Tests Platzhalter oder reguläre Ausdrücke in Antwortdaten?

Nein. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Wie werden Routingregeln in Application Gateway verarbeitet?

Siehe [Application Gateway – Konfigurationsübersicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Was ist im Feld „Host“ für benutzerdefinierte Überprüfungen angegeben?

Wenn Sie mehrere Standorte für Application Gateway konfiguriert haben, wird im Feld „Host“ der Name angegeben, an den der Test gesendet werden soll. Verwenden Sie andernfalls „127.0.0.1“. Dieser Wert entspricht nicht dem Hostnamen des virtuellen Computers. Sein Format ist \<Protokoll\>://\<Host\>:\<Port\>\<Pfad\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kann ich nur einigen wenigen Quell-IP-Adressen Zugriff auf Application Gateway gewähren?

Ja. Weitere Informationen finden Sie unter [Application Gateway – Konfigurationsübersicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kann ich den gleichen Port sowohl für öffentliche als auch für private Listener verwenden?

Nein.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Gibt es Richtlinien für die Migration von der v1-SKU zur v2-SKU?

Ja. Details dazu finden Sie unter [Migrieren von Azure Application Gateway und Web Application Firewall von v1 zu v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Konfiguration: SSL

### <a name="what-certificates-does-application-gateway-support"></a>Welche Zertifikate werden von Application Gateway unterstützt?

Application Gateway unterstützt selbstsignierte Zertifikate, Zertifizierungsstellenzertifikate, Zertifikate für die erweiterte Validierung und Platzhalterzertifikate.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Welche Verschlüsselungssammlungen werden von Application Gateway unterstützt?

Application Gateway unterstützt die folgenden Verschlüsselungssammlungen: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Informationen zum Anpassen von SSL-Optionen finden Sie unter [Konfigurieren von SSL-Richtlinienversionen und Verschlüsselungssammlungen für Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Unterstützt Application Gateway eine erneute Verschlüsselung des Datenverkehrs an das Back-End?

Ja. Application Gateway unterstützt SSL-Abladung sowie End-to-End-SSL, das den Datenverkehr an das Back-End erneut verschlüsselt.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kann ich die SSL-Richtlinie für die Steuerung von SSL-Protokollversionen konfigurieren?

Ja. Sie können Application Gateway zum Abweisen von TLS1.0, TLS1.1 und TLS1.2 konfigurieren. Standardmäßig sind SSL 2.0 und 3.0 bereits deaktiviert und nicht konfigurierbar.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kann ich Verschlüsselungssammlungen und die Richtlinienreihenfolge konfigurieren?

Ja. In Application Gateway können Sie [Verschlüsselungssammlungen konfigurieren](application-gateway-ssl-policy-overview.md). Aktivieren Sie zum Definieren einer benutzerdefinierten Richtlinie mindestens eine der folgenden Verschlüsselungssammlungen: 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway verwendet SHA256 für die Back-End-Verwaltung.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Wie viele Zertifikate werden von Application Gateway unterstützt?

Application Gateway unterstützt bis zu 100 SSL-Zertifikate.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Wie viele Authentifizierungszertifikate für die erneute Back-End-Verschlüsselung werden von Application Gateway unterstützt?

Application Gateway unterstützt bis zu 10 Authentifizierungszertifikate. Der Standardwert ist 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Lässt sich Application Gateway nativ in Azure Key Vault integrieren?

Ja, die Application Gateway v2-SKU unterstützt Key Vault. Weitere Informationen finden Sie unter [SSL-Terminierung mit Key Vault-Zertifikaten](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Wie konfiguriere ich HTTPS-Listener für Websites vom Typ „.com“ und „.net“? 

Für das Routing auf der Grundlage mehrerer Domänen (hostbasiertes Routing) können Sie Listener für mehrere Standorte erstellen, Listener einrichten, die als Protokoll HTTPS verwenden, und die Listener mit den Routingregeln verknüpfen. Weitere Informationen finden Sie unter [Anwendungsgateways – Hosten mehrerer Websites](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Konfiguration: Web Application Firewall (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Bietet die WAF-SKU alle Funktionen, die in der Standard-SKU verfügbar sind?

Ja. WAF unterstützt alle Funktionen in der Standard-SKU.

### <a name="which-crs-versions-does-application-gateway-support"></a>Welche CRS-Versionen werden von Application Gateway unterstützt?

Application Gateway unterstützt CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) und CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Wie überwache ich WAF?

Überwachen Sie WAF über die Diagnoseprotokollierung. Weitere Informationen finden Sie unter [Back-End-Integrität, Diagnoseprotokollierung und Metriken für Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Wird Datenverkehr durch den Erkennungsmodus blockiert?

Nein. Der Erkennungsmodus protokolliert nur Datenverkehr, der eine WAF-Regel auslöst.

### <a name="can-i-customize-waf-rules"></a>Kann ich WAF-Regeln anpassen?

Ja. Weitere Informationen finden Sie unter [Anpassen von Web Application Firewall-Regeln mit dem Azure-Portal](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Welche Regeln sind derzeit für WAF verfügbar?

WAF unterstützt derzeit CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) und [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Diese Regeln bieten grundlegende Sicherheit für die meisten der zehn wichtigsten, von Open Web Application Security Project (OWASP) identifizierten Sicherheitsrisiken. 

* Schutz vor Einschleusung von SQL-Befehlen
* Schutz vor websiteübergreifendem Skripting
* Schutz vor allgemeinen Webangriffen wie Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion.
* Schutz vor Verletzungen des HTTP-Protokolls
* Schutz vor HTTP-Protokollanomalien, z.B. fehlende user-agent- und accept-Header des Hosts
* Verhindern von Bots, Crawlern und Scannern
* Erkennung allgemeiner Fehler bei der Anwendungskonfiguration (in Bezug auf Apache, IIS usw.)

Weitere Informationen finden Sie im Artikel zu den [zehn wichtigsten, von OWASP identifizierten Sicherheitsrisiken](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Unterstützt WAF DDoS-Schutz?

Ja. Sie können DDoS-Schutz im virtuellen Netzwerk aktivieren, in dem das Anwendungsgateway bereitgestellt wird. Mit dieser Einstellung wird sichergestellt, dass der Azure DDoS Protection-Dienst auch die VIP-Adresse des Anwendungsgateways schützt.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Gibt es Richtlinien für die Migration von der v1-SKU zur v2-SKU?

Ja. Details dazu finden Sie unter [Migrieren von Azure Application Gateway und Web Application Firewall von v1 zu v2](migrate-v1-v2.md).

## <a name="diagnostics-and-logging"></a>Diagnose und Protokollierung

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Welche Protokolltypen bietet Application Gateway?

In Application Gateway stehen drei Protokolle zur Verfügung: 

* **ApplicationGatewayAccessLog:** Das Zugriffsprotokoll enthält jede an das Application Gateway-Front-End gesendete Anforderung. Die Daten enthalten die IP des Aufrufers, die angeforderte URL, die Antwortlatenz, den Rückgabecode sowie die ein- und ausgehenden Bytes. Das Zugriffsprotokoll wird alle 300 Sekunden erstellt. Es enthält einen Datensatz pro Anwendungsgateway.
* **ApplicationGatewayPerformanceLog:** Das Leistungsprotokoll erfasst Leistungsinformationen für jedes Anwendungsgateway. Zu den Informationen zählen der Durchsatz in Bytes, die Gesamtanzahl übermittelter Anforderungen, Fehler bei der Anzahl der Anforderungen sowie die Anzahl fehlerfreier und fehlerhafter Back-End-Instanzen.
* **ApplicationGatewayFirewallLog:** Bei mit WAF konfigurierten Anwendungsgateways enthält das Firewallprotokoll Anforderungen, die entweder über den Erkennungs- oder über den Schutzmodus protokolliert werden.

Weitere Informationen finden Sie unter [Back-End-Integrität, Diagnoseprotokolle und Metriken für Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Wie erkenne ich, dass die Mitglieder meines Back-End-Pools fehlerfrei sind?

Überprüfen Sie die Integrität mithilfe des PowerShell-Cmdlets `Get-AzApplicationGatewayBackendHealth` oder über das Portal. Weitere Informationen finden Sie unter [Application Gateway-Diagnose](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Welche Aufbewahrungsrichtlinie gilt für die Diagnoseprotokolle?

Diagnoseprotokolle werden an das Speicherkonto des Kunden gesendet. Kunden können die Aufbewahrungsrichtlinie je nach Präferenz festlegen. Diagnoseprotokolle können auch an einen Event Hub oder an Azure Monitor-Protokolle gesendet werden. Weitere Informationen finden Sie unter [Application Gateway-Diagnose](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Wie erhalte ich die Überwachungsprotokolle für Application Gateway?

Wählen Sie im Portal auf dem Menüblatt eines Anwendungsgateways die Option **Aktivitätsprotokoll** aus, um das Überwachungsprotokoll aufzurufen. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kann ich mit Application Gateway Warnungen einrichten?

Ja. In Application Gateway werden Warnungen für Metriken konfiguriert. Weitere Informationen finden Sie unter [Back-End-Integrität, Diagnoseprotokolle und Metriken für Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) und [Überblick über Warnungen in Microsoft Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Wie lassen sich Datenverkehrsstatistiken für Application Gateway analysieren?

Sie haben verschiedene Möglichkeiten zum Anzeigen und Analysieren von Zugriffsprotokollen. Sie können Azure Monitor-Protokolle, Excel, Power BI usw. verwenden.

Sie können auch eine Resource Manager-Vorlage verwenden, die die beliebte [GoAccess](https://goaccess.io/)-Protokollanalyse für Application Gateway-Zugriffsprotokolle installiert und ausführt. GoAccess stellt wertvolle HTTP-Datenverkehrsstatistiken bereit, z. B. eindeutige Besucher, angeforderte Dateien, Hosts, Betriebssysteme, Browser und HTTP-Statuscodes. Weitere Informationen finden Sie auf GitHub in der [Infodatei im Resource Manager-Vorlagenordner](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Was kann dazu führen, dass für die Back-End-Integrität ein unbekannter Status angezeigt wird?

In der Regel wird ein unbekannter Status angezeigt, wenn der Zugriff auf das Back-End durch eine Netzwerksicherheitsgruppe (NSG), einen benutzerdefinierten DNS oder durch benutzerdefiniertes Routing für das Application Gateway-Subnetz blockiert wird. Weitere Informationen finden Sie unter [Back-End-Integrität, Diagnoseprotokolle und Metriken für Application Gateway](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Application Gateway finden Sie unter [Was ist Azure Application Gateway?](overview.md).
