---
title: Azure Active Directory B2C-Codebeispiele | Microsoft-Dokumentation
description: Enthält Codebeispiele für mobile, Desktop-, Web- und Single-Page-Anwendungen für Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8642f4284770df54704a4e7066054d3bb4640486
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545868"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C-Codebeispiele

Die folgenden Tabellen enthalten Links zu Beispielen für Anwendungen, z.B. iOS, Android, .NET und Node.js.

## <a name="mobile-and-desktop-apps"></a>Mobile Apps und Desktop-Apps

| Beispiel | BESCHREIBUNG |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Ein iOS-Beispiel in Swift, mit dem Azure AD B2C-Benutzer authentifiziert werden und eine API über OAuth 2.0 aufgerufen wird. |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Eine einfache Android-App, mit der die Verwendung von MSAL zum Authentifizieren von Benutzern über Azure Active Directory B2C und der Zugriff auf eine Web-API mit den sich ergebenden Token veranschaulicht wird. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Mit diesem Beispiel wird veranschaulicht, wie Sie eine Drittanbieterbibliothek zum Erstellen einer iOS-Anwendung in Objective-C verwenden können, mit der Microsoft Identity-Benutzer für unseren Azure AD B2C-Identitätsdienst authentifiziert werden. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | In diesem Beispiel wird gezeigt, wie Sie eine Drittanbieterbibliothek zum Erstellen einer Android-Anwendung verwenden können, mit der Microsoft Identity-Benutzer für unseren B2C-Identitätsdienst authentifiziert werden und eine Web-API über OAuth 2.0-Zugriffstoken aufgerufen wird. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | In diesem Beispiel wird veranschaulicht, wie eine Windows Desktop-.NET-Anwendung (WPF) einen Benutzer per Azure AD B2C anmelden, ein Zugriffstoken per MSAL.NET abrufen und eine API aufrufen kann. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Eine einfache Xamarin Forms-App, mit der die Verwendung von MSAL zum Authentifizieren von Benutzern über Azure Active Directory B2C und der Zugriff auf eine Web-API mit den sich ergebenden Token veranschaulicht wird. |

## <a name="web-apps-and-apis"></a>Web-Apps und -APIs

| Beispiel | BESCHREIBUNG |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Ein kombiniertes Beispiel für eine .NET-Webanwendung, die eine .NET-Web-API aufruft, wobei beide Komponenten per Azure AD B2C geschützt sind. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Dies ist eine ASP.NET Core-Webanwendung, die OpenID Connect zum Anmelden von Benutzern bei Azure AD B2C verwendet. |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Eine ASP.NET Core-Webanwendung, die einen Benutzer per Azure AD B2C anmelden, ein Zugriffstoken per MSAL.NET abrufen und eine API aufrufen kann. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Eine Node.js-App, die das schnelle und einfache Einrichten einer Webanwendung mit Express per OpenID Connect ermöglicht. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Eine kleine Node.js-Web-API für Azure AD B2C, mit der das Schützen Ihrer Web-API und das Akzeptieren von B2C-Zugriffstoken per „passport.js“ veranschaulicht wird. |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Veranschaulicht die B2C-Integration von Microsoft Identity Platform in eine Python-Webanwendung.  |

## <a name="single-page-apps"></a>Einzelseiten-Apps

| Beispiel | BESCHREIBUNG |
|--------| ----------- |
| [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Eine Single-Page-Anwendung (SPA), die eine Web-API aufruft. Die Authentifizierung erfolgt mit Azure AD B2C über „MSAL.js“. In diesem Beispiel wird der Autorisierungscodeflow mit PKCE verwendet. |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Eine Single-Page-Anwendung (SPA), die eine Web-API aufruft. Die Authentifizierung erfolgt mit Azure AD B2C über „MSAL.js“. In diesem Beispiel wird der implizite Flow verwendet.|

## <a name="saml-test-application"></a>SAML-Testanwendung

| Beispiel | BESCHREIBUNG |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | SAML-Testanwendung zum Testen der Azure AD B2C-Instanz, die als SAML-Identitätsanbieter konfiguriert ist |

## <a name="api-connectors"></a>API-Connectors

Die folgenden Tabellen enthalten Links zu Codebeispielen für die Nutzung von Web-APIs in den Benutzerflows mithilfe von [API-Connectors](api-connectors-overview.md).

### <a name="azure-function-quickstarts"></a>Azure-Funktion – Schnellstarts

| Beispiel                                                                                                                          | BESCHREIBUNG                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Dieses .NET Core-Beispiel für eine Azure-Funktion veranschaulicht, wie Sie Registrierungen auf bestimmte E-Mail-Domänen beschränken und vom Benutzer bereitgestellte Informationen überprüfen. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Dieses Node.js-Beispiel für eine Azure-Funktion veranschaulicht, wie Sie Registrierungen auf bestimmte E-Mail-Domänen beschränken und vom Benutzer bereitgestellte Informationen überprüfen.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Dieses Python-Beispiel für eine Azure-Funktion veranschaulicht, wie Sie Registrierungen auf bestimmte E-Mail-Domänen beschränken und vom Benutzer bereitgestellte Informationen überprüfen.    |


### <a name="automated-fraud-protection-services--captcha"></a>Automatisierte Betrugsschutzdienste und CAPTCHA
| Beispiel                                                                                                            | BESCHREIBUNG                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Betrugs- und Missbrauchsschutz von Arkose Labs](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | In diesem Beispiel wird gezeigt, wie Sie Ihre Benutzerregistrierungen mithilfe des Betrugs- und Missbrauchsschutzdiensts von Arkose Labs schützen. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | In diesem Beispiel wird gezeigt, wie Sie Ihre Benutzerregistrierungen mithilfe einer reCAPTCHA-Prüfung schützen, um automatisierten Missbrauch zu verhindern. |


### <a name="identity-verification"></a>Identitätsüberprüfung

| Beispiel                                                                                                            | BESCHREIBUNG                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | In diesem Beispiel wird gezeigt, wie Sie eine Benutzeridentität im Rahmen Ihrer Registrierungsflows überprüfen, indem Sie einen API-Connector für die Integration mit IDology verwenden. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | In diesem Beispiel wird gezeigt, wie Sie eine Benutzeridentität im Rahmen Ihrer Registrierungsflows überprüfen, indem Sie einen API-Connector für die Integration mit Experian verwenden. |


### <a name="other"></a>Sonstiges

| Beispiel                                                                                                            | BESCHREIBUNG                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Einladungscode](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | In diesem Beispiel wird gezeigt, wie Sie die Registrierung mithilfe von Einladungscodes auf bestimmte Zielgruppen beschränken.|
| [Beispiele für Community im Zusammenhang mit API-Connector](https://github.com/azure-ad-b2c/api-connector-samples) | Dieses Repository enthält von der Community verwaltete Beispiele für Szenarien, die durch API-Connectors aktiviert wurden.|
