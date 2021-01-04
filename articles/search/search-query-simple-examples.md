---
title: Erstellen einer einfachen Abfrage
titleSuffix: Azure Cognitive Search
description: Lernen Sie anhand von Beispielen, indem Sie Abfragen auf der Grundlage der einfachen Syntax für Volltextsuche, Filtersuche, geografische Suche und Facettensuche für einen Azure Cognitive Search-Index ausführen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 834e4fe8c7b3923f40a07c02c0310200db222308
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697253"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Erstellen einer einfachen Abfrage in Azure Cognitive Search

Bei der [einfachen Abfragesyntax](query-simple-syntax.md) in Azure Cognitive Search wird der Standardabfrageparser aufgerufen, um Volltextsuchabfragen für einen Index auszuführen. Dieser Parser ist schnell und verarbeitet gängige Szenarien, z.B. Volltextsuche, Filter- und Facettensuche und geografische Suche. 

In diesem Artikel werden anhand von Beispielen die einfache Syntax veranschaulicht und bei einem Vorgang mit [Suchdokumenten](/rest/api/searchservice/search-documents) der Parameter `search=` aufgefüllt.

Eine Alternative zu dieser Abfragesyntax ist die [vollständige Lucene-Abfragesyntax](query-lucene-syntax.md), bei der auch komplexere Abfragestrukturen unterstützt werden, z.B. Fuzzy- und Platzhaltersuche, sodass sich die Verarbeitungsdauer verlängern kann. Weitere Informationen und Beispiele zur Veranschaulichung der vollständigen Syntax finden Sie unter [Verwenden der vollständigen Lucene-Syntax](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulieren von Anforderungen in Postman

Der in den folgenden Beispielen verwendete Suchindex „NYC Jobs“ besteht aus Stellenangeboten basierend auf einem Dataset, das von der Initiative [City of New York OpenData](https://nycopendata.socrata.com/) bereitgestellt wird. Diese Daten sollten weder als aktuell noch als vollständig betrachtet werden. Der Index wird über einen Sandboxdienst von Microsoft bereitgestellt. Das bedeutet, dass Sie zum Testen dieser Abfragen weder ein Azure-Abonnement noch Azure Cognitive Search benötigen.

Sie benötigen lediglich Postman oder ein gleichwertiges Tool zum Senden einer HTTP-Anforderung per GET. Weitere Informationen finden Sie unter [Quickstart: Untersuchen der Azure Cognitive Search-REST-API](search-get-started-rest.md).

### <a name="set-the-request-header"></a>Festlegen des Anforderungsheaders

1. Legen Sie im Anforderungsheader **Content-Type** auf `application/json` fest.

2. Fügen Sie einen **api-key** hinzu, und legen Sie ihn auf diese Zeichenfolge fest: `252044BE3886FE4A8E3BAA4F595114BB`. Dies ist ein Abfrageschlüssel für den Sandbox-Suchdienst, der den Index „NYC Jobs“ hostet.

Nachdem Sie den Anforderungsheader angegeben haben, können Sie ihn für alle Abfragen in diesem Artikel wiederverwenden, indem Sie lediglich die Zeichenfolge **search=** austauschen. 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Festlegen von Parametern für Postman-Anforderungsheader" border="false":::

### <a name="set-the-request-url"></a>Festlegen der Anforderungs-URL

Die Anforderung ist ein GET-Befehl, der mit einer URL gekoppelt ist, die den Azure Cognitive Search-Endpunkt und die Suchzeichenfolge enthält.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="Postman-Anforderungsheader GET" border="false":::

Die URL-Komposition umfasst die folgenden Elemente:

+ **`https://azs-playground.search.windows.net/`** ist ein Sandbox-Suchdienst, der vom Azure Cognitive Search-Entwicklungsteam gepflegt wird. 
+ **`indexes/nycjobs/`** ist der Index „NYC Jobs“ in der Indexsammlung dieses Diensts. Sowohl der Dienstname als auch der Index sind für die Anforderung erforderlich.
+ **`docs`** ist die Dokumentsammlung, die den gesamten durchsuchbaren Inhalt enthält. Der im Anforderungsheader angegebene Abfrage-API-Schlüssel funktioniert nur für Lesevorgänge, die die Dokumentsammlung betreffen.
+ **`api-version=2020-06-30`** legt die API-Version fest. Dies ist für alle Anforderungen ein erforderlicher Parameter.
+ **`search=*`** ist die Abfragezeichenfolge, die in der ersten Abfrage null ist und (standardmäßig) die ersten 50 Ergebnisse zurückgibt.

## <a name="send-your-first-query"></a>Senden Ihrer ersten Abfrage

Fügen Sie als Überprüfungsschritt die folgende Anforderung in GET ein, und klicken Sie auf **Senden**. Die Ergebnisse werden als ausführliche JSON-Dokumente zurückgegeben. Es werden ganze Dokumente zurückgegeben, sodass Sie alle Felder und alle Werte sehen können.

Fügen Sie diese URL in einen REST-Client als Überprüfungsschritt und zum Anzeigen der Dokumentstruktur ein.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

Die Abfragezeichenfolge **`search=*`** entspricht einer Suche ohne Angaben, die einer NULL-Suche oder leeren Suche gleicht. Dies hat keinen besonderen Nutzen, aber es ist die einfachste Suche, die Sie durchführen können.

Optional können Sie der URL **`$count=true`** hinzufügen, um eine Anzahl für die Dokumente zurückzugeben, die die Suchkriterien erfüllen. In einer leeren Suchzeichenfolge entspricht dies allen Dokumenten im Index (ca. 2.800 für „NYC Jobs“).

## <a name="how-to-invoke-simple-query-parsing"></a>Aufrufen der einfachen Abfrageanalyse

Für interaktive Abfragen müssen Sie nichts angeben: einfache Abfragen sind die Standardeinstellung. Wenn Sie zuvor **queryType=full** für die vollständige Abfragesyntax aufgerufen haben, können Sie im Code die Standardeinstellung wiederherstellen, indem Sie **queryType=simple** verwenden.

## <a name="example-1-field-scoped-query"></a>Beispiel 1: Feldbezogene Abfrage

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
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=business_title&$select=business_title&search=*
```

Die Antwort auf diese Abfrage sollte etwa wie im folgenden Screenshot aussehen.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Postman-Beispielantwort" border="false":::

Möglicherweise ist Ihnen die Suchbewertung in der Antwort aufgefallen. Zu einer einheitlichen Bewertung von „1“ kommt es, wenn kein Rang vorhanden ist, weil es entweder keine Volltextsuche war oder weil keine Kriterien angewandt wurden. Bei einer NULL-Suche ohne Kriterien werden Zeilen in willkürlicher Reihenfolge zurückgegeben. Wenn Sie tatsächliche Kriterien einfügen, werden aussagekräftige Werte für die Suchbewertungen angezeigt.

## <a name="example-2-look-up-by-id"></a>Beispiel 2: Suchen anhand der ID

Dieses Beispiel ist etwas ungewöhnlich. Beim Auswerten des Suchverhaltens ist es aber ratsam, den gesamten Inhalt eines bestimmten Dokuments zu untersuchen, um zu ermitteln, warum er in die Ergebnisse einbezogen bzw. ausgeschlossen wurde. Verwenden Sie zum Zurückgeben eines einzelnen Dokuments in seiner Gesamtheit einen [Suchvorgang](/rest/api/searchservice/lookup-document), um die Dokument-ID zu übergeben.

Alle Dokumente verfügen über einen eindeutigen Bezeichner. Beim Ausprobieren der Syntax für eine Suchabfrage können Sie zuerst eine Liste mit Dokument-IDs zurückgeben, um eine geeignete ID auszuwählen. Für „NYC Jobs“ sind diese Bezeichner im Feld `id` gespeichert.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=id&$select=id&search=*
```

Das nächste Beispiel ist eine Suchabfrage, bei der ein bestimmtes Dokument basierend auf `id` „9E1E3AF9-0660-4E00-AF51-9B654925A2D5“ ausgegeben wird, das zuerst in der vorherigen Antwort enthalten war. Mit der folgenden Abfrage wird das gesamte Dokument zurückgegeben, nicht nur die ausgewählten Felder. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Beispiel 3: Filterabfragen

Die [Filtersyntax](./search-query-odata-filter.md) ist ein OData-Ausdruck, den Sie mit **search** oder allein verwenden können. Ein eigenständiger Filter ohne Suchparameter ist nützlich, wenn der Filterausdruck Dokumente von Interesse vollständig qualifizieren kann. Ohne Abfragezeichenfolge wird keine lexikalische oder linguistische Analyse, Bewertung („1“ für alle Bewertungen) und Rangzuweisung durchgeführt. Beachten Sie, dass die Suchzeichenfolge leer ist.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Wenn sie zusammen verwendet werden, wird der Filter zuerst auf den gesamten Index angewendet, und anschließend wird die Suche für die Ergebnisse des Filters ausgeführt. Filter können daher eine nützliche Methode zum Verbessern der Abfrageleistung darstellen, da sie die Menge der Dokumente reduzieren, die bei der Suchabfrage verarbeitet werden müssen.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Filtern von Abfrageantworten" border="false":::

Wenn Sie dies mithilfe von GET in Postman testen möchten, können Sie die folgende Zeichenfolge einfügen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Eine andere leistungsstarke Möglichkeit zum Kombinieren von Filter und Suche ist die Verwendung von **`search.ismatch*()`** in einem Filterausdruck, wobei Sie eine Suchabfrage innerhalb des Filters durchführen können. Bei diesem Filterausdruck wird ein Platzhalter für *plan* (um „term plan“, „planner“, „planning“ usw. einzuschließen) verwendet, um „business_title“ auszuwählen.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Weitere Informationen zu dieser Funktion finden Sie unter [„search.ismatch“ unter „Filterbeispiele“](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Beispiel 4: Bereichsfilter

Die Bereichsfilterung wird durch **`$filter`** -Ausdrücke für jeden Datentyp unterstützt. In den folgenden Beispielen werden numerische und Zeichenfolgenfelder durchsucht. 

Datentypen sind bei Bereichsfiltern wichtig und funktionieren am besten bei numerischen Daten in numerischen Feldern und Zeichenfolgendaten in Zeichenfolgenfeldern. Numerische Daten in Zeichenfolgenfeldern eignen sich nicht für Bereiche, da sich numerische Zeichenfolgen in Azure Cognitive Search nicht vergleichen lassen. 

Die folgenden Beispiele weisen zur besseren Lesbarkeit das POST-Format (numerischer Bereich gefolgt vom Textbereich) auf:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Bereichsfilter für numerische Bereiche" border="false":::


```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Bereichsfilter für Textbereiche" border="false":::

Dies können Sie mithilfe von GET auch in Postman testen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Die Verwendung von Facettenwertbereichen ist eine verbreitete Anforderung für Suchanwendungen. Weitere Informationen und Beispiele zum Erstellen von Filtern für Facettennavigationsstrukturen finden Sie unter [„Filtern nach einem Bereich“ im Artikel *Implementieren der Facettennavigation in Azure Search*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Beispiel 5: Geografische Suche

Der Beispielindex enthält das Feld „geo_location“ mit Breiten- und Längengradkoordinaten. In diesem Beispiel wird die [geo.distance-Funktion](./search-query-odata-geo-spatial-functions.md#examples) verwendet, mit der Dokumente im Umkreis eines Startpunkts bis zu einer beliebigen festgelegten Entfernung (in Kilometer) gefiltert werden. Sie können den letzten Wert in der Abfrage (4) anpassen, um die Fläche der Abfrage zu verkleinern oder zu vergrößern.

Im folgenden Beispiel wird zur besseren Lesbarkeit das POST-Format verwendet:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
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
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Beispiel 6: Suchgenauigkeit

Begriffsabfragen sind einzelne Begriffe (ggf. in größerer Zahl), die unabhängig voneinander ausgewertet werden. Ausdrucksabfragen sind in Anführungszeichen gesetzt und werden Wort für Wort als Zeichenfolge ausgewertet. Die Genauigkeit der Übereinstimmung wird mithilfe von Operatoren und des searchMode-Elements gesteuert.

Beispiel 1: **`&search=fire`** gibt 150 Ergebnisse zurück, wobei alle Übereinstimmungen das Wort „fire“ an einer Stelle im Dokument enthalten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire
```

Beispiel 2: **`&search=fire department`** gibt 2.002 Ergebnisse zurück. Übereinstimmungen werden für Dokumente zurückgegeben, die entweder den Begriff „fire“ oder „department“ enthalten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire department
```

Beispiel 3: **`&search="fire department"`** gibt 82 Ergebnisse zurück. Wenn die Zeichenfolge in Anführungszeichen gesetzt wird, wird für beide Begriffe eine „wörtliche“ Suche durchgeführt, und es werden Übereinstimmungen für tokenisierte Begriffe im Index gefunden, die aus einer Kombination der Begriffe bestehen. Dies ist der Grund dafür, warum die Suche nach **`search=+fire +department`** hiermit nicht gleichwertig ist. Beide Begriffe sind erforderlich, aber es wird unabhängig danach gesucht. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Beispiel 7: Boolesche Werte mit „searchMode“

Bei der einfachen Syntax werden boolesche Operatoren in Form von Zeichen (`+, -, |`) unterstützt. Mit dem Parameter „searchMode“ werden Kompromisse in Bezug auf die Genauigkeit und Trefferquote angegeben, wobei bei `searchMode=any` die Trefferquote (eine Übereinstimmung mit einem der Kriterien qualifiziert ein Dokument für das Resultset) und bei `searchMode=all` die Genauigkeit (alle Kriterien müssen übereinstimmen) im Mittelpunkt steht. Die Standardeinstellung ist `searchMode=any`. Dies kann verwirrend sein, wenn Sie für eine Abfrage mehrere Operatoren verwenden und keinen engeren Bereich von Ergebnissen erhalten, sondern einen weiteren Bereich. Dies gilt besonders für den Operator NOT, bei dem die Ergebnisse alle Dokumente umfassen, die einen bestimmten Begriff „nicht enthalten“.

Bei Verwendung der Standardeinstellung für „searchMode“ (any) werden 2.800 Dokumente zurückgegeben: Dokumente, die den mehrteiligen Begriff „fire department“ enthalten, sowie alle Dokumente, die den Begriff „Metrotech Center“ nicht enthalten.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="Suchmodus „any“" border="false":::

Wenn Sie searchMode in `all` ändern, kommt es für die Kriterien zu einem kumulativen Effekt, und es wird ein kleineres Resultset mit nur 21 Dokumenten zurückgegeben. Dies sind Dokumente, die den gesamten Begriff „fire department“ enthalten, abzüglich der Jobs unter der Metrotech Center-Adresse.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="Suchmodus „all“" border="false":::

## <a name="example-8-structuring-results"></a>Beispiel 8: Strukturieren von Ergebnissen

Mit mehreren Parametern wird gesteuert, welche Felder in den Suchergebnissen enthalten sind, wie viele Dokumente in jedem Batch zurückgegeben werden und welche Sortierreihenfolge verwendet wird. In diesem Beispiel wird auf einige der vorherigen Beispiele zurückgegriffen, indem Ergebnisse mit der **$select**-Anweisung auf bestimmte Felder beschränkt und „wörtliche“ Suchkriterien verwendet werden. Es werden 82 Übereinstimmungen zurückgegeben. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Basierend auf dem vorherigen Beispiel können Sie nach der Bezeichnung sortieren. Diese Sortierung funktioniert, weil „civil_service_title“ im Index *sortierbar* ist.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Die Auslagerungsergebnisse werden mit dem Parameter **$top** implementiert, und in diesem Fall werden die obersten fünf Dokumente zurückgegeben:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Überspringen Sie den ersten Batch, um die nächsten fünf Dokumente zu erhalten:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Nächste Schritte
Versuchen Sie, Abfragen in Ihrem Code anzugeben. Unter den folgenden Links wird erläutert, wie Sie Suchabfragen sowohl für .NET als auch für die REST-API einrichten, indem Sie die einfache Standardsyntax verwenden.

* [Abfragen des Index mit dem .NET SDK](./search-get-started-dotnet.md)
* [Abfragen des Index mit der REST-API](./search-get-started-powershell.md)

Eine zusätzliche Syntaxreferenz, eine Abfragearchitektur und Beispiele finden Sie unter den folgenden Links:

+ [Beispiele für die Lucene-Abfragesyntax zum Erstellen von Abfragen in Azure Search](search-query-lucene-examples.md)
+ [Funktionsweise der Volltextsuche in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Einfache Abfragesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene query syntax in Azure Search](/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Lucene-Abfragesyntax in Azure Search)
+ [Filter und Orderby-Syntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search)