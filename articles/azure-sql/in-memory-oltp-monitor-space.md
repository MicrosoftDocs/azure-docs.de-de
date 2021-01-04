---
title: Überwachen von XTP-In-Memory-Speicher
description: Einschätzen und Überwachen der XTP-In-Memory-Speicherverwendung und -kapazität; Beheben des Kapazitätsfehlers 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5717c2479c1d894117bae44826a814c3cfeaa98a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493388"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Überwachen von In-Memory-OLTP-Speicher in Azure SQL Database und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Bei der Verwendung von [In-Memory-OLTP](in-memory-oltp-overview.md)befinden sich die Daten der speicheroptimierten Tabellen und Tabellenvariablen im In-Memory-OLTP-Speicher.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Bestimmen, ob genügend In-Memory-OLTP-Speicherplatz für die Daten zur Verfügung steht

Bestimmen Sie die Speicherkapazitäten der verschiedenen Dienstebenen. Jede Dienstebene vom Typ „Premium“ und „Unternehmenskritisch“ verfügt über eine maximale In-Memory-OLTP-Speichergröße.

- [DTU-basierte Ressourcenlimits – Einzeldatenbank](database/resource-limits-dtu-single-databases.md)
- [DTU-basierte Ressourcenlimits – Pools für elastische Datenbanken](database/resource-limits-dtu-elastic-pools.md)
- [V-Kern-basierte Ressourceneinschränkungen – Einzeldatenbanken](database/resource-limits-vcore-single-databases.md)
- [V-Kern-basierte Ressourceneinschränkungen – Pool für elastische Datenbanken](database/resource-limits-vcore-elastic-pools.md)
- [V-Kern-basierte Ressourcenlimits – verwaltete Instanz](managed-instance/resource-limits.md)

Das Einschätzen der Arbeitsspeicheranforderungen für eine speicheroptimierte Tabelle funktioniert für SQL Server genauso wie in Azure SQL-Datenbank und Azure SQL Managed Instance. Nehmen Sie sich einige Minuten Zeit, um den Artikel [Schätzen der Arbeitsspeicheranforderungen speicheroptimierter Tabellen](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables) zu lesen.

Sowohl die Tabellen- und Tabellenvariablenzeilen als auch die Indizes werden in die maximale Größe für Benutzerdaten eingerechnet. Darüber hinaus benötigt ALTER TABLE genügend Platz, um eine neue Version der gesamten Tabelle und ihrer Indizes zu erstellen.

Wenn dieser Grenzwert überschritten wird, tritt bei Einfüge- und Aktualisierungsvorgängen möglicherweise der Fehler 41823 bei Einzeldatenbanken in Azure SQL-Datenbank und Datenbanken in Azure SQL Managed Instance bzw. der Fehler 41840 bei Pools für elastische Datenbanken in Azure SQL-Datenbank auf. An diesem Punkt müssen Sie entweder Daten löschen, um Speicherplatz freizugeben, oder die Dienstebene bzw. Computegröße für Ihre Datenbank upgraden.

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung

Sie können die Nutzung von In-Memory-Speicher als Prozentsatz der Speicherkapazität für Ihre Computegröße im [Azure-Portal](https://portal.azure.com/) überwachen:

1. Suchen Sie auf dem Blatt „Datenbank“ das Feld „Ressourcenverwendung“, und klicken Sie auf „Bearbeiten“.
2. Wählen Sie die Metrik `In-Memory OLTP Storage percentage` aus.
3. Um eine Warnung hinzuzufügen, klicken Sie auf das Feld „Ressourcenverwendung“, um das Blatt „Metrik“ zu öffnen, und klicken Sie dann auf „Warnung hinzufügen“.

Oder verwenden Sie folgende Abfrage, um die In-Memory-Speicherverwendung anzuzeigen:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Behandeln von Situationen mit zu wenig In-Memory-OLTP-Speicher: Fehler 41823 und 41840

Bei Erreichen der Obergrenze des In-Memory-OLTP-Speichers in Ihrer Datenbank tritt bei INSERT-, UPDATE-, ALTER- und CREATE-Vorgängen der Fehler 41823 (Einzeldatenbanken) bzw. der Fehler 41840 (Pools für elastische Datenbanken) auf. Beide Fehler führen zum Abbruch der aktiven Transaktion.

Die Fehlermeldungen 41823 und 41840 geben an, dass die speicheroptimierten Tabellen und Tabellenvariablen in der Datenbank oder im Pool die maximale In-Memory-OLTP-Speichergröße erreicht haben.

Beheben Sie den Fehler mit einer der folgenden Methoden:

- Löschen Sie Daten aus den speicheroptimierten Tabellen – Sie können die Daten beispielsweise in herkömmliche datenträgerbasierte Tabellen auslagern.
- Führen Sie ein Upgrade auf eine Dienstebene durch, die genügend In-Memory-Speicher für die Daten bietet, die Sie in In-Memory-Tabellen speichern müssen.

> [!NOTE]
> In seltenen Fällen treten die Fehler 41823 und 41840 nur vorübergehend auf. In diesem Fällen steht genügend In-Memory-OLTP-Speicher zur Verfügung, und der Vorgang ist erfolgreich, wenn er erneut ausgeführt wird. Es empfiehlt sich daher, den insgesamt verfügbaren In-Memory OLTP-Speicher zu überwachen und den Vorgang beim erstmaligen Auftreten des Fehlers 41823 oder 41840 zu wiederholen. Weitere Informationen zur Wiederholungslogik finden Sie unter [Konflikterkennung und Wiederholungslogik](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Nächste Schritte

Eine Anleitung zur Überwachung finden Sie unter [Überwachen der Leistung mithilfe von dynamischen Verwaltungssichten](database/monitoring-with-dmvs.md).