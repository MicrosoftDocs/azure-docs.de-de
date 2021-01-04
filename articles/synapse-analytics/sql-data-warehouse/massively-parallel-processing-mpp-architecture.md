---
title: Architektur des dedizierten SQL-Pools (früher SQL DW)
description: In diesem Artikel erfahren Sie, wie der dedizierte SQL-Pool (früher SQL DW) in Azure Synapse Analytics die Verarbeitungsfunktionen für verteilte Abfragen mit Azure Storage kombiniert, um hohe Leistung und Skalierbarkeit zu erzielen.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 45c7f89f773095a102429c07f7441223de3c2dec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448266"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Architektur des dedizierten SQL-Pools (früher SQL DW) in Azure Synapse Analytics

Azure Synapse Analytics ist ein Analysedienst, der Data Warehousing für Unternehmen mit Big Data-Analysen vereint. Er ermöglicht es Ihnen, Daten zu ihren Bedingungen abzufragen.

> [!NOTE]
>Informieren Sie sich genauer in der [Dokumentation zu Azure Synapse Analytics](../overview-what-is.md).
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Architekturkomponenten von Synapse SQL

[Der dedizierte SQL-Pool (früher SQL DW)](sql-data-warehouse-overview-what-is.md) nutzt eine Architektur mit horizontaler Skalierung zum Verteilen der Berechnungsverarbeitung von Daten auf mehrere Knoten. Die Skalierungseinheit ist eine Abstraktion der Computeleistung, die als [Data Warehouse-Einheit](what-is-a-data-warehouse-unit-dwu-cdwu.md) bezeichnet wird. Compute- und Speicherressourcen sind getrennt, sodass Sie Compute unabhängig von den Daten in Ihrem System skalieren können.

