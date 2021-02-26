---
title: Signieren einer HTTP-Anforderung mit C#
description: Dies ist die C#-Version für das Signieren einer HTTP-Anforderung mit einer HMAC-Signatur für Communication Services.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 3c1b56f81e5164bbdfa94fdaeca5f5f1f55b3b51
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551873"
---
## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die folgenden Schritte aus, bevor Sie beginnen:
- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installieren von [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführliche Informationen finden Sie unter [Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource](../../quickstarts/create-communication-resource.md). Für dieses Tutorial benötigen Sie Ihren Ressourcenendpunkt (**resourceEndpoint**) und Ihren Ressourcenzugriffsschlüssel (**resourceAccessKey**).



## <a name="sign-an-http-request-with-c"></a>Signieren einer HTTP-Anforderung mit C#
Bei der Authentifizierung per Zugriffsschlüssel wird ein gemeinsamer geheimer Schlüssel verwendet, um eine HMAC-Signatur für jede HTTP-Anforderung zu generieren. Diese Signatur wird mit dem SHA256-Algorithmus generiert und im Header `Authorization` unter Verwendung des Schemas `HMAC-SHA256` gesendet. Beispiel:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature` umfasst Folgendes: 

- HTTP-Verb (beispielsweise `GET` oder `PUT`)
- HTTP-Anforderungspfad
- Datum
- Host
- x-ms-content-sha256

## <a name="setting-up"></a>Einrichten
In den folgenden Schritten wird beschrieben, wie Sie den Autorisierungsheader erstellen:

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `SignHmacTutorial`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie das Paket `Newtonsoft.Json` für die Textserialisierung:

```console
dotnet add package Newtonsoft.Json
```

Aktualisieren Sie die Deklaration der Methode `Main`, sodass asynchroner Code unterstützt wird. Verwenden Sie zum Einstieg den folgenden Code:

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>Erstellen einer Anforderungsnachricht

In diesem Beispiel wird eine Anforderung zum Erstellen einer neuen Identität mithilfe der Authentifizierungs-API (Version `2021-03-07`) von Communication Services signiert.

Fügen Sie der Methode `Main` den folgenden Code hinzu:

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Ersetzen Sie `resourceEndpoint` durch den Wert Ihres tatsächlichen Ressourcenendpunkts.

## <a name="create-content-hash"></a>Erstell des Inhaltshashs

Der Inhaltshash ist Teil Ihrer HMAC-Signatur. Verwenden Sie den folgenden Code, um den Inhaltshash zu berechnen. Diese Methode können Sie unter der Methode `Main` zu `Progam.cs` hinzufügen:

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Berechnen einer Signatur
Verwenden Sie den folgenden Code, um eine Methode zum Berechnen der HMAC-Signatur zu erstellen:

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Ersetzen Sie `resourceAccessKey` durch den Zugriffsschlüssel Ihrer tatsächlichen Azure Communication Services-Ressource.

## <a name="create-an-authorization-header-string"></a>Erstellen einer Autorisierungsheader-Zeichenfolge

In diesem Abschnitt wird die Zeichenfolge erstellt, die wir später unserem Autorisierungsheader hinzufügen:

1. Berechnen Sie einen Inhaltshash.
2. Geben Sie den UTC-Zeitstempel (koordinierte Weltzeit) an.
3. Bereiten Sie eine Zeichenfolge für die Signierung vor.
4. Berechnen Sie die Signatur.
5. Verketten Sie die Zeichenfolge für den Autorisierungsheader.
 
Fügen Sie der Methode `Main` den folgenden Code hinzu:

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Hinzufügen von Headern zu „requestMessage“

Verwenden Sie den folgenden Code, um Ihre Anforderungsnachricht (`requestMessage`) die erforderlichen Header hinzuzufügen:

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Testen des Clients
Rufen Sie den Endpunkt mithilfe von `HttpClient` auf, und überprüfen Sie die Antwort.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
