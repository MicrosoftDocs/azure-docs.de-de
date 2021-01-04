---
title: Apache Cassandra- und Azure Cosmos DB-Konsistenzebenen
description: Hier werden die Apache Cassandra- und Azure Cosmos DB-Konsistenzebenen erläutert.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: a02076c09d038b02c0ab846440ad14e799271733
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339953"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra- und Azure Cosmos DB-Konsistenzebenen
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Im Gegensatz zu Azure Cosmos DB bietet Apache Cassandra nativ keine genau definierten Konsistenzgarantien. Stattdessen bietet Apache Cassandra eine Schreib- und Lesekonsistenzebene, um in den Genuss von Vorteilen in Bezug auf Hochverfügbarkeit, Konsistenz und Latenz zu kommen. Bei Verwenden der Cassandra-API von Azure Cosmos DB gilt Folgendes:

* Die Schreibkonsistenzebene von Apache Cassandra wird der Standardkonsistenzebene zugeordnet, die in Ihrem Azure Cosmos-Konto konfiguriert ist. Die Konsistenz für einen Schreibvorgang (CL) kann nicht anforderungsweise geändert werden.

* Azure Cosmos DB ordnet die vom Cassandra-Clienttreiber angegebene Lesekonsistenzebene dynamisch einer der Azure Cosmos DB-Konsistenzebenen zu, die bei einer Leseanforderung dynamisch konfiguriert wird.

## <a name="mapping-consistency-levels"></a>Zuordnen von Konsistenzebenen

Die folgende Tabelle veranschaulicht, wie die nativen Cassandra-Konsistenzebenen den Konsistenzebenen von Azure Cosmos DB zugeordnet werden, wenn die Cassandra-API verwendet wird:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Zuordnung zum Cassandra-Konsistenzmodell" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Wenn Ihr Azure Cosmos-Konto mit einer anderen Konsistenzebene als der starken Konsistenz konfiguriert ist, können Sie anhand der *PBS* -Metrik (Probabilistically Bounded Staleness) die Wahrscheinlichkeit ermitteln, dass Ihre Clients starke und konsistente Lesevorgänge für Ihre Workloads erzielen. Diese Metrik wird im Azure-Portal verfügbar gemacht. Weitere Informationen finden Sie unter [Überwachen der PBS-Metrik (Probabilistically Bounded Staleness)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

PBS (Probabilistic Bounded Staleness) zeigt, wie letztendlich Ihre letztendliche Konsistenz ist. Diese Metrik gibt Aufschluss darüber, wie oft Sie eine stärkere Konsistenz erreichen können als die Konsistenzebene, die Sie aktuell in Ihrem Azure Cosmos-Konto konfiguriert haben. Das heißt, Sie sehen die Wahrscheinlichkeit (gemessen in Millisekunden), dass stark konsistente Lesevorgänge in Regionen mit Schreib- und Lesevorgängen auftreten.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über die globale Verteilung und Konsistenzebenen in Azure Cosmos DB:

* [Übersicht über die globale Verteilung](distribute-data-globally.md)
* [Was sind Konsistenzebenen in Azure Cosmos DB?](consistency-levels.md)
* [Hochverfügbarkeit](high-availability.md)
