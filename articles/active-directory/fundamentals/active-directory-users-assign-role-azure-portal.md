---
title: Zuweisen von Verzeichnisrollen zu Benutzern – Azure Active Directory | Microsoft-Dokumentation
description: Anweisungen zum Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd26fa53c91c53893c7f326afda5158fa430be1e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60248136"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory
Wenn für einen Benutzer in Ihrer Organisation die Berechtigung zum Verwalten von Azure AD-Ressourcen (Azure Active Directory) erforderlich ist, müssen Sie dem Benutzer eine geeignete Rolle in Azure AD zuweisen – basierend auf den Aktionen, für deren Durchführung der Benutzer die Berechtigung benötigt.

Weitere Informationen zu den verfügbaren Rollen finden Sie unter [Zuweisen von Administratorrollen in Azure AD](../users-groups-roles/directory-assign-admin-roles.md). Weitere Informationen zum Hinzufügen von Benutzern finden Sie unter [Hinzufügen neuer Benutzer zu Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Zuweisen von Rollen
Eine gängige Möglichkeit zum Zuweisen von Azure AD-Rollen zu einem Benutzer ist die Nutzung der Seite **Verzeichnisrolle** für einen Benutzer.

Sie können Rollen auch zuweisen, indem Sie Privileged Identity Management (PIM) verwenden. Ausführlichere Informationen zur Verwendung von PIM finden Sie unter [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>So weisen Sie einem Benutzer eine Rolle zu
1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Azure Active Directory** und **Benutzer**, und suchen Sie dann nach dem Benutzer, der die Rollenzuweisung erhalten soll, und wählen Sie ihn aus. Beispiel: _Alain Charon_.

3. Wählen Sie auf der Seite **Alain Charon – Profil** die Option **Verzeichnisrolle**.

    Die Seite **Alain Charon – Verzeichnisrolle** wird angezeigt.

4. Wählen Sie **Rolle hinzufügen** und dann die Rolle aus, die Alain Charon zugewiesen werden soll (z.B. _Anwendungsadministrator_). Wählen Sie anschließend die Option **Auswählen**.

    ![Seite „Verzeichnisrollen“ mit der ausgewählten Rolle](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Die Rolle „Anwendungsadministrator“ wird Alain Charon zugewiesen und auf der Seite **Alain Charon – Verzeichnisrolle** angezeigt.

## <a name="remove-a-role-assignment"></a>Entfernen einer Rollenzuweisung
Wenn Sie die Rollenzuweisung für einen Benutzer entfernen möchten, können Sie ebenfalls die Seite **Alain Charon – Verzeichnisrolle** nutzen.

### <a name="to-remove-a-role-assignment-from-a-user"></a>So entfernen Sie eine Rollenzuweisung für einen Benutzer

1. Wählen Sie **Azure Active Directory** und **Benutzer**, und suchen Sie dann nach dem Benutzer, für den die Rollenzuweisung entfernt werden soll, und wählen Sie ihn aus. Beispiel: _Alain Charon_.

2. Wählen Sie **Verzeichnisrolle**, **Anwendungsadministrator** und dann **Rolle entfernen**.

    ![Seite „Verzeichnisrollen“ mit der ausgewählten Rolle und der Option zum Entfernen](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Die Rolle „Anwendungsadministrator“ wird für Alain Charon entfernt und auf der Seite **Alain Charon – Verzeichnisrolle** nicht mehr angezeigt.

## <a name="next-steps"></a>Nächste Schritte
- [Hinzufügen oder Löschen von Benutzern](add-users-azure-active-directory.md)

- [Hinzufügen oder Ändern von Profilinformationen](active-directory-users-profile-azure-portal.md)

- [Hinzufügen von Gastbenutzern aus einem anderen Verzeichnis](../b2b/what-is-b2b.md)

Sie können auch andere Aufgaben für die Benutzerverwaltung ausführen, z.B. Delegate zuweisen, Richtlinien verwenden und Benutzerkonten freigeben. Weitere Informationen zu anderen verfügbaren Aktionen finden Sie unter [Dokumentation zur Azure Active Directory-Benutzerverwaltung](../users-groups-roles/index.yml).


