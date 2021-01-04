---
title: 'Verwenden von Web-Apps: Personalisierung'
description: Passen Sie eine C# .NET-Web-App mit einer Personalisierungsschleife an, um einem Benutzer basierend auf Aktionen (mit Features) und Kontextfeatures die richtigen Inhalte zu präsentieren.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c004887e3883ae711974b544510dff16a98d4ef9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363917"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Tutorial: Hinzufügen einer Personalisierung zu einer .NET-Web-App

Passen Sie eine C# .NET-Web-App mit einer Personalisierungsschleife an, um einem Benutzer basierend auf Aktionen (mit Features) und Kontextfeatures die richtigen Inhalte zu präsentieren.

**In diesem Tutorial lernen Sie Folgendes:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Einrichten von Schlüssel und Endpunkt für die Personalisierung
> * Sammeln von Features
> * Aufrufen von Rangfolge- und Belohnungs-APIs
> * Anzeigen der obersten Aktion, die als _rewardActionId_ gekennzeichnet wird



## <a name="select-the-best-content-for-a-web-app"></a>Auswählen des optimalen Inhalts für eine Web-App

Eine Web-App sollte Personalisierung verwenden, wenn auf der Webseite eine Liste von _Aktionen_ (Inhalte beliebiger Art) vorhanden ist, die für die Anzeige im Hinblick auf ein einzelnes oberstes Element (rewardActionId) personalisiert werden muss. Beispiele für solche Aktionslisten sind etwa Nachrichtenartikel, Positionen für die Schaltflächenplatzierung oder die gewählten Wörter für Produktnamen.

Sie senden die Liste der Aktionen zusammen mit Kontextmerkmalen an die Personalisierungsschleife. Die Personalisierung wählt die einzelne beste Aktion aus, anschließend zeigt Ihre Web-App diese Aktion an.

In diesem Tutorial werden die Aktionen durch Lebensmitteltypen dargestellt:

* Pasta
* Eiskrem
* Saft
* Salat
* Popcorn
* Kaffee
* Suppe

Um der Personalisierung dabei zu helfen, ihre Aktionen kennenzulernen, senden Sie mit jeder Anforderung der Rangfolge-API sowohl _Aktionen mit Features_ als auch _Kontextmerkmale_.

Ein **Feature** des Modells sind Informationen über die Aktion oder den Kontext, die über Mitglieder Ihrer Web-App-Benutzerbasis hinweg aggregiert sein können. Ein Feature ist _kein_ individuell spezifisches (wie eine Benutzer-ID) oder hochgradig spezifisches Merkmal (wie eine genaue Uhrzeit).

### <a name="actions-with-features"></a>Aktionen mit Features

Jede Aktion (Inhaltselement) weist Features (Merkmale) auf, die die Unterscheidung des Lebensmittels erleichtern.

Die Features werden nicht im Rahmen der Schleifenkonfiguration im Azure-Portal konfiguriert. Stattdessen werden sie zusammen mit jedem Aufruf der Rangfolge-API als JSON-Objekt gesendet. Auf diese Weise wird Flexibilität für die Aktionen und ihre Features erreicht, sodass sie im Lauf der Zeit zunehmen, sich ändern oder abnehmen können, wodurch die Personalisierung Trends folgen kann.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Kontextfeatures

Kontextfeatures helfen der Personalisierung dabei, den Kontext der Aktionen zu verstehen. Der Kontext für diese Beispielanwendung beinhaltet:

* Die Tageszeit: Morgen, Nachmittag, Abend, Nacht
* Die Geschmacksvorliebe des Benutzers: salzig, süß, bitter, sauer oder herzhaft
* Den Browserkontext: Benutzer-Agent, geografischen Standort, Verweiser

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Wie verwendet die Web-App die Personalisierung?

Die Web-App verwendet die Personalisierung, um in der Liste der zur Wahl stehenden Lebensmittel die beste Aktion auszuwählen. Sie tut dies, indem sie zusammen mit jedem Aufruf der Rangfolge-API die folgenden Informationen sendet:
* **Aktionen** mit ihren Features wie `taste` und `spiceLevel`
* **Kontextfeatures** wie `time` am Tag, `taste`-Präferenz des Benutzers und die Benutzer-Agentinformationen des Browsers sowie Kontextfeatures
* **Auszuschließende Aktionen** wie z. B. Saft
* **Ereignis-ID** , die sich für jeden Aufruf der Rangfolge-API unterscheidet

