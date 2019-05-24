---
title: Microsoft Identity Platform – Referenz zu ID-Token | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über die Verwendung von ID-Token, die von den Azure AD v1.0- und Microsoft Identity Platform (v2.0)-Endpunkten ausgegeben werden.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms:custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25408b2120a9ac9f38e7959ef8e9dbbb34df7c2b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962568"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity Platform – ID-Token

`id_tokens` werden als Teil eines [OpenID Connect](v1-protocols-openid-connect-code.md)-Flusses an die Clientanwendung gesendet. Sie können zusammen mit einem Zugriffstoken oder anstelle eines Zugriffstokens gesendet werden und werden vom Client zur Authentifizierung des Benutzers verwendet.

## <a name="using-the-idtoken"></a>Verwenden des ID-Tokens

ID-Token sollten verwendet werden, um zu überprüfen, ob ein Benutzer der ist, der er vorgibt zu sein, und um zusätzliche nützliche Informationen über ihn zu erhalten. Sie sollten nicht für die Autorisierung anstelle eines [Zugriffstokens](access-tokens.md) verwendet werden. Die bereitgestellten Ansprüche können für UX in Ihrer Anwendung, das Versehen einer Datenbank mit Schlüsseln und die Bereitstellung von Zugriff auf die Clientanwendung verwendet werden.

## <a name="claims-in-an-idtoken"></a>Ansprüche in einem ID-Token

`id_tokens` für Microsoft Identity sind [JWTs](https://tools.ietf.org/html/rfc7519), d.h. sie bestehen aus einem Header, Nutzlast und einer Signatur. Sie können den Header und die Signatur verwenden, um die Authentizität des Tokens zu überprüfen, während die Nutzlast die Informationen zu dem von Ihrem Client angeforderten Benutzer enthält. Sofern nicht anders angegeben, sind alle hier aufgeführten Ansprüche sowohl in v1.0- als auch in v2.0-Token enthalten.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Zeigen Sie dieses v1. 0-Beispieltoken in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q) an.

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Zeigen Sie dieses v2. 0-Beispieltoken in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw) an.

### <a name="header-claims"></a>Headeransprüche

|Anspruch | Format | BESCHREIBUNG |
|-----|--------|-------------|
|`typ` | Zeichenfolge – immer „JWT“ | Gibt an, dass das Token ein JWT ist.|
|`alg` | Zeichenfolge | Gibt den Algorithmus an, mit dem das Token signiert wurde. Beispiel: "RS256" |
|`kid` | Zeichenfolge | Der Fingerabdruck des öffentlichen Schlüssels, der verwendet wird, um dieses Token zu signieren. Wird in v1. 0- und v2. 0-`id_tokens` ausgegeben. |
|`x5t` | Zeichenfolge | Identisch (in Verwendung und Wert) mit `kid`. Dies ist jedoch ein Legacyanspruch, der aus Kompatibilitätsgründen nur in v1.0-`id_tokens` ausgegeben wird. |

### <a name="payload-claims"></a>Nutzlastansprüche

Diese Liste zeigt die Ansprüche, die (sofern nichts anderes angegeben ist) in den meisten ID-Token standardmäßig enthalten sind.  Allerdings kann Ihre App mithilfe von [optionalen Ansprüchen](active-directory-optional-claims.md) zusätzliche Ansprüche im ID-Token anfordern.  Diese können vom `groups`-Anspruch bis zu Informationen zum Namen des Benutzers reichen.

