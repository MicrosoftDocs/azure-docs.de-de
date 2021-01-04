---
title: Aktivieren von End-to-End-TLS in Azure Application Gateway
description: Dieser Artikel enthält eine Übersicht über die Unterstützung von End-to-End-TLS in Application Gateway.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: c39401289ffc6f27c292168adaa15c5163a3967b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001285"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway

Transport Layer Security (TLS), zuvor bekannt als Secure Sockets Layer (SSL), ist die standardmäßige Sicherheitstechnologie, mit der eine verschlüsselte Verbindung zwischen einem Webserver und einem Browser hergestellt wird. Über diese Verbindung wird sichergestellt, dass alle Daten privat und verschlüsselt zwischen dem Webserver und dem Browser übertragen werden. Application Gateway unterstützt sowohl die TLS-Terminierung am Gateway als auch die End-to-End-TLS-Verschlüsselung.

## <a name="tls-termination"></a>TLS-Terminierung

Application Gateway unterstützt die TLS-Beendigung am Gateway, wonach der Datenverkehr in der Regel unverschlüsselt zu den Back-End-Servern gelangt. Die TLS-Terminierung am Application Gateway bietet zahlreiche Vorteile:

- **Verbesserte Leistung:** Der größte Leistungsabfall bei der TLS-Entschlüsselung entsteht beim anfänglichen Handshake. Der Entschlüsselungsserver verbessert die Leistung durch das Zwischenspeichern von TLS-Sitzungs-IDs und das Verwalten von TLS-Sitzungstickets. Wenn dieser Vorgang am Application Gateway erfolgt, können alle Anforderungen vom gleichen Client die zwischengespeicherten Werte verwenden. Wenn der Vorgang auf den Back-End-Servern erfolgt, muss der Client jedes Mal eine neue Authentifizierung vornehmen, wenn die Clientanforderungen an einen anderen Server gerichtet werden. Sie können dieses Problem durch die Verwendung von TLS-Tickets beheben, diese werden jedoch nicht von allen Clients unterstützt und können das Konfigurieren und Verwalten erschweren.
- **Bessere Auslastung der Back-End-Server:** Die SSL/TLS-Verarbeitung bringt eine hohe CPU-Intensität mit sich, die sich mit steigenden Schlüsselgrößen weiter verstärkt. Wenn Sie die Back-End-Server von dieser Aufgabe befreien, können diese für ihren effizientesten Zweck eingesetzt werden: das Bereitstellen von Inhalten.
- **Intelligentes Routing:** Durch die Entschlüsselung des Datenverkehrs erhält das Anwendungsgateway Zugriff auf Anforderungsinhalte wie Header, URI etc. und kann diese Daten dann zum Weiterleiten von Anforderungen verwenden.
- **Zertifikatverwaltung:** Zertifikate müssen nicht für alle Back-End-Server, sondern nur für das Anwendungsgateway erworben und installiert werden. Das spart Zeit und Geld.

Zum Konfigurieren der TLS-Terminierung muss dem Listener ein TLS/SSL-Zertifikat hinzugefügt werden, damit Application Gateway einen symmetrischen Schlüssel gemäß der Spezifikation des TLS/SSL-Protokolls ableiten kann. Der symmetrische Schlüssel wird dann zum Verschlüsseln und Entschlüsseln des Datenverkehrs mit dem Gateway verwendet. Das TLS/SSL-Zertifikat muss im PFX-Format (privater Informationsaustausch) vorliegen. In diesem Dateiformat können Sie den privaten Schlüssel exportieren. Das ist erforderlich, damit das Anwendungsgateway die Ver- und Entschlüsselung des Datenverkehrs durchführen kann.

> [!IMPORTANT] 
> Das Zertifikat auf dem Listener erfordert das Hochladen der gesamten Zertifikatskette (Stammzertifikat von der Zertifizierungsstelle, die Zwischenzertifikate und untergeordnetes Zertifikat), um die Vertrauenskette herzustellen. 


> [!NOTE] 
>
> Das Anwendungsgateway bietet keine Funktionen zum Erstellen neuer Zertifikate oder Senden von Zertifikatanforderungen an Zertifizierungsstellen.

