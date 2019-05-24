---
title: Sichern von Single-Page-Webanwendungen mit dem impliziten Datenfluss der Microsoft Identity Platform | Azure
description: Erstellen von Webanwendungen mit der Microsoft Identity Platform-Implementierung des impliziten Datenflusses für Single-Page-Apps.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f73756373a145375aa2b3d0bcb1c8fa0ede5cdb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823485"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity Platform und der implizit gewährte Datenfluss

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Mit dem Microsoft Identity Platform-Endpunkt können Sie Benutzer sowohl mit persönlichen Konten als auch mit Geschäfts-, Schul- oder Unikonten von Microsoft bei Single-Page-Apps anmelden. Bei Single-Page-Apps und anderen JavaScript-Apps, die hauptsächlich im Browser ausgeführt werden, gibt es in Bezug auf die Authentifizierung einige interessante Herausforderungen:

* Die Sicherheitsmerkmale dieser Apps unterscheiden sich grundlegend von herkömmlichen serverbasierten Webanwendungen.
* Zahlreiche Autorisierungsserver und Identitätsanbieter unterstützen keine CORS-Anforderungen.
* Umleitungen auf ganzseitige Browserseiten stören die Benutzererfahrung erheblich.

Bei diesen Anwendungen (AngularJS, Ember.js, React.js usw.) unterstützt Microsoft Identity Platform den impliziten OAuth 2.0-Gewährungsablauf. Der implizite Fluss wird in der [OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749#section-4.2) beschrieben. Der größte Vorteil besteht darin, dass die App Token aus Microsoft Identity Platform abrufen kann, ohne dass die Anmeldeinformationen für den Back-End-Server ausgetauscht werden müssen. Dadurch kann die App den Benutzer anmelden, die Sitzung aufrechterhalten und Token für andere Web-APIs abrufen. All dies geschieht innerhalb des Client-JavaScript-Codes. Bei der Verwendung des impliziten Flusses gibt es einige wichtige Sicherheitsaspekte zu beachten, insbesondere in Bezug auf [Clients](https://tools.ietf.org/html/rfc6749#section-10.3) und [Identitätswechsel](https://tools.ietf.org/html/rfc6749#section-10.3).

Wenn Sie mit dem impliziten Fluss und Microsoft Identity Platform Ihrer JavaScript-App eine Authentifizierungsmöglichkeit hinzufügen möchten, sollten Sie die Open Source-JavaScript-Bibliothek [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) verwenden.

Wenn Sie jedoch in Ihrer Single-Page-App keine Bibliothek verwenden und Protokollmeldungen selbst senden möchten, führen Sie die folgenden allgemeinen Schritte aus.

> [!NOTE]
> Es werden nicht alle Azure Active Directory-Szenarien und -Features (Azure AD) vom Microsoft Identity Platform-Endpunkt unterstützt. Informieren Sie sich über die [Einschränkungen der Microsoft Identity Platform](active-directory-v2-limitations.md), um zu ermitteln, ob Sie den Microsoft Identity Platform-Endpunkt verwenden sollten.

## <a name="protocol-diagram"></a>Protokolldiagramm

Das folgende Diagramm zeigt, wie der gesamte implizite Anmeldevorgang aussieht, und in den folgenden Abschnitten wird jeder Schritt im Detail beschrieben.

![OpenID Connect-Verantwortlichkeitsbereiche](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Senden der Anmeldeanforderung

Zur anfänglichen Anmeldung des Benutzers bei Ihrer App können Sie eine [OpenID Connect](v2-protocols-oidc.md)-Authentifizierungsanforderung senden und ein `id_token` vom Microsoft Identity Platform-Endpunkt abrufen.

> [!IMPORTANT]
> Um ein ID-Token erfolgreich anfordern zu können, muss für die App-Registrierung im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) der Flow zur impliziten Genehmigung aktiviert sein. Zu diesem Zweck wählen Sie **Zugriffstoken** und **ID-Token** unter dem Abschnitt **Implizite Genehmigung** aus. Wenn sie nicht aktiviert ist, wird ein Fehler des Typs `unsupported_response` zurückgegeben: **The provided value for the input parameter ‚response_type‘ is not allowed for this client. Expected value is ‚code‘.** (Der angegebene Wert für den Eingabeparameter ‚response_type‘ ist für diesen Client nicht zulässig. Erwarteter Wert: ‚code‘.)

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Klicken Sie zum Testen der Anmeldung mit dem impliziten Fluss auf <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Nach der Anmeldung sollte der Browser mit einem `id_token` in der Adressleiste zu `https://localhost/myapp/` umgeleitet werden.
>

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | required |Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints). |
| `client_id` | required | Die Anwendungs-ID (Client-ID), die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `response_type` | required |Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Kann auch den Antworttyp `token` enthalten. Mithilfe von `token` kann Ihre App ein Zugriffstoken direkt vom Autorisierungsendpunkt abrufen, ohne dass eine zweite Anforderung an den Autorisierungsendpunkt erforderlich ist. Wenn Sie den Antworttyp `token` verwenden, muss der `scope`-Parameter einen Bereich enthalten, der angibt, für welche Ressource das Token ausgestellt wird. |
| `redirect_uri` | empfohlen |Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| `scope` | required |Eine durch Leerzeichen getrennte Liste von [Bereichen](v2-permissions-and-consent.md). Für OpenID Connect muss der Bereich `openid`enthalten sein, der auf der Zustimmungsbenutzeroberfläche die Anmeldeberechtigung ergibt. Schließen Sie gegebenenfalls auch die Bereiche `email` oder `profile` mit ein, um Zugriff auf zusätzliche Benutzerdaten zu erhalten. Sie können in diese Anforderung auch andere Bereiche aufnehmen, um die Zustimmung für verschiedene Ressourcen anzufordern. |
| `response_mode` | optional |Gibt die Methode an, die zum Senden des resultierenden Tokens zurück an Ihre App verwendet werden soll. Der Standardwert für ein Zugriffstoken ist „Abfrage“, aber „Fragment“, wenn die Anforderung ein „id_token“ enthält. |
| `state` | empfohlen |Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um [websiteübergreifende Anforderungsfälschungsangriffe zu verhindern](https://tools.ietf.org/html/rfc6749#section-10.12). Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `nonce` | required |Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Token als Anspruch enthalten sein wird. Die App kann diesen Wert dann überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu vermindern. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. Nur erforderlich, wenn ein „id_token“ angefordert wird. |
| `prompt` | optional |Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt sind die einzigen gültigen Werte „login“, „none“, „select_account“ und „consent“. `prompt=login` zwingt den Benutzer, die Anmeldeinformationen bei dieser Anforderung einzugeben. Einmaliges Anmelden ist dadurch nicht möglich. `prompt=none` ist genau das Gegenteil: Dieser Wert stellt sicher, dass dem Benutzer keine interaktive Eingabeaufforderung angezeigt wird. Wenn die Anforderung nicht über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, gibt der Microsoft Identity Platform-Endpunkt einen Fehler zurück. `prompt=select_account` sendet den Benutzer an eine Kontoauswahl, in der alle in der Sitzung gespeicherten Konten angezeigt werden. `prompt=consent` löst nach der Anmeldung des Benutzers das OAuth-Zustimmungsdialogfeld aus, in dem der Benutzer aufgefordert wird, der App Berechtigungen zu gewähren. |
| `login_hint`  |optional |Dieser Wert kann verwendet werden, um das Feld für den Benutzernamen oder die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen, wenn dessen Benutzername im Vorfeld bekannt ist. Apps verwenden diesen Parameter häufig für die wiederholte Authentifizierung, nachdem sie den Benutzernamen aus einer vorherigen Anmeldung mithilfe des Anspruchs `preferred_username` extrahiert haben.|
| `domain_hint` | optional |Kann `consumers` oder `organizations` sein. Wenn dieser Parameter vorhanden ist, wird der E-Mail-basierte Ermittlungsvorgang übersprungen, den der Benutzer auf der Anmeldeseite durchläuft, und so die Benutzerfreundlichkeit verbessert. Apps verwenden diesen Parameter häufig für die wiederholte Authentifizierung, indem sie den Anspruch `tid` aus dem ID-Token extrahieren. Verwenden Sie `domain_hint=consumers`, wenn für den Anspruch `tid` der Wert `9188040d-6c67-4c5b-b112-36a304b66dad` festgelegt ist (der Endkundenmandant des Microsoft-Kontos). Andernfalls können Sie `domain_hint=organizations` während der erneuten Authentifizierung verwenden. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der Microsoft Identity Platform-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer **keiner** Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Weitere Informationen finden Sie unter [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps](v2-permissions-and-consent.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der Microsoft Identity Platform-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

#### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort mithilfe von `response_mode=fragment` und `response_type=id_token+token` sieht wie folgt aus, wobei die Zeilenumbrüche der Lesbarkeit dienen:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `access_token` |Ist enthalten, wenn `token` in `response_type` enthalten ist. Das von der Anwendung angeforderte Zugriffstoken, in diesem Fall für Microsoft Graph. Das Zugriffstoken sollte nicht decodiert oder anderweitig untersucht werden, es sollte als nicht transparente Zeichenfolge behandelt werden. |
| `token_type` |Ist enthalten, wenn `token` in `response_type` enthalten ist. Ist immer `Bearer`. |
| `expires_in`|Ist enthalten, wenn `token` in `response_type` enthalten ist. Gibt für die Zwischenspeicherung den Gültigkeitszeitraum des Tokens in Sekunden an. |
| `scope` |Ist enthalten, wenn `token` in `response_type` enthalten ist. Gibt die Bereiche an, für die das Zugriffstoken gültig ist. Umfasst eventuell nicht alle angeforderten Bereiche, wenn sie nicht auf den Benutzer anwendbar sind (im Fall von reinen Azure AD-Bereichen, die angefordert werden, wenn ein persönliches Konto für die Anmeldung verwendet wird). |
| `id_token` | Ein signiertes JSON Web Token (JWT). Die App kann die Segmente dieses Tokens decodieren, um Informationen zum angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich in Bezug auf Autorisierungs- und Sicherheitsgrenzen aber nicht darauf verlassen. Weitere Informationen zu ID-Token finden Sie unter [`id_token reference`](id-tokens.md). <br> **Hinweis:** Wird nur bei Anforderung des `openid`-Bereichs bereitgestellt. |
| `state` |Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

#### <a name="error-response"></a>Fehlerantwort

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` |Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## <a name="validate-the-idtoken"></a>Überprüfen des ID-Tokens

Das Empfangen eines ID-Tokens allein reicht nicht aus, um den Benutzer zu authentifizieren. Sie müssen auch die Signatur des ID-Tokens (id_token) validieren und die Ansprüche im Token auf Grundlage der Anforderungen Ihrer App überprüfen. Der Microsoft Identity Platform-Endpunkt verwendet [JSON-Webtoken (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und die Verschlüsselung mit öffentlichem Schlüssel, um Token zu signieren und deren Gültigkeit zu überprüfen.

Sie können `id_token` auch im Clientcode überprüfen. Es ist jedoch eine bewährte Methode, `id_token` an einen Back-End-Server zu senden und die Überprüfung dort auszuführen. Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie noch einige Ansprüche überprüfen. Weitere Informationen finden Sie in der [`id_token`-Referenz](id-tokens.md). Dort finden Sie auch Einzelheiten zum [Überprüfen von Token](id-tokens.md#validating-an-id_token) und [Wichtige Informationen zum Rollover von Signaturschlüsseln](active-directory-signing-key-rollover.md). Wir empfehlen, zum Analysieren und Überprüfen von Token eine Bibliothek zu verwenden. Für die meisten Sprachen und Plattformen ist mindestens eine Bibliothek verfügbar.

Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

* Sicherstellen, dass sich der Benutzer/die Organisation für die App angemeldet hat.
* Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und Berechtigungen verfügt.
* Sicherstellen, dass eine bestimmte Stärke der Authentifizierung aufgetreten ist, z.B. die mehrstufige Authentifizierung.

Nachdem Sie das ID-Token überprüft haben, können Sie mit dem Benutzer eine Sitzung beginnen und die Ansprüche im ID-Token zum Abrufen von Informationen über den Benutzer in der App verwenden. Diese Informationen können für die Anzeige, für Datensätze, für die Personalisierung usw. verwendet werden.

## <a name="get-access-tokens"></a>Abrufen von Zugriffstoken

Nachdem Sie den Benutzer bei der Single-Page-App angemeldet haben, können Sie Zugriffstoken zum Aufrufen der von der Microsoft Identity Platform gesicherten Web-APIs abrufen, z. B. [Microsoft Graph](https://developer.microsoft.com/graph). Auch wenn Sie mithilfe des Antworttyps `token` bereits ein Token erhalten haben, können Sie diese Methode zum Abrufen von Token für zusätzliche Ressourcen verwenden, ohne den Benutzer zur erneuten Anmeldung umzuleiten.

Im herkömmlichen OpenID Connect/OAuth-Fluss senden Sie dazu eine Anforderung an den Microsoft Identity Platform-Endpunkt `/token`. Der Microsoft Identity Platform-Endpunkt unterstützt jedoch keine CORS-Anforderungen, daher kommen AJAX-Aufrufe zum Abrufen und Aktualisieren von Token nicht infrage. Stattdessen können Sie den impliziten Fluss in einem ausgeblendeten IFrame verwenden, um neue Token für andere Web-APIs zu erhalten: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Weitere Informationen zu den Abfrageparametern in der URL finden Sie unter [Senden der Anmeldeanforderung](#send-the-sign-in-request).

> [!TIP]
> Kopieren und fügen Sie die folgende Anforderung in eine Browserregisterkarte ein. (Vergessen Sie dabei nicht, den Wert `login_hint` durch den richtigen Wert für den Benutzer zu ersetzen.)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Dank des Parameters `prompt=none` ist diese Anforderung entweder erfolgreich oder sie schlägt direkt fehl und kehrt zu Ihrer Anwendung zurück. Eine erfolgreiche Antwort wird an Ihre App an den angegebenen Umleitungs-URI (`redirect_uri`) gesendet. Dabei wird die im Parameter `response_mode` angegebene Methode verwendet.

#### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort mit `response_mode=fragment` sieht wie folgt aus:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `access_token` |Ist enthalten, wenn `token` in `response_type` enthalten ist. Das von der Anwendung angeforderte Zugriffstoken, in diesem Fall für Microsoft Graph. Das Zugriffstoken sollte nicht decodiert oder anderweitig untersucht werden, es sollte als nicht transparente Zeichenfolge behandelt werden. |
| `token_type` | Ist immer `Bearer`. |
| `expires_in` | Gibt für die Zwischenspeicherung den Gültigkeitszeitraum des Tokens in Sekunden an. |
| `scope` | Gibt die Bereiche an, für die das Zugriffstoken gültig ist. Umfasst eventuell nicht alle angeforderten Bereiche, wenn sie nicht auf den Benutzer anwendbar sind (im Fall von reinen Azure AD-Bereichen, die angefordert werden, wenn ein persönliches Konto für die Anmeldung verwendet wird). |
| `id_token` | Ein signiertes JSON Web Token (JWT). Ist enthalten, wenn `id_token` in `response_type` enthalten ist. Die App kann die Segmente dieses Tokens decodieren, um Informationen zum angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich in Bezug auf Autorisierungs- und Sicherheitsgrenzen aber nicht darauf verlassen. Weitere Informationen zu ID-Token (id_token) finden Sie in der [`id_token`-Referenz](id-tokens.md). <br> **Hinweis:** Wird nur bei Anforderung des `openid`-Bereichs bereitgestellt. |
| `state` |Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

#### <a name="error-response"></a>Fehlerantwort

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann: Im Fall von `prompt=none` wird folgender Fehler erwartet:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` |Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

Wenn Sie diesen Fehler in der IFrame-Anforderung erhalten, muss sich der Benutzer erneut anmelden, um ein neues Token abzurufen. Diesen Fall können Sie so behandeln, wie es für Ihre Anwendung am sinnvollsten ist.

## <a name="validating-access-tokens"></a>Überprüfen von Zugriffstoken

Überprüfen Sie nach Erhalt eines Zugriffstokens die Signatur des Tokens sowie die folgenden Ansprüche. Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen.

* Anspruch **Zielgruppe**: Zum Sicherstellen, dass das Token an Ihre App übergeben werden sollte
* Anspruch **issuer** (Aussteller): zum Überprüfen, ob das Token tatsächlich vom Microsoft Identity Platform-Endpunkt für Ihre App ausgestellt wurde
* Ansprüche **Nicht vor** und **Ablaufzeit**: Zum Sicherstellen, dass das Token nicht abgelaufen ist

Weitere Informationen zu den Ansprüchen im Zugriffstoken finden Sie in der [Zugriffstokenreferenz](access-tokens.md).

## <a name="refreshing-tokens"></a>Aktualisieren von Token

Die implizite Gewährung stellt keine Aktualisierungstoken bereit. `id_token`s und `access_token`s laufen nach kurzer Zeit ab. Ihre App muss daher diese Token in regelmäßigen Abständen aktualisieren. Zum Aktualisieren beider Tokentypen können Sie die oben erwähnte verborgene IFrame-Anforderung unter Verwendung des Parameters `prompt=none` ausführen, um das Verhalten der Microsoft Identity Platform zu steuern. Wenn Sie ein neues `id_token` erhalten möchten, verwenden Sie unbedingt `response_type=id_token` und `scope=openid`, sowie den Parameter `nonce`.

## <a name="send-a-sign-out-request"></a>Senden einer Abmeldungsanforderung

Die OpenID Connect `end_session_endpoint` ermöglicht Ihrer App das Senden einer Anforderung an den Microsoft Identity Platform-Endpunkt zum Beenden der Sitzung eines Benutzers und zum Löschen von Cookies, die vom Microsoft Identity Platform-Endpunkt festgelegt wurden. Um einen Benutzer vollständig von einer Webanwendung abzumelden, muss Ihre App ihre eigene Sitzung mit dem Benutzer beenden (in der Regel durch Löschen eines Tokencaches oder von Cookies) und dann den Browser zu folgender Adresse umleiten:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` |required |Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | empfohlen | Die URL, zu der der Benutzer nach erfolgreicher Abmeldung umgeleitet werden soll. Dieser Wert muss einem der Umleitung-URIs entsprechen, die für die Anwendung registriert sind. Wenn keine Angabe erfolgt, wird dem Benutzer vom Microsoft Identity Platform-Endpunkt eine allgemeine Meldung angezeigt. |

## <a name="next-steps"></a>Nächste Schritte

* Wechseln Sie zu den [MSAL JS-Beispielen](sample-v2-code.md#single-page-applications-spa), um in die Programmierung einzusteigen.
