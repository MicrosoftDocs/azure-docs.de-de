---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178681"
---
### <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung

Erstellen Sie eine neue JavaScript-Datei namens `listener.js`.

### <a name="add-the-relay-npm-package"></a>Hinzufügen des Relay-NPM-Pakets

Führen Sie `npm install hyco-ws` über eine Node-Eingabeaufforderung in Ihrem Projektordner aus.

### <a name="write-some-code-to-receive-messages"></a>Schreiben von Code zum Empfangen von Nachrichten

1. Fügen Sie am Anfang der `listener.js`-Datei die folgende Konstante hinzu.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Fügen Sie der Datei `listener.js` die folgenden Konstanten als Hybridverbindungsdetails hinzu. Ersetzen Sie die Platzhalter in Klammern durch die Werte, die beim Erstellen der Hybridverbindung abgerufen wurden.
   
   1. `const ns` – der Relay-Namespace. Achten Sie darauf, dass Sie den vollqualifizierten Namespacenamen verwenden, wie z.B. `{namespace}.servicebus.windows.net`.
   2. `const path` – der Name der Hybridverbindung
   3. `const keyrule` – der Name des SAS-Schlüssels
   4. `const key` – der Wert des SAS-Schlüssels

3. Fügen Sie der Datei `listener.js` den folgenden Code hinzu:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Die Datei „listener.js“ sollte nun wie folgt aussehen:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

