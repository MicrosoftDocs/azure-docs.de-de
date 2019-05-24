---
title: Azure SQL Data Warehouse – MPP-Architektur | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie in Azure SQL Data Warehouse durch Kombination von Massively Parallel Processing (MPP) und Azure Storage hohe Leistung und Skalierbarkeit erzielt werden.
services: sql-data-warehouse
author: happynicolle
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: nicw
ms.reviewer: igorstan
ms.openlocfilehash: c3cdd464dffc810e76cf101ac70c2a14bbc4f9ff
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790713"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse – MPP-Architektur (Massively Parallel Processing)
In diesem Artikel erfahren Sie, wie in Azure SQL Data Warehouse durch Kombination von Massively Parallel Processing (MPP) und Azure Storage hohe Leistung und Skalierbarkeit erzielt werden. 

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="mpp-architecture-components"></a>Komponenten der MPP-Architektur
SQL Data Warehouse nutzt eine horizontal hochskalierte Architektur zur Verteilung der Berechnungsverarbeitung von Daten auf mehrere Knoten. Die Skalierungseinheit ist eine Abstraktion der Computeleistung, die als [Data Warehouse-Einheit](what-is-a-data-warehouse-unit-dwu-cdwu.md) bezeichnet wird. In SQL Data Warehouse sind Compute- und Speicherressourcen getrennt, sodass Sie die Computedaten unabhängig von den Daten in Ihrem System skalieren können.

