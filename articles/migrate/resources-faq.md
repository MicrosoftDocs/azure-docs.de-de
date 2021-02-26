---
title: 'Azure Migrate: häufig gestellte Fragen'
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zum Azure Migrate-Dienst.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 6396ca4483f5e9782890123a7c39a0cbcdbd30a3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832571"
---
# <a name="azure-migrate-common-questions"></a>Von der Azure Häufig gestellte Fragen

In diesem Artikel werden Fragen zu Azure Migrate beantwortet. Wenn Sie Fragen haben, nachdem Sie diesen Artikel gelesen haben, können Sie diese im [Azure Migrate-Forum](https://aka.ms/AzureMigrateForum) stellen. Außerdem können Sie die folgenden Artikel lesen:

- Fragen zur [Azure Migrate-Appliance](common-questions-appliance.md)
- Fragen zur [Ermittlung, Bewertung und Abhängigkeitsvisualisierung](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Was ist Azure Migrate?

Azure Migrate bietet einen zentralen Hub zum Nachverfolgen der Ermittlung und Bewertung Ihrer lokalen Apps und Workloads und von VMs in der privaten und öffentlichen Cloud sowie deren Migration zu Azure. Der Hub stellt Azure Migrate-Tools für die Bewertung und Migration sowie Angebote von unabhängigen Drittanbietern bereit. [Weitere Informationen](migrate-services-overview.md)

## <a name="what-can-i-do-with-azure-migrate"></a>Wofür kann ich Azure Migrate verwenden?

Verwenden Sie Azure Migrate, um lokale Infrastrukturen, Anwendungen und Daten in Azure zu ermitteln, zu bewerten und zu Azure zu migrieren. Azure Migrate unterstützt die Bewertung und Migration von lokalen VMware-VMs, Hyper-V-VMs, physischen Servern, anderen virtualisierten VMs, Datenbanken, Web-Apps und virtuellen Desktops. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Was ist der Unterschied zwischen Azure Migrate und Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) stellt einen zentralisierten Hub für die Bewertung und Migration zu Azure bereit. 

- Die Verwendung von Azure Migrate bietet Interoperabilität und ermöglicht eine zukünftige Erweiterbarkeit mit Azure Migrate-Tools, anderen Azure-Diensten und Tools von Drittanbietern.
- Azure Migrate: Das Servermigrationstool ist speziell für die Servermigration zu Azure konzipiert. Es ist für die Migration optimiert. Sie müssen sich nicht mit Konzepten und Szenarien vertraut machen, die in keiner direkten Relation zur Migration stehen. 
- Ab dem Startzeitpunkt der Replikation für eine VM fallen 180 Tage lang keine Gebühren für die Nutzung des Tools an. So haben Sie genügend Zeit, um die Migration abzuschließen. Sie zahlen lediglich für die bei der Replikation verwendeten Speicher- und Netzwerkressourcen sowie für Computeressourcen, die bei Testmigrationen genutzt werden.
- Azure Migrate unterstützt alle Migrationsszenarios, die von Site Recovery unterstützt werden. Außerdem bietet Azure Migrate für VMware-VMs eine Migrationsoption ohne Agent.
- Wir priorisieren nur neue Migrationsfeatures für das Servermigrationstool von Azure Migrate. Diese Features sind für Site Recovery nicht vorgesehen.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) sollte nur für die Notfallwiederherstellung verwendet werden.

Azure Migrate: Servermigration: Dieses Tool nutzt bestimmte Site Recovery-Back-End-Funktionen für die Lift & Shift-Migration einiger lokaler Computer.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Ich habe ein Projekt mit der vorherigen klassischen Erfahrung von Azure Migrate. Wie beginne ich mit der Verwendung der neuen Version?

Die klassische Version von Azure Migrate wird im Februar 2024 eingestellt. Ab Februar 2024 wird die klassische Version von Azure Migrate nicht mehr unterstützt, und die Bestandsmetadaten im klassischen Projekt werden gelöscht. Sie können keine Projekte oder Komponenten aus der vorherigen Version in die neue Version aktualisieren. Sie müssen [ein neues Azure Migrate-Projekt](create-manage-projects.md) erstellen und ihm [Bewertungs- und Migrationstools](./create-manage-projects.md) hinzufügen. Verwenden Sie die Tutorials, um zu verstehen, wie die verfügbaren Bewertungs- und Migrationstools verwendet werden. Wenn Sie einen Log Analytics-Arbeitsbereich mit einem klassischen Projekt verknüpft haben, können Sie ihn an ein Projekt der aktuellen Version anfügen, nachdem Sie das klassische Projekt gelöscht haben.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Was ist der Unterschied zwischen der Serverbewertung von Azure Migrate und dem MAP Toolkit?

