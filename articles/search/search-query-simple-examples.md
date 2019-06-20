---
title: Abfragebeispiele mit „einfacher“ Suchsyntax – Azure Search
description: Enthält einfache Abfragebeispiele für die Volltextsuche, Filtersuche, geografische Suche, Facettensuche und andere Abfragezeichenfolgen, die zum Abfragen eines Azure Search-Index verwendet werden.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0c47212e51725e7d4a173c441709dca739d4e357
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024532"
---
# <a name="query-examples-using-the-simple-search-syntax-in-azure-search"></a>Abfragebeispiele mit „einfacher“ Suchsyntax in Azure Search

Bei der [einfachen Abfragesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) wird der Standardabfrageparser aufgerufen, um Volltextsuchabfragen für einen Azure Search-Index auszuführen. Die einfache Abfrageanalyse ist schnell und verarbeitet gängige Szenarien in Azure Search, z.B. Volltextsuche, Filter- und Facettensuche und geografische Suche. In diesem Artikel werden anhand von Beispielen Abfragevorgänge, die bei Verwendung der einfachen Syntax verfügbar sind, Schritt für Schritt beschrieben.

Die Alternative zu dieser Abfragesyntax ist die [vollständige Lucene-Abfragesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), bei der auch komplexere Abfragestrukturen unterstützt werden, z.B. Fuzzy- und Platzhaltersuche, sodass sich die Verarbeitungsdauer verlängern kann. Weitere Informationen und Beispiele zur Veranschaulichung der vollständigen Syntax finden Sie unter [Beispiele für Lucene-Syntaxabfragen](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulieren von Anforderungen in Postman

Der in den folgenden Beispielen verwendete Suchindex „NYC Jobs“ besteht aus Stellenangeboten basierend auf einem Dataset, das von der Initiative [City of New York OpenData](https://nycopendata.socrata.com/) bereitgestellt wird. Diese Daten sollten weder als aktuell noch als vollständig betrachtet werden. Der Index wird über einen Sandboxdienst von Microsoft bereitgestellt. Dies bedeutet, dass Sie kein Azure-Abonnement und keine Azure Search-Instanz benötigen, um diese Abfragen auszuprobieren.

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

Die Abfragezeichenfolge **`search=*`** entspricht einer Suche ohne Angaben, die einer NULL-Suche oder leeren Suche gleicht. Dies hat keinen besonderen Nutzen, aber es ist die einfachste Suche, die Sie durchführen können.

Optional können Sie der URL **`$count=true`** hinzufügen, um eine Anzahl für die Dokumente zurückzugeben, die die Suchkriterien erfüllen. In einer leeren Suchzeichenfolge entspricht dies allen Dokumenten im Index (ca. 2.800 für „NYC Jobs“).

## <a name="how-to-invoke-simple-query-parsing"></a>Aufrufen der einfachen Abfrageanalyse

Für interaktive Abfragen müssen Sie nichts angeben: einfache Abfragen sind die Standardeinstellung. Wenn Sie zuvor **queryType=full** für die vollständige Abfragesyntax aufgerufen haben, können Sie im Code die Standardeinstellung wiederherstellen, indem Sie **queryType=simple** verwenden.

## <a name="example-1-field-scoped-query"></a>Beispiel 1: Feldbezogene Abfrage

Dieses erste Beispiel ist nicht parserspezifisch, wir beginnen jedoch mit diesem Beispiel, um das erste Grundkonzept für Abfragen vorzustellen: die Eingrenzung. In diesem Beispiel beschränken sich die Abfrageausführung und die Antwort auf nur wenige bestimmte Felder. Bei Verwendung des Tools Postman oder Suchexplorer ist es wichtig, zu wissen, wie eine lesbare JSON-Antwort strukturiert wird. 

Um das Beispiel kurz zu halten, zielt die Abfrage nur auf das Feld *business_title* ab, und es werden nur Berufsbezeichnungen zurückgegeben. Die Syntax lautet **searchFields**, um die Abfrageausführung nur auf das Feld „business_title“ zu beschränken, und **select**, um anzugeben, welche Felder in der Antwort enthalten sind.

### <a name="partial-query-string"></a>Partielle Abfragezeichenfolge

```http
searchFields=business_title&$select=business_title&search=*
```

Hier ist dieselbe Abfrage mit mehreren Feldern in einer Liste mit Kommas als Trennzeichen.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Vollständige URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

Die Antwort auf diese Abfrage sollte etwa wie im folgenden Screenshot aussehen.

  ![Postman-Beispielantwort](media/search-query-lucene-examples/postman-sample-results.png)

Möglicherweise ist Ihnen die Suchbewertung in der Antwort aufgefallen. Zu einer einheitlichen Bewertung von „1“ kommt es, wenn kein Rang vorhanden ist, weil es entweder keine Volltextsuche war oder weil keine Kriterien angewandt wurden. Bei einer NULL-Suche ohne Kriterien werden Zeilen in willkürlicher Reihenfolge zurückgegeben. Wenn Sie tatsächliche Kriterien einfügen, werden aussagekräftige Werte für die Suchbewertungen angezeigt.

## <a name="example-2-look-up-by-id"></a>Beispiel 2: Suchen anhand der ID

Dieses Beispiel ist etwas ungewöhnlich. Beim Auswerten des Suchverhaltens ist es aber ratsam, den gesamten Inhalt eines bestimmten Dokuments zu untersuchen, um zu ermitteln, warum er in die Ergebnisse einbezogen bzw. ausgeschlossen wurde. Verwenden Sie zum Zurückgeben eines einzelnen Dokuments in seiner Gesamtheit einen [Suchvorgang](https://docs.microsoft.com/rest/api/searchservice/lookup-document), um die Dokument-ID zu übergeben.

Alle Dokumente verfügen über einen eindeutigen Bezeichner. Beim Ausprobieren der Syntax für eine Suchabfrage können Sie zuerst eine Liste mit Dokument-IDs zurückgeben, um eine geeignete ID auszuwählen. Für „NYC Jobs“ sind diese Bezeichner im Feld `id` gespeichert.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

Das nächste Beispiel ist eine Suchabfrage, bei der ein bestimmtes Dokument basierend auf `id` „9E1E3AF9-0660-4E00-AF51-9B654925A2D5“ ausgegeben wird, das zuerst in der vorherigen Antwort enthalten war. Mit der folgenden Abfrage wird das gesamte Dokument zurückgegeben, nicht nur die ausgewählten Felder. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Beispiel 3: Filterabfragen

Die [Filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) ist ein OData-Ausdruck, den Sie mit **search** oder allein verwenden können. Ein eigenständiger Filter ohne Suchparameter ist nützlich, wenn der Filterausdruck Dokumente von Interesse vollständig qualifizieren kann. Ohne Abfragezeichenfolge wird keine lexikalische oder linguistische Analyse, Bewertung („1“ für alle Bewertungen) und Rangzuweisung durchgeführt. Beachten Sie, dass die Suchzeichenfolge leer ist.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Wenn sie zusammen verwendet werden, wird der Filter zuerst auf den gesamten Index angewendet, und anschließend wird die Suche für die Ergebnisse des Filters ausgeführt. Filter können daher eine nützliche Methode zum Verbessern der Abfrageleistung darstellen, da sie die Menge der Dokumente reduzieren, die bei der Suchabfrage verarbeitet werden müssen.

  ![Filtern von Abfrageantworten](media/search-query-simple-examples/filtered-query.png)

Wenn Sie dies mithilfe von GET in Postman testen möchten, können Sie die folgende Zeichenfolge einfügen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Eine andere leistungsstarke Möglichkeit zum Kombinieren von Filter und Suche ist die Verwendung von **`search.ismatch*()`** in einem Filterausdruck, wobei Sie eine Suchabfrage innerhalb des Filters durchführen können. Bei diesem Filterausdruck wird ein Platzhalter für *plan* (um „term plan“, „planner“, „planning“ usw. einzuschließen) verwendet, um „business_title“ auszuwählen.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Weitere Informationen zu dieser Funktion finden Sie unter [„search.ismatch“ unter „Filterbeispiele“](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="example-4-range-filters"></a>Beispiel 4: Bereichsfilter

Die Bereichsfilterung wird durch **`$filter`** -Ausdrücke für jeden Datentyp unterstützt. In den folgenden Beispielen werden numerische und Zeichenfolgenfelder durchsucht. 

Datentypen sind bei Bereichsfiltern wichtig und funktionieren am besten bei numerischen Daten in numerischen Feldern und Zeichenfolgendaten in Zeichenfolgenfeldern. Numerische Daten in Zeichenfolgenfeldern eignen sich nicht für Bereiche, da sich numerische Zeichenfolgen in Azure Search nicht vergleichen lassen. 

Die folgenden Beispiele weisen zur besseren Lesbarkeit das POST-Format (numerischer Bereich gefolgt vom Textbereich) auf:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Bereichsfilter für numerische Bereiche](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Bereichsfilter für Textbereiche](media/search-query-simple-examples/rangefiltertext.png)

Dies können Sie mithilfe von GET auch in Postman testen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Die Verwendung von Facettenwertbereichen ist eine verbreitete Anforderung für Suchanwendungen. Weitere Informationen und Beispiele zum Erstellen von Filtern für Facettennavigationsstrukturen finden Sie unter [„Filtern nach einem Bereich“ im Artikel *Implementieren der Facettennavigation in Azure Search*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Beispiel 5: Geografische Suche

Der Beispielindex enthält das Feld „geo_location“ mit Breiten- und Längengradkoordinaten. In diesem Beispiel wird die [geo.distance-Funktion](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) verwendet, mit der Dokumente im Umkreis eines Startpunkts bis zu einer beliebigen festgelegten Entfernung (in Kilometer) gefiltert werden. Sie können den letzten Wert in der Abfrage (4) anpassen, um die Fläche der Abfrage zu verkleinern oder zu vergrößern.

Im folgenden Beispiel wird zur besseren Lesbarkeit das POST-Format verwendet:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Zur besseren Übersichtlichkeit enthalten die Suchergebnisse eine Stellenkennung, eine Position und den Arbeitsstandort. Die Anfangskoordinaten wurden aus einem zufällig ausgewählten Dokument im Index abgerufen (in diesem Fall für einen Arbeitsstandort auf Staten Island).

Dies können Sie mithilfe von GET auch in Postman testen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Beispiel 6: Suchgenauigkeit

Begriffsabfragen sind einzelne Begriffe (ggf. in größerer Zahl), die unabhängig voneinander ausgewertet werden. Ausdrucksabfragen sind in Anführungszeichen gesetzt und werden Wort für Wort als Zeichenfolge ausgewertet. Die Genauigkeit der Übereinstimmung wird mithilfe von Operatoren und des searchMode-Elements gesteuert.

Beispiel 1: **`&search=fire`** gibt 150 Ergebnisse zurück, wobei alle Übereinstimmungen das Wort „fire“ an einer Stelle im Dokument enthalten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Beispiel 2: **`&search=fire department`** gibt 2.002 Ergebnisse zurück. Übereinstimmungen werden für Dokumente zurückgegeben, die entweder den Begriff „fire“ oder „department“ enthalten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Beispiel 3: **`&search="fire department"`** gibt 82 Ergebnisse zurück. Wenn die Zeichenfolge in Anführungszeichen gesetzt wird, wird für beide Begriffe eine „wörtliche“ Suche durchgeführt, und es werden Übereinstimmungen für tokenisierte Begriffe im Index gefunden, die aus einer Kombination der Begriffe bestehen. Dies ist der Grund dafür, warum die Suche nach **`search=+fire +department`** hiermit nicht gleichwertig ist. Beide Begriffe sind erforderlich, aber es wird unabhängig danach gesucht. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Beispiel 7: Boolesche Werte mit „searchMode“

Bei der einfachen Syntax werden boolesche Operatoren in Form von Zeichen (`+, -, |`) unterstützt. Mit dem Parameter „searchMode“ werden Kompromisse in Bezug auf die Genauigkeit und Trefferquote angegeben, wobei bei `searchMode=any` die Trefferquote (eine Übereinstimmung mit einem der Kriterien qualifiziert ein Dokument für das Resultset) und bei `searchMode=all` die Genauigkeit (alle Kriterien müssen übereinstimmen) im Mittelpunkt steht. Die Standardeinstellung ist `searchMode=any`. Dies kann verwirrend sein, wenn Sie für eine Abfrage mehrere Operatoren verwenden und keinen engeren Bereich von Ergebnissen erhalten, sondern einen weiteren Bereich. Dies gilt besonders für den Operator NOT, bei dem die Ergebnisse alle Dokumente umfassen, die einen bestimmten Begriff „nicht enthalten“.

Bei Verwendung der Standardeinstellung für „searchMode“ (any) werden 2.800 Dokumente zurückgegeben: Dokumente, die den mehrteiligen Begriff „fire department“ enthalten, sowie alle Dokumente, die den Begriff „Metrotech Center“ nicht enthalten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![Suchmodus „any“](media/search-query-simple-examples/searchmodeany.png)

Wenn Sie searchMode in `all` ändern, kommt es für die Kriterien zu einem kumulativen Effekt, und es wird ein kleineres Resultset mit nur 21 Dokumenten zurückgegeben. Dies sind Dokumente, die den gesamten Begriff „fire department“ enthalten, abzüglich der Jobs unter der Metrotech Center-Adresse.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![Suchmodus „all“](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Beispiel 8: Strukturieren von Ergebnissen

Mit mehreren Parametern wird gesteuert, welche Felder in den Suchergebnissen enthalten sind, wie viele Dokumente in jedem Batch zurückgegeben werden und welche Sortierreihenfolge verwendet wird. In diesem Beispiel wird auf einige der vorherigen Beispiele zurückgegriffen, indem Ergebnisse mit der **$select**-Anweisung auf bestimmte Felder beschränkt und „wörtliche“ Suchkriterien verwendet werden. Es werden 82 Übereinstimmungen zurückgegeben. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Basierend auf dem vorherigen Beispiel können Sie nach der Bezeichnung sortieren. Diese Sortierung funktioniert, weil „civil_service_title“ im Index *sortierbar* ist.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Die Auslagerungsergebnisse werden mit dem Parameter **$top** implementiert, und in diesem Fall werden die obersten fünf Dokumente zurückgegeben:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Überspringen Sie den ersten Batch, um die nächsten fünf Dokumente zu erhalten:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Nächste Schritte
Versuchen Sie, Abfragen in Ihrem Code anzugeben. Unter den folgenden Links wird erläutert, wie Sie Suchabfragen sowohl für .NET als auch für die REST-API einrichten, indem Sie die einfache Standardsyntax verwenden.

* [Abfragen des Azure Search-Indexes mit dem .NET SDK](search-query-dotnet.md)
* [Abfragen des Azure Search-Indexes mit der REST-API](search-create-index-rest-api.md)

Eine zusätzliche Syntaxreferenz, eine Abfragearchitektur und Beispiele finden Sie unter den folgenden Links:

+ [Beispiele für die Lucene-Abfragesyntax zum Erstellen von Abfragen in Azure Search](search-query-lucene-examples.md)
+ [Funktionsweise der Volltextsuche in Azure Search](search-lucene-query-architecture.md)
+ [Einfache Abfragesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Lucene-Abfragesyntax in Azure Search)
+ [Filter und Orderby-Syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
