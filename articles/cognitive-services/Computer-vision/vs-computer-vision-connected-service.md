---
title: 'Visual Studio: Verbundener Dienst – Maschinelles Sehen-API'
titleSuffix: Azure Cognitive Services
description: Stellen Sie für eine ASP.NET Core-Webanwendung eine Verbindung mit der Maschinelles Sehen-API her, indem Sie das Visual Studio-Feature „Verbundener Dienst“ verwenden.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: 24d9a5fa1e9c2f44ef32ac1fc05ad09f8a550f12
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827755"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Verwenden von verbundenen Diensten in Visual Studio zum Herstellen einer Verbindung mit der Maschinelles Sehen-API

Dieser Artikel und die Begleitartikel enthalten Details zur Verwendung des Features „Verbundener Visual Studio-Dienst“ für die Maschinelles Sehen-API von Cognitive Services. Die Funktion ist in Visual Studio 2017 15.7 oder höher verfügbar, wenn die Cognitive Services-Erweiterung installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/)registrieren.
- Eine Installation von Visual Studio 2017 (Version 15.7 oder höher) mit der Workload für **Webentwicklung**. [Jetzt herunterladen](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Hinzufügen von Unterstützung für die Maschinelles Sehen-API von Cognitive Services zu Ihrem Projekt

1. Erstellen Sie ein ASP.NET Core-Webprojekt. Verwenden Sie eine leere Projektvorlage. 

1. Wählen Sie im **Projektmappen-Explorer** die Option **Hinzufügen** > **Verbundener Dienst** aus.
   Die Seite „Verbundener Dienst“ wird mit den Diensten angezeigt, die Sie dem Projekt hinzufügen können.

   ![Kontextmenü in einem Visual Studio-Projekt: Hinzufügen > Verbundener Dienst](../media/vs-common/Connected-Service-Menu.PNG)

1. Wählen Sie im Menü der verfügbaren Dienste die Option **Maschinelles Sehen-API von Cognitive Services** aus.

   ![Menü „Verbundene Dienste“: Hervorhebung von „Analyze Images...“ (Analysieren von Bildern...)](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Wenn Sie sich bei Visual Studio angemeldet haben und Ihrem Konto ein Azure-Abonnement zugeordnet ist, wird eine Seite mit einer Dropdownliste mit Ihren Abonnements angezeigt.

   ![Fenster „Maschinelles Sehen-API“ mit Hervorhebung der Dropdownliste „Abonnement“](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Wählen Sie das zu verwendende Abonnement aus, und geben Sie einen Namen für die Maschinelles Sehen-API an, oder wählen Sie den Link „Bearbeiten“ aus, um den automatisch generierten Namen zu ändern. Wählen Sie dann die Ressourcengruppe und den Tarif aus.

   ![Bearbeiten der Details des verbundenen Diensts](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Folgen Sie dem Link zu den Details für Tarife.

1. Wählen Sie „Hinzufügen“ aus, um Unterstützung für den verbundenen Dienst hinzufügen.
   Visual Studio ändert das Projekt, um die NuGet-Pakete, Konfigurationsdateieinträge und sonstigen Änderungen zur Unterstützung einer Verbindung mit der Maschinelles Sehen-API hinzuzufügen. Im Ausgabefenster wird das Protokoll der ausgeführten Aktionen für Ihr Projekt angezeigt. Folgendes sollte angezeigt werden:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Verwende der Maschinelles Sehen-API zum Erkennen von Attributen eines Bilds

1. Fügen Sie in „Startup.cs“ die folgenden using-Anweisungen hinzu.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Fügen Sie ein Konfigurationsfeld hinzu, und fügen Sie einen Konstruktor hinzu, der das Konfigurationsfeld in der `Startup`-Klasse initialisiert, um die Konfiguration in Ihrem Programm zu aktivieren.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Fügen Sie im Ordner „wwwroot“ Ihres Projekts einen Ordner „images“ hinzu, und fügen Sie dem wwwroot-Ordner eine Bilddatei hinzu. Sie können beispielsweise eins der Bilder auf dieser Seite zur [Maschinelles Sehen-API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) verwenden. Klicken Sie mit der rechten Maustaste auf eins der Bilder, und speichern Sie es auf Ihrer lokalen Festplatte. Klicken Sie dann im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner „images“, und wählen Sie **Hinzufügen** > **Vorhandenes Element** aus, um das Bild zu Ihrem Projekt hinzuzufügen. Ihr Projekt sollte im Projektmappen-Explorer etwa wie folgt aussehen: 
  
   ![Screenshot: Projektmappen-Explorer mit ausgewählter Bilddatei](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Klicken Sie mit der rechten Maustaste auf die Bilddatei, und wählen Sie nacheinander „Eigenschaften“ und **Kopieren, wenn neuer** aus. 

   ![Fenster mit Bildeigenschaften; „In Ausgabeverzeichnis kopieren“ auf „Kopieren, wenn neuer“ festgelegt](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Ersetzen Sie die Configure-Methode durch den folgenden Code, um auf die Maschinelles Sehen-API zuzugreifen und ein Bild zu testen.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    Dieser Code erstellt eine HTTP-Anforderung mit dem URI und dem Bild als binären Inhalt für einen Aufruf der Maschinelles Sehen-REST-API.

1. Fügen Sie die Hilfsfunktionen „GetImageAsByteArray“ und „JsonPrettyPrint“ hinzu.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. Führen Sie die Webanwendung aus, und sehen Sie sich an, was die Maschinelles Sehen-API in Ihrem Bild gefunden hat.

   ![Bild und formatierte Ergebnisse der Maschinelles Sehen-API](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, wenn Sie sie nicht mehr benötigen. Dadurch werden der Cognitive Service und die zugehörigen Ressourcen gelöscht. So löschen Sie die Ressourcengruppe über das Portal:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das Suchfeld am oberen Rand des Portals ein. Klicken Sie in den Suchergebnissen auf die Ressourcengruppe aus dieser Schnellstartanleitung.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie im Feld **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen der Ressourcengruppe ein, und klicken Sie anschließend auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [Dokumentation zur Maschinelles Sehen-API](Home.md), um mehr über diese API zu erfahren.
