---
title: Microsoft Identity Platform-Zugriffstoken | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie mehr über Zugriffstoken, die von den Azure AD v1.0- und Microsoft Identity Platform-Endpunkten (v2.0) ausgegeben werden.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: e1dcd52660ff43a93c6a170912fea5a5847fe9d3
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575753"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft Identity Platform-Zugriffstoken

Zugriffstoken bieten Clients die Möglichkeit, auf sichere Weise geschützte Web-APIs aufzurufen, welche die Zugriffstoken zur Authentifizierung und Autorisierung verwenden. Gemäß der OAuth-Spezifikation sind Zugriffstoken nicht transparente Zeichenfolgen ohne ein bestimmtes Format. Einige Identitätsanbieter (IDPs) verwenden GUIDs, andere verschlüsselte Blobs. Je nach Konfiguration der API, die das Token akzeptiert, verwendet Microsoft Identity Platform für Zugriffstoken verschiedene Formate. Für [benutzerdefinierte, von Entwicklern in Microsoft Identity Platform registrierte APIs](quickstart-configure-app-expose-web-apis.md) stehen zwei unterschiedliche JSON Web-Token (JWT)-Formate mit der Bezeichnung „v1“ und „v2“ zur Auswahl, und für von Microsoft entwickelte APIs wie Microsoft Graph oder APIs in Azure können zusätzliche proprietäre Tokenformate verwendet werden. Bei diesen proprietären Formaten kann es sich um verschlüsselte Token, JWTs oder spezielle JWT-ähnliche Token handeln, die nicht überprüft werden.

