---
title: Konfigurieren der MFA-Registrierungsrichtlinie – Azure Active Directory Identity Protection
description: Es wird beschrieben, wie Sie die Registrierungsrichtlinie für die mehrstufige Authentifizierung in Azure AD Identity Protection konfigurieren.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835865"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Anleitung: Konfigurieren der Registrierungsrichtlinie für Azure AD Multi-Factor Authentication

Azure AD Identity Protection unterstützt Sie beim Verwalten des Rollouts der Registrierung für Azure AD Multi-Factor Authentication (MFA): Es wird eine Richtlinie für bedingten Zugriff zur Anforderung der MFA-Registrierung konfiguriert – unabhängig davon, bei welcher App mit moderner Authentifizierung Sie sich anmelden möchten.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Was ist die Registrierungsrichtlinie für die mehrstufige Authentifizierung in Azure (Azure AD Multi-Factor Authentication)?

Mit Azure AD Multi-Factor Authentication können Sie über die Verwendung eines Benutzernamens und Kennworts hinaus Ihre Identität verifizieren. Sie stellt eine zweite Sicherheitsebene für Benutzeranmeldungen dar. Damit Benutzer auf MFA-Aufforderungen reagieren können, müssen sie sich zuerst bei Azure AD Multi-Factor Authentication registrieren.

Die obligatorische Verwendung von Azure AD Multi-Factor Authentication für Benutzeranmeldungen empfiehlt sich aus folgenden Gründen:

- Sie bietet eine sichere Authentifizierung mit einer Auswahl von Überprüfungsoptionen.
- Sie spielt eine wichtige Rolle bei der Vorbereitung Ihrer Organisation auf die Eigenwartung bei Risikoerkennungen in Identity Protection.

Weitere Informationen zu Azure AD Multi-Factor Authentication finden Sie unter [Was ist Azure AD Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md).

## <a name="policy-configuration"></a>Richtlinienkonfiguration

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Identity Protection** > **MFA-Registrierungsrichtlinie**.
   1. Unter **Zuweisungen**
      1. **Benutzer**: Wählen Sie **Alle Benutzer** oder **Einzelne Benutzer und Gruppen auswählen** aus, wenn Sie den Rollout einschränken.
         1. Optional können Sie Benutzer aus der Richtlinie ausschließen.
   1. Unter **Steuerelemente**
      1. Vergewissern Sie sich, dass das Kontrollkästchen **Azure AD-MFA-Registrierung als erforderlich festlegen** aktiviert ist, und wählen Sie **Auswählen** aus.
   1. **Richtlinie erzwingen** - **Ein**
   1. **Speichern**

## <a name="user-experience"></a>Benutzererfahrung

Azure Active Directory Identity Protection fordert Ihre Benutzer auf, sich bei der nächsten interaktiven Anmeldung zu registrieren, und gibt ihnen 14 Tage Zeit, um die Registrierung vorzunehmen. Während dieses Zeitraums von 14 Tagen kann die Registrierung umgangen werden, aber am Ende des Zeitraums müssen die Benutzer sich registrieren, um den Anmeldevorgang abschließen zu können.

Eine Übersicht über die zugehörige Benutzeroberfläche finden Sie unter:

- [Anmeldeverfahren von Azure AD Identity Protection](concept-identity-protection-user-experience.md)  

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren von Anmelde- und Benutzerrisikorichtlinien](howto-identity-protection-configure-risk-policies.md)

- [Aktivieren der Self-Service-Kennwortzurücksetzung in Azure AD](../authentication/howto-sspr-deployment.md)

- [Aktivieren von Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
