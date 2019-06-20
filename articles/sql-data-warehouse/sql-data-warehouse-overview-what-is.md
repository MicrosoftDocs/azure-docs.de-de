---
title: Was ist Azure SQL Data Warehouse? | Microsoft-Dokumentation
description: Verteilte Datenbank der Unternehmensklasse, die relationale und nicht relationale Daten im Petabytebereich verarbeiten kann. Es handelt sich um das branchenweit erste Data Warehouse für die Cloud, das innerhalb weniger Sekunden, vergrößert, verkleinert und angehalten werden kann.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: a9126e9023091dd8c3df71f2aa2558a01227a8be
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428033"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Was ist Azure SQL Data Warehouse?

SQL Data Warehouse ist ein cloudbasiertes Enterprise Data Warehouse (EDW), das Massively Parallel Processing (MPP) zur schnellen Ausführung komplexer Abfragen für mehrere Petabytes an Daten nutzt. Verwenden Sie SQL Data Warehouse als Schlüsselkomponente einer Big Data-Lösung. Importieren Sie große Datenmengen mit einfachen [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest)-T-SQL-Abfragen in SQL Data Warehouse, und nutzen Sie dann die Vorteile von MPP für aufwendige Analysen. Im Zuge von Integrationen und Analysen wird das Data Warehouse zur Single Version of Truth, auf die sich Ihr Unternehmen bei der Gewinnung von Erkenntnissen verlassen kann.  

## <a name="key-component-of-big-data-solution"></a>Schlüsselkomponente für Big Data-Lösungen

SQL Data Warehouse ist eine Schlüsselkomponente einer umfassenden Big Data-Lösung in der Cloud.

![Data Warehouse-Lösung](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

In einer Clouddatenlösung werden Daten aus verschiedensten Quellen in Big Data-Speichern erfasst. Nach der Speicherung in einem Big Data-Speicher werden die Daten durch Hadoop, Spark und Machine Learning-Algorithmen vorbereitet und trainiert. Wenn die Daten für komplexe Analysen bereit sind, fragt SQL Data Warehouse die Big Data-Speicher mithilfe von PolyBase ab. PolyBase verwendet T-SQL-Standardabfragen, um die Daten an SQL Data Warehouse zu übermitteln.
 
SQL Data Warehouse speichert Daten in relationalen Tabellen mit spaltenbasiertem Speicher. Dieses Format zeichnet sich durch erheblich geringere Datenspeicherkosten und eine bessere Abfrageleistung aus. Für die in SQL Data Warehouse gespeicherten Daten können Sie umfangreiche Analysen durchführen. Im Vergleich zu herkömmlichen Datenbanksystemen dauern Analyseabfragen nur noch Sekunden statt Minuten (oder Stunden statt Tage). 

Die Analyseergebnisse können an Berichtsdatenbanken oder Anwendungen auf der ganzen Welt übermittelt werden. Business Analysts können so Erkenntnisse gewinnen und fundierte Geschäftsentscheidungen treffen.

## <a name="next-steps"></a>Nächste Schritte

- Erkunden der [Azure SQL Data Warehouse-Architektur](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Schnelles [Erstellen einer SQL Data Warehouse-Instanz][create a SQL Data Warehouse]
- [Laden von Beispieldaten][load sample data]
- Ansehen von [Videos](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Weitere SQL Data Warehouse-Ressourcen:  
* Suche in [Blogs]
* Übermitteln von [Funktionsanfragen]
* Suche in [Customer Advisory Team-Blogs]
* [Erstellen eines Supporttickets]
* Suche im [MSDN-Forum]
* Suche im [Stack Overflow-Forum]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Erstellen eines Supporttickets]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-Blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsanfragen]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-Forum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-Forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
