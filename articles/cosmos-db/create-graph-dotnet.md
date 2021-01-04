---
title: Erstellen einer Azure Cosmos DB-.NET Framework- oder Core-Anwendung mit der Gremlin-API
description: Hier finden Sie ein .NET Framework-/Core-Codebeispiel, das Sie zum Herstellen einer Verbindung mit und zum Abfragen von Azure Cosmos DB verwenden können.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: chrande
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1953f4a21df6f550320592fbe009834a7b573887
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360529"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Schnellstart: Erstellen einer .NET Framework- oder Core-Anwendung mithilfe des Gremlin-API-Kontos für Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-Konsole](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel/Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen. 

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mithilfe des Azure-Portals ein [Gremlin-API](graph-introduction.md)-Konto, eine Datenbank und einen Graph (Container) für Azure Cosmos DB erstellen. Anschließend erstellen Sie eine Konsolenanwendung mithilfe des Open-Source-Treibers [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) und führen diese aus.  

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie Visual Studio 2019 noch nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Öffnen Sie anschließend Visual Studio und die Projektmappendatei.

5. Stellen Sie die NuGet-Pakete im Projekt wieder her. Diese sollten den Treiber „Gremlin.Net“ und das Paket „Newtonsoft.Json“ umfassen.


6. Sie können den Treiber „Gremlin.Net“ aber auch manuell mithilfe des NuGet-Paket-Managers oder des [NuGet-Befehlszeilentools](/nuget/install-nuget-client-tools) installieren: 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Datenbankressourcen im Code erstellt werden, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Aktualisieren der Verbindungszeichenfolge](#update-your-connection-string) fortfahren. 

Die folgenden Codeausschnitte stammen alle aus der Datei „Program.cs“.

* Legen Sie Ihre Verbindungsparameter auf der Grundlage des weiter oben erstellten Kontos fest: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* Die auszuführenden Gremlin-Befehle sind in einem Wörterbuch aufgeführt:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Erstellen Sie neue Verbindungsobjekte (`GremlinServer` und `GremlinClient`) mit den weiter oben angegebenen Parametern:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Führen Sie die einzelnen Gremlin-Abfragen unter Verwendung des Objekts `GremlinClient` mit einer asynchronen Aufgabe aus. Die Gremlin-Abfragen können aus dem weiter oben definierten Wörterbuch gelesen und ausgeführt werden. Rufen Sie später die Ergebnisse ab, und lesen Sie die als Wörterbuch formatierten Werte unter Verwendung der Klasse `JsonSerializer` aus dem Paket „Newtonsoft.Json“:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Graphdatenbankkonto. Auf der Registerkarte **Übersicht** werden zwei Endpunkte angezeigt: 
 
   **.NET SDK-URI** : Dieser Wert wird verwendet, wenn Sie unter Verwendung der Bibliothek „Microsoft.Azure.Graphs“ eine Verbindung mit dem Graphkonto herstellen. 

   **Gremlin-Endpunkt** : Dieser Wert wird verwendet, wenn Sie unter Verwendung der Bibliothek „Gremlin.Net“ eine Verbindung mit dem Graphkonto herstellen.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="Kopieren des Endpunkts":::

   Kopieren Sie für dieses Beispiel den Wert für **Gremlin-Endpunkt** , und löschen Sie die Portnummer am Ende. Der URI sieht dann wie folgt aus: `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`. Der Endpunktwert sollte wie folgt aussehen: `testgraphacct.gremlin.cosmosdb.azure.com`

1. Navigieren Sie als Nächstes zur Registerkarte **Schlüssel** , und kopieren Sie den Wert für **Primärschlüssel** aus dem Azure-Portal. 

1. Nachdem Sie den URI und den Primärschlüssel Ihres Kontos kopiert haben, können Sie diese Werte in einer neuen Umgebungsvariablen auf dem lokalen Computer speichern, auf dem die Anwendung ausgeführt wird. Öffnen Sie zum Festlegen der Umgebungsvariablen ein Eingabeaufforderungsfenster, und führen Sie den folgenden Befehl aus. Ersetzen Sie dabei <Your_Azure_Cosmos_account_URI> und <Your_Azure_Cosmos_account_PRIMARY_KEY> durch den URI bzw. durch den Primärschlüssel Ihres Azure Cosmos-Kontos.

   ```console
   setx Host "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Öffnen Sie die Datei *Program.cs* , und aktualisieren Sie die Variablen „database“ und „container“ mit den weiter oben erstellten Namen für die Datenbank und den Container. (Der Containername ist zugleich auch der Diagrammname.)

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Speichern Sie die Datei "Program.cs". 

Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Azure Cosmos DB aktualisiert. 

## <a name="run-the-console-app"></a>Ausführen der Konsolenanwendung

Drücken Sie STRG+F5, um die Anwendung auszuführen. Die Anwendung gibt sowohl die Gremlin-Abfragebefehle als auch die Ergebnisse in der Konsole aus.

   Im Konsolenfenster werden die Scheitelpunkte und Ränder angezeigt, die dem Graph hinzugefügt werden. Drücken Sie nach Abschluss des Skripts die EINGABETASTE, um das Konsolenfenster zu schließen.

## <a name="browse-using-the-data-explorer"></a>Durchsuchen mit dem Daten-Explorer

Sie können nun zum Daten-Explorer im Azure-Portal zurückkehren und die neuen Graph-Daten durchsuchen und abfragen.

1. Im Daten-Explorer wird die neue Datenbank im Diagrammbereich angezeigt. Erweitern Sie den Datenbank- und den Containerknoten, und klicken Sie anschließend auf **Graph**.

2. Klicken Sie auf die Schaltfläche **Filter anwenden** , um die Standardabfrage zum Anzeigen aller Scheitelpunkte im Diagramm zu verwenden. Die von der Beispiel-App generierten Daten werden im Graphen-Bereich angezeigt.

    Sie können die Diagrammansicht vergrößern und verkleinern, den Anzeigebereich des Diagramms erweitern, zusätzliche Scheitelpunkte hinzufügen und Scheitelpunkte auf der Anzeigeoberfläche verschieben.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="Anzeigen des Diagramms im Daten-Explorer im Azure-Portal":::

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, einen Graph mit dem Daten-Explorer erstellen und eine App ausführen. Nun können Sie komplexere Abfragen erstellen und leistungsfähige Logik zum Traversieren von Graphen mit Gremlin implementieren. 

> [!div class="nextstepaction"]
> [Query using Gremlin (Abfragen mithilfe von Gremlin)](tutorial-query-graph.md)