## <a name="personalizer-model-features-in-a-web-app"></a>Features von Personalisierungsmodellen in einer Web-App

Die Personalisierung benötigt Features für die Aktionen (Inhalte) und den aktuellen Kontext (Benutzer und Umgebung). Features werden verwendet, um Aktionen am aktuellen Kontext im Modell auszurichten. Das Modell ist eine Darstellung des Wissens der Personalisierung aus der Vergangenheit über Aktionen, Kontext und deren Features, die es ihr ermöglichen, fundierte Entscheidungen zu treffen.

Das Modell wird, einschließlich der Features, nach einem Zeitplan aktualisiert, der auf Ihrer Einstellung der **Aktualisierungsrate des Modells** im Azure-Portal basiert.

> [!CAUTION]
> Features sollen in dieser Anwendung Features und Werte von Features veranschaulichen, stellen aber nicht zwangsläufig die besten Features für den Einsatz in einer Web-App dar.

### <a name="plan-for-features-and-their-values"></a>Planen von Features und ihren Werten

Features sollten mit dem gleichen Maß an Planung und Überlegung ausgewählt werden, das Sie Schemas oder Modellen in Ihrer technischen Architektur widmen. Die Werte der Features können mithilfe von Geschäftslogik oder Drittanbietersystemen festgelegt werden. Featurewerte sollten nicht so hochgradig spezifisch sein, dass sie nicht für eine Gruppe oder Klasse von Features gelten.

### <a name="generalize-feature-values"></a>Generalisieren von Featurewerten

#### <a name="generalize-into-categories"></a>Generalisieren in Kategorien

Diese App verwendet `time` als Feature, gruppiert die Zeit aber in Kategorien wie `morning`, `afternoon`, `evening` und `night`. Dies ist ein Beispiel dafür, wie die Zeitinformation zwar verwendet wird, aber nicht in einer hochspezifischen Weise wie in `10:05:01 UTC+2`.

#### <a name="generalize-into-parts"></a>Generalisieren in Teile

Diese App verwendet die HTTP-Anforderungsfeatures vom Browser. Diese beginnen bei allen Daten mit einer sehr spezifischen Zeichenfolge, beispielsweise:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

Die **HttpRequestFeatures** -Klassenbibliothek generalisiert diese Zeichenfolge zu einem **userAgentInfo** -Objekt mit einzelnen Werten. Alle Werte, die zu spezifisch sind, werden auf eine leere Zeichenfolge festgelegt. Wenn die Kontextfeatures für die Anforderung gesendet werden, weisen sie das folgende JSON-Format auf:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Verwenden der Beispiel-Web-App

Für das Ausführen der browserbasierten Web-App (der Code steht vollständig zur Verfügung) müssen die folgenden Anwendungen installiert sein.

Installieren Sie folgende Software:

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1): Der Back-End-Beispielserver verwendet .NET Core.
* [Node.js](https://nodejs.org/): Der Client/das Front-End hängt von dieser Anwendung ab
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) oder [.NET Core CLI](/dotnet/core/tools/): Verwenden Sie entweder die Entwicklerumgebung von Visual Studio 2019 oder .NET Core CLI, um die App zu erstellen und auszuführen

### <a name="set-up-the-sample"></a>Einrichten des Beispiels
1. Klonen Sie das Beispielrepository der Azure-Personalisierung.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Navigieren Sie zu _samples/HttpRequestFeatures_ , um die Projektmappe `HttpRequestFeaturesExample.sln` zu öffnen.

    Erlauben Sie Visual Studio auf Anforderung, das .NET-Paket für die Personalisierung zu aktualisieren.

### <a name="set-up-azure-personalizer-service"></a>Einrichten des Azure-Personalisierungsdiensts

1. [Erstellen Sie im Azure-Portal eine Personalisierungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).

1. Suchen Sie im Azure-Portal auf der Registerkarte **Schlüssel und Endpunkte** den `Endpoint` und wahlweise `Key1` oder `Key2` (beide funktionieren). Diese stellen Ihren `PersonalizerServiceEndpoint` und Ihren `PersonalizerApiKey` dar.
1. Setzen Sie den `PersonalizerServiceEndpoint` in **appsettings.json** ein.
1. Konfigurieren Sie auf eine der folgenden Weisen den `PersonalizerApiKey` als ein [App-Geheimnis](/aspnet/core/security/app-secrets):

    * Wenn Sie die .NET Core CLI verwenden, können Sie den Befehl `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` verwenden.
    * Wenn Sie Visual Studio verwenden, können Sie mit der rechten Maustaste auf das Projekt klicken und die Menüoption **Geheime Benutzerschlüssel verwalten** verwenden, um die Schlüssel für die Personalisierung zu konfigurieren. Dadurch öffnet Visual Studio eine `secrets.json`-Datei, in der Sie die Schlüssel wie hier gezeigt hinzufügen können:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Erstellen Sie HttpRequestFeaturesExample mit einer der folgenden Methoden, und führen Sie es anschließend aus:

