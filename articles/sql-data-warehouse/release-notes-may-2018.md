---
title: Azure SQL Data Warehouse – Versionshinweise Mai 2018 | Microsoft-Dokumentation
description: Versionshinweise zu Azure SQL Data Warehouse
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: eb933e8085ec956be713a18602b8baff9c87084b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912200"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Neuerungen in Azure SQL Data Warehouse Mai 2018 
Azure SQL Data Warehouse wird fortlaufend verbessert. In diesem Artikel informieren wir Sie über neue Features und Änderungen, die im Mai 2018 eingeführt wurden. 

## <a name="gen-2-instances"></a>Gen 2-Instanzen
![alt](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Der Azure SQL Data Warehouse-Tarif „Für Compute optimiert, Gen2“ setzt neue Leistungsmaßstäbe für das Data Warehousing in der Cloud. Kunden profitieren ab sofort von einer bis zu fünfmal höheren Abfrageleistung, einer viermal höheren Nebenläufigkeit und einer fünfmal höheren Computeleistung im Vergleich zur aktuellen Generation. Es können jetzt 128 Abfragen von einem einzelnen Cluster parallel verarbeitet werden – dieser Wert ist im Bereich der Data Warehousing-Dienste für die Cloud unerreicht.

Sehen Sie sich hierzu auch die Ankündigung im Blog [Turbocharge cloud analytics with Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) (in englischer Sprache) von Rohan Kumar an, Corporate Vice President, Azure Data.

## <a name="auto-statistics"></a>Automatische Statistik
Statistiken sind ein entscheidender Faktor bei der Optimierung der Abfrageplangenerierung in modernen kostenbasierten Optimierern wie z. B. der Engine in SQL Data Warehouse. Wenn alle Abfragen im Voraus bekannt sind, ist das Bestimmen der zu erstellenden Statistikobjekte eine leicht zu bewältigende Aufgabe. Wenn das System aber Ad-hoc-Abfragen und zufällige Abfragen verarbeiten muss – dies ist für Data Warehousing-Workloads typisch –, können Systemadministratoren möglicherweise nur schwer vorhersagen, welche Statistiken erstellt werden müssen. Dies führt dann zu suboptimalen Abfrageausführungsplänen und längeren Antwortzeiten für Abfragen. Eine Möglichkeit zur Abmilderung dieses Problems besteht darin, im Voraus Statistikobjekte für alle Tabellenspalten zu erstellen. Diese Vorgehensweise ist jedoch mit Einbußen verbunden, weil Statistikobjekte während des Ladevorgangs der Tabellen beibehalten werden müssen und zu längeren Ladezeiten führen.

SQL Data Warehouse unterstützt ab sofort die automatische Erstellung von Statistikobjekten und bietet dadurch eine höhere Flexibilität, Produktivität und Benutzerfreundlichkeit für Systemadministratoren und Entwickler. Gleichzeitig wird sichergestellt, dass das System weiterhin qualitativ hochwertige Ausführungspläne und beste Antwortzeiten liefert.

Führen Sie die folgende Anweisung aus, um die automatische Statistikerstellung in SQL Data Warehouse zu aktivieren oder zu deaktivieren:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Als Best Practice und Richtlinie wird empfohlen, die Option `AUTO_CREATE_STATISTICS` auf `ON` festzulegen.

> [!NOTE]
> Die automatische Statistikerstellung ist für alle neuen Data Warehouse-Instanzen *standardmäßig aktiviert*.
>  

Zusätzliche Details finden Sie im Artikel [ALTER DATABASE SET-Optionen](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options).

## <a name="rejected-row-support"></a>Unterstützung für abgelehnte Zeilen
Kunden verwenden aufgrund der hohen Leistung beim parallelen Laden von Daten häufig [PolyBase (externe Tabellen)](design-elt-data-loading.md), um Daten in SQL Data Warehouse zu laden. PolyBase ist auch beim Laden von Daten über [Azure Data Factory](https://azure.com/adf) das Standardmodell für Ladevorgänge. 

SQL Data Warehouse bietet mit dem `REJECTED_ROW_LOCATION`-Parameter für die [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)-Anweisung zusätzlich die Möglichkeit zum Definieren eines Speicherorts für abgelehnte Zeilen. Nach dem Ausführen von [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) aus der externen Tabelle werden alle Zeilen, die nicht geladen werden konnten, für eine weitere Untersuchung in einer Datei in der Nähe der Quelle gespeichert. 

Weitere Informationen zum Verhalten für abgelehnte Zeilen finden Sie im Blog [Load confidently with SQL Data Warehouse PolyBase Rejected Row Location](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) (in englischer Sprache).

Das folgende Beispiel zeigt die neue Syntax zum Angeben abgelehnter Zeilen.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTER VIEW
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) ermöglicht einem Benutzer das Ändern einer zuvor erstellten Sicht, ohne DELETE/CREATE für die Sicht ausführen und Berechtigungen erneut anwenden zu müssen. 

Mit dem folgenden Beispiel wird eine zuvor erstellte Sicht geändert.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
Die [The CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql)-Funktion gibt eine Zeichenfolge zurück, die aus der End-to-End-Verkettung von mindestens zwei Werten resultiert. Die verketteten Werte werden durch das im ersten Argument angegebenen Trennzeichen voneinander getrennt. Die `CONCAT_WS`-Funktion ist nützlich, um eine CSV-Ausgabe zu erzeugen.

Das folgende Beispiel zeigt die Verkettung mehrerer int-Werte mit einem Komma als Trennzeichen.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Die Anweisung gibt das folgende Ergebnis zurück:
```
result
---------
1,2,3
```
Das folgende Beispiel zeigt die Verkettung mehrerer verschiedener Datentypwerte mit einem Komma als Trennzeichen.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Die Anweisung gibt das folgende Ergebnis zurück:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
Die gespeicherte Systemprozedur [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) gibt Informationen zu den Datentypen zurück, die von der aktuellen Umgebung unterstützt werden. Sie wird häufig von Tools verwendet, die sich für eine Datentypuntersuchung über ODBC-Verbindungen verbinden.

Mit dem folgenden Beispiel werden Details zu allen von SQL Data Warehouse unterstützten Datentypen abgerufen.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Behavior Change bei SELECT INTO mit ORDER BY
SQL Data Warehouse blockiert ab sofort `SELECT INTO`-Abfragen, die eine `ORDER BY`-Klausel enthalten. Bisher konnte dieser Vorgang erfolgreich ausgeführt werden, wenn zunächst die Daten im Arbeitsspeicher sortiert wurden und dann beim Einfügen in die Zieltabelle neu sortiert wurden, um der Tabellenform zu entsprechen.

### <a name="previous-behavior"></a>Vorheriges Verhalten
Die folgende Anweisung konnte ohne zusätzlichen Verarbeitungsaufwand erfolgreich ausgeführt werden.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Aktuelles Verhalten
Die folgende Anweisung führt zu einem Fehler mit dem Hinweis, dass die `ORDER BY`-Klausel in einer `SELECT INTO`-Anweisung nicht unterstützt wird.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
Die Anweisung gibt folgenden Fehler zurück:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>SET PARSEONLY ON-Abfragestatus (Behavior Change)
Mithilfe der `SET PARSEONLY ON`-Syntax kann ein Benutzer die SQL Data Warehouse-Engine anweisen, die Syntax jeder T-SQL-Anweisung zu untersuchen und Fehlermeldungen zurückzugeben, ohne die Anweisung zu kompilieren oder auszuführen. Bisher verblieb der Status für diese Anweisungen in der Systemsicht [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) im Zustand `Running`. Die Sicht `sys.dm_pdw_exec_requests` gibt den Status jetzt als `Complete` zurück.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich mit den Grundlagen von SQL Data Warehouse vertraut gemacht haben, können Sie sich nun darüber informieren, wie Sie schnell [eine SQL Data Warehouse-Instanz erstellen][create a SQL Data Warehouse]. Falls Sie mit Azure noch nicht vertraut sind und auf neue Terminologie stoßen, ist das [Azure-Glossar][Azure glossary] sehr nützlich. Weitere SQL Data Warehouse-Ressourcen:  

* [Kundenerfolgsgeschichten]
* [Blogs]
* [Funktionsanfragen]
* [Videos]
* [Customer Advisory Team-Blogs]
* [Stack Overflow-Forum]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-Blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundenerfolgsgeschichten]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsanfragen]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-Forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
