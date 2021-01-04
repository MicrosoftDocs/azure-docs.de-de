---
title: 'Abfragen mit der Azure Cosmos DB Gremlin-API mithilfe der TinkerPop Gremlin-Konsole: Lernprogramm'
description: Schnellstart von Azure Cosmos DB zum Erstellen von Scheitelpunkten, Kanten und Abfragen mit der Gremlin-API von Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: quickstart
ms.date: 07/10/2020
ms.author: chrande
ms.openlocfilehash: 20cb4cea52317022aea8a5b9e4b8280f8b88ff85
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361510"
---
# <a name="quickstart-create-query-and-traverse-an-azure-cosmos-db-graph-database-using-the-gremlin-console"></a>Schnellstart: Erstellen, Abfragen und Durchlaufen einer Azure Cosmos DB-Graphdatenbank mithilfe der Gremlin-Konsole
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

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals ein [Gremlin-API](graph-introduction.md)-Konto, eine Datenbank und einen Graph (Container) in Azure Cosmos DB erstellen und anschließend über die [Gremlin-Konsole](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) von [Apache TinkerPop](https://tinkerpop.apache.org) mit Daten der Gremlin-API arbeiten. In diesem Tutorial erstellen Sie Scheitelpunkte und Kanten und fragen diese ab. Außerdem führen Sie eine Aktualisierung einer Scheitelpunkteigenschaft durch sowie eine Abfrage von Scheitelpunkten, eine Übertragung eines Graphen und das Löschen eines Scheitelpunkts.

:::image type="content" source="./media/create-graph-gremlin-console/gremlin-console.png" alt-text="Azure Cosmos DB in der Gremlin-Konsole von Apache":::

Die Gremlin-Konsole basiert auf Groovy bzw. Java und kann unter Linux, Mac und Windows ausgeführt werden. Sie können die Konsole von der [Seite „Apache TinkerPop“](https://tinkerpop.apache.org/downloads.html) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen eines Azure Cosmos DB-Kontos für diesen Schnellstart benötigen Sie ein Azure-Abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Außerdem müssen Sie die [Gremlin-Konsole](https://tinkerpop.apache.org/downloads.html) installieren. Die **empfohlene Version ist v3.4.3** oder früher. (Zur Verwendung der Gremlin-Konsole unter Windows müssen Sie [Java Runtime](https://www.oracle.com/technetwork/java/javase/overview/index.html) installieren).

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Hinzufügen eines Graphs

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="connect-to-your-app-servicegraph"></a><a id="ConnectAppService"></a>Herstellen einer Verbindung mit Ihrem App-Dienst/Graph

1. Erstellen bzw. ändern Sie die Konfigurationsdatei „remote-secure.yaml“ im Verzeichnis `apache-tinkerpop-gremlin-console-3.2.5/conf`, bevor Sie die Gremlin-Konsole starten.
2. Füllen Sie die Konfigurationen *Host* , *Port* , *Benutzername* , *Kennwort* , *connectionPool* und *Serialisierungsmodul* aus, wie in der folgenden Tabelle definiert:

    Einstellung|Vorgeschlagener Wert|BESCHREIBUNG
    ---|---|---
    hosts|[ *account-name*. **gremlin**.cosmos.azure.com]|Der folgende Screenshot zeigt dies. Dies ist der Wert für den **Gremlin-URI** auf der Seite „Übersicht“ des Azure-Portals (in eckigen Klammern und ohne den Zusatz „:443/“). Hinweis: Achten Sie darauf, den Gremlin-Wert und **nicht** den URI zu verwenden, der auf [ *account-name*.documents.azure.com] endet. Andernfalls wird beim späteren Ausführen von Gremlin-Abfragen wahrscheinlich eine Ausnahme „Host did not respond in a timely fashion“ (Host hat nicht rechtszeitig reagiert) ausgelöst. 
    port|443|Legen Sie den Wert 443 fest.
    username|*Ihr Benutzername*|Die Ressource im Format `/dbs/<db>/colls/<coll>`, wobei `<db>` der Datenbankname und `<coll>` der Sammlungsname ist.
    password|*Ihr Primärschlüssel*| Siehe zweiten Screenshot unten. Dies ist Ihr Primärschlüssel, den Sie von der Seite „Schlüssel“ des Azure-Portals im Feld „Primärschlüssel“ abrufen können. Verwenden Sie die Schaltfläche „Kopieren“ links vom Feld, um den Wert zu kopieren.
    connectionPool|{enableSsl: true}|Ihre Verbindungspooleinstellung für TLS.
    serializer|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV2d0,<br> config: { serializeResultToString: true }}|Legen Sie diesen Wert fest, und löschen Sie alle `\n`-Zeilenumbrüche, wenn Sie den Wert einfügen.

   Kopieren Sie zur Angabe des Werts „Hosts“ den **Gremlin-URI** auf der Seite **Übersicht** :

   :::image type="content" source="./media/create-graph-gremlin-console/gremlin-uri.png" alt-text="Anzeigen und Kopieren des den Gremlin-URI-Werts auf der Seite „Übersicht“ im Azure-Portal":::

   Kopieren Sie für den Kennwortwert den **Primärschlüssel** aus der Seite **Schlüssel** :

   :::image type="content" source="./media/create-graph-gremlin-console/keys.png" alt-text="Anzeigen und Kopieren Ihres Primärschlüssels im Azure-Portal, Seite „Schlüssel“":::

   Die Datei „remote-secure.yaml“ sollte wie folgt aussehen:

   ```yaml
   hosts: [your_database_server.gremlin.cosmos.azure.com] 
   port: 443
   username: /dbs/your_database_account/colls/your_collection
   password: your_primary_key
   connectionPool: {
     enableSsl: true
   }
   serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV2d0, config: { serializeResultToString: true }}
   ```

   Setzen Sie den Wert des hosts-Parameters in eckige Klammern: []. 

1. Führen Sie in Ihrem Terminal `bin/gremlin.bat` oder `bin/gremlin.sh` aus, um die [Gremlin-Konsole](https://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/) zu starten.

1. Führen Sie in Ihrem Terminal `:remote connect tinkerpop.server conf/remote-secure.yaml` aus, um eine Verbindung mit Ihrer App Service-Instanz herzustellen.

    > [!TIP]
    > Sollte die Fehlermeldung `No appenders could be found for logger` angezeigt werden, vergewissern Sie sich, dass der Serialisierungsmodulwert in der Datei „remote-secure.yaml“ wie in Schritt 2 beschrieben aktualisiert wurde. Wenn Ihre Konfiguration korrekt ist, kann diese Warnung sicher ignoriert werden, da Sie sich nicht auf die Verwendung der Konsole auswirkt. 

1. Führen Sie als Nächstes `:remote console` aus, um alle Konsolenbefehle an den Remoteserver umzuleiten.

   > [!NOTE]
   > Wenn Sie den `:remote console`-Befehl nicht ausführen, aber alle Konsolenbefehle an den Remoteserver umleiten möchten, sollten Sie dem Befehl ein `:>` voranstellen. So sollten Sie den Befehl beispielsweise als `:> g.V().count()` ausführen. Dieses Präfix ist ein Teil des Befehls und wichtig bei der Verwendung der Gremlin-Konsole mit Azure Cosmos DB. Wenn Sie das Präfix weglassen, wird die Konsole angewiesen, den Befehl lokal auszuführen. Dies wird häufig für einen In-Memory-Graphen durchgeführt. Mit diesem Präfix – `:>` – weisen Sie die Konsole an, einen Remotebefehl auszuführen – in diesem Fall für Azure Cosmos DB (entweder der localhost-Emulator oder eine Azure-Instanz).

Prima. Damit ist die Einrichtung abgeschlossen. Jetzt können Sie mit dem Ausführen von Konsolenbefehlen beginnen.

Probieren Sie einen einfachen count()-Befehl aus. Geben Sie an der Eingabeaufforderung in der Konsole Folgendes ein:

```java
g.V().count()
```

## <a name="create-vertices-and-edges"></a>Erstellen von Scheitelpunkten und Kanten

Fügen Sie zunächst fünf Scheitelpunkte für Personen hinzu: *Thomas* , *Mary Kay* , *Robin* , *Ben* und *Jack*.

Eingabe (Thomas):

```java
g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1).property('pk', 'pk')
```

Ausgabe:

```bash
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```

Eingabe (Mary Kay):

```java
g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2).property('pk', 'pk')

```

Ausgabe:

```bash
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Eingabe (Robin):

```java
g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3).property('pk', 'pk')
```

Ausgabe:

```bash
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Eingabe (Ben):

```java
g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4).property('pk', 'pk')

```

Ausgabe:

```bash
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Eingabe (Jack):

```java
g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5).property('pk', 'pk')
```

Ausgabe:

```bash
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


