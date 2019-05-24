---
title: Azure Active Directory v1.0 – Codebeispiele | Microsoft-Dokumentation
description: Stellt einen Index der Azure Active Directory-Codebeispiele (v1.0-Endpunkt) bereit, geordnet nach Szenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b14dcb5f5af971b10c83ea7fd9064a08cde3ab35
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545436"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory-Codebeispiele (v1.0-Endpunkt)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Sie können Microsoft Azure Active Directory (Azure AD) verwenden, um Ihren Webanwendungen und Web-APIs Authentifizierung und Autorisierung hinzuzufügen.

Dieser Abschnitt enthält Links zu Beispielen, die Sie verwenden können, um weitere Informationen zum v1.0-Endpunkt von Azure AD zu erhalten. Anhand dieser Beispiele wird die Vorgehensweise erläutert, und die Codeausschnitte können Sie in Ihren Anwendungen verwenden. Auf der Codebeispielseite finden Sie ausführliche Infothemen mit hilfreichen Angaben zu Anforderungen, Installation und Einrichtung. Außerdem ist der Code kommentiert, um die wichtigsten Abschnitte besser verständlich zu machen.

> [!NOTE]
> Wenn Sie an Azure AD-Codebeispielen für den V2-Endpunkt interessiert sind, lesen Sie [V2.0-Codebeispiele nach Szenario](sample-v2-code.md).

Informationen zum grundlegenden Szenario für jeden Beispieltyp finden Sie unter [Authentifizierungsszenarien für Azure AD](authentication-scenarios.md).

