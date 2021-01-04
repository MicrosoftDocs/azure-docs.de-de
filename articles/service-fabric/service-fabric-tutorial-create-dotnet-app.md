---
title: Erstellen einer .NET-App unter Service Fabric in Azure
description: In diesem Tutorial erfahren Sie, wie Sie eine Anwendung mit einem ASP.NET Core-Front-End und einem zustandsbehafteten zuverlässigen Dienst-Back-End erstellen und in einem Cluster bereitstellen.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-js, devx-track-csharp
ms.openlocfilehash: a783f5338dbc7ce0832b346492490525da332e39
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96297116"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Tutorial: Erstellen und Bereitstellen einer Anwendung mit einem ASP.NET Core-Web-API-Front-End-Dienst und einem zustandsbehafteten Back-End-Dienst

Dieses Tutorial ist der erste Teil einer Serie.  Hier erfahren Sie, wie Sie eine Azure Service Fabric-Anwendung mit einem ASP.NET Core-Web-API-Front-End und einem zustandsbehafteten Back-End-Dienst zum Speichern Ihrer Daten erstellen. Am Ende verfügen Sie über eine Abstimmungsanwendung mit einem ASP.NET Core-Web-Front-End, mit der Abstimmungsergebnisse im Cluster in einem zustandsbehafteten Back-End-Dienst gespeichert werden. Für diese Tutorialreihe ist ein Windows-Entwicklungscomputer erforderlich. Wenn Sie die Abstimmungsanwendung nicht manuell erstellen möchten, können Sie den [Quellcode für die fertige Anwendung herunterladen](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) und mit [Durchlaufen der Beispielanwendung für die Abstimmung](#walkthrough_anchor) fortfahren.  Wenn Sie möchten, können Sie sich auch eine [schrittweise Videoanleitung](https://channel9.msdn.com/Events/Connect/2017/E100) zu diesem Tutorial ansehen.

![AngularJS- + ASP.NET-API-Front-End, Herstellen einer Verbindung mit einem zustandsbehafteten Dienst in Service Fabric](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Im ersten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines ASP.NET Core-Web-API-Diensts als zustandsbehafteter zuverlässiger Dienst
> * Erstellen eines ASP.NET Core-Webanwendungsdiensts als zustandsloser Webdienst
> * Kommunizieren mit dem zustandsbehafteten Dienst unter Verwendung des Reverseproxys

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen einer .NET Service Fabric-Anwendung
> * [Bereitstellen der Anwendung in einem Remotecluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Hinzufügen eines HTTPS-Endpunkts zu einem ASP.NET Core-Front-End-Dienst](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurieren von CI/CD mit Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Einrichten der Überwachung und Diagnose für die Anwendung](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:
* Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Installieren Sie Visual Studio 2019](https://www.visualstudio.com/) (Version 15.5 oder höher) mit den Workloads **Azure-Entwicklung** und **ASP.NET und Webentwicklung**.
* [Installieren Sie das Service Fabric SDK](service-fabric-get-started.md).

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Erstellen eines ASP.NET-Web-API-Diensts als zuverlässigen Dienst

Erstellen Sie mithilfe von ASP.NET Core zunächst das Web-Front-End der Abstimmungsanwendung. ASP.NET Core ist ein einfaches, plattformübergreifendes Webentwicklungsframework, das Sie zur Erstellung von modernen Benutzeroberflächen und Web-APIs verwenden können. Damit Sie die Integration von ASP.NET Core mit Service Fabric vollständig verstehen, wird dringend empfohlen, den Artikel [ASP.NET Core in Service Fabric-Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) zu lesen. Für einen schnellen Einstieg reicht es jedoch, wenn Sie vorerst nur diesem Tutorial folgen. Weitere Informationen zu ASP.NET Core finden Sie in der [ASP.NET Core-Dokumentation](/aspnet/core/).

1. Starten Sie Visual Studio als **Administrator**.

2. Erstellen Sie mit **Datei**->**Neu**->**Projekt** ein Projekt.

3. Wählen Sie im Dialogfeld **Neues Projekt** die Option **Cloud > Service Fabric-Anwendung** aus.

4. Geben Sie der Anwendung den Namen **Voting**, und klicken Sie auf **OK**.

   ![Dialogfeld „Neues Projekt“ in Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Wählen Sie auf der Seite **Neuer Service Fabric-Dienst** die Option **Zustandsloses ASP.NET Core** aus, und nennen Sie den Dienst **VotingWeb**, und klicken Sie dann auf **OK**.
   
   ![Auswählen eines neuen ASP.NET-Webdienstes im Dialogfeld „Neuer Dienst“](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Die nächste Seite umfasst eine Reihe von ASP.NET Core-Projektvorlagen. Wählen Sie für dieses Tutorial die Option **Webanwendung (Model-View-Controller)** , und klicken Sie dann auf **OK**.
   
   ![Auswählen des ASP.NET-Projekttyps](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio erstellt ein Anwendungs- und ein Dienstprojekt und zeigt sie im Projektmappen-Explorer an.

   ![Projektmappen-Explorer nach der Erstellung der Anwendung mit einem ASP.NET Core-Web-API-Dienst]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>Aktualisieren der Datei „site.js“
Öffnen Sie die Datei **wwwroot/js/site.js**.  Ersetzen Sie den Inhalt der Datei durch den folgenden JavaScript-Code, der von den Home-Ansichten verwendet wird, und speichern Sie anschließend Ihre Änderungen:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Aktualisieren der Datei „Index.cshtml“

Öffnen Sie die Datei **Views/Home/Index.cshtml** (die spezifische Ansicht für den Home-Controller).  Ersetzen Sie den Inhalt durch Folgendes, und speichern Sie anschließend Ihre Änderungen:

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.Key)">
                            <span class="pull-left">
                                {{vote.Key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.Value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.Key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-_layoutcshtml-file"></a>Aktualisieren der Datei „_Layout.cshtml“

Öffnen Sie die Datei **Views/Shared/_Layout.cshtml** (das Standardlayout für die ASP.NET-App).  Ersetzen Sie den Inhalt durch Folgendes, und speichern Sie anschließend Ihre Änderungen:


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Aktualisieren der Datei „VotingWeb.cs“

Öffnen Sie die Datei *VotingWeb.cs* (erstellt den ASP.NET Core-Webhost innerhalb des zustandslosen Diensts unter Verwendung des WebListener-Webservers).

Fügen Sie am Anfang der Datei die Direktive `using System.Net.Http;` hinzu.

Ersetzen Sie die Funktion `CreateServiceInstanceListeners()` durch den folgenden Code, und speichern Sie anschließend Ihre Änderungen.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Fügen Sie außerdem die folgende `GetVotingDataServiceName`-Methode unter `CreateServiceInstanceListeners()` hinzu, und speichern Sie anschließend Ihre Änderungen. Wenn `GetVotingDataServiceName` abgerufen wird, wird der Name des Diensts zurückgegeben.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Hinzufügen der Datei „VotesController.cs“

Fügen Sie einen Controller zum Definieren von Abstimmungsaktionen hinzu. Klicken Sie mit der rechten Maustaste auf den Ordner **Controllers**, und wählen Sie **Hinzufügen > Neues Element > Visual C# > ASP.NET Core > Klasse** aus. Nennen Sie die Datei **VotesController.cs**, und klicken Sie auf **Hinzufügen**.  

Ersetzen Sie den Inhalt der Datei *VotesController.cs* durch Folgendes, und speichern Sie anschließend Ihre Änderungen.  Unter [Aktualisieren der Datei „VotesController.cs“](#updatevotecontroller_anchor) wird diese Datei dann für das Lesen und Schreiben von Abstimmungsdaten aus dem Back-End-Dienst angepasst.  Vorläufig gibt der Controller statische Zeichenfolgendaten an die Ansicht zurück.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Konfigurieren des Lauschports

Beim Erstellen des VotingWeb Front-End-Diensts wählt Visual Studio nach dem Zufallsprinzip einen Port aus, an dem der Dienst lauscht.  Der VotingWeb-Dienst fungiert als Front-End für diese Anwendung und akzeptiert externen Datenverkehr. Daher binden wir diesen Dienst an einen festen und bekannten Port.  Mit dem [Dienstmanifest](service-fabric-application-and-service-manifests.md) werden die Dienstendpunkte deklariert.

Öffnen Sie *VotingWeb/PackageRoot/ServiceManifest.xml* im Projektmappen-Explorer.  Navigieren Sie im Abschnitt **Ressourcen** zum Element **Endpunkt**, und legen Sie den Wert für **Port** auf **8080** fest. Wenn Sie die Anwendung lokal bereitstellen und ausführen möchten, muss der Anwendungslauschport auf dem Computer geöffnet und verfügbar sein.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

Aktualisieren Sie außerdem den Wert der Eigenschaft „Anwendungs-URL“ im Projekt „Voting“, damit ein Webbrowser beim Debuggen der Anwendung mit dem korrekten Port geöffnet wird.  Wählen Sie im Projektmappen-Explorer das Projekt **Voting** aus, und ändern Sie die Eigenschaft **Anwendungs-URL** in **8080**.

### <a name="deploy-and-run-the-voting-application-locally"></a>Lokales Bereitstellen und Ausführen der Anwendung „Voting“
Sie können die Anwendung „Voting“ jetzt zum Debuggen ausführen. Drücken Sie in Visual Studio **F5**, um die Anwendung für Ihren lokalen Service Fabric-Cluster im Debugmodus bereitzustellen. Die Anwendung schlägt fehl, wenn Sie Visual Studio zuvor nicht als **Administrator** geöffnet haben.

> [!NOTE]
> Wenn Sie die Anwendung zum ersten Mal lokal ausführen und bereitstellen, erstellt Visual Studio einen lokalen Service Fabric-Cluster für das Debuggen.  Die Clustererstellung kann etwas dauern. Der Status der Clustererstellung wird im Ausgabefenster von Visual Studio angezeigt.

Nachdem die Voting-Anwendung für Ihren lokalen Service Fabric-Cluster bereitgestellt wurde, wird Ihre Web-App automatisch in einer Browserregisterkarte geöffnet, und sollte wie folgt aussehen:

![ASP.NET Core-Front-End](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Zum Beenden des Debuggens der Anwendung wechseln Sie zurück zu Visual Studio, und drücken Sie **UMSCHALT+F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Hinzufügen eines zustandsbehafteten Back-End-Diensts zur Anwendung

Nachdem in der Anwendung nun ein ASP.NET-Web-API-Dienst ausgeführt wird, können Sie einen zustandsbehafteten zuverlässigen Dienst hinzufügen, um einige Daten in der Anwendung zu speichern.

Service Fabric ermöglicht eine konsistente und zuverlässige Speicherung von Daten direkt innerhalb des Diensts anhand von Reliable Collections. Bei Reliable Collections handelt es sich um eine Gruppe hochverfügbarer und zuverlässiger Sammlungsklassen, die jedem vertraut sind, der bereits mit C#-Auflistungen gearbeitet hat.

In diesem Tutorial erstellen Sie einen Dienst, der einen Zählerwert in einer Reliable Collection speichert.

1. Klicken Sie im Projektmappen-Explorer im Anwendungsprojekt „Voting“ mit der rechten Maustaste auf **Dienste**, und wählen Sie **Hinzufügen > Neuer Service Fabric-Dienst** aus.
    
2. Wählen Sie im Dialogfeld **Neuer Service Fabric-Dienst** die Option **Zustandsbehaftetes ASP.NET Core** aus, nennen Sie den Dienst **VotingData**, und klicken Sie dann auf **OK**.

    Nach Erstellung Ihres Dienstprojekts stehen Ihnen zwei Dienste in Ihrer Anwendung zur Verfügung. Bei der weiteren Erstellung Ihrer Anwendung können Sie weitere Dienste auf die gleiche Weise hinzufügen. Diese können unabhängig versioniert und aktualisiert werden.

3. Die nächste Seite umfasst eine Reihe von ASP.NET Core-Projektvorlagen. Wählen Sie für dieses Tutorial **API** aus.

    Visual Studio erstellt ein VotingData-Dienstprojekt und zeigt es im Projektmappen-Explorer an.

    ![Projektmappen-Explorer](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Hinzufügen der Datei „VoteDataController.cs“

Klicken Sie im Projekt **VotingData** mit der rechten Maustaste auf den Ordner **Controllers**, und wählen Sie **Hinzufügen > Neues Element > Klasse** aus. Nennen Sie die Datei **VoteDataController.cs**, und klicken Sie auf **Hinzufügen**. Ersetzen Sie den Inhalt der Datei durch Folgendes, und speichern Sie anschließend Ihre Änderungen:

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>Verbinden der Dienste

Im nächsten Schritt verbinden Sie die beiden Dienste miteinander und sorgen dafür, dass die Front-End-Webanwendung Abstimmungsinformationen aus dem Back-End-Dienst abruft und festlegt.

Service Fabric bietet absolute Flexibilität bei der Kommunikation mit Reliable Services. Sie können in einer Anwendung über Dienste verfügen, auf die über TCP zugegriffen werden kann. Auf andere Dienste wird möglicherweise über eine HTTP-REST-API zugegriffen, und bei wieder anderen Diensten erfolgt der Zugriff über Websockets. Ausführliche Informationen zu den verfügbaren Optionen und deren Vor- und Nachteilen finden Sie unter [Kommunizieren mit Diensten](service-fabric-connect-and-communicate-with-services.md).

In diesem Tutorial verwenden Sie die [ASP.NET Core-Web-API](service-fabric-reliable-services-communication-aspnetcore.md) und den [Service Fabric-Reverseproxy](service-fabric-reverseproxy.md), damit der Front-End-Webdienst „VotingWeb“ mit dem Back-End-Datendienst „VotingData“ kommunizieren kann. Der Reverseproxy ist standardmäßig zur Verwendung des Ports 19081 konfiguriert und sollte für dieses Tutorial funktionieren. Der Port des Reverseproxys ist in der Azure Resource Manager-Vorlage zum Einrichten des Clusters festgelegt. Welcher Port verwendet wird, geht aus der Cluster-Vorlage in der Ressource **Microsoft.ServiceFabric/clusters** hervor: 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
Um den in Ihrem lokalen Entwicklungscluster verwendeten Port des Reverseproxys zu ermitteln, sehen Sie sich das **HttpApplicationGatewayEndpoint**-Element im lokalen Service Fabric-Clustermanifest an:
1. Öffnen Sie ein Browserfenster, und navigieren Sie zu http:\//localhost:19080, um das Tool Service Fabric Explorer zu öffnen.
2. Wählen Sie **Cluster > Manifest** aus.
3. Notieren Sie sich den Port des Elements „HttpApplicationGatewayEndpoint“. Dieser sollte standardmäßig 19081 sein. Wenn es nicht 19081 ist, müssen Sie den Port in der Methode „GetProxyAddress“ im folgenden Code in der Datei „VotesController.cs“ ändern.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Aktualisieren der Datei „VotesController.cs“

Öffnen Sie im Projekt **VotingWeb** die Datei *Controllers/VotesController.cs*.  Ersetzen Sie den Inhalt der Definition der Klasse `VotesController` durch Folgendes, und speichern Sie anschließend Ihre Änderungen: Wenn der Reverseproxyport, den Sie im vorherigen Schritt ermittelt haben, nicht 19081 ist, ändern Sie den Port in der Methode „GetProxyAddress“ verwendeten Port von 19081 in den ermittelten Port.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Durchlaufen der Beispielanwendung für die Abstimmung

Die Abstimmungsanwendung besteht aus zwei Diensten:

* Web-Front-End-Dienst (VotingWeb): Ein ASP.NET Core-Web-Front-End-Dienst, der die Webseite bereitstellt und Web-APIs für die Kommunikation mit dem Back-End-Dienst verfügbar macht.
* Back-End-Dienst (VotingData): Ein ASP.NET Core-Web-Dienst, der eine API verfügbar macht, um die Abstimmungsergebnisse in einem zuverlässigen Wörterbuch zu speichern, das dauerhaft auf dem Datenträger vorhanden ist.

![Anwendungsdiagramm](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Beim Abstimmen in der Anwendung treten die folgenden Ereignisse ein:

1. Ein JavaScript sendet die Abstimmungsanforderung als HTTP PUT-Anforderung an die Web-API im Web-Front-End-Dienst.

2. Der Web-Front-End-Dienst nutzt einen Proxy, um eine HTTP PUT-Anforderung zu lokalisieren und an den Back-End-Dienst weiterzuleiten.

3. Der Back-End-Dienst erhält die eingehende Anforderung und speichert das aktualisierte Ergebnis in einem zuverlässigen Wörterbuch, das auf mehreren Knoten im Cluster repliziert und dauerhaft auf dem Datenträger gespeichert wird. Alle Daten der Anwendung werden im Cluster gespeichert, sodass keine Datenbank erforderlich ist.

## <a name="debug-in-visual-studio"></a>Debuggen in Visual Studio

Beim Debuggen der Anwendung in Visual Studio verwenden Sie einen lokalen Service Fabric-Entwicklungscluster. Sie haben die Möglichkeit, Ihre Oberfläche für das Debuggen an Ihr Szenario anzupassen. In dieser Anwendung speichern Sie Daten im Back-End-Dienst, indem Sie ein zuverlässiges Wörterbuch verwenden. Standardmäßig wird die Anwendung von Visual Studio entfernt, wenn Sie den Debugger beenden. Die Entfernung der Anwendung führt dazu, dass auch die Daten im Back-End-Dienst entfernt werden. Um die Daten zwischen den Debugsitzungen beizubehalten, können Sie den **Debugmodus für die Anwendung** als Eigenschaft im Projekt **Voting** in Visual Studio ändern.

Führen Sie die folgenden Schritte aus, um zu ermitteln, was im Code passiert:

1. Öffnen Sie die Datei **VotingWeb\VotesController.cs**, und legen Sie in der **Put**-Methode der Web-API (Zeile 72) einen Haltepunkt fest.

2. Öffnen Sie die Datei **VotingData\VoteDataController.cs**, und legen Sie in der **Put**-Methode der Web-API (Zeile 54) einen Haltepunkt fest.

3. Drücken Sie **F5**, um die Anwendung im Debugmodus auszuführen.

4. Wechseln Sie zurück in den Browser, und klicken Sie auf eine Abstimmungsoption, oder fügen Sie eine neue Abstimmungsoption hinzu. Sie stoßen im API-Controller des Web-Front-Ends auf den ersten Breakpoint.
    

   1. An diesem Punkt sendet das JavaScript im Browser eine Anforderung an den Web-API-Controller im Front-End-Dienst.

      ![Front-End-Dienst „Stimme hinzufügen“](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. Erstellen Sie zunächst die URL zum Reverseproxy für den Back-End-Dienst **(1)** .
   3. Senden Sie anschließend die HTTP PUT-Anforderung an den Reverseproxy **(2)** .
   4. Zum Schluss wird die Antwort vom Back-End-Dienst an den Client **(3)** zurückgegeben.

5. Drücken Sie **F5**, um fortzufahren.
   1. Sie befinden sich jetzt am Breakpoint im Back-End-Dienst.

      ![Back-End-Dienst „Stimme hinzufügen“](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. Verwenden Sie in der ersten Zeile der Methode **(1)** das `StateManager`-Element, um ein zuverlässiges Wörterbuch mit dem Namen `counts` abzurufen bzw. hinzuzufügen.
   3. Für alle Interaktionen mit Werten in einem zuverlässigen Wörterbuch ist eine Transaktion erforderlich. Diese Transaktion wird mithilfe der Anweisung **(2)** erstellt.
   4. Aktualisieren Sie in der Transaktion den Wert des relevanten Schlüssels für die Abstimmungsoption, und committen Sie den Vorgang **(3)** . Nachdem die Rückgabe für die Commit-Methode durchgeführt wurde, werden die Daten im Wörterbuch aktualisiert und auf anderen Knoten im Cluster repliziert. Die Daten sind jetzt sicher im Cluster gespeichert, und der Back-End-Dienst kann das Failover auf andere Knoten durchführen, während die Daten weiterhin verfügbar sind.
6. Drücken Sie **F5**, um fortzufahren.

Drücken Sie **UMSCHALT+F5**, um die Debugsitzung zu beenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines ASP.NET Core-Web-API-Diensts als zustandsbehafteter zuverlässiger Dienst
> * Erstellen eines ASP.NET Core-Webanwendungsdiensts als zustandsloser Webdienst
> * Kommunizieren mit dem zustandsbehafteten Dienst unter Verwendung des Reverseproxys

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Bereitstellen der Anwendung für Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
