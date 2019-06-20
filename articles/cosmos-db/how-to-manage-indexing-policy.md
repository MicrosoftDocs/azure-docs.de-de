---
title: Verwalten von Indizierungsrichtlinien in Azure Cosmos DB
description: Informationen zur Verwaltung von Indizierungsrichtlinien in Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 05fd369cfebba03c814507f82755fa6cb6a89400
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386800"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Verwalten von Indizierungsrichtlinien in Azure Cosmos DB

In Azure Cosmos DB werden Daten gemäß [Indizierungsrichtlinien](index-policy.md) indiziert, die für jeden Container definiert sind. Die standardmäßige Indizierungsrichtlinie für neu erstellte Container erzwingt Bereichsindizes für jede Zeichenfolge oder Zahl und räumliche Indizes für jedes GeoJSON-Objekt vom Typ „Punkt“. Diese Richtlinie kann wie folgt außer Kraft gesetzt werden:

- über das Azure-Portal
- mit der Azure CLI
- mit einem der SDKs

Eine [Aktualisierung der Indizierungsrichtlinie](index-policy.md#modifying-the-indexing-policy) löst eine Indextransformation aus. Der Status dieser Transformation kann auch über die SDKs nachverfolgt werden.

> [!NOTE]
> Als Bestandteil des SDK- und Portal-Upgrades entwickeln wir die Indexrichtlinie weiter, um sie an ein neues Indexlayout anzupassen, das wir für neue Container eingeführt haben. Mit diesem neuen Layout werden alle primitiven Datentypen als Bereich mit vollständiger Genauigkeit (-1) indiziert. Daher werden die Indexarten und die Genauigkeit dem Benutzer nicht mehr verfügbar gemacht. Zukünftig müssen Benutzer lediglich Pfade zum Abschnitt „includedPaths“ hinzufügen und können „indexKinds“ und „precision“ ignorieren. Diese Änderung wirkt sich nicht auf die Leistung aus, und Sie können die Indizierungsrichtlinie weiterhin mit derselben Syntax aktualisieren. Sie können weiterhin alle Beispiele in der vorhandenen Dokumentation verwenden, um die Indexrichtlinie zu aktualisieren.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Azure Cosmos-Container speichern ihre Indizierungsrichtlinie als ein JSON-Dokument, das im Azure-Portal direkt bearbeitet werden kann.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Erstellen Sie ein neues Azure Cosmos-Konto, oder wählen Sie ein bereits vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer**, und wählen Sie den gewünschten Container aus.

1. Klicken Sie auf **Skalierung und Einstellungen**.

1. Ändern Sie das JSON-Dokument der Indizierungsrichtlinie (siehe [folgende](#indexing-policy-examples) Beispiele).

1. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

![Verwalten der Indizierung über das Azure-Portal](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

Mit dem Befehl [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) über die Azure CLI können Sie die JSON-Definition der Indizierungsrichtlinie eines Containers ersetzen:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Verwenden des .NET SDK v2

Das `DocumentCollection`-Objekt aus [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (Informationen zur Verwendung in [diesem Schnellstart](create-sql-api-dotnet.md)) macht eine `IndexingPolicy`-Eigenschaft verfügbar, mit der Sie `IndexingMode` ändern sowie `IncludedPaths` und `ExcludedPaths` hinzufügen oder entfernen können.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Zum Nachverfolgen des Fortschritts der Indextransformation übergeben Sie ein `RequestOptions`-Objekt, das die `PopulateQuotaInfo`-Eigenschaft auf `true` festlegt.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Verwenden des Java SDK

Das `DocumentCollection`-Objekt aus dem [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (Informationen zur Verwendung in [diesem Schnellstart](create-sql-api-java.md)) macht die Methoden `getIndexingPolicy()` und `setIndexingPolicy()` verfügbar. Mit dem bearbeiteten `IndexingPolicy`-Objekt können Sie den Indizierungsmodus ändern sowie ein- und ausgeschlossene Pfade hinzufügen und entfernen.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
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

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
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

Bei Verwendung des [Python SDK](https://pypi.org/project/azure-cosmos/) (Informationen zur Verwendung in[diesem Schnellstart](create-sql-api-python.md)) wird die Konfiguration des Containers als Wörterbuch verwaltet. Über dieses Wörterbuch ist der Zugriff auf die Indizierungsrichtlinie und alle ihre Attribute möglich.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Beispiele für Indizierungsrichtlinien

Hier sehen Sie einige Beispiele für Indizierungsrichtlinien im JSON-Format. So werden sie für das Azure-Portal verfügbar gemacht. Die gleichen Parameter können über die Azure CLI oder ein beliebiges SDK festgelegt werden.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Deaktivierungsrichtlinie zum selektiven Ausschließen einiger Eigenschaftspfade
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
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
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
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

Hinweis: Allgemein wird die Verwendung einer Indizierungsrichtlinie zur **Deaktivierung** zu verwenden, damit Azure Cosmos DB neue Eigenschaften, die Ihrem Modell hinzugefügt werden, proaktiv indizieren kann.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Verwenden eines räumlichen Index nur für einen bestimmten Eigenschaftspfad
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
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
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Ausschließen aller Eigenschaftspfade aber Aktivhalten der Indizierung

Diese Richtlinie kann in Situationen verwendet werden, in denen das [Feature „Gültigkeitsdauer“ (Time-to-Live, TTL)](time-to-live.md) aktiv ist, aber kein Sekundärindex erforderlich ist (um Azure Cosmos DB als reinen Schlüsselwertspeicher zu verwenden).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Keine Indizierung
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Zusammengesetzte Indizierung – Richtlinienbeispiele

Über das Einschließen oder Ausschließen von Pfaden für einzelne Eigenschaften hinaus können Sie auch einen zusammengesetzten Index angeben. Wenn Sie eine Abfrage für ausführen möchten, die eine `ORDER BY`-Klausel für mehrere Eigenschaften aufweist, ist ein [zusammengesetzter Index](index-policy.md#composite-indexes) für diese Eigenschaften erforderlich.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Zusammengesetzter Index, definiert für (name asc, age desc):
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Dieser zusammengesetzte Index könnte die zwei folgenden Abfragen unterstützen:

Abfrage 1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Abfrage 2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Zusammengesetzter Index, definiert für (name asc, age asc) und (name asc, age desc):

Sie können innerhalb der gleichen Indizierungsrichtlinie mehrere verschiedene zusammengesetzte Indizes definieren. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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
            ]
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Zusammengesetzter Index, definiert für (name asc, age asc):

Die Angabe der Reihenfolge ist optional. Ohne Angabe ist die Reihenfolge aufsteigend.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Indizierung erhalten Sie in den folgenden Artikeln:

- [Übersicht über die Indizierung](index-overview.md)
- [Indizierungsrichtlinie](index-policy.md)
