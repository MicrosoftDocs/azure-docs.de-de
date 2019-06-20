---
title: Gruppieren von Computern für die Bewertung mit Azure Migrate | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Computer gruppieren, bevor Sie mit dem Azure Migrate-Dienst eine Bewertung ausführen.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544914"
---
# <a name="group-machines-for-assessment"></a>Gruppieren von Computern für die Bewertung

In diesem Artikel wird beschrieben, wie Sie eine Gruppe mit Computern für die Bewertung durch [Azure Migrate](migrate-overview.md) erstellen. Azure Migrate bewertet Computer in der Gruppe, um zu ermitteln, ob sie sich für die Migration zu Azure eignen, und stellt Größen- und Kostenschätzungen für die Ausführung der Computer in Azure bereit. Wenn Sie wissen, welche Computer zusammen migriert werden müssen, können Sie die Gruppe in Azure Migrate mit dem folgenden Verfahren manuell erstellen. Wenn Sie nicht sicher sind, welche Computer gruppiert werden müssen, können die Funktionalität für die Abhängigkeitsvisualisierung in Azure Migrate zum Erstellen von Gruppen verwenden. [Weitere Informationen.](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> Die Funktion zur Visualisierung von Abhängigkeiten ist in Azure Government nicht verfügbar.

## <a name="create-a-group"></a>Erstellen einer Gruppe

1. Klicken Sie in der **Übersicht** des Azure Migrate-Projekts unter „Verwalten“ auf  **Gruppen** > **+Gruppe**, und geben Sie einen Gruppennamen an.
2. Fügen Sie Computer zur Gruppe hinzu, und klicken Sie auf  **Erstellen**.
3. Sie können optional festlegen, dass eine neue Bewertung für die Gruppe ausgeführt wird.

    ![Erstellen einer Gruppe](./media/how-to-create-a-group/create-group.png)

Nachdem die Gruppe erstellt wurde, können Sie sie ändern, indem Sie die Gruppe auf der Seite **Gruppen** auswählen und Computer hinzufügen bzw. entfernen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Erstellen von Gruppen mit hoher Vertrauenswürdigkeit mithilfe der [Zuordnung von Computerabhängigkeiten](how-to-create-group-machine-dependencies.md).
- [Weitere Informationen](concepts-assessment-calculation.md) zur Berechnung von Bewertungen
