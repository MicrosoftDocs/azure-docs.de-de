---
title: 'Microsoft Identity Plattform: Authentifizierungsbibliotheken'
description: Enthält eine Liste mit den kompatiblen Clientbibliotheken und Bibliotheken der Servermiddleware zusammen mit Links zu verwandten Bibliotheken, Quellen und Beispielen für den Microsoft Identity Platform-Endpunkt.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: ebe36c6f4b8b010d2b53bf4153f1476183eb8a4a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651259"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft Identity Plattform: Authentifizierungsbibliotheken

Der [Microsoft Identity Platform-Endpunkt](../azuread-dev/azure-ad-endpoint-comparison.md) unterstützt die Branchenstandardprotokolle OAuth 2.0 und OpenID Connect 1.0. Die Microsoft Authentication Library (MSAL) ist für die Zusammenarbeit mit dem Microsoft Identity Platform-Endpunkt ausgelegt. Sie können auch Open-Source-Bibliotheken verwenden, die OAuth 2.0 und OpenID Connect 1.0 unterstützen.

Sie sollten von Protokollspezialisten geschriebene Bibliotheken verwenden, die sich an eine SDL-Methodik (Security Development Lifecycle) halten. Zu diesen Methoden gehören auch [die von Microsoft befolgten][Microsoft-SDL]. Wenn Sie Code für die Protokolle übergeben, sollten Sie sich an eine Methodik wie Microsoft SDL halten. Achten Sie auf die Sicherheitsüberlegungen in den Standardspezifikationen für jedes Protokoll.

> [!NOTE]
> Suchen Sie die Azure Active Directory Authentication Library (ADAL)? Sehen Sie den [Leitfaden zu den ADAL-Bibliotheken](../azuread-dev/active-directory-authentication-libraries.md) ein.

## <a name="types-of-libraries"></a>Bibliothekstypen

Der Microsoft Identity Platform-Endpunkt funktioniert mit zwei Arten von Bibliotheken:

* **Clientbibliotheken**: Für native Clients und Server werden Clientbibliotheken verwendet, um den Zugriff auf Token zum Aufrufen einer Ressource zu erhalten, z.B. Microsoft Graph.
* **Bibliotheken der Servermiddleware**: Web-Apps nutzen die Bibliotheken der Servermiddleware für die Benutzeranmeldung. Web-APIs nutzen die Bibliotheken der Servermiddleware zum Überprüfen von Token, die von nativen Clients oder anderen Servern gesendet werden.

## <a name="library-support"></a>Bibliotheksunterstützung

Bibliotheken werden in zwei Kategorien unterteilt:

* **Von Microsoft unterstützt**: Microsoft bietet Fixes für diese Bibliotheken an und hat sie in Bezug auf die SDL-Anforderungen umfassend geprüft.
* **Kompatibel**: Microsoft hat diese Bibliotheken in grundlegenden Szenarien getestet und bestätigt, dass sie mit dem Microsoft Identity Platform-Endpunkt funktionieren. Microsoft stellt keine Fehlerbehebungen für diese Bibliotheken bereit und hat diese Bibliotheken keinerlei Prüfungen unterzogen. Probleme und Funktionsanforderungen müssen direkt an das Open Source-Projekt der Bibliothek weitergeleitet werden.

In den folgenden Abschnitten finden Sie eine Liste mit Bibliotheken, die mit dem Microsoft Identity Platform-Endpunkt verwendet werden können.

## <a name="microsoft-supported-client-libraries"></a>Von Microsoft unterstützte Clientbibliotheken

Verwenden Sie Clientauthentifizierungsbibliotheken, um ein Token zum Aufrufen einer geschützten Web-API abzurufen.

