---
title: Überwachung – Azure Database for MySQL – Flexible Server
description: In diesem Artikel werden die Überwachungs- und Warnmetriken (CPU, Speicher, Verbindungsstatistiken und Ähnliches) für Azure Database for MySQL Flexible Server beschrieben.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 5fe1d5a5a472b47abd364a89d1a65f1249c67c0d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538662"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Überwachen von Azure Database for MySQL Flexible Server mit integrierten Metriken

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server ermöglicht die Überwachung von Servern über Azure Monitor. Metriken sind numerische Werte, die einen Aspekt der Ressourcen Ihres Servers zu einem bestimmten Zeitpunkt beschreiben. Die Überwachung der Ressourcen Ihres Servers hilft Ihnen bei der Fehlerbehebung und Optimierung Ihrer Workload, indem Sie überwachen können, was für Sie am wichtigsten ist. Die Überwachung der richtigen Metriken hilft Ihnen, die Leistung, Zuverlässigkeit und Verfügbarkeit Ihres Servers und Ihrer Anwendungen aufrechtzuerhalten.

In diesem Artikel erfahren Sie mehr über die verschiedenen Metriken, die für Ihren flexiblen Server verfügbar sind und Erkenntnisse zum Verhalten Ihres Servers bieten.

## <a name="available-metrics"></a>Verfügbare Metriken

Azure Database for MySQL Flexible Server bietet verschiedene Metriken, um die Leistung Ihrer Workload zu verstehen. Anhand dieser Daten können Sie die Auswirkungen auf Ihren Server und Ihre Anwendung nachvollziehen. Sie können z. B. bei flexiblen Servern **Host-CPU in Prozent** , **Aktive Verbindungen** , **E/A in Prozent** und **Hostarbeitsspeicher in Prozent** überwachen, um festzustellen, wann eine Leistungsbeeinträchtigung auftritt. Von dort aus müssen Sie möglicherweise Ihre Workload optimieren, vertikal skalieren, indem Sie die Computeebenen ändern, oder horizontal skalieren, indem Sie ein Lesereplikat verwenden.

Alle Azure-Metriken werden im Minutentakt erfasst, und für jede Metrik steht ein Verlauf von 30 Tagen zur Verfügung. Sie können Warnungen für die Metriken konfigurieren. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Einrichten von Warnungen](./how-to-alert-on-metric.md). Darüber hinaus können weitere Aufgaben wie das Einrichten automatisierter Aktionen, das Durchführen erweiterter Analysen und das Archivieren des Verlaufs ausgeführt werden. Weitere Informationen finden Sie unter [Überblick über Metriken in Microsoft Azure](../../azure-monitor/platform/data-platform.md).

### <a name="list-of-metrics"></a>Liste der Metriken
Für Azure Database for MySQL sind folgende Metriken verfügbar:

|Anzeigename der Metrik|Metrik|Einheit|BESCHREIBUNG|
|---|---|---|---|
|Host-CPU in Prozent|cpu_percent|Percent|Der Prozentsatz der CPU-Auslastung auf dem Server, einschließlich der CPU-Auslastung durch Kundenworkload und Azure MySQL-Prozesse.|
|Hostnetzwerk eingehend |network_bytes_ingress|Byte|Eingehender Netzwerkdatenverkehr auf dem Server, einschließlich des Datenverkehrs sowohl von der Kundendatenbank als auch von Azure MySQL-Features wie Replikation, Überwachung, Protokolle usw.|
|Hostnetzwerk ausgehend|network_bytes_egress|Byte|Ausgehender Netzwerkdatenverkehr auf dem Server, einschließlich des Datenverkehrs sowohl von der Kundendatenbank als auch von Azure MySQL-Features wie Replikation, Überwachung, Protokolle usw.|
|Verzögerung bei der Replikation|replication_lag|Sekunden|Die Zeit seit der letzten wiedergegebenen Transaktion. Diese Metrik steht nur für Replikatserver zur Verfügung.|
|Die aktiven Verbindungen.|active_connection|Anzahl|Die Anzahl aktiver Verbindungen mit dem Server|
|Verwendeter Sicherungsspeicher|backup_storage_used|Byte|Die Menge des verwendeten Sicherungsspeichers.|
|E/A in Prozent|io_consumption_percent|Percent|Die E/A-Auslastung in Prozent|
|Hostarbeitsspeicher in Prozent|memory_percent|Percent|Der prozentuale Anteil des auf dem Server genutzten Arbeitsspeichers, einschließlich der Speicherauslastung sowohl durch Kundenworkload als auch durch Azure MySQL-Prozesse.|
|Speicherbegrenzung|storage_limit|Byte|Der maximale Speicher für diesen Server|
|Speicher in Prozent|storage_percent|Percent|Der verwendete Speicher relativ zum Maximalwert des Servers (in Prozent)|
|Verwendeter Speicher|storage_used|Byte|Die Menge des verwendeten Speichers. Der vom Dienst verwendete Speicher kann die Datenbankdateien, Transaktionsprotokolle und Serverprotokolle umfassen.|
|Verbindungen gesamt|total_connections|Anzahl|Die Anzahl der gesamten Verbindungen zum Server.|
|Abgebrochene Verbindungen|aborted_connections|Anzahl|Die Anzahl der fehlerhaften Versuche, eine Verbindung mit MySQL herzustellen, z. B. fehlerhafte Verbindung aufgrund ungültiger Anmeldeinformationen.|
|Abfragen|Abfragen|Anzahl|Die Anzahl der Abfragen pro Sekunde.|

## <a name="next-steps"></a>Nächste Schritte
- Anleitungen zum Erstellen einer Warnung zu einer Metrik finden Sie unter [Einrichten von Warnungen](./how-to-alert-on-metric.md).
- Erfahren Sie mehr über die [Skalierung von IOPS](./concepts/../concepts-compute-storage.md#iops) zur Verbesserung der Leistung.