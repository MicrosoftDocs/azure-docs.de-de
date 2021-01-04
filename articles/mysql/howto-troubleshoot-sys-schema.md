---
title: Verwenden von sys_schema – Azure Database for MySQL
description: Erfahren Sie, wie Sie mit sys_schema Leistungsprobleme ermitteln und die Datenbank in Azure Database for MySQL verwalten können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: a20510ee2800a54f9a51a2f498ee8ae8a3e51d55
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543148"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Verwenden von sys_schema zum Optimieren der Leistung und Datenbankwartung in Azure Database for MySQL

Das Schema performance_schema von MySQL wurde zuerst in MySQL 5.5 eingeführt und ermöglicht die Instrumentierung für viele wichtige Serverressourcen, z.B. Speicherzuteilung, gespeicherte Programme, Metadatensperrung usw. Allerdings enthält performance_schema mehr als 80 Tabellen, und häufig erfordert das Abrufen der erforderlichen Informationen das Verknüpfen von Tabellen in performance_schema sowie von Tabellen aus information_schema. Das auf performance_schema und information_schema basierende sys_schema bietet eine leistungsstarke Auflistung von [benutzerfreundlichen Sichten](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) in einer schreibgeschützten Datenbank bei vollständiger Verfügbarkeit in Azure Database for MySQL Version 5.7.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/sys-schema-views.png" alt-text="Sichten von sys_schema":::

Es gibt in sys_schema 52 Sichten, die jeweils folgende Präfixe aufweisen:

- Host_summary oder IO: Latenzen im Zusammenhang mit E/A.
- InnoDB: InnoDB-Pufferstatus und -sperren.
- Memory: Auslastung des Arbeitsspeichers durch Host und Benutzer.
- Schema: Schemabezogene Informationen wie automatische Inkremente, Indizes usw.
- Statement: Informationen zu SQL-Anweisungen, etwa Anweisungen, die vollständige Tabellenscans oder langen Abfragezeiten verursacht haben.
- User: Belegte Ressourcen, gruppiert nach Benutzern. Beispiele sind die Datei-E/As, Verbindungen und Arbeitsspeicher.
- Wait: Warteereignisse, gruppiert nach Host oder Benutzer.

Im Folgenden finden Sie einige allgemeine Verwendungsmuster für sys_schema. Zunächst gruppieren wir die Verwendungsmuster in zwei Kategorien: **Leistungsoptimieren** und **Datenbankwartung**.

## <a name="performance-tuning"></a>Leistungsoptimierung

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

E/A ist der aufwendigste Vorgang in der Datenbank. Wir ermitteln die durchschnittliche E/A-Wartezeit durch Abfragen der Sicht *sys.user_summary_by_file_io*. Mit dem standardmäßig bereitgestellten Speicher von 125 GB beträgt die E/A-Wartezeit ca. 15 Sekunden.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-125GB.png" alt-text="E/A-Latenz: 125 GB":::

Da Azure Database for MySQL die E/A im Hinblick auf den Speicher skaliert, wird die E/A-Wartezeit nach der Erhöhung des bereitgestellten Speichers auf 1 TB auf 571 ms reduziert.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-1TB.png" alt-text="E/A-Latenz: 1 TB":::

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Trotz sorgfältiger Planung können viele Abfragen weiterhin zu vollständigen Tabellenscans führen. Weitere Informationen zu den Indextypen und deren Optimierung finden Sie im folgenden Artikel: [Beheben von Problemen bei der Abfrageleistung](./howto-troubleshoot-query-performance.md). Vollständige Tabellenscans sind ressourcenintensiv und beeinträchtigen die Datenbankleistung. Die schnellste Möglichkeit zum Suchen von Tabellen mit vollständigen Tabellenscans stellt eine Abfrage der Sicht *sys.schema_tables_with_full_table_scans* dar.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/full-table-scans.png" alt-text="Vollständige Tabellenscans":::

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Um Probleme mit der Datenbankleistung zu beheben, kann es nützlich sein, die Ereignisse, die innerhalb der Datenbank eintreten, zu ermitteln. Dazu verwenden Sie die Sicht *sys.user_summary_by_statement_type*.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/summary-by-statement.png" alt-text="Zusammenfassung nach Anweisung":::

In diesem Beispiel benötigt Azure Database for MySQL 53 Minuten, um das slog-Abfrageprotokoll 44.579-mal zu leeren. Dies ist eine lange Zeit und bedeutet viele E/As. Sie können diese Aktivität reduzieren, indem Sie Ihr langsames Abfrageprotokoll deaktivieren oder die Häufigkeit der langsamen Abfrageprotokolle im Azure-Portal verringern.

## <a name="database-maintenance"></a>Datenbankwartung

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> Das Abfragen dieser Ansicht kann sich auf die Leistung auswirken. Es wird empfohlen, diese Problembehandlung in ruhigeren Geschäftszeiten durchzuführen.

Der InnoDB-Pufferpool befindet sich im Arbeitsspeicher. Er ist der wichtigste Mechanismus zur Zwischenspeicherung zwischen dem DBMS und dem Speicher. Die Größe des InnoDB-Pufferpools wird durch die Leistungsstufe begrenzt und kann nur geändert werden, indem eine andere Produkt-SKU ausgewählt wird. Wie beim Arbeitsspeicher im Betriebssystem werden alte Seiten ausgelagert, um Platz für neuere Daten bereitzustellen. Um herauszufinden, welche Tabellen den meisten Speicher im InnoDB-Pufferpool belegen, können Sie die Sicht *sys.innodb_buffer_stats_by_table* abfragen.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png" alt-text="InnoDB-Pufferstatus":::

In der Abbildung oben wird ersichtlich, dass mit Ausnahme der Systemtabellen und -sichten alle Tabellen in der Datenbank „mysqldatabase033“, die eine WordPress-Website hostet, 16 KB oder 1 Seite der Daten im Arbeitsspeicher belegen.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indizes sind hervorragende Tools zur Verbesserung der Leistung bei Lesevorgängen, sie verursachen jedoch zusätzliche Kosten für Einfügevorgänge und Speicher. *sys.schema_unused_indexes* und *sys.schema_redundant_indexes* geben Einblick in nicht genutzte oder doppelte Indizes.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/unused-indexes.png" alt-text="Nicht verwendete Indizes":::

:::image type="content" source="./media/howto-troubleshoot-sys-schema/redundant-indexes.png" alt-text="Redundante Indizes":::

## <a name="conclusion"></a>Zusammenfassung

Zusammenfassend lässt sich sagen, dass sys_schema ein großartiges Tool sowohl für die Leistungsoptimierung als auch für die Datenbankwartung ist. Nutzen Sie dieses Feature in Ihrer Instanz von Azure Database for MySQL. 

## <a name="next-steps"></a>Nächste Schritte
- Um Antworten anderer Benutzer auf häufig gestellte Fragen zu erhalten oder eine neue Frage/Antwort zu veröffentlichen, besuchen Sie die [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-database-mysql.html) oder [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).