| Plattform | Bibliothek | Download | Quellcode | Beispiel | Verweis | Konzeptionelle Dokumentation | Roadmap |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Einseitige App](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referenz](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Konzeptionelle Dokumentation](msal-overview.md)| [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Angular-SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Referenz](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Konzeptionelle Dokumentation](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Desktop-App](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview&preserve-view=true) |[Konzeptionelle Dokumentation](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![.NET Core-Symbol](media/sample-v2-code/logo_NETCore.png) | Microsoft Identity-Web  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Web) |[GitHub](https://github.com/AzureAD/microsoft-identity-web) | [Beispiele](https://aka.ms/ms-id-web/samples) | [Microsoft.Identity.Web](/dotnet/api/microsoft.identity.web?view=azure-dotnet-preview&preserve-view=true) |[Konzeptionelle Dokumentation](https://aka.ms/ms-id-web/conceptual-doc) | [Roadmap](https://github.com/AzureAD/microsoft-identity-web/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Beispiele](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://search.maven.org/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Beispiele](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Referenz](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS und macOS | MSAL für iOS und macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-App](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [macOS-App](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Referenz](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Konzeptionelle Dokumentation](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Zentrales Repository](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-Apps](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Konzeptionelle Dokumentation](msal-overview.md) |[Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Von Microsoft unterstützte Bibliotheken der Servermiddleware

Verwenden Sie Middlewarebibliotheken, um Webanwendungen und Web-APIs zu schützen. Mit ASP.NET oder ASP.NET Core geschriebene Web-Apps oder Web-APIs verwenden die Middlewarebibliotheken.

| Plattform | Bibliothek | Download | Quellcode | Beispiel | Verweis
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET Security |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC-App](quickstart-v2-aspnet-webapp.md) |[ASP.NET API-Referenz](/dotnet/api/?view=aspnetcore-2.0&preserve-view=true) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel-Erweiterungen für .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-App](quickstart-v2-aspnet-webapp.md) |[Referenz](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet&preserve-view=true) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web-App](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Von Microsoft unterstützte Bibliotheken nach Betriebssystem/Sprache

Bezüglich der unterstützten Betriebssysteme und Sprachen sieht die Zuordnung wie folgt aus:

| Plattform    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.NET (.NET FW, Core, UWP)| ASP.NET Core, MSAL.NET (.NET Core) | ASP.NET Core, MSAL.NET (macOS)       | MSAL.NET (Xamarin.iOS) | MSAL.NET (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [MSAL für iOS und macOS](msal-overview.md) | [MSAL für iOS und macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport.node | Passport.node | Passport.node |

Siehe auch: [Szenarien und unterstützte Plattformen und Sprachen](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Kompatible Clientbibliotheken

| Plattform | Name der Bibliothek | Getestete Version | Quellcode | Beispiel |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Version 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[Vue MSAL](https://github.com/mvertopoulos/vue-msal) | Version 3.0.3 |[vue-msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect library (Gluu OpenID Connect-Bibliothek)](https://github.com/GluuFederation/oxAuth) | [Version 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect library (Gluu OpenID Connect-Bibliothek)](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [Version 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Version 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [The PHP League oauth2-Client](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-Client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS und Android  | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth) | [Version 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

Für jede standardkonforme Bibliothek können Sie den Microsoft Identity Platform-Endpunkt verwenden. Sie müssen wissen, wo Sie Support erhalten:

* Wenden Sie sich bei Problemen und neuen Featureanfragen in Bezug auf den Bibliothekscode an den Besitzer der Bibliothek.
* Wenden Sie sich bei Problemen und neuen Featureanfragen in Bezug auf die dienstseitige Protokollimplementierung an Microsoft.
* [Erstellen Sie eine Featureanfrage](https://feedback.azure.com/forums/169401-azure-active-directory) zu zusätzlichen Features, die Sie sich für das Protokoll wünschen.
* [Erstellen Sie eine Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md), wenn Sie ein Problem finden, bei dem der Microsoft Identity Platform-Endpunkt nicht mit OAuth 2.0 oder OpenID Connect 1.0 konform ist.

## <a name="related-content"></a>Verwandte Inhalte

Weitere Informationen zum Microsoft Identity Platform-Endpunkt finden Sie unter [Übersicht über Microsoft Identity Platform][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
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
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
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
