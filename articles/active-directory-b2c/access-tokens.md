---
title: Anfordern eines Zugriffstokens – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Zugriffstoken von Azure Active Directory B2C anfordern.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 937041bbb48f112e2c8ed7d222dc7c7ef7ea8d81
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631392"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Anfordern eines Zugriffstokens in Azure Active Directory B2C

Ein *Zugriffstoken* enthält Ansprüche, mit denen Sie in Azure Active Directory B2C (Azure AD B2C) die gewährten Berechtigungen für Ihre APIs identifizieren können. Beim Aufrufen eines Ressourcenservers muss ein Zugriffstoken in der HTTP-Anforderung vorhanden sein. Zugriffstoken werden in den Antworten von Azure AD B2C als **access_token** angegeben.

In diesem Artikel wird erläutert, wie Sie ein Zugriffstoken für eine Webanwendung und eine Web-API anfordern. Weitere Informationen zu Token in Azure AD B2C finden Sie in der [Übersicht über Token in Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Web-API-Ketten (im Auftrag von) werden von Azure AD B2C nicht unterstützt.** Viele Architekturen umfassen eine Web-API, mit der eine andere Downstream-Web-API aufgerufen werden muss, wobei beide durch Azure AD B2C gesichert sind. Dieses Szenario kommt häufig bei Clients mit einem Web-API-Back-End vor, über das wiederum ein anderer Dienst aufgerufen wird. Dieses Szenario der verketteten Web-API kann mithilfe der Berechtigung für Anmeldeinformationen über den OAuth 2.0-JWT-Bearer unterstützt werden, auch bekannt als „Im Auftrag von“-Ablauf. Der „Im Auftrag von“-Ablauf ist in Azure AD B2C derzeit noch nicht implementiert.

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen Sie einen Benutzerflow](tutorial-create-user-flows.md), damit sich Benutzer bei Ihrer Anwendung registrieren und anmelden können.
- Wenn dies noch nicht erfolgt ist, [fügen Sie Ihrem Azure Active Directory B2C-Mandanten eine Web-API-Anwendung hinzu](add-web-api-application.md).

## <a name="scopes"></a>Bereiche

Bereiche ermöglichen die Verwaltung von Berechtigungen für geschützte Ressourcen. Bei der Anforderung eines Zugriffstokens müssen in der Clientanwendung im **scope** -Parameter der Anforderung die gewünschten Berechtigungen angegeben werden. Um z. B. den **Bereichswert**`read` für die API mit dem **App-ID-URI**`https://contoso.onmicrosoft.com/api` anzugeben, lautet der Bereich `https://contoso.onmicrosoft.com/api/read`.

Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. Benutzer der Web-API können beispielsweise über Lese- und Schreibzugriff oder nur über Lesezugriff verfügen. Um mehrere Berechtigungen in derselben Anforderung zu beziehen, können Sie mehrere durch Leerzeichen getrennte Einträge in einem einzelnen **scope** -Parameter der Anforderung hinzufügen.

Im folgenden Beispiel sind decodierte Bereiche in einer URL dargestellt:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Im folgenden Beispiel sind codierte Bereiche in einer URL dargestellt:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Wenn Sie mehr Bereiche als die für die Clientanwendung erteilten Berechtigungen anfordern, wird der Aufruf erfolgreich ausgeführt, wenn mindestens eine Berechtigung erteilt wird. Der **scp** -Anspruch im resultierenden Zugriffstoken wird nur mit den Berechtigungen gefüllt, die erfolgreich erteilt wurden. 

### <a name="openid-connect-scopes"></a>OpenID Connect-Bereiche

Mit dem OpenID Connect-Standard werden mehrere spezielle Bereichswerte angegeben. Die folgenden Bereiche stellen die Berechtigung für den Zugriff auf das Profil des Benutzers dar:

- **openid:** Hierdurch wird ein ID-Token angefordert.
- **offline_access:** Hierdurch wird ein Aktualisierungstoken (mithilfe von [Autorisierungscodeabläufen](authorization-code-flow.md)) angefordert.
- **00000000-0000-0000-0000-000000000000** – Mit der Verwendung der Client-ID als Bereich wird angegeben, dass für Ihre App ein Zugriffstoken erforderlich ist, das für Ihren eigenen Dienst oder die Web-API mit derselben Client-ID verwendet werden kann.

Wenn der Parameter **response_type** in einer `/authorize`-Anforderung `token` enthält, muss der Parameter **scope** mindestens einen Ressourcenbereich (außer `openid` und `offline_access`) enthalten, für den Berechtigungen erteilt werden. Andernfalls wird die `/authorize`-Anforderung abgelehnt.

## <a name="request-a-token"></a>Anfordern eines Tokens

Zur Anforderung eines Zugriffstokens benötigen Sie einen Autorisierungscode. Nachfolgend ist ein Beispiel für eine Anforderung an den Endpunkt `/authorize` für einen Autorisierungscode aufgeführt. Benutzerdefinierte Domänen werden zur Verwendung mit Zugriffstoken nicht unterstützt. Verwenden Sie die Domäne „Mandantenname.onmicrosoft.com“ in der Anforderungs-URL.

Ersetzen Sie im folgenden Beispiel diese Werte:

- `<tenant-name>`: Name des Azure AD B2C-Mandanten.
- `<policy-name>`: Name der benutzerdefinierten Richtlinie oder des Benutzerflows.
- `<application-ID>`: Anwendungs-ID der Webanwendung, die Sie zur Unterstützung des Benutzerflows registriert haben.
- `<redirect-uri>`: Der **Umleitungs-URI** , den Sie bei der Registrierung der Clientanwendung eingegeben haben.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Die Antwort mit dem Autorisierungscode sollte dem folgenden Beispiel ähneln:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Nach dem Empfang des Autorisierungscodes können Sie über diesen Code ein Zugriffstoken anfordern:

```http
POST <tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Die Antwort sollte der folgenden ähneln:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Bei Verwendung von https://jwt.ms zum Überprüfen des zurückgegebenen Zugriffstokens sollte etwa die folgende Ausgabe angezeigt werden:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zum [Konfigurieren von Token in Azure AD B2C](configure-tokens.md).
