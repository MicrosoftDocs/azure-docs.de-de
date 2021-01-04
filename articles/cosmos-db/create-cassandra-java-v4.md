---
title: Java-App mit der Azure Cosmos DB-Cassandra-API unter Verwendung des Java 4.0 SDK
description: In diesem Schnellstart erfahren Sie, wie Sie mithilfe der Cassandra-API von Azure Cosmos DB eine Profilanwendung mit dem Azure-Portal und Java SDK 4.0 erstellen.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ee30af3f07c8b350393822f01833feec6e3b52e2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099809"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v4-driver"></a>Schnellstart: Erstellen einer Java-App zum Verwalten von Azure Cosmos DB-Cassandra-API-Daten (v4-Treiber)
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> 

In dieser Schnellstartanleitung erstellen Sie ein Azure Cosmos DB-Cassandra-API-Konto und verwenden eine über GitHub geklonte Cassandra-Java-App, um eine Cassandra-Datenbank und einen Cassandra-Container mithilfe der [Apache Cassandra v4.x-Treiber](https://github.com/datastax/java-driver/tree/4.x) für Java zu erstellen. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Die Umgebungsvariable `JAVA_HOME` muss auf den Ordner verweisen, in dem das JDK installiert ist.
- Ein [binäres Maven-Archiv](https://maven.apache.org/download.cgi). Führen Sie unter Ubuntu `apt-get install maven` aus, um Maven zu installieren.
- [Git](https://www.git-scm.com/downloads). Führen Sie unter Ubuntu `sudo apt-get install git` aus, um Git zu installieren.

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

Vor dem Erstellen einer Dokumentdatenbank müssen Sie mit Azure Cosmos DB ein Cassandra-Konto erstellen.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen wir nun mit der Verwendung von Code. Wir klonen eine Cassandra-App aus GitHub, legen die Verbindungszeichenfolge fest und führen die App aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können. 

1. Öffnen Sie eine Eingabeaufforderung. Erstellen Sie einen neuen Ordner namens `git-samples`. Schließen Sie dann die Eingabeaufforderung.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started-v4.git
    ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie der Code die Datenbankressourcen erstellt, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) fortfahren. Alle Codeausschnitte stammen aus der Datei *src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java*.  

* `CqlSession` stellt eine Verbindung mit der Cassandra-API von Azure Cosmos DB her und gibt eine Sitzung für den Zugriff zurück (das `Cluster`-Objekt vom v3-Treiber ist jetzt veraltet). Cassandra-Host, -Port, -Benutzername und -Kennwort werden auf der Seite „Verbindungszeichenfolge“ des Azure-Portals festgelegt.

    ```java
        this.session = CqlSession.builder().withSslContext(sc)
                .addContactPoint(new InetSocketAddress(cassandraHost, cassandraPort)).withLocalDatacenter(region)
                .withAuthCredentials(cassandraUsername, cassandraPassword).build();
    ```


Die folgenden Codeausschnitte stammen aus der Datei *src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java*.

* Löschen Sie den Keyspace, wenn er bereits aus einem vorherigen Testlauf vorhanden ist.

    ```java
    public void dropKeyspace() {
        String query = "DROP KEYSPACE IF EXISTS "+keyspace+"";
        session.execute(query);
        LOGGER.info("dropped keyspace '"+keyspace+"'");
    } 
    ```
* Ein neuer Keyspace wird erstellt.

    ```java
    public void createKeyspace() {
        String query = "CREATE KEYSPACE "+keyspace+" WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }";
        session.execute(query);
        LOGGER.info("Created keyspace '"+keyspace+"'");
    }
    ```

* Eine neue Tabelle wird erstellt.

   ```java
    public void createTable() {
        String query = "CREATE TABLE "+keyspace+"."+table+" (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table '"+table+"'");
    }
   ```

* Benutzerentitäten werden mithilfe eines vorbereiteten Anweisungsobjekts eingefügt.

    ```java
    public String prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  "+keyspace+"."+table+" (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return insertStatement;
    }

    public void insertUser(String preparedStatement, int id, String name, String city) {
        PreparedStatement prepared = session.prepare(preparedStatement);
        BoundStatement bound = prepared.bind(id, city, name).setIdempotent(true);
        session.execute(bound);
    }
    ```

* Führen Sie eine Abfrage zum Abrufen der gesamten Benutzerinformationen aus.

    ```java
    public void selectAllUsers() {
        final String query = "SELECT * FROM "+keyspace+"."+table+"";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

 * Führen Sie eine Abfrage zum Abrufen einer einzelnen Benutzerinformation aus.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM "+keyspace+"."+table+" where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren. Die Angabe der Verbindungszeichenfolgendetails ermöglicht Ihrer App die Kommunikation mit Ihrer gehosteten Datenbank.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Azure Cosmos DB-Konto die Option **Verbindungszeichenfolge** aus. 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="Anzeigen und Kopieren eines Benutzernamens im Azure-Portal auf der Seite „Verbindungszeichenfolge“":::

2. Verwenden Sie die Schaltfläche :::image type="icon" source="./media/create-cassandra-java/copy-button-azure-portal.png"::: auf der rechten Seite des Bildschirms, um den Kontaktpunktwert zu kopieren.

3. Öffnen Sie die Datei *config.properties* im Ordner *C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources*. 

3. Ersetzen Sie `<Cassandra endpoint host>` in Zeile 2 durch den Kontaktpunktwert aus dem Portal.

    Zeile 2 von *config.properties* sollte etwa wie folgt aussehen: 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Kehren Sie zum Portal zurück, und kopieren Sie den Wert für BENUTZERNAME. Ersetzen Sie `<cassandra endpoint username>` in Zeile 4 durch den Benutzernamen aus dem Portal.

    Zeile 4 von *config.properties* sollte etwa wie folgt aussehen: 

    `cassandra_username=cosmos-db-quickstart`

4. Kehren Sie zum Portal zurück, und kopieren Sie den Wert für KENNWORT. Ersetzen Sie `<cassandra endpoint password>` in Zeile 5 durch das Kennwort aus dem Portal.

    Zeile 5 von *config.properties* sollte etwa wie folgt aussehen: 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Wenn Sie ein bestimmtes TLS-/SSL-Zertifikat verwenden möchten, ersetzen Sie `<SSL key store file location>` in Zeile 6 durch den Speicherort des TLS-/SSL-Zertifikats. Wenn kein Wert angegeben ist, wird das unter „<JAVA_HOME>/jre/lib/security/cacerts“ installierte JDK-Zertifikat verwendet. 

6. Wenn Sie Zeile 6 so geändert haben, dass ein bestimmtes TLS-/SSL-Zertifikat verwendet wird, aktualisieren Sie Zeile 7 so, dass das Kennwort für das betreffende Zertifikat verwendet wird. 

7. Beachten Sie, dass Sie die Standardregion (z. B. `West US`) für den Kontaktpunkt hinzufügen müssen. Beispiel:

    `region=West US`

    Der Grund dafür ist, dass der v.4x-Treiber nur die Kombination eines lokalen DC mit dem Kontaktpunkt zulässt. Wenn Sie eine andere Region als die Standardregion (d. h. die Region, die bei der ersten Erstellung des Cosmos DB-Kontos angegeben wurde) hinzufügen möchten, müssen Sie beim Hinzufügen eines Kontaktpunkts das regionale Suffix verwenden, z. B. `host-westus.cassandra.cosmos.azure.com`.

8. Speichern Sie die Datei *config.properties*.

## <a name="run-the-java-app"></a>Ausführen der Java-App

1. Wechseln Sie im Git-Terminalfenster mithilfe von `cd` in den Ordner `azure-cosmosdb-cassandra-java-getting-started-v4`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started-v4"
    ```

2. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die Datei `cosmosdb-cassandra-examples.jar` zu generieren.

    ```git
    mvn clean install
    ```

3. Führen Sie im Terminalfenster von Git den folgenden Befehl aus, um die Java-Anwendung zu starten.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Im Terminalfenster werden Benachrichtigungen angezeigt, dass der Keyspace und die Tabelle erstellt wurden. Anschließend werden alle Benutzer in der Tabelle ausgewählt und zurückgegeben, und die Ausgabe wird angezeigt. Dann wird eine Zeile anhand der ID ausgewählt und der Wert angezeigt.  

    Drücken Sie STRG+C, um die Programmausführung zu beenden und das Konsolenfenster zu schließen.

4. Öffnen Sie im Azure-Portal den **Daten-Explorer** , um diese neuen Daten abzufragen, zu ändern und zu verwenden. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Anzeigen der Daten im Daten-Explorer: Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie ein Azure Cosmos DB-Konto mit Cassandra-API erstellen und eine Cassandra-Java-App ausführen, die eine Cassandra-Datenbank und einen Cassandra-Container erstellt. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB: Import Cassandra data](cassandra-import-data.md) (Azure Cosmos DB: Importieren von Cassandra-Daten)
