---
title: Überwachung – Azure Database for MariaDB
description: In diesem Artikel werden die Überwachungs- und Warnmetriken (CPU, Speicher, Verbindungsstatistiken und Ähnliches) für Azure Database for MariaDB beschrieben.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 7420a7c6355fbcd70ce57fbb36ab29d1241df31f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453319"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Überwachung in Azure Database for MariaDB
Die Überwachung der Daten zu Ihren Servern unterstützt Sie bei der Problembehandlung und der Optimierung Ihrer Workloads. Azure Database for MariaDB bietet verschiedene Metriken, die Einblicke in das Verhalten Ihres Servers ermöglichen.

## <a name="metrics"></a>Metriken
Alle Azure-Metriken werden im Minutentakt erfasst, und für jede Metrik steht ein Verlauf von 30 Tagen zur Verfügung. Sie können Warnungen für die Metriken konfigurieren. Darüber hinaus können weitere Aufgaben wie das Einrichten automatisierter Aktionen, das Durchführen erweiterter Analysen und das Archivieren des Verlaufs ausgeführt werden. Weitere Informationen finden Sie unter [Überblick über Metriken in Microsoft Azure](../azure-monitor/platform/data-platform.md).

Eine Schritt-für-Schritt-Anleitung finden Sie unter [Use the Azure portal to set up alerts on metrics for Azure Database for PostgreSQL](howto-alert-metric.md) (Verwenden des Azure-Portals zum Einrichten von Warnungen zu Metriken für Azure Database for PostgreSQL).

### <a name="list-of-metrics"></a>Liste der Metriken
Für Azure Database for MariaDB sind folgende Metriken verfügbar:

|Metrik|Metrikanzeigename|Einheit|BESCHREIBUNG|
|---|---|---|---|
|cpu_percent|CPU in Prozent|Percent|Die CPU-Auslastung in Prozent|
|memory_percent|Arbeitsspeicher in Prozent|Percent|Die Arbeitsspeicherauslastung in Prozent|
|io_consumption_percent|E/A in Prozent|Percent|Die E/A-Auslastung in Prozent (Gilt nicht für Server im Tarif „Basic“.)|
|storage_percent|Speicher in Prozent|Percent|Der verwendete Speicher relativ zum Maximalwert des Servers (in Prozent)|
|storage_used|Verwendeter Speicher|Byte|Die Menge des verwendeten Speichers. Der vom Dienst verwendete Speicher kann die Datenbankdateien, Transaktionsprotokolle und Serverprotokolle umfassen.|
|serverlog_storage_percent|Serverprotokollspeicher in Prozent|Percent|Der Prozentsatz des Serverprotokollspeichers, der aus dem maximalen Serverprotokollspeicher des Servers verwendet wird.|
|serverlog_storage_usage|Verwendeter Serverprotokollspeicher|Byte|Die Menge des verwendeten Serverprotokollspeichers.|
|serverlog_storage_limit|Begrenzung des Serverprotokollspeichers|Byte|Der maximale Serverprotokollspeicher für diesen Server.|
|storage_limit|Speicherbegrenzung|Byte|Der maximale Speicher für diesen Server|
|active_connections|Die aktiven Verbindungen.|Anzahl|Die Anzahl aktiver Verbindungen mit dem Server|
|connections_failed|Verbindungsfehler|Anzahl|Die Anzahl von Verbindungsfehlern für den Server|
|seconds_behind_master|Replikationsverzögerung in Sekunden|Anzahl|Die Anzahl von Sekunden, die die Verzögerung des Replikatservers im Vergleich zum Quellserver angeben. (Gilt nicht für Server im Tarif „Basic“.)|
|network_bytes_egress|Netzwerk ausgehend|Byte|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen.|
|network_bytes_ingress|Netzwerk eingehend|Byte|Eingehender Netzwerkdatenverkehr über aktive Verbindungen.|
|backup_storage_used|Verwendeter Sicherungsspeicher|Byte|Die Menge des verwendeten Sicherungsspeichers. Diese Metrik stellt den gesamten Speicherplatz dar, der von allen vollständigen Datenbanksicherungen, differenziellen Sicherungen und Protokollsicherungen beansprucht wurde, die auf der Grundlage der für den Server festgelegten Beibehaltungsdauer für Sicherungen aufbewahrt wurden. Die Häufigkeit der Sicherungen wird durch den Dienst verwaltet und im Artikel zu [Konzepten](concepts-backup.md) erläutert. Bei georedundantem Speicher wird doppelt so viel Sicherungsspeicher genutzt wie bei lokal redundantem Speicher.|

