---
title: Architektur der Engine für die Volltextsuche und Indizierung (Lucene)
titleSuffix: Azure Cognitive Search
description: Beschreibung der Konzepte für die Lucene-Abfrageverarbeitung und den Dokumentabruf für die Volltextsuche bei der kognitiven Azure-Suche.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 50a1656fcb92d9777d4a9476ef2a4c1fd2f2efc6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002747"
---
# <a name="full-text-search-in-azure-cognitive-search"></a>Volltextsuche in Azure Cognitive Search

Dieser Artikel ist für Entwickler bestimmt, die eingehendere Informationen zur Funktionsweise der Lucene-Volltextsuche in der kognitiven Azure-Suche benötigen. Für Textabfragen werden die erwarteten Ergebnisse in der kognitiven Azure-Suche in den meisten Szenarien problemlos bereitgestellt, aber es kann auch vorkommen, dass Sie ein unerwartetes Ergebnis erhalten. In diesen Situationen können Kenntnisse der vier Phasen der Lucene-Abfragenausführung (Abfrageanalyse, lexikalische Analyse, Dokumentabgleich, Bewertung) hilfreich beim Identifizieren von bestimmten Änderungen von Abfrageparametern oder der Indexkonfiguration sein, auf denen das gewünschte Ergebnis basiert. 

> [!Note] 
> Bei der kognitiven Azure-Suche wird Lucene für die Volltextsuche eingesetzt, die Lucene-Integration deckt jedoch nicht alles ab. Wir machen die Lucene-Funktionalität wahlweise verfügbar und erweitern sie, um die für die kognitive Azure-Suche wichtigen Szenarien zu ermöglichen. 

## <a name="architecture-overview-and-diagram"></a>Architektur – Übersicht und Diagramm

Die Verarbeitung einer Abfrage der Volltextsuche beginnt mit der Analyse des Abfragetexts, um die Suchbegriffe zu extrahieren. Für das Suchmodul wird ein Index verwendet, um Dokumente mit übereinstimmenden Begriffen abzurufen. Einzelne Abfrageausdrücke können in bestimmten Fällen auch aufgeteilt und neu zusammengesetzt werden, um eine größere Abdeckung für potenzielle Übereinstimmungen zu erreichen. Anschließend wird ein Resultset nach einer Relevanzbewertung sortiert, die jedem einzelnen Dokument zugewiesen wird, für das sich eine Übereinstimmung ergibt. Die in der Liste ganz oben aufgeführten Dokumente werden an die aufrufende Anwendung zurückgegeben.

Die Abfrageausführung besteht also aus vier Phasen: 

1. Abfrageanalyse 
2. Lexikalische Analyse 
3. Dokumentabgleich/-abruf 
4. Bewertung 

Im Diagramm unten sind die Komponenten dargestellt, die zum Verarbeiten einer Suchanfrage verwendet werden. 

 ![Diagramm zur Lucene-Abfragearchitektur in der kognitiven Azure-Suche][1]


| Wichtige Komponenten | Beschreibung der Funktion | 
|----------------|------------------------|
|**Abfrageparser** | Dient zum Trennen von Abfrageausdrücken von Abfrageoperatoren und zum Erstellen der Abfragestruktur, die an das Suchmodul gesendet werden soll. |
|**Analysemodule** | Dienen zum Durchführen der lexikalischen Analyse für Abfrageausdrücke. Dieser Prozess kann das Transformieren, Entfernen oder Erweitern von Abfrageausdrücken umfassen. |
|**Index** | Eine effiziente Datenstruktur zum Speichern und Organisieren von Suchbegriffen, die aus indizierten Dokumenten extrahiert werden. |
|**Suchmodul** | Dient zum Abrufen und Bewerten von Dokumenten mit Übereinstimmungen basierend auf dem Inhalt des invertierten Index. |

## <a name="anatomy-of-a-search-request"></a>Anatomie einer Suchanfrage

Eine Suchanfrage ist eine vollständige Spezifikation dessen, was in einem Resultset zurückgegeben werden soll. In ihrer einfachsten Form handelt es sich um eine leere Abfrage ohne jegliche Kriterien. Ein realistischeres Beispiel enthält Parameter, mehrere Abfrageausdrücke, die ggf. auf einen bestimmten Feldbereich festgelegt sind, und unter Umständen einen Filterausdruck und Regeln für die Sortierung.  