|Anspruch | Format | BESCHREIBUNG |
|-----|--------|-------------|
|`aud` |  Zeichenfolge, ein App-ID-URI | Identifiziert den vorgesehenen Empfänger des Tokens. In `id_tokens` ist die Zielgruppe die Anwendungs-ID Ihrer App, die Ihrer App im Azure-Portal zugewiesen ist. Ihre App sollte diesen Wert überprüfen und das Token ablehnen, wenn der Wert nicht übereinstimmt. |
|`iss` |  Zeichenfolge, ein STS-URI | Identifiziert den Sicherheitstokendienst (STS), der das Token und den Azure AD-Mandanten, in dem der Benutzer authentifiziert wurde, erstellt und zurückgibt. Wenn das Token vom v2.0-Endpunkt ausgegeben wurde, endet der URI mit `/v2.0`.  Die GUID, die angibt, dass der Benutzer ein Consumer-Benutzer eines Microsoft-Kontos ist, lautet `9188040d-6c67-4c5b-b112-36a304b66dad`. Ihre App sollte ggf. den GUID-Teil des Anspruchs verwenden, um die Mandanten einzuschränken, die sich bei der App anmelden können. |
|`iat` |  Ganze Zahl, ein UNIX-Zeitstempel | „Issued At“ gibt an, wann die Authentifizierung für dieses Token erfolgt ist.  |
|`idp`|Zeichenfolge, in der Regel ein STS-URI | Der Identitätsanbieter, der den Antragsteller des Tokens authentifiziert hat. Dieser Wert ist identisch mit dem Wert des Ausstelleranspruchs, es sei denn, das Benutzerkonto ist nicht im gleichen Mandanten wie der Aussteller vorhanden (etwa Gäste). Wenn der Anspruch nicht vorhanden ist, bedeutet dies, dass stattdessen der Wert `iss` verwendet werden kann.  Für in einem Organisationskontext verwendete persönliche Konten (etwa ein zu einem Azure AD-Mandanten eingeladenes persönliches Konto) kann der `idp`-Anspruch „live.com“ oder ein STS-URI sein, der den Microsoft-Kontomandanten `9188040d-6c67-4c5b-b112-36a304b66dad` enthält. |
|`nbf` |  Ganze Zahl, ein UNIX-Zeitstempel | Der Anspruch „nbf“ (nicht vor) gibt die Zeit an, vor der das JWT NICHT für die Bearbeitung akzeptiert werden darf.|
|`exp` |  Ganze Zahl, ein UNIX-Zeitstempel | Der Anspruch „exp“ (Ablaufzeit) gibt die Ablaufzeit an, ab oder nach der das JWT NICHT für die Bearbeitung akzeptiert werden darf.  Es ist wichtig zu beachten, dass eine Ressource das Token auch vor diesem Zeitpunkt ablehnen kann (wenn beispielsweise eine Änderung der Authentifizierung erforderlich ist oder ein Tokenwiderruf erkannt wurde). |
| `c_hash`| Zeichenfolge |Der Codehash ist nur dann in ID-Token enthalten, wenn das ID-Token zusammen mit einem OAuth 2.0-Autorisierungscode ausgestellt wird. Mit seiner Hilfe kann die Authentizität eines Autorisierungscodes überprüft werden. Weitere Informationen zum Ausführen dieser Überprüfung finden Sie in der [OpenID Connect-Spezifikation](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Zeichenfolge |Der Zugriffstokenhash ist nur in ID-Token enthalten, wenn das ID-Token zusammen mit einem OAuth 2.0-Zugriffstoken ausgestellt wird. Mit seiner Hilfe kann die Authentizität eines Zugriffstokens überprüft werden. Weitere Informationen zum Ausführen dieser Überprüfung finden Sie in der [OpenID Connect-Spezifikation](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Nicht transparente Zeichenfolge | Ein interner Anspruch, der von Azure AD verwendet wird, um die Daten für die Wiederverwendung von Token aufzuzeichnen. Sollte ignoriert werden.|
|`preferred_username` | Zeichenfolge | Der primäre Benutzername, der den Benutzer darstellt. Dabei kann es sich um eine E-Mail-Adresse, eine Telefonnummer oder einen generischen Benutzernamen ohne bestimmtes Format handeln. Der Wert kann geändert werden und sich im Laufe der Zeit ändern. Da er geändert werden kann, darf dieser Wert nicht verwendet werden, um Autorisierungsentscheidungen zu treffen. Der Bereich `profile` ist erforderlich, um diesen Anspruch zu empfangen.|
|`email` | Zeichenfolge | Der Anspruch `email` ist standardmäßig für Gastkonten vorhanden, die über eine E-Mail-Adresse verfügen.  Ihre App kann den E-Mail-Anspruch für verwaltete Benutzer (unter demselben Mandanten wie die Ressource) über den [optionalen Anspruch](active-directory-optional-claims.md) `email` anfordern.  Auf dem v2.0-Endpunkt kann Ihre App auch den OpenID Connect-Bereich `email` anfordern. Sie müssen nicht sowohl den optionalen Anspruch als auch den Bereich abrufen, um den Anspruch zu erhalten.  Für den Anspruch „email“ werden nur adressierbare E-Mails aus den Profilinformationen des Benutzers unterstützt. |
|`name` | Zeichenfolge | Der `name`-Anspruch gibt einen visuell lesbaren Wert an, der den Antragsteller des Tokens identifiziert. Der Wert ist nicht zwingend eindeutig, kann geändert werden und dient nur zu Anzeigezwecken. Der Bereich `profile` ist erforderlich, um diesen Anspruch zu empfangen. |
|`nonce`| Zeichenfolge | Die Nonce stimmt mit dem Parameter überein, der in der ursprünglichen /authorize-Anforderung an den IDP enthalten ist. Wenn diese Angaben nicht übereinstimmen, sollte Ihre Anwendung das Token ablehnen. |
|`oid` | Zeichenfolge, eine GUID | Der unveränderliche Bezeichner für ein Objekt im Microsoft-Identitätssystem, in diesem Fall ein Benutzerkonto. Diese ID identifiziert den Benutzer anwendungsübergreifend eindeutig: Zwei verschiedene Anwendungen, die den gleichen Benutzer anmelden, erhalten den gleichen Wert im `oid`-Anspruch. Microsoft Graph gibt diese ID als `id`-Eigenschaft für ein bestimmtes Benutzerkonto zurück. Da mit `oid` mehrere Apps Benutzer korrelieren können, ist der `profile`-Bereich erforderlich, um diesen Anspruch zu erhalten. Beachten Sie Folgendes: Wenn ein einzelner Benutzer in mehreren Mandanten vorhanden ist, enthält der Benutzer in jedem Mandanten eine andere Objekt-ID. Sie werden als unterschiedliche Konten betrachtet, obwohl sich der Benutzer bei jedem Konto mit den gleichen Anmeldeinformationen anmeldet. |
|`roles`| Array von Zeichenfolgen | Die Rollen, die dem sich anmeldenden Benutzer zugewiesen wurden. |
|`rh` | Nicht transparente Zeichenfolge |Ein interner Anspruch, der von Azure verwendet wird, um Token erneut zu überprüfen. Sollte ignoriert werden. |
|`sub` | Zeichenfolge, eine GUID | Der Prinzipal, für den das Token Informationen zusichert, z. B. der Benutzer einer App. Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Der Antragsteller ist ein paarweiser Bezeichner: Er gilt nur für eine bestimmte Anwendungs-ID. Wenn sich ein Benutzer bei zwei verschiedenen Apps mit zwei verschiedenen Client-IDs anmeldet, erhalten diese Apps zwei unterschiedliche Werte für den Antragstelleranspruch. Dies kann – abhängig von den Architektur- und Datenschutzanforderungen – wünschenswert sein oder nicht. |
|`tid` | Zeichenfolge, eine GUID | Eine GUID, die den Azure AD-Mandanten darstellt, aus dem der Benutzer stammt. Bei Geschäfts- und Schulkonten ist die GUID die unveränderliche Mandanten-ID der Organisation, zu der der Benutzer gehört. Für persönliche Konten lautet der Wert `9188040d-6c67-4c5b-b112-36a304b66dad`. Der Bereich `profile` ist erforderlich, um diesen Anspruch zu empfangen. |
|`unique_name` | Zeichenfolge | Ein lesbarer Wert, der Aufschluss über den Antragsteller des Tokens gibt. Dieser Wert ist innerhalb eines Mandanten nicht zwingend eindeutig. Er sollte daher nur zu Anzeigezwecken verwendet werden. Wird nur in v1.0-`id_tokens` ausgegeben. |
|`uti` | Nicht transparente Zeichenfolge | Ein interner Anspruch, der von Azure verwendet wird, um Token erneut zu überprüfen. Sollte ignoriert werden. |
|`ver` | Zeichenfolge, 1.0 oder 2.0 | Gibt die Version des ID-Tokens an. |

## <a name="validating-an-idtoken"></a>Überprüfen eines ID-Tokens

Die Überprüfung eines `id_token` ist dem ersten Schritt der [Überprüfung eines Zugriffstokens](access-tokens.md#validating-tokens) ähnlich. Ihr Client sollte bestätigen, dass der richtige Aussteller das Token zurückgesendet hat und dass es nicht manipuliert wurde. Da `id_tokens` immer JWT sind, sind zahlreiche Bibliotheken zum Überprüfen dieses Tokens verfügbar. Es wird empfohlen, eine dieser Bibliotheken zu verwenden, anstatt den Vorgang selbst auszuführen.

Um das Token manuell zu überprüfen, beachten Sie die Details der Schritte unter [Überprüfen eines Zugriffstokens](access-tokens.md#validating-tokens). Nach der Überprüfung der Signatur des Tokens sollten die folgenden Ansprüche im id_token überprüft werden (dies kann auch von Ihrer Tokenüberprüfungsbibliothek ausgeführt werden):

* Zeitstempel: Die Zeitstempel `iat`, `nbf` und `exp` sollten alle vor oder nach der aktuellen Zeit liegen (je nach Bedarf). 
* Zielgruppe: Der `aud`-Anspruch sollte mit der App-ID Ihrer Anwendung übereinstimmen.
* Nonce: Der `nonce`-Anspruch in der Nutzlast muss mit dem nonce-Parameter übereinstimmen, der während der ersten Anforderung an den /authorize-Endpunkt übergeben wurde.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu [Zugriffstoken](access-tokens.md)
* Passen Sie die Ansprüche in Ihrem ID-Token mithilfe von [optionalen Ansprüchen](active-directory-optional-claims.md) an.