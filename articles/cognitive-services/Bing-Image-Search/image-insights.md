---
title: Abrufen von Bildauswertungen – Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie die Bing-Bildersuche-API verwendet wird, um weitere Informationen zu einem Bild abzurufen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 9dffc16a1d1171343fa10fce10fddba2a12c2167
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348244"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Abrufen von Bildauswertungen mit der Bing-Bildersuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

> [!IMPORTANT]
> Anstatt zum Abrufen von Bildauswertungen den Endpunkt „/images/details“ zu verwenden, sollten Sie die [visuelle Suche](../bing-visual-search/overview.md) nutzen, weil diese umfassendere Informationen bereitstellt.


Jedes Bild enthält ein Auswertungstoken, das Sie verwenden können, um Informationen zum Bild zu erhalten. Beispielsweise können Sie eine Sammlung von zugehörigen Bildern, Webseiten, auf denen das Bild enthalten ist, oder eine Liste von Anbietern abrufen, bei denen Sie das abgebildete Produkt erwerben können.  

Um Auswertungen zu einem Bild zu erhalten, erfassen Sie das Token [imageInsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) des Bilds in der Antwort.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Anschließend rufen Sie den Bilddetailsendpunkt auf und legen den [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)-Abfrageparameter auf das Token in `imageInsightsToken` fest.  

Um die Auswertungen anzugeben, die Sie abrufen möchten, legen Sie den `modules`-Abfrageparameter fest. Um alle Auswertungen abzurufen, legen Sie `modules` auf `All` fest. Um nur die Beschriftungs- und Sammlungsinformationen abzurufen, legen Sie `modules` auf `Caption%2CCollection` fest. Eine vollständige Liste der möglichen Auswertungen finden Sie unter [Module](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). Nicht alle Auswertungen sind für alle Bilder verfügbar. Die Antwort enthält alle Auswertungen, die Sie angefordert haben (wenn verfügbar).

Das folgende Beispiel fordert alle verfügbaren Auswertungen für das Bild oben an.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Abrufen von Auswertungen eines bekannten Bilds

Wenn Sie über die URL für ein Bild verfügen, zu dem Sie Auswertungen erhalten möchten, verwenden Sie den [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)-Abfrageparameter anstelle des [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)-Parameters, um das Bild anzugeben. Wenn Sie über die Bilddatei verfügen, können Sie die Binärdatei des Bilds auch im Text einer POST-Anforderung senden. Wenn Sie eine POST-Anforderung verwenden, muss der `Content-Type`-Header auf `multipart/data-form` festgelegt werden. Mit beiden Optionen darf die Größe des Bilds 1 MB nicht überschreiten.  

Wenn Sie über die URL für das Bild verfügen, zeigt das folgende Beispiel, wie Auswertungen zu diesem Bild angefordert werden.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Abrufen aller Bildauswertungen  

Um alle Auswertungen eines Bilds anzufordern, legen Sie den [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)-Abfrageparameter auf `All` fest. Um verwandte Suchvorgänge abzurufen, muss die Anforderung die Abfragezeichenfolge des Benutzers enthalten. Dieses Beispiel zeigt die Verwendung von [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) zum Angeben des Bilds.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Das Objekt der obersten Ebene ist ein [ImageInsightsResponse](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)-Objekt und kein [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images)-Objekt.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Erkennen von Entitäten in einem Bild  

Das Entitätserkennungsfeature identifiziert Entitäten in einem Bild (zurzeit nur Personen). Um Entitäten in einem Bild zu identifizieren, legen Sie den [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)-Abfrageparameter auf `RecognizedEntities` fest.  

> [!NOTE]
> Sie können dieses Modul nicht mit anderen Modulen angeben. Wenn Sie dieses Modul mit anderen Modulen angeben, enthält die Antwort keine erkannten Entitäten.  

Im Folgenden wird gezeigt, wie das Bild mithilfe des [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)-Parameters angegeben wird. Sie müssen die Abfrageparameter URL-codieren.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung. Da das Bild zwei Personen zeigt, identifiziert die Antwort einen Bereich für jede Person. In diesem Fall wurden die Personen in den Gruppen „CelebrityAnnotations“ und „CelebRecognitionAnnotations“ erkannt. Bing listet die Personen in jeder Gruppe basierend auf der Wahrscheinlichkeit auf, dass sie mit der Person im ursprünglichen Bild übereinstimmen. Die Liste enthält die Personen in absteigender Reihenfolge der Wahrscheinlichkeit. Die Gruppe „CelebRecognitionAnnotations“ bietet das höchste Maß an Wahrscheinlichkeit, dass die Übereinstimmung zutrifft.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

Das `region`-Feld identifiziert den Bereich des Bilds, in dem Bing die Entität erkannt hat. Für Personen stellt dieser Bereich das Gesicht dieser Person dar.  

