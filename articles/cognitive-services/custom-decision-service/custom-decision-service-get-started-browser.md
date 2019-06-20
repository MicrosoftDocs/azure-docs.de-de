---
title: Aufrufen einer API in einem Browser – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Informationen zum Optimieren einer Webseite, indem API-Aufrufe direkt aus einem Browser für den Custom Decision Service vorgenommen werden.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 2b356e2f0fe9235d49dffa7417cd3894059f9caf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829156"
---
# <a name="call-api-from-a-browser"></a>Aufrufen einer API in einem Browser

Dieser Artikel hilft unterstützt Sie dabei, die Azure Custom Decision Service-APIs direkt über einen Browser aufzurufen.

Stellen Sie sicher, dass Sie Ihre [Anwendung zuerst registrieren](custom-decision-service-get-started-register.md).

Lassen Sie uns anfangen. Ihre Anwendung wird als eine Startseite modelliert, die Links zu mehreren Artikelseiten enthält. Die Startseite verwendet Custom Decision Service, um die Reihenfolge der Artikelseiten festzulegen. Fügen Sie den folgenden Code in den HTML-Header der Startseite ein:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

Das `data`-Argument enthält die Rangfolge der zu rendernden URLs. Weitere Informationen finden Sie in der [API](custom-decision-service-api-reference.md)-Referenz.

Um einen Benutzerklick auf den Artikel an oberster Stelle zu behandeln, rufen Sie den folgenden Code auf der Startseite auf:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Hier ist `data` das Argument für die `callback()`-Funktion. Ein Implementierungsbeispiel finden Sie in diesem [Tutorial](custom-decision-service-tutorial-news.md#use-the-apis).

Zuletzt müssen Sie noch die Aktionssatz-API angeben, die die Liste der Artikel (Aktionen) zurückgibt, die von Custom Decision Service berücksichtigt werden müssen. Implementieren Sie diese API wie folgt als RSS-Feed:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Hier beschreibt jedes `<item>`-Element auf oberster Ebene einen Artikel. Der `<link>` ist verpflichtend und wird von Custom Decision Service als Aktions-ID verwendet. Geben Sie `<date>` (im Standard-RSS-Format) an, wenn mehr als 15 Artikel vorhanden sind. Es werden die 15 aktuellsten Artikel verwendet. Der `<title>` ist optional und wird verwendet, um textgebundene Features für den Artikel zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

* Abschließen Sie eines [Tutorials](custom-decision-service-tutorial-news.md), um ein tiefergehendes Beispiel kennenzulernen.
* Weitere Informationen zu den bereitgestellten Funktionen finden Sie in der [API-Referenz](custom-decision-service-api-reference.md).
