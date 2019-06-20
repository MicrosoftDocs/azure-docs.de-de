---
title: Konsistenzebenen und Azure Cosmos DB-APIs
description: Grundlegendes zu den Konsistenzebenen von APIs in Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: sngun
ms.openlocfilehash: 1129152c1823fbffb3d6c9ec918d7b8cb4426bbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235630"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsistenzebenen und Azure Cosmos DB-APIs

Azure Cosmos DB bietet für gängige Datenbanken native Unterstützung für Wire Protocol-kompatible APIs. Dazu zählen MongoDB, Apache Cassandra, Gremlin und Azure Table Storage. Diese Datenbanken bieten keine genau definierten Konsistenzmodelle und keine von der SLA unterstützten Garantien für die Konsistenzebenen. Sie umfassen in der Regel nur eine Teilmenge der fünf in Azure Cosmos DB bereitgestellten Konsistenzmodelle. 

Für die SQL-API, die Gremlin-API und die Tabellen-API wird die im Azure Cosmos-Konto konfigurierte Standardkonsistenzebene verwendet. 

Bei Verwenden der Cassandra-API oder der Azure Cosmos DB-API für MongoDB erhalten Anwendungen einen vollständigen Satz von Konsistenzebenen, die von Apache Cassandra bzw. MongoDB geboten werden, mit noch stärkeren Garantien für Konsistenz und Dauerhaftigkeit. In diesem Dokument sind die entsprechenden Konsistenzebenen von Azure Cosmos DB für die Konsistenzebenen von Apache Cassandra und MongoDB aufgeführt.


## <a id="cassandra-mapping"></a>Zuordnung zwischen Apache Cassandra- und Azure Cosmos DB-Konsistenzebenen

Im Gegensatz zu Azure Cosmos DB bietet Apache Cassandra nativ keine genau definierten Konsistenzgarantien.  Stattdessen bietet Apache Cassandra eine Schreib- und Lesekonsistenzebene, um in den Genuss von Vorteilen in Bezug auf Hochverfügbarkeit, Konsistenz und Latenz zu kommen. Bei Verwenden der Cassandra-API von Azure Cosmos DB gilt Folgendes: 

* Die Schreibkonsistenzebene von Apache Cassandra wird der Standardkonsistenzebene zugeordnet, die in Ihrem Azure Cosmos-Konto konfiguriert ist. 

* Azure Cosmos DB ordnet die vom Cassandra-Clienttreiber angegebene Lesekonsistenzebene dynamisch einer der Azure Cosmos DB-Konsistenzebenen zu, die bei einer Leseanforderung dynamisch konfiguriert wird. 

Die folgende Tabelle veranschaulicht, wie die nativen Cassandra-Konsistenzebenen den Konsistenzebenen von Azure Cosmos DB zugeordnet werden, wenn die Cassandra-API verwendet wird:  

[ ![Zuordnung zum Cassandra-Konsistenzmodell](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Zuordnung zwischen Konsistenzebenen von MongoDB und Azure Cosmos DB

Im Gegensatz zu Azure Cosmos DB bietet MongoDB nativ keine genau definierten Konsistenzgarantien. Stattdessen ermöglicht MongoDB Benutzern nativ die Konfiguration folgender Konsistenzgarantien: eine Schreibbestätigung, eine Lesebestätigung und die „isMaster“-Anweisung, um die Lesevorgänge entweder zu primären oder sekundären Replikaten zu leiten, damit die gewünschte Konsistenzebene erreicht wird. 

Wenn Sie die Azure Cosmos DB-API für MongoDB verwenden, behandelt der MongoDB-Treiber Ihren Schreibbereich als primäres Replikat und alle anderen Bereiche als Lesereplikat. Sie können wählen, welche mit Ihrem Azure Cosmos-Konto verknüpfte Region das primäre Replikat ist. 

Bei Verwenden der Azure Cosmos DB-API für MongoDB gilt Folgendes:

* Die Schreibbestätigung wird der Standardkonsistenzebene zugeordnet, die in Ihrem Azure Cosmos-Konto konfiguriert ist.
 
* Azure Cosmos DB ordnet die vom MongoDB-Clienttreiber angegebene Lesebestätigung dynamisch einer der Azure Cosmos DB-Konsistenzebenen zu, die bei einer Leseanforderung dynamisch konfiguriert wird. 

* Sie können eine bestimmte mit Ihrem Azure Cosmos-Konto verknüpfte Region als „Master“ kennzeichnen, indem Sie die Region als erste beschreibbare Region festlegen. 

Die folgende Tabelle veranschaulicht, wie die nativen Schreib- und Lesebestätigungen von MongoDB den Konsistenzebenen von Azure Cosmos DB zugeordnet werden, wenn die Azure Cosmos DB-API für MongoDB verwendet wird:

[ ![Zuordnung zum MongoDB-Konsistenzmodell](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Konsistenzebenen und die Kompatibilität zwischen Azure Cosmos DB-APIs und Open-Source-APIs. Entsprechende Informationen finden Sie in den folgenden Artikeln:

* [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](consistency-levels-tradeoffs.md)
* [MongoDB-Features, die von der API für MongoDB von Azure Cosmos DB unterstützt werden](mongodb-feature-support.md)
* [Apache Cassandra-Features, die von der Cassandra-API für Azure Cosmos DB unterstützt werden](cassandra-support.md)