---
title: Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank
description: Erfahren Sie, wie eine SQL Server-Datenbank zu Azure SQL-Datenbank nigriert wird.
keywords: Datenbankmigration,SQL Server-Datenbankmigration,Datenbankmigrationstools,Migrieren einer Datenbank,Migrieren einer SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: fab52fcea03a2f65c868cfac27f8a8cef115b2be
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917646"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält Informationen zu den Hauptmethoden, mit denen eine SQL Server-Datenbank (ab SQL Server 2005) zu Azure SQL-Datenbank migriert wird. Informationen zum Migrieren zu Azure SQL Managed Instance finden Sie unter [Migration einer SQL Server-Instanz zu Azure SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md). Einen Leitfaden zu Migrationsoptionen und Tools für die Migration zu Azure SQL finden Sie unter [Migrieren zu Azure SQL](../migration-guides/index.yml).


## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrieren zu einer Einzel- oder Pooldatenbank

Es gibt zwei Hauptmethoden für das Migrieren einer SQL Server-Datenbank (ab SQL Server 2005) zu Azure SQL-Datenbank. Die erste Methode ist einfacher, aber auch mit einer gewissen (möglicherweise sogar erheblichen) Ausfallzeit während der Migration verbunden. Die zweite Methode ist komplexer, verringert aber deutlich die Ausfallzeit während der Migration.

In beiden Fällen muss mit dem [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) sichergestellt werden, dass die Quelldatenbank mit Azure SQL-Datenbank kompatibel ist. Azure SQL-Datenbank nähert sich der [Featureübereinstimmung](features-comparison.md) mit SQL Server (abgesehen von Problemen mit Vorgängen auf Serverebene und datenbankübergreifenden Vorgängen). Datenbanken und Anwendungen, die auf [teilweise oder nicht unterstützten Funktionen](transact-sql-tsql-differences-sql-server.md) basieren, müssen zur Behebung dieser Kompatibilitätsprobleme vor der Migration der SQL Server-Datenbank etwas [umstrukturiert](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) werden.