Zum Hersteller der TLS-Verbindung müssen Sie sicherstellen, dass das TLS/SSL-Zertifikat die folgenden Bedingungen erfüllt:

- Das aktuelle Datum und die aktuelle Uhrzeit entsprechen den Datumsbereichen „Gültig ab“ und „Gültig bis“ im Zertifikat.
- Der allgemeine Name (Common Name, CN) des Zertifikats entspricht dem Hostheader in der Anforderung. Wenn der Client eine Anforderung beispielsweise an `https://www.contoso.com/` richtet, muss der CN `www.contoso.com` lauten.

### <a name="certificates-supported-for-tls-termination"></a>Unterstützte Zertifikate für die TLS-Beendigung

Das Anwendungsgateway unterstützt die folgenden Arten von Zertifikaten:

- CA-Zertifikat: Ein CA-Zertifikat ist ein digitales Zertifikat, das von einer Zertifizierungsstelle (Certificate Authority, CA) ausgestellt wurde.
- EV-Zertifikat: Ein EV-Zertifikat (Extended Validation) ist ein Zertifikat, das den Branchenrichtlinien zur erweiterten Überprüfung entspricht. Die Locator-Leiste des Browsers wird dadurch grün, und auch der Firmennamen wird veröffentlicht.
- Platzhalterzertifikat: Dieses Zertifikat unterstützt eine beliebige Anzahl von untergeordneten Domänen basierend auf *.site.com. * wird durch Ihre untergeordnete Domäne ersetzt. Es unterstützt jedoch nicht site.com. Falls Ihre die auf Benutzer Ihre Website zugreifen, ohne die führende Zeichenfolge „www“ einzugeben, wird das Platzhalterzertifikat diese Eingabe nicht berücksichtigen.
- Selbstsignierte Zertifikate: Clientbrowser vertrauen diesen Zertifikaten nicht und warnen den Benutzer, dass das virtuelle Zertifikat des Diensts keiner Vertrauenskette angehört. Selbstsignierte Zertifikate eignen sich gut für Tests oder Umgebungen, in denen Administratoren die Clients kontrollieren und Sicherheitswarnungen des Browsers sicher umgehen können. Produktionsworkloads sollten niemals selbstsignierte Zertifikate verwenden.

Weitere Informationen finden Sie im Artikel zum [Konfigurieren der TLS-Terminierung mit Application Gateway](./create-ssl-portal.md).

### <a name="size-of-the-certificate"></a>Größe des Zertifikats
Lesen Sie den Abschnitt [Application Gateway-Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits) durch, um die maximal unterstützte Größe von TLS/SSL-Zertifikaten zu erfahren.

## <a name="end-to-end-tls-encryption"></a>End-to-End-TLS-Verschlüsselung

Möglicherweise möchten Sie keine unverschlüsselte Kommunikation mit den Back-End-Servern verwenden. Möglicherweise sollen Sicherheits- oder Complianceanforderungen erfüllt werden, oder in der Anwendung wird nur eine sichere Verbindung akzeptiert. Im Hinblick auf diese Anforderungen unterstützt Azure Application Gateway die End-to-End-TLS-Verschlüsselung.

End-to-End-TLS ermöglicht Ihnen die Verschlüsselung und sichere Übertragung vertraulicher Daten an das Back-End bei Verwendung der Lastenausgleichsfunktionen von Schicht 7 von Application Gateway. Zu diesen Funktionen zählen unter anderem cookiebasierte Sitzungsaffinität, URL-basiertes Routing, Unterstützung von standortbasiertem Routing und die Möglichkeit zum Umschreiben oder Einfügen von Headern vom Typ „X-Forwarded-*“.

