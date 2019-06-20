---
title: Konfigurieren von Sicherheitswarnungen für Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Sicherheitswarnungen für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) konfigurieren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0d99fb283be8cbeba6f8a7954ff49161a2d511
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60288550"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-pim"></a>Konfigurieren von Sicherheitswarnungen für Azure AD-Rollen in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) generiert bei verdächtigen oder nicht sicheren Aktivitäten in Ihrer Umgebung Warnungen. Wenn eine Warnung ausgelöst wird, wird sie im PIM-Dashboard angezeigt. Wählen Sie die Warnung aus, um einen Bericht mit allen Benutzern oder Rollen anzuzeigen, die die Warnung ausgelöst haben.

![PIM-Sicherheitswarnungen – Screenshot](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Sicherheitswarnungen

Dieser Abschnitt enthält alle Sicherheitswarnungen für Azure AD-Rollen sowie Informationen zur Behebung und Vermeidung. Der Schweregrad hat folgende Bedeutung:

* **Hoch**: Aufgrund einer Richtlinienverletzung ist eine sofortige Aktion erforderlich.
* **Mittel:** Es ist keine sofortige Aktion erforderlich, aber es wird eine potenzielle Richtlinienverletzung angezeigt.
* **Niedrig**: Es ist keine sofortige Aktion erforderlich, aber es wird eine Richtlinienänderung vorgeschlagen.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratoren verwenden ihre privilegierten Rollen nicht.

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Benutzer, denen nicht benötigte privilegierte Rollen zugewiesen wurden, erhöhen die Möglichkeit eines Angriffs. Es ist auch einfacher für Angreifer, auf Konten, die nicht aktiv genutzt werden, unbemerkt zu bleiben. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie die Zuweisung zu privilegierten Rollen, die sie nicht benötigen. |
| **Prävention** | Weisen Sie privilegierte Rollen Benutzern zu, die eine geschäftliche Rechtfertigung haben. </br>Planen Sie regelmäßige [Zugriffsüberprüfungen](pim-how-to-start-security-review.md), um sicherzustellen, dass die Benutzer ihren Zugriff weiterhin benötigen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **Trigger** | Wird auslöst, wenn ein Benutzer für eine bestimmte Zeit eine Rolle nicht aktiviert hat. |
| **Anzahl von Tagen** | Diese Einstellung gibt die Anzahl von Tagen zwischen 0 und 100 an, die ein Benutzer eine Rolle nicht aktivieren muss.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Rollen erfordern für die Aktivierung keine Multi-Factor Authentication.

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Ohne MFA können kompromittierte Benutzer privilegierte Rollen aktivieren. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Liste der Rollen, und [fordern Sie MFA für jede Rolle an](pim-how-to-change-default-settings.md). |
| **Prävention** | [Fordern Sie MFA](pim-how-to-change-default-settings.md) für jede Rolle an.  |
| **Aktionen zur Risikominderung im Portal** | Damit ist MFA für die Aktivierung der privilegierten Rolle erforderlich. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>Der Mandant verfügt nicht über Microsoft Azure AD Premium P2.

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Der aktuelle Mandant verfügt nicht über Microsoft Azure AD Premium P2. |
| **Wie behebe ich das Problem?** | Lesen Sie die Informationen zu [Azure AD-Editionen](../fundamentals/active-directory-whatis.md). Führen Sie ein Upgrade auf Azure AD Premium P2 durch. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenzielle veraltete Konten in einer privilegierten Rolle

| | |
| --- | --- |
| **Severity** | Mittel |
| **Warum erhalte ich diese Warnung?** | Konten in einer privilegierten Rolle, die ihr Kennwort in den letzten 90 Tagen nicht geändert haben. Diese Konten können Dienst- oder gemeinsam genutzte Konten sein, die nicht verwaltet werden und anfällig für Angriffe sind. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Konten in der Liste. Wenn kein Zugriff darauf mehr erforderlich ist, entfernen sie aus ihren privilegierten Rollen. |
| **Prävention** | Stellen Sie sicher, dass die starken Kennwörter für freigegebenen Konten rotiert werden, wenn sich die Benutzer ändern, die das Kennwort kennen. </br>Überprüfen Sie Konten mit privilegierten Rollen regelmäßig mithilfe von [Zugriffsüberprüfungen](pim-how-to-start-security-review.md), und entfernen Sie nicht mehr benötigte Rollenzuweisungen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **bewährten Methoden** | Für freigegebene Konten sowie Dienst- und Notfallzugriffskonten, die ein Kennwort zur Authentifizierung verwenden und administrativen Rollen mit erhöhten Berechtigungen zugewiesen sind (z. B. globaler Administrator oder Sicherheitsadministrator), ist für die folgenden Fälle eine Kennwortrotation erforderlich:<ul><li>Nach einem Sicherheitsvorfall, bei dem administrative Zugriffsrechte missbraucht oder kompromittiert wurden</li><li>Nachdem die Berechtigungen eines Benutzers geändert wurden, sodass er kein Administrator mehr ist (beispielsweise nachdem ein Mitarbeiter, der ein Administrator war, die IT-Abteilung oder Organisation verlassen hat)</li><li>In regelmäßigen Abständen (z. B. vierteljährlich oder jährlich), auch wenn keine Sicherheitsverletzung bekannt ist oder kein Wechsel im IT-Personal stattgefunden hat</li></ul>Da mehrere Personen Zugriff auf die Anmeldeinformationen dieser Konten haben, sollte durch eine Rotation der Anmeldeinformationen sichergestellt werden, dass Personen, die ihre Rollen nicht mehr innehaben, keinen Zugriff auf die Konten mehr haben. [Weitere Informationen](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>Rollen werden außerhalb von PIM zugewiesen

| | |
| --- | --- |
| **Severity** | Hoch |
| **Warum erhalte ich diese Warnung?** | Privilegierte Rollenzuweisungen außerhalb von PIM werden nicht ordnungsgemäß überwacht und können auf einen aktiven Angriff hinweisen. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie die Zuweisung zu privilegierten Rollen, die außerhalb von PIM zugewiesen wurden. |
| **Prävention** | Untersuchen Sie, wo Benutzern außerhalb des PIM privilegierte Rollen zugewiesen werden, und verbieten Sie zukünftige Zuweisungen, die von dort stammen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |

### <a name="there-are-too-many-global-administrators"></a>Es gibt zu viele globale Administratoren

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Globaler Administrator ist die höchste privilegierte Rolle. Wenn ein globaler Administrator gefährdet ist, erhält der Angreifer Zugriff auf alle seine Berechtigungen, sodass Ihr gesamtes System gefährdet ist. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und entfernen Sie alle, die nicht unbedingt die Rolle eines globalen Administrators benötigen. </br>Weisen Sie diesen Benutzern weniger privilegierte Rollen zu. |
| **Prävention** | Weisen Sie Benutzern die am wenigsten privilegierte Rolle zu, die sie benötigen. |
| **Aktionen zur Risikominderung im Portal** | Entfernt das Konto aus der privilegierte Rolle. |
| **Trigger** | Wird ausgelöst, wenn zwei verschiedene Kriterien erfüllt sind, und Sie können beide konfigurieren. Erstens müssen Sie einen bestimmten Schwellenwert an globalen Administratoren erreichen. Zweitens muss ein bestimmter Anteil aller Rollenzuweisungen globale Administratoren betreffen. Wenn nur eines dieser Kriterien erfüllt ist, wird die Warnung nicht angezeigt. |
| **Mindestanzahl globaler Administratoren** | Diese Einstellung gibt die Anzahl von globalen Administratoren zwischen 2 und 100 an, die Sie als eine unsichere Anzahl betrachten. |
| **Prozentsatz von globalen Administratoren** | Diese Einstellung gibt den Prozentsatz von Administratoren an, bei denen es sich um globale Administratoren handelt, zwischen 0 % und 100 % an, der in Ihrer Umgebung als unsicher gilt. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollen werden zu häufig aktiviert

| | |
| --- | --- |
| **Severity** | Niedrig |
| **Warum erhalte ich diese Warnung?** | Mehrere Aktivierungen für dieselbe privilegierte Rolle durch denselben Benutzer sind ein Zeichen für einen Angriff. |
| **Wie behebe ich das Problem?** | Überprüfen Sie die Benutzer in der Liste, und stellen Sie sicher, dass die [Aktivierungsdauer](pim-how-to-change-default-settings.md) für die privilegierte Rolle lang genug gewählt ist, damit diese Aufgaben durchgeführt werden können. |
| **Prävention** | Stellen Sie sicher, dass die [Aktivierungsdauer](pim-how-to-change-default-settings.md) für privilegierte Rollen lang genug gewählt ist, damit Benutzer diese Aufgaben durchführen können.</br>[Erzwingen Sie MFA](pim-how-to-change-default-settings.md) für privilegierte Rollen, deren Konten von mehreren Administratoren gemeinsam verwendet werden. |
| **Aktionen zur Risikominderung im Portal** | – |
| **Trigger** | Wird ausgelöst, wenn ein Benutzer mehrmals innerhalb eines bestimmten Zeitraums dieselbe privilegierte Rolle aktiviert. Sie können den Zeitraum und die Anzahl der Aktivierungen konfigurieren. |
| **Zeitrahmen für Aktivierungsverlängerung** | Diese Einstellung gibt den Zeitraum in Tagen, Stunden, Minuten und Sekunden an, den Sie verwenden möchten, um verdächtige Verlängerungen nachzuverfolgen. |
| **Anzahl von Aktivierungsverlängerungen** | Diese Einstellung gibt im gewählten Zeitraum die Anzahl von Aktivierungen zwischen 2 und 100 an, für die Sie eine Warnung ausgeben möchten. Sie können diese Einstellung mithilfe des Schiebereglers oder durch Eingabe einer Zahl in das Textfeld ändern. |

## <a name="configure-security-alert-settings"></a>Konfigurieren der Einstellungen für Sicherheitswarnungen

Sie können einige der Sicherheitswarnungen in PIM so anpassen, dass sie in der Umgebung und mit den Sicherheitszielen genutzt werden können. Gehen Sie wie folgt vor, um Einstellungen für Sicherheitswarnungen zu öffnen:

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Einstellungen** und dann auf **Warnung**.

    ![Navigieren zu den Einstellungen für Sicherheitswarnungen](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Klicken Sie auf den Name einer Warnung, um die Einstellung für die Warnung zu konfigurieren.

    ![Einstellungen zu Sicherheitswarnungen](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](pim-how-to-change-default-settings.md)
