---
title: Web-App, die Web-APIs aufruft (Aufrufen einer Web-API) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Web-APIs aufruft (Aufrufen einer Web-API).
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
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080068"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Web-App, die Web-APIs aufruft – Aufrufen einer Web-API

Da Sie nun über ein Token verfügen, können Sie eine geschützte Web-API aufrufen.

## <a name="aspnet-core"></a>ASP.NET Core

Im folgenden Ausschnitt sehen Sie vereinfachten Code für die `HomeController`-Aktion. Mit diesem wird ein Token für den Aufruf von Microsoft Graph abgerufen. Anhand des hinzugefügten Codes wird verdeutlicht, wie Microsoft Graph als REST-API aufgerufen werden kann.

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

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Sie können dasselbe Prinzip für den Aufruf jeder anderen Web-API verwenden.
>
> Die meisten Azure-Web-APIs stellen ein SDK bereit, das den Aufruf vereinfacht. Dies ist auch für Microsoft Graph der Fall. Im nächsten Artikel erfahren Sie, wo Sie ein Tutorial finden, das diese Aspekte veranschaulicht.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Übergang in die Produktion](scenario-web-app-call-api-production.md)
