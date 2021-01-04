---
title: Hinzufügen von Migrationstools in Azure Migrate
description: Hinzufügen von Migrationstools in Azure Migrate
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 97051e97ec9868f6941b579241e16e62fdd2162b
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751783"
---
# <a name="add-migration-tools"></a>Hinzufügen von Migrationstools

In diesem Artikel wird beschrieben, wie Sie in [Azure Migrate](./migrate-services-overview.md) Migrationstools hinzufügen.

- Wenn Sie ein Migrationstool hinzufügen möchten und noch kein Azure Migrate-Projekt angelegt haben, befolgen Sie die Anweisungen in diesem [Artikel](create-manage-projects.md).
- Wenn Sie für die Migration ein ISV-Tool hinzugefügt haben, [befolgen Sie die Schritte](prepare-isv-movere.md) zur Vorbereitung auf die Arbeit mit dem Tool.

## <a name="select-a-migration-scenario"></a>Auswählen eines Datenmigrationsszenarios

1. Klicken Sie im Azure Migrate-Projekt auf **Übersicht**.
2. Wählen Sie das Migrationsszenario aus, das Sie verwenden möchten:

    - Um Computer und Workloads zu Azure zu migrieren, wählen Sie die Option **Server bewerten und migrieren** aus.
    - Um lokale Datenbanken zu migrieren, wählen Sie die Option **Datenbanken bewerten und migrieren** aus.
    - Um lokale Web-Apps zu migrieren, wählen Sie **Mehr entdecken** > **Web-Apps** aus.
    - Um Daten mithilfe von Data Box zu Azure zu migrieren, wählen Sie **Mehr entdecken** > **Data Box** aus.

    ![Optionen zum Auswählen eines Migrationsszenarios](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Auswählen eines Tools für die Servermigration

1. Fügen Sie ein Tool hinzu:

    - Wenn Sie ein Azure Migrate-Projekt über die Option **Server bewerten und migrieren** im Portal erstellt haben, wird das Tool für die Azure Migrate-Servermigration automatisch im Projekt hinzugefügt. Zum Hinzufügen zusätzlicher Migrationstools wählen Sie unter **Server** neben **Migrationstools** den Link **Weitere Tools hinzufügen** aus.
    
         ![Schaltfläche zum Hinzufügen weiterer Migrationstools](./media/how-to-migrate/add-migration-tools.png)

    - Wenn Sie ein Projekt über eine andere Option erstellt haben und noch keine Migrationstools vorhanden sind, wählen Sie unter **Server** > **Migrationstools** den Link **Hier klicken** aus.

    ![Schaltfläche zum Hinzufügen der ersten Migrationstools](./media/how-to-migrate/no-migration-tool.png)

2. Wählen Sie unter **Azure Migrate** > **Tools hinzufügen** die hinzuzufügenden Tools aus. Wählen Sie dann **Tool hinzufügen** aus.

    ![Auswählen der Bewertungstools in der Liste](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Auswählen eines Datenbankmigrationstools

Wenn Sie ein Azure Migrate-Projekt über die Option **Datenbanken bewerten und migrieren** im Portal erstellt haben, wird das Datenbankmigrationstool automatisch im Projekt hinzugefügt. 

1. Wenn das Datenmigrationstool nicht im Projekt vorhanden ist, wählen Sie unter **Datenbanken** > **Bewertungstools** den Link **Hier klicken** aus.
    
    ![Hinzufügen von Datenmigrationstools](./media/how-to-migrate/no-database-migration-tool.png)


2. Wählen Sie unter **Azure Migrate** > **Tools hinzufügen** das Datenmigrationstool aus. Wählen Sie dann **Tool hinzufügen** aus.

    ![Auswählen des Datenmigrationstools in der Liste](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Auswählen eines Web-App-Migrationstools

Wenn Sie ein Azure Migrate-Projekt über **Mehr entdecken** > **Web-Apps** im Portal erstellt haben, wird das Web-App-Migrationstool automatisch im Projekt hinzugefügt. 

1. Wenn das Web-App-Migrationstool nicht im Projekt vorhanden ist, wählen Sie unter **Web-Apps** > **Bewertungstools** den Link **Hier klicken** aus.

    ![Hinzufügen eines Web-App-Migrationstools](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. Wählen Sie unter **Azure Migrate** > **Tools hinzufügen** das Web-App-Migrationstool aus. Wählen Sie dann **Tool hinzufügen** aus.

    ![Auswählen der Web-App-Bewertungstools in der Liste](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Bestellen von Azure Data Box

Um große Datenmengen zu Azure zu migrieren, können Sie für die Offlinedatenübertragung Azure Data Box bestellen.

1. Wählen Sie unter **Übersicht** die Option **Mehr entdecken** aus.
2. Wählen Sie unter **Mehr entdecken** die Option **Data Box** aus.
3. Wählen Sie unter **Erste Schritte mit Data Box** das Abonnement und die Ressourcengruppe aus, die für die Bestellung einer Data Box verwendet werden sollen.
4. Der **Übertragungstyp** ist ein Import in Azure. Geben Sie das Land, in dem sich die Daten befinden, und die Azure-Region an, in die die Daten übertragen werden sollen. 
5. Klicken Sie auf **Anwenden**, um die Einstellungen zu speichern.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie eine Migration mithilfe des Tools für die Azure Migrate-Servermigration für [Hyper-V](tutorial-migrate-hyper-v.md)- oder [VMware](tutorial-migrate-vmware.md)-VMs.
