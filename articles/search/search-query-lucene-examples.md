---
title: Lucene-Abfragebeispiele – Azure Search
description: Die Lucene-Abfragesyntax für Fuzzysuche, NEAR-Suche, Term Boosting, Suche mit regulären Ausdrücken und Platzhaltersuche in einem Azure Search-Dienst.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 467c323a0b669e70e12f801fd8fdd6df119e793d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595904"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Abfragebeispiele, die „vollständige“ Lucene-Suchsyntax verwenden (erweiterte Abfragen in Azure Search)

Beim Erstellen von Abfragen für Azure Search können Sie den standardmäßigen [einfachen Abfrageparser](query-simple-syntax.md) durch den umfangreicheren [Lucene-Abfrageparser in Azure Search](query-lucene-syntax.md) ersetzen, um spezialisierte und erweiterte Abfragedefinitionen zu formulieren. 

Der Lucene-Parser unterstützt komplexe Abfragekonstrukte, z.B. feldbezogene Abfragen, Fuzzy- und Präfixplatzhaltersuche, NEAR-Suche, Term Boosting (Begriffsverstärkung) und die Suche mit regulären Ausdrücken. Die zusätzliche Leistung kommt mit zusätzlichen Verarbeitungsanforderungen, stellen Sie sich deshalb auf eine etwas längere Ausführungsdauer ein. In diesem Artikel können Sie Beispiele mit Abfragevorgängen durchlaufen, die bei Verwendung der vollständigen Syntax verfügbar sind.

