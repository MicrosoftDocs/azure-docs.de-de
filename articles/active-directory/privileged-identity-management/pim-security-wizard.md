---
title: Sicherheitsassistent für Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Beschreibt den Sicherheitsassistenten, mit dem Sie mit Azure AD Privileged Identity Management (PIM) permanent privilegierte Azure AD-Rollenzuweisungen in berechtigte Rollen konvertieren können.
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
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6f978612cbbf0c326c3e66f25a0fbf4b749cc73
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60286933"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Sicherheitsassistent für Azure AD-Rollen in PIM

Wenn Sie die erste Person sind, die Azure Active Directory (Azure AD) Privileged Identity Management (PIM) für Ihre Organisation ausführt, wird Ihnen ein Assistent angezeigt. Der Assistent bietet Einblick in die Sicherheitsrisiken privilegierter Identitäten und informiert darüber, wie Sie diese Risiken mithilfe von PIM reduzieren. Sie müssen die vorhandenen Rollenzuweisungen im Assistenten nicht ändern, wenn Sie dies lieber später machen möchten.

## <a name="wizard-overview"></a>Übersicht über den Assistenten

Bevor Ihre Organisation mit der Verwendung von PIM beginnt, sind alle Rollenzuweisungen permanent, d. h. die Benutzer besitzen diese Rollen ständig, auch wenn sie die zugehörigen Berechtigungen derzeit nicht benötigen. Der erste Schritt des Assistenten bietet eine Liste der Rollen mit hohen Berechtigungen und zeigt an, wie viele Benutzer derzeit in diesen Rollen vorhanden sind. Sie können Details zu einer bestimmten Rolle anzeigen, um weitere Informationen zu den Benutzern zu erhalten und zu ermitteln, ob ein oder mehrere Benutzer unbekannt sind.

Im zweiten Schritt des Assistenten haben Sie die Möglichkeit, die Rollenzuweisungen des Administrators zu ändern.  

> [!WARNING]
> Es müssen unbedingt mindestens ein globaler Administrator sowie mehrere Administratoren für privilegierte Rollen mit einem Organisationskonto (keinem Microsoft-Konto) vorhanden sein. Wenn nur ein Administrator für privilegierte Rollen vorhanden ist, kann die Organisation PIM nicht mehr verwalten, sollte dieses Konto gelöscht werden.
> Verwenden Sie außerdem permanente Rollenzuweisungen, wenn ein Benutzer über ein Microsoft-Konto verfügt (ein Konto zum Anmelden bei Microsoft-Diensten wie Skype und Outlook.com). Wenn Sie planen, zur Aktivierung dieser Rolle MFA als erforderlich festzulegen, kann der Benutzer sie nicht übernehmen.

## <a name="run-the-wizard"></a>Ausführen des Assistenten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure AD-Rollen** und dann auf **Assistenten**.

    ![Azure AD-Rollen – Assistent](./media/pim-security-wizard/wizard-start.png)

1. Klicken Sie auf **1 Privilegierte Rollen ermitteln**.

1. Überprüfen Sie die Liste der Rollen, um festzustellen, welche Benutzer permanente oder berechtigte Zuweisungen haben.

    ![Ermitteln von Benutzern mit privilegierten Rollen](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Klicken Sie auf **Weiter**, um die Mitglieder auszuwählen, denen Sie in berechtige Mitglieder umwandeln möchten.

    ![Mitglieder in berechtigte Mitglieder konvertieren](./media/pim-security-wizard/convert-members-eligible.png)

1. Klicken Sie nach dem Auswählen der Mitglieder auf **Weiter**.

    ![Änderungen überprüfen](./media/pim-security-wizard/review-changes.png)

1. Klicken Sie auf **OK**, um die permanenten in berechtigte Zuweisungen zu konvertieren.

    Nach Abschluss der Konvertierung wird eine Benachrichtigung angezeigt.

    ![Benachrichtigungen](./media/pim-security-wizard/notification-completion.png)

Wenn Sie weitere privilegierte Rollenzuweisungen in berechtigte Zuweisungen konvertieren müssen, führen Sie den Assistenten erneut aus. Wenn Sie die PIM-Schnittstelle anstelle des Assistenten verwenden möchten, lesen Sie [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
- [Gewähren von Zugriff für andere Administratoren zum Verwalten von PIM](pim-how-to-give-access-to-pim.md)