Die Serverbewertung ermöglicht eine Bewertung zum Feststellen der Migrationsbereitschaft und eine Auswertung der Workloads für die Migration nach Azure. Das [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) unterstützt Sie bei anderen Aufgaben, beispielsweise der Migrationsplanung für neuere Versionen von Windows-Client- und -Serverbetriebssystemen sowie der Nachverfolgung der Softwarenutzung. Verwenden Sie für diese Szenarien weiterhin das MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Was ist der Unterschied zwischen der Serverbewertung und dem Site Recovery-Bereitstellungsplaner?

Die Serverbewertung ist ein Tool zur Migrationsplanung. Der Site Recovery-Bereitstellungsplaner ist ein Tool zur Notfallwiederherstellung.

Wählen Sie das Tool basierend auf den gewünschten Aktionen aus:

- **Planen der lokalen Migration zu Azure**: Wenn Sie planen, Ihre lokalen Server zu Azure zu migrieren, verwenden Sie für die Migrationsplanung die Serverbewertung. Die Serverbewertung bewertet lokale Workloads und stellt Anleitungen und Tools zur Verfügung, die Sie bei der Migration unterstützen. Nach dem Einrichten des Migrationsplans können Sie Tools wie die Serverbewertung von Azure Migrate verwenden, um die Computer zu Azure zu migrieren.
- **Planen der Notfallwiederherstellung in Azure**: Wenn Sie planen, mit Site Recovery eine Notfallwiederherstellung lokaler Computer in Azure einzurichten, verwenden Sie die Site Recovery-Bereitstellungsplaner. Der Bereitstellungsplaner führt eine umfassende, Site Recovery-spezifische Bewertung Ihrer lokalen Umgebung für die Notfallwiederherstellung durch. Er stellt Empfehlungen im Zusammenhang mit der Notfallwiederherstellung zur Verfügung, z. B. Replikation und Failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Wie funktioniert die Servermigration mit Site Recovery?

- Wenn Sie Azure Migrate verwenden: Servermigration zum Durchführen einer Migration *ohne Agent* für lokale VMware-VMs – die Migration ist in diesem Fall nativ für Azure Migrate, und Site Recovery wird nicht verwendet.
- Wenn Sie Azure Migrate verwenden: Servermigration zum Durchführen einer *Agent-basierten* Migration von VMware-VMs (oder Migrieren von Hyper-V-VMs oder physischen Servern) – die Servermigration von Azure Migrate verwendet in diesem Fall die Replikations-Engine von Azure Site Recovery.

## <a name="which-geographies-are-supported"></a>Welche geografischen Regionen werden unterstützt?

Lesen Sie die unterstützten geografischen Regionen für die [öffentliche Cloud](migrate-support-matrix.md#supported-geographies-public-cloud) und [Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government) nach.

## <a name="how-do-i-get-started"></a>Wie fange ich an?

Ermitteln Sie das benötigte Tool, und fügen Sie dieses dann einem Azure Migrate-Projekt hinzu. 

So fügen Sie ein ISV-Tool oder Movere hinzu

1. Beziehen Sie zunächst eine Lizenz, oder registrieren Sie sich für eine kostenlose Testversion gemäß der Toolrichtlinie. Die Lizenzierung für ISV-Tools erfolgt gemäß dem Lizenzierungsmodell des ISV oder Tools.
2. In jedem Tool ist eine Option zum Herstellen einer Verbindung mit Azure Migrate vorhanden. Befolgen Sie die Anweisungen und die Dokumentation des Tools, um dieses mit Azure Migrate zu verbinden.

Sie können Ihre Migrationsjourney innerhalb des Azure Migrate-Projekts, an anderen Orten in Azure und in anderen Tools nachverfolgen.

## <a name="how-do-i-delete-a-project"></a>Wie lösche ich ein Projekt?

Erfahren Sie, wie Sie [ein Projekt löschen](how-to-delete-project.md). 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Übersicht zu Azure Migrate](migrate-services-overview.md) an.