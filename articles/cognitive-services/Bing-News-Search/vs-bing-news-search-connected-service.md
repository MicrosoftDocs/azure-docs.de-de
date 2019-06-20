---
title: Herstellen einer Verbindung mit der Bing-News-Suche-API mithilfe von verbundenen Diensten in Visual Studio und C#
titleSuffix: Azure Cognitive Services
description: Stellen Sie über eine ASP.NET Core-Webanwendung eine Verbindung mit der Bing-News-Suche her.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 6f697c91851e7ddc95f84239987bb4378bafd094
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798635"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>Tutorial: Herstellen einer Verbindung mit der Bing-News-Suche-API mithilfe von verbundenen Diensten in Visual Studio und C#

Indem Sie die Bing-News-Suche verwenden, können Sie Apps und Diensten die Nutzung einer leistungsstarken, werbefreien Suchmaschine für das gesamte Web ermöglichen. Die Bing-News-Suche ist einer der in Cognitive Services verfügbaren Suchdienste.

Dieser Artikel enthält Details zur Verwendung des Visual Studio-Features „Verbundener Dienst“ für die Bing-News-Suche. Die Funktion ist in Visual Studio 2017 15.7 oder höher verfügbar, wenn die Cognitive Services-Erweiterung installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/)registrieren.
- Eine Installation von Visual Studio 2017 (Version 15.7 oder höher) mit der Workload für Webentwicklung. [Jetzt herunterladen](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Hinzufügen der Unterstützung für die Bing-News-Suche-API zu Ihrem Projekt

1. Erstellen Sie ein neues ASP.NET Core-Webprojekt mit dem Namen „MyWebApplication“. Verwenden Sie die Projektvorlage **Webanwendung (Model-View-Controller)** mit allen Standardeinstellungen. Sie müssen das Projekt „MyWebApplication“ nennen, damit der Namespace übereinstimmt, wenn Sie Code in das Projekt kopieren. 

1. Wählen Sie im **Projektmappen-Explorer** die Option **Hinzufügen** > **Verbundener Dienst** aus.
   Die Seite „Verbundener Dienst“ wird mit den Diensten angezeigt, die Sie dem Projekt hinzufügen können.

   ![Screenshot des Menüelements „Verbundenen Dienst hinzufügen“](../media/vs-common/Connected-Service-Menu.PNG)

1. Wählen Sie im Menü der verfügbaren Dienste die Option **Ergänzen Sie Ihre Apps um intelligente Suchfunktionen** aus.

   ![Screenshot der Liste der verbundenen Dienste](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Wenn Sie sich bei Visual Studio angemeldet haben und Ihrem Konto ein Azure-Abonnement zugeordnet ist, wird eine Seite mit einer Dropdownliste mit Ihren Abonnements angezeigt. Wählen Sie das Abonnement aus, das Sie verwenden möchten, und geben Sie dann einen Namen für die Bing-News-Suche-API ein. Sie können auch auf **Bearbeiten** klicken, um den automatisch generierten Namen zu ändern.

   ![Screenshot der Felder für Abonnement und Name](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Wählen Sie die Ressourcengruppe und den Tarif aus.

   ![Screenshot der Felder für Ressourcengruppe und Tarif](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Wenn Sie weitere Details zu den Tarifen anzeigen möchten, wählen Sie **Tarife überprüfen** aus.

1. Wählen Sie **Hinzufügen** aus, um Unterstützung für den verbundenen Dienst hinzufügen.
   Visual Studio ändert das Projekt, um die NuGet-Pakete, Konfigurationsdateieinträge und sonstigen Änderungen zur Unterstützung einer Verbindung mit der Bing-News-Suche-API hinzuzufügen. Die Ausgabe zeigt das Protokoll der ausgeführten Aktionen für Ihr Projekt an. Folgendes sollte angezeigt werden:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Die Datei „appsettings.json“ enthält jetzt die folgenden neuen Einstellungen:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Verwenden der Bing-News-Suche-API zum Hinzufügen von Suchfunktionen zu einer Webseite

Nachdem Sie die Unterstützung für die Bing-News-Suche-API zu Ihrem Projekt hinzugefügt haben, können Sie die API jetzt verwenden, um einer Webseite intelligente Suchfunktionen hinzuzufügen.

1. Fügen Sie in der Datei *Startup.cs* in der Methode `ConfigureServices` einen Aufruf von `IServiceCollection.AddSingleton` ein. Dadurch wird ein Konfigurationsobjekt mit den Schlüsseleinstellungen erstellt, die für den Code in Ihrem Projekt verfügbar sind.
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. Fügen Sie im Ordner **Models** eine neue Klassendatei namens *BingNewsModel.cs* hinzu. Wenn Sie Ihr Projekt anders benannt haben, verwenden Sie statt MyWebApplication den Namespace Ihres Projekts. Ersetzen Sie den Inhalt durch den folgenden Code:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   In diesem Modell werden die Ergebnisse eines Aufrufs des Bing-News-Suche-Diensts gespeichert.
 
1. Fügen Sie im Ordner **Controllers** einen neue Klassendatei namens *IntelligentSearchController.cs* hinzu. Ersetzen Sie den Inhalt durch den folgenden Code:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   In diesem Code richtet der Konstruktor das Konfigurationsobjekt ein, das Ihre Schlüssel enthält. Die Methode für die `Search`-Route ist einfach nur eine Weiterleitung an die Funktion `BingSearchResult`. Diese ruft die Methode `GetNewsSearchClient` auf, um das Clientobjekt `NewsSearchAPI` abzurufen.  Das Clientobjekt `NewsSearchAPI` enthält die Methode `SearchAsync`, die den Dienst aufruft und die Ergebnisse in dem von Ihnen erstellten `SearchResult`-Modell zurückgibt. 

1. Fügen Sie die Klasse `MyHandler` hinzu, auf die im vorherigen Code verwiesen wurde. Diese delegiert den asynchronen Aufruf des Suchdiensts an ihre Basisklasse `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Um Unterstützung für die Übermittlung von Suchläufen und die Anzeige der Ergebnisse hinzuzufügen, erstellen Sie im Ordner **Views** einen neuen Ordner namens **IntelligentSearch**. Fügen Sie in diesem neuen Ordner die Ansicht *BingSearchResult.cshtml* hinzu. Kopieren Sie den folgenden Code:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Starten Sie die Webanwendung lokal, geben Sie die URL für die gerade erstellte Seite ein (/IntelligentSearch/BingSearchResult), und senden Sie mithilfe der Schaltfläche „Suchen“ eine Suchanforderung.

   ![Screenshot der Ergebnisse der Bing-News-Suche](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe nicht mehr benötigt wird, können Sie sie löschen. Dadurch werden der Cognitive Service und die zugehörigen Ressourcen gelöscht. So löschen Sie die Ressourcengruppe über das Portal:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das Suchfeld am oberen Rand des Portals ein. Wählen Sie die Ressourcengruppe aus, die Sie löschen möchten.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie im Feld **Geben Sie den Ressourcengruppennamen ein** den Namen der Ressourcengruppe ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Bing-News-Suche-API finden Sie unter [Was ist die Bing-News-Suche?](index.yml).
