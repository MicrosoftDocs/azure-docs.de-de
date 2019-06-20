---
title: Übersicht über die Azure Relay-Node-APIs | Microsoft-Dokumentation
description: Relay-Node-API – Übersicht
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 794e797e504d6064c13ffe0a4ed131e668d86e97
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64699389"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Node-API für Relay Hybrid Connections – Übersicht

## <a name="overview"></a>Übersicht

Das Node-Paket [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) für Azure Relay-Hybrid Connections basiert auf dem und erweitert das NPM-Paket [ws](https://www.npmjs.com/package/ws). Dieses Paket exportiert alle Exporte des Basispakets erneut und fügt neue Exporte hinzu, die die Integration mit dem Azure Relay-Feature „Hybrid Connections“ ermöglichen. 

Vorhandene Anwendungen, die `require('ws')` erfordern, können dieses Paket mit `require('hyco-ws')` verwenden, um Hybrid-Szenarien zu ermöglichen, in denen eine Anwendung lokal „innerhalb der Firewall“ sowie über Hybrid Connections auf WebSocket-Verbindungen lauschen kann.
  
## <a name="documentation"></a>Dokumentation

Die APIs sind [im Hauptpaket für „ws“ dokumentiert](https://github.com/websockets/ws/blob/master/doc/ws.md). Dieser Artikel beschreibt, inwiefern sich dieses Paket vom Basispaket unterscheidet. 

Der größte Unterschied zwischen dem Basispaket und diesem „hyco-ws“ besteht darin, dass es eine neue über `require('hyco-ws').RelayedServer` exportierte Serverklasse sowie einige Hilfsmethoden hinzufügt.

### <a name="package-helper-methods"></a>Paket-Hilfsmethoden

Im Paketexport sind mehrere Hilfsmethoden verfügbar, die Sie wie folgt referenzieren können:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Die Hilfsmethoden sind für dieses Paket bestimmt, können jedoch auch von einem Node-Server verwendet werden, um Web- oder Geräteclients das Erstellen von Listenern oder Sendern zu ermöglichen. Der Server verwendet diese Methoden, indem er ihnen URIs übergibt, die kurzlebige Token einbetten. Diese URIs können auch mit gängigen WebSocket-Stapeln verwendet werden, die das Festlegen von HTTP-Headern für den WebSocket-Handshake nicht unterstützen. Das Einbetten von Autorisierungstoken in die URI wird primär in Szenarien außerhalb der Bibliothek unterstützt. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Erstellt für den angegebenen Namespace und Pfad einen gültigen Listener-URI für Azure Relay Hybrid Connection. Dieser URI kann dann mit der Relay-Version der WebSocketServer-Klasse verwendet werden.

- `namespaceName` (erforderlich) – der domänenqualifizierte Name des zu verwendenden Azure-Relay-Namespace
- `path` (erforderlich) – der Name einer vorhandenen Azure Relay Hybrid Connection in diesem Namespace
- `token` (optional) – ein zuvor ausgegebenes Relay-Zugriffstoken, das in den Listener URI eingebettet ist (siehe folgendes Beispiel)
- `id` (optional) – ein Überwachungsbezeichner, der die komplette Diagnosenachverfolgung von Anforderungen ermöglicht

Der `token`-Wert ist optional und sollte nur verwendet werden, wenn es wie im Fall mit dem W3C-WebSocket-Stapel nicht möglich ist, HTTP-Header mit dem WebSocket-Handshake zu senden.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Erstellt für den angegebenen Namespace und Pfad einen gültigen Send-URI für Azure Relay Hybrid Connection. Dieser URI kann mit jedem WebSocket-Client verwendet werden.

- `namespaceName` (erforderlich) – der domänenqualifizierte Name des zu verwendenden Azure-Relay-Namespace
- `path` (erforderlich) – der Name einer vorhandenen Azure Relay Hybrid Connection in diesem Namespace
- `token` (optional) – ein zuvor ausgegebenes Relay-Zugriffstoken, das in den Send-URI eingebettet ist (siehe folgendes Beispiel)
- `id` (optional) – ein Überwachungsbezeichner, der die komplette Diagnosenachverfolgung von Anforderungen ermöglicht

Der `token`-Wert ist optional und sollte nur verwendet werden, wenn es wie im Fall mit dem W3C-WebSocket-Stapel nicht möglich ist, HTTP-Header mit dem WebSocket-Handshake zu senden.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Erstellt ein Azure Relay Shared Access Signature (SAS)-Token für den angegebenen Ziel-URI, die SAS-Regel und den SAS-Regelschlüssel, das für die angegebene Anzahl von Sekunden oder eine Stunde ab dem Zeitpunkt, zu dem das Ablaufargument ausgegeben wird, gültig ist.

- `uri` (erforderlich) – der URI, für den das Token ausgestellt wird Der URI wird zur Verwendung des HTTP-Protokollschemas normalisiert, und die Informationen zur Abfragezeichenfolgen werden entfernt.
- `ruleName` (erforderlich) – der Name der SAS Regel für die durch den angegebenen URI repräsentierten Entität oder für den durch den Hostteil der URI dargestellten Namespace.
- `key` (erforderlich) – der gültige Schlüssel für die SAS-Regel 
- `expirationSeconds` (optional) – die Anzahl der Sekunden, bis das generierte Token ablaufen soll Der Standardwert beträgt eine Stunde (3600), falls nichts angegeben wird.

Das ausgestellte Token überträgt die mit der angegebenen SAS-Regel für die angegebene Dauer zugeordneten Rechte.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Diese Methode entspricht funktionell der zuvor dokumentierten `createRelayToken`-Methode, aber gibt das Token ordnungsgemäß an die Eingabe-URI angefügt zurück.

### <a name="class-wsrelayedserver"></a>Klasse „ws.RelayedServer“

Die Klasse `hycows.RelayedServer` ist eine Alternative zur Klasse `ws.Server`, die nicht auf das lokale Netzwerk lauscht, sondern den Lauschvorgang an den Azure Relay-Dienst delegiert.

Die beiden Klassen sind größtenteils vertragskompatibel. Das bedeutet, dass eine vorhandene Anwendung mit der Klasse `ws.Server` problemlos zum Verwenden der Relay-Version geändert werden kann. Die größten Unterschiede liegen im Konstruktor und in den verfügbaren Optionen.

#### <a name="constructor"></a>Konstruktor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

Der Konstruktur `RelayedServer` unterstützt andere Argumente als den `Server`, da er kein eigenständiger Listener ist, oder nicht in ein vorhandenes HTTP-Listener-Framework eingebettet werden kann. Zudem sind weniger Optionen verfügbar, da die WebSocket-Verwaltung größtenteils an den Relay-Dienst delegiert werden kann.

Konstruktorargumente:

- `server` (erforderlich) – der vollqualifizierte URI für einen Hybrid Connection-Name, auf den gelauscht werden soll, in der Regel mit der Hilfsmethode WebSocket.createRelayListenUri() erstellt
- `token` (erforderlich) – dieses Argument enthält eine zuvor ausgegebene Token-Zeichenfolge oder eine Rückruffunktion, die zum Abrufen einer solchen Token-Zeichenfolge aufgerufen werden kann Die Rückrufoption wird empfohlen, da sie die Token-Verlängerung ermöglicht.

#### <a name="events"></a>Events

Instanzen vom Typ `RelayedServer` geben drei Ereignisse aus, mit denen Sie eingehende Anforderungen behandeln, Verbindungen herstellen und Fehlerzustände aufzeigen können. Sie müssen das Ereignis `connect` abonnieren, um Meldungen zu verarbeiten. 

##### <a name="headers"></a>headers

```JavaScript 
function(headers)
```

Das Ereignis `headers` wird ausgelöst, bevor eine eingehende Verbindung akzeptiert wird, und ermöglicht die Änderung der Header zum Senden an den Client. 

##### <a name="connection"></a>connection

```JavaScript
function(socket)
```

Wird ausgegeben, wenn eine neue WebSocket-Verbindung akzeptiert wird. Das Objekt entspricht dem Typ `ws.WebSocket` (identisch zum Basispaket).


##### <a name="error"></a>error

```JavaScript
function(error)
```

Wenn der zugrunde liegenden-Server einen Fehler ausgibt, wird er hier weitergeleitet.  

#### <a name="helpers"></a>Hilfsmethoden

Zum einfacheren Starten eines Relay-Servers und sofortigen Abonnieren von eingehenden Verbindungen bietet das Paket eine einfache Hilfsmethode, die wie in den folgenden Beispielen verwendet wird:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Diese Methode ruft den Konstruktor zum Erstellen einer neuen Instanz des RelayedServers auf und abonniert dann den bereitgestellten Rückruf an das Ereignis „connection“.
 
##### <a name="relayedconnect"></a>relayedConnect

Einfaches Spiegeln der Hilfsmethode `createRelayedServer` in der Funktion `relayedConnect` erstellt eine Clientverbindung und abonniert das Ereignis „open“ auf dem sich ergebenden Socket.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Relay finden Sie unter diesen Links:
* [Was ist Azure Relay?](relay-what-is-it.md)
* [Verfügbare Relay-APIs](relay-api-overview.md)
