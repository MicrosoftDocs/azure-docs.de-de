---
title: 'Tutorial: Erstellen eines benutzerdefinierten Analysetools'
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie ein benutzerdefiniertes Analysetool erstellen, um die Qualität der Suchergebnisse in Azure Cognitive Search zu verbessern.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097656"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Tutorial: Erstellen eines benutzerdefinierten Analysetools für Telefonnummern

[Analysetools](search-analyzers.md) sind eine wichtige Komponente in jeder Suchlösung. Um die Qualität der Suchergebnisse zu verbessern, ist es wichtig zu verstehen, wie Analysetools funktionieren und welche Auswirkungen sie auf diese Ergebnisse haben.

In einigen Fällen, z. B. bei einem Freitextfeld, verbessert bereits die Auswahl des richtigen [Sprachanalysetools](index-add-language-analyzers.md) die Suchergebnisse. Einige Szenarien, wie z. B. das genaue Suchen nach Telefonnummern, URLs oder E-Mails, erfordern jedoch möglicherweise die Verwendung benutzerdefinierter Analysetools.

In diesem Tutorial werden Postman und die [REST-APIs](/rest/api/searchservice/) von Azure Cognitive Search für die folgenden Aufgaben verwendet:

> [!div class="checklist"]
> * Erläutern der Funktionsweise von Analysetools
> * Definieren eines benutzerdefinierten Analysetools zum Suchen nach Telefonnummern
> * Testen, wie das benutzerdefinierte Analysetool Text mit Token versieht
> * Erstellen separater Analysetools für die Indizierung und Suche zur weiteren Verbesserung der Ergebnisse

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial sind folgende Dienste und Tools erforderlich:

