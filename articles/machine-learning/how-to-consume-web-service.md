---
title: Erstellen eines Clients für ein als Webdienst bereitgestelltes Modell
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie ein Webdienst-Endpunkt aufgerufen werden kann, der bei der Bereitstellung eines Modells über Azure Machine Learning generiert wurde.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: fa1b19ef4ece3011c97c3158d54edd1d39efc09d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832635"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells


Durch die Bereitstellung eines Azure Machine Learning-Modells als Webdienst wird ein REST-API-Endpunkt erstellt. Sie können Daten an diesen Endpunkt senden und die vom Modell zurückgegebene Vorhersage empfangen. In diesem Dokument erfahren Sie, wie Sie Clients für den Webdienst mithilfe von C#, Go, Java und Python erstellen.

Sie erstellen einen Webdienst, wenn Sie ein Modell in Ihrer lokalen Umgebung oder für Azure Container Instances, Azure Kubernetes Service oder Field Programmable Gate Arrays (FPGA) bereitstellen. Der URI für den Zugriff auf den Webdienst kann über das [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) abgerufen werden. Wenn die Authentifizierung aktiviert ist, können Sie das SDK auch zum Abrufen der Authentifizierungsschlüssel oder -token verwenden.

Dies ist der allgemeine Workflow zum Erstellen eines Clients, der einen Machine Learning-Webdienst verwendet:

1. Verwenden des SDK zum Abrufen der Verbindungsinformationen
1. Festlegen des Typs der vom Modell verwendeten Anforderungsdaten
1. Erstellen einer Anwendung, die den Webdienst aufruft

