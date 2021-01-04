---
title: Häufig gestellte Fragen zu Azure Application Gateway
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: d46c1d8efb5234d47c80ca4256c2f56d56ccf805
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862172"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Häufig gestellte Fragen zu Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Im Folgenden finden Sie häufig gestellte Fragen zu Azure Application Gateway.

## <a name="general"></a>Allgemein

### <a name="what-is-application-gateway"></a>Was ist Application Gateway?

Azure Application Gateway stellt einen Application Deliver Controller (ADC) als Dienst bereit. Er bietet verschiedene Layer-7-Lastenausgleichsfunktionen für Ihre Anwendungen. Dieser Dienst ist hoch verfügbar und skalierbar und wird vollständig von Azure verwaltet.

### <a name="what-features-does-application-gateway-support"></a>Welche Funktionen werden von Application Gateway unterstützt?

Application Gateway unterstützt automatische Skalierung, TLS-Abladung und End-to-End-TLS, Web Application Firewall (WAF), cookiebasierte Sitzungsaffinität, Routing auf URL-Pfadbasis, Hosting für mehrere Standorte und vieles mehr. Eine vollständige Liste der unterstützten Funktionen finden Sie unter [Einführung in Application Gateway](./overview.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Was ist der Unterschied zwischen Application Gateway und Azure Load Balancer?

Application Gateway ist ein Layer-7-Lastenausgleich und somit nur für Webdatenverkehr (HTTP/HTTPS/WebSocket und HTTP/2) geeignet. Er unterstützt Funktionen wie TLS-Terminierung, cookiebasierte Sitzungsaffinität und Roundrobin für den Lastenausgleich von Datenverkehr. Load Balancer nimmt einen Lastausgleich des Datenverkehrs auf Schicht 4 (TCP/UDP) vor.

### <a name="what-protocols-does-application-gateway-support"></a>Welche Protokolle werden von Application Gateway unterstützt?

Application Gateway unterstützt HTTP, HTTPS, HTTP/2 und WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Wie unterstützt Application Gateway HTTP/2?

Siehe [Application Gateway – Konfigurationsübersicht](./configuration-listeners.md#http2-support)

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Welche Ressourcen werden als Teil eines Back-End-Pools unterstützt?

Siehe [Application Gateway-Komponenten](./application-gateway-components.md#backend-pools)

### <a name="in-what-regions-is-application-gateway-available"></a>In welchen Regionen ist Application Gateway verfügbar?

Application Gateway v1 (Standard und WAF) ist in allen Regionen der globalen Azure-Umgebung verfügbar. Der Dienst ist auch in [Azure China 21Vianet](https://www.azure.cn/) und [Azure Government](https://azure.microsoft.com/overview/clouds/government/) verfügbar.

Informationen zur Verfügbarkeit von Application Gateway v2 (Standard_v2 und WAF_v2) finden Sie unter [Unterstützte Regionen](./application-gateway-autoscaling-zone-redundant.md#supported-regions).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Ist diese Bereitstellung für mein Abonnement dediziert, oder wird sie zur gemeinsamen Nutzung für Kunden freigegeben?

Application Gateway ist eine dedizierte Bereitstellung in Ihrem virtuellen Netzwerk.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Unterstützt Application Gateway HTTP-zu-HTTPS-Umleitung?

Die Umleitung wird unterstützt. Weitere Informationen finden Sie unter [Übersicht über die Umleitung in Application Gateway](./redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In welcher Reihenfolge werden Listener verarbeitet?

Informationen hierzu finden Sie unter [Application Gateway – Konfigurationsübersicht](./configuration-listeners.md#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Wo finde ich IP und DNS von Application Gateway?

Wenn Sie eine öffentliche IP-Adresse als Endpunkt verwenden, finden Sie die Informationen zu IP und DNS in der Ressource der öffentlichen IP-Adresse. Sie können sie auch im Portal auf der Übersichtsseite für das Anwendungsgateway abrufen. Bei Verwendung von internen IP-Adressen finden Sie die Informationen auf der Übersichtsseite.

Öffnen Sie für die v2-SKU die öffentliche IP-Adresse, und klicken Sie auf **Konfiguration**. Das Feld **DNS-Namenbezeichnung (optional)** steht zum Konfigurieren des DNS-Namens zur Verfügung.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Welche Einstellungen werden für das Keep-Alive-Timeout und das TCP-Leerlauftimeout verwendet?

*Keep-Alive-Timeout* steuert, wie lange das Application Gateway wartet, bis ein Client eine andere HTTP-Anforderung über eine persistente Verbindung sendet, bevor diese wiederverwendet oder geschlossen wird. *TCP-Leerlauftimeout* steuert, wie lange eine TCP-Verbindung bei fehlender Aktivität offengehalten wird. 

Der *Keep-Alive-Timeout* in der Application Gateway v1-SKU beträgt 120 Sekunden und in der v2-SKU 75 Sekunden. Der *TCP-Leerlauftimeout* beträgt standardmäßig 4 Minuten für die virtuelle IP (VIP) des Front-Ends von Application Gateway sowohl für die v1- als auch die v2-SKU. Sie können den Wert für das TCP-Leerlauftimeout für v1- und v2-Anwendungsgateways zwischen 4 Minuten und 30 Minuten festlegen. Für v1- und v2-Anwendungsgateways müssen Sie zur öffentlichen IP-Adresse des Anwendungsgateways navigieren und das TCP-Leerlauftimeout auf dem Blatt „Konfiguration“ der öffentlichen IP-Adresse im Portal ändern. Sie können den Wert für das TCP-Leerlauftimeout der öffentlichen IP-Adresse über PowerShell festlegen, indem Sie folgende Befehle ausführen: 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Ändert sich die IP-Adresse oder der DNS-Name während der Lebensdauer des Anwendungsgateways?

In der Application Gateway V1-SKU kann sich die VIP ändern, wenn Sie das Anwendungsgateway beenden und dann erneut starten. Der zugeordnete DNS-Name des Anwendungsgateways ändert sich während der Lebensdauer des Gateways nicht. Daher wird empfohlen, einen CNAME-Alias zu verwenden und damit auf die DNS-Adresse des Anwendungsgateways zu verweisen. In der Application Gateway V2-SKU können Sie die IP-Adresse als statisch festlegen, sodass sich die IP-Adresse und der DNS-Name während der Lebensdauer des Anwendungsgateways nicht ändern. 

### <a name="does-application-gateway-support-static-ip"></a>Unterstützt Application Gateway statische IP-Adressen?

Ja, die Application Gateway v2-SKU unterstützt statische öffentliche IP-Adressen. Die v1-SKU unterstützt statische interne IP-Adressen.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Unterstützt Application Gateway mehrere öffentliche IP-Adressen auf dem Gateway?

Ein Anwendungsgateway unterstützt nur eine öffentliche IP-Adresse.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Wie groß soll ich mein Subnetz für Application Gateway auslegen?

Siehe [Application Gateway – Konfigurationsübersicht](./configuration-infrastructure.md#size-of-the-subnet)

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kann ich mehrere Application Gateway-Ressourcen in einem einzelnen Subnetz bereitstellen?

Ja. Sie können nicht nur mehrere Instanzen einer bestimmten Application Gateway-Bereitstellung besitzen, sondern auch eine weitere eindeutige Application Gateway-Ressource zu einem vorhandenen Subnetz hinzufügen, das eine andere Application Gateway-Ressource enthält.

Ein einzelnes Subnetz kann nicht gleichzeitig v2- und v1-SKUs für Application Gateway unterstützen.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Unterstützt Application Gateway v2 benutzerdefinierte Routen?

Ja, aber nur für spezifische Szenarios. Weitere Informationen finden Sie unter [Konfiguration der Application Gateway-Infrastruktur](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Werden X-Forwarded-For-Header von Application Gateway unterstützt?

Ja. Entsprechende Informationen finden Sie unter [Funktionsweise von Application Gateway](./how-application-gateway-works.md#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Wie lange dauert das Bereitstellen eines Anwendungsgateways? Funktioniert das Anwendungsgateway während einer Aktualisierung?

Die Ausführung neuer Bereitstellungen der Application Gateway v1-SKU kann bis zu 20 Minuten dauern. Beim Vornehmen von Änderungen der Instanzgröße oder -anzahl wird der Betrieb nicht unterbrochen, und das Gateway bleibt währenddessen aktiv.

Die Bereitstellung von Instanzen mit der v2-SKU dauert in der Regel etwa sechs Minuten. Bei manchen Bereitstellungsarten kann sie jedoch auch mehr Zeit beanspruchen. Bereitstellungen, die sich über mehrere Verfügbarkeitszonen erstrecken und viele Instanzen umfassen, können beispielsweise länger als sechs Minuten dauern. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kann ich Exchange Server als Back-End mit Application Gateway verwenden?

Nein. Application Gateway unterstützt keine E-Mail-Protokolle wie SMTP, IMAP oder POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Gibt es Richtlinien für die Migration von der v1-SKU zur v2-SKU?

Ja. Details dazu finden Sie unter [Migrieren von Azure Application Gateway und Web Application Firewall von v1 zu v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Wird die Application Gateway v1-SKU weiterhin unterstützt?

Ja. Die Application Gateway v1-SKU wird weiterhin unterstützt. Es wird jedoch dringend empfohlen, zu v2 zu wechseln, um die Funktionsupdates in dieser SKU zu nutzen. Weitere Informationen finden Sie unter [Automatische Skalierung und zonenredundantes Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Unterstützt Application Gateway V2 Proxyanforderungen mit NTLM-Authentifizierung?

Nein. Application Gateway V2 unterstützt keine Proxyanforderungen mit NTLM-Authentifizierung.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Unterstützt das Affinitätscookie von Application Gateway das SameSite-Attribut?
Das [v80-Update](https://chromiumdash.appspot.com/schedule) des [Chromium-Browsers](https://www.chromium.org/Home) enthielt erstmals ein Mandat, bei dem HTTP-Cookies ohne SameSite-Attribut als „SameSite=Lax“ behandelt werden. Das bedeutet, dass das Affinitätscookie von Application Gateway nicht vom Browser in einem Drittanbieterkontext gesendet wird. 

Zur Unterstützung dieses Szenarios fügt Application Gateway zusätzlich zum vorhandenen Cookie *ApplicationGatewayAffinity* ein weiteres Cookie namens *ApplicationGatewayAffinityCORS* ein.  Diese Cookies sind ähnlich, aber dem Cookie *ApplicationGatewayAffinityCORS* wurden zwei weitere Attribute hinzugefügt: *SameSite=None; Secure*. Diese Attribute behalten persistente Sitzungen selbst bei quellenübergreifenden Anforderungen bei. Weitere Informationen finden Sie im [Abschnitt zur cookiebasierten Affinität](configuration-http-settings.md#cookie-based-affinity).

## <a name="performance"></a>Leistung

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Wie unterstützt Application Gateway Hochverfügbarkeit und Skalierbarkeit?

Die Application Gateway v1-SKU unterstützt Szenarien mit Hochverfügbarkeit, wenn zwei oder mehr Instanzen bereitgestellt wurden. Azure verteilt diese Instanzen auf Update- und Fehlerdomänen, um sicherzustellen, dass nicht alle Instanzen gleichzeitig ausfallen. Die v1-SKU unterstützt Skalierbarkeit durch Hinzufügen mehrerer Instanzen des gleichen Gateways, um die Last zu teilen.

Die v2-SKU stellt automatisch sicher, dass neue Instanzen über Fehlerdomänen und Updatedomänen hinweg verteilt werden. Haben Sie Zonenredundanz gewählt, werden die neuesten Instanzen darüber hinaus über Verfügbarkeitszonen hinweg verteilt, um Resilienz gegen Zonenausfälle zu erreichen.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Wie erziele ich mit Application Gateway ein rechenzentrumsübergreifendes DR-Szenario?

Verteilen Sie Datenverkehr mithilfe von Traffic Manager auf mehrere Anwendungsgateways in verschiedenen Rechenzentren.

### <a name="does-application-gateway-support-autoscaling"></a>Unterstützt Application Gateway automatische Skalierung?

Ja, die Application Gateway v2-SKU unterstützt automatische Skalierung. Weitere Informationen finden Sie unter [Automatische Skalierung und zonenredundantes Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Werden durch das manuelle oder automatische Hoch- oder Herunterskalieren Ausfallzeiten verursacht?

Nein. Instanzen sind auf Upgrade- und Fehlerdomänen verteilt.

### <a name="does-application-gateway-support-connection-draining"></a>Unterstützt Application Gateway den Verbindungsausgleich?

Ja. Sie können den Verbindungsausgleich einrichten, um die Mitglieder in einem Back-End-Pool ohne Unterbrechung zu ändern. Weitere Informationen finden Sie unter [Application Gateway im Abschnitt „Verbindungsausgleich“](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kann ich Instanzgröße ohne Unterbrechung von mittel zu groß ändern?

Ja.

## <a name="configuration"></a>Konfiguration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Wird Application Gateway immer in einem virtuellen Netzwerk bereitgestellt?

Ja. Application Gateway wird immer in einem Subnetz des virtuellen Netzwerks bereitgestellt. Dieses Subnetz kann nur Anwendungsgateways enthalten. Weitere Informationen finden Sie unter [Application Gateway – Konfigurationsübersicht](./configuration-infrastructure.md#virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kann Application Gateway mit Instanzen außerhalb des eigenen virtuellen Netzwerks oder des eigenen Abonnements kommunizieren?

Application Gateway kann mit Instanzen außerhalb des eigenen virtuellen Netzwerks kommunizieren, sofern eine IP-Verbindung besteht. Application Gateway kann auch mit Instanzen außerhalb des eigenen Abonnements kommunizieren. Wenn Sie die Verwendung interner IP-Adressen als Back-End-Pool-Mitglieder planen, ist ein [Peering virtueller Netzwerke](../virtual-network/virtual-network-peering-overview.md) oder [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) erforderlich.

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kann ich im Application Gateway-Subnetz noch etwas anderes bereitstellen?

Nein. Aber Sie können weitere Application Gateway-Instanzen im Subnetz bereitstellen.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Werden Netzwerksicherheitsgruppen im Application Gateway-Subnetz unterstützt?

Siehe [Application Gateway – Konfigurationsübersicht](./configuration-infrastructure.md#network-security-groups)

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Unterstützt das Application Gateway-Subnetz benutzerdefinierte Routen?

Siehe [Application Gateway – Konfigurationsübersicht](./configuration-infrastructure.md#supported-user-defined-routes)

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>Werden Dienstendpunktrichtlinien im Application Gateway-Subnetz unterstützt?

Nein. [Dienstendpunktrichtlinien](../virtual-network/virtual-network-service-endpoint-policies-overview.md) für Speicherkonten werden im Application Gateway-Subnetz nicht unterstützt, und durch die Konfiguration solcher Richtlinien wird der Azure-Infrastrukturdatenverkehr blockiert.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Was sind die Grenzwerte für Application Gateway? Kann ich diese Grenzwerte erhöhen?

Siehe [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)

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

Siehe [Application Gateway – Konfigurationsübersicht](./configuration-request-routing-rules.md#order-of-processing-rules)

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Was ist im Feld „Host“ für benutzerdefinierte Überprüfungen angegeben?

Wenn Sie mehrere Standorte für Application Gateway konfiguriert haben, wird im Feld „Host“ der Name angegeben, an den der Test gesendet werden soll. Verwenden Sie andernfalls „127.0.0.1“. Dieser Wert entspricht nicht dem Hostnamen des virtuellen Computers. Das Format lautet:\<protocol\>://\<host\>:\<port\>\<path\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kann ich nur einigen wenigen Quell-IP-Adressen Zugriff auf Application Gateway gewähren?

Ja. Weitere Informationen finden Sie unter [Application Gateway – Konfigurationsübersicht](./configuration-infrastructure.md#allow-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kann ich den gleichen Port sowohl für öffentliche als auch für private Listener verwenden?

Nein.

### <a name="does-application-gateway-support-ipv6"></a>Unterstützt Application Gateway IPv6?

Von Application Gateway v2 wird IPv6 derzeit nicht unterstützt. Es kann in einem Dual Stack-VNet betrieben werden, wo nur IPv4 verwendet wird, aber nur in Verbindung mit einem IPv4-Gatewaysubnetz. Application Gateway v1 bietet keine Unterstützung für Dual Stack-VNets. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Wie verwende ich Application Gateway v2 mit einer rein privaten Front-End-IP-Adresse?

Application Gateway v2 unterstützt derzeit nicht den rein privaten IP-Modus. Es unterstützt die folgenden Kombinationen:
* Private IP- und öffentliche IP-Adresse
* Nur öffentliche IP-Adresse

Wenn Sie jedoch Application Gateway v2 nur mit privater IP-Adresse verwenden möchten, können Sie das folgende Verfahren befolgen:
1. Erstellen einer Application Gateway-Instanz sowohl mit öffentlicher als auch privater Front-End-IP-Adresse
2. Erstellen Sie keine Listener für die öffentliche Front-End-IP-Adresse. Application Gateway lauscht nicht über die öffentliche IP-Adresse auf Datenverkehr, wenn keine Listener dafür erstellt werden.
3. Erstellen Sie für das Application Gateway-Subnetz eine [Netzwerksicherheitsgruppe](../virtual-network/network-security-groups-overview.md) mit der folgenden Konfiguration in der Reihenfolge der Priorität:
    
    a. Lassen Sie Datenverkehr von der Quelle als **GatewayManager**-Diensttag zu, das Ziel als **Beliebig** und den Zielport als **65200-65535**. Dieser Portbereich ist für die Kommunikation mit der Azure-Infrastruktur erforderlich. Diese Ports werden von der Zertifikatauthentifizierung geschützt (gesperrt). Externe Entitäten einschließlich der Gatewaybenutzeradministratoren können ohne entsprechende Zertifikate keine Änderungen an diesen Endpunkten vornehmen.
    
    b. Lassen Sie Datenverkehr von der Quelle als **AzureLoadBalancer**-Diensttag und Ziel und Zielport als **Beliebig** zu.
    
    c. Lehnen Sie sämtlichen eingehenden Datenverkehr von der Quelle als **Internet**-Diensttag und Ziel und Zielport als **Beliebig** ab. Weisen Sie dieser Regel die *geringste Priorität* in den Eingangsregeln zu.
    
    d. Behalten Sie die Standardregeln wie das Zulassen des VirtualNetwork-Eingangs bei, sodass der Zugriff auf die private IP-Adresse nicht blockiert wird.
    
    e. Die ausgehende Internetverbindung kann nicht blockiert sein. Andernfalls treten Probleme mit der Protokollierung, den Metriken usw. auf.

Beispiel-NSG-Konfiguration für den ausschließlichen Zugriff auf private IP-Adressen: ![Application Gateway v2-NSG-Konfiguration für den ausschließlichen Zugriff auf private IP-Adressen](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Konfiguration: TLS

### <a name="what-certificates-does-application-gateway-support"></a>Welche Zertifikate werden von Application Gateway unterstützt?

Application Gateway unterstützt selbstsignierte Zertifikate, Zertifizierungsstellenzertifikate (CA), Zertifikate für die erweiterte Validierung (EV), Zertifikate mit mehreren Domänen (SAN) und Platzhalterzertifikate.

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

Informationen zum Anpassen von TLS-Optionen finden Sie unter [Konfigurieren von TLS-Richtlinienversionen und Verschlüsselungssammlungen für Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Unterstützt Application Gateway eine erneute Verschlüsselung des Datenverkehrs an das Back-End?

Ja. Application Gateway unterstützt TLS-Abladung sowie End-to-End-TLS, das den Datenverkehr an das Back-End erneut verschlüsselt.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Kann ich die TLS-Richtlinie konfigurieren, um TLS-Protokollversionen zu steuern?

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

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Wie viele TLS-/SSL-Zertifikate werden von Application Gateway unterstützt?

Application Gateway unterstützt bis zu 100 TLS-/SSL-Zertifikate.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Wie viele Authentifizierungszertifikate für die erneute Back-End-Verschlüsselung werden von Application Gateway unterstützt?

Application Gateway unterstützt bis zu 100 Authentifizierungszertifikate.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Lässt sich Application Gateway nativ in Azure Key Vault integrieren?

Ja, die Application Gateway v2-SKU unterstützt Key Vault. Weitere Informationen finden Sie unter [TLS-Terminierung mit Key Vault-Zertifikaten](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Wie konfiguriere ich HTTPS-Listener für Websites vom Typ „.com“ und „.net“? 

Für das Routing auf der Grundlage mehrerer Domänen (hostbasiertes Routing) können Sie Listener für mehrere Standorte erstellen, Listener einrichten, die als Protokoll HTTPS verwenden, und die Listener mit den Routingregeln verknüpfen. Weitere Informationen finden Sie unter [Anwendungsgateways – Hosten mehrerer Websites](./multiple-site-overview.md).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Darf ich Sonderzeichen im PFX-Dateikennwort verwenden?

Nein, verwenden Sie nur alphanumerische Zeichen in Ihrem PFX-Dateikennwort.

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>Mein EV-Zertifikat wird von DigiCert ausgestellt, und mein Zwischenzertifikat wurde widerrufen. Wie erneuere ich mein Zertifikat in Application Gateway?

Zertifizierungsstellen-Browsermitglieder haben kürzlich Berichte veröffentlicht, in denen mehrere von Zertifizierungsstellenanbietern ausgestellte Zertifikate aufgeführt sind, die von unseren Kunden, Microsoft und der größeren Technologiecommunity verwendet werden und nicht den Industriestandards für öffentlich vertrauenswürdige Zertifizierungsstellen entsprechen. Die Berichte über die nicht konformen Zertifizierungsstellen (Certification Authorities, CAs) finden Sie unter den folgenden Links:  

* [Bug 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [Bug 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

Gemäß den Complianceanforderungen der Branche haben Zertifizierungsstellenanbieter damit begonnen, nicht konforme Zertifikate zu widerrufen und konforme Zertifikate auszustellen. Dies erfordert, dass die Zertifikate der Kunden neu ausgestellt werden. Microsoft arbeitet eng mit diesen Anbietern zusammen, um die potenziellen Auswirkungen auf Azure-Dienste zu minimieren. **Bei Ihren selbst ausgestellten Zertifikaten oder in „Bring Your Own Certificate“-Szenarien (BYOC-Szenarien) verwendeten Zertifikaten besteht jedoch nach wie vor das Risiko, dass sie unerwartet widerrufen werden**.

Wenn Sie überprüfen möchten, ob von Ihrer Anwendung verwendete Zertifikate widerrufen wurden, sehen Sie sich die [Ankündigung von DigiCert](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement) und den [Zertifikatsperrungs-Tracker](https://misissued.com/#revoked) an. Wenn Ihre Zertifikate widerrufen wurden (oder werden), müssen Sie neue Zertifikate von dem für Ihre Anwendungen verwendeten Zertifizierungsstellenanbieter anfordern. Um zu erfahren, wie Sie verhindern können, dass die Verfügbarkeit Ihrer Anwendung aufgrund unerwartet widerrufener Zertifikate unterbrochen wird, oder wie Sie ein Zertifikat aktualisieren, das widerrufen wurde, lesen Sie unseren Azure-Updatebeitrag. Darin finden Sie Links zu Korrekturmaßnahmen für verschiedene Azure-Dienste, die BYOC unterstützen: https://azure.microsoft.com/updates/certificateauthorityrevocation/

Application Gateway-spezifische Informationen finden Sie weiter unten.

Wenn Sie ein Zertifikat verwenden, das von einer der widerrufenen ICAs ausgestellt wurde, ist die Verfügbarkeit Ihrer Anwendung möglicherweise unterbrochen, und je nach Anwendung erhalten Sie möglicherweise eine Vielzahl von Fehlermeldungen, einschließlich der folgenden: 

1.  Ungültiges Zertifikat/Widerrufenes Zertifikat
2.  Timeout bei Verbindung
3.  HTTP 502

Um Beeinträchtigungen Ihrer Anwendung aufgrund dieses Problems zu vermeiden oder ein von einer Zertifizierungsstelle widerrufenes Zertifikat erneut ausstellen zu lassen, müssen Sie die folgenden Aktionen ausführen: 

1.  Wenden Sie sich an Ihren Zertifikatanbieter, um dafür zu sorgen, dass Ihre Zertifikate erneut ausgestellt werden.
2.  Aktualisieren Sie Ihre erneut ausgestellten Zertifikate in Azure Application Gateway/WAF mit der vollständigen [Vertrauenskette](/windows/win32/seccrypto/certificate-chains) (untergeordnetes Zertifikat, Zwischenzertifikat, Stammzertifikat). Führen Sie abhängig davon, wo Sie Ihr Zertifikat verwenden, entweder auf dem Listener oder in den HTTP-Einstellungen des Application Gateway die folgenden Schritte aus, um die Zertifikate zu aktualisieren. Und nutzen Sie die aufgeführten Dokumentationslinks, um weitere Informationen zu erhalten.
3.  Aktualisieren Sie Ihre Back-End-Anwendungsserver so, dass sie das erneut ausgestellte Zertifikat verwenden. Die Schritte zum Aktualisieren des Zertifikats können abhängig vom jeweils verwendeten Back-End-Server variieren. Lesen Sie die Dokumentation Ihres Herstellers.

Gehen Sie wie folgt vor, um das Zertifikat in Ihrem Listener zu aktualisieren:

1.  Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Application Gateway-Ressource.
2.  Öffnen Sie die Listenereinstellungen, die Ihrem Zertifikat zugeordnet sind.
3.  Klicken Sie auf „Ausgewähltes Zertifikat erneuern oder bearbeiten“.
4.  Laden Sie Ihr neues PFX-Zertifikat mit dem Kennwort hoch, und klicken Sie auf „Speichern“.
5.  Greifen Sie auf die Website zu, und überprüfen Sie, ob die Website erwartungsgemäß funktioniert. Weitere Informationen finden Sie in [dieser Dokumentation](./renew-certificates.md).

Wenn Sie in Ihrem Application Gateway-Listener auf Zertifikate aus Azure Key Vault verweisen, empfehlen wir die Ausführung der folgenden Schritte, um eine schnelle Änderung zu erreichen:

1.  Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihren Azure Key Vault-Einstellungen, die dem Application Gateway zugeordnet sind.
2.  Fügen Sie das neu ausgestellte Zertifikat zu Ihrem Speicher hinzu, oder importieren Sie es. Weitere Informationen zur Vorgehensweise finden Sie in [dieser Dokumentation](../key-vault/certificates/quick-create-portal.md).
3.  Navigieren Sie nach dem Importieren des Zertifikats zu Ihren Application Gateway-Listenereinstellungen, und klicken Sie unter „Zertifikat aus Key Vault auswählen“ auf die Dropdownliste „Zertifikat“, und wählen Sie das zuletzt hinzugefügte Zertifikat aus.
4.  Klicken Sie auf „Speichern“. Weitere Informationen zur TLS-Beendigung in Application Gateway mit Key Vault-Zertifikaten finden Sie in [diese Dokumentation](./key-vault-certs.md).


Gehen Sie wie folgt vor, um das Zertifikat in Ihren HTTP-Einstellungen zu aktualisieren:

Wenn Sie die V1-SKU des Application Gateway-/WAF-Diensts verwenden, müssen Sie das neue Zertifikat als Back-End-Authentifizierungszertifikat hochladen.
1.  Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Application Gateway-Ressource.
2.  Öffnen Sie die HTTP-Einstellungen, die Ihrem Zertifikat zugeordnet sind.
3.  Klicken Sie auf „Zertifikat hinzufügen“, laden Sie das neu ausgestellte Zertifikat hoch, und klicken Sie dann auf „Speichern“.
4.  Sie können das alte Zertifikat zu einem späteren Zeitpunkt entfernen. Klicken Sie dazu neben dem alten Zertifikat auf die Optionsschaltfläche „...“, und wählen Sie „Löschen“ aus. Klicken Sie dann auf „Speichern“.
Weitere Informationen finden Sie in [dieser Dokumentation](./end-to-end-ssl-portal.md#add-authenticationtrusted-root-certificates-of-back-end-servers).

Wenn Sie die V2-SKU des Application Gateway-/WAF-Diensts verwenden, müssen Sie das neue Zertifikat nicht in die HTTP-Einstellungen hochladen, da die V2-SKU „vertrauenswürdige Stammzertifikate“ verwendet und an dieser Stelle keine Aktion erforderlich ist.

## <a name="configuration---ingress-controller-for-aks"></a>Konfiguration: Eingangscontroller für AKS

### <a name="what-is-an-ingress-controller"></a>Was ist ein Eingangscontroller?

Kubernetes ermöglicht die Erstellung von `deployment`- und `service`-Ressourcen, um eine Gruppe von Pods intern im Cluster bereitzustellen. Um denselben Dienst extern verfügbar zu machen, wird eine [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/)-Ressource definiert, die Lastenausgleich, TLS-Terminierung und namensbasiertes virtuelles Hosting bereitstellt.
Um diese `Ingress`-Ressource zu bedienen, ist ein Eingangscontroller erforderlich, der auf Änderungen an `Ingress`-Ressourcen lauscht und die Lastenausgleichsrichtlinien konfiguriert.

Der Application Gateway-Eingangscontroller (AGIC) ermöglicht die Verwendung von [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) als Eingangsressource für einen [Azure Kubernetes Service-Cluster](https://azure.microsoft.com/services/kubernetes-service/) (auch als AKS-Cluster bezeichnet).

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Kann eine einzelne Eingangscontrollerinstanz mehrere Application Gateways verwalten?

Derzeit kann eine Instanz des Eingangscontrollers nur einem Application Gateway zugeordnet werden.

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>Warum funktioniert mein AKS-Cluster mit kubenet nicht mit dem Application Gateway-Eingangscontroller?

Der Application Gateway-Eingangscontroller versucht, die Routingtabellenressource automatisch dem Application Gateway-Subnetz zuzuordnen. Dabei können jedoch aufgrund fehlender Berechtigungen des Application Gateway-Eingangscontrollers Fehler auftreten. Wenn der Application Gateway-Eingangscontroller die Routingtabelle nicht dem Application Gateway-Subnetz zuordnen kann, wird in seinen Protokollen ein entsprechender Fehler angezeigt. In diesem Fall müssen Sie die vom AKS-Cluster erstellte Routingtabelle dem Application Gateway-Subnetz manuell zuordnen. Weitere Informationen finden Sie unter [Unterstützte benutzerdefinierte Routen](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>Kann ich meinen AKS-Cluster und Application Gateway in separaten virtuellen Netzwerken verbinden? 

Ja, solange die virtuellen Netzwerke mittels Peering verknüpft sind und keine Adressräume mit Überschneidungen aufweisen. Wenn Sie AKS mit kubenet ausführen, müssen Sie die von AKS generierte Routingtabelle dem Application Gateway-Subnetz zuordnen. 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>Welche Funktionen werden für das AGIC-Add-On nicht unterstützt? 

Die Unterschiede zwischen dem über Helm bereitgestellten AGIC und dem als AKS-Add-On bereitgestellten AGIC finden Sie [hier](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on).

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>Wann sollte ich das Add-On anstelle der Bereitstellung über Helm verwenden? 

Die Unterschiede zwischen dem über Helm bereitgestellten AGIC und dem als AKS-Add-On bereitgestellten AGIC finden Sie [hier](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Beachten Sie dabei insbesondere die Tabellen, in denen beschrieben wird, welche Szenarien unterstützt werden, wenn der AGIC über Helm bereitgestellt wird im Gegensatz zur Bereitstellung als AKS-Add-On. Im Allgemeinen können Sie durch die Bereitstellung über Helm Betafunktionen und Release Candidates vor einem offiziellen Release testen. 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>Kann ich steuern, welche AGIC-Version mit dem Add-On bereitgestellt wird?

Nein, das AGIC-Add-On ist ein verwalteter Dienst, d. h., Microsoft aktualisiert das Add-On automatisch auf die neueste stabile Version. 

## <a name="diagnostics-and-logging"></a>Diagnose und Protokollierung

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Welche Protokolltypen bietet Application Gateway?

In Application Gateway stehen drei Protokolle zur Verfügung: 

* **ApplicationGatewayAccessLog:** Das Zugriffsprotokoll enthält jede an das Application Gateway-Front-End gesendete Anforderung. Die Daten enthalten die IP des Aufrufers, die angeforderte URL, die Antwortlatenz, den Rückgabecode sowie die ein- und ausgehenden Bytes. Es enthält einen Datensatz pro Anwendungsgateway.
* **ApplicationGatewayPerformanceLog:** Das Leistungsprotokoll erfasst Leistungsinformationen für jedes Anwendungsgateway. Zu den Informationen zählen der Durchsatz in Bytes, die Gesamtanzahl übermittelter Anforderungen, Fehler bei der Anzahl der Anforderungen sowie die Anzahl fehlerfreier und fehlerhafter Back-End-Instanzen.
* **ApplicationGatewayFirewallLog:** Bei mit WAF konfigurierten Anwendungsgateways enthält das Firewallprotokoll Anforderungen, die entweder über den Erkennungs- oder über den Schutzmodus protokolliert werden.

Alle Protokolle werden alle 60 Sekunden erfasst. Weitere Informationen finden Sie unter [Back-End-Integrität, Diagnoseprotokolle und Metriken für Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Wie erkenne ich, dass die Mitglieder meines Back-End-Pools fehlerfrei sind?

Überprüfen Sie die Integrität mithilfe des PowerShell-Cmdlets `Get-AzApplicationGatewayBackendHealth` oder über das Portal. Weitere Informationen finden Sie unter [Application Gateway-Diagnose](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Welche Aufbewahrungsrichtlinie gilt für die Diagnoseprotokolle?

Diagnoseprotokolle werden an das Speicherkonto des Kunden gesendet. Kunden können die Aufbewahrungsrichtlinie je nach Präferenz festlegen. Diagnoseprotokolle können auch an einen Event Hub oder an Azure Monitor-Protokolle gesendet werden. Weitere Informationen finden Sie unter [Application Gateway-Diagnose](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Wie erhalte ich die Überwachungsprotokolle für Application Gateway?

Wählen Sie im Portal auf dem Menüblatt eines Anwendungsgateways die Option **Aktivitätsprotokoll** aus, um das Überwachungsprotokoll aufzurufen. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kann ich mit Application Gateway Warnungen einrichten?

Ja. In Application Gateway werden Warnungen für Metriken konfiguriert. Weitere Informationen finden Sie unter [Back-End-Integrität, Diagnoseprotokolle und Metriken für Application Gateway](./application-gateway-metrics.md) und [Überblick über Warnungen in Microsoft Azure](../azure-monitor/platform/alerts-overview.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Wie lassen sich Datenverkehrsstatistiken für Application Gateway analysieren?

Sie haben verschiedene Möglichkeiten zum Anzeigen und Analysieren von Zugriffsprotokollen. Sie können Azure Monitor-Protokolle, Excel, Power BI usw. verwenden.

Sie können auch eine Resource Manager-Vorlage verwenden, die die beliebte [GoAccess](https://goaccess.io/)-Protokollanalyse für Application Gateway-Zugriffsprotokolle installiert und ausführt. GoAccess stellt wertvolle HTTP-Datenverkehrsstatistiken bereit, z. B. eindeutige Besucher, angeforderte Dateien, Hosts, Betriebssysteme, Browser und HTTP-Statuscodes. Weitere Informationen finden Sie auf GitHub in der [Infodatei im Resource Manager-Vorlagenordner](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Was kann dazu führen, dass für die Back-End-Integrität ein unbekannter Status angezeigt wird?

In der Regel wird ein unbekannter Status angezeigt, wenn der Zugriff auf das Back-End durch eine Netzwerksicherheitsgruppe (NSG), einen benutzerdefinierten DNS oder durch benutzerdefiniertes Routing für das Application Gateway-Subnetz blockiert wird. Weitere Informationen finden Sie unter [Back-End-Integrität, Diagnoseprotokolle und Metriken für Application Gateway](application-gateway-diagnostics.md).

### <a name="are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet"></a>Werden NSG-Datenflussprotokolle in Netzwerksicherheitsgruppen unterstützt, die dem Application Gateway v2-Subnetz zugeordnet sind?

Wenn Sie über eine Netzwerksicherheitsgruppe im Application Gateway v2-Subnetz (Standard_v2, WAF_v2) verfügen, und wenn darin die NSG-Datenflussprotokolle aktiviert sind, kommt es aufgrund aktueller Plattformeinschränkungen zu nicht deterministischem Verhalten, und dieses Szenario wird derzeit nicht unterstützt.

### <a name="does-application-gateway-store-customer-data"></a>Speichert Application Gateway Kundendaten?

Nein, Kundendaten werden in Application Gateway nicht gespeichert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Application Gateway finden Sie unter [Was ist Azure Application Gateway?](overview.md).
