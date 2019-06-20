---
title: 'Basisrichtlinie: MFA für Administratoren verlangen – Azure Active Directory'
description: Richtlinie für bedingten Zugriff, die verlangt, dass Administratoren die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verwenden
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1ce48126c3e8867ac7f2696d8cf7db992a9a60a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003546"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>Basisrichtlinie: Anfordern von MFA für Administratoren

Benutzer mit Zugriff auf privilegierte Konten haben uneingeschränkten Zugriff auf Ihre Umgebung. Aufgrund der weitreichenden Befugnisse, die diese Konten haben, sollten Sie sie mit Bedacht verwalten. Eine gängige Methode, um den Schutz von privilegierten Konten zu verbessern, ist eine stärkere Form der Kontoüberprüfung bei der Anmeldung. In Azure Active Directory können Sie eine striktere Kontoüberprüfung erreichen, indem Sie eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) anfordern.

**MFA für Administratoren verlangen** ist eine [Basisrichtlinie](concept-baseline-protection.md), die verlangt, dass Administratoren mit den folgenden privilegierten Rollen bei jeder Anmeldung MFA verwenden:

* Globaler Administrator
* SharePoint-Administrator
* Exchange-Administrator
* Administrator für bedingten Zugriff
* Sicherheitsadministrator
* Helpdeskadministrator/Kennwortadministrator
* Rechnungsadministrator
* Benutzeradministrator

Beim Aktivieren der Richtlinie „MFA für Administratoren verlangen“ müssen sich die obigen neun Administratorrollen mit der Authenticator-App für MFA registrieren. Sobald die MFA-Registrierung abgeschlossen ist, müssen Administratoren bei jeder Anmeldung MFA ausführen.

![Basisrichtlinie: MFA für Administratoren verlangen](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

Weil die Richtlinie **MFA für Administratoren verlangen** für alle wichtigen Administratoren gilt, sind verschiedene Aspekte zu berücksichtigen, um eine reibungslose Bereitstellung sicherzustellen. Beispielsweise müssen Benutzer und Dienstprinzipale in Azure AD identifiziert werden, die MFA nicht ausführen dürfen oder sollen, sowie die von Ihrer Organisation verwendeten Anwendungen und Clients, die keine moderne Authentifizierung unterstützen.

### <a name="legacy-protocols"></a>Ältere Protokolle

Ältere Authentifizierungsprotokolle (IMAP, SMTP, POP3 usw.) werden von E-Mail-Clients für Authentifizierungsanforderungen verwendet. Diese Protokolle unterstützen keine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA). Die meisten der von Microsoft festgestellten Kontosicherheitsverletzungen werden durch Angriffe verursacht, die sich gegen ältere Protokolle richten und versuchen, die MFA zu umgehen. Um sicherzustellen, dass die MFA bei der Anmeldung mit einem Administratorkonto obligatorisch ist und von Angreifern nicht umgangen werden kann, blockiert diese Richtlinie alle Authentifizierungsanforderungen, die über ältere Protokolle für Administratorkonten ausgeführt werden.

> [!WARNING]
> Stellen Sie vor dem Aktivieren dieser Richtlinie sicher, dass Ihre Administratoren keine älteren Authentifizierungsprotokolle verwenden. Weitere Informationen finden Sie im Artikel [Blockieren der Legacyauthentifizierung bei Azure AD mit bedingtem Zugriff](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use).

### <a name="user-exclusions"></a>Ausschluss von Benutzern

Diese Basisrichtlinie bietet Ihnen die Möglichkeit, Benutzer auszuschließen. Es wird empfohlen, die folgenden Konten auszuschließen, bevor Sie die Richtlinie für Ihren Mandanten aktivieren:

* **Notfallzugriffs**- oder **Break-Glass**-Konten, um eine mandantenweite Kontosperrung zu vermeiden. In dem unwahrscheinlichen Fall, dass alle Administratoren aus dem Mandanten ausgeschlossen sind, können Sie sich mit Ihrem Administratorkonto für den Notfallzugriff beim Mandanten anmelden und Maßnahmen ergreifen, um den Zugriff wiederherzustellen.
   * Weitere Informationen finden Sie im Artikel [Verwalten von Konten für den Notfallzugriff in Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Dienstkonten** und **Dienstprinzipale**, z. B. das Konto für die Azure AD Connect-Synchronisierung. Dienstkonten sind nicht interaktive Konten, die an keinen bestimmten Benutzer gebunden sind. Sie werden normalerweise von Back-End-Diensten verwendet und ermöglichen den programmgesteuerten Zugriff auf Anwendungen. Dienstkonten sollten ausgeschlossen werden, weil die MFA nicht programmgesteuert abgeschlossen werden kann.
   * Wenn Ihre Organisation diese Konten in Skripts oder Code verwendet, sollten Sie in Betracht ziehen, diese durch  [verwaltete Identitäten](../managed-identities-azure-resources/overview.md) zu ersetzen. Als vorübergehende Problemumgehung können Sie diese spezifischen Konten aus der Basisrichtlinie ausschließen.
* Benutzer, die kein Smartphone besitzen oder verwenden können.
   * Diese Richtlinie erfordert, dass sich Administratoren mithilfe der Microsoft Authenticator-App für MFA registrieren.

## <a name="enable-the-baseline-policy"></a>Aktivieren der Basisrichtlinie

Die **Basisrichtlinie: MFA für Administratoren verlangen** ist vorkonfiguriert und wird im Azure-Portal auf dem Blatt „Bedingter Zugriff“ ganz oben angezeigt.

Gehen Sie wie folgt vor, um diese Richtlinie zu aktivieren und Ihre Administratoren zu schützen:

1. Melden Sie sich beim  **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Bedingter Zugriff**.
1. Wählen Sie in der Liste der Richtlinien den Eintrag **Basisrichtlinie: MFA für Administratoren verlangen** aus.
1. Legen Sie **Richtlinie aktivieren** auf **Richtlinie sofort verwenden** fest.
1. Fügen Sie ausgeschlossene Benutzer hinzu, indem Sie auf **Benutzer** > **Ausgeschlossene Benutzer auswählen** klicken und die Benutzer auswählen, die ausgeschlossen werden sollen. Klicken Sie auf **Auswählen** und dann auf **Fertig**.
1. Klicken Sie auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

* [Baselineschutzrichtlinien für bedingten Zugriff](concept-baseline-protection.md)
* [Fünf Schritte zum Sichern Ihrer Identitätsinfrastruktur](../../security/azure-ad-secure-steps.md)
* [Was ist der bedingte Zugriff in Azure Active Directory?](overview.md)
