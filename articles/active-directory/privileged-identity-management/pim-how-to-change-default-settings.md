---
title: Konfigurieren von Einstellungen für Azure AD-Rollen in PIM – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Einstellungen für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) konfigurieren.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fadcf3f5aefa76ab03d6313643fc18df71f6c3b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004652"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurieren von Einstellungen für Azure AD-Rollen in PIM

Ein Administrator für privilegierte Rollen kann Privileged Identity Management (PIM) in der Azure AD-Organisation (Azure Active Directory) anpassen und dabei auch die Art und Weise ändern, in der ein Benutzer eine berechtigte Rollenzuweisung aktiviert.

## <a name="determine-your-version-of-pim"></a>Ermitteln der PIM-Version

Ab November 2019 werden die Azure AD Rollen von Privileged Identity Management auf eine neue Version aktualisiert, die dem Verhalten von Azure-Ressourcenrollen entspricht. Dadurch werden zusätzliche Features verfügbar, und es kommt zu [Änderungen an der vorhandenen API](azure-ad-roles-features.md#api-changes). Welche Verfahren Sie in diesem Artikel während der Einführung der neuen Version befolgen, hängt von Ihrer aktuellen Version von Privileged Identity Management ab. Führen Sie die Schritte in diesem Abschnitt aus, um Ihre Version von Privileged Identity Management zu ermitteln. Nachdem Sie Ihre Version von Privileged Identity Management kennen, können Sie die Verfahren in diesem Artikel auswählen, die dieser Version entsprechen.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) als Benutzer mit der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) an.
1. Öffnen Sie **Azure AD Privileged Identity Management**. Wenn sich oben auf der Übersichtsseite ein Banner befindet, befolgen Sie die Anweisungen auf der Registerkarte **Neue Version** dieses Artikels. Andernfalls befolgen Sie die Anweisungen auf der Registerkarte **Vorherige Version**.

  [![Auswählen von „Azure AD“ > „Privileged Identity Management“](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Führen Sie die in diesem Artikel beschriebenen Schritte aus, um Anforderungen für Azure AD-Rollen zu genehmigen oder abzulehnen.

# <a name="new-version"></a>[Neue Version](#tab/new)

## <a name="open-role-settings"></a>Öffnen von Rolleneinstellungen

Führen Sie die folgenden Schritte aus, um die Einstellungen für eine Azure AD-Rolle zu öffnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Benutzer mit der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) an.
gt
1. Öffnen Sie **Azure AD Privileged Identity Management** &gt; **Azure AD-Rollen** &gt; **Rolleneinstellungen**.

    ![Seite „Rolleneinstellungen“ mit einer Auflistung der Azure AD-Rollen](./media/pim-how-to-change-default-settings/role-settings.png)

1. Wählen Sie die Rolle aus, deren Einstellungen Sie konfigurieren möchten.

    ![Seite „Details zur Rolleneinstellung“ mit einer Auflistung mehrerer Zuweisungs- und Aktivierungseinstellungen](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Klicken Sie auf **Bearbeiten**, um die Seite „Rolleneinstellungen“ zu öffnen.

    ![Seite „Rolleneinstellungen“ mit Optionen zum Aktualisieren von Zuweisungs- und Aktivierungseinstellungen](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    Im Bereich „Rolleneinstellungen“ für jede Rolle können Sie verschiedene Einstellungen konfigurieren.

## <a name="assignment-duration"></a>Zuweisungsdauer

Bei der Konfiguration von Einstellungen für eine Rolle können Sie für jeden Zuweisungstyp („Berechtigt“ und „Aktiv“) zwischen zwei Optionen für die Zuweisungsdauer wählen. Diese Optionen werden zur maximalen Standarddauer, wenn ein Benutzer der Rolle in Privileged Identity Management zugewiesen wird.

Sie können beim Typ **Berechtigt** eine dieser Optionen für die Zuweisungsdauer wählen:

| | |
| --- | --- |
| **Dauerhafte berechtigte Zuweisung zulassen** | Globale Administratoren und Administratoren mit einer privilegierten Rolle können dauerhafte berechtigte Zuweisungen zuweisen. |
| **Berechtigte Zuweisungen laufen ab nach** | Globale Administratoren und Administratoren mit einer privilegierten Rolle können anfordern, dass alle berechtigten Zuweisungen ein bestimmtes Start- und Enddatum haben. |

Beim Typ **Aktiv** können Sie eine dieser Optionen für die Zuweisungsdauer wählen:

| | |
| --- | --- |
| **Permanente aktive Zuweisung zulassen** | Globale Administratoren und Administratoren mit einer privilegierten Rolle können dauerhafte aktive Zuweisungen zuweisen. |
| **Aktive Zuweisungen laufen ab nach** | Globale Administratoren und Administratoren mit einer privilegierten Rolle können anfordern, dass alle aktiven Zuweisungen ein bestimmtes Start- und Enddatum haben. |

> [!NOTE]
> Alle Zuweisungen mit einem angegebenen Enddatum können von globalen Administratoren und Administratoren mit einer privilegierten Rolle erneuert werden. Zudem können Benutzer Self-Service-Anforderungen auslösen, um [Rollenzuweisungen zu verlängern oder zu erneuern](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Mehrstufige Authentifizierung erforderlich

Privileged Identity Management ermöglicht die optionale Erzwingung von Azure AD Multi-Factor Authentication in zwei bestimmten Szenarien.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication bei aktiver Zuweisung erforderlich

Es kann vorkommen, dass Sie einen Benutzer einer Rolle für einen kurzen Zeitraum (z. B. einen Tag) zuweisen möchten. In diesem Fall müssen die zugewiesenen Benutzer die Aktivierung nicht anfordern. In diesem Szenario kann Privileged Identity Management die Multi-Factor Authentication (MFA) nicht erzwingen, wenn der Benutzer seine Rollenzuweisung verwendet, weil die Rolle ab dem Moment der Zuweisung bereits aktiv ist.

Sie können sicherstellen, dass der Administrator, der die Zuweisung bearbeitet, auch wirklich die angegebene Person ist, indem Sie für die aktive Zuweisung MFA erzwingen. Aktivieren Sie dafür das Kontrollkästchen neben der Option **Multi-Factor Authentication bei aktiver Zuweisung erforderlich**.

### <a name="require-multi-factor-authentication-on-activation"></a>Bei Aktivierung Multi-Factor Authentication anfordern

Sie können erzwingen, dass Benutzer, die für eine Rolle berechtigt sind, vor der Aktivierung ihre Identität über Azure AD Multi-Factor Authentication bestätigen müssen. Mithilfe von MFA kann mit hoher Wahrscheinlichkeit sichergestellt werden, dass es sich auch wirklich um den jeweiligen Benutzer handelt. Durch die Erzwingung dieser Option werden wichtige Ressourcen in Situationen geschützt, in denen das Benutzerkonto unter Umständen kompromittiert wurde.

Damit vor der Aktivierung MFA erzwungen wird, müssen Sie unter **Rolleneinstellung bearbeiten** auf der Registerkarte „Zuweisung“ das Kontrollkästchen für die Option **Bei Aktivierung Multi-Factor Authentication anfordern** aktivieren.

Weitere Informationen finden Sie unter [Multi-Factor Authentication und Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximale Aktivierungsdauer

Mit dem Schieberegler **Maximale Aktivierungsdauer** geben Sie die maximale Zeit in Stunden an, die eine Rolle aktiv bleibt, bevor sie abläuft. Dieser Wert kann zwischen einer und 24 Stunden betragen.

## <a name="require-justification"></a>Verlangen einer Begründung

Sie können verlangen, dass Benutzer bei der Aktivierung eine geschäftliche Begründung angeben müssen. Um eine Begründung zu verlangen, aktivieren Sie das Kontrollkästchen **Begründung für aktive Zuweisung erforderlich** oder **Begründung für Aktivierung erforderlich**.

## <a name="require-approval-to-activate"></a>Erzwingen der Genehmigung für die Aktivierung

Wenn Sie mehrere genehmigende Personen festlegen, ist der Genehmigungsvorgang abgeschlossen, sobald eine dieser Personen die Genehmigung gewährt oder verweigert hat. Sie können keine Genehmigung von mindestens zwei Benutzern anfordern. Wenn Sie für die Aktivierung einer Rolle eine Genehmigung anfordern möchten, gehen Sie wie folgt vor.

1. Aktivieren Sie das Kontrollkästchen **Genehmigung zum Aktivieren anfordern**.

1. Wählen Sie **Genehmigende Personen auswählen** aus.

    ![Bereich „Mitglied oder Gruppe auswählen“ zum Auswählen von genehmigenden Personen](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Wählen Sie mindestens einen Benutzer aus, und klicken Sie dann auf **Auswählen**. Sie müssen mindestens eine genehmigende Person auswählen. Für genehmigende Personen gibt es keine Standardeinstellung.

    Ihre Auswahl wird in der Liste der ausgewählten genehmigenden Personen angezeigt.

1. Wenn Sie alle gewünschten Rolleneinstellungen angegeben haben, klicken Sie auf **Aktualisieren**, um Ihre Änderungen zu speichern.

# <a name="previous-version"></a>[Vorherige Version](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Öffnen von Rolleneinstellungen (vorherige Version)

Führen Sie die folgenden Schritte aus, um die Einstellungen für eine Azure AD-Rolle zu öffnen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Settings** aus.

    ![Azure AD-Rollen – Einstellungen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Wählen Sie **Rollen** aus.

1. Wählen Sie die Rolle aus, deren Einstellungen Sie konfigurieren möchten.

    ![Azure AD-Rollen – Einstellungen für Rollen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Auf der Einstellungsseite für jede Rolle können Sie verschiedene Einstellungen konfigurieren. Diese Einstellungen wirken sich nur auf Benutzer aus, die über **berechtigte** Zuweisungen verfügen, nicht über **permanente** Zuweisungen.

## <a name="activations"></a>Aktivierungen

Legen Sie mit dem Schieberegler **Aktivierungen** die maximale Zeit in Stunden fest, die eine Rolle aktiv bleibt, bevor sie abläuft. Dieser Wert kann zwischen 1 und 72 Stunden liegen.

## <a name="notifications"></a>Benachrichtigungen

Mit dem Schalter **Benachrichtigungen** können Sie angeben, ob Administratoren E-Mail-Benachrichtigungen empfangen, wenn Rollen aktiviert werden. Diese Benachrichtigungen können für die Erkennung von nicht autorisierten oder unzulässigem Aktivierungen nützlich sein.

Wenn diese Option auf **Aktivieren** festgelegt ist, werden Benachrichtigungen an folgende Administratoren gesendet:

- Administrator für privilegierte Rollen
- Sicherheitsadministrator
- Globaler Administrator

Weitere Informationen finden Sie unter [E-Mail-Benachrichtigungen in Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Incident/Ticket anfordern

Mit dem Schalter **Incident/Ticket anfordern** können Sie festlegen, dass berechtigte Administratoren eine Ticketnummer angeben müssen, wenn sie ihre Rolle aktivieren. Dies kann die Überprüfung des Rollenzugriffs effektiver machen.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Mit dem Schalter **Multi-Factor Authentication** können Sie angeben, ob Benutzer ihre Identität mit MFA verifizieren müssen, bevor sie ihre Rollen aktivieren können. Sie müssen ihre Identität nur einmal pro Sitzung verifizieren, nicht jedes Mal, wenn sie eine Rolle aktivieren. Beachten Sie zwei Tipps, wenn Sie die MFA aktivieren:

- Benutzer, die Microsoft-Konten als E-Mail-Adressen verwenden (in der Regel @outlook.com, aber nicht immer), können sich nicht für Azure AD Multi-Factor Authentication registrieren. Wenn Sie Benutzern mit Microsoft-Konten Rollen zuweisen möchten, sollten Sie sie zu permanenten Administratoren machen oder Multi-Factor Authentication für diese Rolle deaktivieren.
- Sie können Azure AD Multi-Factor Authentication für sehr privilegierte Rollen für Azure AD und Microsoft 365 nicht deaktivieren. Dieses Sicherheitsfeature trägt zum Schutz der folgenden Rollen bei:  
  
  - Azure Information Protection-Administrator
  - Rechnungsadministrator
  - Cloudanwendungsadministrator
  - Complianceadministrator
  - Administrator für bedingten Zugriff
  - Dynamics 365-Administrator
  - Genehmigende Person für den LockBox-Kundenzugriff
  - Verzeichnis schreiben
  - Exchange-Administrator
  - Globaler Administrator
  - Intune-Administrator
  - Power BI-Administrator
  - Administrator für privilegierte Rollen
  - Sicherheitsadministrator
  - SharePoint-Administrator
  - Skype for Business-Administrator
  - Benutzeradministrator

Weitere Informationen finden Sie unter [Multi-Factor Authentication und Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Genehmigung anfordern

Wenn Sie die angeforderte Genehmigung zum Aktivieren einer Rolle delegieren möchten, gehen Sie wie folgt vor.

1. Legen Sie den Schalter **Genehmigung anfordern** auf **Aktiviert** fest. Der Bereich wird erweitert und zeigt Optionen zum Auswählen von genehmigenden Personen an.

    ![Screenshot: Schalter „Genehmigung erforderlich“ mit ausgewählter Option „Aktivieren“](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Wenn Sie keine genehmigenden Personen angeben, wird der Administrator für privilegierte Rollen zum Standardgenehmiger und muss dann alle Aktivierungsanforderungen für diese Rolle genehmigen.

1. Um genehmigende Personen hinzuzufügen, klicken Sie auf **Genehmigende Personen auswählen**.

    ![Azure AD-Rollen – Einstellungen: Genehmigung anfordern](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wählen Sie zusätzlich zum Administrator für privilegierten Rollen eine oder mehrere genehmigende Personen aus, und klicken Sie dann auf **Auswählen**. Es sollten mindestens zwei genehmigende Personen hinzugefügt werden. Auch wenn Sie sich selbst als genehmigende Person hinzufügen, können Sie eine Rollenaktivierung nicht für sich selbst genehmigen. Ihre Auswahl wird in der Liste der ausgewählten genehmigenden Personen angezeigt.

1. Wenn Sie alle gewünschten Rolleneinstellungen angegeben haben, wählen Sie **Speichern** aus, um die Änderungen zu speichern.

---

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
- [Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in Privileged Identity Management (PIM)](pim-how-to-configure-security-alerts.md)