Die Werte des Rechtecks sind relativ zur Breite und Höhe des ursprünglichen Bilds und liegen im Bereich von 0,0 bis 1,0. Wenn das Bild beispielsweise 300 x 200 groß ist und die obere linke Ecke des Bereichs an Punkt (10, 20) und die untere rechte Ecke an Punkt (290, 150) liegt, ergibt sich das folgende normalisierte Rechteck:  

-   Links: 10 / 300 = 0,03333...  
-   Oben: 20 / 200 = 0,1  
-   Rechts: 290 / 300 = 0,9667...  
-   Unten: 150 / 200 = 0,75  

Sie können den Bereich, den Bing zurückgibt, in nachfolgenden Auswertungsaufrufen verwenden. Beispielsweise zum Abrufen visuell ähnlicher Bilder für die erkannte Entität. Weitere Informationen finden Sie unter „Zuschneiden von Bildern zur Verwendung mit Modulen für visuelle Ähnlichkeit und Entitätserkennung“. Das folgende Beispiel zeigt die Zuordnung zwischen den Bereichsfeldern und den Abfrageparametern, die Sie zum Zuschneiden von Bildern verwenden.  

-   Links wird [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal) zugeordnet.  
-   Oben wird [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat) zugeordnet.  
-   Rechts wird [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) zugeordnet.  
-   Unten wird [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab) zugeordnet.  

## <a name="finding-visually-similar-images"></a>Suchen nach visuell ähnlichen Bildern  

Um nach Bildern zu suchen, die dem ursprünglichen Bild visuell ähnlich sind, legen Sie den [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)-Abfrageparameter auf „SimilarImages“ fest.  

Die folgende Anforderung zeigt, wie visuell ähnliche Bilder abgerufen werden. Die Anforderung verwendet den [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)-Abfrageparameter, um das ursprüngliche Bild zu identifizieren. Um die Relevanz zu verbessern, sollten Sie die Abfragezeichenfolge des Benutzers einschließen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Zuschneiden von Bildern zur Verwendung mit Modulen für visuelle Ähnlichkeit und Entitätserkennung  

Um den Bereich des Bilds anzugeben, den Bing verwendet, um festzustellen, ob Bilder visuell ähnlich sind oder um eine Entitätserkennung auszuführen, verwenden Sie die Abfrageparameter [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab) und [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car). Standardmäßig verwendet Bing das gesamte Bild.  

Die Parameter geben die obere linke und die untere rechte Ecke des Bereichs an, den Bing für den Vergleich verwendet. Geben Sie die Werte als Bruchteil der Breite und Höhe des Originalbilds an. Die Bruchwerte beginnen mit (0,0, 0,0) in der oberen linken Ecke und enden mit (1,0, 1,0) in der unteren rechten Ecke. Um beispielsweise anzugeben, dass die obere linke Ecke ein Viertel des Weges von oben nach unten und ein Viertel des Weges von der linken Seite nach innen beginnt, legen Sie `cal` auf 0,25 und `cat` auf 0,25 fest.  

Die folgende Sequenz von Aufrufen zeigt die Auswirkung der Angabe des Zuschneidebereichs an. Der erste Aufruf enthält keine Zuschneideoption, und Bing erkennt zwei Personen, die nebeneinander in der Mitte des Bilds stehen.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Die Antwort zeigt zwei erkannte Entitäten.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

Der zweite Aufruf schneidet das Bild vertikal in der Mitte zu, und Bing hat eine einzelne Person auf der rechten Seite des Bilds erkannt.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Die Antwort zeigt eine erkannte Entität.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Suchen nach visuell ähnlichen Produkten  

Um nach Bildern zu suchen, die Produkte enthalten, die den im ursprünglichen Bild gefundenen Produkten visuell ähnlich sind, legen Sie den [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)-Abfrageparameter auf „SimilarProducts“ fest.  

Die folgende Anforderung zeigt, wie Bilder visuell ähnlicher Produkte abgerufen werden. Die Anforderung verwendet den [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)-Abfrageparameter, um das ursprüngliche Bild zu identifizieren, das in einer vorherigen Anforderung zurückgegeben wurde. Um die Relevanz zu verbessern, sollten Sie die Abfragezeichenfolge des Benutzers einschließen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung. Die Antwort enthält ein Bild eines ähnlichen Produkts und gibt an, wie viele Händler das Produkt online anbieten und ob Produktbewertungen vorliegen. Außerdem nennt sie den niedrigsten gefundenen Preis (siehe `aggregateOffer`-Feld).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Um eine Liste der Anbietern abzurufen, die das Produkt online anbieten (siehe [offerCount](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)-Feld), rufen Sie die API erneut auf, und legen Sie `modules` auf „ShoppingSources“ fest. Legen Sie dann den `insightsToken`-Abfrageparameter auf das Token fest, das im Produktzusammenfassungsbild gefunden wurde.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```