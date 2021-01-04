---
title: Versionshinweise und Ressourcen zu Spring Data Azure Cosmos DB v2 für die SQL-API
description: Hier finden Sie Informationen zu Spring Data Azure Cosmos DB v2 für die SQL-API, z. B. Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d5c85095e767c0e92c22410054ac4f8fc5267660
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097140"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v2 für die Core (SQL)-API: Versionshinweise und Ressourcen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-Ressourcenanbieter](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

 Mit Spring Data Azure Cosmos DB Version 2 für Core (SQL) können Entwickler Azure Cosmos DB in Spring-Anwendungen nutzen. Bei Spring Data Azure Cosmos DB wird die Spring Data-Schnittstelle für die Bearbeitung von Datenbanken und Sammlungen, die Verwendung von Dokumenten und die Ausgabe von Abfragen verfügbar gemacht. Für dasselbe Maven-Artefakt werden sowohl synchrone als auch asynchrone (reaktive) APIs unterstützt. 

Das [Spring-Framework](https://spring.io/projects/spring-framework) ist ein Programmier- und Konfigurationsmodell, mit dem die Java-Anwendungsentwicklung optimiert wird. Spring optimiert die „Verknüpfung“ von Anwendungen mithilfe von Abhängigkeitsinjektion. Spring ist bei vielen Entwicklern beliebt, weil dadurch das Erstellen und Testen von Anwendungen vereinfacht wird. Mit [Spring Boot](https://spring.io/projects/spring-boot) wird diese „Verknüpfung“ auf die Entwicklung von Webanwendungen und Microservices ausgedehnt. [Spring Data](https://spring.io/projects/spring-data) ist ein Programmiermodell für den Zugriff auf Datenspeicher wie Azure Cosmos DB, im Kontext einer Spring- bzw. Spring Boot-Anwendung. 

Sie können Spring Data Azure Cosmos DB in Ihren [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/)-Anwendungen verwenden.

> [!IMPORTANT]  
> Diese Versionshinweise gelten für Version 2 von Spring Data Azure Cosmos DB. Sie können [Versionshinweise zu Version 3 hier](sql-api-sdk-java-spring-v3.md) finden. 
>
> Spring Data Azure Cosmos DB unterstützt nur die SQL-API.
>
> In den folgenden Artikeln finden Sie Informationen zu Spring Data für andere Azure Cosmos DB-APIs:
> * [Spring Data für Apache Cassandra mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin Starter mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Möchten Sie schnell loslegen?
> 1. Installieren Sie die [mindestens unterstützte Java-Runtime, JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true), damit Sie das SDK verwenden können.
> 2. Erstellen Sie eine Spring Data Azure Cosmos DB-App mit dem [Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). Es ist ganz leicht!
> 3. Arbeiten Sie das [Entwicklerhandbuch zu Spring Data Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) durch. Darin werden einfache Azure Cosmos DB-Anforderungen Schritt für Schritt beschrieben.
>
> Sie können Spring Boot Starter-Apps mithilfe von [Spring Initializr](https://start.spring.io/) schnell erstellen!
>

## <a name="resources"></a>Ressourcen

| Resource | Link |
|---|---|
| **SDK-Download** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-Dokumentation** | [Referenzdokumentation zu Spring Data Azure Cosmos DB]() |
|**Tragen zum SDK bei** | [Spring Data Azure Cosmos DB-Repository auf GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Azure Cosmos DB Spring Boot Starter: Clientbibliothek für Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Spring-TODO-Beispiel-App mit Azure Cosmos DB**| [End-to-End-Java-Benutzeroberfläche in App Service unter Linux (Teil 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Entwicklerhandbuch** | [Entwicklerhandbuch zu Spring Data Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Verwenden von Starter** | [Verwendung von Spring Boot Starter mit der SQL-API von Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub-Repository für Azure Cosmos DB Spring Boot Starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Beispiel mit Azure App Service** | [Verwenden von Spring und Azure Cosmos DB mit App Service für Linux](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Beispiel für TODO-App](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Releaseverlauf

### <a name="230-may-21-2020"></a>2.3.0 (21. Mai 2020)
#### <a name="new-features"></a>Neue Funktionen
* Aktualisiert die Spring Boot-Version auf 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19. Mai 2020)
#### <a name="new-features"></a>Neue Funktionen
* Aktualisiert die Azure Cosmos DB-Version auf 3.7.3.
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Enthält Fehlerbehebungen für Arbeitsspeicherverlust und Upgrades der Netty-Version von Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6. April 2020)
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Korrigiert das Flag `allowTelemetry`, das aus `CosmosDbConfig` berücksichtigt werden soll.
* Korrigiert die Eigenschaft `TTL` für den Container.

### <a name="223-february-25-2020"></a>2.2.3 (25. Februar 2020)
#### <a name="new-features"></a>Neue Funktionen
* Fügt neues `findAll` nach Partitionsschlüssel-API hinzu.
* Aktualisiert die Azure Cosmos DB-Version auf 3.7.0.
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Korrigiert `collectionName` -> `containerName`.
* Korrigiert `entityClass` und `domainClass` -> `domainType`.
* Korrigiert „Rückgabe der im Repository gespeicherten Entitätssammlung (anstelle der Eingabeentität)“.

### <a name="2110-february-25-2020"></a>2.1.10 (25. Februar 2020)
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Führt ein Backport der Fehlerbehebung für „Rückgabe der im Repository gespeicherten Entitätssammlung (anstelle der Eingabeentität)“ durch.

### <a name="222-january-15-2020"></a>2.2.2 (15. Januar 2020)
#### <a name="new-features"></a>Neue Funktionen
* Aktualisiert die Azure Cosmos DB-Version auf 3.6.0.
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen

### <a name="221-december-31-2019"></a>2.2.1 (31. Dezember 2019)
#### <a name="new-features"></a>Neue Funktionen
* Die Azure Cosmos DB SDK-Version wird auf 3.5.0 aktualisiert.
* Fügt ein Anmerkungsfeld zum Aktivieren oder Deaktivieren der automatischen Sammlungserstellung hinzu.
* Verbessert die Ausnahmebehandlung. Macht `CosmosClientException` über `CosmosDBAccessException` verfügbar.
* Macht `requestCharge` und `activityId` über `ResponseDiagnostics` verfügbar.
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* SDK 3.5.0-Update korrigiert „Ausnahme, wenn Cosmos DB-HTTP-Antwortheader größer als 8.192 Byte ist“ und „Fehler für ConsistencyPolicy.defaultConsistencyLevel() bei begrenzter Veraltung und Präfixkonsistenz“.
* Korrigiert das Verhalten der Methode `findById`. Zuvor gab diese Methode „empty" zurück, wenn die Entität nicht gefunden wurde, statt eine Ausnahme auszulösen.
* Korrigiert einen Fehler, bei dem die Sortierung auf der nächsten Seite nicht angewendet wurde, wenn `CosmosPageRequest` verwendet wurde.

### <a name="219-december-26-2019"></a>2.1.9 (26. Dezember 2019)
#### <a name="new-features"></a>Neue Funktionen
* Fügt ein Anmerkungsfeld zum Aktivieren oder Deaktivieren der automatischen Sammlungserstellung hinzu.
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
*  Korrigiert das Verhalten der Methode `findById`. Zuvor gab diese Methode „empty" zurück, wenn die Entität nicht gefunden wurde, statt eine Ausnahme auszulösen.

### <a name="220-october-21-2019"></a>2.2.0 (21. Oktober 2019)
#### <a name="new-features"></a>Neue Funktionen
* Unterstützung für vollständiges reaktives Cosmos-Repository.
* Unterstützung für Azure Cosmos DB-Anforderungsdiagnose-Zeichenfolge und -Abfragemetriken.
* Update der Azure Cosmos DB SDK-Version auf 3.3.1.
* Upgrade der Spring Framework-Version auf 5.2.0.RELEASE.
* Upgrade der Spring Data Commons-Version auf 2.2.0.RELEASE.
* Fügt `findByIdAndPartitionKey`- und `deleteByIdAndPartitionKey`-APIs hinzu.
* Entfernt die Abhängigkeit aus „azure-documentdb“.
* Benennt „DocumentDB“ in „Azure Cosmos DB“ um.
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Korrigiert „Bei der Sortierung wird ein Fehler ausgelöst, wenn ‚pageSize‘ kleiner als die Gesamtmenge der Elemente im Repository ist“.

### <a name="218-october-18-2019"></a>2.1.8 (18. Oktober 2019)
#### <a name="new-features"></a>Neue Funktionen
* Kennzeichnet „DocumentDB-APIs“ als veraltet.
* Fügt `findByIdAndPartitionKey`- und `deleteByIdAndPartitionKey`-APIs hinzu.
* Fügt optimistische Sperre basierend auf `_etag` hinzu.
* Aktiviert „SPeL“-Ausdruck für den Namen der Dokumentsammlung.
* Fügt `ObjectMapper`-Verbesserungen hinzu.

### <a name="217-october-18-2019"></a>2.1.7 (18. Oktober 2019)
#### <a name="new-features"></a>Neue Funktionen
* Fügt Abhängigkeit für Azure Cosmos DB SDK-Version 3 hinzu.
* Fügt reaktives Cosmos-Repository hinzu.
* Aktualisiert die Implementierung von `DocumentDbTemplate`, damit Azure Cosmos DB SDK-Version 3 verwendet wird.
* Fügt weitere Konfigurationsänderungen für Unterstützung des reaktiven Cosmos-Repositorys hinzu.

### <a name="212-march-19-2019"></a>2.1.2 (19. März 2019)
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Entfernt `applicationInsights`-Abhängigkeit für:
    * Potenzielles Risiko einer Verunreinigung von Abhängigkeiten.
    * Java 11-Inkompatibilität.
    * Vermeidung potenzieller Leistungsbeeinträchtigungen für CPU bzw. Arbeitsspeicher.

### <a name="207-march-20-2019"></a>2.0.7 (20. März 2019)
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Backport entfernt `applicationInsights`-Abhängigkeit für:
    * Potenzielles Risiko einer Verunreinigung von Abhängigkeiten.
    * Java 11-Inkompatibilität.
    * Vermeidung potenzieller Leistungsbeeinträchtigungen für CPU bzw. Arbeitsspeicher.

### <a name="211-march-7-2019"></a>2.1.1 (7. März 2019)
#### <a name="new-features"></a>Neue Funktionen
* Hauptversion auf 2.1.1 aktualisiert.

### <a name="206-march-7-2019"></a>2.0.6 (7. März 2019)
#### <a name="new-features"></a>Neue Funktionen
* Ignorierung aller Ausnahmen von Telemetrie.

### <a name="210-december-17-2018"></a>2.1.0 (17. Dezember 2018)
#### <a name="new-features"></a>Neue Funktionen
* Aktualisiert die Version auf 2.1.0, um das Problem zu beheben.

### <a name="205-september-13-2018"></a>2.0.5 (13. September 2018)
#### <a name="new-features"></a>Neue Funktionen
* Fügt die Schlüsselwörter `exists` und `startsWith` hinzu.
* Aktualisiert die Infodatei.
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Korrigiert „Direkter Aufruf von self href für Entität nicht möglich“.
* Korrigiert „Fehler für findAll, wenn Sammlung nicht erstellt wird“.

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (Vorabversion) (23. August 2018)
#### <a name="new-features"></a>Neue Funktionen
* Benennt das Paket von „documentdb“ in „cosmosdb“ um.
* Fügt ein neues Feature des Schlüsselworts für die Abfragemethode hinzu. Jetzt werden 16 Schlüsselwörter aus der SQL-API unterstützt.
* Fügt ein neues Feature für Abfragen mit Seitenverwaltung und Sortierung hinzu.
* Vereinfacht die Konfiguration von „spring-data-cosmosdb“.
* Fügt `deleteCollection`- und `deleteAll`-APIs hinzu.

#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Fehlerbehebung und Entschärfung von Fehlern.

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Informieren Sie sich genauer über das [Spring-Framework](https://spring.io/projects/spring-framework).

Informieren Sie sich genauer über [Spring Boot](https://spring.io/projects/spring-boot).

Informieren Sie sich genauer über [Spring Data](https://spring.io/projects/spring-data).