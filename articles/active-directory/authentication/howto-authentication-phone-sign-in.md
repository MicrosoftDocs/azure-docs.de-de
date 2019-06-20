---
title: Kennwortlose Anmeldung mit der Microsoft Authenticator-App (Vorschauversion) – Azure Active Directory
description: Anmelden bei Azure AD mithilfe der Microsoft Authenticator-App ohne Verwendung Ihres Kennworts (Public Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb10378d890c2b7156b6764321e177a22ffc538a
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472758"
---
# <a name="password-less-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Telefonanmeldung ohne Kennwort mit der Microsoft Authenticator-App (Public Preview)

Mit der Microsoft Authenticator-App können sich Benutzer bei jedem beliebigen Azure AD-Konto anmelden, ohne ein Kennwort zu verwenden. Ähnlich wie die Technologie von [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) nutzt Microsoft Authenticator die schlüsselbasierte Authentifizierung, um die Verwendung von Benutzeranmeldeinformationen zu ermöglichen, die an ein Gerät gebunden sind und auf biometrischen Daten oder einer PIN beruhen.

![Beispiel für eine browserbasierte Anmeldung, die der Benutzer bestätigen muss](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Für einen Benutzer, der die Anmeldung per Smartphone in der Microsoft Authenticator-App aktiviert hat, wird nach der Eingabe eines Benutzernamens keine Kennwortaufforderung angezeigt, sondern eine Meldung, in der er aufgefordert wird, in der App auf eine Nummer zu tippen. In der App muss der Benutzer auf die richtige Nummer tippen, „Genehmigen“ auswählen und dann seine PIN eingeben oder seinen biometrischen Schlüssel verwenden, um die Authentifizierung abzuschließen.

## <a name="enable-my-users"></a>Aktivieren der Benutzer

Bei der Public Preview-Version muss ein Administrator zunächst mithilfe von PowerShell eine Richtlinie hinzufügen, um die Verwendung der Anmeldeinformationen im Mandanten zu ermöglichen. Lesen Sie den Abschnitt „Bekannte Probleme“, bevor Sie diesen Schritt ausführen.

### <a name="tenant-prerequisites"></a>Voraussetzungen für den Mandanten

* Azure Active Directory
* Microsoft Azure Multi-Factor Authentication ist für Endbenutzer aktiviert.
* Benutzer können ihre Geräte registrieren.

### <a name="steps-to-enable"></a>Schritte zum Aktivieren

1. Stellen Sie sicher, dass bei Ihnen die neueste Public Preview-Version des Azure Active Directory V2 PowerShell-Moduls installiert ist. Dazu empfiehlt es sich unter Umständen, das Modul zu deinstallieren und anschließend erneut zu installieren. Führen Sie hierzu die folgenden Befehle aus:

    ```powershell
    Uninstall-Module -Name AzureADPreview
    Install-Module -Name AzureADPreview
    ```

2. Führen Sie die Authentifizierung für den Azure AD-Mandanten durch, um das Azure AD V2 PowerShell-Modul zu verwenden. Beim verwendeten Konto muss es sich um einen Sicherheitsadministrator oder globalen Administrator handeln.

    ```powershell
    Connect-AzureAD
    ```

3. Erstellen Sie die Richtlinie für die Authenticator-Anmeldung:

    ```powershell
    New-AzureADPolicy -Type AuthenticatorAppSignInPolicy -Definition '{"AuthenticatorAppSignInPolicy":{"Enabled":true}}' -isOrganizationDefault $true -DisplayName AuthenticatorAppSignIn
    ```

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Wie aktivieren Endbenutzer die Anmeldung per Smartphone?

In der Public Preview-Version ist es nicht möglich, die Erstellung oder Verwendung dieser neuen Anmeldeinformationen durch Benutzer zu erzwingen. Für einen Endbenutzer gilt die Anmeldung ohne Kennwort erst, wenn ein Administrator die Richtlinie für den jeweiligen Mandanten aktiviert und der Benutzer die Microsoft Authenticator-App aktualisiert hat, um die Anmeldung per Smartphone zu aktivieren.

> [!NOTE]
> Diese Funktion ist seit März 2017 in der App verfügbar. Wenn die Richtlinie für einen Mandanten aktiviert ist, ist es daher möglich, dass dieser Anmeldungsablauf sofort für Benutzer verwendet wird. Bedenken Sie dies, und breiten Sie Ihre Benutzer auf die Umstellung vor.
>

1. Für Microsoft Azure Multi-Factor Authentication (MFA) registrieren.
1. Aktuelle Version von Microsoft Authenticator auf Geräten mit iOS 8.0 oder höher oder Android 6.0 oder höher installieren.
1. Geschäfts-, Schul- oder Unikonto mit Pushbenachrichtigungen zur App hinzufügen. Die Dokumentation für Endbenutzer finden Sie unter [https://aka.ms/authappstart](https://aka.ms/authappstart).

Sobald die Benutzer das MFA-Konto mit Pushbenachrichtigungen in der Microsoft Authenticator-App eingerichtet haben, können sie den Schritten im Artikel [Sign in with your phone, not your password](../user-help/microsoft-authenticator-app-phone-signin-faq.md) (Anmelden per Smartphone anstelle Ihres Kennworts) folgen, um die Registrierung für die Anmeldung per Smartphone abzuschließen.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="ad-fs-integration"></a>AD FS-Integration

Wenn ein Benutzer die Microsoft Authenticator-Anmeldeinformationen ohne Kennwort aktiviert hat, wird die Authentifizierung für diesen Benutzer standardmäßig immer auf das Senden einer Genehmigungsbenachrichtigung festgelegt. Diese Logik verhindert, dass Benutzer in einem Hybridmandanten zur Überprüfung der Anmeldung zu Active Directory-Verbunddienste (AD FS) weitergeleitet werden, ohne zusätzlich auf „Stattdessen Ihr Kennwort verwenden“ zu klicken. Bei diesem Prozess werden auch alle lokalen Richtlinien für bedingten Zugriff und Passthrough-Authentifizierungsflüsse umgangen. Dabei gilt jedoch folgende Ausnahme: Wenn ein Anmeldehinweis (login_hint) angegeben ist, wird ein Benutzer automatisch zu AD FS weitergeleitet, und die Option zur Verwendung von Anmeldeinformationen ohne Kennwort wird umgangen.

### <a name="azure-mfa-server"></a>Azure MFA-Server

Endbenutzer, für die MFA über den lokalen Azure MFA-Server einer Organisation aktiviert ist, können weiterhin einen einzelnen Satz von Anmeldeinformationen für die Anmeldung per Smartphone ohne Kennwort erstellen und verwenden. Wenn der Benutzer versucht, mehrere Installationen (fünf oder mehr) von Microsoft Authenticator mit den Anmeldeinformationen zu aktualisieren, kann diese Änderung zu einem Fehler führen.  

### <a name="device-registration"></a>Geräteregistrierung

Eine der Voraussetzungen für die Erstellung dieser neuen, sicheren Anmeldeinformationen ist die Registrierung des zugehörigen Geräts im Azure AD-Mandanten für einen einzelnen Benutzer. Aufgrund der Einschränkungen, die für die Registrierung von Geräten gelten, kann ein Gerät nur in einem einzigen Mandanten registriert werden. Dies bedeutet, dass nur ein Geschäfts-, Schul- oder Unikonto in der Microsoft Authenticator-App für die Anmeldung per Smartphone aktiviert werden kann.

## <a name="next-steps"></a>Nächste Schritte

[Informationen zur Geräteregistrierung](../devices/overview.md#getting-devices-in-azure-ad)

[Informationen zu Microsoft Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
