---
title: 'Schnellstart: Erstellen einer Instanz im Azure-Portal'
titleSuffix: Azure Database Migration Service
description: Verwenden des Azure-Portals zum Erstellen einer Instanz von Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 6232c842514c10a5440e574621ca74e2f4867d86
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981623"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Schnellstart: Erstellen einer Azure Database Migration Service-Instanz über das Azure-Portal

In dieser Schnellstartanleitung verwenden Sie das Azure-Portal, um eine Instanz von Azure Database Migration Service zu erstellen. Nach der Erstellung der Instanz können Sie sie zum Migrieren von Daten aus mehreren Datenbankquellen zu Azure-Datenplattformen verwenden, etwa von SQL Server zu Azure SQL-Datenbank oder von SQL Server zu Azure SQL Managed Instance.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Öffnen Sie Ihren Webbrowser, navigieren Sie zum [Microsoft Azure-Portal](https://portal.azure.com/), und geben Sie dann Ihre Anmeldeinformationen ein, um sich am Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.

> [!NOTE]
> Sie können bis zu zehn Instanzen von DMS pro Abonnement und Region erstellen. Wenn Sie eine größere Anzahl von Instanzen benötigen, erstellen Sie ein Supportticket.

## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters

Sie müssen den Microsoft.DataMigration-Ressourcenanbieter registrieren, bevor Sie Ihre erste Instanz von Database Migration Service erstellen.

1. Suchen Sie im Azure-Portal nach dem Eintrag **Abonnements**, und wählen Sie ihn aus.

   ![Abonnements im Portal anzeigen](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie dann auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie dann **Registrieren** für **Microsoft.DataMigration** aus.

    ![Registrieren des Ressourcenanbieters](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Erstellen einer Instanz des Diensts

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus. Suchen Sie **Azure Database Migration Service**, und wählen Sie ihn aus.

    ![Azure Marketplace](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. Auf dem Bildschirm **Create Migration Service** (Migrationsdienst erstellen) mit den Grundeinstellungen:

     - Wählen Sie das Abonnement aus.
     - Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus.
     - Geben Sie einen Namen für die Instanz von Azure Database Migration Service an.
     - Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten.
     - Wählen Sie **Azure** als Dienstmodus aus.
     - Wählen Sie einen Tarif. Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).
     
    ![Konfigurieren der Grundeinstellungen einer Azure Database Migration Service-Instanz](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Klicken Sie auf Weiter: Netzwerk.

4. Auf dem Netzwerkbildschirm **Create Migration Service** (Migrationsdienst erstellen):

    - Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues. Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die Quelldatenbank und die Zielumgebung. Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

    ![Konfigurieren der Netzwerkeinstellungen einer Azure Database Migration Service-Instanz](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Wählen Sie zum Erstellen des Diensts die Option **Überprüfen + erstellen** aus. 
    
    - Nach einigen Augenblicken wird die Azure Database Migration Service-Instanz erstellt und kann verwendet werden:

    ![Erstellte Migration Service-Instanz](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können alle in dieser Schnellstartanleitung erstellten Ressourcen bereinigen, indem Sie die [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) löschen. Navigieren Sie zum Löschen der Ressourcengruppe zu der von Ihnen erstellten Azure Database Migration Service-Instanz. Klicken Sie auf den Namen der **Ressourcengruppe** und anschließend auf **Ressourcengruppe löschen**. Durch diese Aktion werden alle Ressourcen in der Ressourcengruppe sowie die Gruppe selbst gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* [Offlinemigration von SQL Server zu Azure SQL-Datenbank](tutorial-sql-server-to-azure-sql.md)
* [Onlinemigration von SQL Server zu Azure SQL-Datenbank](tutorial-sql-server-azure-sql-online.md)
* [Offlinemigration von SQL Server zu Azure SQL Managed Instance](tutorial-sql-server-to-managed-instance.md)
* [Onlinemigration von SQL Server zu Azure SQL Managed Instance](tutorial-sql-server-managed-instance-online.md)