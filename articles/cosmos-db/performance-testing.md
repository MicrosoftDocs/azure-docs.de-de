---
title: Leistungs- und Skalierungstests mit Azure Cosmos DB
description: Erfahren Sie, wie Sie Skalierungs- und Leistungstests mit Azure Cosmos DB durchführen. Sie können dann die Funktionalität von Azure Cosmos DB für Anwendungsszenarien mit hoher Leistung auswerten.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: cb0dd8eb879c5511da3c79a3dee6a715fb1275e4
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358642"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Leistungs- und Skalierungstests mit Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Leistungs- und Skalierungstests sind ein wichtiger Schritt bei der Entwicklung von Anwendungen. Bei vielen Anwendungen hat die Datenbankebene einen erheblichen Einfluss auf die allgemeine Leistung und Skalierbarkeit. Daher ist sie eine wichtige Komponente für Leistungstests. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ist speziell für elastische Skalierung und vorhersagbare Leistung ausgelegt. Durch diese Eigenschaften eignet es sich hervorragend für Anwendungen, die eine leistungsstarke Datenbankebene benötigen. 

Dieser Artikel stellt eine Referenz für Entwickler dar, die Leistungtestsuites für ihre Azure Cosmos DB-Workloads implementieren. Außerdem kann Azure Cosmos DB damit auf Anwendungsszenarien mit hoher Leistung hin ausgewertet werden. Er konzentriert sich hauptsächlich auf isolierte Leistungstests der Datenbank, enthält jedoch auch bewährte Methoden für Produktionsanwendungen.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten: 

* Wo finde ich eine .NET-Beispielclientanwendung für Leistungstests von Azure Cosmos DB? 
* Wie erreiche ich über meine Clientanwendung hohe Durchsätze mit Azure Cosmos DB?

Sie können das Projekt aus dem [Azure Cosmos DB-Beispiel zur Leistungsüberprüfung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark) herunterladen, um mit dem Programmieren zu beginnen. 

> [!NOTE]
> Das Ziel dieser Anwendung ist die Veranschaulichung von Methoden, um mit einer geringen Anzahl von Clientcomputern eine optimale Leistung für Cosmos DB zu erzielen. Das Ziel des Beispiels ist nicht, die maximale Durchsatzkapazität von Azure-Cosmos DB zu erreichen (die ohne Grenzwerte skaliert werden kann).

Die clientseitigen Konfigurationsoptionen zur Verbesserung der Leistung von Azure Cosmos DB finden Sie unter [Tipps zur Leistungssteigerung für Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Ausführen der Anwendung zur Leistungsüberprüfung
Den schnellsten Einstieg ermöglicht das Kompilieren und Ausführen des .NET-Beispiels, wie in den folgenden Schritten beschrieben. Sie können auch den Quellcode prüfen und Konfigurationen implementieren, die Ihren eigenen Clientanwendungen ähneln.

**Schritt 1:** Laden Sie das Projekt aus dem [Azure Cosmos DB-Beispiel zur Leistungsüberprüfung](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark) herunter, oder erstellen Sie einen Fork des GitHub-Repositorys.

**Schritt 2:** Ändern Sie in der Datei „App.config“ die Einstellungen für „EndpointUrl“, „AuthorizationKey“, „CollectionThroughput“ und „DocumentTemplate“ (optional).

> [!NOTE]
> Besuchen Sie vor dem Bereitstellen von Sammlungen mit hohem Durchsatz die [Seite mit den Preisinformationen](https://azure.microsoft.com/pricing/details/cosmos-db/), um die Kosten pro Sammlung abschätzen zu können. Bei Azure Cosmos DB werden Speicher und Durchsatz unabhängig voneinander auf Stundenbasis abgerechnet. Sie können Kosten sparen, indem Sie den Durchsatz Ihrer Azure Cosmos-Container nach dem Testen abbrechen oder verringern.
> 
> 

**Schritt 3:** Kompilieren Sie die Konsolen-App über die Befehlszeile, und führen Sie sie aus. Ihnen sollte eine Ausgabe wie die folgende angezeigt werden:

```bash
C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
Summary:
---------------------------------------------------------------------
Endpoint: https://arramacquerymetrics.documents.azure.com:443/
Collection : db.data at 100000 request units per second
Document Template*: Player.json
Degree of parallelism*: -1
---------------------------------------------------------------------
DocumentDBBenchmark starting...
Found collection data with 100000 RU/s
Starting Inserts with 100 tasks
Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

Summary:
---------------------------------------------------------------------
Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
---------------------------------------------------------------------
DocumentDBBenchmark completed successfully.
Press any key to exit...
```

**Schritt 4 (bei Bedarf):** Der vom Tool gemeldete Durchsatz (RUs/Sek.) muss mindestens dem bereitgestellten Durchsatz der Sammlung oder einer Gruppe von Sammlungen entsprechen. Wenn dies nicht der Fall ist, kann die Erhöhung von DegreeOfParallelism in kleinen Schritten dabei helfen, den Grenzwert zu erreichen. Wenn der Durchsatz Ihrer Client-App stagniert, starten Sie mehrere Instanzen der App auf zusätzlichen Clientcomputern. Wenn Sie bei diesem Schritt Hilfe benötigen, legen Sie im [Azure-Portal](https://portal.azure.com) ein Supportticket an.

Sobald die App ausgeführt wird, können Sie verschiedene [Indizierungsrichtlinien](index-policy.md) und [Konsistenzebenen](consistency-levels.md) ausprobieren, um die Auswirkungen auf Durchsatz und Latenz zu ermitteln. Sie können auch den Quellcode prüfen und Konfigurationen implementieren, die Ihren eigenen Testsammlungen oder Produktionsanwendungen ähneln.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mithilfe einer .NET-Konsolenanwendung die Leistung und Skalierung mit Azure Cosmos DB testen können. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Beispiel zu Leistungstests in Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark)
* [Clientkonfigurationsoptionen zur Verbesserung der Leistung von Azure Cosmos DB](performance-tips.md)
* [Serverseitige Partitionierung in Azure Cosmos DB](partitioning-overview.md)


