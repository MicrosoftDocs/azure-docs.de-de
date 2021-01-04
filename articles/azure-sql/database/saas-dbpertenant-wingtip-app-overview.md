---
title: 'Beispiel für eine mehrinstanzenfähige App: Wingtip SaaS'
description: In diesem Artikel erfahren Sie, wie Sie eine mehrinstanzenfähige Beispielanwendung nutzen, die Azure SQL-Datenbank verwendet – das Wingtip-SaaS-Beispiel.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 09651ed5c7472c12d82108fdbd450cca6e84349f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447089"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Einführung in eine mehrinstanzenfähige SaaS-App, die das Muster mit einer Datenbank pro Mandant mit Azure SQL-Datenbank verwendet.
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Die Wingtip-SaaS-Anwendung ist eine Beispiel-App mit mehreren Mandanten. Die App verwendet das SaaS-Anwendungsmuster mit einer Datenbank pro Mandant, um mit mehreren Mandanten zu kommunizieren. Die App stellt Features von Azure SQL-Datenbank vor, die mithilfe verschiedener SaaS-Entwurfs- und -Verwaltungsmuster SaaS-Szenarien ermöglichen. Um einen schnellen Einstieg sicherzustellen, wird die Wingtip-SaaS-App in weniger als fünf Minuten bereitgestellt.

Der Quellcode der Anwendung und die Verwaltungsskripts sind im GitHub-Repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) verfügbar. Bevor Sie beginnen, lesen Sie die Schritte zum Herunterladen und Entsperren der Wingtip-Tickets-Verwaltungsskripts unter [Allgemeine Hinweise](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="application-architecture"></a>Anwendungsarchitektur

Die Wingtip-SaaS-App verwendet das Modell mit einer Datenbank pro Mandant. Zudem nutzt sie Pools für elastische SQL-Datenbanken, um die Effizienz zu maximieren. Für die Bereitstellung von Mandanten und deren Zuordnung mit den jeweiligen Daten wird eine Katalogdatenbank verwendet. Die Wingtip-SaaS-Kernanwendung verwendet einen Pool mit drei Beispielmandanten sowie eine Katalogdatenbank. Die Katalog- und Mandantenserver wurden mit DNS-Aliasen bereitgestellt. Diese Aliase werden zum Verwalten eines Verweises auf die aktiven Ressourcen verwendet, die von der Wingtip-Anwendung genutzt werden. Die Aliase werden aktualisiert, um in den Tutorials zur Notfallwiederherstellung auf Wiederherstellungsressourcen zu verweisen. Die anfängliche Bereitstellung wird nach Abschluss vieler Wingtip-SaaS-Tutorials durch Add-Ons ergänzt. Dabei werden Add-Ons wie analytische Datenbanken und datenbankübergreifende Schemaverwaltung eingeführt.


![Architektur der SaaS-Anwendung Wingtip](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Wenn Sie die Tutorials durchgehen und mit der App arbeiten, legen Sie den Schwerpunkt auf die SaaS-Muster, da sie im Zusammenhang mit der Datenebene stehen. Mit anderen Worten: Konzentrieren Sie sich auf die Datenebene, und nehmen Sie keine übermäßige Analyse der App selbst vor. Sie müssen die Implementierung dieser SaaS-Muster verstehen, damit Sie die Implementierung dieser Muster in Ihre Anwendungen vornehmen können. Berücksichtigen Sie auch, dass Sie ggf. Änderungen entsprechend Ihren Geschäftsanforderungen vornehmen müssen.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutorials zur SaaS-App Wingtip für die SQL-Datenbank

Sehen Sie sich nach der Bereitstellung der App die folgenden Tutorials an, die auf der erstmaligen Bereitstellung aufbauen. In diesen Tutorials werden allgemeine SaaS-Muster behandelt, die die integrierten Features der SQL-Datenbank sowie von Azure Synapse Analytics und anderen Azure-Diensten nutzen. Die Tutorials enthalten PowerShell-Skripts mit ausführlichen Erklärungen. Die Erklärungen erleichtern das Verständnis und die Implementierung der gleichen SaaS-Verwaltungsmuster in Ihren Anwendungen.


| Lernprogramm | BESCHREIBUNG |
|:--|:--|
| [Einführung in eine mehrinstanzenfähige SaaS-App, die das Muster mit einer Datenbank pro Mandant mit SQL-Datenbank verwendet](saas-tenancy-wingtip-app-guidance-tips.md) | Laden Sie PowerShell-Skripts herunter, und führen Sie sie aus, um Teile der Anwendung vorzubereiten. |
|[Bereitstellen und Kennenlernen der SaaS-Anwendung Wingtip](./saas-dbpertenant-get-started-deploy.md)|  Stellen Sie die Wingtip-SaaS-Anwendung mit Ihrem Azure-Abonnement bereit, und machen Sie sich mit ihr vertraut. |
|[Bereitstellen und Katalogisieren von Mandanten](./saas-dbpertenant-provision-and-catalog.md)| Hier erfahren Sie, wie die Anwendung mithilfe einer Katalogdatenbank mit Mandanten verbunden wird und wie den Mandanten im Katalog die entsprechenden Daten zugeordnet werden. |
|[Überwachen und Verwalten der Leistung](./saas-dbpertenant-performance-monitoring.md)| Hier erfahren Sie, wie Überwachungsfunktionen der SQL-Datenbank verwendet und Benachrichtigungen festgelegt werden, wenn Leistungsschwellenwerte überschritten werden. |
|[Überwachen mit Azure Monitor-Protokollen](./saas-dbpertenant-log-analytics.md) | Erfahren Sie, wie Sie mithilfe von [Azure Monitor-Protokollen](../../azure-monitor/log-query/log-query-overview.md) große Mengen von Ressourcen über mehrere Pools hinweg überwachen können. |
|[Wiederherstellen einzelner Mandanten](./saas-dbpertenant-restore-single-tenant.md)| Hier erfahren Sie, wie Sie eine Mandantendatenbank zu einem früheren Zeitpunkt wiederherstellen. Lernen Sie auch, wie eine parallele Datenbank wiederhergestellt wird, wobei die vorhandene Mandantendatenbank online bleibt. |
|[Manage schema for multiple tenants in the WTP SaaS application](saas-tenancy-schema-management.md) (Verwalten des Schemas für mehrere Mandanten in der SaaS-Anwendung WTP)| Erfahren Sie, wie Sie Schemas und Verweisdaten in allen Mandantendatenbanken aktualisieren. |
|[Ausführen von mandantenübergreifenden verteilten Abfragen](saas-tenancy-cross-tenant-reporting.md) | Erstellen Sie eine Datenbank für Ad-hoc-Analysen, und führen Sie verteilte Echtzeitabfragen für alle Mandanten aus.  |
|[Ausführen von Analysen für extrahierte Mandantendaten](saas-tenancy-tenant-analytics.md) | Extrahieren Sie Mandantendaten in eine Analysedatenbank oder ein Data Warehouse für Offlineanalyseabfragen. |


## <a name="next-steps"></a>Nächste Schritte

- [Allgemeine Anweisungen zum Arbeiten mit den Wingtip-Tickets-Beispielanwendungen für SaaS](saas-tenancy-wingtip-app-guidance-tips.md)
- [Bereitstellen der SaaS-Anwendung Wingtip](./saas-dbpertenant-get-started-deploy.md)