---
title: Zuweisen eines Benutzers als Administrator eines Azure-Abonnements – Azure RBAC
description: Erfahren Sie, wie Sie einen Benutzer über das Azure-Portal und die rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) als Administrator für ein Azure-Abonnement festlegen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556838"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Zuweisen von Administratorzugriff für ein Azure-Abonnement

Um einen Benutzer zum Administrator eines Azure-Abonnements zu machen, weisen Sie ihm die Rolle [Besitzer](built-in-roles.md#owner) im Abonnementbereich zu. Durch die Rolle „Besitzer“ erhält der Benutzer Vollzugriff auf alle Ressourcen im Abonnement, einschließlich der Berechtigung, anderen Personen den Zugriff zu gewähren. Diese Schritte sind identisch mit allen anderen Rollenzuweisungen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Schritt 1: Öffnen des Abonnements

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im oberen Suchfeld nach Abonnements.

    ![Suche nach Ressourcengruppe im Azure-Portal](./media/shared/sub-portal-search.png)

1. Klicken Sie auf das Abonnement, das Sie verwenden möchten.

    Die folgende Abbildung zeigt ein Beispielabonnement.

    ![Übersicht über Ressourcengruppen](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Schritt 2: Öffnen des Bereichs „Rollenzuweisung hinzufügen“

Die Zuweisung von Rollen zum Gewähren von Zugriff auf Azure-Ressourcen erfolgt in der Regel über die Seite **Zugriffssteuerung (IAM)** . Diese wird auch als Identity & Access Management (IAM) bezeichnet und wird an mehreren Stellen im Azure-Portal angezeigt.

1. Klicken Sie auf **Zugriffssteuerung (IAM)**.

    Die folgende Abbildung zeigt ein Beispiel der Seite „Zugriffssteuerung (IAM)“ für ein Abonnement:

    ![Seite „Zugriffssteuerung (IAM)“ für eine Ressourcengruppe](./media/shared/sub-access-control.png)

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.

1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen**.
   Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „Rollenzuweisung hinzufügen“ deaktiviert.

   ![Menü „Rollenzuweisung hinzufügen“](./media/shared/add-role-assignment-menu.png)

    Der Bereich „Rollenzuweisung hinzufügen“ wird geöffnet.

   ![Bereich „Rollenzuweisung hinzufügen“](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Schritt 3: Auswählen der Rolle „Besitzer“

Über die Rolle [Besitzer](built-in-roles.md#owner) wird Vollzugriff zum Verwalten aller Ressourcen gewährt, einschließlich der Möglichkeit, Rollen in Azure RBAC zuzuweisen. Sie sollten höchstens 3 Abonnementbesitzer haben, um die Möglichkeit einer Sicherheitsverletzung durch einen kompromittierten Besitzer zu verringern.

- Wählen Sie in der Liste **Rolle** die Rolle **Besitzer** aus.

   ![Auswählen der Rolle „Besitzer“ im Bereich „Rollenzuweisung hinzufügen“](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Schritt 4: Auswahl zugriffsberechtigter Benutzer

1. Wählen Sie in der Liste **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder -Dienstprinzipal** aus.

1. Suchen Sie im Abschnitt **Auswählen** nach dem Benutzer, indem Sie eine Zeichenfolge eingeben oder durch die Liste scrollen.

   ![Auswählen von Benutzern unter „Rollenzuweisung hinzufügen“](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Nachdem Sie den Benutzer gefunden haben, wählen Sie ihn durch Mausklick aus.

## <a name="step-5-assign-role"></a>Schritt 5: Zuweisen einer Rolle

1. Klicken Sie auf **Speichern**, um die Rolle zuzuweisen.

   Nach einigen Augenblicken wird dem Benutzer die Rolle für den ausgewählten Bereich zugewiesen.

1. Überprüfen Sie auf der Registerkarte **Rollenzuweisungen**, ob die Rollenzuweisung in der Liste angezeigt wird.

    ![Hinzugefügte Rollenzuweisung gespeichert](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure-Rollen über das Azure-Portal](role-assignments-portal.md)
- [Auflisten von Azure-Rollenzuweisungen über das Azure-Portal](role-assignments-list-portal.md)
- [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)
