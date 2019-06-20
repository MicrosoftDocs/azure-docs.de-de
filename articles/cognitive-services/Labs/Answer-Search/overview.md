---
title: Worum handelt es sich bei Project Answer Search?
titlesuffix: Azure Cognitive Services
description: Einführung in Project Answer Search
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: ac1717a8e8a08fcfedc3bc21bb0f03b3e3ca2511
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "60721858"
---
# <a name="what-is-project-answer-search"></a>Worum handelt es sich bei Project Answer Search?
Die Project Answer Search-API verwendet den Bing-v7-Endpunkt, um Antworten auf interrogative Abfragen zu erhalten. Auf eine Frage wie z.B. „Wie groß ist der Erdumfang?“ wird eine Antwort mit Fakten zurückgegeben.  Eine Abfrage für eine Person, einen Ort oder eine Sache gibt Informationen über die in der Abfrage angegebene Entität zurück. Diese Szenarien können in Anwendungen wie Konversationsbots, Messaging-Apps, Readern usw. nützlich sein.  

Abfragen geben Antworten zurück, die vom Abfrageszenario abhängen: Webseiten werden immer zurückgegeben, während [Fakten](fact-queries.md) und/oder [Entitäten](entity-queries.md) zurückgegeben werden, falls sie relevant sind.

## <a name="endpoint"></a>Endpunkt
Um Antworten auf eine Frage oder Informationen zu einer Person, einem Ort oder einer Sache zu erhalten, senden Sie eine Anforderung an den Answer Search-API-Endpunkt. Verwenden Sie die Header und URL-Parameter für verschiedene Spezifikationen.  Schließen Sie den Header *Ocp-Apim-Subscription-Key* mit einem gültigen Token ein.  Der Parameter für den Markt ist erforderlich. Derzeit wird nur der `en-us`-Markt unterstützt.

Mit der folgenden Abfrage werden Antworten auf die Frage „What is the circumference of the earth?“ (Wie groß ist der Erdumfang?) abgerufen:

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

Der URL-Parameter `q=` ist erforderlich, um das Objekt der Suche anzugeben.

## <a name="response-object"></a>Antwortobjekt

Die Antwort enthält HTTP-Header, Webseiten, Fakten und/oder Entitäten.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>Nutzungsbedingungen
Project Answer Search und Project Video Trends unterliegen den [Anforderungen für Verwendung und Anzeige der Bing-Suche](use-display-requirements.md).

Sie oder ein Dritter in Ihrem Namen dürfen Daten aus der URL-Vorschau-API nicht für Tests, Entwicklung, Schulung, Verteilung oder Bereitstellung eines Nicht-Microsoft-Diensts oder einer Nicht-Microsoft-Funktion verwenden, beibehalten, speichern, zwischenspeichern, freigeben oder verteilen. 

## <a name="throttling-requests"></a>Drosselungsanforderungen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Datenzuordnung  

Project Answer Search-Antworten enthalten Informationen, deren Besitzer Drittanbieter sind. Es ist Ihre Aufgabe sicherzustellen, dass Sie diese in geeigneter Form verwenden, z.B. durch Einhaltung von Creative Commons-Lizenzen, auf denen Ihre Benutzeroberfläche möglicherweise basiert.  
  
Wenn eine Antwort oder ein Ergebnis die Felder `contractualRules`, `attributions` oder `provider` enthält, müssen Sie die Daten zuordnen. Enthält die Antwort keines dieser Felder, ist keine Zuordnung erforderlich. Wenn die Antwort das Feld `contractualRules` sowie die Felder `attributions` und/oder `provider` enthält, müssen Sie die vertraglichen Regeln zum Zuordnen der Daten verwenden.  
  
