---
title: Verwalten von Computeressourcen in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Funktionen zur horizontalen Leistungsskalierung in Azure SQL Data Warehouse. Skalieren Sie durch Anpassen der DWUs horizontal hoch, oder senken Sie die Kosten durch Anhalten des Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 47be738a4e5dcec144d482c28e39cbe950bba3e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748933"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Verwalten von Computeressourcen in Azure SQL Data Warehouse
Erfahren Sie mehr über die Verwaltung von Computeressourcen in Azure SQL Data Warehouse. Senken Sie die Kosten, indem Sie das Data Warehouse anhalten, oder skalieren Sie das Data Warehouse, um Leistungsanforderungen zu erfüllen. 

## <a name="what-is-compute-management"></a>Was ist Computeverwaltung?
In der Architektur von SQL Data Warehouse werden Speicher- und Computeressourcen voneinander getrennt, sodass diese unabhängig voneinander skaliert werden können. Daher können Sie Computeressourcen skalieren, um Leistungsanforderungen unabhängig vom Datenspeicher zu erfüllen. Sie können Computeressourcen auch anhalten und fortsetzen. Eine logische Konsequenz dieser Architektur ist es, dass die [Abrechnung](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) für Compute- und Speicherressourcen unabhängig voneinander erfolgt. Wenn Sie Ihr Data Warehouse für eine Weile nicht verwenden müssen, können Sie Computekosten sparen, indem Sie Computeressourcen anhalten. 

## <a name="scaling-compute"></a>Skalieren von Computeressourcen
Sie können Computeressourcen horizontal hoch- und wieder herunterskalieren, indem Sie die Einstellung für [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md) für das Data Warehouse anpassen. Die Lade- und die Abfrageleistung kann linear erhöht werden, wenn Sie weitere Data Warehouse-Einheiten hinzufügen. 

Schritte zur horizontalen Skalierung finden Sie in den Schnellstarts zum [Azure-Portal](quickstart-scale-compute-portal.md), zu [PowerShell](quickstart-scale-compute-powershell.md) oder zu [T-SQL](quickstart-scale-compute-tsql.md). Sie können auch horizontale Skalierungsvorgänge mit einer [REST-API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute) ausführen.

Um einen Skalierungsvorgang auszuführen, beendet SQL Data Warehouse zunächst alle eingehenden Abfragen und führt dann einen Rollback der Transaktionen durch, um einen konsistenten Zustand zu gewährleisten. Die Skalierung tritt erst auf, wenn der Transaktionsrollback abgeschlossen ist. Für einen Skalierungsvorgang trennt das System die Speicherebene von den Computeknoten, fügt Computeknoten hinzu und verbindet dann die Speicherebene wieder mit der Computeebene. Jedes Data Warehouse wird als 60 Verteilungen gespeichert, die auf die Computeknoten gleichmäßig verteilt sind. Durch Hinzufügen von weiteren Computeknoten wird die Computeleistung erhöht. Mit zunehmender Anzahl von Computeknoten verringert sich die Anzahl der Verteilungen pro Computeknoten, sodass mehr Computeleistung für Ihre Abfragen bereitsteht. Mit abnehmender Anzahl von Data Warehouse-Einheiten verringert sich die Anzahl der Computeknoten, was die Computeressourcen für Abfragen verringert.

Die folgende Tabelle zeigt, wie sich die Anzahl von Verteilungen pro Computeknoten ändert, wenn sich die Data Warehouse-Einheiten ändern.  DWU6000 bietet 60 Computeknoten und führt zu einer viel höheren Abfrageleistung als DWU100. 

| Data Warehouse-Einheiten  | \# Computeknoten | \# Verteilungen pro Knoten |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Ermitteln der richtigen Größe der Data Warehouse-Einheiten

Um von den Leistungsvorteilen der Skalierung insbesondere für größere Data Warehouse-Einheiten zu profitieren, sollten Sie ein Dataset von mindestens 1 TB verwenden. Um die optimale Anzahl der Data Warehouse-Einheiten für das Data Warehouse zu ermitteln, versuchen Sie, hoch und herunter zu skalieren. Führen Sie nach dem Laden Ihrer Daten einige Abfragen mit verschiedenen Mengen an Datawarehouse-Einheiten aus. Da die Skalierung schnell erfolgt, können Sie innerhalb einer Stunde verschiedene Leistungsebenen ausprobieren. 

