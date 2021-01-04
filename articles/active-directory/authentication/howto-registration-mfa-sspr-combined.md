---
title: Aktivieren der kombinierten Registrierung von Sicherheitsinformationen – Azure Active Directory
description: Erfahren Sie, wie Sie die Endbenutzererfahrung durch die kombinierte Registrierung mit Azure AD Multi-Factor Authentication und Self-Service-Kennwortzurücksetzung vereinfachen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29a577b50a561cb5b829e453c523e0bd18a70e1a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741676"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Aktivieren der kombinierten Registrierung von Sicherheitsinformationen in Azure Active Directory

Vor der kombinierten Registrierung registrierten Benutzer Authentifizierungsmethoden für Azure AD Multi-Factor Authentication (MFA) und die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) getrennt voneinander. Benutzer waren verwirrt, dass ähnliche Methoden für Azure AD Multi-Factor Authentication und für SSPR verwendet wurden, sie sich jedoch für beide Funktionen registrieren mussten. Mit der kombinierten Registrierung können sich Benutzer jetzt einmalig registrieren und die Vorteile von Azure AD Multi-Factor Authentication und SSPR nutzen.

> [!NOTE]
> Ab dem 15. August 2020 werden alle neuen Azure AD-Mandanten automatisch für die kombinierte Registrierung aktiviert. Ab dem 14. Dezember 2020 werden alle neuen Azure AD-Mandanten mit einem Benutzer automatisch für die kombinierte Registrierung aktiviert.

Lesen Sie vor dem Aktivieren der neuen Funktion den Artikel [Kombinierte Registrierung von Sicherheitsinformationen](concept-registration-mfa-sspr-combined.md), um sich mit der Funktionalität und den Auswirkungen dieser Funktion vertraut zu machen.

![Verbesserte Oberfläche für die kombinierte Registrierung von Sicherheitsinformationen](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Aktivieren der kombinierten Registrierung

Führen Sie die folgenden Schritte aus, um die kombinierte Registrierung zu aktivieren:

1. Melden Sie sich als Benutzeradministrator oder globaler Administrator beim Azure-Portal an.
2. Navigieren Sie zu **Azure Active Directory** > **Benutzereinstellungen** > **Vorschaueinstellungen für Benutzerfeatures verwalten**.
3. Wählen Sie unter **Benutzer können die kombinierte Oberfläche zur Registrierung von Sicherheitsinformationen verwenden** aus, ob Sie die Funktion für eine **Ausgewählte** Gruppe an Benutzern oder für **Alle** Benutzer aktivieren möchten.

   ![Aktivieren der kombinierten Oberfläche zur Registrierung von Sicherheitsinformationen für Benutzer](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Nachdem Sie die kombinierte Registrierung aktiviert haben, können Benutzer, die ihre Telefonnummer oder mobile App durch die neue Funktion registrieren oder bestätigen, diese für Azure AD Multi-Factor Authentication und SSPR verwenden, wenn diese Methoden in den Azure AD MFA- und SSPR-Richtlinien aktiviert sind.
>
> Wenn Sie diese Funktion dann deaktivieren, müssen Benutzer, die zur vorherigen SSPR-Registrierungsseite unter `https://aka.ms/ssprsetup` navigieren, die mehrstufige Authentifizierung durchführen, bevor sie auf die Seite zugreifen können.

Wenn Sie die *Liste der Site-zu-Zonen-Zuweisungen* in Internet Explorer konfiguriert haben, müssen sich die folgenden Websites in der gleichen Zone befinden:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Richtlinien für bedingten Zugriff für die kombinierte Registrierung

Mit Benutzeraktionen in der Richtlinie für bedingten Zugriff kann sichergestellt werden, wann und wie sich Benutzer für Azure AD Multi-Factor Authentication registrieren, und auch die Self-Service-Kennwortzurücksetzung ist möglich. Diese Funktionalität kann in Organisationen aktiviert werden, in denen sich Benutzer an einem zentralen Ort, z. B. einem vertrauenswürdigen Netzwerkspeicherort während des Onboardings von Personal, für Azure AD Multi-Factor Authentication und SSPR registrieren sollen.

> [!NOTE]
> Diese Richtlinie gilt nur, wenn ein Benutzer auf eine kombinierte Registrierungsseite zugreift. Diese Richtlinie erzwingt keine MFA-Registrierung, wenn ein Benutzer auf andere Anwendungen zugreift.
>
> Sie können eine MFA-Registrierungsrichtlinie mithilfe von [Azure Identity Protection – MFA-Richtlinie konfigurieren](../identity-protection/howto-identity-protection-configure-mfa-policy.md) erstellen.

Weitere Informationen zum Erstellen vertrauenswürdiger Speicherorte bei bedingtem Zugriff finden Sie unter [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](../conditional-access/location-condition.md#named-locations).

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Erstellen einer Richtlinie zum Erzwingen der Registrierung von einem vertrauenswürdigen Standort

Führen Sie die folgenden Schritte aus, um eine Richtlinie für alle ausgewählten Benutzer zu erstellen, die versuchen, sich über die Benutzeroberfläche mit der kombinierten Registrierung zu registrieren. Der Zugriff wird blockiert, sofern die Verbindung nicht von einem Standort aus hergestellt wird, der als ein vertrauenswürdiges Netzwerk gekennzeichnet ist:

1. Navigieren Sie im **Azure-Portal** zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **+ Neue Richtlinie** aus.
1. Geben Sie einen Namen für diese Richtlinie ein, beispielsweise *Kombinierte Registrierung von Sicherheitsinformationen in vertrauenswürdigen Netzwerken*.
1. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen**. Wählen Sie die Benutzer und Gruppen aus, auf die diese Richtlinie angewendet werden soll, und klicken Sie anschließend auf **Fertig**.

   > [!WARNING]
   > Benutzer müssen für die kombinierte Registrierung aktiviert sein.

1. Wählen Sie unter **Cloud-Apps oder -Aktionen** die Option **Alle Cloud-Apps** aus. Aktivieren Sie **Sicherheitsinformationen registrieren**, und klicken Sie dann auf **Fertig**.

    ![Erstellen einer Richtlinie für bedingten Zugriff zur Registrierung von Sicherheitsinformationen](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Konfigurieren Sie unter **Bedingungen** > **Standorte** die folgenden Optionen:
   1. Konfigurieren Sie **Ja**.
   1. Schließen Sie **Alle Standorte** ein.
   1. Schließen Sie **Alle vertrauenswürdigen Standorte** aus.
1. Klicken Sie auf im Fenster *Standorte* auf **Fertig** und dann im Fenster **Bedingungen** auf *Fertig*.
1. Wählen Sie unter **Zugriffssteuerung** > **Erteilen** die Option **Zugriff blockieren** und dann **Auswählen** aus.
1. Legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Klicken Sie auf **Erstellen**, um die Richtlinie zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Hilfe benötigen, finden Sie unter [Problembehandlung für die kombinierte Registrierung von Sicherheitsinformationen](howto-registration-mfa-sspr-combined-troubleshoot.md) sowie unter [Was sind Standortbedingungen beim bedingten Zugriff in Azure AD?](../conditional-access/location-condition.md) weitere Informationen.

Nachdem Benutzer für die kombinierte Registrierung aktiviert wurden, können Sie die [Self-Service-Kennwortzurücksetzung](tutorial-enable-sspr.md) und [Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md) aktivieren.

Informieren Sie sich bei Bedarf, wie Sie [die erneute Registrierung von Authentifizierungsmethoden durch Benutzer erzwingen](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