Das folgende Beispiel ist eine Suchanforderung, die Sie per [REST-API](/rest/api/searchservice/search-documents) an die kognitive Azure-Suche senden können.  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
```

Für diese Anforderung führt das Suchmodul die folgenden Schritte aus:

1. Filtert die Dokumente heraus, für die der Preis zwischen 60 und 300 USD liegt.
2. Führt die Abfrage aus. In diesem Beispiel besteht die Suchabfrage aus Wortgruppen und Ausdrücken: `"Spacious, air-condition* +\"Ocean view\""`. (Benutzer geben häufig keine Satzzeichen ein, aber anhand der Verwendung im Beispiel können wir erklären, wie sie von Analysemodulen verarbeitet werden.) Für diese Abfrage durchsucht das Suchmodul die Felder mit der Beschreibung und dem Titel, die in `searchFields` angegeben sind, nach Dokumenten mit dem Text „Ocean view“ sowie nach dem Begriff „spacious“ oder nach Begriffen, die mit „air-condition“ beginnen. Der Parameter `searchMode` wird verwendet, um eine Übereinstimmung für einen beliebigen Begriff (Standard) oder alle Begriffe zu erzielen, wenn ein Begriff nicht explizit erforderlich ist (`+`).
3. Sortiert die sich ergebenden Hotels nach ihrer Entfernung zu einem bestimmten geografischen Ort und gibt die Daten dann an die aufrufende Anwendung zurück. 

In diesem Artikel geht es hauptsächlich um die Verarbeitung der *Suchabfrage*: `"Spacious, air-condition* +\"Ocean view\""`. Auf die Filterung und Sortierung wird hier nicht näher eingegangen. Weitere Informationen finden Sie in der [Referenzdokumentation zur Search-API](/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Phase 1: Abfrageanalyse 

Wie bereits erwähnt, ist die Abfragezeichenfolge die erste Zeile der Anfrage: 

```
 "search": "Spacious, air-condition* +\"Ocean view\"", 
