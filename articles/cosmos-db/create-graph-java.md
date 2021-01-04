---
title: Erstellen einer Graphdatenbank mit Java in Azure Cosmos DB
description: Hier finden Sie ein Java-Codebeispiel, das Sie zum Herstellen einer Verbindung mit und zum Abfragen von Diagrammdaten in Azure Cosmos DB mit Gremlin verwenden können.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: chrande
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a793eb69b344e361679759e46db47c78885831e
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360954"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Schnellstart: Erstellen einer Graphdatenbank mit dem Java SDK und der Azure Cosmos DB-Gremlin-API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-Konsole](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

In dieser Schnellstartanleitung erstellen und verwalten Sie ein Azure Cosmos DB-Gremlin-API-Konto (Graph) im Azure-Portal und fügen Daten mithilfe einer über GitHub geklonten Java-App hinzu. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Die Umgebungsvariable `JAVA_HOME` muss auf den Ordner verweisen, in dem das JDK installiert ist.
- Ein [binäres Maven-Archiv](https://maven.apache.org/download.cgi) 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

Vor dem Erstellen einer Graphdatenbank müssen Sie ein Gremlin (Graph)-Datenbankkonto mit Azure Cosmos DB erstellen.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Hinzufügen eines Graphs

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen wir nun mit der Verwendung von Code. Klonen Sie eine Gremlin-API-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie sie aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können.  

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Terminalfenster von Git (z. B. Git Bash), und verwenden Sie den Befehl `cd`, um den Ordner zu ändern, in den die Beispiel-App gespeichert wird.  

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-information) fortfahren.

Die folgenden Codeausschnitte stammen alle aus der Datei *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java*.

