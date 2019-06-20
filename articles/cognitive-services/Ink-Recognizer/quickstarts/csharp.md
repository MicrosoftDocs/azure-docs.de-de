---
title: 'Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und C#'
description: Verwenden Sie die Freihanderkennungs-REST-API, um Freihandstriche zu erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: d661d6eca6e4916946944c48cc2e5411aeaf8f14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060992"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und C#

In diesem Schnellstart werden die ersten Schritte zum Senden von Freihandstrichen an die Freihanderkennungs-API erläutert. Diese C#-Anwendung sendet eine API-Anforderung, die JSON-formatierte Freihandstrichdaten enthält, und erhält die Antwort.

Die Anwendung ist zwar in C# geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

Normalerweise würden Sie die API über eine App für Freihandeingabe aufrufen. In diesem Schnellstart werden Freihandstrichdaten für das folgende handschriftliche Beispiel aus einer JSON-Datei gesendet.

![Abbildung, die handschriftlichen Text zeigt](../media/handwriting-sample.jpg)

Den Quellcode für diese Schnellstartanleitung finden Sie auf [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Voraussetzungen

- Eine beliebige [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)-Edition.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - So installieren Sie Newtonsoft.Json als NuGet-Paket in Visual Studio
        1. Klicken Sie im **Projektmappen-Manager** mit der rechten Maustaste.
        2. Klicken Sie auf **Manage NuGet Packages**
        3. Suchen Sie nach `Newtonsoft.Json`, und installieren Sie das Paket.
- Unter Linux/MacOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden.

- Die Beispiel-Freihandstrichdaten für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Erstellen einer neuen Anwendung

1. Erstellen Sie in Visual Studio eine neue Konsolenprojektmappe, und fügen Sie die folgenden Pakete hinzu. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel und Ihren Endpunkt. Den folgenden URI können Sie für die Freihanderkennung verwenden. Er wird später dem Dienstendpunkt angefügt, um die URL für API-Anforderungen zu erstellen.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Erstellen einer Funktion zum Senden von Anforderungen

1. Erstellen Sie eine neuen asynchronen Funktion namens `Request`, die die oben erstellten Variablen verwendet.

2. Legen Sie das Sicherheitsprotokoll und die Headerinformationen des Clients mithilfe eines `HttpClient`-Objekts fest. Achten Sie darauf, dass Sie dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzufügen. Erstellen Sie dann ein `StringContent`-Objekt für die Anforderung.
 
3. Senden Sie die Anforderung mit `PutAsync()`. Wenn die Anforderung erfolgreich ist, geben Sie die Antwort zurück.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Senden einer Freihanderkennungsanforderung

1. Erstellen Sie eine neue Funktion namens `recognizeInk()`. Erstellen Sie die Anforderung und senden Sie sie, indem Sie die Funktion `Request()` mit Ihrem Endpunkt, Ihrem Abonnementschlüssel, der URL für die API und den digitalen Freihandstrichdaten aufrufen.

2. Deserialisieren Sie das JSON-Objekt, und schreiben Sie es in die Konsole. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Laden der digitalen Freihanddaten

Erstellen Sie eine Funktion namens `LoadJson()`, um die Freihanddaten-JSON-Datei zu laden. Verwenden Sie ein `StreamReader`- und ein `JsonTextReader`-Element zum Erstellen eines `JObject`-Objekts, und geben Sie es zurück.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Senden der API-Anforderung

1. Laden Sie in der Hauptmethode Ihrer Anwendung Ihre JSON-Daten mit der oben erstellten Funktion. 

2. Rufen Sie die oben erstellte `recognizeInk()`-Funktion auf. Verwenden Sie `System.Console.ReadKey()`, damit das Konsolenfenster nach dem Ausführen der Anwendung geöffnet bleibt.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Ausführen der Anwendung und Anzeigen der Antwort

Führen Sie die Anwendung aus. Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Sie finden die JSON-Antwort auch auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz](https://go.microsoft.com/fwlink/?linkid=2089907)


Sehen Sie sich in den folgenden Beispielanwendungen auf GitHub an, wie die Freihanderkennungs-API in einer Freihandschriftinhalte-App funktioniert:
* [C# und universelle Windows-Plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# und Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-Webbrowser-App](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobile Java- und Android-App](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobile Swift- und iOS-App](https://go.microsoft.com/fwlink/?linkid=2089805)
