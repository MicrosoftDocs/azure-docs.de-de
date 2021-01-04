---
title: Zuweisen von Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) zuweisen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/16/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0e230a975748fe2f737c4b8fe8491887351d387
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004669"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Zuweisen von Azure AD-Rollen in Privileged Identity Management

Mit Azure Active Directory (Azure AD) kann ein globaler Administrator **dauerhafte** Rollenzuweisungen für Azure AD-Administratoren vornehmen. Diese Rollenzuweisungen können über das [Azure-Portal](../roles/permissions-reference.md) oder über [PowerShell-Befehle](/powershell/module/azuread#directory_roles) erstellt werden.

Der Dienst Azure AD Privileged Identity Management (PIM) ermöglicht es auch Administratoren für privilegierte Rollen, dauerhafte Administratorrollen zuzuweisen. Außerdem können Administratoren für privilegierte Rollen Benutzer als für Azure AD-Administratorrollen **berechtigt** festlegen. Ein berechtigter Administrator kann die Rolle bei Bedarf aktivieren, und die entsprechenden Berechtigungen laufen nach einem bestimmten Zeitraum ab.

## <a name="determine-your-version-of-pim"></a>Ermitteln der PIM-Version

Ab November 2019 werden die Azure AD Rollen von Privileged Identity Management auf eine neue Version aktualisiert, die dem Verhalten von Azure-Ressourcenrollen entspricht. Dadurch werden zusätzliche Features verfügbar, und es kommt zu [Änderungen an der vorhandenen API](azure-ad-roles-features.md#api-changes). Welche Verfahren Sie in diesem Artikel während der Einführung der neuen Version befolgen, hängt von Ihrer aktuellen Version von Privileged Identity Management ab. Führen Sie die Schritte in diesem Abschnitt aus, um Ihre Version von Privileged Identity Management zu ermitteln. Nachdem Sie Ihre Version von Privileged Identity Management kennen, können Sie die Verfahren in diesem Artikel auswählen, die dieser Version entsprechen.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) als Benutzer mit der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) an.
1. Öffnen Sie **Azure AD Privileged Identity Management**. Wenn sich oben auf der Übersichtsseite ein Banner befindet, befolgen Sie die Anweisungen auf der Registerkarte **Neue Version** dieses Artikels. Andernfalls befolgen Sie die Anweisungen auf der Registerkarte **Vorherige Version**.

  [![Auswählen von „Azure AD“ > „Privileged Identity Management“](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Neue Version](#tab/new)

## <a name="assign-a-role"></a>Zuweisen einer Rolle

Führen Sie folgende Schritte aus, um einen Benutzer als für eine Azure AD-Administratorrolle „berechtigt“ festzulegen.

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) als Benutzer an, der ein Mitglied der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) ist.

    Informationen dazu, wie Sie anderen Administratoren Zugriff zum Verwalten von Privileged Identity Management gewähren, finden Sie unter [Gewähren von Zugriff für andere Administratoren zum Verwalten von PIM](pim-how-to-give-access-to-pim.md).

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Rollen** aus, um die Liste der Rollen für Azure AD-Berechtigungen anzuzeigen.

    ![Screenshot: Seite „Rollen“ mit ausgewählter Aktion „Zuweisungen hinzufügen“](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Wählen Sie **Zuweisungen hinzufügen** aus, um die Seite **Zuweisungen hinzufügen** zu öffnen.

1. Wählen Sie **Rolle auswählen** aus, um die Seite **Rolle auswählen** zu öffnen.

    ![Bereich „Neue Zuweisung“](./media/pim-how-to-add-role-to-user/select-role.png)

1. Wählen Sie eine Rolle aus, die Sie zuweisen möchten. Wählen Sie ein Mitglied aus, dem die Rolle zugewiesen werden soll. Wählen Sie dann **Weiter** aus.

1. Wählen Sie in der Liste **Zuweisungstyp** im Bereich **Mitgliedschaftseinstellungen** die Option **Berechtigt** oder **Aktiv** aus.

    - Für **berechtigte** Zuweisungen muss das Mitglied der Rolle eine Aktion durchführen, um die Rolle verwenden zu können. Beispiele für Aktionen sind eine erfolgreiche Multi-Factor Authentication-Überprüfung (MFA), die Angabe einer geschäftlichen Begründung oder das Anfordern einer Genehmigung von den angegebenen genehmigenden Personen.

    - Für **aktive** Zuweisungen ist es nicht erforderlich, dass das Mitglied eine Aktion durchführt, um die Rolle nutzen zu können. Für Mitglieder, die als „Aktiv“ zugewiesen sind, sind die Berechtigungen immer der Rolle zugewiesen.

1. Wenn Sie eine bestimmte Zuweisungsdauer angeben möchten, fügen Sie ein Start-und ein Enddatum und Zeitfelder hinzu. Wählen Sie abschließend **Zuweisen** aus, um die neue Rollenzuweisung zu erstellen.

    ![Mitgliedschaftseinstellungen: Datum und Uhrzeit](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Nachdem die Rolle zugewiesen wurde, wird eine Benachrichtigung über den Zuweisungsstatus angezeigt.

    ![Neue Zuweisung: Benachrichtigung](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Zuweisen einer Rolle mit eingeschränktem Bereich

Bei bestimmten Rollen kann der Bereich der erteilten Berechtigungen auf eine einzelne Verwaltungseinheit, einen Dienstprinzipal oder eine Anwendung beschränkt werden. Dieses Verfahren ist ein Beispiel für die Zuweisung einer Rolle, die den Bereich einer Verwaltungseinheit aufweist. Eine Liste der Rollen, die einen Bereich per Verwaltungseinheit unterstützen, finden Sie unter [Zuweisen von bereichsbezogenen Rollen zu einer Verwaltungseinheit](../roles/admin-units-assign-roles.md). Diese Funktion wird derzeit in Azure AD-Organisationen eingeführt.

1. Melden Sie sich mit Berechtigungen der Rolle „Administrator für privilegierte Rollen“ im [Azure Active Directory Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus.

1. Wählen Sie **Benutzeradministrator** aus.

    ![Der Befehl „Zuweisung hinzufügen“ ist verfügbar, wenn Sie eine Rolle im Portal öffnen](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Wählen Sie **Zuweisungen hinzufügen** aus.

    ![Wenn eine Rolle Bereiche unterstützt, können Sie einen Bereich auswählen](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Auf der Seite **Zuweisungen hinzufügen** haben Sie folgende Möglichkeiten:

   - Auswählen eines Benutzers oder einer Gruppe, der bzw. die der Rolle zugewiesen werden soll
   - Auswählen des Rollenbereichs (in diesem Fall „Verwaltungseinheiten“)
   - Auswählen einer Verwaltungseinheit für den Bereich

Weitere Informationen zum Erstellen von Verwaltungseinheiten finden Sie unter [Hinzufügen und Entfernen von Verwaltungseinheiten](../roles/admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung

Befolgen Sie diese Anweisungen zum Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Rollen** aus, um die Liste der Rollen für Azure AD anzuzeigen.

1. Wählen Sie die Rolle aus, die Sie aktualisieren oder entfernen möchten.

1. Suchen Sie die Rollenzuweisung auf den Registerkarten **Berechtigte Rollen** oder **Aktive Rollen**.

    ![Aktualisieren oder Entfernen der Rollenzuweisung](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Wählen Sie **Aktualisieren** oder **Entfernen** aus, um die Rollenzuweisung zu aktualisieren oder zu entfernen.

# <a name="previous-version"></a>[Vorherige Version](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Festlegen eines Benutzers als „berechtigt“ für eine Rolle

Führen Sie folgende Schritte aus, um einen Benutzer als für eine Azure AD-Administratorrolle „berechtigt“ festzulegen.

1. Wählen Sie **Rollen** oder **Mitglieder** aus.

    ![Öffnen Sie Azure AD-Rollen](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Wählen Sie **Mitglied hinzufügen** aus, um das Dialogfeld **Verwaltete Mitglieder hinzufügen** zu öffnen.

1. Wählen Sie **Rolle auswählen** und eine Rolle aus, die Sie verwalten möchten, und wählen Sie dann **Auswählen** aus.

    ![Auswählen einer Rolle](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Wählen Sie **Mitglieder auswählen** und die Benutzer aus, die der Rolle zugewiesen werden sollen, und wählen Sie dann **Auswählen** aus.

    ![Wählen Sie zum Zuweisen einen Benutzer oder eine Gruppe aus](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Wählen Sie **OK** in **Verwaltete Mitglieder hinzufügen** aus, um den Benutzer der Rolle hinzuzufügen.

1. Wählen Sie in der Liste der Rollen die soeben zugewiesene Rolle aus, um die Liste der Mitglieder anzuzeigen.

     Nach dem Zuweisen der Rolle wird der von Ihnen ausgewählte Benutzer in der Liste als **Berechtigt** für die Rolle angezeigt.

    ![Berechtigter Benutzer für eine Rolle](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nachdem der Benutzer nun für die Rolle berechtigt ist, informieren Sie ihn darüber, dass er die Rolle wie unter [Aktivieren meiner Azure AD-Rollen in PIM](pim-how-to-activate-role.md) beschrieben aktivieren kann.

    Berechtigte Administratoren werden aufgefordert, sich während der Aktivierung für Azure AD Multi-Factor Authentication zu registrieren. Wenn sich ein Benutzer nicht für MFA registrieren kann oder ein Microsoft-Konto nutzt (z.B. @outlook.com), müssen Sie ihn in allen seinen Rollen als permanent einrichten.

## <a name="make-a-role-assignment-permanent"></a>Einrichten einer permanenten Rollenzuweisung

Standardmäßig sind neue Benutzer nur für eine Azure AD-Administratorrolle *berechtigt*. Führen Sie die folgenden Schritte aus, wenn Sie eine Rollenzuweisung permanent festlegen möchten.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Mitglieder** aus.

    ![Liste der Mitglieder](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Wählen Sie eine als **Berechtigt** markierte Rolle aus, die Sie als dauerhafte Rollenzuweisung festlegen möchten.

1. Wählen Sie **Weitere** und dann **Als permanent festlegen** aus.

    ![Einrichten einer permanenten Rollenzuweisung](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Die Rolle wird jetzt als **permanent** aufgeführt.

    ![Liste der Mitglieder mit Änderung in „permanent“](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Entfernen eines Benutzers aus einer Rolle

Sie können Benutzer aus Rollenzuweisungen entfernen. Stellen Sie dabei jedoch sicher, dass immer mindestens ein Benutzer als permanenter globaler Administrator vorhanden ist. Wenn Sie nicht sicher sind, welche Benutzer ihre Rollenzuweisungen noch benötigen, können Sie [eine Zugriffsüberprüfung für die Rolle starten](pim-how-to-start-security-review.md).

Führen Sie folgende Schritte aus, um einen Benutzer aus einer Azure AD-Administratorrolle zu entfernen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Mitglieder** aus.

    ![Liste der Mitglieder](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Wählen Sie eine zu entfernende Rollenzuweisung aus.

1. Wählen Sie **Weitere** und dann **Entfernen** aus.

    ![Entfernen einer Rolle](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Klicken Sie im Bestätigungsdialogfeld auf **Ja**.

    ![Bestätigen Sie das Entfernen](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Die Rollenzuweisung wurde entfernt.

## <a name="authorization-error-when-assigning-roles"></a>Autorisierungsfehler beim Zuweisen von Rollen

Wenn Sie Privileged Identity Management erst kürzlich für ein Abonnement aktiviert haben und beim Versuch, einen Benutzer einer Azure AD-Administratorrolle zuzuweisen, einen Autorisierungsfehler erhalten, verfügt der MS-PIM-Dienstprinzipal möglicherweise noch nicht über die erforderlichen Berechtigungen. Der MS-PIM-Dienstprinzipal benötigt die Rolle [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator), um anderen Benutzern Rollen zuzuweisen. Anstatt zu warten, bis PIM die Rolle „Benutzerzugriffsadministrator“ zugewiesen wurde, können Sie sie auch manuell zuweisen.

Gehen Sie wie folgt vor, um die Rolle „Benutzerzugriffsadministrator“ dem MS-PIM-Dienstprinzipal für ein Abonnement zuzuweisen.

1. Melden Sie sich beim Azure-Portal als globaler Administrator an.

1. Wählen Sie **Alle Dienste** und dann **Abonnements** aus.

1. Wählen Sie Ihr Abonnement aus.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Wählen Sie **Rollenzuweisungen** aus, um die aktuelle Liste der Rollenzuweisungen im Abonnementbereich anzuzeigen.

   ![Blatt „Zugriffssteuerung (IAM)“ für ein Abonnement](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Überprüfen Sie, ob der **MS-PIM**-Dienstprinzipal der Rolle **Benutzerzugriffsadministrator** zugewiesen ist.

1. Falls nicht, wählen Sie **Rollenzuweisung hinzufügen** aus, um den Bereich **Rollenzuweisung hinzufügen** zu öffnen.

1. Wählen Sie in der Dropdownliste **Rolle** die Rolle **Benutzerzugriffsadministrator** aus.

1. Wählen Sie in der Liste **Auswählen** den Dienstprinzipal **MS-PIM** aus.

   ![Bereich „Rollenzuweisung hinzufügen“: Hinzufügen von Berechtigungen für den Dienstprinzipal „MS-PIM“](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Wählen Sie zum Zuweisen einer Rolle **Speichern** aus.

   Nach kurzer Zeit wird dem MS-PIM-Dienstprinzipal im Abonnementbereich die Rolle „Benutzerzugriffsadministrator“ zugewiesen.

   ![Zugriffssteuerungsseite mit Rollenzuweisung „Benutzerzugriffsadministrator“ für den MS-PIM-Dienstprinzipal](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](pim-how-to-change-default-settings.md)
- [Zuweisen von Azure-Ressourcenrollen in PIM](pim-resource-roles-assign-roles.md)