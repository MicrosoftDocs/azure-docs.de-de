---
title: Erstellen einer Azure Cosmos DB-Node.js-Anwendung mit der Gremlin-API
description: Hier finden Sie ein Node.js-Codebeispiel, das Sie zum Verbinden mit Azure Cosmos DB und Senden entsprechender Abfragen verwenden können.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/05/2019
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: 0b88456bd245f79c38d7829dca76de702f768c06
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360138"
---
# <a name="quickstart-build-a-nodejs-application-by-using-azure-cosmos-db-gremlin-api-account"></a>Schnellstart: Erstellen einer Node.js-Anwendung mithilfe eines Gremlin-API-Kontos für Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-Konsole](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

In dieser Schnellstartanleitung erstellen und verwalten Sie ein Azure Cosmos DB-Gremlin-API-Konto (Graph) im Azure-Portal und fügen Daten mithilfe einer über GitHub geklonten Node.js-App hinzu. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Node.js 0.10.29 oder höher](https://nodejs.org/)
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Hinzufügen eines Graphs

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Klonen Sie eine Gremlin-API-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie sie aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können. 

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Öffnen Sie die Projektmappendatei in Visual Studio. 

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) fortfahren. 

Die folgenden Codeausschnitte stammen alle aus der Datei *app.js*.

In dieser Konsolen-App wird der Open-Source-Treiber [Gremlin Node.js](https://www.npmjs.com/package/gremlin) verwendet.

* Der Gremlin-Client wird erstellt.

    ```javascript
    const authenticator = new Gremlin.driver.auth.PlainTextSaslAuthenticator(
        `/dbs/${config.database}/colls/${config.collection}`, 
        config.primaryKey
    )


    const client = new Gremlin.driver.Client(
        config.endpoint, 
        { 
            authenticator,
            traversalsource : "g",
            rejectUnauthorized : true,
            mimeType : "application/vnd.gremlin-v2.0+json"
        }
    );

    ```

  Die Konfigurationen befinden sich alle in *config.js*. Diese Datei wird im [folgenden Abschnitt](#update-your-connection-string) bearbeitet.

* Eine Reihe von Funktionen wird definiert, um verschiedene Gremlin-Vorgänge auszuführen. Hierzu zählt beispielsweise Folgendes:

    ```javascript
    function addVertex1()
    {
        console.log('Running Add Vertex1'); 
        return client.submit("g.addV(label).property('id', id).property('firstName', firstName).property('age', age).property('userid', userid).property('pk', 'pk')", {
                label:"person",
                id:"thomas",
                firstName:"Thomas",
                age:44, userid: 1
            }).then(function (result) {
                    console.log("Result: %s\n", JSON.stringify(result));
            });
    }
    ```

* Jede Funktion führt eine Methode vom Typ `client.execute` mit einem Gremlin-Abfragezeichenfolgenparameter aus. Beispiel für die Ausführung von `g.V().count()`:

    ```javascript
    function countVertices()
    {
        console.log('Running Count');
        return client.submit("g.V().count()", { }).then(function (result) {
            console.log("Result: %s\n", JSON.stringify(result));
        });
    }
    ```

* Am Ende der Datei werden dann alle Methoden aufgerufen. So werden sie nacheinander ausgeführt:

    ```javascript
    client.open()
    .then(dropGraph)
    .then(addVertex1)
    .then(addVertex2)
    .then(addEdge)
    .then(countVertices)
    .catch((err) => {
        console.error("Error running query...");
        console.error(err)
    }).then((res) => {
        client.close();
        finish();
    }).catch((err) => 
        console.error("Fatal error:", err)
    );
    ```


## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

1. Öffnen Sie die Datei *config.js*. 

2. Fügen Sie in der Datei *config.js* den `config.endpoint`-Schlüssel mit dem Wert für den **Gremlin-Endpunkt** von der Seite **Übersicht** Ihres Cosmos DB-Kontos im Azure-Portal ein. 

    `config.endpoint = "https://<your_Gremlin_account_name>.gremlin.cosmosdb.azure.com:443/";`

    :::image type="content" source="./media/create-graph-nodejs/gremlin-uri.png" alt-text="Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal auf der Seite „Übersicht“":::

3. Fügen Sie in der Datei *config.js* den config.primaryKey-Wert mit dem Wert für **Primärschlüssel** von der Seite **Schlüssel** Ihres Cosmos DB-Kontos im Azure-Portal ein. 

    `config.primaryKey = "PRIMARYKEY";`

   :::image type="content" source="./media/create-graph-nodejs/keys.png" alt-text="Azure-Portal-Blatt „Schlüssel“":::

4. Geben Sie den Datenbanknamen und den Graphnamen (Container) für den Wert von „config.database“ und „config.collection“ ein. 

Hier sehen Sie ein Beispiel dafür, wie Ihre fertige Datei *config.js* aussehen sollte:

```javascript
var config = {}

// Note that this must include the protocol (HTTPS:// for .NET SDK URI or wss:// for Gremlin Endpoint) and the port number
config.endpoint = "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"; 
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Ausführen der Konsolenanwendung

1. Öffnen Sie ein Terminalfenster, und wechseln Sie (über den `cd`-Befehl) zum Installationsverzeichnis der Datei *package.json* , die im Projekt enthalten ist.

2. Führen Sie `npm install` aus, um die erforderlichen npm-Module zu installieren, einschließlich `gremlin`.

3. Führen Sie `node app.js` in einem Terminal aus, um Ihre Node-Anwendung zu starten.

## <a name="browse-with-data-explorer"></a>Durchsuchen mit dem Daten-Explorer

Sie können nun zum Daten-Explorer im Azure-Portal zurückkehren, um mit den neuen Graph-Daten zu arbeiten und um diese anzuzeigen, abzufragen und zu ändern.

Im Daten-Explorer wird die neue Datenbank im **Diagrammbereich** angezeigt. Erweitern Sie die Datenbank und den Container, und wählen Sie anschließend **Graph** aus.

Die von der Beispiel-App generierten Daten werden im nächsten Bereich auf der Registerkarte **Graph** angezeigt, wenn Sie **Filter anwenden** auswählen.

Vervollständigen Sie `g.V()` mit `.has('firstName', 'Thomas')`, um den Filter zu testen. Halten Sie sich dabei an die Groß-/Kleinschreibung des Werts.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, einen Graph mit dem Daten-Explorer erstellen und eine Node.js-App ausführen, um dem Graph Daten hinzuzufügen. Nun können Sie komplexere Abfragen erstellen und leistungsfähige Logik zum Durchlaufen von Diagrammen mit Gremlin implementieren. 

> [!div class="nextstepaction"]
> [Abfragen mit Gremlin](tutorial-query-graph.md)
