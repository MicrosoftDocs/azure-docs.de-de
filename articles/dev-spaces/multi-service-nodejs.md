---
title: Ausführen mehrerer abhängiger Dienste mithilfe von Node.js und Visual Studio Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Schnelle Kubernetes-Entwicklung mit Containern und Microservices in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
ms.openlocfilehash: d6c09b51a0b5200ed8b723042b7ee5629210c4d2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800896"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Entwicklung mit mehreren Diensten mit Azure Dev Spaces

In diesem Tutorial erfahren Sie, wie Sie unter Verwendung von Azure Dev Spaces Anwendungen mit mehreren Diensten entwickeln. Außerdem werden hier einige der Vorteile von Dev Spaces behandelt.

## <a name="call-a-service-running-in-a-separate-container"></a>Aufrufen eines in einem separaten Container ausgeführten Diensts

In diesem Abschnitt erstellen Sie einen zweiten Dienst (`mywebapi`) und lassen ihn von `webfrontend` aufrufen. Jeder Dienst wird in einem separaten Container ausgeführt. Anschließend debuggen Sie beide Container.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Öffnen von Beispielcode für *mywebapi*
Der Beispielcode für `mywebapi` für diese Anleitung sollte bereits im Ordner `samples` enthalten sein. (Rufen Sie andernfalls https://github.com/Azure/dev-spaces auf, und klicken Sie auf **Klonen oder herunterladen**, um das GitHub-Repository herunterzuladen.) Der Code für diesen Abschnitt befindet sich in `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Ausführen von *mywebapi*
1. Öffnen Sie den Ordner `mywebapi` in einem *separaten VS Code-Fenster*.
1. Öffnen Sie die **Befehlspalette** (über das Menü **Ansicht | Befehlspalette**), verwenden Sie die automatische Vervollständigung für die Eingabe, und wählen Sie den Befehl `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces` aus. Dieser Befehl darf nicht mit dem Befehl `azds prep` verwechselt werden, der zum Konfigurieren des Projekts für die Bereitstellung dient.
1. Drücken Sie F5, und warten Sie, bis der Dienst erstellt und bereitgestellt wurde. Wenn der Prozess abgeschlossen ist, wird in der Debugkonsole die Meldung *An Port 80 lauschen* angezeigt.
1. Notieren Sie sich die Endpunkt-URL. Sie sieht ungefähr wie folgt aus: `http://localhost:<portnumber>`. **Tipp: Auf der Statusleiste von VS Code wird eine klickbare URL angezeigt.** Es sieht unter Umständen so aus, als würde der Container lokal ausgeführt, tatsächlich wird er jedoch in der Entwicklungsumgebung in Azure ausgeführt. Die localhost-Adresse wird verwendet, da `mywebapi` keine öffentlichen Endpunkte definiert hat und auf den Dienst nur über die Kubernetes-Instanz zugegriffen werden kann. Um die Interaktion mit dem privaten Dienst auf Ihrem lokalen Computer zu erleichtern, erstellt Azure Dev Spaces einen temporären SSH-Tunnel zu dem in Azure ausgeführten Container.
1. Öffnen Sie in Ihrem Browser die localhost-Adresse, wenn `mywebapi` bereit ist. Eine Antwort vom Dienst `mywebapi` („Hello from mywebapi“) sollte angezeigt werden.


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Senden einer Anforderung von *webfrontend* an *mywebapi*
Schreiben Sie nun Code in `webfrontend`, der eine Anforderung an `mywebapi` sendet.
1. Wechseln Sie ins VS Code-Fenster für `webfrontend`.
1. Fügen Sie die folgenden Codezeilen oben in `server.js` ein:
    ```javascript
    var request = require('request');
    ```

3. *Ersetzen Sie* den Code für den GET-Handler für `/api`. Bei der Verarbeitung einer Anforderung ruft diese wiederum `mywebapi` auf und gibt anschließend die Ergebnisse aus beiden Diensten zurück.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *Entfernen Sie* die `server.close()`-Zeile am Ende von `server.js`.

Im obigen Codebeispiel wird der Header `azds-route-as` aus der eingehenden Anforderung an die ausgehende Anforderung weitergeleitet. Sie erfahren später, wie dadurch Teams bei der gemeinsamen Entwicklung unterstützt werden.

### <a name="debug-across-multiple-services"></a>Debuggen mehrerer Dienste
1. Zu diesem Zeitpunkt sollte `mywebapi` noch mit angefügtem Debugger ausgeführt werden. Ist dies nicht der Fall, drücken Sie im Projekt `mywebapi` F5.
1. Legen Sie im GET-Standardhandler für `/` einen Breakpoint fest.
1. Legen Sie im Projekt `webfrontend` direkt vor dem Senden einer GET-Anforderung an `http://mywebapi` einen Breakpoint fest.
1. Drücken Sie im Projekt `webfrontend` F5.
1. Öffnen Sie die Web-App, und durchlaufen Sie den Code in beiden Diensten. In der Web-App sollte eine gemeinsame Nachricht der beiden Dienste angezeigt werden: „Hello from webfrontend and Hello from mywebapi“.

### <a name="automatic-tracing-for-http-messages"></a>Automatische Nachverfolgung für HTTP-Nachrichten
*webfrontend* enthält zwar keinen speziellen Code zum Ausgeben des an *mywebapi* gerichteten HTTP-Aufrufs, trotzdem werden im Ausgabefenster HTTP-Nachverfolgungsnachrichten angezeigt:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --hyh-> webfrontend:
   GET /api?_=1544485357627 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --1b1-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-1b1-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-hyh-- webfrontend:
   HTTP/1.1 200 OK
   Hello from webfrontend and Hello from mywebapi
```
Das ist einer der Vorteile, der sich durch die Verwendung der Dev Spaces-Instrumentierung ergibt. Wir fügen Komponenten ein, die HTTP-Anforderungen nachverfolgen, während sie das System durchlaufen, um Entwickler bei der Nachverfolgung komplexer Aufrufe für mehrere Dienste zu unterstützen.

### <a name="well-done"></a>Gut gemacht!
Sie besitzen nun eine Anwendung mit mehreren Containern, in der die einzelnen Container separat entwickelt und bereitgestellt werden können.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Teamentwicklung in Dev Spaces](team-development-nodejs.md)
