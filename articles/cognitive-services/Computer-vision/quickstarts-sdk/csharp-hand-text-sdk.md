---
title: 'Schnellstart: Extrahieren von handschriftlichem Text – SDK, C#'
titleSuffix: Azure Cognitive Services
description: In diesem Schnellstart extrahieren Sie Text aus einem Bild, indem Sie die Windows-Clientbibliothek der Maschinelles Sehen-API verwenden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 656e2519dc814baffa2f1c427d46e66054969e25
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357168"
---
# <a name="quickstart-extract-handwritten-text-using-the-computer-vision-c-sdk"></a>Schnellstart: Extrahieren von handschriftlichem Text mit dem C# SDK für maschinelles Sehen

In dieser Schnellstartanleitung extrahieren Sie mit dem SDK für maschinelles Sehen für C# handschriftlichen oder gedruckten Text aus einem Bild. Auf Wunsch können Sie den Code in dieser Anleitung als vollständige Beispiel-App aus dem GitHub-Repository [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Schlüssel für ein Maschinelles Sehen-Abonnement. Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) können Sie einen Schlüssel für eine kostenlose Testversion abrufen. Oder gehen Sie wie unter [Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beschrieben vor, um „Maschinelles Sehen“ zu abonnieren und Ihren Schlüssel zu erhalten.
* Eine beliebige Edition von [Visual Studio 2015 oder 2017](https://www.visualstudio.com/downloads/).
* Das NuGet-Paket [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) mit der Clientbibliothek. Es ist nicht erforderlich, das Paket herunterzuladen. Unten ist die Installationsanleitung angegeben.

## <a name="create-and-run-the-sample-app"></a>Erstellen und Ausführen der Beispiel-App

Führen Sie zum Ausführen des Beispiels die folgenden Schritte aus:

1. Erstellen Sie in Visual Studio eine neue Visual C#-Konsolen-App.
1. Installieren Sie das NuGet-Paket mit der Clientbibliothek der Maschinelles Sehen-API.
    1. Klicken Sie im Menü auf **Werkzeuge**, wählen Sie **NuGet-Paket-Manager** und dann **NuGet-Pakete für Projektmappe verwalten** aus.
    1. Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie „Microsoft.Azure.CognitiveServices.Vision.ComputerVision“ in das Feld **Suchen** ein.
    1. Wählen Sie **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** aus, wenn das Paket angezeigt wird, aktivieren Sie das Kontrollkästchen neben dem Projektnamen, und klicken Sie dann auf **Installieren**.
1. Ersetzen Sie `Program.cs` durch den folgenden Code. Die Methoden `BatchReadFileAsync` und `BatchReadFileInStreamAsync` umschließen die [Batch Read-API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) für lokale Bilder bzw. Remotebilder. Die `GetReadOperationResultAsync`-Methode umschließt die [Get Read Operation Result-API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d).

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ExtractText
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<Subscription key>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

            private const int numberOfCharsInOperationId = 36;

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the westcentralus
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
                var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Read text from a remote image
            private static async Task ExtractRemoteTextAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                // Start the async process to read the text
                BatchReadFileHeaders textHeaders =
                    await computerVision.BatchReadFileAsync(
                        imageUrl);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }

            // Recognize text from a local image
            private static async Task ExtractLocalTextAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    // Start the async process to recognize the text
                    BatchReadFileInStreamHeaders textHeaders =
                        await computerVision.BatchReadFileInStreamAsync(
                            imageStream);

                    await GetTextAsync(computerVision, textHeaders.OperationLocation);
                }
            }

            // Retrieve the recognized text
            private static async Task GetTextAsync(
                ComputerVisionClient computerVision, string operationLocation)
            {
                // Retrieve the URI where the recognized text will be
                // stored from the Operation-Location header
                string operationId = operationLocation.Substring(
                    operationLocation.Length - numberOfCharsInOperationId);

                Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
                ReadOperationResult result =
                    await computerVision.GetReadOperationResultAsync(operationId);

                // Wait for the operation to complete
                int i = 0;
                int maxRetries = 10;
                while ((result.Status == TextOperationStatusCodes.Running ||
                        result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
                {
                    Console.WriteLine(
                        "Server status: {0}, waiting {1} seconds...", result.Status, i);
                    await Task.Delay(1000);

                    result = await computerVision.GetReadOperationResultAsync(operationId);
                }

                // Display the results
                Console.WriteLine();
                var recResults = result.RecognitionResults;
                foreach (TextRecognitionResult recResult in recResults)
                {
                    foreach (Line line in recResult.Lines)
                    {
                        Console.WriteLine(line.Text);
                    }
                }
                Console.WriteLine();
            }
        }
    }
    ```

1. Ersetzen Sie `<Subscription Key>` durch Ihren gültigen Abonnementschlüssel.
1. Ändern Sie `computerVision.Endpoint` bei Bedarf in die Azure-Region, die Ihren Abonnementschlüsseln zugeordnet ist, wenn erforderlich.
1. Ersetzen Sie `<LocalImage>` durch den Pfad und Dateinamen eines lokalen Bilds.
1. Legen Sie optional `remoteImageUrl` auf ein anderes Bild fest.
1. Führen Sie das Programm aus.

## <a name="examine-the-response"></a>Untersuchen der Antwort

Bei einer erfolgreichen Antwort werden für jedes Bild die Zeilen mit dem erkannten Text ausgegeben.

```console
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

Weitere Informationen finden Sie unter [Schnellstart: Extrahieren von handschriftlichem Text – REST, C#](../QuickStarts/CSharp-hand-text.md#examine-the-response) finden Sie ein Beispiel für eine unformatierte JSON-Ausgabe des API-Aufrufs.

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie die Maschinelles Sehen-APIs, die zum Analysieren von Bildern, Erkennen von Prominenten und Sehenswürdigkeiten, Erstellen von Miniaturansichten und Extrahieren von gedrucktem sowie handschriftlichem Text verwendet werden.

> [!div class="nextstepaction"]
> [Maschinelles Sehen-APIs erkunden](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
