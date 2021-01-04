---
title: Freigeben von Azure-Portaldashboards mithilfe von rollenbasierter Zugriffssteuerung in Azure
description: In diesem Artikel wird erläutert, wie Sie mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) ein Dashboard im Azure-Portal freigeben.
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: de2efd75d07c3dc7df771aad1bd9c73453dad212
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745977"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>Freigeben von Azure-Dashboards mithilfe der rollenbasierten Zugriffssteuerung

Nach der Konfiguration eines Dashboards können Sie es veröffentlichen und für andere Benutzer in Ihrer Organisation freigeben. Sie gewähren anderen Benutzern Zugriff auf Ihr Dashboard über [rollenbasierte Zugriffssteuerung (RBAC) von Azure](../role-based-access-control/role-assignments-portal.md). Sie weisen einen Benutzer oder eine Gruppe von Benutzern einer Rolle zu. Mit dieser Rolle wird dann definiert, ob diese Benutzer das veröffentlichte Dashboard anzeigen oder ändern können.

Alle veröffentlichten Dashboards werden als Azure-Ressourcen implementiert. Sie sind als verwaltbare Elemente innerhalb Ihres Abonnements vorhanden und in einer Ressourcengruppe enthalten. Aus Sicht der Zugriffssteuerung unterscheiden sich Dashboards nicht von anderen Ressourcen, z.B. einem virtuellen Computer oder Speicherkonto.

> [!TIP]
> Einzelne Kacheln im Dashboard erzwingen basierend auf den von ihnen gezeigten Ressourcen eigene Anforderungen an die Zugriffssteuerung. Sie können ein Dashboard umfassend freigeben, während Sie die Daten auf einzelnen Kacheln schützen.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Grundlagen der Zugriffssteuerung für Dashboards

Mit der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) können Sie Benutzer auf drei verschiedenen Bereichsebenen Rollen zuweisen:

* Abonnement
* Ressourcengruppe
* resource

Die von Ihnen zugewiesenen Berechtigungen werden vom Abonnement an die Ressource vererbt. Das veröffentlichte Dashboard ist eine Ressource. Es kann sein, dass Sie Benutzer bereits Rollen für das Abonnement zugewiesen haben, die für das veröffentlichte Dashboard gelten.

Ein Beispiel: Sie verfügen über ein Azure-Abonnement, und verschiedenen Mitgliedern Ihres Teams wurden die Rollen *Besitzer*, *Mitwirkender* oder *Leser* für das Abonnement zugewiesen. Benutzer, die als Besitzer oder Mitwirkende festgelegt sind, können Dashboards im Abonnement anzeigen, erstellen, ändern oder löschen. Benutzer, die als Leser festgelegt wurden, können Dashboards auflisten und anzeigen, aber sie können diese nicht ändern oder löschen. Benutzer mit Leserzugriff können lokale Änderungen an einem veröffentlichten Dashboard vornehmen (z. B. bei der Problembehandlung), aber sie können diese Änderungen nicht erneut auf dem Server veröffentlichen. Sie können eine private Kopie des Dashboards zur eigenen Verwendung erstellen.

Sie können der Ressourcengruppe mit mehreren Dashboards oder einem einzelnen Dashboard Berechtigungen zuweisen. Beispielsweise können Sie entscheiden, dass eine Gruppe von Benutzern über begrenzte Berechtigungen in Bezug auf das Abonnement und gleichzeitig über einen besseren Zugriff auf ein bestimmtes Dashboard verfügen soll. Weisen Sie diese Benutzer einer Rolle für das Dashboard zu.

## <a name="publish-dashboard"></a>Veröffentlichen des Dashboards

Angenommen, Sie konfigurieren ein Dashboard, das Sie für eine Gruppe von Benutzern in Ihrem Abonnement freigeben möchten. In den folgenden Schritten wird gezeigt, wie Sie ein Dashboard für eine Gruppe mit dem Namen „Storage Managers“ freigeben. Sie können Ihre Gruppe beliebig benennen. Weitere Informationen finden Sie unter [Verwalten von Gruppen in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Vor dem Zuweisen des Zugriffs müssen Sie das Dashboard veröffentlichen.

1. Wählen Sie im Dashboard die Option **Freigeben**.

    ![Auswählen einer Freigabe für Ihr Dashboard](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. Wählen Sie unter **Freigabe und Zugriffssteuerungs** die Option **Veröffentlichen** aus.

    ![Veröffentlichen des Dashboards](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Standardmäßig wird das Dashboard beim Freigeben in einer Ressourcengruppe mit dem Namen **Dashboards** veröffentlicht. Deaktivieren Sie das Kontrollkästchen, um eine andere Ressourcengruppe auszuwählen.

Das Dashboard wurde veröffentlicht. Wenn die aus dem Abonnement geerbten Berechtigungen geeignet sind, sind keine weiteren Schritte erforderlich. Andere Benutzer in Ihrer Organisation können je nach Abonnementebenenrolle auf das Dashboard zugreifen und Änderungen vornehmen.

## <a name="assign-access-to-a-dashboard"></a>Zuweisen des Zugriffs auf ein Dashboard

Sie können einer Gruppe von Benutzern einer Rolle für das Dashboard zuweisen.

1. Wählen Sie nach dem Veröffentlichen des Dashboards die Option **Freigeben** oder **Freigabe aufheben** aus, um auf **Freigabe + Zugriffssteuerung** zuzugreifen.

1. Wählen Sie unter **Freigabe + Zugriffssteuerung** die Option **Benutzer verwalten** aus.

    ![Verwalten von Benutzern für ein Dashboard](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Wählen Sie **Rollenzuweisungen** aus, um vorhandene Benutzer anzuzeigen, die bereits einer Rolle für dieses Dashboard zugewiesen sind.

1. Um einen neuen Benutzer oder eine neue Gruppe hinzuzufügen, wählen Sie **Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus.

    ![Hinzufügen eines Benutzers für den Zugriff auf das Dashboard](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Wählen Sie die Rolle aus, die die zu gewährenden Berechtigungen darstellt. Wählen Sie für dieses Beispiel die Option **Mitwirkender**.

1. Wählen Sie den Benutzer bzw. die Gruppe aus, der bzw. die der Rolle zugewiesen werden soll. Verwenden Sie das Suchfeld, wenn der gesuchte Benutzer oder die Gruppe in der Liste nicht angezeigt wird. Die Liste mit den verfügbaren Gruppen hängt von den Gruppen ab, die Sie in Active Directory erstellt haben.

1. Wählen Sie nach dem Hinzufügen von Benutzern oder Gruppen die Option **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

* Eine Liste mit den Rollen finden Sie unter [Integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md).
* Informationen zum Verwalten von Ressourcen finden Sie unter [Verwalten von Azure-Ressourcen unter Verwendung des Azure-Portals](../azure-resource-manager/management/manage-resources-portal.md).