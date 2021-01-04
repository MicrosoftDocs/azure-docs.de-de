---
title: Versionshinweise und Ressourcen zu Spring Data Azure Cosmos DB v3 für die SQL-API
description: Hier finden Sie Informationen zu Spring Data Azure Cosmos DB v3 für die SQL-API, z. B. Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 64054a2bb5c1f7e17eef87c3babb28137b6c912a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097123"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v3 für die Core (SQL)-API: Versionshinweise und Ressourcen
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
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-Ressourcenanbieter](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

Mit Spring Data Azure Cosmos DB Version 3 für Core (SQL) können Entwickler Azure Cosmos DB in Spring-Anwendungen nutzen. Bei Spring Data Azure Cosmos DB wird die Spring Data-Schnittstelle für die Bearbeitung von Datenbanken und Sammlungen, die Verwendung von Dokumenten und die Ausgabe von Abfragen verfügbar gemacht. Für dasselbe Maven-Artefakt werden sowohl synchrone als auch asynchrone (reaktive) APIs unterstützt. 

Spring Data Azure Cosmos DB ist abhängig vom Spring Data-Framework. Das Azure Cosmos DB SDK-Team veröffentlicht Maven-Artefakte für die Spring Data-Versionen 2.2 und 2.3.

Das [Spring-Framework](https://spring.io/projects/spring-framework) ist ein Programmier- und Konfigurationsmodell, mit dem die Java-Anwendungsentwicklung optimiert wird. Spring optimiert die „Verknüpfung“ von Anwendungen mithilfe von Abhängigkeitsinjektion. Spring ist bei vielen Entwicklern beliebt, weil dadurch das Erstellen und Testen von Anwendungen vereinfacht wird. Mit [Spring Boot](https://spring.io/projects/spring-boot) wird diese „Verknüpfung“ auf die Entwicklung von Webanwendungen und Microservices ausgedehnt. [Spring Data](https://spring.io/projects/spring-data) ist ein Programmiermodell und -framework für den Zugriff auf Datenspeicher wie Azure Cosmos DB, im Kontext einer Spring- bzw. Spring Boot-Anwendung. 

Sie können Spring Data Azure Cosmos DB in Ihren [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/)-Anwendungen verwenden.

> [!IMPORTANT]  
> Diese Versionshinweise gelten für Version 3 von Spring Data Azure Cosmos DB. Sie können [Versionshinweise zu Version 2 hier](sql-api-sdk-java-spring-v2.md) finden. 
>
> Spring Data Azure Cosmos DB unterstützt nur die SQL-API.
>
> In diesen Artikeln finden Sie Informationen zu Spring Data für andere Azure Cosmos DB-APIs:
> * [Spring Data für Apache Cassandra mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin Starter mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Beginnen Sie hier

# <a name="explore"></a>[Erkunden](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Diese Registerkarten enthalten grundlegende Beispiele zu Spring Data Azure Cosmos DB.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurieren von Abhängigkeiten

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Herstellen einer Verbindung](#tab/connect)

### <a name="connect"></a>Verbinden

Geben Sie Details zum Azure Cosmos DB-Konto und -Container an. Spring Data Azure Cosmos DB erstellt den Client automatisch und stellt eine Verbindung mit dem Container her.

[application.properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc-Ops](#tab/docs)

### <a name="document-operations"></a>Dokumentenvorgänge

---

## <a name="resources"></a>Ressourcen

* **Tragen zum SDK bei** : [Spring Data Azure Cosmos DB-Repository auf GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Tutorial** : [Spring Data Azure Cosmos DB-Tutorial auf GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Informieren Sie sich genauer über das [Spring-Framework](https://spring.io/projects/spring-framework).

Informieren Sie sich genauer über [Spring Boot](https://spring.io/projects/spring-boot).

Informieren Sie sich genauer über [Spring Data](https://spring.io/projects/spring-data).