+ [Postman-Desktop-App](https://www.getpostman.com/)
+ [Neuer](search-create-service-portal.md) oder [bereits vorhandener](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Suchdienst

## <a name="download-files"></a>Herunterladen von Dateien

Den Quellcode für dieses Tutorial finden Sie im Ordner [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) im GitHub-Repository [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples).

## <a name="1---create-azure-cognitive-search-service"></a>1\. Erstellen eines Azure Cognitive Search-Diensts

Den für dieses Tutorial erforderlichen Azure Cognitive Search-Dienst können Sie [über das Portal erstellen](search-create-service-portal.md). Im Rahmen dieser exemplarischen Vorgehensweise können Sie den Free-Tarif verwenden.

Im nächsten Schritt müssen Sie den Namen des Suchdiensts und den zugehörigen API-Schlüssel kennen. Wenn Sie nicht sicher sind, wie Sie diese Elemente ermitteln, sehen Sie sich diesen [Schnellstart](search-create-service-portal.md#get-a-key-and-url-endpoint) an.


## <a name="2---set-up-postman"></a>2\. Einrichten von Postman

Starten Sie anschließend Postman, und importieren Sie die Sammlung, die Sie aus [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) heruntergeladen haben.

Um die Sammlung zu importieren, navigieren Sie zu **Dateien** > **Importieren**, und wählen Sie dann die Sammlungsdatei aus, die Sie importieren möchten.

Für jede Anforderung müssen Sie die folgenden Schritte ausführen:

1. Ersetzen Sie `<YOUR-SEARCH-SERVICE>` durch den Namen Ihres Suchdiensts.

1. Ersetzen Sie `<YOUR-ADMIN-API-KEY>` durch den primären oder sekundären Schlüssel Ihres Suchdiensts.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Postman-Anforderungs-URL und -Header" border="false":::

Wenn Sie mit Postman nicht vertraut sind, lesen Sie [Schnellstart: Erstellen eines Azure Cognitive Search-Index mithilfe von REST-APIs](search-get-started-rest.md).

## <a name="3---create-an-initial-index"></a>3\. Erstellen eines Anfangsindexes

In diesem Schritt erstellen wir einen Anfangsindex, laden Dokumente in den Index und fragen dann die Dokumente dann ab, um zu sehen, wie die anfänglichen Suchvorgänge durchgeführt werden.

### <a name="create-index"></a>Erstellen eines Index

Zunächst erstellen wir einen einfachen Index namens `tutorial-basic-index` mit zwei Feldern: `id` und `phone_number`. Wir haben noch kein Analysetool definiert, sodass das `standard.lucene`-Analysetool standardmäßig verwendet wird.

Um den Index zu erstellen, senden wir die folgende Anforderung:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Laden der Daten

Dann laden wir Daten in den Index. In einigen Fällen haben Sie möglicherweise keine Kontrolle über das Format der erfassten Telefonnummern, daher testen wir auf unterschiedliche Arten von Formaten. Im Idealfall gibt eine Suchlösung unabhängig von ihrem Format alle übereinstimmenden Telefonnummern zurück.

Die Daten werden mithilfe der folgenden Anforderung in den Index geladen: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Mit den Daten im Index sind wir bereit, mit der Suche zu beginnen.

### <a name="search"></a>Suchen

Um die Suche intuitiv zu gestalten, sollten Sie nicht erwarten, dass Benutzer Abfragen auf bestimmte Weise formatieren. Ein Benutzer könnte nach `(425) 555-0100` in einem der oben gezeigten Formate suchen und dennoch erwarten, dass Ergebnisse zurückgegeben werden. In diesem Schritt testen wir einige Beispielabfragen, um zu sehen, wie sie funktionieren.

Wir beginnen mit der Suche nach `(425) 555-0100`:

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

Diese Abfrage gibt **drei von vier erwarteten Ergebnissen zurück**, jedoch auch **zwei unerwartete Ergebnisse**:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Nun suchen wir nach einer Nummer ohne Formatierung: `4255550100`.

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Diese Abfrage schneidet noch schlechter ab, da sie nur **eine von vier richtigen Übereinstimmungen** zurückgibt.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Wenn diese Ergebnisse verwirrend finden, sind Sie nicht allein. Im nächsten Abschnitt untersuchen wir, warum wir diese Ergebnisse erhalten.

## <a name="4---debug-search-results"></a>4\. Debuggen der Suchergebnisse

Um diese Suchergebnisse zu verstehen, ist es wichtig, zunächst zu verstehen, wie Analysetools funktionieren. Anschließend können wir das Standardanalysetool mit der [Textanalyse-API](/rest/api/searchservice/test-analyzer) testen und dann ein Analysetool erstellen, das unseren Anforderungen entspricht.

### <a name="how-analyzers-work"></a>Funktionsweise von Analysetools

Ein [Analysetool](search-analyzers.md) ist eine Komponente der [Volltext-Suchmaschine](search-lucene-query-architecture.md), mit der Text in Abfragezeichenfolgen und indizierten Dokumenten verarbeitet wird. Verschiedene Analysetools bearbeiten Text je nach Szenario auf verschiedene Arten. In diesem Szenario müssen wir ein Analysetool erstellen, das auf Telefonnummern zugeschnitten ist.

Analysetools bestehen aus drei Komponenten:

+ Aus [**Zeichenfiltern**](#CharFilters), die einzelne Zeichen aus dem Eingabetext entfernen oder ersetzen.
+ Aus einem [**Tokenizer**](#Tokenizers), der den Eingabetext in Token zerlegt, die zu Schlüsseln im Suchindex werden.
+ Aus [**Tokenfiltern**](#TokenFilters), die die vom Tokenizer generierten Token manipulieren.

In der folgenden Abbildung sehen Sie, wie diese drei Komponenten zusammenwirken, um einen Satz in Token zu zerlegen:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Abbildung des Analysetoolvorgangs zum Zerlegen eines Satzes in Token":::

Diese Token werden dann in einem invertierten Index gespeichert, der eine schnelle Volltextsuche ermöglicht.  Ein invertierter Index ermöglicht Volltextsuche, indem er alle eindeutigen Begriffe, die bei der lexikalischen Analyse extrahiert wurden, den Dokumenten zuordnet, in denen sie vorkommen. Ein Beispiel sehen Sie in der Abbildung unten:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Beispiel für invertierten Index":::

Die gesamte Suche läuft auf die Suche nach den im invertierten Index gespeicherten Begriffen hinaus. Wenn ein Benutzer eine Abfrage ausgibt:

1. Wird die Abfrage analysiert, und die Abfragebegriffe werden analysiert.
1. Der invertierte Index wird dann nach Dokumenten mit übereinstimmenden Begriffen gescannt.
1. Schließlich wir die Rangfolge der abgerufenen Dokumente durch den [Ähnlichkeitsalgorithmus](index-ranking-similarity.md) ermittelt.

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Abbildung des Analysetoolvorgangs zum Ermitteln der Ähnlichkeitsrangfolge":::

Wenn die Abfragebegriffe nicht mit den Begriffen in Ihrem invertierten Index übereinstimmen, werden keine Ergebnisse zurückgegeben. Weitere Informationen zur Funktionsweise von Abfragen finden Sie im Artikel zur [Volltextsuche](search-lucene-query-architecture.md).

> [!Note]
> [Bei partiellen Begriffsabfragen](search-query-partial-matching.md) handelt es sich um eine wichtige Ausnahme von dieser Regel. Diese Abfragen (Präfixabfrage, Platzhalterabfrage, Regex-Abfrage) umgehen den lexikalischen Analyseprozess im Gegensatz zu regulären Begriffsabfragen. Partielle Begriffe werden nur in Kleinbuchstaben umgewandelt, bevor Sie mit Begriffen im Index verglichen werden. Wenn ein Analysetool nicht für die Unterstützung dieser Abfrage Typen konfiguriert ist, erhalten Sie häufig unerwartete Ergebnisse, weil keine übereinstimmenden Begriffe im Index vorhanden sind.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Testen des Analysetools mit der Textanalyse-API

Azure Cognitive Search stellt eine [Textanalyse-API](/rest/api/searchservice/test-analyzer) zur Verfügung, die es Ihnen ermöglicht, Analysetools zu testen, um zu verstehen, wie sie Text verarbeiten.

Die Textanalyse-API wird mithilfe der folgenden Anforderung aufgerufen:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

Die API gibt dann eine Liste der aus dem Text extrahierten Token zurück. Sie können sehen, dass das Lucene-Standardanalysetool die Telefonnummer in drei separate Token unterteilt:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

Umgekehrt wird die ohne jegliche Interpunktion formatierte Telefonnummer `4255550100` in ein einziges Token umgewandelt.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Denken Sie daran, dass sowohl die Suchbegriffe als auch die indizierten Dokumente analysiert werden. Wenn wir an die Suchergebnisse aus dem vorherigen Schritt zurückdenken, können wir anfangen zu verstehen, warum diese Ergebnisse zurückgegeben wurden.

Bei der ersten Abfrage wurden die falschen Telefonnummern zurückgegeben, weil einer ihrer Begriffe (`555`) mit einem der von uns gesuchten Begriffe übereinstimmte. In der zweiten Abfrage wurde nur die eine Nummer zurückgegeben, da dies der einzige Datensatz war, bei dem ein Begriff mit `4255550100` übereinstimmte.

## <a name="5---build-a-custom-analyzer"></a>5\. Erstellen eines benutzerdefinierten Analysetools

Da wir nun die Ergebnisse verstehen, die ausgegeben werden, erstellen wir ein benutzerdefiniertes Analysetool, um die Tokenisierungslogik zu verbessern.

Ziel ist es, eine intuitive Suche nach Telefonnummern zu ermöglichen, unabhängig davon, in welchem Format die Abfrage oder die indizierte Zeichenfolge vorliegt. Um dieses Ergebnis zu erzielen, geben wir einen [Zeichenfilter](#CharFilters), einen [Tokenizer](#Tokenizers) und einen [Tokenfilter](#TokenFilters) an.

<a name="CharFilters"></a>

### <a name="character-filters"></a>Zeichenfilter

Zeichenfilter werden verwendet, um Text zu verarbeiten, bevor er an den Tokenizer übergeben wird. Zu den häufigen Verwendungsmöglichkeiten von Zeichenfiltern gehören das Filtern von HTML-Elementen oder das Ersetzen von Sonderzeichen.

Bei Telefonnummern möchten wir Leerzeichen und Sonderzeichen entfernen, da nicht alle Telefonnummerformate dieselben Sonderzeichen und Leerzeichen enthalten.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

Mit dem Filter oben werden die Zeichen `-` `(` `)` `+` `.` und Leerzeichen aus der Eingabe entfernt.

|Eingabe|Output|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizer

Tokenizer teilen Text in Token auf und verwerfen einige Zeichen, z. B. Interpunktion. In vielen Fällen besteht das Ziel der Tokenisierung darin, einen Satz in einzelne Wörter aufzuteilen.

In diesem Szenario verwenden wir einen Schlüsselworttokenizer (`keyword_v2`), weil wir die Telefonnummer als einen einzelnen Begriff erfassen möchten. Beachten Sie, dass dies nicht die einzige Möglichkeit ist, dieses Problem zu lösen. Weitere Informationen finden Sie unten im Abschnitt [Alternative Ansätze](#Alternate).

Schlüsselworttokenizer geben immer denselben Text aus, den sie als einen einzigen Begriff erhalten haben.

|Eingabe|Output|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Tokenfilter

Tokenfilter werden verwendet, um die von einem Tokenizer generierten Token herauszufiltern oder zu ändern. Eine häufige Verwendung eines Tokenfilters besteht darin, alle Zeichen mithilfe eines Kleinbuchstabentokenfilters in Kleinbuchstaben umzuwandeln. Eine weitere häufige Verwendung ist das Herausfiltern von Stoppwörtern wie `the`, `and` oder `is`.

Während wir für dieses Szenario keinen dieser beiden Filter verwenden müssen, werden wir einen nGram-Token-Filter verwenden, um eine partielle Suche nach Telefonnummern zu ermöglichen.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

Der [nGram_v2-Tokenfilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) teilt Token basierend auf den Parametern `minGram` und `maxGram` in n-Gramme einer bestimmten Größe auf.

Für das Telefonnummern-Analysetool legen wir `minGram` auf `3` fest, da dies die kürzeste Teilzeichenfolge ist, nach der Benutzern wahrscheinlich suchen. `maxGram` wird auf `20` festgelegt, um sicherzustellen, dass alle Telefonnummern (selbst mit Durchwahlen) in ein einzelnes n-Gramm passen.

 Der unglückliche Nebeneffekt von n-Grammen besteht darin, dass einige falsch positive Ergebnisse zurückgegeben werden. Dieses Problem wird in Schritt 7 behoben, indem ein separates Analysetool für Suchvorgänge erstellt wird, das nicht den n-Gramm-Tokenfilter enthält.

|Eingabe|Output|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analysetool

Nachdem wir die Zeichenfilter, den Tokenizer und die Tokenfilter eingerichtet haben, können wir unser Analysetool definieren.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Eingabe|Output|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Beachten Sie, dass nun beliebige Token in der Ausgabe durchsucht werden können. Wenn unsere Abfrage eines dieser Token enthält, wird die Telefonnummer zurückgegeben.

Da das benutzerdefinierte Analysetool nun definiert wurde, erstellen Sie den Index neu, sodass das benutzerdefinierte Analysetool im nächsten Schritt zum Testen zur Verfügung steht. Der Einfachheit halber erstellt die Postman-Sammlung einen neuen Index mit dem Namen `tutorial-first-analyzer` mit dem von uns definierten Analysetool.

## <a name="6---test-the-custom-analyzer"></a>6\. Testen eines benutzerdefinierten Analysetools

Nachdem Sie den Index erstellt haben, können Sie das Analysetool nun mithilfe der folgenden Anforderung testen:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Anschließend können Sie die Sammlung der sich aus der Telefonnummer ergebenden Token anzeigen:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7\. Erstellen eines benutzerdefinierten Analysetools für Abfragen

Nachdem Sie einige Beispielabfragen für den Index mit dem benutzerdefinierten Analysetool durchgeführt haben, werden Sie feststellen, dass sich die Trefferquote verbessert hat und nun alle übereinstimmenden Telefonnummern zurückgegeben werden. Der n-Gramm-Tokenfilter bewirkt jedoch, dass auch einige falsch positive Ergebnisse zurückgegeben werden. Dies ist ein gängiger Nebeneffekt eines n-Gramm-Tokenfilters.

Um falsch positive Ergebnisse zu vermeiden, erstellen wir ein separates Analysetool für Abfragen. Dieser Analysetool ist mit dem Analysetool identisch, das wir bereits erstellt haben, aber **, ohne** den `custom_ngram_filter`.

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

In der Indexdefinition geben wir dann ein `indexAnalyzer`- und ein `searchAnalyzer`-Element an.

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Mit dieser Änderung haben Sie alles erledigt. Erstellen Sie den Index neu, indizieren Sie die Daten, und testen Sie die Abfragen erneut, um sicherzustellen, dass die Suche erwartungsgemäß funktioniert. Wenn Sie die Postman-Sammlung verwenden, wird ein dritter Index mit dem Namen `tutorial-second-analyzer` erstellt.

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Alternative Ansätze

Das oben beschriebene Analysetool wurde entwickelt, um die Flexibilität der Suche zu maximieren. Dies geschieht jedoch auf Kosten der Speicherung vieler potenziell unwichtiger Begriffe im Index.

Das folgende Beispiel zeigt ein anderes Analysetool, das auch für diese Aufgabe verwendet werden kann. 

Das Analysetool funktioniert gut, außer bei Eingabedaten wie `14255550100`, die es schwierig machen, die Telefonnummer logisch aufzuschlüsseln. Beispielsweise wäre das Analysetool nicht in der Lage, den Ländercode `1` von der Vorwahl `425`zu trennen. Diese Diskrepanz würde dazu führen, dass die Nummer oben nicht zurückgegeben wird, wenn ein Benutzer bei seiner Suche keinen Ländercode angibt.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

Im folgenden Beispiel sehen Sie, dass die Telefonnummer in die Blöcke aufgeteilt wird, nach denen ein Benutzer normalerweise suchen würde.

|Eingabe|Output|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

Je nach Ihren Anforderungen ist dies möglicherweise ein effizienterer Ansatz für das Problem.

## <a name="reset-and-rerun"></a>Zurücksetzen und erneut ausführen

Der Einfachheit halber haben Sie in diesem Tutorial drei neue Indizes erstellt. Es ist jedoch üblich, Indizes in den frühen Phasen der Entwicklung zu löschen und neu zu erstellen. Sie können einen Index im Azure-Portal löschen oder den folgenden API-Aufruf verwenden:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Wesentliche Punkte

In diesem Tutorial wurde der Prozess zum Erstellen und Testen eines benutzerdefinierten Analysetools veranschaulicht. Sie haben einen Index erstellt, Daten indiziert und dann anhand des Index abgefragt, um zu sehen, welche Suchergebnisse zurückgegeben wurden. Dann haben Sie die Textanalyse-API verwendet, um den lexikalischen Analyseprozess in Aktion zu sehen.

Während das in diesem Tutorial definierte Analysetool eine einfache Lösung für die Suche nach Telefonnummern bietet, kann derselbe Prozess verwendet werden, um ein benutzerdefiniertes Telefonnummern-Analysetool für jedes denkbare Szenario zu erstellen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, ist es ratsam, nach Abschluss eines Projekts die nicht mehr benötigten Ressourcen zu entfernen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link „Alle Ressourcen“ oder „Ressourcengruppen“ im linken Navigationsbereich gesucht und verwaltet werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit dem Erstellen eines benutzerdefinierten Analysetools vertraut sind, sehen wir uns die verschiedenen Filter, Tokenizer und Analysetools an, die Ihnen zur Verfügung stehen, um eine umfangreiche Suchfunktion zu erstellen.

> [!div class="nextstepaction"]
> [Benutzerdefinierte Analysetools in Azure Cognitive Search](index-add-custom-analyzers.md)