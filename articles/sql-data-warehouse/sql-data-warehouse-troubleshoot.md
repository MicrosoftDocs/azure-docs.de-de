---
title: Problembehandlung bei Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Problembehandlung bei Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 4/26/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 02591185914f3b04a70af3b7c5d607f4a2865806
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154267"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Problembehandlung bei Azure SQL Data Warehouse
Dieser Artikel enthält allgemeine Fragen zur Problembehandlung.

## <a name="connecting"></a>Herstellen einer Verbindung
| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Fehler bei der Anmeldung für den Benutzer 'NT-AUTORITÄT\ANONYME ANMELDUNG'. (Microsoft SQL Server, Fehler: 18456) | Dieser Fehler tritt auf, wenn ein AAD-Benutzer versucht, eine Verbindung mit der Masterdatenbank herzustellen, aber nicht über einen Masterdatenbank-Benutzer verfügt.  Zum Beheben dieses Problems geben Sie entweder das SQL Data Warehouse an, mit dem Sie gerade eine Verbindung herstellen möchten, oder fügen Sie den Benutzer der Masterdatenbank hinzu.  Weitere Informationen finden Sie im Artikel [Sichern einer Datenbank in SQL Data Warehouse][Security overview]. |
| Der Serverprinzipal „MeinBenutzername“ kann unter dem aktuellen Sicherheitskontext nicht auf die Datenbank „Master“ zugreifen. Standardbenutzerdatenbank kann nicht geöffnet werden. Anmeldefehler. Fehler bei der Anmeldung für den Benutzer 'MeinBenutzername'. (Microsoft SQL Server, Fehler: 916) | Dieser Fehler tritt auf, wenn ein AAD-Benutzer versucht, eine Verbindung mit der Masterdatenbank herzustellen, aber nicht über einen Masterdatenbank-Benutzer verfügt.  Zum Beheben dieses Problems geben Sie entweder das SQL Data Warehouse an, mit dem Sie gerade eine Verbindung herstellen möchten, oder fügen Sie den Benutzer der Masterdatenbank hinzu.  Weitere Informationen finden Sie im Artikel [Sichern einer Datenbank in SQL Data Warehouse][Security overview]. |
| CTAIP-Fehler                                                  | Dieser Fehler kann auftreten, wenn zwar eine Anmeldung für die SQL Server-Masterdatenbank erstellt wurde, dies jedoch in der SQL Data Warehouse-Datenbank unterlassen wurde.  Lesen Sie in diesem Fall den Artikel [Sichern einer Datenbank in SQL Data Warehouse][Security overview].  In diesem Artikel wird erläutert, wie Sie zunächst eine Anmeldung und einen Benutzer für die Masterdatenbank erstellen und anschließend in der SQL Data Warehouse-Datenbank einen Benutzer erstellen. |
| Von der Firewall blockiert                                          | Azure SQL-Datenbanken sind durch Firewalls auf Server- und Datenbankebene geschützt, um sicherzustellen, dass nur bekannte IP-Adressen Zugriff auf eine Datenbank haben. Firewalls sind standardmäßig sicher. Sie müssen daher eine IP-Adresse oder einen Adressbereich explizit aktivieren, bevor Sie eine Verbindung herstellen können.  Führen Sie die Schritte aus dem Abschnitt [Erstellen einer Firewallregel auf Serverebene im Azure-Portal][Configure server firewall access for your client IP] der [Bereitstellungsanweisungen][Provisioning instructions] aus, um Ihre Firewall für den Zugriff zu konfigurieren. |
| Verbindung mit Tool oder Treiber kann nicht hergestellt werden                           | SQL Data Warehouse empfiehlt, zum Abfragen von Daten [SSMS][SSMS], [SSDT für Visual Studio][SSDT for Visual Studio] oder [sqlcmd][sqlcmd] zu verwenden. Weitere Informationen zu Treibern und zum Herstellen einer Verbindung mit SQL Data Warehouse finden Sie in den Artikeln [Treiber für Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] und [Verbinden mit Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse]. |