> [!NOTE]
> Informationen zum Migrieren einer SQL Server-fremden Datenbank (einschließlich Microsoft Access, Sybase, MySQL Oracle und DB2) zu Azure SQL-Datenbank finden Sie unter [SQL Server-Migrations-Assistent](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Methode 1: Migration mit Ausfallzeit

 Verwenden Sie diese Methode zum Migrieren zu einer Einzel -oder Pooldatenbank, falls eine gewisse Ausfallzeit akzeptabel ist oder Sie eine Testmigration für eine später zu migrierende Produktionsdatenbank durchführen möchten. Ein Tutorial finden Sie unter [Migrieren einer SQL Server-Datenbank](../../dms/tutorial-sql-server-to-azure-sql.md).

Die folgende Liste enthält den allgemeinen Workflow für die SQL Server-Migration einer Einzel- oder Pooldatenbank mit dieser Methode. Informationen zur Migration zu SQL Managed Instance finden Sie unter [Migration zu SQL Managed Instance](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![Diagramm der VSSSDT-Migration](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Untersuchen](/sql/dma/dma-assesssqlonprem) Sie die Kompatibilität der Datenbank mit der neuesten Version von [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Bereiten Sie alle erforderlichen Korrekturen in Form von Transact-SQL-Skripts vor.
3. Erstellen Sie eine im Hinblick auf Transaktionen konsistente Kopie der zu migrierenden Quelldatenbank, oder verhindern Sie neue Transaktionen in der Quelldatenbank, während die Migration erfolgt. Zu den Methoden für letztere Option gehören das Deaktivieren der Clientkonnektivität oder das Erstellen einer [Datenbankmomentaufnahme](/sql/relational-databases/databases/create-a-database-snapshot-transact-sql). Nach der Migration können Sie möglicherweise mithilfe der Transaktionsreplikation die migrierten Datenbanken mit Änderungen aktualisieren, die nach dem Umstellungspunkt für die Migration erfolgen. Siehe [Verwenden der Transaktionsmigration](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Stellen Sie die Transact-SQL-Skripts bereit, um die Korrekturen auf die Datenbankkopie anzuwenden.
5. [Migrieren](/sql/dma/dma-migrateonpremsql) Sie die Datenbankkopie mithilfe von Data Migration Assistant zu einer Azure SQL-Datenbank-Instanz.

> [!NOTE]
> Anstelle von DMA können Sie auch eine BACPAC-Datei verwenden. Siehe [Importieren einer BACPAC-Datei in eine neue Datenbank in Azure SQL-Datenbank](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optimieren der Datenübertragungsleistung während der Migration

Die folgende Liste enthält Empfehlungen, mit denen Sie während des Importprozesses eine optimale Leistung erzielen.

- Wählen Sie im Rahmen Ihres verfügbaren Budgets die höchste Dienstebene und Computegröße, um die Übertragungsleistung zu maximieren. Nach Abschluss der Migration können Sie zentral herunterskalieren, um Geld zu sparen.
- Minimieren Sie die Entfernung zwischen Ihrer BACPAC-Datei und dem Zielrechenzentrum.
- Deaktivieren der automatischen Statistik während der Migration
- Partitionierte Tabellen und Indizes
- Verwerfen Sie indizierte Sichten, und erstellen Sie nach Abschluss des Vorgangs neu.
- Verschieben Sie selten abgefragte Verlaufsdaten in eine andere Datenbank, und migrieren Sie die Verlaufsdaten in eine separate Azure SQL-Datenbank-Instanz. Die Verlaufsdaten können dann mithilfe [elastischer Abfragen](elastic-query-overview.md) abgefragt werden.

### <a name="optimize-performance-after-the-migration-completes"></a>Optimieren der Leistung nach Abschluss der Migration

[Aktualisieren Sie die Statistik](/sql/t-sql/statements/update-statistics-transact-sql) mit einem vollständigen Scan nach Abschluss der Migration.

## <a name="method-2-use-transactional-replication"></a>Methode 2: Verwenden der Transaktionsreplikation

Wenn Sie es sich nicht leisten können, Ihre SQL Server-Datenbank während der Migration aus der Produktion herauszunehmen, können Sie die SQL Server-Transaktionsreplikation als Migrationslösung verwenden. Die Verwendung dieser Methode setzt voraus, dass die Quelldatenbank die [Anforderungen für die Transaktionsreplikation](./replication-to-sql-database.md) erfüllt und mit Azure SQL-Datenbank kompatibel ist. Weitere Informationen zur SQL-Replikation mit Always On finden Sie unter [Konfigurieren der Replikation für AlwaysOn-Verfügbarkeitsgruppen (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Zur Verwendung dieser Lösung können Sie Ihre Azure SQL-Datenbank-Instanz als Abonnent der SQL Server-Instanz konfigurieren, die Sie migrieren möchten. Der Verteiler der Transaktionsreplikation synchronisiert die Daten aus der zu synchronisierenden Datenbank (der Veröffentlicher), während weiterhin neue Transaktionen ausgeführt werden.

Bei der Transaktionsreplikation werden alle Änderungen an Daten oder Schema in Ihrer Azure SQL-Datenbank-Instanz angezeigt. Wenn die Synchronisierung abgeschlossen ist, und Sie zur Migration bereit sind, ändern Sie die Verbindungszeichenfolge Ihrer Anwendungen so, dass sie auf Ihre Datenbank verweist. Sobald die Transaktionsreplikation alle verbleibenden Änderungen Ihrer Quelldatenbank abgearbeitet hat und alle Anwendungen auf Azure DB verweisen, können Sie die Transaktionsreplikation deinstallieren. Ihre Azure SQL-Datenbank-Instanz ist jetzt Ihr Produktionssystem.

 ![SeedCloudTR-Diagramm](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> Mit der Transaktionsreplikation können Sie auch einen Teil Ihrer Quelldatenbank migrieren. Die Veröffentlichung, die Sie zu Azure SQL-Datenbank replizieren, kann auf eine Teilmenge der Tabellen in der replizierten Datenbank beschränkt werden. Für jede replizierte Tabelle können Sie die Daten auf eine Teilmenge der Zeilen bzw. eine Teilmenge der Spalten beschränken.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migration zu SQL-Datenbank mithilfe des Workflows der Transaktionsreplikation

> [!IMPORTANT]
> Verwenden Sie die neueste Version von SQL Server Management Studio, damit Ihr System mit den Updates von Azure und SQL-Datenbank synchronisiert bleibt. Ältere Versionen von SQL Server Management Studio können SQL-Datenbank nicht als Abonnenten einrichten. [Aktualisieren Sie SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Einrichten der Verteilung
   - [Verwenden von SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Verwenden von Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Erstellen der Veröffentlichung
   - [Verwenden von SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Verwenden von Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Erstellen von Abonnements
   - [Verwenden von SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Verwenden von Transact-SQL](/sql/relational-databases/replication/create-a-push-subscription/)

Einige Tipps und Informationen zu den Unterschieden, die beim Migrieren zu SQL-Datenbank zu beachten sind

- Verwendung eines lokalen Verteilers:
  - Dadurch wird die Leistung des Servers beeinträchtigt.
  - Falls die Leistungsbeeinträchtigung nicht akzeptabel ist, können Sie einen anderen Server verwenden. Dies erhöht jedoch die Komplexität der Verwaltung und Administration.
- Achten Sie bei der Wahl eines Momentaufnahmeordners darauf, dass der Ordner groß genug ist, um darin eine BCP-Datei jeder zu replizierenden Tabelle zu speichern.
- Bei der Momentaufnahmenerstellung werden die betreffenden Tabellen bis zum Abschluss des Vorgangs gesperrt. Planen Sie die Momentaufnahme daher entsprechend.
- In Azure SQL-Datenbank werden nur Pushabonnements unterstützt. Sie können nur Abonnenten aus der Quelldatenbank hinzufügen.

## <a name="resolving-database-migration-compatibility-issues"></a>Beheben von Kompatibilitätsproblemen bei der Datenbankmigration

Es können verschiedenste Kompatibilitätsprobleme auftreten. Dies hängt von der SQL Server-Version in der Quelldatenbank und der Komplexität der Datenbank ab, die Sie migrieren. Ältere Versionen von SQL Server weisen mehr Kompatibilitätsprobleme auf. Verwenden Sie die folgenden Ressourcen und eine gezielte Internetsuche mithilfe Ihrer bevorzugten Suchmaschine:

- [In Azure SQL-Datenbank nicht unterstützte SQL Server-Datenbankfunktionen](transact-sql-tsql-differences-sql-server.md)
- [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server 2016](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server)
- [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server 2014](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?viewFallbackFrom=sql-server-2014)
- [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server 2012](/previous-versions/sql/sql-server-2012/ms144262(v=sql.110))
- [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server 2008 R2](/previous-versions/sql/sql-server-2008-r2/ms144262(v=sql.105))
- [Nicht mehr unterstützte Datenbank-Engine-Funktionalität in SQL Server 2005](/previous-versions/sql/sql-server-2005/ms144262(v=sql.90))

Zusätzlich zur Suche im Internet und zur Verwendung dieser Ressourcen sollten Sie die [Microsoft F&A-Seite für Azure SQL-Datenbank](/answers/topics/azure-sql-database.html) oder [StackOverflow](https://stackoverflow.com/) verwenden.

> [!IMPORTANT]
> Eine verwaltete Azure SQL-Instanz ermöglicht die Migration einer vorhandenen SQL Server-Instanz und deren Datenbanken mit minimalen oder keinen Kompatibilitätsproblemen. Siehe [Was ist eine verwaltete Instanz?](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Verwenden Sie das Skript aus dem Azure SQL EMEA Engineers-Blog zum [Überwachen der TempDB-Auslastung während der Migration](/archive/blogs/azuresqlemea/lesson-learned-10-monitoring-tempdb-usage).
- Verwenden Sie das Skript aus dem Azure SQL EMEA Engineers-Blog zum [Überwachen des Transaktionsprotokoll-Speicherplatzes Ihrer Datenbank während der Migration](/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von BACPAC-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).
- Informationen zur Verwendung von UTC-Zeit nach der Migration finden Sie unter [Modifying the default time zone for your local time zone](/archive/blogs/azuresqlemea/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone) (Ändern der Standardzeitzone für Ihre lokale Zeitzone).
- Informationen zum Ändern der Standardsprache einer Datenbank nach der Migration finden Sie unter [How to change the default language of Azure SQL Database](/archive/blogs/azuresqlemea/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database) (Ändern der Standardsprache von Azure SQL-Datenbank).
