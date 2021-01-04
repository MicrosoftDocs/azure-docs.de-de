---
title: 'Synapse SQL: Architektur'
description: In diesem Artikel erfahren Sie, wie Azure Synapse SQL die Verarbeitungsfunktionen für verteilte Abfragen mit Azure Storage kombiniert, um hohe Leistung und Skalierbarkeit zu erzielen.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: da6c9f6df0e9e74de297cf6c8f655b62e3446bad
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462718"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL-Architektur 

In diesem Artikel werden die Architekturkomponenten von Synapse SQL beschrieben.

## <a name="synapse-sql-architecture-components"></a>Architekturkomponenten von Synapse SQL

Synapse SQL nutzt eine aufskalierte Architektur zur Verteilung der Berechnungsverarbeitung von Daten auf mehrere Knoten. Compute- und Speicherressourcen sind getrennt, sodass Sie Compute unabhängig von den Daten in Ihrem System skalieren können. 

Beim dedizierten SQL-Pool ist die Skalierungseinheit eine Abstraktion der Computeleistung, die als [Data Warehouse-Einheit](resource-consumption-models.md) bezeichnet wird. 

Beim serverlosen SQL-Pool erfolgt die Skalierung automatisch, um den Anforderungen an die Abfrageressourcen zu entsprechen. Wenn sich die Topologie im Laufe der Zeit durch Hinzufügen, Entfernen von Knoten oder Failover ändert, passt sie sich den Änderungen an und stellt sicher, dass Ihre Abfrage über genügend Ressourcen verfügt und erfolgreich abgeschlossen wird. Die Abbildung unten beispielsweise zeigt einen serverlosen SQL-Pool unter Verwendung von 4 Serverknoten zum Ausführen einer Abfrage.