Diese Java-Konsolen-App verwendet eine [Gremlin-API](graph-introduction.md)-Datenbank mit dem OSS-Treiber [Apache TinkerPop](https://tinkerpop.apache.org/). 

- Das Gremlin-Element `Client` wird über die Konfiguration in der Datei *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml* initialisiert.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Eine Reihe von Gremlin-Schritten wird mithilfe der `client.submit`-Methode ausgeführt.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Aktualisieren der Verbindungsinformationen

Wechseln Sie nun zurück zum Azure-Portal, um die Verbindungsinformationen abzurufen und in die App zu kopieren. Durch diese Einstellungen kann Ihre App mit Ihrer gehosteten Datenbank kommunizieren.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Azure Cosmos DB-Konto die Option **Schlüssel** aus. 

    Kopieren Sie den ersten Teil des URI-Werts.

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal auf der Seite „Schlüssel“":::

2. Öffnen Sie die Datei *src/remote.yaml* , und fügen Sie den eindeutigen ID-Wert über `$name$` in `hosts: [$name$.graphs.azure.com]` ein.

    Zeile 1 von *remote.yaml* sollte etwa wie folgt aussehen: 

    `hosts: [test-graph.graphs.azure.com]`

3. Ändern Sie `graphs` in `gremlin.cosmosdb` im Wert `endpoint`. (Falls Sie Ihr Graphdatenbankkonto vor dem 20. Dezember 2017 erstellt haben, lassen Sie den Endpunktwert unverändert, und fahren Sie mit dem nächsten Schritt fort.)

    Der Endpunktwert sollte nun wie folgt aussehen:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. Verwenden Sie die Schaltfläche „Kopieren“ im Azure-Portal, um den Primärschlüssel zu kopieren und ihn über `$masterKey$` in `password: $masterKey$` einzufügen.

    Zeile 4 von *remote.yaml* sollte etwa wie folgt aussehen: 

    `password: 2Ggkr662ifxz2Mg==`

5. Ändern Sie Zeile 3 in *remote.yaml* von

    `username: /dbs/$database$/colls/$collection$`

    zu 

    `username: /dbs/sample-database/colls/sample-graph`

    Falls Sie für Ihre Beispieldatenbank oder für Ihr Diagramm einen eindeutigen Namen verwendet haben, aktualisieren Sie die Werte entsprechend.

6. Speichern Sie die Datei *remote.yaml*.

## <a name="run-the-console-app"></a>Ausführen der Konsolenanwendung

1. Wechseln Sie im Terminalfenster von Git mithilfe von `cd` zum Ordner „azure-cosmos-db-graph-java-getting-started“.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die erforderlichen Java-Pakete zu installieren.

   ```git
   mvn package
   ```

3. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die Java-Anwendung zu starten.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    Im Terminalfenster werden die Scheitelpunkte angezeigt, die dem Graph hinzugefügt werden. 
    
    Wenn Timeoutfehler auftreten, überprüfen Sie, ob Sie die Verbindungsinformationen unter [Aktualisieren der Verbindungsinformationen](#update-your-connection-information) richtig aktualisiert haben, und versuchen Sie auch, den letzten Befehl erneut auszuführen. 
    
    Drücken Sie nach Abschluss des Programms die EINGABETASTE, und wechseln Sie dann in Ihrem Internetbrowser wieder zum Azure-Portal. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Überprüfen und Hinzufügen von Beispieldaten

Nun können Sie wieder zum Daten-Explorer zurückkehren, um sich die dem Graph hinzugefügten Scheitelpunkte anzusehen und weitere Datenpunkte hinzuzufügen.

1. Wählen Sie im Azure-Portal in Ihrem Azure Cosmos DB-Konto **Daten-Explorer** aus, erweitern Sie den Eintrag **sample-graph** , und wählen Sie **Graph** und anschließend **Filter anwenden** aus. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="Screenshot: In der API ausgewählter Graph mit der Option „Filter anwenden“":::

2. Beachten Sie in der Liste **Ergebnisse** die Benutzer, die dem Graph neu hinzugefügt wurden. Wählen Sie **ben** aus, und beachten Sie, dass der Benutzer mit „robin“ verbunden ist. Sie können die Scheitelpunkte bewegen, indem Sie sie ziehen und loslassen, zoomen, indem Sie das Mausrad verwenden, und die Größe des Graphen verändern, indem Sie auf den Doppelpfeil klicken. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Neue Scheitelpunkte im Graph im Daten-Explorer im Azure-Portal":::

3. Fügen Sie nun einige neue Benutzer hinzu. Wählen Sie **New Vertex** (Neuer Scheitelpunkt) aus, um dem Graph Daten hinzuzufügen.

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Screenshot: Bereich „New Vertex“ (Neuer Scheitelpunkt), in dem Sie Werte eingeben können":::

4. Geben Sie im Feld für die Bezeichnung die Zeichenfolge *person* ein.

5. Wählen Sie **Eigenschaft hinzufügen** aus, um die folgenden Eigenschaften hinzuzufügen. Beachten Sie, dass Sie für jede Person in Ihrem Graph eindeutige Eigenschaften erstellen können. Nur der id-Schlüssel ist erforderlich.

    Schlüssel|value|Notizen
    ----|----|----
    id|ashley|Der eindeutige Bezeichner für den Scheitelpunkt. Wenn Sie keine ID angeben, wird automatisch eine ID generiert.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > In dieser Schnellstartanleitung wird eine nicht partitionierte Sammlung erstellt. Wenn Sie hingegen eine partitionierte Sammlung erstellen, indem Sie bei der Sammlungserstellung einen Partitionsschlüssel angeben, muss der Partitionsschlüssel jedem neuen Scheitelpunkt als Schlüssel hinzugefügt werden. 

6. Klicken Sie auf **OK**. Der Bildschirm muss möglicherweise erweitert werden, damit **OK** am unteren Bildschirmrand zu sehen ist.

7. Wählen Sie erneut **New Vertex** (Neuer Scheitelpunkt) aus, und fügen Sie einen weiteren neuen Benutzer hinzu. 

8. Geben Sie als Bezeichnung *Person* ein.

9. Wählen Sie **Eigenschaft hinzufügen** aus, um die folgenden Eigenschaften hinzuzufügen:

    Schlüssel|value|Notizen
    ----|----|----
    id|rakesh|Der eindeutige Bezeichner für den Scheitelpunkt. Wenn Sie keine ID angeben, wird automatisch eine ID generiert.
    gender|male| 
    school|MIT| 

10. Klicken Sie auf **OK**. 

11. Wählen Sie die Schaltfläche **Filter anwenden** mit dem Standardfilter `g.V()` aus, um alle Werte des Graphen anzuzeigen. Daraufhin werden alle Benutzer in der Liste **Ergebnisse** angezeigt. 

    Wenn Sie weitere Daten hinzufügen, können Sie Ihre Ergebnisse mithilfe von Filtern eingrenzen. Daten-Explorer verwendet standardmäßig `g.V()`, um alle Scheitelpunkte eines Graphen abzurufen. Sie können ihn in eine andere [Graphabfrage](tutorial-query-graph.md) wie z.B. `g.V().count()` ändern, um die Anzahl aller Scheitelpunkte eines Graphen im JSON-Format zurückzugeben. Wenn Sie den Filter geändert haben, ändern Sie den Filter zurück in `g.V()`, und wählen Sie **Filter anwenden** aus, um wieder alle Ergebnisse anzuzeigen.

12. Als Nächstes verbinden Sie „rakesh“ und „ashley“. Vergewissern Sie sich, dass in der Liste **Ergebnisse** der Eintrag **ashley** ausgewählt ist, und wählen Sie anschließend rechts unten neben **Ziele** die Option :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="Ziel eines Scheitelpunkts in einem Graph ändern"::: aus. Sollte die Schaltfläche nicht zu sehen sein, müssen Sie ggf. das Fenster verbreitern.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Ändern des Ziels eines Scheitelpunkts in einem Graph: Azure CosmosDB":::

13. Geben Sie im Feld **Ziel** die Zeichenfolge *rakesh* und im Feld **Kantenbezeichnung** die Zeichenfolge *knows* ein, und aktivieren Sie anschließend das Kontrollkästchen.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="Hinzufügen einer Verbindung im Daten-Explorer: Azure CosmosDB":::

14. Wählen Sie nun in der Ergebnisliste den Eintrag **rakesh** aus. Wie Sie sehen, sind „ashley“ und „rakesh“ miteinander verbunden. 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="Zwei im Daten-Explorer verbundene Scheitelpunkte: Azure CosmosDB":::

Damit haben Sie den Teil des Tutorials, in dem die Ressourcen erstellt werden, abgeschlossen. Sie können weiter Scheitelpunkte zu Ihrem Graphen hinzufügen, die vorhandenen Scheitelpunkte anpassen oder die Abfragen ändern. Sehen Sie sich nun die von Azure Cosmos DB bereitgestellten Metriken an, und bereinigen Sie anschließend die Ressourcen. 

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, einen Graph mit dem Daten-Explorer erstellen und eine Java-App ausführen, die dem Graph Daten hinzufügt. Nun können Sie komplexere Abfragen erstellen und leistungsfähige Logik zum Traversieren von Graphen mit Gremlin implementieren. 

> [!div class="nextstepaction"]
> [Query using Gremlin (Abfragen mithilfe von Gremlin)](tutorial-query-graph.md)

