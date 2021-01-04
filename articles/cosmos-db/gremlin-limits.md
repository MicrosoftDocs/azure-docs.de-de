---
title: Grenzwerte für Azure Cosmos DB Gremlin
description: Referenzdokumentation zu Laufzeitbeschränkungen der Graph-Engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: sngun
ms.openlocfilehash: 4e638fdff67ad2d0bc6f191cdfd46867ab847923
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080106"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB: Gremlin-Grenzwerte
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In diesem Artikel werden die Grenzwerte der Azure Cosmos DB Gremlin-Engine beschrieben, und es wird erläutert, wie sie sich auf Kundendurchläufe auswirken können.

Cosmos DB Gremlin baut auf der Cosmos DB-Infrastruktur auf. Aus diesem Grund gelten weiterhin alle Grenzwerte, die in [Azure Cosmos DB-Diensteinschränkungen](./concepts-limits.md) erläutert werden.

## <a name="limits"></a>Einschränkungen

Wenn der Gremlin-Grenzwert erreicht wird, wird der Durchlauf mit einem **x-ms-status-code** von 429 abgebrochen. Dies ist ein Hinweis auf einen Drosselungsfehler. Weitere Informationen finden Sie unter [Gremlin-Serverantwortheader](gremlin-limits.md).

**Ressource**    | **Standardlimit** | **Erklärung**
--- | --- | ---
*Skriptlänge* | **64 KB** | Maximale Länge eines Gremlin-Durchlaufskripts pro Anforderung.
*Operatortiefe* | **400** |  Die Gesamtzahl eindeutiger Schritte eines Durchlaufs. Beispielsweise weist ```g.V().out()``` eine Operatoranzahl 2 auf: V() und out(), ```g.V('label').repeat(out()).times(100)``` weist eine Operatortiefe von 3 auf: V(), repeat() und out(), weil ```.times(100)``` ein Parameter für den Operator ```.repeat()``` ist.
*Parallelitätsgrad* | **32** | Maximale Anzahl von Speicherpartitionen, die mit einer einzelnen Anforderung an die Speicherebene abgefragt werden. Graphen mit Hunderten von Partitionen werden durch diesen Grenzwert beeinträchtigt.
*Grenzwert für Wiederholungen* | **32** | Maximale Anzahl von Durchläufen, die mit dem Operator ```.repeat()``` ausgeführt werden können. Bei jeder Iteration des ```.repeat()```-Schritts wird in den meisten Fällen ein Durchlauf mit „breitem Ansatz“ ausgeführt. Dies bedeutet, dass jeder Durchlauf auf maximal 32 Hops zwischen Vertices begrenzt ist.
*Durchlauftimeout* | **30 Sekunden** | Der Durchlauf wird abgebrochen, wenn dieser Zeitraum überschritten wird. Cosmos DB Graph ist eine OLTP-Datenbank, bei der der Großteil der Durchläufe innerhalb von Millisekunden durchgeführt wird. Verwenden Sie zum Ausführen von OLAP-Abfragen für Cosmos DB Graph [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) mit [Graph-Dataframes](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) und dem [Cosmos DB Spark-Connector](https://github.com/Azure/azure-cosmosdb-spark).
*Verbindungstimeout bei Leerlauf* | **1 Stunde** | Die Zeitspanne, in der der Gremlin-Dienst Websocketverbindungen im Leerlauf beibehalten wird. TCP-Keep-Alive-Pakete oder HTTP-Keep-Alive-Anforderungen erweitern die Lebensdauer der Verbindung nicht über diesen Grenzwert hinaus. Cosmos DB Graph-Engine betrachtet Websocketverbindungen als im Leerlauf, wenn darauf keine aktiven Gremlin-Anforderungen ausgeführt werden.
*Ressourcentoken pro Stunde* | **100** | Die Anzahl eindeutiger Ressourcentoken, die von Gremlin-Clients zum Herstellen einer Verbindung mit dem Gremlin-Konto in einer Region verwendet werden. Wenn die Anwendung das stündliche eindeutige Tokenlimit überschreitet, wird bei der nächsten Authentifizierungsanforderung `"Exceeded allowed resource token limit of 100 that can be used concurrently"` zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte
* [Azure Cosmos DB: Gremlin-Antwortheader](gremlin-headers.md)
* [Azure Cosmos DB-Ressourcentoken mit Gremlin](how-to-use-resource-tokens-gremlin.md)