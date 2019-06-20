---
title: Web-App, die Web-APIs aufruft (Anmelden) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Web-APIs aufruft (Anmelden)
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080058"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Web-App, die Web-APIs aufruft – Anmelden

Sie wissen bereits, wie Ihrer Web-App Anmeldedaten hinzugefügt werden. Dies lernen Sie in [Web app that signs-in users - add sign-in (Web-App, die Benutzer anmeldet – Hinzufügen von Anmeldedaten)](scenario-web-app-sign-user-sign-in.md).

Der Unterschied besteht hier darin, dass Sie nach der Abmeldung des Benutzers von dieser oder einer anderen Anwendung den Tokencache mit den dem Benutzer zugeordneten Tokens leeren möchten.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Abfangen eines Rückrufs nach der Abmeldung – Einmaliges Abmelden

Ihre Anwendung kann das „after `logout`“-Ereignis abfangen, um beispielsweise den Eintrag aus dem Tokencache zu löschen, dem das abgemeldete Konto zugeordnet ist. Im zweiten Teil dieses Tutorials (über die Web-App, die Web-APIs abruft) erfahren Sie, dass die Web-App Zugriffstoken für den Benutzer in einem Cache speichert. Durch das Abfangen des „after `logout`“-Rückrufs kann Ihre Web-App den Benutzer aus dem Tokencache entfernen. Dieser Mechanismus wird in der `AddMsal()`-Methode von [StartupHelper.cs L137-143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143) veranschaulicht.

Die **Abmelde-URL**, die Sie für Ihre Anwendung registriert haben, ermöglicht Ihnen das Implementieren der einmaligen Abmeldung. Der Microsoft Identity Platform-Endpunkt `logout` ruft die für Ihre Anwendung registrierte **Abmelde-URL** auf. Dieser Aufruf erfolgt, wenn die Abmeldung von Ihrer Web-App, einer anderen Web-App oder dem Browser initiiert wurde. Weitere Informationen finden Sie unter [Einmaliges Abmelden](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) in der konzeptuellen Dokumentation.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Abrufen eines Tokens für die Web-App](scenario-web-app-call-api-acquire-token.md)
