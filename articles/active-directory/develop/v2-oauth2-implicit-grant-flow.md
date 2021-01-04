---
title: OAuth 2.0-Flow für die implizite Genehmigung – Microsoft Identity Platform | Azure
description: Schützen Sie Single-Page-Apps mit dem impliziten Fluss von Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4b5465cc5c1c3447af5303a5c0bfe82874705362
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511190"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity Platform und der Flow für die implizite Genehmigung

Microsoft Identity Platform unterstützt den Flow für die implizite Genehmigung von OAuth 2.0, der in der [OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749#section-4.2) beschrieben ist. Das definierende Merkmal der impliziten Genehmigung ist, dass Token (ID-Token oder Zugriffstoken) nicht über den Tokenendpunkt, sondern direkt über den Autorisierungsendpunkt zurückgegeben werden. Dies wird häufig im Rahmen des [Autorisierungscodeflows](v2-oauth2-auth-code-flow.md) genutzt und als „hybrider Flow“ bezeichnet. Hierbei wird das ID-Token bei der Autorisierungsanforderung zusammen mit einem Autorisierungscode abgerufen.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Empfohlene Verwendung des Autorisierungscodeflows

Da geplant ist, [Drittanbietercookies aus Browsern zu entfernen](reference-third-party-cookies-spas.md), ist der **Flow für die implizite Genehmigung keine geeignete Authentifizierungsmethode mehr**.  Da die [SSO-Hintergrundfeatures](#getting-access-tokens-silently-in-the-background) des Flows für die implizite Genehmigung nicht ohne Drittanbietercookies funktionieren, tritt für Anwendungen ein Fehler auf, wenn diese ein neues Token abrufen möchten. Wir empfehlen Ihnen dringend, für alle neuen Anwendungen anstelle des Flows für die implizite Genehmigung den [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md) zu verwenden, für den jetzt Single-Page-Apps unterstützt werden. Darüber hinaus ist es ratsam, auch [für vorhandene Single-Page-Apps mit der Migration zum Autorisierungscodeflow zu beginnen](migrate-spa-implicit-to-auth-code.md).

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Geeignete Szenarien für die implizite OAuth2-Genehmigung

Die implizite Genehmigung ist nur für den ersten interaktiven Teil Ihres Anmeldevorgangs zuverlässig, bei dem das Fehlen der [Drittanbietercookies](reference-third-party-cookies-spas.md) keine Beeinträchtigung Ihrer Anwendung bewirkt. Diese Einschränkung bedeutet, dass Sie diesen Ansatz ausschließlich im Rahmen des hybriden Flows verwenden sollten, bei dem Ihre Anwendung einen Code und ein Token vom Autorisierungsendpunkt anfordert. So ist dafür gesorgt, dass Ihre Anwendung einen Code erhält, der gegen ein Aktualisierungstoken eingelöst werden kann. Zudem bleibt die Gültigkeit für die Anmeldesitzung Ihrer App erhalten.

## <a name="protocol-diagram"></a>Protokolldiagramm

Das folgende Diagramm zeigt, wie der gesamte implizite Anmeldevorgang aussieht, und in den folgenden Abschnitten wird jeder Schritt im Detail beschrieben.

![Diagramm: Impliziter Anmeldevorgang](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Senden der Anmeldeanforderung

Zur anfänglichen Anmeldung des Benutzers bei Ihrer App können Sie eine [OpenID Connect](v2-protocols-oidc.md)-Authentifizierungsanforderung senden und ein `id_token` vom Microsoft Identity Platform-Endpunkt abrufen.

> [!IMPORTANT]
> Um ein ID-Token und/oder ein Zugriffstoken erfolgreich anfordern zu können, muss für die App-Registrierung im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) der entsprechende Flow zur impliziten Genehmigung aktiviert sein. Zu diesem Zweck wählen Sie im Abschnitt **Implizite Genehmigung** die Option **ID-Token** und/oder **Zugriffstoken** aus. Wenn sie nicht aktiviert ist, wird ein Fehler des Typs `unsupported_response` zurückgegeben: **The provided value for the input parameter ‚response_type‘ is not allowed for this client. Expected value is ‚code‘.** (Der angegebene Wert für den Eingabeparameter ‚response_type‘ ist für diesen Client nicht zulässig. Erwarteter Wert: ‚code‘.)

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

| Parameter | type | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | required |Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints). |
| `client_id` | required | Die Anwendungs-ID (Client-ID), die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `response_type` | required |Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Kann auch den Antworttyp `token` enthalten. Mithilfe von `token` kann Ihre App ein Zugriffstoken direkt vom Autorisierungsendpunkt abrufen, ohne dass eine zweite Anforderung an den Autorisierungsendpunkt erforderlich ist. Wenn Sie den Antworttyp `token` verwenden, muss der `scope`-Parameter einen Bereich enthalten, der angibt, für welche Ressource das Token ausgestellt wird (z. B. „user.read“ in Microsoft Graph). Er kann anstelle von `token` auch `code` enthalten, damit ein Autorisierungscode für die Verwendung im [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md) bereitgestellt wird. Diese Antwort vom Typ „ID-Token plus Code“ wird auch als hybrider Flow bezeichnet.  |
| `redirect_uri` | empfohlen |Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| `scope` | required |Eine durch Leerzeichen getrennte Liste von [Bereichen](v2-permissions-and-consent.md). Für OpenID Connect (id_tokens) muss der Bereich `openid` enthalten sein, der auf der Zustimmungsbenutzeroberfläche die Berechtigung „Anmeldung in Ihrem Namen“ ergibt. Optional können Sie auch die Bereiche `email` und `profile` einschließen, um Zugriff auf zusätzliche Benutzerdaten zu erhalten. Sie können in diese Anforderung auch andere Bereiche aufnehmen, um die Zustimmung für verschiedene Ressourcen anzufordern, wenn ein Zugriffstoken angefordert wird. |
| `response_mode` | optional |Gibt die Methode an, die zum Senden des resultierenden Tokens zurück an Ihre App verwendet werden soll. Der Standardwert nur für ein Zugriffstoken ist „Abfrage“, aber „Fragment“, wenn die Anforderung ein „id_token“ enthält. |
| `state` | empfohlen |Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um [websiteübergreifende Anforderungsfälschungsangriffe zu verhindern](https://tools.ietf.org/html/rfc6749#section-10.12). Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `nonce` | required |Ein in der Anforderung enthaltener, von der App generierter Wert, der in das resultierende id_token als Anspruch einbezogen wird. Die App kann diesen Wert dann verifizieren, um Tokenwiederholungsangriffe abzuwehren. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die zur Identifizierung des Ursprungs der Anforderung verwendet werden kann. Nur erforderlich, wenn ein „id_token“ angefordert wird. |
| `prompt` | optional |Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt sind die einzigen gültigen Werte „login“, „none“, „select_account“ und „consent“. `prompt=login` zwingt den Benutzer, die Anmeldeinformationen bei dieser Anforderung einzugeben. Einmaliges Anmelden ist dadurch nicht möglich. `prompt=none` ist genau das Gegenteil: Dieser Wert stellt sicher, dass dem Benutzer keine interaktive Eingabeaufforderung angezeigt wird. Wenn die Anforderung nicht über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, gibt der Microsoft Identity Platform-Endpunkt einen Fehler zurück. `prompt=select_account` sendet den Benutzer an eine Kontoauswahl, in der alle in der Sitzung gespeicherten Konten angezeigt werden. `prompt=consent` löst nach der Anmeldung des Benutzers das OAuth-Zustimmungsdialogfeld aus, in dem der Benutzer aufgefordert wird, der App Berechtigungen zu gewähren. |
| `login_hint`  |optional |Kann verwendet werden, um das Feld für Benutzername/E-Mail-Adresse der Anmeldeseite für den Benutzer vorab auszufüllen, wenn du seinen Benutzernamen vorab kennst. Apps verwenden diesen Parameter häufig für die wiederholte Authentifizierung, nachdem sie den Benutzernamen über den Anspruch `preferred_username` aus einer vorherigen Anmeldung extrahiert haben.|
| `domain_hint` | optional |Wenn dieser Parameter vorhanden ist, wird der E-Mail-basierte Ermittlungsvorgang übersprungen, den der Benutzer auf der Anmeldeseite durchläuft, und so die Benutzerfreundlichkeit verbessert. Dieser Parameter wird häufig für branchenspezifische Apps verwendet, die mit nur einem Mandanten betrieben werden. Hierbei wird in einem bestimmten Mandanten ein Domänenname angegeben, und der Benutzer wird an den Verbundanbieter für diesen Mandanten weitergeleitet.  Beachten Sie, dass sich Gäste hierbei nicht bei der Anwendung anmelden können und die Nutzung von Cloudanmeldeinformationen wie FIDO eingeschränkt wird.  |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der Microsoft Identity Platform-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer **keiner** Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Weitere Informationen finden Sie unter [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps](v2-permissions-and-consent.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der Microsoft Identity Platform-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

#### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort mithilfe von `response_mode=fragment` und `response_type=id_token+code` sieht wie folgt aus, wobei die Zeilenumbrüche der Lesbarkeit dienen:

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `code` | Ist enthalten, wenn `code` in `response_type` enthalten ist. Dies ist ein Autorisierungscode, der im [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md) verwendet werden kann.  |
| `access_token` |Ist enthalten, wenn `token` in `response_type` enthalten ist. Das von der App angeforderte Zugriffstoken. Das Zugriffstoken sollte nicht decodiert oder anderweitig untersucht werden, es sollte als nicht transparente Zeichenfolge behandelt werden. |
| `token_type` |Ist enthalten, wenn `token` in `response_type` enthalten ist. Ist immer `Bearer`. |
| `expires_in`|Ist enthalten, wenn `token` in `response_type` enthalten ist. Gibt für die Zwischenspeicherung den Gültigkeitszeitraum des Tokens in Sekunden an. |
| `scope` |Ist enthalten, wenn `token` in `response_type` enthalten ist. Gibt die Bereiche an, für die das Zugriffstoken gültig ist. Umfasst eventuell nicht alle angeforderten Bereiche, wenn sie nicht auf den Benutzer anwendbar sind (im Fall von reinen Azure AD-Bereichen, die angefordert werden, wenn ein persönliches Konto für die Anmeldung verwendet wird). |
| `id_token` | Ein signiertes JSON Web Token (JWT). Die App kann die Segmente dieses Tokens decodieren, um Informationen zum angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich in Bezug auf Autorisierungs- und Sicherheitsgrenzen aber nicht darauf verlassen. Weitere Informationen zu ID-Token finden Sie unter [`id_token reference`](id-tokens.md). <br> **Hinweis:** Wird nur bei Anforderung des Bereichs `openid` und bei Auswahl von `id_tokens` für `response_type` bereitgestellt. |
| `state` |Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

#### <a name="error-response"></a>Fehlerantwort

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` |Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Automatisches Abrufen von Zugriffstoken im Hintergrund

> [!Important]
> Dieser Teil des impliziten Flows funktioniert für Ihre Anwendung wahrscheinlich nicht. Der Grund ist, dass er aufgrund der [standardmäßigen Entfernung von Drittanbietercookies](reference-third-party-cookies-spas.md) für unterschiedliche Browser genutzt wird.  Für Chromium-basierte Browser funktioniert dies zwar noch, wenn diese nicht im Inkognitomodus verwendet werden, aber Entwickler sollten die Nutzung dieses Teils des Flows trotzdem überdenken. In Browsern, die keine Drittanbietercookies unterstützen, erhalten Sie eine Fehlermeldung mit dem Hinweis, dass keine Benutzer angemeldet sind. Der Grund ist, dass die Sitzungscookies der Anmeldeseite vom Browser entfernt wurden. 

Nachdem Sie den Benutzer bei der Single-Page-App angemeldet haben, können Sie Zugriffstoken zum Aufrufen der von der Microsoft Identity Platform gesicherten Web-APIs automatisch abrufen, z. B. [Microsoft Graph](https://developer.microsoft.com/graph). Auch wenn Sie mithilfe des Antworttyps `token` bereits ein Token erhalten haben, können Sie diese Methode zum Abrufen von Token für zusätzliche Ressourcen verwenden, ohne den Benutzer zur erneuten Anmeldung umzuleiten.

Im herkömmlichen OpenID Connect/OAuth-Fluss senden Sie dazu eine Anforderung an den Microsoft Identity Platform-Endpunkt `/token`. Sie können die Anforderung in einem ausgeblendeten IFrame senden, um neue Token für andere Web-APIs zu erhalten:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Weitere Informationen zu den Abfrageparametern in der URL finden Sie unter [Senden der Anmeldeanforderung](#send-the-sign-in-request).

> [!TIP]
> Kopieren und fügen Sie die folgende Anforderung in eine Browserregisterkarte ein. (Vergessen Sie dabei nicht, den Wert `login_hint` durch den richtigen Wert für den Benutzer zu ersetzen.)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Beachten Sie, dass dies auch in Browsern ohne Unterstützung von Drittanbietercookies funktioniert. Der Grund ist, dass die Eingabe direkt in einer Browserleiste erfolgt (im Gegensatz zum Öffnen in einem IFrame). 

Dank des Parameters `prompt=none` ist diese Anforderung entweder erfolgreich oder sie schlägt direkt fehl und kehrt zu Ihrer Anwendung zurück. Die Antwort wird an Ihre App an den angegebenen Umleitungs-URI (`redirect_uri`) gesendet. Dabei wird die im Parameter `response_mode` angegebene Methode verwendet.

#### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort mit `response_mode=fragment` sieht wie folgt aus:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
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

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` |Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

Wenn Sie diesen Fehler in der IFrame-Anforderung erhalten, muss sich der Benutzer erneut anmelden, um ein neues Token abzurufen. Diesen Fall können Sie so behandeln, wie es für Ihre Anwendung am sinnvollsten ist.

## <a name="refreshing-tokens"></a>Aktualisieren von Token

Die implizite Genehmigung stellt keine Aktualisierungstoken bereit. `id_token`s und `access_token`s laufen nach kurzer Zeit ab. Ihre App muss daher diese Token in regelmäßigen Abständen aktualisieren. Zum Aktualisieren beider Tokentypen können Sie die oben erwähnte verborgene IFrame-Anforderung unter Verwendung des Parameters `prompt=none` ausführen, um das Verhalten der Microsoft Identity Platform zu steuern. Wenn Sie ein neues `id_token` erhalten möchten, verwenden Sie unbedingt `id_token` in `response_type` und `scope=openid` sowie einen `nonce`-Parameter.

In Browsern, für die keine Drittanbietercookies unterstützt werden, führt dies zu einer Fehlermeldung mit dem Hinweis, dass kein Benutzer angemeldet ist. 

## <a name="send-a-sign-out-request"></a>Senden einer Abmeldungsanforderung

Die OpenID Connect `end_session_endpoint` ermöglicht Ihrer App das Senden einer Anforderung an den Microsoft Identity Platform-Endpunkt zum Beenden der Sitzung eines Benutzers und zum Löschen von Cookies, die vom Microsoft Identity Platform-Endpunkt festgelegt wurden. Um einen Benutzer vollständig von einer Webanwendung abzumelden, muss Ihre App ihre eigene Sitzung mit dem Benutzer beenden (in der Regel durch Löschen eines Tokencaches oder von Cookies) und dann den Browser zu folgender Adresse umleiten:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter | type | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` |required |Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | empfohlen | Die URL, zu der der Benutzer nach erfolgreicher Abmeldung umgeleitet werden soll. Dieser Wert muss einem der Umleitung-URIs entsprechen, die für die Anwendung registriert sind. Wenn keine Angabe erfolgt, wird dem Benutzer vom Microsoft Identity Platform-Endpunkt eine allgemeine Meldung angezeigt. |

## <a name="next-steps"></a>Nächste Schritte

* Wechseln Sie zu den [MSAL JS-Beispielen](sample-v2-code.md), um in die Programmierung einzusteigen.
* Informieren Sie sich über den [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md) als neuere und bessere Alternative zur impliziten Genehmigung. 
