---
title: Ausführen mehrerer abhängiger Dienste mithilfe von .NET Core und Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Schnelle Kubernetes-Entwicklung mit Containern und Microservices in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
ms.openlocfilehash: 487ad5c4f68f2fd965384a33aa9f6c0e8da351a4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800744"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Entwicklung mit mehreren Diensten mit Azure Dev Spaces

In diesem Tutorial erfahren Sie, wie Sie unter Verwendung von Azure Dev Spaces Anwendungen mit mehreren Diensten entwickeln. Außerdem werden hier einige der Vorteile von Dev Spaces behandelt.

## <a name="call-another-container"></a>Aufrufen eines anderen Containers
In diesem Abschnitt erstellen Sie einen zweiten Dienst (`mywebapi`) und lassen ihn von `webfrontend` aufrufen. Jeder Dienst wird in einem separaten Container ausgeführt. Anschließend debuggen Sie beide Container.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Herunterladen von Beispielcode für *mywebapi*
Laden Sie Beispielcode aus einem GitHub-Repository herunter, um Zeit zu sparen. Navigieren Sie zu https://github.com/Azure/dev-spaces, und klicken Sie auf **Klonen oder herunterladen**, um das GitHub-Repository herunterzuladen. Der Code für diesen Abschnitt befindet sich in `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Ausführen von *mywebapi*
1. Öffnen Sie das Projekt `mywebapi` in einem *separaten Visual Studio-Fenster*.
1. Wählen Sie wie zuvor für das Projekt `webfrontend` im Dropdownmenü mit den Starteinstellungen **Azure Dev Spaces** aus. Erstellen Sie diesmal keinen neuen AKS-Cluster, sondern wählen Sie den bereits erstellten Cluster aus. Übernehmen Sie wie zuvor für „Space“ (Bereich) den Standardwert `default`, und klicken Sie auf **OK**. Sie sehen im Ausgabefenster, dass Visual Studio diesen neuen Dienst in Ihrem Entwicklungsbereich vorbereitet, um das Debuggen zu beschleunigen.
1. Drücken Sie F5, und warten Sie, bis der Dienst erstellt und bereitgestellt wurde. Der Vorgang ist abgeschlossen, wenn die Statusleiste von Visual Studio orange angezeigt wird.
1. Notieren Sie die Endpunkt-URL, die im Fenster **Ausgabe** im Bereich **Azure Dev Spaces for AKS** (Azure Dev Spaces für AKS) angezeigt wird. Sie sieht ungefähr wie folgt aus: `http://localhost:<portnumber>`. Es sieht unter Umständen so aus, als würde der Container lokal ausgeführt, tatsächlich wird er jedoch im Entwicklungsbereich in Azure ausgeführt.
2. Wenn `mywebapi` bereit ist, öffnen Sie in Ihrem Browser die localhost-Adresse, und fügen Sie `/api/values` an die URL an, um die Standard-GET-API für `ValuesController` aufzurufen. 
3. Wenn alle Schritte erfolgreich ausgeführt wurden, sollte eine Antwort vom Dienst `mywebapi` angezeigt werden, die wie folgt aussieht:

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Senden einer Anforderung von *webfrontend* an *mywebapi*
Schreiben Sie nun Code in `webfrontend`, der eine Anforderung an `mywebapi` sendet. Wechseln Sie zum Visual Studio-Fenster mit dem Projekt `webfrontend`. *Ersetzen* Sie in der Datei `HomeController.cs` den Code für die About-Methode durch den folgenden Code:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

Im obigen Codebeispiel wird der Header `azds-route-as` aus der eingehenden Anforderung an die ausgehende Anforderung weitergeleitet. Später wird gezeigt, wie dadurch eine produktivere Entwicklungsumgebung in [Teamszenarien](team-development-netcore-visualstudio.md) ermöglicht wird.

### <a name="debug-across-multiple-services"></a>Debuggen mehrerer Dienste
1. Zu diesem Zeitpunkt sollte `mywebapi` noch mit angefügtem Debugger ausgeführt werden. Ist dies nicht der Fall, drücken Sie im Projekt `mywebapi` F5.
1. Legen Sie einen Breakpoint in der `Get(int id)`-Methode in der Datei `Controllers/ValuesController.cs` fest, die GET-Anforderungen für `api/values/{id}` verarbeitet.
1. Legen Sie im Projekt `webfrontend`, in das Sie den obigen Code eingefügt haben, direkt vor dem Senden einer GET-Anforderung an `mywebapi/api/values` einen Breakpoint fest.
1. Drücken Sie im Projekt `webfrontend` F5. Visual Studio öffnet erneut einen Browser mit dem entsprechenden localhost-Port, und die Web-App wird angezeigt.
1. Klicken Sie oben auf der Seite auf den Link **Info**, um den Breakpoint im Projekt `webfrontend` auszulösen. 
1. Drücken Sie F10, um den Vorgang fortzusetzen. Der Breakpoint im Projekt `mywebapi` wird nun ausgelöst.
1. Drücken Sie F5, um den Vorgang fortzusetzen, und Sie befinden sich wieder im Code im Projekt `webfrontend`.
1. Durch nochmaliges Drücken von F5 wird die Anforderung abgeschlossen und eine Seite im Browser zurückgegeben. In der Web-App wird auf der Seite „Info“ eine gemeinsame Nachricht der beiden Dienste angezeigt: „Hello from webfrontend and Hello from mywebapi“.

Gut gemacht! Sie besitzen nun eine Anwendung mit mehreren Containern, in der die einzelnen Container separat entwickelt und bereitgestellt werden können.

### <a name="automatic-tracing-for-http-messages"></a>Automatische Nachverfolgung für HTTP-Nachrichten
*webfrontend* enthält zwar keinen speziellen Code zum Ausgeben des an *mywebapi* gerichteten HTTP-Aufrufs, trotzdem werden im Ausgabefenster HTTP-Nachverfolgungsnachrichten angezeigt:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --gyk-> webfrontend:
   GET /Home/About HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend-668b7ddb9f-n5rhj --pu5-> mywebapi:
   GET /api/values/1 HTTP/1.1

// Response from *mywebapi*
webfrontend-668b7ddb9f-n5rhj <-pu5-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-gyk-- webfrontend:
   HTTP/1.1 200 OK
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-sc...<[TRUNCATED]>
```
Das ist einer der Vorteile, der sich durch die Verwendung der Dev Spaces-Instrumentierung ergibt. Wir fügen Komponenten ein, die HTTP-Anforderungen nachverfolgen, während sie das System durchlaufen, um Entwickler bei der Nachverfolgung komplexer Aufrufe für mehrere Dienste zu unterstützen.

### <a name="well-done"></a>Gut gemacht!
Sie besitzen nun eine Anwendung mit mehreren Containern, in der die einzelnen Container separat entwickelt und bereitgestellt werden können.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Teamentwicklung in Dev Spaces](team-development-netcore-visualstudio.md)