```

Der Abfrageparser trennt Operatoren (z.B. `*` und `+` im Beispiel) von Suchbegriffen und unterteilt die Suchabfrage in *Unterabfragen* eines unterstützten Typs: 

+ *Begriffsabfrage* für eigenständige Begriffe (z.B. „spacious“)
+ *Ausdrucksabfrage* für Begriffe in Anführungszeichen (z.B. „ocean view“)
+ *Präfixabfrage* für Ausdrücke, auf die der Präfixoperator `*` folgt (z.B. „air-condition“)

Eine vollständige Liste mit unterstützten Abfragetypen finden Sie unter [Lucene-Abfragesyntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search).

Mit Operatoren, denen eine Unterabfrage zugeordnet ist, wird bestimmt, ob die Abfrage erfüllt werden „muss“ oder erfüllt sein „sollte“, damit ein Dokument als Übereinstimmung angesehen wird. `+"Ocean view"` „muss“ aufgrund des Operators `+` beispielsweise erfüllt sein. 

Der Abfrageparser strukturiert die Unterabfragen als *Abfragestruktur* (interne Struktur zur Darstellung der Abfrage) neu und übergibt sie an das Suchmodul. In der ersten Phase der Abfrageanalyse sieht die Abfragestruktur wie folgt aus:  

 ![Boolesche Abfrage: Suchmodus „any“][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Unterstützte Parser: Lucene-Optionen „simple“ und „full“ 

 Bei der kognitiven Azure-Suche werden zwei unterschiedliche Abfragesprachen verfügbar gemacht: `simple` (Standard) und `full`. Indem Sie den Parameter `queryType` für Ihre Suchanfrage festlegen, weisen Sie den Abfrageparser an, welche Abfragesprache zur Verwendung ausgewählt werden soll. Er verfügt somit über die Funktionen zum Interpretieren der Operatoren und der Syntax. Die [einfache Abfragesprache](/rest/api/searchservice/simple-query-syntax-in-azure-search) ist intuitiv und robust und häufig gut geeignet, um die Benutzereingabe unverändert ohne clientseitige Verarbeitung zu interpretieren. Sie unterstützt Abfrageoperatoren, die Sie aus anderen Websuchmodulen kennen. Bei der [vollständigen Lucene-Abfragesprache](/rest/api/searchservice/lucene-query-syntax-in-azure-search), die Sie durch das Festlegen von `queryType=full` erhalten, wird die einfache Abfragesprache erweitert. Es wird Unterstützung für weitere Operatoren und Abfragetypen hinzugefügt, z.B. Platzhalter, Fuzzy Matching, reguläre Ausdrücke und feldbezogene Abfragen. Ein regulärer Ausdruck, der mit einfacher Abfragesyntax gesendet wird, wird beispielsweise als Abfragezeichenfolge und nicht als Ausdruck interpretiert. Für die Beispielabfrage in diesem Artikel wird die vollständige Lucene-Abfragesprache verwendet.

### <a name="impact-of-searchmode-on-the-parser"></a>Auswirkung von searchMode auf den Parser 

Ein anderer Parameter der Suchanfrage, der sich auf die Analyse auswirkt, ist der Parameter `searchMode`. Er steuert den Standardoperator für boolesche Abfragen: „any“ (Standard) oder „all“.  

Bei `searchMode=any`, also der Standardeinstellung, lautet die Trennung zwischen „spacious“ und „air-condition“ OR (`||`), sodass der Text der Beispielabfrage Folgendem entspricht: 

```
Spacious,||air-condition*+"Ocean view" 
```

Explizite Operatoren, z.B. `+` in `+"Ocean view"`, sind in der booleschen Abfrage eindeutig (der Begriff *muss* übereinstimmen). Weniger eindeutig ist, wie die restlichen Begriffe interpretiert werden müssen: „spacious“ und „air-condition“. Soll das Suchmodul nach Übereinstimmungen für „ocean view“ *und* „spacious“ *und* „air-condition“ suchen? Oder soll nach „ocean view“ und *einem* der beiden anderen Begriffe gesucht werden? 

Standardmäßig (`searchMode=any`) wird vom Suchmodul die weniger eingeschränkte Interpretation vorausgesetzt. Für eines der Felder *sollte* sich eine Übereinstimmung ergeben. Dies wird mit „OR“ angegeben. In der Abbildung mit der ersten Abfragestruktur weiter oben, die die beiden Vorgänge vom Typ „sollte“ enthält, ist die Standardeinstellung dargestellt.  

Angenommen, wir legen jetzt `searchMode=all` fest. In diesem Fall wird die Leerstelle als Vorgang vom Typ „and“ interpretiert. Jeder der restlichen Begriffe muss im Dokument vorhanden sein, damit sich dafür eine Übereinstimmung ergibt. Die sich ergebende Beispielabfrage wird dann wie folgt interpretiert: 

```
+Spacious,+air-condition*+"Ocean view"
```

Eine geänderte Abfragestruktur für diese Abfrage, bei der ein übereinstimmendes Dokument der Schnittpunkt aller drei Unterabfragen ist, würde wie folgt lauten: 

 ![Boolesche Abfrage: Suchmodus „all“][3]

> [!Note] 
> Sie können am besten entscheiden, ob Sie `searchMode=any` oder `searchMode=all` wählen sollen, indem Sie jeweils aussagekräftige Testabfragen durchführen. Benutzer, die Operatoren verwenden (häufig beim Durchsuchen von Dokumentspeichern), empfinden die Ergebnisse ggf. als intuitiver, wenn `searchMode=all` für boolesche Abfragen genutzt wird. Weitere Informationen zum Zusammenwirken von `searchMode` und Operatoren finden Sie unter [Einfache Abfragesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Phase 2: Lexikalische Analyse 

Mit lexikalischen Analysemodulen werden *Begriffsabfragen* und *Ausdrucksabfragen* verarbeitet, nachdem die Abfragestruktur erstellt wurde. Ein Analysemodul akzeptiert die Texteingaben des Parsers, verarbeitet den Text und sendet dann mit Token versehene Begriffe zurück, damit sie in die Abfragestruktur einbezogen werden können. 

Die häufigste Form der lexikalischen Analyse ist die *linguistische Analyse*, bei der Abfrageausdrücke basierend auf bestimmten Regeln einer Abfragesprache transformiert werden: 

* Reduzieren eines Abfrageausdrucks auf den Stamm eines Worts 
* Entfernen von nicht unbedingt benötigten Wörtern (Stoppwörter, z.B. „the“ oder „and“ für Englisch) 
* Aufteilen eines zusammengesetzten Worts in seine Bestandteile 
* Konvertieren eines kleingeschriebenen Worts in ein großgeschriebenes Wort 

Bei all diesen Vorgängen werden Unterschiede zwischen der Texteingabe des Benutzers und den im Index gespeicherten Begriffen normalerweise beseitigt. Vorgänge dieser Art gehen über die Textverarbeitung hinaus und erfordern umfassende Kenntnisse der Sprache. Um diese Ebene der linguistischen Erkennung hinzuzufügen, unterstützt die kognitive Azure-Suche eine lange Liste mit [Sprachanalysen](/rest/api/searchservice/language-support) von Lucene und Microsoft.

> [!Note]
> Die Analyseanforderungen können je nach Szenario von minimal bis ausführlich reichen. Sie können die Komplexität der lexikalischen Analyse steuern, indem Sie eine der vordefinierten Analysen auswählen oder Ihre eigene [benutzerdefinierte Analyse](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) erstellen. Der Bereich der Analysen ist auf die suchbaren Felder festgelegt, und die Analysen werden als Teil einer Felddefinition angegeben. So können Sie die lexikalische Analyse pro Feld festlegen. Wenn keine Angabe vorhanden ist, wird die Analyse *Standard* von Lucene verwendet.

In unserem Beispiel enthält die erste Abfragestruktur vor der Analyse den Begriff „Spacious“ mit einem großen „S“ und einem Komma, das vom Abfrageparser als Teil des Abfrageausdrucks interpretiert wird (ein Komma wird nicht als Operator einer Abfragesprache angesehen).  

Wenn die Standardanalyse den Ausdruck verarbeitet, werden „ocean view“ und „spacious“ in Kleinbuchstaben konvertiert, und das Komma wird entfernt. Die geänderte Abfragestruktur sieht wie folgt aus: 

 ![Boolesche Abfrage mit analysierten Ausdrücken][4]

### <a name="testing-analyzer-behaviors"></a>Testen des Analyseverhaltens 

Das Verhalten einer Analyse kann mit der [Analyse-API](/rest/api/searchservice/test-analyzer) getestet werden. Geben Sie den Text ein, den Sie analysieren möchten, um zu ermitteln, welche Ausdrücke von der jeweiligen Analyse generiert werden. Sie können beispielsweise die folgende Anfrage ausgeben, um zu ermitteln, wie die Standardanalyse den Text „air-condition“ verarbeitet:

```json
{
    "text": "air-condition",
    "analyzer": "standard"
}
```

Die Standardanalyse teilt den Eingabetext in die folgenden beiden Token auf und fügt Attribute wie Start- und Endoffset (zur Hervorhebung von Treffern) und die Position (für den Wortgruppenabgleich) hinzu:

```json
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
```

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Ausnahmen bei der lexikalischen Analyse 

Die lexikalische Analyse gilt nur für Abfragetypen, für die vollständige Ausdrücke erforderlich sind – entweder eine Begriffsabfrage oder eine Ausdrucksabfrage. Dies gilt nicht für Abfragetypen mit unvollständigen Ausdrücken – Präfixabfrage, Platzerhalterabfrage, Abfrage regulärer Ausdrücke – oder für eine Fuzzyabfrage. Diese Abfragetypen, einschließlich der Präfixabfrage mit dem Ausdruck `air-condition*` in unserem Beispiel, werden der Abfragestruktur direkt hinzugefügt, und die Analysephase wird umgangen. Die einzige Transformation, die für die Abfrageausdrücke dieser Typen durchgeführt wird, ist die Umwandlung in Kleinbuchstaben.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Phase 3: Dokumentabgleich/-abruf 

Der Dokumentabruf bezieht sich auf das Suchen nach Dokumenten mit übereinstimmenden Ausdrücken im Index. Diese Phase lässt sich am besten anhand eines Beispiels beschreiben. Wir beginnen mit einem Hotelindex mit dem folgenden einfachen Schema: 

```json
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
```

Wir nehmen weiter an, dass dieser Index die folgenden vier Dokumente enthält: 

```json
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
```

**Indizieren von Ausdrücken**

Für das Verständnis des Abrufs ist es hilfreich, einige Grundlagen zur Indizierung zu kennen. Die Speichereinheit ist ein invertierter Index (einer für jedes suchbare Feld). In einem invertierten Index ist eine sortierte Liste aller Ausdrücke aus allen Dokumenten enthalten. Jeder Ausdruck wird der Liste mit den Dokumenten zugeordnet, in denen er vorkommt. Dies ist im Beispiel unten dargestellt.

Zum Erstellen der Ausdrücke in einem invertierten Index führt das Suchmodul eine lexikalische Analyse für den Inhalt von Dokumenten durch. Dies ähnelt der Vorgehensweise während der Abfrageverarbeitung:

1. *Texteingaben* werden je nach Konfiguration des Analysemoduls an eine Analyse übergeben, in Kleinbuchstaben konvertiert, von Satzzeichen befreit usw. 
2. *Token* sind die Ausgabe der lexikalischen Analyse.
3. *Ausdrücke* werden dem Index hinzugefügt.

Es ist zwar nicht unbedingt erforderlich, aber häufig werden die gleichen Analysen für Such- und Indiziervorgänge verwendet, sodass Abfrageausdrücke eher wie Ausdrücke im Index aussehen.

> [!Note]
> Bei der kognitiven Azure-Suche können Sie verschiedene Analysen für das Indizieren und Suchen nach zusätzlichen Feldparametern vom Typ `indexAnalyzer` und `searchAnalyzer` angeben. Wenn keine Angabe vorhanden ist, wird die Analyse, die mit der `analyzer`-Eigenschaft festgelegt wird, sowohl für die Indizierung als auch für die Suche verwendet.  

**Invertierter Index für Beispieldokumente**

Wir kehren zu unserem Beispiel zurück. Für das Feld **title** sieht der invertierte Index wie folgt aus:

| Begriff | Dokumentliste |
|------|---------------|
| atman | 1 |
| beach | 2 |
| hotel | 1, 3 |
| ocean | 4  |
| playa | 3 |
| resort | 3 |
| retreat | 4 |

Im Feld „title“ kommt *hotel* nur in zwei Dokumenten vor: 1 und 3.

Für das Feld **description** sieht der Index wie folgt aus:

| Begriff | Dokumentliste |
|------|---------------|
| air | 3
| and | 4
| beach | 1
| conditioned | 3
| comfortable | 3
| distance | 1
| island | 2
| kauaʻi | 2
| located | 2
| north | 2
| ocean | 1, 2, 3
| of | 2
| on |2
| quiet | 4
| rooms  | 1, 3
| secluded | 4
| shore | 2
| spacious | 1
| the | 1, 2
| zu | 1
| Ansicht | 1, 2, 3
| walking | 1
| durch | 3


**Ermitteln von Übereinstimmungen zwischen Abfrageausdrücken und indizierten Ausdrücken**

Basierend auf den obigen invertierten Indizes kehren wir zur Beispielabfrage zurück und sehen uns an, wie für unsere Beispielabfrage Dokumente mit Übereinstimmungen gefunden werden. Hier zur Erinnerung noch einmal die fertige Abfragestruktur: 

 ![Boolesche Abfrage mit analysierten Ausdrücken][4]

Während der Ausführung der Abfrage werden einzelne Abfragen für die suchbaren Felder unabhängig voneinander ausgeführt. 

+ Die Begriffsabfrage „spacious“ ergibt eine Übereinstimmung mit Dokument 1 (Hotel Atman). 

+ Die Präfixabfrage „air-condition*“ ergibt keine Übereinstimmung für Dokumente. 

  Dies ist ein Verhalten, das bei Entwicklern unter Umständen zu Verwirrung führen kann. Obwohl der Ausdruck „air-conditioned“ im Dokument vorhanden ist, wird er von der Standardanalyse in zwei Ausdrücke unterteilt. Wir wissen, dass Präfixabfragen, die Teilausdrücke enthalten, nicht analysiert werden. Aus diesem Grund wird im invertierten Index nicht nach Ausdrücken mit dem Präfix „air-condition“ gesucht, und sie werden somit auch nicht gefunden.

+ Bei der Ausdrucksabfrage „ocean view“ wird nach den Begriffen „ocean“ und „view“ gesucht und die Nähe der Begriffe zueinander im Originaldokument überprüft. Für die Dokumente 1, 2 und 3 ergeben sich für diese Abfrage im Feld „description“ Übereinstimmungen. Beachten Sie, dass Dokument 4 den Begriff „ocean“ im Titel enthält, aber nicht als Übereinstimmung angesehen wird, da wir nach dem Ausdruck „ocean view“ und nicht nach einzelnen Wörtern suchen. 

> [!Note]
> Eine Suchanforderung wird unabhängig für alle suchbaren Felder im Index der kognitiven Azure-Suche ausgeführt, sofern Sie den Satz der Felder nicht mit dem Parameter `searchFields` beschränken. Dies ist in der Beispielsuchanfrage dargestellt. Dokumente, für die sich in den ausgewählten Feldern Übereinstimmungen ergeben, werden zurückgegeben. 

Insgesamt ergeben sich für die hier verwendete Abfrage Übereinstimmungen für die Dokumente 1, 2 und 3. 

## <a name="stage-4-scoring"></a>Phase 4: Bewertung  

Jedem Dokument eines Suchergebnisses wird eine Relevanzbewertung zugewiesen. Die Funktion der Relevanzbewertung besteht darin, die Dokumente höher einzustufen, die in Bezug auf die Suchabfrage die beste Beantwortung der Suchabfrage eines Benutzers darstellen. Die Bewertung wird basierend auf den statistischen Eigenschaften der Ausdrücke berechnet, für die sich Übereinstimmungen ergeben. Der Kern der Bewertungsformel ist das [TF-IDF-Maß](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) (term frequency – inverse document frequency, Vorkommenshäufigkeit – inverse Dokumenthäufigkeit). In Abfragen, die seltene und häufige Ausdrücke enthalten, werden mit TF/IDF Ergebnisse höhergestuft, die den seltenen Ausdruck enthalten. Für einen hypothetischen Index mit allen Wikipedia-Artikeln gilt für Dokumente, für die die Abfrage *the president* durchgeführt wurde, beispielsweise Folgendes: Dokumente, die Übereinstimmungen für *president* enthalten, werden mit einer höheren Relevanz als Dokumente versehen, die Übereinstimmungen für *the* enthalten.


### <a name="scoring-example"></a>Beispiel für die Bewertung

Für die drei Dokumente, für die sich Übereinstimmungen mit unserer Beispielabfrage ergeben haben, galt Folgendes:

```
search=Spacious, air-condition* +"Ocean view"  
```

```json
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
```

Für Dokument 1 hat die Abfrage die beste Übereinstimmung ergeben, da sowohl der Begriff *spacious* als auch der erforderliche Ausdruck *ocean view* im Feld „description“ vorkommt. Für die nächsten beiden Dokumente ergibt sich nur eine Übereinstimmung mit dem Ausdruck *ocean view*. Es ist vielleicht überraschend, dass die Relevanzbewertung für Dokument 2 und 3 unterschiedlich ist, obwohl beide zu einer Übereinstimmung für die Abfrage geführt haben. Dies liegt daran, dass die Bewertungsformel über mehr Komponenten als nur TF/IDF verfügt. In diesem Fall wurde Dokument 3 eine etwas höhere Bewertung zugewiesen, da dessen Beschreibung kürzer ist. Informieren Sie sich über [Lucene's Practical Scoring Formula](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) (Bewertungsformel von Lucene), damit Sie verstehen, wie sich die Feldlänge und andere Faktoren auf die Relevanzbewertung auswirken können.

Einige Abfragetypen (Platzhalter, Präfix, regulärer Ausdruck) tragen immer eine konstante Bewertungspunktzahl zur Gesamtbewertung des Dokuments bei. So können Übereinstimmungen, die über die Abfrageerweiterung ermittelt werden, in die Ergebnisse einbezogen werden, ohne dabei die Rangfolge zu beeinträchtigen. 

In einem Beispiel ist dargestellt, warum dies wichtig ist. Platzhaltersuchen mit Präfixen sind der Definition nach mehrdeutig, da die Eingabe eine Teilzeichenfolge mit potenziellen Übereinstimmungen für eine sehr große Zahl unterschiedlicher Begriffe ist (beispielsweise die Eingabe „tour*“ mit Übereinstimmungen für „tours“, „tourettes“ und „tourmaline“). Aufgrund der Art dieser Ergebnisse lässt sich nicht sicher ableiten, welche Begriffe „wertvoller“ als andere sind. Daher werden Begriffshäufigkeiten beim Bewerten von Ergebnissen in Abfragen vom Typ Platzhalter, Präfix und regulärer Ausdruck ignoriert. Bei einer mehrteiligen Suchanfrage, die Teilbegriffe und vollständige Begriffe enthält, werden Ergebnisse aus der Teileingabe mit einer konstanten Bewertung eingebunden, um die Bevorzugung von potenziell unerwarteten Übereinstimmungen zu vermeiden.

### <a name="score-tuning"></a>Optimieren der Bewertung

Es gibt zwei Möglichkeiten, wie Sie Relevanzbewertungen in der kognitiven Azure-Suche optimieren können:

1. Mit **Bewertungsprofilen** werden Dokumente in der Rangfolgenliste der Ergebnisse basierend auf einer Gruppe von Regeln höhergestuft. In unserem Beispiel können wir Dokumente, für die sich Übereinstimmungen im Feld „title“ ergeben, als relevanter als Dokumente einstufen, für die sich Übereinstimmungen im Feld „description“ ergeben. Wenn unser Index über ein Preisfeld für jedes Hotel verfügen würde, könnten wir zusätzlich Dokumente mit einem niedrigeren Preis höherstufen. Lesen Sie die weiteren Informationen zum [Hinzufügen von Bewertungsprofilen zu einem Suchindex](/rest/api/searchservice/add-scoring-profiles-to-a-search-index).
2. Beim **Term Boosting** („Begriffsverstärkung“, nur in der vollständigen Lucene-Abfragesyntax verfügbar) wird der Verstärkungsoperator `^` bereitgestellt, der auf alle Teile der Abfragestruktur angewendet werden kann. In unserem Beispiel könnten wir anstatt nach dem Präfix *air-condition*\* auch entweder nach dem exakten Begriff *air-condition* oder dem Präfix suchen. Dokumente, für die sich eine Übereinstimmung mit dem exakten Begriff ergibt, werden dann höher eingestuft, indem die Begriffsabfrage verstärkt wird: *air-condition^2||air-condition*\*. [Hier finden Sie weitere Informationen zum „Term Boosting“](/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Durchführen von Bewertungen in einem verteilten Index

Alle Indizes in der kognitiven Azure-Suche werden automatisch in mehrere Shards unterteilt, sodass der Index beim Hoch- oder Herunterskalieren des Diensts schnell auf mehrere Knoten verteilt werden kann. Wenn eine Suchanfrage ausgegeben wird, erfolgt dies für jeden Shard individuell. Die Ergebnisse aller Shards werden dann zusammengeführt und nach der Bewertung sortiert (falls keine andere Sortierung festgelegt ist). Es ist wichtig, dass Sie Folgendes wissen: Die Bewertungsfunktion wägt die Vorkommenshäufigkeit von Abfragebegriffen gegenüber der inversen Dokumenthäufigkeit für alle Dokumente des Shards ab. Nicht übergreifend für alle Shards!

Dies bedeutet, dass eine Relevanzbewertung für identische Dokumente unterschiedliche ausfallen *kann*, wenn diese sich auf unterschiedlichen Shards befinden. Glücklicherweise sind diese Unterschiede vernachlässigbar, wenn im Index eine größere Zahl von Dokumenten enthalten ist, weil die Begriffe gleichmäßiger verteilt sind. Es kann nicht vorausgesagt werden, auf welchem Shard ein Dokument angeordnet wird. Wenn wir aber davon ausgehen, dass sich ein Dokumentschlüssel nicht ändert, wird es immer demselben Shard zugewiesen.

Im Allgemeinen ist die Dokumentbewertung nicht das am besten geeignete Attribut zum Sortieren von Dokumenten, wenn die Stabilität der Sortierung wichtig ist. Wenn beispielsweise zwei Dokumente mit einer identischen Bewertung vorhanden sind, kann nicht garantiert werden, welches bei nachfolgenden Ausführungen derselben Abfrage zuerst angezeigt wird. Die Dokumentbewertung soll nur als allgemeiner Hinweis auf die Relevanz des Dokuments relativ zu anderen Dokumenten des Resultsets dienen.

## <a name="conclusion"></a>Zusammenfassung

Aufgrund des Erfolgs von Internetsuchmaschinen sind die Erwartungen in Bezug auf die Volltextsuche für private Daten gestiegen. Für nahezu alle Arten von Suchoberflächen erwarten wir mittlerweile, dass die Suchmaschine unsere Absicht erkennt – auch bei falsch geschriebenen oder unvollständigen Suchbegriffen. Wir erwarten vielleicht sogar Übereinstimmungen anhand von ähnlichen Begriffen oder Synonymen, die wir gar nicht angegeben haben.

Aus technischer Sicht ist die Volltextsuche hochkomplex und erfordert eine anspruchsvolle linguistische Analyse und einen systematischen Verarbeitungsansatz, bei dem Abfrageausdrücke herausgefiltert, erweitert und transformiert werden, um die relevanten Ergebnisse zu liefern. Diese komplexen Anforderungen sind mit vielen Faktoren verbunden, die sich auf das Ergebnis einer Abfrage auswirken können. Aus diesem Grund ist es sinnvoll, Zeit für die Einarbeitung in die Details der Volltextsuche zu investieren. Mit diesem Wissen ergeben sich für Sie nützliche Vorteile bei der Analyse von unerwarteten Ergebnissen.  

In diesem Artikel wurde die Volltextsuche im Kontext der kognitiven Azure-Suche beschrieben. Wir hoffen, dass er genügend Hintergrundinformationen enthält, die Ihnen das Erkennen von potenziellen Ursachen und Lösungen für häufig auftretende Abfrageprobleme ermöglichen. 

## <a name="next-steps"></a>Nächste Schritte

+ Erstellen Sie den Beispielindex, probieren Sie verschiedene Abfragen aus, und sehen Sie sich die Ergebnisse an. Eine Anleitung finden Sie unter [Erstellen und Abfragen Ihres ersten Azure Search-Index im Portal](search-get-started-portal.md#query-index) im Abschnitt „Abfragen des Index“.

+ Probieren Sie andere Abfragesyntax in den Beispielen unter [Search Documents](/rest/api/searchservice/search-documents#bkmk_examples) (Suchen nach Dokumenten) oder unter [Simple query syntax in Azure Search](/rest/api/searchservice/simple-query-syntax-in-azure-search) (Einfache Abfragesyntax in Azure Search) im Suchexplorer im Portal aus.

+ Sehen Sie sich die [Bewertungsprofile](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) an, wenn Sie die Rangfolge in Ihrer Suchanwendung optimieren möchten.

+ Informieren Sie sich darüber, wie Sie [sprachspezifische lexikalische Analysen](/rest/api/searchservice/language-support) anwenden.

+ [Konfigurieren Sie benutzerdefinierte Analysen](/rest/api/searchservice/custom-analyzers-in-azure-search) für die minimale oder spezielle Verarbeitung anhand von bestimmten Feldern.

## <a name="see-also"></a>Weitere Informationen

[Search Documents (Azure Search Service REST API)](/rest/api/searchservice/search-documents) (Suchen nach Dokumenten (Azure Search Service-REST-API)) 

[Einfache Abfragesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Vollständige Lucene-Abfragesyntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Verarbeiten von Suchergebnissen](./search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png