---
title: Gewähren von Zugriff zum Verwalten von PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie anderen Administratoren Zugriff zum Verwalten von Azure AD Privileged Identity Management (PIM) gewähren.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835202"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Delegieren des Zugriffs auf Privileged Identity Management

Um Zugriff auf Privileged Identity Management (PIM) zu gewähren, kann ein globaler Administrator anderen Benutzern die Rolle „Administrator für privilegierte Rollen“ zuweisen. Sicherheitsadministratoren und Sicherheitsleseberechtigte erhalten standardmäßig schreibgeschützten Zugriff auf Privileged Identity Management. Um Zugriff auf Privileged Identity Management zu gewähren, kann der erste Benutzer anderen die Rolle **Administrator für privilegierte Rollen** zuweisen. Die Rolle „Administrator für privilegierte Rolle“ ist nur für die Verwaltung von Azure AD-Rollen erforderlich. Berechtigungen vom Typ „Administrator für privilegierte Rolle“ sind zum Verwalten der Einstellungen für Azure-Ressourcen nicht erforderlich.

> [!NOTE]
> Für das Verwalten von Privileged Identity Management ist Azure AD Multi-Factor Authentication erforderlich. Da Microsoft-Konten nicht für Azure AD Multi-Factor Authentication registriert werden können, kann ein Benutzer, der sich mit einem Microsoft-Konto anmeldet, nicht auf Privileged Identity Management zugreifen.

Stellen Sie sicher, dass immer mindestens zwei Benutzer mit der Rolle „Administrator für privilegierte Rollen“ vorhanden sind, falls ein Benutzer gesperrt oder sein Konto gelöscht wird.

## <a name="delegate-access-to-manage-pim"></a>Delegieren des Zugriffs zur Verwaltung von PIM

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie in Azure AD **Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Rollen** aus.

    ![„Azure AD-Rollen“ in Privileged Identity Management – Rollen](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Wählen Sie die Rolle **Administrator für privilegierte Rollen** aus, um die Seite „Mitglieder“ zu öffnen.

    ![Administrator für privilegierte Rollen: Mitglieder](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Wählen Sie **Mitglied hinzufügen** aus, um den Bereich **Verwaltete Mitglieder hinzufügen** zu öffnen.

1. Wählen Sie **Mitglieder auswählen** aus, um den Bereich **Mitglieder auswählen** zu öffnen.

    ![Administrator für privilegierte Rollen: Mitglieder auswählen](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Wählen Sie ein Mitglied aus, und klicken Sie dann auf **Auswählen**.

1. Klicken Sie auf **OK**, damit das Mitglied für die Rolle **Administrator für privilegierte Rollen** berechtigt ist.

    Wenn Sie einem Benutzer in Privileged Identity Management eine neue Rolle zuweisen, wird er automatisch als **Berechtigt** zum Aktivieren der Rolle konfiguriert.

1. Um das Mitglied dauerhaft zu machen, wählen Sie in der Mitgliederliste „Administrator für privilegierte Rollen“ den Benutzer aus.

1. Wählen Sie **Mehr** und dann auf **Als permanent festlegen** aus, um die Zuweisung dauerhaft einzurichten.

    ![Administrator für privilegierte Rollen: Als permanent festlegen](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Senden Sie an den Benutzer einen Link zu [Einstieg in Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Aufheben des Zugriffs zur Verwaltung von PIM

Bevor Sie einen Benutzer von der Rolle „Administrator für privilegierte Rollen“ entfernen, stellen Sie sicher, dass dieser Rolle danach noch mindestens zwei Benutzer zugewiesen sind.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Rollen** aus.

1. Wählen Sie die Rolle **Administrator für privilegierte Rollen** aus, um die Seite „Mitglieder“ zu öffnen.

1. Aktivieren Sie das Kontrollkästchen neben dem Benutzer, den Sie entfernen möchten, und wählen Sie dann **Mitglied entfernen** aus.

    ![Administrator für privilegierte Rollen: Mitglied entfernen](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Wenn Sie aufgefordert werden zu bestätigen, dass Sie das Mitglied aus der Rolle entfernen möchten, wählen Sie **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte

- [Einstieg in Privileged Identity Management](pim-getting-started.md)