Fügen Sie als Nächstes Kanten für die Personenbeziehungen hinzu.

Eingabe (Thomas -> Mary Kay):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Ausgabe:

```bash
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Eingabe (Thomas -> Robin):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Ausgabe:

```bash
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Eingabe (Robin -> Ben):

```java
g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Ausgabe:

```bash
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Aktualisieren eines Scheitelpunkts

Aktualisieren Sie den Scheitelpunkt *Thomas* mit dem neuen Alter *45*.

Eingabe:
```java
g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Ausgabe:

```bash
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Abfragen des Graphs

Jetzt können Sie einige Abfragen mit dem Graph durchführen.

Führen Sie zunächst eine Abfrage mit einem Filter durch, um nur Personen zurückzugeben, die älter als 40 sind.

Eingabe (Filterabfrage):

```java
g.V().hasLabel('person').has('age', gt(40))
```

Ausgabe:

```bash
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Projizieren Sie als Nächstes die Vornamen aller Personen, die älter als 40 sind.

Eingabe (Filter + Abfrage zur Projektion):

```java
g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Ausgabe:

```bash
==>Thomas
```

## <a name="traverse-your-graph"></a>Traversieren des Graphs

Traversieren Sie den Graph, um alle Freunde von Thomas zurückzugeben.

Eingabe (Freunde von Thomas):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Ausgabe: 

