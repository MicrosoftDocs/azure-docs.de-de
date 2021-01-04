---
title: Indizierungsrichtlinien für Azure Cosmos DB
description: In diesem Artikel werden das Konfigurieren und Ändern der Standardindizierungsrichtlinie zur automatischen Indizierung und zur Steigerung der Leistung in Azure Cosmos DB erläutert.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: tisande
ms.openlocfilehash: 2d99e0e2b65f7131e564e6ab64e454d2947c58a6
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903019"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indizierungsrichtlinien in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB verfügt jeder Container über eine Indizierungsrichtlinie, die bestimmt, wie die Elemente eines Containers indiziert werden. Die standardmäßige Indizierungsrichtlinie für neu erstellte Container indiziert sämtliche Eigenschaften jedes Elements und erzwingt Bereichsindizes für jede Zeichenfolge oder Zahl. Dadurch erzielen Sie eine gute Abfrageleistung, ohne sich im Vorfeld Gedanken über die Indizierung und Indexverwaltung machen zu müssen.

In einigen Fällen ist eventuell sinnvoll, dieses automatische Verhalten besser an Ihre Anforderungen anzupassen. Sie können die Indizierungsrichtlinie eines Containers anpassen, indem Sie seinen *Indizierungsmodus* festlegen und *Eigenschaftenpfade* ein- oder ausschließen.

> [!NOTE]
> Die in diesem Artikel beschriebene Methode zur Aktualisierung von Indizierungsrichtlinien gilt nur für die SQL (Core)-API von Azure Cosmos DB. Informationen zum Indizieren finden Sie unter [Verwalten der Indizierung in der Azure Cosmos DB-API für MongoDB](mongodb-indexing.md).

## <a name="indexing-mode"></a>Indizierungsmodus

Azure Cosmos DB unterstützt zwei Indizierungsmodi:

- **Konsistent:** Der Index wird synchron aktualisiert, wenn Sie Elemente erstellen, aktualisieren oder löschen. Damit entspricht die Konsistenz Ihrer Leseabfragen der [für das Konto konfigurierten Konsistenz](consistency-levels.md).
- **Keine:** Die Indizierung ist für den Container deaktiviert. Dies wird häufig verwendet, wenn ein Container als reiner Schlüssel-Wert-Speicher verwendet wird, für den keine sekundären Indizes erforderlich sind. Sie kann auch verwendet werden, um die Leistung von Massenvorgängen zu verbessern. Nach Abschluss der Massenvorgänge kann der Indexmodus auf „Konsistent“ festgelegt und dann mit [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) überwacht werden, bis er abgeschlossen ist.

> [!NOTE]
> Azure Cosmos DB unterstützt auch einen verzögerten Indizierungsmodus. Bei der verzögerten Indizierung werden Updates des Indexes mit einer wesentlich niedrigeren Prioritätsstufe ausgeführt, wenn die Engine keine andere Arbeit ausführt. Dies kann zu **inkonsistenten oder unvollständigen** Abfrageergebnissen führen. Wenn Sie beabsichtigen, einen Cosmos-Container abzufragen, sollten Sie nicht die verzögerte Indizierung verwenden. Für neue Container kann keine verzögerte Indizierung ausgewählt werden. Sie können eine Ausnahme beantragen, indem Sie sich an den [Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) wenden (außer wenn Sie ein Azure Cosmos-Konto im [serverlosen](serverless.md) Modus verwenden, der keine verzögerte Indizierung unterstützt).

Diese Indizierungsrichtlinie ist standardmäßig auf `automatic` festgelegt. Hierzu wird die `automatic`-Eigenschaft der Indizierungsrichtlinie auf `true` festgelegt. Ist diese Eigenschaft auf `true` festgelegt, kann Azure Cosmos DB Dokumente automatisch indizieren, während sie geschrieben werden.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Ein- und Ausschließen von Eigenschaftenpfaden

