---
title: Aktivieren von End-to-End-SSL in Azure Application Gateway
description: Dieser Artikel enthält eine Übersicht über die Unterstützung von End-to-End-SSL in Application Gateway.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ee901fdcae9717cc6d03d7653bcaacc0c32518e0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254319"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Übersicht über SSL-Beendigung und End-to-End-SSL mit Application Gateway

Secure Sockets Layer (SSL) ist die standardmäßige Sicherheitstechnologie, mit der eine verschlüsselte Verbindung zwischen einem Webserver und einem Browser hergestellt wird. Über diese Verbindung wird sichergestellt, dass alle Daten privat und verschlüsselt zwischen dem Webserver und dem Browser übertragen werden. Application Gateway unterstützt sowohl die SSL-Beendigung am Gateway als auch die End-to-End-SSL-Verschlüsselung.

## <a name="ssl-termination"></a>SSL-Beendigung

Application Gateway unterstützt die SSL-Beendigung am Gateway, wonach der Datenverkehr in der Regel unverschlüsselt zu den Back-End-Servern gelangt. Die SSL-Beendigung am Application Gateway bietet zahlreiche Vorteile:

- **Verbesserte Leistung:** Der größte Leistungsabfall bei der SSL-Entschlüsselung entsteht beim anfänglichen Handshake. Der Entschlüsselungsserver verbessert die Leistung durch das Zwischenspeichern von SSL-Sitzungs-IDs und das Verwalten von TLS-Sitzungstickets. Wenn dieser Vorgang am Application Gateway erfolgt, können alle Anforderungen vom gleichen Client die zwischengespeicherten Werte verwenden. Wenn der Vorgang auf den Back-End-Servern erfolgt, muss der Client jedes Mal eine neue Authentifizierung vornehmen, wenn die Clientanforderungen an einen anderen Server gerichtet werden. Sie können dieses Problem durch die Verwendung von TLS-Tickets beheben, diese werden jedoch nicht von allen Clients unterstützt und können das Konfigurieren und Verwalten erschweren.
- **Bessere Auslastung der Back-End-Server:** Die SSL/TLS-Verarbeitung bringt eine hohe CPU-Intensität mit sich, die sich mit steigenden Schlüsselgrößen weiter verstärkt. Wenn Sie die Back-End-Server von dieser Aufgabe befreien, können diese für ihren effizientesten Zweck eingesetzt werden: das Bereitstellen von Inhalten.
- **Intelligentes Routing:** Durch die Entschlüsselung des Datenverkehrs erhält das Anwendungsgateway Zugriff auf Anforderungsinhalte wie Header, URI etc. und kann diese Daten dann zum Weiterleiten von Anforderungen verwenden.
- **Zertifikatverwaltung:** Zertifikate müssen nicht für alle Back-End-Server, sondern nur für das Anwendungsgateway erworben und installiert werden. Das spart Zeit und Geld.

Zum Konfigurieren der SSL-Beendigung muss dem Listener ein SSL-Zertifikat hinzugefügt werden, damit das Anwendungsgateway einen symmetrischen Schlüssel gemäß der Spezifikation des SSL-Protokolls ableiten kann. Der symmetrische Schlüssel wird dann zum Verschlüsseln und Entschlüsseln des Datenverkehrs mit dem Gateway verwendet. Das SSL-Zertifikat muss im PFX-Format (privater Informationsaustausch) vorliegen. In diesem Dateiformat können Sie den privaten Schlüssel exportieren. Das ist erforderlich, damit das Anwendungsgateway die Ver- und Entschlüsselung des Datenverkehrs durchführen kann.

> [!NOTE] 
>
> Das Anwendungsgateway bietet keine Funktionen zum Erstellen neuer Zertifikate oder Senden von Zertifikatanforderungen an Zertifizierungsstellen.

Zum Hersteller der SSL-Verbindung müssen Sie sicherstellen, dass das SSL-Zertifikat die folgenden Bedingungen erfüllt:

- Das aktuelle Datum und die aktuelle Uhrzeit entsprechen den Datumsbereichen „Gültig ab“ und „Gültig bis“ im Zertifikat.
- Der allgemeine Name (Common Name, CN) des Zertifikats entspricht dem Hostheader in der Anforderung. Wenn der Client eine Anforderung beispielsweise an `https://www.contoso.com/` richtet, muss der CN `www.contoso.com` lauten.

### <a name="certificates-supported-for-ssl-termination"></a>Unterstützte Zertifikate für die SSL-Beendigung

Das Anwendungsgateway unterstützt die folgenden Arten von Zertifikaten:

- CA-Zertifikat: Ein CA-Zertifikat ist ein digitales Zertifikat, das von einer Zertifizierungsstelle (Certificate Authority, CA) ausgestellt wurde.
- EV-Zertifikat: Ein EV-Zertifikat (Extended Validation) ist ein Zertifikat mit erweiterter Überprüfung gemäß Branchenrichtlinien. Die Locator-Leiste des Browsers wird dadurch grün, und auch der Firmennamen wird veröffentlicht.
- Platzhalterzertifikat: Dieses Zertifikat unterstützt eine beliebige Anzahl von untergeordneten Domänen basierend auf *.site.com. * wird durch Ihre untergeordnete Domäne ersetzt. Es unterstützt jedoch nicht site.com. Falls Ihre die auf Benutzer Ihre Website zugreifen, ohne die führende Zeichenfolge „www“ einzugeben, wird das Platzhalterzertifikat diese Eingabe nicht berücksichtigen.
- Selbstsignierte Zertifikate: Clientbrowser vertrauen diesen Zertifikaten nicht und warnen den Benutzer, dass das virtuelle Zertifikat des Diensts keiner Vertrauenskette angehört. Selbstsignierte Zertifikate eignen sich gut für Tests oder Umgebungen, in denen Administratoren die Clients kontrollieren und Sicherheitswarnungen des Browsers sicher umgehen können. Produktionsworkloads sollten niemals selbstsignierte Zertifikate verwenden.

