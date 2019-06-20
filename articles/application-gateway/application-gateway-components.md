---
title: Application Gateway-Komponenten
description: Dieser Artikel bietet Informationen zu den verschiedenen Komponenten in einem Anwendungsgateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831832"
---
# <a name="application-gateway-components"></a>Application Gateway-Komponenten

 Ein Anwendungsgateway dient als zentraler Kontaktpunkt für Clients. Es verteilt eingehenden Anwendungsdatenverkehr über mehrere Back-End-Pools hinweg, z.B. virtuelle Azure-Computer, VM-Skalierungsgruppen, Azure App Service oder lokale bzw. externe Server. Zum Verteilen von Datenverkehr verwendet ein Anwendungsgateway verschiedene Komponenten, die in diesem Artikel beschrieben werden.

![In einem Anwendungsgateway verwendete Komponenten](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Front-End-IP-Adressen

Eine Front-End-IP-Adresse ist die IP-Adresse, die einem Anwendungsgateway zugeordnet ist. Sie können ein Anwendungsgateway mit einer öffentlichen IP-Adresse, mit einer privaten IP-Adresse oder mit beidem konfigurieren. Ein Anwendungsgateway unterstützt eine öffentliche oder eine private IP-Adresse. Ihr virtuelles Netzwerk und die öffentliche IP-Adresse müssen sich am gleichen Standort befinden wie Ihr Anwendungsgateway. Eine Front-End-IP-Adresse wird nach dem Erstellen einem Listener zugeordnet.

### <a name="static-versus-dynamic-public-ip-address"></a>Statische und dynamische öffentliche IP-Adresse im Vergleich

Die v2-SKU von Azure Application Gateway unterstützt sowohl statische interne als auch statische öffentliche IP-Adressen, während die v1-SKU nur statische interne IP-Adressen unterstützt. Die VIP-Adresse (virtuelle IP-Adresse) kann sich ändern, wenn ein Anwendungsgateway beendet und gestartet wird.

Der einem Anwendungsgateway zugeordnete DNS-Name ändert sich während des Lebenszyklus des Gateways nicht. Daher wird empfohlen, einen CNAME-Alias zu verwenden und damit auf die DNS-Adresse des Anwendungsgateways zu verweisen.

## <a name="listeners"></a>Listener

Ein Listener ist eine logische Entität, die auf eingehende Verbindungsanforderungen prüft. Ein Listener akzeptiert eine Anforderung, wenn das Protokoll, der Port, der Host und die IP-Adresse mit den entsprechenden Elementen der Listenerkonfiguration übereinstimmen.

Bevor Sie ein Anwendungsgateway verwenden, müssen Sie mindestens einen Listener hinzufügen. An ein Anwendungsgateway können mehrere Listener angefügt sein, und sie können für dasselbe Protokoll verwendet werden.

Wenn ein Listener eingehende Anforderungen von Clients erkannt hat, leitet das Anwendungsgateway diese Anforderungen an Mitglieder im Back-End-Pool weiter. Das Anwendungsgateway verwendet Routingregeln für Anforderungen, die für den Listener definiert sind, der die eingehende Anforderung empfangen hat.

Listener unterstützen die folgenden Ports und Protokolle.

### <a name="ports"></a>Ports

Ein Port wird von einem Listener auf die Clientanforderung überwacht. Sie können Ports von 1 bis 65502 für die v1-SKU und 1 bis 65199 für die v2-SKU konfigurieren.

### <a name="protocols"></a>Protokolle

Application Gateway unterstützt vier Protokolle: HTTP, HTTPS, HTTP/2 und WebSocket:

- Sie geben die Option für HTTP- und HTTPS-Protokolle in der Listenerkonfiguration an.
- Unterstützung für [die Protokolle WebSocket und HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) wird systemintern bereitgestellt, und [WebSocket-Unterstützung](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) ist standardmäßig aktiviert. Die WebSocket-Unterstützung kann von Benutzern nicht selektiv aktiviert oder deaktiviert werden. Sie können das WebSocket-Protokoll sowohl mit HTTP- als auch mit HTTPS-Listenern verwenden.
- Die Unterstützung des HTTP/2-Protokolls ist nur für Clients verfügbar, die mit Application Gateway-Listenern verbunden sind. Die Kommunikation mit Back-End-Serverpools erfolgt über HTTP/1.1. Die HTTP/2-Unterstützung ist standardmäßig deaktiviert. Sie können es aktivieren.

Verwenden Sie einen HTTPS-Listener für die SSL-Terminierung. Ein HTTPS-Listener lagert die Ver- und Entschlüsselung an Ihr Anwendungsgateway aus, sodass Ihre Webserver mit diesem Overhead nicht belastet werden. Ihre Apps können sich ganz auf die Geschäftslogik konzentrieren.

### <a name="custom-error-pages"></a>Benutzerdefinierte Fehlerseiten

Mit Application Gateway können Sie benutzerdefinierte Fehlerseiten erstellen, anstatt Standardfehlerseiten anzuzeigen. Sie können für eine benutzerdefinierte Fehlerseite Ihr eigenes Branding und Layout verwenden. Application Gateway zeigt eine benutzerdefinierte Fehlerseite an, wenn eine Anforderung das Back-End nicht erreichen kann.

Weitere Informationen finden Sie unter [Benutzerdefinierte Application Gateway-Fehlerseiten](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Arten von Listenern

Es gibt zwei Arten von Listenern:

- **Basic**. Diese Listener lauschen an einer einzelnen Domänenwebsite, wo eine einzelne DNS-Zuordnung zur IP-Adresse des Anwendungsgateways vorliegt. Diese Listenerkonfiguration ist erforderlich, wenn Sie eine einzelne Website hinter einem Anwendungsgateway hosten.

- **Mehrere Websites**. Diese Listenerkonfiguration ist erforderlich, wenn Sie mehrere Webanwendungen in der gleichen Anwendungsgateway-Instanz konfigurieren. Sie können eine effizientere Topologie für Ihre Bereitstellungen konfigurieren, indem Sie bis zu 100 Websites zu einem einzigen Anwendungsgateway hinzufügen. Jede Website kann an ihren eigenen Back-End-Pool weitergeleitet werden. Beispiel: Drei Unterdomänen („abc.contoso.com“, „xyz.contoso.com“ und „pqr.contoso.com“) zeigen auf die IP-Adresse des Anwendungsgateways. Sie erstellen drei Listener vom Typ „Mehrere Websites“ und konfigurieren jeden Listener für den jeweiligen Port und die jeweilige Protokolleinstellung.

    Weitere Informationen finden Sie unter [Hosten von mehreren Websites](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Nachdem Sie einen Listener erstellt haben, ordnen Sie ihm eine Anforderungsroutingregel zu. Diese Regel bestimmt, wie die vom Listener empfangene Anforderung an das Back-End weitergeleitet werden soll.

Application Gateway verarbeitet Listener in der gezeigten Reihenfolge. Wenn ein einfacher Listener zu einer eingehenden Anforderung passt, wird diese zuerst verarbeitet. Konfigurieren Sie einen Listener für mehrere Websites vor einem einfachen Listener, Datenverkehr an das richtige Back-End weiterzuleiten.

## <a name="request-routing-rules"></a>Anforderungsroutingregeln

Eine Anforderungsroutingregel ist eine wichtige Komponente eines Anwendungsgateways, da sie bestimmt, wie Datenverkehr im Listener weitergeleitet wird. Diese Regel bindet den Listener, den Back-End-Serverpool und die Back-End-HTTP-Einstellungen.

Wenn ein Listener eine Anforderung akzeptiert, leitet die Anforderungsroutingregel die Anwendung an das Back-End weiter oder an einen anderen Ort um. Wenn die Anforderung an das Back-End weitergeleitet wird, definiert die Anforderungsroutingregel, an welchen Back-End-Serverpool die Weiterleitung erfolgen soll. Zudem legt die Anforderungsroutingregel fest, ob die Header in der Anforderung neu geschrieben werden sollen. Ein Listener kann mit genau einer Regel verknüpft werden.

Es gibt zwei Arten von Anforderungsroutingregeln:

- **Basic**. Alle Anforderungen für den zugeordneten Listener (Beispiel: blog.contoso.com/*) werden mit der zugeordneten HTTP-Einstellung an den zugeordneten Back-End-Pool weitergeleitet.

- **Pfadbasiert**. Mit einer solchen Routingregel können Sie Anforderungen für den zugeordneten Listener basierend auf der URL in der Anforderung an einen bestimmten Back-End-Pool weiterleiten. Wenn der Pfad der URL in einer Anforderung dem Pfadmuster in einer pfadbasierten Regel entspricht, wird die Anforderung anhand dieser Regel geroutet. Das Pfadmuster wird nur auf den URL-Pfad angewendet, nicht auf die zugehörigen Abfrageparameter. Wenn der URL-Pfad einer Anforderung in einem Listener keiner pfadbasierten Regel entspricht, wird die Anforderung mit den HTTP-Standardeinstellungen an den standardmäßigen Back-End-Pool weitergeleitet.

Weitere Informationen finden Sie unter [URL-basiertes Routing](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Umleitungsunterstützung

Die Routingregel für Anforderungen ermöglicht es Ihnen auch, Datenverkehr im Anwendungsgateway umzuleiten. Vielmehr handelt es sich um einen generischen Umleitungsmechanismus, sodass Sie Umleitungen für jeden Port durchführen können, den Sie mithilfe von Regeln definieren.

Sie können als Umleitungsziel einen anderen Listener (wodurch die automatische Umleitung von HTTP zu HTTPS ermöglicht werden kann) oder eine externe Website auswählen. Sie können außerdem auswählen, ob die Umleitung temporär oder dauerhaft erfolgen soll, oder angeben, dass der URI-Pfad und die Abfragezeichenfolge an die Umleitungs-URL angefügt werden sollen.

Weitere Informationen finden Sie unter [Umleiten von Datenverkehr im Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>Erneutes Generieren von HTTP-Headern

Mit Anwendungsroutingregeln können Sie HTTP(S)-Anforderungs- und -Antwortheader hinzufügen, entfernen oder aktualisieren, während die Anforderungs- und Antwortpakete über das Anwendungsgateway zwischen dem Client und den Back-End-Pools weitergeleitet werden.

Die Header können auf statische Werte und auf andere Header und Servervariablen festgelegt werden. Dies ist hilfreich bei wichtigen Anwendungsfällen, z.B. beim Extrahieren von Client-IP-Adressen, beim Entfernen vertraulicher Informationen zum Back-End, beim Verbessern der Sicherheit usw.

Weitere Informationen finden Sie unter [Erneutes Generieren von HTTP-Headern im Application Gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>HTTP-Einstellungen

Ein Anwendungsgateway routet Datenverkehr an die Back-End-Server (diese sind in der Anforderungsroutingregel angegeben, die HTTP-Einstellungen einschließt). Dabei werden Portnummer, Protokoll sowie weitere in dieser Komponente angegebene Einstellungen verwendet.

Die Angaben für Port und Protokoll in den HTTP-Einstellungen bestimmen, ob der Datenverkehr zwischen dem Anwendungsgateway und den Back-End-Servern verschlüsselt wird (um End-to-End-SSL zu erzielen) oder ob er unverschlüsselt bleibt.

Diese Komponente wird außerdem für folgende Zwecke verwendet:

- Bestimmen, ob eine Benutzersitzung durch [cookiebasierte Sitzungsaffinität](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) auf demselben Server beibehalten wird.

- Ordnungsgemäßes Entfernen von Mitgliedern des Back-End-Pools über den [Verbindungsausgleich](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Zuordnen eines benutzerdefinierten Tests zum Überwachen der Integrität des Back-Ends, Überschreiben von Hostname und -pfad in der Anforderung und benutzerfreundliches Angeben von Einstellungen für das App Service-Back-End mit nur einem Klick.

## <a name="backend-pools"></a>Back-End-Pools

Ein Back-End-Adresspool leitet die Anforderung an Back-End-Server weiter, die sie verarbeiten. Back-End-Pools können Folgendes umfassen:

- NICs
- VM-Skalierungsgruppen
- Öffentliche IP-Adressen
- Interne IP-Adressen
- FQDN
- Mehrinstanzenfähige Back-Ends (z.B. App Service)

Mitglieder des Application Gateway-Back-End-Pools sind nicht an eine Verfügbarkeitsgruppe gebunden. Ein Anwendungsgateway kann mit Instanzen außerhalb des eigenen virtuellen Netzwerks kommunizieren. Mitglieder von Back-End-Pools können auf mehrere Cluster und Rechenzentren verteilt sein oder sich außerhalb von Azure befinden, sofern sie über IP-Konnektivität verfügen.

Wenn Sie interne IP-Adressen als Back-End-Pool-Mitglieder verwenden, ist das [Peering virtueller Netzwerke](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) oder ein [VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) erforderlich. Das Peering virtueller Netzwerke wird unterstützt und bietet Vorteile für den Lastenausgleich von Datenverkehr in anderen virtuellen Netzwerken.

Ein Anwendungsgateway kann auch mit lokalen Servern kommunizieren, wenn diese über Azure ExpressRoute oder VPN-Tunnel verbunden sind und Datenverkehr zulässig ist.

Sie können verschiedene Back-End-Pools für verschiedene Arten von Anforderungen erstellen. Sie können z.B. einen Back-End-Pool für allgemeine Anforderungen und einen anderen für Anforderungen an die Microservices Ihrer Anwendung erstellen.

## <a name="health-probes"></a>Integritätstests

In der Standardeinstellung überwacht ein Anwendungsgateway die Integrität aller Ressourcen in seinem Back-End-Pool und entfernt automatisch alle fehlerhaften Ressourcen. Es überwacht die fehlerhaften Instanzen weiterhin und fügt sie dem fehlerfreien Back-End-Pool hinzu, sobald sie verfügbar sind und auf Integritätsüberprüfungen reagieren.

Zusätzlich zur Nutzung der standardmäßigen Überwachung der Integritätsüberprüfung können Sie die Integritätsüberprüfung auch an die Anforderungen Ihrer Anwendung anpassen. Benutzerdefinierte Überprüfungen ermöglichen Ihnen eine präzisere Kontrolle über die Überwachung des Systemzustands. Bei Verwendung von benutzerdefinierten Überprüfungen können Sie das Überprüfungsintervall, die URL und den zu überprüfenden Pfad konfigurieren und festlegen, wie viele fehlerhafte Antworten akzeptiert werden, bevor die Back-End-Pool-Instanz als fehlerhaft gekennzeichnet wird. Wir empfehlen, benutzerdefinierte Überprüfungen zu konfigurieren, um die Integrität jedes Back-End-Pools zu überwachen.

Weitere Informationen finden Sie unter [Überwachen der Integrität Ihres Anwendungsgateways](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Nächste Schritte

Erstellen eines Anwendungsgateways

* [Im Azure-Portal](quick-create-portal.md)
* [Mit Azure PowerShell](quick-create-powershell.md)
* [Mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI)](quick-create-cli.md)
