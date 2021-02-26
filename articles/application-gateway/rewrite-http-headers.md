---
title: Erneutes Generieren von HTTP-Headern mit Azure Application Gateway | Microsoft-Dokumentation
description: In diesem Artikel erhalten Sie eine Übersicht über das erneute Generieren von HTTP-Headern in Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: 7c5b4f0d5d4b153684683963c56b7506e76d963e
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575652"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Erneutes Generieren von HTTP-Headern mit Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP-Header ermöglichen einem Client und Server das Übergeben von zusätzlichen Informationen mit einer Anforderung oder Antwort. Durch das erneute Generieren dieser HTTP-Header können Sie verschiedene wichtige Aufgaben umsetzen, z.B. das Hinzufügen von sicherheitsbezogenen Headerfeldern wie HSTS/X-XSS-Protection, das Entfernen von Antwortheaderfeldern, über die sensible Informationen offengelegt werden können, und das Entfernen von Portinformationen aus X-Forwarded-For-Headern.

Application Gateway ermöglicht Ihnen das Hinzufügen, Entfernen oder Aktualisieren von HTTP-Anforderungs- und -Antwortheadern, während die Anforderung/Antwort-Pakete zwischen dem Client und den Back-End-Pools verschoben werden. Außerdem können Sie Bedingungen hinzufügen, um sicherzustellen, dass die angegebenen Header nur dann neu generiert werden, wenn bestimmte Bedingungen erfüllt sind.