![Synapse SQL: Architektur](./media//overview-architecture/sql-architecture.png)

Synapse SQL verwendet eine knotenbasierte Architektur. Anwendungen stellen eine Verbindung her und geben T-SQL-Befehle an einen Steuerknoten aus, der der einzige Einstiegspunkt für Synapse SQL ist. 

Der Azure Synapse SQL-Steuerknoten nutzt eine Engine für verteilte Abfragen, um die Abfragen für die Parallelverarbeitung zu optimieren und dann Vorgänge an Computeknoten zu übergeben, sodass die Vorgänge parallel ausgeführt werden. 

Der Steuerknoten des serverlosen SQL-Pools verwendet eine DQP-Engine (Distributed Query Processing) zur Optimierung und Orchestrierung der verteilten Ausführung von Benutzerabfragen, indem er sie in kleinere Abfragen aufteilt, die auf Serverknoten ausgeführt werden. Jede kleine Abfrage wird als Aufgabe bezeichnet und stellt eine verteilte Ausführungseinheit dar. Sie liest Dateien aus dem Speicher, verknüpft Ergebnisse aus anderen Aufgaben, gruppiert oder ordnet Daten an, die aus anderen Aufgaben abgerufen wurden. 

Auf den Serverknoten werden alle Benutzerdaten in Azure Storage gespeichert und die parallelen Abfragen ausgeführt. Der Datenverschiebungsdienst (Data Movement Service, DMS) ist ein interner Dienst auf Systemebene, der Daten nach Bedarf zwischen den Knoten verschiebt, sodass Abfragen parallel ausgeführt und genaue Ergebnisse zurückgegeben werden. 

Durch die Entkopplung von Speicherung und Compute profitieren Sie beim Einsatz von Synapse SQL von einer unabhängigen Skalierung der Computeleistung, die unabhängig von Ihrem Speicherbedarf ist. Bei einem serverlosen SQL-Pool erfolgt die Skalierung automatisch, während bei einem dedizierten SQL-Pool Folgendes möglich ist:

* Vergrößern oder Verkleinern der Computeleistung innerhalb eines dedizierten SQL-Pools, ohne Daten verschieben zu müssen.
* Anhalten der Computekapazität ohne Beeinträchtigung der Daten (und nur Bezahlung für den Speicher)
* Fortsetzen der Computekapazität während der Betriebszeiten

## <a name="azure-storage"></a>Azure Storage

Synapse SQL nutzt Azure Storage, um Benutzerdaten zu schützen. Da Ihre Daten von Azure Storage gespeichert und verwaltet werden, werden die Kosten für Ihre Speichernutzung getrennt berechnet. 

Beim serverlosen SQL-Pool können Sie Dateien in Ihrem Data Lake schreibgeschützt abfragen, während Sie beim SQL-Pool auch Daten erfassen können. Wenn Daten im dedizierten SQL-Pool erfasst werden, werden sie zur Optimierung der Systemleistung in **Verteilungen** horizontal partitioniert. Beim Definieren der Tabelle können Sie das Shardingmuster zum Verteilen der Daten auswählen. Folgende Shardingmuster werden unterstützt:

* Hash
* Roundrobin
* Replikat

## <a name="control-node"></a>Steuerknoten

Der Steuerknoten ist der zentrale Knoten der Architektur. Dies ist das Front-End, das mit allen Anwendungen und Verbindungen interagiert. 

Die Engine für verteilte Abfragen wird in Synapse SQL auf dem Steuerknoten ausgeführt, um parallele Abfragen zu optimieren und zu koordinieren. Wenn Sie eine T-SQL-Abfrage an den dedizierten SQL-Pool übermitteln, wird sie vom Steuerknoten in Abfragen transformiert, die für die einzelnen Verteilungen parallel ausgeführt werden.

Im serverlosen SQL-Pool wird die DQP-Engine zur Optimierung und Koordinierung der verteilten Ausführung einer Benutzerabfrage auf dem Steuerknoten ausgeführt, indem sie in kleinere Abfragen aufgeteilt wird, die auf Serverknoten ausgeführt werden. Sie weist auch Sätze von Dateien zu, die von den einzelnen Knoten verarbeitet werden sollen.

## <a name="compute-nodes"></a>Serverknoten

Die Serverknoten liefern die Rechnerleistung. 

Im dedizierten SQL-Pool werden Verteilungen Serverknoten zur Verarbeitung zugeordnet. Wenn Sie für weitere Computeressourcen bezahlen, ordnet der Pool die Verteilungen den verfügbaren Serverknoten neu zu. Die Anzahl der Serverknoten liegt zwischen 1 und 60 und wird durch die Dienstebene für den dedizierten SQL-Pool vorgegeben. Jedem Serverknoten ist eine Knoten-ID zugewiesen, die in Systemsichten sichtbar ist. Sie können die Serverknoten-ID anzeigen, indem Sie in den Systemsichten, deren Namen mit „sys.pdw_nodes“ beginnen, nach der Spalte „node_id“ suchen. Eine Liste dieser Systemsichten finden Sie unter [Synapse SQL-Systemsichten](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

Im serverlosen SQL-Pool wird jedem Serverknoten eine Aufgabe und ein Satz von Dateien zugewiesen, für die die Aufgabe ausgeführt werden soll. „Aufgabe“ ist eine verteilte Abfrageausführungseinheit, die eigentlich Teil der vom Benutzer eingereichten Abfrage ist. Es erfolgt eine automatische Skalierung, um sicherzustellen, dass genügend Computeknoten zur Ausführung der Benutzerabfrage verwendet werden.

## <a name="data-movement-service"></a>Datenverschiebungsdienst

Der Datenverschiebungsdienst (Data Movement Service, DMS) ist die Datentransporttechnologie im dedizierten SQL-Pool, mit der die Datenverschiebung zwischen den Serverknoten koordiniert wird. Einige Abfragen erfordern eine Datenverschiebung, damit sichergestellt ist, dass die parallelen Abfragen genaue Ergebnisse zurückgeben. Wenn eine Datenverschiebung erforderlich ist, wird durch den Datenverschiebungsdienst sichergestellt, dass die richtigen Daten an die richtige Position verschoben werden.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Verteilungen

Eine Verteilung ist die Basiseinheit zur Speicherung und Verarbeitung von parallelen Abfragen, die für verteilte Daten im dedizierten SQL-Pool ausgeführt werden. Wenn der dedizierte SQL-Pool eine Abfrage ausführt, wird der Vorgang in 60 kleinere Abfragen unterteilt, die parallel ausgeführt werden. 

Die einzelnen 60 kleineren Abfragen werden jeweils auf einer der Datenverteilungen ausgeführt. Auf jedem Serverknoten werden eine oder mehrere der 60 Verteilungen verwaltet. Bei einem dedizierten SQL-Pool mit maximalen Computeressourcen befindet sich eine Verteilung auf jeweils einem Serverknoten. Bei einem dedizierten SQL-Pool mit minimalen Computeressourcen befinden sich alle Verteilungen auf einem einzigen Serverknoten. 

## <a name="hash-distributed-tables"></a>Tabellen mit Hashverteilung
Eine Tabelle mit Hashverteilung kann die höchste Abfrageleistung für Verknüpfungen und Aggregationen in großen Tabellen bieten. 

Zum horizontalen Partitionieren von Daten in eine Tabelle mit Hashverteilung verwendet der dedizierte SQL-Pool eine Hashfunktion, um jede Zeile einer einzigen Verteilung deterministisch zuzuweisen. In der Tabellendefinition wird eine der Spalten als Verteilungsspalte festgelegt. Die Hashfunktion verwendet die Werte in der Verteilungsspalte, um jede Zeile einer Verteilung zuzuweisen.

Im folgenden Diagramm ist dargestellt, wie eine vollständige (nicht verteilte) Tabelle als Tabelle mit Hashverteilung gespeichert wird. 

![Verteilte Tabelle](media//overview-architecture/hash-distributed-table.png "Verteilte Tabelle") 

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

Das nachstehende Diagramm zeigt eine replizierte Tabelle, die bei der ersten Verteilung auf den einzelnen Serverknoten zwischengespeichert wird. 

![Replizierte Tabelle](media/overview-architecture/replicated-table.png "Replizierte Tabelle") 

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt etwas über Synapse SQL erfahren haben, informieren Sie sich, wie Sie schnell [einen dedizierten SQL-Pool erstellen](../quickstart-create-sql-pool-portal.md) und [Beispieldaten laden](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) können (./sql-data-warehouse-load-sample-databases.md). Oder Sie beginnen mit der [Verwendung eines serverlosen SQL-Pools](../quickstart-sql-on-demand.md). Falls Sie mit Azure noch nicht vertraut sind und auf neue Terminologie stoßen, ist das [Azure-Glossar](../../azure-glossary-cloud-terminology.md) sehr nützlich. 
