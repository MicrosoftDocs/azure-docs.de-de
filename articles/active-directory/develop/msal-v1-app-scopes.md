---
title: Bereiche für v1. 0-Apps (MSAL) | Azure
description: Hier finden Sie Informationen zu den Geltungsbereichen für eine v1.0-Anwendung in der Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7e2fcf2dc0dc53038b82bbf182cb12f580d88357
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583585"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Geltungsbereiche für eine Web-API, die v1.0-Token akzeptiert

OAuth2-Berechtigungen sind Berechtigungsbereiche, die eine Azure AD-Web-API-Anwendung (Ressource) für Entwickler (v1.0) für Clientanwendungen zur Verfügung stellt. Diese Berechtigungsbereiche können Clientanwendungen im Zuge der Zustimmung gewährt werden. Weitere Informationen finden Sie im Abschnitt über `oauth2Permissions` in der [Azure Active Directory-Anwendungsmanifestreferenz](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Geltungsbereiche, mit denen der Zugriff auf bestimmte OAuth2-Berechtigungen einer v1.0-Anwendung angefordert wird

Zum Abrufen von Token für bestimmte Geltungsbereiche einer v1.0-Anwendung (z. B. die Microsoft Graph-API unter https://graph.microsoft.com) ) müssen Sie Geltungsbereiche erstellen, indem Sie einen gewünschten Ressourcenbezeichner mit einer gewünschten OAuth2-Berechtigung für die entsprechende Ressource verketten.

Beispiel für den Zugriff auf eine v1. 0-Web-API im Auftrag des Benutzers mit dem App-ID-URI `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Wenn Sie mit MSAL.NET für Azure AD Lese- und Schreibvorgänge über die Microsoft Graph-API (https:\//graph.microsoft.com/) ausführen möchten, erstellen Sie wie in den folgenden Beispielen gezeigt eine Liste von Geltungsbereichen:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Zum Schreiben des Geltungsbereichs für die Azure Resource Manager-API (https:\//management.core.windows.net/) müssen Sie den folgenden Geltungsbereich anfordern (beachten Sie die beiden Schrägstriche):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Sie müssen zwei Schrägstriche verwenden, weil die Azure Resource Manager-API einen Schrägstrich im Zielgruppenanspruch (aud) erwartet und ein weiterer Schrägstrich erforderlich ist, um den API-Namen vom Geltungsbereich zu trennen.

Die von Azure AD verwendete Logik lautet wie folgt:

- Für einen Endpunkt zu einer Active Directory-Authentifizierungsbibliothek (v1.0) mit einem v1.0-Zugriffstoken (einzige Möglichkeit): aud=resource
- Für MSAL (Microsoft Identity Platform) bei der Abfrage eines Zugriffstokens für eine Ressource, die v2.0-Token akzeptiert: `aud=resource.AppId`
- Für einen MSAL-Endpunkt (v2.0), der ein Zugriffstoken für eine Ressource abfragt, die ein v1.0-Zugriffstoken akzeptiert (wie im Fall oben), analysiert Azure AD die gewünschte Zielgruppe aus dem angeforderten Geltungsbereich, indem alles vor dem letzten Schrägstrich als Ressourcenbezeichner interpretiert wird. Wenn „https:\//database.windows.net“ die Zielgruppe „https:\//database.windows.net/“ erwartet, müssen Sie daher den Geltungsbereich „https:\//database.windows.net//.default“ anfordern. Siehe auch GitHub-Problem [#747: Resource url's trailing slash is omitted, which caused sql auth failure](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747) (Bei der URL der Ressource wurde der nachgestellte Schrägstrich ausgelassen, wodurch bei der SQL-Authentifizierung ein Fehler aufgetreten ist).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Geltungsbereiche, mit denen der Zugriff auf alle Berechtigungen einer v1.0-Anwendung angefordert wird

Wenn Sie ein Token für alle statischen Geltungsbereiche einer v1.0-Anwendung abrufen möchten, fügen Sie an den App-ID-URI der API die Zeichenfolge „.default“ an:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Für Clientanmeldeinformationsflows/Daemon-Apps anzufordernde Geltungsbereiche

Bei einem Clientanmeldeinformations-Flow lautet der zu übergebende Geltungsbereich ebenfalls `/.default`. Dies bedeutet für Azure AD: Alle Berechtigungen auf Anwendungsebene, denen der Administrator bei der Anwendungsregistrierung zugestimmt hat.