* Visual Studio 2019: Drücken Sie **F5**
* .NET Core CLI: `dotnet build` dann `dotnet run`

Über einen Webbrowser können Sie eine Rangfolge- und eine Belohnungsanforderung senden und deren Antworten sowie die aus Ihrer Umgebung extrahierten HTTP-Anforderungsfeatures anzeigen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Beispiel des HTTP-Anforderungsfeatures in einem Webbrowser](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Veranschaulichung der Personalisierungsschleife

1. Wählen Sie die Schaltfläche **Generate new Rank Request** (Neue Rangfolgenanforderung generieren) aus, um ein neues JSON-Objekt für den Aufruf der Rangfolgen-API zu erstellen. Dadurch werden die Aktionen (mit Features) und Kontextfeatures erstellt und die Werte angezeigt, sodass Sie sehen können, wie der JSON-Code aussieht.

    Für Ihre eigene zukünftige Anwendung kann die Generierung der Aktionen und Features auf dem Client, auf dem Server, gemischt auf beiden oder durch Aufrufen von anderen Diensten erfolgen.

1. Wählen Sie **Send Rank Request** (Rangfolgenanforderung senden) aus, um das JSON-Objekt an den Server zu senden. Der Server ruft die Rangfolgen-API für die Personalisierung auf. Der Server empfängt die Antwort und gibt die Aktion mit der höchsten Rangfolge zum Anzeigen an den Client zurück.

1. Legen Sie den Belohnungswert fest, und wählen Sie dann die Schaltfläche **Send Reward Request** (Belohnungsanforderung senden) aus. Wenn Sie den Belohnungswert nicht ändern, sendet die Clientanwendung immer den Wert `1` an die Personalisierung.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Abschnitt zur Belohnungsanforderung](./media/tutorial-web-app/reward-score-api-call.png)

    Bei Ihrer eigenen zukünftigen Anwendung kann die Generierung des Belohnungsergebnisses nach dem Sammeln von Informationen über das Benutzerverhalten auf dem Client zusammen mit Geschäftslogik auf dem Server erfolgen.

## <a name="understand-the-sample-web-app"></a>Verstehen der Beispiel-Web-App

Die Beispiel-Web-App verfügt über einen **C# .NET** -Server, der das Sammeln von Features und das Senden und Empfangen von HTTP-Aufrufen an Ihren Personalisierungsendpunkt verwaltet.

Die Beispiel-Web-App verwendet eine **Knockout-Front-End-Clientanwendung** , um Features zu erfassen und Aktionen der Benutzeroberfläche wie das Klicken auf Schaltflächen zu verarbeiten sowie Daten an den .NET-Server zu senden.

In den folgenden Abschnitten werden die Teile von Server und Client erläutert, die ein Entwickler verstehen muss, um die Personalisierung zu verwenden.

## <a name="rank-api-client-application-sends-context-to-server"></a>Rangfolge-API: Clientanwendung sendet Kontext an Server

Die Clientanwendung erfasst den _Benutzer-Agent_ auf dem Browser des Benutzers.

> [!div class="mx-imgBorder"]
> ![Erstellen und Ausführen des HTTPRequestFeaturesExample-Projekts. Ein Browserfenster wird geöffnet und zeigt die Einzelseitenanwendung ](./media/tutorial-web-app/user-agent.png) an.

## <a name="rank-api-server-application-calls-personalizer"></a>Rangfolge-API: Serveranwendung ruft Personalisierung auf

Hier handelt es sich um eine typische .NET-Web-App mit einer Clientanwendung, ein großer Teil des Boiler Plate-Codes wird für Sie bereitgestellt. Jeglicher nicht für die Personalisierung spezifischer Code wurde aus den Codeausschnitten entfernt, Sie können sich also auf den spezifischen Code der Personalisierung konzentrieren.

### <a name="create-personalizer-client"></a>Erstellen eines Personalisierungsclients

