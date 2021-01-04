---
title: 'Azure Front Door: Häufig gestellte Fragen'
description: Diese Seite enthält Antworten auf häufig gestellte Fragen zu Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2020
ms.author: duau
ms.openlocfilehash: abc4529d6076496b34859eec2b931a8dcbd1ce93
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296589"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Häufig gestellte Fragen zu Azure Front Door

In diesem Artikel werden häufige Fragen zu den Features und der Funktionalität von Azure Front Door beantwortet. Wenn Sie hier keine Antwort auf Ihre Frage finden, können Sie sich über die folgenden Kanäle an uns wenden (Eskalationsreihenfolge):

1. Im Abschnitt „Kommentare“ dieses Artikels.
2. [UserVoice zu Azure Front Door](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft-Support:** Wählen Sie zum Erstellen einer neuen Supportanfrage im Azure-Portal auf der Registerkarte **Hilfe** die Schaltfläche **Hilfe und Support** und anschließend die Option **Neue Supportanfrage**.

## <a name="general"></a>Allgemein

### <a name="what-is-azure-front-door"></a>Was ist Azure Front Door?

Azure Front Door ist ein ADN (Application Delivery Network) als Dienst und bietet verschiedene Lastenausgleichsfunktionen auf Schicht 7 für Ihre Anwendung. Es bietet die Beschleunigung dynamischer Websites (Dynamic Site Acceleration, DSA) zusammen mit globalem Lastenausgleich mit Failover nahezu in Echtzeit. Es ist ein hoch verfügbarer und skalierbarer Dienst, der vollständig von Azure verwaltet wird.

### <a name="what-features-does-azure-front-door-support"></a>Welche Features werden von Azure Front Door unterstützt?

Azure Front Door unterstützt die Beschleunigung dynamischer Websites (Dynamic Site Acceleration, DSA), TLS/SSL-Abladung und End-to-End-TLS, Web Application Firewall, cookiebasierte Sitzungsaffinität, Routing auf URL-Pfadbasis, kostenlose Zertifikate, die Verwaltung mehrerer Domänen sowie weitere Features. Eine vollständige Liste der unterstützten Features finden Sie unter [Übersicht über Azure Front Door](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Was ist der Unterschied zwischen Azure Front Door und Azure Application Gateway?

Obwohl sowohl Front Door als auch Application Gateway einen Lastenausgleich der Schicht 7 (HTTP/HTTPS) darstellt, besteht der Hauptunterschied darin, dass Front Door ein globaler und Application Gateway ein regionaler Dienst ist. Während Front Door den Lastenausgleich zwischen den verschiedenen Skalierungseinheiten, Clustern und Stempeleinheiten über Regionen hinweg ermöglicht, gestattet Application Gateway den Lastenausgleich zwischen VMs/Containern usw., die sich innerhalb der Skalierungseinheit befinden.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Wann sollte ein Application Gateway hinter Front Door bereitgestellt werden?

Wichtige Szenarien, in denen Application Gateway hinter Front Door verwendet werden sollte:

- Front Door kann einen pfadbasierten Lastenausgleich nur auf globaler Ebene durchführen, aber wenn der Datenverkehr in ihrem virtuellen Netzwerk (VNET) noch weiter ausgeglichen werden soll, dann sollte Application Gateway verwendet werden.
- Da Front Door auf VM-/Containerebene nicht funktioniert, kann es keinen Verbindungsausgleich durchführen. Mit Application Gateway können Sie jedoch den Verbindungsausgleich durchführen. 
- Mit einem Application Gateway hinter Azure Front Door Service können Sie eine TLS/SSL-Abladung von 100 % erreichen und nur HTTP-Anforderungen innerhalb ihres virtuellen Netzwerks (VNET) weiterleiten.
- Sowohl Front Door als auch Application Gateway unterstützen die Sitzungsaffinität. Während Front Door den nachfolgenden Datenverkehr von einer Benutzersitzung auf denselben Cluster oder Back-End in einer bestimmten Region leiten kann, kann Application Gateway den Datenverkehr direkt auf demselben Server innerhalb des Clusters zuordnen.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kann Azure Load Balancer hinter Front Door bereitgestellt werden?

Azure Front Door benötigt einen öffentlichen VIP oder einen öffentlich verfügbaren DNS-Namen, um den Datenverkehr zu weiterzuleiten. Die Bereitstellung von Azure Load Balancer hinter Front Door ist ein häufiger Anwendungsfall.

### <a name="what-protocols-does-azure-front-door-support"></a>Welche Protokolle werden von Azure Front Door unterstützt?

Azure Front Door unterstützt HTTP, HTTPS und HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Wie unterstützt Azure Front Door HTTP/2?

Die Unterstützung des HTTP/2-Protokolls ist nur für Clients verfügbar, die mit Azure Front Door verbunden sind. Die Kommunikation mit den Back-Ends im Back-End-Pool erfolgt über HTTP/1.1. Die HTTP/2-Unterstützung ist standardmäßig aktiviert.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Welche Ressourcen werden derzeit als Teil des Back-End-Pools unterstützt?

Back-End-Pools können aus Storage, Web App, Kubernetes-Instanzen oder einem beliebigen anderen benutzerdefinierten Hostnamen mit öffentlicher Konnektivität bestehen. Azure Front Door erfordert, dass die Back-Ends entweder über eine öffentliche IP-Adresse oder einen öffentlich auflösbaren DNS-Hostnamen definiert werden. Mitglieder von Back-End-Pools können auf Zonen, Regionen oder sogar außerhalb von Azure verteilt sein oder sich außerhalb von Azure befinden, sofern sie über eine öffentliche Konnektivität verfügen.

### <a name="what-regions-is-the-service-available-in"></a>In welchen Regionen ist der Dienst verfügbar?

Azure Front Door ist ein globaler und nicht an eine bestimmte Azure-Region gebundener Dienst. Der einzige Speicherort, den Sie beim Erstellen einer Front Door-Instanz angeben müssen, ist der Speicherort der Ressourcengruppe, der im Wesentlichen angibt, wo die Metadaten für die Ressourcengruppe gespeichert werden. Die Front Door-Ressource selbst wird als globale Ressource erstellt und die Konfiguration wird global für alle POPs (Point of Presence) bereitgestellt. 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Welche POP-Standorte sind für Azure Front Door verfügbar?

Azure Front Door hat dieselbe Liste von POP-Standorten (Point of Presence) wie Azure CDN von Microsoft. Die vollständige Liste unserer POPs finden Sie unter [Azure CDN-POP-Standorte von Microsoft](../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Ist Azure Front Door eine dedizierte Bereitstellung für meine Anwendung, oder wird es zur gemeinsamen Nutzung für Kunden freigegeben?

Azure Front Door ist ein global verteilter, mehrinstanzenfähiger Dienst. Die Infrastruktur für Front Door wird somit von allen Kunden gemeinsam genutzt. Wenn Sie jedoch ein Front Door-Profil erstellen, definieren Sie die für Ihre Anwendung erforderliche spezifische Konfiguration, und Änderungen an Ihrer Front Door-Ressource wirken sich nicht auf andere Front Door-Konfigurationen aus.

### <a name="is-http-https-redirection-supported"></a>Wird die Umleitung von HTTP zu HTTPS unterstützt?

Ja. Tatsächlich unterstützt Azure Front Door Host-, Pfad- und Abfragezeichenfolgen-Umleitung sowie einen Teil der URL-Umleitung. Weitere Informationen zur [URL-Umleitung](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>In welcher Reihenfolge werden Routingregeln verarbeitet?

Die Routen für Front Door sind nicht sortiert und eine bestimmte Route wird anhand der besten Übereinstimmung ausgewählt. Weitere Informationen zu [Abgleich von Anforderungen mit Routingregeln durch Front Door](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Wie kann ich den Zugriff auf mein Back-End nur auf Azure Front Door beschränken?

Um Ihre Anwendung so zu sperren, dass sie nur Datenverkehr von Ihrer spezifischen Front Door-Ressource akzeptiert, müssen Sie IP-ACLs für Ihr Back-End einrichten und dann den Satz der akzeptierten Werte auf den Header „X-Forwarded-Host“ einschränken, der von Azure Front Door gesendet wird. Diese Schritte werden wie folgt beschrieben:

- Konfigurieren Sie IP-ACLs für Ihre Back-Ends so, dass sie nur Datenverkehr aus dem Back-End-IP-Adressraum von Azure Front Door und den Infrastrukturdiensten von Azure akzeptieren. Lesen Sie die unten stehenden IP-Details, um Ihr Backend mit ACLs zu versehen:
 
    - Ziehen Sie den Abschnitt *AzureFrontDoor.Backend* unter [IP-Bereiche und Diensttags in Azure](https://www.microsoft.com/download/details.aspx?id=56519) für den IPv4-Back-End-IP-Adressbereich von Front Door zurate. Alternativ können Sie auch das Diensttag *AzureFrontDoor.Backend* in Ihren [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules) verwenden.
    - [Grundlegenden Infrastrukturdienste](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) von Azure über virtualisierte IP-Hostadressen: `168.63.129.16` und `169.254.169.254`

    > [!WARNING]
    > Der Back-End-IP-Adressbereich von Front Door kann später geändert werden, aber wir werden sicherstellen, dass zuvor eine Integration in [Azure-IP-Adressbereiche und Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) erfolgt ist. Es wird empfohlen, dass Sie [Azure-IP-Adressbereiche und Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) für alle Änderungen oder Updates abonnieren.

-    Führen Sie eine GET-Operation in Front Door mit der API-Version `2020-01-01` oder höher aus. Suchen Sie im API-Befehl nach dem `frontdoorID`-Feld. Filtern Sie nach dem von Front Door an Ihr Back-End gesendeten eingehenden Header „**X-Azure-FDID**“ mit dem Wert des Felds `frontdoorID`. Sie finden den `Front Door ID`-Wert auch im Abschnitt „Übersicht“ über die Front Door-Portalseite. 

- Wenden Sie Regelfilterung in Ihrem Back-End-Webserver an, um den Datenverkehr basierend auf dem resultierenden „X-Azure-FDID“-Headerwert einzuschränken.

  Hier folgt ein Beispiel für [Microsoft-Internetinformationsdienste (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Kann sich die Anycast-IP-Adresse während der Lebensdauer meiner Front Door-Instanz ändern?

Die Front-End-Anycast-IP-Adresse für Ihre Front Door-Instanz sollte sich in der Regel nicht ändern und kann während der Front Door-Lebensdauer statisch bleiben. Es gibt hierfür jedoch **keine Garantien**. Es wird empfohlen, keine direkten Abhängigkeiten von der IP-Adresse herzustellen.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Unterstützt Azure Front Door statische oder dedizierte IP-Adressen?

Azure Front Door unterstützt derzeit keine statischen oder dedizierten Front-End-Anycast-IP-Adressen. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Unterstützt Azure Front Door X-Forwarded-For-Header?

Ja. Azure Front Door unterstützt die Header „X-Forwarded-For“, „X-Forwarded-Host“ und „X-Forwarded-Proto“. Wenn der Header für „X-Forwarded-For“ bereits vorhanden war, fügt Front Door die Client-Socket-IP-Adresse an diesen an. Andernfalls wird der Header mit der Client-Socket-IP-Adresse als Wert hinzugefügt. Für X-Forwarded-Host und X-Forwarded-Proto wird der Wert außer Kraft gesetzt.

Weitere Informationen zu [von Front Door unterstützten HTTP-Headern](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Wie lange dauert die Bereitstellung von Azure Front Door? Funktioniert meine Front Door-Instanz auch bei einem Update weiterhin?

Eine neue Front Door-Erstellung oder jegliche Updates für eine bestehende Front Door-Instanz benötigen ungefähr drei bis fünf Minuten für die globale Bereitstellung. Das bedeutet, dass Ihre Front Door-Konfiguration in ungefähr drei bis fünf Minuten global auf allen unseren POPs bereitgestellt wird.

Hinweis: Bei benutzerdefinierten TLS/SSL-Zertifikatupdates dauert die globale Bereitstellung etwa 30 Minuten.

Alle Aktualisierungen von Routen oder Back-End-Pools usw. erfolgen nahtlos und verursachen keine Ausfallzeiten (wenn die neue Konfiguration korrekt ist). Zertifikataktualisierungen sind ebenfalls unteilbar und verursachen keine Ausfälle, es sei denn, Sie wechseln von „AFD Managed“ (von Azure Front Door verwaltet) zu „Use your own cert“ (Eigenes Zertifikat verwenden) oder umgekehrt.


## <a name="configuration"></a>Konfiguration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kann Azure Front Door für den Datenverkehr in einem virtuellen Netzwerk einen Lastenausgleich vornehmen oder den Datenverkehr weiterleiten?

Azure Front Door (AFD) erfordert eine öffentliche IP-Adresse oder einen öffentlich auflösbaren DNS-Namen, um den Datenverkehr weiterzuleiten. Die Antwort ist somit, dass AFD den Datenverkehr in einem virtuellen Netzwerk nicht direkt weiterleiten kann, aber die zwischenzeitliche Verwendung von Application Gateway oder Azure Load Balancer führt in diesem Szenario zu einer Lösung.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Welche Timeouts und Grenzwerte gelten für Azure Front Door?

Weitere Informationen zu allen dokumentierten [Timeouts und Grenzwerten für Azure Front Door](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Wie lange dauert es, bis eine Regel wirksam wird, nachdem sie zur Front Door-Regel-Engine hinzugefügt wurde?

Die Konfiguration der Regel-Engine benötigt etwa 10 bis 15 Minuten, um ein Update durchzuführen. Sie können erwarten, dass die Regel wirksam wird, sobald das Update abgeschlossen ist. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-vice-versa"></a>Kann ich Azure CDN hinter meinem Front Door-Profil konfigurieren oder umgekehrt?

Azure Front Door und Azure CDN können nicht zusammen konfiguriert werden, da beide Dienste bei der Antwort auf Anforderungen die gleichen Azure Edge-Sites nutzen. 

## <a name="performance"></a>Leistung

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Wie unterstützt Azure Front Door die Hochverfügbarkeit und Skalierbarkeit?

Azure Front Door ist eine global verteilte mehrinstanzenfähige Plattform mit riesigem Kapazitätsvolumen, um die Skalierungsanforderungen Ihrer Anwendung zu erfüllen. Front Door wird vom Rand des globalen Netzwerks von Microsoft bereitgestellt und bietet globale Funktionen für den Lastenausgleich, mit denen Sie für Ihre gesamte Anwendung oder sogar für einzelne Microservices über Regionen oder verschiedene Clouds hinweg einen Failover ausführen können.

## <a name="tls-configuration"></a>TLS-Konfiguration

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Welche TLS-Versionen werden vom Azure Front Door unterstützt?

Alle Front Door-Profile, die nach September 2019 erstellt wurden, verwenden standardmäßig TLS 1.2 als Mindestvoraussetzung.

Front Door unterstützt die TLS-Versionen 1.0, 1.1 und 1.2. TLS 1.3 wird noch nicht unterstützt.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Welche Zertifikate werden von Azure Front Door unterstützt?

Um das HTTPS-Protokoll für die sichere Bereitstellung von Inhalten in einer benutzerdefinierten Front Door-Domäne zu aktivieren, können Sie ein von Azure Front Door verwaltetes oder Ihr eigenes Zertifikat verwenden.
Die von Front Door verwaltete Option stellt über Digicert ein TLS/SSL-Standardzertifikat zur Verfügung, das im Schlüsseltresor von Front Door gespeichert wird. Wenn Sie ein eigenes Zertifikat verwenden möchten, können Sie ein Zertifikat einer unterstützten Zertifizierungsstelle integrieren. Hierbei kann es sich um ein Standard-TLS-, ein erweitertes Validierungs- oder sogar um ein Platzhalterzertifikat handeln. Selbstsignierte Zertifikate werden nicht unterstützt. Weitere Informationen zu [Aktivieren von HTTPS für eine benutzerdefinierte Domäne](./front-door-custom-domain-https.md).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Unterstützt Front Door die automatische Rotation von Zertifikaten?

Bei der Option für verwaltete Zertifikate von Front Door werden die Zertifikate von Front Door automatisch rotiert. Wenn Sie ein verwaltetes Zertifikat von Front Door verwenden und feststellen, dass das Ablaufdatum des Zertifikats weniger als 60 Tage entfernt ist, erstellen Sie ein Supportticket.
</br>Bei Ihrem eigenen benutzerdefinierten TLS/SSL-Zertifikat wird die automatische Rotation nicht unterstützt. Ähnlich wie bei der erstmaligen Einrichtung für eine bestimmte benutzerdefinierte Domäne müssen Sie Front Door auf die richtige Zertifikatversion in Ihrem Key Vault verweisen und sicherstellen, dass der Dienstprinzipal für Front Door noch Zugriff auf den Key Vault besitzt. Dieser aktualisierte Zertifikatrolloutvorgang von Front Door ist atomisch und verursacht keine Produktionsauswirkungen, sofern sich der Antragstellername oder der alternative Antragstellername (SAN) für das Zertifikat nicht ändert.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Welche Verschlüsselungssammlungen werden derzeit von Azure Front Door unterstützt?

Bei TLS1.2 werden folgende Verschlüsselungssammlungen unterstützt: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Wenn Sie benutzerdefinierte Domänen mit TLS1.0/1.1 verwenden, werden folgende Verschlüsselungssammlungen unterstützt:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Kann ich die TLS-Richtlinie konfigurieren, um die TLS-Protokollversionen zu steuern?

Sie können eine minimale TLS-Version in Azure Front Door in den HTTPS-Einstellungen der benutzerdefinierten Domäne über das Azure-Portal oder die [Azure-REST-API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) konfigurieren. Derzeit können Sie zwischen 1.0 und 1.2 wählen.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kann ich Front Door so konfigurieren, dass nur bestimmte Verschlüsselungssammlungen unterstützt werden?

Nein. Die Konfiguration von Front Door für bestimmte Verschlüsselungssammlungen wird nicht unterstützt. Sie können jedoch Ihr eigenes benutzerdefiniertes TLS/SSL-Zertifikat von Ihrer Zertifizierungsstelle (z. B. Verisign, Entrust oder Digicert) erhalten und beim Generieren bestimmte Suites mit Verschlüsselungsverfahren auf dem Zertifikat markieren lassen. 

### <a name="does-front-door-support-ocsp-stapling"></a>Unterstützt Front Door OCSP-Anheftung?

Ja, OCSP-Anheftung wird von Front Door standardmäßig unterstützt, und es ist keine Konfiguration erforderlich.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Unterstützt Azure Front Door auch eine erneute Verschlüsselung des Datenverkehrs an das Back-End?

Ja. Azure Front Door unterstützt TLS/SSL-Abladung sowie End-to-End-TLS, das den Datenverkehr an das Back-End erneut verschlüsselt. Da die Verbindungen zum Back-End über dessen öffentliche IP-Adresse erfolgen, empfehlen wir Ihnen, Front Door so zu konfigurieren, dass HTTPS als Weiterleitungsprotokoll verwendet wird.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Werden von Front Door selbstsignierte Zertifikate auf dem Back-End für die HTTPS-Verbindung unterstützt?

Nein, selbstsignierte Zertifikate werden von Front Door nicht unterstützt, und die Einschränkung gilt für beide:

1. **Back-Ends**: Sie können keine selbstsignierten Zertifikate verwenden, wenn Sie den Datenverkehr als HTTPS oder HTTPS-Integritätstests weiterleiten oder den Cache vom Ursprungsbereich für die Routingregeln mit aktiviertem Zwischenspeichern auffüllen.
2. **Front-End**: Sie können keine selbstsignierten Zertifikate verwenden, wenn Sie Ihr eigenes benutzerdefiniertes TLS/SSL-Zertifikat zum Aktivieren von HTTPS für Ihre benutzerdefinierte Domäne verwenden.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Warum schlägt der HTTPS-Datenverkehr an das Back-End fehl?

Es kann zwei Gründe geben, warum der HTTPS-Datenverkehr für eine erfolgreiche HTTPS-Verbindung mit Ihrem Back-End – ob für Integritätstests oder Weiterleitungsanforderungen – fehlschlägt:

1. **Zertifikatantragsteller-Namenskonflikt**: Bei HTTPS-Verbindungen erwartet Front Door, dass Ihr Back-End ein Zertifikat von einer gültigen Zertifizierungsstelle vorlegt, und dass die Antragstellernamen mit dem Back-End-Hostnamen übereinstimmen. Wenn beispielsweise der Back-End-Hostname auf `myapp-centralus.contosonews.net` festgelegt ist und das Zertifikat, das Ihr Back-End während des TLS-Handshakes vorlegt, weder `myapp-centralus.contosonews.net` noch `*myapp-centralus*.contosonews.net` im Antragstellernamen aufweist, verweigert Front Door die Verbindung, und ein Fehler tritt auf. 
    1. **Lösung**: Auch wenn es im Hinblick auf Compliance nicht empfohlen wird, können Sie diesen Fehler umgehen, indem Sie die Prüfung des Zertifikatantragsteller-Namens für Ihre Front Door-Instanz deaktivieren. Diese Option finden Sie unter „Einstellungen“ im Azure-Portal und unter „BackendPoolsSettings“ in der API.
2. **Back-End-Hostingzertifikat von einer ungültigen Zertifizierungsstelle**: In Front Door können nur Zertifikate von [gültigen Zertifizierungsstellen](./front-door-troubleshoot-allowed-ca.md) am Back-End verwendet werden. Zertifikate von internen Zertifizierungsstellen oder selbstsignierten Zertifikate sind nicht zulässig.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Kann ich bei Azure Front Door die Clientauthentizierung/gegenseitige Authentifizierung verwenden?

Nein. Obwohl Azure Front Door TLS 1.2 unterstützt, bei dem die Clientauthentizierung/gegenseitige Authentifizierung in [RFC 5246](https://tools.ietf.org/html/rfc5246) eingeführt wurde, unterstützt Azure Front Door diese Authentifizierungsart zurzeit nicht.

## <a name="diagnostics-and-logging"></a>Diagnose und Protokollierung

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Welche Arten von Metriken und Protokollen sind mit Azure Front Door verfügbar?

Informationen zu Protokollen und anderen Diagnosefunktionen finden Sie unter [Überwachen von Metriken und Protokollen für Front Door](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Welche Aufbewahrungsrichtlinie gilt für die Diagnoseprotokolle?

Diagnoseprotokolle gelangen in die Speicherkonten von Kunden, und Kunden können die Aufbewahrungsrichtlinie je nach Präferenz festlegen. Diagnoseprotokolle können auch an einen Event Hub oder Azure Monitor-Protokolle gesendet werden. Weitere Informationen finden Sie unter [Azure Front Door-Diagnose](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Wie erhalte ich Überwachungsprotokolle für Azure Front Door?

Überwachungsprotokolle sind für Azure Front Door verfügbar. Klicken Sie im Portal auf dem Menüblatt Ihrer Front Door-Instanz auf **Aktivitätsprotokoll**, um das Überwachungsprotokoll aufzurufen. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Kann ich mit Azure Front Door Warnungen festlegen?

Ja. Azure Front Door unterstützt Warnungen. Warnungen werden für Metriken konfiguriert. 

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
