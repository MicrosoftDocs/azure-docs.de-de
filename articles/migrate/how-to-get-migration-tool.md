---
title: Migrieren von Computern nach der Bewertung mit Azure Migrate | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie nach dem Ausführen einer Bewertung mit dem Azure Migrate-Dienst Empfehlungen zur Migration von Computern erhalten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 068b55e40afc96dbcfae26c8bf7da8a1b9ea349a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60598329"
---
# <a name="migrate-machines-after-assessment"></a>Migrieren von Computern nach der Bewertung


[Azure Migrate](migrate-overview.md) bewertet lokale Computer, um zu prüfen, ob diese für die Migration zu Azure geeignet sind, und gibt Größen- und Kostenschätzungen zum Ausführen der Computer in Azure an. Derzeit bewertet Azure Migrate Computer lediglich für die Migration. Die eigentliche Migration erfolgt aktuell unter Verwendung anderer Azure-Dienste.

In diesem Artikel wird beschrieben, wie Sie nach dem Ausführen einer Migrationsbewertung Empfehlungen für ein Migrationstool erhalten.

> [!NOTE]
> Die Empfehlung eines Migrationstools ist in Azure Government nicht verfügbar.

## <a name="migration-tool-suggestion"></a>Vorschlag eines Migrationstools

Um Empfehlungen in Bezug auf Migrationstools zu erhalten, müssen Sie eine umfassende Ermittlung der lokalen Umgebung durchführen. Die umfassende Ermittlung erfolgt durch die Installation von Agents auf den lokalen Computern.  

1. Erstellen Sie ein Azure Migrate-Projekt, ermitteln Sie lokale Computer, und erstellen Sie eine Migrationsbewertung. [Weitere Informationen](tutorial-assessment-vmware.md)
2. Laden Sie die Azure Migrate-Agents herunter, und installieren Sie sie auf allen lokalen Computern, für die die empfohlene Migrationsmethode angezeigt werden soll. [Führen Sie diese Schritte aus](how-to-create-group-machine-dependencies.md#prepare-for-dependency-visualization), um die Agents zu installieren.
2. Identifizieren Sie die lokalen Computer, die für die Lift & Shift-Migration geeignet sind. Hierbei handelt es sich um die virtuellen Computer, auf denen keine Änderungen an den ausgeführten Anwendungen vorgenommen werden müssen und die im Istzustand migriert werden können.
3. Für die Lift & Shift-Migration wird die Verwendung von Azure Site Recovery empfohlen. [Weitere Informationen](../site-recovery/tutorial-migrate-on-premises-to-azure.md) Alternativ können Sie auch Drittanbietertools verwenden, die die Migration zu Azure unterstützen.
4. Wenn Sie über lokale Computer verfügen, die für die Lift & Shift-Migration nicht geeignet sind, d.h, wenn Sie statt einem gesamten virtuellen Computer bestimmte Anwendungen migrieren möchten, können Sie andere Migrationstools verwenden. Beispielsweise empfiehlt sich der [Azure Database Migration Service](https://azure.microsoft.com/campaigns/database-migration/), wenn Sie lokale Datenbanken, z.B. SQL Server, MySQL oder Oracle, zu Azure migrieren möchten.


## <a name="review-suggested-migration-methods"></a>Überprüfen der vorgeschlagenen Migrationsmethoden

1. Damit Sie eine empfohlene Migrationsmethode erhalten, müssen Sie zunächst ein Azure Migrate-Projekt erstellen, lokale Computer ermitteln und eine Migrationsbewertung ausführen. [Weitere Informationen](tutorial-assessment-vmware.md)
2. Nachdem die Bewertung erstellt wurde, zeigen Sie sie im Projekt > **Übersicht** > **Dashboard** an. Klicken Sie auf **Assessment Readiness** (Bewertungsbereitschaft)

    ![Bewertungsbereitschaft](./media/tutorial-assessment-vmware/assessment-report.png)  

3. Überprüfen Sie in **Vorgeschlagenes Tool** die Vorschläge für die Tools, die Sie für die Migration verwenden können.

    ![Vorgeschlagenes Tool](./media/tutorial-assessment-vmware/assessment-suitability.png)




## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen](concepts-assessment-calculation.md) zur Berechnung von Bewertungen
