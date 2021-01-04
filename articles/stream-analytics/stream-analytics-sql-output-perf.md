---
title: Erhöhen der Durchsatzleistung zu Azure SQL-Datenbank aus Azure Stream Analytics
description: Erfahren Sie mehr über die Ausgabe von Daten aus Azure Stream Analytics in SQL Azure, und erzielen Sie höhere Durchsatzraten für Schreibvorgänge.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: a902cbc8256e249a608a5da8436a1145ddb0d565
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490932"
---
# <a name="increase-throughput-performance-to-azure-sql-database-from-azure-stream-analytics"></a>Erhöhen der Durchsatzleistung zu Azure SQL-Datenbank aus Azure Stream Analytics

In diesem Artikel werden Tipps zum Erzielen einer höheren Durchsatzleistung bei Schreibvorgängen erörtert, wenn Sie Daten mit Azure Stream Analytics in SQL Azure-Datenbank laden.

Bei der SQL-Ausgabe in Azure Stream Analytics werden parallele Schreibvorgänge als Option unterstützt. Diese Option ermöglicht [vollständig parallele](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) Auftragstopologien, in denen mehrere Ausgabepartitionen gleichzeitig in die Zieltabelle schreiben. Allerdings reicht das Aktivieren dieser Option in Azure Stream Analytics möglicherweise nicht aus, um einen höheren Durchsatz zu erzielen, weil sie erheblich von Ihrer Datenbankkonfiguration und vom Tabellenschema abhängt. Die Auswahl von Indizes, Gruppierungsschlüssel, Indexfüllfaktor und Komprimierung besitzt Auswirkungen auf die Dauer von Tabellenladevorgängen. Weitere Informationen zum Optimieren Ihrer Datenbank, um die Abfrage- und Ladeleistung basierend auf internen Benchmarks zu verbessern, finden Sie unter [Leistungsoptimierungen bei Anwendungen und Datenbanken in Azure SQL-Datenbank und Azure SQL Managed Instance](../azure-sql/database/performance-guidance.md). Die Reihenfolge der Schreibvorgänge ist beim parallelen Schreiben in SQL Azure-Datenbank nicht gewährleistet.

Hier sind einige Konfigurationen innerhalb der einzelnen Dienste aufgeführt, mit denen Sie den Durchsatz Ihrer Lösung insgesamt verbessern können.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Partitionierung erben** – Diese Konfigurationsoption für die SQL-Ausgabe ermöglicht das Erben des Partitionierungsschemas des vorherigen Abfrageschritts oder der vorherigen Eingabe. Wenn diese Option aktiviert ist, sind beim Schreiben in eine datenträgerbasierte Tabelle und beim Verwenden einer [vollständig parallelen](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) Topologie für Ihren Auftrag bessere Durchsatzwerte zu erwarten. Diese Partitionierung erfolgt für viele weitere [Ausgaben](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs) bereits automatisch. Die Tabellensperre (TABLOCK) wird für mit dieser Option durchgeführte Masseneinfügungen ebenfalls deaktiviert.

