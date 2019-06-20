---
title: Verwenden des Endpunkts der Vorhersage für das programmgesteuerte Testen von Bildern mit einer Klassifizierung – Custom Vision
titlesuffix: Azure Cognitive Services
description: API-Nutzung für das programmgesteuerte Testen von Bildern mit Ihrer Custom Vision Service-Klassifizierung
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816736"
---
# <a name="use-your-model-with-the-prediction-api"></a>Verwenden des Modells mit der Vorhersage-API

Nachdem Sie Ihr Modell trainiert haben, können Sie Bilder programmgesteuert testen, indem Sie sie an den Endpunkt der Vorhersage-API senden.

> [!NOTE]
> Dieser Artikel veranschaulicht, wie Sie mit C# ein Bild an die Vorhersage-API senden. Weitere Informationen und Beispiele finden Sie in der [Vorhersage-API-Referenz](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Veröffentlichen der trainierten Iteration

Wählen Sie auf der [Custom Vision-Webseite](https://customvision.ai) Ihr Projekt und dann die Registerkarte __Leistung__ aus.

Um Bilder an die Vorhersage-API zu senden, müssen Sie zuerst Ihre Iteration für die Vorhersage veröffentlichen. Dazu wählen Sie __Veröffentlichen__ aus und geben einen Namen für die veröffentlichte Iteration an. Auf diese Weise wird Ihr Modell für die Vorhersage-API Ihrer Azure-Ressource von Custom Vision zugänglich.

![Die Registerkarte „Leistung“ wird mit einem roten Rechteck um die Schaltfläche „Veröffentlichen“ angezeigt.](./media/use-prediction-api/unpublished-iteration.png)

Sobald das Modell erfolgreich veröffentlicht wurde, wird neben der Iteration in der linken Seitenleiste die Bezeichnung „Veröffentlicht“ angezeigt. Ihr Name wird in der Beschreibung der Iteration angezeigt.

![Die Registerkarte „Leistung“ wird mit einem roten Rechteck um die Bezeichnung „Veröffentlicht“ und den Namen der veröffentlichten Iteration angezeigt.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Abrufen der Vorhersage-URL und des Vorhersage-Schlüssels

Nachdem das Modell veröffentlicht wurde, können Sie die erforderlichen Informationen abrufen, indem Sie __Vorhersage-URL__ auswählen. Dadurch wird ein Dialogfeld mit Informationen zur Verwendung der Vorhersage-API geöffnet, einschließlich __Vorhersage-URL__ und __Vorhersage-Schlüssel__.

![Die Registerkarte „Leistung“ wird mit einem roten Rechteck um die Schaltfläche „Vorhersage-URL“ angezeigt.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Die Registerkarte „Leistung“ wird mit einem roten Rechteck um den Wert „Vorhersage-URL“ für die Verwendung einer Bilddatei und um den Wert „Vorhersage-Schlüssel“ angezeigt.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Ihr __Vorhersage-Schlüssel__ befindet sich auch auf der Seite [Azure-Portal](https://portal.azure.com) für die Ihrem Projekt zugeordnete Azure-Ressource von Custom Vision unter dem Blatt __Schlüssel__.

In dieser Anleitung verwenden Sie ein lokales Bild, also kopieren Sie die URL unter **Wenn eine Bilddatei vorliegt** an einen temporären Speicherort. Kopieren Sie auch den entsprechenden __Vorhersage-Schlüssel__-Wert.

## <a name="create-the-application"></a>Erstellen der Anwendung

1. Erstellen Sie in Visual Studio eine neue C#-Konsolenanwendung.

1. Verwenden Sie den folgenden Code als Textkörper der Datei __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Ändern Sie die folgenden Angaben:
   * Legen Sie für das Feld `namespace` den Namen Ihres Projekts fest.
   * Ersetzen Sie den Platzhalter `<Your prediction key>` durch den Schlüsselwert, den Sie zuvor abgerufen haben.
   * Ersetzen Sie den Platzhalter `<Your prediction URL>` durch die URL, die Sie zuvor abgerufen haben.

## <a name="run-the-application"></a>Ausführen der Anwendung

Wenn Sie die Anwendung ausführen, werden Sie aufgefordert, einen Pfad zu einer Bilddatei in der Konsole einzugeben. Das Bild wird dann an die Vorhersage-API übergeben und die Ergebnisse der Vorhersage werden als Zeichenfolge im JSON-Format zurückgegeben. Hier sehen Sie eine Beispielantwort.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie erfahren, wie Sie Bilder an Ihren benutzerdefinierten Bildklassifizierer bzw. an die Bilderkennung senden und eine programmgesteuerte Antwort mit dem C# SDK erhalten. Als Nächstes erfahren Sie, wie Sie End-to-End-Szenarien mit C# abschließen oder die ersten Schritte mit einem SDK für eine andere Sprache durchführen können.

* [Schnellstart: .NET SDK](csharp-tutorial.md)
* [Schnellstart: Python SDK](python-tutorial.md)
* [Schnellstart: Java SDK](java-tutorial.md)
* [Schnellstart: Node SDK](node-tutorial.md)
* [Schnellstart: Go SDK](go-tutorial.md)
