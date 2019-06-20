---
title: Konfigurieren von Einstellungen für Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Einstellungen für Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) konfigurieren.
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
ms.openlocfilehash: 73d42c693fae6b538136d1e8c93094a0ea9e2077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60288378"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Konfigurieren von Einstellungen für Azure-Ressourcenrollen in PIM

Wenn Sie Einstellungen von Azure-Ressourcenrollen konfigurieren, definieren Sie die Standardeinstellungen, die auf Zuweisungen von Azure-Ressourcenrollen in Azure Active Directory (Azure AD) Privileged Identity Management (PIM) angewendet werden. Führen Sie die folgenden Schritte aus, um den Genehmigungsworkflow zu konfigurieren und anzugeben, wer Anforderungen genehmigen oder ablehnen kann.

## <a name="open-role-settings"></a>Öffnen von Rolleneinstellungen

Führen Sie diese Schritte aus, um die Einstellungen für eine Azure-Ressourcenrolle zu öffnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Benutzer an, der ein Mitglied der Rolle [Administrator für privilegierte Rollen](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ist.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure-Ressourcen**.

1. Klicken Sie auf die Ressource, die Sie verwalten möchten, z.B. ein Abonnement oder eine Verwaltungsgruppe.

    ![Liste der zu verwaltenden Azure-Ressourcen](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Klicken Sie auf **Rolleneinstellungen**.

    ![Rolleneinstellungen](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Klicken Sie auf die Rolle, deren Einstellungen Sie konfigurieren möchten.

    ![Details zur Rolleneinstellung](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Klicken Sie auf **Bearbeiten**, um den Bereich „Rolleneinstellungen“ zu öffnen.

    ![Bearbeiten von Rolleneinstellungen](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    Im Bereich „Rolleneinstellungen“ für jede Rolle können Sie verschiedene Einstellungen konfigurieren.

## <a name="assignment-duration"></a>Zuweisungsdauer

Bei der Konfiguration von Einstellungen für eine Rolle können Sie für jeden Zuweisungstyp („Berechtigt“ und „Aktiv“) zwischen zwei Optionen für die Zuweisungsdauer wählen. Diese Optionen werden zur maximalen Standarddauer, wenn ein Mitglied der Rolle in PIM zugewiesen wird.

Sie können beim Typ **Berechtigt** eine dieser Optionen für die Zuweisungsdauer wählen:

| | |
| --- | --- |
| **Dauerhafte berechtigte Zuweisung zulassen** | Ressourcenadministratoren können eine dauerhafte berechtigte Mitgliedschaft zuweisen. |
| **Berechtigte Zuweisungen laufen ab nach** | Ressourcenadministratoren können verlangen, dass alle berechtigten Zuweisungen ein bestimmtes Start- und Enddatum haben. |

Beim Typ **Aktiv** können Sie eine dieser Optionen für die Zuweisungsdauer wählen:

| | |
| --- | --- |
| **Permanente aktive Zuweisung zulassen** | Ressourcenadministratoren können eine permanente aktive Mitgliedschaft zuweisen. |
| **Aktive Zuweisungen laufen ab nach** | Ressourcenadministratoren können verlangen, dass alle aktiven Zuweisungen ein bestimmtes Start- und Enddatum haben. |

> [!NOTE] 
> Alle Zuweisungen mit einem angegebenen Enddatum können von Ressourcenadministratoren erneuert werden. Zudem können Mitglieder Self-Service-Anforderungen auslösen, um [Rollenzuweisungen zu verlängern oder zu erneuern](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Mehrstufige Authentifizierung erforderlich

In PIM ist die optionale Erzwingung der Azure Multi-Factor Authentication (MFA) für zwei bestimmte Szenarien möglich.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication bei aktiver Zuweisung erforderlich

Es kann vorkommen, dass Sie ein Mitglied einer Rolle für einen kurzen Zeitraum (z.B. einen Tag) zuweisen möchten. In diesem Fall müssen die zugewiesenen Mitglieder die Aktivierung nicht anfordern. In diesem Szenario kann MFA von PIM nicht erzwungen werden, wenn das Mitglied seine Rollenzuweisung verwendet, da die Rolle ab dem Moment der Zuweisung bereits aktiv ist.

Um sicherzustellen, dass der Ressourcenadministrator, der die Zuweisung bearbeitet, auch wirklich die angegebene Person ist, können Sie für die aktive Zuweisung MFA erzwingen, indem Sie das Kontrollkästchen **Multi-Factor Authentication bei aktiver Zuweisung erforderlich** aktivieren.

### <a name="require-multi-factor-authentication-on-activation"></a>Bei Aktivierung Multi-Factor Authentication anfordern

Sie können erzwingen, dass berechtigte Mitglieder einer Rolle zuerst die MFA durchlaufen müssen, bevor sie aktiviert werden können. Mit diesem Prozess wird sichergestellt, dass es sich bei dem Benutzer, der die Aktivierung anfordert, auch wirklich um den angegebenen Benutzer handelt. Durch die Erzwingung dieser Option werden wichtige Ressourcen in Situationen geschützt, in denen das Benutzerkonto unter Umständen kompromittiert wurde.

Um von einem berechtigten Mitglied die MFA vor der Aktivierung zu verlangen, aktivieren Sie das Kontrollkästchen **Bei Aktivierung Multi-Factor Authentication anfordern**.

Weitere Informationen finden Sie unter [Multi-Factor Authentication (MFA) und PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maximale Aktivierungsdauer

Mit dem Schieberegler **Maximale Aktivierungsdauer** geben Sie die maximale Zeit in Stunden an, die eine Rolle aktiv bleibt, bevor sie abläuft. Möglich ist ein Wert von 1 bis 24 Stunden.

## <a name="require-justification"></a>Verlangen einer Begründung

Sie können verlangen, dass Mitglieder bei aktiver Zuweisung oder bei Aktivierung eine Begründung eingeben. Um eine Begründung zu verlangen, aktivieren Sie das Kontrollkästchen **Begründung für aktive Zuweisung erforderlich** oder **Begründung für Aktivierung erforderlich**.

## <a name="require-approval-to-activate"></a>Erzwingen der Genehmigung für die Aktivierung

Wenn Sie für die Aktivierung einer Rolle eine Genehmigung anfordern möchten, gehen Sie wie folgt vor.

1. Aktivieren Sie das Kontrollkästchen **Genehmigung zum Aktivieren anfordern**.

1. Klicken Sie auf **Genehmigende Personen auswählen**, um den Bereich „Mitglied oder Gruppe auswählen“ zu öffnen.

    ![Mitglied oder Gruppe auswählen](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Wählen Sie mindestens ein Mitglied oder eine Gruppe aus, und klicken Sie dann auf **Auswählen**. Sie können eine beliebige Kombination von Mitgliedern und Gruppen hinzufügen. Sie müssen mindestens eine genehmigende Person auswählen. Für genehmigende Personen gibt es keine Standardeinstellung.

    Ihre Auswahl wird in der Liste der ausgewählten genehmigenden Personen angezeigt.

1. Wenn Sie alle gewünschten Rolleneinstellungen angegeben haben, klicken Sie auf **Aktualisieren**, um Ihre Änderungen zu speichern.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure-Ressourcenrollen in PIM](pim-resource-roles-assign-roles.md)
- [Konfigurieren von Sicherheitswarnungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-configure-alerts.md)
