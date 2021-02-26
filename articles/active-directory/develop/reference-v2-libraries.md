---
title: 'Microsoft Identity Plattform: Authentifizierungsbibliotheken | Azure'
description: Liste der mit der Microsoft Identity Platform kompatiblen Clientbibliotheken und Middleware. Verwenden Sie diese Bibliotheken, um Ihren Anwendungen Unterstützung für die Benutzeranmeldung (Authentifizierung) und den geschützten Web-API-Zugriff (Autorisierung) hinzuzufügen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218276"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft Identity Plattform: Authentifizierungsbibliotheken

Die folgenden Tabellen zeigen die Unterstützung der Microsoft-Authentifizierungsbibliothek für mehrere Anwendungstypen. Sie enthält Links zum Quellcode der Bibliothek, den Speicherort des Pakets für das Projekt Ihrer App und Informationen dazu, ob die Bibliothek die Benutzeranmeldung (Authentifizierung), den Zugriff auf geschützte Web-APIs (Autorisierung) oder beides unterstützt.

Die Microsoft Identity Platform wurde von der OpenID Foundation als [Certified OpenID Provider](https://openid.net/certification/) zertifiziert. Wenn Sie lieber eine andere Bibliothek als die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) oder eine andere von Microsoft unterstützte Bibliothek verwenden möchten, wählen Sie eine mit [zertifizierter OpenID Connect-Implementierung](https://openid.net/developers/certified/) aus.

Wenn Sie Ihre eigene Implementierung von [OAuth 2.0 oder OpenID Connect 1.0](active-directory-v2-protocols.md) auf Protokollebene von Hand schreiben möchten, achten Sie auf die Sicherheitsüberlegungen in den Spezifikationen der einzelnen Standards, und befolgen Sie eine SDL-Methodik (Software Development Lifecycle) wie z. B. [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Single-Page-Webanwendung (SPA)

Eine Single-Page-Webanwendung wird vollständig auf der Browseroberfläche ausgeführt und ruft die Seitendaten (HTML, CSS und JavaScript) dynamisch oder zur Ladezeit der Anwendung ab. Sie kann für die Interaktion mit Back-End-Datenquellen Web-APIs aufrufen.

Da der Code einer Single-Page-Webanwendung vollständig im Browser ausgeführt wird, gilt er als *öffentlicher Client*, der Geheimnisse nicht sicher speichern kann.

| Programmiersprache/Framework | Projekt auf<br/>GitHub                                                                                                    | Paket                                                                      | Erste Schritte<br/>gestartet                             | Anmelden von Benutzern                                         | Zugriff auf Web-APIs                                                 | Allgemein verfügbar (Generally Available, GA) *oder*<br/>Öffentliche Vorschau<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Public Preview                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Tutorial](tutorial-v2-angular.md)              | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Public Preview                                               |
| JavaScript           | [MSAL.js 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Tutorial](tutorial-v2-javascript-auth-code.md) | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Public Preview                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> Für Bibliotheken in der *öffentlichen Vorschau* gelten [ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen][preview-tos].

## <a name="web-application"></a>Webanwendung

Eine Webanwendung führt Code auf einem Server aus, der HTML-, CSS- und JavaScript-Code generiert und zum Rendern an den Webbrowser eines Benutzers sendet. Die Identität des Benutzers wird zwischen dem Browser des Benutzers (dem Front-End) und dem Webserver (dem Back-End) als Sitzung verwaltet.

Da der Code einer Webanwendung auf dem Webserver ausgeführt wird, gilt er als *vertraulicher Client*, der Geheimnisse sicher speichern kann.

| Programmiersprache/Framework | Projekt auf<br/>GitHub                                                                                     | Paket                                                                                                    | Erste Schritte<br/>gestartet                               | Anmelden von Benutzern                                            | Zugriff auf Web-APIs                                                    | Allgemein verfügbar (Generally Available, GA) *oder*<br/>Öffentliche Vorschau<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![Bibliothek kann keine ID-Token für die Benutzeranmeldung anfordern][n] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y]    | Allgemein verfügbar                                                           |
| ASP.NET Core         | [ASP.NET Security](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y]    | ![Bibliothek kann keine Zugriffstoken für geschützte Web-APIs anfordern][n] | Allgemein verfügbar                                                           |
| ASP.NET Core         | [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y]    | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y]    | Allgemein verfügbar                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Schnellstart](quickstart-v2-java-webapp.md)        | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y]    | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y]    | Allgemein verfügbar                                                           |
| Node.js              | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                                                | [Schnellstart](quickstart-v2-nodejs-webapp-msal.md) | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y]    | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y]    | Public Preview                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [passport-azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [Schnellstart](quickstart-v2-nodejs-webapp.md)      | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y]    | ![Bibliothek kann keine Zugriffstoken für geschützte Web-APIs anfordern][n] | Allgemein verfügbar                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Schnellstart](quickstart-v2-python-webapp.md)      | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y]    | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y]    | Allgemein verfügbar                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> Für Bibliotheken in der *öffentlichen Vorschau* gelten [ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen][preview-tos].