## <a name="tools"></a>Tools
| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| In Visual Studio-Objekt-Explorer fehlen AAD-Benutzer           | Dies ist ein bekanntes Problem.  Sie können die Benutzer in [ys.database_principals][sys.database_principals] anzeigen, um dieses Problem zu umgehen.  Weitere Informationen zur Verwendung von Azure Active Directory mit SQL Data Warehouse finden Sie unter [Authentifizierung in Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse]. |
| Manuelle, über den Assistenten für die Skripterstellung erstellte Skripts oder über SSMS hergestellte Verbindungen sind langsam, reagieren nicht mehr oder erzeugen Fehler | Vergewissern Sie sich, dass Benutzer in der Masterdatenbank erstellt wurden. Stellen Sie zudem in den Skriptoptionen sicher, dass die Engine-Edition auf „Microsoft Azure SQL Data Warehouse Edition“ und der Engine-Typ auf „Microsoft Azure SQL-Datenbank“ festgelegt ist. |
| Fehler beim Generieren von Skripts in SSMS                             | Beim Generieren von Skripts für SQL Data Warehouse tritt ein Fehler auf, wenn die Option „Generate script for dependent objects“ (Skript für abhängige Objekte generieren) auf TRUE festgelegt ist. Zum Umgehen dieses Problems müssen Benutzer manuell zu „Extras -> Optionen -> SQL Server-Objekt-Explorer -> Generate script for dependent objects (Skript für abhängige Objekte generieren)“ wechseln und diese Option auf FALSE festlegen. |

