---
title: 'Schnellstart: Gremlin-API mit Python: Azure Cosmos DB'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Gremlin-API von Azure Cosmos DB eine Konsolenanwendung mit dem Azure-Portal und Python erstellen.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: chrande
ms.custom: devx-track-python
ms.openlocfilehash: 91b04b05b810b2b0fc221943a73e81c49f7119dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972474"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Schnellstart: Erstellen einer Graphdatenbank in Azure Cosmos DB mit Python und dem Azure-Portal
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-Konsole](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

In dieser Schnellstartanleitung erstellen und verwalten Sie ein Azure Cosmos DB-Gremlin-API-Konto (Graph) im Azure-Portal und fügen Daten mithilfe einer über GitHub geklonten Python-App hinzu. Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement.
- [Python 3.5+](https://www.python.org/downloads/) einschließlich [pip](https://pip.pypa.io/en/stable/installing/)-Paketinstallationsprogramm
- [Python-Treiber für Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python)
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> Für die in dieser Schnellstartanleitung beschriebenen Schritte benötigen Sie ein Graphdatenbankkonto, das nach dem 20. Dezember 2017 erstellt wurde. Bestehende Konten unterstützen Python, nachdem diese zur Version für die allgemeine Verfügbarkeit migriert wurden.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Die Codeausschnitte stammen alle aus der Datei *connect.py* im Ordner *C:\git-samples\azure-cosmos-db-graph-python-getting-started\\* . Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-information) fortfahren. 

* Der Gremlin-Client (`client`) wird in *connect.py* in Zeile 104 initialisiert:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* Am Anfang der Datei *connect.py* wird eine Reihe von Schritten für Gremlin deklariert. Diese werden dann mit der Methode `client.submitAsync()` ausgeführt:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Aktualisieren der Verbindungsinformationen

Wechseln Sie nun zurück zum Azure-Portal, um die Verbindungsinformationen abzurufen und in die App zu kopieren. Durch diese Einstellungen kann Ihre App mit Ihrer gehosteten Datenbank kommunizieren.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Azure Cosmos DB-Konto die Option **Schlüssel** aus. 

    Kopieren Sie den ersten Teil des URI-Werts.

    :::image type="content" source="./media/create-graph-python/keys.png" alt-text="Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal auf der Seite „Schlüssel“":::

2. Öffnen Sie die Datei *connect.py*, und ersetzen Sie `<YOUR_ENDPOINT>` in Zeile 104 durch den URI-Wert:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    Der URI-Teil des Clientobjekts sollte nun ungefähr wie im folgenden Code aussehen:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Ändern Sie den zweiten Parameter des `client`-Objekts, um die Zeichenfolgen `<YOUR_DATABASE>` und `<YOUR_COLLECTION_OR_GRAPH>` zu ersetzen. Wenn Sie die vorgeschlagenen Werte verwendet haben, sollte der Parameter wie im folgenden Code aussehen:

    `username="/dbs/sample-database/colls/sample-graph"`

    Das gesamte `client`-Objekt sollte nun wie im folgenden Code aussehen:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. Kopieren Sie auf der Seite **Schlüssel** den PRIMÄRSCHLÜSSEL mithilfe der Kopierschaltfläche, und fügen Sie ihn im Parameter `password=<YOUR_PASSWORD>` für `<YOUR_PASSWORD>` ein.

    Das gesamte `client`-Objektdefinition sollte nun wie im folgenden Code aussehen:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Speichern Sie die Datei *connect.py*.

## <a name="run-the-console-app"></a>Ausführen der Konsolenanwendung

1. Wechseln Sie im Terminalfenster von Git mithilfe von `cd` zum Ordner „azure-cosmos-db-graph-python-getting-started“.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die erforderlichen Python-Pakete zu installieren.

   ```
   pip install -r requirements.txt
   ```

3. Verwenden Sie im Git-Terminalfenster den folgenden Befehl, um die Python-Anwendung zu starten.
    
    ```
    python connect.py
    ```

    Im Terminalfenster werden die Scheitelpunkte und Kanten angezeigt, die dem Graph hinzugefügt werden. 
    
    Wenn Timeoutfehler auftreten, überprüfen Sie, ob Sie die Verbindungsinformationen unter [Aktualisieren der Verbindungsinformationen](#update-your-connection-information) richtig aktualisiert haben, und versuchen Sie auch, den letzten Befehl erneut auszuführen. 
    
    Drücken Sie nach Abschluss des Programms die EINGABETASTE, und wechseln Sie dann in Ihrem Internetbrowser wieder zum Azure-Portal.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Überprüfen und Hinzufügen von Beispieldaten

Nach dem Einfügen der Scheitelpunkte und Kanten können Sie wieder zum Daten-Explorer zurückkehren, um sich die dem Graph hinzugefügten Scheitelpunkte anzusehen und weitere Datenpunkte hinzuzufügen.

1. Wählen Sie im Azure-Portal in Ihrem Azure Cosmos DB-Konto **Daten-Explorer** aus, erweitern Sie den Eintrag **sample-graph**, und wählen Sie **Graph** und anschließend **Filter anwenden** aus. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png" alt-text="Screenshot: In der API ausgewählter Graph mit der Option „Filter anwenden“":::

2. Beachten Sie in der Liste **Ergebnisse** drei neue Benutzer, die dem Graph hinzugefügt wurden. Sie können die Scheitelpunkte bewegen, indem Sie sie ziehen und loslassen, zoomen, indem Sie das Mausrad verwenden, und die Größe des Graphen verändern, indem Sie auf den Doppelpfeil klicken. 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png" alt-text="Neue Scheitelpunkte im Graph im Daten-Explorer im Azure-Portal":::

3. Fügen Sie nun einige neue Benutzer hinzu. Wählen Sie die Schaltfläche **Neuer Vertex** aus, um dem Graph Daten hinzuzufügen.

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Screenshot: Bereich „New Vertex“ (Neuer Scheitelpunkt), in dem Sie Werte eingeben können":::

4. Geben Sie als Bezeichnung *Person* ein.

5. Wählen Sie **Eigenschaft hinzufügen** aus, um die folgenden Eigenschaften hinzuzufügen. Beachten Sie, dass Sie für jede Person in Ihrem Graph eindeutige Eigenschaften erstellen können. Nur der id-Schlüssel ist erforderlich.

    Schlüssel|value|Notizen
    ----|----|----
    pk|/pk| 
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
    pk|/pk| 
    id|rakesh|Der eindeutige Bezeichner für den Scheitelpunkt. Wenn Sie keine ID angeben, wird automatisch eine ID generiert.
    gender|male| 
    school|MIT| 

10. Klicken Sie auf **OK**. 

11. Wählen Sie die Schaltfläche **Filter anwenden** mit dem Standardfilter `g.V()` aus, um alle Werte des Graphen anzuzeigen. Daraufhin werden alle Benutzer in der Liste **Ergebnisse** angezeigt. 

    Wenn Sie weitere Daten hinzufügen, können Sie Ihre Ergebnisse mithilfe von Filtern eingrenzen. Daten-Explorer verwendet standardmäßig `g.V()`, um alle Scheitelpunkte eines Graphen abzurufen. Sie können ihn in eine andere [Graphabfrage](tutorial-query-graph.md) wie z.B. `g.V().count()` ändern, um die Anzahl aller Scheitelpunkte eines Graphen im JSON-Format zurückzugeben. Wenn Sie den Filter geändert haben, ändern Sie den Filter zurück in `g.V()`, und wählen Sie **Filter anwenden** aus, um wieder alle Ergebnisse anzuzeigen.

12. Als Nächstes verbinden wir „rakesh“ und „ashley“. Vergewissern Sie sich, dass **ashley** in der Liste **Ergebnisse** ausgewählt ist, und wählen Sie anschließend rechts unten neben **Ziele** die Bearbeitungsschaltfläche aus. Möglicherweise müssen Sie Ihr Fenster verbreitern, damit der Bereich **Eigenschaften** zu sehen ist.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Ändern des Ziels eines Scheitelpunkts in einem Graph":::

13. Geben Sie im Feld **Ziel** die Zeichenfolge *rakesh* und im Feld **Kantenbezeichnung** die Zeichenfolge *knows* ein, und wählen Sie anschließend das Häkchen aus.

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png" alt-text="Hinzufügen einer Verbindung zwischen „ashley“ und „rakesh“ im Daten-Explorer":::

14. Wählen Sie nun in der Ergebnisliste den Eintrag **rakesh** aus. Wie Sie sehen, sind „ashley“ und „rakesh“ miteinander verbunden. 

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer.png" alt-text="Zwei verbundene Scheitelpunkte im Daten-Explorer":::

Damit haben Sie den Teil des Tutorials, in dem die Ressourcen erstellt werden, abgeschlossen. Sie können weiter Scheitelpunkte zu Ihrem Graphen hinzufügen, die vorhandenen Scheitelpunkte anpassen oder die Abfragen ändern. Sehen Sie sich nun die von Azure Cosmos DB bereitgestellten Metriken an, und bereinigen Sie anschließend die Ressourcen. 

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, einen Graph mit dem Daten-Explorer erstellen und eine Python-App ausführen, um dem Graph Daten hinzuzufügen. Nun können Sie komplexere Abfragen erstellen und leistungsfähige Logik zum Traversieren von Graphen mit Gremlin implementieren. 

> [!div class="nextstepaction"]
> [Query using Gremlin (Abfragen mithilfe von Gremlin)](tutorial-query-graph.md)

