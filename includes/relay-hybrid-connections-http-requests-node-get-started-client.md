---
title: Includedatei
description: Includedatei
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66166189"
---
### <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung

Wenn Sie die Option „Clientautorisierung erforderlich“ bei der Relay-Erstellung deaktiviert haben, können Sie mit jedem Browser Anforderungen an die URL der Hybridverbindungen senden. Für den Zugriff auf geschützte Endpunkte müssen Sie ein Token im `ServiceBusAuthorization`-Header, der hier gezeigt wird, erstellen und übergeben.

Erstellen Sie zu Beginn eine neue JavaScript-Datei namens `sender.js`.

### <a name="add-the-relay-npm-package"></a>Hinzufügen des Relay-NPM-Pakets

Führen Sie `npm install hyco-https` über eine Node-Eingabeaufforderung in Ihrem Projektordner aus. Dieses Paket importiert auch das reguläre `https`-Paket. Für die Clientseite ist der wesentliche Unterschied, dass das Paket Funktionen zum Erstellen von Relay-URIs und Token bietet.

### <a name="write-some-code-to-send-messages"></a>Schreiben von Code zum Senden von Nachrichten

1. Fügen Sie die folgenden `constants` am Anfang der Datei `sender.js` hinzu:
   
    ```js
    const https = require('hyco-https');
    ```

2. Fügen Sie der Datei `sender.js` die folgenden Konstanten als Hybridverbindungsdetails hinzu. Ersetzen Sie die Platzhalter in Klammern durch die Werte, die beim Erstellen der Hybridverbindung abgerufen wurden.
   
   1. `const ns` – der Relay-Namespace. Achten Sie darauf, dass Sie den vollqualifizierten Namespacenamen verwenden, wie z.B. `{namespace}.servicebus.windows.net`.
   2. `const path` – der Name der Hybridverbindung
   3. `const keyrule` – der Name des SAS-Schlüssels
   4. `const key` – der Wert des SAS-Schlüssels

3. Fügen Sie der Datei `sender.js` den folgenden Code hinzu. Sie werden feststellen, dass der Code sich nicht wesentlich von der normalen Verwendung des Node.js-HTTPS-Clients unterscheidet; er fügt nur den Autorisierungsheader hinzu.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    Die Datei „sender.js“ sollte nun wie folgt aussehen:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

