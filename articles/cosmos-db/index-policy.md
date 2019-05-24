---
title: Indizierungsrichtlinien für Azure Cosmos DB
description: In diesem Artikel werden das Konfigurieren und Ändern der Standardindizierungsrichtlinie zur automatischen Indizierung und zur Steigerung der Leistung in Azure Cosmos DB erläutert.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: thweiss
ms.openlocfilehash: c45beb3ed6f87e95d171e2299c533b4be2827f27
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954036"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indizierungsrichtlinien in Azure Cosmos DB

In Azure Cosmos DB verfügt jeder Container über eine Indizierungsrichtlinie, die bestimmt, wie die Elemente eines Containers indiziert werden. Die standardmäßige Indizierungsrichtlinie für neu erstellte Container indiziert sämtliche Eigenschaften jedes Elements und erzwingt Bereichsindizes für jede Zeichenfolge oder Zahl und räumliche Indizes für jedes GeoJSON-Objekt vom Typ „Punkt“. Dadurch erzielen Sie eine hohe Abfrageleistung, ohne sich im Vorfeld Gedanken über die Indizierung und Indexverwaltung machen zu müssen.

In einigen Fällen ist eventuell sinnvoll, dieses automatische Verhalten besser an Ihre Anforderungen anzupassen. Sie können die Indizierungsrichtlinie eines Containers anpassen, indem Sie seinen *Indizierungsmodus* festlegen und *Eigenschaftenpfade* ein- oder ausschließen.

## <a name="indexing-mode"></a>Indizierungsmodus

Azure Cosmos DB unterstützt zwei Indizierungsmodi:

- **Konsistent:** Wenn die Indizierungsrichtlinie eines Containers auf „Konsistent“ festgelegt ist, wird der Index synchron aktualisiert, wenn Sie Elemente erstellen, aktualisieren oder löschen. Damit entspricht die Konsistenz Ihrer Leseabfragen der [für das Konto konfigurierten Konsistenz](consistency-levels.md).

- **Keine:** Wenn die Indizierungsrichtlinie eines Containers auf „Keine“ festgelegt wird, ist die Indizierung für diesen Container praktisch deaktiviert. Dies wird häufig verwendet, wenn ein Container als reiner Schlüssel-Wert-Speicher verwendet wird, für den keine sekundären Indizes erforderlich sind. Dies kann auch Masseneinfügevorgänge beschleunigen.

## <a name="including-and-excluding-property-paths"></a>Ein- und Ausschließen von Eigenschaftenpfaden