Weitere Informationen finden Sie im Artikel zum [Konfigurieren der SSL-Beendigung mit Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Größe des Zertifikats
Lesen Sie den Abschnitt [Application Gateway-Grenzwerte](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits) durch, um die maximal unterstützte Größe von SSL-Zertifikaten zu erfahren.

## <a name="end-to-end-ssl-encryption"></a>End-to-End-SSL-Verschlüsselung

Einige Kunden wünschen möglicherweise keine unverschlüsselte Kommunikation mit den Back-End-Servern. Dies kann beispielsweise der Fall sein, wenn Sicherheits- oder Complianceanforderungen erfüllt werden müssen oder die Anwendung nur eine sichere Verbindung akzeptiert. Für Anwendungen dieser Art unterstützt Application Gateway die End-to-End-SSL-Verschlüsselung.

End-to-End-SSL ermöglicht die sichere, verschlüsselte Übertragung sensibler Daten an das Back-End und nutzt gleichzeitig die Vorteile der von Application Gateway bereitgestellten Lastenausgleichsfeatures (Schicht 7). Zu diesen Features zählen unter anderem cookiebasierte Sitzungsaffinität, URL-basiertes Routing, Unterstützung von standortbasiertem Routing und die Möglichkeit zum Einfügen von X-Forwarded-*-Headern.

Bei der Konfiguration mit dem End-to-End-SSL-Kommunikationsmodus beendet Application Gateway die SSL-Sitzungen am Gateway und entschlüsselt den Benutzerdatenverkehr. Anschließend werden die konfigurierten Regeln angewendet, um eine geeignete Instanz des Back-End-Pools auszuwählen, an die der Datenverkehr geroutet werden soll. Application Gateway initiiert anschließend eine neue SSL-Verbindung mit dem Back-End-Server und verschlüsselt die Daten mit dem öffentlichen Schlüssel des Back-End-Serverzertifikats erneut, bevor die Anforderung an das Back-End übertragen wird. Antworten vom Webserver durchlaufen denselben Prozess zurück an den Endbenutzer. End-to-End-SSL wird aktiviert, indem Sie die Protokolleinstellung in der [Back-End-HTTP-Einstellung](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) auf „HTTPS“ festlegen. Diese Einstellung wird anschließend auf einen Back-End-Pool angewendet.

Die SSL-Richtlinie gilt für sowohl Front-End- als auch Back-End-Datenverkehr. Auf dem Front-End fungiert Application Gateway als Server und erzwingt die Richtlinie. Auf dem Back-End fungiert Application Gateway als Client und sendet die Protokoll- bzw. Verschlüsselungsinformationen als bevorzugte Einstellung während SSL-Handshakes.

Application Gateway kommuniziert nur mit Back-End-Instanzen, die ihr Zertifikat mit Application Gateway in die Whitelist aufgenommen haben oder deren Zertifikate von bekannten Zertifizierungsstellen signiert wurden und deren CA-Name mit dem Hostnamen in der Back-End-HTTP-Einstellung übereinstimmt. Dazu zählen vertrauenswürdige Azure-Dienste wie Azure App Service-Web-Apps und Azure API Management.

Wenn die Zertifikate der Mitglieder im Back-End-Pool nicht von bekannten Zertifizierungsstellen signiert wurden, muss jede Instanz im Back-End-Pool mit aktiviertem End-to-End-SSL mit einem Zertifikat konfiguriert werden, um die sichere Kommunikation zu ermöglichen. Durch das Hinzufügen des Zertifikats wird sichergestellt, dass das Anwendungsgateway nur mit bekannten Back-End-Instanzen kommuniziert. Außerdem wird auf diese Weise die End-to-End-Kommunikation gesichert.

> [!NOTE] 
>
> Die Einrichtung des Authentifizierungszertifikats ist für vertrauenswürdige Azure-Dienste wie Azure App Service-Web-Apps und Azure API Management nicht erforderlich.

> [!NOTE] 
>
> Das Zertifikat, das der **Back-End-HTTP-Einstellung** zum Authentifizieren der Back-End-Server hinzugefügt wurde, kann mit dem Zertifikat übereinstimmen, das dem **Listener** zur SSL-Beendigung am Anwendungsgateway oder an einer anderen Instanz hinzugefügt wurde, um die Sicherheit zu verbessern.

![End-to-End-SSL-Szenario][1]

In diesem Beispiel werden Anforderungen, für die TLS 1.2 verwendet wird, an Back-End-Server in Pool1 geleitet, indem End-to-End-SSL genutzt wird.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>End-to-End-SSL und Whitelists für Zertifikate

Application Gateway kommuniziert nur mit bekannten Back-End-Instanzen, deren Zertifikat in der Whitelist des Anwendungsgateways enthalten ist. Um die Whitelistfunktion für Zertifikate zu aktivieren, müssen Sie den öffentlichen Schlüssel der Back-End-Serverzertifikate auf das Anwendungsgateway hochladen (nicht das Stammzertifikat). Es sind dann nur Verbindungen mit bekannten und in Whitelists enthaltenen Back-Ends zulässig. Die übrigen Back-Ends führen zu einem Gatewayfehler. Selbstsignierte Zertifikate dienen nur zu Testzwecken und werden für Produktionsworkloads nicht empfohlen. Solche Zertifikate müssen, wie unter den obigen Schritten beschrieben, in der Whitelist des Anwendungsgateways enthalten sein, damit sie verwendet werden können.

> [!NOTE]
> Die Einrichtung des Authentifizierungszertifikats ist für vertrauenswürdige Azure-Dienste wie Azure App Service nicht erforderlich.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>End-to-End-SSL mit der v2-SKU

Authentifizierungszertifikate wurden als veraltet markiert und durch Trusted Root-Zertifikate in der v2-SKU von Application Gateway ersetzt. Sie funktionieren auf ähnliche Weise wie Authentifizierungszertifikate mit einigen wichtigen Unterschieden:

- Zertifikate, die von bekannten Zertifizierungsstellen, deren CN dem Hostnamen in den HTTP-Einstellungen des Back-Ends entspricht, zertifiziert wurden, erfordern keinen zusätzlichen Schritt, damit End-to-End-SSL funktioniert. 

   Wenn beispielsweise die Back-End-Zertifikate von einer bekannten Zertifizierungsstelle ausgestellt wurden, der CN „contoso.com“ lautet und in der HTTP-Einstellung des Back-Ends das Feld „Host“ ebenfalls auf „contoso.com“ festgelegt wurde, sind keine weiteren Schritte erforderlich. Sie können das Protokoll in der HTTP-Einstellung des Back-Ends auf HTTPS festlegen. In diesem Fall ist SSL sowohl für den Integritätstest als auch für den Datenpfad aktiviert. Wenn Sie Azure App Service oder andere Azure-Webdienste als Back-End verwenden, gelten diese ebenfalls als implizit vertrauenswürdig, und für End-to-End-SSL sind keine weiteren Schritte erforderlich.
- Wenn das Zertifikat selbstsigniert ist oder von einem unbekannten Vermittler signiert wurde, müssen Sie zum Aktivieren von End-to-End-SSL in der v2-SKU ein vertrauenswürdiges Stammzertifikat definieren. Application Gateway kommuniziert nur mit Back-Ends, bei denen das Stammzertifikat des Serverzertifikats mit einem der vertrauenswürdigen Stammzertifikate in der Liste der HTTP-Einstellung für das Back-End, das dem Pool zugeordnet ist, übereinstimmt.
- Zusätzlich zur Übereinstimmung des Stammzertifikats überprüft Application Gateway auch, ob die Einstellung „Host“ in der HTTP-Einstellung des Back-Ends mit dem allgemeinen Namen (Common Name, CN), der vom SSL-Zertifikat des Back-End-Servers angegeben wird, übereinstimmt. Beim Herstellen einer SSL-Verbindung mit dem Back-End legt Application Gateway die SNI-Erweiterung (Server Name Indication, Servernamensanzeige) auf den Host fest, der in der HTTP-Einstellung des Back-Ends angegeben wurde.
- Wenn das **Auswählen des Hostnamen aus der Back-End-Adresse** anstelle des Felds „Host“ in der HTTP-Einstellung des Back-Ends ausgewählt wurde, wird der SNI-Header immer auf den FQDN des Back-End-Pools festgelegt, und der CN im SSL-Zertifikat des Back-End-Servers muss mit diesem FQDN übereinstimmen. Mitglieder des Back-End-Pools mit IP-Adressen werden in diesem Szenario nicht unterstützt.
- Das Stammzertifikat ist ein Base64-codiertes Stammzertifikat aus den Zertifikaten des Back-End-Servers.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich über End-to-End-SSL informiert haben, fahren Sie mit dem [Konfigurieren von End-to-End-SSL mit Application Gateway und PowerShell](application-gateway-end-to-end-ssl-powershell.md) fort, um ein Anwendungsgateway mit End-to-End-SSL zu erstellen.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
