---
title: REST-API (Version 4) – C# – QnA Maker
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen und Codebeispiele zu C#-REST, mit denen Sie schnell mit der Microsoft-Textübersetzungs-API in Microsoft Cognitive Services in Azure loslegen können.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: cf801b8b6b458104a469932487860285102a641b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65777641"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-c"></a>Verwenden der QnA Maker-REST-API mit C# 
<a name="HOLTop"></a>

In diesem Artikel erfahren Sie, wie Sie die [Microsoft QnA Maker-API](../Overview/overview.md)  mit C# verwenden können, um folgende Aktionen auszuführen.

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

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen [Visual Studio 2017](https://www.visualstudio.com/downloads/), um diesen Code unter Windows ausführen zu können. (Die kostenlose Community Edition ist hierfür geeignet.)

Sie müssen über ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit der **Microsoft QnA Maker-API** verfügen. Sie benötigen einen kostenpflichtigen Abonnementschlüssel aus Ihrem [Azure-Dashboard](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Erstellen einer Wissensdatenbank

Über den folgenden Code können Sie mithilfe der Methode zum [Erstellen einer Wissensdatenbank](https://go.microsoft.com/fwlink/?linkid=2092179) eine neue Wissensdatenbank erstellen.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/create";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string kb = @"
{
  'name': 'QnA Maker FAQ',
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
  ],
  'urls': [
    'https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs',
    'https://docs.microsoft.com/bot-framework/resources-bot-framework-faq'
  ],
  'files': []
}
";

        public struct Response
        {
            public HttpResponseHeaders headers;
            public string response;

            public Response(HttpResponseHeaders headers, string response)
            {
                this.headers = headers;
                this.response = response;
            }
        }

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<Response> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        async static Task<Response> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        async static Task<Response> PostCreateKB(string kb)
        {
            string uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            return await Post(uri, kb);
        }

        async static Task<Response> GetStatus(string operation)
        {
            string uri = host + service + operation;
            Console.WriteLine("Calling " + uri + ".");
            return await Get(uri);
        }

        async static void CreateKB()
        {
            
            var response = await PostCreateKB(kb);
            var operation = response.headers.GetValues("Location").First();
            Console.WriteLine(PrettyPrint(response.response));

            var done = false;
            while (true != done)
            {
                response = await GetStatus(operation);
                Console.WriteLine(PrettyPrint(response.response));

                var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

                String state = fields["operationState"];
                if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
                {
                    var wait = response.headers.GetValues("Retry-After").First();
                    Console.WriteLine("Waiting " + wait + " seconds...");
                    Thread.Sleep(Int32.Parse(wait) * 1000);
                }
                else
                {
                    Console.WriteLine("Press any key to continue.");
                    done = true;
                }
            }
        }

        static void Main(string[] args)
        {
            CreateKB();
            Console.ReadLine();
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

Der folgende Code aktualisiert mithilfe der Methode zum [Aktualisieren](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update) eine vorhandene Wissensdatenbank.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string new_kb = @"
{
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
}
";

        public struct Response
        {
            public HttpResponseHeaders headers;
            public string response;

            public Response(HttpResponseHeaders headers, string response)
            {
                this.headers = headers;
                this.response = response;
            }
        }

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<Response> Patch(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = new HttpMethod("PATCH");
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        async static Task<Response> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        async static Task<Response> PostUpdateKB(string kb, string new_kb)
        {
            string uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            return await Patch(uri, new_kb);
        }

        async static Task<Response> GetStatus(string operation)
        {
            string uri = host + service + operation;
            Console.WriteLine("Calling " + uri + ".");
            return await Get(uri);
        }

        async static void UpdateKB(string kb, string new_kb)
        {
            var response = await PostUpdateKB(kb, new_kb);
            var operation = response.headers.GetValues("Location").First();
            Console.WriteLine(PrettyPrint(response.response));

            var done = false;
            while (true != done)
            {
                response = await GetStatus(operation);
                Console.WriteLine(PrettyPrint(response.response));

                var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

                String state = fields["operationState"];
                if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
                {
                    var wait = response.headers.GetValues("Retry-After").First();
                    Console.WriteLine("Waiting " + wait + " seconds...");
                    Thread.Sleep(Int32.Parse(wait) * 1000);
                }
                else
                {
                    Console.WriteLine("Press any key to continue.");
                    done = true;
                }
            }
        }

        static void Main(string[] args)
        {
            UpdateKB(kb, new_kb);
            Console.ReadLine();
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

Der folgende Code veröffentlicht mithilfe der Methode zum [Veröffentlichen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) eine vorhandene Wissensdatenbank.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Post(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void PublishKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Post(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            PublishKB();
            Console.ReadLine();
        }
    }
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

Der folgende Code ersetzt mithilfe der Methode zum [Ersetzen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace) die Inhalte einer angegebenen Wissensdatenbank.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string new_kb = @"
{
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
}
";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Put(string uri, String body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Put;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void ReplaceKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Put(uri, new_kb);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            ReplaceKB();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code können Sie die Inhalte einer angegebenen Datenbank über die Methode zum [Herunterladen einer Wissensdatenbank](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download) herunterladen.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/{0}/{1}/qna/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        // NOTE: Replace this with "test" or "prod".
        static string env = "test";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetQnA()
        {
            var method_with_id = String.Format(method, kb, env);
            var uri = host + service + method_with_id;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetQnA();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code können Sie mithilfe einer angegebenen Wissensdatenbank Antworten auf eine Frage erhalten, indem Sie die Methode zum **Generieren von Antworten** verwenden.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
1. Fügen Sie den unten stehenden Code hinzu.
1. Ersetzen Sie den `host`-Wert durch den Websitenamen für Ihr QnA Maker-Abonnement. Weitere Informationen finden Sie unter [Erstellen eines QnA Maker-Diensts](../How-To/set-up-qnamaker-service-azure.md).
1. Ersetzen Sie den `endpoint_key`-Wert durch einen für Ihr Abonnement gültigen Endpunktschlüssel. Beachten Sie, dass es sich dabei nicht um den Abonnementschlüssel handelt. Sie können Ihre Endpunktschlüssel über die Methode zum [Abrufen von Endpunktschlüsseln](#GetKeys) abrufen.
1. Ersetzen Sie den `kb`-Wert durch die ID der Wissensdatenbank, die Sie auf Antworten abfragen möchten. Beachten Sie, dass diese Wissensdatenbank bereits mithilfe der Methode zum [Veröffentlichen](#Publish) veröffentlicht worden sein muss.
1. Führen Sie das Programm aus.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

namespace QnAMaker
{
    class Program
    {
        // NOTE: Replace this with a valid host name.
        static string host = "ENTER HOST HERE";

        // NOTE: Replace this with a valid endpoint key.
        // This is not your subscription key.
        // To get your endpoint keys, call the GET /endpointkeys method.
        static string endpoint_key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        // Make sure you have published the knowledge base with the
        // POST /knowledgebases/{knowledge base ID} method.
        static string kb = "ENTER KB ID HERE";

        static string service = "/qnamaker";
        static string method = "/knowledgebases/" + kb + "/generateAnswer/";

        static string question = @"
{
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}
";

        async static Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpoint_key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetAnswers()
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Post(uri, question);
            Console.WriteLine(response);
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetAnswers();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code können Sie Informationen zur angegebenen Wissensdatenbank abrufen, indem Sie die Methode zum [Abrufen von Details zur Wissensdatenbank](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails) verwenden.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetKB();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code können Sie Informationen zu allen Wissensdatenbanken für einen angegebenen Benutzer abrufen, indem Sie die Methode zum [Abrufen Wissensdatenbank für Benutzer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/listall) verwenden.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetKBsByUser()
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetKBsByUser();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code werden die Inhalte einer angegebenen Datenbank mithilfe der Methode zum [Löschen von Wissensdatenbanken](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete) gelöscht.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Delete(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void DeleteKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Delete(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            DeleteKB();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code können Sie mithilfe der Methode zum [Abrufen von Endpunktschlüsseln](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys) die aktuellen Endpunktschlüssel abrufen.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/endpointkeys/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetEndpointKeys()
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetEndpointKeys();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code können Sie mithilfe der Methode zum [Aktualisieren von Endpunktschlüsseln](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/refreshkeys) die aktuellen Endpunktschlüssel aktualisieren.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/endpointkeys/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with "PrimaryKey" or "SecondaryKey."
        static string key_type = "PrimaryKey";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Patch(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = new HttpMethod("PATCH");
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void RefreshEndpoints()
        {
            var uri = host + service + method + key_type;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Patch(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            RefreshEndpoints();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code können Sie aktuelle Wortvarianten mithilfe der Methode zum [Herunterladen von Varianten](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/get) abrufen.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/alterations/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetAlterations()
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetAlterations();
            Console.ReadLine();
        }
    }
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

Über den folgenden Code können Sie die aktuellen Wortvarianten mithilfe der Methode zum [Ersetzen von Varianten](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) ersetzen.

1. Erstellen Sie in Ihrer bevorzugten IDE ein neues C#-Projekt.
2. Fügen Sie den unten stehenden Code hinzu.
3. Ersetzen Sie den `key`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
4. Führen Sie das Programm aus.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/alterations/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string alterations = @"
{
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
}
";

        public struct Response
        {
            public HttpResponseHeaders headers;
            public string response;

            public Response(HttpResponseHeaders headers, string response)
            {
                this.headers = headers;
                this.response = response;
            }
        }

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Put(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = new HttpMethod("PUT");
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void ReplaceAlterations(string alterations)
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Put(uri, alterations);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            ReplaceAlterations(alterations);
            Console.ReadLine();
        }
    }
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
