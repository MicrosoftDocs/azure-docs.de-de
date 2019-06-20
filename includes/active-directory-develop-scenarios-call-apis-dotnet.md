---
title: include file
description: include file
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178274"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AuthenticationResult-Eigenschaften in MSAL.NET

Diese Methoden zum Abrufen von Token geben ein `AuthenticationResult` zurück (bzw. im Fall von asynchronen Methoden ein `Task<AuthenticationResult>`).

In MSAL.NET macht `AuthenticationResult` Folgendes verfügbar:

- Das `AccessToken`, über das die Web-API auf Ressourcen zugreift. Dieser Parameter ist eine Zeichenfolge, in der Regel ein Base64-codiertes JWT. Der Client sollte jedoch niemals Zugriff auf die Informationen im Zugriffstoken erhalten. Die Stabilität des Formats ist nicht garantiert, und das Format kann für die Ressource verschlüsselt werden. Programmcode, der vom Inhalt von Zugriffstoken auf dem Client abhängig ist, ist eine der häufigsten Ursachen für Fehler und Brüche in der Clientlogik. Siehe auch [Zugriffstoken](../articles/active-directory/develop/access-tokens.md).
- Das `IdToken` für den Benutzer (dieser Parameter ist ein codiertes JWT). Siehe [ID-Token](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn` gibt das Datum und die Uhrzeit des Tokenablaufs an.
- Die `TenantId` enthält den Mandanten, in dem der Benutzer gefunden wurde. Bei Gastbenutzern (Azure AD-B2B-Szenarien) entspricht die Mandanten-ID dem Gastmandanten, nicht dem eindeutigen Mandanten.
Wenn das Token für einen Benutzer bereitgestellt wird, enthält `AuthenticationResult` auch Informationen zu diesem Benutzer. Bei Flows für vertrauliche Clients, in denen Token ohne Benutzer (für die Anwendung) angefordert werden, wird für diese Benutzerinformationen NULL zurückgegeben.
- Die `Scopes`, für die das Token ausgegeben wurde.
- Die eindeutige ID für den Benutzer.

### <a name="iaccount"></a>IAccount

MSAL.NET definiert das Konzept des Kontos (über die `IAccount`-Schnittstelle). Dieser Breaking Change sorgt für die richtige Semantik, um die Tatsache widerzuspiegeln, dass ein und derselbe Benutzer über mehrere Konten in verschiedenen Azure AD-Verzeichnissen verfügen kann. Zudem bietet MSAL.NET bessere Informationen für Gastszenarien, da Informationen zum Stammkonto bereitgestellt werden.
Das folgende Diagramm zeigt die Struktur der `IAccount`-Schnittstelle:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Die `AccountId`-Klasse identifiziert ein Konto in einem bestimmten Mandanten. Es stehen folgende Eigenschaften zur Verfügung:

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| `TenantId` | Eine Zeichenfolgendarstellung für eine GUID, bei der es sich um die ID des Mandanten handelt, in dem sich das Konto befindet. |
| `ObjectId` | Eine Zeichenfolgendarstellung für eine GUID, bei der es sich um die ID des Benutzers handelt, der Besitzer des Kontos im Mandanten ist. |
| `Identifier` | Eindeutiger Bezeichner für das Konto. `Identifier` ist die Verkettung von `ObjectId` und `TenantId`, durch ein Komma getrennt und nicht Base64-codiert. |

Die `IAccount`-Schnittstelle stellt Informationen über ein einziges Konto dar. Ein und derselbe Benutzer kann in verschiedenen Mandanten vorhanden sein. Anders gesagt: Ein Benutzer kann über mehrere Konten verfügen. Die Member sind:

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| `Username` | Eine Zeichenfolge mit dem anzeigbaren Wert im UPN-Format (UserPrincipalName), z.B. john.doe@contoso.com. Diese Zeichenfolge kann NULL sein. HomeAccountId und HomeAccountId.Identifier dagegen sind nie NULL. Diese Eigenschaft ersetzt die `DisplayableId`-Eigenschaft von `IUser` in früheren Versionen von MSAL.NET. |
| `Environment` | Eine Zeichenfolge mit dem Identitätsanbieter für dieses Konto, z.B. `login.microsoftonline.com`. Diese Eigenschaft ersetzt die `IdentityProvider`-Eigenschaft von `IUser`, mit der Ausnahme, dass `IdentityProvider` auch Informationen über den Mandanten enthielt (zusätzlich zur Cloudumgebung), der Wert hier dagegen nur den Host umfasst. |
| `HomeAccountId` | AccountId des Stammkontos für einen Benutzer. Diese Eigenschaft identifiziert den Benutzer eindeutig über mehrere Azure AD-Mandanten hinweg. |

### <a name="using-the-token-to-call-a-protected-api"></a>Verwenden des Tokens zum Aufruf einer geschützten API

Sobald das `AuthenticationResult` von MSAL zurückgegeben wurde (in `result`), müssen Sie es zum HTTP-Autorisierungsheader hinzufügen, bevor der Aufruf zum Zugriff auf die geschützte Web-API erfolgt.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```