---
title: REST-API (Version 4) – Go – QnA Maker
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen und Codebeispiele zu Go-REST, mit denen Sie schnell mit der Microsoft-Textübersetzungs-API in Microsoft Cognitive Services in Azure loslegen können.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 76be371547bcb9daf0e10b62df62df5065cd6465
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791462"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-go"></a>Verwenden der QnA Maker-REST-API mit Go 
<a name="HOLTop"></a>

In diesem Artikel erfahren Sie, wie Sie die [Microsoft QnA Maker-API](../Overview/overview.md)  mit Go verwenden können, um folgende Aktionen auszuführen.

- [Erstellen einer neuen Wissensdatenbank](#Create)
- [Aktualisieren einer vorhandenen Wissensdatenbank](#Update)
- [Abrufen des Status einer Anforderung zum Erstellen oder Aktualisieren einer Wissensdatenbank](#Status)
- [Veröffentlichen einer vorhandenen Wissensdatenbank](#Publish)
- [Ersetzen der Inhalte einer vorhandenen Wissensdatenbank](#Replace)
- [Herunterladen der Inhalte einer Wissensdatenbank](#GetQnA)
- [Abrufen von Antworten auf Fragen mithilfe einer Wissensdatenbank](#GetAnswers)
- [Abrufen von Informationen zu einer Wissensdatenbank](#GetKB)
- [Abrufen von Informationen zu allen Wissensdatenbanken eines bestimmten Benutzers](#GetKBsByUser)
- [Löschen einer Wissensdatenbank](#Delete)
- [Abrufen der aktuellen Endpunktschlüssel](#GetKeys)
- [Erneutes Generieren der aktuellen Endpunktschlüssel](#PutKeys)
- [Abrufen der aktuellen Wortvarianten ohne Berücksichtigung von Groß-/Kleinbuchstaben](#GetAlterations)
- [Ersetzen der aktuellen Wortvarianten ohne Berücksichtigung von Groß-/Kleinbuchstaben](#PutAlterations)

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen [Go 1.10.1](https://golang.org/dl/), um diesen Code auszuführen.

Sie müssen über ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit der **Microsoft QnA Maker-API** verfügen. Sie benötigen einen kostenpflichtigen Abonnementschlüssel aus Ihrem [Azure-Dashboard](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Erstellen einer Wissensdatenbank

Der folgende Code erstellt mithilfe der Methode [Create](https://go.microsoft.com/fwlink/?linkid=2092179) (Erstellen) eine neue Wissensdatenbank.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "time"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/create"

type Response struct {
    Headers map[string][]string
    Body    string
}

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func post(uri string, content string) Response {
    req, _ := http.NewRequest("POST", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    return Response {response.Header, string(body)}
}

func get(uri string) Response {
    req, _ := http.NewRequest("GET", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    return Response {response.Header, string(body)}
}

var req string = `{
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://go.microsoft.com/fwlink/?linkid=2092179",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    }
  ],
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
}`;

func create_kb(uri string, req string) (string, string) {
    fmt.Println("Calling " + uri + ".")
    result := post(uri, req)
    return result.Headers["Location"][0], result.Body
}

func check_status(uri string) (string, string) {
    fmt.Println("Calling " + uri + ".")
    result := get(uri)
    if retry, success := result.Headers["Retry-After"]; success {
        return retry[0], result.Body
    } else {
// If the response headers did not include a Retry-After value, default to 30 seconds.
        return "30", result.Body
    }
}

func main() {
    var uri = host + service + method
    operation, body := create_kb(uri, req)
    fmt.Printf(body + "\n")
    var done bool = false
    for done == false {
        uri := host + service + operation
        wait, status := check_status(uri)
        fmt.Println(status)
        var status_obj map[string]interface{}
        json.Unmarshal([]byte(status), &status_obj)
        state := status_obj["operationState"]
// If the operation isn't finished, wait and query again.
        if state == "Running" || state == "NotStarted" {
            fmt.Printf ("Waiting " + wait + " seconds...")
            sec, _ := strconv.Atoi(wait)
            time.Sleep (time.Duration(sec) * time.Second)
        } else {
            done = true
        }
    }
}
```

**Antwort auf das Erstellen einer Wissensdatenbank**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Nach oben](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Aktualisieren einer Wissensdatenbank

Der folgende Code aktualisiert mithilfe der Methode [Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update) (Aktualisieren) eine vorhandene Wissensdatenbank.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "time"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with a valid knowledge base ID.
var kb string = "ENTER ID HERE";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/"

type Response struct {
    Headers map[string][]string
    Body    string
}

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func patch(uri string, content string) Response {
    req, _ := http.NewRequest("PATCH", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    return Response {response.Header, string(body)}
}

func get(uri string) Response {
    req, _ := http.NewRequest("GET", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    return Response {response.Header, string(body)}
}

var req string = `{
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': [
      'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
    ]
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
}`;

func update_kb (uri string, req string) (string, string) {
    fmt.Println("Calling " + uri + ".")
    result := patch(uri, req)
    return result.Headers["Location"][0], result.Body
}

func check_status(uri string) (string, string) {
    fmt.Println("Calling " + uri + ".")
    result := get(uri)
    if retry, success := result.Headers["Retry-After"]; success {
        return retry[0], result.Body
    } else {
// If the response headers did not include a Retry-After value, default to 30 seconds.
        return "30", result.Body
    }
}

func main() {
    var uri = host + service + method + kb
    operation, body := update_kb (uri, req)
    fmt.Printf(body + "\n")
    var done bool = false
    for done == false {
        uri := host + service + operation
        wait, status := check_status(uri)
        fmt.Println(status)
        var status_obj map[string]interface{}
        json.Unmarshal([]byte(status), &status_obj)
        state := status_obj["operationState"]
// If the operation isn't finished, wait and query again.
        if state == "Running" || state == "NotStarted" {
            fmt.Printf ("Waiting " + wait + " seconds...")
            sec, _ := strconv.Atoi(wait)
            time.Sleep (time.Duration(sec) * time.Second)
        } else {
            done = true
        }
    }
}
```

**Antwort auf das Aktualisieren einer Wissensdatenbank**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Nach oben](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Abrufen des Anforderungsstatus

Sie können die Methode für [Vorgänge](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails) aufrufen, um den Status einer Anforderung zum Erstellen oder Aktualisieren einer Wissensdatenbank zu prüfen. Sie können dem Beispielcode für die Methoden zum [Erstellen](#Create) oder [Aktualisieren](#Update) entnehmen, wie diese Methode verwendet wird.

[Nach oben](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

Der folgende Code veröffentlicht mithilfe der Methode [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Veröffentlichen) eine vorhandene Wissensdatenbank.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with a valid knowledge base ID.
var kb string = "ENTER ID HERE";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func post(uri string, content string) string {
    req, _ := http.NewRequest("POST", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    if(response.StatusCode == 204) {
        return "{'result' : 'Success.'}"
    } else {
        return string(body)
    }
}

func publish(uri string, req string) string {
    fmt.Println("Calling " + uri + ".")
    return post(uri, req)
}

func main() {
    var uri = host + service + method + kb
    body := publish(uri, "")
    fmt.Printf(body + "\n")

}
```

**Antwort auf das Veröffentlichen einer Wissensdatenbank**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "result": "Success."
}
```

[Nach oben](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Ersetzen einer Wissensdatenbank

Der folgende Code ersetzt mithilfe der Methode [Replace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace) (Ersetzen) die Inhalte einer angegebenen Wissensdatenbank.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with a valid knowledge base ID.
var kb string = "ENTER ID HERE";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func put(uri string, content string) string {
    req, _ := http.NewRequest("PUT", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    if(response.StatusCode == 204) {
        return "{'result' : 'Success.'}"
    } else {
        return string(body)
    }
}

var req string = `{
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my Knowledge Base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ]
}`;

func replace_kb (uri string, req string) (string) {
    fmt.Println("Calling " + uri + ".")
    return put(uri, req)
}

func main() {
    var uri = host + service + method + kb
    body := replace_kb (uri, req)
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Ersetzen einer Wissensdatenbank**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
    "result": "Success."
}
```

[Nach oben](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Herunterladen von Inhalten einer Wissensdatenbank

Der folgende Code lädt mithilfe der Methode [Download knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download) (Wissensdatenbank herunterladen) die Inhalte der angegebenen Wissensdatenbank herunter.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with a valid knowledge base ID.
var kb string = "ENTER ID HERE";

// NOTE: Replace this with "test" or "prod".
var env string = "test";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/%s/%s/qna"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func get(uri string) string {
    req, _ := http.NewRequest("GET", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)
    return string(body)
}

func getQnA(uri string) string {
    fmt.Println("Calling " + uri + ".")
    return get(uri)
}

func main() {
    var method_with_id = fmt.Sprintf(method, kb, env)
    var uri = host + service + method_with_id
    body := getQnA(uri)
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Herunterladen einer Wissensdatenbank**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Nach oben](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-by-using-a-knowledge-base"></a>Abrufen von Antworten auf Fragen aus einer Wissensdatenbank

Der folgende Code ruft mithilfe der Methode **Generate answers** (Antworten generieren) Antworten auf Fragen aus der angegebenen Wissensdatenbank ab.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
1. Fügen Sie den unten stehenden Code hinzu.
1. Ersetzen Sie den `host`-Wert durch den Websitenamen für Ihr QnA Maker-Abonnement. Weitere Informationen finden Sie unter [Erstellen eines QnA Maker-Diensts](../How-To/set-up-qnamaker-service-azure.md).
1. Ersetzen Sie den `endpoint_key`-Wert durch einen für Ihr Abonnement gültigen Endpunktschlüssel. Beachten Sie, dass es sich dabei nicht um den Abonnementschlüssel handelt. Sie können Ihre Endpunktschlüssel über die Methode zum [Abrufen von Endpunktschlüsseln](#GetKeys) abrufen.
1. Ersetzen Sie den `kb`-Wert durch die ID der Wissensdatenbank, die Sie auf Antworten abfragen möchten. Beachten Sie, dass diese Wissensdatenbank bereits mithilfe der Methode zum [Veröffentlichen](#Publish) veröffentlicht worden sein muss.
1. Führen Sie das Programm aus.

```go
package main

import (
    "bytes"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// NOTE: Replace this with a valid host name.
var host string = "ENTER HOST HERE";

// NOTE: Replace this with a valid endpoint key.
// This is not your subscription key.
// To get your endpoint keys, call the GET /endpointkeys method.
var endpoint_key string = "ENTER KEY HERE";

// NOTE: Replace this with a valid knowledge base ID.
// Make sure you have published the knowledge base with the
// POST /knowledgebases/{knowledge base ID} method.
var kb string = "ENTER KB ID HERE";

var method string = "/qnamaker/knowledgebases/" + kb + "/generateanswer"

func post(uri string, content string) string {
    req, _ := http.NewRequest("POST", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Authorization", "EndpointKey " + endpoint_key)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    return string(body)
}

var req string = `{
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}`;

func get_answers(uri string, req string) string {
    fmt.Println("Calling " + uri + ".")
    return post(uri, req)
}

func main() {
    var uri = host + method
    body := get_answers(uri, req)
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Abrufen von Antworten**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Nach oben](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Abrufen von Informationen zu einer Wissensdatenbank

Der folgende Code ruft mithilfe der Methode [Get knowledge base details](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails) (Details zur Wissensdatenbank abrufen) Informationen zur angegebenen Wissensdatenbank ab.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with a valid knowledge base ID.
var kb string = "ENTER ID HERE";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func get(uri string) string {
    req, _ := http.NewRequest("GET", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)
    return string(body)
}

func getKB(uri string) string {
    fmt.Println("Calling " + uri + ".")
    return get(uri)
}

func main() {
    var uri = host + service + method + kb
    body := getKB(uri)
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Abrufen von Details zur Wissensdatenbank**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Nach oben](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Abrufen aller Wissensdatenbanken für einen Benutzer

Der folgende Code ruft mithilfe der Methode [Get knowledge bases for user](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/listall) (Wissensdatenbanken für Benutzer abrufen) Informationen zu allen Wissensdatenbanken für einen bestimmten Benutzer ab.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func get(uri string) string {
    req, _ := http.NewRequest("GET", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)
    return string(body)
}

func getKBs(uri string) string {
    fmt.Println("Calling " + uri + ".")
    return get(uri)
}

func main() {
    var uri = host + service + method
    body := getKBs(uri)
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Abrufen von Wissensdatenbanken für Benutzer**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Nach oben](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Löschen einer Wissensdatenbank

Der folgende Code löscht mithilfe der Methode [Delete knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete) (Wissensdatenbank löschen) die angegebene Wissensdatenbank.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with a valid knowledge base ID.
var kb string = "ENTER ID HERE";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func delete(uri string) string {
    req, _ := http.NewRequest("DELETE", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    if(response.StatusCode == 204) {
        return "{'result' : 'Success.'}"
    } else {
        return string(body)
    }
}

func deleteKB(uri string) string {
    fmt.Println("Calling " + uri + ".")
    return delete(uri)
}

func main() {
    var uri = host + service + method + kb
    body := deleteKB(uri)
    fmt.Printf(body + "\n")

}
```

**Antwort auf das Löschen von Wissensdatenbanken**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "result": "Success."
}
```

[Nach oben](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Abrufen von Endpunktschlüsseln

Der folgende Code ruft mithilfe der Methode [Get endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys) (Endpunktschlüssel abrufen) die aktuellen Endpunktschlüssel ab.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/endpointkeys/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func get(uri string) string {
    req, _ := http.NewRequest("GET", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)
    return string(body)
}

func getEndpointKeys(uri string) string {
    fmt.Println("Calling " + uri + ".")
    return get(uri)
}

func main() {
    var uri = host + service + method
    body := getEndpointKeys(uri)
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Abrufen von Endpunktschlüsseln**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Nach oben](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Aktualisieren von Endpunktschlüsseln

Der folgende Code generiert mithilfe der Methode [Refresh endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/refreshkeys) (Endpunktschlüssel aktualisieren) die aktuellen Endpunktschlüssel neu.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with "PrimaryKey" or "SecondaryKey."
var key_type string = "PrimaryKey";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/endpointkeys/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func patch(uri string, content string) string {
    req, _ := http.NewRequest("PATCH", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    return string(body)
}

func refresh (uri string, req string) string {
    fmt.Println("Calling " + uri + ".")
    return patch(uri, req)
}

func main() {
    var uri = host + service + method + key_type
    body := refresh (uri, "")
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Aktualisieren von Endpunktschlüsseln**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Nach oben](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Abrufen von Wortänderungen

Der folgende Code ruft mithilfe der Methode [Download alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/get) (Änderungen herunterladen) aktuelle Wortänderungen ab.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/alterations/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func get(uri string) string {
    req, _ := http.NewRequest("GET", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)
    return string(body)
}

func getAlterations(uri string) string {
    fmt.Println("Calling " + uri + ".")
    return get(uri)
}

func main() {
    var uri = host + service + method
    body := getAlterations(uri)
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Abrufen von Wortvarianten**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Nach oben](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Ersetzen von Wortänderungen

Der folgende Code ersetzt mithilfe der Methode [Replace alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) (Änderungen ersetzen) die aktuellen Wortänderungen.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues Go-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/alterations/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func put(uri string, content string) string {
    req, _ := http.NewRequest("PUT", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    if(response.StatusCode == 204) {
        return "{'result' : 'Success.'}"
    } else {
        return string(body)
    }
}

var req string = `{
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
}`;

func putAlterations (uri string, req string) (string) {
    fmt.Println("Calling " + uri + ".")
    return put(uri, req)
}

func main() {
    var uri = host + service + method
    body := putAlterations (uri, req)
    fmt.Printf(body + "\n")
}
```

**Antwort auf das Ersetzen von Wortvarianten**

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "result": "Success."
}
```

[Nach oben](#HOLTop)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

## <a name="see-also"></a>Weitere Informationen 

[Übersicht über QnA Maker](../Overview/overview.md)
