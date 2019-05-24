---
title: Auflösen von T-SQL-Unterschieden bei Migration zur Azure SQL-Datenbank | Microsoft-Dokumentation
description: Transact-SQL-Anweisungen, die in Azure SQL-Datenbank nicht vollständig unterstützt werden
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 84b93005941acf625d05a48f0df7b685e1bea0bd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785494"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Auflösen von Transact-SQL-Unterschieden während der Migration zur SQL-Datenbank

Beim [Migrieren der Datenbank](sql-database-single-database-migrate.md) von SQL Server zu Azure SQL-Server stellen Sie möglicherweise fest, dass die Datenbank einige Umstrukturierungen erfordert, bevor SQL Server migriert werden kann. Dieser Artikel enthält Anleitungen, die Ihnen sowohl bei der Durchführung dieser Umstrukturierung als auch beim Verstehen der Gründe helfen, warum diese Umstrukturierung notwendig ist. Verwenden Sie zum Erkennen von Kompatibilitätsproblemen den [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Übersicht

Die meisten Transact-SQL-Funktionen, die von Anwendungen verwendet werden, werden in Microsoft SQL Server und in der Azure SQL-Datenbank unterstützt. Die zentralen SQL-Komponenten wie z.B. Datentypen, Operatoren, Zeichenfolgen-, Arithmetik-, logische und Cursorfunktionen funktionieren in SQL Server und in der SQL-Datenbank gleich. Es gibt jedoch einige T-SQL-Unterschiede zwischen Elementen der Datendefinitionssprache (Data Definition Language, DDL) und der Datenbearbeitungssprache (Data Manipulation Language, DML), die dazu führen, dass T-SQL-Anweisungen und -Abfragen nur teilweise unterstützt werden (mehr dazu weiter unten in diesem Artikel).

Darüber hinaus werden einige Features und Syntax überhaupt nicht unterstützt, da die Azure SQL-Datenbank entwickelt wurde, um Features von Abhängigkeiten auf Masterdatenbank und Betriebssystem zu isolieren. An sich sind die meisten Aktivitäten auf Serverebene für die SQL-Datenbank nicht geeignet. T-SQL-Anweisungen und -Optionen sind nicht verfügbar, wenn sie Optionen auf Serverebene, Betriebssystemkomponenten oder bestimmte Dateisystemkonfigurationen konfigurieren. Wenn solche Funktionen erforderlich sind, dann ist häufig eine andere geeignete Alternative von der SQL-Datenbank oder aus einem anderen Azure-Feature oder -Dienst verfügbar.

Beispielsweise ist Hochverfügbarkeit in Azure SQL-Datenbank anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) integriert. T-SQL-Anweisungen, die sich auf Verfügbarkeitsgruppen beziehen, werden nicht von der SQL-Datenbank unterstützt, und die dynamischen Verwaltungssichten, die sich auf AlwaysOn-Verfügbarkeitsgruppen beziehen, werden auch nicht unterstützt.

Eine Liste mit den Funktionen, die von der SQL-Datenbank unterstützt bzw. nicht unterstützt werden, finden Sie unter [Funktionen von Azure SQL-Datenbank](sql-database-features.md). Die Liste auf dieser Seite ergänzt diesen Artikel zu Richtlinien und Funktionen und konzentriert sich auf Transact-SQL-Anweisungen.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Transact-SQL-Syntaxanweisungen mit partiellen Unterschieden

Die zentralen DDL-Anweisungen sind verfügbar, aber einige DDL-Anweisungen haben Erweiterungen im Zusammenhang mit Datenträger-Platzierung und nicht unterstützten Funktionen.

