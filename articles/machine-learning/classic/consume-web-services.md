---
title: 'ML Studio (Classic): Nutzen von Webdiensten – Azure'
description: Nachdem ein Machine Learning-Dienst über Azure Machine Learning Studio (klassisch) bereitgestellt wurde, kann der RESTFul-Webdienst als Anforderung/Antwort-Dienst in Echtzeit oder als Stapelausführungsdienst genutzt werden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: 0dc49265c0ea799e194e4ac7004b558d8a9d4dd8
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519268"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>Nutzen eines Webdiensts in Machine Learning Studio (Classic)

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Sobald Sie ein (klassisches) Azure Machine Learning Studio-Vorhersagemodell als Webdienst bereitstellen, können Sie eine REST-API verwenden, um Daten dorthin zu senden und Vorhersagen abzurufen. Sie können die Daten in Echtzeit oder im Batchmodus senden.

Weitere Informationen zum Erstellen und Bereitstellen eines Machine Learning-Webdiensts mit Machine Learning Studio (klassisch) finden Sie hier:

* Ein Tutorial zum Erstellen eines Experiments in Machine Learning Studio (klassisch) finden Sie unter [Erstellen Ihres ersten Experiments](create-experiment.md).
* Ausführliche Informationen zum Bereitstellen eines Webdiensts finden Sie unter [Bereitstellen eines Machine Learning-Webdiensts](deploy-a-machine-learning-web-service.md).
* Allgemeine Informationen zu Machine Learning finden Sie im [Machine Learning-Dokumentationscenter](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Übersicht
Mit dem Azure Machine Learning-Webdienst kommunizieren externe Anwendungen in Echtzeit mit einem Machine Learning-Workflow-Bewertungsmodell. Ein Machine Learning-Webdienstaufruf gibt Vorhersageergebnisse an eine externe Anwendung zurück. Zur Durchführung eines Machine Learning-Webdienstaufrufs übergeben Sie einen API-Schlüssel, der beim Bereitstellen einer Vorhersage erstellt wird. Der Machine Learning-Webdienst basiert auf REST, einer verbreiteten Architektur für Webprogrammierungsprojekte.

Azure Machine Learning Studio (klassisch) verfügt über zwei Arten von Diensten:

* Anforderung-/Antwort-Dienst (Request-Response Service, RRS): ein hochskalierbarer Webdienst mit niedriger Latenz, der eine Schnittstelle für zustandslose Modelle bereitstellt, die in Machine Learning Studio (klassisch) erstellt und bereitgestellt wurden.
* Batch Execution Service (BES) – ein asynchroner Dienst für die Bewertung eines Stapels für Datensätze.

Weitere Informationen zu Machine Learning-Webdiensten finden Sie unter [Bereitstellen eines Machine Learning-Webdiensts](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Abrufen eines Autorisierungsschlüssels
Wenn Sie Ihr Experiment bereitstellen, werden API-Schlüssel für den Webdienst erstellt. Sie können die Schlüssel von verschiedenen Stellen abrufen.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Im Azure Machine Learning Web Services-Portal
Melden Sie sich beim [Microsoft Azure Machine Learning Web Services-Portal](https://services.azureml.net) an.

So rufen Sie den API-Schlüssel für einen neuen Machine Learning-Webdienst ab

1. Klicken Sie im Azure Machine Learning Web Services-Portal im oberen Menü auf **Web Services**.
2. Klicken Sie auf den Webdienst, für den der Schlüssel abgerufen werden soll.
3. Klicken Sie im oberen Menü auf **Consume**.
4. Kopieren und speichern Sie den **Primary Key**.

So rufen Sie den API-Schlüssel für einen klassischen Machine Learning-Webdienst ab

1. Klicken Sie im Azure Machine Learning Web Services-Portal im oberen Menü auf **Classic Web Services**.
2. Klicken Sie auf den Webdienst, mit dem Sie arbeiten.
3. Klicken Sie auf den Endpunkt, für den der Schlüssel abgerufen werden soll.
4. Klicken Sie im oberen Menü auf **Consume**.
5. Kopieren und speichern Sie den **Primary Key**.

### <a name="classic-web-service"></a>Klassischer Webdienst
 Sie können auch einen Schlüssel für einen klassischen Webdienst aus Machine Learning Studio (klassisch) abrufen.

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klassisch)
1. Klicken Sie in Machine Learning Studio (klassisch) links auf **WEB SERVICES**.
2. Klicken Sie auf einen Webdienst. Der **API Key** befindet sich auf der Registerkarte **DASHBOARD**.

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Herstellen der Verbindung mit einem Machine Learning-Webdienst
Sie können mit jeder Programmiersprache, die HTTP-Anforderungen und -Antworten unterstützt, eine Verbindung mit einem Machine Learning-Webdienst herstellen. Sie können Beispiele in C#, Python und R auf einer Hilfeseite für den Machine Learning-Webdienst anzeigen.

**Hilfe zur Machine Learning-API** Wenn Sie einen Webdienst bereitstellen, wird Hilfe zur Machine Learning-API erstellt. Weitere Informationen finden Sie unter [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md).
Die Machine Learning-API-Hilfeseite enthält Details zu einem Vorhersagewebdienst.

1. Klicken Sie auf den Webdienst, mit dem Sie arbeiten.
2. Klicken Sie auf den Endpunkt, für den die API-Hilfeseite angezeigt werden soll.
3. Klicken Sie im oberen Menü auf **Consume**.
4. Klicken Sie unter den Endpunkten „Request-Response“ oder „Batch Execution“ auf **API help page**.

**So zeigen Sie Machine Learning-API-Hilfe für einen neuen Webdienst an**

Im [Azure Machine Learning Web Services-Portal](https://services.azureml.net/):

1. Klicken Sie im oberen Menü auf **WEB SERVICES**.
2. Klicken Sie auf den Webdienst, für den der Schlüssel abgerufen werden soll.

Klicken Sie auf **Use Web Service** (Webdienst verwenden), um die URIs für die Dienste „Request-Reposonse“ und „Batch Execution“ und Beispielcode in C#, R und Python abzurufen.

Klicken Sie auf **Swagger API**, um auf Swagger basierende Dokumentation zu den APIs abzurufen, die über die angegebenen URIs aufgerufen werden.

### <a name="c-sample"></a>C#-Beispiel
Verwenden Sie zum Verbinden mit einem Machine Learning-Webdienst einen **HttpClient** durch Übergeben von ScoreData. ScoreData enthält ein FeatureVector, ein n-dimensionaler Vektor von numerischen Funktionen, die die ScoreData darstellen. Sie authentifizieren sich mit einem API-Schlüssel beim Machine Learning-Dienst.

Für die Verbindung mit einem Machine Learning-Webdienst muss das NuGet-Paket **Microsoft.AspNet.WebApi.Client** installiert sein.

**Installieren von Microsoft.AspNet.WebApi.Client NuGet in Visual Studio**

1. Veröffentlichen Sie den „Download Dataset from UCI: Adult 2 class dataset“-Webdienst.
2. Klicken Sie auf **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.
3. Wählen Sie **Install-Package Microsoft.AspNet.WebApi.Client aus**.

**So führen Sie das Codebeispiel aus**

1. Stellen Sie das Experiment „Sample 1: Download dataset from UCI: Adult 2 class dataset“ bereit, das Teil der Machine Learning-Beispielsammlung ist.
2. Weisen Sie „apiKey“ den Schlüssel von einem Webdienst zu. Siehe **Abrufen eines Autorisierungsschlüssels** weiter oben.
3. Weisen Sie "serviceUri" die Anforderungs-URI zu.

**So sieht eine vollständige Anforderung aus.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python-Beispiel
Verwenden Sie für eine Verbindung mit einem Machine Learning-Webdienst die Bibliothek **urllib2** für Python 2.X und die Bibliothek **urllib.request** für Python 3.X. Sie übergeben „ScoreData“. Darin ist ein „FeatureVector“-Element enthalten, ein n-dimensionaler Vektor von numerischen Features, die „ScoreData“ darstellen. Sie authentifizieren sich mit einem API-Schlüssel beim Machine Learning-Dienst.

**So führen Sie das Codebeispiel aus**

1. Stellen Sie „Sample 1: Download dataset from UCI: Adult 2 class dataset“ bereit, das Teil der Machine Learning-Beispielsammlung ist.
2. Weisen Sie „apiKey“ den Schlüssel von einem Webdienst zu. Im Abschnitt **Abrufen eines Autorisierungsschlüssels** weiter oben in diesem Artikel finden Sie weitere Informationen.
3. Weisen Sie "serviceUri" die Anforderungs-URI zu.

**So sieht eine vollständige Anforderung aus.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R-Beispiel

Für eine Verbindung mit einem Machine Learning-Webdienst verwenden Sie die Bibliotheken **RCurl** und **rjson**, um die Anforderung auszuführen und die zurückgegebene JSON-Antwort zu verarbeiten. Sie übergeben „ScoreData“. Darin ist ein „FeatureVector“-Element enthalten, ein n-dimensionaler Vektor von numerischen Features, die „ScoreData“ darstellen. Sie authentifizieren sich mit einem API-Schlüssel beim Machine Learning-Dienst.

**So sieht eine vollständige Anforderung aus.**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>JavaScript-Beispiel

Verwenden Sie für eine Verbindung mit einem Machine Learning-Webdienst das npm-Paket **request** in Ihrem Projekt. Zudem verwenden Sie das `JSON`-Objekt, um Ihre Eingabe zu formatieren und das Ergebnis zu analysieren. Führen Sie die Installation mit `npm install request --save` durch, oder fügen Sie `"request": "*"` in „package.json“ unter `dependencies` hinzu, und führen Sie `npm install` aus.

**So sieht eine vollständige Anforderung aus.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```