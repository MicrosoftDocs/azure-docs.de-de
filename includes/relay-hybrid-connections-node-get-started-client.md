---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 9d4f7faa18ee7fae158afb42b8c42287e61dd103
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178678"
---
### <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung

Erstellen Sie eine neue JavaScript-Datei namens `sender.js`.

### <a name="add-the-relay-npm-package"></a>Hinzufügen des Relay-NPM-Pakets

Führen Sie `npm install hyco-ws` über eine Node-Eingabeaufforderung in Ihrem Projektordner aus.

### <a name="write-some-code-to-send-messages"></a>Schreiben von Code zum Senden von Nachrichten

1. Fügen Sie die folgenden `constants` am Anfang der Datei `sender.js` hinzu:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Fügen Sie der Datei `sender.js` die folgenden Konstanten als Hybridverbindungsdetails hinzu. Ersetzen Sie die Platzhalter in Klammern durch die Werte, die beim Erstellen der Hybridverbindung abgerufen wurden.
   
   1. `const ns` – der Relay-Namespace. Achten Sie darauf, dass Sie den vollqualifizierten Namespacenamen verwenden, wie z.B. `{namespace}.servicebus.windows.net`.
   2. `const path` – der Name der Hybridverbindung
   3. `const keyrule` – der Name des SAS-Schlüssels
   4. `const key` – der Wert des SAS-Schlüssels

3. Fügen Sie der Datei `sender.js` den folgenden Code hinzu:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    Die Datei „sender.js“ sollte nun wie folgt aussehen:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