Das folgende Beispiel zeigt eine Entität, die eine vertragliche MediaAttribution-Regel enthält, sowie ein Bild, das ein `provider`-Feld umfasst. Die MediaAttribution-Regel identifiziert das Bild als Ziel der Regel, sodass Sie das `provider`-Feld des Bilds ignorieren und stattdessen die MediaAttribution-Regel für die Zuordnung verwenden müssen.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Wenn eine vertragliche Regel das Feld `targetPropertyName` enthält, gilt die Regel nur für das Zielfeld. Andernfalls gilt die Regel für das übergeordnete Objekt, das das Feld `contractualRules` enthält.  
  
  
Im folgenden Beispiel enthält die Regel `LinkAttribution` das Feld `targetPropertyName`, sodass die Regel für das Feld `description` gilt. Bei Regeln, die für bestimmte Felder gelten, müssen Sie eine Zeile einfügen, die unmittelbar auf die Zieldaten folgt und einen Link zur Website des Anbieters enthält. Um beispielsweise die Beschreibung zuzuordnen, fügen Sie eine Zeile unmittelbar nach dem Beschreibungstext ein, der einen Link zu den Daten auf der Website des Anbieters enthält, also in diesem Fall einen Link zu „en.wikipedia.org“.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Lizenzzuordnung  

Wenn die Liste der vertraglichen Regeln eine [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution)-Regel enthält, müssen Sie den Hinweis in der Zeile anzeigen, die unmittelbar auf den Inhalt folgt, für den die Lizenz gilt. Die `LicenseAttribution`-Regel verwendet das Feld `targetPropertyName`, um die Eigenschaft anzugeben, für die die Lizenz gilt.  
  
Nachfolgend sehen Sie ein Beispiel, das eine `LicenseAttribution`-Regel enthält.  
  
![Lizenzzuordnung](./media/licenseattribution.png)  
  
Der von Ihnen angezeigte Lizenzhinweis muss einen Link zu der Website enthalten, die die Informationen zur Lizenz enthält. In der Regel verwenden Sie den Namen der Lizenz als Link. Wenn beispielsweise der Hinweis **Text unter CC-BY-SA-Lizenz** lautet und „CC-BY-SA“ der Name der Lizenz ist, würden Sie „CC-BY-SA“ als Link verwenden.  
  
### <a name="link-and-text-attribution"></a>Link- und Textzuordnung  

Die Regeln [LinkAttribution](reference.md#linkattribution) und [TextAttribution](reference.md#textattribution) werden normalerweise verwendet, um den Anbieter der Daten anzugeben. Das Feld `targetPropertyName` bezeichnet das Feld, für das die Regel gilt.  
  
Zum Zuordnen der Anbieter fügen Sie eine Zeile ein, die unmittelbar auf den Inhalt folgt, für den die Zuordnungen gelten (z.B. das Zielfeld). Die Zeile sollte eindeutig gekennzeichnet sein, um anzugeben, dass die Anbieter die Quelle der Daten sind (z.B. „Daten von: en.wikipedia.org“). Im Fall von `LinkAttribution`-Regeln müssen Sie einen Link zur Website des Anbieters erstellen.  
  
Nachfolgend sehen Sie ein Beispiel, das `LinkAttribution`- und `TextAttribution`-Regeln enthält.  
  
![Link- und Textzuordnung](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Medienzuordnung  

Wenn die Entität ein Bild enthält und Sie dieses anzeigen, müssen Sie einen Link zum Durchklicken zur Website des Anbieters bereitstellen. Wenn die Entität eine [MediaAttribution](reference.md#mediaattribution)-Regel enthält, verwenden Sie die URL der Regel, um den Link zum direkten Klicken zu erstellen. Verwenden Sie andernfalls die URL, die im `provider`-Feld des Bilds enthalten ist, um den Link zum direkten Klicken zu erstellen.  
  
Nachfolgend sehen Sie ein Beispiel, das ein `provider`-Feld eines Bilds und vertragliche Regeln enthält. Da das Beispiel die vertragliche Regel enthält, ignorieren Sie das `provider`-Feld des Bilds und wenden die `MediaAttribution`-Regel an.  
  
![Medienzuordnung](./media/mediaattribution.png)  

## <a name="next-steps"></a>Nächste Schritte
- [C#-Schnellstart](c-sharp-quickstart.md)
- [Java-Schnellstart](java-quickstart.md)
- [Node-Schnellstart](node-quickstart.md)
- [Python-Schnellstart](python-quickstart.md)
