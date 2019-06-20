---
title: 'Tutorial: Herstellen einer Verbindung mit dem Textanalysedienst mithilfe von „Verbundene Dienste“ in Visual Studio'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie über eine ASP.NET Core-Webanwendung eine Verbindung mit der Textanalyse herstellen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 75228b8c939cb5b8dd04471662ba86b46cfc808c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860474"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>Tutorial: Herstellen einer Verbindung mit dem Textanalysedienst mithilfe von „Verbundene Dienste“ in Visual Studio

Mit dem Textanalysedienst können Sie umfassende Informationen extrahieren, um visuelle Daten zu kategorisieren und zu verarbeiten. Zudem können Sie die computergestützte Moderation von Bildern verwenden, um Ihre Dienste zu kuratieren.

Dieser Artikel und die Begleitartikel enthalten Details zur Verwendung des Features „Verbundener Visual Studio-Dienst“ für den Textanalysedienst. Die Funktion ist in Visual Studio 2019 oder höher verfügbar, wenn die Cognitive Services-Erweiterung installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/)registrieren.
- Visual Studio 2019 mit installierter Workload für Webentwicklung. [Jetzt herunterladen](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Hinzufügen von Unterstützung für den Textanalysedienst zu Ihrem Projekt

1. Erstellen Sie ein neues ASP.NET Core-Webprojekt mit dem Namen „TextAnalyticsDemo“. Verwenden Sie die Projektvorlage „Webanwendung (Model-View-Controller)“ mit allen Standardeinstellungen. Sie müssen das Projekt „MyWebApplication“ nennen, damit der Namespace beim Kopieren von Code in das Projekt übereinstimmt.  Für das Beispiel in diesem Artikel wird MVC verwendet, Sie können den verbundenen Textanalysedienst jedoch mit jedem ASP.NET-Projekttyp nutzen.

1. Doppelklicken Sie im **Projektmappen-Explorer** auf das Element **Verbundener Dienst**.
   Die Seite „Verbundener Dienst“ wird mit den Diensten angezeigt, die Sie dem Projekt hinzufügen können.

   ![Screenshot von „Verbundener Dienst“ im Projektmappen-Explorer](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. Wählen Sie im Menü der verfügbaren Dienste **Evaluate Sentiment with Text Analytics** aus.

   ![Screenshot des Bildschirms „Verbundene Dienste“](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Wenn Sie sich bei Visual Studio angemeldet haben und Ihrem Konto ein Azure-Abonnement zugeordnet ist, wird eine Seite mit einer Dropdownliste mit Ihren Abonnements angezeigt.

   ![Screenshot des Bildschirms des verbundenen Textanalysediensts](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Wählen Sie das zu verwendende Abonnement aus, und wählen Sie einen Namen für den Textanalysedienst aus, oder wählen Sie den Link **Bearbeiten** aus, um den automatisch generierten Namen zu ändern. Wählen Sie dann die Ressourcengruppe und den Tarif aus.

   ![Screenshot der Felder für Ressourcengruppe und Tarif](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Folgen Sie dem Link zu den Details für Tarife.

1. Wählen Sie **Hinzufügen** aus, um Unterstützung für den verbundenen Dienst hinzufügen.
   Visual Studio ändert das Projekt, um die NuGet-Pakete, Konfigurationsdateieinträge und sonstigen Änderungen zur Unterstützung einer Verbindung mit dem Textanalysedienst hinzuzufügen. Im **Ausgabefenster** wird das Protokoll der ausgeführten Aktionen für Ihr Projekt angezeigt. Folgendes sollte angezeigt werden:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Verwenden Sie den Textanalysedienst, um die Sprache eines Textbeispiels zu erkennen.

1. Fügen Sie die folgenden using-Anweisungen in „Startup.cs“ hinzu.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Fügen Sie ein Konfigurationsfeld hinzu, und fügen Sie einen Konstruktor hinzu, der das Konfigurationsfeld in der Startup-Klasse initialisiert, um die Konfiguration in Ihrem Programm zu aktivieren.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Fügen Sie im Ordner „Controller“ eine Klassendatei namens „DemoTextAnalyzeController“ hinzu, und ersetzen Sie deren Inhalt durch den folgenden Code:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    Der Code enthält „GetTextAnalyzeClient“ zum Abrufen des Clientobjekts, mit dem Sie die Textanalyse-API aufrufen können. Zudem enthält er einen Anforderungshandler, der „DetectLanguage“ für einen angegebenen Text aufruft.

1. Fügen Sie die Hilfsklasse „MyHandler“ hinzu, die vom obigen Code verwendet wird.

    ```csharp
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

1. Fügen Sie im Ordner „Modelle“ eine Klasse für das Modell hinzu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Fügen Sie eine Ansicht hinzu, um den analysierten Text, die ermittelte Sprache und die Bewertung, die den Zuverlässigkeitsgrad der Analyse darstellt, anzuzeigen. Klicken Sie dazu mit der rechten Maustaste auf den Ordner **Ansichten**, wählen Sie **Hinzufügen** aus, klicken Sie dann auf **Ansicht**. Geben Sie im daraufhin angezeigten Dialogfeld den Namen _TextAnalyzeResult_ ein, übernehmen Sie die Standardeinstellungen zum Hinzufügen einer neuen Datei namens _TextAnalyzeResult.cshtml_ in den Ordner **Ansichten**, und kopieren Sie den folgenden Inhalt hinein:
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Erstellen Sie das Beispiel, und führen Sie es lokal aus. Geben Sie Text ein, und finden Sie heraus, welche Sprache die Textanalyse erkennt.
   
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, wenn Sie sie nicht mehr benötigen. Dadurch werden der Cognitive Service und die zugehörigen Ressourcen gelöscht. So löschen Sie die Ressourcengruppe über das Portal:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das Suchfeld am oberen Rand des Portals ein. Klicken Sie in den Suchergebnissen auf die Ressourcengruppe aus diesem Tutorial.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie im Feld **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen der Ressourcengruppe ein, und klicken Sie anschließend auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den Textanalysedienst, indem Sie die [Dokumentation zum Textanalysedienst](index.yml) lesen.
