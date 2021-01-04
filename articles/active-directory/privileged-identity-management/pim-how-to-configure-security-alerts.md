---
title: Sicherheitswarnungen für Azure AD-Rollen in PIM – Azure AD | Microsoft-Dokumentation
description: Konfigurieren Sie Sicherheitswarnungen für Azure AD-Rollen in Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a632c0e31de1c2d7e5417656d537e5f9f82ecfbe
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180488"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Konfigurieren von Sicherheitswarnungen für Azure AD-Rollen in Privileged Identity Management

Privileged Identity Management (PIM) generiert bei verdächtigen oder nicht sicheren Aktivitäten in Ihrer Azure Active Directory-Organisation (Azure AD) Warnungen. Eine ausgelöste Warnung wird auf dem Privileged Identity Management-Dashboard angezeigt. Wählen Sie die Warnung aus, um einen Bericht mit allen Benutzern oder Rollen anzuzeigen, die die Warnung ausgelöst haben.

## <a name="determine-your-version-of-pim"></a>Ermitteln der PIM-Version

Ab November 2019 werden die Azure AD Rollen von Privileged Identity Management auf eine neue Version aktualisiert, die dem Verhalten von Azure-Ressourcenrollen entspricht. Dadurch werden zusätzliche Features verfügbar, und es kommt zu [Änderungen an der vorhandenen API](azure-ad-roles-features.md#api-changes). Welche Verfahren Sie in diesem Artikel während der Einführung der neuen Version befolgen, hängt von Ihrer aktuellen Version von Privileged Identity Management ab. Führen Sie die Schritte in diesem Abschnitt aus, um Ihre Version von Privileged Identity Management zu ermitteln. Nachdem Sie Ihre Version von Privileged Identity Management kennen, können Sie die Verfahren in diesem Artikel auswählen, die dieser Version entsprechen.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) als Benutzer mit der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) an.
1. Öffnen Sie **Azure AD Privileged Identity Management**. Wenn sich oben auf der Übersichtsseite ein Banner befindet, befolgen Sie die Anweisungen auf der Registerkarte **Neue Version** dieses Artikels. Andernfalls befolgen Sie die Anweisungen auf der Registerkarte **Vorherige Version**.

  [![Wählen Sie „Azure AD“ > „Privileged Identity Management“ aus.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Führen Sie die in diesem Artikel beschriebenen Schritte aus, um Sicherheitswarnungen für Azure AD-Rollen zu untersuchen.

# <a name="new-version"></a>[Neue Version](#tab/new)

![Screenshot der Seite „Warnungen“ mit einer Liste von Warnungen mit jeweiligem Schweregrad](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Sicherheitswarnungen

Dieser Abschnitt enthält alle Sicherheitswarnungen für Azure AD-Rollen sowie Informationen zur Behebung und Vermeidung. Der Schweregrad hat folgende Bedeutung:

- **Hoch**: Aufgrund einer Richtlinienverletzung ist eine sofortige Aktion erforderlich.
- **Mittel:** Es ist keine sofortige Aktion erforderlich, aber es wird eine potenzielle Richtlinienverletzung angezeigt.
- **Niedrig**: Es ist keine sofortige Aktion erforderlich, aber es wird eine Richtlinienänderung vorgeschlagen.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratoren verwenden ihre privilegierten Rollen nicht.

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Benutzer, denen nicht benötigte privilegierte Rollen zugewiesen wurden, erhöhen die Möglichkeit eines Angriffs. Es ist auch einfacher für Angreifer, auf Konten, die nicht aktiv genutzt werden, unbemerkt zu bleiben. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie die Zuweisung zu privilegierten Rollen, die sie nicht benötigen. |
| **Prävention** | Weisen Sie privilegierte Rollen nur Benutzern zu, die eine geschäftliche Begründung angegeben haben. </br>Planen Sie regelmäßige [Zugriffsüberprüfungen](pim-how-to-start-security-review.md), um sicherzustellen, dass die Benutzer ihren Zugriff weiterhin benötigen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **Trigger** | Wird ausgelöst, wenn ein Benutzer eine angegebene Anzahl von Tagen keine Rolle aktiviert hat. |
| **Anzahl von Tagen** | Diese Einstellung gibt die maximale Anzahl von Tagen (von 0 bis 100) an, in denen ein Benutzer keine Rolle aktivieren muss.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Rollen erfordern für die Aktivierung keine Multi-Factor Authentication.

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Ohne mehrstufige Authentifizierung können kompromittierte Benutzer privilegierte Rollen aktivieren. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Liste der Rollen, und [erzwingen Sie die mehrstufige Authentifizierung](pim-how-to-change-default-settings.md) für jede Rolle. |
| **Prävention** | [Fordern Sie MFA](pim-how-to-change-default-settings.md) für jede Rolle an.  |
| **Aktionen zur Risikominderung im Portal** | Dadurch wird die mehrstufige Authentifizierung für die Aktivierung der privilegierten Rolle erforderlich. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Die Organisation verfügt nicht über Azure AD Premium P2

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Die aktuelle Azure AD-Organisation verfügt nicht über Azure AD Premium P2. |
| **Wie behebe ich das Problem?** | Lesen Sie die Informationen zu [Azure AD-Editionen](../fundamentals/active-directory-whatis.md). Führen Sie ein Upgrade auf Azure AD Premium P2 durch. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenzielle veraltete Konten in einer privilegierten Rolle

| | |
| --- | --- |
| **Severity** | Medium |
| **Warum erhalte ich diese Warnung?** | Für Konten in einer privilegierten Rolle wurde das Kennwort in den letzten 90 Tagen nicht geändert. Diese Konten können Dienst- oder gemeinsam genutzte Konten sein, die nicht verwaltet werden und anfällig für Angriffe sind. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Konten in der Liste. Wenn kein Zugriff darauf mehr erforderlich ist, entfernen sie aus ihren privilegierten Rollen. |
| **Prävention** | Stellen Sie sicher, dass die starken Kennwörter für freigegebenen Konten rotiert werden, wenn sich die Benutzer ändern, die das Kennwort kennen. </br>Überprüfen Sie Konten mit privilegierten Rollen regelmäßig mithilfe von [Zugriffsüberprüfungen](pim-how-to-start-security-review.md), und entfernen Sie nicht mehr benötigte Rollenzuweisungen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **bewährten Methoden** | Für freigegebene Konten sowie Dienst- und Notfallzugriffskonten, die ein Kennwort zur Authentifizierung verwenden und administrativen Rollen mit hohen Berechtigungen (z. B. der Rolle „Globaler Administrator“ oder „Sicherheitsadministrator“) zugewiesen sind, ist für die folgenden Fälle eine Kennwortrotation erforderlich:<ul><li>Nach einem Sicherheitsvorfall, bei dem administrative Zugriffsrechte missbraucht oder kompromittiert wurden</li><li>Nachdem die Berechtigungen eines Benutzers geändert wurden, sodass er kein Administrator mehr ist (beispielsweise nachdem ein Mitarbeiter, der ein Administrator war, die IT-Abteilung oder Organisation verlassen hat)</li><li>In regelmäßigen Abständen (z. B. vierteljährlich oder jährlich), auch wenn keine Sicherheitsverletzung bekannt ist oder kein Wechsel im IT-Personal stattgefunden hat</li></ul>Da mehrere Personen Zugriff auf die Anmeldeinformationen dieser Konten haben, sollte durch eine Rotation der Anmeldeinformationen sichergestellt werden, dass Personen, die ihre Rollen nicht mehr innehaben, keinen Zugriff auf die Konten mehr haben. [Erfahren Sie mehr über das Sichern von Konten.](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Rollen werden außerhalb von Privileged Identity Management zugewiesen

| | |
| --- | --- |
| **Severity** | High |
| **Warum erhalte ich diese Warnung?** | Außerhalb von Privileged Identity Management vorgenommene Zuweisungen privilegierter Rollen werden nicht ordnungsgemäß überwacht und können auf einen aktiven Angriff hinweisen. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie die Benutzer aus privilegierten Rollen, die außerhalb von Privileged Identity Management zugewiesen wurden. |
| **Prävention** | Untersuchen Sie, wo Benutzern privilegierte Rollen außerhalb von Privileged Identity Management zugewiesen werden, und verbieten Sie zukünftige Zuweisungen, die von dort stammen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt den Benutzer aus der privilegierten Rolle. |

### <a name="there-are-too-many-global-administrators"></a>Es gibt zu viele globale Administratoren

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | „Globaler Administrator“ ist die höchste privilegierte Rolle. Wenn ein globaler Administrator gefährdet ist, erhält der Angreifer Zugriff auf alle Berechtigungen dieses Administrators, sodass Ihr gesamtes System gefährdet ist. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie alle Benutzer, die nicht unbedingt die Rolle „Globaler Administrator“ benötigen. </br>Weisen Sie diesen Benutzern stattdessen weniger privilegierte Rollen zu. |
| **Prävention** | Weisen Sie Benutzern die am wenigsten privilegierte Rolle zu, die sie benötigen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **Trigger** | Wird ausgelöst, wenn zwei verschiedene Kriterien erfüllt sind, und Sie können beide konfigurieren. Erstens müssen Sie einen bestimmten Schwellenwert für Rollenzuweisungen für globale Administratoren erreichen. Zweitens muss ein bestimmter Prozentsatz aller Rollenzuweisungen globale Administratoren betreffen. Wenn nur eines dieser Kriterien erfüllt ist, wird die Warnung nicht angezeigt. |
| **Mindestanzahl globaler Administratoren** | Diese Einstellung gibt die Anzahl von Rollenzuweisungen für globale Administratoren (von 2 bis 100) an, die Sie für Ihre Azure AD-Organisation als zu gering betrachten. |
| **Prozentsatz von globalen Administratoren** | Diese Einstellung gibt den Mindestprozentsatz von globalen Administratoren (von 0 % bis 100 %) an, unter den Ihre Azure AD-Organisation nicht fallen soll. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollen werden zu häufig aktiviert

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Mehrere Aktivierungen für dieselbe privilegierte Rolle durch denselben Benutzer sind ein Zeichen für einen Angriff. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und stellen Sie sicher, dass die [Aktivierungsdauer](pim-how-to-change-default-settings.md) für die privilegierte Rolle lang genug gewählt ist, damit diese Aufgaben durchgeführt werden können. |
| **Prävention** | Stellen Sie sicher, dass die [Aktivierungsdauer](pim-how-to-change-default-settings.md) für privilegierte Rollen lang genug gewählt ist, damit Benutzer diese Aufgaben durchführen können.</br>[Erzwingen Sie die mehrstufige Authentifizierung](pim-how-to-change-default-settings.md) für privilegierte Rollen, deren Konten von mehreren Administratoren gemeinsam verwendet werden. |
| **Aktionen zur Risikominderung im Portal** | – |
| **Trigger** | Wird ausgelöst, wenn ein Benutzer mehrmals innerhalb eines bestimmten Zeitraums dieselbe privilegierte Rolle aktiviert. Sie können den Zeitraum und die Anzahl der Aktivierungen konfigurieren. |
| **Zeitrahmen für Aktivierungsverlängerung** | Diese Einstellung gibt den Zeitraum in Tagen, Stunden, Minuten und Sekunden an, den Sie verwenden möchten, um verdächtige Verlängerungen nachzuverfolgen. |
| **Anzahl von Aktivierungsverlängerungen** | Diese Einstellung gibt die Anzahl von Aktivierungen (von 2 bis 100) im gewählten Zeitraum an, über die Sie benachrichtigt werden möchten. Sie können diese Einstellung mithilfe des Schiebereglers oder durch Eingabe einer Zahl in das Textfeld ändern. |

## <a name="customize-security-alert-settings"></a>Anpassen der Einstellungen für Sicherheitswarnungen

Klicken Sie auf der Seite **Warnungen** auf **Einstellungen**.

![Seite „Warnungen“ mit hervorgehobenen „Einstellungen“](media/pim-how-to-configure-security-alerts/alert-settings.png)

Passen Sie die Einstellungen für die verschiedenen Warnungen so an, dass sie für Ihre Umgebung und Sicherheitsziele funktionieren.

![Einstellungenseite für eine Warnung zum Aktivieren und Konfigurieren von Einstellungen](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

# <a name="previous-version"></a>[Vorherige Version](#tab/previous)

![Azure AD-Rollen: Warnungsbereich mit Auflistung von Warnungen und Schweregrad](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alert-details"></a>Details der Sicherheitswarnung

Dieser Abschnitt enthält alle Sicherheitswarnungen für Azure AD-Rollen sowie Informationen zur Behebung und Vermeidung. Der Schweregrad hat folgende Bedeutung:

- **Hoch**: Aufgrund einer Richtlinienverletzung ist eine sofortige Aktion erforderlich.
- **Mittel:** Es ist keine sofortige Aktion erforderlich, aber es wird eine potenzielle Richtlinienverletzung angezeigt.
- **Niedrig**: Es ist keine sofortige Aktion erforderlich, aber es wird eine Richtlinienänderung vorgeschlagen.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratoren verwenden ihre privilegierten Rollen nicht.

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Benutzer, denen nicht benötigte privilegierte Rollen zugewiesen wurden, erhöhen die Möglichkeit eines Angriffs. Es ist auch einfacher für Angreifer, auf Konten, die nicht aktiv genutzt werden, unbemerkt zu bleiben. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie die Zuweisung zu privilegierten Rollen, die sie nicht benötigen. |
| **Prävention** | Weisen Sie privilegierte Rollen nur Benutzern zu, die eine geschäftliche Begründung angegeben haben. </br>Planen Sie regelmäßige [Zugriffsüberprüfungen](pim-how-to-start-security-review.md), um sicherzustellen, dass die Benutzer ihren Zugriff weiterhin benötigen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **Trigger** | Wird ausgelöst, wenn ein Benutzer eine angegebene Anzahl von Tagen keine Rolle aktiviert hat. |
| **Anzahl von Tagen** | Diese Einstellung gibt die maximale Anzahl von Tagen (von 0 bis 100) an, in denen ein Benutzer keine Rolle aktivieren muss.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Rollen erfordern für die Aktivierung keine Multi-Factor Authentication.

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Ohne mehrstufige Authentifizierung können kompromittierte Benutzer privilegierte Rollen aktivieren. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Liste der Rollen, und [erzwingen Sie die mehrstufige Authentifizierung](pim-how-to-change-default-settings.md) für jede Rolle. |
| **Prävention** | [Fordern Sie MFA](pim-how-to-change-default-settings.md) für jede Rolle an.  |
| **Aktionen zur Risikominderung im Portal** | Dadurch wird die mehrstufige Authentifizierung für die Aktivierung der privilegierten Rolle erforderlich. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Die Organisation verfügt nicht über Azure AD Premium P2

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Die aktuelle Azure AD-Organisation verfügt nicht über Azure AD Premium P2. |
| **Wie behebe ich das Problem?** | Lesen Sie die Informationen zu [Azure AD-Editionen](../fundamentals/active-directory-whatis.md). Führen Sie ein Upgrade auf Azure AD Premium P2 durch. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenzielle veraltete Konten in einer privilegierten Rolle

| | |
| --- | --- |
| **Severity** | Medium |
| **Warum erhalte ich diese Warnung?** | Für Konten in einer privilegierten Rolle wurde das Kennwort in den letzten 90 Tagen nicht geändert. Diese Konten können Dienst- oder gemeinsam genutzte Konten sein, die nicht verwaltet werden und anfällig für Angriffe sind. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Konten in der Liste. Wenn kein Zugriff darauf mehr erforderlich ist, entfernen sie aus ihren privilegierten Rollen. |
| **Prävention** | Stellen Sie sicher, dass die starken Kennwörter für freigegebenen Konten rotiert werden, wenn sich die Benutzer ändern, die das Kennwort kennen. </br>Überprüfen Sie Konten mit privilegierten Rollen regelmäßig mithilfe von [Zugriffsüberprüfungen](pim-how-to-start-security-review.md), und entfernen Sie nicht mehr benötigte Rollenzuweisungen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **bewährten Methoden** | Für freigegebene Konten sowie Dienst- und Notfallzugriffskonten, die ein Kennwort zur Authentifizierung verwenden und administrativen Rollen mit hohen Berechtigungen (z. B. der Rolle „Globaler Administrator“ oder „Sicherheitsadministrator“) zugewiesen sind, ist für die folgenden Fälle eine Kennwortrotation erforderlich:<ul><li>Nach einem Sicherheitsvorfall, bei dem administrative Zugriffsrechte missbraucht oder kompromittiert wurden</li><li>Nachdem die Berechtigungen eines Benutzers geändert wurden, sodass er kein Administrator mehr ist (beispielsweise nachdem ein Mitarbeiter, der ein Administrator war, die IT-Abteilung oder Organisation verlassen hat)</li><li>In regelmäßigen Abständen (z. B. vierteljährlich oder jährlich), auch wenn keine Sicherheitsverletzung bekannt ist oder kein Wechsel im IT-Personal stattgefunden hat</li></ul>Da mehrere Personen Zugriff auf die Anmeldeinformationen dieser Konten haben, sollte durch eine Rotation der Anmeldeinformationen sichergestellt werden, dass Personen, die ihre Rollen nicht mehr innehaben, keinen Zugriff auf die Konten mehr haben. [Weitere Informationen](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Rollen werden außerhalb von Privileged Identity Management zugewiesen

| | |
| --- | --- |
| **Severity** | High |
| **Warum erhalte ich diese Warnung?** | Außerhalb von Privileged Identity Management vorgenommene Zuweisungen privilegierter Rollen werden nicht ordnungsgemäß überwacht und können auf einen aktiven Angriff hinweisen. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie die Benutzer aus privilegierten Rollen, die außerhalb von Privileged Identity Management zugewiesen wurden. |
| **Prävention** | Untersuchen Sie, wo Benutzern privilegierte Rollen außerhalb von Privileged Identity Management zugewiesen werden, und verbieten Sie zukünftige Zuweisungen, die von dort stammen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt den Benutzer aus der privilegierten Rolle. |

### <a name="there-are-too-many-global-administrators"></a>Es gibt zu viele globale Administratoren

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | „Globaler Administrator“ ist die höchste privilegierte Rolle. Wenn ein globaler Administrator gefährdet ist, erhält der Angreifer Zugriff auf alle Berechtigungen dieses Administrators, sodass Ihr gesamtes System gefährdet ist. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie alle Benutzer, die nicht unbedingt die Rolle „Globaler Administrator“ benötigen. </br>Weisen Sie diesen Benutzern stattdessen weniger privilegierte Rollen zu. |
| **Prävention** | Weisen Sie Benutzern die am wenigsten privilegierte Rolle zu, die sie benötigen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **Trigger** | Wird ausgelöst, wenn zwei verschiedene Kriterien erfüllt sind, und Sie können beide konfigurieren. Erstens müssen Sie einen bestimmten Schwellenwert für globale Administratoren erreichen. Zweitens muss ein bestimmter Prozentsatz aller Rollenzuweisungen globale Administratoren betreffen. Wenn nur eines dieser Kriterien erfüllt ist, wird die Warnung nicht angezeigt. |
| **Mindestanzahl globaler Administratoren** | Diese Einstellung gibt die Anzahl von globalen Administratoren (von 2 bis 100) an, die Sie für Ihre Azure AD-Organisation als zu gering betrachten. |
| **Prozentsatz von globalen Administratoren** | Diese Einstellung gibt den Mindestprozentsatz von globalen Administratoren (von 0 % bis 100 %) an, unter den Ihre Azure AD-Organisation nicht fallen soll. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollen werden zu häufig aktiviert

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Mehrere Aktivierungen für dieselbe privilegierte Rolle durch denselben Benutzer sind ein Zeichen für einen Angriff. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und stellen Sie sicher, dass die [Aktivierungsdauer](pim-how-to-change-default-settings.md) für die privilegierte Rolle lang genug gewählt ist, damit diese Aufgaben durchgeführt werden können. |
| **Prävention** | Stellen Sie sicher, dass die [Aktivierungsdauer](pim-how-to-change-default-settings.md) für privilegierte Rollen lang genug gewählt ist, damit Benutzer diese Aufgaben durchführen können.</br>[Erzwingen Sie die mehrstufige Authentifizierung](pim-how-to-change-default-settings.md) für privilegierte Rollen, deren Konten von mehreren Administratoren gemeinsam verwendet werden. |
| **Aktionen zur Risikominderung im Portal** | – |
| **Trigger** | Wird ausgelöst, wenn ein Benutzer mehrmals innerhalb eines bestimmten Zeitraums dieselbe privilegierte Rolle aktiviert. Sie können den Zeitraum und die Anzahl der Aktivierungen konfigurieren. |
| **Zeitrahmen für Aktivierungsverlängerung** | Diese Einstellung gibt den Zeitraum in Tagen, Stunden, Minuten und Sekunden an, den Sie verwenden möchten, um verdächtige Verlängerungen nachzuverfolgen. |
| **Anzahl von Aktivierungsverlängerungen** | Diese Einstellung gibt die Anzahl von Aktivierungen (von 2 bis 100) im gewählten Zeitraum an, über die Sie benachrichtigt werden möchten. Sie können diese Einstellung mithilfe des Schiebereglers oder durch Eingabe einer Zahl in das Textfeld ändern. |

## <a name="configure-security-alert-settings"></a>Konfigurieren der Einstellungen für Sicherheitswarnungen

Sie können einige der Sicherheitswarnungen in Privileged Identity Management gemäß den Anforderungen und Sicherheitszielen Ihrer Organisation anpassen. Gehen Sie wie folgt vor, um Einstellungen für Sicherheitswarnungen zu öffnen:

1. Öffnen Sie in Azure AD den Dienst **Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Einstellungen** und dann **Warnungen** aus.

    ![Azure AD-Rollen – Einstellungen mit ausgewählten Warnungen](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Wählen Sie einen Warnungsnamen aus, um die Einstellung für die entsprechende Warnung zu konfigurieren.

    ![Sicherheitswarnungseinstellungen-Bereich für die ausgewählte Warnung](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](pim-how-to-change-default-settings.md)