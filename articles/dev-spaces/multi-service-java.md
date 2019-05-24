---
title: Ausführen mehrerer abhängiger Dienste mithilfe von Java und Visual Studio Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Schnelle Kubernetes-Entwicklung mit Containern und Microservices in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
manager: yuvalm
ms.openlocfilehash: a5afd093e0f961d048681465850419c5e8712557
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800389"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Entwicklung mit mehreren Diensten mit Azure Dev Spaces

In diesem Tutorial erfahren Sie, wie Sie unter Verwendung von Azure Dev Spaces Anwendungen mit mehreren Diensten entwickeln. Außerdem werden hier einige der Vorteile von Dev Spaces behandelt.

## <a name="call-a-service-running-in-a-separate-container"></a>Aufrufen eines in einem separaten Container ausgeführten Diensts

In diesem Abschnitt erstellen Sie einen zweiten Dienst (`mywebapi`) und lassen ihn von `webfrontend` aufrufen. Jeder Dienst wird in einem separaten Container ausgeführt. Anschließend debuggen Sie beide Container.

![Mehrere Container](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Herunterladen von Beispielcode für *mywebapi*
Laden Sie Beispielcode aus einem GitHub-Repository herunter, um Zeit zu sparen. Navigieren Sie zu https://github.com/Azure/dev-spaces, und klicken Sie auf **Klonen oder herunterladen**, um das GitHub-Repository herunterzuladen. Der Code für diesen Abschnitt befindet sich in `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Ausführen von *mywebapi*
1. Öffnen Sie den Ordner `mywebapi` in einem *separaten VS Code-Fenster*.
1. Öffnen Sie die **Befehlspalette** (über das Menü **Ansicht | Befehlspalette**), verwenden Sie die automatische Vervollständigung für die Eingabe, und wählen Sie den Befehl `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces` aus.
1. Drücken Sie F5, und warten Sie, bis der Dienst erstellt und bereitgestellt wurde. Wenn der Prozess abgeschlossen ist, wird in der Debugging-Konsole etwa folgende Meldung angezeigt:

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. Die Endpunkt-URL sieht ungefähr wie folgt aus: `http://localhost:<portnumber>`. **Tipp: Auf der Statusleiste von VS Code wird eine klickbare URL angezeigt.** Es sieht unter Umständen so aus, als würde der Container lokal ausgeführt. Tatsächlich wird er jedoch im Entwicklungsbereich in Azure ausgeführt. Die localhost-Adresse wird verwendet, da `mywebapi` keine öffentlichen Endpunkte definiert hat und auf den Dienst nur über die Kubernetes-Instanz zugegriffen werden kann. Um die Interaktion mit dem privaten Dienst auf Ihrem lokalen Computer zu erleichtern, erstellt Azure Dev Spaces einen temporären SSH-Tunnel zu dem in Azure ausgeführten Container.
1. Öffnen Sie in Ihrem Browser die localhost-Adresse, wenn `mywebapi` bereit ist.
1. Wenn alle Schritte erfolgreich ausgeführt wurden, sollte eine Antwort vom Dienst `mywebapi` angezeigt werden.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Senden einer Anforderung von *webfrontend* an *mywebapi*
Schreiben Sie nun Code in `webfrontend`, der eine Anforderung an `mywebapi` sendet.
1. Wechseln Sie ins VS Code-Fenster für `webfrontend`.
1. *Fügen Sie* die folgenden `import`-Anweisungen nach der `package`-Anweisung hinzu:

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *Ersetzen Sie* den Code für die Begrüßungsmethode:

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

Im obigen Codebeispiel wird der Header `azds-route-as` aus der eingehenden Anforderung an die ausgehende Anforderung weitergeleitet. Sie erfahren später, wie dadurch Teams bei der gemeinsamen Entwicklung unterstützt werden.

### <a name="debug-across-multiple-services"></a>Debuggen mehrerer Dienste
1. Zu diesem Zeitpunkt sollte `mywebapi` noch mit angefügtem Debugger ausgeführt werden. Ist dies nicht der Fall, drücken Sie im Projekt `mywebapi` F5.
1. Legen Sie einen Haltepunkt in der `index()`-Methode des `webapi`-Projekts fest.
1. Legen Sie im Projekt `webfrontend` direkt vor dem Senden einer GET-Anforderung an `mywebapi` einen Haltepunkt in der mit `try` beginnenden Zeile fest.
1. Drücken Sie F5 im `webfrontend`-Projekt (oder starten Sie den Debugger neu, wenn er derzeit ausgeführt wird).
1. Rufen Sie die Web-App auf, und durchlaufen Sie den Code in beiden Diensten.
1. In der Web-App wird auf der Seite „Info“ eine gemeinsame Nachricht der beiden Dienste angezeigt: „Hello from webfrontend and Hello from mywebapi“.

### <a name="automatic-tracing-for-http-messages"></a>Automatische Nachverfolgung für HTTP-Nachrichten
*webfrontend* enthält zwar keinen speziellen Code zum Ausgeben des an *mywebapi* gerichteten HTTP-Aufrufs, trotzdem werden im Ausgabefenster HTTP-Nachverfolgungsnachrichten angezeigt:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --ytv-> webfrontend:8080:
   GET /greeting?_=1544503627515 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --ve4-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-ve4-- mywebapi:
   HTTP/1.1 200
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-ytv-- webfrontend:8080:
   HTTP/1.1 200
   Hello from webfrontend and
   Hello from mywebapi
```
Das ist einer der Vorteile, der sich durch die Verwendung der Dev Spaces-Instrumentierung ergibt. Wir fügen Komponenten ein, die HTTP-Anforderungen nachverfolgen, während sie das System durchlaufen, um Entwickler bei der Nachverfolgung komplexer Aufrufe für mehrere Dienste zu unterstützen.

### <a name="well-done"></a>Gut gemacht!
Sie besitzen nun eine Anwendung mit mehreren Containern, in der die einzelnen Container separat entwickelt und bereitgestellt werden können.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Teamentwicklung in Dev Spaces](team-development-java.md)
