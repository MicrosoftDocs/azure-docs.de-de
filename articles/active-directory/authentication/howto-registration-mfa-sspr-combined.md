---
title: Erste Schritte mit der kombinierten Registrierung für Azure AD-SSPR und mehrstufige Authentifizierung (Vorschau) – Azure Active Directory
description: Aktivieren der kombinierten Registrierung für Azure AD Multi-Factor Authentication und die Self-Service-Kennwortzurücksetzung (Vorschauversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cafe81a1b5ab6d26e71eff05e355d2ed79c4d5e7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119381"
---
# <a name="enable-combined-security-information-registration-preview"></a>Aktivieren der kombinierten Registrierung von Sicherheitsinformationen (Vorschauversion)

Lesen Sie vor dem Aktivieren der neuen Funktion den Artikel [Kombinierte Registrierung von Sicherheitsinformationen (Vorschauversion)](concept-registration-mfa-sspr-combined.md), um sich mit der Funktionalität und den Auswirkungen dieser Funktion vertraut zu machen.

![Verbesserte Oberfläche für die kombinierte Registrierung von Sicherheitsinformationen](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Die kombinierte Registrierung von Sicherheitsinformationen für Azure Multi-Factor Authentication und die Azure AD-Self-Service-Kennwortzurücksetzung (Azure Active Directory) ist eine Funktion der öffentlichen Vorschau von Azure AD. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Aktivieren der kombinierten Registrierung

Führen Sie die folgenden Schritte aus, um die kombinierte Registrierung zu aktivieren:

1. Melden Sie sich als Benutzeradministrator oder globaler Administrator beim Azure-Portal an.
2. Navigieren Sie zu **Azure Active Directory** > **Benutzereinstellungen** > **Einstellungen für Zugriffspanel-Vorschaufeatures verwalten**.
3. Wählen Sie unter **Benutzer können Vorschaufeatures zum Registrieren und Verwalten von Sicherheitsinformationen verwenden – Aktualisieren** aus, ob Sie die Funktion für eine **ausgewählte** Gruppe von Benutzern oder für **alle** Benutzer aktivieren möchten.

   ![Aktivieren der kombinierten Registrierung von Sicherheitsinformationen für alle Benutzer](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Ab März 2019 sind die Telefonanrufoptionen für MFA- (Multi-Factor Authentication) und SSPR-Benutzer in kostenlosen bzw. Testversionen von Azure AD-Mandanten nicht mehr verfügbar. SMS-Nachrichten sind von dieser Änderung nicht betroffen. Für Benutzer in kostenpflichtigen Azure AD-Mandanten ist die Telefonanrufoption weiterhin verfügbar.

> [!NOTE]
> Nachdem Sie die kombinierte Registrierung aktiviert haben, können Benutzer, die ihre Telefonnummer oder mobile App durch die neue Funktion registrieren oder bestätigen, diese für die mehrstufige Authentifizierung und SSPR verwenden, wenn diese Methoden in den MFA- und SSPR-Richtlinien aktiviert sind. Wenn Sie diese Funktion dann deaktivieren, müssen Benutzer, die zur vorherigen SSPR-Registrierungsseite unter `https://aka.ms/ssprsetup` navigieren, die mehrstufige Authentifizierung durchführen, bevor sie auf die Seite zugreifen können.

Wenn Sie die „Liste der Site zu Zonenzuweisungen“ in Internet Explorer konfiguriert haben, müssen sich die folgenden Websites in der gleichen Zone befinden:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Richtlinien für bedingten Zugriff für die kombinierte Registrierung

Mit Benutzeraktionen in der Richtlinie für bedingten Zugriff kann jetzt sichergestellt werden, wann und wie sich Benutzer für Azure Multi-Factor Authentication registrieren, und auch die Self-Service-Kennwortzurücksetzung ist möglich. Diese Previewfunktion ist für Organisationen verfügbar, die die [kombinierte Registrierung (Vorschauversion)](../authentication/concept-registration-mfa-sspr-combined.md) aktiviert haben. Diese Funktionalität kann in Organisationen aktiviert werden, in denen sich Benutzer an einem zentralen Ort, z. B. einem vertrauenswürdigen Netzwerkspeicherort während des Onboarding-Prozesses für Personal, für Azure Multi-Factor Authentication und SSPR registrieren sollen. Weitere Informationen zur Erstellung von vertrauenswürdigen Speicherorten bei bedingtem Zugriff finden Sie im Artikel [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](../conditional-access/location-condition.md#named-locations).

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Erstellen einer Richtlinie zum Erzwingen der Registrierung von einem vertrauenswürdigen Standort

Die folgende Richtlinie gilt für alle ausgewählten Benutzer, die versuchen, sich über die Benutzeroberfläche für die kombinierte Registrierung zu registrieren. Der Zugriff wird blockiert, sofern die Verbindung nicht von einem Standort aus hergestellt wird, der als ein vertrauenswürdiges Netzwerk gekennzeichnet ist.

![Erstellen einer Richtlinie für bedingten Zugriff zum Steuern der Registrierung der Sicherheitsinformationen](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. Navigieren Sie im **Azure-Portal** zu **Azure Active Directory** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Geben Sie unter „Name“ einen Namen für diese Richtlinie ein. Beispiel: **Kombinierte Registrierung mit Sicherheitsinformationen in vertrauenswürdigen Netzwerken**
1. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen**, und wählen Sie die Benutzer und Gruppen aus, auf die Sie diese Richtlinie anwenden möchten.

   > [!WARNING]
   > Benutzer müssen für die [kombinierte Registrierung (Vorschauversion)](../authentication/howto-registration-mfa-sspr-combined.md) aktiviert sein.

1. Wählen Sie unter **Cloud-Apps oder -aktionen** die Option **Benutzeraktionen**, und aktivieren Sie **Sicherheitsinformationen registrieren (Vorschau)** .
1. Unter **Bedingungen** > **Standorte**:
   1. Konfigurieren Sie **Ja**.
   1. Schließen Sie **Alle Standorte** ein.
   1. Schließen Sie **Alle vertrauenswürdigen Standorte** aus.
   1. Klicken Sie auf dem Blatt „Standorte“ auf **Fertig**.
   1. Klicken Sie auf dem Blatt „Bedingungen“ auf **Fertig**.
1. Unter **Zugriffssteuerungen** > **Erteilen**:
   1. Klicken Sie auf **Zugriff blockieren**.
   1. Klicken Sie anschließend auf **Auswählen**.
1. Legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Klicken Sie anschließend auf **Erstellen**.

## <a name="next-steps"></a>Nächste Schritte

[Verfügbare Methoden für Multi-Factor Authentication und SSPR](concept-authentication-methods.md)

[Konfigurieren der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)

[Konfigurieren von Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Problembehandlung für die kombinierte Registrierung von Sicherheitsinformationen](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](../conditional-access/location-condition.md)
