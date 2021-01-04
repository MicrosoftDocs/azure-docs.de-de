---
title: Überwachung und Optimierung – Azure Database for PostgreSQL – Einzelserver
description: In diesem Artikel werden die Überwachungs- und Optimierungsfunktionen in Azure Database for PostgreSQL (Einzelserver) beschrieben.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 4d4287b877f5327b7fd485358b26148686b9515b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487924"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Überwachung und Optimierung in Azure Database for PostgreSQL – Einzelserver
Die Überwachung der Daten zu Ihren Servern unterstützt Sie bei der Problembehandlung und der Optimierung Ihrer Workloads. Azure Database for PostgreSQL bietet verschiedene Überwachungsoptionen, um Einblicke in das Verhalten Ihres Servers zu gewähren.

## <a name="metrics"></a>Metriken
Azure Database for PostgreSQL bietet verschiedene Metriken, die Einblicke in das Verhalten der Ressourcen gewähren, die dem PostgreSQL-Server zugrunde liegen. Jede Metrik wird mit einer Frequenz von einer Minute ausgegeben und verfügt über einen [Verlauf von bis zu 93 Tagen](../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics). Sie können Warnungen für die Metriken konfigurieren. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Use the Azure portal to set up alerts on metrics for Azure Database for PostgreSQL](howto-alert-on-metric.md) (Verwenden des Azure-Portals zum Einrichten von Warnungen zu Metriken für Azure Database for PostgreSQL). Darüber hinaus können weitere Aufgaben wie das Einrichten automatisierter Aktionen, das Durchführen erweiterter Analysen und das Archivieren des Verlaufs ausgeführt werden. Weitere Informationen finden Sie unter [Überblick über Metriken in Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="list-of-metrics"></a>Liste der Metriken
Die folgenden Metriken sind für Azure Database for PostgreSQL verfügbar:

|Metrik|Metrikanzeigename|Einheit|BESCHREIBUNG|
|---|---|---|---|
|cpu_percent|CPU in Prozent|Percent|Die CPU-Auslastung in Prozent|
|memory_percent|Arbeitsspeicher in Prozent|Percent|Die Arbeitsspeicherauslastung in Prozent|
|io_consumption_percent|E/A in Prozent|Percent|Die E/A-Auslastung in Prozent (Gilt nicht für Server im Tarif Basic.)|
|storage_percent|Speicher in Prozent|Percent|Der verwendete Speicher relativ zum Maximalwert des Servers (in Prozent)|
|storage_used|Verwendeter Speicher|Byte|Die Menge des verwendeten Speichers. Der vom Dienst verwendete Speicher kann die Datenbankdateien, Transaktionsprotokolle und Serverprotokolle umfassen.|
|storage_limit|Speicherbegrenzung|Byte|Der maximale Speicher für diesen Server|
|serverlog_storage_percent|Serverprotokollspeicher in Prozent|Percent|Der Prozentsatz des Serverprotokollspeichers, der aus dem maximalen Serverprotokollspeicher des Servers verwendet wird.|
|serverlog_storage_usage|Verwendeter Serverprotokollspeicher|Byte|Die Menge des verwendeten Serverprotokollspeichers.|
|serverlog_storage_limit|Begrenzung des Serverprotokollspeichers|Byte|Der maximale Serverprotokollspeicher für diesen Server.|
|active_connections|Die aktiven Verbindungen.|Anzahl|Die Anzahl aktiver Verbindungen mit dem Server|
|connections_failed|Verbindungsfehler|Anzahl|Gibt an, wie viele fehlerhafte Verbindungen hergestellt wurden.|
|network_bytes_egress|Netzwerk ausgehend|Byte|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen.|
|network_bytes_ingress|Netzwerk eingehend|Byte|Eingehender Netzwerkdatenverkehr über aktive Verbindungen.|
|backup_storage_used|Verwendeter Sicherungsspeicher|Byte|Die Menge des verwendeten Sicherungsspeichers. Diese Metrik stellt den gesamten Speicherplatz dar, der von allen vollständigen Datenbanksicherungen, differenziellen Sicherungen und Protokollsicherungen beansprucht wurde, die auf der Grundlage der für den Server festgelegten Beibehaltungsdauer für Sicherungen aufbewahrt wurden. Die Häufigkeit der Sicherungen wird durch den Dienst verwaltet und im Artikel zu [Konzepten](concepts-backup.md) erläutert. Bei georedundantem Speicher wird doppelt so viel Sicherungsspeicher genutzt wie bei lokal redundantem Speicher.|
|pg_replica_log_delay_in_bytes|Maximale Verzögerung zwischen Replikaten|Byte|Die Verzögerung in Bytes zwischen dem primären Server und dem Replikat mit der größten Verzögerung. Diese Metrik steht nur auf dem primären Server zur Verfügung.|
|pg_replica_log_delay_in_seconds|Replikatverzögerung|Sekunden|Die Zeit seit der letzten wiedergegebenen Transaktion. Diese Metrik steht nur für Replikatserver zur Verfügung.|

## <a name="server-logs"></a>Serverprotokolle
Sie können die Protokollierung auf Ihrem Server aktivieren. Diese Ressourcenprotokolle können an [Azure Monitor-Protokolle](../azure-monitor/log-query/log-query-overview.md), Event Hubs und ein Speicherkonto gesendet werden. Weitere Informationen zur Protokollierung finden Sie auf der Seite [Serverprotokolle](concepts-server-logs.md).

## <a name="query-store"></a>Abfragespeicher
Der [Abfragespeicher](concepts-query-store.md) dient dazu, die Abfrageleistung im Zeitablauf zu verfolgen, einschließlich Statistiken zur Abfrageausführungszeit und Warteereignissen. Das Feature speichert Informationen zur Leistung der Abfrageausführungszeit in einer Systemdatenbank namens **azure_sys** unter dem Schema „query_store“ persistent. Sie können die Sammlung und Speicherung von Daten über verschiedene Konfigurationsoptionen steuern.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) arbeitet mit dem Abfragespeicher zusammen, um Visualisierungen bereitzustellen, auf die über das Azure-Portal zugegriffen werden kann. Diese Diagramme ermöglichen es Ihnen, wichtige Abfragen zu identifizieren, die sich auf die Leistung auswirken. „Query Performance Insight“ ist im Abschnitt **Support und Problembehandlung** auf der Portalseite Ihres Azure Database for PostgreSQL-Servers verfügbar.

