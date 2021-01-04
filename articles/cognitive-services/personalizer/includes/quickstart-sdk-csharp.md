---
title: include file
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: ffe7988dbdcf685ec98b42ea3b8272cd0a004aa5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371534"
---
[Referenzdokumentation](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Erstellen einer Personalisierungsressource"  target="_blank"> im Azure-Portal eine Personalisierungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Personalisierungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Erstellen Sie eine neue .NET Core-Anwendung in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE.

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl dotnet `new` zum Erstellen einer neuen Konsolen-App mit dem Namen `personalizer-quickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: `Program.cs`.

```console
dotnet new console -n personalizer-quickstart
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

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Personalisierungsclientbibliothek für .NET:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

> [!TIP]
> Bei Verwendung der Visual Studio-IDE ist die Clientbibliothek als herunterladbares NuGet-Paket verfügbar.

Öffnen Sie über das Projektverzeichnis die Datei `Program.cs` in Ihrem bevorzugten Editor oder in Ihrer bevorzugten IDE. Fügen Sie die folgenden using-Direktiven hinzu:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;
```

## <a name="object-model"></a>Objektmodell

Der Personalisierungsclient ist ein Objekt vom Typ [PersonalizerClient](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet), das mithilfe der Klasse „Microsoft.Rest.ServiceClientCredentials“, die Ihren Schlüssel enthält, bei Azure authentifiziert wird.

Erstellen Sie zum Anfordern des besten Inhaltselements eine Rangfolgeanforderung ([RankRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)), und übergeben Sie sie an die Methode [client.Rank](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview). Die Methode „Rank“ gibt eine Rangfolgeantwort (RankResponse) zurück.

Erstellen Sie eine Relevanzanforderung ([RewardRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)), und übergeben Sie sie an die Methode [client.Reward](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview), um eine Relevanzbewertung an die Personalisierung zu senden.

Im Rahmen dieser Schnellstartanleitung ist die Bestimmung der Relevanzbewertung ganz einfach. In einem Produktionssystem kann die Bestimmung der Einflussfaktoren für die [Relevanzbewertung](../concept-rewards.md) sowie deren jeweilige Gewichtung allerdings eine komplexe Angelegenheit sein und muss unter Umständen im Laufe der Zeit überarbeitet werden. Diese Entwurfsentscheidung sollte eine der wichtigsten Entscheidungen im Zusammenhang mit Ihrer Personalisierungsarchitektur sein.

## <a name="code-examples"></a>Codebeispiele

In diesen Codeausschnitten werden folgende Aufgaben mit der Personalisierungsclientbibliothek für .NET veranschaulicht:

* [Erstellen eines Personalisierungsclients](#authenticate-the-client)
* [Rangfolge-API](#request-the-best-action)
* [Relevanz-API](#send-a-reward)


## <a name="authenticate-the-client"></a>Authentifizieren des Clients

In diesem Abschnitt führen Sie zwei Aktionen aus:
* Angeben Ihres Schlüssels und Endpunkts
* Erstellen eines Personalisierungsclients

Fügen Sie zunächst der Program-Klasse die unten angegebenen Zeilen hinzu. Fügen Sie unbedingt den Schlüssel und Endpunkt Ihrer Personalisierungsressource hinzu.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
private static readonly string ApiKey = "REPLACE-WITH-YOUR-PERSONALIZER-KEY";
private static readonly string ServiceEndpoint = "https://REPLACE-WITH-YOUR-PERSONALIZER-RESOURCE-NAME.cognitiveservices.azure.com";
```

Fügen Sie als Nächstes dem Programm eine Methode hinzu, um einen neuen Personalisierungsclient zu erstellen.

```csharp
static PersonalizerClient InitializePersonalizerClient(string url)
{
    PersonalizerClient client = new PersonalizerClient(
        new ApiKeyServiceClientCredentials(ApiKey)) { Endpoint = url };

    return client;
}
```

## <a name="get-food-items-as-rankable-actions"></a>Abrufen von Lebensmitteln als Aktionen, denen ein Rang zugewiesen werden kann

Aktionen stellen die Inhaltsoptionen dar, aus denen die Personalisierung das beste Inhaltselement auswählen soll. Fügen Sie der Klasse „Program“ die folgenden Methoden hinzu, um die Gruppe der Aktionen und deren Features darzustellen. 

```csharp
static IList<RankableAction> GetActions()
{
    IList<RankableAction> actions = new List<RankableAction>
    {
        new RankableAction
        {
            Id = "pasta",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
        },

        new RankableAction
        {
            Id = "ice cream",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
        },

        new RankableAction
        {
            Id = "juice",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
        },

        new RankableAction
        {
            Id = "salad",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
        }
    };

    return actions;
}
```

## <a name="get-user-preferences-for-context"></a>Abrufen von Benutzerwünschen für den Kontext

Fügen Sie der Klasse „Program“ die folgenden Methoden hinzu, um die Benutzereingaben für Tageszeit und aktuellen Essenswunsch aus der Befehlszeile abzurufen. Diese werden als Kontextfeatures verwendet.

```csharp
static string GetUsersTimeOfDay()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetUsersTastePreference()
{
    string[] tasteFeatures = new string[] { "salty", "sweet" };

    Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
    if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
        tasteIndex = 1;
    }

    return tasteFeatures[tasteIndex - 1];
}
```

Bei beiden Methoden wird die Methode `GetKey` verwendet, um die Auswahl des Benutzers aus der Befehlszeile zu lesen.

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

## <a name="create-the-learning-loop"></a>Erstellen der Lernschleife

Die Lernschleife der Personalisierung ist ein Zyklus aus [Rangfolge-](#request-the-best-action) und [Relevanzaufrufen](#send-a-reward). In dieser Schnellstartanleitung folgt auf jeden Rangfolgeaufruf zur Personalisierung des Inhalts ein Relevanzaufruf, um der Personalisierung mitzuteilen, wie gut der Dienst abgeschnitten hat.

Der folgende Code durchläuft einen Zyklus in Form einer Schleife: Der Benutzer wird an der Befehlszeile nach seinen Präferenzen befragt, die Angaben werden zur Wahl der besten Aktion an die Personalisierung gesendet, die Auswahl wird dem Kunden in einer Auswahlliste angezeigt, und anschließend wird eine Relevanzbewertung an die Personalisierung gesendet, die angibt, wie gut die Wahl des Diensts war.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    IList<RankableAction> actions = GetActions();

    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        IList<string> excludeActions = new List<string> { "juice" };

        string eventId = Guid.NewGuid().ToString();

        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        client.Reward(response.EventId, new RewardRequest(reward));

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

Fügen Sie vor dem Ausführen der Codedatei die folgenden Methoden hinzu, die die [Inhaltsoptionen abrufen](#get-food-items-as-rankable-actions):

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Anfordern der besten Aktion

Für die Rangfolgeanforderung erfragt das Programm die Präferenzen des Benutzers, um für die Inhaltsoptionen ein Element vom Typ `currentContext` zu erstellen. Der Prozess kann Inhalte erstellen, die von den Aktionen ausgeschlossen werden sollen (angezeigt als `excludeActions`). Die Rangfolgeanforderung muss die Aktionen und ihre Features, die Features des aktuellen Kontexts (currentContext), die auszuschließenden Aktionen (excludeActions) und eine eindeutige Ereignis-ID enthalten, um die Antwort zu erhalten.

In dieser Schnellstartanleitung werden die einfachen Kontextmerkmale „Tageszeit“ und „Essenswunsch des Benutzers“ verwendet. In Produktionssystemen kann die Bestimmung und [Auswertung](../concept-feature-evaluation.md) von [Aktionen und Merkmalen](../concepts-features.md) allerdings eine komplexe Angelegenheit sein.

```csharp
string timeOfDayFeature = GetUsersTimeOfDay();
string tasteFeature = GetUsersTastePreference();

IList<object> currentContext = new List<object>() {
    new { time = timeOfDayFeature },
    new { taste = tasteFeature }
};

IList<string> excludeActions = new List<string> { "juice" };

string eventId = Guid.NewGuid().ToString();

var request = new RankRequest(actions, currentContext, excludeActions, eventId);
RankResponse response = client.Rank(request);
```

## <a name="send-a-reward"></a>Senden einer Relevanz

Um die Relevanzbewertung zu erhalten, die in der Relevanzanforderung gesendet wird, ruft das Programm die Auswahl des Benutzers aus der Befehlszeile ab, weist der Auswahl einen numerischen Wert zu und sendet dann die eindeutige Ereignis-ID und die Relevanzbewertung als numerischen Wert an die Relevanz-API.

In dieser Schnellstartanleitung wird als Relevanzbewertung eine einfache Zahl (0 oder 1) zugewiesen. In Produktionssystemen ist die Entscheidung, was wann an den [Relevanzaufruf](../concept-rewards.md) gesendet werden soll (abhängig von Ihren spezifischen Anforderungen), unter Umständen etwas komplizierter.

```csharp
float reward = 0.0f;
string answer = GetKey();

if (answer == "Y")
{
    reward = 1;
    Console.WriteLine("\nGreat! Enjoy your food.");
}
else if (answer == "N")
{
    reward = 0;
    Console.WriteLine("\nYou didn't like the recommended food choice.");
}
else
{
    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
}

Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
foreach (var rankedResponse in response.Ranking)
{
    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
}

// Send the reward for the action based on user response.
client.Reward(response.EventId, new RewardRequest(reward));
```

## <a name="run-the-program"></a>Ausführen des Programms

Führen Sie die Anwendung mit dem Befehl dotnet `run` über das Anwendungsverzeichnis aus.

```console
dotnet run
```

![Das Schnellstartprogramm stellt ein paar Fragen zum Sammeln von Benutzereinstellungen – auch bekannt als Features – und stellt dann die am besten bewertete Aktion bereit.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

Der [Quellcode für diese Schnellstartanleitung](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) ist verfügbar.