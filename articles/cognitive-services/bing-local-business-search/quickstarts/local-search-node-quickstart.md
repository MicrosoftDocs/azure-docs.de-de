---
title: 'Schnellstart: Senden einer Abfrage an die API für die Bing-Suche nach ortsansässigen Unternehmen mit Node.js | Microsoft-Dokumentation'
titleSuffix: Azure Cognitive Services
description: Einführung in die Verwendung der API für die Bing-Suche nach ortsansässigen Unternehmen mit Node
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d0760f89eb98955f7ebb503ce59f904192635f7a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796894"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Schnellstart: Senden einer Abfrage an die API für die Bing-Suche nach ortsansässigen Unternehmen mit Node.js

Verwenden Sie diesen Schnellstart, um Anforderungen an die API für die Bing-Suche nach ortsansässigen Unternehmen zu senden, die zum Leistungsumfang von Cognitive Services gehört. Diese einfache Anwendung ist zwar in Node.js geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit jeder Programmiersprache kompatibel ist, die HTTP-Anforderungen stellen und JSON analysieren kann.

Diese Beispielanwendung ruft lokale Antwortdaten aus der API für die Suchabfrage `hotel in Bellevue` ab.

## <a name="prerequisites"></a>Voraussetzungen

* Die aktuelle Version von [Node.js](https://nodejs.org/en/download/)

* Die [JavaScript-Bibliothek für Anforderungen](https://github.com/request/request)

Sie benötigen ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit Bing-APIs. Die [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ist für diesen Schnellstart ausreichend. Verwenden Sie den Zugriffsschlüssel der kostenlosen Testversion.  Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

## <a name="code-scenario"></a>Codeszenario

Der folgende Code wird definiert und sendet die Anforderung. Er wird in den folgenden Schritten implementiert:

1. Deklarieren Sie Variablen zum Angeben des Endpunkts nach Host und Pfad.
2. Geben Sie die Abfrage an, und fügen Sie den Abfrageparameter hinzu.
3. Erstellen Sie eine Handlerfunktion für die Antwort.
4. Definieren Sie die Suchfunktion, die die Anforderung erstellt und den Header „Ocp-Apim-Subscription-Key“ hinzufügt.
5. Führen Sie die Suchfunktion aus.

Der vollständige Code für diese Demo sieht wie folgt aus:

```
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

* [Schnellstart: Suche nach ortsansässigen Unternehmen](local-quickstart.md)
* [Schnellstart: Suche nach ortsansässigen Unternehmen mit Java](local-search-java-quickstart.md)
* [Schnellstart: Suche nach ortsansässigen Unternehmen mit Python](local-search-python-quickstart.md)
