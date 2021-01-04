---
title: 'Auflösen von T-SQL-Unterschieden: Migration'
description: Transact-SQL-Anweisungen, die in Azure SQL-Datenbank nicht vollständig unterstützt werden
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 1e286b2329cb98d580bbf64071ff8767db304a00
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461884"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Auflösen von Transact-SQL-Unterschieden während der Migration zur SQL-Datenbank

Beim [Migrieren der Datenbank](migrate-to-database-from-sql-server.md) von SQL Server zu Azure SQL-Datenbank stellen Sie möglicherweise fest, dass die SQL Server-Datenbank einige Umstrukturierungen erfordert, bevor sie migriert werden kann. Dieser Artikel enthält Anleitungen, die Ihnen sowohl bei der Durchführung dieser Umstrukturierung als auch beim Verstehen der Gründe helfen, warum diese Umstrukturierung notwendig ist. Verwenden Sie zum Erkennen von Kompatibilitätsproblemen den [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Übersicht

Die meisten Transact-SQL-Funktionen, die von Anwendungen verwendet werden, werden in Microsoft SQL Server und in der Azure SQL-Datenbank unterstützt. Die zentralen SQL-Komponenten wie Datentypen, Operatoren sowie Zeichenfolgen-, Arithmetik-, logische und Cursorfunktionen funktionieren in SQL Server und in SQL-Datenbank gleich. Es gibt jedoch einige T-SQL-Unterschiede zwischen Elementen der Datendefinitionssprache (Data Definition Language, DDL) und der Datenbearbeitungssprache (Data Manipulation Language, DML), die dazu führen, dass T-SQL-Anweisungen und -Abfragen nur teilweise unterstützt werden (mehr dazu weiter unten in diesem Artikel).

Darüber hinaus werden einige Features und bestimmte Syntax überhaupt nicht unterstützt, da Azure SQL-Datenbank darauf ausgelegt ist, Features von Abhängigkeiten von der Masterdatenbank und dem Betriebssystem zu isolieren. An sich sind die meisten Aktivitäten auf Serverebene für die SQL-Datenbank nicht geeignet. T-SQL-Anweisungen und -Optionen sind nicht verfügbar, wenn sie Optionen auf Serverebene oder Betriebssystemkomponenten konfigurieren oder Dateisystemkonfigurationen angeben. Wenn solche Funktionen erforderlich sind, dann ist häufig eine andere geeignete Alternative von der SQL-Datenbank oder aus einem anderen Azure-Feature oder -Dienst verfügbar.

Beispielsweise ist Hochverfügbarkeit in Azure SQL-Datenbank anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) integriert. T-SQL-Anweisungen, die sich auf Verfügbarkeitsgruppen beziehen, werden nicht von der SQL-Datenbank unterstützt, und die dynamischen Verwaltungssichten, die sich auf AlwaysOn-Verfügbarkeitsgruppen beziehen, werden auch nicht unterstützt.

Eine Liste der Funktionen, die von der SQL-Datenbank unterstützt bzw. nicht unterstützt werden, finden Sie unter [Funktionen von Azure SQL-Datenbank](features-comparison.md). Die Liste auf dieser Seite ergänzt diesen Artikel zu Richtlinien und Funktionen und konzentriert sich auf Transact-SQL-Anweisungen.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Transact-SQL-Syntaxanweisungen mit partiellen Unterschieden

Die zentralen DDL-Anweisungen sind verfügbar, aber einige DDL-Anweisungen haben Erweiterungen im Zusammenhang mit Datenträger-Platzierung und nicht unterstützten Funktionen.