Application Gateway unterstützt auch mehrere [Servervariablen](#server-variables), mit denen Sie weitere Informationen zu Anforderungen und Antworten speichern können. Dies erleichtert Ihnen das Erstellen leistungsstarker Neuschreibungsregeln.

> [!NOTE]
>
> Die Unterstützung für das erneute Generieren von HTTP-Headern ist nur für [Standard_V2 und WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md) verfügbar.

![Erneutes Generieren von Headern](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Unterstützte Header

Sie können alle Header in Anforderungen und Antworten neu generieren, Host-, Verbindungs- und Upgradeheader ausgenommen. Sie können auch über das Anwendungsgateway benutzerdefinierte Header erstellen und den Anforderungen und Antworten hinzufügen, die über das Gateway weitergeleitet werden.

## <a name="rewrite-conditions"></a>Bedingungen für das erneute Generieren

Sie können mit Bedingungen für das erneute Generieren den Inhalt der HTTP(S)-Anforderungen und -Antworten auswerten, um nur dann eine Neugenerierung auszuführen, wenn eine oder mehrere Bedingungen erfüllt sind. Das Anwendungsgateway wertet mit diesen Typen von Variablen den Inhalt von HTTP(S)-Anforderungen und -Antworten aus:

- HTTP-Header in der Anforderung.
- HTTP-Header in der Antwort.
- Application Gateway-Servervariablen.

Mit einer Bedingung können Sie ermitteln, ob eine angegebene Variable vorhanden ist, ob eine angegebene Variable mit einem bestimmten Wert übereinstimmt oder ob eine angegebene Variable einem bestimmten Muster entspricht. Mit der [PCRE-Bibliothek (Perl Compatible Regular Expressions)](https://www.pcre.org/) richten Sie Muster für reguläre Ausdrücke ein, mit denen die Bedingungen übereinstimmen sollen. Weitere Informationen zur Syntax von regulären Ausdrücken finden Sie auf der [Perl-Mainpage für reguläre Ausdrücke](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Aktionen für das erneute Generieren

Mit Aktionen für das erneute Generieren geben Sie die Anforderungs- und Antwortheader an, die Sie erneut generieren möchten, sowie den neuen Wert für die Header. Sie können entweder einen neuen Header erstellen, den Wert eines vorhandenen Headers ändern oder einen vorhandenen Header löschen. Der Wert eines neuen Headers oder eines vorhandenen Headers kann auf die folgenden Typen von Werten festgelegt werden:

- Text.
- Anforderungsheader. Um einen Anforderungsheader festzulegen, müssen Sie die Syntax {http_req_ *headerName*} verwenden.
- Antwortheader. Um einen Antwortheader festzulegen, müssen Sie die Syntax {http_resp_ *headerName*} verwenden.
- Servervariable. Um eine Servervariable festzulegen, müssen Sie die Syntax {var_ *serverVariable*} verwenden.
- Eine Kombination aus Text, Anforderungsheader, Antwortheader und Servervariable.

## <a name="server-variables"></a>Servervariablen

Application Gateway speichert mit Servervariablen nützliche Informationen zum Server, zur Verbindung mit dem Client und zur momentanen Anforderung an die Verbindung. Beispiele für Informationen, die gespeichert werden, sind die IP-Adresse des Clients und der Webbrowsertyp. Servervariablen ändern sich dynamisch, wenn z.B. eine neue Seite geladen oder ein Formular gesendet wird. Anhand dieser Variablen können Sie Bedingungen für das erneute Generieren und Header für das erneute Generieren auswerten. Um den Wert von Servervariablen zum erneuten Generieren von Headern zu verwenden, müssen Sie diese Variablen in der Syntax {var_ *serverVariable*} angeben.

Application Gateway unterstützt diese Servervariablen:

| Variablenname | BESCHREIBUNG                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Das Clientanforderung-Headerfeld „X-Forwarded-For“ mit der Variablen `client_ip` (in dieser Tabelle unten erläutert), die im Format IP1, IP2, IP3 usw. angefügt ist. Ist das Feld „X-Forwarded-For“ im Header der Clientanforderung nicht vorhanden, ist die Variable `add_x_forwarded_for_proxy` gleich der Variablen `$client_ip`. Diese Variable ist besonders hilfreich, wenn Sie den X-Forwarded-For-Header, der von Application Gateway festgelegt wurde, neu generieren möchten, sodass der Header nur die IP-Adresse und keine Portinformationen enthält. |
| ciphers_supported          | Eine Liste der Verschlüsselungen, die vom Client unterstützt werden.          |
| ciphers_used               | Die Verschlüsselungszeichenfolge, die für eine eingerichtete TLS-Verbindung verwendet wird. |
| client_ip                  | Die IP-Adresse des Clients, von dem das Anwendungsgateway die Anforderung empfangen hat. Befindet sich ein Reverseproxy vor dem Anwendungsgateway und dem ursprünglichen Client, gibt *client_ip* die IP-Adresse des Reverseproxys zurück. |
| client_port                | Der Port des Clients.                                                  |
| client_tcp_rtt             | Informationen zur TCP-Verbindung des Clients. Verfügbar auf Systemen, die die TCP_INFO-Socketoption unterstützen. |
| client_user                | Wenn HTTP-Authentifizierung verwendet wird, der Benutzername, der bei der Authentifizierung angegeben wird. |
| host                       | In dieser Reihenfolge: Hostname aus der Anforderungszeile, Hostname aus dem Anforderungsheaderfeld „Host“ oder der Servername, der mit einer Anforderung übereinstimmt. Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` ist der Hostwert dann *contoso.com*. |
| cookie_ *Name*              | Das *name*-Cookie.                                            |
| http_method                | Die Methode, die für die URL-Anforderung verwendet wird. Beispielsweise GET oder POST. |
| http_status                | Der Sitzungsstatus. Beispielsweise 200, 400 oder 403.                       |
| http_version               | Das Anforderungsprotokoll. In der Regel HTTP/1.0, HTTP/1.1 oder HTTP/2.0. |
| query_string               | Die Liste der Variablen/Wert-Paare nach dem „?“ in der angeforderten URL. Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der Wert von „query_string“ *id=123&title=fabrikam*. |
| received_bytes             | Die Länge der Anforderung (einschließlich Anforderungszeile, Header und Anforderungstext). |
| request_query              | Die Argumente in der Anforderungszeile.                                |
| request_scheme             | Das Anforderungsschema: „http“ oder „https“.                            |
| request_uri                | Der vollständige ursprüngliche Anforderungs-URI (mit Argumenten). Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der Wert von „request_uri“ */article.aspx?id=123&title=fabrikam*.   |
| sent_bytes                 | Die Anzahl der an einen Client gesendeten Bytes.                             |
| server_port                | Der Port des Servers, der eine Anforderung akzeptiert hat.                 |
| ssl_connection_protocol    | Das Protokoll einer hergestellten TLS-Verbindung.        |
| ssl_enabled                | „Ein“, wenn die Verbindung im TLS-Modus ausgeführt wird. Andernfalls eine leere Zeichenfolge. |
| uri_path                   | Identifiziert die bestimmte Ressource auf dem Host, auf die der Webclient zugreifen möchte. Dies ist der Teil des Anforderungs-URI ohne die Argumente. Beispiel: In der Anforderung `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` lautet der Wert von „uri_path“ */article.aspx*.  |

## <a name="rewrite-configuration"></a>Konfiguration für das erneute Generieren

Um das erneute Generieren von HTTP-Headern zu konfigurieren, müssen Sie diese Schritte durchführen.

1. Erstellen Sie die Objekte, die zum erneuten Generieren von HTTP-Headern erforderlich sind:

   - **Aktion für das erneute Generieren**: Dient dazu, die Felder für Anforderung und Anforderungsheader, die Sie erneut generieren möchten, und den neuen Wert für die Header anzugeben. Sie können eine oder mehrere Bedingungen für das erneute Generieren mit einer Aktion für das erneute Generieren verknüpfen.

   - **Bedingung für das erneute Generieren**: Eine optionale Konfiguration. Bedingungen für das erneute Generieren werten den Inhalt von HTTP(S)-Anforderungen und -Antworten aus. Die Aktion für das erneute Generieren wird ausgeführt, wenn die HTTP(S)-Anforderung oder -Antwort die Bedingung für das erneute Generieren erfüllt.

     Wenn Sie der Aktion mehr als eine Bedingung zuordnen, erfolgt die Aktion nur, wenn alle Bedingungen erfüllt sind. Das heißt also, dass der Vorgang ein logischer UND-Vorgang ist.

   - **Regel zum erneuten Generieren**: Enthält mehrere Kombinationen aus Aktion und Bedingung für das erneute Generieren.

   - **Regelsequenz**: Hilft, die Reihenfolge zu bestimmen, in der die Regeln zum erneuten Generieren ausgeführt werden. Diese Konfiguration ist hilfreich, wenn Sie mehrere Regeln zum erneuten Generieren in einem Satz zum erneuten Generieren haben. Eine Regel zum erneuten Generieren, die eine niedrigere Regelsequenz besitzt, wird zuerst ausgeführt. Wenn Sie dieselbe Regelsequenz zwei Regeln zum erneuten Generieren zuweisen, ist die Reihenfolge der Ausführung unbestimmt.

   - **Satz zum erneuten Generieren**: Enthält mehrere Regeln zum erneuten Generieren, die einer Anforderungsroutingregel zugeordnet werden.

2. Fügen Sie den Satz zum erneuten Generieren (*rewriteRuleSet*) einer Routingregel an. Die Konfiguration für das erneute Generieren wird dem Quelllistener über die Routingregel angefügt. Bei Verwendung einer einfachen Routingregel wird die Konfiguration der erneuten Generierung eines Headers einem Quelllistener zugeordnet und fungiert als erneute Generierung eines globalen Headers. Wenn eine pfadbasierte Routingregel verwendet wird, wird die Konfiguration der erneuten Generierung eines Headers in der URL-Pfadzuordnung definiert. In diesem Fall gilt sie nur für den bestimmten Pfadbereich einer Site.
   > [!NOTE]
   > Beim erneuten Generieren der URL werden die Header geändert, die URL für den Pfad wird jedoch nicht geändert.

Sie können mehrere solche Sätze für das erneute Generieren eines HTTP-Headers erstellen, und jeder dieser Sätze kann auf mehrere Listener angewendet werden. Allerdings können Sie auf einen bestimmten Listener nur einen Satz für das erneute Generieren anwenden.

## <a name="common-scenarios"></a>Häufige Szenarios

Hier sind einige allgemeine Szenarien für das erneute Generieren von Headern.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Entfernen von Portinformationen aus dem X-Forwarded-For-Header

Application Gateway fügt in alle Anforderungen einen X-Forwarded-For-Header ein, bevor es die Anforderungen an das Back-End weiterleitet. Dieser Header ist eine durch Trennzeichen getrennte Liste von IP-Ports. Es gibt möglicherweise Szenarien, in denen Back-End-Server nur die Header benötigen, um IP-Adressen zu enthalten. Sie können mit dem erneuten Generieren von Headern die Portinformationen aus dem X-Forwarded-For-Header entfernen. Dies kann beispielsweise erreicht werden, indem der Header auf die Servervariable „add_x_forwarded_for_proxy“ festgelegt wird:

![Entfernen des Ports](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Ändern einer Umleitungs-URL

Wenn eine Back-End-Anwendung eine Umleitungsantwort sendet, empfiehlt es sich u.U., den Client an eine andere als die von der Back-End-Anwendung angegebene URL umzuleiten. Beispielsweise sollten Sie dies tun, wenn ein App-Dienst hinter einem Anwendungsgateway gehostet wird und erfordert, dass der Client eine Umleitung zu seinem relativen Pfad durchführt. (Beispielsweise eine Umleitung von „contoso.azurewebsites.net/path1“ zu „contoso.azurewebsites.net/path2“.)

Da App Service ein mehrinstanzenfähiger Dienst ist, nutzt er den Hostheader in der Anforderung zur Weiterleitung der Anforderung an den richtigen Endpunkt. App-Dienste weisen den Standarddomänennamen „*.azurewebsites.net“ auf (z. B. „contoso.azurewebsites.net“), der sich vom Domänennamen des Anwendungsgateways (z.B. „contoso.com“) unterscheidet. Da die ursprüngliche Anforderung vom Client den Domänennamen des Anwendungsgateways „contoso.com“ als Hostnamen aufweist, ändert das Anwendungsgateway nun den Hostnamen in „contoso.azurewebsites.net“. Diese Änderung wird vorgenommen, damit der App-Dienst die Anforderung an den richtigen Endpunkt weiterleiten kann.

Wenn der App Service eine Umleitungsantwort sendet, verwendet er den gleichen Hostnamen im Adressheader seiner Antwort wie in der Anforderung, die er vom Anwendungsgateway empfängt. So sendet der Client die Anforderung direkt an „contoso.azurewebsites.net/path2“ und durchläuft nicht das Anwendungsgateway („contoso.com/path2“). Das Umgehen des Anwendungsgateways ist nicht wünschenswert.

Sie können dieses Problem durch Festlegen des Hostnamens im Adressheader des Anwendungsgateway-Domänennamens lösen.

Hier sind die Schritte zum Ersetzen des Hostnamens:

1. Erstellen Sie eine Regel zum erneuten Generieren mit einer Bedingung, die prüft, ob der Adressheader in der Antwort „azurewebsites.net“ enthält. Geben Sie das Muster `(https?):\/\/.*azurewebsites\.net(.*)$` ein.
1. Führen Sie eine Aktion zum erneuten Generieren des Adressheaders durch, damit sie den Hostnamen des Anwendungsgateways hat. Geben Sie hierzu `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als Headerwert ein.

![Ändern des Adressheaders](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementieren von HTTP-Sicherheitsheadern, um Sicherheitsrisiken zu verhindern

Sie können verschiedene Sicherheitsrisiken beheben, indem Sie die erforderlichen Header in die Anwendungsantwort implementieren. Zu diesen Sicherheitsheadern zählen X-XSS-Protection, Strict-Transport-Security und Content-Security-Policy. Über Application Gateway können Sie diese Header für alle Antworten festlegen.

![Sicherheitsheader](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Löschen von unerwünschten Headern

Möglicherweise möchten Sie Header entfernen, die vertrauliche Informationen aus einer HTTP-Antwort anzeigen. Beispielsweise möchten Sie möglicherweise Informationen wie den Namen des Back-End-Servers, Betriebssystem oder Bibliothek entfernen. Sie können diese Header mit dem Anwendungsgateway entfernen:

![Löschen des Headers](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Überprüfen des Vorhandenseins eines Headers

Sie können einen HTTP-Anforderungs- oder -Antwortheader auf das Vorhandensein einer Header- oder Servervariablen untersuchen. Diese Untersuchung ist hilfreich, wenn Sie nur dann erneut Header generieren möchten, wenn der betreffende Header auch vorhanden ist.

![Überprüfen des Vorhandenseins eines Headers](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Einschränkungen

- Wenn eine Antwort über mehrere Header gleichen Namens verfügt, führt das erneute Generieren des Werts eines dieser Header zum Löschen der anderen Header in der Antwort. Dies kann in der Regel bei Set-Cookie-Headern auftreten, da eine Antwort mehrere Set-Cookie-Header enthalten kann. Ein solches Szenario liegt vor, wenn Sie einen App-Dienst mit einem Anwendungsgateway verwenden und cookiebasierte Sitzungsaffinität auf dem Anwendungsgateway konfiguriert haben. In diesem Fall enthält die Antwort zwei Set-Cookie-Header: einen vom App-Dienst verwendeten (z. B. `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`) und einen anderen für die Anwendungsgatewayaffinität (z. B. `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`). Das Umschreiben einer der Set-Cookie-Header in diesem Szenario kann dazu führen, dass der andere Set-Cookie-Header aus der Antwort entfernt wird.

- Das erneute Generieren wird nicht unterstützt, wenn das Anwendungsgateway so konfiguriert ist, dass die Anforderungen umgeleitet werden oder eine benutzerdefinierte Fehlerseite angezeigt wird.

- Das erneute Generieren von Verbindungs-, Upgrade- und Hostheadern wird derzeit nicht unterstützt.

- Wie in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) festgelegt, können Headernamen sämtliche alphanumerischen Zeichen und Sonderzeichen beinhalten. Derzeit wird das Sonderzeichen „Unterstrich“ (\_) in Headernamen nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Eine Anleitung zum erneuten Generieren von HTTP-Header finden Sie in folgenden Artikeln:

- [Erneutes Generieren von HTTP-Headern über das Azure-Portal](./rewrite-http-headers-portal.md)
- [Erneutes Generieren von HTTP-Headern über Azure PowerShell](add-http-header-rewrite-rule-powershell.md)