Empfehlungen für die Ermittlung der besten Anzahl von Data Warehouse-Einheiten:

- Beginnen Sie bei einem in der Entwicklung befindlichen Data Warehouse mit einer geringen Anzahl von Data Warehouse-Einheiten.  Ein guter Ausgangspunkt ist DW400 oder DW200.
- Überwachen Sie die Anwendungsleistung, und beobachten Sie dabei die Anzahl der ausgewählten Data Warehouse-Einheiten im Vergleich zur beobachteten Leistung.
- Gehen Sie von einer linearen Skalierung aus, und bestimmen Sie, um wie viel Sie die Data Warehouse-Einheiten erhöhen oder verringern müssen. 
- Nehmen Sie weitere Anpassungen vor, bis Sie die optimale Leistungsstufe für Ihre geschäftlichen Anforderungen erreichen.

## <a name="when-to-scale-out"></a>Fälle für das horizontale Hochskalieren
Das horizontale Hochskalieren von Data Warehouse-Einheiten wirkt sich auf die folgenden Aspekte der Leistung aus:

- Lineares Verbessern der Systemleistung bei Scans, Aggregationen und CTAS-Anweisungen.
- Erhöhen der Anzahl von Readern und Writern für des Laden von Daten.
- Maximieren der Anzahl von gleichzeitigen Abfragen und Parallelitätsslots.

Empfehlungen für Fälle, in den Sie Data Warehouse-Einheiten skalieren sollten:

- Bevor Sie einen umfangreichen Vorgang zum Laden oder Transformieren von Daten durchführen, skalieren Sie horizontal hoch, damit die Daten schneller verfügbar sind.
- Skalieren Sie während der Hauptgeschäftszeiten horizontal hoch, um eine größere Anzahl gleichzeitiger Abfragen verarbeiten zu können. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Welche Optionen gibt es, wenn horizontales Hochskalieren die Leistung nicht verbessert?