![SQL Data Warehouse-Architektur](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse verwendet eine knotenbasierte Architektur. Anwendungen stellen eine Verbindung her und geben T-SQL-Befehle an einen Steuerknoten aus, der auch der einzige Einstiegspunkt für das Data Warehouse ist. Der Steuerknoten führt die MPP-Engine aus, das Abfragen für die Parallelverarbeitung optimiert und dann Vorgänge an Serverknoten übergibt, sodass die Vorgänge parallel ausgeführt werden. Auf den Serverknoten werden alle Benutzerdaten in Azure Storage gespeichert und die parallelen Abfragen ausgeführt. Der Datenverschiebungsdienst (Data Movement Service, DMS) ist ein interner Dienst auf Systemebene, der Daten nach Bedarf zwischen den Knoten verschiebt, sodass Abfragen parallel ausgeführt und genaue Ergebnisse zurückgegeben werden. 

Die Entkoppelung von Speicher und Compute ermöglicht SQL Data Warehouse Folgendes:

* Anpassen der Größe der Computeleistung unabhängig von den Speicheranforderungen
* Erhöhen oder Verringern der Computeleistung ohne Datenverschiebung
* Anhalten der Computekapazität ohne Beeinträchtigung der Daten (und nur Bezahlung für den Speicher)
* Fortsetzen der Computekapazität während der Betriebszeiten

### <a name="azure-storage"></a>Azure-Speicher
SQL Data Warehouse verwendet Azure Storage zum Schützen Ihrer Benutzerdaten.  Da Ihre Daten in Azure Storage gespeichert und verwaltet werden, berechnet SQL Data Warehouse die Kosten für die Speichernutzung getrennt. Die Daten selbst werden zur Optimierung der Systemleistung in **Verteilungen** horizontal partitioniert. Beim Definieren der Tabelle können Sie das Shardingmuster zum Verteilen der Daten auswählen. SQL Data Warehouse unterstützt folgende Shardingmuster:

* Hash
* Round-Robin
* Replikat

### <a name="control-node"></a>Steuerknoten

Der Steuerknoten ist der zentrale Knoten des Data Warehouse. Dies ist das Front-End, das mit allen Anwendungen und Verbindungen interagiert. Die MPP-Engine wird auf dem Steuerknoten ausgeführt, um parallele Abfragen zu optimieren und zu koordinieren. Wenn Sie eine T-SQL-Abfrage an SQL Data Warehouse übermitteln, wird sie vom Steuerknoten in Abfragen transformiert, die für alle Verteilungen parallel ausgeführt werden.

### <a name="compute-nodes"></a>Serverknoten

Die Serverknoten liefern die Rechnerleistung. Verteilungen werden Serverknoten zur Verarbeitung zugeordnet. Wenn Sie für weitere Computeressourcen zahlen, werden die Verteilungen in SQL Data Warehouse den verfügbaren Serverknoten neu zugeordnet. Die Anzahl der Serverknoten liegt zwischen 1 und 60 und wird durch den Servicelevel für das Data Warehouse vorgegeben.

Jedem Serverknoten ist eine Knoten-ID zugewiesen, die in Systemsichten sichtbar ist. Sie können die Serverknoten-ID anzeigen, indem Sie in den Systemsichten, deren Namen mit „sys.pdw_nodes“ beginnen, nach der Spalte „node_id“ suchen. Eine Liste dieser Systemsichten finden Sie unter [MPP-Systemsichten](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Datenverschiebungsdienst
Der Datenverschiebungsdienst (Data Movement Service, DMS) ist die Datentransporttechnologie, mit der die Datenverschiebung zwischen den Serverknoten koordiniert wird. Einige Abfragen erfordern eine Datenverschiebung, damit sichergestellt ist, dass die parallelen Abfragen genaue Ergebnisse zurückgeben. Wenn eine Datenverschiebung erforderlich ist, wird durch den Datenverschiebungsdienst sichergestellt, dass die richtigen Daten an die richtige Position verschoben werden. 

## <a name="distributions"></a>Verteilungen

Eine Verteilung ist die Basiseinheit zur Speicherung und Verarbeitung von parallelen Abfragen, die für verteilte Daten ausgeführt werden. Wenn SQL Data Warehouse eine Abfrage ausführt, wird der Vorgang in 60 kleinere Abfragen unterteilt, die parallel ausgeführt werden. Die einzelnen 60 kleineren Abfragen werden jeweils auf einer der Datenverteilungen ausgeführt. Auf jedem Serverknoten werden eine oder mehrere der 60 Verteilungen verwaltet. Ein Data Warehouse mit maximalen Computeressourcen weist eine Verteilung pro Serverknoten auf. In einem Data Warehouse mit minimalen Computeressourcen befinden sich alle Verteilungen auf einem Serverknoten.  

## <a name="hash-distributed-tables"></a>Tabellen mit Hashverteilung
Eine Tabelle mit Hashverteilung kann die höchste Abfrageleistung für Verknüpfungen und Aggregationen in großen Tabellen bieten. 

Um Daten horizontal in eine Tabelle mit Hashverteilung zu partitionieren, verwendet SQL Data Warehouse eine Hashfunktion, um deterministisch jede Zeile einer Verteilung zuzuweisen. In der Tabellendefinition wird eine der Spalten als Verteilungsspalte festgelegt. Die Hashfunktion verwendet die Werte in der Verteilungsspalte, um jede Zeile einer Verteilung zuzuweisen.

Im folgenden Diagramm ist dargestellt, wie eine vollständige (nicht verteilte) Tabelle als Tabelle mit Hashverteilung gespeichert wird. 

![Verteilte Tabelle](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Verteilte Tabelle")  

* Jede Zeile gehört zu einer Verteilung.  
* Mit einem deterministischen Hashalgorithmus wird jede Zeile einer Verteilung zugewiesen.  
* Die Anzahl von Tabellenzeilen pro Verteilung variiert, wie an den unterschiedlichen Größen von Tabellen zu sehen ist.

Bei der Auswahl einer Verteilungsspalte sollten einige Aspekte in Bezug auf die Leistung beachtet werden, z.B. Eindeutigkeit, eventuelle Datenschiefe und die Arten von Abfragen, die im System ausgeführt werden.

## <a name="round-robin-distributed-tables"></a>Tabellen mit Roundrobin-Verteilung
Eine Roundrobin-Tabelle lässt sich am einfachsten erstellen und bietet schnelle Leistung, wenn sie als Stagingtabelle für Ladevorgänge verwendet wird.

In einer Tabelle mit Roundrobin-Verteilung werden Daten gleichmäßig auf die Tabelle aufgeteilt, dies erfolgt jedoch ohne weitere Optimierung. Eine Verteilung wird zunächst nach dem Zufallsprinzip ausgewählt. Dann werden Zeilenpuffer sequenziell Verteilungen zugewiesen. Die Daten lassen sich schnell in eine Roundrobin-Tabelle laden, die Abfrageleistung ist jedoch häufig bei Tabellen mit Hashverteilung besser. Verknüpfungen für Roundrobin-Tabellen machen eine neue Verteilung der Daten erforderlich. Dies erfordert zusätzlich Zeit.


## <a name="replicated-tables"></a>Replizierte Tabellen
Eine replizierte Tabelle bietet die schnellste Abfrageleistung für kleine Tabellen.

In einer Tabelle, die repliziert wird, wird eine vollständige Kopie der Tabelle auf jedem Serverknoten zwischengespeichert. Daher müssen beim Replizieren einer Tabelle Daten vor einer Verknüpfung oder Aggregation nicht mehr auf Serverknoten übertragen werden. Replizierte Tabellen werden am besten mit kleinen Tabellen verwendet. Zusätzlicher Speicherplatz ist erforderlich, und beim Schreiben von Daten entsteht zusätzlicher Mehraufwand, sodass dies für große Tabellen nicht praktikabel ist.  

Im folgenden Diagramm ist eine replizierte Tabelle dargestellt. Für SQL Data Warehouse wird die replizierte Tabelle jeweils in der ersten Verteilung auf jedem Serverknoten zwischengespeichert.  

![Replizierte Tabelle](media/sql-data-warehouse-distributed-data/replicated-table.png "Replizierte Tabelle") 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich mit den Grundlagen von SQL Data Warehouse vertraut gemacht haben, können Sie sich nun darüber informieren, wie Sie schnell [eine SQL Data Warehouse-Instanz erstellen][create a SQL Data Warehouse] und [Beispieldaten][load sample data] laden. Falls Sie mit Azure noch nicht vertraut sind und auf neue Terminologie stoßen, ist das [Azure-Glossar][Azure glossary] sehr nützlich. Weitere SQL Data Warehouse-Ressourcen:  

* [Kundenerfolgsgeschichten]
* [Blogs]
* [Funktionsanfragen]
* [Videos]
* [Customer Advisory Team-Blogs]
* [Erstellen eines Supporttickets]
* [MSDN-Forum]
* [Stack Overflow-Forum]
* [Twitter]

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
[Kundenerfolgsgeschichten]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-Blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsanfragen]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-Forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow-Forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
