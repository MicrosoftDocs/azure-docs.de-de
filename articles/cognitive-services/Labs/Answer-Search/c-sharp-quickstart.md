---
title: 'Schnellstart: Projekt: Suche nach Antworten, C#'
titlesuffix: Azure Cognitive Services
description: 'Codebeispiel für die ersten Schritte mit dem „Projekt: Suche nach Antworten“ mit C#.'
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: dce13636cce299098c988ccea3a059f0f0e12acd
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823255"
---
# <a name="quickstart-project-answer-search-query-in-c"></a>Schnellstart: Project Answer Search-Abfrage in C#

Das folgende C#-Beispiel erstellt und sendet eine Abfrage für Informationen über den Fundamentalsatz der Analysis.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/), um diesen Code unter Windows ausführen zu können. (Die kostenlose Community Edition ist hierfür geeignet.)

Rufen Sie einen Zugriffsschlüssel für die kostenlose Testversion von [Cognitive Services Labs](https://aka.ms/answersearchsubscription) ab.

## <a name="code-scenario"></a>Codeszenario

Der folgende C#-Code erstellt und sendet die Abfrage. 

Er wird in den folgenden Schritten implementiert:
1. Deklarieren von Variablen zum Angeben des Endpunkts und Abfragen einer URL für die Vorschau  
2. Erstellen der Anforderung
3. Hinzufügen des *Ocp-Apim-Subscription-Key*-Headers 
4. Asynchrone Ausführung der Webanforderung 
5. Lesen der Antwort
6. Ausgeben der Header und JSON-Ergebnisse an die Konsole

**Quellcode**

```
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace Answers_csharp
{
    class Program
    {
        // Replace the accessKey string value with your valid access key.
        const string accessKey = "YOUR-SUBSCRIPTION-KEY";

        const string uriBase = "https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search"; 

        const string searchTerm = "third law of calculus"; 

        // Used to return news search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Does Bing knowledge search and returns the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Send the Web request and get the response.
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
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

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>Ausführen der Anwendung

So führen Sie die Anwendung aus:

1. Erstellen Sie eine neue Konsolenprojektmappe in Visual Studio
2. Ersetzen Sie `Program.cs` durch den bereitgestellten Code
3. Ersetzen Sie den `YOUR-ACCESS-KEY`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel
4. Führen Sie das Programm aus.

## <a name="next-steps"></a>Nächste Schritte
[Java-Schnellstart](java-quickstart.md)