> [!Note]
> Viele der spezialisierten Abfragekonstruktionen, die über die vollständige Lucene-Abfragesyntax aktiviert werden, verfügen nicht über eine [Textanalyse](search-lucene-query-architecture.md#stage-2-lexical-analysis). Dies kann zu Überraschungen führen, wenn Sie mit Wortstammerkennung und Lemmatisierung rechnen. Die lexikalische Analyse erfolgt nur für vollständige Begriffe (Begriffsabfrage oder Ausdrucksabfrage). Abfragetypen mit unvollständigen Begriffen (Präfixabfrage, Platzerhalterabfrage, Abfrage regulärer Ausdrücke, Fuzzyabfrage) werden direkt unter Umgehung der Analysephase an die Abfragestruktur angehängt. Die einzige Transformation, die für unvollständige Abfrageausdrücke durchgeführt wird, ist die Umwandlung in Kleinbuchstaben. 
>

## <a name="formulate-requests-in-postman"></a>Formulieren von Anforderungen in Postman

Der in den folgenden Beispielen verwendete Suchindex „NYC Jobs“ besteht aus Stellenangeboten basierend auf einem Dataset, das von der Initiative [City of New York OpenData](https://opendata.cityofnewyork.us/) bereitgestellt wird. Diese Daten sollten weder als aktuell noch als vollständig betrachtet werden. Der Index wird über einen Sandboxdienst von Microsoft bereitgestellt. Dies bedeutet, dass Sie kein Azure-Abonnement und keine Azure Search-Instanz benötigen, um diese Abfragen auszuprobieren.

Sie benötigen lediglich Postman oder ein gleichwertiges Tool zum Senden einer HTTP-Anforderung per GET. Weitere Informationen finden Sie unter [Untersuchen mit REST-Clients](search-fiddler.md).

### <a name="set-the-request-header"></a>Festlegen des Anforderungsheaders

1. Legen Sie im Anforderungsheader **Content-Type** auf `application/json` fest.

2. Fügen Sie einen **api-key** hinzu, und legen Sie ihn auf diese Zeichenfolge fest: `252044BE3886FE4A8E3BAA4F595114BB`. Dies ist ein Abfrageschlüssel für den Sandbox-Suchdienst, der den Index „NYC Jobs“ hostet.

Nachdem Sie den Anforderungsheader angegeben haben, können Sie ihn für alle Abfragen in diesem Artikel wiederverwenden, indem Sie lediglich die Zeichenfolge **search=** austauschen. 

  ![Postman-Anforderungsheader](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Festlegen der Anforderungs-URL

Die Anforderung ist ein GET-Befehl, der mit einer URL gekoppelt ist, in der der Azure Search-Endpunkt und die Suchzeichenfolge enthalten sind.

  ![Postman-Anforderungsheader](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Die URL-Komposition umfasst die folgenden Elemente:

+ **`https://azs-playground.search.windows.net/`** ist ein Sandbox-Suchdienst, der vom Entwicklungsteam von Azure Search gewartet wird. 
+ **`indexes/nycjobs/`** ist der Index „NYC Jobs“ in der Indexsammlung dieses Diensts. Sowohl der Dienstname als auch der Index sind für die Anforderung erforderlich.
+ **`docs`** ist die Dokumentsammlung, die den gesamten durchsuchbaren Inhalt enthält. Der im Anforderungsheader angegebene Abfrage-API-Schlüssel funktioniert nur für Lesevorgänge, die die Dokumentsammlung betreffen.
+ **`api-version=2019-05-06`** legt die API-Version fest. Dies ist für alle Anforderungen ein erforderlicher Parameter.
+ **`search=*`** ist die Abfragezeichenfolge, die in der ersten Abfrage null ist und (standardmäßig) die ersten 50 Ergebnisse zurückgibt.

## <a name="send-your-first-query"></a>Senden Ihrer ersten Abfrage

Fügen Sie als Überprüfungsschritt die folgende Anforderung in GET ein, und klicken Sie auf **Senden**. Die Ergebnisse werden als ausführliche JSON-Dokumente zurückgegeben. Es werden ganze Dokumente zurückgegeben, sodass Sie alle Felder und alle Werte sehen können.

Fügen Sie diese URL in einen REST-Client als Überprüfungsschritt und zum Anzeigen der Dokumentstruktur ein.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Die Abfragezeichenfolge **`search=*`** entspricht einer Suche ohne Angaben, die einer NULL-Suche oder leeren Suche gleicht. Es ist die einfachste Suche, die Sie ausführen können.

Optional können Sie der URL **`$count=true`** hinzufügen, um eine Anzahl für die Dokumente zurückzugeben, die die Suchkriterien erfüllen. In einer leeren Suchzeichenfolge entspricht dies allen Dokumenten im Index (ca. 2.800 für „NYC Jobs“).

## <a name="how-to-invoke-full-lucene-parsing"></a>Aufrufen der vollständigen Lucene-Analyse

Fügen Sie **queryType=full** hinzu, um die vollständige Abfragesyntax aufzurufen und die standardmäßige einfache Abfragesyntax außer Kraft zu setzen. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

In allen Beispielen in diesem Artikel wird der Suchparameter **queryType=full** verwendet. Damit wird angegeben, dass die vollständige Syntax vom Lucene-Abfrageparser verarbeitet wird. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Beispiel 1: Auf eine Liste von Feldern begrenzte Abfrage

Dieses erste Beispiel gilt nicht spezifisch für Lucene, es wird jedoch damit begonnen, um das erste Grundkonzept für Abfragen vorzustellen: der Feldbereich. In diesem Beispiel werden die gesamte Abfrage und die Antwort auf wenige bestimmte Felder beschränkt. Bei Verwendung des Tools Postman oder Suchexplorer ist es wichtig, zu wissen, wie eine lesbare JSON-Antwort strukturiert wird. 

Um das Beispiel kurz zu halten, zielt die Abfrage nur auf das Feld *business_title* ab, und es werden nur Berufsbezeichnungen zurückgegeben. Der Parameter **searchFields** schränkt die Abfrageausführung auf das Feld „business_title“ ein, und **select** gibt an, welche Felder in der Antwort enthalten sein sollen.

### <a name="partial-query-string"></a>Partielle Abfragezeichenfolge

```http
&search=*&searchFields=business_title&$select=business_title
```

Hier ist dieselbe Abfrage mit mehreren Feldern in einer Liste mit Kommas als Trennzeichen.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Die Leerzeichen nach den Kommas sind optional.

> [!Tip]
> Wenn Sie die REST-API über Ihren Anwendungscode verwenden, sollten Sie die URL-Codierung für Parameter wie `$select` und `searchFields` nicht vergessen.

### <a name="full-url"></a>Vollständige URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Die Antwort auf diese Abfrage sollte etwa wie im folgenden Screenshot aussehen.

  ![Postman-Beispielantwort](media/search-query-lucene-examples/postman-sample-results.png)

Möglicherweise ist Ihnen die Suchbewertung in der Antwort aufgefallen. Zu einer einheitlichen Bewertung von „1“ kommt es, wenn kein Rang vorhanden ist, weil es entweder keine Volltextsuche war oder weil keine Kriterien angewandt wurden. Bei einer NULL-Suche ohne Kriterien werden Zeilen in willkürlicher Reihenfolge zurückgegeben. Wenn Sie tatsächliche Suchkriterien einfügen, werden aussagekräftige Werte für die Suchbewertungen angezeigt.

## <a name="example-2-fielded-search"></a>Beispiel 2: Feldbezogene Suche

Die vollständige Lucene-Syntax unterstützt die Bereichsdefinition einzelner Ausdrücke auf ein bestimmtes Feld. Dieses Beispiel sucht nach Geschäftstiteln, in denen der Begriff „Senior“, jedoch nicht der Begriff „Junior“ enthalten ist.

### <a name="partial-query-string"></a>Partielle Abfragezeichenfolge

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Hier ist dieselbe Abfrage mit mehreren Feldern.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Vollständige URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Postman-Beispielantwort](media/search-query-lucene-examples/intrafieldfilter.png)

Sie können einen feldbezogenen Suchvorgang mit der **fieldName:searchExpression**-Syntax definieren, wobei es sich bei dem Suchausdruck um ein einzelnes Wort, einen einfachen Ausdruck oder einen komplexeren Ausdruck in Klammern handeln kann, optional mit booleschen Operatoren. Beispiele hierfür sind:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Achten Sie darauf, dass Sie mehrere Zeichenfolgen in Anführungszeichen setzen, wenn beide Zeichenfolgen als einzelne Entität ausgewertet werden sollen, da in diesem Fall im Feld `state` nach zwei verschiedenen Standorten gesucht wird. Stellen Sie außerdem sicher, dass der Operator großgeschrieben wird, wie im Fall von NOT und AND.

Das in **fieldName:searchExpression** angegebene Feld muss durchsuchbar sein. Einzelheiten zur Verwendung von Indexattributen in Felddefinitionen finden Sie unter [Index erstellen (REST-API in Azure Search-Dienst)](https://docs.microsoft.com/rest/api/searchservice/create-index) .

> [!NOTE]
> Im obigen Beispiel war der Parameter `searchFields` nicht erforderlich, da für jeden Teil der Abfrage Feldname explizit angegeben ist. Allerdings können Sie den Parameter `searchFields` trotzdem verwenden, wenn Sie eine Abfrage ausführen möchten, bei der einige Teile auf ein bestimmtes Feld beschränkt sind, der Rest sich jedoch auf mehrere Felder beziehen kann. Zum Beispiel würde `senior NOT junior` in der Abfrage `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` nur mit dem Feld `business_title` und „external“ mit dem Feld `posting_type` abgeglichen werden. Der in **fieldName:searchExpression** angegebene Feldname hat immer Vorrang vor dem Parameter `searchFields`, weshalb `business_title` in diesem Beispiel nicht in den Parameter `searchFields` aufgenommen werden muss.

## <a name="example-3-fuzzy-search"></a>Beispiel 3: Fuzzysuche

Die vollständige Lucene-Syntax unterstützt auch die Fuzzysuche, bei der sich Übereinstimmungen für Begriffe ergeben, die über eine ähnliche Konstruktion verfügen. Hängen Sie für eine Fuzzysuche das Tildesymbol `~` an das Ende eines einzelnen Worts mit einem optionalen Parameter an, einem Wert zwischen 0 und 2, der die Editierdistanz angibt. Beispielsweise würden bei `blue~` oder `blue~1` die Werte „blue“, „blues“ und „glue“ zurückgegeben.

### <a name="partial-query-string"></a>Partielle Abfragezeichenfolge

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Ausdrücke werden nicht direkt unterstützt, Sie können jedoch eine Fuzzyübereinstimmung für Komponententeile eines Ausdrucks angeben.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Vollständige URL

Diese Abfrage sucht nach Stellenangeboten mit dem Begriff „Associate“ (absichtlich falsch geschrieben):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Antwort für die Fuzzysuche](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Fuzzyabfragen werden nicht [analysiert](search-lucene-query-architecture.md#stage-2-lexical-analysis). Abfragetypen mit unvollständigen Begriffen (Präfixabfrage, Platzerhalterabfrage, Abfrage regulärer Ausdrücke, Fuzzyabfrage) werden direkt unter Umgehung der Analysephase an die Abfragestruktur angehängt. Die einzige Transformation, die für unvollständige Abfrageausdrücke durchgeführt wird, ist die Umwandlung in Kleinbuchstaben.
>

## <a name="example-4-proximity-search"></a>Beispiel 4: NEAR-Suche
NEAR-Suchen werden verwendet, um Begriffe zu suchen, die in einem Dokument nahe beieinander liegen. Fügen Sie ein Tildesymbol „~“ Symbol am Ende eines Ausdrucks ein, gefolgt von der Anzahl der Wörter, die den NEAR-Bereich bilden. Beispielsweise finden Sie mit der Abfrage „"Hotel Flughafen"~5“ die Begriffe „Hotel“ und „Flughafen“, wenn sie innerhalb von 5 Wörtern in einem Dokument vorkommen.

### <a name="partial-query-string"></a>Partielle Abfragezeichenfolge

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Vollständige URL

Diese Abfrage sucht nach Stellen mit dem Begriff „Senior Analyst“, wobei die beiden Wörter durch höchstens ein Wort getrennt sein dürfen:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Näherungsabfrage](media/search-query-lucene-examples/proximity-before.png)

Versuchen Sie es noch einmal, und entfernen Sie die Wörter zwischen „Senior“ und „Analyst“. Beachten Sie, dass für diese Abfrage acht Dokumente zurückgegeben werden, während es für die vorherige Abfrage zehn waren.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Beispiel 5: Term Boosting
Die Begriffsverstärkung (Term Boosting) bezieht sich auf das Höherbewerten eines Dokuments, wenn es den verstärkten Begriff enthält, im Verhältnis zu Dokumenten, die den Begriff nicht enthalten. Verwenden Sie zum Verstärken eines Begriffs das Caretzeichen „^“ mit einem Verstärkungsfaktor (einer Zahl) am Ende des Begriffs, nach dem Sie suchen. 

### <a name="full-urls"></a>Vollständige URLs

In dieser „Vorher“-Abfrage wird nach Stellen mit dem Begriff *computer analyst* gesucht. Beachten Sie, dass keine Ergebnisse mit den beiden Wörtern *computer* und *analyst* vorhanden sind, aber dass Stellen mit *computer* in den Ergebnissen oben angezeigt werden.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Term Boosting davor](media/search-query-lucene-examples/termboostingbefore.png)

Wiederholen Sie die Suche mit der „Nachher“-Abfrage, und verstärken Sie die Ergebnisse nun mit dem Begriff *analyst* für den Begriff *computer*, falls nicht beide Wörter vorhanden sind. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Eine für Menschen besser lesbare Version der obigen Abfrage lautet `search=business_title:computer analyst^2`. Bei einer geeigneten Abfrage wird `^2` als `%5E2` codiert, und dies ist nicht so leicht erkennbar.

  ![Term Boosting danach](media/search-query-lucene-examples/termboostingafter.png)

Das Term Boosting unterscheidet sich von Bewertungsprofilen darin, dass bei Bewertungsprofilen anstelle von bestimmten Begriffen bestimmte Felder verstärkt werden. Im folgenden Beispiel werden die Unterschiede veranschaulicht.

Betrachten Sie ein Bewertungsprofil, das Übereinstimmungen in einem bestimmten Feld verstärkt, beispielsweise **genre** im musicstoreindex-Beispiel. Mit der Begriffsverstärkung könnten Sie bestimmte Suchbegriffe noch höher bewerten als andere. Zum Beispiel werden mit „rock^2 electronic“ Dokumente, die die Suchbegriffe im Feld **genre** enthalten, höher eingestuft als andere durchsuchbare Felder im Index. Darüber hinaus werden Dokumente, die den Suchbegriff „Rock“ enthalten, höher eingestuft als der andere Suchbegriff „electronic“ – aufgrund des Werts (2) für die Begriffsverstärkung.

Je höher beim Festlegen der Faktorebene der Verstärkungsfaktor ist, desto relevanter wird der Begriff im Verhältnis zu anderen Suchbegriffen. Der Standardverstärkungsfaktor ist 1. Der Verstärkungsfaktor muss zwar positiv, kann jedoch kleiner als 1 sein (z. B. „0.2“).


## <a name="example-6-regex"></a>Beispiel 6: RegEx

Bei einer Suche mit regulärem Ausdruck werden Übereinstimmungen basierend auf dem Inhalt zwischen Schrägstrichen „/“ gefunden, wie in der [RegExp-Klasse](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)dokumentiert.

### <a name="partial-query-string"></a>Partielle Abfragezeichenfolge

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Vollständige URL

Suchen Sie in dieser Abfrage nach Stellenangeboten mit dem Begriff „Senior“ oder „Junior“: `search=business_title:/(Sen|Jun)ior/`

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![RegEx-Abfrage](media/search-query-lucene-examples/regex.png)

> [!Note]
> Abfragen mit regulärem Ausdruck werden nicht [analysiert](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Die einzige Transformation, die für unvollständige Abfrageausdrücke durchgeführt wird, ist die Umwandlung in Kleinbuchstaben.
>

## <a name="example-7-wildcard-search"></a>Beispiel 7: Platzhaltersuche
Sie können die allgemein bekannte Syntax für die Platzhaltersuche nach mehreren (\*) oder einzelnen (?) Zeichen verwenden. Beachten Sie, dass der Lucene-Abfrageparser die Verwendung dieser Symbole bei einem einzelnen Begriff, nicht bei einem Ausdruck, unterstützt.

### <a name="partial-query-string"></a>Partielle Abfragezeichenfolge

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Vollständige URL

Mit dieser Abfrage wird nach Stellen gesucht, die das Präfix „Prog“ enthalten. Dazu gehören beispielsweise Berufsbezeichnungen mit den Begriffen „Programmierer“ und „Programmierung“. Sie können die Symbole * oder ? nicht als erstes Zeichen in einer Suche verwenden.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Platzhalterabfrage](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Abfragen mit Platzhaltern werden nicht [analysiert](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Die einzige Transformation, die für unvollständige Abfrageausdrücke durchgeführt wird, ist die Umwandlung in Kleinbuchstaben.
>

## <a name="next-steps"></a>Nächste Schritte
Versuchen Sie, den Lucene-Abfrageparser in Ihrem Code anzugeben. Unter den folgenden Links wird erläutert, wie Sie Suchabfragen sowohl für .NET als auch für die REST-API einrichten. Bei diesen Links wird die einfache Standardsyntax „simple“ verwendet, daher müssen Sie das in diesem Artikel Gelernte anwenden, um den **queryType**anzugeben.

* [Abfragen des Azure Search-Indexes mit dem .NET SDK](search-query-dotnet.md)
* [Abfragen des Azure Search-Indexes mit der REST-API](search-create-index-rest-api.md)

Eine zusätzliche Syntaxreferenz, eine Abfragearchitektur und Beispiele finden Sie unter den folgenden Links:

+ [Beispiele für Abfragen mit einfacher Syntax](search-query-simple-examples.md)
+ [Funktionsweise der Volltextsuche in Azure Search](search-lucene-query-architecture.md)
+ [Einfache Abfragesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Vollständige Lucene-Abfragesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)