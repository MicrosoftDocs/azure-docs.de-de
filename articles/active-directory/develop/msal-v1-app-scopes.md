---
title: Geltungsbereiche für eine v1.0-Anwendung (Microsoft Authentication Library) | Azure
description: Hier finden Sie Informationen zu den Geltungsbereichen für eine v1.0-Anwendung in der Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44aad6b2fab7e0ab2ff11d8469782b001b1f4d18
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545897"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Geltungsbereiche für eine Web-API, die v1.0-Token akzeptiert

OAuth2-Berechtigungen sind Berechtigungsbereiche, die eine Azure AD-Web-API-Anwendung (Ressource) für Entwickler (v1.0) für Clientanwendungen verfügbar macht. Diese Berechtigungsbereiche können Clientanwendungen im Zuge der Zustimmung gewährt werden. Weitere Informationen finden Sie im Abschnitt über `oauth2Permissions` in der [Azure Active Directory-Anwendungsmanifestreferenz](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Geltungsbereiche, mit denen der Zugriff auf bestimmte OAuth2-Berechtigungen einer v1.0-Anwendung angefordert wird
Wenn Sie Token für bestimmte Geltungsbereiche einer v1.0-Anwendung (z. B. Azure AD-Graph unter https://graph.windows.net) abrufen möchten, müssen Sie Geltungsbereiche erstellen, indem Sie einen gewünschten Ressourcenbezeichner mit einer gewünschten OAuth2-Berechtigung für die entsprechende Ressource verketten.

Beispiel für den Zugriff auf eine v1. 0-Web-API im Auftrag des Benutzers mit dem App-ID-URI `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Wenn Sie mit Azure Active Directory MSAL.NET unter Verwendung der Azure AD-Graph-API (https://graph.windows.net/) Lese- und Schreibvorgänge ausführen möchten, erstellen Sie eine Liste von Geltungsbereichen wie im folgenden Beispiel:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Um den Geltungsbereich für die Azure Resource Manager-API (https://management.core.windows.net/) zu schreiben, müssen Sie den folgenden Geltungsbereich anfordern (beachten Sie die beiden Schrägstriche):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Sie müssen zwei Schrägstriche verwenden, weil die Azure Resource Manager-API einen Schrägstrich im Zielgruppenanspruch/„audience“-Anspruch (aud) erwartet und ein weiterer Schrägstrich erforderlich ist, um den API-Namen vom Geltungsbereich zu trennen.

Die von Azure AD verwendete Logik lautet wie folgt:

- Für einen ADAL (v1.0)-Endpunkt mit einem v1.0-Zugriffstoken (einzige Möglichkeit): aud=resource
- Für einen MSAL (v2.0)-Endpunkt (Microsoft Identity Platform v2.0), der ein Zugriffstoken für eine Ressource abfragt, die v2.0-Token akzeptiert: aud=resource.AppId
- Für einen MSAL (v2.0)-Endpunkt, der ein Zugriffstoken für eine Ressource abfragt, die ein v1.0-Zugriffstoken akzeptiert (wie im Fall oben), analysiert Azure AD die gewünschte Zielgruppe aus dem angeforderten Geltungsbereich, indem alles vor dem letzten Schrägstrich als Ressourcenbezeichner interpretiert wird. Wenn https://database.windows.net die Zielgruppe „https://database.windows.net/“ erwartet, müssen Sie daher den Geltungsbereich „https://database.windows.net//.default“ anfordern. Weitere Informationen finden Sie auf GitHub unter[ Resource url's trailing slash is omitted, which caused sql auth failure #747 (Bei der URL der Ressource wurde der nachgestellte Schrägstrich entfernt, wodurch bei der SQL-Authentifizierung Fehler 747 aufgetreten ist)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

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

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Für Clientanmeldeinformations-Flows/Daemon-Apps anzufordernde Geltungsbereiche
Bei einem Clientanmeldeinformations-Flow lautet der zu übergebende Geltungsbereich ebenfalls `/.default`. Dies bedeutet für Azure AD: Alle Berechtigungen auf Anwendungsebene, denen der Administrator bei der Anwendungsregistrierung zugestimmt hat.