- CREATE- und ALTER DATABASE-Anweisungen haben mehr als drei Dutzend Optionen. Die Anweisungen enthalten Ablage von Dateien, FILESTREAM und Service Broker-Optionen, die nur für SQL Server gelten. Dies ist möglicherweise unwichtig, wenn Sie Datenbanken erstellen, bevor Sie migrieren, aber wenn Sie T-SQL-Code migrieren, der Datenbanken erstellt, sollten Sie [CREATE DATABASE (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn268335.aspx) mit der SQL Server-Syntax in [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) vergleichen, um sicherzustellen, dass alle Optionen, die Sie verwenden, unterstützt werden. CREATE DATABASE für Azure SQL-Datenbank verfügt auch über Optionen für Dienstziel und elastische Skalierung, die nur für SQL-Datenbank gelten.
- Die CREATE- und ALTER TABLE-Anweisungen verfügen über FileTable-Optionen, die nicht auf der SQL-Datenbank verwendet werden können, weil FILESTREAM nicht unterstützt wird.
- CREATE- und ALTER LOGIN-Anweisungen werden unterstützt, aber die SQL-Datenbank bietet nicht alle Optionen. Um die Portabilität der Datenbank zu verbessern, unterstützt die SQL-Datenbank nach Möglichkeit die Verwendung von eigenständigen Datenbankbenutzern anstelle von Anmeldungen. Weitere Informationen finden Sie unter [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) und [Steuern und Gewähren von Datenbankzugriff](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL-Syntax, die nicht in Azure SQL-Datenbank unterstützt wird

Zusätzlich zu Transact-SQL-Anweisungen, die sich auf die nicht unterstützen Features beziehen, die unter [Funktionen von Azure SQL-Datenbank](sql-database-features.md) beschrieben sind, werden die folgenden Anweisungen und Gruppen von Anweisungen nicht unterstützt. Wenn die zu migrierende Datenbank eine der folgenden Funktionen verwendet, müssen Sie deshalb T-SQL so umstrukturieren, dass diese T-SQL-Funktionen und -Anweisungen beseitigt werden.

- Sortierung von Systemobjekten
- Verbindungsbezogen: Endpunktanweisungen. SQL-Datenbank unterstützt keine Windows-Authentifizierung, jedoch die ähnliche Azure Active Directory-Authentifizierung. Einige Authentifizierungstypen erfordern die neueste Version von SSMS. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SQL-Datenbank oder SQL Data Warehouse unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md).
- Plattformübergreifende Datenbankabfragen mit drei oder vier Teilnamen. (Schreibgeschützte datenbankübergreifende Abfragen werden durch die Verwendung einer [Abfrage von elastischen Datenbanken](sql-database-elastic-query-overview.md)unterstützt.)
- Datenbankübergreifende Besitzverkettung, `TRUSTWORTHY`-Einstellung
- `EXECUTE AS LOGIN` Verwenden Sie stattdessen „EXECUTE AS USER“.
- Die Verschlüsselung wird mit Ausnahme der erweiterbaren Schlüsselverwaltung unterstützt.
- Ereignisse: Ereignisse, Ereignisbenachrichtigungen, Abfragebenachrichtigungen
- Dateiplatzierung: Syntax im Zusammenhang mit der Ablage von Datenbankdateien, Größe und Datenbankdateien, die automatisch von Microsoft Azure verwaltet werden.
- Hochverfügbarkeit: Syntax im Zusammenhang mit Hochverfügbarkeit, die über Ihr Microsoft Azure-Konto verwaltet wird. Dies schließt die Syntax für die Sicherung, Wiederherstellung, für Always On, die Datenbankspiegelung, den Protokollversand und Wiederherstellungsmodi ein.
- Protokollleser: Syntax, die vom Protokollleser abhängig ist, der nicht in der SQL-Datenbank-Instanz verfügbar ist: Pushreplikation, Erfassung geänderter Daten. Die SQL-Datenbank kann ein Abonnent eines Pushreplikationsartikels sein.
- Funktionen: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Syntax im Zusammenhang mit hardwarebezogenen Servereinstellungen: Arbeitsspeicher, Worker-Threads, CPU-Affinität, Ablaufverfolgungskennzeichen usw. Verwenden Sie stattdessen Dienstebenen und Computegrößen.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` und vierteilige Namen
- .NET Framework: CLR-Integration in SQL Server
- Semantische Suche
- Serveranmeldeinformationen: Verwenden Sie stattdessen [datenbankbezogene Anmeldeinformationen](https://msdn.microsoft.com/library/mt270260.aspx).
- Elemente auf Serverebene: Serverrollen, `sys.login_token`. `GRANT`, `REVOKE` und `DENY` der Berechtigungen auf Serverebene sind nicht verfügbar, obwohl einige durch Berechtigungen auf Datenbankebene ersetzt wurden. Einige nützliche DMVs auf Serverebene verfügen über entsprechende DMVs auf Datenbankebene.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-Optionen und `RECONFIGURE`. Einige Optionen sind verfügbar mit [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
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

Weitere Informationen zu Transact-SQL-Grammatik und -Syntax sowie Beispiele finden Sie unter [Transact-SQL-Referenz (Datenbank-Engine)](https://msdn.microsoft.com/library/bb510741.aspx) in der SQL Server-Onlinedokumentation.

### <a name="about-the-applies-to-tags"></a>Informationen zu Tags vom Typ "Gilt für"

Die Transact-SQL-Referenz umfasst Artikel zu SQL Server-Versionen ab 2008. Unter der Artikelüberschrift befindet sich ein Symbol, in dem die vier SQL Server-Plattformen aufgelistet sind und die Anwendbarkeit angezeigt wird. Beispielsweise wurden Verfügbarkeitsgruppen in SQL Server 2012 eingeführt. Im Artikel [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) ist angegeben, dass die Anweisung für **SQL Server gilt (beginnend mit 2012)**. Die Anweisung gilt nicht für SQL Server 2008, SQL Server 2008 R2, Azure SQL-Datenbank, Azure SQL Data Warehouse oder Parallel Data Warehouse.

In einigen Fällen kann der allgemeine Gegenstand eines Artikels in einem Produkt verwendet werden, es liegen jedoch kleine Unterschiede im Hinblick auf die verschiedenen Produkte vor. Die Unterschiede werden dann im Artikel entsprechend angegeben. In einigen Fällen kann der allgemeine Gegenstand eines Artikels in einem Produkt verwendet werden, es liegen jedoch kleine Unterschiede im Hinblick auf die verschiedenen Produkte vor. Die Unterschiede werden dann im Artikel entsprechend angegeben. Der Artikel CREATE TRIGGER ist zum Beispiel in SQL-Datenbank verfügbar. Aber die Option **ALL SERVER** für Trigger auf Serverebene gibt an, dass Trigger auf Serverebene in der SQL-Datenbank nicht verwendet werden können. Verwenden Sie stattdessen Trigger auf Datenbankebene.

## <a name="next-steps"></a>Nächste Schritte

Eine Liste mit den Funktionen, die von der SQL-Datenbank unterstützt bzw. nicht unterstützt werden, finden Sie unter [Funktionen von Azure SQL-Datenbank](sql-database-features.md). Die Liste auf dieser Seite ergänzt diesen Artikel zu Richtlinien und Funktionen und konzentriert sich auf Transact-SQL-Anweisungen.
