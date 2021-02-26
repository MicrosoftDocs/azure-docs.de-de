---
title: 'Schnellstart: Erstellen einer Verwaltungsgruppe mit dem Portal'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe des Azure-Portals eine Verwaltungsgruppe, um Ihre Ressourcen in einer Ressourcenhierarchie zu organisieren.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 5a336fe4caf5d2f825a93b5c7b8f8d05f6255c39
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592431"
---
# <a name="quickstart-create-a-management-group"></a>Schnellstart: Erstellen einer Verwaltungsgruppe

Bei Verwaltungsgruppen handelt es sich um Container, mit denen Sie Zugriff, Richtlinien und Konformität abonnementübergreifend verwalten können. Erstellen Sie diese Container, um eine effektive und effiziente Hierarchie zu erstellen, die mit [Azure Policy](../policy/overview.md) und mit der [rollenbasierten Zugriffssteuerung (Role-Based Access Controls, RBAC) von Azure](../../role-based-access-control/overview.md) verwendet werden kann. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](overview.md).

Es kann bis zu 15 Minuten dauern, bis die Erstellung der ersten Verwaltungsgruppe im Verzeichnis abgeschlossen ist. Bei der ersten Erstellung werden Prozesse zum Einrichten des Verwaltungsgruppendiensts in Azure für Ihr Verzeichnis ausgeführt. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist. Weitere Informationen finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Alle Azure AD-Benutzer im Mandanten können eine Verwaltungsgruppe erstellen, ohne dafür die Schreibberechtigung für die Verwaltungsgruppe zu benötigen, wenn der [Hierarchieschutz](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nicht aktiviert ist. Diese neue Verwaltungsgruppe ist dann der Stammverwaltungsgruppe oder der [Standardverwaltungsgruppe](./how-to/protect-resource-hierarchy.md#setting---default-management-group) untergeordnet, und dem Ersteller wird die Rolle „Besitzer“ zugewiesen. Der Verwaltungsgruppendienst ermöglicht dies, damit Rollen nicht auf Stammebene zugewiesen werden müssen. Bei der Erstellung der Stammverwaltungsgruppe hat kein Benutzer auf sie Zugriff. Der Sinn dahinter, dass Verwaltungsgruppen zu Beginn auf Stammebene erstellt werden können, ist die Vermeidung der Hürde, erst nach den globalen Administratoren für Azure AD suchen zu müssen, bevor Verwaltungsgruppen verwendet werden können.

### <a name="create-in-portal"></a>Erstellen im Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Alle Dienste** > **Verwaltung und Governance**.

1. Wählen Sie **Verwaltungsgruppen** aus.

1. Wählen Sie **+ Verwaltungsgruppe hinzufügen** aus.

   :::image type="content" source="./media/main.png" alt-text="Screenshot: Seite „Verwaltungsgruppen“ mit untergeordneten Verwaltungsgruppen und Abonnements" border="false":::

1. Lassen Sie die Option **Neu erstellen** aktiviert, und füllen Sie das Feld für die Verwaltungsgruppen-ID aus.

   - Die **ID der Verwaltungsgruppe** ist der eindeutige Bezeichner des Verzeichnisses, der zum Übermitteln von Befehlen für diese Verwaltungsgruppe verwendet wird. Dieser Bezeichner kann nach der Erstellung nicht bearbeitet werden, da er im gesamten Azure-System zum Identifizieren dieser Gruppe verwendet wird. Die [Stammverwaltungsgruppe](./overview.md#root-management-group-for-each-directory) wird mit einer ID, bei der es sich um die Azure Active Directory-ID handelt, automatisch erstellt. Weisen Sie bei allen anderen Verwaltungsgruppen eine eindeutige ID zu.
   - Im Feld für den Anzeigenamen wird der Name angegeben, der im Azure-Portal angezeigt wird. Beim Erstellen der Verwaltungsgruppe gibt es ein optionales Feld für einen separaten Anzeigenamen, der jederzeit geändert werden kann.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Screenshot: Optionen unter „Verwaltungsgruppe hinzufügen“ zum Erstellen einer neuen Verwaltungsgruppe":::

1. Wählen Sie **Speichern** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie zum Entfernen der erstellten Verwaltungsgruppe die folgenden Schritte aus:

1. Klicken Sie auf **Alle Dienste** > **Verwaltung und Governance**.

1. Wählen Sie **Verwaltungsgruppen** aus.

1. Suchen Sie die oben erstellte Verwaltungsgruppe, wählen Sie sie aus, und wählen Sie dann neben dem Namen **Details** aus.
   Wählen Sie anschließend **Löschen** aus, und bestätigen Sie die Aktion.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Verwaltungsgruppe zum Organisieren der Ressourcenhierarchie erstellt. Die Verwaltungsgruppe kann Abonnements oder andere Verwaltungsgruppen enthalten.

Weitere Informationen zu Verwaltungsgruppen und zur Verwaltung Ihrer Ressourcenhierarchie finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwalten von Ressourcen mit Verwaltungsgruppen](./manage.md)