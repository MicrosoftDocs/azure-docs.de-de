---
title: 'Azure Active Directory-REST-API: Authentifizierung'
description: Verwenden von Azure Active Directory zum Authentifizieren bei Azure App Configuration über die REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930516"
---
# <a name="azure-active-directory-authentication"></a>Authentifizierung über Azure Active Directory

Sie können HTTP-Anforderungen mithilfe des `Bearer`-Authentifizierungsschemas authentifizieren, wobei ein Token aus Azure Active Directory (Azure AD) abgerufen wird. Diese Anforderungen müssen Sie über TLS (Transport Layer Security) übertragen.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen den Prinzipal, der zum Anfordern eines Azure AD-Tokens verwendet wird, einer der anwendbaren [Azure App Configuration-Rollen](./rest-api-authorization-azure-ad.md) zuweisen.

Geben Sie jede Anforderung mit allen für die Authentifizierung erforderlichen HTTP-Headern an. Es gilt folgende Mindestanforderung:

|  Anforderungsheader | BESCHREIBUNG  |
| --------------- | ------------ |
| `Authorization` | Authentifizierungsinformationen, die für das `Bearer`-Schema erforderlich sind. |

**Beispiel:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD-Tokenabruf

Vor dem Abrufen eines Azure AD-Tokens muss festgelegt werden, welcher Benutzer sich authentifizieren soll, für welche Zielgruppe das Token angefordert wird und welcher Azure AD-Endpunkt (Autorität) verwendet wird.

### <a name="audience"></a>Zielgruppe

Fordern Sie das Azure AD-Token mit einer passenden Zielgruppe an. Verwenden Sie für Azure App Configuration eine der folgenden Zielgruppen. Die Zielgruppe kann auch als die *Ressource* bezeichnet werden, für die das Token angefordert wird.

- {Konfigurationsspeichername}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> Wenn `{configurationStoreName}.azconfig.io` die angeforderte Zielgruppe ist, muss sie genau mit dem Anforderungsheader `Host` (unter Berücksichtigung der Groß-/Kleinschreibung) übereinstimmen, der zum Senden der Anforderung verwendet wird.

### <a name="azure-ad-authority"></a>Azure AD-Autorität

Die Azure AD-Autorität ist der Endpunkt, den Sie zum Abrufen eines Azure AD-Tokens verwenden. Sie weist das Format `https://login.microsoftonline.com/{tenantId}` auf. Das Segment `{tenantId}` verweist auf die Azure AD-Mandanten-ID, zu der der Benutzer oder die Anwendung gehört, der bzw. die die Authentifizierung durchführt.

### <a name="authentication-libraries"></a>Authentifizierungsbibliotheken

Azure umfasst eine Reihe von Bibliotheken, die sogenannten Azure Active Directory-Authentifizierungsbibliotheken, die das Abrufen eines Azure AD-Tokens vereinfachen sollen. Diese Bibliotheken sind in Azure für mehrere Sprachen konzipiert. Weitere Informationen finden Sie in der [Dokumentation](../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="errors"></a>Errors

Die folgenden Fehler können möglicherweise auftreten.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Grund:** Sie haben den „Authorization“-Anforderungsheader beim `Bearer`-Schema nicht angegeben.

**Lösung:** Geben Sie einen gültigen `Authorization`-HTTP-Anforderungsheader an.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Grund:** Das Azure AD-Token ist ungültig.

**Lösung:** Rufen Sie ein Azure AD-Token von der Azure AD-Autorität ab, und stellen Sie sicher, dass Sie die passende Zielgruppe verwenden.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Grund:** Das Azure AD-Token ist ungültig.

**Lösung:** Rufen Sie ein Azure AD-Token von der Azure AD-Autorität ab. Stellen Sie sicher, dass der Azure AD-Mandant dem Abonnement zugeordnet ist, zu dem der Konfigurationsspeicher gehört. Dieser Fehler kann auftreten, wenn der Prinzipal zu mehreren Azure AD-Mandanten gehört.