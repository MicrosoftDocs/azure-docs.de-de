---
title: Verwalten von Indizierungsrichtlinien in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Indizierungsrichtlinien verwalten, eine Eigenschaft in die Indizierung aufnehmen oder davon ausschließen und die Indizierung mithilfe verschiedener Azure Cosmos DB SDKs definieren.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 11/02/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: cd51210a64223fab5d2d48a91bd3d0a6521a9627
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341313"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Verwalten von Indizierungsrichtlinien in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB werden Daten gemäß [Indizierungsrichtlinien](index-policy.md) indiziert, die für jeden Container definiert sind. Die standardmäßige Indizierungsrichtlinie für neu erstellte Container erzwingt Bereichsindizes für jede Zeichenfolge oder Zahl. Diese Richtlinie kann mit ihrer eigenen benutzerdefinierten Indizierungsrichtlinie überschrieben werden.

> [!NOTE]
> Die in diesem Artikel beschriebene Methode zur Aktualisierung von Indizierungsrichtlinien gilt nur für die SQL (Core)-API von Azure Cosmos DB. Unter [Azure Cosmos DB-API für MongoDB](mongodb-indexing.md) und [Sekundäre Indizierung in der Cassandra-API für Azure Cosmos DB](cassandra-secondary-index.md) erfahren Sie mehr über die Indizierung.

## <a name="indexing-policy-examples"></a>Beispiele für Indizierungsrichtlinien

Hier sehen Sie einige Beispiele für Indizierungsrichtlinien im [JSON-Format](index-policy.md#include-exclude-paths). So werden sie für das Azure-Portal verfügbar gemacht. Die gleichen Parameter können über die Azure CLI oder ein beliebiges SDK festgelegt werden.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Deaktivierungsrichtlinie zum selektiven Ausschließen einiger Eigenschaftspfade

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Diese Indizierungsrichtlinie entspricht der unten gezeigten Indizierungsrichtlinie, mit der ```kind```, ```dataType``` und ```precision``` manuell auf die jeweiligen Standardwerte festgelegt werden. Diese Eigenschaften müssen nicht mehr explizit festgelegt werden, und Sie sollten sie in der Indizierungsrichtlinie vollständig auslassen (wie im Beispiel oben gezeigt).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Aktivierungsrichtlinie zum selektiven Einschließen einiger Eigenschaftspfade

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Diese Indizierungsrichtlinie entspricht der unten gezeigten Indizierungsrichtlinie, mit der ```kind```, ```dataType``` und ```precision``` manuell auf die jeweiligen Standardwerte festgelegt werden. Diese Eigenschaften müssen nicht mehr explizit festgelegt werden, und Sie sollten sie in der Indizierungsrichtlinie vollständig auslassen (wie im Beispiel oben gezeigt).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE]
> Allgemein wird die Verwendung einer Indizierungsrichtlinie zur **Deaktivierung** empfohlen, damit Azure Cosmos DB neue Eigenschaften, die Ihrem Datenmodell hinzugefügt werden, proaktiv indizieren kann.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Verwenden eines räumlichen Index nur für einen bestimmten Eigenschaftspfad

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Zusammengesetzte Indizierung – Richtlinienbeispiele

