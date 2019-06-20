---
title: 'Tutorial: Gesichtserkennungs-API, C#'
titleSuffix: Azure Cognitive Services
description: Erstellen Sie eine einfache Windows-App, die die Gesichtserkennungs-API von Cognitive Services verwendet, um Merkmale von Gesichtern in einem Bild zu erkennen.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: 492161a9a47627db8a06686daf953e99ca652e02
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827663"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Herstellen einer Verbindung mit der Gesichtserkennungs-API von Cognitive Services mithilfe der verbundenen Dienste in Visual Studio

Verwenden Sie die Gesichtserkennungs-API von Cognitive Services, um Gesichter in Fotos zu erkennen, zu analysieren, zu organisieren und zu mit Tags zu versehen.

Dieser Artikel und die Begleitartikel enthalten Details zur Verwendung des Features „Verbundener Visual Studio-Dienst“ für die Gesichtserkennungs-API von Cognitive Services. Die Funktion ist in Visual Studio 2017 15.7 oder höher verfügbar, wenn die Cognitive Services-Erweiterung installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/)registrieren.
- Eine Installation von Visual Studio 2017 (Version 15.7 oder höher) mit der Workload für **Webentwicklung**. [Jetzt herunterladen](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Erstellen eines Projekts und Hinzufügen von Unterstützung für die Gesichtserkennungs-API von Cognitive Services

1. Erstellen Sie ein ASP.NET Core-Webprojekt. Verwenden Sie eine leere Projektvorlage. 

1. Wählen Sie im **Projektmappen-Explorer** die Option **Hinzufügen** > **Verbundener Dienst** aus.
   Die Seite „Verbundener Dienst“ wird mit den Diensten angezeigt, die Sie dem Projekt hinzufügen können.

   ![Menüelement „Verbundenen Dienst hinzufügen“](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Wählen Sie im Menü der verfügbaren Dienste die Option **Gesichtserkennungs-API von Cognitive Services** aus.

   ![Auswählen des Diensts, mit dem eine Verbindung hergestellt werden soll](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Wenn Sie sich bei Visual Studio angemeldet haben und Ihrem Konto ein Azure-Abonnement zugeordnet ist, wird eine Seite mit einer Dropdownliste mit Ihren Abonnements angezeigt.

   ![Wählen Sie Ihr Abonnement aus.](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Wählen Sie das zu verwendende Abonnement aus, und geben Sie einen Namen für die Gesichtserkennungs-API an, oder wählen Sie den Link „Bearbeiten“ aus, um den automatisch generierten Namen zu ändern. Wählen Sie dann die Ressourcengruppe und den Tarif aus.

   ![Bearbeiten der Details des verbundenen Diensts](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Folgen Sie dem Link zu den Details für Tarife.

1. Wählen Sie „Hinzufügen“ aus, um Unterstützung für den verbundenen Dienst hinzufügen.
   Visual Studio ändert das Projekt, um die NuGet-Pakete, Konfigurationsdateieinträge und sonstigen Änderungen zur Unterstützung einer Verbindung mit der Gesichtserkennungs-API hinzuzufügen.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Verwenden der Gesichtserkennungs-API, um Attribute von Gesichtern in einem Bild zu erkennen

1. Fügen Sie in „Startup.cs“ die folgenden using-Anweisungen hinzu.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Fügen Sie ein Konfigurationsfeld und einen Konstruktor hinzu, der das Konfigurationsfeld in der Startup-Klasse initialisiert, um die Konfiguration in Ihrem Programm zu aktivieren.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Fügen Sie im Ordner „wwwroot“ Ihres Projekts einen Ordner „images“ hinzu, und fügen Sie dem wwwroot-Ordner eine Bilddatei hinzu. Sie können beispielsweise eins der Bilder auf dieser Seite zur [Gesichtserkennungs-API](https://azure.microsoft.com/services/cognitive-services/face/) verwenden. Klicken Sie mit der rechten Maustaste auf eins der Bilder, und speichern Sie es auf Ihrer lokalen Festplatte. Klicken Sie dann im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner „images“, und wählen Sie **Hinzufügen** > **Vorhandenes Element** aus, um das Bild zu Ihrem Projekt hinzuzufügen. Ihr Projekt sollte im Projektmappen-Explorer etwa wie folgt aussehen:
 
   ![images-Ordner mit Bilddatei](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Klicken Sie mit der rechten Maustaste auf die Bilddatei, und wählen Sie nacheinander „Eigenschaften“ und **Kopieren, wenn neuer** aus.

   ![Kopieren, wenn neuer](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Ersetzen Sie die Configure-Methode durch den folgenden Code, um auf die Gesichtserkennungs-API zuzugreifen und ein Bild zu testen. Ändern Sie die imagePath-Zeichenfolge in die richtigen Werte für Pfad und Dateiname des Bilds mit dem Gesicht.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    Der Code in diesem Schritt erstellt eine HTTP-Anforderung mit einem Aufruf der Gesichtserkennungs-REST-API und verwendet dabei den Schlüssel, den Sie beim Hinzufügen des verbundenen Diensts hinzugefügt haben.

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

1. Führen Sie die Webanwendung aus, und sehen Sie sich an, was die Gesichtserkennungs-API im Bild gefunden hat.
 
   ![Bild und formatierte Ergebnisse der Gesichtserkennungs-API](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, wenn Sie sie nicht mehr benötigen. Dadurch werden der Cognitive Service und die zugehörigen Ressourcen gelöscht. So löschen Sie die Ressourcengruppe über das Portal:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das Suchfeld am oberen Rand des Portals ein. Klicken Sie in den Suchergebnissen auf die Ressourcengruppe aus dieser Schnellstartanleitung.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Geben Sie im Feld **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen der Ressourcengruppe ein, und klicken Sie anschließend auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie in der [Dokumentation zur Gesichtserkennungs-API](Overview.md) mehr über diese API.
