---
title: Vorbereitende Schritte für Datenmigrationen von MongoDB zur Azure Cosmos DB-API für MongoDB
description: In diesem Artikel erhalten Sie einen Überblick über die Voraussetzungen für die Datenmigration von MongoDB nach Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61330856"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Vorbereitende Schritte für Datenmigrationen von MongoDB zur Azure Cosmos DB-API für MongoDB

Vor dem Migrieren Ihrer Daten von MongoDB (lokal oder in der Cloud (IaaS)) zur Azure Cosmos DB-API für MongoDB müssen Sie folgende Vorgänge ausführen:

1. [Erstellen eines Azure Cosmos DB-Kontos](#create-account)
2. [Schätzen des benötigten Durchsatzes für Ihre Workloads](#estimate-throughput)
3. [Auswählen eines optimalen Partitionsschlüssels für Ihre Daten](#partitioning)
4. [Verstehen der Indizierungsrichtlinie, die Sie für die Daten festlegen können](#indexing)

Wenn Sie die obigen erforderlichen Vorbereitungsschritte für die Migration bereits ausgeführt haben, finden Sie in [Migrieren von MongoDB-Daten zur Azure Cosmos DB-API für MongoDB](../dms/tutorial-mongodb-cosmos-db.md) die Anweisungen für die tatsächliche Datenmigration. Andernfalls erhalten Sie in diesem Artikel Anweisungen zum Ausführen dieser vorausgesetzten Schritte. 

## <a id="create-account"></a> Erstellen eines Azure Cosmos DB-Kontos 

Vor dem Starten der Migration müssen Sie [ein Azure Cosmos-Konto mit der Azure Cosmos DB-API für MongoDB erstellen](create-mongodb-dotnet.md). 

Beim Erstellen des Kontos können Sie Einstellungen für die [globale Verteilung](distribute-data-globally.md) Ihrer Daten auswählen. Sie haben auch die Möglichkeit, Schreibvorgänge in mehreren Regionen (oder die Konfiguration mit mehreren Mastern) zu aktivieren, sodass jede Ihrer Regionen sowohl für Schreib- als auch für Lesevorgänge zuständig sein kann.

![Kontoerstellung](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Schätzen des benötigten Durchsatzes für Ihre Workloads

Vor dem Starten der Migration mit dem [Database Migration Service (DMS)](../dms/dms-overview.md) müssen Sie den Durchsatz schätzen, der für Azure Cosmos-Datenbanken und -Sammlungen bereitgestellt werden muss.

Durchsatz kann für Folgendes bereitgestellt werden:

- Sammlung

- Datenbank

> [!NOTE]
> Auch eine Kombination beider Elemente ist möglich, wobei für einige Sammlungen in einer Datenbank dedizierter Durchsatz bereitgestellt wird, der mit den anderen gemeinsam genutzt wird. Einzelheiten hierzu finden Sie auf der Seite [Festlegen des Durchsatzes für eine Datenbank und einen Container](set-throughput.md).
>

Sie sollten zuerst entscheiden, ob Sie Durchsatz auf der Datenbank- oder der Sammlungsebene oder auf beiden bereitstellen. Im Allgemeinen empfiehlt es sich, einen dedizierten Durchsatz auf Sammlungsebene zu konfigurieren. Durch Bereitstellen von Durchsatz auf der Datenbankebene können Sammlungen in der Datenbank den bereitgestellten Durchsatz gemeinsam verwenden. Bei gemeinsam verwendetem Durchsatz ist jedoch für die einzelnen Sammlungen kein bestimmter Durchsatz garantiert, und die Performance einer bestimmten Sammlung kann nicht prognostiziert werden.

Wenn Sie nicht sicher sind, welchen Durchsatz Sie den einzelnen Sammlungen zuordnen müssen, können Sie sich für den Durchsatz auf Datenbankebene entscheiden. Sie können sich den für die Azure Cosmos-Datenbank bereitgestellten Durchsatz als logische Entsprechung der Computekapazität einer MongoDB-VM oder eines physischen Servers vorstellen (jedoch kostengünstiger und effizienter hinsichtlich der Skalierung). Weitere Informationen finden Sie unter [Bereitstellen von Durchsatz für Azure Cosmos-Container und -Datenbanken](set-throughput.md).

Wenn Sie Durchsatz auf Datenbankebene bereitstellen, müssen alle in der betreffenden Datenbank erstellten Sammlungen mit einem Partitions-/Shard-Schlüssel erstellt werden. Weitere Informationen zum Partitionieren von Daten finden Sie unter [Partitionieren und horizontales Skalieren in Azure Cosmos DB](partition-data.md). Wenn Sie während der Migration keinen Partitions-/Shard-Schlüssel angeben, füllt der Azure Database Migration Service das Feld für den Shard-Schlüssel automatisch mit einem *_id*-Attribut, das für jedes Dokument automatisch generiert wird.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Optimale Anzahl von Anforderungseinheiten (RUs) für die Bereitstellung

In Azure Cosmos DB wird der Durchsatz vorab bereitgestellt und in Anforderungseinheiten (RU) pro Sekunde gemessen. Wenn Sie über Workloads verfügen, die MongoDB auf einer VM oder lokal ausführen, können Sie RUs als einfache Abstraktion physischer Ressourcen ansehen, beispielsweise für die Größe einer VM oder eines lokalen Servers und deren zugehörige Ressourcen wie Arbeitsspeicher, CPU, IOPs. 

Im Gegensatz zu VMs oder lokalen Servern können RUs jederzeit einfach zentral hoch- und herunterskaliert werden. Sie können die Anzahl bereitgestellter RUs innerhalb von Sekunden ändern, und Ihnen wird lediglich die maximale Anzahl von RUs berechnet, die Sie für einen bestimmten 1-Stunden-Zeitraum bereitstellen. Weitere Informationen finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](request-units.md).

Im Folgenden sind wichtige Faktoren beschrieben, die sich auf die Anzahl der erforderlichen RUs auswirken:
- **Größe des Elements (d.h. des Dokuments)** : Je größer ein Element/Dokument, desto mehr RUs werden beim Lesen oder Schreiben des Elements/Dokuments genutzt.
- **Anzahl der Elementeigenschaften**: Bei Verwendung der [standardmäßigen Indizierung](index-overview.md) aller Eigenschaften erhöht sich die Anzahl von RUs, die beim Schreiben eines Elements genutzt werden, wenn sich die Anzahl von Elementeigenschaften erhöht. Sie können die für Schreibvorgänge genutzten Anforderungseinheiten verringern, indem Sie die [Anzahl indizierter Eigenschaften begrenzen](index-policy.md).
- **Parallele Vorgänge**: Der Verbrauch von Anforderungseinheiten hängt auch von der Frequenz ab, mit der verschiedene CRUD-Vorgänge (wie Schreib-, Lese, Aktualisierungs- und Löschvorgänge) und komplexere Abfragen ausgeführt werden. Mit [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) können Sie den Parallelitätsbedarf Ihrer aktuellen MongoDB-Daten ausgeben.
- **Abfragemuster**: Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten von ihr verbraucht werden.

Wenn Sie JSON-Dateien mit [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) exportieren und verstehen, wie viele Schreib-, Lese-, Aktualisierungs- und Löschvorgänge pro Sekunde stattfinden, können Sie mit dem [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) die anfängliche Anzahl der RUs schätzen, die bereitgestellt werden müssen. Capacity Planner berücksichtigt nicht die Kosten komplexerer Abfragen. Wenn also komplexe Abfragen Ihrer Daten gesendet werden, werden weitere RUs verbraucht. Der Rechner geht davon aus, dass alle Felder indiziert sind und Sitzungskonsistenz verwendet wird. Am besten ermitteln Sie die Kosten von Abfragen wie folgt: Migrieren Sie Ihre Daten (oder Beispieldaten) zu Azure Cosmos DB, [stellen Sie eine Verbindung mit dem Endpunkt von Cosmos DB her](connect-mongodb-account.md), und führen Sie mithilfe des `getLastRequestStastistics`-Befehls eine Beispielabfrage aus der MongoDB-Shell aus, um die Anforderungsgebühr abzurufen, von welcher die Anzahl der verbrauchten RUs ausgegeben wird:

`db.runCommand({getLastRequestStatistics: 1})`

Dieser Befehl gibt ein JSON-Dokument wie das folgende aus:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Wenn Ihnen bekannt ist, welche Anzahl von RUs von einer Abfrage verbraucht werden und welchen Parallelitätsbedarf die betreffende Abfrage hat, können Sie die Anzahl der bereitgestellten RUs anpassen. Das Optimieren der RUs ist keine einmalige Angelegenheit – Sie sollten die bereitgestellten RUs laufend optimieren bzw. zentral hochskalieren, je nachdem, ob sie keinen starken Datenverkehr erwarten oder ob Sie eine große Workload planen oder Daten importieren.

## <a id="partitioning"></a>Auswählen des Partitionsschlüssels
Die Partitionierung ist ein zentraler Aspekt der Überlegungen vor dem Migrieren zu einer global verteilten Datenbank wie Azure Cosmos DB. Azure Cosmos DB arbeitet mit Partitionierung, um einzelne Container in einer Datenbank entsprechend den Skalierbarkeits- und Leistungsanforderungen Ihrer Anwendung zu skalieren. Bei der Partitionierung werden die Elemente in einem Container in eindeutige Teilgruppen unterteilt, die als logische Partitionen bezeichnet werden. Ausführliche Informationen und Empfehlungen zum Auswählen des richtigen Partitionierungsschlüssels für Ihre Daten finden Sie im Abschnitt [Auswählen des Partitionsschlüssels](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indizieren Ihrer Daten
In der Standardeinstellung indiziert Azure Cosmos DB alle Datenfelder bei Erfassung. Sie können die [Indizierungsrichtlinie](index-policy.md) in Azure Cosmos DB jederzeit ändern. Tatsächlich empfiehlt es sich häufig, die Indizierung beim Migrieren von Daten zu deaktivieren und anschließend wieder zu aktivieren, wenn sich die Daten bereits in Cosmos DB befinden. Weitere Einzelheiten zum Indizieren finden Sie im Abschnitt [Indizierung in Azure Cosmos DB](index-overview.md). 

MongoDB-Sammlungen mit eindeutigen Indizes werden von [Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatisch migriert. Die eindeutigen Indizes müssen jedoch vor der Migration erstellt werden. Wenn sich in den Sammlungen bereits Daten befinden, wird die Erstellung eindeutiger Indizes von Azure Cosmos DB nicht unterstützt. Weitere Informationen finden Sie unter [Eindeutige Schlüssel in Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Nächste Schritte
* [Migrieren von MongoDB-Daten in Cosmos DB mit dem Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Bereitstellen des Durchsatzes für Azure Cosmos-Container und -Datenbanken](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partition-data.md) (Partitionierung in Azure Cosmos DB)
* [Globale Verteilung in Azure Cosmos DB](distribute-data-globally.md)
* [Indizierung in Azure Cosmos DB](index-overview.md)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
