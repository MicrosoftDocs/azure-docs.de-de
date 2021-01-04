---
title: Auflisten von Azure-Rollenzuweisungen mithilfe des Azure-Portals – Azure RBAC
description: Erfahren Sie, wie Sie mithilfe des Azure-Portals und Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung) ermitteln können, auf welche Ressourcen Benutzer, Gruppen, Dienstprinzipale und verwaltete Identitäten Zugriff haben.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/16/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a5b019068368f1c0c076f5a1ed157c288bb7169c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479917"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Auflisten von Azure-Rollenzuweisungen über das Azure-Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] In diesem Artikel wird beschrieben, wie Sie mit dem Azure-Portal Rollenzuweisungen auflisten.

> [!NOTE]
> Wenn Ihre Organisation Verwaltungsfunktionen an einen Dienstanbieter ausgelagert hat, der die [delegierte Azure-Ressourcenverwaltung](../lighthouse/concepts/azure-delegated-resource-management.md) verwendet, werden die von diesem Dienstanbieter autorisierten Rollenzuweisungen hier nicht angezeigt.

## <a name="list-role-assignments-for-a-user-or-group"></a>Auflisten von Rollenzuweisungen für einen Benutzer oder eine Gruppe

Die einfachste Möglichkeit, die einem Benutzer oder einer Gruppe in einem Abonnement zugewiesenen Rollen anzuzeigen, ist die Verwendung des Bereichs **Azure-Rollenzuweisungen** .

1. Klicken Sie im Menü des Azure-Portals auf **Alle Dienste** .

1. Klicken Sie anschließend auf **Azure Active Directory** und dann auf **Benutzer** oder **Gruppen** .

1. Klicken Sie auf den Benutzer oder die Gruppe, für den bzw. die Sie Rollenzuweisungen auflisten möchten.