## <a name="performance-recommendations"></a>Leistungsempfehlungen
Das Feature [Leistungsempfehlungen](concepts-performance-recommendations.md) identifiziert Möglichkeiten zur Verbesserung der Workloadleistung. Unter „Leistungsempfehlungen“ erhalten Sie Empfehlungen zum Erstellen neuer Indizes, mit denen sich die Leistung Ihrer Workloads u. U. verbessern lässt. Um Indexempfehlungen zu generieren, berücksichtigt das Feature verschiedene Datenbankmerkmale einschließlich des Schemas und der Workload laut Abfragespeicher. Nach der Implementierung von Leistungsempfehlungen sollten Kunden die Leistung testen, um die Auswirkungen dieser Änderungen auszuwerten. 

## <a name="planned-maintenance-notification"></a>Benachrichtigungen zu geplanten Wartungen

[Benachrichtigungen zu geplanten Wartungen](./concepts-planned-maintenance-notification.md) ermöglichen Ihnen das Empfangen von Warnungen für anstehende geplante Wartungsarbeiten an Azure Database for PostgreSQL – Einzelner Server. Diese Benachrichtigungen sind in die geplante Wartung von [Service Health](../service-health/overview.md) integriert, sodass Sie alle geplanten Wartungsarbeiten für Ihre Abonnements an zentraler Stelle anzeigen können. Außerdem ist es hilfreich, die Benachrichtigungen an die richtigen Zielgruppen für verschiedene Ressourcengruppen zu richten, da möglicherweise unterschiedliche Ansprechpartner für verschiedene Ressourcen zuständig sind. Sie erhalten die Benachrichtigung über die anstehende Wartung 72 Stunden vor dem Ereignis.

Im Dokument [Benachrichtigungen zu geplanten Wartungen](./concepts-planned-maintenance-notification.md) finden Sie weitere Informationen zum Einrichten von Benachrichtigungen.

## <a name="next-steps"></a>Nächste Schritte
- Anleitungen zum Erstellen einer Warnung zu einer Metrik finden Sie unter [Einrichten von Warnungen](howto-alert-on-metric.md).
- Weitere Informationen dazu, wie Sie mit dem Azure-Portal, der REST-API oder der CLI auf Metriken zugreifen bzw. diese exportieren, finden Sie unter [Überblick über Metriken in Microsoft Azure](../azure-monitor/platform/data-platform.md).
- Lesen Sie unseren Blog zu [Best Practices für die Überwachung Ihres Servers](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/) (in englischer Sprache).
- Informieren Sie sich über [Benachrichtigungen bei geplanten Wartungen](./concepts-planned-maintenance-notification.md) in Azure Database for PostgreSQL (Einzelserver).