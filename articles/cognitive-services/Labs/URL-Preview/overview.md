---
title: Worum handelt es sich bei der Projekt-URL-Vorschau?
titlesuffix: Azure Cognitive Services
description: Einführung in die Projekt-URL-Vorschau
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 7022c3b2d2f3618d55b0a70d2690abf1497ec6a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "61473178"
---
# <a name="what-is-project-url-preview"></a>Worum handelt es sich bei der Projekt-URL-Vorschau?
Der URL-Vorschau-Endpunkt nutzt einen URL-Abfrageparameter und gibt eine JSON-Antwort mit dem Namen der Zielressource, einer kurzen Beschreibung und einem Link zu einem Bild, das in einer Vorschau angezeigt wird, zurück. Die Antwort hat außerdem das [isFamilyFriendly](url-preview-reference.md#query-parameters)-Flag, das angibt, ob die URL jugendgefährdende, raubkopierte oder andere illegale Inhalte enthält. 

Um Ergebnisse für die URL-Vorschau abzurufen, senden Sie eine GET-Anforderung, und fügen Sie den Header *Ocp-Apim-Subscription-Key* mit einem gültigen Token ein:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
Die Antwort: 
```
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

```
## <a name="scenarios"></a>Szenarien 

Die URL-Vorschau-API unterstützt kurze Beschreibungen von Webressourcen. Mit ihr erstellen Entwickler umfangreiche Vorschauen.  Benutzer können Webseiten, News, Blogs, Foren usw. freigeben oder als Lesezeichen speichern. Außerdem lässt sich diese API für die Inhaltsmoderation verwenden.    

Anwendungen verwenden die URL-Vorschau, um Webanforderungen an den Endpunkt zu senden, mit einer der URL zugewiesenen Abfrage für die Vorschau.  Die JSON-Antwort enthält die Vorschauinformationen: Name, Beschreibung der Ressource, *familyFriendly*-Flag und Links, die Zugriff auf ein repräsentatives Bild und die vollständige Ressource online bereitstellen. 

## <a name="terms-of-use"></a>Nutzungsbedingungen
Verwenden Sie nur die Daten aus der Projekt-URL-Vorschau, um Vorschauausschnitte und durch Links mit ihren Quellwebsites verbundene Miniaturansichten in einer vom Endbenutzer initiierten URL-Freigabe in sozialen Medien, Chatbots oder ähnlichen Angeboten anzuzeigen. Kopieren, speichern oder zwischenspeichern Sie keine Daten, die Sie aus der Projekt-URL-Vorschau erhalten. Sie müssen allen Anforderungen zum Deaktivieren von Vorschauen nachkommen, die Sie von Website- oder Inhaltsbesitzern erhalten.

Sie oder ein Dritter in Ihrem Namen dürfen Daten aus der URL-Vorschau-API nicht für Tests, Entwicklung, Aus- und Weiterbildung, Verteilung oder Bereitstellung eines Nicht-Microsoft-Diensts oder einer Nicht-Microsoft-Funktion verwenden, beibehalten, speichern, zwischenspeichern, freigeben oder verteilen. 

## <a name="throttling-requests"></a>Drosselungsanforderungen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nächste Schritte
- [C#-Schnellstart](csharp.md)
- [Java-Schnellstart](java-quickstart.md)
- [JavaScript-Schnellstart](javascript.md)
- [Node-Schnellstart](node-quickstart.md)
- [Python-Schnellstart](python-quickstart.md)
