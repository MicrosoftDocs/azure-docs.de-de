---
title: API-Referenz – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Eine vollständige API-Anleitung für Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ms.openlocfilehash: be9966f5d8e8d94aa3f49aac91b35b105195b108
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60510953"
---
# <a name="api"></a>API

Azure Custom Decision Service stellt zwei APIs bereit, die bei jeder Entscheidung aufgerufen werden. Mit der [Rangfolge-API](#ranking-api) wird die Rangfolge von Aktionen eingegeben, während die [Relevanz-API](#reward-api) zur Ausgabe der Benutzerrelevanz (engl. „Reward“) verwendet wird. Zusätzlich können Sie mithilfe der [Aktionsmengen-API](#action-set-api-customer-provided) Aktionen festlegen, die an Azure Custom Decision Service gesendet werden. In diesem Artikel werden die drei APIs beschrieben. Ein typisches Szenario zur Optimierung der Rangfolge von Artikeln durch Custom Decision Service wird unten gezeigt.

## <a name="ranking-api"></a>Rangfolge-API

Die Rangfolge-API nutzt ein Standardkommunikationsmuster unter Verwendung von [JSONP](https://en.wikipedia.org/wiki/JSONP), um die Latenz zu optimieren und die [Gleiche-Herkunft-Richtlinie](https://en.wikipedia.org/wiki/Same-origin_policy) zu umgehen. Durch die Richtlinie wird JavaScript am Abruf von Daten gehindert, deren Herkunft sich von derjenigen der Seite unterscheidet.

Fügen Sie diesen Ausschnitt in den HTML-Kopfteil Ihrer Startseite ein, auf der eine benutzerdefinierte Liste mit Artikeln angezeigt wird:

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> Die Rückruffunktion muss vor dem Aufruf der Rangfolge-API definiert werden.

> [!TIP]
> Zur Verbesserung der Latenz wird die Rangfolge-API über HTTP (beispielsweise in der Form `https://ds.microsoft.com/api/v2/<appId>/rank/*`) statt über HTTPS verfügbar gemacht.
> Wenn die Startseite jedoch über HTTPS bereitgestellt wird, muss auch ein HTTPS-Endpunkt verwendet werden.

Wenn keine Parameter verwendet werden, entspricht die HTTP-Antwort der Rangfolge-API einer JSONP-formatierten Zeichenfolge:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Der Browser führt diese Zeichenfolge dann als Aufruf der `callback()`-Funktion aus.

Folgende Parameter werden für die im vorherigen Beispiel gezeigte Rückruffunktion verwendet:

- `ranking` stellt die Rangfolge der anzuzeigenden URLs bereit.
- `eventId` wird intern von Custom Decision Service verwendet, um diese Rangfolge mit den entsprechenden Klicks abzugleichen.
- `appId` ermöglicht es der Rückruffunktion, zwischen mehreren Custom Decision Service-Anwendungen zu unterscheiden, die auf derselben Webseite ausgeführt werden.
- `actionSets` führt alle Aktionsmengen auf, die beim Aufruf der Rangfolge-API zusammen mit dem UTC-Zeitstempel der letzten erfolgreichen Aktualisierung verwendet wurden. Custom Decision Service aktualisiert die Feeds für die Aktionsmengen in regelmäßigen Abständen. Wenn einige Aktionsmengen nicht aktuell sind, muss die Rückruffunktion möglicherweise die Standardreihenfolge verwenden.

> [!IMPORTANT]
> Zur Erstellung der Artikelstandardrangfolge werden die angegebene Aktionsmengen verarbeitet und dabei möglicherweise verkleinert. Die Standardrangfolge wird anschließend neu sortiert in der HTTP-Antwort zurückgegeben. Die Standardrangfolge wird wie folgt hergestellt:
>
> - In jeder Aktionsmenge wird die Anzahl der Artikel auf die 15 neuesten Artikel beschränkt (falls mehr als 15 zurückgegeben werden).
> - Werden mehrere Aktionsmengen angegeben, werden diese in der gleichen Reihenfolge wie beim API-Aufruf zusammengeführt. Die ursprüngliche Reihenfolge der Artikel wird in den einzelnen Aktionsmengen beibehalten. Duplikate werden durch frühere Kopien ersetzt.
> - Die ersten `n` Artikel der zusammengeführten Artikelliste werden beibehalten, wobei `n=20` der Standardwert ist.

### <a name="ranking-api-with-parameters"></a>Rangfolge-API mit Parametern

Die folgenden Parameter können für die Rangfolge-API verwendet werden:

- Durch `details=1` und `details=2` werden zusätzliche Details zu jedem Artikel in `ranking` angegeben.
- `limit=<n>` legt die maximale Anzahl von Artikeln in der Standardrangfolge fest. `n` muss zwischen `2` und `30` liegen (ansonsten wird der Wert auf `2` bzw. `30` gekürzt).
- `dnt=1` deaktiviert Benutzercookies.

Die Parameter können mit der Standardsyntax für Abfragezeichenfolgen beispielsweise wie folgt kombiniert werden: `details=2&dnt=1`.

> [!IMPORTANT]
> In Europa sollte als Standardeinstellung `dnt=1` festgelegt werden, bis der Kunde der Nutzung von Cookies über ein Banner zustimmt. Auch auf Websites, die sich an minderjährige Personen richten, sollte diese Einstellung als Standardeinstellung verwendet werden. Weitere Informationen finden Sie in den [Nutzungsbestimmungen](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

Durch das `details=1`-Element wird für jeden Artikel die zugehörige `guid` eingefügt, wenn diese von der Aktionsmengen-API bereitgestellt wird. Die HTTP-Antwort sieht wie folgt aus:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

Durch das `details=2`-Element werden zusätzliche Details hinzugefügt, die Custom Decision Service möglicherweise aus dem [Featurebereitstellungs-Code](https://github.com/Microsoft/mwt-ds/tree/master/Crawl) für SEO-Metatags von Artikeln extrahieren kann:

- Extraktion von `title` aus `<meta property="og:title" content="..." />` oder `<meta property="twitter:title" content="..." />` oder `<title>...</title>`
- Extraktion von `description` aus `<meta property="og:description" ... />` oder `<meta property="twitter:description" content="..." />` oder `<meta property="description" content="..." />`
- Extraktion von `image` aus `<meta property="og:image" content="..." />`
- Extraktion von `ds_id` aus `<meta name=”microsoft:ds_id” content="..." />`

Die HTTP-Antwort sieht wie folgt aus:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

Das `<details>`-Element sieht so aus:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Relevanz-API

Custom Decision Service erfasst nur Klicks auf den obersten Slot. Jeder Klick führt dazu, dass die Relevanz um den Wert eins steigt. Beim Ausbleiben eines Klicks ändert sich am Relevanzwert nichts. Die Klicks werden mit den entsprechenden Rangfolgen abgeglichen. Dazu werden Ereignis-IDs verwendet, die durch den [Rangfolgen-API](#ranking-api)-Aufruf erzeugt werden. Bei Bedarf können Ereignis-IDs mit Sitzungscookies übergeben werden.

Verwenden Sie den folgenden Code auf Ihrer Startseite, um Klicks auf den obersten Slot zu verarbeiten:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

`data` ist in diesem Fall wie oben beschrieben das Argument für die `callback()`-Funktion. Bei der Verwendung von `data` im Code zur Klickverarbeitung ist einiges zu beachten. Ein Nutzungsbeispiel finden Sie im entsprechenden [Tutorial](custom-decision-service-tutorial-news.md#use-the-apis).

Die Relevanz-API kann wie unten gezeigt über [cURL](https://en.wikipedia.org/wiki/CURL) aufgerufen werden. Diese Vorgehensweise eignet sich aber ausschließlich für Testzwecke:

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Dabei sollte die HTTP-Antwort 200 (OK) angezeigt werden. Der um eins erhöhte Relevanzwert ist im Protokoll einsehbar, falls ein Schlüssel für das Azure-Speicherkonto im Portal bereitgestellt wurde.

## <a name="action-set-api-customer-provided"></a>Aktionsmengen-API (vom Kunden bereitgestellt)

Auf allgemeiner Ebene gibt die Aktionsmengen-API eine List mit Artikeln (Aktionen) zurück. Jeder Artikel wird über die zugehörige URL und optional über den Artikelnamen und das Veröffentlichungsdatum festgelegt. Sie können mehrere Aktionsmengen im Portal festlegen. Für jede Aktionsmenge sollte eine andere Aktionsmengen-API mit einer unterschiedlichen URL verwendet werden.

Jede Aktionsmengen-API kann entweder als RSS-Feed oder als Atom-Feed implementiert werden. Unabhängig von der Auswahl sollte die Implementierung dem Standardformat entsprechen und gültigen XML-Code zurückgeben. Das folgende Beispiel bezieht sich auf einen RSS-Feed:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Jedes übergeordnete `<item>`-Element beschreibt eine Aktion:

- `<link>` ist obligatorisch und wird als Aktions-ID verwendet.
- `<date>` wird ignoriert, wenn der Wert für Elemente kleiner oder gleich 15 ist. Andernfalls ist das Element obligatorisch.
  - Wenn mehr als 15 Elemente vorhanden sind, werden die neuesten 15 verwendet.
  - Das Element muss das Standardformat für RSS bzw. Atom verwenden:
    - Für RSS ist das Standardformat in [RFC 822](https://tools.ietf.org/html/rfc822) definiert. Ein Beispiel ist `"Fri, 28 Apr 2017 18:02:06 GMT"`.
    - Für Atom ist das Standardformat in [RFC 3339](https://tools.ietf.org/html/rfc3339) definiert. Ein Beispiel ist `"2016-12-19T16:39:57-08:00"`.
- `<title>` ist optional und wird verwendet, um Features zu generieren, die den Artikel beschreiben.
- `<guid>` ist optional und wird über das System der Rückruffunktion übergeben (falls der `?details`-Parameter im Rangfolgen-API-Aufruf angegeben wurde).

Andere Elemente innerhalb von `<item>` werden ignoriert.

In der Atom-Feed-Version werden dieselbe XML-Syntax und dieselben XML-Konventionen verwendet.

> [!TIP]
> Wenn Ihr System eigene Artikel-IDs verwendet, können diese durch `<guid>` der Rückruffunktion übergeben werden.