```bash
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Gehen Sie dann zur nächsten Scheitelpunktebene über. Traversieren Sie den Graph, um alle Freunde von Freunden von Thomas zurückzugeben.

Eingabe (Freunde von Freunden von Thomas):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Ausgabe:

```bash
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Löschen eines Scheitelpunkts

Löschen Sie jetzt einen Scheitelpunkt aus der Graphdatenbank.

Eingabe (Löschen des Scheitelpunkts „Jack“):

```java
g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Bereinigen des Graphs

Löschen Sie zum Schluss alle Scheitelpunkte und Kanten aus der Datenbank.

Eingabe:

```java
g.E().drop()
g.V().drop()
```

Glückwunsch! Sie haben das Tutorial zu Azure Cosmos DB: Gremlin-API erfolgreich abgeschlossen.

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie ein Konto für Azure Cosmos DB und einen Graph mit dem Daten-Explorer erstellen können. Außerdem wissen Sie nun, wie Sie Scheitelpunkte und Kanten erstellen können, und wie Sie den Graph mit der Gremlin-Konsole traversieren können. Nun können Sie komplexere Abfragen erstellen und leistungsfähige Logik zum Traversieren von Graphen mit Gremlin implementieren. 

> [!div class="nextstepaction"]
> [Query using Gremlin (Abfragen mithilfe von Gremlin)](tutorial-query-graph.md)