Clients müssen Zugriffstoken wie nicht transparente Zeichenfolgen behandeln, da der Inhalt des Tokens nur für die Ressource (die API) bestimmt ist. *Ausschließlich* zu Prüfungs- und Debugzwecken können Entwickler JWTs mit einer Website wie [jwt.ms](https://jwt.ms) decodieren. Beachten Sie jedoch, dass die Token, die Sie für eine Microsoft-API erhalten, möglicherweise nicht immer JWTs sind und auch nicht immer decodiert werden können.

Ausführliche Informationen zum Inhalt des Zugriffstokens können Clients über die Tokenantwortdaten erhalten, die mit dem Zugriffstoken an den Client zurückgegeben werden. Wenn Ihr Client ein Zugriffstoken anfordert, gibt Microsoft Identity Platform auch einige Metadaten zum Zugriffstoken zurück, die von Ihrer App genutzt werden können. Diese Informationen umfassen die Ablaufzeit eines Zugriffstokens und die Bereiche, für die es gilt. Die Daten ermöglichen Ihrer App das intelligente Zwischenspeichern von Zugriffstoken, ohne dass dabei das Zugriffstoken selbst analysiert werden muss.

In den folgenden Abschnitten erfahren Sie, wie Ihre API die Ansprüche in einem Zugriffstoken überprüfen und verwenden kann.  

> [!NOTE]
> Die gesamte Dokumentation auf dieser Seite gilt, sofern nichts Gegenteiliges erwähnt ist, nur für Token, die für von Ihnen registrierte APIs ausgegeben werden.  Sie gilt weder für Token, die für Microsoft-eigene APIs ausgegeben werden, noch kann anhand dieser Token überprüft werden, wie Microsoft Identity Platform Token für eine von Ihnen erstellte API ausgibt.  

## <a name="token-formats-and-ownership"></a>Tokenformate und Besitz

### <a name="v10-and-v20"></a>v1.0 und v2.0 

Es gibt zwei Versionen von Zugriffstoken, die in Microsoft Identity Platform verfügbar sind: v1.0 und v2.0.  Diese Versionen geben die Ansprüche im Token vor und stellen dabei sicher, dass eine Web-API das Aussehen ihrer Token steuern kann. Eine dieser beiden Versionen wird bei der Registrierung von Web-APIs als Standard ausgewählt: v1.0 für reine Azure AD-Apps, v2.0 für Apps, die Consumerkonten unterstützen.  Dies kann von Anwendungen mit der Einstellung `accessTokenAcceptedVersion` im [App-Manifest](reference-app-manifest.md#manifest-reference) gesteuert werden, wobei `null` und `1` v1.0-Token ergeben und `2` in v2.0-Token resultiert.

### <a name="what-app-is-a-token-for"></a>Für welche App ist ein Token bestimmt?

An einer Zugriffstokenanforderung sind zwei Parteien beteiligt: der Client, der das Token anfordert, und die Ressource (die API), die das Token akzeptiert, wenn die API aufgerufen wird. Der Anspruch `aud` in einem Token gibt die Ressource an, für die das Token bestimmt ist (die *Zielgruppe*). Clients verwenden das Token, sollten es jedoch weder verstehen noch analysieren können. Ressourcen akzeptieren das Token.  

Microsoft Identity Platform unterstützt das Ausstellen beliebiger Tokenversionen von beliebigen Versionsendpunkten – sie sind nicht miteinander verknüpft. Die Ressourceneinstellung `2` unter `accessTokenAcceptedVersion` bedeutet daher, dass ein Client, der den v1.0-Endpunkt zum Abrufen eines Tokens für diese API aufruft, ein v2.0-Zugriffstoken erhält.  Ressourcen sind immer Besitzer ihrer Token (Token mit dem Anspruch `aud`) und können als einzige Anwendungen die Tokendetails ändern. Daher wird durch eine Änderung der [optionalen Ansprüche](active-directory-optional-claims.md) des Zugriffstokens für Ihren *Client* auch nicht das beim Anfordern eines Tokens für `user.read` empfangene Zugriffstoken geändert, das sich im Besitz der Microsoft Graph-Ressource befindet.

### <a name="sample-tokens"></a>Beispieltoken

v1.0- und v2.0-Token ähneln sich und enthalten viele der gleichen Ansprüche. Im Folgenden ist jeweils ein Beispiel aufgeführt. Diese Beispieltoken können jedoch nicht [überprüft](#validating-tokens) werden, da die Schlüssel vor der Veröffentlichung rotiert und persönliche Informationen aus ihnen entfernt wurden.

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Zeigen Sie dieses v1.0-Token in [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd) an.

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Zeigen Sie dieses v2.0-Token in [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt) an.

## <a name="claims-in-access-tokens"></a>Ansprüche in Zugriffstoken

JSON Web Token (JWTs) sind in drei Teile unterteilt:

* **Header**: Enthält Informationen dazu, wie Sie das [Token überprüfen](#validating-tokens) können. Dazu zählen auch Informationen zum Typ des Tokens und zu seiner Signierung.
* **Nutzlast**: Enthält alle wichtigen Daten über den Benutzer oder die App, der bzw. die Ihren Dienst aufzurufen versucht.
* **Signatur**: Dies ist das Rohmaterial, das zum Überprüfen des Tokens verwendet wird.

Diese Teile sind jeweils durch einen Punkt (`.`) getrennt und werden separat Base64-codiert.

Ansprüche sind nur enthalten, wenn ein Wert zum Füllen des Anspruchs vorhanden ist. Ihre App sollte nicht vom Vorhandensein eines Anspruchs abhängig sein. Beispiele hierfür sind `pwd_exp` (nicht jeder Mandant erfordert das Ablaufen von Kennwörtern) und `family_name` (Flows für [Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) erfolgen im Auftrag von Anwendungen, die keine Namen haben). Zur Überprüfung von Zugriffstoken verwendete Ansprüche sind immer vorhanden.

Einige Ansprüche werden verwendet, um Azure AD das Schützen von Token im Fall einer Wiederverwendung zu ermöglichen. Diese Ansprüche sind in der Beschreibung als „nicht transparent“ markiert, um anzugeben, dass sie nicht für die öffentliche Nutzung vorgesehen sind. Solche Ansprüche können eventuell in einem Token enthalten sein, und neue Ansprüche können ohne vorherige Ankündigung hinzugefügt werden.

### <a name="header-claims"></a>Headeransprüche

|Anspruch | Format | BESCHREIBUNG |
|--------|--------|-------------|
| `typ` | Zeichenfolge – immer „JWT“ | Gibt an, dass das Token ein JWT ist.|
| `nonce` | String | Ein eindeutiger Bezeichner, der zum Schutz vor Token-Replay-Angriffen verwendet wird. Ihre Ressource kann diesen Wert zum Schutz vor Wiedergaben aufzeichnen. |
| `alg` | String | Gibt den Algorithmus an, mit dem das Token signiert wurde, beispielsweise „RS256“. |
| `kid` | String | Gibt den Fingerabdruck des öffentlichen Schlüssels an, der zum Signieren dieses Tokens verwendet wird. Wird in v1.0- und v2.0-Zugriffstoken ausgegeben. |
| `x5t` | String | Hat dieselbe Funktion (hinsichtlich Verwendung und Wert) wie `kid`. `x5t` ist ein Legacyanspruch, der aus Kompatibilitätsgründen nur in v1.0-Zugriffstoken ausgegeben wird. |

### <a name="payload-claims"></a>Nutzlastansprüche

| Anspruch | Format | BESCHREIBUNG |
|-----|--------|-------------|
| `aud` | Zeichenfolge, App-ID-URI oder GUID | Identifiziert den vorgesehenen Empfänger des Tokens – die Zielgruppe.  Ihre API sollte diesen Wert überprüfen und das Token ablehnen, wenn der Wert nicht übereinstimmt. In v2.0-Token ist dies immer die Client-ID der API, während es in v1.0-Token die Client-ID oder der in der Anforderung verwendete Ressourcen-URI sein kann. Das richtet sich danach, wie der Client das Token angefordert hat.|
| `iss` | Zeichenfolge, ein STS-URI | Identifiziert den Sicherheitstokendienst (STS), der das Token und den Azure AD-Mandanten, in dem der Benutzer authentifiziert wurde, erstellt und zurückgibt. Wenn das ausgegebene Token ein v2. 0-Token ist (weitere Informationen finden Sie im Anspruch `ver`), endet der URI in `/v2.0`. Die GUID, die angibt, dass der Benutzer ein Consumer-Benutzer eines Microsoft-Kontos ist, lautet `9188040d-6c67-4c5b-b112-36a304b66dad`. Ihre App kann den GUID-Teil des Anspruchs verwenden, um die Mandanten einzuschränken, die sich bei der App anmelden können (sofern zutreffend). |
|`idp`| Zeichenfolge, in der Regel ein STS-URI | Der Identitätsanbieter, der den Antragsteller des Tokens authentifiziert hat. Dieser Wert ist identisch mit dem Wert des Ausstelleranspruchs, es sei denn, das Benutzerkonto ist nicht im gleichen Mandanten wie der Aussteller vorhanden (etwa Gäste). Wenn der Anspruch nicht vorhanden ist, bedeutet dies, dass stattdessen der Wert `iss` verwendet werden kann.  Für in einem Organisationskontext verwendete persönliche Konten (etwa ein zu einem Azure AD-Mandanten eingeladenes persönliches Konto) kann der `idp`-Anspruch „live.com“ oder ein STS-URI sein, der den Microsoft-Kontomandanten `9188040d-6c67-4c5b-b112-36a304b66dad` enthält. |
| `iat` | Ganze Zahl, ein UNIX-Zeitstempel | „Issued At“ gibt an, wann die Authentifizierung für dieses Token erfolgt ist. |
| `nbf` | Ganze Zahl, ein UNIX-Zeitstempel | Der Anspruch „nbf“ (nicht vor) gibt die Zeit an, vor der das JWT NICHT für die Bearbeitung akzeptiert werden darf. |
| `exp` | Ganze Zahl, ein UNIX-Zeitstempel | Der Anspruch „exp“ (Ablaufzeit) gibt die Ablaufzeit an, ab oder nach der das JWT NICHT für die Bearbeitung akzeptiert werden darf. Wichtig ist hierbei, dass eine Ressource das Token auch vor diesem Zeitpunkt ablehnen kann (beispielsweise wenn eine Änderung der Authentifizierung erforderlich ist oder ein Tokenwiderruf erkannt wurde). |
| `aio` | Nicht transparente Zeichenfolge | Ein interner Anspruch, der von Azure AD verwendet wird, um Daten für die Wiederverwendung von Token aufzuzeichnen. Ressourcen sollten diesen Anspruch nicht verwenden. |
| `acr` | Zeichenfolge, 0 oder 1 | Ist nur in v1.0-Token vorhanden. Der Anspruch „Authentication context class“ (Authentifizierungskontextklasse). Der Wert "0" gibt an, dass die Endbenutzerauthentifizierung nicht die ISO/IEC 29115-Anforderungen erfüllt. |
| `amr` | JSON-Array von Zeichenfolgen | Ist nur in v1.0-Token vorhanden. Gibt an, wie der Antragsteller des Tokens authentifiziert wurde. Weitere Details finden Sie im Abschnitt zum [amr-Anspruch](#the-amr-claim). |
| `appid` | Zeichenfolge, eine GUID | Ist nur in v1.0-Token vorhanden. Die Anwendungs-ID des Clients, der das Token verwendet. Die Anwendung kann als sie selbst oder im Auftrag eines Benutzers agieren. Die Anwendungs-ID stellt in der Regel ein Anwendungsobjekt dar, kann aber auch ein Dienstprinzipalobjekt in Azure AD darstellen. |
| `azp` | Zeichenfolge, eine GUID | Nur in v2.0-Token enthalten, ersetzt `appid`. Die Anwendungs-ID des Clients, der das Token verwendet. Die Anwendung kann als sie selbst oder im Auftrag eines Benutzers agieren. Die Anwendungs-ID stellt in der Regel ein Anwendungsobjekt dar, kann aber auch ein Dienstprinzipalobjekt in Azure AD darstellen. |
| `appidacr` | 0, 1 oder 2 | Ist nur in v1.0-Token vorhanden. Gibt an, wie der Client authentifiziert wurde. Bei einem öffentlichen Client ist der Wert 0. Wenn die Client-ID und der geheime Clientschlüssel verwendet werden, ist der Wert 1. Wenn ein Clientzertifikat für die Authentifizierung verwendet wurde, lautet der Wert 2. |
| `azpacr` | 0, 1 oder 2 | Nur in v2.0-Token enthalten, ersetzt `appidacr`. Gibt an, wie der Client authentifiziert wurde. Bei einem öffentlichen Client ist der Wert 0. Wenn die Client-ID und der geheime Clientschlüssel verwendet werden, ist der Wert 1. Wenn ein Clientzertifikat für die Authentifizierung verwendet wurde, lautet der Wert 2. |
| `preferred_username` | String | Der primäre Benutzername, der den Benutzer darstellt. Dabei kann es sich um eine E-Mail-Adresse, eine Telefonnummer oder einen generischen Benutzernamen ohne bestimmtes Format handeln. Der Wert kann geändert werden und sich im Laufe der Zeit ändern. Da er geändert werden kann, darf dieser Wert nicht verwendet werden, um Autorisierungsentscheidungen zu treffen.  Er kann jedoch für Hinweise auf den Benutzernamen und auf der Benutzeroberfläche als Benutzername verwendet werden. Der Bereich `profile` ist erforderlich, um diesen Anspruch zu empfangen. Er ist nur in v2.0-Token vorhanden. |
| `name` | String | Ein lesbarer Wert, der den Antragsteller des Tokens angibt. Der Wert ist nicht zwingend eindeutig, kann geändert werden und dient nur zu Anzeigezwecken. Der Bereich `profile` ist erforderlich, um diesen Anspruch zu empfangen. |
| `scp` | Zeichenfolge, eine durch Leerzeichen getrennte Liste von Bereichen | Die von Ihrer Anwendung verfügbar gemachte Gruppe von Bereichen, für die die Clientanwendung eine Einwilligung angefordert (und empfangen) hat. Ihre App sollte überprüfen, ob diese Bereiche gültige, von Ihrer App verfügbar gemachte Bereiche sind, und Autorisierungsentscheidungen basierend auf dem Wert der Bereiche treffen. Wird nur für [Benutzertoken](#user-and-application-tokens) verwendet. |
| `roles` | Array von Zeichenfolgen, eine Liste der Berechtigungen | Die von Ihrer Anwendung verfügbar gemachte Gruppe von Berechtigungen, für die der anfordernden Anwendung bzw. dem anfordernden Benutzer die Berechtigung zum Aufrufen gewährt wurde. Bei [Anwendungstoken](#user-and-application-tokens) wird dieser Anspruch während des Flows für Clientanmeldeinformationen ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) anstelle von Benutzerbereichen verwendet.  Bei [Benutzertoken](#user-and-application-tokens) wird dieser Wert mit den Rollen aufgefüllt, die dem Benutzer für die Zielanwendung zugewiesen wurden. |
| `wids` | Array von [RoleTemplateID](../roles/permissions-reference.md#role-template-ids)-GUIDs | Gibt die mandantenweiten Rollen an, die diesem Benutzer aus dem Abschnitt mit Rollen auf der Seite[Administratorrollen](../roles/permissions-reference.md#role-template-ids) zugewiesen wurden.  Dieser Anspruch wird über die `groupMembershipClaims`-Eigenschaft des [Anwendungsmanifests](reference-app-manifest.md) anwendungsspezifisch konfiguriert.  Der Wert muss auf „All“ oder „DirectoryRole“ festgelegt werden.  Ist in Token, die über den impliziten Flow abgerufen wurden, aufgrund von Beschränkungen der Tokenlänge u. U. nicht enthalten. |
| `groups` | JSON-Array von GUIDs | Enthält die Objekt-IDs, die die Gruppenmitgliedschaften des Antragstellers darstellen. Diese Werte sind eindeutig (siehe „Object ID“) und eignen sich zum sicheren Verwalten des Zugriffs, z.B. für das Erzwingen der Autorisierung für den Zugriff auf eine Ressource. Die im Anspruch „groups“ enthaltenen Gruppen werden über die `groupMembershipClaims`-Eigenschaft des [Anwendungsmanifests](reference-app-manifest.md) anwendungsspezifisch konfiguriert. Mit dem Wert „Null“ werden alle Gruppen ausgeschlossen. Beim Wert „SecurityGroup“ sind nur Mitglieder von Active Directory-Sicherheitsgruppen enthalten. Beim Wert „All“ sind sowohl Sicherheitsgruppen als auch Microsoft 365-Verteilerlisten enthalten. <br><br>Informationen zur Verwendung des Anspruchs `groups` mit impliziter Gewährung finden Sie unter dem Anspruch `hasgroups`. <br>Wenn bei anderen Flows die Anzahl von Gruppen, denen der Benutzer angehört, einen Grenzwert überschreitet (150 für SAML, 200 für JWT), wird den Anspruchsquellen, die auf den Microsoft Graph-Endpunkt mit der Liste der Gruppen für den Benutzer verweisen, ein Überschreitungsanspruch hinzugefügt. |
| `hasgroups` | Boolean | Ist immer auf `true` festgelegt (sofern vorhanden) und gibt an, dass der Benutzer mindestens einer Gruppe angehört. Wird anstelle des Anspruchs `groups` für JWTs in Flows mit impliziter Gewährung verwendet, wenn der Anspruch für vollständige Gruppen das URI-Fragment über die URL-Längenbeschränkung (derzeit 6 oder mehr Gruppen) erweitert. Gibt an, dass der Client die Gruppen des Benutzers über die Microsoft Graph-API bestimmen soll (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`). |
| `groups:src1` | JSON-Objekt | Für Tokenanforderungen ohne Längenbeschränkung (siehe `hasgroups` oben), die aber dennoch zu groß für das Token sind, ist ein Link zur Liste der vollständigen Gruppen für den Benutzer enthalten. Für JWTs als verteilter Anspruch, für SAML als neuer Anspruch anstelle des Anspruchs `groups`. <br><br>**JWT-Beispielwert**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | String | Der Prinzipal, für den das Token Informationen zusichert, z. B. der Benutzer einer App. Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Er kann für die sichere Durchführung von Autorisierungsüberprüfungen verwendet werden, z.B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Er kann auch als Schlüssel in Datenbanktabellen verwendet werden. Da der Antragsteller immer in den Token vorhanden ist, die Azure AD ausstellt, wird die Nutzung dieses Werts in einem allgemeinen Autorisierungssystem empfohlen. Der Antragsteller ist allerdings ein paarweiser Bezeichner: Er gilt nur für eine bestimmte Anwendungs-ID. Wenn sich ein Benutzer bei zwei verschiedenen Apps mit zwei verschiedenen Client-IDs anmeldet, erhalten diese Apps zwei unterschiedliche Werte für den Antragstelleranspruch. Dies kann abhängig von den Architektur- und Datenschutzanforderungen möglicherweise wünschenswert sein oder nicht. Siehe auch den `oid`-Anspruch (der innerhalb eines Mandanten für alle Apps immer gleich bleibt). |
| `oid` | Zeichenfolge, eine GUID | Der unveränderliche Bezeichner für ein Objekt in der Microsoft Identity Platform (in diesem Fall ein Benutzerkonto). Er kann auch verwendet werden, um Autorisierungsüberprüfungen auf sichere Weise durchzuführen, und er kann als Schlüssel in Datenbanktabellen genutzt werden. Diese ID identifiziert den Benutzer anwendungsübergreifend eindeutig: Zwei verschiedene Anwendungen, die den gleichen Benutzer anmelden, erhalten den gleichen Wert im `oid`-Anspruch. Dies bedeutet, dass `oid` beim Senden von Abfragen an Microsoft-Onlinedienste wie Microsoft Graph verwendet werden kann. Microsoft Graph gibt diese ID als `id`-Eigenschaft für ein bestimmtes [Benutzerkonto](/graph/api/resources/user) zurück. Da mit `oid` mehrere Apps Benutzer korrelieren können, ist der `profile`-Bereich erforderlich, um diesen Anspruch zu erhalten. Beachten Sie Folgendes: Wenn ein einzelner Benutzer in mehreren Mandanten vorhanden ist, enthält der Benutzer in jedem Mandanten eine andere Objekt-ID. Sie werden als unterschiedliche Konten betrachtet, obwohl sich der Benutzer bei jedem Konto mit den gleichen Anmeldeinformationen anmeldet. |
| `tid` | Zeichenfolge, eine GUID | Stellt den Azure AD-Mandanten dar, aus dem der Benutzer stammt. Bei Geschäfts- und Schulkonten ist die GUID die unveränderliche Mandanten-ID der Organisation, zu der der Benutzer gehört. Für persönliche Konten lautet der Wert `9188040d-6c67-4c5b-b112-36a304b66dad`. Der Bereich `profile` ist erforderlich, um diesen Anspruch zu empfangen. |
| `unique_name` | String | Ist nur in v1.0-Token vorhanden. Ein lesbarer Wert, der Aufschluss über den Antragsteller des Tokens gibt. Es wird nicht garantiert, dass der Wert innerhalb eines Mandanten eindeutig ist. Er sollte daher nur zu Anzeigezwecken verwendet werden. |
| `uti` | Nicht transparente Zeichenfolge | Ein interner Anspruch, der von Azure zum erneuten Überprüfen von Token verwendet wird. Ressourcen sollten diesen Anspruch nicht verwenden. |
| `rh` | Nicht transparente Zeichenfolge | Ein interner Anspruch, der von Azure zum erneuten Überprüfen von Token verwendet wird. Ressourcen sollten diesen Anspruch nicht verwenden. |
| `ver` | Zeichenfolge, `1.0` oder `2.0` | Gibt die Version des Zugriffstokens an. |

**Gruppenüberschreitungsanspruch**

Um sicherzustellen, dass die Tokengröße die Grenzwerte für HTTP-Header nicht überschreitet, schränkt Azure AD die Anzahl der im Gruppenanspruch enthaltenen Objekt-IDs ein. Wenn ein Benutzer Mitglied in mehr Gruppen ist, als es die Überschreitungsgrenze (150 für SAML-Token, 200 für JWT-Token und nur 6 bei Ausstellung über den impliziten Flow) zulässt, gibt Azure AD den Gruppenanspruch im Token nicht aus. Stattdessen ist ein Überschreitungsanspruch im Token enthalten, der der Anwendung anzeigt, dass die Microsoft Graph-API abgefragt werden soll, um die Gruppenmitgliedschaft des Benutzers abzurufen.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

Sie können die im Ordner [App-Erstellungsskripts](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) verfügbare Datei `BulkCreateGroups.ps1` verwenden, um Überschreitungsszenarien zu testen.

#### <a name="v10-basic-claims"></a>Grundlegende v1.0-Ansprüche

Die folgenden Ansprüche werden ggf. in v1.0-Token eingeschlossen, sind standardmäßig aber nicht in v2.0-Token enthalten. Wenn Sie v2.0 verwenden und einen dieser Ansprüche benötigen, können Sie ihn mithilfe von [optionalen Ansprüchen](active-directory-optional-claims.md) anfordern.

| Anspruch | Format | BESCHREIBUNG |
|-----|--------|-------------|
| `ipaddr`| String | Die IP-Adresse, mit der sich der Benutzer authentifiziert hat. |
| `onprem_sid`| Zeichenfolge, im [SID-Format](/windows/desktop/SecAuthZ/sid-components) | In Fällen, in denen der Benutzer über eine lokale Authentifizierung verfügt, gibt dieser Anspruch die SID an. Sie können `onprem_sid` für die Autorisierung in älteren Anwendungen verwenden.|
| `pwd_exp`| Ganze Zahl, ein UNIX-Zeitstempel | Gibt an, wann das Kennwort des Benutzers abläuft. |
| `pwd_url`| String | Eine URL, an die Benutzer zum Zurücksetzen ihres Kennworts weitergeleitet werden können. |
| `in_corp`| boolean | Signalisiert, ob sich der Client aus dem Unternehmensnetzwerk anmeldet. Andernfalls ist der Anspruch nicht enthalten. |
| `nickname`| String | Ein zusätzlicher Name für den Benutzer, der vom Vor- oder Nachnamen abweicht.|
| `family_name` | String | Gibt den Nachnamen des Benutzers entsprechend der Definition im Benutzerobjekt an. |
| `given_name` | String | Gibt den Vornamen des Benutzers entsprechend der Definition im Benutzerobjekt an. |
| `upn` | String | Der Benutzername des Benutzers. Hierbei kann es sich um eine Telefonnummer, E-Mail-Adresse oder unformatierte Zeichenfolge handeln. Dieser Anspruch sollte nur zu Anzeigezwecken und zum Bereitstellen von Hinweisen zum Benutzernamen in Szenarien mit erneuter Authentifizierung verwendet werden. |

#### <a name="the-amr-claim"></a>`amr`-Anspruch

Microsoft-Identitäten können auf verschiedene Arten authentifiziert werden, die möglicherweise für Ihre Anwendung relevant sind. Der `amr`-Anspruch ist ein Array, das mehrere Elemente (beispielsweise `["mfa", "rsa", "pwd"]`) für eine Authentifizierung enthalten kann, bei der sowohl ein Kennwort als auch die Authenticator-App verwendet wurden.

| Wert | BESCHREIBUNG |
|-----|-------------|
| `pwd` | Kennwortauthentifizierung, entweder das Microsoft-Kennwort eines Benutzers oder der geheime Clientschlüssel einer App. |
| `rsa` | Die Authentifizierung erfolgt basierend auf der Prüfung eines RSA-Schlüssels, beispielsweise mit der [Microsoft Authenticator-App](https://aka.ms/AA2kvvu). Dies beinhaltet auch, ob die Authentifizierung von einem selbst signierten JWT mit einem X509-Zertifikat im Besitz eines Diensts durchgeführt wurde. |
| `otp` | Einmalkennung mit einer E-Mail oder SMS. |
| `fed` | Eine Verbundauthentifizierungsassertion (z. B. JWT oder SAML) wurde verwendet. |
| `wia` | Integrierte Windows-Authentifizierung |
| `mfa` | Die [mehrstufige Authentifizierung](../authentication/concept-mfa-howitworks.md) wurde verwendet. Wenn dieser Wert vorhanden ist, werden auch andere Authentifizierungsmethoden eingeschlossen. |
| `ngcmfa` | Entspricht `mfa` und wird zum Bereitstellen bestimmter erweiterter Anmeldeinformationstypen verwendet. |
| `wiaormfa`| Der Benutzer hat sich mit Windows oder MFA-Anmeldeinformationen authentifiziert. |
| `none` | Es wurde keine Authentifizierung durchgeführt. |

## <a name="validating-tokens"></a>Überprüfen von Token

Nicht alle Apps sollten Token überprüfen. Apps sollten nur in bestimmten Szenarien ein Token überprüfen:

* [Web-APIs](quickstart-configure-app-expose-web-apis.md) müssen Zugriffstoken überprüfen, die von einem Client an sie gesendet werden.  Sie dürfen nur Token akzeptieren, die ihren Anspruch `aud` enthalten.
* Vertrauliche Web-Apps wie ASP.NET Core müssen ID-Token überprüfen, die über den Browser des Benutzers im Hybridflow an sie gesendet werden, bevor sie den Zugriff auf die Daten eines Benutzers gewähren oder eine Sitzung einrichten.

Wenn kein oben genanntes Szenario zutrifft, kann Ihre Anwendung nicht von der Überprüfung des Tokens profitieren und stellt möglicherweise ein Sicherheits- und Zuverlässigkeitsrisiko dar, wenn Entscheidungen anhand der Gültigkeit des Tokens getroffen werden.  Öffentliche Clients wie native Apps oder SPAs profitieren nicht von der Tokenüberprüfung. Die App kommuniziert direkt mit dem IDP, sodass die Gültigkeit der Token durch den SSL-Schutz sichergestellt ist.

APIs und Web-Apps dürfen nur Token mit dem Anspruch `aud` überprüfen, der mit ihrer Anwendung übereinstimmt. Für andere Ressourcen gibt es möglicherweise benutzerdefinierte Tokenüberprüfungsregeln. Token für Microsoft Graph werden beispielsweise aufgrund ihres proprietären Formats nicht anhand dieser Regeln überprüft. Das Überprüfen und Akzeptieren von Token, die für eine andere Ressource bestimmt sind, ist ein Beispiel für das sogenannte [verwirrte Stellvertreterproblem](https://cwe.mitre.org/data/definitions/441.html) (confused deputy problem).

Wenn Ihre Anwendung ein ID-Token (id_token) oder ein Zugriffstoken (access_token) entsprechend dem oben Gesagten überprüfen muss, sollte Ihre App zunächst Signatur und Aussteller des Tokens anhand der Werte im OpenID Discovery-Dokument überprüfen. Die mandantenunabhängige Version des Dokuments finden Sie beispielsweise unter [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Die folgenden Informationen richten sich an Leser, die den zugrundeliegenden Prozess verstehen möchten. Azure AD-Middleware verfügt über integrierte Funktionen zum Überprüfen von Zugriffstoken. Sie können auch unsere [Beispiele](sample-v2-code.md) nach einer gewünschten Programmiersprache durchsuchen. Für die JWT-Überprüfung stehen zudem verschiedene Open Source-Bibliotheken von Drittanbietern zur Verfügung. Für nahezu jede Plattform und Programmiersprache ist mindestens eine Option verfügbar. Weitere Informationen zu Azure AD-Authentifizierungsbibliotheken sowie Codebeispiele finden Sie unter [Authentifizierungsbibliotheken](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Überprüfen der Signatur

Ein JWT enthält drei Segmente, die durch das Zeichen `.` getrennt sind. Das erste Segment wird als **Header**, das zweite als **Text** und das dritte als **Signatur** bezeichnet. Mit dem Signatursegment kann die Authentizität des Tokens überprüft werden, sodass es für Ihre App als vertrauenswürdig eingestuft werden kann.

Von Azure AD ausgestellte Token werden mit asymmetrischen Verschlüsselungsalgorithmen nach Industriestandard (z. B. RS256) signiert. Der Header des JWT enthält Informationen zum Schlüssel und zur Verschlüsselungsmethode, die zum Signieren des Tokens verwendet werden:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

Der `alg`-Anspruch gibt den Algorithmus an, mit dem das Token signiert wurde, und der `kid`-Anspruch gibt den bestimmten öffentlichen Schlüssel an, mit dem das Token überprüft wurde.

Zu einem beliebigen Zeitpunkt signiert Azure AD unter Umständen ein ID-Token mithilfe eines bestimmten Satzes von Paaren aus öffentlichen und privaten Schlüsseln. Azure AD wechselt regelmäßig durch die möglichen Sätze von Schlüsseln. Ihre App muss also auf die automatische Verarbeitung dieser Schlüsseländerungen ausgelegt sein. Die von Azure AD verwendeten öffentlichen Schlüssel sollten alle 24 Stunden auf Änderungen überprüft werden.

Sie können die Signaturschlüsseldaten, die zum Überprüfen der Signatur erforderlich sind, mithilfe des [OpenID Connect-Metadatendokuments](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) abrufen. Dieses Dokument befindet sich unter:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Testen Sie diese [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) in einem Browser!

Dieses Metadatendokument:

* Ist ein JSON-Objekt, das zahlreiche nützliche Informationen enthält, beispielsweise den Speicherort der verschiedenen Endpunkte, die zum Ausführen der OpenID Connect-Authentifizierung erforderlich sind.
* Enthält einen `jwks_uri`, der den Speicherort des Satzes von öffentlichen Schlüsseln zum Signieren von Token angibt. Der JSON-Webschlüssel (JWK) unter `jwks_uri` enthält alle Informationen zu den zu diesem Zeitpunkt verwendeten öffentlichen Schlüsseln.  Das JWK-Format wird in [RFC 7517](https://tools.ietf.org/html/rfc7517) beschrieben.  Ihre App kann mit dem Anspruch `kid` im JWT-Header auswählen, welcher öffentliche Schlüssel in diesem Dokument zum Signieren eines bestimmten Tokens verwendet wurde. Sie kann anschließend die Signaturüberprüfung mithilfe des korrekten öffentlichen Schlüssels und des angegebenen Algorithmus ausführen.

> [!NOTE]
> Es wird empfohlen, Ihr Token mithilfe des Anspruchs `kid` zu überprüfen. Während v1.0-Token sowohl den Anspruch `x5t` als auch den Anspruch `kid` enthalten, können v2.0-Token nur den Anspruch `kid` enthalten.

Die Ausführung der Signaturüberprüfung wird in diesem Dokument nicht erläutert. Es stehen jedoch viele Open-Source-Bibliotheken mit hilfreichen Informationen zur Verfügung, falls Sie dabei Unterstützung benötigen.  Microsoft Identity Platform bietet jedoch eine Tokensignaturerweiterung für die Standards – benutzerdefinierte Signaturschlüssel.

Wenn Ihre App durch die Verwendung der Funktion [Anspruchszuordnung](active-directory-claims-mapping.md) über benutzerdefinierte Signaturschlüssel verfügt, müssen Sie einen `appid`-Abfrageparameter mit der App-ID anfügen, um einen `jwks_uri` abzurufen, der auf die Signaturschlüsselinformationen Ihrer App verweist, die für die Überprüfung verwendet werden sollen. Beispiel: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` enthält einen `jwks_uri` von `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Anspruchsbasierte Autorisierung

Diesen Schritt bestimmt die Geschäftslogik Ihrer Anwendung. Im Folgenden finden Sie einige allgemeine Autorisierungsmethoden.

* Überprüfen Sie den `scp`- oder `roles`-Anspruch, um sicherzustellen, dass alle vorhandenen Bereiche mit den von Ihrer API verfügbar gemachten Bereichen übereinstimmen und der Client die angeforderte Aktion durchführen kann.
* Stellen Sie mithilfe des `appid`-Anspruchs sicher, dass der aufrufende Client Ihre API aufrufen darf.
* Überprüfen Sie den Authentifizierungsstatus des aufrufenden Clients mit `appidacr`. Er sollte nicht 0 sein, wenn öffentlichen Clients das Aufrufen Ihrer API nicht erlaubt ist.
* Stellen Sie anhand einer Liste früherer `nonce`-Ansprüche sicher, dass das Token nicht wiedergegeben wird.
* Stellen Sie sicher, dass der `tid`-Anspruch einem Mandanten entspricht, dem das Aufrufen Ihrer API erlaubt ist.
* Verwenden Sie den `amr`-Anspruch, um zu überprüfen, ob der Benutzer die MFA ausgeführt hat. Dies sollte durch [bedingten Zugriff](../conditional-access/overview.md) erzwungen werden.
* Wenn Sie die `roles`- oder `groups`-Ansprüche im Zugriffstoken angefordert haben, stellen Sie sicher, dass der Benutzer der Gruppe angehört, der das Ausführen dieser Aktion erlaubt ist.
  * Bei Token, die mit dem impliziten Flow abgerufen werden, müssen Sie wahrscheinlich den [Microsoft Graph](https://developer.microsoft.com/graph/) nach diesen Daten abfragen, weil sie oft zu groß für das Token sind.

## <a name="user-and-application-tokens"></a>Benutzer- und Anwendungstoken

Ihre Anwendung kann Token für einen Benutzer (der in der Regel behandelte Flow) oder direkt von einer Anwendung (über den [Flow für Clientanmeldeinformationen](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)) empfangen. Diese nur für die App geltenden Token geben an, dass der Aufruf von einer Anwendung stammt und nicht von einem Benutzer unterstützt wird. Diese Token werden weitgehend gleich behandelt:

* Verwenden Sie `roles`, um die Berechtigungen anzuzeigen, die dem Antragsteller des Tokens erteilt wurden.
* Verwenden Sie `oid` oder `sub`, um zu überprüfen, ob der aufrufende Dienstprinzipal der erwartete Dienstprinzipal ist.

Wenn Ihre App zwischen reinen App-Zugriffstoken und Zugriffstoken für Benutzer unterscheiden muss, verwenden Sie den optionalen `idtyp`-[Anspruch](active-directory-optional-claims.md).  Durch Hinzufügen des `idtyp`-Anspruchs zum Feld `accessToken` und Überprüfen auf den Wert `app` können Sie reine App-Zugriffstoken erkennen.  ID-Token und Zugriffstoken für Benutzer enthalten den `idtyp`-Anspruch nicht.

## <a name="token-revocation"></a>Widerrufen von Token

Aktualisierungstoken können jederzeit aus vielen verschiedenen Gründen ungültig werden. Hierbei gibt es zwei Hauptkategorien: Zeitüberschreitungen und Widerrufe.

### <a name="token-timeouts"></a>Tokenzeitüberschreitungen

Mit der [Konfiguration der Tokenlebensdauer](active-directory-configurable-token-lifetimes.md) kann die Lebensdauer von Aktualisierungstoken geändert werden.  Es ist bei einigen Token normal und erwartungsgemäß, dass sie nicht verwendet werden (wenn z. B. der Benutzer die App drei Monate lang nicht öffnet) und daher ablaufen.  Für Apps können Szenarien eintreten, in denen der Anmeldeserver ein Aktualisierungstoken aufgrund seines Alters ablehnt.

* MaxInactiveTime: Wenn das Aktualisierungstoken innerhalb des von MaxInactiveTime vorgegebenen Zeitraums nicht verwendet wurde, ist es nicht mehr gültig.
* MaxSessionAge: Wenn „MaxAgeSessionMultiFactor“ oder „MaxAgeSessionSingleFactor“ auf einen anderen Wert als die Standardeinstellung (Until-revoked) festgelegt wurden, ist eine erneute Authentifizierung erforderlich, nachdem der in „MaxAgeSession*“ festgelegte Zeitraum abgelaufen ist.
* Beispiele:
  * Der MaxInactiveTime-Wert des Mandanten beträgt fünf Tage, und der Benutzer war eine Woche lang im Urlaub. Aus diesem Grund hat Azure AD sieben Tage lang keine neue Tokenanforderung vom Benutzer erhalten. Bei der nächsten Anforderung eines neuen Tokens durch den Benutzer wurde das Aktualisierungstoken widerrufen, sodass der Benutzer seine Anmeldeinformationen erneut eingeben muss.
  * Eine sensible Anwendung verfügt über einen MaxAgeSessionSingleFactor-Wert von einem Tag. Wenn sich ein Benutzer am Montag anmeldet, muss er sich am Dienstag (nach Ablauf von 25 Stunden) erneut authentifizieren.

### <a name="revocation"></a>Widerruf

Aktualisierungstoken können vom Server aufgrund einer Änderung der Anmeldeinformationen oder aufgrund der Verwendung oder einer Administratoraktion widerrufen werden.  Aktualisierungstoken werden in zwei Klassen unterteilt: solche, die für vertrauliche Clients ausgestellt werden (die Spalte ganz rechts), und Aktualisierungstoken, die für öffentliche Clients (alle anderen Spalten) ausgestellt werden.

| Change | Kennwortbasiertes Cookie | Kennwortbasiertes Token | Nicht kennwortbasiertes Cookie | Nicht kennwortbasiertes Token | Vertrauliches Clienttoken |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Kennwort läuft ab | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv |
| Kennwort wird vom Benutzer geändert | Widerrufen | Widerrufen | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv |
| Benutzer verwendet SSPR | Widerrufen | Widerrufen | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv |
| Administrator setzt Kennwort zurück | Widerrufen | Widerrufen | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv |
| Benutzer widerruft Aktualisierungstoken [über PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Widerrufen | Widerrufen | Widerrufen | Widerrufen | Widerrufen |
| Administrator widerruft alle Aktualisierungstoken für einen Benutzer [über PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Widerrufen | Widerrufen |Widerrufen | Widerrufen | Widerrufen |
| Einmaliges Abmelden ([v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v2.0](v2-protocols-oidc.md#single-sign-out)) im Web | Widerrufen | Bleibt aktiv | Widerrufen | Bleibt aktiv | Bleibt aktiv |

#### <a name="non-password-based"></a>Nicht kennwortbasiert

Bei einer *Nicht kennwortbasierten* Anmeldung hat der Benutzer kein Kennwort eingegeben, um sich anzumelden. Beispiele für die nicht kennwortbasierte Anmeldung:

- Gesichtserkennung mit Windows Hello
- FIDO2-Schlüssel
- SMS
- Sprache
- PIN

Weitere Informationen zu primären Aktualisierungstoken finden Sie unter [Was ist ein primäres Aktualisierungstoken (Primary Refresh Token, PRT)?](../devices/concept-primary-refresh-token.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [`id_tokens` in Azure AD](id-tokens.md).
* Erfahren Sie mehr über Berechtigungen und die Einwilligung ([v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
