---
title: 'Schnellstart: Projekt: URL-Vorschau, C#'
titlesuffix: Azure Cognitive Services
description: 'Erste Schritte mit dem „Projekt: URL-Vorschau“ mit C#.'
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 2cb3f1de803b8549a88866da1042f4f32609a601
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827160"
---
# <a name="quickstart-url-preview-query-in-c"></a>Schnellstart: URL-Vorschauabfrage in C#

Im folgenden C#-Beispiel wird eine URL-Vorschau für die SwiftKey-Website erstellt: https://swiftkey.com/en.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen [Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/), um diesen Code unter Windows ausführen zu können. (Die kostenlose Community Edition ist hierfür geeignet.)

Rufen Sie einen Zugriffsschlüssel für die kostenlose Testversion von [Cognitive Services Labs](https://aka.ms/answersearchsubscription) ab.

## <a name="code-scenario"></a>Codeszenario

Der folgende C#-Code erstellt eine URL-Vorschau der SwiftKey-Website: https://swiftkey.com/en. 

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
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
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
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
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
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
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
- [Java-Schnellstart](java-quickstart.md)
- [JavaScript-Schnellstart](javascript.md)
- [Node-Schnellstart](node-quickstart.md)
- [Python-Schnellstart](python-quickstart.md)
