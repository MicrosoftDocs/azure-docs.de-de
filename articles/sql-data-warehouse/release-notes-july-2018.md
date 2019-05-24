---
title: 'Azure SQL Data Warehouse: Anmerkungen zu dieser Version – Juni 2018 | Microsoft-Dokumentation'
description: Versionshinweise zu Azure SQL Data Warehouse
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/06/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4ec90bef9228eccb9556d07bb5680deb00e2550e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912176"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Neuerungen in Azure SQL Data Warehouse Juli 2018
Azure SQL Data Warehouse wird fortlaufend verbessert. In diesem Artikel informieren wir Sie über neue Features und Änderungen, die im Juli 2018 eingeführt wurden.

## <a name="lightning-fast-query-performance"></a>Blitzschnelle Abfragen
[Azure SQL Data Warehouse](https://aka.ms/sqldw) erreicht mit der Einführung des unmittelbaren Datenzugriffs, durch den Vorgänge mit unsortierten Daten verbessert werden, neue Leistungsbenchmarks. Der unmittelbare Datenzugriff verringert den Aufwand für Datenverschiebungsvorgänge, indem direkte native Datenvorgänge von SQL Server zu SQL Server durchgeführt werden. Durch die direkte Integration mit dem SQL Server-Modul für Datenverschiebungen ist SQL Data Warehouse nun **um 67 % schneller als Amazon Redshift**. Zugrunde liegt diesem Wert eine Workload nach dem anerkannten Industriestandard [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/).

![Azure SQL Data Warehouse ist schneller und kostengünstiger als Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>Quelle: [Gigaom Research Analyst Report: Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

Neben der Laufzeitleistung wurde im Bericht von [Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) auch das Preis-Leistungs-Verhältnis gemessen, indem die Kosten bestimmter Workloads in US-Dollar quantifiziert wurden. SQL Data Warehouse war für Workloads von 30 TB **um mindestens 23 % kostengünstiger** als Redshift. Mit der Möglichkeit von SQL Data Warehouse zum elastischen Skalieren der Computeleistung und zum Anhalten und Fortsetzen von Workloads bezahlen Kunden nur, wenn der Dienst verwendet wird, und senken ihre Kosten somit weiterhin.
![Azure SQL Data Warehouse ist schneller und kostengünstiger als Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>Quelle: [Gigaom Research Analyst Report: Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>Abfrageparallelität
SQL Data Warehouse stellt auch sicher, dass die Daten in der gesamten Organisation zugänglich sind. Microsoft hat den Dienst verbessert, sodass 128 Abfragen gleichzeitig unterstützt werden. Dadurch können mehr Benutzer dieselbe Datenbank abfragen, ohne von anderen Anforderungen blockiert zu werden. Im Vergleich dazu ist bei Amazon Redshift die maximale Anzahl gleichzeitiger Abfragen auf 50 beschränkt und dadurch der Datenzugriff innerhalb der Organisation begrenzt.

SQL Data Warehouse bietet diesen Zuwachs der Abfrageleistung und Abfrageparallelität ohne Preiserhöhung und unter Verwendung seiner einzigartigen Architektur mit Entkopplung von Speicher und Compute.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Feinere Granularität für regionen- und serverübergreifende Wiederherstellungen
Sie können jetzt regionen- und serverübergreifende Wiederherstellungen über jeden beliebigen Wiederherstellungspunkt durchführen anstatt georedundante Sicherungen auszuwählen, die alle 24 Stunden ausgeführt werden. Regionen- und serverübergreifende Wiederherstellungen werden sowohl für benutzerdefinierte als auch für automatische Wiederherstellungspunkte unterstützt, die eine feinere Granularität ermöglichen und so zusätzlichen Datenschutz bieten. Mit mehreren verfügbaren Wiederherstellungspunkten können Sie sicher sein, dass Ihr Data Warehouse bei der regionsübergreifenden Wiederherstellung logisch konsistent ist.

![Wiederherstellungsbefehl – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![Wiederherstellungsoptionen – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Weitere Informationen finden Sie im Blogbeitrag [Accelerated and Flexible Restore Points (Beschleunigte und flexible Wiederherstellungspunkte)](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/).

## <a name="20-minute-restorations"></a>20-minütige Wiederherstellungen
SQL Data Warehouse ermöglicht jetzt die Wiederherstellung für jedes Data Warehouse in **weniger als 20 Minuten** innerhalb der gleichen Region unabhängig von der Datenbankgröße. Die Wiederherstellungsdauer gilt für Wiederherstellungen auf demselben oder einem anderen logischen Server in derselben Region. Darüber hinaus wurde der Prozess für Momentaufnahmen verbessert, um die Zeitspanne zum Erstellen eines Wiederherstellungspunkts zu verkürzen. In niedrigeren Leistungsstufen (niedrigere DWU-Einstellungen) besteht die Verbesserung in einer *2-fachen Verringerung* der Zeit zum Erstellen einer Momentaufnahme.

Weitere Informationen finden Sie im Blogbeitrag [Accelerated and Flexible Restore Points (Beschleunigte und flexible Wiederherstellungspunkte)](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/).

## <a name="custom-restoration-configurations"></a>Benutzerdefinierte Wiederherstellungskonfigurationen
Sie können jetzt bei Wiederherstellungen im Azure-Portal die Leistungsstufe (DWU) ändern. Außerdem ist es möglich, die Wiederherstellung auf ein auf Gen2 aktualisiertes Data Warehouse auszuführen. Durch die Wiederherstellung auf eine Gen2-Instanz können Sie nun die Auswirkungen von Gen2 auswerten bevor Sie [ein Upgrade Ihres Gen1-Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation) durchführen.

![Benutzerdefinierte Wiederherstellungskonfiguration – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
Die gespeicherte Prozedur [Sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) wird häufig von Tools verwendet, um Metadaten zu Parametern in einem Transact-SQL-Batch abzurufen. Die Prozedur gibt für jeden Parameter im Batch eine Zeile mit den abgeleiteten Typinformationen für den entsprechenden Parameter an. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Das Resultset enthält Metadaten zum Parameter `@id` (Teilergebnisse werden angezeigt)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
Die gespeicherte Prozedur [sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) aktualisiert die Metadaten für ein Datenbankobjekt, wenn die zugrunde liegenden Metadaten durch Änderungen an den zugrunde liegenden Objekten veraltet sind. Dies kann auftreten, wenn die Basistabellen für eine Sicht geändert werden und die Sicht nicht neu erstellt wird. Dies erspart Ihnen den Schritt, abhängige Objekte zu löschen und neu zu erstellen.

Das nachstehende Beispiel zeigt eine Sicht, die aufgrund einer Änderung an der zugrunde liegenden Tabelle veraltet ist. Sie werden feststellen, dass die Daten für die erste Spaltenänderung („1“ bis „Mollie“) korrekt sind, aber der Spaltenname ungültig ist, und dass die zweite Spalte nicht vorhanden ist. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

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
