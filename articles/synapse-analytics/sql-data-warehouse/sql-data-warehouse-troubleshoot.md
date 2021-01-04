---
title: Problembehandlung bei einem dedizierten SQL-Pool (früher SQL DW)
description: Problembehandlung bei einem dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c69df88828a179cf1dce7bd35ac19c518b07903d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530713"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Problembehandlung bei einem dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics

Dieser Artikel enthält allgemeine Informationen zur Problembehandlung bei einem dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics.

## <a name="connecting"></a>Verbindung

| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Fehler bei der Anmeldung für den Benutzer 'NT-AUTORITÄT\ANONYME ANMELDUNG'. (Microsoft SQL Server, Fehler: 18456) | Dieser Fehler tritt auf, wenn ein Azure AD-Benutzer versucht, eine Verbindung mit der Masterdatenbank herzustellen, aber nicht über einen Benutzer in der Masterdatenbank verfügt.  Geben Sie zum Beheben dieses Problems entweder den dedizierten SQL-Pool (früher SQL DW) an, mit dem Sie gerade eine Verbindung herstellen möchten, oder fügen Sie den Benutzer zur Masterdatenbank hinzu.  Weitere Informationen finden Sie im Artikel [Sichern einer Datenbank in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) . |
| Der Serverprinzipal „MeinBenutzername“ kann unter dem aktuellen Sicherheitskontext nicht auf die Datenbank „Master“ zugreifen. Die Standarddatenbank des Benutzers kann nicht geöffnet werden. Fehler bei der Anmeldung. Fehler bei der Anmeldung für den Benutzer 'MeinBenutzername'. (Microsoft SQL Server, Fehler: 916) | Dieser Fehler tritt auf, wenn ein Azure AD-Benutzer versucht, eine Verbindung mit der Masterdatenbank herzustellen, aber nicht über einen Benutzer in der Masterdatenbank verfügt.  Geben Sie zum Beheben dieses Problems entweder den dedizierten SQL-Pool (früher SQL DW) an, mit dem Sie gerade eine Verbindung herstellen möchten, oder fügen Sie den Benutzer zur Masterdatenbank hinzu.  Weitere Informationen finden Sie im Artikel [Sichern einer Datenbank in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) . |
| CTAIP-Fehler                                                  | Dieser Fehler kann auftreten, wenn eine Anmeldung zwar für die SQL-Datenbank-Masterdatenbank erstellt wurde, aber nicht in der spezifischen SQL-Datenbank.  Lesen Sie den [Übersichtsartikel zur Sicherheit](sql-data-warehouse-overview-manage-security.md) , wenn dieser Fehler auftritt.  In diesem Artikel wird erläutert, wie Sie zunächst eine Anmeldung und einen Benutzer für die Masterdatenbank erstellen und anschließend einen Benutzer in der SQL-Datenbank. |
| Von der Firewall blockiert                                          | Dedizierte SQL-Pools (früher SQL DW) werden durch Firewalls geschützt, um sicherzustellen, dass nur bekannte IP-Adressen auf eine Datenbank zugreifen können. Firewalls sind standardmäßig sicher. Sie müssen daher eine IP-Adresse oder einen Adressbereich explizit aktivieren, bevor Sie eine Verbindung herstellen können.  Um Ihre Firewall für den Zugriff zu konfigurieren, führen Sie die in den [Bereitstellungsanweisungen](create-data-warehouse-portal.md) beschriebenen Schritte zum [Konfigurieren des Serverfirewallzugriffs für Ihre Client-IP](create-data-warehouse-portal.md) aus. |
| Verbindung mit Tool oder Treiber kann nicht hergestellt werden                           | Beim dedizierten SQL-Pool (früher SQL DW) empfiehlt sich die Verwendung von [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [SSDT für Visual Studio](sql-data-warehouse-install-visual-studio.md) oder [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) zum Abfragen Ihrer Daten. Weitere Informationen zu Treibern und zum Herstellen einer Verbindung mit Azure Synapse finden Sie in den Artikeln [Treiber für Azure Synapse](sql-data-warehouse-connection-strings.md) und [Herstellen einer Verbindung mit Azure Synapse](sql-data-warehouse-connect-overview.md). |

## <a name="tools"></a>Tools

| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Im Objekt-Explorer von Visual Studio fehlen Azure AD-Benutzer           | Dies ist ein bekanntes Problem.  Sie können die Benutzer in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) anzeigen, um dieses Problem zu umgehen.  Weitere Informationen zur Verwendung von Azure Active Directory beim dedizierten SQL-Pool (früher SQL DW) finden Sie unter [Authentifizierung in Azure Synapse](sql-data-warehouse-authentication.md). |
| Manuelle, über den Assistenten für die Skripterstellung erstellte Skripts oder über SSMS hergestellte Verbindungen sind langsam, reagieren nicht mehr oder erzeugen Fehler | Vergewissern Sie sich, dass Benutzer in der Masterdatenbank erstellt wurden. Stellen Sie zudem in den Skriptoptionen sicher, dass die Engine-Edition auf „Microsoft Azure Synapse Analytics Edition“ und der Engine-Typ auf „Microsoft Azure SQL-Datenbank“ festgelegt ist. |
| Fehler beim Generieren von Skripts in SSMS                               | Beim Generieren eines Skripts für den dedizierten SQL-Pool (früher SQL DW) tritt ein Fehler auf, wenn die Option „Skript für abhängige Objekte generieren“ auf „True“ festgelegt ist. Um dieses Problem zu umgehen, müssen Benutzer manuell zu **Extras > Optionen > SQL Server-Objekt-Explorer > Skript für abhängige Objekte generieren** navigieren und diese Option auf FALSE festlegen. |

