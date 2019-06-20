---
title: Web-App für Benutzeranmeldungen (Anmeldung) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App zur Benutzeranmeldung erstellen (Anmeldung).
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080066"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Web-App für Benutzeranmeldungen: Anmeldung

Erfahren Sie, wie Sie dem Code für Ihre Web-App eine Anmeldefunktion zur Benutzeranmeldung hinzufügen.

## <a name="sign-in"></a>Anmeldung

Der im vorherigen Artikel zur [App-Codekonfiguration](scenario-web-app-sign-user-app-configuration.md) erarbeitete Code ist alles, was Sie zum Implementieren der Abmeldung benötigen. Nachdem der Benutzer sich bei Ihrer App angemeldet hat, möchten Sie ihm wahrscheinlich auch das Abmelden ermöglichen. ASP.NET Core verarbeitet die Abmeldung für Sie.

## <a name="what-sign-out-involves"></a>Voraussetzungen für die Abmeldung

Beim Abmelden von einer Web-App geht es um mehr als um das Entfernen der Informationen über das angemeldete Konto aus dem Status der Web-App.
Die Web-App muss den Benutzer für die Abmeldung außerdem an den `logout`-Endpunkt von Microsoft Identity Platform v2.0 umleiten. Wenn Ihre Web-App den Benutzer an den `logout`-Endpunkt umleitet, löscht dieser Endpunkt die Sitzung des Benutzers aus dem Browser. Wenn die App nicht zum `logout`-Endpunkt wechselt, kann sich der Benutzer erneut für Ihre App authentifizieren, ohne die Anmeldeinformationen erneut einzugeben, weil der Benutzer nach wie vor über eine gültige SSO-Sitzung (Single Sign-On, Einmaliges Anmelden) beim Microsoft Identity Platform v2.0-Endpunkt verfügt.

Weitere Informationen finden Sie im Abschnitt [Senden einer Abmeldeanforderung](v2-protocols-oidc.md#send-a-sign-out-request) in der konzeptionellen Dokumentation von [Microsoft Identity Platform v2. 0 und dem OpenID Connect-Protokoll](v2-protocols-oidc.md).

## <a name="application-registration"></a>Anwendungsregistrierung

Bei der Anwendungsregistrierung haben Sie einen **URI nach der Abmeldung** registriert. In diesem Tutorial haben Sie auf der Seite **Authentifizierung** im Abschnitt **Erweiterte Einstellungen** im Feld **Abmelde-URL** die URL `https://localhost:44321/signout-oidc` registriert. Weitere Informationen finden Sie unter [Registrieren der webApp-App](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

## <a name="aspnet-core-code"></a>ASP.NET Core-Code

### <a name="signout-button"></a>Schaltfläche „Abmelden“

Die Abmeldeschaltfläche wird in `Views\Shared\_LoginPartial.cshtml` verfügbar gemacht und nur angezeigt, wenn ein authentifiziertes Konto vorliegt (wenn sich der Benutzer also zuvor angemeldet hat).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`-Aktion von `AccountController`

Durch Auswahl der Schaltfläche **Abmelden** in der Web-App wird die Aktion `SignOut` auf dem `Account`-Controller ausgelöst. In früheren Versionen von ASP.NET Core-Vorlagen wurde der `Account`-Controller in die Web-App eingebettet. Dies ist jedoch nicht mehr der Fall, weil er jetzt Bestandteil des ASP.NET Core-Frameworks selbst ist. 

Der Code für den `AccountController` steht im ASP.NET Core-Repository in [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) zur Verfügung. Die Kontosteuerung führt folgende Aktionen durch:

- Sie legt einen OpenID-Umleitungs-URI auf `/Account/SignedOut` fest, damit der Controller zurückgerufen wird, wenn Azure AD die Abmeldung durchgeführt hat.
- Sie ruft `Signout()` auf, damit die OpenIdConnect-Middleware den Microsoft Identity Platform-`logout`-Endpunkt kontaktieren kann, der:

  - den Sitzungscookie aus dem Browser löscht und
  - einen Rückruf für die **Abmelde-URL** ausführt, die standardmäßig die als Bestandteil von ASP.NET Core bereitgestellte Seite mit der Abmeldeansicht [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) anzeigt

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Abfangen des Aufrufs an den `logout`-Endpunkt

Mit der OpenIdConnect-Middleware von ASP.NET Core kann Ihre App den Aufruf an den Microsoft Identity Platform-`logout`-Endpunkt durch die Bereitstellung eines OpenId Connect-Ereignisses namens `OnRedirectToIdentityProviderForSignOut` abfangen. Die Web-App versucht damit zu vermeiden, dass dem Benutzer bei der Abmeldung das Dialogfeld zur Kontoauswahl angezeigt wird. Dieses Abfangen erfolgt in der `AddAzureAdV2Authentication` der wiederverwendbaren Bibliothek `Microsoft.Identity.Web`. Weitere Informationen finden Sie unter [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66).

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET-Code

In ASP.NET wird die Abmeldung von der SignOut()-Methode auf einem Controller ausgelöst (z.B. AccountController). Diese Methode gehört nicht zum ASP.NET-Framework (im Gegensatz zu den Vorgängen in ASP.NET Core), und verwendet nicht „async“. Daher werden folgende Vorgänge durchgeführt:

- Sie sendet eine OpenId-Abmeldeaufforderung.
- Sie löscht den Cache.
- Sie leitet auf die gewünschte Seite um.

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

Wenn Sie nicht ASP.NET Core oder ASP.NET verwenden möchten, sehen Sie sich die Protokolldokumentation an, die über [Open ID Connect](./v2-protocols-oidc.md) verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Übergang in die Produktion](scenario-web-app-sign-user-production.md)
