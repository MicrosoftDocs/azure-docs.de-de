---
title: 'Microsoft Identity Platform: Authentifizierungsbibliotheken | Microsoft-Dokumentation'
description: Enthält eine Liste mit den kompatiblen Clientbibliotheken und Bibliotheken der Servermiddleware sowie Links zu verwandten Bibliotheken, Quellen und Beispielen für den Microsoft Identity Platform-Endpunkt.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19718dc884685f6c1e1a588eea6a5de36e446fb1
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545539"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft Identity Plattform: Authentifizierungsbibliotheken

Der [Microsoft Identity Platform-Endpunkt](active-directory-v2-compare.md) unterstützt die Branchenstandardprotokolle OAuth 2.0 und OpenID Connect 1.0. Die Microsoft Authentication Library (MSAL) ist für die Zusammenarbeit mit dem Microsoft Identity Platform-Endpunkt ausgelegt. Es ist auch möglich, Open-Source-Bibliotheken zu verwenden, die OAuth 2.0 und OpenID Connect 1.0 unterstützen.

Es wird empfohlen, von Protokollspezialisten geschriebene Bibliotheken zu verwenden, die sich an eine SDL-Methodik (Security Development Lifecycle) halten, z.B. [die SDL-Methodik von Microsoft][Microsoft-SDL]. Wenn Sie die Protokolle selbst schreiben möchten, sollten Sie sich nach einer Methodik wie Microsoft SDL richten und die Sicherheitsaspekte in den Standardspezifikationen für die einzelnen Protokolle sorgfältig beachten.

> [!NOTE]
> Suchen Sie nach der Azure AD Authentication Library (ADAL)? Sehen Sie den [Leitfaden zu den ADAL-Bibliotheken](active-directory-authentication-libraries.md) ein.

## <a name="types-of-libraries"></a>Bibliothekstypen

Der Microsoft Identity Platform-Endpunkt funktioniert mit zwei Arten von Bibliotheken:

* **Clientbibliotheken**: Für native Clients und Server werden Clientbibliotheken verwendet, um den Zugriff auf Token zum Aufrufen einer Ressource zu erhalten, z.B. Microsoft Graph.
* **Bibliotheken der Servermiddleware**: Web-Apps nutzen die Bibliotheken der Servermiddleware für die Benutzeranmeldung. Web-APIs nutzen die Bibliotheken der Servermiddleware zum Überprüfen von Token, die von nativen Clients oder anderen Servern gesendet werden.

## <a name="library-support"></a>Bibliotheksunterstützung

Bibliotheken werden in zwei Kategorien unterteilt:

* **Von Microsoft unterstützt**: Microsoft bietet Fixes für diese Bibliotheken an und hat sie in Bezug auf die SDL-Anforderungen umfassend geprüft.
* **Kompatibel**: Microsoft hat diese Bibliotheken in grundlegenden Szenarien getestet und bestätigt, dass sie mit dem Microsoft Identity Platform-Endpunkt funktionieren. Microsoft stellt keine Fehlerbehebungen für diese Bibliotheken bereit und hat diese Prüfungen keinerlei Prüfungen unterzogen. Bei Problemen und Featureanfragen sollten Sie sich an die Community für das Open Source-Projekt der Bibliothek wenden.

In den nächsten Abschnitten dieses Artikels finden Sie eine Liste mit den Bibliotheken, die mit dem Microsoft Identity Platform-Endpunkt verwendet werden können.

## <a name="microsoft-supported-client-libraries"></a>Von Microsoft unterstützte Clientbibliotheken

Clientauthentifizierungsbibliotheken werden verwendet, um ein Token zum Aufrufen einer geschützten Web-API abzurufen.

| Plattform | Bibliothek | Download | Quellcode | Beispiel | Verweis | Konzeptionelle Dokumentation | Roadmap |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Einseitige App](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referenz](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Konzeptionelle Dokumentation](msal-overview.md)| [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular (Vorschau) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL .NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Desktop-App](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python (Vorschauversion) | [PyPI](https://pypi.org/project/msal) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Beispiele](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java (Vorschauversion) | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Beispiele](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| ![iOS/Objective-C/Swift](media/sample-v2-code/logo_iOS.png) | MSAL obj_c (Vorschau) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-Apps](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL (Vorschau) | [Zentrales Repository](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-Apps](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Von Microsoft unterstützte Bibliotheken der Servermiddleware

Middlewarebibliotheken werden verwendet, um Webanwendungen und Web-APIs zu schützen. Für Web-Apps oder Web-APIs, die mit ASP.NET oder ASP.NET Core geschrieben wurden, werden die Middlewarebibliotheken von ASP.NET/ASP.NET Core verwendet.

| Plattform | Bibliothek | Download | Quellcode | Beispiel | Verweis
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET Security |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC-App](quickstart-v2-aspnet-webapp.md) |[ASP.NET API-Referenz](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel-Erweiterungen für .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-App](quickstart-v2-aspnet-webapp.md) |[Referenz](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web-App](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Kompatible Clientbibliotheken

| Plattform | Name der Bibliothek | Getestete Version | Quellcode | Beispiel |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect library (Gluu OpenID Connect-Bibliothek)](https://github.com/GluuFederation/oxAuth) | [Version 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect library (Gluu OpenID Connect-Bibliothek)](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [Version 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Version 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [The PHP League oauth2-Client](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-Client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth) | [Version 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

Für jede standardkonforme Bibliothek können Sie den Microsoft Identity Platform-Endpunkt verwenden. Daher ist es wichtig zu wissen, wohin Sie sich wenden müssen, um Unterstützung zu erhalten.

* Wenden Sie sich bei Problemen und neuen Featureanfragen in Bezug auf den Bibliothekscode an den Besitzer der Bibliothek.
* Wenden Sie sich bei Problemen und neuen Featureanfragen in Bezug auf die dienstseitige Protokollimplementierung an Microsoft.
* [Erstellen Sie eine Featureanfrage](https://feedback.azure.com/forums/169401-azure-active-directory) zu zusätzlichen Features, die Sie sich für das Protokoll wünschen.
* [Erstellen Sie eine Supportanfrage](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request), wenn Sie ein Problem finden, bei dem der Microsoft Identity Platform-Endpunkt nicht mit OAuth 2.0 oder OpenID Connect 1.0 konform ist.

## <a name="related-content"></a>Verwandte Inhalte

Weitere Informationen zum Microsoft Identity Platform-Endpunkt finden Sie unter [Übersicht über Microsoft Identity Platform][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
