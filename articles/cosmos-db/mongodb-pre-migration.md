---
title: Vorbereitende Schritte für Datenmigrationen zur Azure Cosmos DB-API für MongoDB
description: In diesem Artikel erhalten Sie einen Überblick über die Voraussetzungen für die Datenmigration von MongoDB nach Cosmos DB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/01/2020
ms.author: chrande
ms.openlocfilehash: 337341daf0e092def639a4e8f6fc8ee0a9b57c75
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349417"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Vorbereitende Schritte für Datenmigrationen von MongoDB zur Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Vor dem Migrieren Ihrer Daten aus MongoDB (lokal oder in der Cloud) zur Azure Cosmos DB-API für MongoDB sollten Sie die folgenden Schritte ausführen:

1. [Lesen der zentralen Aspekte beim Verwenden der Azure Cosmos DB-API für MongoDB](#considerations)
2. [Auswählen einer Option zum Migrieren Ihrer Daten](#options)
3. [Schätzen des benötigten Durchsatzes für Ihre Workloads](#estimate-throughput)
4. [Auswählen eines optimalen Partitionsschlüssels für Ihre Daten](#partitioning)
5. [Verstehen der Indizierungsrichtlinie, die Sie für die Daten festlegen können](#indexing)

Wenn Sie die obigen erforderlichen vorbereitenden Schritte für die Migration bereits ausgeführt haben, können Sie mit dem [Migrieren von MongoDB-Daten zur Azure Cosmos DB-API für MongoDB mit Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) beginnen. Wenn Sie noch kein Konto erstellt haben, können Sie außerdem alle [Schnellstarts](create-mongodb-dotnet.md) durchsuchen, in denen die Schritte zum Erstellen eines Kontos beschrieben werden.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Aspekte beim Verwenden der Azure Cosmos DB-API für MongoDB

Nachfolgend werden spezifische Merkmale der Azure Cosmos DB-API für MongoDB aufgeführt:

- **Kapazitätsmodell**: Die Datenbankkapazität in Azure Cosmos DB basiert auf einem durchsatzbasierten Modell. Dieses Modell basiert auf [Anforderungseinheiten pro Sekunde](request-units.md). Dabei handelt es sich um eine Einheit, die die Anzahl von Datenbankvorgängen darstellt, die pro Sekunde für eine Sammlung ausgeführt werden können. Diese Kapazität kann auf [Datenbank- oder Sammlungsebene](set-throughput.md) zugeordnet werden, und sie kann in einem Zuordnungsmodell oder mithilfe des [per Autoskalierung bereitgestellten Durchsatzes](provision-throughput-autoscale.md) bereitgestellt werden.

- **Anforderungseinheiten**: In Azure Cosmos DB sind jedem Datenbankvorgang Kosten für Anforderungseinheiten (Request Units, RUs) zugeordnet. Bei der Ausführung werden diese von der verfügbaren Anforderungseinheitenebene für eine bestimmte Sekunde subtrahiert. Wenn für eine Anforderung mehr RUs als die aktuell zugeordnete Anzahl von Anforderungseinheiten pro Sekunde erforderlich sind, gibt es zwei Möglichkeiten, das Problem zu beheben: Sie können die Anzahl von RUs erhöhen oder warten, bis die nächste Sekunde beginnt, und dann den Vorgang wiederholen.

- **Elastische Kapazität**: Die Kapazität für eine bestimmte Sammlung oder Datenbank kann sich jederzeit ändern. Dadurch kann die Datenbank elastisch an die Durchsatzanforderungen Ihrer Workload angepasst werden.

- **Automatisches Sharding**: Azure Cosmos DB bietet ein automatisches Partitionierungssystem, das nur einen Shard (oder einen Partitionsschlüssel) erfordert. Der [automatische Partitionierungsmechanismus](partitioning-overview.md) wird von allen Azure Cosmos DB-APIs gemeinsam genutzt und ermöglicht nahtlose Daten und die gesamte Skalierung durch horizontale Verteilung.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Migrationsoptionen für die Azure Cosmos DB-API für MongoDB

Der [Azure Database Migration Service für die Azure Cosmos DB-API für MongoDB](../dms/tutorial-mongodb-cosmos-db.md) bietet einen Mechanismus, der die Datenmigration vereinfacht, indem eine vollständig verwaltete Hostingplattform, Migrationsüberwachungsoptionen und die automatische Drosselung bereitgestellt werden. Nachfolgend finden Sie die vollständige Liste der Optionen:

|**Migrationstyp**|**Lösung**|**Überlegungen**|
|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Eignet sich für große Datasets und übernimmt die Replikation von Liveänderungen <br/>&bull; Funktioniert nur mit anderen MongoDB-Quellen|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Eignet sich für große Datasets und übernimmt die Replikation von Liveänderungen <br/>&bull; Funktioniert nur mit anderen MongoDB-Quellen|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Einfache Einrichtung und Unterstützung mehrerer Quellen <br/>&bull; Nutzt die Azure Cosmos DB-BulkExecutor-Bibliothek <br/>&bull; Geeignet für große Datasets <br/>&bull; Fehlende Prüfpunktausführung: Wenn während der Migration ein Problem auftritt, muss der gesamte Migrationsprozess neu gestartet werden.<br/>&bull; Fehlende Warteschlange für unzustellbare Nachrichten: Dies bedeutet, dass wenige fehlerhafte Dateien den gesamten Migrationsprozess unterbrechen können. <br/>&bull; Erfordert benutzerdefinierten Code, um den Lesedurchsatz für bestimmte Datenquellen zu erhöhen|
|Offline|[Vorhandene Mongo-Tools (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Einfache Einrichtung und Integration <br/>&bull; Erfordert die benutzerdefinierte Behandlung von Drosselungen|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> Schätzen des benötigten Durchsatzes für Ihre Workloads

In Azure Cosmos DB wird der Durchsatz vorab bereitgestellt und in Anforderungseinheiten (RU) pro Sekunde gemessen. Im Gegensatz zu VMs oder lokalen Servern können RUs jederzeit einfach hoch- und herunterskaliert werden. Sie können die Anzahl der bereitgestellten RUs sofort ändern. Weitere Informationen finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](request-units.md).

Sie können den [Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/) verwenden, um die Anzahl von Anforderungseinheiten basierend auf der Konfiguration Ihres Datenbankkontos, der Datenmenge, der Dokumentgröße und den erforderlichen Lese- und Schreibvorgängen pro Sekunde zu ermitteln.

Im Folgenden sind wichtige Faktoren beschrieben, die sich auf die Anzahl der erforderlichen RUs auswirken:
- **Dokumentgröße**: Je größer ein Element/Dokument, desto mehr RUs werden beim Lesen oder Schreiben des Elements/Dokuments genutzt.

- **Anzahl der Dokumenteigenschaften**: Die Anzahl der zum Erstellen oder Aktualisieren eines Dokuments verbrauchten RUs bezieht sich auf die Anzahl, Komplexität und Länge der zugehörigen Eigenschaften. Sie können die für Schreibvorgänge genutzten Anforderungseinheiten verringern, indem Sie die [Anzahl indizierter Eigenschaften begrenzen](mongodb-indexing.md).

- **Abfragemuster**: Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten von ihr verbraucht werden. 

Am besten können Sie die Kosten von Abfragen verstehen, wenn Sie Beispieldaten in Azure Cosmos DB verwenden und mithilfe des Befehls `getLastRequestStastistics` [Beispielabfragen aus der MongoDB-Shell ausführen](connect-mongodb-account.md), um die Anforderungsgebühr abzurufen. Dadurch wird die Anzahl der verbrauchten RUs ausgegeben:

`db.runCommand({getLastRequestStatistics: 1})`

Dieser Befehl gibt ein JSON-Dokument wie das folgende aus:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Sie können auch die [Diagnoseeinstellungen](cosmosdb-monitor-resource-logs.md) verwenden, um die Häufigkeit und die Muster der für Azure Cosmos DB ausgeführten Abfragen zu verstehen. Die Ergebnisse der Diagnoseprotokolle können an ein Speicherkonto, eine EventHub-Instanz oder an [Azure Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) gesendet werden.  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Auswählen des Partitionsschlüssels
Die Partitionierung (auch als Sharding bezeichnet) ist ein wichtiger Aspekt, den Sie vor dem Migrieren von Daten berücksichtigen müssen. Azure Cosmos DB verwendet die vollständig verwaltete Partitionierung, um die Kapazität in einer Datenbank zur Erfüllung der Speicher- und Durchsatzanforderungen zu erhöhen. Diese Funktion erfordert weder das Hosting noch die Konfiguration von Routingservern.   

Auf ähnliche Weise fügt die Partitionierungsfunktion automatisch Kapazität hinzu und gleicht die Daten entsprechend neu aus. Ausführliche Informationen und Empfehlungen zum Auswählen des richtigen Partitionsschlüssels für Ihre Daten finden Sie unter [Auswählen eines Partitionsschlüssels](partitioning-overview.md#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indizieren Ihrer Daten

Die Azure Cosmos DB-API für die MongoDB-Serverversion 3.6 indiziert automatisch nur das Feld `_id`. Dieses Feld kann nicht gelöscht werden. Sie erzwingt automatisch die Eindeutigkeit des Felds `_id` durch einen Shardschlüssel. Um zusätzliche Felder zu indizieren, verwenden Sie die MongoDB-Indexverwaltungsbefehle. Diese Standardindizierungsrichtlinie unterscheidet sich von der Azure Cosmos DB-SQL-API, die standardmäßig alle Felder indiziert.

Die von Azure Cosmos DB bereitgestellten Indizierungsfunktionen umfassen das Hinzufügen von zusammengesetzten Indizes, eindeutigen Indizes und Time-to-Live-Indizes (TTL-Indizes). Die Indexverwaltungsschnittstelle wird dem Befehl `createIndex()` zugeordnet. Weitere Informationen finden Sie im Artikel [Indizieren in der API für MongoDB von Azure Cosmos DB](mongodb-indexing.md).

MongoDB-Sammlungen mit eindeutigen Indizes werden von [Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatisch migriert. Die eindeutigen Indizes müssen jedoch vor der Migration erstellt werden. Wenn sich in den Sammlungen bereits Daten befinden, wird die Erstellung eindeutiger Indizes von Azure Cosmos DB nicht unterstützt. Weitere Informationen finden Sie unter [Eindeutige Schlüssel in Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Nächste Schritte
* [Migrieren von MongoDB-Daten in Cosmos DB mit dem Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Bereitstellen des Durchsatzes für Azure Cosmos-Container und -Datenbanken](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Globale Verteilung in Azure Cosmos DB](distribute-data-globally.md)
* [Indizierung in Azure Cosmos DB](index-overview.md)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)