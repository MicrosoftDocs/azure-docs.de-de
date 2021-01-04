---
title: Hinzufügen von Bewertungstools in Azure Migrate
description: Hier erfahren Sie, wie Sie Bewertungstools in Azure Migrate hinzufügen.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 9a4ed5542945b8d281ec750d9bbd3a8f444e44d2
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752259"
---
# <a name="add-assessment-tools"></a>Hinzufügen von Bewertungstools

In diesem Artikel wird beschrieben, wie Sie in [Azure Migrate](./migrate-services-overview.md) Bewertungstools hinzufügen. 

- Wenn Sie ein Bewertungstool hinzufügen möchten und noch nicht über ein Azure Migrate-Projekt verfügen, hilft Ihnen [dieser Artikel](create-manage-projects.md) weiter.
- Wenn Sie für die Bewertung ein ISV-Tool oder Movere hinzugefügt haben, [befolgen Sie die Schritte](prepare-isv-movere.md) zur Vorbereitung auf die Arbeit mit dem Tool.

## <a name="select-an-assessment-scenario"></a>Auswählen eines Bewertungsszenarios

1. Klicken Sie im Azure Migrate-Projekt auf **Übersicht**.
2. Wählen Sie das Bewertungsszenario aus:

    - Wählen Sie **Server bewerten und migrieren**, um Computer und Workloads zu ermitteln und für die Migration zu Azure zu bewerten.
    - Um lokale SQL Server-Datenbanken zu bewerten, wählen Sie die Option **Datenbanken bewerten und migrieren** aus.
    - Um lokale Web-Apps zu bewerten oder zu migrieren, wählen Sie **Mehr entdecken** > **Web-Apps** aus.
    - Wählen Sie zum Bewerten Ihrer virtuellen Desktopinfrastruktur die Option **Mehr entdecken** > **Virtuelle Desktopinfrastruktur** aus.

    ![Optionen für die Auswahl eines Bewertungsszenarios](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Auswählen eines Tools für die Serverbewertung 


1. Fügen Sie ein Tool hinzu:

    - Wenn Sie ein Azure Migrate-Projekt über die Option **Server bewerten und migrieren** im Portal erstellt haben, wird das Tool für die Azure Migrate-Serverbewertung automatisch im Projekt hinzugefügt. Wählen Sie zum Hinzufügen zusätzlicher Bewertungstools unter **Server** neben **Bewertungstools** die Option **Weitere Tools hinzufügen** aus.
    
         ![Schaltfläche zum Hinzufügen weiterer Bewertungstools](./media/how-to-assess/add-assessment-tool.png)

    - Wenn Sie ein Projekt über eine andere Option erstellt haben und noch keine Bewertungstools vorhanden sind, wählen Sie unter **Server** > **Bewertungstools** den Link **Hier klicken** aus.

        ![Schaltfläche zum Hinzufügen des ersten Bewertungstools](./media/how-to-assess/no-assessment-tool.png)

2. Wählen Sie unter **Azure Migrate** > **Tools hinzufügen** die hinzuzufügenden Tools aus. Wählen Sie dann **Tool hinzufügen** aus.

    ![Auswählen der Bewertungstools in der Liste](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Auswählen eines Tools für die Datenbankbewertung

1. Fügen Sie ein Tool hinzu:

    - Wenn Sie ein Azure Migrate-Projekt über die Option **Datenbanken bewerten und migrieren** im Portal erstellt haben, wird das Datenbankbewertungstool automatisch im Projekt hinzugefügt. Wählen Sie zum Hinzufügen zusätzlicher Bewertungstools unter **Datenbanken** neben **Bewertungstools** die Option **Weitere Tools hinzufügen** aus.

    - Wenn Sie ein Projekt über eine andere Option erstellt haben und noch keine Datenbankbewertungstools vorhanden sind, wählen Sie unter **Datenbanken** > **Bewertungstools** den Link **Hier klicken** aus.

2. Wählen Sie unter **Azure Migrate** > **Tools hinzufügen** die hinzuzufügenden Tools aus. Wählen Sie dann **Tool hinzufügen** aus.

    ![Auswählen von Datenbankbewertungstools aus einer Liste](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Auswählen eines Tools für die Web-App-Bewertung

Wenn Sie ein Azure Migrate-Projekt über **Mehr entdecken** > **WebApps** im Portal erstellt haben, wird das Web-App-Bewertungstool automatisch im Projekt hinzugefügt. 


1. Wenn das Web-App-Bewertungstool nicht im Projekt vorhanden ist, wählen Sie unter **Web-Apps** > **Bewertungstools** den Link **Hier klicken** aus.
    
    ![Hinzufügen eines Web-App-Bewertungstools](./media/how-to-assess/no-web-app-assessment-tool.png)


2. Wählen Sie unter **Azure Migrate** > **Tools hinzufügen** das Web-App-Bewertungstool aus. Wählen Sie dann **Tool hinzufügen** aus.

    ![Auswählen des Datenmigrationstools in der Liste](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Nächste Schritte

Entdecken Sie lokale Computer für die Bewertung mithilfe des Azure Migrate Server-Bewertungstools für [VMware](./tutorial-discover-vmware.md)-VMs, [Hyper-V](./tutorial-discover-hyper-v.md)- oder [physische Server](./tutorial-discover-physical.md).