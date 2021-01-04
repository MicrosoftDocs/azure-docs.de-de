---
title: Indizieren in einem Cassandra-API-Konto von Azure Cosmos DB
description: Erfahren Sie, wie sekundäre Indizierung in einem Cassandra-API-Konto von Azure Cosmos DB funktioniert.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 98e8f713ad2e4eef47e40d89a23dbf49a98ad67c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339889"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Sekundäre Indizierung in der Cassandra-API von Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Der Cassandra-API in Azure Cosmos DB nutzt die zugrunde liegende Indizierungsinfrastruktur, um die Indizierungsstärke bereitzustellen, die der Plattform eigen ist. Im Gegensatz zur SQL-Kern-API indiziert die Cassandra-API in Azure Cosmos DB jedoch nicht standardmäßig alle Attribute. Stattdessen wird sekundäre Indizierung unterstützt, um einen Index für bestimmte Attribute zu erstellen. Dieses Verhalten ist mit Apache Cassandra identisch.  

Im Allgemeinen ist es nicht ratsam, Filterabfragen für die Spalten auszuführen, die nicht partitioniert sind. Sie müssen explizit ALLOW FILTERING-Syntax verwenden, was zu einem Vorgang führt, dessen Leistung möglicherweise schlecht ist. In Azure Cosmos DB können Sie solche Abfragen für Attribute mit niedriger Kardinalität ausführen, da sie über Partitionen hinweg aufgefächert werden, um die Ergebnisse abzurufen.

Es wird nicht empfohlen, einen Index für eine häufig aktualisierte Spalte zu erstellen. Es ist ratsam, einen Index zu erstellen, wenn Sie die Tabelle definieren. Dadurch wird sichergestellt, dass sich Daten und Indizes in einem konsistenten Zustand befinden. Wenn Sie für die vorhandenen Daten einen neuen Index erstellen, können Sie den Fortschritt der Indexänderung für die Tabelle zurzeit nicht nachverfolgen. Wenn Sie den Fortschritt für diesen Vorgang nachverfolgen müssen, müssen Sie die Fortschrittsänderung über ein [Supportticket]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) anfordern.


> [!NOTE]
> Ein sekundärer Index wird für die folgenden Objekte nicht unterstützt:
> - Datentypen wie fixierte Sammlungstypen, Dezimal- und Variant-Typen.
> - Statische Spalten
> - Clusteringschlüssel

## <a name="indexing-example"></a>Indizierungsbeispiel

Erstellen Sie zunächst einen Beispielkeyspace und eine Beispieltabelle, indem Sie die folgenden Befehle an der Eingabeaufforderung der CQL-Shell ausführen:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Fügen Sie dann Beispielbenutzerdaten mit den folgenden Befehlen ein:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Wenn Sie versuchen, die folgende Anweisung auszuführen, tritt ein Fehler auf, bei dem Sie aufgefordert werden, `ALLOW FILTERING` zu verwenden: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Obwohl die Cassandra-API ALLOW FILTERING unterstützt (wie im vorherigen Abschnitt erwähnt), wird dies nicht empfohlen. Sie sollten stattdessen einen Index wie im folgenden Beispiel gezeigt erstellen:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Nachdem Sie einen Index für das Feld „lastname“ erstellt haben, können Sie die vorherige Abfrage jetzt erfolgreich ausführen. Mit der Cassandra-API in Azure Cosmos DB müssen Sie keinen Indexnamen angeben. Es wird ein Standardindex mit dem Format `tablename_columnname_idx` verwendet. Beispielsweise ist ` t1_lastname_idx` der Indexname der vorherigen Tabelle.

## <a name="dropping-the-index"></a>Löschen des Indexes 
Sie müssen wissen, wie der Indexname lautet, um den Index zu löschen. Führen Sie den Befehl `desc schema` aus, um die Beschreibung der Tabelle abzurufen. Die Ausgabe dieses Befehls enthält den Indexnamen im Format `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`. Sie können den Indexnamen dann verwenden, um den Index zu löschen, wie im folgenden Beispiel gezeigt:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie [automatische Indizierung](index-overview.md) in Azure Cosmos DB funktioniert.
* [Apache Cassandra-Features, die von der Cassandra-API für Azure Cosmos DB unterstützt werden](cassandra-support.md)
