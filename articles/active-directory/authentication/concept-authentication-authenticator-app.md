---
title: Authentifizierungsmethode mit der Microsoft Authenticator-App – Azure Active Directory
description: Erfahren Sie mehr über die Verwendung der Microsoft Authenticator-App in Azure Active Directory zum Verbessern und Sichern von Anmeldeereignissen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7309d9d32eba5cae350bbe8a1df325dfb2ab0c8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744362"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Authentifizierungsmethoden in Azure Active Directory – Microsoft Authenticator-App

Die Microsoft Authenticator-App bietet eine zusätzliche Sicherheitsstufe für Ihr Azure AD-Geschäfts-, Schul- oder Uni-Konto bzw. Ihr Microsoft-Konto und ist verfügbar für [Android](https://go.microsoft.com/fwlink/?linkid=866594) und [iOS](https://go.microsoft.com/fwlink/?linkid=866594). Mit der Microsoft Authenticator-App können sich Benutzer bei der Anmeldung ohne Kennwort authentifizieren. Alternativ können Sie die App bei der Self-Service-Kennwortzurücksetzung (SSPR) oder bei Azure AD Multi-Factor Authentication-Ereignissen als zusätzliche Prüfoption verwenden.

Benutzer erhalten dann eine Benachrichtigung über die mobile App und können den Vorgang genehmigen oder ablehnen. Sie können aber auch mit der Microsoft Authenticator-App einen OATH-Prüfcode generieren und auf einem Anmeldebildschirm eingeben. Wenn Sie sowohl die Benachrichtigung als auch den Prüfcode aktivieren, können Benutzer, die die Microsoft Authenticator-App registrieren, ihre Identität anhand von beiden Methoden bestätigen.

Informationen zur Verwendung der Microsoft Authenticator-App an einer Anmeldeaufforderung (anstelle der Kombination aus Benutzername und Kennwort) finden Sie unter [Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App (Vorschau)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Wenn Benutzer SSPR aktivieren, haben sie keine Möglichkeit, die mobile App zu registrieren. Stattdessen können sie die mobile App auf [https://aka.ms/mfasetup](https://aka.ms/mfasetup) oder im Rahmen der kombinierten Registrierung von Sicherheitsinformationen auf [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) registrieren.

## <a name="passwordless-sign-in"></a>Anmeldung ohne Kennwort

Einem Benutzer, der die Anmeldung per Telefon über die Microsoft Authenticator-App aktiviert hat, wird nach dem Eingeben eines Benutzernamens keine Aufforderung zur Eingabe eines Kennworts angezeigt, sondern eine Meldung, in der er aufgefordert wird, in der App auf eine Zahl zu tippen. Nach der Auswahl der richtigen Nummer ist der Anmeldevorgang abgeschlossen.

![Beispiel für eine browserbasierte Anmeldung, die der Benutzer bestätigen muss](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Diese Authentifizierungsmethode bietet ein hohes Maß an Sicherheit. Zudem muss der Benutzer bei der Anmeldung kein Kennwort mehr eingeben. Die Anmeldung ohne Kennwort mithilfe der Microsoft Authenticator-App ist derzeit als Vorschauversion verfügbar.

Informationen zu den ersten Schritten mit der Anmeldung ohne Kennwort finden Sie unter [Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Benachrichtigung über mobile App

Die Microsoft Authenticator-App kann dazu beitragen, nicht autorisierten Zugriff auf Konten zu verhindern und betrügerische Transaktionen zu stoppen, indem sie eine Benachrichtigung an Ihr Smartphone oder Tablet sendet. Benutzer sehen die Benachrichtigung und wählen **Bestätigen** aus, wenn der Vorgang rechtmäßig ist. Andernfalls können sie **Verweigern** auswählen.

![Screenshot der Webbrowser-Eingabeaufforderung als Beispiel für die Benachrichtigung der Microsoft Authenticator-App zum Abschließen des Anmeldevorgangs](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Wenn Mitarbeiter Ihrer Organisation nach China reisen oder dort arbeiten müssen, funktioniert die Methode *Benachrichtigung über mobile App* auf Android-Geräten in diesem Land nicht, da in dieser Region Google Play Services (einschließlich Pushbenachrichtigungen) blockiert sind. iOS-Benachrichtigungen funktionieren jedoch. Daher sollten Benutzern von Android-Geräten alternative Authentifizierungsmethoden zur Verfügung gestellt werden.

## <a name="verification-code-from-mobile-app"></a>Prüfcode über die mobile App

Die Microsoft Authenticator-App kann als Softwaretoken zum Generieren eines OATH-Prüfcodes verwendet werden. Nachdem Sie Benutzernamen und Kennwort eingegeben haben, geben Sie im Anmeldebildschirm den in der Authenticator-App generierten Code ein. Der Überprüfungscode kann als zweite Authentifizierungsmethode eingegeben werden.

Benutzer verfügen möglicherweise über eine Kombination aus bis zu fünf OATH-Hardwaretoken oder Authentifizierungsanwendungen wie die Microsoft Authenticator-App, die für die jederzeitige Verwendung konfiguriert sind.

> [!WARNING]
> Wenn bei der Self-Service-Kennwortzurücksetzung nur eine Methode zum Zurücksetzen erforderlich ist und Sie ein Höchstmaß an Sicherheit gewährleisten möchten, steht den Benutzern als einzige Option nur der Prüfcode zur Verfügung.
>
> Wenn zwei Methoden erforderlich sind, können Benutzer zum Zurücksetzen neben allen anderen aktivierten Methoden entweder eine Benachrichtigung oder einen Prüfcode verwenden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit der Anmeldung ohne Kennwort finden Sie unter [Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App](howto-authentication-passwordless-phone.md).

Im Artikel [API-Übersicht zu Azure AD-Authentifizierungsmethoden](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta) finden Sie weitere Informationen zur Betaversion der REST-API für Microsoft Graph.
