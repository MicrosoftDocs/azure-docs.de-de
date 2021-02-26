---
title: Microsoft Identity Platform und der OAuth 2.0-Autorisierungscodeflow | Azure
titleSuffix: Microsoft identity platform
description: Erstellen von Webanwendungen mit der Microsoft Identity Platform-Implementierung des OAuth 2.0-Authentifizierungsprotokolls.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2687141ea870b0af0a4405ebef2261c5a303c767
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584111"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft Identity Platform und der OAuth 2.0-Autorisierungscodeflow

Der OAuth 2.0-Autorisierungcodefluss kann in Apps verwendet werden, die auf einem Gerät installiert sind, um Zugriff auf geschützte Ressourcen wie Web-APIs zu gewähren. Mithilfe der Microsoft Identity Platform-Implementierung von OAuth 2.0 können Sie sich bei mobilen Apps und Desktop-Apps anmelden und über APIs darauf zugreifen.

In diesem Artikel wird beschrieben, wie Sie direkt für das Protokoll in Ihrer Anwendung in einer beliebigen Sprache programmieren.  Es wird stattdessen empfohlen, ggf. die unterstützten Microsoft Authentication Libraries (MSAL) zu verwenden, um [Token zu erhalten und gesicherte Web-APIs aufzurufen](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Sehen Sie sich auch die [Beispiel-Apps an, die MSAL verwenden](sample-v2-code.md).

Der OAuth 2.0-Autorisierungscodefluss wird in [Abschnitt 4.1 der OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749)beschrieben. Er wird zum Ausführen der Authentifizierung und Autorisierung in den meisten App-Typen einschließlich [Single-Page-Webanwendungen](v2-app-types.md#single-page-apps-javascript), [Web-Apps](v2-app-types.md#web-apps) und [nativ installierten Apps](v2-app-types.md#mobile-and-native-apps) verwendet. Der Flow ermöglicht Apps das sichere Abrufen von Zugriffstoken, die für den Zugriff auf durch Microsoft Identity Platform geschützte Ressourcen verwendet werden können, sowie von Aktualisierungstoken, um zusätzliche Zugriffstoken und ID-Token für den angemeldeten Benutzer abzurufen.

## <a name="protocol-diagram"></a>Protokolldiagramm

Allgemein sieht der gesamte Authentifizierungsflow für eine Anwendung in etwa wie folgt aus:

![OAuth-Autorisierungscodefluss](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Für Single-Page-Webanwendungen erforderliche Einrichtung eines Umleitungs-URI

Der Autorisierungscodeflow für Single-Page-Webanwendungen erfordert einige zusätzliche Einrichtungsschritte.  Befolgen Sie die Anweisungen zum [Erstellen Ihrer Single-Page-Webanwendung](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow), um den Umleitungs-URI ordnungsgemäß als für CORS aktiviert zu kennzeichnen. Um CORS für einen vorhandenen Umleitungs-URI zu aktivieren, öffnen Sie den Manifest-Editor, und legen Sie im Abschnitt `replyUrlsWithType` das Feld `type` für den Umleitungs-URI auf `spa` fest. Sie können auch auf der Registerkarte „Authentifizierung“ im Abschnitt „Web“ auf den Umleitungs-URI klicken und die URIs auswählen, die Sie für die Verwendung des Autorisierungscodeflows migrieren möchten.

Der Umleitungstyp `spa` ist abwärtskompatibel mit dem impliziten Flow. Apps, die derzeit den impliziten Flow zum Abrufen von Token verwenden, können ohne Probleme auf den Umleitungs-URI-Typ `spa` umgestellt werden und den impliziten Flow weiterhin verwenden.

Wenn Sie versuchen, den Autorisierungscodeflow zu verwenden, und der folgende Fehler angezeigt wird:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Rufen Sie dann Ihre App-Registrierung auf, und ändern Sie den Umleitungs-URI für Ihre App in den Typ `spa`.

## <a name="request-an-authorization-code"></a>Anfordern eines Autorisierungscodes

Der Autorisierungscodefluss beginnt damit, dass der Client den Benutzer auf den `/authorize` -Endpunkt leitet. In dieser Anforderung fordert der Client die Berechtigungen `openid`, `offline_access` und `https://graph.microsoft.com/mail.read ` vom Benutzer an.  Einige Berechtigungen sind auf Administratoren beschränkt, z. B. das Schreiben von Daten in das Verzeichnis einer Organisation mithilfe von `Directory.ReadWrite.All`. Wenn Ihre Anwendung von einem Organisationsbenutzer Zugriff auf eine dieser Berechtigungen anfordert, wird dem Benutzer in einer Fehlermeldung mitgeteilt, dass er nicht befugt ist, den Berechtigungen Ihrer App zuzustimmen. Zugriff auf Bereiche, die auf Administratoren beschränkt sind, sollten Sie direkt von einem globalen Administrator anfordern.  Weitere Informationen finden Sie unter [Auf Administratoren beschränkte Berechtigungen](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> Klicken Sie auf den Link unten, um diese Anforderung auszuführen. Nach der Anmeldung sollte der Browser mit einem `code` in der Adressleiste zu `https://localhost/myapp/` umgeleitet werden.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter    | Erforderlich/optional | BESCHREIBUNG |
|--------------|-------------|--------------|
| `tenant`    | required    | Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | required    | Die **Anwendungs-ID (Client-ID)** , die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde.  |
| `response_type` | required    | Muss `code` für den Autorisierungscodefluss enthalten. Kann auch `id_token` oder `token` enthalten, wenn der [Hybridflow](#request-an-id-token-as-well-hybrid-flow) verwendet wird. |
| `redirect_uri`  | Erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. Für native und mobile Apps sollten Sie den Standardwert `https://login.microsoftonline.com/common/oauth2/nativeclient` verwenden.   |
| `scope`  | required    | Eine durch Leerzeichen getrennte Liste mit [Bereichen](v2-permissions-and-consent.md) , denen der Benutzer zustimmen soll.  Für den Abschnitt `/authorize` der Anforderung kann dies mehrere Ressourcen abdecken, sodass Ihre App Zustimmung für mehrere Web-APIs abrufen kann, die Sie aufrufen möchten. |
| `response_mode`   | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Tokens zurück an Ihre App verwendet werden soll. Dabei kann es sich um eine der folgenden Methoden handeln:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` gibt den Code als ein Abfragezeichenfolgen-Parameter in der Umleitungs-URI an. Wenn Sie ein ID-Token mit dem impliziten Flow anfordern, können Sie `query` nicht gemäß [OpenID-Spezifikation](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations) verwenden. Wenn Sie lediglich den Code anfordern, können Sie `query`, `fragment` oder `form_post` verwenden. `form_post` führt ein POST-Element mit dem Code zu Ihrer Umleitungs-URI aus. |
| `state`                 | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um [websiteübergreifende Anforderungsfälschungsangriffe zu verhindern](https://tools.ietf.org/html/rfc6749#section-10.12). Der Wert kann ebenfalls Informationen über den Status des Benutzers in der App codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `prompt`  | optional    | Gibt den Typ der erforderlichen Benutzerinteraktion an. Die einzigen gültigen Werte sind gegenwärtig `login`, `none` und `consent`.<br/><br/>- `prompt=login` zwingt den Benutzer, die Anmeldeinformationen bei dieser Anforderung einzugeben. Einmaliges Anmelden ist dadurch nicht möglich.<br/>- `prompt=none` ist genau das Gegenteil: Dieser Wert stellt sicher, dass dem Benutzer keine interaktive Eingabeaufforderung angezeigt wird. Wenn die Anforderung nicht über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, gibt Microsoft Identity Platform den Fehler `interaction_required` zurück.<br/>- `prompt=consent` löst nach der Anmeldung des Benutzers das OAuth-Zustimmungsdialogfeld aus, in dem der Benutzer aufgefordert wird, der App Berechtigungen zu erteilen.<br/>- `prompt=select_account` unterbricht beim einmaligen Anmelden die Kontoauswahlumgebung, in der entweder alle Konten in der Sitzung, alle gespeicherten Konten oder eine Option zur Verwendung eines ganz anderen Kontos aufgelistet werden.<br/> |
| `login_hint`  | optional    | Dieser Wert kann verwendet werden, um das Feld für den Benutzernamen oder die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen, wenn dessen Benutzername im Vorfeld bekannt ist. Apps verwenden diesen Parameter häufig für die wiederholte Authentifizierung, nachdem sie den Benutzernamen aus einer vorherigen Anmeldung mithilfe des Anspruchs `preferred_username` extrahiert haben.   |
| `domain_hint`  | optional    | Wenn dieser Parameter vorhanden ist, wird der E-Mail-basierte Ermittlungsvorgang übersprungen, den der Benutzer auf der Anmeldeseite durchläuft. Dadurch wird die Benutzerfreundlichkeit verbessert, und der Benutzer wird beispielsweise an seinen Verbundidentitätsanbieter weitergeleitet. Apps verwenden diesen Parameter häufig für die wiederholte Authentifizierung, indem sie `tid` aus einer vorherigen Anmeldung extrahieren. Verwenden Sie `domain_hint=consumers`, wenn der Anspruch `tid` den Wert `9188040d-6c67-4c5b-b112-36a304b66dad` hat. Verwenden Sie andernfalls `domain_hint=organizations`.  |
| `code_challenge`  | Empfohlen/erforderlich | Wird verwendet, um die Gewährung von Autorisierungscodes über Proof Key for Code Exchange (PKCE) zu sichern. Erforderlich, wenn `code_challenge_method` enthalten ist. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636). Dies wird jetzt für alle Anwendungstypen (native Apps, SPAs und vertrauliche Clients wie Web-Apps) empfohlen. |
| `code_challenge_method` | Empfohlen/erforderlich | Die Methode wird zum Codieren von `code_verifier` für den `code_challenge`-Parameter verwendet. Dieser *SOLLTE* `S256` lauten, die Spezifikation ermöglicht jedoch die Verwendung von `plain`, wenn der Client aus irgendeinem Grund SHA256 nicht unterstützt. <br/><br/>Wenn ausgeschlossen, wird angenommen, dass `code_challenge` Klartext ist, wenn `code_challenge` enthalten ist. Microsoft Identity Platform unterstützt sowohl `plain` als auch `S256`. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636). Dies ist für [Single-Page-Webanwendungen erforderlich, die den Autorisierungscodeflow verwenden](reference-third-party-cookies-spas.md).|


Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Microsoft Identity Platform stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im Abfrageparameter `scope` angegeben sind. Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Nähere Einzelheiten zu [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps erhalten Sie hier](v2-permissions-and-consent.md).

Sobald sich der Benutzer authentifiziert und seine Zustimmung erteilt hat, gibt Microsoft Identity Platform mithilfe der im Parameter `response_mode` festgelegten Methode eine Antwort über den angegebenen `redirect_uri` an Ihre App zurück.

#### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | BESCHREIBUNG  |
|-----------|--------------|
| `code` | Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden. Autorisierungscodes sind kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| `state` | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

Sie können auch ein ID-Token erhalten, wenn Sie eines anfordern und die implizite Genehmigung in Ihrer Anwendungsregistrierung aktiviert ist.  Dies wird manchmal als [Hybridflow](#request-an-id-token-as-well-hybrid-flow) bezeichnet und von Frameworks wie ASP.NET verwendet.

#### <a name="error-response"></a>Fehlerantwort

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | BESCHREIBUNG  |
|----------|------------------|
| `error`  | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Fehlercodes beim Autorisierungsendpunktfehler

Die folgende Tabelle beschreibt die verschiedenen Fehlercodes, die im `error` -Parameter der Fehlerantwort zurückgegeben werden können:

| Fehlercode  | BESCHREIBUNG    | Clientaktion   |
|-------------|----------------|-----------------|
| `invalid_request` | Protokollfehler, z.B. ein fehlender erforderlicher Parameter. | Korrigieren Sie die Anforderung, und senden Sie sie erneut. Dies ist ein Entwicklungsfehler, der in der Regel bei den Eingangstest entdeckt wird. |
| `unauthorized_client` | Die Clientanwendung darf keinen Autorisierungscode anfordern. | Dieser Fehler tritt in der Regel auf, wenn die Clientanwendung nicht in Azure AD registriert ist oder dem Azure AD-Mandanten des Benutzers nicht hinzugefügt wird. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| `access_denied`  | Der Ressourcenbesitzer hat die Zustimmung verweigert.  | Die Clientanwendung kann den Benutzer benachrichtigen, dass sie ohne Zustimmung des Benutzers nicht fortfahren kann. |
| `unsupported_response_type` | Der Autorisierungsserver unterstützt den Antworttyp in der Anforderung nicht. | Korrigieren Sie die Anforderung, und senden Sie sie erneut. Dies ist ein Entwicklungsfehler, der in der Regel bei den Eingangstest entdeckt wird. Wenn er im [Hybridflow](#request-an-id-token-as-well-hybrid-flow) auftritt, deutet dies darauf hin, dass Sie in der Client-App-Registrierung die Einstellung für die implizite Genehmigung des ID-Tokens aktivieren müssen. |
| `server_error`  | Der Server hat einen unerwarteten Fehler erkannt.| Wiederholen Sie die Anforderung. Diese Fehler werden durch temporäre Bedingungen verursacht. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund eines temporären Fehlers verzögert ist. |
| `temporarily_unavailable`   | Der Server ist vorübergehend überlastet und kann die Anforderung nicht verarbeiten. | Wiederholen Sie die Anforderung. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund einer temporären Bedingung verzögert ist. |
| `invalid_resource`  | Die Zielressource ist ungültig, da sie nicht vorhanden ist, Azure AD sie nicht findet oder sie nicht ordnungsgemäß konfiguriert ist. | Dieser Fehler gibt an, dass die Ressource, falls vorhanden, im Mandanten nicht konfiguriert wurde. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| `login_required` | Zu viele oder keine Benutzer gefunden | Der Client hat die Authentifizierung im Hintergrund angefordert (`prompt=none`), ein einzelner Benutzer konnte jedoch nicht gefunden werden. Dies kann bedeuten, dass in der Sitzung mehrere Benutzer oder keine Benutzer aktiv sind. Dabei wird der ausgewählte Mandant berücksichtigt (wenn z.B. zwei Azure AD-Konten und ein Microsoft-Konto aktiv sind und `consumers` ausgewählt wird, wird die Authentifizierung im Hintergrund ausgeführt). |
| `interaction_required` | Die Anforderung erfordert eine Benutzerinteraktion. | Es ist ein zusätzlicher Schritt zur Authentifizierung oder eine Zustimmung erforderlich. Wiederholen Sie die Anforderung ohne `prompt=none`. |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>Anfordern eines zusätzlichen ID-Tokens (Hybridflow)

Um vor dem Einlösen eines Autorisierungscodes zu ermitteln, wer der Benutzer ist, fordern Anwendungen häufig auch ein ID-Token an, wenn sie den Autorisierungscode anfordern. Dies wird als *Hybridflow* bezeichnet, da dabei die implizite Genehmigung mit dem Autorisierungscodeflow kombiniert wird. Der Hybridflow wird häufig bei Web-Apps verwendet, die eine Seite für einen Benutzer rendern möchten, ohne dass es bei der Codeeinlösung zu einer Sperrung kommt. Dies tritt insbesondere bei [ASP.NET](quickstart-v2-aspnet-core-webapp.md) auf. Sowohl einseitige als auch herkömmliche Web-Apps profitieren von der geringeren Latenz dieses Modells.

Der Hybridflow ist abgesehen von drei Ergänzungen mit dem oben beschriebenen Autorisierungscodeflow identisch. Diese Ergänzungen sind zum Anfordern eines ID-Tokens erforderlich: neue Bereiche, ein neuer „response_type“ und der neue Abfrageparameter `nonce`.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Aktualisierte Parameter | Erforderlich/optional | BESCHREIBUNG |
|---------------|-------------|--------------|
|`response_type`| Erforderlich | Das Hinzufügen von `id_token` informiert den Server darüber, dass die Anwendung in der Antwort vom `/authorize`-Endpunkt ein ID-Token erwartet.  |
|`scope`| Erforderlich | Für ID-Token ist eine Anpassung erforderlich, um die ID-Tokenbereiche `openid` und optional `profile` und `email` einzufügen. |
|`nonce`| Erforderlich|     Ein in der Anforderung enthaltener, von der App generierter Wert, der in das resultierende id_token als Anspruch einbezogen wird. Die App kann diesen Wert dann verifizieren, um Tokenwiederholungsangriffe abzuwehren. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die zur Identifizierung des Ursprungs der Anforderung verwendet werden kann. |
|`response_mode`| Empfohlen | Gibt die Methode an, die zum Senden des resultierenden Tokens zurück an Ihre App verwendet werden soll. Wenn nur ein Autorisierungscode enthalten ist, lautet der Standardwert `query`, enthält die Anforderung jedoch das ID-Token `response_type`, lautet der Wert `fragment`.|

Die Verwendung von `fragment` als Antwortmodus kann Probleme bei Web-Apps verursachen, die den Code aus der Umleitung lesen, da Browser das Fragment nicht an den Webserver übergeben.  In diesen Fällen sollten die Apps den Antwortmodus `form_post` verwenden, mit dem sichergestellt wird, dass alle Daten an den Server gesendet werden. 

#### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort mit `response_mode=fragment` sieht wie folgt aus:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| Parameter | BESCHREIBUNG  |
|-----------|--------------|
| `code` | Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden. Autorisierungscodes sind kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| `id_token` | Ein ID-Token für den Benutzer wird über eine *implizite Genehmigung* ausgestellt. Es enthält einen speziellen `c_hash`-Anspruch: den Hash des `code` in derselben Anforderung. |
| `state` | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die App sollte bestätigen, dass die state-Werte in der Anforderung und der Antwort identisch sind. |

## <a name="request-an-access-token"></a>Anfordern eines Zugriffstokens

Nun, da Sie einen Autorisierungscode erworben und die Berechtigung vom Benutzer erhalten haben, können Sie den `code` für ein `access_token` auf die gewünschte Ressource einlösen. Senden Sie hierzu eine `POST`-Anforderung an den `/token`-Endpunkt:

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Führen Sie diese Anforderung in Postman aus. (Vergessen Sie nicht, `code` zu ersetzen) [![Diese Anforderung in Postman ausführen](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parameter  | Erforderlich/optional | BESCHREIBUNG     |
|------------|-------------------|----------------|
| `tenant`   | required   | Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | required  | Die Anwendungs-ID (Client-ID), die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `grant_type` | required   | Muss der `authorization_code` für den Autorisierungscodefluss sein.   |
| `scope`      | Optional   | Eine durch Leerzeichen getrennte Liste von Bereichen. Die Bereiche müssen alle von einer einzelnen Ressource stammen, zusammen mit den OIDC-Bereichen (`profile`, `openid`, `email`). Eine ausführlichere Erläuterung von Bereichen finden Sie in [Berechtigungen, Zustimmung und Bereiche](v2-permissions-and-consent.md). Dies ist eine Microsoft-Erweiterung für den Autorisierungscodeflow, der es Apps ermöglichen soll, während der Tokeneinlösung die Ressource zu deklarieren, für die sie das Token benötigen.|
| `code`          | required  | Der Autorisierungscode, den Sie im ersten Abschnitt des Vorgangs erhalten haben. |
| `redirect_uri`  | required  | Derselbe Wert für den Umleitungs-URI, der zum Abrufen des Autorisierungscodes verwendet wurde |
| `client_secret` | Für vertrauliche Web-Apps erforderlich | Der geheime App-Schlüssel, den Sie im App-Registrierungsportal für Ihre App erstellt haben. Sie sollten den geheimen Anwendungsschlüssel nicht in einer nativen App oder in einer Single-Page-Webanwendung verwenden, weil geheime Clientschlüssel nicht zuverlässig auf Geräten oder Webseiten gespeichert werden können. Er ist erforderlich für Web-Apps und Web-APIs, die die Möglichkeit haben, den geheimen Client-Schlüssel sicher auf dem Server zu speichern.  Wie alle hier erläuterten Parameter muss der geheime Clientschlüssel vor dem Senden URL-codiert werden. Dieser Schritt wird normalerweise vom SDK durchgeführt. Weitere Informationen zur URI-Codierung finden Sie in der [Spezifikation der generischen URI-Syntax](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | empfohlen  | Derselbe code_verifier-Parameter, der auch zum Abrufen von „authorization_code“ verwendet wurde. Erforderlich, wenn PKCE bei der Anforderung für die Gewährung des Autorisierungscodes verwendet wurde. Weitere Informationen finden Sie unter [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parameter     | BESCHREIBUNG   |
|---------------|------------------------------|
| `access_token`  | Das angeforderte Zugriffstoken. Die App kann dieses Token zur Authentifizierung bei geschützten Ressourcen verwenden, wie z.B. eine Web-API.  |
| `token_type`    | Gibt den Wert des Tokentyps an. Der Bearertyp ist der einzige Typ, den Azure AD unterstützt. |
| `expires_in`    | Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| `scope`         | Die Bereiche, für die das Zugriffstoken gültig ist. Optional: Dies ist keine Standardangabe, und wenn hier kein Wert angegeben ist, gilt das Token für die Bereiche, die im ersten Abschnitt des Flows angefordert wurden. |
| `refresh_token` | Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um zusätzliche Zugriffstoken nach Ablauf der aktuellen Zugriffstoken zu erhalten. Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Informationen zum Aktualisieren eines Zugriffstokens finden Sie [im Abschnitt weiter unten](#refresh-the-access-token). <br> **Hinweis:** Wird nur bei Anforderung des `offline_access`-Bereichs bereitgestellt. |
| `id_token`      | JSON Web Token (JWT). Die App kann die Segmente dieses Tokens decodieren, um Informationen zum angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und anzeigen, und vertrauliche Clients, können die Werte zur Autorisierung verwenden. Weitere Informationen zu ID-Token finden Sie unter [`id_token reference`](id-tokens.md). <br> **Hinweis:** Wird nur bei Anforderung des `openid`-Bereichs bereitgestellt. |

### <a name="error-response"></a>Fehlerantwort

Fehlerantworten sehen wie folgt aus:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | BESCHREIBUNG    |
|-------------------|----------------|
| `error`       | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| `error_codes` | Eine Liste der STS-spezifischen Fehlercodes, die bei der Diagnose helfen können.  |
| `timestamp`   | Der Zeitpunkt, zu dem der Fehler aufgetreten ist |
| `trace_id`    | Ein eindeutiger Bezeichner für die Anforderung, die bei der Diagnose helfen kann |
| `correlation_id` | Ein eindeutiger Bezeichner für die Anforderung, die bei der komponentenübergreifenden Diagnose helfen kann |

### <a name="error-codes-for-token-endpoint-errors"></a>Fehlercodes für Token-Endpunktfehler

| Fehlercode         | BESCHREIBUNG        | Clientaktion    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokollfehler, z.B. ein fehlender erforderlicher Parameter. | Korrigieren Sie die Anforderung oder die App-Registrierung, und senden Sie die Anforderung erneut.   |
| `invalid_grant`    | Der Autorisierungscode oder PKCE-Codeprüfer ist ungültig oder abgelaufen. | Versuchen Sie, eine neue Anforderung für den `/authorize`-Endpunkt zu senden, und stellen Sie sicher, dass der „code_verifier“-Parameter korrekt war.  |
| `unauthorized_client` | Der authentifizierte Client ist zur Verwendung dieses Autorisierungsgewährungstyps nicht autorisiert. | Dies tritt in der Regel auf, wenn die Clientanwendung nicht in Azure AD registriert ist oder dem Azure AD-Mandanten des Benutzers nicht hinzugefügt wird. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| `invalid_client` | Clientauthentifizierung fehlgeschlagen.  | Die Client-Anmeldeinformationen sind nicht gültig. Um das Problem zu beheben, aktualisiert der Anwendungsadministrator die Anmeldeinformationen.   |
| `unsupported_grant_type` | Der Autorisierungsserver unterstützt den Autorisierungsgewährungstyp nicht. | Ändern Sie den Gewährungstyp in der Anforderung. Diese Art von Fehler sollte nur während der Entwicklung auftreten und bei den ersten Tests erkannt werden. |
| `invalid_resource` | Die Zielressource ist ungültig, da sie nicht vorhanden ist, Azure AD sie nicht findet oder sie nicht ordnungsgemäß konfiguriert ist. | Dies gibt an, dass die Ressource, falls vorhanden, im Mandanten nicht konfiguriert wurde. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern.  |
| `interaction_required` | Kein Standard, da die OIDC-Spezifikation dies nur im `/authorize`-Endpunkt erfordert. Die Anforderung erfordert eine Benutzerinteraktion. Beispielsweise ist ein zusätzlicher Schritt zur Authentifizierung erforderlich. | Wiederholen Sie die `/authorize`-Anforderung mit den gleichen Bereichen. |
| `temporarily_unavailable` | Der Server ist vorübergehend überlastet und kann die Anforderung nicht verarbeiten. | Wiederholen Sie die Anforderung nach kurzer Zeit. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund einer temporären Bedingung verzögert ist. |
|`consent_required` | Die Anforderung erfordert die Zustimmung des Benutzers. Dies ist kein Standardfehler, und er wird in der Regel gemäß OIDC-Spezifikation nur im `/authorize`-Endpunkt zurückgegeben. Wird zurückgegeben, wenn im Einlösungsflow des Codes ein `scope`-Parameter verwendet wurde, für dessen Anforderung die Client-App keine Berechtigung besitzt.  | Der Client muss den Benutzer mit dem richtigen Bereich wieder an den `/authorize`-Endpunkt zurückverweisen, um die Zustimmung auszulösen. |
|`invalid_scope` | Der von der App angeforderte Bereich ist ungültig.  | Aktualisieren Sie den Wert des scope-Parameters in der Authentifizierungsanforderung auf einen gültigen Wert. |

> [!NOTE]
> Single-Page-Webanwendungen erhalten möglicherweise eine Fehlermeldung des Typs `invalid_request`, die besagt, dass eine ursprungsübergreifende Tokeneinlösung nur für den Clienttyp „Single-Page-Webanwendung“ zulässig ist.  Dies weist darauf hin, dass der zum Anfordern des Tokens verwendete Umleitungs-URI nicht als `spa`-Umleitungs-URI gekennzeichnet wurde.  Informationen zum Aktivieren dieses Flows finden Sie im Abschnitt [Für Single-Page-Webanwendungen erforderliche Einrichtung](#redirect-uri-setup-required-for-single-page-apps).

## <a name="use-the-access-token"></a>Verwenden des Zugriffstokens

Nachdem Sie erfolgreich ein `access_token` abgerufen haben, können Sie das Token für Anforderungen an Web-APIs verwenden, indem Sie es in den `Authorization`-Header einschließen:

> [!TIP]
> Führen Sie diese Anforderung in Postman aus. (Ersetzen Sie zunächst den `Authorization`-Header) [![Diese Anforderung in Postman ausführen](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Aktualisieren des Zugriffstokens

Zugriffstoken sind kurzlebig. Daher müssen Sie sie nach Ablauf aktualisieren, um weiterhin auf Ressourcen zuzugreifen. Dazu übermitteln Sie eine weitere `POST`-Anforderung an den `/token`-Endpunkt, dieses Mal unter Angabe des `refresh_token` statt des `code`.  Aktualisierungstoken sind für alle Berechtigungen gültig, für die Ihr Client bereits die Einwilligung erhalten hat. Daher kann ein Aktualisierungstoken, das für eine Anforderung für `scope=mail.read` ausgestellt wurde, zum Anfordern eines neuen Zugriffstokens für `scope=api://contoso.com/api/UseResource` verwendet werden.

Für Aktualisierungstoken für Web-Apps und native Apps ist keine bestimmte Lebensdauer festgelegt. Normalerweise verfügen Aktualisierungstoken über relativ lange Lebensdauern. In einigen Fällen laufen Aktualisierungstoken aber ab, werden widerrufen oder verfügen nicht über ausreichende Berechtigungen für die gewünschte Aktion. Von Ihrer Anwendung müssen [Fehler, die vom Tokenausstellungsendpunkt zurückgegeben werden](#error-codes-for-token-endpoint-errors), erwartet und richtig behandelt werden. Single-Page-Webanwendungen erhalten jedoch ein Token mit einer Lebensdauer von 24 Stunden, wobei täglich eine neue Authentifizierung erforderlich ist.  Diese Authentifizierung kann im Hintergrund in einem IFrame erfolgen, wenn Cookies von Drittanbietern aktiviert sind. In Browsern ohne Cookies von Drittanbietern (z. B. in Safari) muss sie jedoch in einem Frame der obersten Ebene (entweder vollständige Seitennavigation oder Popup) ausgeführt werden.

Obwohl Aktualisierungstoken nicht widerrufen werden, wenn sie zum Abrufen neuer Zugriffstoken verwendet werden, sollten Sie das alte Aktualisierungstoken verwerfen. Die [OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749#section-6) besagt Folgendes: „Der Autorisierungsserver KANN ein neues Aktualisierungstoken ausstellen. In dem Fall MUSS der Client das alte Aktualisierungstoken verwerfen und durch das neue Aktualisierungstoken ersetzen. Der Autorisierungsserver KANN das alte Aktualisierungstoken nach dem Ausstellen eines neuen Aktualisierungstokens für den Client widerrufen.“

>[!IMPORTANT]
> Aktualisierungstoken, die an einen als `spa` registrierten Umleitungs-URI gesendet werden, laufen nach 24 Stunden ab. Weitere Aktualisierungstoken, die mithilfe des ersten Aktualisierungstokens abgerufen werden, übernehmen diese Ablaufzeit, sodass Apps darauf vorbereitet werden müssen, den Autorisierungscodeflow mithilfe einer interaktiven Authentifizierung erneut auszuführen, um alle 24 Stunden ein neues Aktualisierungstoken abzurufen. Benutzer müssen ihre Anmeldeinformationen nicht eingeben, und normalerweise wird nicht einmal eine Benutzeroberfläche, sondern nur ein erneutes Laden Ihrer Anwendung angezeigt. Der Browser muss jedoch die Anmeldeseite in einem Frame der obersten Ebene aufrufen, damit die Anmeldesitzung angezeigt wird.  Dies liegt an den [Datenschutzfunktionen in Browsern, die Cookies von Drittanbietern blockieren](reference-third-party-cookies-spas.md).

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Führen Sie diese Anforderung in Postman aus. (Vergessen Sie nicht, `refresh_token` zu ersetzen) [![Diese Anforderung in Postman ausführen](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parameter     | type           | BESCHREIBUNG        |
|---------------|----------------|--------------------|
| `tenant`        | required     | Mit dem `{tenant}` -Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | required    | Die **Anwendungs-ID (Client-ID)** , die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `grant_type`    | Erforderlich    | Muss der `refresh_token` für diesen Abschnitt des Autorisierungscodeflusses sein. |
| `scope`         | Erforderlich    | Eine durch Leerzeichen getrennte Liste von Bereichen. Die in diesem Abschnitt angeforderten Bereiche müssen den Bereichen entsprechen oder eine Teilmenge der Bereiche sein, die im ursprünglichen Autorisierungscode-Abschnitt angefordert wurden. Wenn die in dieser Anforderung angegebenen Bereiche mehrere Ressourcenserver umfassen, gibt Microsoft Identity Platform ein Token für die im ersten Bereich angegebene Ressource zurück. Eine ausführlichere Erläuterung von Bereichen finden Sie in [Berechtigungen, Zustimmung und Bereiche](v2-permissions-and-consent.md). |
| `refresh_token` | Erforderlich    | Das Aktualisierungstoken, das Sie im zweiten Abschnitt des Vorgangs erhalten haben. |
| `client_secret` | erforderlich für Web-Apps | Der geheime App-Schlüssel, den Sie im App-Registrierungsportal für Ihre App erstellt haben. Er sollte nicht in einer systemeigenen App verwendet werden, da geheime Client-Schlüssel nicht zuverlässig auf Geräten gespeichert werden können. Er ist erforderlich für Web-Apps und Web-APIs, die die Möglichkeit haben, den geheimen Client-Schlüssel sicher auf dem Server zu speichern. Dieser geheime Schlüssel muss URL-codiert sein. Weitere Informationen finden Sie in der [Spezifikation der generischen URI-Syntax](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parameter     | BESCHREIBUNG         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Das angeforderte Zugriffstoken. Die App kann dieses Token zur Authentifizierung bei geschützten Ressourcen verwenden, wie z.B. eine Web-API. |
| `token_type`    | Gibt den Wert des Tokentyps an. Der Bearertyp ist der einzige Typ, den Azure AD unterstützt. |
| `expires_in`    | Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist.   |
| `scope`         | Die Bereiche, für die das Zugriffstoken gültig ist.    |
| `refresh_token` | Ein neues Aktualisierungstoken von OAuth 2.0. Ersetzen Sie das alte Aktualisierungstoken durch das neu erworbene, um sicherzustellen, dass Ihre Aktualisierungstoken so lange wie möglich gültig bleiben. <br> **Hinweis:** Wird nur bei Anforderung des `offline_access`-Bereichs bereitgestellt.|
| `id_token`      | Ein unsigniertes JSON-Webtoken (JWT). Die App kann die Segmente dieses Tokens decodieren, um Informationen zum angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich in Bezug auf Autorisierungs- und Sicherheitsgrenzen aber nicht darauf verlassen. Weitere Informationen zu ID-Token finden Sie unter [`id_token reference`](id-tokens.md). <br> **Hinweis:** Wird nur bei Anforderung des `openid`-Bereichs bereitgestellt. |

#### <a name="error-response"></a>Fehlerantwort

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | BESCHREIBUNG                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können.           |
| `error_codes` |Eine Liste der STS-spezifischen Fehlercodes, die bei der Diagnose helfen können. |
| `timestamp` | Der Zeitpunkt, zu dem der Fehler aufgetreten ist |
| `trace_id` | Ein eindeutiger Bezeichner für die Anforderung, die bei der Diagnose helfen kann |
| `correlation_id` | Ein eindeutiger Bezeichner für die Anforderung, die bei der komponentenübergreifenden Diagnose helfen kann |

Eine Beschreibung der Fehlercodes und der jeweils empfohlenen Clientaktion finden Sie unter [Fehlercodes für Token-Endpunktfehler](#error-codes-for-token-endpoint-errors).
