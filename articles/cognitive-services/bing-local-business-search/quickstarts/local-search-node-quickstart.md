---
title: 'Schnellstart: Senden einer Abfrage an die API mit Node.js – Bing-Suche für ortsansässige Unternehmen'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Schnellstart, um Anforderungen an die API für die Bing-Suche nach ortsansässigen Unternehmen zu senden, die zum Leistungsumfang von Cognitive Services gehört.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 03e1b159dbdc26d53af290c4370581788562ff3b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499627"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Schnellstart: Senden einer Abfrage an die API für die Bing-Suche nach ortsansässigen Unternehmen mit Node.js

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Verwenden Sie diesen Schnellstart, um zu lernen. wie Sie Anforderungen an die API für die Bing-Suche für ortsansässige Unternehmen senden, die zum Leistungsumfang von Azure Cognitive Services gehört. Diese einfache Anwendung ist zwar in Node.js geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit jeder Programmiersprache kompatibel ist, die HTTP-Anforderungen stellen und JSON analysieren kann.

Diese Beispielanwendung ruft lokale Antwortdaten aus der API für eine Suchabfrage ab.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Die aktuelle Version von [Node.js](https://nodejs.org/en/download/)
* Die [JavaScript-Bibliothek für Anforderungen](https://github.com/request/request).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Erstellen einer Ressource für die Bing-Suche"  target="_blank"> im Azure-Portal eine Ressource für die Bing-Suche <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.


## <a name="code-scenario"></a>Codeszenario

Der folgende Code definiert und sendet die Anforderung, die in den folgenden Schritten implementiert wird:

1. Deklarieren Sie Variablen zum Angeben des Endpunkts nach Host und Pfad.
2. Geben Sie die Abfrage an, und fügen Sie den Abfrageparameter hinzu.
3. Erstellen Sie eine Handlerfunktion für die Antwort.
4. Definieren Sie die Suchfunktion, die die Anforderung erstellt und den Header `Ocp-Apim-Subscription-Key` hinzufügt.
5. Führen Sie die Suchfunktion aus.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Suche nach ortsansässigen Unternehmen mit C#](local-quickstart.md)
* [Schnellstart: Suche nach ortsansässigen Unternehmen mit Java](local-search-java-quickstart.md)
* [Schnellstart: Suche nach ortsansässigen Unternehmen mit Python](local-search-python-quickstart.md)