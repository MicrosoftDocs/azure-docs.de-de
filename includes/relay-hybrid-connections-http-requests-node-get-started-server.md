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
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66166171"
---
### <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung

Erstellen Sie eine neue JavaScript-Datei namens `listener.js`.

### <a name="add-the-relay-npm-package"></a>Hinzufügen des Relay-NPM-Pakets

Führen Sie `npm install hyco-https` über eine Node-Eingabeaufforderung in Ihrem Projektordner aus.

### <a name="write-some-code-to-handle-requests"></a>Schreiben von Code für die Verarbeitung von Anforderungen

1. Fügen Sie am Anfang der `listener.js`-Datei die folgende Konstante hinzu.

    ```js
    const https = require('hyco-https');
    ```
2. Fügen Sie der Datei `listener.js` die folgenden Konstanten als Hybridverbindungsdetails hinzu. Ersetzen Sie die Platzhalter in Klammern durch die Werte, die beim Erstellen der Hybridverbindung abgerufen wurden.

   1. `const ns` – der Relay-Namespace. Achten Sie darauf, dass Sie den vollqualifizierten Namespacenamen verwenden, wie z.B. `{namespace}.servicebus.windows.net`.
   2. `const path` – der Name der Hybridverbindung
   3. `const keyrule` – der Name des SAS-Schlüssels
   4. `const key` – der Wert des SAS-Schlüssels

3. Fügen Sie der Datei `listener.js` den folgenden Code hinzu. :

    Sie werden feststellen, dass der Code sich nicht wesentlich von einem einfachen HTTP-Serverbeispiel unterscheidet, wie Sie es in Node.js-Einsteigertutorials finden, mit Ausnahme der Verwendung von `createRelayedServer` anstelle der normalen `createServer`-Funktion.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    Die Datei „listener.js“ sollte nun wie folgt aussehen:
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

