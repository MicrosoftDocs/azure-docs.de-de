---
title: 'Clientbibliothek für die Bing-Vorschlagssuche für C#: Schnellstart'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 9d6d65ea7255528146b144c4bfa35e98b10b4055
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371725"
---
Beginnen Sie mit dem Einsatz der Clientbibliothek für die Bing-Vorschlagssuche für .NET. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben.

Verwenden Sie die Clientbibliothek für die Bing-Vorschlagssuche für .NET, um Suchvorschläge basierend auf Abfragen mit Teilzeichenfolgen zu erhalten.

[Referenzdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/cognitive-services).
* Aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Erstellen von Umgebungsvariablen

>[!NOTE]
> Die nach dem 1. Juli 2019 erstellten Endpunkte für Ressourcen nutzen das unten gezeigte benutzerdefinierte Format für Subdomänen. Weitere Informationen und eine vollständige Liste mit regionalen Endpunkten finden Sie unter [Benutzerdefinierte Unterdomänennamen für Cognitive Services](../../../cognitive-services-custom-subdomains.md).

Erstellen Sie unter Verwendung des Schlüssels und des Endpunkts der von Ihnen erstellten Ressource zwei Umgebungsvariablen für die Authentifizierung:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: Der Ressourcenschlüssel zum Authentifizieren Ihrer Anforderungen.
* `AUTOSUGGEST_ENDPOINT`: Der Ressourcenendpunkt zum Senden von API-Anforderungen. Diese sollte wie folgt aussehen: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`.

Führen Sie die Schritte für Ihr Betriebssystem aus:
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Starten Sie das Konsolenfenster neu, nachdem Sie die Umgebungsvariable hinzugefügt haben.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source ~/.bashrc` aus, damit die Änderungen wirksam werden.

### <a name="macos"></a>[macOS](#tab/unix)

Bearbeiten Sie `.bash_profile`, und fügen Sie die Umgebungsvariable hinzu:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source .bash_profile` aus, damit die Änderungen wirksam werden.
***

## <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Erstellen Sie eine neue .NET Core-Anwendung in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE.

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `bing-autosuggest-quickstart`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*.

```console
dotnet new console -n bing-autosuggest-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Öffnen Sie über das Projektverzeichnis die Datei *program.cs* in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Fügen Sie die folgenden `using`-Anweisungen hinzu:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

Erstellen Sie in der `Program`-Klasse Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. Wenn Sie die Umgebungsvariable nach dem Start der Anwendung erstellt haben, müssen der Editor, die IDE oder die Shell, in dem bzw. der sie ausgeführt wird, geschlossen und erneut geöffnet werden, damit der Zugriff auf die Variable möglich ist.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

Fügen Sie in der `Main`-Methode der Anwendung die folgenden Methodenaufrufe hinzu (diese definieren Sie später):

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

## <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Clientbibliothek für die Bing-Vorschlagssuche für .NET:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Bei Verwendung der Visual Studio-IDE ist die Clientbibliothek als herunterladbares NuGet-Paket verfügbar.

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie folgende Vorgänge mit der Clientbibliothek für die Bing-Vorschlagssuche für .NET ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Senden einer Anforderung für die Vorschlagssuche](#send-an-autosuggest-request)

### <a name="authenticate-the-client"></a>Authentifizieren des Clients

> [!NOTE]
> In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie eine [Umgebungsvariable](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) namens `AUTOSUGGEST_SUBSCRIPTION_KEY` für Ihren Schlüssel für die Bing-Vorschlagssuche sowie eine weitere Umgebungsvariable namens `AUTOSUGGEST_ENDPOINT` für Ihren Endpunkt erstellt haben.


Instanziieren Sie in einer neuen asynchronen Methode einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet)-Objekt mit Ihrem Schlüssel, und verwenden Sie dieses mit Ihrem Endpunkt, um ein [AutosuggestClient](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet)-Objekt zu erstellen.

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

### <a name="send-an-autosuggest-request"></a>Senden einer Anforderung für die Vorschlagssuche

Verwenden Sie in derselben Methode die [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_)-Methode des Clients, um eine Abfrage an Bing zu senden. Durchlaufen Sie dann die [Suggestions](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet)-Antwort, und geben Sie den ersten Vorschlag aus.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind:

* [Löschen einer Ressourcengruppe im Azure-Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Löschen einer Ressourcengruppe über die Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial für die Bing-Vorschlagssuche](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Weitere Informationen

- [Worum handelt es sich bei der Bing-Vorschlagssuche?](../../get-suggested-search-terms.md)
- [Referenz für die Bing-Vorschlagssuche für .NET](/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)