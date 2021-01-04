---
title: Azure Active Directory-Authentifizierungsbibliotheken | Microsoft-Dokumentation
description: Die Azure AD-Authentifizierungsbibliothek (ADAL) ermöglicht es Entwicklern von Clientanwendungen, auf einfache Weise eine Benutzerauthentifizierung mit Active Directory (Cloud oder lokal) bereitzustellen und anschließend Zugriffstoken zur Absicherung von API-Aufrufen abzurufen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: reference
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7b6ffd885e1be2dd59cea87cacd6e5fd5e0f8f49
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861169"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory-Authentifizierungsbibliotheken

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory Authentication Library (ADAL) v1.0 ermöglicht es Entwicklern von Anwendungen, eine Benutzerauthentifizierung mit Active Directory (AD) (Cloud oder lokal) bereitzustellen und Token zur Absicherung von API-Aufrufen abzurufen. ADAL bietet Entwicklern folgende Features, um die Authentifizierung zu vereinfachen:

- Konfigurierbarer Tokencache zum Speichern von Zugriffs- und Aktualisierungstoken
- Automatische Tokenaktualisierung, wenn ein Zugriffstoken abläuft und ein Aktualisierungstoken verfügbar ist
- Unterstützung für asynchrone Methodenaufrufe

> [!NOTE]
> Suchen Sie nach den Azure AD v2.0-Bibliotheken (MSAL)? Sehen Sie den [Leitfaden zu den MSAL-Bibliotheken](../develop/reference-v2-libraries.md) ein.
>
>

## <a name="microsoft-supported-client-libraries"></a>Von Microsoft unterstützte Clientbibliotheken

| Plattform | Bibliothek | Download | Quellcode | Beispiel | Verweis
| --- | --- | --- | --- | --- | --- |
| .NET-Client, Windows Store, UWP, Xamarin iOS und Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Desktop-App](../develop/quickstart-v2-windows-desktop.md) |[Referenz](/dotnet/api/microsoft.identitymodel.clients.activedirectory) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Einseitige App](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-Apps](../develop/quickstart-v2-ios.md) | [Referenz](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-Apps](../develop/quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js-Web-Apps](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referenz](/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java-Web-App](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referenz](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python-Web-App](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referenz](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Von Microsoft unterstützte Serverbibliotheken

| Plattform | Bibliothek | Download | Quellcode | Beispiel | Verweis
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN für Azure AD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[MVC-App](../develop/quickstart-v2-aspnet-webapp.md) | |
| .NET |OWIN für OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Web App](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN für WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[MVC-Web-App](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Identitätsprotokollerweiterungen für .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT-Handler für .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web-API](../develop/authentication-flows-app-scenarios.md)| |

## <a name="scenarios"></a>Szenarien

Im Folgenden werden drei allgemeine Szenarien für die Verwendung von ADAL in einem Client vorgestellt, der auf eine Remoteressource zugreift:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Authentifizieren von Benutzern einer auf einem Gerät ausgeführten nativen Clientanwendung

In diesem Szenario hat ein Entwickler eine mobile Client- oder Desktopanwendung erstellt, die auf eine Remoteressource (z.B. eine Web-API) zugreifen muss. Die Web-API lässt keine anonymen Aufrufe zu und muss im Kontext eines authentifizierten Benutzers aufgerufen werden. Die Web-API ist so vorkonfiguriert, dass sie den von einem bestimmten Azure AD-Mandanten ausgestellten Zugriffstoken vertraut. Azure AD ist für die Ausstellung von Zugriffstoken für diese Ressource vorkonfiguriert. Für den Aufruf der Web-API vom Client verwendet der Entwickler ADAL, um die Authentifizierung mit Azure AD zu vereinfachen. Die sicherste Möglichkeit zur Verwendung von ADAL besteht darin, darüber die Benutzeroberfläche für die Sammlung von Benutzeranmeldeinformationen (als Browserfenster gerendert) zu rendern.

Dank ADAL ist es einfach, den Benutzer zu authentifizieren, ein Zugriffstoken und ein Aktualisierungstoken aus Azure AD abzurufen und mithilfe des Zugriffstokens dann die Web-API aufzurufen.

Ein Codebeispiel, in dem dieses Szenario mithilfe von Authentifizierung für Azure AD veranschaulicht wird, finden Sie unter [Systemeigene Client-WPF-Anwendung für Web-API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Authentifizieren einer auf einem Webserver ausgeführten vertraulichen Clientanwendung

In diesem Szenario verwendet ein Entwickler eine Anwendung, die auf einem Server ausgeführt wird und auf eine Remoteressource (z.B. eine Web-API) zugreifen muss. Die Web-API lässt keine anonymen Aufrufe zu und muss daher von einem autorisierten Dienst aufgerufen werden. Die Web-API ist so vorkonfiguriert, dass sie den von einem bestimmten Azure AD-Mandanten ausgestellten Zugriffstoken vertraut. Azure AD ist für die Ausstellung von Zugriffstoken für diese Ressource für einen Dienst mit Clientanmeldeinformationen (Client-ID und Geheimnis) vorkonfiguriert. ADAL vereinfacht die Authentifizierung des Diensts bei Azure AD, wobei ein Zugriffstoken für den Aufruf der Web-API zurückgegeben werden kann. ADAL übernimmt außerdem die Verwaltung der Lebensdauer des Zugriffstokens, indem es zwischengespeichert und bei Bedarf erneuert wird. Ein Codebeispiel, das dieses Szenario veranschaulicht, finden Sie unter [Daemon console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet) (Daemon-Konsolenanwendung für Web-API).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Authentifizieren einer auf einem Server ausgeführten vertraulichen Clientanwendung im Namen eines Benutzers

In diesem Szenario verwendet ein Entwickler eine Webanwendung, die auf einem Server ausgeführt wird und auf eine Remoteressource (z.B. eine Web-API) zugreifen muss. Die Web-API lässt keine anonymen Aufrufe zu und muss daher im Auftrag eines authentifizierten Benutzers von einem autorisierten Dienst aufgerufen werden. Die Web-API ist so vorkonfiguriert, dass sie von einem bestimmten Azure AD-Mandanten ausgestellte Zugriffstoken vertraut, während Azure AD dafür vorkonfiguriert ist, dass Zugriffstokens für diese Ressource in einem Dienst mit Clientanmeldeinformationen ausgestellt werden. Sobald der Benutzer in der Webanwendung authentifiziert ist, kann die Anwendung einen Autorisierungscode für den Benutzer aus Azure AD abrufen. Die Webanwendung kann dann ADAL verwenden, um im Auftrag eines Benutzers ein Zugriffstoken und ein Aktualisierungstoken abzurufen, wobei der Autorisierungscode und die Clientanmeldeinformationen verwendet werden, die der Anwendung aus Azure AD zugeordnet sind. Sobald die Webanwendung im Besitz des Zugriffstokens ist, kann sie bis zum Ablauf des Tokens die Web-API aufrufen. Nach Ablauf des Tokens kann die Webanwendung über ADAL mit dem zuvor empfangenen Aktualisierungstoken ein neues Zugriffstoken abrufen. Ein Codebeispiel, das dieses Szenario veranschaulicht, finden Sie unter [Native client to Web API to Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) (Nativer Clientaufruf zwischen Web-APIs).

## <a name="see-also"></a>Weitere Informationen

- [Entwicklerhandbuch zu Azure Active Directory](v1-overview.md)
- [Authentifizierungsszenarien für Azure Active Directory](v1-authentication-scenarios.md)
- [Azure Active Directory-Codebeispiele](sample-v1-code.md)
