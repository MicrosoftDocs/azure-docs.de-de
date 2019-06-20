---
title: Authentifizierungsflows (Microsoft-Authentifizierungsbibliothek) | Azure
description: Erfahren Sie mehr über die von der Microsoft-Authentifizierungsbibliothek (MSAL) verwendeten Authentifizierungsflows/-gewährungstypen.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb9a6f162a10408469669cf40b29efc6d2903944
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546047"
---
# <a name="authentication-flows"></a>Authentifizierungsflows

In diesem Artikel werden die verschiedenen Authentifizierungsflows beschrieben, die von der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) bereitgestellt werden.  Diese Flows können in vielen verschiedenen Anwendungsszenarien verwendet werden.

| Flow | BESCHREIBUNG | Verwendet für|  
| ---- | ----------- | ------- | 
| [Interactive](#interactive) | Ruft in einem interaktiven Prozess das Token ab, durch das der Benutzer über einen Browser oder ein Popupfenster zur Eingabe von Anmeldeinformationen aufgefordert wird. | [Desktop-Apps](scenario-desktop-overview.md), [mobile Apps](scenario-mobile-overview.md) |
| [Implizite Gewährung](#implicit-grant) | Ermöglicht es der App, Token abzurufen, ohne dass die Anmeldeinformationen für den Back-End-Server ausgetauscht werden müssen. Dadurch kann die App den Benutzer anmelden, die Sitzung aufrechterhalten und Token für andere Web-APIs abrufen. All dies geschieht innerhalb des Client-JavaScript-Codes.| [Single-Page-Webanwendungen (SPA)](scenario-spa-overview.md) |
| [Autorisierungscode](#authorization-code) | Wird in Apps verwendet, die auf einem Gerät installiert sind, um auf geschützte Ressourcen wie Web-APIs zuzugreifen. Dadurch können Sie mobile und Desktop-Apps mit Anmeldefunktionen und API-Zugriff ausstatten. | [Desktop-Apps](scenario-desktop-overview.md), [mobile Apps](scenario-mobile-overview.md), [Web-Apps](scenario-web-app-call-api-overview.md) | 
| [OBO (On-Behalf-Of)](#on-behalf-of) | Eine Anwendung ruft eine Dienst- oder Web-API auf, die wiederum eine andere Dienst- oder Web-API aufrufen muss. Die Idee dabei ist, die delegierte Benutzeridentität und Berechtigungen über die Anforderungskette weiterzugeben. | [Web-APIs](scenario-web-api-call-api-overview.md) |
| [Clientanmeldeinformationen](#client-credentials) | Ermöglicht es, über die Identität einer Anwendung auf Ressourcen zugreifen, die im Web gehostet werden. Wird häufig für Interaktionen zwischen Servern verwendet, die ohne direkten Benutzereingriff im Hintergrund ausgeführt werden müssen. | [Daemon-Apps](scenario-daemon-overview.md) |
| [Gerätecode](#device-code) | Ermöglicht es Benutzern, sich bei Geräten mit Eingabeeinschränkung wie einem Smart-TV, IoT-Gerät oder Drucker anzumelden. | [Desktop-/mobile Apps](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Integrierte Windows-Authentifizierung](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Ermöglicht es Anwendungen auf Computern, die in eine Domäne oder Azure AD eingebunden sind, (ohne Eingriff des Benutzers über die Benutzeroberfläche) automatisch ein Token abzurufen.| [Desktop-/mobile Apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Benutzername/Kennwort](scenario-desktop-acquire-token.md#username--password) | Ermöglicht es einer Anwendung, den Benutzer durch die direkte Verarbeitung seines Kennworts anzumelden. Dieser Flow wird nicht empfohlen. | [Desktop-/mobile Apps](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interactive
MSAL bietet die Möglichkeit, die Anmeldeinformationen interaktiv vom Benutzer abzufragen und mit diesen Anmeldeinformationen ein Token abzurufen.

![Interaktiver Flow](media/msal-authentication-flows/interactive.png)

Im Folgenden finden Sie weitere Informationen zur Verwendung von MSAL.NET für den interaktiven Abruf von Token für bestimmte Plattformen:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Universelle Windows-Plattform](msal-net-uwp-considerations.md)

Weitere Informationen zu interaktiven Aufrufen in MSAL.js finden Sie im Thema zum [Aufforderungsverhalten in interaktiven MSAL.js-Anforderungen](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit grant (Implizite Gewährung)

MSAL unterstützt den [impliziten OAuth 2-Gewährungsflow](v2-oauth2-implicit-grant-flow.md), wodurch die App Token von Microsoft Identity Platform abrufen kann, ohne dass die Anmeldeinformationen für den Back-End-Server ausgetauscht werden müssen. Dadurch kann die App den Benutzer anmelden, die Sitzung aufrechterhalten und Token für andere Web-APIs abrufen. All dies geschieht innerhalb des Client-JavaScript-Codes.

![Impliziter Gewährungsflow](media/msal-authentication-flows/implicit-grant.svg)

Viele moderne Webanwendungen werden als clientseitige Single-Page-Webanwendung erstellt, die mithilfe von JavaScript- oder SPA-Frameworks wie Angular, Vue.js oder React.js geschrieben werden. Diese Anwendungen werden in einem Webbrowser ausgeführt und haben andere Authentifizierungsmerkmale als herkömmliche serverseitige Webanwendungen. Microsoft Identity Platform ermöglicht Single-Page-Webanwendungen mithilfe des impliziten Gewährungsflows das Anmelden von Benutzern und Abrufen von Token für den Zugriff auf Back-End-Dienste oder -Web-APIs. Durch den impliziten Flow kann die Anwendung ID-Token abrufen, um den authentifizierten Benutzer darzustellen, sowie Zugriffstoken, die zum Aufrufen geschützter APIs erforderlich sind.

Dieser Authentifizierungsflow umfasst keine Anwendungsszenarien, in denen plattformübergreifende JavaScript-Frameworks, wie Electron und React-Native, verwendet werden, da sie weitere Funktionen für die Interaktion mit den nativen Plattformen erfordern.

## <a name="authorization-code"></a>Authorization code (Autorisierungscode)
MSAL unterstützt den [OAuth 2-Flow zum Gewähren eines Autorisierungscodes](v2-oauth2-auth-code-flow.md), der in Apps verwendet werden kann, die auf einem Gerät installiert sind, um Zugriff auf geschützte Ressourcen wie Web-APIs zu erhalten. Dadurch können Sie mobile und Desktop-Apps mit Anmeldefunktionen und API-Zugriff ausstatten. 

Wenn sich Benutzer bei Webanwendungen (Websites) anmelden, erhält die Webanwendung einen Autorisierungscode.  Der Autorisierungscode wird eingelöst, um ein Token zum Aufrufen von Web-APIs abzurufen. In ASP.NET-/ASP.NET Core-Web-Apps besteht das einzige Ziel von `AcquireTokenByAuthorizationCode` darin, dem Tokencache ein Token hinzuzufügen, damit es dann von der Anwendung (normalerweise in den Controllern) verwendet werden kann, die mit `AcquireTokenSilent` einfach nur ein Token für eine API abruft.

![Autorisierungscodeflow](media/msal-authentication-flows/authorization-code.png)

1. Fordert einen Autorisierungscode an, der gegen ein Zugriffstoken eingelöst wird.
2. Verwendet das Zugriffstoken zum Aufrufen einer Web-API.

### <a name="considerations"></a>Überlegungen
- Der Autorisierungscode kann nur einmal zum Einlösen eines Tokens verwendet werden. Versuchen Sie nicht, ein Token mehrmals mit demselben Autorisierungscode abzurufen (dies ist laut Protokollstandardspezifikation explizit untersagt). Wenn Sie den Code absichtlich mehrmals einlösen, oder sich nicht bewusst sind, dass der Code ebenfalls von einem Framework für Sie eingelöst wird, erhalten Sie eine Fehlermeldung: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Beim Schreiben einer ASP.NET-/ASP.NET Core-Anwendung kann dies geschehen, wenn Sie das ASP.NET-/Core-Framework nicht darüber informieren, dass Sie den Autorisierungscode bereits eingelöst haben. Dazu müssen Sie die `context.HandleCodeRedemption()`-Methode des `AuthorizationCodeReceived`-Ereignishandlers aufrufen.

- Sie sollten das Zugriffstoken nicht mit ASP.NET freigeben, weil dadurch verhindert werden kann, dass die inkrementelle Zustimmung ordnungsgemäß abläuft.  Weitere Informationen finden Sie unter [Problem Nr. 693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>OBO (On-Behalf-Of)

MSAL unterstützt den [OAuth 2-On-Behalf-Of-Authentifizierungsflow](v2-oauth2-on-behalf-of-flow.md).  Dieser Flow wird verwendet, wenn eine Anwendung eine Dienst- oder Web-API aufruft, die wiederum eine andere Dienst- oder Web-API aufrufen muss. Die Idee dabei ist, die delegierte Benutzeridentität und Berechtigungen über die Anforderungskette weiterzugeben. Damit der Dienst auf der mittleren Ebene Authentifizierungsanforderungen an den Downstreamdienst stellen kann, muss für den Benutzer ein Zugriffstoken der Microsoft Identity Platform gesichert werden.

![„Im Auftrag von“-Ablauf](media/msal-authentication-flows/on-behalf-of.png)

1. Ruft ein Zugriffstoken für die Web-API ab.
2. Ein Client (Web-, Desktop-, mobile Anwendung oder Single-Page-Webanwendung) ruft eine geschützte Web-API auf und fügt dabei das Zugriffstoken als Bearertoken in den Authentifizierungsheader der HTTP-Anforderung ein. Die Web-API authentifiziert den Benutzer.
3. Wenn der Client die Web-API aufruft, fordert die Web-API ein weiteres Token im Namen des (On-Behalf-Of) Benutzers an.  
4. Die geschützte Web-API verwendet dieses Token, um eine Downstream-Web-API im Namen des Benutzers aufzurufen.  Die Web-API kann später auch Token für andere Downstream-APIs anfordern (allerdings immer noch im Namen desselben Benutzers).

## <a name="client-credentials"></a>Client credentials (Clientanmeldeinformationen)

MSAL unterstützt den [OAuth 2-Clientanmeldeinformations-Flow](v2-oauth2-client-creds-grant-flow.md). Mit diesem Flow können Sie über die Identität einer Anwendung auf Ressourcen zugreifen, die im Web gehostet werden. Diese Art der Gewährung wird häufig für Interaktionen zwischen Servern verwendet, die ohne Benutzereingriff im Hintergrund ausgeführt werden müssen. Diese Anwendungstypen werden oft als Daemons oder Dienstkonten bezeichnet. 

Beim Flow zur Gewährung von Clientanmeldeinformationen kann ein Webdienst (ein vertraulicher Client) seine eigenen Anmeldeinformationen zum Authentifizieren verwenden, wenn ein anderer Webdienst aufgerufen wird, anstatt die Identität eines Benutzers anzunehmen. In diesem Szenario ist der Client normalerweise ein Webdienst der mittleren Ebene, ein Daemondienst oder eine Website. Für ein höheres Maß an Sicherheit bietet die Microsoft Identity Platform auch die Möglichkeit, dass der aufrufende Dienst ein Zertifikat (statt eines gemeinsamen Geheimnisses) als Anmeldeinformationen verwendet.

> [!NOTE]
> Der Flow für vertrauliche Clients ist auf mobilen Plattformen (UWP, Xamarin.iOS und Xamarin.Android) nicht verfügbar, da diese nur öffentliche Clientanwendungen unterstützen.  Öffentliche Clientanwendungen sind nicht in der Lage, die Identität der Anwendung gegenüber dem Identitätsanbieter nachzuweisen. Um eine sichere Verbindung für Web-App- oder Web-API-Back-Ends zu erzielen, können Sie ein Zertifikat bereitstellen.

MSAL.NET unterstützt zwei Arten von Clientanmeldeinformationen. Diese Clientanmeldeinformationen müssen in Azure AD registriert werden. Die Anmeldeinformationen werden an die Konstruktoren der vertraulichen Clientanwendung im Code übergeben.

### <a name="application-secrets"></a>Anwendungsgeheimnisse 

![Vertraulicher Client mit Kennwort](media/msal-authentication-flows/confidential-client-password.png)

1. Ruft ein Token unter Verwendung der Anmeldeinformationen ab, die aus Anwendungsgeheimnis/Kennwort bestehen.
2. Verwendet das Token für Anforderungen an die Ressource.

### <a name="certificates"></a>Zertifikate 

![Vertraulicher Client mit Zertifikat](media/msal-authentication-flows/confidential-client-certificate.png)

1. Ruft ein Token unter Verwendung von Zertifikatanmeldeinformationen ab.
2. Verwendet das Token für Anforderungen an die Ressource.

Die Clientanmeldeinformationen müssen folgende Voraussetzungen erfüllen:
- Sie wurden in Azure AD registriert.
- Sie wurden bei der Erstellung der vertraulichen Clientanwendung im Code übergeben.


## <a name="device-code"></a>Gerätecode
MSAL unterstützt den [OAuth 2-Gerätecodeflow](v2-oauth2-device-code.md), der es Benutzern ermöglicht, sich bei Geräten mit Eingabeeinschränkung wie einem Smart-TV, IoT-Gerät oder Drucker anzumelden. Für die interaktive Authentifizierung mit Azure AD wird ein Webbrowser benötigt. Der Gerätecodeflow ermöglicht es dem Benutzer, ein anderes Gerät (z. B. einen anderen Computer oder ein Mobiltelefon) zu verwenden, um sich interaktiv anzumelden, wenn das Gerät oder Betriebssystem keinen Webbrowser bereitstellt.

Mithilfe des Gerätecodeflows ruft die Anwendung Token in einem zweistufigen Prozess ab, der speziell für diese Geräte/Betriebssysteme entwickelt wurde. Beispiele sind Anwendungen auf iOT-Geräten oder Befehlszeilentools (CLI). 

![Gerätecodefluss](media/msal-authentication-flows/device-code.png)

1. Sobald eine Benutzerauthentifizierung erforderlich ist, stellt die App einen Code bereit und fordert den Benutzer auf, mit einem anderen Gerät (z. B. einem Smartphone mit Internetverbindung) zu einer URL (z. B. https://microsoft.com/devicelogin) zu navigieren, unter der er den Code eingeben muss. Anschließend wird der Benutzer auf der Webseite durch einen normalen Authentifizierungsprozess geführt, der ggf. Zustimmungsaufforderungen und eine mehrstufige Authentifizierung umfasst.

2. Nach erfolgreicher Authentifizierung empfängt die Befehlszeilen-App die erforderlichen Token über einen Backchannel und führt damit die benötigten Web-API-Aufrufe aus.

### <a name="constraints"></a>Einschränkungen

- Der Gerätecodeflow ist nur in öffentlichen Clientanwendungen verfügbar.
- Die beim Erstellen der öffentlichen Clientanwendung übergebene Autorität muss folgende Voraussetzungen erfüllen:
  - Sie muss auf Mandanten beruhen (im Format `https://login.microsoftonline.com/{tenant}/`, wobei `{tenant}` entweder die GUID ist, die die Mandanten-ID darstellt, oder eine Domäne, die dem Mandanten zugeordnet ist).
  - Sie muss einem Geschäfts-, Schul- oder Unikonto entsprechen (`https://login.microsoftonline.com/organizations/`).
- Persönliche Microsoft-Konten werden vom Azure AD v2.0-Endpunkt noch nicht unterstützt (Mandanten vom Typ `/common` oder `/consumers` können nicht verwendet werden).

## <a name="integrated-windows-authentication"></a>Integrierte Windows-Authentifizierung
MSAL unterstützt die integrierte Windows-Authentifizierung (IWA) für Desktop- oder mobile Anwendungen auf Windows-Computern, die in eine Domäne oder in Azure AD eingebunden sind. Mit IWA können diese Anwendungen automatisch ein Token abrufen (ohne dass der Benutzer mit der Benutzeroberfläche interagieren muss). 

![Integrierte Windows-Authentifizierung](media/msal-authentication-flows/integrated-windows-authentication.png)

1. Ruft ein Token mithilfe der integrierten Windows-Authentifizierung ab.
2. Verwendet das Token für Anforderungen an die Ressource.

### <a name="constraints"></a>Einschränkungen

IWA unterstützt nur **Partnerbenutzer**.  In einem Active Directory erstellte und von Azure Active Directory unterstützte Benutzer. Direkt in Azure AD erstellte Benutzer ohne AD-Unterstützung (d. h. **verwaltete Benutzer**) können diesen Authentifizierungsflow nicht verwenden. Diese Einschränkung wirkt sich nicht auf den [Benutzername/Kennwort](#usernamepassword)-Flow aus.

IWA ist für Apps bestimmt, die für die .NET Framework- und .NET Core-Plattform und die universelle Windows-Plattform geschrieben wurden.

Die MFA (mehrstufige Authentifizierung) wird von IWA NICHT umgangen. Wenn die MFA konfiguriert ist, kann IWA fehlschlagen, wenn eine MFA-Aufforderung erforderlich ist, da bei der MFA eine Benutzerinteraktion benötigt wird. Dies ist eine komplizierte Situation. IWA ist nicht interaktiv, während die zweistufige Authentifizierung (2FA) jedoch eine Benutzerinteraktion erfordert. Wann der Identitätsanbieter eine zweistufige Authentifizierung anfordert, hängt nicht von Ihnen, sondern vom Mandantenadministrator ab. Wir haben festgestellt, dass die zweistufige Authentifizierung erforderlich ist, wenn Sie sich aus dem Ausland anmelden, wenn Sie nicht über ein VPN mit einem Unternehmensnetzwerk verbunden sind und gelegentlich sogar dann, wenn eine VPN-Verbindung besteht. Sie sollten keine verbindlichen Regeln erwarten, da Azure Active Directory anhand von KI kontinuierlich ermittelt, ob eine zweistufige Authentifizierung erforderlich ist. Falls IWA fehlschlägt, sollten Sie auf eine Eingabeaufforderung (https://aka.ms/msal-net-interactive) ausweichen.

Die beim Erstellen der öffentlichen Clientanwendung übergebene Autorität muss folgende Voraussetzungen erfüllen:
- Sie muss auf Mandanten beruhen (im Format `https://login.microsoftonline.com/{tenant}/`, wobei `tenant` entweder die GUID ist, die die Mandanten-ID darstellt, oder eine Domäne, die dem Mandanten zugeordnet ist).
- Sie muss einem Geschäfts-, Schul- oder Unikonto entsprechen (`https://login.microsoftonline.com/organizations/`). Persönliche Microsoft-Konten werden nicht unterstützt (Mandanten vom Typ `/common` oder `/consumers` können nicht verwendet werden).

IWA ist ein automatischer Flow, sodass
- der Benutzer zuvor zugestimmt haben muss, Ihre Anwendung zu nutzen, 
- oder der Mandantenadministrator für alle Benutzer im Mandanten zuvor zugestimmt haben muss, die Anwendung zu nutzen.
- Dies bedeutet Folgendes:
    - Entweder Sie als Entwickler haben im Azure-Portal die Schaltfläche **Gewähren** für sich selbst ausgewählt, 
    - oder ein Mandantenadministrator hat bei der Anwendungsregistrierung auf der Registerkarte **API-Berechtigungen** die Schaltfläche **Administratoreinwilligung für {Mandantendomäne} erteilen/widerrufen** ausgewählt (siehe [Hinzufügen von Zugriffsberechtigungen für Web-APIs](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)),
    - oder Sie haben Benutzern die Möglichkeit eingeräumt, der Anwendung zuzustimmen (siehe [Anfordern der Zustimmung einzelner Benutzer](v2-permissions-and-consent.md#requesting-individual-user-consent)),
    - oder Sie haben dem Mandantenadministrator die Möglichkeit eingeräumt, der Anwendung zuzustimmen (siehe [Anfordern der Zustimmung für einen gesamten Mandanten](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Der IWA-Flow ist für .NET-Desktop-, .NET Core- und UWP-Apps (universelle Windows-Plattform) aktiviert. Für .NET Core ist nur die Überladung verfügbar, die den Benutzernamen annimmt, da die .NET Core-Plattform nicht den Benutzernamen für das Betriebssystem anfordern kann.
  
Weitere Informationen zur Zustimmung finden Sie unter [Berechtigungen und Zustimmung für v2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Benutzername/Kennwort 
MSAL unterstützt die [OAuth 2-Gewährung für Kennwortanmeldeinformationen des Ressourcenbesitzers](v2-oauth-ropc.md). So kann eine Anwendung Benutzer anmelden, indem sie ihr Kennwort direkt verarbeitet. Sie können in Ihrer Desktopanwendung den Benutzername/Kennwort-Flow verwenden, um ein Token automatisch abzurufen. Bei Verwendung der Anwendung ist keine Benutzeroberfläche erforderlich.

![Benutzername/Kennwort-Flow](media/msal-authentication-flows/username-password.png)

1. Ruft ein Token ab, indem der Benutzername und das Kennwort an den Identitätsanbieter gesendet werden.
2. Ruft eine Web-API mit dem Token auf.

> [!WARNING]
> Von diesem Flow wird **abgeraten**, da er ein hohes Maß an Vertrauen und die Offenlegung von Benutzerinformationen erfordert.  Verwenden Sie diesen Flow nur, wenn kein anderer Flow verfügbar ist, der mehr Sicherheit bietet. Weitere Informationen zu diesem Problem erhalten Sie in [diesem Artikel](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Der bevorzugte Flow für das automatische Abrufen eines Tokens auf Computern in Windows-Domänen ist die [integrierte Windows-Authentifizierung](#integrated-windows-authentication). Alternativ können Sie auch den [Gerätecodeflow](#device-code) verwenden.

Dies ist u. U. gelegentlich hilfreich (in DevOps-Szenarien). Wenn Sie jedoch Benutzername/Kennwort in interaktiven Szenarien verwenden möchten, in denen Sie eine eigene Benutzeroberfläche bereitstellen, sollten Sie eine Umstellung erwägen. Die Verwendung von Benutzername/Kennwort birgt eine Reihe von Nachteilen und Risiken:
- Core-Mandanten mit moderner Identität: Kennwörter werden durch Phishing entwendet und wiedergegeben. Dies liegt am Konzept des gemeinsamen geheimen Schlüssels, der abgefangen werden kann.
Dies ist inkompatibel mit einem Szenario ohne Kennwort.
- Benutzer, die die MFA verwenden müssen, können sich nicht anmelden (da keine Interaktion erfolgt).
- Einmaliges Anmelden (SSO) ist für Benutzer nicht möglich.

### <a name="constraints"></a>Einschränkungen

Neben den [Einschränkungen der integrierten Windows-Authentifizierung](#integrated-windows-authentication) gelten zusätzlich folgende Einschränkungen:

- Der Benutzername/Kennwort-Flow ist mit dem bedingten Zugriff und der mehrstufigen Authentifizierung nicht kompatibel: Wenn Ihre App daher in einem Azure AD-Mandanten ausgeführt wird, für den der Mandantenadministrator die mehrstufige Authentifizierung erfordert, können Sie diesen Flow nicht nutzen. Dies ist aber in vielen Organisationen der Fall.
- Er funktioniert nur für Geschäfts-, Schul- oder Unikonten (nicht für MSA).
- Der Flow ist für .NET-Desktop- und .NET Core-Plattformen, aber nicht für die universelle Windows-Plattform verfügbar.

### <a name="azure-ad-b2c-specifics"></a>Spezifische Informationen zu Azure AD B2C

Weitere Informationen zur Verwendung von MSAL.NET und Azure AD B2C finden Sie unter [Verwenden von ROPC mit Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