> [!NOTE] 
> Wenn mehr als acht Eingabepartitionen vorliegen, ist das Erben des Eingabepartitionierungsschemas möglicherweise keine geeignete Option. Die Obergrenze wurde für eine Tabelle mit einer einzigen Identitätsspalte und einem gruppierten Index festgestellt. Erwägen Sie in diesem Fall die Verwendung von [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 in Ihrer Abfrage, um die Zahl der Ausgabeschreiber explizit anzugeben. Basierend auf Ihrem Schema und Ihrer Auswahl von Indizes können Ihre Beobachtungen abweichen.

- **Batchgröße**: In der SQL-Ausgabekonfiguration können Sie die maximale Batchgröße in einer Azure Stream Analytics-SQL-Ausgabe basierend auf der Natur Ihrer Zieltabelle/Arbeitsauslastung angeben. Die Batchgröße ist die maximale Anzahl von Datensätzen, die bei jeder Masseneinfügungstransaktion gesendet werden. In gruppierten Columnstore-Indizes ermöglichen Batchgrößen von etwa [100.000](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) eine weitere Parallelisierung, minimale Protokollierung sowie Sperroptimierungen. In datenträgerbasierten Tabellen ist möglicherweise ein Wert von höchstens 10.000 (Standard) für Ihre Lösung optimal, weil durch höhere Batchgrößen eine Sperrenausweitung bei Masseneinfügungen ausgelöst werden kann.

- **Optimierung von Eingabemeldungen**: Wenn Sie eine Optimierung mit Partitionierungsvererbung und Batchgrößen durchgeführt haben, können Sie den Schreibdurchsatz durch Erhöhen der Anzahl von Eingabeereignissen pro Meldung pro Partition noch weiter steigern. Die Optimierung von Eingabemeldungen ermöglicht innerhalb von Azure Stream Analytics Batchgrößen bis hin zur angegebenen Batchgröße, was zu einem verbesserten Durchsatz führt. Dies lässt sich mithilfe von [Komprimierung](stream-analytics-define-inputs.md) oder durch Heraufsetzen der Größe von Eingabemeldungen in EventHub oder Blob erreichen.

## <a name="sql-azure"></a>SQL Azure

- **Partitionierte Tabelle und Indizes**: Konflikte zwischen Partitionen während Schreibvorgängen können über eine [partitionierte](/sql/relational-databases/partitions/partitioned-tables-and-indexes) SQL-Tabelle und partitionierte Indizes in der Tabelle mit derselben Spalte wie der Partitionsschlüssel (z.B. "PartitionId") erheblich reduziert werden. Für eine partitionierte Tabelle müssen Sie in der PRIMARY-Dateigruppe eine [Partitionsfunktion](/sql/t-sql/statements/create-partition-function-transact-sql) und ein [Partitionsschema](/sql/t-sql/statements/create-partition-scheme-transact-sql) erstellen. Dadurch erhöht sich auch die Verfügbarkeit der vorhandenen Daten, während neue Daten geladen werden. Das Protokoll-E/A-Limit wird möglicherweise aufgrund der Anzahl von Partitionen erreicht. Es kann jedoch durch ein Upgrade der SKU erhöht werden.

- **Verstöße gegen eindeutige Schlüssel vermeiden**: Wenn Sie im Azure Stream Analytics-Aktivitätsprotokoll [mehrere Warnmeldungen zu Schlüsselverstößen](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) erhalten, stellen Sie sicher, dass Ihr Auftrag nicht durch Verstöße gegen die Unique-Einschränkung beeinträchtigt wird, die üblicherweise in Wiederherstellungsfällen auftreten. Dies lässt sich durch Festlegen der Option [IGNORE\_DUP\_KEY](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) für Ihre Indizes vermeiden.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory und In-Memory-Tabellen

- **In-Memory-Tabelle als temporäre Tabelle**: [In-Memory-Tabellen](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) ermöglichen Datenladevorgänge in sehr hoher Geschwindigkeit. Die Daten müssen jedoch in den Arbeitsspeicher passen. Benchmarks zeigen, dass das Massenladen aus einer In-Memory-Tabelle in eine datenträgerbasierte Tabelle ungefähr 10-mal schneller verläuft als eine direkte Masseneinfügung über einen einzigen Writer in die datenträgerbasierte Tabelle mit einer Identitätsspalte und einem gruppierten Index. Um diese Masseneinfügungsleistung zu nutzen, richten Sie einen [Kopierauftrag mithilfe von Azure Data Factory](../data-factory/connector-azure-sql-database.md) ein, bei dem Daten aus der In-Memory-Tabelle in die datenträgerbasierte Tabelle kopiert werden.

## <a name="avoiding-performance-pitfalls"></a>Vermeiden von Leistungsproblemen
Die Masseneinfügung von Daten ist erheblich schneller als das Laden von Daten mit einzelnen Einfügevorgängen, da der wiederholte Mehraufwand für das Übertragen der Daten, das Analysieren der Einfügeanweisung, das Ausführen der Anweisung und das Ausgeben eines Transaktionsdatensatzes entfällt. Stattdessen wird ein effizienterer Pfad in die Speicher-Engine verwendet, um die Daten als Datenstrom zu übertragen. Die Kosten für die Einrichtung dieses Pfades sind jedoch sehr viel höher als bei einer einzelnen Einfügeanweisung in einer datenträgerbasierten Tabelle. Gleichstand wird normalerweise bei ungefähr 100 Zeilen erreicht, jenseits dessen ist das Massenladen nahezu immer effizienter. 

Wenn die Rate der eingehenden Ereignisse gering ist, können leicht Batchgrößen von weniger als 100 Zeilen entstehen, sodass das Massenladen ineffizient ist und zu viel Speicherplatz verbraucht. Um diese Einschränkung zu umgehen, können Sie eine der folgenden Aktionen ausführen:
* Erstellen eines INSTEAD OF-[Triggers](/sql/t-sql/statements/create-trigger-transact-sql), um für jede Zeile einfache Einfügung zu verwenden.
* Verwenden einer temporären In-Memory-Tabelle, wie im vorhergehenden Abschnitt beschrieben.

Ein solches Szenario entsteht auch beim Schreiben in einen nicht gruppierten Columnstore-Index (NCCI), bei dem kleinere Masseneinfügungen zu viele Segmente erstellen können, die eine Beschädigung des Index bewirken können. In diesem Fall wird empfohlen, stattdessen einen gruppierten Columnstore-Index zu verwenden.

## <a name="summary"></a>Zusammenfassung

Das Feature der partitionierten Ausgabe in Azure Stream Analytics für die SQL-Ausgabe sollte zusammen mit der an einer partitionierten Tabelle in SQL Azure ausgerichteten Parallelisierung Ihres Auftrags enorme Durchsatzverbesserungen erzielen. Indem Sie Azure Data Factory zur Orchestrierung von Datenverschiebungen aus einer In-Memory-Tabelle in datenträgerbasierte Tabellen nutzen, können Sie den Durchsatz erheblich steigern. Sofern dies möglich ist, kann auch die Verbesserung der Meldungsdichte einen entscheidenden Faktor bei der Verbesserung des Gesamtdurchsatzes darstellen.