1. Klicken Sie auf **Azure-Rollenzuweisungen** .

    Es wird eine Liste der Rollen angezeigt, die dem ausgewählten Benutzer bzw. der ausgewählten Gruppe in verschiedenen Bereichen (etwa Verwaltungsgruppe, Abonnement, Ressourcengruppe oder Ressource) zugewiesen sind. Diese Liste enthält alle Rollenzuweisungen, für die Sie über Leseberechtigungen verfügen.

    ![Rollenzuweisungen für einen Benutzer](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Um das Abonnement zu ändern, klicken Sie auf die Liste **Abonnements** .

## <a name="list-owners-of-a-subscription"></a>Auflisten der Besitzer eines Abonnements

Benutzer, denen die Rolle [Besitzer](built-in-roles.md#owner) für ein Abonnement zugewiesen wurde, können alles in dem Abonnement verwalten. Befolgen Sie diese Schritte, um die Besitzer eines Abonnements aufzulisten.

1. Klicken Sie im Azure-Portal auf **Alle Dienste** und anschließend auf **Abonnements** .

1. Klicken Sie auf das Abonnement, dessen Besitzer Sie auflisten möchten.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen** , um alle Rollenzuweisungen für dieses Abonnement anzuzeigen.

1. Blättern Sie zum Abschnitt **Besitzer** , um alle Benutzer anzuzeigen, denen die Rolle des Besitzers für dieses Abonnement zugewiesen wurde.

   ![Zugriffssteuerung für Abonnements: Registerkarte „Rollenzuweisungen“](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Auflisten der Rollenzuweisungen für einen Bereich

1. Klicken Sie im Azure-Portal auf **Alle Dienste** , und wählen Sie anschließend den Bereich aus. Sie können beispielsweise **Verwaltungsgruppen** , **Abonnements** , **Ressourcengruppen** oder eine Ressource auswählen.

1. Klicken Sie auf die gewünschte Ressource.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen** , um alle Rollenzuweisungen für diesen Bereich anzuzeigen.

   ![Zugriffssteuerung: Registerkarte „Rollenzuweisungen“](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Auf der Registerkarte „Rollenzuweisungen“ können Sie sehen, wer Zugriff auf diesen Bereich hat. Beachten Sie, dass einige Rollen auf **Diese Ressource** begrenzt sind, während andere von einem anderen Bereich **geerbt** werden. Der Zugriff wird entweder speziell dieser Ressource zugewiesen oder von einer Zuweisung des übergeordneten Bereichs geerbt.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Auflisten von Rollenzuweisungen für einen Benutzer für einen Bereich

Um den Zugriff für einen Benutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität aufzulisten, listen Sie die Rollenzuweisungen auf. Führen Sie diese Schritte aus, um die Rollenzuweisungen für einen einzelnen Benutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität für einen bestimmten Bereich aufzulisten.

1. Klicken Sie im Azure-Portal auf **Alle Dienste** , und wählen Sie anschließend den Bereich aus. Sie können beispielsweise **Verwaltungsgruppen** , **Abonnements** , **Ressourcengruppen** oder eine Ressource auswählen.

1. Klicken Sie auf die gewünschte Ressource.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Zugriff überprüfen** .

    ![Zugriffssteuerung: Registerkarte „Zugriff überprüfen“](./media/role-assignments-list-portal/access-control-check-access.png)

1. Wählen Sie in der Liste **Finden** den Typ des Sicherheitsprinzipals aus, für den Sie den Zugriff überprüfen möchten.

1. Geben Sie in das Suchfeld eine Zeichenfolge ein, nach der das Verzeichnis durchsucht werden soll. Sie können nach Anzeigenamen, E-Mail-Adressen oder Objektbezeichnern suchen.

    ![Auswahlliste für Zugriffsüberprüfung](./media/role-assignments-list-portal/check-access-select.png)

1. Klicken Sie auf den Sicherheitsprinzipal, um den Bereich **Zuweisungen** zu öffnen.

    ![Bereich „Zuweisungen“](./media/role-assignments-list-portal/check-access-assignments.png)

    In diesem Bereich werden die Rollen angezeigt, die dem ausgewählten Sicherheitsprinzipal für den Bereich zugewiesen sind. Wenn Ablehnungszuweisungen für diesen Bereich oder an diesen Bereich vererbte Ablehnungszuweisungen vorhanden sind, werden diese angezeigt.

## <a name="list-role-assignments-for-a-managed-identity"></a>Auflisten von Rollenzuweisungen für eine verwaltete Identität

Sie können Rollenzuweisungen für vom System zugewiesene und vom Benutzer zugewiesene verwaltete Identitäten in einem bestimmten Bereich mithilfe des zuvor beschriebenen Blatts für die **Zugriffssteuerung (IAM)** auflisten. In diesem Abschnitt wird beschrieben, wie Rollenzuweisungen nur für die verwaltete Identität aufgelistet werden.

### <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

1. Öffnen Sie im Azure-Portal eine systemseitig zugewiesene verwaltete Identität.

1. Klicken Sie im linken Menü auf **Identität** .

    ![Systemseitig zugewiesene verwaltete Identität](./media/shared/identity-system-assigned.png)

1. Klicken Sie unter **Berechtigungen** auf **Azure-Rollenzuweisungen** .

    Es wird eine Liste der Rollen angezeigt, die der ausgewählten systemseitig zugewiesenen verwalteten Identität in verschiedenen Bereichen (etwa Verwaltungsgruppe, Abonnement, Ressourcengruppe oder Ressource) zugewiesen sind. Diese Liste enthält alle Rollenzuweisungen, für die Sie über Leseberechtigungen verfügen.

    ![Rollenzuweisungen für eine systemseitig zugewiesene verwaltete Identität](./media/shared/role-assignments-system-assigned.png)

1. Um das Abonnement zu ändern, klicken Sie auf die Liste **Abonnement** .

### <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

1. Öffnen Sie im Azure-Portal eine benutzerseitig zugewiesene verwaltete Identität.

1. Klicken Sie auf **Azure-Rollenzuweisungen** .

    Es wird eine Liste der Rollen angezeigt, die der ausgewählten benutzerseitig zugewiesenen verwalteten Identität in verschiedenen Bereichen (etwa Verwaltungsgruppe, Abonnement, Ressourcengruppe oder Ressource) zugewiesen sind. Diese Liste enthält alle Rollenzuweisungen, für die Sie über Leseberechtigungen verfügen.

    ![Screenshot, der Rollenzuweisungen für eine benutzerseitig zugewiesene verwaltete Identität zeigt.](./media/shared/role-assignments-user-assigned.png)

1. Um das Abonnement zu ändern, klicken Sie auf die Liste **Abonnement** .

## <a name="list-number-of-role-assignments"></a>Auflisten der Anzahl von Rollenzuweisungen

Sie können in jedem Abonnement bis zu **2000**  Rollenzuweisungen haben. Dieser Grenzwert schließt Rollenzuweisungen im Abonnement, in der Ressourcengruppe und im Ressourcenbereich ein. Damit Sie diesen Grenzwert nachverfolgen können, enthält die Registerkarte **Rollenzuweisungen** ein Diagramm, in dem die Anzahl der Rollenzuweisungen für das aktuelle Abonnement aufgeführt ist.

![Zugriffssteuerung: Diagramm „Anzahl der Rollenzuweisungen“](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Wenn Sie sich der maximalen Anzahl nähern und versuchen, weitere Rollenzuweisungen hinzuzufügen, wird im Bereich **Rollenzuweisung hinzufügen** eine Warnung angezeigt. Möglichkeiten, wie Sie die Anzahl der Rollenzuweisungen reduzieren können, finden Sie unter [Problembehandlung von Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Zugriffssteuerung: Warnung „Rollenzuweisung hinzufügen“](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>Herunterladen von Rollenzuweisungen

Sie können Rollenzuweisungen in einem Bereich im CSV-oder JSON-Format herunterladen. Dies kann hilfreich sein, wenn Sie die Liste in einer Kalkulationstabelle überprüfen oder bei der Migration eines Abonnements eine Inventur durchführen müssen.

Beachten Sie beim Herunterladen von Rollenzuweisungen unbedingt die folgenden Kriterien:

- Wenn Sie nicht über die Berechtigungen zum Lesen des Verzeichnisses verfügen, z. B. die Rolle „Verzeichnisleseberechtigte“, sind die Spalten DisplayName, SignInName und ObjectType leer.
- Rollenzuweisungen, deren Sicherheitsprinzipal gelöscht wurde, sind nicht eingeschlossen.
- Klassischen Administratoren gewährter Zugriff ist nicht eingeschlossen.

Führen Sie die folgenden Schritte aus, um Rollenzuweisungen in einem Bereich herunterzuladen.

1. Klicken Sie im Azure-Portal auf **Alle Dienste** , und wählen Sie dann den Bereich aus, in dem Sie Rollenzuweisungen herunterladen möchten. Sie können beispielsweise **Verwaltungsgruppen** , **Abonnements** , **Ressourcengruppen** oder eine Ressource auswählen.

1. Klicken Sie auf die gewünschte Ressource.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf **Rollenzuweisungen herunterladen** , um den Bereich „Rollenzuweisungen herunterladen“ zu öffnen.

    ![Zugriffssteuerung: Rollenzuweisungen herunterladen](./media/role-assignments-list-portal/download-role-assignments.png)

1. Verwenden Sie die Kontrollkästchen, um die Rollenzuweisungen auszuwählen, die Sie in die heruntergeladene Datei einschließen möchten.

    - **Geerbt** : Geerbte Rollenzuweisungen für den aktuellen Bereich einschließen.
    - **Im aktuellen Bereich** : Rollenzuweisungen für den aktuellen Bereich einschließen.
    - **Untergeordnet** : Rollenzuweisungen auf Ebenen unterhalb des aktuellen Bereichs einschließen. Dieses Kontrollkästchen ist für den Verwaltungsgruppenbereich deaktiviert.

1. Wählen Sie das Dateiformat, durch Trennzeichen getrennte Werte (CSV) oder JavaScript Object Notation (JSON).

1. Geben Sie den Dateinamen an.

1. Klicken Sie auf **Starten** , um den Download zu starten.

    Im folgenden finden Sie Beispiele für die Ausgabe in den einzelnen Dateiformaten.

    ![Herunterladen von Rollenzuweisungen im CSV-Format](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Screenshot der heruntergeladenen Rollenzuweisungen im JSON-Format.](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe des Azure-Portals](role-assignments-portal.md)
- [Behandeln von Problemen bei Azure RBAC](troubleshooting.md)