Mit einer benutzerdefinierten Indizierungsrichtlinie können Eigenschaftenpfade angegeben werden, die explizit in die Indizierung eingeschlossen oder von ihr ausgeschlossen werden. Durch das Optimieren der Anzahl der Pfade, die indiziert werden, können Sie das Speichervolumen, das von Ihrem Container verwendet wird, und die Latenz von Schreibvorgängen verringern. Diese Pfade werden anhand [der Methode, die im Übersichtsabschnitt zur Indizierung beschrieben wird](index-overview.md#from-trees-to-property-paths), definiert. Dabei gelten die folgenden Ergänzungen:

- Ein Pfad zu einem Skalarwert (Zeichenfolge oder Zahl) endet auf `/?`.
- Elemente aus einem Array werden über die `/[]`-Notation (anstelle von `/0`, `/1` usw.) adressiert.
- Der Platzhalter `/*` kann verwendet werden, um alle Elemente unter einem Knoten einzufügen.

Betrachten wir dasselbe Beispiel erneut:

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

- Der `employees`-Pfad von `headquarters` lautet `/headquarters/employees/?`.
- Der `country`-Pfad von `locations` lautet `/locations/[]/country/?`.
- Der Pfad zu allen Elementen unter `headquarters` lautet `/headquarters/*`.

Wenn ein Pfad explizit in eine Indizierungsrichtlinie eingeschlossen wird, muss diese auch definieren, welche Indextypen auf diesen Pfad angewandt werden. Außerdem muss für jeden Index angegeben werden, für welchen Datentyp dieser Index gilt:

| Indextyp | Zulässige Zieldatentypen |
| --- | --- |
| Range | Zeichenfolge oder Zahl |
| Spatial | Point, LineString oder Polygon |

Wir könnten beispielsweise den Pfad `/headquarters/employees/?` einschließen und angeben, dass ein `Range`-Index auf diesen Pfad für die beiden Werte `String` und `Number` angewandt werden soll.

### <a name="includeexclude-strategy"></a>Strategie für das Ein- und Ausschließen

Jede Indizierungsrichtlinie muss den Stammpfad `/*` entweder als eingeschlossenen oder als ausgeschlossenen Pfad enthalten.

- Schließen Sie den Stammpfad ein, um einzelne Pfade auszuschließen, die nicht indiziert werden müssen. Dies ist die empfohlene Vorgehensweise, da dabei Azure Cosmos DB proaktiv jede neue Eigenschaft indiziert, die dem Modell hinzugefügt wird.
- Schließen Sie den Stammpfad aus, um einzelne Pfade einzuschließen, die indiziert werden müssen.

- Für Pfade mit regulären Zeichen, die alphanumerische Zeichen und Unterstriche (_) enthalten, müssen Sie die Pfadzeichenkette nicht mit doppelten Anführungszeichen als Escapezeichen umgeben (z.B. "/Pfad/?"). Für Pfade mit anderen Sonderzeichen müssen Sie die Pfadzeichenkette mit doppelten Anführungszeichen als Escapezeichen umgeben (z.B. "/\"Pfad-abc\"/?"). Wenn Sie Sonderzeichen in Ihrem Pfad erwarten, können Sie aus Sicherheitsgründen jeden Pfad mit Escapezeichen umgeben. Funktionell macht es keinen Unterschied, ob Sie jedem Pfad mit Escapezeichen umgeben oder nur diejenigen mit Sonderzeichen.

In [diesem Abschnitt](how-to-manage-indexing-policy.md#indexing-policy-examples) finden Sie Beispiele für Indizierungsrichtlinien.

## <a name="composite-indexes"></a>Zusammengesetzte Indizes

`ORDER BY`-Abfragen von zwei oder mehr Eigenschaften erfordern einen zusammengesetzten Index. Derzeit werden zusammengesetzte Indizes nur von Abfragen mit mehreren `ORDER BY`-Klauseln verwendet. Standardmäßig sind keine zusammengesetzten Indizes definiert, weshalb Sie [zusammengesetzte Indizes ](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) je nach Bedarf hinzufügen müssen.

Beim Definieren eines zusammengesetzten Indexes geben Sie Folgendes an:

- Zwei oder mehr Eigenschaftspfade. Die Reihenfolge, in der Eigenschaftspfade definiert sind, ist entscheidend.
- Die Reihenfolge (aufsteigend oder absteigend).

Die folgenden Aspekte werden bei der Verwendung zusammengesetzter Indizes berücksichtigt:

- Wenn die zusammengesetzten Indexpfade nicht mit der Reihenfolge der Eigenschaften in der ORDER BY-Klausel übereinstimmen, kann der zusammengesetzte Index die Abfrage nicht unterstützen.

- Die Reihenfolge der zusammengesetzten Indexpfade (aufsteigend oder absteigend) muss auch mit der Reihenfolge in der ORDER BY-Klausel übereinstimmen.

- Der zusammengesetzte Index unterstützt auch eine ORDER BY-Klausel mit umgekehrter Reihenfolge in allen Pfaden.

Betrachten Sie das folgende Beispiel, bei dem ein zusammengesetzter Index für die Eigenschaften a, b und c definiert ist:

| **Zusammengesetzter Index**     | **Beispiel einer `ORDER BY`-Abfrage**      | **Vom Index unterstützt?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, bcasc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

Sie müssen Ihre Indizierungsrichtlinie anpassen, damit Sie alle notwendigen `ORDER BY`-Abfragen durchführen können.

## <a name="modifying-the-indexing-policy"></a>Ändern der Indizierungsrichtlinie

Die Indizierungsrichtlinie eines Containers kann jederzeit [im Azure-Portal oder mit einem der unterstützten SDKs](how-to-manage-indexing-policy.md) aktualisiert werden. Die Aktualisierung einer Indizierungsrichtlinie löst eine Transformation vom alten Index auf den neuen aus. Dies erfolgt online und direkt (sodass während des Vorgangs kein zusätzlicher Speicherplatz verbraucht wird). Der Index der alten Richtlinie wird effizient anhand der neuen Richtlinie transformiert, ohne die Schreibverfügbarkeit oder den Durchsatz, der für den Container bereitgestellt wird, zu beeinträchtigen. Die Indextransformation ist ein asynchroner Vorgang. Der erforderliche Zeitaufwand hängt vom bereitgestellten Durchsatz, der Anzahl der Elemente und ihrer Größe ab. 

> [!NOTE]
> Während der Neuindizierung geben Abfragen möglicherweise nicht alle übereinstimmenden Ergebnisse zurück und geben dabei keinen Fehler zurück. Dies bedeutet, dass die Abfrageergebnisse möglicherweise bis zum Abschluss der Transformation nicht konsistent sind. Es ist möglich, den Fortschritt der Indextransformation [mit einem der SDKs](how-to-manage-indexing-policy.md) zu verfolgen.

Wenn der Modus der neuen Indizierungsrichtlinie auf „Konsistent“ festgelegt wurde, kann während der Ausführung der Indextransformation keine andere Änderung an der Indizierungsrichtlinie angewandt werden. Sie können eine aktuell ausgeführte Indextransformation abbrechen, indem Sie den Modus der Indizierungsrichtlinie auf „Keine“ festlegen (der Index wird direkt gelöscht).

## <a name="indexing-policies-and-ttl"></a>Indizierungsrichtlinien und Gültigkeitsdauer

Für die Funktion [Gültigkeitsdauer](time-to-live.md) (Time-to-Live, TTL) muss die Indizierung für den Container aktiviert sein. Dies bedeutet Folgendes:

- Es ist nicht möglich, die Gültigkeitsdauer (TTL) für einen Container zu aktivieren, wenn dessen Indizierungsmodus auf „Keine“ festgelegt ist.
- Es ist nicht möglich, den Indizierungsmodus für einen Container, in dem die Gültigkeitsdauer (TTL) aktiviert ist, auf „Keine“ festzulegen.

Für Szenarien, in denen kein Eigenschaftenpfad indiziert werden muss, aber die Gültigkeitsdauer (TTL) erforderlich ist, können Sie eine Indizierungsrichtlinie mit Folgendem verwenden:

- Der Indizierungsmodus ist auf „Konsistent“ festgelegt.
- Es wurde kein Pfad eingeschlossenen.
- `/*` ist der einzige ausgeschlossene Pfad.

## <a name="obsolete-attributes"></a>Veraltete Attribute

Bei der Arbeit mit Indizierungsrichtlinien stoßen Sie möglicherweise auf die folgenden Attribute, die mittlerweile veraltet sind:

- `automatic` ist ein boolescher Wert, der im Stamm einer Indizierungsrichtlinie definiert wird. Er wird nun ignoriert und kann auf `true` festgelegt werden, wenn das von Ihnen verwendete Tool dies erfordert.
- `precision` ist eine Zahl, die auf der Indexebene für eingeschlossene Pfade definiert wird. Sie wird nun ignoriert und kann auf `-1` festgelegt werden, wenn das von Ihnen verwendete Tool dies erfordert.
- `hash` ist ein Indextyp, der durch den Range-Typ ersetzt wurde.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Indizierung erhalten Sie in den folgenden Artikeln:

- [Übersicht über die Indizierung](index-overview.md)
- [Gewusst wie: Verwalten der Indizierungsrichtlinie](how-to-manage-indexing-policy.md)