- CREATE- und ALTER DATABASE-Anweisungen haben mehr als drei Dutzend Optionen. Die Anweisungen enthalten Ablage von Dateien, FILESTREAM und Service Broker-Optionen, die nur für SQL Server gelten. Dies ist möglicherweise nicht wichtig, wenn Sie vor der Migration Datenbanken erstellen. Wenn Sie jedoch T-SQL-Code migrieren, der Datenbanken erstellt, sollten Sie [CREATE DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/create-database-transact-sql) mit der SQL Server-Syntax in [CREATE DATABASE (SQL Server Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) vergleichen, um sicherzustellen, dass alle verwendeten Optionen unterstützt werden. CREATE DATABASE für Azure SQL-Datenbank verfügt auch über Optionen für Dienstziel und elastische Skalierung, die nur für SQL-Datenbank gelten.
- Die CREATE und ALTER TABLE-Anweisungen verfügen über Dateitabellenoptionen, die nicht mit SQL-Datenbank verwendet werden können, da FILESTREAM nicht unterstützt wird.
- CREATE und ALTER LOGIN-Anweisungen werden unterstützt, SQL-Datenbank bietet jedoch nicht alle Optionen. Um die Portabilität der Datenbank zu verbessern, unterstützt die SQL-Datenbank nach Möglichkeit die Verwendung von eigenständigen Datenbankbenutzern anstelle von Anmeldungen. Weitere Informationen finden Sie unter [CREATE/ALTER LOGIN](/sql/t-sql/statements/alter-login-transact-sql) und [Steuern und Gewähren des Datenbankzugriffs für SQL-Datenbank und SQL Data Warehouse](logins-create-manage.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL-Syntax, die nicht in Azure SQL-Datenbank unterstützt wird

Zusätzlich zu Transact-SQL-Anweisungen, die sich auf die unter [Vergleich der Features von Azure SQL-Datenbank](features-comparison.md) beschriebenen nicht unterstützten Features beziehen, werden die folgenden Anweisungen und Gruppen von Anweisungen nicht unterstützt. Wenn die zu migrierende Datenbank eine der folgenden Funktionen verwendet, müssen Sie deshalb T-SQL so umstrukturieren, dass diese T-SQL-Funktionen und -Anweisungen beseitigt werden.

- Sortierung von Systemobjekten
- Verbindungsbezogen: Endpunktanweisungen. SQL-Datenbank unterstützt keine Windows-Authentifizierung, jedoch die ähnliche Azure Active Directory-Authentifizierung. Einige Authentifizierungstypen erfordern die neueste Version von SSMS. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SQL-Datenbank oder Azure Synapse Analytics unter Verwendung der Azure Active Directory-Authentifizierung](authentication-aad-overview.md).
- Plattformübergreifende Datenbankabfragen mit drei oder vier Teilnamen. (Schreibgeschützte datenbankübergreifende Abfragen werden durch die Verwendung einer [Abfrage von elastischen Datenbanken](elastic-query-overview.md)unterstützt.)
- Datenbankübergreifende Besitzverkettung, `TRUSTWORTHY`-Einstellung
- `EXECUTE AS LOGIN` Verwenden Sie stattdessen „EXECUTE AS USER“.
- Die Verschlüsselung wird mit Ausnahme der erweiterbaren Schlüsselverwaltung unterstützt.
- Ereignisse: Ereignisse, Ereignisbenachrichtigungen, Abfragebenachrichtigungen
- Dateiplatzierung: Syntax im Zusammenhang mit der Ablage von Datenbankdateien, Größe und Datenbankdateien, die automatisch von Microsoft Azure verwaltet werden.
- Hochverfügbarkeit: Syntax im Zusammenhang mit Hochverfügbarkeit, die über Ihr Microsoft Azure-Konto verwaltet wird. Dies schließt die Syntax für die Sicherung, Wiederherstellung, für Always On, die Datenbankspiegelung, den Protokollversand und Wiederherstellungsmodi ein.
- Protokollleser: Syntax, die vom in SQL-Datenbank nicht verfügbaren Protokollleser abhängig ist: Pushreplikation, Erfassung geänderter Daten. Die SQL-Datenbank kann ein Abonnent eines Pushreplikationsartikels sein.
- Funktionen: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Syntax im Zusammenhang mit hardwarebezogenen Servereinstellungen: Arbeitsspeicher, Worker-Threads, CPU-Affinität, Ablaufverfolgungskennzeichen usw. Verwenden Sie stattdessen Dienstebenen und Computegrößen.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` und vierteilige Namen
- .NET Framework: CLR-Integration in SQL Server
- Semantische Suche
- Serveranmeldeinformationen: Verwenden Sie stattdessen [datenbankbezogene Anmeldeinformationen](/sql/t-sql/statements/create-database-scoped-credential-transact-sql).
- Elemente auf Serverebene: Serverrollen, `sys.login_token`. `GRANT`, `REVOKE` und `DENY` für Berechtigungen auf Serverebene sind nicht verfügbar, obwohl einige durch Berechtigungen auf Datenbankebene ersetzt wurden. Einige nützliche DMVs auf Serverebene verfügen über entsprechende DMVs auf Datenbankebene.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-Optionen und `RECONFIGURE`. Einige Optionen sind verfügbar mit [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server-Agent: Syntax, die auf dem SQL Server-Agent oder der MSDB-Datenbank beruht: Warnungen, Operatoren, zentrale Verwaltungsserver. Verwenden Sie stattdessen Skripterstellung, z.B. Azure PowerShell.
- SQL Server-Überwachung: Verwenden Sie stattdessen die SQL-Datenbank-Überwachung.
- SQL Server-Ablaufverfolgung
- Ablaufverfolgungsflags: Einige Ablaufverfolgungskennzeichen-Elemente wurden in Kompatibilitätsmodi verschoben.
- Transact-SQL-Debugging
- Trigger: Auf Server begrenzt oder Anmeldetrigger
- `USE`-Anweisung: Sie müssen eine neue Verbindung mit der neuen Datenbank herstellen, um den Datenbankkontext in eine andere Datenbank zu ändern.

## <a name="full-transact-sql-reference"></a>Vollständige Transact-SQL-Referenz

Weitere Informationen zu Transact-SQL-Grammatik und -Syntax sowie Beispiele finden Sie unter [Transact-SQL-Referenz (Datenbank-Engine)](/sql/t-sql/language-reference) in der SQL Server-Onlinedokumentation.

### <a name="about-the-applies-to-tags"></a>Informationen zu Tags vom Typ "Gilt für"

Die Transact-SQL-Referenz umfasst Artikel zu SQL Server-Versionen ab 2008. Unter der Artikelüberschrift befindet sich eine Symbolleiste, auf der die vier SQL Server-Plattformen aufgelistet sind und ihre Anwendbarkeit angezeigt wird. Beispielsweise wurden Verfügbarkeitsgruppen in SQL Server 2012 eingeführt. Der Artikel [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql) gibt an, dass die Anweisung für **SQL Server gilt (ab SQL Server 2012)** . Die Anweisung gilt nicht für SQL Server 2008, SQL Server 2008 R2, Azure SQL-Datenbank, Azure Synapse Analytics oder Parallel Data Warehouse.

In einigen Fällen kann der allgemeine Gegenstand eines Artikels in einem Produkt verwendet werden, es liegen jedoch kleine Unterschiede im Hinblick auf die verschiedenen Produkte vor. Die Unterschiede werden dann im Artikel entsprechend angegeben. In einigen Fällen kann der allgemeine Gegenstand eines Artikels in einem Produkt verwendet werden, es liegen jedoch kleine Unterschiede im Hinblick auf die verschiedenen Produkte vor. Die Unterschiede werden dann im Artikel entsprechend angegeben. Der Artikel CREATE TRIGGER ist beispielsweise in SQL-Datenbank verfügbar. Die Option **ALL SERVER** für Trigger auf Serverebene gibt jedoch an, dass Trigger auf Serverebene in SQL-Datenbank nicht verwendet werden können. Verwenden Sie stattdessen Trigger auf Datenbankebene.

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Funktionen, die von der SQL-Datenbank unterstützt bzw. nicht unterstützt werden, finden Sie unter [Funktionen von Azure SQL-Datenbank](features-comparison.md). Die Liste auf dieser Seite ergänzt diesen Artikel zu Richtlinien und Funktionen und konzentriert sich auf Transact-SQL-Anweisungen.