## <a name="data-ingestion-and-preparation"></a>Datenerfassung und -vorbereitung

| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Das Exportieren leerer Zeichenfolgen mithilfe von CETAS führt in Parquet- und ORC-Dateien zu NULL-Werten. Beachten Sie: Wenn Sie leere Zeichenfolgen aus Spalten mit NOT NULL-Einschränkungen exportieren, führt CETAS zu abgelehnten Datensätzen, und möglicherweise ist der Export nicht möglich. | Entfernen Sie leere Zeichenfolgen oder die problematische Spalte aus der SELECT-Anweisung Ihres CETAS-Befehls. |
| Das Laden eines Werts außerhalb des Bereichs zwischen 0 und 127 in eine tinyint-Spalte für die Dateiformate Parquet und ORC wird nicht unterstützt. | Geben Sie einen größeren Datentyp für die Zielspalte an.           |

## <a name="performance"></a>Leistung

| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Behandlung von Problemen mit der Abfrageleistung                            | Wenn Sie die Problembehandlung für eine bestimmte Abfrage durchführen möchten, sollten Sie sich zunächst über das [Untersuchen der Ausführung von Abfragen](sql-data-warehouse-manage-monitor.md#monitor-query-execution)informieren. |
| TempDB-Speicherplatzprobleme | [Überwachen Sie die Speicherauslastung von TempDB.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  Häufige Ursachen für unzureichenden TempDB-Speicherplatz:<br>- Der Abfrage sind keine ausreichenden Ressourcen zugeordnet, wodurch ein Überlauf der Daten in TempDB stattfindet.  Siehe [Workloadverwaltung](resource-classes-for-workload-management.md). <br>- Fehlende oder veraltete Statistik, wodurch übermäßige Datenverschiebungen stattfinden.  Ausführliche Informationen zum Erstellen von Statistiken finden Sie unter [Tabellenstatistik in Azure SQL Data Warehouse](sql-data-warehouse-tables-statistics.md).<br>- Der TempDB-Speicherplatz wird nach Dienstebene zugeordnet.  [Skalieren Sie Ihren dedizierten SQL-Pool (früher SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) auf eine höhere DWU-Einstellung, um mehr TempDB-Speicherplatz zuzuordnen.|
| Schlechte Abfrageleistung und Planung ist häufig das Ergebnis fehlender Statistiken | Die häufigste Ursache für schlechte Leistung ist das Fehlen von Statistiken für Ihre Tabellen.  Ausführliche Informationen dazu, wie Sie Statistiken erstellen und warum sie für die Leistung wichtig sind, finden Sie unter [Tabellenstatistik in Azure SQL Data Warehouse](sql-data-warehouse-tables-statistics.md). |
| Geringe Parallelität/Abfragen in der Warteschlange                             | Das Verständnis der [Workloadverwaltung](resource-classes-for-workload-management.md) ist wichtig, damit Sie wissen, wie Sie die Speicherbelegung und die Parallelität abwägen sollen. |
| Implementieren von bewährten Methoden                              | Wenn Sie die Leistung bei Ihren Abfragen verbessern möchten, ist der Artikel [Bewährte Methoden für den dedizierten SQL-Pool (früher SQL DW)](sql-data-warehouse-best-practices.md) ein idealer Ausgangspunkt. |
| Verbessern der Leistung mit der Skalierung                      | Manchmal besteht der Weg zur Leistungsverbesserung einfach darin, mehr Computeleistung für Ihre Abfragen zur Verfügung zu stellen, indem Sie [Ihren dedizierten SQL-Pool (früher SQL DW) skalieren](sql-data-warehouse-manage-compute-overview.md). |
| Schlechte Leistung aufgrund von schlechter Indexqualität     | Es kann vorkommen, dass sich Abfragen aufgrund der [schlechten Qualität des Columnstore-Index](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) verlangsamen.  Weitere Informationen finden Sie unter [Rebuild indexes to improve segment quality](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)(Neuerstellen von Indizes zum Verbessern der Segmentqualität). |

## <a name="system-management"></a>Systemverwaltung

| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Der Vorgang konnte nicht ausgeführt werden, da der Server das zulässige Datenbanktransaktionseinheit-Kontingent von 45.000 überschreiten würde. | Reduzieren Sie entweder die [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) der Datenbank, die Sie erstellen möchten, oder [fordern Sie eine Erhöhung des Kontingents an](sql-data-warehouse-get-started-create-support-ticket.md). |
| Untersuchen der Speicherauslastung                              | Informationen zu den Grundlagen der Speicherauslastung des Systems finden Sie unter [Tabellengrößen](sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Hilfe beim Verwalten von Tabellen                                    | Hilfe zur Verwaltung von Tabellen finden Sie in der [Übersicht über Tabellen](sql-data-warehouse-tables-overview.md).  Dieser Artikel enthält auch Links zu ausführlicheren Themen wie [Tabellendatentypen](sql-data-warehouse-tables-data-types.md), [Verteilen einer Tabelle](sql-data-warehouse-tables-distribute.md), [Indizieren einer Tabelle](sql-data-warehouse-tables-index.md), [Partitionieren einer Tabelle](sql-data-warehouse-tables-partition.md), [Verwalten von Tabellenstatistiken](sql-data-warehouse-tables-statistics.md) und [Temporäre Tabellen](sql-data-warehouse-tables-temporary.md). |
| Die Statusanzeige für Transparent Data Encryption (TDE) wird im Azure-Portal nicht aktualisiert | Sie können den Status von TDE über [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) überprüfen. |

## <a name="differences-from-sql-database"></a>Unterschiede zu SQL-Datenbank

| Problem                                 | Lösung                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nicht unterstützte Funktionen von SQL-Datenbank     | Siehe [Nicht unterstützte Tabellenfunktionen](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nicht unterstützte SQL-Datenbank-Datentypen   | Siehe [Nicht unterstützte Datentypen](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Einschränkungen für gespeicherte Prozeduren          | In „Gespeicherte Prozeduren in SQL Data Warehouse“ werden unter [Einschränkungen](sql-data-warehouse-develop-stored-procedures.md#limitations) einige Einschränkungen für gespeicherte Prozeduren beschrieben. |
| UDFs unterstützen keine SELECT-Anweisungen | Dies ist eine aktuelle Beschränkung unserer benutzerdefinierten Funktionen (User-Defined Functions, UDFs).  Informationen zur unterstützten Syntax finden Sie unter [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . |

## <a name="next-steps"></a>Nächste Schritte

Für weitere Hilfe bei der Suche nach einer Lösung für Ihr Problem, stehen Ihnen hier weitere Ressourcen zur Verfügung.

* [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Funktionsanfragen](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Erstellen eines Supporttickets](sql-data-warehouse-get-started-create-support-ticket.md)
* [Frageseite von Microsoft Q&A (Fragen und Antworten)](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow-Forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
