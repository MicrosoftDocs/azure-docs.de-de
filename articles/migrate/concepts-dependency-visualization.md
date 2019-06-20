---
title: Visualisierung von Abhängigkeiten in Azure Migrate | Microsoft-Dokumentation
description: Bietet eine Übersicht über Bewertungsberechnungen im Azure Migrate-Dienst.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 8df587db7655e2aafd876d80581f3296c8c99fbf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60201560"
---
# <a name="dependency-visualization"></a>Visualisierung von Abhängigkeiten

Der [Azure Migrate](migrate-overview.md)-Dienst bewertet Gruppen von lokalen Computern für die Migration zu Azure. Sie können die Funktionalität zur Visualisierung von Abhängigkeiten in Azure Migrate zum Erstellen von Gruppen nutzen. Dieser Artikel enthält Informationen zu diesem Feature.

> [!NOTE]
> Die Funktion zur Visualisierung von Abhängigkeiten ist in Azure Government nicht verfügbar.

## <a name="overview"></a>Übersicht

Mithilfe der Abhängigkeitsvisualisierung in Azure Migrate können Sie Gruppen mit hoher Vertrauenswürdigkeit für Migrationsbewertungen erstellen. Sie können die Abhängigkeitsvisualisierung verwenden, um Netzwerkabhängigkeiten von Computern anzuzeigen und zugehörige Computer zu identifizieren, die ebenfalls zu Azure migriert werden müssen. Diese Funktionalität ist in Szenarios nützlich, in denen nicht vollständig bekannt ist, welche Computer für Ihre Anwendung erforderlich sind und in die Migration zu Azure eingeschlossen werden müssen.

## <a name="how-does-it-work"></a>Wie funktioniert dies?

Azure Migrate verwendet für die Abhängigkeitsvisualisierung die [Dienstzuordnung](../operations-management-suite/operations-management-suite-service-map.md) in [Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md).
- Sie müssen jedem Azure Migrate-Projekt einen neuen oder vorhandenen Log Analytics-Arbeitsbereich zuordnen, um die Abhängigkeitsvisualisierung nutzen zu können.
- Sie können einen Arbeitsbereich nur in dem Abonnement erstellen oder anfügen, in dem das Migrationsprojekt erstellt wird.
- Navigieren Sie zum Abschnitt **Essentials** (Zusammenfassung) der Seite **Übersicht** für ein Projekt, und klicken Sie auf **Konfiguration erforderlich**, um einem Projekt einen Log Analytics-Arbeitsbereich anzufügen.

    ![Zuordnen von Log Analytics-Arbeitsbereichen](./media/concepts-dependency-visualization/associate-workspace.png)