## <a name="server-logs"></a>Serverprotokolle

Sie können die Protokollierung von langsamen Abfragen auf Ihrem Server aktivieren. Diese Protokolle sind ebenfalls durch Azure-Diagnoseprotokolle in Azure Monitor-Protokolle, Event Hubs und im Speicherkonto verfügbar. Weitere Informationen zur Protokollierung finden Sie auf der Seite [Serverprotokolle](concepts-server-logs.md).

## <a name="query-store"></a>Abfragespeicher

Der [Abfragespeicher](concepts-query-store.md) dient dazu, die Abfrageleistung im Zeitablauf zu verfolgen, einschließlich Statistiken zur Abfrageausführungszeit und Warteereignissen. Das Feature speichert Informationen zur Laufzeitleistung der Abfrage im **mysql**-Schema. Sie können die Sammlung und Speicherung von Daten über verschiedene Konfigurationsoptionen steuern.

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) arbeitet mit dem Abfragespeicher zusammen, um Visualisierungen bereitzustellen, auf die über das Azure-Portal zugegriffen werden kann. Diese Diagramme ermöglichen es Ihnen, wichtige Abfragen zu identifizieren, die sich auf die Leistung auswirken. Query Performance Insight ist im Abschnitt **Intelligente Leistung** auf der Portalseite Ihres Azure Database for MariaDB-Servers verfügbar.

## <a name="performance-recommendations"></a>Leistungsempfehlungen

Das Feature [Leistungsempfehlungen](concepts-performance-recommendations.md) identifiziert Möglichkeiten zur Verbesserung der Workloadleistung. Unter „Leistungsempfehlungen“ erhalten Sie Empfehlungen zum Erstellen neuer Indizes, mit denen sich die Leistung Ihrer Workloads u. U. verbessern lässt. Um Indexempfehlungen zu generieren, berücksichtigt das Feature verschiedene Datenbankmerkmale einschließlich des Schemas und der Workload laut Abfragespeicher. Nach der Implementierung von Leistungsempfehlungen sollten Kunden die Leistung testen, um die Auswirkungen dieser Änderungen auszuwerten.

## <a name="planned-maintenance-notification"></a>Benachrichtigungen zu geplanten Wartungen

[Benachrichtigungen zu geplanten Wartungen](./concepts-planned-maintenance-notification.md) ermöglichen Ihnen das Empfangen von Warnungen für anstehende geplante Wartungsarbeiten an Azure Database for MariaDB. Diese Benachrichtigungen sind in die geplante Wartung von [Service Health](../service-health/overview.md) integriert, sodass Sie alle geplanten Wartungsarbeiten für Ihre Abonnements an zentraler Stelle anzeigen können. Außerdem ist es hilfreich, die Benachrichtigungen an die richtigen Zielgruppen für verschiedene Ressourcengruppen zu richten, da möglicherweise unterschiedliche Ansprechpartner für verschiedene Ressourcen zuständig sind. Sie erhalten die Benachrichtigung über die anstehende Wartung 72 Stunden vor dem Ereignis.

Im Dokument [Benachrichtigungen zu geplanten Wartungen](./concepts-planned-maintenance-notification.md) finden Sie weitere Informationen zum Einrichten von Benachrichtigungen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen dazu, wie Sie mit dem Azure-Portal, der REST-API oder der CLI auf Metriken zugreifen bzw. diese exportieren, finden Sie unter [Überblick über Metriken in Microsoft Azure](../azure-monitor/platform/data-platform.md).
- Anleitungen zum Erstellen einer Warnung zu einer Metrik finden Sie unter [Einrichten von Warnungen](howto-alert-metric.md).
- Im Artikel [Benachrichtigungen zu geplanten Wartungen in Azure Database for MariaDB](./concepts-planned-maintenance-notification.md) finden Sie weitere Informationen zum Einrichten von Benachrichtigungen.