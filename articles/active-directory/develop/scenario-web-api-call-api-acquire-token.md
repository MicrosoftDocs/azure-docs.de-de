---
title: Web-API, die andere Web-APIs aufruft (Abrufen eines Tokens für die App) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-API erstellen, die andere Web-APIs aufruft (Abruf eines Tokens für die App).
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65231096"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web-API, die Web-APIs aufruft – Abrufen eines Tokens für die App

Mit Ihrem erstellen Clientanwendungsobjekt rufen Sie im Folgenden ein Token ab. Dieses verwenden Sie anschließend, um Web-APIs aufzurufen.

## <a name="code-in-the-controller"></a>Code im Controller

Hier sehen Sie ein Beispiel für Code, der in den Aktionen des API-Controllers aufgerufen wird und eine Downstream-API (mit dem Namen „ToDoList“) aufruft.

```CSharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` gleicht dem unter [Web-API, die Web-APIs aufruft (App-Konfiguration)](scenario-web-api-call-api-app-configuration.md) Beschriebenen. `BuildConfidentialClient()` instanziiert `IConfidentialClientApplication` mit einem Zwischenspeicher, der alle Informationen zu einem Konto enthält. Das Konto wird über die `GetAccountIdentifier`-Methode bereitgestellt.

Die Methode `GetAccountIdentifier` verwendet die Ansprüche, die zur Identität des Benutzer gehören, für den die API das JSON Web Token erhalten hat:

```CSharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aufrufen einer Web-API](scenario-web-api-call-api-call-api.md)
