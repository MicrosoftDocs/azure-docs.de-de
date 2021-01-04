---
title: Durchsuchen von Azure Cosmos DB-Daten
titleSuffix: Azure Cognitive Search
description: Importieren Sie Daten aus Azure Cosmos DB in einen durchsuchbaren Index in Azure Cognitive Search. Indexer automatisieren die Datenerfassung für ausgewählte Datenquellen wie Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: be7c6ec9dbc577143e6c7219580f42c876f536bc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499967"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Indizieren von Cosmos DB-Daten mithilfe eines Indexers in der kognitiven Azure-Suche 

> [!IMPORTANT] 
> Die SQL-API ist allgemein verfügbar.
> Die Unterstützung von MongoDB-API, Gremlin-API und Cassandra-API befindet sich aktuell in der Public Preview-Phase. Die Vorschaufunktion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Füllen Sie [dieses Formular](https://aka.ms/azure-cognitive-search/indexer-preview) aus, wenn Sie Zugriff auf die Vorschauversionen anfordern möchten. 
> Diese Features werden von den [Vorschauversionen der REST-API](search-api-preview.md) bereitgestellt. Die Portalunterstützung ist momentan eingeschränkt, und das .NET SDK wird nicht unterstützt.

> [!WARNING]
> Nur Cosmos DB-Sammlungen, deren [Indizierungsrichtlinie](../cosmos-db/index-policy.md) auf [Konsistent](../cosmos-db/index-policy.md#indexing-mode) festgelegt ist, werden von Azure Cognitive Search unterstützt. Das Indizieren von Sammlungen mit der Indizierungsrichtlinie „Verzögert“ wird nicht empfohlen, da es zu fehlenden Daten führen kann. Sammlungen mit deaktivierter Indizierung werden nicht unterstützt.

In diesem Artikel erfahren Sie, wie Sie einen Azure Cosmos DB-[Indexer](search-indexer-overview.md) zum Extrahieren von Inhalten konfigurieren und dafür sorgen, dass er in der kognitiven Azure-Suche durchsucht werden kann. Mit diesem Workflow erstellen Sie einen Index der kognitiven Azure-Suche und laden ihn mit vorhandenem aus Azure Cosmos DB extrahiertem Text. 

Da die Begrifflichkeiten etwas verwirrend sind, soll hier darauf hingewiesen werden, dass die [Indizierung in Azure Cosmos DB](../cosmos-db/index-overview.md) und die [Indizierung in der kognitiven Azure-Suche](search-what-is-an-index.md) zwei verschiedene Vorgänge sind, die jedem Dienst eigen sind. Wenn Sie mit der Indizierung in der kognitiven Azure-Suche beginnen, muss Ihre Azure Cosmos DB-Datenbank bereits vorhanden sein und Daten enthalten.

Mit dem Cosmos DB-Indexer in Azure Cognitive Search können Sie [Azure Cosmos DB-Elemente](../cosmos-db/account-databases-containers-items.md#azure-cosmos-items) durchforsten, auf die über verschiedene Protokolle zugegriffen wird. 

+ Für die allgemein verfügbare [SQL-API](../cosmos-db/sql-query-getting-started.md) können Sie das [Portal](#cosmos-indexer-portal), die [REST-API](/rest/api/searchservice/indexer-operations) oder das [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) verwenden, um die Datenquelle und den Indexer zu erstellen.

+ Für die [MongoDB-API (Vorschauversion)](../cosmos-db/mongodb-introduction.md) können Sie entweder das [Portal](#cosmos-indexer-portal) oder die [REST-API-Version 2020-06-30-Preview](search-api-preview.md) verwenden, um die Datenquelle und den Indexer zu erstellen.

+ Für die [Cassandra-API (Vorschauversion)](../cosmos-db/cassandra-introduction.md) und die [Gremlin-API (Vorschauversion)](../cosmos-db/graph-introduction.md) können Sie nur die [REST-API-Version 2020-06-30-Preview](search-api-preview.md) verwenden, um die Datenquelle und den Indexer zu erstellen.


> [!Note]
> Bei User Voice können Sie für die [Tabellen-API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) abstimmen, wenn Sie möchten, dass sie in Azure Cognitive Search unterstützt wird.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Verwenden des Portals

> [!Note]
> Das Portal unterstützt derzeit die SQL-API und die MongoDB-API (Vorschauversion).

Die einfachste Methode zum Indizieren von Azure Cosmos DB-Elementen ist die Verwendung eines Assistenten im [Azure-Portal](https://portal.azure.com/). Der [**Datenimport**](search-import-data-portal.md)-Assistent in der kognitiven Azure-Suche kann durch die Entnahme von Stichproben und das Lesen von Metadaten im Container einen Standardindex erstellen, Quellfelder Zielindexfeldern zuordnen und den Index in einem einzigen Vorgang laden. Je nach Größe und Komplexität der Quelldaten können Sie auch innerhalb von Minuten einen funktionsfähigen Volltextsuchindex erstellen.

Es wird empfohlen, die gleiche Region oder den gleichen Speicherort für Azure Cognitive Search und Azure Cosmos DB zu verwenden, um geringere Latenzzeiten zu erreichen und Bandbreitengebühren zu vermeiden.

### <a name="1---prepare-source-data"></a>1\. Vorbereiten von Quelldaten

Sie benötigen ein Cosmos DB-Konto, eine Azure Cosmos DB-Datenbank, die der SQL-API, der MongoDB-API (Vorschauversion) oder der Gremlin-API (Vorschauversion) zugeordnet ist, sowie Inhalte in der Datenbank.

Stellen Sie sicher, dass Ihre Cosmos DB-Datenbank Daten enthält. Der [Datenimport-Assistent](search-import-data-portal.md) liest Metadaten und entnimmt Stichproben, um ein Indexschema abzuleiten. Darüber hinaus lädt er auch Daten aus Cosmos DB. Wenn die Daten nicht vorhanden sind, wird der Assistent mit der folgenden Fehlermeldung beendet: „Fehler bei der Ermittlung des Indexschemas der Datenquelle: Could not build a prototype index because datasource 'emptycollection' returned no data“ (Prototyp-Index konnte nicht erstellt werden, da von der Datenquelle „emptycollection“ keine Daten zurückgegeben wurden).

### <a name="2---start-import-data-wizard"></a>2\. Starten des Datenimport-Assistenten

Sie können den Assistenten auf der Seite des Azure Cognitive Search-Diensts über die Befehlsleiste starten. (Weitere Informationen finden Sie [hier](search-import-data-portal.md).) Falls Sie eine Verbindung mit der Cosmos DB-SQL-API herstellen, können Sie alternativ im Abschnitt **Einstellungen** (im linken Navigationsbereich Ihres Cosmos DB-Kontos) auf **Azure Cognitive Search hinzufügen** klicken.

   ![Befehl „Daten importieren“ im Portal](./media/search-import-data-portal/import-data-cmd2.png "Starten des Datenimport-Assistenten")

### <a name="3---set-the-data-source"></a>3\. Einrichten der Datenquelle

Auf der Seite **Datenquelle** muss die Quelle **Cosmos DB** lauten und folgende Angaben enthalten:

+ Der **Name** ist der Name des Datenquellenobjekts. Nachdem Sie es erstellt haben, können Sie es für andere Workloads verwenden.

+ Das **Cosmos DB-Konto** muss eines der folgenden Formate aufweisen:
    1. Die primäre oder sekundäre Verbindungszeichenfolge aus Cosmos DB und folgendes Format: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;`.
        + Verwenden Sie für **MongoDB-Sammlungen** in den Versionen 3.2 und 3.6 das folgende Format für das Cosmos DB-Konto im Azure-Portal: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`.
        + Registrieren Sie sich bei Verwendung von **Gremlin-Graphen und Cassandra-Tabellen** für die [geschlossene Indexervorschau](https://aka.ms/azure-cognitive-search/indexer-preview), um Zugriff auf die Vorschauversion sowie Informationen zur Formatierung der Anmeldeinformationen zu erhalten.
    1.  Eine Verbindungszeichenfolge für eine verwaltete Identität, die keinen Kontoschlüssel enthält, im folgenden Format: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)`. Um dieses Verbindungszeichenfolgenformat zu verwenden, befolgen Sie die Anweisungen unter [Einrichten einer Indexerverbindung mit einer Cosmos DB-Datenbank mithilfe einer verwalteten Identität](search-howto-managed-identities-cosmos-db.md).

+ Die **Datenbank** ist eine im Konto vorhandene Datenbank. 

+ Eine **Sammlung** ist ein Container mit Dokumenten. Dokumente müssen vorhanden sein, damit ein Import durchgeführt werden kann. 

+ Die **Abfrage** kann leer sein, wenn alle Dokumente gewünscht werden. Andernfalls können Sie eine Abfrage eingeben, mit der eine Teilmenge von Dokumenten ausgewählt wird. **Abfrage** ist nur für die SQL-API verfügbar.

   ![Cosmos DB-Datenquellendefinition](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB-Datenquellendefinition")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>Schritt 4: Überspringen der Seite „Inhalte anreichern“ im Assistenten

Das Hinzufügen kognitiver Qualifikationen (für Anreicherungen) ist keine Importanforderung. Wenn Sie der Indizierungspipeline keine [KI-Anreicherung hinzufügen](cognitive-search-concept-intro.md) möchten, können Sie diesen Schritt überspringen.

Klicken Sie auf die blauen Schaltflächen ganz unten auf der Seite (für „Weiter“ und „Überspringen“), um diesen Schritt zu überspringen.

### <a name="5---set-index-attributes"></a>5\. Festlegen von Indexattributen

Auf der **Indexseite** sollte eine Liste von Feldern mit einem Datentyp sowie mehrere Kontrollkästchen zum Festlegen von Indexattributen aufgeführt sein. Der Assistent kann basierend auf Metadaten und durch Sampling der Quelldaten eine Felderliste erstellen. 

Sie können durch Klicken auf das Kontrollkästchen am Kopf einer Attributspalte mehrere Attribute gleichzeitig auswählen. Wählen Sie **Abrufbar** und **Durchsuchbar** für jedes Feld aus, das an eine Client-App zurückgegeben werden sollte und der Volltextsuche unterliegt. Sie werden feststellen, dass Ganzzahlen nicht mit der Volltext- oder Fuzzysuche durchsuchbar sind (Zahlen werden wörtlich ausgewertet und eignen sich häufig in Filtern).

Weitere Informationen finden Sie in den Beschreibungen der [Indexattribute](/rest/api/searchservice/create-index#bkmk_indexAttrib) und [Sprachanalysetools](/rest/api/searchservice/language-support). 

Nehmen Sie sich einen Moment Zeit, um Ihre Auswahl zu überprüfen. Wenn Sie den Assistenten ausführen, werden physische Datenstrukturen erstellt, und Sie können diese Felder nicht bearbeiten, ohne alle Objekte zu löschen und neu zu erstellen.

   ![Cosmos DB-Indexdefinition](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB-Indexdefinition")

### <a name="6---create-indexer"></a>6: Erstellen des Indexers

Wenn alle Angaben gemacht wurden, erstellt der Assistent drei unterschiedliche Objekte in Ihrem Suchdienst. Ein Datenquellenobjekt und ein Indexobjekt werden als benannte Ressourcen in Ihrem Dienst der kognitiven Azure-Suche gespeichert. Mit dem letzten Schritt wird ein Indexerobjekt erstellt. Wenn der Indexer benannt wird, kann er als eigenständige Ressource existieren. Diese können Sie unabhängig vom Index und Datenquellenobjekt, die im selben Durchlauf des Assistenten erstellt wurden, planen und verwalten.

Falls Sie noch nicht mit Indexern vertraut sind: Ein *Indexer* ist eine Ressource in der kognitiven Azure-Suche, die eine externe Datenquelle nach durchsuchbarem Inhalt durchforstet. Die Ausgabe des **Datenimport**-Assistenten ist ein Indexer, der die Cosmos DB-Datenquelle durchforstet, durchsuchbaren Inhalt extrahiert und diesen in einen Index in der kognitiven Azure-Suche importiert.

Der folgende Screenshot zeigt die Konfiguration des Standardindexers. Sie können zu **Einmal** wechseln, wenn Sie den Indexer einmal ausführen möchten. Klicken Sie auf **Senden**, um den Assistenten auszuführen und alle Objekte zu erstellen. Die Indizierung wird sofort durchgeführt.

   ![Cosmos DB-Indexerdefinition](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB-Indexerdefinition")

Sie können den Datenimport auf den Portalseiten überwachen. Fortschrittsbenachrichtigungen geben den Indizierungsstatus an und wie viele Dokumente hochgeladen werden. 

Wenn die Indizierung abgeschlossen ist, können Sie den [Such-Explorer](search-explorer.md) zum Abfragen Ihres Indexes verwenden.

> [!NOTE]
> Wenn nicht die Daten angezeigt werden, die Sie erwarten, müssen Sie vielleicht weitere Attribute für weitere Felder festlegen. Löschen Sie den Index und Indexer, die Sie gerade erstellt haben, und führen Sie die Schritte des Assistenten erneut aus, wobei Sie Ihre Auswahl für Indexattribute in Schritt 5 ändern. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Verwenden von REST-APIs

Sie können die REST-API für die Indizierung von Azure Cosmos DB-Daten in einem dreiteiligen Workflow verwenden, der für alle Indexer in der kognitiven Azure-Suche gleich ist: Erstellen einer Datenquelle, eines Index und eines Indexers. Die Datenextraktion aus Cosmos DB erfolgt, wenn Sie die Anforderung für die Indexererstellung übermitteln. Nachdem diese Anforderung abgeschlossen ist, verfügen Sie über einen Index, der abgefragt werden kann. 

> [!NOTE]
> Wenn Sie Daten per Cosmos DB-Gremlin-API oder Cosmos DB-Cassandra-API indizieren möchten, müssen Sie zunächst [dieses Formular](https://aka.ms/azure-cognitive-search/indexer-preview) ausfüllen, um Zugriff auf die geschlossene Vorschau anzufordern. Nachdem Ihre Anforderung bearbeitet wurde, erhalten Sie Anweisungen für die Erstellung der Datenquelle unter Verwendung der [REST-API-Version 2020-06-30-Preview](search-api-preview.md).

Wie bereits weiter oben in diesem Artikel erwähnt, sind [Azure Cosmos DB-Indizierung](../cosmos-db/index-overview.md) und [Azure Cognitive Search-Indizierung](search-what-is-an-index.md) zwei unterschiedliche Vorgänge. Bei der Cosmos DB-Indizierung werden standardmäßig alle Dokumente automatisch indiziert (außer bei Verwendung der Cassandra-API). Wenn Sie die automatische Indizierung deaktivieren, kann auf die Dokumente nur über ihre Self-Links oder über Abfragen mit der entsprechenden Dokument-ID zugegriffen werden. Bei der Azure Cognitive Search-Indizierung muss die automatische Cosmos DB-Indizierung für die Sammlung aktiviert sein, die von Azure Cognitive Search indiziert wird. Wenn Sie sich für die Vorschauversion des Indexers der Cosmos DB-Cassandra-API registrieren, erhalten Sie eine Einrichtungsanleitung für die Cosmos DB-Indizierung.

> [!WARNING]
> Azure Cosmos DB ist die nächste Generation von DocumentDB. Zuvor konnten Sie mit der API-Version **2017-11-11** die `documentdb`-Syntax verwenden. Das bedeutete, dass Sie den Datenquellentyp als `cosmosdb` oder `documentdb` angeben konnten. Ab API-Version **2019-05-06** wird mit den APIs der kognitiven Azure-Suche sowie im Portal nur die `cosmosdb`-Syntax wie in diesem Artikel beschrieben unterstützt. Das bedeutet, dass der Datenquellentyp `cosmosdb` sein muss, wenn Sie eine Verbindung mit einem Cosmos DB-Endpunkt herstellen möchten.

### <a name="1---assemble-inputs-for-the-request"></a>1 – Zusammenstellen der Eingaben für die Anforderung

Für jede Anforderung müssen Sie den Dienstnamen und den Administratorschlüssel für die kognitive Azure-Suche (im POST-Header) sowie den Namen des Speicherkontos und den Schlüssel für Blob Storage angeben. Sie können [Postman oder Visual Studio Code](search-get-started-rest.md) zum Senden von HTTP-Anforderungen an Azure Cognitive Search verwenden.

Kopieren Sie die folgenden vier Werten in den Editor, sodass Sie sie in eine Anforderung einfügen können:

+ Dienstname der kognitiven Azure-Suche
+ Administratorschlüssel der kognitiven Azure-Suche
+ Cosmos DB-Verbindungszeichenfolge

Sie finden diese Werte im Portal:

1. Kopieren Sie in den Portalseiten für die kognitive Azure-Suche die Suchdienst-URL von der Seite „Übersicht“.

2. Klicken Sie im linken Navigationsbereich auf **Schlüssel**, und kopieren Sie dann entweder den primären oder sekundären Schlüssel (sie sind identisch).

3. Wechseln Sie zu den Portalseiten für Ihr Cosmos-Speicherkonto. Klicken Sie im linken Navigationsbereich unter **Einstellungen** auf die Option **Schlüssel**. Diese Seite enthält einen URI, zwei Sätze Verbindungszeichenfolgen und zwei Sätze Schlüssel. Kopieren Sie eine der Verbindungszeichenfolgen in den Editor.

### <a name="2---create-a-data-source"></a>2 - Erstellen einer Datenquelle

Eine **Datenquelle** gibt die zu indizierenden Daten, Anmeldeinformationen und Richtlinien für das Bestimmen von Änderungen in den Daten an (z.B. geänderte oder gelöschte Dokumente in Ihrer Sammlung). Die Datenquelle wird als unabhängige Ressource definiert, sodass sie von mehreren Indexern verwendet werden kann.

Formulieren Sie zum Erstellen einer Datenquelle eine POST-Anforderung:

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }
```

Der Anforderungstext umfasst die Datenquellendefinition, welche die folgenden Felder enthalten sollte:

| Feld   | Beschreibung |
|---------|-------------|
| **name** | Erforderlich. Wählen Sie für Ihr Datenquellenobjekt einen beliebigen Namen aus. |
|**type**| Erforderlich. Muss `cosmosdb`lauten. |
|**credentials** | Erforderlich. Dieses Feld muss entweder dem Cosmos DB-Verbindungszeichenfolgenformat oder dem Format einer Verbindungszeichenfolge für eine verwaltete Identität folgen.<br/><br/>Für **SQL-Sammlungen** kann die Verbindungszeichenfolge in einem der folgenden Formate vorliegen: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<li>Eine Verbindungszeichenfolge für eine verwaltete Identität, die keinen Kontoschlüssel enthält, im folgenden Format: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;`. Um dieses Verbindungszeichenfolgenformat zu verwenden, befolgen Sie die Anweisungen unter [Einrichten einer Indexerverbindung mit einer Cosmos DB-Datenbank mithilfe einer verwalteten Identität](search-howto-managed-identities-cosmos-db.md).<br/><br/>Verwenden Sie für **MongoDB-Sammlungen** in den Versionen 3.2 und 3.6 eines der folgenden Formate für die Verbindungszeichenfolge: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<li>Eine Verbindungszeichenfolge für eine verwaltete Identität, die keinen Kontoschlüssel enthält, im folgenden Format: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;ApiKind=MongoDb;`. Um dieses Verbindungszeichenfolgenformat zu verwenden, befolgen Sie die Anweisungen unter [Einrichten einer Indexerverbindung mit einer Cosmos DB-Datenbank mithilfe einer verwalteten Identität](search-howto-managed-identities-cosmos-db.md).<br/><br/>Registrieren Sie sich bei Verwendung von **Gremlin-Graphen und Cassandra-Tabellen** für die [geschlossene Indexervorschau](https://aka.ms/azure-cognitive-search/indexer-preview), um Zugriff auf die Vorschauversion sowie Informationen zur Formatierung der Anmeldeinformationen zu erhalten.<br/><br/>Vermeiden Sie Portnummern in der Endpunkt-URL. Wenn Sie die Portnummer einfügen, kann die Azure Cosmos DB-Datenbank in der kognitiven Azure-Suche nicht indiziert werden.|
| **container** | Enthält die folgenden Elemente: <br/>**name:** Erforderlich. Geben Sie die ID der zu indizierenden Datenbanksammlung an.<br/>**Abfrage**: Optional. Sie können eine Abfrage angeben, um ein beliebiges JSON-Dokument in einem Flatfile-Schema zu vereinfachen, das in der kognitiven Azure-Suche indiziert werden kann.<br/>Für die MongoDB-API, die Gremlin-API und die Cassandra-API werden keine Abfragen unterstützt. |
| **dataChangeDetectionPolicy** | Empfohlen. Weitere Informationen finden Sie im Abschnitt [Indizieren von geänderten Dokumenten](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | Optional. Weitere Informationen finden Sie im Abschnitt [Indizieren von gelöschten Dokumenten](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Verwenden von Abfragen zum Formen indizierter Daten
Sie können eine SQL-Abfrage angeben, um geschachtelte Eigenschaften oder Arrays zu vereinfachen, JSON-Eigenschaften zu projizieren und die zu indizierenden Daten zu filtern. 

> [!WARNING]
> Für die **MongoDB-API**, die **Gremlin-API** und die **Cassandra-API** werden keine benutzerdefinierten Abfragen unterstützt: Der Parameter `container.query` muss auf NULL festgelegt oder weggelassen werden. Wenn Sie eine benutzerdefinierte Abfrage verwenden möchten, informieren Sie uns auf [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Beispieldokument:

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

Abfrage zur Filterung:

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

Vereinfachen der Abfrage:

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Abfrage zur Projektion:

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Abfrage zum Vereinfachen eines Arrays:

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

### <a name="3---create-a-target-search-index"></a>3 - Erstellen eines Zielsuchindex 

[Erstellen Sie einen Zielindex der kognitiven Azure-Suche ](/rest/api/searchservice/create-index), wenn Sie bislang noch über keinen verfügen. Anhand des folgenden Beispiels wird ein Index mit einer ID und einem Beschreibungsfeld erstellt:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }
```

Stellen Sie sicher, dass das Schema des Ziel-Indexes mit dem Schema der JSON-Quelldokumente oder mit der Ausgabe Ihrer benutzerdefinierten Abfrageprojektion kompatibel ist.

> [!NOTE]
> Bei partitionierten Sammlungen ist der Standarddokumentschlüssel die Eigenschaft `_rid` von Azure Cosmos DB. Diese wird von Azure Cognitive Search automatisch in `rid` umbenannt, da Feldnamen nicht mit einem Unterstrich beginnen dürfen. Darüber hinaus enthalten die `_rid`-Werte von Azure Cosmos DB Zeichen, die in Schlüsseln der kognitiven Azure-Suche ungültig sind. Deshalb sind die `_rid`-Werte Base64-codiert.
> 
> Für MongoDB-Sammlungen wird die `_id`-Eigenschaft in der kognitiven Azure-Suche automatisch in `id` umbenannt.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Zuordnung zwischen JSON-Datentypen und Datentypen der kognitiven Azure-Suche
| JSON-Datentyp | Kompatible Feldtypen im Zielindex |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Zahlen, die wie Ganzzahlen aussehen |Edm.Int32, Edm.Int64, Edm.String |
| Zahlen, die wie Gleitkommas aussehen |Edm.Double, Edm.String |
| String |Edm.String |
| Arrays primitiver Typen, z.B. ["a", "b", "c"] |Collection(Edm.String) |
| Zeichenfolgen, die wie Datumsangaben aussehen |Edm.DateTimeOffset, Edm.String |
| GeoJSON-Objekte, z.B. { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Andere JSON-Objekte |– |

### <a name="4---configure-and-run-the-indexer"></a>4 - Konfigurieren und Ausführen des Indexers

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

Dieser Indexer wird alle zwei Stunden ausgeführt (das Planungsintervall ist auf „PT2H“ festgelegt). Um einen Indexer alle 30 Minuten auszuführen, legen Sie das Intervall auf „PT30M“ fest. Das kürzeste unterstützte Intervall beträgt fünf Minuten. Der Zeitplan ist optional. Ohne Zeitplan wird ein Indexer nur einmal bei seiner Erstellung ausgeführt. Allerdings können Sie ein Indexer bei Bedarf jederzeit ausführen.   

Weitere Informationen zur API zum Erstellen eines Indexers finden Sie unter [Erstellen eines Indexers](/rest/api/searchservice/create-indexer).

Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Verwenden von .NET

Das allgemein verfügbare .NET SDK ist vollständig gleichwertig mit der allgemein verfügbaren REST-API. Es wird empfohlen, dass Sie den vorherige Abschnitt zur REST-API genau lesen, um die Konzepte, den Workflow und die Anforderungen zu verstehen. Sie können sich anschließend auf die folgende .NET-API-Referenzdokumentation beziehen, um einen JSON-Indexer in verwalteten Code zu implementieren.

+ [azure.search.documents.indexes.models.searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents.indexes.models.searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents.indexes.models.searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents.indexes.models.searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indizieren von geänderten Dokumenten

Die Richtlinie zum Erkennen von Datenänderungen dient einer effizienten Identifizierung geänderter Datenelemente. Derzeit wird nur die [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy)-Richtlinie unterstützt, die die `_ts`-Eigenschaft (Zeitstempel) verwendet, die von Azure Cosmos DB bereitgestellt wird und wie folgt angegeben wird:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

Wenn Sie diese Richtlinie verwenden, wird dringend empfohlen sicherzustellen, dass der Indexer eine hohe Leistung erzielt. 

Wenn Sie eine benutzerdefinierte Abfrage verwenden, stellen sicher, dass die `_ts`Eigenschaft von der Abfrage projiziert wird.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Inkrementeller Status und benutzerdefinierte Abfragen

Der inkrementelle Status während der Indizierung stellt sicher, dass der Indexer bei der nächsten Ausführung an der gleichen Stelle fortgesetzt werden kann und nicht die gesamte Sammlung von vorn indizieren muss, wenn er durch vorübergehende Fehler oder das Erreichen des Ausführungszeitlimits unterbrochen wird. Dies ist besonders beim Indizieren umfangreicher Sammlungen wichtig. 

Stellen Sie zum Aktivieren des inkrementellen Status bei Verwendung einer benutzerdefinierten Abfrage sicher, dass die Abfrage die Ergebnisse nach der Spalte `_ts` sortiert. Dadurch können regelmäßige Prüfpunkte erstellt werden, die in der kognitiven Azure-Suche bei Fehlern zur Bereitstellung des inkrementellen Status verwendet werden.   

In bestimmten Fällen kann in der kognitiven Azure-Suche unter Umständen nicht erkannt werden, dass die Abfrage nach `_ts` sortiert ist. (Dies kann sogar vorkommen, wenn die Abfrage eine `ORDER BY [collection alias]._ts`-Klausel enthält.) Mithilfe der Konfigurationseigenschaft `assumeOrderByHighWaterMarkColumn` können Sie in der kognitiven Azure-Suche angeben, dass die Ergebnisse sortiert sind. Erstellen oder aktualisieren Sie Ihren Indexer wie folgt, um diesen Hinweis anzugeben: 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indizieren von gelöschten Dokumenten

Wenn Zeilen aus der Quelltabelle gelöscht werden, möchten Sie diese Zeilen in der Regel auch aus dem Suchindex löschen. Die Richtlinie zum Erkennen von Datenlöschungen dient einer effizienten Identifizierung gelöschter Datenelemente. Zurzeit ist `Soft Delete` die einzige unterstützte Richtlinie (die Löschung wird durch ein bestimmtes Kennzeichen markiert). Diese wird folgendermaßen festgelegt:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

Wenn Sie eine benutzerdefinierte Abfrage verwenden, stellen Sie sicher, dass die Eigenschaft, auf die `softDeleteColumnName` verweist, von der Abfrage projiziert wird.

Im folgenden Beispiel wird eine Datenquelle mit einer Richtlinie zum vorläufigen Löschen erstellt:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

## <a name="next-steps"></a><a name="NextSteps"></a>Nächste Schritte

Glückwunsch! Sie wissen nun, wie Azure Cosmos DB unter Verwendung eines Indexers in die kognitive Azure-Suche integriert wird.

* Weitere Informationen zu Azure Cosmos DB finden Sie auf der [Seite über den Azure Cosmos DB-Dienst](https://azure.microsoft.com/services/cosmos-db/).
* Weitere Informationen zur kognitiven Azure-Suche finden Sie auf der [Seite des Search-Diensts](https://azure.microsoft.com/services/search/).