Fügen Sie Data Warehouse-Einheiten hinzu, um die Parallelität zu erhöhen. Wenn die Arbeit gleichmäßig zwischen den Computeknoten verteilt wird, wird die Abfrageleistung durch die zusätzliche Parallelität verbessert. Wenn das horizontale Hochskalieren die Leistung nicht ändert, sind einige Ursachen denkbar. Die Daten können über die Verteilungen verstreut sein, oder Abfragen führen zu umfangreichen Datenverschiebung. Informationen zu Leistungsproblemen bei Abfragen finden Sie unter [Behandlung von Problemen mit der Abfrageleistung](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Anhalten und Fortsetzen von Computeressourcen
Das Anhalten einer Computeressource bewirkt, dass die Speicherebene von den Computeknoten getrennt wird. Die Computeressourcen werden aus Ihrem Konto freigegeben. Computeressourcen werden Ihnen nicht berechnet, während Computeressourcen angehalten sind. Beim Fortsetzen von Computeressourcen wird der Speicher wieder mit den Computeknoten verbunden, die Computeressourcen werden wieder berechnet. Wenn Sie ein Data Warehouse anhalten, geschieht Folgendes:

* Compute- und Speicherressourcen werden an den Pool der verfügbaren Ressourcen im Rechenzentrum zurückgegeben.
* Die Kosten für Data Warehouse-Einheiten sind für die Dauer der Pause gleich null.
* Die Speicherung von Daten ist nicht betroffen, und Ihre Daten bleiben intakt. 
* SQL Data Warehouse bricht alle Vorgänge ab, die gerade ausgeführt werden oder in der Warteschlange stehen.

Wenn Sie ein Data Warehouse fortsetzen, geschieht Folgendes:

* SQL Data Warehouse lädt Compute- und Speicherressourcen entsprechend Ihrer Einstellung für Data Warehouse-Einheiten.
* Computeressourcen werden wieder für die Data Warehouse-Einheiten berechnet.
* Ihre Daten sind verfügbar.
* Wenn das Data Warehouse online ist, müssen Sie Ihre Workloadabfragen neu starten.

Wenn das Data Warehouse immer verfügbar sein soll, könnten Sie es auf die kleinste Größe herunterskalieren, statt es anzuhalten. 

Schritte zum Anhalten und Fortsetzen finden Sie in den Schnellstarts zum [Azure-Portal](pause-and-resume-compute-portal.md) oder zu [PowerShell](pause-and-resume-compute-powershell.md). Sie Können auch die [REST-API zum Anhalten](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) oder die [REST-API zum Fortsetzen](sql-data-warehouse-manage-compute-rest-api.md#resume-compute) verwenden.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Beseitigen von Transaktionen vor dem Pausieren oder Skalieren
Es wird empfohlen, dass vorhandene Transaktionen abgeschlossen werden, bevor Sie einen Anhalte- oder Skalierungsvorgang initiieren.

Beim Anhalten oder Skalieren Ihres SQL Data Warehouse werden Ihre Abfragen hinter den Kulissen abgebrochen, wenn Sie die Anforderung zum Anhalten oder Skalieren initiieren.  Das Abbrechen einer einfachen SELECT-Abfrage ist ein schneller Vorgang und hat fast keinerlei Auswirkung auf den Zeitraum, der für das Pausieren oder Skalieren Ihrer Instanz anfällt.  Dagegen können Transaktionsabfragen, bei denen die Daten oder die Struktur der Daten geändert wird, unter Umständen nicht so schnell beendet werden.  **Transaktionsabfragen müssen laut Definition entweder vollständig abgeschlossen sein, oder es muss ein Rollback der Änderungen durchgeführt werden.**  Ein Rollback der Schritte, die von einer Transaktionsabfrage ausgeführt wurden, kann genauso lange oder sogar länger als die ursprüngliche Änderung dauern, die mit der Abfrage durchgeführt werden sollte.  Wenn Sie beispielsweise eine Abfrage abbrechen, mit der Zeilen gelöscht werden, und die Abfrage bereits eine Stunde lang ausgeführt wurde, kann es eine Stunde dauern, bis die gelöschten Zeilen wieder eingefügt wurden.  Wenn Sie das Pausieren oder Skalieren bei aktiven Transaktionen ausführen, kann das Pausieren oder Skalieren lange dauern, weil erst gewartet werden muss, bis das Rollback abgeschlossen ist.

Siehe auch: [Grundlagen von Transaktionen](sql-data-warehouse-develop-transactions.md) und [Optimieren von Transaktionen](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatisieren der Computeverwaltung
Informationen zum Automatisieren der Computeverwaltungsvorgänge finden Sie unter [Verwenden von Azure Functions zum Automatisieren von SQL DW-Computeebenen](manage-compute-with-azure-functions.md).

Jeder Vorgang zum horizontalen Skalieren, Anhalten und Fortsetzen kann mehrere Minuten in Anspruch nehmen. Wenn Sie das Skalieren, Anhalten oder Fortsetzen automatisch durchführen, empfiehlt es sich, eine Logik zu implementieren, die sicherstellt, dass bestimmte Vorgänge abgeschlossen wurden, bevor mit einer anderen Aktion fortgefahren wird. Überprüfen Sie den Data Warehouse-Zustand über verschiedene Endpunkte, um sicherzugehen, dass die Automatisierung dieser Vorgänge ordnungsgemäß implementiert werden kann. 

Informationen zum Überprüfen des Data Warehouse-Zustands finden Sie in den Schnellstarts zu [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) oder [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state). Sie können den Data Warehouse-Zustand auch mit einer [REST-API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state) überprüfen.


## <a name="permissions"></a>Berechtigungen

Zum Skalieren des Data Warehouse sind die in [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) beschriebenen Berechtigungen erforderlich.  Zum Anhalten und Fortsetzen ist die Berechtigung [Mitwirkender von SQL DB](../role-based-access-control/built-in-roles.md#sql-db-contributor) erforderlich, insbesondere „Microsoft.Sql/servers/databases/action“.


## <a name="next-steps"></a>Nächste Schritte
Ein weiterer Aspekt der Verwaltung von Computeressourcen ist das Zuordnen der verschiedenen Computeressourcen zu einzelnen Abfragen. Weitere Informationen finden Sie unter [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md).
