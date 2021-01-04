---
title: Ändern von Lizenzplänen für Benutzer und Gruppen – Azure AD | Microsoft-Dokumentation
description: Migrieren von Benutzern in einer Gruppe zu verschiedenen Dienstplänen mithilfe der Gruppenlizenzierung in Azure Active Directory
services: active-directory
keywords: Azure AD-Lizenzierung
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050ae95c79e7ecb98f8508c2fdb41b90fc1b1da0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546538"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Ändern der Lizenzzuweisungen für einen Benutzer oder eine Gruppe in Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie Benutzer und Gruppen zwischen Dienstlizenzplänen in Azure Active Directory (Azure AD) verschieben. Das Ziel des Ansatzes von Azure AD ist, sicherzustellen, dass es während der Lizenzänderung nicht zu Dienstausfällen oder Datenverlusten kommt. Benutzer sollen nahtlos zwischen den Diensten wechseln können. In den Schritten für die Lizenzplanzuweisung in diesem Artikel wird beschrieben, wie Sie einen Benutzer oder eine Gruppe von Office 365 E1 in Office 365 E3 wechseln, aber die Schritte gelten für alle Lizenzpläne. Wenn Sie Lizenzzuweisungen für einen Benutzer oder eine Gruppe aktualisieren, erfolgen das Entfernen von Lizenzzuweisungen und das Hinzufügen der neuen Zuweisungen gleichzeitig, sodass Benutzer während der Lizenzänderungen den Zugriff auf ihre Dienste nicht verlieren und keine Lizenzkonflikte zwischen Plänen auftreten.

## <a name="before-you-begin"></a>Voraussetzungen

Vor dem Aktualisieren der Lizenzzuweisungen ist es wichtig, sicherzustellen, dass bestimmte Annahmen für die zu aktualisierenden Benutzer oder Gruppen zutreffen. Wenn die Annahmen nicht für alle Benutzer in der Gruppe zutreffen, kann die Migration für einige davon fehlschlagen. Infolgedessen könnten einige der Benutzer den Zugriff auf Dienste oder Daten verlieren. Stellen Sie Folgendes sicher:

- Benutzer verfügen über den aktuellen Lizenzplan (in diesem Fall „Office 365 E1“), der einer Gruppe zugewiesen und vom Benutzer geerbt und nicht direkt zugewiesen wurde.

- Sie verfügen über ausreichend verfügbare Lizenzen für den Lizenzplan, den Sie zuweisen. Wenn Sie nicht über genügend Lizenzen verfügen, wird einigen Benutzer der neue Lizenzplan möglicherweise nicht zugewiesen. Sie können die Anzahl der verfügbaren Lizenzen überprüfen.

- Benutzern sind keine anderen Dienstlizenzen zugewiesen, die u.U. mit der gewünschten Lizenz in Konflikt stehen oder die Entfernung der aktuellen Lizenz verhindern. Beispiel: Eine Lizenz für einen Dienst wie Workplace Analytics oder Project Online, die von einem anderen Dienst abhängig ist.

- Wenn Sie Gruppen lokal verwalten und über Azure AD Connect in Azure AD synchronisieren, erfolgt das Hinzufügen oder Entfernen von Benutzern in Ihrem lokalen System. Es kann einige Zeit dauern, bis die mit Azure AD zu synchronisierenden Änderungen von der Gruppenlizenzierung übernommen werden.

- Wenn Sie dynamische Azure AD-Gruppenmitgliedschaften verwenden, können Sie Benutzer hinzufügen oder entfernen, indem Sie deren Attribute ändern, aber der Updatevorgang für die Lizenzzuweisungen bleibt unverändert.

## <a name="change-user-license-assignments"></a>Ändern der Benutzerlizenzzuweisungen

Wenn Sie auf der Seite **Lizenzzuweisungen aktualisieren** feststellen, dass einige Kontrollkästchen nicht verfügbar sind, weist dies auf Dienste hin, die nicht geändert werden können, da Sie von einer Gruppenlizenz geerbt werden.

1. Melden Sie sich mit dem Konto eines Lizenzadministrators Ihrer Azure AD-Organisation beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie **Azure Active Directory** > **Benutzer** aus, und öffnen Sie dann die Seite **Profil** für einen Benutzer.
1. Wählen Sie **Lizenzen** aus.
1. Wählen Sie **Zuweisungen** aus, um die Lizenzzuweisung für den Benutzer oder die Gruppe zu bearbeiten. Auf der Seite **Zuweisungen** können Sie Lizenzzuweisungskonflikte auflösen.
1. Aktivieren Sie das Kontrollkästchen für Office 365 E3, und stellen Sie sicher, dass mindestens alle dem Benutzer zugewiesenen E1-Dienste ausgewählt sind.
1. Deaktivieren Sie das Kontrollkästchen für „Office 365 E1“.

    ![Seite „Lizenzzuweisungen“ für einen Benutzer, mit „Office 365 E1“ deaktiviert und „Office 365 E3“ aktiviert](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Wählen Sie **Speichern** aus.

Azure AD entfernt die alten Lizenzen und wendet gleichzeitig die neuen Lizenzen an, um die Dienstkontinuität zu gewährleisten.

## <a name="change-group-license-assignments"></a>Ändern der Gruppenlizenzzuweisungen

1. Melden Sie sich mit dem Konto eines Lizenzadministrators Ihrer Azure AD-Organisation beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie **Azure Active Directory** > **Gruppen** aus, und öffnen Sie dann die Seite **Übersicht** für eine Gruppe.
1. Wählen Sie **Lizenzen** aus.
1. Wählen Sie den Befehl **Zuweisungen** aus, um die Lizenzzuweisung für den Benutzer oder die Gruppe zu bearbeiten.
1. Aktivieren Sie das Kontrollkästchen für Office 365 E3. Um die Kontinuität des Diensts aufrechtzuerhalten, stellen Sie sicher, dass Sie alle E1-Dienste aktivieren, die dem Benutzer bereits zugewiesen sind.
1. Deaktivieren Sie das Kontrollkästchen für „Office 365 E1“.

    ![Wählen Sie den Befehl „Zuweisungen“ auf einer Benutzer- oder Gruppenlizenzseite aus.](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Wählen Sie **Speichern** aus.

Um die Dienstkontinuität zu gewährleisten, entfernt Azure AD die alten Lizenzen und wendet gleichzeitig die neuen Lizenzen auf alle Benutzer in der Gruppe an.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Szenarien für die Lizenzverwaltung über Gruppen finden Sie in den folgenden Artikeln:

- [Zuweisen von Lizenzen zu einer Gruppe in Azure Active Directory](licensing-groups-assign.md)
- [Bestimmen und Beheben von Lizenzproblemen für eine Gruppe in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Migrieren einzelner lizenzierter Benutzer zur Gruppenlizenzierung in Azure Active Directory](licensing-groups-migrate-users.md)
- [Azure Active Directory-Gruppenlizenzierung – zusätzliche Szenarien](licensing-group-advanced.md)
- [PowerShell-Beispiele für die Gruppenlizenzierung in Azure Active Directory](licensing-ps-examples.md)