![Architektur des dedizierten SQL-Pools (früher SQL DW)](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Der dedizierte SQL-Pool (früher SQL DW) verwendet eine knotenbasierte Architektur. Anwendungen stellen eine Verbindung her und geben T-SQL-Befehle an einen Steuerknoten aus. Der Steuerknoten hostet die Engine für verteilte Abfragen, die Abfragen für die Parallelverarbeitung optimiert und dann Vorgänge an Serverknoten übergibt, sodass die Vorgänge parallel ausgeführt werden.

Auf den Serverknoten werden alle Benutzerdaten in Azure Storage gespeichert und die parallelen Abfragen ausgeführt. Der Datenverschiebungsdienst (Data Movement Service, DMS) ist ein interner Dienst auf Systemebene, der Daten nach Bedarf zwischen den Knoten verschiebt, sodass Abfragen parallel ausgeführt und genaue Ergebnisse zurückgegeben werden.

Mit entkoppeltem Speicher und Compute ist bei Verwendung eines dedizierten SQL-Pools (früher SQL DW) Folgendes möglich:

- Anpassen der Größe der Computeleistung unabhängig von den Speicheranforderungen
- Vergrößern oder Verkleinern der Computeleistung innerhalb eines dedizierten SQL-Pools (früher SQL DW), ohne Daten verschieben zu müssen.
- Anhalten der Computekapazität ohne Beeinträchtigung der Daten und nur Bezahlung für den Speicher
- Fortsetzen der Computekapazität während der Betriebszeiten

### <a name="azure-storage"></a>Azure Storage

Der dedizierte SQL-Pool (früher SQL DW) nutzt Azure Storage, um Ihre Benutzerdaten zu schützen.  Da Ihre Daten von Azure Storage gespeichert und verwaltet werden, werden die Kosten für Ihre Speichernutzung getrennt berechnet. Die Daten werden zur Optimierung der Systemleistung in **Verteilungen** horizontal partitioniert. Beim Definieren der Tabelle können Sie das Shardingmuster zum Verteilen der Daten auswählen. Folgende Shardingmuster werden unterstützt:

- Hash
- Roundrobin
- Replikat

### <a name="control-node"></a>Steuerknoten

Der Steuerknoten ist der zentrale Knoten der Architektur. Dies ist das Front-End, das mit allen Anwendungen und Verbindungen interagiert. Die Engine für verteilte Abfragen wird auf dem Steuerknoten ausgeführt, um parallele Abfragen zu optimieren und zu koordinieren. Wenn Sie eine T-SQL-Abfrage übermitteln, wird sie vom Steuerknoten in Abfragen transformiert, die für die einzelnen Verteilungen parallel ausgeführt werden.

### <a name="compute-nodes"></a>Serverknoten

Die Serverknoten liefern die Rechnerleistung. Verteilungen werden Serverknoten zur Verarbeitung zugeordnet. Wenn Sie weitere kostenpflichtige Computeressourcen nutzen, werden die Verteilungen den verfügbaren Serverknoten neu zugeordnet. Die Anzahl der Serverknoten liegt zwischen 1 und 60 und wird durch die Dienstebene für Synapse SQL bestimmt.

Jedem Serverknoten ist eine Knoten-ID zugewiesen, die in Systemsichten sichtbar ist. Sie können die Serverknoten-ID anzeigen, indem Sie in den Systemsichten, deren Namen mit „sys.pdw_nodes“ beginnen, nach der Spalte „node_id“ suchen. Eine Liste dieser Systemsichten finden Sie unter [Synapse SQL-Systemsichten](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="data-movement-service"></a>Datenverschiebungsdienst

Der Datenverschiebungsdienst (Data Movement Service, DMS) ist die Datentransporttechnologie, mit der die Datenverschiebung zwischen den Serverknoten koordiniert wird. Einige Abfragen erfordern eine Datenverschiebung, damit sichergestellt ist, dass die parallelen Abfragen genaue Ergebnisse zurückgeben. Wenn eine Datenverschiebung erforderlich ist, wird durch den Datenverschiebungsdienst sichergestellt, dass die richtigen Daten an die richtige Position verschoben werden.

## <a name="distributions"></a>Verteilungen

Eine Verteilung ist die Basiseinheit zur Speicherung und Verarbeitung von parallelen Abfragen, die für verteilte Daten ausgeführt werden. Wenn Synapse SQL eine Abfrage ausführt, wird der Vorgang in 60 kleinere Abfragen unterteilt, die parallel ausgeführt werden.

Die einzelnen 60 kleineren Abfragen werden jeweils auf einer der Datenverteilungen ausgeführt. Auf jedem Serverknoten werden eine oder mehrere der 60 Verteilungen verwaltet. Bei einem dedizierten SQL-Pool (früher SQL DW) mit maximalen Computeressourcen befindet sich eine Verteilung auf jeweils einem Serverknoten. Bei einem dedizierten SQL-Pool (früher SQL DW) mit minimalen Computeressourcen befinden sich alle Verteilungen auf einem einzigen Serverknoten.  

## <a name="hash-distributed-tables"></a>Tabellen mit Hashverteilung

Eine Tabelle mit Hashverteilung kann die höchste Abfrageleistung für Verknüpfungen und Aggregationen in großen Tabellen bieten.

Zum horizontalen Partitionieren von Daten in eine Tabelle mit Hashverteilung wird eine Hashfunktion verwendet, um jede Zeile deterministisch zu einer einzigen Verteilung zuzuweisen. In der Tabellendefinition wird eine der Spalten als Verteilungsspalte festgelegt. Die Hashfunktion verwendet die Werte in der Verteilungsspalte, um jede Zeile einer Verteilung zuzuweisen.

Im folgenden Diagramm ist dargestellt, wie eine vollständige (nicht verteilte) Tabelle als Tabelle mit Hashverteilung gespeichert wird.

![Verteilte Tabelle](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Verteilte Tabelle")  

- Jede Zeile gehört zu einer Verteilung.  
- Mit einem deterministischen Hashalgorithmus wird jede Zeile einer Verteilung zugewiesen.  
- Die Anzahl von Tabellenzeilen pro Verteilung variiert, wie an den unterschiedlichen Größen von Tabellen zu sehen ist.

Bei der Auswahl einer Verteilungsspalte sollten einige Aspekte in Bezug auf die Leistung beachtet werden, z.B. Eindeutigkeit, eventuelle Datenschiefe und die Arten von Abfragen, die im System ausgeführt werden.

## <a name="round-robin-distributed-tables"></a>Tabellen mit Roundrobin-Verteilung

Eine Roundrobin-Tabelle lässt sich am einfachsten erstellen und bietet schnelle Leistung, wenn sie als Stagingtabelle für Ladevorgänge verwendet wird.

In einer Tabelle mit Roundrobin-Verteilung werden Daten gleichmäßig auf die Tabelle aufgeteilt, dies erfolgt jedoch ohne weitere Optimierung. Eine Verteilung wird zunächst nach dem Zufallsprinzip ausgewählt. Dann werden Zeilenpuffer sequenziell Verteilungen zugewiesen. Die Daten lassen sich schnell in eine Roundrobin-Tabelle laden, die Abfrageleistung ist jedoch häufig bei Tabellen mit Hashverteilung besser. Verknüpfungen für Roundrobin-Tabellen machen eine neue Verteilung der Daten erforderlich. Dies erfordert zusätzlich Zeit.

## <a name="replicated-tables"></a>Replizierte Tabellen

Eine replizierte Tabelle bietet die schnellste Abfrageleistung für kleine Tabellen.

In einer Tabelle, die repliziert wird, wird eine vollständige Kopie der Tabelle auf jedem Serverknoten zwischengespeichert. Daher müssen beim Replizieren einer Tabelle Daten vor einer Verknüpfung oder Aggregation nicht mehr auf Serverknoten übertragen werden. Replizierte Tabellen werden am besten mit kleinen Tabellen verwendet. Zusätzlicher Speicherplatz ist erforderlich, und beim Schreiben von Daten entsteht zusätzlicher Mehraufwand, sodass dies für große Tabellen nicht praktikabel ist.  

Das nachstehende Diagramm zeigt eine replizierte Tabelle, die bei der ersten Verteilung auf den einzelnen Serverknoten zwischengespeichert wird.  

![Replizierte Tabelle](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Replizierte Tabelle")

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Grundlagen von Azure Synapse vertraut gemacht haben, informieren Sie sich nun darüber, wie Sie innerhalb kurzer Zeit [einen dedizierten SQL-Pool (früher SQL DW) erstellen](create-data-warehouse-portal.md) und [Beispieldaten laden](load-data-from-azure-blob-storage-using-polybase.md) können. Falls Sie mit Azure noch nicht vertraut sind und auf neue Terminologie stoßen, ist das [Azure-Glossar](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sehr nützlich. Oder sehen Sie sich einige der folgenden weiteren Azure Synapse-Ressourcen an:  

- [Kundenerfolgsgeschichten](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Funktionsanfragen](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Videos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Erstellen eines Supporttickets](sql-data-warehouse-get-started-create-support-ticket.md)
- [Frageseite von Microsoft Q&A (Fragen und Antworten)](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow-Forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
