---
title: Unterstützung und Kompatibilität von Gremlin in Azure Cosmos DB mit TinkerPop-Features
description: Informieren Sie sich über die Sprache Gremlin von Apache TinkerPop. Erfahren Sie, welche Features und Schritte in Azure Cosmos DB verfügbar sind und welche Kompatibilitätsunterschiede zur TinkerPop-Graph-Engine bestehen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: a149f0b331a77462aa53b948fedf25dd1331969e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "94683623"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Unterstützung und Kompatibilität von Gremlin-Diagrammen in Azure Cosmos DB mit TinkerPop-Features
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB unterstützt die [Apache Tinkerpop](https://tinkerpop.apache.org)-Graphdurchlauf-Sprache, auch als [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps) bezeichnet. Mithilfe der Gremlin-Sprache können Sie Diagrammentitäten (Vertices und Edges) erstellen, Eigenschaften innerhalb dieser Entitäten ändern, Abfragen und Traversierungen ausführen und Entitäten löschen.

Die Azure Cosmos DB Graph-Engine hält sich eng an die [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)-Spezifikation für die Durchlaufschritte, es gibt jedoch Azure Cosmos DB-spezifische Unterschiede bei der Implementierung. Dieser Artikel enthält eine kurze exemplarische Vorgehensweise zu Gremlin und listet die Funktionen von Gremlin auf, die von der Gremlin-API unterstützt werden.

## <a name="compatible-client-libraries"></a>Kompatible Clientbibliotheken

In der folgenden Tabelle werden gängige Gremlin-Treiber aufgeführt, die Sie für Azure Cosmos DB verwenden können:

| Download | `Source` | Erste Schritte | Unterstützte Connector-Version |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET auf GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Erstellen von Graph mithilfe von .NET](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Erstellen von Graph mithilfe von Java](create-graph-java.md) | 3.2.0 und höher |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript auf GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Erstellen von Graph mithilfe von Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python auf GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Erstellen von Graph mithilfe von Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP auf GitHub](https://github.com/PommeVerte/gremlin-php) | [Erstellen von Graph mithilfe von PHP](create-graph-php.md) | 3.1.0 |
| [Go Lang](https://github.com/supplyon/gremcos/) | [Go Lang](https://github.com/supplyon/gremcos/) | | Diese Bibliothek wird von externen Mitwirkenden erstellt. Das Azure Cosmos DB-Team bietet keine Unterstützung für die Bibliothek, und die Bibliothek wird nicht vom Azure Cosmos DB-Team gewartet. |
| [Gremlin-Konsole](https://tinkerpop.apache.org/downloads.html) | [TinkerPop-Dokumente](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Erstellen von Graph mithilfe der Gremlin-Konsole](create-graph-gremlin-console.md) | 3.2.0 und höher |

## <a name="supported-graph-objects"></a>Unterstützte Graph-Objekte

TinkerPop ist ein Standard, der eine große Bandbreite an Diagrammtechnologien abdeckt. Daher weist diese Standardterminologie auf, die beschreiben, welche Funktionen von einem Diagrammanbieter bereitgestellt werden. Azure Cosmos DB stellt eine beständige und beschreibbare Diagrammdatenbank mit hoher Parallelität dar, die über mehrere Server oder Cluster hinweg partitioniert werden kann. 

Die folgende Tabelle enthält die TinkerPop-Funktionen, die von Azure Cosmos DB implementiert werden: 

| Category | Implementierung von Azure Cosmos DB |  Notizen | 
| --- | --- | --- |
| Diagrammfunktionen | Stellen Persistenz und ConcurrentAccess bereit. Sind zur Unterstützung für Transaktionen ausgelegt. | Computermethoden können über den Spark-Connector implementiert werden. |
| Variablenfunktionen | Unterstützen Boolean-, Integer-, Byte-, Double-, Float-, Long- und String-Datentypen | Unterstützen primitive Typen; ist mit komplexen Typen über Datenmodelle kompatibel |
| Vertex-Funktionen | Unterstützen RemoveVertices MetaProperties, AddVertices MultiProperties, StringIds UserSuppliedIds, AddProperty, RemoveProperty  | Unterstützen die Erstellung, Änderung und Löschung von Vertices |
| Vertex-Eigenschafts-Funktionen | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Unterstützen die Erstellung, Änderung und Löschung von Vertex-Eigenschaften |
| Edgefunktionen | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Unterstützen die Erstellung, Änderung und Löschung von Edges |
| Edge-Eigenschafts-Funktionen | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Unterstützen die Erstellung, Änderung und Löschung von Edge-Eigenschaften |

## <a name="gremlin-wire-format"></a>Gremlin-Sendeformat

Azure Cosmos DB verwendet bei der Rückgabe von Ergebnissen aus Gremlin-Vorgängen das JSON-Format. Von Azure Cosmos DB wird aktuell das JSON-Format unterstützt. Der folgende Ausschnitt zeigt beispielsweise eine JSON-Darstellung eines Vertex, der von Azure Cosmos DB *an den Client zurückgegeben* wurde:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Im Anschluss werden die Eigenschaften beschrieben, die im JSON-Format für Vertices verwendet werden:

| Eigenschaft | Beschreibung | 
| --- | --- | --- |
| `id` | Die ID für den Vertex. Muss eindeutig sein (in Kombination mit dem Wert von `_partition`, falls zutreffend). Wird kein Wert angegeben, wird automatisch eine GUID bereitgestellt. | 
| `label` | Die Bezeichnung des Vertex. Diese Eigenschaft dient zur Beschreibung des Entitätstyps. |
| `type` | Dient zur Unterscheidung von Vertices von anderen Dokumenten, die keine Diagramme sind. |
| `properties` | Sammlung von benutzerdefinierten Eigenschaften in Verbindung mit dem Vertex. Jede Eigenschaft kann mehrere Werte enthalten. |
| `_partition` | Der Partitionsschlüssel des Vertex. Wird für [Graphpartitionierung](graph-partitioning.md) verwendet. |
| `outE` | Diese Eigenschaft enthält eine Liste von externen Edges aus einem Vertex. Die Speicherung von Informationen zur Nähe zum Vertex ermöglicht die schnelle Ausführung von Traversierungen. Edges werden basierend auf deren Bezeichnungen gruppiert. |

Zudem enthalten Edges folgende Informationen, die die Navigation in anderen Teilen des Diagramms unterstützen können.

| Eigenschaft | Beschreibung |
| --- | --- |
| `id` | Die ID für den Edge. Muss eindeutig sein (in Kombination mit dem Wert von `_partition`, falls zutreffend). |
| `label` | Die Bezeichnung des Edge. Diese Eigenschaft ist optional, und dient zur Beschreibung des Beziehungstyps. |
| `inV` | Diese Eigenschaft enthält eine Liste von In-Vertices für einen Edge. Die Speicherung von Informationen über die Nähe zum Vertex mit dem Edge ermöglicht die schnelle Ausführung von Traversierungen. Vertices werden basierend auf ihren Bezeichnungen gruppiert. |
| `properties` | Sammlung von benutzerdefinierten Eigenschaften in Verbindung mit dem Edge. Jede Eigenschaft kann mehrere Werte enthalten. |

Jede Eigenschaft kann mehrere Werte in einem Array speichern. 

| Eigenschaft | Beschreibung |
| --- | --- |
| `value` | Wert der Eigenschaft

## <a name="gremlin-steps"></a>Gremlin-Schritte

Sehen wir uns nun die Gremlin-Schritte an, die von Azure Cosmos DB unterstützt werden. Eine vollständige Referenz zu Gremlin finden Sie in der [TinkerPop-Referenz](https://tinkerpop.apache.org/docs/3.3.2/reference).

| Schritt | Beschreibung | Dokumentation zu TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Fügt einen Edge zwischen zwei Vertices hinzu | [addE-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Fügt einen Vertex zum Diagramm hinzu | [addV-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Stellt sicher, dass alle Traversierungen einen Wert zurückgeben | [and-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Ein Schrittmodulator für die Zuweisung einer Variable zur Ausgabe eines Schritts | [as-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Ein mit `group` und `order` verwendeter Schrittmodulator | [by-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Gibt die erste Traversierung, die ein Ergebnis zurückgibt, zurück | [coalesce-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Gibt einen konstanten Wert zurück. Wird mit `coalesce` verwendet.| [constant-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Gibt die Anzahl aus der Traversierung zurück | [count-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Gibt die Werte mit entfernten Duplikaten zurück | [dedup-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Löscht die Werte (Vertex/Edge) | [drop-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Erstellt eine Beschreibung aller Vorgänge, die im ausgeführten Gremlin-Schritt generiert werden. | [executionProfile-Schritt](graph-execution-profile.md) |
| `fold` | Fungiert als Grenze, die Ergebnisse berechnet und zusammenfasst| [fold-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Gruppiert die Werte basierend auf den angegebenen Bezeichnungen| [group-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Wird zum Filtern von Eigenschaften, Vertices und Edges verwendet. Unterstützt die Varianten `hasLabel`, `hasId`, `hasNot` und `has`. | [has-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Fügt Werte in einen Stream ein| [inject-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Dient zur Ausführung eines Filters mithilfe eines booleschen Ausdrucks | [is-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Dient zur Beschränkung der Anzahl der Elemente in der Traversierung| [limit-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Umschließt lokal einen Abschnitt einer Traversierung, ähnlich wie bei einer Unterabfrage | [local-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Dient zur Negierung eines Filters | [not-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Gibt das Ergebnis der angegebenen Traversierung zurück, wenn diese ein anderes Ergebnis als das vom aufrufenden Element zurückgegebene Ergebnis liefert | [optional-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Stellt sicher, dass mindestens einer der Traversierungen einen Wert zurückgibt | [or-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Gibt die Ergebnisse in der angegebenen Sortierreihenfolge zurück | [order-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Gibt den vollständigen Pfad der Traversierung zurück | [path-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projiziert die Eigenschaften als Karte | [project-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Gibt die Eigenschaften für die angegebenen Bezeichnungen zurück | [properties-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtert auf den angegebenen Wertebereich| [range-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Wiederholt den Schritt für die angegebene Anzahl von Versuchen. Wird für die Ausführung als Schleife verwendet. | [repeat-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Wird zum Testen von Ergebnissen aus der Traversierung verwendet | [sample-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Wird zum Projizieren von Ergebnissen aus der Traversierung verwendet |  [select-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Wird für nicht blockierende Aggregate aus der Traversierung verwendet | [store-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Filterfunktion für Zeichenfolgen. Diese Funktion dient als ein Prädikat für den Schritt `has()`, um eine Eigenschaft mit dem Anfang einer bestimmten Zeichenfolge zu finden. | [TextP-Prädikate](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Filterfunktion für Zeichenfolgen. Diese Funktion dient als ein Prädikat für den Schritt `has()`, um eine Eigenschaft mit dem Ende einer bestimmten Zeichenfolge zu finden. | [TextP-Prädikate](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Filterfunktion für Zeichenfolgen. Diese Funktion dient als ein Prädikat für den Schritt `has()`, um eine Eigenschaft mit dem Inhalt einer bestimmten Zeichenfolge zu finden. | [TextP-Prädikate](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Filterfunktion für Zeichenfolgen. Diese Funktion dient als ein Prädikat für den Schritt `has()`, um eine Eigenschaft zu finden, die nicht mit einer bestimmten Zeichenfolge beginnt. | [TextP-Prädikate](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Filterfunktion für Zeichenfolgen. Diese Funktion dient als ein Prädikat für den Schritt `has()`, um eine Eigenschaft zu finden, die nicht auf eine bestimmte Zeichenfolge endet. | [TextP-Prädikate](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Filterfunktion für Zeichenfolgen. Diese Funktion dient als ein Prädikat für den Schritt `has()`, um eine Eigenschaft zu finden, die eine bestimmte Zeichenfolge nicht enthält. | [TextP-Prädikate](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Aggregiert Pfade aus einem Vertex in einer Struktur | [tree-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Löst einen Iterator als Schritt auf| [unfold-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Führt Ergebnisse aus mehreren Traversierungen zusammen| [union-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Enthält die erforderlichen Schritte für Traversierungen zwischen Vertices und Edges: `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` und `otherV` für | [vertex-Schritte](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Wird zum Filtern von Ergebnissen aus der Traversierung verwendet. Unterstützt die Operatoren `eq`, `neq`, `lt`, `lte`, `gt`, `gte` und `between`.  | [where-Schritt](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Die von Azure Cosmos DB bereitgestellte, für Schreibvorgänge optimierte Engine unterstützt standardmäßig die automatische Indizierung aller Eigenschaften in Vertices und Edges. Daher werden Abfragen mit Filtern, Bereichsabfragen, Sortierungen oder Aggregate von Eigenschaften über den Index verarbeitet und effizient übermittelt. Weitere Informationen zur Indizierung in Azure Cosmos DB finden Sie in unserem Dokument unter [Schemagnostische Indizierung](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="behavior-differences"></a>Unterschiede im Verhalten

* Die Azure Cosmos DB Graph-Engine führt einen Durchlauf mit ***breitenorientiertem Lastenausgleich** _ aus, während TinkerPop Gremlin einen Ansatz vom Typ „Tiefenorientierter Lastenausgleich“ verfolgt. Dieses Verhalten erzielt eine bessere Leistung in einem horizontal skalierbaren System wie Cosmos DB.

## <a name="unsupported-features"></a>Nicht unterstützte Funktionen

_ * **[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)** _ ist eine von der Programmiersprache unabhängige Spezifikation für Diagrammdurchläufe. Cosmos DB Graph unterstützt dies noch nicht. Verwenden Sie `GremlinClient.SubmitAsync()`, und übergeben Sie den Durchlauf als eine Textzeichenfolge.

_ * **`property(set, 'xyz', 1)`** _: Das Festlegen der Kardinalität wird noch nicht unterstützt. Verwenden Sie stattdessen `property(list, 'xyz', 1)`. Weitere Informationen finden Sie unter [Vertex-Eigenschaften mit TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

_ Der ***Schritt `match()`** _ ist derzeit nicht verfügbar. Dieser Schritt bietet deklarative Abfragefunktionen.

_ ***Objekte als Eigenschaften** _ werden für Vertices und Edges nicht unterstützt. Eigenschaften können nur primitive Typen oder Arrays sein.

_ ***Sortieren nach Arrayeigenschaften** _ (`order().by(<array property>)`) wird nicht unterstützt. Die Sortierung wird nur von primitiven Typen unterstützt.

_ ***Nicht primitive JSON-Typen** _ werden nicht unterstützt. Verwenden Sie die Typen `string`, `number` oder `true`/`false`. `null`-Werte werden nicht unterstützt. 

_ Das ***GraphSONv3** _-Serialisierungsmodul wird derzeit nicht unterstützt. Verwenden Sie das `GraphSONv2`-Serialisierungsmodul sowie Reader- und Writerklassen in der Verbindungskonfiguration. Das Format der von der Azure Cosmos DB Gremlin-API zurückgegebenen Ergebnisse ist nicht identisch mit dem GraphSON-Format. 

_ **Lambdaausdrücke und -funktionen** werden derzeit nicht unterstützt. Dies umfasst die Funktionen `.map{<expression>}`, `.by{<expression>}` und `.filter{<expression>}`. Weitere Informationen und wie Sie sie mithilfe der Gremlin-Schritte neu schreiben können, finden Sie unter [A Note on Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas) (Hinweis zu Lambdas).

* ***Transaktionen** _ werden aufgrund der verteilten Natur des Systems nicht unterstützt.  Konfigurieren Sie ein geeignetes Konsistenzmodell für das Gremlin-Konto, um „Ihre eigenen Schreibvorgänge zu lesen“, und verwenden Sie optimistische Nebenläufigkeit, um Schreibvorgänge aufzulösen, zu einen Konflikt verursachen.

## <a name="known-limitations"></a>Bekannte Einschränkungen

_ **Indexverwendung für Gremlin-Abfragen mit Schritten vom Typ `.V()` während des Durchlaufs:** Aktuell wird nur beim ersten `.V()`-Aufruf eines Durchlaufs der Index genutzt, um alle angefügten Filter oder Prädikate aufzulösen. Bei späteren Aufrufen wird der Index nicht herangezogen, wodurch sich die Wartezeit und die Kosten der Abfrage erhöhen können.
    
    Assuming default indexing, a typical read Gremlin query that starts with the `.V()` step would use parameters in its attached filtering steps, such as `.has()` or `.where()` to optimize the cost and performance of the query. For example:

    ```java
    g.V().has('category', 'A')
    ```

    However, when more than one `.V()` step is included in the Gremlin query, the resolution of the data for the query might not be optimal. Take the following query as an example:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    This query will return two groups of vertices based on their property called `category`. In this case, only the first call, `g.V().has('category', 'A')` will make use of the index to resolve the vertices based on the values of their properties.

    A workaround for this query is to use subtraversal steps such as `.map()` and `union()`. This is exemplified below:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    You can review the performance of the queries by using the [Gremlin `executionProfile()` step](graph-execution-profile.md).

## <a name="next-steps"></a>Nächste Schritte

* Erste Schritte zum Erstellen einer Diagrammanwendung [mithilfe unserer SDKs](create-graph-dotnet.md) 
* Informieren Sie sich ausführlicher über die [Diagrammunterstützung](graph-introduction.md) in Azure Cosmos DB.
