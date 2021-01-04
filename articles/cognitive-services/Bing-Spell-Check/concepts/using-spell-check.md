---
title: Verwenden der Bing-Rechtschreibprüfungs-API
titleSuffix: Azure Cognitive Services
description: Hier erhalten Sie Informationen zu den Modi und Einstellungen der Bing-Rechtschreibprüfung sowie weitere Informationen im Zusammenhang mit der API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 2031c31c6ea083452bbdbb95be74adf29be1f858
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349315"
---
# <a name="using-the-bing-spell-check-api"></a>Verwenden der Bing-Rechtschreibprüfungs-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In diesem Artikel erfahren Sie, wie mithilfe der Bing-Rechtschreibprüfungs-API kontextbezogene Grammatik- und Rechtschreibprüfungen durchgeführt werden. Während für die meisten Rechtschreibprüfungen wörterbuchbasierte Regelsätze genutzt werden, werden bei der Bing-Rechtschreibprüfung Machine Learning und die statistische maschinelle Übersetzung verwendet, um präzise und kontextbezogene Korrekturen bereitzustellen. 

## <a name="spell-check-modes"></a>Rechtschreibprüfungsmodi

Die API unterstützt zwei Prüfmodi: `Proof` und `Spell`.  Beispiele finden Sie [hier](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Proof (für Dokumente) 

Der Standardmodus ist `Proof`. Der Rechtschreibmodus `Proof` bietet die umfangreichsten Prüfungen – unter anderem mit Features für Groß-/Kleinschreibung, grundlegende Zeichensetzung und anderen hilfreichen Features für die Dokumenterstellung. Er ist allerdings nur für „en-US“ (Englisch – USA), „es-ES“ (Spanisch) und „pt-BR“ (Portugiesisch) verfügbar. (Hinweis: Für Spanisch und Portugiesisch steht nur eine Betaversion zur Verfügung.) Für alle anderen Märkte muss der Modusabfrageparameter auf „Spell“ festgelegt werden. 

> [!NOTE]
> Wenn die Länge des Abfragetexts 4.096 Zeichen übersteigt, wird er vor der Verarbeitung auf 4.096 Zeichen gekürzt. 

### <a name="spell----for-web-searchesqueries"></a>Spell (für Websuchvorgänge/-abfragen)

`Spell` ist aggressiver, um bessere Suchergebnisse zu liefern. Der Modus `Spell` findet zwar die meisten Rechtschreibfehler, aber nicht alle Grammatikfehler, die von `Proof` erkannt werden (beispielsweise Groß-/Kleinschreibung und Wortwiederholungen).

> [!NOTE]
> * Die maximale unterstützte Länge ist unten angegeben. Überschreitet die Abfrage die maximale Länge, werden die Abfrage und ihre Ergebnisse nicht geändert.
>    * 130 Zeichen für die folgenden Sprachcodes: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. 
>    * 65 Zeichen für alle anderen Sprachcodes.
> * Der Rechtschreibprüfungsmodus unterstützt keine eckigen Klammern (`[` und `]`) in Abfragen, was zu inkonsistenten Ergebnissen führen kann. Es wird empfohlen, diese Klammern aus den Abfragen zu entfernen, wenn Sie den Rechtschreibprüfungsmodus nutzen.

## <a name="market-setting"></a>Markteinstellung

Ein [Marktcode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) muss mit dem `mkt`-Abfrageparameter in Ihrer Anforderung angegeben werden. Andernfalls verwendet die API einen Standardmarkt basierend auf der IP-Adresse der Anforderung.


## <a name="http-post-and-get-support"></a>Unterstützung von HTTP POST und GET

Die API unterstützt HTTP POST oder HTTP GET. Welche dieser Optionen Sie verwenden, hängt von der Länge des zu prüfenden Texts ab. Wenn die Zeichenfolgen immer weniger als 1.500 Zeichen umfassen, verwenden Sie GET. Für Zeichenfolgen mit einer Länge von bis zu 10.000 Zeichen muss dagegen POST verwendet werden. Die Textzeichenfolge kann beliebige gültige UTF-8-Zeichen enthalten.

Das folgende Beispiel zeigt eine POST-Anforderung für die Überprüfung der Rechtschreibung und Grammatik einer Textzeichenfolge. Der Vollständigkeit halber enthält das Beispiel auch den Abfrageparameter [mode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode). Dieser ist hier aber nicht unbedingt notwendig, da `mode` standardmäßig „Proof“ verwendet. Der Abfrageparameter [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) enthält die zu prüfende Zeichenfolge.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Bei Verwendung von HTTP GET muss der Abfrageparameter `text` in die Abfragezeichenfolge der URL eingefügt werden.
  
Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung. Die Antwort enthält ein Objekt vom Typ [SpellCheck](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
Im Feld [flaggedTokens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) werden die Rechtschreib- und Grammatikfehler aufgeführt, die die API in der Zeichenfolge [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) gefunden hat. Das Feld `token` enthält das zu ersetzende Wort. Der nullbasierte Offset im Feld `offset` ermöglicht es, das Token in der Zeichenfolge `text` zu finden. Anschließend wird das Wort an dieser Position durch das Wort im Feld `suggestion` ersetzt. 

Ist das Feld `type` auf „RepeatedToken“ festgelegt, wird das Token weiterhin durch `suggestion` ersetzt, dabei muss aber wahrscheinlich auch das nachfolgende Leerzeichen entfernt werden.

## <a name="throttling-requests"></a>Drosselungsanforderungen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?](../overview.md)
- [Referenz zur Bing-Rechtschreibprüfungs-API v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)