---
title: Project URL Preview-Endpunkt
titlesuffix: Azure Cognitive Services
description: Zusammenfassung des URL Preview-Endpunkts.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 7cc52493ec0e2b9c81d52da4bb22102c2c7e5e5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60712481"
---
# <a name="project-url-preview-endpoint"></a>Project URL Preview-Endpunkt

Die URL Preview-API umfasst einen Endpunkt.

## <a name="endpoint"></a>Endpunkt
Um eine URL-Vorschau zu erhalten, senden Sie eine Anforderung an den folgenden Endpunkt. Verwenden Sie die Header und URL-Parameter für andere Spezifikationen.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Abfrageparameter
|NAME|Wert|Type|Erforderlich|  
|----------|-----------|----------|--------------|  
|q|Die URL, für die eine Vorschau angezeigt werden soll|string |Ja|
|safeSearch|Ungültige, nicht jugendfreie Inhalte oder illegale Inhalte. Diese werden mit dem Fehlercode 400 blockiert, und das Flag *isFamilyFriendly* wird nicht zurückgegeben. <p>Für legale nicht jugendfreie Inhalte wird das Verhalten unten beschrieben. Es wird der Statuscode 200 zurückgegeben, und das Flag *IsFamilyFriendly* wird auf „false“ festgelegt.<ul><li>safeSearch=strict: Titel, Beschreibung, URL und Bild werden nicht zurückgegeben.</li><li>safeSearch=moderate: Titel, URL und Beschreibung werden abgerufen, das beschreibende Bild aber nicht.</li><li>safeSearch=off: Alle Antwortobjekte/-elemente (Titel, URL, Beschreibung und Bild) werden abgerufen.</li></ul> |string|Nicht erforderlich. </br> Standardmäßig wird „safeSearch=strict“ verwendet.| 

## <a name="response-object"></a>Antwortobjekt

Die Antwort enthält HTTP-Header und das WebPage-Objekt mit den folgenden Attributen wie im Beispiel gezeigt: `name`, `url`, `description`, `isFamilyFriendly` und `primaryImageOfPage`.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Nächste Schritte
- [C#-Schnellstart](csharp.md)
- [Java-Schnellstart](java-quickstart.md)
- [JavaScript-Schnellstart](javascript.md)
- [Node-Schnellstart](node-quickstart.md)
- [Python-Schnellstart](python-quickstart.md)
