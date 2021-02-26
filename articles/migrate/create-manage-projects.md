---
title: Erstellen und Verwalten von Azure Migrate-Projekten
description: Suchen, erstellen, verwalten und löschen Sie Projekte in Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0d4ccc1a60f9ac3c2183023d3d3772da6a1e307b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830752"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Erstellen und Verwalten von Azure Migrate-Projekten

In diesem Artikel wird beschrieben, wie Sie [Azure Migrate](migrate-services-overview.md)-Projekte erstellen, verwalten und löschen. 

Die klassische Version von Azure Migrate wird im Februar 2024 eingestellt. Ab Februar 2024 wird die klassische Version von Azure Migrate nicht mehr unterstützt, und die Bestandsmetadaten im klassischen Projekt werden gelöscht. Wenn Sie klassische Azure Migrate-Projekte verwenden, löschen Sie diese Projekte, und befolgen Sie die Schritte zum Erstellen eines neuen Azure Migrate-Projekts. Sie können kein Upgrade klassischer Azure Migrate-Projekte oder -Komponenten auf Azure Migrate durchführen. Lesen Sie die [häufig gestellten Fragen](https://docs.microsoft.com/azure/migrate/resources-faq#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version), bevor Sie mit der Erstellung beginnen.

Ein Azure Migrate-Projekt wird zum Speichern von Ermittlungs-, Bewertungs- und Migrationsmetadaten verwendet, die in der von Ihnen bewerteten oder migrierten Umgebung gesammelt werden. In einem Projekt können Sie ermittelte Ressourcen nachverfolgen, Bewertungen erstellen und Migrationsvorgänge zu Azure orchestrieren.  

## <a name="verify-permissions"></a>Überprüfen von Berechtigungen

Überprüfen Sie, ob Sie über die richtigen Berechtigungen zum Erstellen eines Azure Migrate-Projekts verfügen:

1. Öffnen Sie im Azure-Portal das entsprechende Abonnement, und wählen Sie  **Zugriffssteuerung (IAM)** aus.
2. Suchen Sie unter **Zugriff überprüfen** nach dem relevanten Konto, und wählen Sie es aus, um Berechtigungen anzuzeigen. Sie sollten über die Berechtigung *Mitwirkender* oder *Besitzer* verfügen. 


## <a name="create-a-project-for-the-first-time"></a>Erstmaliges Erstellen eines Projekts

Richten Sie ein neues Azure Migrate-Projekt in einem Azure-Abonnement ein.

1. Suchen Sie im Azure-Portal nach *Azure Migrate*.
2. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.
3. Wählen Sie in der **Übersicht** die Option **Server bewerten und migrieren** aus.

    ![Option unter „Übersicht“ zum Bewerten und Migrieren von Servern](./media/create-manage-projects/assess-migrate-servers.png)

4. Wählen Sie unter **Server** die Option **Projekt erstellen** aus.

    ![Schaltfläche zum Erstellen des Projekts](./media/create-manage-projects/create-project.png)

5. Wählen Sie unter **Projekt erstellen** das Azure-Abonnement und die Ressourcengruppe aus. Falls noch keine vorhanden ist, erstellen Sie eine Ressourcengruppe.
6. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten.
    - Die geografische Region wird nur zum Speichern der Metadaten verwendet, die von den lokalen Computern erfasst werden. Bei der Migration kann eine beliebige Zielregion ausgewählt werden. 
    - Beachten Sie die unterstützten geografischen Regionen für [öffentliche Clouds](migrate-support-matrix.md#supported-geographies-public-cloud) und [Azure Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

8. Klicken Sie auf **Erstellen**.

   ![Seite für die Eingabe der Projekteinstellungen](./media/create-manage-projects/project-details.png)


Warten Sie einige Minuten, bis das Azure Migrate-Projekt bereitgestellt wurde.

## <a name="create-a-project-in-a-specific-region"></a>Erstellen eines Projekts in einer bestimmten Region

Im Portal können Sie die geografische Region auswählen, in der das Projekt erstellt werden soll. Wenn Sie das Projekt in einer bestimmten Azure-Region erstellen möchten, verwenden Sie den folgenden API-Befehl zum Erstellen des Projekts.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Erstellen zusätzlicher Projekte

Wenn Sie bereits über ein Azure Migrate-Projekt verfügen und ein zusätzliches Projekt erstellen möchten, gehen Sie wie folgt vor:  

1. Suchen Sie im [öffentlichen Azure-Portal](https://portal.azure.com) oder in [Azure Government](https://portal.azure.us) nach **Azure Migrate**.
2. Rufen Sie im Dashboard von Azure Migrate **Server** auf, und wählen Sie oben rechts in der Ecke **Ändern** aus.

   ![Ändern des Azure Migrate-Projekts](./media/create-manage-projects/switch-project.png)

3. Wählen Sie zum Erstellen eines neuen Projekts **Hier klicken** aus.


## <a name="find-a-project"></a>Suchen eines Projekts

Suchen Sie ein Projekt wie folgt:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach *Azure Migrate*.
2. Rufen Sie im Dashboard von Azure Migrate **Server** auf, und wählen Sie oben rechts in der Ecke **Ändern** aus.

    ![Wechseln zu einem vorhandenen Azure Migrate-Projekt](./media/create-manage-projects/switch-project.png)

3. Wählen Sie das entsprechende Abonnement und das Azure Migrate-Projekt aus.


### <a name="find-a-legacy-project"></a>Suchen eines Legacyprojekts

Wenn Sie das Projekt in der [vorherigen Version](migrate-services-overview.md#azure-migrate-versions) von Azure Migrate erstellt haben, finden Sie es wie folgt:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach *Azure Migrate*.
2. Wenn Sie ein Projekt in der vorherigen Version erstellt haben, wird im Dashboard von Azure Migrate ein Banner mit einem Verweis auf ältere Projekte angezeigt. Wählen Sie das Banner aus.

    ![Zugreifen auf vorhandene Projekte](./media/create-manage-projects/access-existing-projects.png)

3. Überprüfen Sie die Liste der alten Projekte.


## <a name="delete-a-project"></a>Löschen eines Projekts

Gehen Sie beim Löschen wie folgt vor:

1. Öffnen Sie die Azure-Ressourcengruppe, in der das Projekt erstellt wurde.
2. Wählen Sie auf der Seite für die Ressourcengruppe **Ausgeblendete Typen anzeigen** aus.
3. Wählen Sie das zu löschende Migrationsprojekt und die zugehörigen Ressourcen aus.
    - Der Ressourcentyp ist **Microsoft.Migrate/migrateprojects**.
    - Wenn die Ressourcengruppe ausschließlich vom Azure Migrate-Projekt verwendet wird, können Sie die gesamte Ressourcengruppe löschen.

Beachten Sie dabei Folgendes:

- Beim Löschen werden sowohl das Projekt als auch die Metadaten zu den gefundenen Computern gelöscht.
- Wenn Sie die ältere Version von Azure Migrate verwenden, öffnen Sie die Azure-Ressourcengruppe, in der das Projekt erstellt wurde. Wählen Sie das Migrationsprojekt aus, das Sie löschen möchten (der Ressourcentyp ist **Migrationsprojekt**).
- Wenn Sie die Abhängigkeitsanalyse mit einem Azure Log Analytics-Arbeitsbereich verwenden:
    - Wenn Sie dem Serverbewertungstool einen Log Analytics-Arbeitsbereich angefügt hatten, wird dieser nicht automatisch gelöscht. Derselbe Log Analytics-Arbeitsbereich kann für mehrere Szenarien verwendet werden.
    - Wenn Sie den Log Analytics-Arbeitsbereich löschen möchten, gehen Sie manuell vor.
- Das Löschen eines Projekts kann nicht rückgängig gemacht werden. Gelöschte Objekte können nicht wiederhergestellt werden.

### <a name="delete-a-workspace-manually"></a>Manuelles Löschen eines Arbeitsbereichs

1. Navigieren Sie zu dem Log Analytics-Arbeitsbereich, der dem Projekt angefügt ist.

    - Wenn Sie das Azure Migrate-Projekt nicht gelöscht haben, finden Sie den Link zum Arbeitsbereich unter **Essentials** > **Serverbewertung**.
       ![LA-Arbeitsbereich](./media/create-manage-projects/loganalytics-workspace.png).
       
    - Wenn Sie das Azure Migrate-Projekt bereits gelöscht haben, wählen Sie **Ressourcengruppen** im linken Bereich des Azure-Portals aus, und suchen Sie den Arbeitsbereich.
       
2. [Befolgen Sie die Anweisungen](../azure-monitor/platform/delete-workspace.md), um den Arbeitsbereich zu löschen.

## <a name="next-steps"></a>Nächste Schritte

Fügen Sie den Azure Migrate-Projekten Tools für die [Bewertung](how-to-assess.md) oder [Migration](how-to-migrate.md) hinzu.
