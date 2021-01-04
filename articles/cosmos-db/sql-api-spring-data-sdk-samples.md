---
title: 'Azure Cosmos DB SQL-API: Spring Data v3-Beispiele'
description: Spring Data v3-Beispiele für allgemeine Aufgaben mit der SQL-API von Azure Cosmos DB (einschließlich CRUD-Vorgängen) finden Sie auf GitHub.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 378bb891c8539a6cf3d61f6511a0f58377d2bfd2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091139"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL-API: Spring Data Azure Cosmos DB v3-Beispiele
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK-Beispiele](sql-api-dotnet-samples.md)
> * [.NET V3 SDK-Beispiele](sql-api-dotnet-v3sdk-samples.md)
> * [Java v4 SDK-Beispiele](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK-Beispiele](sql-api-spring-data-sdk-samples.md)
> * [Node.js-Beispiele](sql-api-nodejs-samples.md)
> * [Python-Beispiele](sql-api-python-samples.md)
> * [Katalog mit Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

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

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Sie können [Visual Studio-Abonnementvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Ihr Visual Studio-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Die neuesten Beispielanwendungen, in denen CRUD-Vorgänge und andere gängige Vorgänge für Azure Cosmos DB-Ressourcen ausgeführt werden, finden Sie im GitHub-Repository [azure-spring-data-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples). Dieser Artikel enthält Folgendes:

* Links zu den Aufgaben in den einzelnen Spring Data Azure Cosmos DB-Beispielprojektdateien. 
* Links zum zugehörigen API-Referenzinhalt.

**Voraussetzungen**

Zum Ausführen dieser Beispielanwendung benötigen Sie Folgendes:

* Java Development Kit 8
* Spring Data Azure Cosmos DB v3

Optional können Sie Maven verwenden, um die aktuellen Binärdateien von Spring Data Azure Cosmos DB v3 zur Verwendung in Ihrem Projekt abzurufen. Maven fügt alle erforderlichen Abhängigkeiten automatisch hinzu. Andernfalls können Sie die in der Datei **pom.xml** aufgelisteten Abhängigkeiten direkt herunterladen und Ihrem Buildpfad hinzufügen.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Ausführen der Beispielanwendungen**

Klonen des Beispielrepositorys:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

Sie können die Beispiele entweder in einer IDE (Eclipse, IntelliJ oder VSCODE) oder über die Befehlszeile mit Maven ausführen.

Die folgenden Umgebungsvariablen müssen in **application.properties** festgelegt sein.

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Hiermit wird den Beispielen Lese-/Schreibzugriff auf Ihr Konto, Datenbanken und Container gewährt.

Vielleicht bietet Ihre IDE die Möglichkeit, den Spring Data-Beispielcode auszuführen. Andernfalls können Sie den folgenden Terminalbefehl verwenden, um das Beispiel auszuführen:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>CRUD-Beispiele für Dokumente
Die [Beispieldatei](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über Azure Cosmos-Dokumente zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md).

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen eines Dokuments](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository.save |
| [Lesen eines Dokuments nach ID](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [Löschen aller Dokumente](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>Beispiele für abgeleitete Abfragemethoden
Die [Beispieldatei](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über Azure Cosmos DB-Abfragen zu erfahren, können Sie vor dem Ausführen der folgenden Beispiele den Artikel zu [abgeleiteten Abfragemethoden in Spring von Baeldung](https://www.baeldung.com/spring-data-derived-queries) lesen.

| [Abfragen von Dokumenten](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository.derivedQueryMethod |

## <a name="custom-query-examples"></a>Beispiele für benutzerdefinierte Abfragen
Die [Beispieldatei](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) zeigt, wie die folgenden Aufgaben mithilfe der SDL-Abfragegrammatik ausgeführt werden. Machen Sie sich im Artikel [SQL-Abfragebeispiele](./sql-query-getting-started.md) mit der SQL-Abfragereferenz in Azure Cosmos DB vertraut, bevor Sie die folgenden Beispiele ausführen. 


| Aufgabe | API-Referenz |
| --- | --- |
| [Abfragen aller Dokumente](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query-Anmerkung |
| [Abfragen der Gleichheit mit „==“](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query-Anmerkung |
| [Abfragen der Ungleichheit mit „!=“ und „NOT“](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query-Anmerkung |
| [Abfrage mit Bereichsoperatoren wie „&gt;“, „&lt;“, „&gt;=“, „&lt;=“](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query-Anmerkung |
| [Abfragen mit Bereichsoperatoren für Zeichenfolgen](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query-Anmerkung |
| [Abfragen mit „ORDER BY“](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query-Anmerkung |
| [Abfragen mit „DISTINCT“](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query-Anmerkung |
| [Abfragen mit Aggregatfunktionen](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query-Anmerkung |
| [Verwenden von Unterdokumenten](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query-Anmerkung |
| [Abfragen mit dokumentinternen Verknüpfungen (Joins)](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query-Anmerkung |
| [Abfragen mit Zeichenfolgen-, mathematischen und Arrayoperatoren](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query-Anmerkung |