Schreiben Sie auch Beiträge zu unseren Beispielen auf GitHub. Wie das geht, erfahren Sie unter [Microsoft Azure Active Directory – Beispiele und Dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Single-Page-Webanwendungen

Dieses Beispiel zeigt, wie Sie eine Single-Page-Webanwendung schreiben, die mit Azure AD gesichert ist.

 Plattform | Aufrufen der eigenen API | Aufrufen einer anderen Web-API
 -------- |  --------------------- | ------------------ 
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webanwendungen

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webanwendungen, die Benutzer anmelden und Microsoft Graph oder eine Web-API mit der Identität eines Benutzers aufrufen

Die folgenden Beispiele zeigen Webanwendungen, die Benutzer anmelden. Einige dieser Anwendungen rufen auch die Microsoft Graph- oder Ihre eigene Web-API im Namen des angemeldeten Benutzers auf.

 Plattform | Nur Benutzeranmeldung | Aufrufen von Microsoft Graph oder AAD Graph| Aufrufen einer anderen ASP.NET- oder ASP.NET Core 2.0-Web-API
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(AAD-Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (AAD-Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![PHP](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webanwendungen mit rollenbasierter Zugriffssteuerung (Autorisierung)

Die folgenden Beispiele zeigen, wie rollenbasierte Zugriffssteuerung (RBAC) implementiert wird. RBAC wird verwendet, um die Berechtigungen bestimmter Features in einer Webanwendung auf bestimmte Benutzer einzuschränken. Die Benutzerberechtigung hängt von der Zugehörigkeit zu einer **Azure AD-Gruppe** oder **Anwendungsrolle** ab.

Plattform | Beispiel |
 -------- | ------------------- |
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Eine .NET 4.5 MVC-Web-App, die Azure AD-**Rollen** für die Autorisierung verwendet

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Desktop und mobile öffentliche Clientanwendungen, die Microsoft Graph oder eine Web-API aufrufen

Die folgenden Beispiele zeigen öffentliche Clientanwendungen (Desktop-/mobile Anwendungen), die im Namen eines Benutzers auf die Microsoft Graph- oder eine Web-API zugreifen. Abhängig von den Geräten und Plattformen können Anwendungen Benutzer auf unterschiedliche Weise anmelden (Flows/Gewährungen): 

- interaktiv,
- im Hintergrund (mit der integrierten Windows-Authentifizierung unter Windows oder Benutzername/Kennwort) 
- oder sogar durch Delegieren der interaktiven Anmeldung an ein anderes Gerät (Gerätecodefluss auf Geräten, die keine Websteuerelemente bereitstellen).

Clientanwendung | Plattform | Ablauf/Erteilung | Aufrufen von Microsoft Graph | Aufrufen einer ASP.NET- oder ASP.NET Core 2.x-Web-API
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![.NET/C#](media/sample-v2-code/logo_NET.png)  | Interactive | Teil von [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobil (UWP)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interactive | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Dieses Beispiel verwendet [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager), nicht [ADAL.NET](https://aka.ms/adalnet).|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-Anwendung, die ADAL.NET verwendet, um eine einzelinstanzenfähige Web-API aufzurufen) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-Anwendung, die ADAL.NET verwendet, um eine mehrinstanzenfähige Web-API aufzurufen)|
Mobil (Android, iOS, UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobil (Android)           | ![Android/Java](media/sample-v2-code/logo_Android.png) | Interactive |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobil (iOS)           | ![iOS/Objective-C/Swift](media/sample-v2-code/logo_iOS.png) | Interactive |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Desktop (Konsole)          | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Benutzername und Kennwort </p>  Integrierte Windows-Authentifizierung | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Desktop (Konsole)          | ![Java-Konsole](media/sample-v2-code/logo_Java.png) | Benutzername und Kennwort | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Desktop (Konsole)           | ![.NET Core/C#](media/sample-v2-code/logo_NETcore.png) | Gerätecodefluss | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-Anwendungen (Zugriff auf Web-APIs mit der Identität der Anwendung)

Die folgenden Beispiele veranschaulichen Desktop- oder Web-Anwendungen, die nicht mit einem Benutzer, sondern mit der Identität der Anwendung auf Microsoft Graph oder eine Web-API zugreifen.

Clientanwendung | Plattform | Ablauf/Erteilung | Aufrufen einer ASP.NET- oder ASP.NET Core 2.0-Web-API
------------------ | -------- | ---------- | -------------------- 
Daemon-App (Konsole)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | Clientanmeldeinformationen mit App-Geheimnis oder Zertifikat | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon-App (Konsole)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | Clientanmeldeinformationen mit Zertifikat| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET-Web-App  | ![.NET](media/sample-v2-code/logo_NETframework.png) | Client credentials (Clientanmeldeinformationen) | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web-APIs

### <a name="web-api-protected-by-azure-active-directory"></a>Durch Azure Active Directory geschützte Web-API

Im folgende Beispiel wird gezeigt, wie eine NodeJS-Web-API mit Azure AD geschützt wird.

In den vorherigen Abschnitten dieses Artikels finden Sie auch andere Beispiele, die veranschaulichen, wie eine Clientanwendung eine ASP. NET- oder ASP. NET Core-**Web-API** **aufruft**. Diese Beispiele werden in diesem Abschnitt nicht noch einmal erwähnt, aber Sie finden sie in der letzten Spalte der Tabellen weiter oben oder unten.

Plattform | Beispiel
 -------- | -------------------
![PHP](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Web-API ruft Microsoft Graph oder eine andere Web-API auf

Die folgenden Beispiele zeigen eine Web-API, die eine andere Web-API aufruft. Im zweiten Beispiel wird gezeigt, wie der bedingte Zugriff behandelt wird.

 Plattform |  Aufrufen von Microsoft Graph | Aufrufen einer anderen ASP.NET- oder ASP.NET Core 2.0-Web-API
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>Weitere Microsoft Graph-Beispiele

Beispiele und Lernprogramme, die verschiedene Verwendungsmuster für die Microsoft Graph-API (einschließlich Authentifizierung mit Azure AD) veranschaulichen, finden Sie unter [Microsoft Graph Community Samples & Tutorials](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Weitere Informationen

[Entwicklerhandbuch zu Azure Active Directory](v1-overview.md)

[Azure Active Directory-Authentifizierungsbibliotheken](active-directory-authentication-libraries.md)

[Azure AD-Graph-API – Konzepte und Referenz](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Bibliothek für das Azure AD Graph-API-Hilfsprogramm](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