> [!TIP]
> Die Beispiele in diesem Dokument werden ohne Verwendung von OpenAPI-Spezifikationen (Swagger) manuell erstellt. Wenn Sie eine OpenAPI-Spezifikation für Ihre Bereitstellung aktiviert haben, können Sie mit Tools wie [swagger-codegen](https://github.com/swagger-api/swagger-codegen) Clientbibliotheken für Ihren Dienst erstellen.

## <a name="connection-information"></a>Verbindungsinformationen

> [!NOTE]
> Verwenden Sie das Azure Machine Learning SDK zum Abrufen der Webdienstinformationen. Dies ist ein Python SDK. Sie können eine beliebige Sprache verwenden, um einen Client für den Dienst zu erstellen.

Die [azureml.core.Webservice](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py)-Klasse stellt die erforderlichen Informationen zum Erstellen eines Clients bereit. Die folgenden `Webservice`-Eigenschaften sind hilfreich, wenn Sie eine Clientanwendung erstellen:

* `auth_enabled`: Wenn Schlüsselauthentifizierung aktiviert ist `True`, andernfalls `False`.
* `token_auth_enabled`: Wenn Tokenauthentifizierung aktiviert ist `True`, andernfalls `False`.
* `scoring_uri`: Die REST-API-Adresse.
* `swagger_uri`: Die Adresse der OpenAPI-Spezifikation. Dieser URI ist verfügbar, wenn Sie die automatische Schemagenerierung aktiviert haben. Weitere Informationen finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

Es gibt verschiedene Möglichkeiten zum Abrufen dieser Informationen für bereitgestellte Webdienste:

# <a name="python"></a>[Python](#tab/python)

* Wenn Sie ein Modell bereitstellen, wird ein `Webservice`-Objekt mit Informationen über den Dienst zurückgegeben:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Mit `Webservice.list` können Sie eine Liste bereitgestellter Webdienste für Modelle in Ihrem Arbeitsbereich abrufen. Sie können Filter hinzufügen, um die Liste der zurückgegebenen Informationen einzugrenzen. Weitere Informationen zu den möglichen Filtern finden Sie in der Referenzdokumentation [Webservice.list](/python/api/azureml-core/azureml.core.webservice.webservice.webservice?preserve-view=true&view=azure-ml-py).

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Wenn Sie den Namen des bereitgestellten Diensts kennen, können Sie eine neue Instanz von `Webservice` erstellen und den Arbeitsbereich und den Dienstnamen als Parameter angeben. Dieses neue Objekt enthält Informationen über den bereitgestellten Dienst.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie den Namen des bereitgestellten Diensts kennen, verwenden Sie den Befehl [az ml service show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show):

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Azure Machine Learning Studio __Endpunkte__, __Echtzeitendpunkte__ und dann den Endpunktnamen aus. In den Details für den Endpunkt enthält das Feld __REST-Endpunkt__ den Bewertungs-URI. Das Feld __Swagger-URI__ enthält den Swagger-URI.

---

Die folgende Tabelle zeigt, wie diese URIs aussehen:

| URI-Typ | Beispiel |
| ----- | ----- |
| Bewertungs-URI | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Swagger-URI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> Die IP-Adresse wird für Ihre Bereitstellung unterschiedlich sein. Jeder AKS-Cluster hat seine eigene IP-Adresse, die von den Bereitstellungen in diesem Cluster gemeinsam genutzt wird.

### <a name="secured-web-service"></a>Geschützter Webdienst

Wenn Sie den bereitgestellten Webdienst mit einem TSL/SSL-Zertifikat geschützt haben, können Sie über [HTTPS](https://en.wikipedia.org/wiki/HTTPS) eine Verbindung mit dem Dienst unter Verwendung des Bewertungs- oder Swagger-URIs herstellen. Mit HTTPS wird die Kommunikation zwischen einem Client und einem Webdienst geschützt, indem sie verschlüsselt wird. Die Verschlüsselung verwendet [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). In einigen Fällen wird TLS immer noch als *Secure Sockets Layer* (SSL) bezeichnet, der Vorgänger von TLS.

> [!IMPORTANT]
> Über Azure Machine Learning bereitgestellte Webdienste unterstützen nur TLS-Version 1.2. Stellen Sie beim Erstellen einer Clientanwendung sicher, dass diese Version unterstützt wird.

Weitere Informationen finden Sie unter [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Authentifizierung für Dienste

Azure Machine Learning bietet zwei Möglichkeiten zur Steuerung des Zugriffs auf Ihre Webdienste.

|Authentifizierungsmethode|ACI|AKS|
|---|---|---|
|Schlüssel|Standardmäßig deaktiviert| Standardmäßig aktiviert|
|Token| Nicht verfügbar.| Standardmäßig deaktiviert |

Verwenden Sie beim Senden einer Anforderung an einen Dienst, der mit einem Schlüssel oder Token gesichert ist, den __Authorization__-Header, um den Schlüssel oder das Token zu übergeben. Der Schlüssel oder das Token muss als `Bearer <key-or-token>` formatiert sein, wobei `<key-or-token>` der Schlüssel- oder Tokenwert ist.

Der primäre Unterschied zwischen Schlüsseln und Token besteht darin, dass **Schlüssel statisch sind und manuell neu generiert werden können** und **Token bei Ablauf aktualisiert werden müssen**. Die schlüsselbasierte Authentifizierung wird für Webdienste unterstützt, die über Azure Container Instances und Azure Kubernetes Service bereitgestellt werden. Die tokenbasierte Authentifizierung ist **nur** für Azure Kubernetes Service-Bereitstellungen verfügbar. Weitere Informationen zum Konfigurieren der Authentifizierung finden Sie unter [Konfigurieren der Authentifizierung für Modelle, die als Webdienst bereitgestellt werden](how-to-authenticate-web-service.md).


#### <a name="authentication-with-keys"></a>Authentifizierung mit Schlüsseln

Wenn Sie Authentifizierung für eine Bereitstellung aktivieren, werden automatisch Authentifizierungsschlüssel erstellt.

* Die Authentifizierung ist standardmäßig aktiviert, wenn die Bereitstellung in Azure Kubernetes Service erfolgt.
* Die Authentifizierung ist standardmäßig deaktiviert, wenn die Bereitstellung in Azure Container Instances erfolgt.

Um die Authentifizierung zu steuern, verwenden Sie den `auth_enabled`-Parameter beim Erstellen oder Aktualisieren einer Bereitstellung.

Wenn die Authentifizierung aktiviert ist, können Sie mithilfe der Methode `get_keys` einen primären und einen sekundären Authentifizierungsschlüssel abrufen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Wenn Sie einen Schlüssel erneut generieren müssen, verwenden Sie [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Authentifizierung mit Tokens

Wenn Sie die Tokenauthentifizierung für einen Webdienst aktivieren, muss ein Benutzer ein Azure Machine Learning JWT-Token für den Webdienst bereitstellen, um darauf zugreifen zu können. 

* Die Tokenauthentifizierung ist standardmäßig deaktiviert, wenn die Bereitstellung in Azure Kubernetes Service erfolgt.
* Die Tokenauthentifizierung wird nicht unterstützt, wenn die Bereitstellung in Azure Container Instances erfolgt.

Um die Tokenauthentifizierung zu steuern, verwenden Sie beim Erstellen oder Aktualisieren einer Bereitstellung den Parameter `token_auth_enabled`.

Wenn die Tokenauthentifizierung aktiviert ist, können Sie mithilfe der Methode `get_token` ein Bearertoken und dessen Ablaufzeit abrufen:

```python
token, refresh_by = service.get_token()
print(token)
```

Wenn Sie über die [Azure CLI und die Machine-Learning-Erweiterung](reference-azure-machine-learning-cli.md) verfügen, können Sie den folgenden Befehl zum Abrufen eines Tokens verwenden:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Aktuell besteht der einzige Weg, das Token abzurufen, darin, das Azure Machine Learning-SDK oder die Azure CLI-Machine-Learning-Erweiterung zu verwenden.

Nach Ablauf der für `refresh_by` festgelegten Zeit müssen Sie ein neues Token anfordern. 

## <a name="request-data"></a>Anforderungsdaten

Die REST-API erwartet als Hauptteil der Anforderung ein JSON-Dokument mit der folgenden Struktur:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Die Struktur der Daten muss dem entsprechen, was vom Bewertungsskript und vom Modell im Dienst erwartet wird. Die Daten können vor der Übergabe an das Modell vom Bewertungsskript geändert werden.

### <a name="binary-data"></a>Binärdaten

Informationen zum Aktivieren der Unterstützung für Binärdaten in Ihrem Dienst finden Sie unter [Binärdaten](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> Die Aktivierung der Unterstützung für Binärdaten erfolgt in der Datei „score.py“, die vom bereitgestellten Modell verwendet wird. Verwenden Sie über den Client die HTTP-Funktionalität Ihrer Programmiersprache. Mit dem folgenden Codeausschnitt wird der Inhalt einer JPG-Datei beispielsweise an einen Webdienst gesendet:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)

Informationen zum Aktivieren der CORS-Unterstützung in Ihrem Dienst finden Sie unter [Ressourcenfreigabe zwischen verschiedenen Ursprüngen](how-to-deploy-advanced-entry-script.md#cors) (Cross-Origin Resource Sharing).

## <a name="call-the-service-c"></a>Aufrufen des Diensts (C#)

In diesem Beispiel wird die Verwendung von C# zum Aufrufen des Webdiensts veranschaulicht, der im Beispiel [Train within notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) erstellt wurde:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Die zurückgegebenen Ergebnisse ähneln dem folgenden JSON-Dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Aufrufen des Diensts (Go)

In diesem Beispiel wird die Verwendung von Go zum Aufrufen des Webdiensts veranschaulicht, der im Beispiel [Train within notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) erstellt wurde:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Die zurückgegebenen Ergebnisse ähneln dem folgenden JSON-Dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Aufrufen des Diensts (Java)

In diesem Beispiel wird die Verwendung von Java zum Aufrufen des Webdiensts veranschaulicht, der im Beispiel [Train within notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) erstellt wurde:

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Die zurückgegebenen Ergebnisse ähneln dem folgenden JSON-Dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Aufrufen des Diensts (Python)

In diesem Beispiel wird die Verwendung von Python zum Aufrufen des Webdiensts veranschaulicht, der im Beispiel [Train within notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) erstellt wurde:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Die zurückgegebenen Ergebnisse ähneln dem folgenden JSON-Dokument:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Webdienst Schema (OpenAPI-Spezifikation)

Wenn Sie automatische Schemagenerierung mit Ihrer Bereitstellung verwendet haben, können Sie die Adresse der OpenAPI-Spezifikation für den Dienst mithilfe der [swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueswagger-uri)-Eigenschaft abrufen. (Beispiel: `print(service.swagger_uri)`.) Verwenden Sie eine GET-Anforderung, oder öffnen Sie den URI in einem Browser, um die Spezifikation abzurufen.

Das folgende JSON-Dokument ist ein Beispiel für ein Schema (OpenAPI-Spezifikation), das für eine Bereitstellung generiert wurde:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Weitere Informationen finden Sie in der [OpenAPI-Spezifikation](https://swagger.io/specification/).

Ein Hilfsprogramm, das Clientbibliotheken aus der Spezifikation erstellen kann, finden Sie unter [swagger-codegen](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> Sie können das JSON-Schemadokument abrufen, nachdem Sie den Dienst bereitgestellt haben. Verwenden Sie die [swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueswagger-uri)-Eigenschaft des bereitgestellten Webdiensts (beispielsweise `service.swagger_uri`), um den URI für die Swagger-Datei des lokalen Webdiensts abzurufen.

## <a name="consume-the-service-from-power-bi"></a>Nutzen des Diensts aus Power BI

Power BI unterstützt die Nutzung von Azure Machine Learning-Webdiensten, um die Daten in Power BI um Vorhersagen zu erweitern. 

Zum Generieren eines Webdiensts, der für die Nutzung in Power BI unterstützt wird, muss das Schema das für Power BI benötigte Format unterstützen. [Informieren Sie sich über die Erstellung eines von Power BI unterstützten Schemas](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint).

Nachdem der Webdienst bereitgestellt wurde, kann er über Power BI-Dataflows genutzt werden. [Informieren Sie sich über die Integration von Azure Machine Learning in Power BI (Vorschau)](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Nächste Schritte

Eine Referenzarchitektur für die Echtzeitbewertung von Python und Deep Learning-Modellen finden Sie im [Azure Architecture Center](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
