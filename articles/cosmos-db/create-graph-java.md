---
title: Erstellen einer Azure Cosmos DB-Graphdatenbank mit Java
description: Hier finden Sie ein Java-Codebeispiel, das Sie zum Herstellen einer Verbindung mit und zum Abfragen von Diagrammdaten in Azure Cosmos DB mit Gremlin verwenden können.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: lbosq
ms.openlocfilehash: a56fab3f8f572f277c11dc566c7d74104226b4fc
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480565"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-the-java-sdk"></a>Schnellstart: Erstellen einer Graphdatenbank in Azure Cosmos DB mit dem Java-SDK 

> [!div class="op_single_selector"]
> * [Gremlin-Konsole](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB ist der global verteilte Microsoft-Datenbankdienst mit mehreren Modellen. Mit Azure Cosmos DB können Sie verwaltete Dokument-, Tabellen- und Diagrammdatenbanken erstellen und abfragen. 

In dieser Schnellstartanleitung wird mithilfe der Tools des Azure-Portals für Azure Cosmos DB eine einfache Graphdatenbank erstellt. Darüber hinaus erfahren Sie hier, wie Sie unter Verwendung des OSS-Treibers [Apache TinkerPop](https://tinkerpop.apache.org/) schnell eine Java-Konsolen-App erstellen, die eine [Gremlin-API](graph-introduction.md) verwendet. Die Anweisungen in dieser Schnellstartanleitung gelten für alle Betriebssysteme, unter denen Java ausgeführt werden kann. In dieser Schnellstartanleitung erfahren Sie, wie Sie Graphen über die Benutzeroberfläche oder programmgesteuert erstellen und ändern. 

## <a name="prerequisites"></a>Voraussetzungen
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Außerdem haben Sie folgende Möglichkeiten:

* [Java Development Kit (JDK), Version 8](https://aka.ms/azure-jdks)
    * Achten Sie darauf, dass die Umgebungsvariable „JAVA_HOME“ auf den Ordner verweist, in dem das JDK installiert ist.
* Ein binäres [Maven](https://maven.apache.org/)-Archiv ([Download](https://maven.apache.org/download.cgi)/[Installationsanleitung](https://maven.apache.org/install.html))
    * Unter Ubuntu können Sie `apt-get install maven` ausführen, um Maven zu installieren.
* [Git-Client](https://www.git-scm.com/)
    * Unter Ubuntu können Sie `sudo apt-get install git` ausführen, um Git zu installieren.

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

Die folgenden Codeausschnitte stammen alle aus der Datei „C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java“.

* Der `Client` von Gremlin wird über die Konfiguration in der Datei „C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml“ initialisiert.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* Eine Reihe von Gremlin-Schritten wird mithilfe der `client.submit`-Methode ausgeführt.

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

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Schlüssel**. 

    Kopieren Sie den ersten Teil des URI-Werts.

    ![Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal auf der Seite „Schlüssel“](./media/create-graph-java/keys.png)
2. Öffnen Sie die Datei „src/remote.yaml“, und fügen Sie den eindeutigen ID-Wert über `$name$` in `hosts: [$name$.graphs.azure.com]` ein.

    Die erste Zeile von „remote.xaml“ sollte etwa wie folgt aussehen: 

    `hosts: [test-graph.graphs.azure.com]`

3. Ändern Sie `graphs` in `gremlin.cosmosdb` im Wert `endpoint`. (Falls Sie Ihr Graphdatenbankkonto vor dem 20. Dezember 2017 erstellt haben, lassen Sie den Endpunktwert unverändert, und fahren Sie mit dem nächsten Schritt fort.)

    Der Endpunktwert sollte nun wie folgt aussehen:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. Verwenden Sie die Schaltfläche „Kopieren“ im Azure-Portal, um den Primärschlüssel zu kopieren und ihn über `$masterKey$` in `password: $masterKey$` einzufügen.

    Die vierte Zeile von „remote.xaml“ sollte etwa wie folgt aussehen: 

    `password: 2Ggkr662ifxz2Mg==`

5. Ändern Sie die dritte Zeile von „remote.yaml“ in

    `username: /dbs/$database$/colls/$collection$`

    zu 

    `username: /dbs/sample-database/colls/sample-graph`

    Falls Sie für Ihre Beispieldatenbank oder für Ihr Diagramm einen eindeutigen Namen verwendet haben, aktualisieren Sie die Werte entsprechend.

6. Speichern Sie die Datei „remote.yaml“.

## <a name="run-the-console-app"></a>Ausführen der Konsolenanwendung

1. Wechseln Sie im Terminalfenster von Git mithilfe von `cd` zum Ordner „azure-cosmos-db-graph-java-getting-started“.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die erforderlichen Java-Pakete zu installieren.

   ```
   mvn package
   ```

3. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die Java-Anwendung zu starten.
    
    ```
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    Im Terminalfenster werden die Scheitelpunkte angezeigt, die dem Graph hinzugefügt werden. 
    
    Wenn Timeoutfehler auftreten, überprüfen Sie, ob Sie die Verbindungsinformationen unter [Aktualisieren der Verbindungsinformationen](#update-your-connection-information) richtig aktualisiert haben, und versuchen Sie auch, den letzten Befehl erneut auszuführen. 
    
    Drücken Sie nach Abschluss des Programms die EINGABETASTE, und wechseln Sie dann in Ihrem Internetbrowser wieder zum Azure-Portal. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Überprüfen und Hinzufügen von Beispieldaten

Nun können Sie wieder zum Daten-Explorer zurückkehren, um sich die dem Graph hinzugefügten Scheitelpunkte anzusehen und weitere Datenpunkte hinzuzufügen.

1. Erweitern Sie im **Daten-Explorer** **sample-graph**, klicken Sie auf **Graph**, und klicken Sie anschließend auf **Filter anwenden**. 

   ![Neue Dokumente im Daten-Explorer im Azure-Portal erstellen](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. Beachten Sie in der Liste **Ergebnisse** die Benutzer, die dem Graph neu hinzugefügt wurden. Wählen Sie **ben** aus, und beachten Sie, dass der Benutzer mit „robin“ verbunden ist. Sie können die Scheitelpunkte bewegen, indem Sie sie ziehen und loslassen, zoomen, indem Sie das Mausrad verwenden, und die Größe des Graphen verändern, indem Sie auf den Doppelpfeil klicken. 

   ![Neue Scheitelpunkte im Graph im Daten-Explorer im Azure-Portal](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Fügen Sie nun einige neue Benutzer hinzu. Klicken Sie auf die Schaltfläche **New Vertex** (Neuer Scheitelpunkt), um dem Graph Daten hinzuzufügen.

   ![Neue Dokumente im Daten-Explorer im Azure-Portal erstellen](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Geben Sie im Feld für die Bezeichnung die Zeichenfolge *person* ein.

5. Klicken Sie auf **Eigenschaft hinzufügen**, um die folgenden Eigenschaften hinzuzufügen: Beachten Sie, dass Sie für jede Person in Ihrem Graph eindeutige Eigenschaften erstellen können. Nur der id-Schlüssel ist erforderlich.

    key|value|Notizen
    ----|----|----
    id|ashley|Der eindeutige Bezeichner für den Scheitelpunkt. Wenn Sie keine ID angeben, wird automatisch eine ID generiert.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > In dieser Schnellstartanleitung wird eine nicht partitionierte Sammlung erstellt. Wenn Sie hingegen eine partitionierte Sammlung erstellen, indem Sie bei der Sammlungserstellung einen Partitionsschlüssel angeben, muss der Partitionsschlüssel jedem neuen Scheitelpunkt als Schlüssel hinzugefügt werden. 

6. Klicken Sie auf **OK**. Der Bildschirm muss möglicherweise erweitert werden, damit **OK** am unteren Bildschirmrand zu sehen ist.

7. Klicken Sie erneut auf **New Vertex** (Neuer Scheitelpunkt), und fügen Sie einen weiteren neuen Benutzer hinzu. 

8. Geben Sie als Bezeichnung *Person* ein.

9. Klicken Sie auf **Eigenschaft hinzufügen**, um die folgenden Eigenschaften hinzuzufügen:

    key|value|Notizen
    ----|----|----
    id|rakesh|Der eindeutige Bezeichner für den Scheitelpunkt. Wenn Sie keine ID angeben, wird automatisch eine ID generiert.
    gender|male| 
    school|MIT| 

10. Klicken Sie auf **OK**. 

11. Klicken Sie auf die Schaltfläche **Filter anwenden** mit dem Standardfilter `g.V()`, um alle Werte des Graphen anzuzeigen. Daraufhin werden alle Benutzer in der Liste **Ergebnisse** angezeigt. 

    Wenn Sie weitere Daten hinzufügen, können Sie Ihre Ergebnisse mithilfe von Filtern eingrenzen. Daten-Explorer verwendet standardmäßig `g.V()`, um alle Scheitelpunkte eines Graphen abzurufen. Sie können ihn in eine andere [Graphabfrage](tutorial-query-graph.md) wie z.B. `g.V().count()` ändern, um die Anzahl aller Scheitelpunkte eines Graphen im JSON-Format zurückzugeben. Wenn Sie den Filter geändert haben, ändern Sie den Filter zurück in `g.V()`, und klicken Sie auf **Filter anwenden**, um wieder alle Ergebnisse anzuzeigen.

12. Als Nächstes verbinden Sie „rakesh“ und „ashley“. Vergewissern Sie sich, dass **ashley** in der Liste **Ergebnisse** ausgewählt ist, und klicken Sie anschließend rechts unten neben **Ziele** auf ![Ziel eines Scheitelpunkts in einem Graph ändern](./media/create-graph-java/edit-pencil-button.png). Sollte die Schaltfläche nicht zu sehen sein, müssen Sie ggf. das Fenster verbreitern.

    ![Ändern des Ziels eines Scheitelpunkts in einem Graph](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. Geben Sie im Feld **Ziel** die Zeichenfolge *rakesh* und im Feld **Edge label** (Edgebezeichner) die Zeichenfolge *knows* ein, und aktivieren Sie anschließend das Kontrollkästchen.

    ![Hinzufügen einer Verbindung zwischen „ashley“ und „rakesh“ im Daten-Explorer](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. Wählen Sie nun in der Ergebnisliste den Eintrag **rakesh** aus. Wie Sie sehen, sind „ashley“ und „rakesh“ miteinander verbunden. 

    ![Zwei verbundene Scheitelpunkte im Daten-Explorer](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    Damit haben Sie den Teil des Tutorials, in dem die Ressourcen erstellt werden, abgeschlossen. Sie können weiter Scheitelpunkte zu Ihrem Graphen hinzufügen, die vorhandenen Scheitelpunkte anpassen oder die Abfragen ändern. Sehen Sie sich nun die von Azure Cosmos DB bereitgestellten Metriken an, und bereinigen Sie anschließend die Ressourcen. 

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, einen Graph mit dem Daten-Explorer erstellen und eine App ausführen. Nun können Sie komplexere Abfragen erstellen und leistungsfähige Logik zum Traversieren von Graphen mit Gremlin implementieren. 

> [!div class="nextstepaction"]
> [Query using Gremlin (Abfragen mithilfe von Gremlin)](tutorial-query-graph.md)

