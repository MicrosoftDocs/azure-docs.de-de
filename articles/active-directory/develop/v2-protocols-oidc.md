---
title: Microsoft Identity Platform und das OpenID Connect-Protokoll | Azure
description: Erstellen von Webanwendungen mit der Microsoft Identity Platform-Implementierung des OpenID Connect-Authentifizierungsprotokolls.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
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
ms.openlocfilehash: 23a8eaaf095be1d59944791bd793047886dda40c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544816"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity Platform und das OpenID Connect-Protokoll

OpenID Connect ist ein Authentifizierungsprotokoll auf Grundlage von OAuth 2.0, mit dem Benutzer sicher bei Webanwendungen angemeldet werden können. Die Implementierung von OpenID Connect im Microsoft Identity Platform-Endpunkt ermöglicht es Ihnen, Anmeldungen und API-Zugriff für Ihre webbasierten Apps hinzuzufügen. In diesem Artikel wird erläutert,wie dies sprachunabhängig funktioniert. Außerdem wird beschrieben, wie HTTP-Nachrichten gesendet und empfangen werden, ohne Open Source-Bibliotheken zu verwenden.

> [!NOTE]
> Der Microsoft Identity Platform-Endpunkt unterstützt nicht alle Azure Active Directory-Szenarien und -Features (Azure AD). Informieren Sie sich über die [Einschränkungen von Microsoft Identity Platform](active-directory-v2-limitations.md), um zu bestimmen, ob Sie den Microsoft Identity Platform-Endpunkt verwenden sollten.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) erweitert das OAuth 2.0-*Autorisierungsprotokoll*, das als *Authentifizierungsprotokoll* verwendet werden soll, sodass das einmalige Anmelden mithilfe von OAuth funktioniert. OpenID Connect führt das Konzept eines *ID-Tokens* ein. Hierbei handelt es sich um ein Sicherheitstoken, mit dem der Client die Identität des Benutzers überprüfen kann. Ferner ruft das ID-Token auch Basisprofilinformationen über den Benutzer ab. Da OpenID Connect das OAuth 2.0-Protokoll erweitert, können Apps auf sichere Weise *Zugriffstoken* abrufen, die für den Zugriff auf Ressourcen verwendet werden können, welche mithilfe eines [Autorisierungsservers](active-directory-v2-protocols.md#the-basics) geschützt werden. Der Microsoft Identity Platform-Endpunkt lässt auch zu, dass bei Azure AD registrierte Drittanbieter-Apps Zugriffstoken für geschützte Ressourcen wie Web-APIs ausstellen. Weitere Informationen zum Einrichten einer Anwendung zum Ausstellen von Zugriffstoken finden Sie unter [Registrieren einer App mit dem Microsoft Identity Platform-Endpunkt](quickstart-register-app.md). OpenID Connect wird für das Erstellen von [Webanwendungen](v2-app-types.md#web-apps) empfohlen, die auf einem Server gehostet werden und auf die über einen Browser zugegriffen wird.

## <a name="protocol-diagram-sign-in"></a>Protokolldiagramm: Anmeldung

Der grundlegende Anmeldefluss besteht aus den in der nächsten Abbildung gezeigten Schritten. Jeder Schritt wird in diesem Artikel detailliert beschrieben.

![OpenID Connect-Protokoll: Anmeldung](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Abrufen des OpenID Connect-Metadatendokuments

OpenID Connect beschreibt ein Metadatendokument, das den Großteil der Informationen enthält, die für eine App erforderlich sind, um eine Anmeldung auszuführen. Hierzu gehören Informationen wie z.B. die zu verwendenden URLs und der Speicherort der öffentlichen Signaturschlüssel des Dienstes. Für den Microsoft Identity Platform-Endpunkt sollten Sie folgendes OpenID Connect-Metadatendokument verwenden:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Testen Klicken Sie auf [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration), um die Mandantenkonfiguration `common` anzuzeigen.

Der `{tenant}` kann einen von vier möglichen Werten annehmen:

| Wert | BESCHREIBUNG |
| --- | --- |
| `common` |Benutzer mit einem persönlichen Microsoft-Konto und einem Geschäfts-, Schul- oder Unikonto aus Azure AD können sich bei der Anwendung anmelden. |
| `organizations` |Nur Benutzer mit Geschäfts-, Schul- oder Unikonten aus Azure AD können sich bei der Anwendung anmelden. |
| `consumers` |Nur Benutzer mit einem persönlich Microsoft-Konto können sich bei der Anwendung anmelden. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` oder `contoso.onmicrosoft.com` | Nur Benutzer mit einem Geschäfts-, Schul- oder Unikonto eines bestimmten Azure AD-Mandanten können sich bei der Anwendung anmelden. Dabei kann entweder der Anzeigename der Domäne des Azure AD-Mandanten oder der GUID-Bezeichner des Mandanten verwendet werden. Sie können auch den Consumermandanten `9188040d-6c67-4c5b-b112-36a304b66dad` anstelle des Mandanten `consumers` verwenden.  |

Bei den Metadaten handelt es sich um ein einfaches JavaScript Object Notation-Dokument (JSON). Die folgenden Codeausschnitte zeigen Beispiele. Die vollständigen Inhalte der Codeausschnitte werden in der [OpenID Connect-Spezifikation](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2) beschrieben.

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Wenn Ihre App durch die Verwendung der Funktion [Anspruchszuordnung](active-directory-claims-mapping.md) über benutzerdefinierte Signaturschlüssel verfügt, müssen Sie einen `appid`-Abfrageparameter mit der App-ID anfügen, um einen `jwks_uri` abzurufen, der auf die Signaturschlüsselinformationen Ihrer App verweist. Beispiel: `https://login.microsoftonline.com/{tenant}/.well-known/v2.0/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` enthält einen `jwks_uri` von `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

In der Regel verwenden Sie dieses Metadatendokument zum Konfigurieren einer OpenID Connect-Bibliothek oder eines SDKs; die Bibliothek führt ihre Aufgaben mithilfe der Metadaten durch. Wenn Sie jedoch keine OpenID Connect-Prä-Build-Bibliothek verwenden, können Sie die Schritte weiter unten in diesem Artikel ausführen, um die Anmeldung bei einer Web-App mithilfe des Microsoft Identity Platform-Endpunkts durchzuführen.

## <a name="send-the-sign-in-request"></a>Senden der Anmeldeanforderung

Wenn die Web-App den Benutzer authentifizieren muss, kann sie ihn direkt an den `/authorize` -Endpunkt weiterleiten. Diese Anforderung ähnelt dem ersten Abschnitt des [OAuth 2.0-Autorisierungscodeflusses](v2-oauth2-auth-code-flow.md), allerdings mit einigen wichtigen Unterschieden:

* Die Anforderung muss im `scope`-Parameter den `openid`-Bereich enthalten.
* Der `response_type`-Parameter muss `id_token` enthalten.
* Die Anforderung muss den `nonce` -Parameter enthalten.

> [!IMPORTANT]
> Zur erfolgreichen Anforderung eines ID-Tokens vom Endpunkt „/authorization“ muss für die App-Registrierung im [Registrierungsportal](https://portal.azure.com) auf der Registerkarte „Authentifizierung“ die implizite Genehmigung von „id_tokens“ aktiviert sein (dadurch wird das Flag `oauth2AllowIdTokenImplicitFlow` im [Anwendungsmanifest ](reference-app-manifest.md) auf `true` festgelegt). Wenn sie nicht aktiviert ist, wird ein `unsupported_response`-Fehler zurückgegeben: „The provided value for the input parameter 'response_type' isn't allowed for this client. Expected value is ‚code‘“. (Der angegebene Wert für den Eingabeparameter ‚response_type‘ ist für diesen Client nicht zulässig. Erwarteter Wert: ‚code‘.)

Beispiel:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Klicken Sie auf den folgenden Link, um diese Anforderung auszuführen. Nachdem Sie sich angemeldet haben, wird Ihr Browser an `https://localhost/myapp/` mit einem ID-Token in der Adressleiste weitergeleitet. Hinweis: Diese Anforderung verwendet `response_mode=fragment` (nur zu Demonstrationszwecken). Wir empfehlen Ihnen, hierfür `response_mode=form_post` zu verwenden.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Mit dem `{tenant}`-Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Protokollgrundlagen](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Erforderlich | Die **Anwendungs-ID (Client-ID)** , die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `response_type` | Erforderlich | Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Es kann auch andere `response_type`-Werte enthalten, z.B. `code`. |
| `redirect_uri` | Empfohlen | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben – mit dem Unterschied, dass er URL-codiert sein muss. Wenn dieser Parameter nicht vorhanden ist, wählt der Endpunkt nach dem Zufallsprinzip einen registrierten Umleitungs-URI aus, der an den Benutzer zurückgesendet wird. |
| `scope` | Erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Für OpenID Connect muss der Bereich `openid`enthalten sein, der auf der Zustimmungsbenutzeroberfläche die Anmeldeberechtigung ergibt. Sie können in diese Anforderung auch andere Bereiche für das Anfordern der Zustimmung aufnehmen. |
| `nonce` | Erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Tokenwert als Anspruch enthalten ist. Die App kann diesen Wert überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu reduzieren. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| `response_mode` | Empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann `form_post` oder `fragment` sein. Bei Webanwendungen empfiehlt sich die Verwendung von `response_mode=form_post`, um eine möglichst sichere Tokenübertragung an die Anwendung zu gewährleisten. |
| `state` | Empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Normalerweise wird ein zufällig generierter eindeutiger Wert verwendet, um [websiteübergreifende Anforderungsfälschungsangriffe zu verhindern](https://tools.ietf.org/html/rfc6749#section-10.12). Der Status wird außerdem verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `prompt` | Optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Die einzigen gültigen Werte sind gegenwärtig `login`, `none` und `consent`. Der Anspruch `prompt=login` zwingt den Benutzer, seine Anmeldeinformationen bei dieser Anforderung einzugeben. Einmaliges Anmelden ist dadurch nicht möglich. Der Anspruch `prompt=none` verhält sich genau entgegengesetzt. Dieser Anspruch stellt sicher, dass dem Benutzer keine interaktive Eingabeaufforderung angezeigt wird. Wenn die Anforderung nicht über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, gibt der Microsoft Identity Platform-Endpunkt einen Fehler zurück. Der Anspruch `prompt=consent` löst das OAuth-Zustimmungsdialogfeld aus, sobald sich der Benutzer angemeldet hat. Das Dialogfeld fordert den Benutzer zum Erteilen von Berechtigungen für die App auf. |
| `login_hint` | Optional | Sie können diesen Parameter verwenden, um das Feld für den Benutzernamen und die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen, wenn dessen Benutzername im Vorfeld bekannt ist. Apps verwenden diesen Parameter häufig für die erneute Authentifizierung, nachdem sie den Benutzernamen bereits aus einer vorherigen Anmeldung mithilfe des `preferred_username`-Anspruchs extrahiert haben. |
| `domain_hint` | Optional | Der Bereich des Benutzers in einem Verbundverzeichnis.  Mit diesem Parameter wird der E-Mail-basierte Ermittlungsvorgang übersprungen, den der Benutzer auf der Anmeldeseite durchläuft, was die Benutzerfreundlichkeit verbessert. Für Mandanten, die über ein lokales Verzeichnis wie z. B. AD FS verbunden sind, führt dies wegen der vorhandenen Anmeldesitzung häufig zu einer nahtlosen Anmeldung. |

An dieser Stelle wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der Microsoft Identity Platform-Endpunkt überprüft, ob der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer keiner dieser Berechtigungen zugestimmt hat, wird er vom Microsoft Identity Platform-Endpunkt aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Lesen Sie mehr über [Berechtigungen, die Zustimmung und mehrinstanzenfähige Apps](v2-permissions-and-consent.md).

Sobald der Benutzer authentifiziert wurde und seine Zustimmung erteilt hat, gibt der Microsoft Identity Platform-Endpunkt mithilfe der im Parameter `response_mode` festgelegten Methode eine Antwort über den angegebenen Umleitungs-URI an Ihre App zurück.

### <a name="successful-response"></a>Erfolgreiche Antwort

Bei Verwendung von `response_mode=form_post` sieht eine erfolgreiche Antwort wie folgt aus:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `id_token` | Das ID-Token, das die App angefordert hat. Sie können mit dem Parameter `id_token` die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [`id_tokens`-Referenz](id-tokens.md). |
| `state` | Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

### <a name="error-response"></a>Fehlerantwort

Fehlerantworten können auch an den Umleitungs-URI gesendet werden, damit die App diese angemessen behandeln kann. Eine Fehlerantwort sieht wie folgt aus:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Fehlercodes beim Autorisierungsendpunktfehler

Die folgende Tabelle beschreibt die Fehlercodes, die im Parameter `error` der Fehlerantwort zurückgegeben werden können:

| Fehlercode | BESCHREIBUNG | Clientaktion |
| --- | --- | --- |
| `invalid_request` | Protokollfehler, z.B. ein fehlender erforderlicher Parameter. |Korrigieren Sie die Anforderung, und senden Sie sie erneut. Dies ist ein Entwicklungsfehler, der in der Regel bei den Eingangstests festgestellt wird. |
| `unauthorized_client` | Die Clientanwendung darf keinen Autorisierungscode anfordern. |Dies tritt in der Regel auf, wenn die Clientanwendung nicht in Azure AD registriert ist oder dem Azure AD-Mandanten des Benutzers nicht hinzugefügt wird. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| `access_denied` | Der Ressourcenbesitzer hat die Zustimmung verweigert. |Die Clientanwendung kann den Benutzer benachrichtigen, dass sie ohne Zustimmung des Benutzers nicht fortfahren kann. |
| `unsupported_response_type` |Der Autorisierungsserver unterstützt den Antworttyp in der Anforderung nicht. |Korrigieren Sie die Anforderung, und senden Sie sie erneut. Dies ist ein Entwicklungsfehler, der in der Regel bei den Eingangstests festgestellt wird. |
| `server_error` | Der Server hat einen unerwarteten Fehler erkannt. |Wiederholen Sie die Anforderung. Diese Fehler werden durch temporäre Bedingungen verursacht. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund eines temporären Fehlers verzögert ist. |
| `temporarily_unavailable` | Der Server ist vorübergehend überlastet und kann die Anforderung nicht verarbeiten. |Wiederholen Sie die Anforderung. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund einer temporären Bedingung verzögert ist. |
| `invalid_resource` | Die Zielressource ist ungültig, da sie nicht vorhanden ist, Azure AD sie nicht findet oder sie nicht ordnungsgemäß konfiguriert wurde. |Dies zeigt an, dass die Ressource, sofern vorhanden, nicht im Mandanten konfiguriert wurde. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |

## <a name="validate-the-id-token"></a>Überprüfen des ID-Tokens

Das Empfangen eines ID-Tokens (id_token) allein reicht nicht aus, um den Benutzer zu authentifizieren. Sie müssen die Signatur des ID-Tokens validieren und die Ansprüche im Token gemäß der App-Anforderungen überprüfen. Der Microsoft Identity Platform-Endpunkt verwendet [JSON-Webtoken (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und die Verschlüsselung mit öffentlichem Schlüssel, um Token zu signieren und deren Gültigkeit zu überprüfen.

Sie können `id_token` auch im Clientcode überprüfen. Es ist jedoch eine bewährte Methode, `id_token` an einen Back-End-Server zu senden und die Überprüfung dort vorzunehmen. Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie noch einige Ansprüche überprüfen. Weitere Informationen finden Sie in der [`id_token`-Referenz](id-tokens.md). Dort finden Sie auch Einzelheiten zum [Überprüfen von Token](id-tokens.md#validating-an-id_token) und [Wichtige Informationen zum Rollover von Signaturschlüsseln](active-directory-signing-key-rollover.md). Wir empfehlen, zum Analysieren und Überprüfen von Token eine Bibliothek zu verwenden. Für die meisten Sprachen und Plattformen ist mindestens eine Bibliothek verfügbar.

Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

* Sicherstellen, dass sich der Benutzer/die Organisation für die App angemeldet hat.
* Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und Berechtigungen verfügt.
* Sicherstellen, dass eine bestimmte Stärke der Authentifizierung aufgetreten ist, z.B. die mehrstufige Authentifizierung.

Nachdem Sie das ID-Token überprüft haben, können Sie mit dem Benutzer eine Sitzung beginnen und die Ansprüche im ID-Token zum Abrufen von Informationen über den Benutzer in der App verwenden. Diese Informationen können für die Anzeige, für Datensätze, für die Personalisierung usw. verwendet werden.

## <a name="send-a-sign-out-request"></a>Senden einer Abmeldeanforderung

Wenn Sie den Benutzer bei der App abmelden möchten, reicht es nicht, die Cookies der App zu löschen oder die Sitzung des Benutzers auf andere Weise zu beenden. Sie müssen den Benutzer für die Abmeldung außerdem an den Microsoft Identity Platform-Endpunkt umleiten. Andernfalls kann sich der Benutzer erneut für Ihre App authentifizieren, ohne die Anmeldeinformationen erneut einzugeben, da der Benutzer nach wie vor über eine gültige SSO-Sitzung (Single Sign-On, Einmaliges Anmelden) beim Microsoft Identity Platform-Endpunkt verfügt.

Sie können den Benutzer an den `end_session_endpoint` umleiten, der im OpenID Connect-Metadatendokument aufgeführt wird:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Bedingung | BESCHREIBUNG |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Empfohlen | Die URL, an die der Benutzer nach erfolgreicher Abmeldung umgeleitet wird. Wenn der Parameter nicht enthalten ist, wird dem Benutzer eine generische Meldung angezeigt, die vom Microsoft Identity Platform-Endpunkt generiert wird. Diese URL muss mit einem der Umleitungs-URIs übereinstimmen, die im App-Registrierungsportal für Ihre Anwendung registriert wurden. |

## <a name="single-sign-out"></a>Einmaliges Abmelden

Wenn Sie den Benutzer an `end_session_endpoint` umleiten, löscht der Microsoft Identity Platform-Endpunkt die Sitzung des Benutzers aus dem Browser. Allerdings kann der Benutzer weiterhin bei anderen Anwendungen angemeldet sein, die Microsoft-Konten für die Authentifizierung verwenden. Damit diese Anwendungen den Benutzer gleichzeitig abmelden können, sendet der Microsoft Identity Platform-Endpunkt eine HTTP GET-Anforderung an die registrierte `LogoutUrl` aller Anwendungen, bei denen der Benutzer zurzeit angemeldet ist. Anwendungen müssen auf diese Anforderung antworten, indem sie alle Sitzungen löschen, mit denen der Benutzer identifiziert wird, und eine `200`-Antwort zurückgeben. Wenn Sie das einmalige Abmelden in Ihrer Anwendung unterstützen möchten, müssen Sie eine solche `LogoutUrl` im Code Ihrer Anwendung implementieren. Sie können die `LogoutUrl` über das App-Registrierungsportal festlegen.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokolldiagramm: Zugriffstokenbeschaffung

Viele Web-Apps müssen nicht nur den Benutzer anmelden, sondern auch mithilfe von OAuth im Namen dieses Benutzers auf einen Webdienst zugreifen. Dieses Szenario kombiniert OpenID Connect für die Benutzerauthentifizierung und ruft gleichzeitig einen Autorisierungscode ab, der mithilfe des OAuth-Autorisierungscodeflusses Zugriffstoken abrufen kann.

Der vollständige Ablauf für die Anmeldung mit OpenID Connect und den Abruf von Token sieht etwa wie folgt aus. Die einzelnen Schritte werden in den nächsten Abschnitten dieses Artikels im Detail beschrieben.

![OpenID Connect-Protokoll: Tokenbeschaffung](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Abrufen von Zugriffstoken
Zum Abrufen von Zugriffstoken müssen Sie die Anmeldeanforderung abwandeln:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Klicken Sie auf den folgenden Link, um diese Anforderung auszuführen. Nachdem Sie sich angemeldet haben, wird Ihr Browser an `https://localhost/myapp/` mit einem ID-Token und einem Code in der Adressleiste weitergeleitet. Hinweis: Diese Anforderung verwendet `response_mode=fragment` nur zu Demonstrationszwecken. Wir empfehlen Ihnen, hierfür `response_mode=form_post` zu verwenden.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Durch das Einschließen von Berechtigungsbereichen in die Anforderung und durch die Verwendung von `response_type=id_token code` stellt der Microsoft Identity Platform-Endpunkt sicher, dass der Benutzer den im `scope`-Abfrageparameter angegebenen Berechtigungen zugestimmt hat. Er gibt einen Autorisierungscode an Ihre App für den Austausch gegen ein Zugriffstoken zurück.

### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort mit `response_mode=form_post` sieht wie folgt aus:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `id_token` | Das ID-Token, das die App angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [`id_tokens`-Referenz](id-tokens.md). |
| `code` | Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden. Ein Autorisierungscode ist von kurzer Lebensdauer. Ein Autorisierungscode läuft in der Regel nach ungefähr 10 Minuten ab. |
| `state` | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

### <a name="error-response"></a>Fehlerantwort

Fehlerantworten können auch an den Umleitungs-URI gesendet werden, damit die App diese angemessen behandeln kann. Eine Fehlerantwort sieht wie folgt aus:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

Eine Beschreibung der möglichen Fehlercodes und der jeweils empfohlenen Clientantwort finden Sie unter [Fehlercodes beim Autorisierungsendpunktfehler](#error-codes-for-authorization-endpoint-errors).

Nachdem Sie einen Autorisierungscode und ein ID-Token erhalten haben, können Sie den Benutzer anmelden und Zugriffstoken in seinem Namen abrufen. Zum Anmelden des Benutzers müssen Sie das ID-Token [genau wie beschrieben](id-tokens.md#validating-an-id_token) überprüfen. Führen Sie zum Abrufen von Zugriffstoken die in der [Dokumentation zum OAuth-Codefluss](v2-oauth2-auth-code-flow.md#request-an-access-token) beschriebenen Schritte aus.
