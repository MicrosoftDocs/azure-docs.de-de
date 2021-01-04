---
title: Fuzzysuche
titleSuffix: Azure Cognitive Search
description: Implementieren Sie eine „Meinten Sie“-Benutzeroberfläche für die Suche, um eine Autokorrektur bereitzustellen und falsch geschriebene Begriffe oder Tippfehler zu korrigieren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: ac2690a5f18bb58c29b433f4a07e52096bbd268b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701584"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Fuzzysuche zum Korrigieren von Rechtschreibfehlern und Tippfehlern

Azure Cognitive Search unterstützt die Fuzzysuche, eine Art von Abfrage, bei der Tippfehler und falsch geschriebene Begriffe in der Eingabezeichenfolge ausgeglichen werden. Ermöglicht wird dies durch eine Suche nach Begriffen mit ähnlicher Zusammensetzung. Eine Ausweitung der Suche auf Beinahe-Übereinstimmungen hat den Effekt, dass ein Tippfehler automatisch korrigiert wird, wenn es sich bei dem Unterschied nur um ein paar falsch gesetzte Zeichen handelt. 

## <a name="what-is-fuzzy-search"></a>Was ist eine Fuzzysuche?

Es handelt sich um eine ausgeweitete Suche, die Treffer für Begriffe mit ähnlicher Zusammensetzung liefert. Bei einer Fuzzysuche erstellt die Engine (basierend auf der [Theorie deterministischer endlicher Automaten](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) ein Diagramm aus ähnlich zusammengesetzten Begriffen für alle ganzen Begriffe in der Abfrage. Wenn Ihre Abfrage beispielsweise die drei Begriffe „university of washington“ enthält, wird für jeden Begriff in der Abfrage `search=university~ of~ washington~` ein Diagramm erstellt (bei der Fuzzysuche werden Stoppwörter nicht entfernt, d. h. „of“ erhält ein Diagramm).

Das Diagramm umfasst bis zu 50 Erweiterungen oder Permutationen für jeden Begriff und erfasst sowohl richtige als auch falsche Varianten. Die Engine gibt anschließend in der Antwort die Treffer mit der höchsten Relevanz zurück. 

Für den Begriff „university“ könnten beispielsweise folgende Varianten vorhanden sein: unversty, universty, university, universe, inverse. Jedes Dokument, das eine Übereinstimmung mit den Begriffen in diesem Diagramm aufweist, wird in die Ergebnisse eingeschlossen. Im Gegensatz zu anderen Abfragen, bei denen der Text analysiert wird, um verschiedene Formen desselben Worts zu verarbeiten („Mäuse“ und „Maus“), erfolgen die Vergleiche in einer Fuzzyabfrage ohne linguistische Analyse des Textes. „Universe“ und „inverse“, die sich semantisch unterscheiden, führen beide zu einer Übereinstimmung, weil die syntaktischen Unterschiede gering sind.

Eine Übereinstimmung liegt dann vor, wenn die Abweichungen auf zwei oder weniger Bearbeitungen beschränkt sind, wobei eine Bearbeitung ein eingefügtes, gelöschtes, ersetztes oder vertauschtes Zeichen sein kann. Der Algorithmus zur Zeichenfolgenkorrektur, der das Unterschiedsmerkmal angibt, ist die Metrik [Damerau-Levenshtein-Distanz](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Diese ist beschrieben als die „minimale Anzahl von Operationen (Einfügungen, Löschungen, Ersetzungen oder Vertauschungen von zwei benachbarten Zeichen), um ein Wort in ein anderes umzuwandeln“. 

In Azure Cognitive Search:

+ Die Fuzzyabfrage bezieht sich auf ganze Begriffe, aber Sie können Ausdrücke durch UND-Konstruktionen unterstützen. So würde beispielsweise „Unviersty~ of~ „Wshington~“ mit „University of Washington“ übereinstimmen.

+ Die Standarddistanz einer Bearbeitung beträgt 2. Der Wert `~0` steht für keine Erweiterung (es wird nur der genaue Begriff als Übereinstimmung betrachtet), aber Sie könnten `~1` für eine einzelne Abweichung oder Bearbeitung angeben. 

+ Eine Fuzzysuche kann einen Begriff um bis zu 50 zusätzliche Permutationen erweitern. Dieses Limit ist nicht konfigurierbar, aber Sie können die Anzahl von Erweiterungen effizient senken, indem Sie die Bearbeitungsdistanz auf 1 verringern.

+ Antworten umfassen Dokumente, die eine relevante Übereinstimmung enthalten (bis zu 50).

Gemeinsam werden die Diagramme als Abgleichkriterium für die Token im Index übermittelt. Wie Sie sich vorstellen können, ist eine Fuzzysuche erheblich langsamer als andere Abfrageformen. Die Größe und Komplexität Ihres Index kann bestimmen, ob die Vorteile gegenüber der zeitlichen Verzögerung der Antwort überwiegen.

> [!NOTE]
> Da die Fuzzysuche tendenziell langsam ist, könnte es sich lohnen, Alternativen wie die N-Gramm-Indizierung mit Progression kurzer Zeichenfolgen (Sequenzen aus zwei und drei Zeichen für Bigramm- und Trigramm-Token) zu untersuchen. Je nach Sprache und Abfragebenutzeroberfläche erzielen Sie über ein N-Gramm möglicherweise eine bessere Leistung. Der Nachteil der N-Gramm-Indizierung liegt darin, dass sie sehr speicherintensiv ist und deutlich größere Indizes erzeugt.
>
> Wenn Sie lediglich die gröbsten Fehler behandeln möchten, wäre eine weitere mögliche Alternative eine [Synonymzuordnung](search-synonyms.md). Beispielsweise kann „search“ zu „serach“, „serch“, „sarch“ oder „retrieve“ zu „retreive“ zugeordnet werden.

## <a name="indexing-for-fuzzy-search"></a>Indizierung für die Fuzzysuche

Während der Abfrageverarbeitung zur Erstellung eines Erweiterungsdiagramms werden keine Analysefunktionen verwendet, aber das bedeutet nicht, dass solche Funktionen in Szenarien für die Fuzzysuche ignoriert werden sollten. Schließlich werden Analysefunktionen während der Indizierung zum Erstellen von Token verwendet, anhand derer ein Abgleich erfolgt – unabhängig davon, ob es sich um eine Freiformabfrage, eine gefilterte Suche oder um eine Fuzzysuche mit einem Diagramm als Eingabe handelt. 

Im Allgemeinen basiert bei der Zuweisung einer Analysefunktion auf Pro-Feld-Basis die Entscheidung zur Feinabstimmung der Analysekette auf dem primären Anwendungsfall (ein Filter oder eine Volltextsuche) und nicht auf spezialisierten Abfrageformen wie der Fuzzysuche. Aus diesem Grund gibt es keine spezifische Empfehlung für Analysefunktionen für die Fuzzysuche. 

Wenn Testabfragen jedoch nicht die von Ihnen erwarteten Übereinstimmungen liefern, könnten Sie versuchen, die Analysefunktion für die Indizierung zu variieren und beispielsweise auf eine [Sprachanalyse](index-add-language-analyzers.md) einstellen, um zu sehen, ob Sie bessere Ergebnisse erhalten. Einige Sprachen, insbesondere solche mit Vokalwechsel, können von der Flexion und den unregelmäßigen Wortformen profitieren, die von den natürlichen Sprachprozessoren von Microsoft erzeugt werden. In einigen Fällen kann die Verwendung der richtigen Sprachanalysefunktion einen Unterschied in Bezug darauf machen, ob ein Begriff in einer Weise mit einem Token versehen wird, die mit dem vom Benutzer bereitgestellten Wert kompatibel ist.

## <a name="how-to-use-fuzzy-search"></a>Verwenden der Fuzzysuche

Fuzzy-Abfragen werden unter Verwendung der vollständigen Lucene-Abfragesyntax konstruiert, wobei der [Lucene-Abfrageparser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) aufgerufen wird.

1. Legen Sie den vollständigen Lucene-Abfrageparser für die Abfrage fest (`queryType=full`).

1. Schränken Sie die Anforderung optional auf bestimmte Felder ein, indem Sie den Parameter `searchFields=<field1,field2>` verwenden. 

1. Hängen Sie den Tilden-Operator (`~`) an das Ende des ganzen Begriffes an (`search=<string>~`).

   Schließen Sie einen optionalen Parameter – standardmäßig eine Zahl zwischen 0 und 2 – ein, wenn Sie die Bearbeitungsdistanz angeben möchten (`~1`). Beispielsweise würden bei „blue~“ oder „blue~1“ die Werte „blue“, „blues“ und „glue“ zurückgegeben.

In Azure Cognitive Search können neben Begriff und Distanz (maximal 2) keine zusätzlichen Parameter für die Abfrage festgelegt werden.

> [!NOTE]
> Während der Abfrageverarbeitung erfolgt keine [lexikalische Analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) der Fuzzyabfrage. Die Abfrageeingabe wird direkt zur Abfragestruktur hinzugefügt und zur Erstellung eines Diagramms aus Begriffen erweitert. Die einzige Transformation, die durchgeführt wird, ist eine Umwandlung in Kleinbuchstaben.

## <a name="testing-fuzzy-search"></a>Testen der Fuzzysuche

Für einfache Tests empfehlen wir [Suchexplorer](search-explorer.md) oder [Postman](search-get-started-rest.md) für die Iteration über einen Abfrageausdruck. Beide Tools sind interaktiv, d. h. Sie können schnell die verschiedenen Varianten eines Begriffs durchlaufen und die zurückgegebenen Antworten auswerten.

Wenn die Ergebnisse nicht eindeutig sind, können Sie mithilfe der [Trefferhervorhebung](search-pagination-page-layout.md#hit-highlighting) die Übereinstimmung in der Antwort identifizieren. 

> [!Note]
> Die Verwendung der Trefferhervorhebung zum Identifizieren von Fuzzyübereinstimmungen ist beschränkt und funktioniert nur für die grundlegende Fuzzysuche. Wenn Ihr Index Bewertungsprofile umfasst oder wenn Sie eine zusätzliche Syntaxebene für die Abfrage einfügen, kann die Trefferhervorhebung die Übereinstimmung möglicherweise nicht identifizieren. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Beispiel 1: Fuzzysuche mit dem genauen Begriff

Angenommen, die folgende Zeichenfolge ist in einem `"Description"`-Feld eines Suchdokuments enthalten: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Beginnen Sie mit einer Fuzzysuche nach „special“, und fügen Sie die Trefferhervorhebung für das Feld „Description“ hinzu:

```console
search=special~&highlight=Description
```

In der Antwort wird auf „special“ als Überstimmung eine Formatierung angewendet, weil Sie die Trefferhervorhebung hinzugefügt haben.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Führen Sie die Abfrage erneut aus, und schreiben Sie „special“ falsch, indem Sie mehrere Buchstaben weglassen („pe“):

```console
search=scial~&highlight=Description
```

Bisher keine Änderung in der Antwort. Bei einer Distanz von 2 durch das Weglassen von zwei Zeichen („pe“) in „special“ wird weiterhin eine Übereinstimmung für den Begriff angezeigt.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Ändern Sie den Suchbegriff in einer weiteren Abfrage so ab, dass Sie das letzte Zeichen löschen („scal“ anstelle von „special“). Damit wurden drei Löschungen vorgenommen:

```console
search=scal~&highlight=Description
```

Beachten Sie, dass weiterhin dieselbe Antwort zurückgegeben wird, aber anstelle eines Treffers für „special“ lautet der Treffer jetzt „SQL“.

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

Dieses erweiterte Beispiel soll verdeutlichen, dass die Trefferhervorhebung bei mehrdeutigen Ergebnissen zu mehr Klarheit führen kann. In allen Fällen wird dasselbe Dokument zurückgegeben. Hätten Sie sich bei der Überprüfung der Übereinstimmung auf die Dokument-IDs verlassen, hätten Sie vielleicht den Wechsel von „special“ zu „SQL“ nicht bemerkt.

## <a name="see-also"></a>Weitere Informationen

+ [Funktionsweise der Volltextsuche in der kognitiven Azure-Suche (Architektur für Abfrageanalyse)](search-lucene-query-architecture.md)
+ [Suchexplorer](search-explorer.md)
+ [Abfragen des Azure Search-Index mit dem .NET SDK](./search-get-started-dotnet.md)
+ [Abfragen des Azure Search-Index mit der REST-API](./search-get-started-powershell.md)