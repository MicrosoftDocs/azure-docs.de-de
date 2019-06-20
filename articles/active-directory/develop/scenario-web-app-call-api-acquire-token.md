---
title: Web-App, die Web-APIs aufruft (Abrufen eines Tokens für die App) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Web-APIs aufruft (Abruf eines Tokens für die App).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080090"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web-App, die Web-APIs aufruft – Abrufen eines Tokens für die App

Mit Ihrem erstellen Clientanwendungsobjekt rufen Sie im Folgenden ein Token ab. Dieses verwenden Sie anschließend, um die Web-APIs aufzurufen. In ASP.NET oder ASP.NET Core erfolgt der Aufruf der Web-API daraufhin im Controller. Dabei werden folgende Schritte ausgeführt:

- Aus dem Tokencache wird ein Token für die Web-API abgerufen. Dazu rufen Sie `AcquireTokenSilent` auf.
- Die geschützte API wird über das Zugriffstoken aufgerufen.

## <a name="aspnet-core"></a>ASP.NET Core

Die Controllermethoden sind durch ein `[Authorize]`-Attribut geschützt, mit dem erzwungen wird, dass nur authentifizierte Benutzer die Web-App verwenden dürfen. Mit dem folgenden Code wird Microsoft Graph aufgerufen:

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Im folgenden Ausschnitt sehen Sie vereinfachten Code für die HomeController-Aktion, mit der das Token für den Aufruf von Microsoft Graph abgerufen wird.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

Wenn Sie den gesamten Code nachvollziehen möchten, der für dieses Szenario erforderlich ist, sollten Sie sich den Schritt [2.1 Web-App ruft Microsoft Graph auf](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) in Teil 2 des Tutorials [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) ansehen.

Zusätzliche Komplexität entsteht beispielsweise durch die folgenden Aktionen:

- Implementieren eines Tokencaches für die Web-App (siehe Tutorial für mehrere Implementierungen)
- Entfernen des Kontos aus dem Cache, wenn der Benutzer sich abmeldet
- Aufrufen mehrerer APIs und Verwenden der inkrementellen Einwilligung

## <a name="aspnet"></a>ASP.NET

In ASP.NET ist die Vorgehensweise ähnlich:

- Eine Controlleraktion, die durch das Attribut „[Authorize]“ geschützt ist, extrahiert die Mandanten- und die Benutzer-ID des `ClaimsPrincipal`-Members aus dem Controller (in ASP.NET wird `HttpContext.User` verwendet).
- Anschließend wird ein MSAL.NET-`IConfidentialClientApplication`-Objekt erstellt.
- Danach ruft dieses die `AcquireTokenSilent`-Methode der vertraulichen Clientanwendung auf. 

Der Code ähnelt dem für ASP.NET Core.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aufrufen einer Web-API](scenario-web-app-call-api-call-api.md)