Mit einer benutzerdefinierten Indizierungsrichtlinie können Eigenschaftenpfade angegeben werden, die explizit in die Indizierung eingeschlossen oder von ihr ausgeschlossen werden. Indem Sie die Anzahl der indizierten Pfade optimieren, können Sie die Wartezeit und die RU-Gebühr für Schreibvorgänge erheblich verringern. Diese Pfade werden anhand [der Methode, die im Übersichtsabschnitt zur Indizierung beschrieben wird](index-overview.md#from-trees-to-property-paths), definiert. Dabei gelten die folgenden Ergänzungen:

- Ein Pfad zu einem Skalarwert (Zeichenfolge oder Zahl) endet auf `/?`.
- Elemente aus einem Array werden über die `/[]`-Notation (anstelle von `/0`, `/1` usw.) adressiert.
- Der Platzhalter `/*` kann verwendet werden, um alle Elemente unter einem Knoten einzufügen.

Betrachten wir dasselbe Beispiel erneut:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- Der `employees`-Pfad von `headquarters` lautet `/headquarters/employees/?`.

- Der `country`-Pfad von `locations` lautet `/locations/[]/country/?`.

- Der Pfad zu allen Elementen unter `headquarters` lautet `/headquarters/*`.

Wir könnten beispielsweise den Pfad `/headquarters/employees/?` einschließen. Dadurch könnten wir sicherstellen, dass die Eigenschaft „employees“ indiziert wird, aber kein zusätzlicher geschachtelter JSON-Code innerhalb dieser Eigenschaft.

## <a name="includeexclude-strategy"></a>Strategie für das Ein- und Ausschließen

Jede Indizierungsrichtlinie muss den Stammpfad `/*` entweder als eingeschlossenen oder als ausgeschlossenen Pfad enthalten.

- Schließen Sie den Stammpfad ein, um einzelne Pfade auszuschließen, die nicht indiziert werden müssen. Dies ist die empfohlene Vorgehensweise, da dabei Azure Cosmos DB proaktiv jede neue Eigenschaft indiziert, die dem Modell hinzugefügt wird.
- Schließen Sie den Stammpfad aus, um einzelne Pfade einzuschließen, die indiziert werden müssen.

- Für Pfade mit regulären Zeichen, die alphanumerische Zeichen und Unterstriche (_) enthalten, müssen Sie die Pfadzeichenkette nicht mit doppelten Anführungszeichen als Escapezeichen umgeben (z.B. "/Pfad/?"). Für Pfade mit anderen Sonderzeichen müssen Sie die Pfadzeichenkette mit doppelten Anführungszeichen als Escapezeichen umgeben (z.B. "/\"Pfad-abc\"/?"). Wenn Sie Sonderzeichen in Ihrem Pfad erwarten, können Sie aus Sicherheitsgründen jeden Pfad mit Escapezeichen umgeben. Funktionell macht es keinen Unterschied, ob Sie jeden Pfad mit Escapezeichen umgeben oder nur diejenigen mit Sonderzeichen.

- Die Systemeigenschaft `_etag` wird von der Indizierung standardmäßig ausgeschlossen, sofern sie nicht zum für die Indizierung eingeschlossenen Pfad hinzugefügt wird.

- Wenn der Indizierungsmodus auf **Konsistent** festgelegt ist, werden die Systemeigenschaften `id` und `_ts` automatisch indiziert.

Beim Ein- und Ausschließen von Pfaden begegnen Ihnen unter Umständen folgende Attribute:

- `kind` kann entweder `range` oder `hash` sein. Die Unterstützung von Hashindizes ist auf Gleichheitsfilter eingeschränkt. Die Bereichsindexfunktionalität bietet die gesamte Funktionalität von Hashindizes sowie effiziente Sortierung, Bereichsfilter und Systemfunktionen. Es wird immer empfohlen, einen Bereichsindex zu verwenden.

- `precision` ist eine Zahl, die auf der Indexebene für eingeschlossene Pfade definiert wird. Der Wert `-1` steht für maximale Genauigkeit. Es wird empfohlen, diesen Wert immer auf `-1` festzulegen.

- `dataType` kann entweder `String` oder `Number` sein. Hiermit werden die Arten von JSON-Eigenschaften angegeben, die indiziert werden.

Ohne Angabe haben diese Eigenschaften die folgenden Standardwerte:

| **Eigenschaftenname**     | **Standardwert** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` und `Number` |

Exemplarische Indizierungsrichtlinien zum Ein- und Ausschließen von Pfaden finden Sie in [diesem Abschnitt](how-to-manage-indexing-policy.md#indexing-policy-examples).

## <a name="includeexclude-precedence"></a>Vorrang beim Einschließen/Ausschließen

Wenn es bei Ihren eingeschlossenen und ausgeschlossenen Pfaden zu einem Konflikt kommt, hat der genauere Pfad Vorrang.

Hier sehen Sie ein Beispiel:

**Eingeschlossener Pfad:** `/food/ingredients/nutrition/*`

**Ausgeschlossener Pfad:** `/food/ingredients/*`

In diesem Fall hat der eingeschlossene Pfad Vorrang vor dem ausgeschlossenen Pfad, da dieser präziser ist. Basierend auf diesen Pfaden würden alle Daten im `food/ingredients`-Pfad oder Daten, die darin geschachtelt sind, vom Index ausgeschlossen werden. Die Ausnahme wären Daten innerhalb des eingeschlossenen Pfads `/food/ingredients/nutrition/*`. Diese würden indiziert werden.

Hier finden Sie einige Regeln für den Vorrang bei eingeschlossenen und ausgeschlossenen Pfaden in Azure Cosmos DB:

- Tiefere Pfade sind präziser als enger gefasste Pfade. Beispielsweise ist `/a/b/?` präziser als `/a/?`.

- `/?` ist präziser als `/*`. `/a/?` ist beispielsweise präziser als `/a/*`, `/a/?` hat also Vorrang.

- Der Pfad `/*` muss entweder ein eingeschlossener oder ein ausgeschlossener Pfad sein.

## <a name="spatial-indexes"></a>Räumlichkeitsindizes

Wenn Sie in der Indizierungsrichtlinie einen räumlichen Pfad definieren, müssen Sie definieren, welche Art (```type```) von Index auf diesen Pfad angewendet werden soll. Für räumliche Indizes stehen folgende Arten zur Verfügung:

* Point

* Polygon

* MultiPolygon

* LineString

Azure Cosmos DB erstellt standardmäßig keine räumlichen Indizes. Wenn Sie integrierte räumliche SQL-Funktionen verwenden möchten, müssen Sie einen räumlichen Index für die erforderlichen Eigenschaften erstellen. Exemplarische Indizierungsrichtlinien zum Hinzufügen räumlicher Indizes finden Sie in [diesem Abschnitt](sql-query-geospatial-index.md).

## <a name="composite-indexes"></a>Zusammengesetzte Indizes

Für Abfragen, die eine `ORDER BY`-Klausel mit zwei oder mehr Eigenschaften besitzen, ist ein zusammengesetzter Index erforderlich. Ein zusammengesetzter Index kann auch definiert werden, um die Leistung vieler Gleichheits- und Bereichsabfragen zu verbessern. Standardmäßig sind keine zusammengesetzten Indizes definiert, weshalb Sie [zusammengesetzte Indizes ](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) je nach Bedarf hinzufügen müssen.

Im Gegensatz zu eingeschlossenen oder ausgeschlossenen Pfaden können Sie keinen Pfad mit dem `/*`-Platzhalter erstellen. Jeder zusammengesetzte Pfad muss am Ende des Pfads implizit `/?` aufweisen, was nicht angegeben werden muss. Zusammengesetzte Pfade führen zu einem Skalarwert, und dieser ist der einzige Wert, der im zusammengesetzten Index eingeschlossen wird.

Beim Definieren eines zusammengesetzten Indexes geben Sie Folgendes an:

- Zwei oder mehr Eigenschaftspfade. Die Reihenfolge, in der Eigenschaftspfade definiert sind, ist entscheidend.

- Die Reihenfolge (aufsteigend oder absteigend).

> [!NOTE]
> Wenn Sie einen zusammengesetzten Index hinzufügen, werden in der Abfrage vorhandene Bereichsindizes genutzt, bis das Hinzufügen des neuen zusammengesetzten Index abgeschlossen ist. Daher kann es sein, dass beim Hinzufügen eines zusammengesetzten Index nicht sofort Leistungsverbesserungen erkennbar sind. Es ist möglich, den Fortschritt der Indextransformation [mit einem der SDKs](how-to-manage-indexing-policy.md) zu verfolgen.

### <a name="order-by-queries-on-multiple-properties"></a>ORDER BY-Abfragen für mehrere Eigenschaften:

Bei Verwendung zusammengesetzter Indizes für Abfragen, die eine `ORDER BY`-Klausel mit zwei oder mehr Eigenschaften besitzen, muss Folgendes berücksichtigt werden:

- Wenn die Pfade des zusammengesetzten Index nicht mit der Reihenfolge der Eigenschaften in der `ORDER BY`-Klausel übereinstimmen, kann der zusammengesetzte Index die Abfrage nicht unterstützen.

- Die Reihenfolge der Pfade des zusammengesetzten Index (aufsteigend oder absteigend) muss auch mit der Reihenfolge (`order`) in der `ORDER BY`-Klausel übereinstimmen.

- Der zusammengesetzte Index unterstützt auch eine `ORDER BY`-Klausel mit umgekehrter Reihenfolge für alle Pfade.

Betrachten Sie das folgende Beispiel, in dem ein zusammengesetzter Index für die Eigenschaften „name“, „age“, und „_ts“ definiert wird:

| **Zusammengesetzter Index**     | **Beispiel einer `ORDER BY`-Abfrage**      | **Unterstützung durch zusammengesetzten Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Sie müssen Ihre Indizierungsrichtlinie anpassen, damit Sie alle notwendigen `ORDER BY`-Abfragen durchführen können.

### <a name="queries-with-filters-on-multiple-properties"></a>Abfragen mit Filtern für mehrere Eigenschaften

Wenn eine Abfrage Filter für zwei oder mehr Eigenschaften enthält, kann es hilfreich sein, einen zusammengesetzten Index für diese Eigenschaften zu erstellen.

Sehen Sie sich beispielsweise die folgende Abfrage an, die über einen Gleichheitsfilter für zwei Eigenschaften verfügt:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Diese Abfrage ist effizienter, schneller und beansprucht weniger RUs, wenn sie einen zusammengesetzten Index für „(name ASC, age ASC)“ nutzen kann.

Abfragen mit Bereichsfiltern können ebenfalls mit einem zusammengesetzten Index optimiert werden. Allerdings kann die Abfrage nur einen einzelnen Bereichsfilter enthalten. Zu Bereichsfiltern zählen `>`, `<`, `<=`, `>=` und `!=`. Der Bereichsfilter muss im zusammengesetzten Index zuletzt definiert werden.

Die folgende Abfrage enthält sowohl Gleichheits- als auch Bereichsfilter:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Diese Abfrage ist mit einem zusammengesetzten Index für „(name ASC, age ASC)“ effizienter. Die Abfrage würde jedoch keinen zusammengesetzten Index für „(age ASC, name ASC)“ verwenden, da die Gleichheitsfilter im zusammengesetzten Index zuerst definiert werden müssen.

Bei der Erstellung zusammengesetzter Indizes für Abfragen mit Filtern für mehrere Eigenschaften muss Folgendes berücksichtigt werden:

- Die Eigenschaften im Filter der Abfrage müssen den Eigenschaften im zusammengesetzten Index entsprechen. Ist eine Eigenschaft im zusammengesetzten Index, aber nicht als Filter in der Abfrage enthalten, wird der zusammengesetzte Index von der Abfrage nicht verwendet.
- Enthält der Filter einer Abfrage zusätzliche Eigenschaften, die nicht in einem zusammengesetzten Index definiert wurden, wird zur Auswertung der Abfrage eine Kombination aus zusammengesetztem Index und Bereichsindex verwendet. Dadurch werden weniger RUs beansprucht als bei ausschließlicher Verwendung von Bereichsindizes.
- Wenn eine Eigenschaft einen Bereichsfilter besitzt (`>`, `<`, `<=`, `>=` oder `!=`), muss diese Eigenschaft im zusammengesetzten Index zuletzt definiert werden. Enthält eine Abfrage mehrere Bereichsfilter, wird der zusammengesetzte Index nicht verwendet.
- Wenn Sie einen zusammengesetzten Index erstellen, um Abfragen mit mehreren Filtern zu optimieren, hat die Reihenfolge (`ORDER`) des zusammengesetzten Index keine Auswirkungen auf die Ergebnisse. Diese Eigenschaft ist optional.
- Wenn Sie keinen zusammengesetzten Index für eine Abfrage mit Filtern für mehrere Eigenschaften definieren, wird die Abfrage trotzdem erfolgreich ausgeführt. Mit einem zusammengesetzten Index beansprucht die Abfrage allerdings weniger RUs.
- Abfragen mit Aggregaten (z. B. COUNT oder SUM) und Filtern profitieren ebenfalls von zusammengesetzten Indizes.
- Filterausdrücke können mehrere zusammengesetzte Indizes verwenden.

Betrachten Sie die folgenden Beispiele, in denen ein zusammengesetzter Index für die Eigenschaften „name“, „age“, und „timestamp“ definiert wird:

| **Zusammengesetzter Index**     | **Beispielabfrage**      | **Unterstützung durch zusammengesetzten Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |
| ```(name ASC, age ASC) and (name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp > 123049923``` | ```Yes```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Abfragen mit Filter und ORDER BY-Klausel

Wenn eine Abfrage nach einer oder mehreren Eigenschaften filtert und verschiedene Eigenschaften in der ORDER BY-Klausel enthält, kann es hilfreich sein, die Eigenschaften im Filter der `ORDER BY`-Klausel hinzuzufügen.

Durch Hinzufügen der Eigenschaften im Filter zur ORDER BY-Klausel kann beispielsweise die folgende Abfrage umgeschrieben werden, um einen zusammengesetzten Index zu nutzen:

Abfrage mit Bereichsindex:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Abfrage mit zusammengesetztem Index:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Das gleiche Muster und die gleichen Abfrageoptimierungen können für Abfragen mit mehreren Gleichheitsfiltern verallgemeinert werden:

Abfrage mit Bereichsindex:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Abfrage mit zusammengesetztem Index:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Bei der Erstellung zusammengesetzter Indizes für die Optimierung einer Abfrage mit Filter und `ORDER BY`-Klausel muss Folgendes berücksichtigt werden:

* Wenn die Abfrage nach Eigenschaften filtert, müssen diese in der `ORDER BY`-Klausel zuerst angegeben werden.
* Wenn die Abfrage nach mehreren Eigenschaften filtert, müssen die Gleichheitsfilter die ersten Eigenschaften in der `ORDER BY`-Klausel sein.
* Wenn Sie für eine Abfrage mit einem Filter für eine Eigenschaft und einer separaten `ORDER BY`-Klausel mit einer anderen Eigenschaft keinen zusammengesetzten Index definieren, wird die Abfrage trotzdem erfolgreich ausgeführt. Mit einem zusammengesetzten Index beansprucht die Abfrage allerdings weniger RUs – insbesondere, wenn die Eigenschaft in der `ORDER BY`-Klausel eine hohe Kardinalität besitzt.
* Die Überlegungen im Zusammenhang mit der Erstellung zusammengesetzter Indizes für `ORDER BY`-Abfragen mit mehreren Eigenschaften sowie für Abfragen mit Filtern für mehrere Eigenschaften gelten weiterhin.


| **Zusammengesetzter Index**                      | **Beispiel einer `ORDER BY`-Abfrage**                                  | **Unterstützung durch zusammengesetzten Index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Ändern der Indizierungsrichtlinie

Die Indizierungsrichtlinie eines Containers kann jederzeit [im Azure-Portal oder mit einem der unterstützten SDKs](how-to-manage-indexing-policy.md) aktualisiert werden. Die Aktualisierung einer Indizierungsrichtlinie löst eine Transformation vom alten Index auf den neuen aus. Dies erfolgt online und direkt (sodass während des Vorgangs kein zusätzlicher Speicherplatz verbraucht wird). Die alte Indizierungsrichtlinie wird effizient in die neue Richtlinie transformiert, ohne die Schreibverfügbarkeit, Leseverfügbarkeit oder den Durchsatz, der für den Container bereitgestellt wird, zu beeinträchtigen. Die Indextransformation ist ein asynchroner Vorgang. Der erforderliche Zeitaufwand hängt vom bereitgestellten Durchsatz, der Anzahl der Elemente und ihrer Größe ab.

> [!IMPORTANT]
> Indextransformation ist ein Vorgang, der [Anforderungseinheiten](request-units.md) verbraucht. Von einer Indextransformation verbrauchte Anforderungseinheiten werden derzeit nicht in Rechnung gestellt, wenn Sie [serverlose](serverless.md) Container verwenden. Diese Anforderungseinheiten werden in Rechnung gestellt, sobald serverlos allgemein verfügbar wird.

> [!NOTE]
> Es ist möglich, den Fortschritt der Indextransformation [mit einem der SDKs](how-to-manage-indexing-policy.md) zu verfolgen.

Es gibt keine Auswirkung auf die Schreibverfügbarkeit während der Indextransformationen. Die Indextransformation verwendet Ihre bereitgestellten RUs, allerdings mit einer niedrigeren Priorität als die CRUD-Vorgänge oder -Abfragen.

Das Hinzufügen eines neuen Indexes hat keine Auswirkung auf die Leseverfügbarkeit. Abfragen verwenden neue Indizes erst dann, wenn die Indextransformation abgeschlossen ist. Während der Indextransformation werden von der Abfrage-Engine weiterhin vorhandene Indizes verwendet, sodass Sie während der Indextransformation eine ähnliche Leseleistung beobachten werden wie vor dem Einleiten der Indexänderung. Beim Hinzufügen neuer Indizes besteht auch kein Risiko, unvollständige oder inkonsistente Abfrageergebnisse zu erhalten.

Beim Entfernen von Indizes und beim sofortigen Ausführen von Abfragen, die nach den gelöschten Indizes filtern, gibt es keine Garantie für konsistente oder komplette Abfrageergebnisse. Wenn Sie im Zuge einer einzigen Indizierungsrichtlinienänderung mehrere Indizes entfernen, stellt die Abfrage-Engine konsistente und vollständige Ergebnisse im Rahmen der Indextransformation bereit. Wenn Sie Indizes jedoch über mehrere Indizierungsrichtlinienänderungen entfernen, stellt die Abfrage-Engine keine konsistenten oder vollständigen Ergebnisse bereit, bis alle Indextransformationen abgeschlossen sind. Die meisten Entwickler löschen keine Indizes und versuchen dann sofort, Abfragen auszuführen, die diese Indizes verwenden, sodass diese Situation in der Praxis eher unwahrscheinlich ist.

> [!NOTE]
> Sie sollten immer versuchen, mehrere Indizierungsänderungen zu einer einzelnen Indizierungsrichtlinienänderung zu gruppieren.

## <a name="indexing-policies-and-ttl"></a>Indizierungsrichtlinien und Gültigkeitsdauer

Die Verwendung des [TTL-Features (Time-to-Live)](time-to-live.md) erfordert die Indizierung. Dies bedeutet Folgendes:

- Es ist nicht möglich, die TTL für einen Container zu aktivieren, wenn dessen Indizierungsmodus auf `none` festgelegt ist.
- Es ist nicht möglich, den Indizierungsmodus für einen Container, in dem die TTL aktiviert ist, auf „Keine“ festzulegen.

Für Szenarien, in denen kein Eigenschaftenpfad indiziert werden muss, aber die TTL erforderlich ist, können Sie eine Indizierungsrichtlinie verwenden, bei der der Indizierungsmodus auf `consistent` festgelegt ist, keine Pfade eingeschlossen sind und `/*` der einzige ausgeschlossene Pfad ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Indizierung erhalten Sie in den folgenden Artikeln:

- [Übersicht über die Indizierung](index-overview.md)
- [Gewusst wie: Verwalten der Indizierungsrichtlinie](how-to-manage-indexing-policy.md)
