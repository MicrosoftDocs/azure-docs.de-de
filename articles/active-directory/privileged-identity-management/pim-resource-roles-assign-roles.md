---
title: Zuweisen von Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) zuweisen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 610aeec9e4c40d0aad0c28f02697e2cf01edbe4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438690"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Zuweisen von Azure-Ressourcenrollen in PIM

Azure Active Directory Privileged Identity Management (Azure AD PIM) kann sowohl die integrierten Azure-Ressourcenrollen als auch benutzerdefinierte Rollen verwalten. Beispiele:

- Owner (Besitzer)
- Benutzerzugriffsadministrator
- Mitwirkender
- Sicherheitsadministrator
- Sicherheits-Manager

> [!NOTE]
> Benutzer oder Mitglieder von Gruppen, die der Rolle „Besitzer“ oder „Benutzerzugriffsadministrator“ zugewiesen sind, sowie globale Administratoren, die die Abonnementverwaltung in Azure AD ermöglichen, sind Ressourcenadministratoren. Diese Administratoren können Rollen zuweisen, Rolleneinstellungen konfigurieren und den Zugriff mithilfe von PIM für Azure-Ressourcen prüfen. Eine Liste mit den integrierten Rollen für Azure-Ressourcen finden Sie [hier](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Zuweisen einer Rolle

Führen Sie folgende Schritte aus, um einen Benutzer für eine Azure AD-Ressourcenrolle als „berechtigt“ festzulegen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Benutzer an, der ein Mitglied der Rolle [Administrator für privilegierte Rollen](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ist.

    Informationen dazu, wie Sie anderen Administratoren Zugriff für die Verwaltung in PIM gewähren, finden Sie unter [Gewähren von Zugriff für andere Administratoren zum Verwalten von PIM](pim-how-to-give-access-to-pim.md).

1. Öffnen Sie **Azure AD Privileged Identity Management**.

    Wenn Sie PIM noch nicht im Azure-Portal geöffnet haben, wechseln Sie zu [Einstieg in die Verwendung von PIM](pim-getting-started.md).

1. Klicken Sie auf **Azure-Ressourcen**.

1. Verwenden Sie den **Ressourcenfilter** zum Filtern der Liste verwalteter Ressourcen.

    ![Liste der zu verwaltenden Azure-Ressourcen](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Klicken Sie auf die Ressource, die Sie verwalten möchten, z.B. ein Abonnement oder eine Verwaltungsgruppe.

1. Klicken Sie unter „Verwalten“ auf **Rollen**, um die Liste der Rollen für Azure-Ressourcen anzuzeigen.

    ![Azure-Ressourcenrollen](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Klicken Sie auf **Mitglied hinzufügen**, um den Bereich „Neue Zuweisung“ zu öffnen.

1. Klicken Sie auf **Rolle auswählen**, um den Bereich „Rolle auswählen“ zu öffnen.

    ![Bereich „Neue Zuweisung“](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Klicken Sie auf eine Rolle, die Sie zuweisen möchten, und dann auf **Auswählen**.

    Der Bereich „Mitglied oder Gruppe auswählen“ wird geöffnet.

1. Klicken Sie auf ein Mitglied oder eine Gruppe, dem bzw. der die Rolle zugewiesen werden soll, und dann auf **Auswählen**.

    ![Bereich „Mitglied oder Gruppe auswählen“](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Der Bereich „Mitgliedschaftseinstellungen“ wird geöffnet.

1. Wählen Sie in der Liste **Zuweisungstyp** entweder **Berechtigt** oder **Aktiv** aus.

    ![Bereich „Mitgliedschaftseinstellungen“](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    PIM für Azure-Ressourcen verfügt über zwei unterschiedliche Zuweisungstypen:

    - Für **berechtigte** Zuweisungen muss das Mitglied der Rolle eine Aktion durchführen, um die Rolle verwenden zu können. Beispiele für Aktionen sind eine erfolgreiche Multi-Factor Authentication-Überprüfung (MFA), die Angabe einer geschäftlichen Begründung oder das Anfordern einer Genehmigung von den angegebenen genehmigenden Personen.

    - Für **aktive** Zuweisungen ist es nicht erforderlich, dass das Mitglied eine Aktion durchführt, um die Rolle nutzen zu können. Für Mitglieder, die als „Aktiv“ zugewiesen sind, sind die Berechtigungen immer der Rolle zugewiesen.

1. Wenn die Zuweisung dauerhaft sein soll (dauerhaft berechtigt oder dauerhaft zugewiesen), aktivieren Sie das Kontrollkästchen **Dauerhaft**.

    Je nach Rolleneinstellungen wird das Kontrollkästchen möglicherweise nicht angezeigt oder ist nicht änderbar.

1. Wenn Sie einen bestimmten Zuweisungszeitraum angeben möchten, deaktivieren Sie dieses Kontrollkästchen, und ändern Sie nach Bedarf die Felder für den Start- und/oder den Endzeitpunkt (Datum und Uhrzeit).

    ![Mitgliedschaftseinstellungen: Datum und Uhrzeit](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Klicken Sie abschließend auf **Fertig**.

    ![Neue Zuweisung: Hinzufügen](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Klicken Sie auf **Hinzufügen**, um die neue Rollenzuweisung zu erstellen. Eine Benachrichtigung zum Status wird angezeigt.

    ![Neue Zuweisung: Benachrichtigung](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung

Befolgen Sie diese Anweisungen zum Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure-Ressourcen**.

1. Klicken Sie auf die Ressource, die Sie verwalten möchten, z.B. ein Abonnement oder eine Verwaltungsgruppe.

1. Klicken Sie unter „Verwalten“ auf **Rollen**, um die Liste der Rollen für Azure-Ressourcen anzuzeigen.

    ![Azure-Ressourcenrollen: Rolle auswählen](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Klicken Sie auf die Rolle, die Sie aktualisieren oder entfernen möchten.

1. Suchen Sie die Rollenzuweisung auf den Registerkarten **Berechtigte Rollen** oder **Aktive Rollen**.

    ![Aktualisieren oder Entfernen der Rollenzuweisung](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Klicken Sie auf **Aktualisieren** oder **Entfernen**, um die Rollenzuweisung zu aktualisieren oder zu entfernen.

    Informationen zum Erweitern einer Rollenzuweisung finden Sie unter [Verlängern oder Erneuern von Azure-Ressourcenrollen in PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verlängern oder Erneuern von Azure-Ressourcenrollen in PIM](pim-resource-roles-renew-extend.md)
- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-configure-role-settings.md)
- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