In der **Startup.cs** -Datei des Servers werden Endpunkt und Schlüssel der Personalisierung verwendet, um den Personalisierungsclient zu erstellen. Die Clientanwendung braucht nicht mit der Personalisierung in dieser App zu kommunizieren, sie kann sich stattdessen darauf verlassen, dass der Server die SDK-Aufrufe vornimmt.

Der .NET-Startcode des Webservers lautet:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Auswählen der besten Aktion

In der Datei **PersonalizerController.cs** des Servers fasst die **GenerateRank** -Server-API die Vorbereitung für den Aufruf der Rangfolge-API zusammen.

* Erstellen einer neuen `eventId` für den Aufruf der Rangfolge-API
* Abrufen der Liste der Aktionen
* Abrufen der Liste der Features vom Benutzer und Erstellen der Kontextfeatures
* Optional Festlegen aller ausgeschlossenen Aktionen
* Aufrufen der Rangfolge-API, Rückgabe der Ergebnisse an den Client

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

Der an die Personalisierung gesendete JSON-Code, der beide Aktionen (mit Features) und die aktuellen Kontextfeatures enthält, sieht so aus:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Rückgabe der „rewardActionId“ der Personalisierung an den Client

Die Rangfolge-API gibt die ausgewählte beste Aktion **rewardActionId** an den Server zurück.

Die in **rewardActionId** zurückgegebene Aktion wird angezeigt.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Anzeigen der Aktion „rewardActionId“ im Client

In diesem Tutorial wird der Wert von `rewardActionId` angezeigt.

In ihrer eigenen zukünftigen Anwendung kann es sich um einen genauen Text, eine Schaltfläche oder einen hervorgehobenen Abschnitt der Webseite handeln. Die Liste wird für eine eventuelle Analyse nach der Bewertung zurückgegeben, nicht für eine Ordnung der Inhalte. Es sollte nur der `rewardActionId`-Inhalt angezeigt werden.

## <a name="reward-api-collect-information-for-reward"></a>Belohnungs-API: Sammeln der Informationen für die Belohnung

Das [Belohnungsergebnis](concept-rewards.md) sollte sorgfältig geplant werden, genau wie die Features geplant werden. Das Belohnungsergebnis sollte typischerweise ein Wert zwischen 0 und 1 sein. Der Wert _kann_ zum Teil in der Clientanwendung berechnet werden, ausgehend vom Benutzerverhalten, und zum Teil auf der Server, basierend auf Geschäftslogik und Zielen.

Wenn der Server die Belohnungs-API nicht innerhalb der im Azure-Portal für Ihre Personalisierungsressource konfigurierten **Belohnungswartezeit** aufruft, wird die **Standardbelohnung** (ebenfalls im Azure-Portal konfiguriert) für das betreffende Ereignis verwendet.

In dieser Beispielanwendung können Sie einen Wert auswählen, um zu sehen, welchen Einfluss die Belohnung auf die Auswahl hat.

## <a name="additional-ways-to-learn-from-this-sample"></a>Weitere Lerninhalte in diesem Beispiel

Das Beispiel verwendet verschiedene zeitbasierte Ereignisse, die im Azure-Portal für Ihre Personalisierungsressource konfiguriert sind. Experimentieren Sie mit diesen Werten, und kehren Sie dann zu dieser Beispielwebanwendung zurück, um zu sehen, wie sich die Änderungen auf die Rangfolge- und Belohnungsaufrufe auswirken:

* Belohnungswartezeit
* Häufigkeit der Modellaktualisierung

Dies sind weitere Einstellungen, mit denen Sie experimentieren können:
* Standardbelohnung
* Prozentsatz der Untersuchung


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie dieses Tutorial abgeschlossen haben, bereinigen Sie die folgenden Ressourcen:

* Löschen Sie Ihr Beispielprojektverzeichnis.
* Löschen Sie Ihre Personalisierungsressource – stellen Sie sich die Personalisierungsressource als für Aktionen und Kontext dediziert vor – verwenden Sie die Ressource nur dann wieder, wenn Sie die Lebensmittel weiterhin als Themendomäne für Aktionen verwenden.


## <a name="next-steps"></a>Nächste Schritte
* [Funktionsweise der Personalisierung](how-personalizer-works.md)
* [Features](concepts-features.md): Konzepte zur Verwendung von Feature mit Aktionen und Kontext
* [Belohnungen](concept-rewards.md): Mehr über das Berechnen von Belohnungen