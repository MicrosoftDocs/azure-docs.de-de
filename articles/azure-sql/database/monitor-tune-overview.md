---
title: Überwachen und Optimieren der Datenbankleistung
description: Eine Übersicht über die Funktionen und Methoden zur Überwachung und Leistungsoptimierung in Azure SQL-Datenbank und Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 09/30/2020
ms.openlocfilehash: 3d28cf1a89ff455cfbed26aad234607ec8cd7e63
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490566"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Überwachung und Leistungsoptimierung in Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Um die Leistung einer Datenbank in Azure SQL-Datenbank und Azure SQL Managed Instance zu überwachen, beginnen Sie mit der Überwachung der CPU und der E/A-Ressourcen, die von der Arbeitsauslastung in Bezug auf den Grad der Datenbankleistung verwendet werden, den Sie bei der Auswahl einer bestimmten Dienst- und Leistungsebene ausgewählt haben. Um dies zu erreichen, geben Azure SQL-Datenbank und Azure SQL Managed Instance Ressourcenmetriken aus, die im Azure-Portal oder mithilfe eines dieser SQL Server-Verwaltungstools angezeigt werden können: [Azure Data Studio](/sql/azure-data-studio/what-is) oder [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL-Datenbank bietet eine Reihe von Database Advisors, mit denen Sie Empfehlungen zur Leistungsoptimierung und automatische Optimierungsoptionen für eine bessere Leistung erhalten. Darüber hinaus zeigt Query Performance Insight Details zu den Abfragen an, die für den Großteil der CPU- und E/A-Auslastung für Singletons und Pooldatenbanken verantwortlich sind.

Azure SQL-Datenbank und Azure SQL Managed Instance bieten verbesserte Überwachungs- und Optimierungsfunktionen, die von künstlicher Intelligenz unterstützt werden, um Ihnen bei der Problembehandlung und Maximierung der Leistung Ihrer Datenbanken und Lösungen zu helfen. Sie können den [Streamingexport](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) dieser [Intelligent Insights](intelligent-insights-overview.md) und andere Ressourcenprotokolle und Metriken der Datenbank in eines von mehreren Zielen für die Nutzung und Analyse konfigurieren, insbesondere mithilfe von [SQL-Analyse](../../azure-monitor/insights/azure-sql.md). Azure SQL-Analyse ist eine erweiterte Cloudüberwachungslösung zum abonnementübergreifenden Überwachen der Leistung Ihrer gesamten Datenbankinstanzen im großen Stil in einer zentralen Ansicht. Eine Liste der Protokolle und Metriken, die Sie exportieren können, finden Sie unter [Diagnosetelemetriedaten für den Export](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export).

SQL Server verfügt mit dem [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) und [dynamischen Verwaltungssichten](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) (Dynamic Management Views, DMVs) über eigene Überwachungs- und Diagnosefunktionen, die von SQL-Datenbank und SQL Managed Instance genutzt werden. Weitere Informationen dazu, wie Skripts eine Vielzahl von Leistungsproblemen überwachen können, finden Sie unter [Überwachen mit dynamischen Verwaltungssichten](monitoring-with-dmvs.md).

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Überwachungs- und Optimierungsfunktionen im Azure-Portal

Im Azure-Portal ermöglichen Azure SQL-Datenbank und Azure SQL Managed Instance die Überwachung von Ressourcenmetriken. Azure SQL-Datenbank stellt Database Advisors zur Verfügung, und Query Performance Insight bietet Empfehlungen zur Abfrageoptimierung und eine Leistungsanalyse für Abfragen. Sie können im Azure-Portal eine automatische Optimierung für [logische SQL-Server](logical-servers.md) und deren Einzel- und Pooldatenbanken aktivieren.

