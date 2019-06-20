---
title: Gewähren von Zugriff für andere Administratoren zum Verwalten von PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie anderen Administratoren Zugriff zum Verwalten von Azure AD Privileged Identity Management (PIM) gewähren.
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
ms.openlocfilehash: bb22e3cc93baebac023c0148812c6a4c6c95be60
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60288616"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Gewähren von Zugriff für andere Administratoren zum Verwalten von PIM

Der globale Administrator, der Azure Active Directory (Azure AD) Privileged Identity Management (PIM) für eine Organisation aktiviert, erhält automatisch Rollenzuweisungen und Zugriff auf PIM. Andere Benutzer dagegen erhalten nicht standardmäßig Schreibzugriff, auch nicht die anderen globalen Administratoren. Andere globale Administratoren, Sicherheitsadministratoren und Sicherheitsleseberechtigte erhalten schreibgeschützten Zugriff auf PIM. Um Zugriff auf PIM zu gewähren, kann der erste Benutzer anderen die Rolle **Administrator für privilegierte Rollen** zuweisen.

> [!NOTE]
> Für die Verwaltung von PIM ist Azure MFA erforderlich. Da Microsoft-Konten nicht für Azure MFA registriert werden können, kann ein Benutzer, der sich mit einem Microsoft-Konto anmeldet, nicht auf PIM zugreifen.

Stellen Sie sicher, dass immer mindestens zwei Benutzer mit der Rolle „Administrator für privilegierte Rollen“ vorhanden sind, falls ein Benutzer gesperrt oder sein Konto gelöscht wird.

## <a name="grant-access-to-manage-pim"></a>Gewähren des Zugriffs zur Verwaltung von PIM

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Rollen**.

    ![PIM: Azure AD-Rollen – Rollen](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Klicken Sie auf die Rolle **Administrator für privilegierte Rollen**, um die Seite „Mitglieder“ zu öffnen.

    ![Administrator für privilegierte Rollen: Mitglieder](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Klicken Sie auf **Mitglied hinzufügen**, um den Bereich „Verwaltete Mitglieder hinzufügen“ zu öffnen.

1. Klicken Sie auf **Mitglieder auswählen**, um den Bereich „Mitglieder auswählen“ zu öffnen.

    ![Administrator für privilegierte Rollen: Mitglieder auswählen](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Wählen Sie ein Mitglied aus, und klicken Sie dann auf **Auswählen**.

1. Klicken Sie auf **OK**, damit das Mitglied für die Rolle **Administrator für privilegierte Rollen** berechtigt ist.

    Wenn Sie einem Benutzer in PIM eine neue Rolle zuweisen, wird er automatisch als **Berechtigt** zum Aktivieren der Rolle konfiguriert.

1. Um das Mitglied dauerhaft zu machen, klicken Sie in der Mitgliederliste „Administrator für privilegierte Rollen“ auf den Benutzer.

1. Klicken Sie auf **Mehr** und dann auf **Als permanent festlegen**, um die Zuweisung dauerhaft einzurichten.

    ![Administrator für privilegierte Rollen: Als permanent festlegen](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Senden Sie dem Benutzer einen Link für den [Einstieg in PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Aufheben des Zugriffs zur Verwaltung von PIM

Bevor Sie einen Benutzer von der Rolle „Administrator für privilegierte Rollen“ entfernen, stellen Sie sicher, dass dieser Rolle danach noch mindestens zwei Benutzer zugewiesen sind.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Rollen**.

1. Klicken Sie auf die Rolle **Administrator für privilegierte Rollen**, um die Seite „Mitglieder“ zu öffnen.

1. Fügen Sie ein Häkchen neben dem Benutzer hinzu, den Sie entfernen möchten, und klicken Sie dann auf **Mitglied entfernen**.

    ![Administrator für privilegierte Rollen: Mitglied entfernen](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Klicken Sie in der Meldung, die angezeigt wird und Sie fragt, ob Sie das Mitglied von der Rolle entfernen möchten, auf **Ja**.

## <a name="next-steps"></a>Nächste Schritte

- [Einstieg in die Verwendung von PIM](pim-getting-started.md)
