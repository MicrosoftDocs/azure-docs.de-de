---
title: Zuweisen von Azure-Ressourcenrollen in Privileged Identity Management – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) zuweisen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 011b50449f0a02b128bc000535f93e1809fd9777
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673873"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Zuweisen von Azure-Ressourcenrollen in Privileged Identity Management

Azure Active Directory Privileged Identity Management (Azure AD PIM) kann sowohl die integrierten Azure-Ressourcenrollen als auch benutzerdefinierte Rollen verwalten. Beispiele:

- Besitzer
- Benutzerzugriffsadministrator
- Mitwirkender
- Sicherheitsadministrator
- Sicherheits-Manager

> [!NOTE]
> Benutzer oder Mitglieder von Gruppen, die der Rolle „Besitzer“ oder „Benutzerzugriffsadministrator“ für das Abonnement zugewiesen sind, sowie globale Azure AD-Administratoren, die die Abonnementverwaltung in Azure AD ermöglichen, haben standardmäßig Ressourcenadministratorberechtigungen. Diese Administratoren können Rollen zuweisen, Rolleneinstellungen konfigurieren und den Zugriff mithilfe von Privileged Identity Management für Azure-Ressourcen überprüfen. Ohne Ressourcenadministratorberechtigung kann ein Benutzer in Privileged Identity Management keine Ressourcen verwalten. Zeigen Sie die Liste der [integrierten Azure-Rollen](../../role-based-access-control/built-in-roles.md) an.

## <a name="assign-a-role"></a>Zuweisen einer Rolle

Führen Sie folgende Schritte aus, um einen Benutzer für eine Azure AD-Ressourcenrolle als „berechtigt“ festzulegen.

1. Melden Sie sich mit den Rollenberechtigungen „Besitzer“ oder „Benutzerzugriffsadministrator“ beim [Azure-Portal](https://portal.azure.com/) an.

    Informationen dazu, wie Sie anderen Administratoren Zugriff zum Verwalten von Privileged Identity Management gewähren, finden Sie unter [Gewähren von Zugriff für andere Administratoren zum Verwalten von PIM](pim-how-to-give-access-to-pim.md).

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Verwenden Sie den Ressourcenfilter, um nach den gewünschten verwalteten Ressourcen zu suchen.

    ![Liste der zu verwaltenden Azure-Ressourcen](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Wählen Sie die zu verwaltende Ressource aus, um die Übersichtsseite der Ressource zu öffnen.

1. Wählen Sie unter **Verwalten** den Eintrag **Rollen** aus, um die Liste der Rollen für Azure-Ressourcen anzuzeigen.

    ![Azure-Ressourcenrollen](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Wählen Sie **Zuweisungen hinzufügen** aus, um den Bereich **Zuweisungen hinzufügen** zu öffnen.

1. Wählen Sie **Rolle auswählen** aus, um die Seite **Rolle auswählen** zu öffnen.

    ![Bereich „Neue Zuweisung“](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Wählen Sie die Rolle aus, die Sie zuweisen möchten, und klicken Sie dann auf **Auswählen**.

    Der Bereich **Mitglied oder Gruppe auswählen** wird geöffnet.

1. Wählen Sie zuerst ein Mitglied oder eine Gruppe, dem bzw. der die Rolle zugewiesen werden soll, und dann **Auswählen** aus.

    ![Bereich „Mitglied oder Gruppe auswählen“](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. Wählen Sie auf der Registerkarte **Einstellungen** in der Liste **Zuweisungstyp** entweder **Berechtigt** oder **Aktiv** aus.

    ![Bereich „Mitgliedschaftseinstellungen“](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management für Azure-Ressourcen verfügt über zwei unterschiedliche Zuweisungstypen:

    - Für **berechtigte** Zuweisungen muss das Mitglied der Rolle eine Aktion durchführen, um die Rolle verwenden zu können. Beispiele für Aktionen sind eine erfolgreiche Multi-Factor Authentication-Überprüfung (MFA), die Angabe einer geschäftlichen Begründung oder das Anfordern einer Genehmigung von den angegebenen genehmigenden Personen.

    - Für **aktive** Zuweisungen ist es nicht erforderlich, dass das Mitglied eine Aktion durchführt, um die Rolle nutzen zu können. Für Mitglieder, die als „Aktiv“ zugewiesen sind, sind die Berechtigungen immer der Rolle zugewiesen.

1. Wenn Sie eine bestimmte Zuweisungsdauer angeben möchten, ändern Sie das Start- und Enddatum sowie die Zeitfelder.

1. Klicken Sie abschließend auf **Zuweisen**.

1. Nachdem die neue Rollenzuweisung erstellt wurde, wird eine Statusbenachrichtigung angezeigt.

    ![Neue Zuweisung: Benachrichtigung](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung

Befolgen Sie diese Anweisungen zum Aktualisieren oder Entfernen einer vorhandenen Rollenzuweisung.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Wählen Sie die zu verwaltende Ressource aus, um die Übersichtsseite zu öffnen.

1. Wählen Sie unter **Verwalten** den Eintrag **Rollen** aus, um die Liste der Rollen für Azure-Ressourcen anzuzeigen.

    ![Azure-Ressourcenrollen: Rolle auswählen](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Wählen Sie die Rolle aus, die Sie aktualisieren oder entfernen möchten.

1. Suchen Sie die Rollenzuweisung auf den Registerkarten **Berechtigte Rollen** oder **Aktive Rollen**.

    ![Aktualisieren oder Entfernen der Rollenzuweisung](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Wählen Sie **Aktualisieren** oder **Entfernen** aus, um die Rollenzuweisung zu aktualisieren oder zu entfernen.

    Informationen zum Erweitern einer Rollenzuweisung finden Sie unter [Verlängern oder Erneuern von Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verlängern oder Erneuern von Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Konfigurieren von Einstellungen für Azure-Ressourcenrollen in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
