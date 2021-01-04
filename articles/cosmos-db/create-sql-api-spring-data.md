---
title: 'Schnellstart: Verwenden von Spring Data Azure Cosmos DB v3 zum Erstellen einer Dokumentdatenbank mithilfe von Azure Cosmos DB'
description: Diese Schnellstartanleitung enthält ein Codebeispiel für Spring Data Azure Cosmos DB v3, mit dem Sie eine Verbindung mit der SQL-API von Azure Cosmos DB herstellen und diese API abfragen können.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f31eb0fa6dbb881f7a09b21b9dd4842fdfd291f5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090289"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Schnellstart: Erstellen einer Spring Data Azure Cosmos DB v3-App zum Verwalten von Azure Cosmos DB-SQL-API-Daten
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK V4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In dieser Schnellstartanleitung erstellen und verwalten Sie ein Azure Cosmos DB-SQL-API-Konto im Azure-Portal mithilfe einer über GitHub geklonten Spring Data Azure Cosmos DB v3-App. Sie erstellen zunächst über das Azure-Portal ein Azure Cosmos DB-SQL-API-Konto und anschließend mithilfe des Spring Data Azure Cosmos DB v3-Connectors eine Spring Boot-App. Dann fügen Sie mithilfe der Spring Boot-Anwendung dem Cosmos DB-Konto Ressourcen hinzu. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

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

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement. Sie können auch den [Azure Cosmos DB-Emulator](https://aka.ms/cosmosdb-emulator) mit dem URI `https://localhost:8081` und dem Schlüssel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` verwenden.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) Die Umgebungsvariable `JAVA_HOME` muss auf den Ordner verweisen, in dem das JDK installiert ist.
- Ein [binäres Maven-Archiv](https://maven.apache.org/download.cgi). Führen Sie unter Ubuntu `apt-get install maven` aus, um Maven zu installieren.
- [Git](https://www.git-scm.com/downloads). Führen Sie unter Ubuntu `sudo apt-get install git` aus, um Git zu installieren.

## <a name="introductory-notes"></a>Einführende Hinweise

*Die Struktur eines Cosmos DB-Kontos.* Ein Cosmos DB- *Konto* enthält unabhängig von der API oder der Programmiersprache null oder mehr *Datenbanken* , eine *Datenbank* (DB) enthält null oder mehr *Container* , und ein *Container* enthält null oder mehr Elemente, wie im folgenden Diagramm zu sehen:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entitäten in einem Azure Cosmos-Konto" border="false":::

Weitere Informationen zu Datenbanken, Containern und Elementen finden Sie [hier](account-databases-containers-items.md). Einige wichtige Eigenschaften werden auf der Containerebene definiert. Hierzu zählen unter anderem der *bereitgestellte Durchsatz* und der *Partitionsschlüssel*. 

Der bereitgestellte Durchsatz wird in Anforderungseinheiten (Request Units, *RUs* ) gemessen. Diese haben einen Preis und wirken sich erheblich auf die Betriebskosten des Kontos aus. Der bereitgestellte Durchsatz kann container- oder datenbankspezifisch ausgewählt werden. In der Regel wird jedoch die containerspezifische Durchsatzangabe bevorzugt. Weitere Informationen zur Durchsatzbereitstellung finden Sie [hier](set-throughput.md).

Wenn Elemente in einen Cosmos DB-Container eingefügt werden, wird die Datenbank horizontal skaliert, indem weitere Speicher- und Computeressourcen zur Bewältigung der Anforderungen hinzugefügt werden. Speicher- und Computekapazität werden in diskreten Einheiten ( *Partitionen* ) hinzugefügt. In Ihren Dokumenten muss ein Feld als Partitionsschlüssel ausgewählt werden, durch den jedes Dokument einer Partition zugeordnet wird. Zur Verwaltung von Partitionen wird jeder Partition ein ungefähr gleicher Slice aus dem Bereich der Partitionsschlüsselwerte zugewiesen. Es empfiehlt sich daher, einen relativ zufälligen oder gleichmäßig verteilten Partitionsschlüssel zu wählen. Andernfalls fallen für einige Partitionen deutlich mehr Anforderungen an als für andere. Diese werden als *heiße Partitionen* bzw. *kalte Partitionen* bezeichnet und sollten vermieden werden. Weitere Informationen zur Partitionierung finden Sie [hier](partitioning-overview.md).

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

Vor dem Erstellen einer Dokumentdatenbank müssen Sie ein SQL-API-Konto mit Azure Cosmos DB erstellen.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Hinzufügen eines Containers

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Hinzufügen von Beispieldaten

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Abfragen Ihrer Daten

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen wir nun mit der Verwendung von Code. Klonen Sie zunächst eine SQL-API-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie sie aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können. 

Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Ausführen der App](#run-the-app) fortfahren. 

### <a name="application-configuration-file"></a>Anwendungskonfigurationsdatei

Hier wird gezeigt, wie Spring Boot und Spring Data die Benutzerfreundlichkeit verbessern. Der Prozess zum Einrichten eines Cosmos-Clients und Herstellen einer Verbindung mit Cosmos-Ressourcen erfolgt nun über eine Konfiguration statt über Code. Beim Anwendungsstart setzt Spring Boot alle diese Bausteine mithilfe der Einstellungen in **application.properties** um:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Nachdem Sie ein Azure Cosmos DB-Konto, eine Datenbank und einen Container erstellt haben, füllen Sie einfach die leeren Felder in der Konfigurationsdatei aus, und Spring Boot/Spring Data führt die folgenden Aktionen automatisch aus: (1) Erstellen einer zugrunde liegenden `CosmosClient`-Instanz des Java SDK mit URI und Schlüssel und (2) Herstellen einer Verbindung mit Datenbank und Container. Das ist schon alles – **kein weiterer Ressourcenverwaltungscode!**

### <a name="java-source"></a>Java-Quelle

Der Mehrwert von Spring Data ergibt sich auch durch die einfache, klare, standardisierte und plattformunabhängige Schnittstelle für den Betrieb in Datenspeichern. Nachfolgend finden Sie CRUD- und Abfragebeispiele für die Bearbeitung von Azure Cosmos DB-Dokumenten mit Spring Data Azure Cosmos DB, die auf dem oben verlinkten GitHub-Beispiel für Spring Data basieren.

* Elementerstellung und Aktualisierungen mithilfe der `save`-Methode.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Punktlesevorgänge mithilfe der im Repository definierten abgeleiteten Abfragemethode. `findByIdAndLastName` führt Punktlesevorgänge für `UserRepository` aus. Die im Methodennamen erwähnten Felder bewirken, dass Spring Data einen Punklesevorgang ausführt, der durch die Felder `id` und `lastName` definiert ist:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Löschen von Elementen mithilfe von `deleteAll`:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Abgeleitete Abfrage basierend auf dem Methodennamen im Repository. Spring Data implementiert die `UserRepository` `findByFirstName`-Methode als eine Java SDK-SQL-Abfrage für das Feld `firstName` (diese Abfrage kann nicht als Punktlesevorgang implementiert werden):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Ausführen der App

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen zur Verbindungszeichenfolge abzurufen und die App mit den Endpunktinformationen zu starten. Dadurch kann Ihre App mit Ihrer gehosteten Datenbank kommunizieren.

1. Wechseln Sie im Git-Terminalfenster mithilfe von `cd` in den Ordner mit dem Beispielcode.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die erforderlichen Spring Data Azure Cosmos DB-Pakete zu installieren.

    ```bash
    mvn clean package
    ```

3. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die Spring Data Azure Cosmos DB-Anwendung zu starten:

    ```bash
    mvn spring-boot:run
    ```
    
4. Die App lädt **application.properties** und stellt eine Verbindung mit den Ressourcen in Ihrem Azure Cosmos DB-Konto her.
5. Die App führt die oben beschriebenen CRUD-Punktvorgänge aus.
6. Die App führt eine abgeleitete Abfrage aus.
7. Die App löscht Ihre Ressourcen nicht. Kehren Sie zum Portal zurück, um [die Ressourcen in Ihrem Konto zu bereinigen](#clean-up-resources) und so anfallende Gebühren zu vermeiden.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Cosmos DB-SQL-API-Konto erstellen, eine Dokumentdatenbank und einen Container mit dem Daten-Explorer erstellen und eine Spring Data-App ausführen, um das gleiche Ergebnis programmgesteuert zu erreichen. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB (Importieren von Daten in Azure Cosmos DB)](import-data.md)