## <a name="performance"></a>Leistung
| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Behandlung von Problemen mit der Abfrageleistung                            | Wenn Sie die Problembehandlung für eine bestimmte Abfrage durchführen möchten, sollten Sie sich zunächst über das [Untersuchen der Ausführung von Abfragen][Learning how to monitor your queries] informieren. |
| Schlechte Abfrageleistung und Planung ist häufig das Ergebnis fehlender Statistiken | Die häufigste Ursache für schlechte Leistung ist das Fehlen von Statistiken für Ihre Tabellen.  Ausführliche Informationen dazu, wie Sie Statistiken erstellen und warum sie für die Leistung wichtig sind, finden Sie unter [Verwalten von Statistiken für Tabellen in SQL Data Warehouse][Statistics]. |
| Geringe Parallelität/Abfragen in der Warteschlange                             | Das Verständnis der [Workloadverwaltung][Workload management] ist wichtig zur Abwägung von Speicherbelegung und Parallelität. |
| Implementieren von bewährten Methoden                              | Wenn Sie die Leistung bei Ihren Abfragen verbessern möchten, ist der Artikel [Bewährte Methoden für SQL Data Warehouse][SQL Data Warehouse best practices] ein idealer Ausgangspunkt. |
| Verbessern der Leistung mit der Skalierung                      | Mitunter besteht der Weg zum Verbessern der Leistung einfach darin, mehr Computeleistung für Abfragen zur Verfügung zu stellen, indem Sie [SQL Data Warehouse skalieren][Scaling your SQL Data Warehouse]. |
| Schlechte Leistung aufgrund von schlechter Indexqualität     | Es kann vorkommen, dass sich Abfragen verlangsamen, da eine [schlechte Qualität der Columnstore-Indizes][Poor columnstore index quality] vorliegt.  Weitere Informationen finden Sie unter [Neuerstellen von Indizes zur Verbesserung der Segmentqualität][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Systemverwaltung
| Problem                                                        | Lösung                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Der Vorgang konnte nicht ausgeführt werden, da der Server das zulässige Datenbanktransaktionseinheit-Kontingent von 45.000 überschreiten würde. | Reduzieren Sie entweder die [DWU][DWU] der Datenbank, die Sie erstellen möchten, oder [fordern Sie eine Erhöhung des Kontingents an][request a quota increase]. |
| Untersuchen der Speicherauslastung                              | Informationen zu den Grundlagen der Speicherauslastung des Systems finden Sie unter [Tabellengrößen][Table sizes]. |
| Hilfe beim Verwalten von Tabellen                                    | Hilfe zur Verwaltung von Tabellen finden Sie in der [Übersicht über Tabellen][Overview].  Dieser Artikel enthält auch Links zu ausführlicheren Themen. Hierzu zählen beispielsweise [Tabellendatentypen][Data types], [Verteilen einer Tabelle][Distribute], [Indizieren einer Tabelle][Index], [Partitionieren einer Tabelle][Partition], [Verwalten von Tabellenstatistiken][Statistics] und [Temporäre Tabellen][Temporary]. |
| Die Statusanzeige für Transparent Data Encryption (TDE) wird im Azure-Portal nicht aktualisiert | Sie können den Status von TDE über [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) überprüfen. |

## <a name="polybase"></a>PolyBase
| Problem                                           | Lösung                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| Bei Exporten mit TINYINT- und DATE-Typen tritt ein Fehler auf.             | Bei Parquet- und ORC-Dateiformaten müssen die Werte für den DATE-Typ zwischen 1970-01-01 00:00:01 UTC und 2038-01-19 03:14:07 liegen. Werte für den TINYINT-Typ müssen zwischen 0 und 127 liegen.    |
| Problem beim Parquet-Typ DECIMAL: Beim Schreiben aus dem Spark-Typ „DecimalType(18,4)“ und beim Importieren in eine Spalte vom Typ „double“ oder „real“ wird folgende Fehlermeldung zurückgegeben: „Fehler: ‚java.base/java.lang.Long‘ kann nicht in ‚java.base/java.lang.Float‘ umgewandelt werden.“ | Sie müssen in „bigint“ importieren und durch 10000 dividieren oder den SQL DW-Connector [Databricks] verwenden. |
| Problem beim Parquet-Typ DATE: Beim Schreiben aus dem Spark-Typ „Date“ und beim Importieren in eine Spalte vom Typ „date“ oder „datetime“ wird folgende Fehlermeldung zurückgegeben: „Fehler: ‚java.base/java.lang.Integer‘ kann nicht in ‚parquet.io.api.Binary‘ umgewandelt werden.“ | Sie müssen einen anderen Spark-Typ (int) verwenden und das Datum berechnen oder den SQL DW-Connector [Databricks] verwenden. |

## <a name="differences-from-sql-database"></a>Unterschiede zu SQL-Datenbank
| Problem                                 | Lösung                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nicht unterstützte Funktionen von SQL-Datenbank     | Siehe [Nicht unterstützte Tabellenfunktionen][Unsupported table features]. |
| Nicht unterstützte SQL-Datenbank-Datentypen   | Siehe [Nicht unterstützte Datentypen][Unsupported data types].        |
| DELETE- und UPDATE-Einschränkungen         | Siehe [UPDATE-Problemumgehungen][UPDATE workarounds], [DELETE-Problemumgehungen][DELETE workarounds] und [Umgehen von nicht unterstützten Funktionen mit CTAS][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| MERGE-Anweisung wird nicht unterstützt      | Siehe [MERGE-Problemumgehungen][MERGE workarounds].                  |
| Einschränkungen für gespeicherte Prozeduren          | In „Gespeicherte Prozeduren in SQL Data Warehouse“ werden unter [Einschränkungen][Stored procedure limitations] einige Einschränkungen für gespeicherte Prozeduren beschrieben. |
| UDFs unterstützen keine SELECT-Anweisungen | Dies ist eine aktuelle Beschränkung unserer benutzerdefinierten Funktionen (User-Defined Functions, UDFs).  Informationen zur unterstützten Syntax finden Sie unter der [CREATE-FUNKTION][CREATE FUNCTION]. |

## <a name="next-steps"></a>Nächste Schritte
Für weitere Hilfe bei der Suche nach einer Lösung für Ihr Problem, stehen Ihnen hier weitere Ressourcen zur Verfügung.

* [Blogs]
* [Funktionsanfragen]
* [Videos]
* [CAT Team-Blogs]
* [Erstellen eines Supporttickets]
* [MSDN-Forum]
* [Stack Overflow-Forum]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Erstellen eines Supporttickets]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT Team-Blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsanfragen]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-Forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow-Forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