- Beim Zuordnen eines Arbeitsbereichs erhalten Sie die Möglichkeit, einen neuen Arbeitsbereich zu erstellen oder einen vorhandenen anzufügen:
  - Wenn Sie einen neuen Arbeitsbereich erstellen, müssen Sie für diesen einen Namen angeben. Der Arbeitsbereich wird dann in einer Region in derselben [Azure-Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) erstellt, in der auch das Migrationsprojekt erstellt wurde.
  - Wenn Sie einen vorhandenen Arbeitsbereich anfügen, können Sie zwischen allen verfügbaren Arbeitsbereichen im selben Abonnement wie das Migrationsprojekt auswählen. Beachten Sie, dass nur die Arbeitsbereiche aufgeführt sind, die in einer Region erstellt wurden, in der die [Dienstzuordnung unterstützt wird](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Um einen Arbeitsbereich anzufügen, stellen Sie sicher, dass Sie Leserzugriff auf den Arbeitsbereich haben.

  > [!NOTE]
  > Nachdem Sie einem Projekt einen Arbeitsbereich hinzugefügt haben, können Sie ihn nicht mehr ändern.

- Der zugeordnete Arbeitsbereich wird mit dem Schlüssel **Migration Project** (Migrationsprojekt) und dem Wert **project name** (Projektname) markiert, die Sie für die Suche im Azure-Portal verwenden können.
- Um zum Arbeitsbereich zu navigieren, der dem Projekt zugeordnet ist, können Sie den Abschnitt **Essentials** (Zusammenfassung) der Seite **Übersicht** des Projekts öffnen und auf den Arbeitsbereich zugreifen.

    ![Navigieren zum Log Analytics-Arbeitsbereich](./media/concepts-dependency-visualization/oms-workspace.png)

Zur Verwendung der Abhängigkeitsvisualisierung müssen Sie Agents auf alle lokalen Computer, die Sie analysieren möchten, herunterladen und dort installieren.  

- Der [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) muss auf jedem Computer installiert werden.
- Der [Dependency-Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) muss auf jedem Computer installiert werden.
- Falls Sie über Computer ohne Internetverbindung verfügen, ist es außerdem erforderlich, auf diesen das Log Analytics-Gateway herunterzuladen und zu installieren.

Sie benötigen diese Agents auf Computern nur, wenn Sie die Abhängigkeitsvisualisierung verwenden.

## <a name="do-i-need-to-pay-for-it"></a>Fallen dadurch Kosten an?

Azure Migrate ist ohne Aufpreis erhältlich. Für die Verwendung des Features für die Abhängigkeitsvisualisierung in Azure Migrate ist die Dienstzuordnung erforderlich. Außerdem müssen Sie dem Azure Migrate-Projekt einen neuen oder vorhandenen Log Analytics-Arbeitsbereich zuweisen. Die Funktionalität für die Abhängigkeitsvisualisierung in Azure Migrate ist für die ersten 180 Tage kostenlos.

1. Für andere Lösungen als die Dienstzuordnung in diesem Log Analytics-Arbeitsbereich fallen die [Log Analytics-Standardgebühren](https://azure.microsoft.com/pricing/details/log-analytics/) an.
2. Für die Lösung „Dienstzuordnung“ fallen in den ersten 180 Tagen ab dem Tag der Zuordnung des Log Analytics-Arbeitsbereichs zum Azure Migrate-Projekt keine Kosten an, um Migrationsszenarios ohne weitere Gebühren zu unterstützen. Nach 180 Tagen fallen die Log Analytics-Standardgebühren an.

Verwenden Sie beim Registrieren von Agents für den Arbeitsbereich die ID und den Schlüssel, die bzw. der auf der Seite mit Schritten für die Agent-Installation für das Projekt angegeben wurde.

Wenn das Azure Migrate-Projekt gelöscht wurde, wird der Arbeitsbereich nicht zusammen mit dem Projekt gelöscht. Nach dem Löschen des Projekts ist die Nutzung der Dienstzuordnung nicht mehr kostenlos, und für jeden Knoten werden Kosten gemäß des kostenpflichtigen Tarifs für den Log Analytics-Arbeitsbereich berechnet.

> [!NOTE]
> Für das Feature für die Abhängigkeitsvisualisierung wird die Dienstzuordnung über einen Log Analytics-Arbeitsbereich verwendet. Seit dem 28. Februar 2018 ist das Feature mit der Ankündigung der allgemeinen Verfügbarkeit von Azure Migrate jetzt ohne weitere Kosten verfügbar. Sie müssen ein neues Projekt erstellen, um den kostenlosen Arbeitsbereich nutzen zu können. Für Arbeitsbereiche, die bereits vor der allgemeinen Verfügbarkeit vorhanden waren, können weiterhin Kosten anfallen. Daher empfehlen wir Ihnen, die Umstellung auf ein neues Projekt durchzuführen.

Weitere Informationen zu den Preisen von Azure Migrate finden Sie [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Wie verwalte ich den Arbeitsbereich?

Sie können den Log Analytics-Arbeitsbereich außerhalb von Azure Migrate verwalten. Er wird nicht gelöscht, wenn Sie das Migrationsprojekt löschen, in dem er erstellt wurde. Wenn Sie den Arbeitsbereich nicht mehr benötigen, [löschen Sie ihn](../azure-monitor/platform/manage-access.md) manuell.

Löschen den von Azure Migrate erstellten Arbeitsbereich nur, wenn Sie auch das Migrationsprojekt löschen. Wenn Sie dies tun, funktioniert die Abhängigkeitsvisualisierung nicht wie erwartet.

## <a name="next-steps"></a>Nächste Schritte
- [Group machines using machine dependency mapping](how-to-create-group-machine-dependencies.md) (Gruppieren von Computern mithilfe der Computerabhängigkeitszuordnung)
- [Erfahren Sie mehr](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) über die häufig gestellten Fragen (FAQs) zur Visualisierung von Abhängigkeiten.