## <a name="desktop-application"></a>Desktopanwendung

Eine Desktopanwendung ist in der Regel (kompilierter) Binärcode, der eine Benutzeroberfläche bereitstellt und auf dem Desktop der Benutzer ausgeführt werden soll.

Da eine Desktopanwendung auf dem Desktop des Benutzers ausgeführt wird, gilt sie als *öffentlicher Client*, der Geheimnisse nicht sicher speichern kann.

| Programmiersprache/Framework | Projekt auf<br/>GitHub                                                                                     | Paket                                                                               | Erste Schritte<br/>gestartet                        | Anmelden von Benutzern                                         | Zugriff auf Web-APIs                                                 | Allgemein verfügbar (Generally Available, GA) *oder*<br/>Öffentliche Vorschau<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Public Preview                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| macOS (Swift/Obj-C)  | [MSAL für iOS und macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](tutorial-v2-ios.md)             | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Tutorial](tutorial-v2-windows-uwp.md)     | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Tutorial](tutorial-v2-windows-desktop.md) | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> Für Bibliotheken in der *öffentlichen Vorschau* gelten [ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen][preview-tos].

## <a name="mobile-application"></a>Mobile Anwendung

Eine mobile Anwendung ist in der Regel (kompilierter) Binärcode, der eine Benutzeroberfläche bereitstellt und auf dem mobilen Gerät der Benutzer ausgeführt werden soll.

Da eine mobile Anwendung auf dem mobilen Gerät des Benutzers ausgeführt wird, gilt sie als *öffentlicher Client*, der Geheimnisse nicht sicher speichern kann.

| Plattform          | Projekt auf<br/>GitHub                                                                          | Paket                                                                               | Erste Schritte<br/>gestartet                    | Anmelden von Benutzern                                         | Zugriff auf Web-APIs                                                 | Allgemein verfügbar (Generally Available, GA) *oder*<br/>Öffentliche Vorschau<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Schnellstart](quickstart-v2-android.md) | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| iOS (Swift/Obj-C) | [MSAL für iOS und macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](tutorial-v2-ios.md)         | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Bibliothek kann ID-Token für die Benutzeranmeldung anfordern][y] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> Für Bibliotheken in der *öffentlichen Vorschau* gelten [ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen][preview-tos].

## <a name="service--daemon"></a>Dienst/Daemon

Dienste und Daemons werden häufig für die Kommunikation zwischen Servern und andere unbeaufsichtigte Kommunikationsvorgänge (manchmal auch als *monitorlos* oder „headless“ bezeichnet) verwendet. Da dabei kein Benutzer Anmeldeinformationen eingeben oder den Zugriff auf Ressourcen genehmigen kann, authentifizieren sich diese Anwendungen nicht als Benutzer, sondern als sie selbst, wenn sie autorisierten Zugriff auf die Ressourcen einer Web-API anfordern.

Ein Dienst oder Daemon, der auf einem Server ausgeführt wird, gilt als *vertraulicher Client*, der seine Geheimnisse sicher speichern kann.

| Programmiersprache/Framework | Projekt auf<br/>GitHub                                                                 | Paket                                                                                | Erste Schritte<br/>gestartet                           | Anmelden von Benutzern                                            | Zugriff auf Web-APIs                                                 | Allgemein verfügbar (Generally Available, GA) *oder*<br/>Öffentliche Vorschau<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Schnellstart](quickstart-v2-netcore-daemon.md) | ![Bibliothek kann keine ID-Token für die Benutzeranmeldung anfordern][n] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Bibliothek kann keine ID-Token für die Benutzeranmeldung anfordern][n] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Schnellstart](quickstart-v2-python-daemon.md)  | ![Bibliothek kann keine ID-Token für die Benutzeranmeldung anfordern][n] | ![Bibliothek kann Zugriffstoken für geschützte Web-APIs anfordern][y] | Allgemein verfügbar                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> Für Bibliotheken in der *öffentlichen Vorschau* gelten [ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen][preview-tos].

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Microsoft-Authentifizierungsbibliothek finden Sie in der [Übersicht über die Microsoft-Authentifizierungsbibliothek (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