Über das Einschließen oder Ausschließen von Pfaden für einzelne Eigenschaften hinaus können Sie auch einen zusammengesetzten Index angeben. Wenn Sie eine Abfrage für ausführen möchten, die eine `ORDER BY`-Klausel für mehrere Eigenschaften aufweist, ist ein [zusammengesetzter Index](index-policy.md#composite-indexes) für diese Eigenschaften erforderlich. Außerdem bedeuten zusammengesetzte Indizes einen Leistungsvorteil für Abfragen, die über mehrere Filter oder sowohl über einen Filter als auch eine ORDER BY-Klausel verfügen.

> [!NOTE]
> Zusammengesetzte Pfade weisen die implizite Angabe `/?` auf, weil nur der Skalarwert unter diesem Pfad indiziert wird. Der Platzhalter `/*` wird in zusammengesetzten Pfaden nicht unterstützt. Sie sollten in einem zusammengesetzten Pfad weder `/?` noch `/*` angeben.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Zusammengesetzter Index, definiert für (name asc, age desc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Der oben gezeigte zusammengesetzte Index für Name und Alter ist für Abfrage 1 und Abfrage 2 erforderlich:

Abfrage 1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Abfrage 2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Dieser zusammengesetzte Index profitiert von Abfrage 3 und Abfrage 4 und optimiert die Filter:

Abfrage 3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Abfrage 4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Zusammengesetzter Index, definiert für (name ASC, age ASC) und (name ASC, age DESC):

Sie können innerhalb der gleichen Indizierungsrichtlinie mehrere verschiedene zusammengesetzte Indizes definieren.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Zusammengesetzter Index, definiert für (name ASC, age ASC):

Die Angabe der Reihenfolge ist optional. Ohne Angabe ist die Reihenfolge aufsteigend.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Ausschließen aller Eigenschaftspfade aber Aktivhalten der Indizierung

Diese Richtlinie kann in Situationen verwendet werden, in denen das [Feature „Gültigkeitsdauer“ (Time-to-Live, TTL)](time-to-live.md) aktiv ist, aber keine zusätzlichen Indizes erforderlich sind (um Azure Cosmos DB als reinen Schlüsselwertspeicher zu verwenden).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Keine Indizierung

Mit dieser Richtlinie wird die Indizierung deaktiviert. Wenn `indexingMode` auf `none` festgelegt ist, können Sie keine Gültigkeitsdauer für den Container festlegen.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Aktualisieren der Indizierungsrichtlinie

In Azure Cosmos DB kann die Indizierungsrichtlinie mit den folgenden Methoden aktualisiert werden:

- über das Azure-Portal
- mit der Azure CLI
- mithilfe von PowerShell
- mit einem der SDKs

Eine [Aktualisierung der Indizierungsrichtlinie](index-policy.md#modifying-the-indexing-policy) löst eine Indextransformation aus. Der Status dieser Transformation kann auch über die SDKs nachverfolgt werden.

> [!NOTE]
> Beim Aktualisieren der Indizierungsrichtlinie werden Schreibvorgänge in Azure Cosmos DB ohne Unterbrechung durchführt. Weitere Informationen zu [Indizierungstransformationen](index-policy.md#modifying-the-indexing-policy)

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Azure Cosmos-Container speichern ihre Indizierungsrichtlinie als ein JSON-Dokument, das im Azure-Portal direkt bearbeitet werden kann.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Erstellen Sie ein neues Azure Cosmos-Konto, oder wählen Sie ein bereits vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer** , und wählen Sie den gewünschten Container aus.

1. Klicken Sie auf **Skalierung und Einstellungen**.

1. Ändern Sie das JSON-Dokument der Indizierungsrichtlinie (siehe [folgende](#indexing-policy-examples) Beispiele).

1. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

:::image type="content" source="./media/how-to-manage-indexing-policy/indexing-policy-portal.png" alt-text="Verwalten der Indizierung über das Azure-Portal":::

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

Informationen zum Erstellen eines Containers mit einer benutzerdefinierten Indexrichtlinie finden Sie im Thema zum [Erstellen eines Containers mit einer benutzerdefinierten Indexrichtlinie mithilfe der CLI](manage-with-cli.md#create-a-container-with-a-custom-index-policy).

## <a name="use-powershell"></a>Verwenden von PowerShell

Informationen zum Erstellen eines Containers mit einer benutzerdefinierten Indexrichtlinie finden Sie im Thema zum [Erstellen eines Containers mit einer benutzerdefinierten Indexrichtlinie mithilfe von PowerShell](manage-with-powershell.md#create-container-custom-index).

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a> Verwenden des .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Das `DocumentCollection`-Objekt aus dem [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) macht eine `IndexingPolicy`-Eigenschaft verfügbar, mit der Sie `IndexingMode` ändern sowie `IncludedPaths` und `ExcludedPaths` hinzufügen oder entfernen können.

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Zum Nachverfolgen des Fortschritts der Indextransformation übergeben Sie ein `RequestOptions`-Objekt, das die `PopulateQuotaInfo`-Eigenschaft auf `true` festlegt.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Das `ContainerProperties`-Objekt aus dem [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (Informationen zur Verwendung in [diesem Schnellstart](create-sql-api-dotnet.md)) macht eine `IndexingPolicy`-Eigenschaft verfügbar, mit der Sie `IndexingMode` ändern sowie `IncludedPaths` und `ExcludedPaths` hinzufügen oder entfernen können.

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Zum Nachverfolgen des Fortschritts der Indextransformation übergeben Sie ein `RequestOptions`-Objekt, das die `PopulateQuotaInfo`-Eigenschaft auf `true` festlegt. Anschließend rufen Sie den Wert aus dem `x-ms-documentdb-collection-index-transformation-progress`-Antwortheader ab.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Wenn Sie beim Erstellen eines neuen Containers eine benutzerdefinierte Indizierungsrichtlinie definieren, bietet die Fluent-API aus dem SDK V3 eine präzise und effiziente Möglichkeit zum Schreiben dieser Definition:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Verwenden des Java SDK

Das `DocumentCollection`-Objekt aus dem [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (Informationen zur Verwendung in [diesem Schnellstart](create-sql-api-java.md)) macht die Methoden `getIndexingPolicy()` und `setIndexingPolicy()` verfügbar. Mit dem bearbeiteten `IndexingPolicy`-Objekt können Sie den Indizierungsmodus ändern sowie ein- und ausgeschlossene Pfade hinzufügen und entfernen.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Zum Nachverfolgen der Fortschritts der Indextransformation für einen Container übergeben Sie ein `RequestOptions`-Objekt, das anfordert, dass die Kontingentinformationen ausgefüllt werden. Anschließend rufen Sie den Wert aus dem `x-ms-documentdb-collection-index-transformation-progress`-Antwortheader ab.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Verwenden des Node.js SDK

Die `ContainerDefinition`-Schnittstelle aus [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (Informationen zur Verwendung in [diesem Schnellstart](create-sql-api-nodejs.md)) mach eine `indexingPolicy`-Eigenschaft verfügbar, mit der Sie `indexingMode` ändern sowie `includedPaths` und `excludedPaths` hinzufügen oder entfernen können.

Abrufen der Details des Containers

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Festlegen des Indizierungsmodus auf „Konsistent“

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Hinzufügen eines eingeschlossenen Pfads mit einem räumlichen Index

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Hinzufügen eines ausgeschlossenen Pfads

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Aktualisieren des Containers mit Änderungen

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Zum Nachverfolgen der Fortschritts der Indextransformation für einen Container übergeben Sie ein `RequestOptions`-Objekt, das die `populateQuotaInfo`-Eigenschaft auf `true` festlegt. Anschließend rufen Sie den Wert aus dem `x-ms-documentdb-collection-index-transformation-progress`-Antwortheader ab.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Verwenden des Python SDK

# <a name="python-sdk-v3"></a>[Python SDK V3](#tab/pythonv3)

Bei Verwendung des [Python SDK V3](https://pypi.org/project/azure-cosmos/) (Informationen zur Verwendung in [diesem Schnellstart](create-sql-api-python.md)) wird die Containerkonfiguration als Wörterbuch verwaltet. Über dieses Wörterbuch ist der Zugriff auf die Indizierungsrichtlinie und alle ihre Attribute möglich.

Abrufen der Details des Containers

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Festlegen des Indizierungsmodus auf „Konsistent“

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definieren einer Indizierungsrichtlinie mit einem eingeschlossenen Pfad und einem räumlichen Index

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definieren einer Indizierungsrichtlinie mit einem ausgeschlossenen Pfad

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Hinzufügen eines zusammengesetzten Index

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Aktualisieren des Containers mit Änderungen

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[Python SDK V4](#tab/pythonv4)

Bei Verwendung des [Python SDK V4](https://pypi.org/project/azure-cosmos/) wird die Containerkonfiguration als Wörterbuch verwaltet. Über dieses Wörterbuch ist der Zugriff auf die Indizierungsrichtlinie und alle ihre Attribute möglich.

Abrufen der Details des Containers

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Festlegen des Indizierungsmodus auf „Konsistent“

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Definieren einer Indizierungsrichtlinie mit einem eingeschlossenen Pfad und einem räumlichen Index

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definieren einer Indizierungsrichtlinie mit einem ausgeschlossenen Pfad

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Hinzufügen eines zusammengesetzten Index

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

Aktualisieren des Containers mit Änderungen

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

Abrufen des Fortschritts der Indextransformation aus den Antwortheadern
```python
container_client.read(populate_quota_info = True,
                      response_hook = lambda h,p: print(h['x-ms-documentdb-collection-index-transformation-progress']))
```

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Indizierung erhalten Sie in den folgenden Artikeln:

- [Übersicht über die Indizierung](index-overview.md)
- [Indizierungsrichtlinie](index-policy.md)
