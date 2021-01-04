---
title: Teilausdrücke, Muster und Sonderzeichen
titleSuffix: Azure Cognitive Search
description: Verwenden Sie Platzhalter-, RegEx- und Präfixabfragen, um in einer Azure Cognitive Search-Abfrageanforderung einen Abgleich für Ausdrücke oder Teile von Ausdrücken durchzuführen. Zum Lösen von Mustern mit Sonderzeichen, deren Abgleich schwierig ist, können eine vollständige Abfragesyntax und benutzerdefinierte Analysetools verwendet werden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 79ba186351cc145e012658abc30572e99b123dbb
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573985"
---
# <a name="partial-term-search-and-patterns-with-special-characters-hyphens-wildcard-regex-patterns"></a>Suche nach Teilausdrücken und Mustern mit Sonderzeichen (Bindestriche, Platzhalter, reguläre Ausdrücke, Muster)

Eine *Suche nach Teilausdrücken*  ist eine Abfrage, die aus einem Begriffsfragment besteht, d. h. anstelle eines ganzen Begriffs nur den Beginn, das Mittelteil oder Ende des Begriffs enthält (manchmal auch als Präfix-, Infix- oder Suffixabfrage bezeichnet). Eine Suche nach Teilausdrücken kann eine Kombination von Fragmenten sein, wobei oft Sonderzeichen wie Bindestriche, Gedankenstriche oder Schrägstriche Teil der Abfragezeichenfolge sind. Häufige Anwendungsfälle sind Teile von Telefonnummern, URLs, Codes oder mit Bindestrichen zusammengesetzten Wörtern.

Suchen nach Teilausdrücken und Abfragezeichenfolgen, die Sonderzeichen enthalten, können problematisch sein, wenn der Index keine Token im erwarteten Format enthält. Während der [Phase der lexikalischen Analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) der Indizierung werden (bei Verwendung des Standardanalysetools) Sonderzeichen verworfen, zusammengesetzte Wörter aufgeteilt und Leerzeichen gelöscht. Dies alles kann bewirken, dass bei Abfragen Fehler auftreten, wenn keine Übereinstimmung gefunden wird. Beispielsweise wird die Telefonnummer `+1 (425) 703-6214` (tokenisiert als `"1"`, `"425"`, `"703"`, `"6214"`) in der Abfrage `"3-62"` nicht angezeigt, da dieser Inhalt nicht im Index vorhanden ist. 

Die Lösung besteht darin, während der Indizierung ein Analysetool aufzurufen, das eine vollständige Zeichenfolge beibehält, ggf. einschließlich Leerzeichen und Sonderzeichen, damit Sie die Leer- und Sonderzeichen in Ihre Abfragezeichenfolge einbeziehen können. Ebenso ermöglicht die Verwendung einer vollständigen Zeichenfolge, die nicht in kleinere Teile tokenisiert wurde, den Musterabgleich für „Beginnt mit“- oder „Endet mit“-Abfragen, wobei das von Ihnen bereitgestellte Muster anhand eines Begriffs ausgewertet werden kann, der nicht durch lexikalische Analysen transformiert wird. Das Erstellen eines zusätzlichen Felds für eine intakte Zeichenfolge und die Verwendung eines Analysetools, das den Inhalt beibehält, ist die Lösung für den Musterabgleich und den Abgleich von Abfragezeichenfolgen, die Sonderzeichen enthalten.