> [!NOTE]
> Datenbanken mit extrem geringer Auslastung werden im Portal möglicherweise mit einem geringeren Verbrauch als dem tatsächlichen angezeigt. Aufgrund der Art, wie Telemetriedaten ausgegeben werden, wenn ein Double-Wert in den nächsten Integer-Wert konvertiert wird, werden bestimmte Verbrauchswerte unter 0,5 auf 0 gerundet. Dies kann bei den ausgegebenen Telemetriedaten zu einer verminderten Genauigkeit führen. Weitere Einzelheiten finden Sie unter [Niedrige Metriken für Datenbanken und Pools für elastische Datenbanken werden auf 0 (null) gerundet](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Ressourcenüberwachung für Azure SQL-Datenbank und Azure SQL Managed Instance

In der Ansicht **Metriken** im Azure-Portal können Sie eine Vielzahl von Ressourcenmetriken schnell überwachen. Mithilfe dieser Metriken können Sie festzustellen, ob eine Datenbank 100 % der Prozessor-, Arbeitsspeicher-oder E/A-Ressourcen erreicht. Ein hoher DTU- oder Prozessor-Prozentsatz sowie ein hoher E/A-Prozentsatz weisen darauf hin, dass Ihre Workload möglicherweise mehr CPU- oder E/A-Ressourcen benötigt. Ggf. weist sie aber auf Abfragen hin, die optimiert werden müssen.

  ![Ressourcenmetriken](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Database Advisors in Azure SQL-Datenbank

Azure SQL-Datenbank umfasst [Database Advisors](database-advisor-implement-performance-recommendations.md), die Empfehlungen zur Leistungsoptimierung für Einzel- und Pooldatenbanken bieten. Diese Empfehlungen sind sowohl im Azure-Portal als auch mithilfe von [PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor) verfügbar. Aktivieren Sie die [automatische Optimierung](automatic-tuning-overview.md), damit diese Optimierungsempfehlungen von Azure SQL-Datenbank automatisch implementiert werden können.

### <a name="query-performance-insight-in-azure-sql-database"></a>Query Performance Insight in Azure SQL-Datenbank

[Query Performance Insight](query-performance-insight-use.md) zeigt im Azure-Portal die Leistung der Abfragen für Singletons und Pooldatenbanken mit dem höchsten Verbrauch und der längsten Ausführungsdauer an.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Niedrige Metriken für Datenbanken und Pools für elastische Datenbanken werden auf 0 (null) gerundet

Ab September 2020 werden Datenbanken mit extrem geringer Auslastung im Portal möglicherweise mit einem geringeren Verbrauch als dem tatsächlichen angezeigt. Aufgrund der Art, wie Telemetriedaten ausgegeben werden, wenn ein Double-Wert in den nächsten Integer-Wert konvertiert wird, werden bestimmte Verbrauchswerte unter 0,5 auf 0 gerundet. Dies kann bei den ausgegebenen Telemetriedaten zu einer verminderten Genauigkeit führen.

Beispiel: Gehen Sie von einem 1-minütigen Fenster mit den folgenden vier Datenpunkten aus: 0,1, 0,1, 0,1 und 0,1. Diese niedrigen Werte werden auf 0, 0, 0 und 0 abgerundet und ergeben damit den Durchschnittswert 0. Wenn einer der Datenpunkte größer als 0,5 ist, z. B. 0,1, 0,1, 0,9 und 0,1, werden die Werte auf 0, 0, 1 und 0 gerundet, sodass als Durchschnitt 0,25 berechnet wird.

Betroffene Metriken bei Datenbanken:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Betroffene Metriken bei Pools für elastische Datenbanken:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generieren intelligenter Bewertungen von Leistungsproblemen

[Intelligent Insights](intelligent-insights-overview.md) für Azure SQL-Datenbank und Azure SQL Managed Instance überwacht kontinuierlich die Datenbanknutzung durch künstliche Intelligenz und ermittelt Störungen, die zu schlechter Leistung führen. Intelligent Insights erkennt automatisch Leistungsprobleme mit Datenbanken auf Grundlage von Wartezeiten der Abfrageausführung, Fehlern oder Timeouts. Nach dem Ermitteln wird eine detaillierte Analyse durchgeführt, die ein Ressourcenprotokoll (SQLInsights) mit einer [intelligenten Bewertung des Problems](intelligent-insights-troubleshoot-performance.md) generiert. Die Bewertung besteht aus einer Analyse der Grundursache des Leistungsproblems der Datenbank und nach Möglichkeit Empfehlungen für Leistungsverbesserungen.

Bei Intelligent Insights handelt es sich um eine einzigartige Funktion der in Azure integrierten Logik, die folgende Werte bereitstellt:

- Proaktive Überwachung
- Angepasste Einblicke in die Leistung
- Frühe Erkennung von Leistungsminderungen der Datenbank
- Analyse der Grundursache der erkannten Probleme
- Empfehlungen für Leistungsverbesserungen
- Funktion für das Aufskalieren von einigen Hunderttausend Datenbanken
- Positive Auswirkung auf DevOps-Ressourcen und die Gesamtkosten

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Aktivieren des Streamingexports von Metriken und Ressourcenprotokollen

Sie können den [Streamingexport der Diagnosetelemetrie](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) in eines von mehreren Zielen aktivieren und konfigurieren, einschließlich des Intelligent Insights-Ressourcenprotokolls. Verwenden Sie [SQL Analytics](../../azure-monitor/insights/azure-sql.md) und andere Funktionen, um diese zusätzliche Diagnosetelemetrie zu nutzen und dadurch Leistungsprobleme zu identifizieren und zu beheben.

Konfigurieren Sie Diagnoseeinstellung zum Streamen von Kategorien von Metriken und Ressourcenprotokollen für Singletons, Pooldatenbanken, Pools für elastische Datenbanken, verwaltete Instanzen und Instanzdatenbanken an eine der folgenden Azure-Ressourcen.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics-Arbeitsbereich in Azure Monitor

Sie können Metriken und Ressourcenprotokolle an einen [Log Analytics-Arbeitsbereich in Azure Monitor](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) streamen. Hier gestreamte Daten können von [SQL Analytics](../../azure-monitor/insights/azure-sql.md) genutzt werden. Dabei handelt es sich um eine auf die Cloud beschränkte Überwachungslösung. Sie ermöglicht die intelligente Überwachung Ihrer Datenbanken mit Leistungsberichten, Warnungen und Empfehlungen zur Risikominderung. Die an einen Log Analytics-Arbeitsbereich gestreamten Daten können zusammen mit anderen erfassten Überwachungsdaten analysiert werden. Sie ermöglichen Ihnen außerdem die Nutzung anderer Azure Monitor-Features wie Warnungen und Visualisierungen.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Sie können Metriken und Ressourcenprotokolle in [Azure Event Hubs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) streamen. Streamen Sie Diagnosetelemetrie an Event Hubs, um folgende Funktionalität bereitzustellen:

- **Streamen von Protokollen in Protokollierungs- und Telemetriesystemen von Drittanbietern**

  Streamen Sie alle Ihre Metriken und Ressourcenprotokolle an einen einzelnen Event Hub, um Protokolldaten an ein SIEM- oder Protokollanalysetool eines Drittanbieters weiterzureichen.
- **Erstellen einer benutzerdefinierten Telemetrie- und Protokollierungsplattform**

  Event Hubs ermöglichen dank des hochgradig skalierbaren Veröffentlichen-Abonnieren-Musters eine flexible Erfassung der Metriken und Ressourcenprotokolle auf einer benutzerdefinierten Telemetrieplattform. Ausführliche Informationen finden Sie unter [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) (Entwerfen und Dimensionieren einer globalen Telemetrieplattform in Azure Event Hubs).
- **Anzeigen der Dienstintegrität durch Streamen von Daten an Power BI**

  Verwenden Sie Event Hubs, Stream Analytics und Power BI, um sich anhand Ihrer Diagnosedaten nahezu in Echtzeit einen Einblick in Ihre Azure-Dienste verschaffen. Details zu dieser Lösung finden Sie unter [Stream Analytics und Power BI: Ein Dashboard zur Echtzeitanalyse von Streamingdaten](../../stream-analytics/stream-analytics-power-bi-dashboard.md).

### <a name="azure-storage"></a>Azure Storage

Streamen Sie Metriken und Ressourcenprotokolle in [Azure Storage](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage). Verwenden Sie Azure Storage zum Archivieren großer Mengen von Diagnosetelemetriedaten zu einem Bruchteil der Kosten der beiden vorherigen Streamingoptionen.

## <a name="use-extended-events"></a>Verwenden erweiterter Ereignisse 

Die [erweiterten Ereignisse](/sql/relational-databases/extended-events/extended-events) in SQL Server bieten Ihnen eine erweiterte Überwachung und Problembehandlung. Die Funktionalität der erweiterten Ereignisse ermöglicht es Benutzern, Daten in dem Umfang zu sammeln, der erforderlich ist, um Leistungsproblem zu erkennen oder zu beheben. Informationen zur Verwendung erweiterter Ereignisse in Azure SQL-Datenbank finden Sie unter [Erweiterte Ereignisse in Azure SQL-Datenbank](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu intelligenten Empfehlungen zur Leistung für Einzel- und Pooldatenbanken finden Sie unter [Database Advisor: Empfehlungen zur Leistung](database-advisor-implement-performance-recommendations.md).
- Weitere Informationen zum automatischen Überwachen der Datenbankleistung mit der automatisierten Diagnose und der Ursachenanalyse von Leistungsproblemen finden Sie unter [Azure SQL Intelligent Insights](intelligent-insights-overview.md).