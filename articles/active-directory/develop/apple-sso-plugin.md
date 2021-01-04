---
title: Microsoft Enterprise SSO-Plug-In für Apple-Geräte
titleSuffix: Microsoft identity platform | Azure
description: Enthält eine Beschreibung des Azure Active Directory-SSO-Plug-Ins von Microsoft für iOS- und macOS-Geräte.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: b7ec6ab8b52d9d43d898f481a2f36310e5c0897d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561079"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO-Plug-In für Apple-Geräte (Vorschau)

>[!IMPORTANT]
> Dieses Feature [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Das *Microsoft Enterprise SSO-Plug-In für Apple-Geräte* ermöglicht einmaliges Anmelden (Single Sign-On, SSO) für Azure AD-Konten (Azure Active Directory) über alle Anwendungen hinweg, die das Feature [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) von Apple unterstützen. Microsoft hat bei der Entwicklung dieses Plug-Ins eng mit Apple zusammengearbeitet, um die Nutzbarkeit Ihrer Anwendung zu erhöhen, während gleichzeitig der bestmögliche Schutz durch Apple und Microsoft gewährt wird.

In diesem Public Preview-Release ist das Enterprise SSO-Plug-In nur für iOS-Geräte verfügbar und in bestimmten Microsoft-Anwendungen enthalten.

## <a name="features"></a>Features

Mit dem Microsoft Enterprise SSO-Plug-In für Apple-Geräte ergeben sich für Sie die folgenden Vorteile:

- SSO für Azure AD-Konten über alle Anwendungen hinweg, die das Feature „Enterprise Single Sign-On“ von Apple unterstützen.
- Automatische Bereitstellung in Microsoft Authenticator und Aktivierung mit jeder Lösung für die mobile Geräteverwaltung (Mobile Device Management, MDM).

## <a name="requirements"></a>Anforderungen

Für die Nutzung des Microsoft Enterprise SSO-Plug-Ins für Apple-Geräte gelten die folgenden Anforderungen:

- iOS 13.0 oder höher muss auf dem Gerät installiert sein.
- Eine Microsoft-Anwendung, über die das Microsoft Enterprise SSO-Plug-In für Apple-Geräte bereitgestellt wird, muss auf dem Gerät installiert sein. In der Public Preview-Phase enthalten diese Anwendungen die [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md).
- Das Gerät muss über die mobile Geräteverwaltung registriert werden (z. B. mit Microsoft Intune).
- Die Konfiguration muss per Pushvorgang auf das Gerät übertragen werden, um darauf das Microsoft Enterprise SSO-Plug-In für Apple-Geräte zu aktivieren. Diese Sicherheitseinschränkung wird von Apple gefordert.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Aktivieren des SSO-Plug-Ins mit der mobilen Geräteverwaltung (Mobile Device Management, MDM)

An Ihre Geräte muss über einen MDM-Dienst ein Signal gesendet werden, um das Microsoft Enterprise SSO-Plug-In für Apple-Geräte zu aktivieren. Da das Microsoft Enterprise SSO-Plug-In in die [Microsoft Authenticator-App](..//user-help/user-help-auth-app-overview.md) eingebunden wurde, können Sie Ihre MDM-Lösung zum Konfigurieren der App nutzen, um das Microsoft Enterprise SSO-Plug-In zu aktivieren.

Verwenden Sie die folgenden Parameter, um das Microsoft Enterprise SSO-Plug-In für Apple-Geräte zu konfigurieren:

- **Typ:** Umleiten
- **Erweiterungs-ID**: `com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**: (dieses Feld wird für iOS nicht benötigt)
- **URLs**:
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Zusätzliche Konfigurationsoptionen
Zusätzliche Konfigurationsoptionen können hinzugefügt werden, um die SSO-Funktionalität auf weitere Apps auszuweiten.

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Aktivieren von SSO für Apps, die MSAL nicht verwenden

Mit dem SSO-Plug-In können alle Anwendungen vom einmaligen Anmelden (SSO) partizipieren, auch wenn sie nicht mit einem Microsoft-SDK wie Microsoft Authentication Library (MSAL) entwickelt wurden.

Das SSO-Plug-In wird automatisch auf Geräten installiert, auf denen die Microsoft Authenticator-App heruntergeladen wurde und die bei Ihrer Organisation registriert wurden. Ihre Organisation verwendet die Authenticator-App aktuell wahrscheinlich für Szenarien wie Multi-Factor Authentication, kennwortlose Authentifizierung und bedingten Zugriff. Sie kann mithilfe eines beliebigen MDM-Anbieters für Ihre Anwendungen aktiviert werden, auch wenn Microsoft das Konfigurieren in Microsoft Endpoint Manager von Intune vereinfacht hat. Zum Konfigurieren dieser Anwendungen für die Verwendung des SSO-Plug-Ins, das durch die Authenticator-App installiert wurde, wird eine Positivliste verwendet.

Es werden nur Apps unterstützt, die native Apple-Netzwerktechnologien oder -Webansichten verwenden. Wenn eine Anwendung ihre eigene Implementierung der Netzwerkschicht beinhaltet, wird das Microsoft Enterprise SSO-Plug-In nicht unterstützt.  

Verwenden Sie die folgenden Parameter, um das Microsoft Enterprise SSO-Plug-In für Apps zu konfigurieren, die MSAL nicht verwenden:

- **Schlüssel**: `AppAllowList`
- **Typ:** `String`
- **Value**: Kommagetrennte Liste mit den Bundle-IDs der Anwendungen, die für das einmalige Anmelden (SSO) zugelassen sind.
- **Beispiel**: `com.contoso.workapp, com.contoso.travelapp`

[Mit Zustimmung versehene Apps](./application-consent-experience.md), denen der MDM-Administrator die Genehmigung für die SSO-Teilnahme erteilt hat, können automatisch ein Token für den Endbenutzer erhalten. Daher ist es wichtig, der Positivliste nur vertrauenswürdige Anwendungen hinzuzufügen. 

Sie brauchen dieser Liste keine Anwendungen hinzuzufügen, die MSAL oder ASWebAuthenticationSession verwenden. Diese Anwendungen sind standardmäßig aktiviert. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Zulassen der SSO-Sitzungserstellung von beliebigen Anwendungen

Standardmäßig stellt das Microsoft Enterprise SSO-Plug-In einmaliges Anmelden (SSO) für autorisierte Apps nur dann bereit, wenn das SSO-Plug-In bereits über freigegebene Anmeldeinformationen verfügt. Das Microsoft Enterprise SSO-Plug-In kann freigegebene Anmeldeinformationen abrufen, wenn es beim Tokenerwerb von einer anderen auf ADAL oder MSAL basierenden Anwendung aufgerufen wird. Die meisten Microsoft-Apps verwenden Microsoft Authenticator oder das SSO-Plug-In. Dies bedeutet, dass einmaliges Anmelden (SSO) außerhalb des nativen Anwendungsflows standardmäßig die größte Mühe macht.  

Durch Aktivieren des `browser_sso_interaction_enabled`-Flags können Apps ohne MSAL und der Safari-Browser das anfängliche Bootstrapping ausführen und freigegebene Anmeldeinformationen abrufen. Wenn das Microsoft Enterprise SSO-Plug-In noch nicht über freigegebene Anmeldeinformationen verfügt, versucht es jedes Mal Anmeldeinformationen abzurufen, wenn von einer Azure AD-URL im Safari-Browser, von ASWebAuthenticationSession, von SafariViewController oder von einer anderen zugelassenen nativen Anwendung eine Anmeldung angefordert wird.  

- **Schlüssel**: `browser_sso_interaction_enabled`
- **Typ:** `Integer`
- **Value**: 1 oder 0

Wir empfehlen, dieses Flag zu aktivieren, um in allen Apps eine einheitlichere Darstellung zu erzielen. Diese Einstellung ist standardmäßig deaktiviert. 

#### <a name="disable-oauth2-application-prompts"></a>Deaktivieren von Eingabeaufforderungen durch OAuth2-Anwendungen

Das Microsoft Enterprise SSO-Plug-In ermöglicht einmaliges Anmelden (SSO), indem es an Netzwerkanforderungen, die von zulässigen Anwendungen stammen, freigegebene Anmeldeinformationen anfügt. Einige OAuth2-Anwendungen erzwingen möglicherweise eine Endbenutzereingabeaufforderung auf Protokollebene. Freigegebene Anmeldeinformationen werden von diesen Apps ignoriert.  

Wenn Sie das `disable_explicit_app_prompt`-Flag aktivieren, wird die Fähigkeit von nativen und Webanwendungen, eine Endbenutzereingabeaufforderung auf der Protokollebene zu erzwingen und SSO zu umgehen, eingeschränkt.

- **Schlüssel**: `disable_explicit_app_prompt`
- **Typ:** `Integer`
- **Value**: 1 oder 0

Wir empfehlen, dieses Flag zu aktivieren, um in allen Apps eine einheitlichere Darstellung zu erzielen. Diese Einstellung ist standardmäßig deaktiviert. 

#### <a name="use-intune-for-simplified-configuration"></a>Verwenden von Intune zum Vereinfachen der Konfiguration

Sie können Microsoft Intune als Ihren MDM-Dienst verwenden, um die Konfiguration des Microsoft Enterprise SSO-Plug-Ins zu vereinfachen. Weitere Informationen finden Sie in der [Dokumentation zur Intune-Konfiguration](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Verwenden des SSO-Plug-Ins in Ihrer Anwendung

Version 1.1.0 (und höher) der [Microsoft Authentication Library (MSAL) für Apple-Geräte](https://github.com/AzureAD/microsoft-authentication-library-for-objc) unterstützt das Microsoft Enterprise SSO-Plug-In für Apple-Geräte.

Wenn Sie eine Anwendung für Mitarbeiter im Kundenkontakt oder vergleichbare Szenarien erstellen, finden Sie weitere Informationen zum Setup des Features unter [Modus für gemeinsam genutzte Geräte für iOS-Geräte](msal-ios-shared-devices.md).

## <a name="how-the-sso-plug-in-works"></a>Funktionsweise des SSO-Plug-Ins

Das Microsoft Enterprise SSO-Plug-In basiert auf dem [Enterprise Single Sign-On-Framework von Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). In das Framework integrierte Identitätsanbieter können den Netzwerkdatenverkehr für ihre Domänen abfangen und die Verarbeitung dieser Anforderungen verbessern oder ändern. Das SSO-Plug-In kann z. B. eine zusätzliche Benutzeroberfläche anzeigen, um Anmeldeinformationen von Endbenutzern sicher zu erfassen, MFA anzufordern oder automatisch Token für die Anwendung bereitzustellen.

Native Anwendungen können außerdem benutzerdefinierte Vorgänge implementieren und direkt mit dem SSO-Plug-In kommunizieren.
Weitere Informationen zum SSO-Framework erhalten Sie im [WWDC-Video von 2019 von Apple](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>Anwendungen, die MSAL verwenden

Version 1.1.0 (und höher) der [Microsoft Authentication Library (MSAL) für Apple-Geräte](https://github.com/AzureAD/microsoft-authentication-library-for-objc) unterstützt das Microsoft Enterprise SSO-Plug-In für Apple-Geräte nativ für Geschäfts-, Schul- oder Unikonten. 

Es ist keine besondere Konfiguration erforderlich, wenn Sie [alle empfohlenen Schritte](./quickstart-v2-ios.md) befolgen und das standardmäßige [Umleitungs-URI-Format](./redirect-uris-ios.md) verwenden. Bei der Ausführung auf einem Gerät, auf dem das SSO-Plug-In vorhanden ist, ruft MSAL das Plug-In automatisch für alle interaktiven und automatischen Tokenanforderungen sowie für Kontoenumerations- und Kontoentfernungsvorgänge auf. Da MSAL das native SSO-Plug-In-Protokoll implementiert, das auf benutzerdefinierten Vorgängen basiert, bietet dieses Setup die komfortabelste native Umgebung für den Endbenutzer. 

Wenn das SSO-Plug-In von MDM nicht aktiviert wird, aber die Microsoft Authenticator-App auf dem Gerät vorhanden ist, verwendet MSAL stattdessen die Microsoft Authenticator-App für alle interaktiven Tokenanforderungen. Das SSO-Plug-In gibt SSO für die Microsoft Authenticator-App frei.

### <a name="applications-that-dont-use-msal"></a>Anwendungen, die MSAL nicht verwenden

Anwendungen, die MSAL nicht verwenden, können trotzdem vom einmaligen Anmelden (SSO) profitieren, wenn sie von einem Administrator explizit zur Positivliste hinzugefügt werden. 

In diesen Apps sind keine Codeänderungen erforderlich, solange die folgenden Bedingungen erfüllt sind:

- Die Anwendung verwendet Apple-Frameworks, um Netzwerkanforderungen (z. B. [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) auszuführen. 
- Die Anwendung verwendet Standardprotokolle für die Kommunikation mit Azure AD (z. B. OAuth2, SAML, WS-Verbund).
- Die Anwendung erfasst auf der nativen Benutzeroberfläche keine Benutzernamen und Kennwörter in Klartext.

In diesem Fall wird SSO bereitgestellt, wenn die Anwendung eine Netzwerkanforderung erstellt und einen Webbrowser öffnet, um den Benutzer anzumelden. Wenn ein Benutzer zu einer Azure AD-Anmelde-URL umgeleitet wird, überprüft das SSO-Plug-In die URL und prüft, ob für diese URL SSO-Anmeldeinformationen verfügbar sind. Wenn diese vorhanden sind, übergibt das SSO-Plug-In die SSO-Anmeldeinformationen an Azure AD. Azure AD autorisiert dann die Anwendung, die Netzwerkanforderung abzuschließen, ohne den Benutzer zur Eingabe seiner Anmeldeinformationen aufzufordern. Wenn das Gerät Azure AD bekannt ist, übergibt das SSO-Plug-In außerdem das Gerätezertifikat, um die gerätebasierte bedingte Zugriffsüberprüfung zu erfüllen. 

Zur Unterstützung des einmaligen Anmeldens (SSO) für Apps ohne MSAL implementiert das SSO-Plug-In ein dem Windows-Browser-Plug-In vergleichbares Protokoll, das unter [Was ist ein primäres Aktualisierungstoken?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt) beschrieben ist. 

Im Vergleich zu MSAL-basierten Apps ist die Funktionsweise des SSO-Plug-Ins für Apps ohne MSAL transparenter, da die von den Apps bereitgestellte vorhandene Browseranmeldeumgebung integriert wird. Dem Endbenutzer wird die vertraute Umgebung angezeigt, und er hat zudem den Vorteil, dass er nicht in jeder Anwendung weitere Anmeldungen vornehmen muss. Anstatt die native Kontoauswahl anzuzeigen, fügt das SSO-Plug-In beispielsweise der webbasierten Kontoauswahlumgebung SSO-Sitzungen hinzu. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Modus für gemeinsam genutzte Geräte unter iOS finden Sie im Artikel [Modus für gemeinsam genutzte Geräte für iOS-Geräte](msal-ios-shared-devices.md).