> [!TIP]
> Wenn Sie mit Postman und REST-APIs vertraut sind, [laden Sie die Sammlung von Abfragebeispielen herunter](https://github.com/Azure-Samples/azure-search-postman-samples/), um die in diesem Artikel beschriebenen Teilausdrücke und Sonderzeichen abzufragen.

## <a name="about-partial-term-search"></a>Informationen zur Teilausdrucksuche

Azure Cognitive Search sucht im Index nach vollständigen tokenisierten Begriffen und findet keine Entsprechung für einen Teilausdruck, sofern Sie keine Platzhalteroperatoren (`*` und `?`) einbeziehen oder die Abfrage als regulären Ausdruck formatieren. Teilausdrücke werden mithilfe der folgenden Methoden angegeben:

+ [Abfragen mit regulären Ausdrücken](query-lucene-syntax.md#bkmk_regex) können beliebige reguläre Ausdrücke sein, die unter Apache Lucene gültig sind. 

+ [Platzhalteroperatoren mit Präfixübereinstimmung](query-simple-syntax.md#prefix-search) beziehen sich auf ein allgemein bekanntes Muster, das den Anfang eines Begriffs gefolgt von den Suffixoperatoren `*` oder `?` enthält, wie z. B. `search=cap*` für „Cap'n Jack's Waterfront Inn“ oder „Gacc Capital“. Die Präfixübereinstimmung wird sowohl in der einfachen als auch vollständigen Lucene-Abfragesyntax unterstützt.

+ [Platzhalter mit Infix- und Suffixübereinstimmung](query-lucene-syntax.md#bkmk_wildcard) platzieren die Operatoren `*` und `?` innerhalb oder am Anfang eines Ausdrucks und erfordern eine Syntax regulärer Ausdrücke (wobei der Ausdruck in Schrägstriche eingeschlossen ist). Beispielsweise gibt die Abfragezeichenfolge (`search=/.*numeric*./`) Ergebnisse zu Suffix- und Infixübereinstimmungen mit „alphanumeric“ und „alphanumerical“ zurück.

Bei der Suche nach Teilausdrücken oder Mustern und einigen anderen Abfrageformen wie der Fuzzysuche werden Analysetools zum Abfragezeitpunkt nicht verwendet. Für diese Abfrageformen, die der Parser dadurch erkennt, dass Operatoren und Trennzeichen vorhanden sind, wird die Abfragezeichenfolge ohne eine lexikalische Analyse an die Engine übergeben. Für diese Abfrageformen wird das im Feld angegebene Analysetool ignoriert.

> [!NOTE]
> Bei einer partiellen Abfragezeichenfolge, die Zeichen wie z. B. Schrägstriche in einem URL-Fragment enthält, müssen Sie möglicherweise Escapezeichen hinzufügen. In JSON ist das Escapezeichen für einen Schrägstrich (`/`) ein umgekehrter Schrägstrich (`\`). Somit ist `search=/.*microsoft.com\/azure\/.*/` die Syntax für das URL-Fragment „microsoft.com/azure/“.

## <a name="solving-partialpattern-search-problems"></a>Lösen von Problemen bei der Suche nach Teilausdrücken/Mustern

Wenn Sie nach Fragmenten, Mustern oder Sonderzeichen suchen müssen, können Sie das Standardanalysetool mit einem benutzerdefinierten Analysetool überschreiben, das einfachere Tokenisierungsregeln befolgt und die gesamte Zeichenfolge im Index beibehält. Der grundsätzliche Ansatz ist wie folgt:

1. Definieren Sie ein Feld zum Speichern einer intakten Version der Zeichenfolge (unter der Voraussetzung, dass Sie analysierten und nicht analysierten Text zum Abfragezeitpunkt benötigen).
1. Evaluieren Sie verschiedene Analysetools, die Token mit dem richtigen Grad an Granularität ausgeben, und wählen Sie eines aus.
1. Zuweisen des Analysetools zum Feld
1. Erstellen und Testen des Index

> [!TIP]
> Die Auswertung von Analysetools ist ein iterativer Prozess, bei dem der Index häufig neu erstellt werden muss. Sie können diesen Schritt vereinfachen, indem Sie Postman und die REST-APIs für [Index erstellen](/rest/api/searchservice/create-index), [Index löschen](/rest/api/searchservice/delete-index),[Dokumente laden](/rest/api/searchservice/addupdate-or-delete-documents) und [Dokumente durchsuchen](/rest/api/searchservice/search-documents) verwenden. Für „Dokumente laden“ sollte der Anforderungstext ein kleines repräsentatives Dataset enthalten, das getestet werden soll (z. B. ein Feld mit Telefonnummern oder Produktcodes). Wenn diese APIs in derselben Postman-Sammlung enthalten sind, können Sie diese Schritte schnell durchlaufen.

## <a name="1---create-a-dedicated-field"></a>1 – Erstellen eines dedizierten Felds

Analysetools bestimmen, wie Begriffe in einem Index tokenisiert werden. Da Analysetools pro Feld zugewiesen werden, können Sie Felder in Ihrem Index erstellen, die für verschiedene Szenarien optimiert werden können. Sie können beispielsweise „featureCode“ und „featureCodeRegex“ definieren, um für ein Szenario eine reguläre Volltextsuche und für ein anderes Szenario einen erweiterten Musterabgleich zu unterstützen. Die den einzelnen Feldern zugewiesenen Analysetools bestimmen, wie die Inhalte der einzelnen Felder im Index tokenisiert werden.  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

<a name="set-an-analyzer"></a>

## <a name="2---set-an-analyzer"></a>2 – Festlegen eines Analysetools

Für die Generierung von Token mit vollständigen Ausdrücken werden häufig die folgenden Analysetools ausgewählt:

| Analysetool | Verhalten |
|----------|-----------|
| [Sprachanalysen](index-add-language-analyzers.md) | Behält Bindestriche in zusammengesetzten Wörtern oder Zeichenfolgen, Umlaute und Verbformen bei. Wenn Abfragemuster Bindestriche enthalten, kann die Verwendung einer Sprachanalyse ausreichen. |
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Der Inhalt des gesamten Felds wird als ein Ausdruck tokenisiert. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Die Trennung erfolgt nur anhand von Leerzeichen. Ausdrücke, die Bindestriche oder andere Zeichen enthalten, werden als einzelnes Token behandelt. |
| [Benutzerdefiniertes Analysetool](index-add-custom-analyzers.md) | (Empfohlen) Beim Erstellen eines benutzerdefinierten Analysetools können Sie sowohl den Tokenizer als auch den Tokenfilter angeben. Die obigen Analysetools müssen unverändert verwendet werden. Bei einem benutzerdefinierten Analysetool können Sie auswählen, welche Tokenizer und Tokenfilter genutzt werden sollen. <br><br>Eine empfohlene Kombination ist der [Schlüsselwort-Tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) mit einem [Kleinbuchstaben-Tokenfilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Ohne Filter wandelt das vordefinierte [Schlüsselwort-Analysetool](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) Großbuchstaben nicht in Kleinbuchstaben um. Dies kann dazu führen, dass Abfragen nicht erfolgreich sind. Bei einem benutzerdefinierten Analysetool erhalten Sie einen Mechanismus zum Hinzufügen des Kleinbuchstaben-Tokenfilters. |

Wenn Sie ein Web-API-Testtool wie Postman nutzen, können Sie den [REST-Aufruf für die Textanalyse](/rest/api/searchservice/test-analyzer) hinzufügen, um die tokenisierte Ausgabe zu untersuchen.

Sie müssen über einen ausgefüllten Index verfügen, den Sie verwenden. Bei Verwendung eines vorhandenen Index und eines Felds mit Bindestrichen oder Teilen von Ausdrücken können Sie für bestimmte Ausdrücke verschiedene Analysetools einsetzen, um zu ermitteln, welche Token ausgegeben werden.  

1. Verwenden Sie zunächst das Standardanalysetool, um herauszufinden, wie Ausdrücke standardmäßig tokenisiert werden.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Werten Sie die Antwort aus, um zu ermitteln, wie der Text im Index tokenisiert wird. Beachten Sie, wie jeder Begriff in Kleinschreibung umgewandelt, Bindestriche entfernt und Teilzeichenfolgen in einzelne Token aufgeschlüsselt werden. Nur die Abfragen, die mit diesen Token übereinstimmen, geben dieses Dokument in den Ergebnissen zurück. Eine Abfrage, die „10-NOR“ umfasst, erzeugt einen Fehler.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Ändern Sie die Anforderung so, dass das Analysetool `whitespace` oder `keyword` verwendet wird:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Die Antwort enthält nun ein einzelnes Token in Großbuchstaben, bei dem die Bindestriche der Zeichenfolge beibehalten wurden. Falls Sie nach einem Muster oder Teilausdruck wie „10-NOR“ suchen müssen, verfügt die Abfrage-Engine jetzt über die Grundlage für die Ermittlung einer Übereinstimmung.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Hierbei ist zu beachten, dass Abfrageparser beim Erstellen der Abfragestruktur die Ausdrücke eines Suchbegriffs häufig in Kleinbuchstaben umwandeln. Bei Verwendung eines Analysetools, bei dem Texteingaben während der Indizierung nicht in Kleinbuchstaben umgewandelt werden, kann dies der Grund dafür sein, warum Sie nicht die erwarteten Ergebnisse erhalten. Die Lösung besteht darin, einen Kleinbuchstaben-Tokenfilter hinzuzufügen, wie im Abschnitt „Verwenden von benutzerdefinierten Analysetools“ weiter unten beschrieben.

## <a name="3---configure-an-analyzer"></a>3 – Konfigurieren eines Analysetools
 
Unabhängig davon, ob Sie Analysetools auswerten oder eine bestimmte Konfiguration einsetzen, gilt Folgendes: Sie müssen das Analysetool in der Felddefinition angeben und ggf. das Analysetool selbst konfigurieren, falls Sie keine integrierte Version nutzen. Beim Austauschen von Analysetools müssen Sie in der Regel den Index neu erstellen (löschen, neu erstellen und neu laden). 

### <a name="use-built-in-analyzers"></a>Verwenden von integrierten Analysetools

Integrierte oder vordefinierte Analysetools können anhand des Namens in einer `analyzer`-Eigenschaft einer Felddefinition angegeben werden, ohne dass im Index eine zusätzliche Konfiguration erforderlich ist. Im folgenden Beispiel wird veranschaulicht, wie Sie das Analysetool `whitespace` für ein Feld festlegen. 

Weitere Szenarios und weitere Informationen zu anderen integrierten Analysemodulen finden Sie unter [Vordefinierte Analysetoolreferenz](./index-add-custom-analyzers.md#predefined-analyzers-reference). 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Verwenden von benutzerdefinierten Analysetools

Wenn Sie ein [benutzerdefiniertes Analysetool](index-add-custom-analyzers.md) verwenden, definieren Sie es im Index mit einer benutzerdefinierten Kombination aus Tokenizer und Tokenfilter mit den möglichen Konfigurationseinstellungen. Als Nächstes verweisen Sie in einer Felddefinition darauf, wie Sie dies auch für ein integriertes Analysetool tun.

Wenn das Ziel die Tokenisierung des gesamten Ausdrucks ist, empfehlen wir Ihnen die Verwendung eines benutzerdefinierten Analysetools mit einem **Schlüsselwort-Tokenizer** und einem **Kleinbuchstaben-Tokenfilter**.

+ Der Schlüsselwort-Tokenizer erstellt für den gesamten Inhalt eines Felds jeweils ein Token.
+ Mit dem Kleinbuchstaben-Tokenfilter werden Großbuchstaben in Kleinbuchstaben umgewandelt. Mit Abfrageparsern werden Texteingaben in Großbuchstaben normalerweise in Kleinbuchstaben umgewandelt. Bei der Umwandlung in Kleinbuchstaben werden die Eingaben mit den tokenisierten Ausdrücken homogenisiert.

Im folgenden Beispiel ist ein benutzerdefiniertes Analysetool dargestellt, das über einen Schlüsselwort-Tokenizer und einen Kleinbuchstaben-Tokenfilter verfügt.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> Der Tokenizer `keyword_v2` und der Tokenfilter `lowercase` sind dem System bekannt, und es werden die entsprechenden Standardkonfigurationen verwendet. Daher können Sie darauf anhand des Namens verweisen, ohne sie zuerst definieren zu müssen.

## <a name="4---build-and-test"></a>4 – Erstellen und Testen

Nachdem Sie einen Index mit Analysetools und Felddefinitionen definiert haben, die Ihr Szenario unterstützen, laden Sie Dokumente mit repräsentativen Zeichenfolgen, sodass Sie Abfragen von Teilzeichenfolgen testen können. 

In den vorherigen Abschnitten wurde die Logik erläutert. In diesem Abschnitt werden die einzelnen APIs erläutert, die beim Testen der Lösung aufgerufen werden sollten. Wie bereits erwähnt, können Sie diese Aufgaben schnell durchführen, wenn Sie ein interaktives Webtesttool wie Postman verwenden.

+ [Löschen des Index](/rest/api/searchservice/delete-index): Entfernt einen vorhandenen Index mit demselben Namen, damit Sie ihn neu erstellen können.

+ [Erstellen des Index](/rest/api/searchservice/create-index): Erstellt die Indexstruktur für den Suchdienst. Diese enthält Analyesetooldefinitionen und -felder mit einer Analysetoolspezifikation.

+ [Laden von Dokumenten](/rest/api/searchservice/addupdate-or-delete-documents): Importiert Dokumente, die dieselbe Struktur wie der Index aufweisen, sowie durchsuchbare Inhalte. Nach diesem Schritt kann der Index abgefragt oder getestet werden.

+ [Testen des Analysetools](/rest/api/searchservice/test-analyzer) wurde in [Festlegen eines Analysetools](#set-an-analyzer) beschrieben. Testen Sie einige der Zeichenfolgen im Index mithilfe verschiedener Analysetools, um zu verstehen, wie Ausdrücke tokenisiert werden.

+ [Durchsuchen von Dokumenten](/rest/api/searchservice/search-documents): Erläutert, wie eine Abfrageanforderung mithilfe [einfacher Syntax](query-simple-syntax.md) oder der [vollständigen Lucene-Syntax](query-lucene-syntax.md) für Platzhalter und reguläre Ausdrücke erstellt wird.

  Bei Abfragen von Teilausdrücken wie z. B. „3-6214“, um eine Entsprechung für „+ 1 (425) 703-6214“ zu finden, können Sie die einfache Syntax verwenden: `search=3-6214&queryType=simple`.

  Verwenden Sie für Infix- und Suffixabfragen wie z. B. „num“ oder „numerisch“, um eine Entsprechung für „alphanumerisch“ zu finden, die vollständige Lucene-Syntax und einen regulären Ausdruck: `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>Optimieren der Abfrageleistung

Wenn Sie die empfohlene Konfiguration mit dem Tokenizer „keyword_v2“ und dem Kleinbuchstaben-Tokenfilter implementieren, kommt es ggf. zu einer Verringerung der Abfrageleistung, weil zusätzlich zu den vorhandenen Token in Ihrem Index die Tokenfilterverarbeitung durchgeführt werden muss. 

Im folgenden Beispiel wird ein [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)-Element hinzugefügt, um den Präfixabgleich zu beschleunigen. Zusätzliche Token werden für Kombinationen mit 2 bis 25 Zeichen generiert: (nicht nur MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). 

Diese zusätzliche Tokenisierung führt natürlich zu einem größeren Index. Wenn Sie über ausreichend Kapazität für den größeren Index verfügen, ist dieser Ansatz mit der schnelleren Reaktionszeit möglicherweise eine bessere Lösung.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Analysetools sowohl Probleme bei Abfragen verursachen als auch zu deren Lösung beitragen können. Im nächsten Schritt soll genauer beschrieben werden, wie sich Analysetools auf die Indizierung und Abfrageverarbeitung auswirken. Informieren Sie sich besonders über die Nutzung der API für die Textanalyse, um eine tokenisierte Ausgabe zu erhalten. Sie können dann genau verfolgen, welche Elemente von einem Analysetool für Ihren Index erstellt werden.

+ [Sprachanalysen](search-language-support.md)
+ [Analysetools für Textverarbeitung in Azure Cognitive Search](search-analyzers.md)
+ [Text analysieren (Azure Cognitive Search-REST-API)](/rest/api/searchservice/test-analyzer)
+ [Funktionsweise der Volltextsuche (Abfragearchitektur)](search-lucene-query-architecture.md)