Bei der Konfiguration mit dem End-to-End-TLS-Kommunikationsmodus beendet Application Gateway die TLS-Sitzungen am Gateway und entschlüsselt den Benutzerdatenverkehr. Anschließend werden die konfigurierten Regeln angewendet, um eine geeignete Instanz des Back-End-Pools auszuwählen, an die der Datenverkehr geroutet werden soll. Application Gateway initiiert anschließend eine neue TLS-Verbindung mit dem Back-End-Server und verschlüsselt die Daten mit dem öffentlichen Schlüssel des Back-End-Serverzertifikats erneut, bevor die Anforderung an das Back-End übertragen wird. Antworten vom Webserver durchlaufen denselben Prozess zurück an den Endbenutzer. End-to-End-TLS wird aktiviert, indem Sie die Protokolleinstellung in der [Back-End-HTTP-Einstellung](./configuration-overview.md#http-settings) auf „HTTPS“ festlegen. Diese Einstellung wird anschließend auf einen Back-End-Pool angewandt.

Bei der Application Gateway- und WAF v1-SKU gilt die TLS-Richtlinie für den Front-End- sowie den Back-End-Datenverkehr. Auf dem Front-End fungiert Application Gateway als Server und erzwingt die Richtlinie. Auf dem Back-End fungiert Application Gateway als Client und sendet die Protokoll- bzw. Verschlüsselungsinformationen als bevorzugte Einstellung während TLS-Handshakes.

Bei der Application Gateway- und WAF v2-SKU gilt die TLS-Richtlinie nur für den Front-End-Datenverkehr. Alle Verschlüsselungen werden dem Back-End-Server angeboten, auf dem während des Handshakes die Auswahl spezifischer Verschlüsselungen und der TLS-Version gesteuert wird.

Application Gateway kommuniziert nur mit den Back-End-Servern, deren Zertifikat in der Zulassungsliste von Application Gateway enthalten ist oder deren Zertifikate von bekannten Zertifizierungsstellen signiert wurden und deren CN mit dem Hostnamen in den HTTP-Einstellungen auf dem Back-End übereinstimmt. Dazu zählen vertrauenswürdige Azure-Dienste wie Azure App Service oder Azure App Service-Web-Apps und Azure API Management.

Wenn die Zertifikate der Mitglieder im Back-End-Pool nicht von bekannten Zertifizierungsstellen signiert wurden, muss jede Instanz im Back-End-Pool mit aktiviertem End-to-End-TLS mit einem Zertifikat konfiguriert werden, um die sichere Kommunikation zu ermöglichen. Durch das Hinzufügen des Zertifikats wird sichergestellt, dass das Anwendungsgateway nur mit bekannten Back-End-Instanzen kommuniziert. Außerdem wird auf diese Weise die End-to-End-Kommunikation gesichert.

> [!NOTE] 
>
> Das Zertifikat, das der **Back-End-HTTP-Einstellung** zum Authentifizieren der Back-End-Server hinzugefügt wurde, kann mit dem Zertifikat übereinstimmen, das dem **Listener** zur TLS-Terminierung am Anwendungsgateway oder an einer anderen Instanz hinzugefügt wurde, um die Sicherheit zu verbessern.

![End-to-End-TLS-Szenario][1]

In diesem Beispiel werden Anforderungen, für die TLS 1.2 verwendet wird, an Back-End-Server in Pool1 geleitet, indem End-to-End-TLS genutzt wird.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>End-to-End-TLS und Zulassungslisten für Zertifikate

Application Gateway kommuniziert nur mit bekannten Back-End-Instanzen, deren Zertifikat in der Zulassungsliste des Anwendungsgateways enthalten ist. In Bezug auf die verwendete Version von Application Gateway gibt es einige Unterschiede beim Prozess zum Einrichten von End-to-End-TLS. Im folgenden Abschnitt werden die einzelnen Vorgänge erläutert.

## <a name="end-to-end-tls-with-the-v1-sku"></a>End-to-End-TLS mit der v1-SKU

Zum Aktivieren von End-to-End-TLS mit den Back-End-Servern und zum Weiterleiten von Anforderungen durch Application Gateway an die Back-End-Server müssen die Integritätstests erfolgreich durchgeführt werden und eine fehlerfreie Antwort zurückgeben.

Bei HTTPS-Integritätstests verwendet die Application Gateway v1-SKU eine genaue Entsprechung des Authentifizierungszertifikats (öffentlicher Schlüssel des Back-End-Serverzertifikats anstelle des Stammzertifikats), das in die HTTP-Einstellungen hochgeladen werden soll.

Es sind dann nur Verbindungen mit bekannten und zulässigen Back-Ends gestattet. Die übrigen Back-End-Server werden in den Integritätstests als fehlerhaft eingestuft. Selbstsignierte Zertifikate dienen nur zu Testzwecken und werden für Produktionsworkloads nicht empfohlen. Solche Zertifikate müssen, wie unter den obigen Schritten beschrieben, in der Zulassungsliste des Anwendungsgateways enthalten sein, damit sie verwendet werden können.

> [!NOTE]
> Die Einrichtung des Authentifizierungszertifikats und des vertrauenswürdigen Stammzertifikats ist für vertrauenswürdige Azure-Dienste wie Azure App Service nicht erforderlich. Diese Zertifikate werden standardmäßig als vertrauenswürdig eingestuft.

## <a name="end-to-end-tls-with-the-v2-sku"></a>End-to-End-TLS mit der v2-SKU

Authentifizierungszertifikate wurden als veraltet markiert und durch Trusted Root-Zertifikate in der v2-SKU von Application Gateway ersetzt. Sie funktionieren auf ähnliche Weise wie Authentifizierungszertifikate mit einigen wichtigen Unterschieden:

- Zertifikate, die von bekannten Zertifizierungsstellen, deren CN dem Hostnamen in den HTTP-Einstellungen des Back-Ends entspricht, zertifiziert wurden, erfordern keinen zusätzlichen Schritt, damit End-to-End-TLS funktioniert. 

   Wenn beispielsweise die Back-End-Zertifikate von einer bekannten Zertifizierungsstelle ausgestellt wurden, der CN „contoso.com“ lautet und in der HTTP-Einstellung des Back-Ends das Feld „Host“ ebenfalls auf „contoso.com“ festgelegt wurde, sind keine weiteren Schritte erforderlich. Sie können das Protokoll in der HTTP-Einstellung des Back-Ends auf HTTPS festlegen. In diesem Fall ist TLS sowohl für den Integritätstest als auch für den Datenpfad aktiviert. Wenn Sie Azure App Service oder andere Azure-Webdienste als Back-End verwenden, gelten diese ebenfalls als implizit vertrauenswürdig, und für End-to-End-TLS sind keine weiteren Schritte erforderlich.
   
> [!NOTE] 
>
> Damit ein TLS/SSL-Zertifikat als vertrauenswürdig eingestuft wird, muss dieses Zertifikat des Back-End-Servers von einer bekannten Zertifizierungsstelle ausgestellt worden sein. Wenn das Zertifikat nicht von einer vertrauenswürdigen Zertifizierungsstelle ausgestellt wurde, überprüft das Anwendungsgateway, ob das Zertifikat der ausstellenden Zertifizierungsstelle von einer vertrauenswürdigen Zertifizierungsstelle stammt. Dieser Vorgang wird so lange fortgesetzt, bis entweder eine vertrauenswürdige Zertifizierungsstelle gefunden wurde (woraufhin eine vertrauenswürdige, sichere Verbindung hergestellt wird) oder keine vertrauenswürdige Zertifizierungsstelle gefunden werden kann (woraufhin das Back-End vom Anwendungsgateway als fehlerhaft markiert wird). Das Back-End-Serverzertifikat sollte daher sowohl die Stammzertifizierungsstelle als auch die Zwischenzertifizierungsstellen enthalten.

- Wenn das Zertifikat selbstsigniert ist oder von einem unbekannten Vermittler signiert wurde, müssen Sie zum Aktivieren von End-to-End-TLS in der v2-SKU ein vertrauenswürdiges Stammzertifikat definieren. Application Gateway kommuniziert nur mit Back-End-Servern, bei denen das Stammzertifikat des Serverzertifikats mit einem der vertrauenswürdigen Stammzertifikate in der Liste der HTTP-Einstellung für das Back-End übereinstimmt, das dem Pool zugeordnet ist.

- Zusätzlich zur Übereinstimmung des Stammzertifikats überprüft Application Gateway v2 auch, ob die Einstellung „Host“ in der HTTP-Einstellung des Back-Ends mit dem allgemeinen Namen (Common Name, CN) übereinstimmt, der vom TLS/SSL-Zertifikat des Back-End-Servers angegeben wird. Beim Herstellen einer TLS-Verbindung mit dem Back-End legt Application Gateway v2 die SNI-Erweiterung (Server Name Indication, Servernamensanzeige) auf den Host fest, der in der HTTP-Einstellung des Back-Ends angegeben wurde.

- Wenn das **Auswählen des Hostnamen aus dem Back-End-Ziel** anstelle des Felds „Host“ in der HTTP-Einstellung des Back-Ends ausgewählt wurde, wird der SNI-Header immer auf den FQDN des Back-End-Pools festgelegt, und der CN im TLS/SSL-Zertifikat des Back-End-Servers muss mit diesem FQDN übereinstimmen. Mitglieder des Back-End-Pools mit IP-Adressen werden in diesem Szenario nicht unterstützt.

- Das Stammzertifikat ist ein Base64-codiertes Stammzertifikat aus den Zertifikaten des Back-End-Servers.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>SNI-Unterschiede in der v1- und v2-SKU

Wie zuvor erwähnt, beendet Application Gateway den TLS-Datenverkehr vom Client beim Application Gateway-Listener (d. h. die Front-End-Verbindung), entschlüsselt den Datenverkehr, wendet die erforderlichen Regeln an, um den Back-End-Server zu ermitteln, an den die Anforderung weitergeleitet werden muss, und stellt eine neue TLS-Sitzung mit dem Back-End-Server her (die Back-End-Verbindung).

In den folgenden Tabellen werden die SNI-Unterschiede zwischen der v1-SKU und der v2-SKU hinsichtlich der Front-End- und der Back-End-Verbindung beschrieben.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Front-End-TLS-Verbindung (Client zu Application Gateway)

---
Szenario | v1 | V2 |
| --- | --- | --- |
| Der Client gibt den SNI-Header an, und alle Listener für mehrere Standorte sind mit dem Flag „Require SNI“ (SNI anfordern) aktiviert. | Gibt das entsprechende Zertifikat zurück. Wenn die Site nicht vorhanden ist (entsprechend server_name), wird die Verbindung zurückgesetzt. | Gibt das entsprechende Zertifikat zurück, falls verfügbar. Andernfalls wird das Zertifikat des ersten konfigurierten HTTPS-Listeners (in angegebener Reihenfolge) zurückgegeben.|
| Der Client gibt keinen SNI-Header an, und alle Header für mehrere Standorte sind mit „Require SNI“ (SNI anfordern) aktiviert: | Setzt die Verbindung zurück | Gibt das Zertifikat des ersten konfigurierten HTTPS-Listeners (in angegebener Reihenfolge) zurück
| Der Client gibt keinen SNI-Header an, und ein mit einem Zertifikat konfigurierter Basislistener ist vorhanden: | Gibt das im Basislistener konfigurierte Zertifikat (Standard- oder Fallbackzertifikat) an den Client zurück | Gibt das Zertifikat des ersten konfigurierten HTTPS-Listeners (in angegebener Reihenfolge) zurück |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Back-End-TLS-Verbindung (Application Gateway zum Back-End-Server)

#### <a name="for-probe-traffic"></a>Beim Testdatenverkehr

---
Szenario | v1 | V2 |
| --- | --- | --- |
| SNI-Header (server_name) während des TLS-Handshakes als FQDN | Wird als FQDN über den Back-End-Pool festgelegt. Gemäß [RFC 6066](https://tools.ietf.org/html/rfc6066) sind IPv4- und IPv6-Literaladressen im SNI-Hostnamen nicht zulässig. <br> **Hinweis:** FQDN im Back-End-Pool sollte eine DNS-Auflösung in die IP-Adresse (öffentlich oder privat) des Back-End-Servers durchführen. | Der SNI-Header (server_name) wird als Hostname aus dem benutzerdefinierten Test festgelegt, der den HTTP-Einstellungen angefügt ist (sofern vorhanden), andernfalls aus dem in den HTTP-Einstellungen angegebenen Hostnamen, oder andernfalls aus dem im Back-End-Pool angegebenen FQDN. Dabei gilt folgende Rangfolge: benutzerdefinierter Test > HTTP-Einstellungen > Back-End-Pool. <br> **Hinweis:** Wenn die in den HTTP-Einstellungen und dem benutzerdefinierten Test konfigurierten Hostnamen sich unterscheiden, wird die SNI entsprechend der Rangfolge als Hostname aus dem benutzerdefinierten Test festgelegt.
| Die Adresse des Back-End-Pools ist eine IP-Adresse (v1), oder der Hostname des benutzerdefinierten Tests ist als IP-Adresse konfiguriert (v2). | SNI (server_name) wird nicht festgelegt. <br> **Hinweis:** In diesem Fall sollte der Back-End-Server ein Standard- oder Fallbackzertifikat zurückgeben können. Dieses sollte in den HTTP-Einstellungen unter dem Authentifizierungszertifikat in der Zulassungsliste enthalten sein. Wenn auf dem Back-End-Server kein Standard- oder Fallbackzertifikat konfiguriert ist und die SNI erwartet wird, setzt der Server die Verbindung möglicherweise zurück, was zu Testfehlern führt. | Wenn die IP-Adresse als Hostname festgelegt ist, wird die SNI in der zuvor genannten Rangfolge gemäß [RFC 6066](https://tools.ietf.org/html/rfc6066) nicht festgelegt. <br> **Hinweis:** Die SNI wird außerdem in v2-Tests nicht festgelegt, wenn kein benutzerdefinierter Test konfiguriert und in den HTTP-Einstellungen oder dem Back-End-Pool kein Hostname festgelegt ist. |

> [!NOTE] 
> Wenn kein benutzerdefinierter Test konfiguriert ist, sendet Application Gateway einen Standardtest im Format \<protocol\>://127.0.0.1:\<port\>/. Beispielsweise wird https://127.0.0.1:443/ als HTTPS-Standardtest gesendet. Beachten Sie, dass „127.0.0.1“ nur als HTTP-Host-Header und gemäß RFC 6066 nicht als SNI-Header verwendet wird. Weitere Informationen zu Fehlern bei Integritätstests finden Sie in der Anleitung zum [Behandeln von Problemen mit der Back-End-Integrität](application-gateway-backend-health-troubleshooting.md).

#### <a name="for-live-traffic"></a>Beim Livedatenverkehr

---
Szenario | v1 | V2 |
| --- | --- | --- |
| SNI-Header (server_name) während des TLS-Handshakes als FQDN | Wird als FQDN über den Back-End-Pool festgelegt. Gemäß [RFC 6066](https://tools.ietf.org/html/rfc6066) sind IPv4- und IPv6-Literaladressen im SNI-Hostnamen nicht zulässig. <br> **Hinweis:** FQDN im Back-End-Pool sollte eine DNS-Auflösung in die IP-Adresse (öffentlich oder privat) des Back-End-Servers durchführen. | Der SNI-Header (server_name) wird als Hostname aus den HTTP-Einstellungen festgelegt. Wenn andernfalls die Option *PickHostnameFromBackendAddress* ausgewählt ist oder kein Hostname angegeben ist, wird er als FQDN in der Konfiguration des Back-End-Pools festgelegt.
| Die Adresse des Back-End-Pools ist eine IP-Adresse, oder der Hostname ist in den HTTP-Einstellungen nicht festgelegt. | Die SNI wird gemäß [RFC 6066](https://tools.ietf.org/html/rfc6066) nicht festgelegt, wenn der Eintrag des Back-End-Pools kein FQDN ist. | Die SNI wird als Hostname aus dem Eingabe-FQDN des Clients festgelegt, und der allgemeine Name (CN) des Back-End-Zertifikats muss mit diesem Hostnamen identisch sein.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich über End-to-End-TLS informiert haben, fahren Sie mit dem [Konfigurieren von End-to-End-TLS mit Application Gateway und PowerShell](application-gateway-end-to-end-ssl-powershell.md) fort, um ein Anwendungsgateway mit End-to-End-TLS zu erstellen.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png