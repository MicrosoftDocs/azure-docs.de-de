---
title: Verwaltbarkeit und Überwachung – Abfrageaktivität, Ressourcennutzung
description: Hier erfahren Sie, welche Funktionen zum Verwalten und Überwachen von Azure Synapse Analytics zur Verfügung stehen. Verwenden Sie das Azure-Portal und dynamische Verwaltungssichten (DMVs), um Informationen zu Abfrageaktivität und Ressourcennutzung für Ihre Data Warehouse-Instanz zu erhalten.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f66efb9112a9342122f5b56ab11b862ce3c7c61b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314438"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Überwachen der Ressourcennutzung und Abfrageaktivität in Azure Synapse Analytics

Azure Synapse Analytics bietet umfassende Überwachungsfunktionen im Azure-Portal, um Erkenntnisse zu Ihrer Data Warehouse-Workload zu gewinnen. Das Azure-Portal ist das empfohlene Tool zum Überwachen Ihrer Data Warehouse-Instanz, weil es eine konfigurierbare Aufbewahrungsdauer, Warnungen, Empfehlungen und anpassbare Diagramme und Dashboards für Metriken und Protokolle bietet. Das Portal ermöglicht außerdem eine Integration weiterer Azure-Überwachungsdienste – z. B. Azure Monitor (Protokolle) mit Log Analytics, um Ihnen eine umfassende und integrierte Überwachungsoberfläche für Data Warehouse sowie für Ihre gesamte Azure-Analyseplattform zu bieten. In dieser Dokumentation wird beschrieben, welche Überwachungsfunktionen zur Verfügung stehen, um Ihre Analyseplattform mit Synapse SQL zu optimieren und zu verwalten.

## <a name="resource-utilization"></a>Ressourcenverwendung

Im Azure-Portal stehen die folgenden Metriken für Synapse SQL zur Verfügung. Diese Metriken werden über [Azure Monitor](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics) angegeben.

| Metrikname             | BESCHREIBUNG                                                  | Aggregationstyp |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU-Prozentsatz          | CPU-Auslastung für alle Knoten der Data Warehouse-Instanz      | Durchschnitt, Minimum, Maximum    |
| E/A-Prozentsatz für Daten      | E/A-Auslastung für alle Knoten der Data Warehouse-Instanz       | Durchschnitt, Minimum, Maximum    |
| Arbeitsspeicherprozentsatz       | Arbeitsspeicherauslastung (SQL Server) für alle Knoten der Data Warehouse-Instanz | Durchschnitt, Minimum, Maximum   |
| Aktive Abfragen          | Anzahl aktiver Abfragen, die auf dem System ausgeführt werden             | SUM              |
| Abfragen in Warteschlange          | Anzahl von Abfragen in der Warteschlange, die auf die Ausführung warten          | SUM              |
| Erfolgreiche Verbindungen  | Anzahl erfolgreicher Verbindungen (Anmeldungen) mit der Datenbank | Summe, Anzahl       |
| Verbindungsfehler      | Anzahl fehlerhafter Verbindungen (Anmeldungen) mit der Datenbank | Summe, Anzahl       |
| Von der Firewall blockiert     | Anzahl von blockierten Anmeldungen bei Data Warehouse     | Summe, Anzahl       |
| DWU-Grenzwert               | Servicelevelziel für Data Warehouse                | Durchschnitt, Minimum, Maximum    |
| DWU in Prozent          | Höchstwert zwischen CPU-Prozentsatz und E/A-Prozentsatz für Daten        | Durchschnitt, Minimum, Maximum    |
| DWU-Verbrauch                | DWU-Limit × DWU-Prozentsatz                                   | Durchschnitt, Minimum, Maximum    |
| Prozentsatz der Cachetreffer    | (Cachetreffer/Cachefehler) × 100, wobei die Cachetreffer die Summe aller Columnstore-Segmenttreffer im lokalen SSD-Cache und die Cachefehler die Columnstore-Segmentfehler im lokalen SSD-Cache repräsentieren, summiert über alle Knoten | Durchschnitt, Minimum, Maximum    |
| Cacheverwendung in Prozent   | (Cacheverwendung/Cachekapazität) × 100, wobei die Cacheverwendung die Summe aller Bytes im lokalen SSD-Cache für alle Knoten darstellt und die Cachekapazität die Summe der Speicherkapazität im lokalen SSD-Cache für alle Knoten repräsentiert | Durchschnitt, Minimum, Maximum    |
| Lokaler tempdb-Prozentsatz | Lokale tempdb-Auslastung für alle Computeknoten, Werte werden alle fünf Minuten ausgegeben | Durchschnitt, Minimum, Maximum    |

Beim Anzeigen von Metriken und Festlegen von Warnungen zu berücksichtigende Aspekte:

- Die verwendete DWU stellt nur eine **allgemeine Darstellung der Nutzung** im SQL-Pool dar und soll nicht als umfassender Indikator der Auslastung dienen. Um zu ermitteln, ob zentral hoch- oder herunterskaliert werden soll, sollten Sie alle Faktoren berücksichtigen, die von DWU betroffen sein können, etwa Parallelität, Arbeitsspeicher, tempdb und adaptive Cachekapazität. Es wird empfohlen, [ihre Workload mit verschiedenen DWU-Einstellungen](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) auszuführen, um zu ermitteln, was für Ihre Geschäftsziele am besten funktioniert.
- Fehlerhafte und erfolgreiche Verbindungen werden für ein bestimmtes Data Warehouse gemeldet – nicht für den Server selbst.
- Der Prozentsatz des Arbeitsspeichers spiegelt die Auslastung auch dann wider, wenn sich das Data Warehouse im Leerlauf befindet – er gibt nicht die Speichernutzung durch die aktive Workload wieder. Verwenden Sie diese Metrik zusammen mit anderen (tempdb, Gen2-Cache), und verfolgen Sie sie, um eine ganzheitliche Entscheidung darüber zu treffen, ob eine Skalierung auf zusätzliche Cache Kapazität die Workloadleistung entsprechend Ihren Anforderungen steigert.

## <a name="query-activity"></a>Abfrageaktivität

Für eine programmgesteuerte Benutzeroberfläche bei der Überwachung von Synapse SQL über T-SQL bietet der Dienst verschiedene dynamische Verwaltungssichten (Dynamic Management Views, DMVs). Diese Sichten sind nützlich für die aktive Problembehandlung und das Identifizieren von Leistungsengpässen in Ihrer Workload.

Eine Liste der DMVs, die für Synapse SQL gelten, finden Sie in dieser [Dokumentation](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Protokollierung von Metriken und Diagnosedaten 

Sowohl Metriken als auch Protokolle können in Azure Monitor exportiert werden, insbesondere die Komponente [Azure Monitor-Protokolle](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), und auf sie kann programmgesteuert über [Protokollabfragen](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zugegriffen werden. Die Protokollwartezeit für Synapse SQL beträgt ca. 10 bis 15 Minuten. Weitere Informationen zu den Faktoren, die sich auf die Wartezeit auswirken, finden Sie in der folgenden Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

Die folgende Schrittanleitung beschreibt gängige Szenarien und Anwendungsfälle beim Überwachen und Verwalten Ihrer Data Warehouse-Instanz:

- [Überwachen Ihrer Data Warehouse-Workload mit DMVs](sql-data-warehouse-manage-monitor.md)
