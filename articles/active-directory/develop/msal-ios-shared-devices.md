---
title: Modus für gemeinsam genutzte Geräte für iOS-Geräte
titleSuffix: Microsoft identity platform | Azure
description: Erfahren Sie, wie Sie den Modus für gemeinsam genutzte Geräte aktivieren, über den Mitarbeiter in Service und Produktion ein iOS-Gerät gemeinsam nutzen können
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a97e14bcb68629f5f175a4913146187949af08be
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561062"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modus für gemeinsam genutzte Geräte für iOS-Geräte

>[!IMPORTANT]
> Dieses Feature [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Mitarbeiter in Service und Produktion, z. B. Mitarbeiter im Einzelhandel, Mitglieder der Flugzeugbesatzung und Außendienstmitarbeiter, verwenden für ihre Arbeit häufig ein gemeinsam genutztes mobiles Gerät. Diese gemeinsam genutzten Geräte können Sicherheitsrisiken darstellen, wenn Benutzer Kennwörter oder Pins absichtlich oder versehentlich teilen, um auf Kunden- und Geschäftsdaten auf dem gemeinsam genutzten Gerät zuzugreifen.

Im Modus für gemeinsam genutzte Geräte können Sie ein Gerät mit iOS 13 oder höher konfigurieren, damit es einfacher und sicherer von Mitarbeitern gemeinsam genutzt werden kann. Mitarbeiter können sich anmelden und schnell auf Kundeninformationen zugreifen. Nach ihrer Schicht oder nach Abschluss der Aufgabe können sie sich auf dem Gerät abmelden, das dann sofort für den nächsten Mitarbeiter einsatzbereit ist.

Der Modus für gemeinsam genutzte Geräte ermöglicht auch eine auf Microsoft-Identitäten basierende Verwaltung des Geräts.

Diese Funktion verwendet die [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md), um die Benutzer auf dem Gerät zu verwalten und das [Microsoft Enterprise SSO-Plug-In für Apple-Geräte](apple-sso-plugin.md) zu verteilen.

## <a name="create-a-shared-device-mode-app"></a>Erstellen einer App für den Modus für gemeinsam genutzte Geräte

Bei der Erstellung einer App für den Modus für gemeinsam genutzte Geräte arbeiten Entwickler und Cloudgeräteadministratoren zusammen:

1. **Anwendungsentwickler** schreiben eine App für ein einzelnes Konto (Apps für mehrere Konten werden im Modus für gemeinsam genutzte Geräte nicht unterstützt) sowie Code zum Ausführen von Vorgängen (z. B. das Abmelden vom gemeinsam genutzten Gerät).

1. **Geräteadministratoren** bereiten die Geräte mithilfe eines MDM-Anbieters (Mobile Device Management) wie Microsoft Intune für die gemeinsame Nutzung vor, um sie in ihrer Organisation zu verwalten. MDM überträgt die Microsoft Authenticator-App per Pushvorgang auf die Geräte und aktiviert auf jedem Gerät durch ein Profilupdate den „Modus für gemeinsam genutzte Geräte“. Durch die Einstellung „Modus für gemeinsam genutzte Geräte“ ändert sich das Verhalten der unterstützten Apps auf dem Gerät. Diese Konfiguration vom MDM-Anbieter legt für das Gerät den Modus für gemeinsam genutzte Geräte fest und aktiviert das [Microsoft Enterprise SSO-Plug-In für Apple-Geräte](apple-sso-plugin.md), das für den Modus für gemeinsam genutzte Geräte erforderlich ist.

1. [**Nur bei der öffentlichen Vorschauversion (Public Preview) erforderlich**] Ein Benutzer mit der Rolle [Cloudgeräteadministrator](../roles/permissions-reference.md#cloud-device-administrator) muss dann die [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md) starten und das Gerät mit der Organisation verknüpfen.

    So konfigurieren Sie die Mitgliedschaft der organisatorischen Funktionen im Azure-Portal: **Azure Active Directory** > **Rollen und Administratoren** > **Cloudgeräteadministrator**

In den folgenden Abschnitten erfahren Sie, wie Sie Ihre Anwendung aktualisieren, um den Modus für gemeinsam genutzte Geräte zu unterstützen.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Verwenden von Intune zum Aktivieren des Modus für gemeinsam genutzte Geräte und SSO-Erweiterung

> [!NOTE]
> Der folgende Schritt ist nur bei der öffentlichen Vorschauversion (Public Review) erforderlich.

Ihr Gerät muss konfiguriert werden, um den Modus für gemeinsam genutzte Geräte zu unterstützen. Auf dem Gerät muss iOS 13 oder höher installiert sein, und das Gerät muss für MDM registriert sein. Mit der MDM-Konfiguration muss auch das [Microsoft Enterprise SSO-Plug-In für Apple-Geräte](apple-sso-plugin.md) aktiviert werden. Weitere Informationen zu SSO-Erweiterungen finden Sie im [Apple-Video](https://developer.apple.com/videos/play/tech-talks/301/).

1. Aktivieren Sie für das Gerät im Intune-Konfigurationsportal das [Microsoft Enterprise SSO-Plug-In für Apple-Geräte](apple-sso-plugin.md) mit der folgenden Konfiguration:

    - **Typ:** Redirect
    - **Erweiterungs-ID**: com.microsoft.azureauthenticator.ssoextension
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
    - **Zusätzlich zu konfigurierende Daten**:
      - Schlüssel: sharedDeviceMode
      - Typ: Boolean
      - Wert: true

    Weitere Informationen zum Konfigurieren mit Intune finden Sie in der [Dokumentation zur Konfiguration von Intune](/intune/configuration/ios-device-features-settings).

1. Konfigurieren Sie als nächstes MDM so, dass die Microsoft Authenticator-App per Pushvorgang über ein MDM-Profil an das Gerät übertragen wird.

    Legen Sie die folgenden Konfigurationsoptionen fest, um den Modus für gemeinsam genutzte Geräte zu aktivieren:

    - Konfiguration 1:
      - Schlüssel: sharedDeviceMode
      - Typ: Boolean
      - Wert: true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Ändern der iOS-Anwendung zum Unterstützen des Modus für gemeinsam genutzte Geräte

Ihre Benutzer müssen sich darauf verlassen können, dass Sie sicherstellen, dass ihre Daten nicht anderen Benutzern offengelegt werden. Die folgenden Abschnitte enthalten hilfreiche Signale, die Ihrer Anwendung zeigen, dass eine Änderung eingetreten ist und behandelt werden sollte.

Sie sind bei jeder Verwendung der App verantwortlich für die Überprüfung des Benutzerstatus auf dem Gerät und für das Löschen der Daten des vorherigen Benutzers. Dies gilt auch, wenn die Anwendung beim Multitasking erneut aus dem Hintergrund geladen wird.

Bei einem Wechsel des Benutzers müssen Sie sicherstellen, dass die Daten des vorherigen Benutzers gelöscht sowie alle zwischengespeicherten Daten, die in der Anwendung angezeigt werden, entfernt werden. Wir empfehlen Ihnen und Ihrem Unternehmen dringend, nach dem Aktualisieren Ihrer App zur Unterstützung des Modus für gemeinsam genutzte Geräte eine Sicherheitsüberprüfung durchzuführen.

### <a name="detect-shared-device-mode"></a>Erkennen des Modus für gemeinsam genutzte Geräte

Das Erkennen des Modus für gemeinsam genutzte Geräte ist wichtig für Ihre Anwendung. Viele Anwendungen erfordern eine Änderung der Benutzerumgebung, wenn die Anwendung auf einem gemeinsam genutzten Gerät verwendet wird. Beispielsweise kann Ihre Anwendung über eine „Registrierungsfunktion“ verfügen, die für Mitarbeiter in Service und Produktion nicht geeignet ist, da diese wahrscheinlich bereits über ein Konto verfügen. Vielleicht möchten Sie für die Verarbeitung der Daten in Ihrer Anwendung auch zusätzliche Sicherheit bereitstellen, wenn der Modus für gemeinsam genutzte Geräte verwendet wird.

Ermitteln Sie mit der `getDeviceInformationWithParameters:completionBlock:`-API in `MSALPublicClientApplication`, ob eine App auf einem Gerät mit dem Modus für gemeinsam genutzte Geräte ausgeführt wird.

Die folgenden Codeausschnitte zeigen Beispiele für die Verwendung der `getDeviceInformationWithParameters:completionBlock:`-API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Abrufen des angemeldeten Benutzers und Ermitteln, ob sich der Benutzer des Geräts geändert hat

Ein weiterer wichtiger Bestandteil bei der Unterstützung des Modus für gemeinsam genutzte Geräte besteht darin, den Status des Benutzers auf dem Gerät zu bestimmen und Anwendungsdaten zu löschen, wenn sich der Benutzer geändert hat oder kein Benutzer das Gerät verwendet. Sie sind dafür verantwortlich sicherzustellen, dass Daten anderen Benutzern nicht offengelegt werden.

Mit der `getCurrentAccountWithParameters:completionBlock:`-API können Sie das derzeit angemeldete Konto auf dem Gerät abfragen.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Globales Anmelden eines Benutzers

Wenn ein Gerät als gemeinsam genutztes Gerät konfiguriert ist, kann Ihre Anwendung die `acquireTokenWithParameters:completionBlock:`-API zum Anmelden des Kontos anrufen. Das Konto ist global für alle berechtigten Apps auf dem Gerät verfügbar, nachdem die erste App das Konto angemeldet hat.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Globales Abmelden eines Benutzers

Mit dem folgenden Code wird das angemeldete Konto entfernt, und zwischengespeicherte Token werden nicht nur aus der App, sondern auch vom Gerät gelöscht, das sich im Modus für gemeinsam genutzte Geräte befindet. Es werden jedoch *keine Daten* aus der Anwendung gelöscht. Sie müssen die Daten aus der Anwendung sowie alle zwischengespeicherten Daten, die Ihre Anwendung möglicherweise dem Benutzer anzeigt, löschen.

#### <a name="clear-browser-state"></a>Löschen des Browserstatus

> [!NOTE]
> Der folgende Schritt ist nur bei der öffentlichen Vorschauversion (Public Review) erforderlich.

In dieser öffentlichen Vorschauversion löscht das [Microsoft Enterprise SSO-Plug-In für Apple-Geräte](apple-sso-plugin.md) den Status nur für Anwendungen. Es löscht nicht den Status im Safari-Browser. Es wird empfohlen, die Browsersitzung manuell zu löschen, um sicherzustellen, dass keine Verfolgung des Benutzerstatus mehr möglich ist. Sie können die nachstehend gezeigte, optionale Eigenschaft `signoutFromBrowser` verwenden, um alle Cookies zu löschen. Dies bewirkt, dass der Browser auf dem Gerät kurz gestartet wird.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Nächste Schritte

Um den Modus für gemeinsam genutzte Geräte in Aktion zu sehen, enthält das folgende Codebeispiel auf GitHub ein Beispiel für das Ausführen einer App für Mitarbeiter in Service und Produktion auf einem iOS-Gerät im Modus für gemeinsam genutzte Geräte:

[MSAL: iOS-Beispiel in Swift und Microsoft Graph-API](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
