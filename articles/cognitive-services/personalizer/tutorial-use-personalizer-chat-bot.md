---
title: 'Verwenden der Personalisierung in einem Chatbot: Personalisierung'
description: Passen Sie einen C# .NET-Chatbot mit einer Personalisierungsschleife an, um einem Benutzer basierend auf Aktionen (mit Features) und Kontextfeatures die richtigen Inhalte bereitzustellen.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7c4920eaa7a5619be37d38afd763e7be416d3124
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565720"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Tutorial: Verwenden der Personalisierung in einem .NET-Chatbot

Verwenden Sie einen C# .NET-Chatbot mit einer Personalisierungsschleife, um einem Benutzer die richtigen Inhalte bereitzustellen. Dieser Chatbot schlägt einem Benutzer einen bestimmten Kaffee oder Tee vor. Der Benutzer kann diesen Vorschlag annehmen oder ablehnen. Dadurch erhält die Personalisierung Informationen, die es ihr ermöglichen, beim nächsten Mal einen passenderen Vorschlag zu machen.

**In diesem Tutorial lernen Sie Folgendes:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Einrichten von Azure-Ressourcen
> * Konfigurieren und Ausführen eines Bots
> * Interagieren mit dem Bot mithilfe von Bot Framework Emulator
> * Verstehen, wo und wie der Bot die Personalisierung verwendet


## <a name="how-does-the-chat-bot-work"></a>Wie funktioniert der Chatbot?

Ein Chatbot führt in der Regel eine Unterhaltung mit einem Benutzer. Dieser spezielle Chatbot verwendet Personalisierung, um die beste Aktion (Kaffee oder Tee) für den Benutzer auszuwählen. Die Personalisierung nutzt vertiefendes Lernen, um diese Entscheidung zu treffen.

Der Chatbot muss in der Unterhaltung Turns verwalten. Er verwaltet die Bot-Architektur und die Unterhaltung mithilfe von [Bot Framework](https://github.com/microsoft/botframework-sdk). Außerdem verwendet der Bot das Cognitive Services-Angebot [Language Understanding](../LUIS/index.yml) (LUIS), um die Absicht der natürlichen Sprache des Benutzers zu verstehen.

Bei dem Chatbot handelt es sich um eine Website mit einer bestimmten Route für die Beantwortung von Anfragen: `http://localhost:3978/api/messages`. Während der lokalen Bot-Entwicklung können Sie den Bot-Emulator verwenden, um visuell mit dem ausgeführten Chatbot zu interagieren.

### <a name="user-interactions-with-the-bot"></a>Benutzerinteraktionen mit dem Bot

Hierbei handelt es sich um einen einfachen Chatbot, der die Eingabe von Textabfragen ermöglicht.

|Texteingabe des Benutzers|Textantwort des Bots|Beschreibung der Bot-Aktion zur Bestimmung des Antworttexts|
|--|--|--|
|Keine Texteingabe; Bot beginnt Unterhaltung|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|Der Bot beginnt die Unterhaltung mit einer Anleitung und Informationen zum Kontext: `Tuesday`, `Snowy`.|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Der Bot ermittelt die Absicht der Abfrage mithilfe von LUIS und zeigt anschließend Menüoptionen für Kaffee und Tee an. Features der Aktionen: |
|`What do you suggest`|`How about Latte?`|Der Bot ermittelt die Absicht der Abfrage mithilfe von LUIS. Anschließend ruft er die **Rangfolge-API** auf und zeigt die beste Option als Frage an: `How about {response.RewardActionId}?`. Zur Veranschaulichung zeigt er außerdem den JSON-Aufruf und die Antwort an.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Der Bot ermittelt die Absicht der Abfrage mithilfe von LUIS. Anschließend ruft er die **Relevanz-API** mit der Relevanz `1` auf und zeigt zur Veranschaulichung den JSON-Aufruf und die Antwort an.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Der Bot ermittelt die Absicht der Abfrage mithilfe von LUIS. Anschließend ruft er die **Relevanz-API** mit der Relevanz `0` auf und zeigt zur Veranschaulichung den JSON-Aufruf und die Antwort an.|
|`Reset`|Der Bot gibt Anweisungstext zurück.|Der Bot ermittelt die Absicht der Abfrage mithilfe von LUIS. Anschließend zeigt er die Anleitung an und setzt den Kontext zurück.|


### <a name="personalizer-in-this-bot"></a>Personalisierung in diesem Bot

Dieser Chatbot verwendet die Personalisierung, um basierend auf einer Liste von _Aktionen_ (einem Inhaltstyp) und Kontextfeatures die beste Aktion (bestimmter Kaffee oder Tee) auszuwählen.

Die Liste der Aktionen wird zusammen mit Kontextfeatures an die Personalisierungsschleife gesendet. Die Personalisierung gibt die beste Aktion an Ihren Bot zurück, der sie daraufhin anzeigt.

In diesem Tutorial handelt es sich bei den **Aktionen** um Arten von Kaffee und Tee:

|Kaffee|Tee|
|--|--|
|Cappuccino<br>Espresso<br>Latte<br>Mocha|GreenTea<br>Rooibos|

**Rangfolge-API:** Damit die Personalisierung Informationen zu Ihren Aktionen erhält, sendet der Bot bei jeder Rang-API-Anforderung Folgendes:

* Aktionen _mit Features_
* Kontextfeatures

Bei einem **Feature** des Modells handelt es sich um Informationen zur Aktion oder zum Kontext, die über die Mitglieder Ihrer Chatbot-Benutzerbasis hinweg aggregiert werden können. Ein Feature ist _kein_ individuell spezifisches (wie eine Benutzer-ID) oder hochgradig spezifisches Merkmal (wie eine genaue Uhrzeit).

Features werden verwendet, um Aktionen am aktuellen Kontext im Modell auszurichten. Das Modell ist eine Darstellung des Wissens der Personalisierung aus der Vergangenheit über Aktionen, Kontext und deren Features, die es ihr ermöglichen, fundierte Entscheidungen zu treffen.

Das Modell wird, einschließlich der Features, nach einem Zeitplan aktualisiert, der auf Ihrer Einstellung der **Aktualisierungsrate des Modells** im Azure-Portal basiert.

Features sollten mit dem gleichen Maß an Planung und Überlegung ausgewählt werden, das Sie Schemas oder Modellen in Ihrer technischen Architektur widmen. Die Werte der Features können mithilfe von Geschäftslogik oder Drittanbietersystemen festgelegt werden.

> [!CAUTION]
> Die Features in dieser Anwendung dienen zur Veranschaulichung und stellen nicht unbedingt die besten Features dar, die Sie in Ihrer Web-App für Ihren Anwendungsfall verwenden können.

#### <a name="action-features"></a>Aktionsfeatures

Jede Aktion (Inhaltselement) weist Features (Merkmale) auf, die die Unterscheidung des Kaffee- oder Teeelements erleichtern.

Die Features werden nicht im Rahmen der Schleifenkonfiguration im Azure-Portal konfiguriert. Stattdessen werden sie zusammen mit jedem Aufruf der Rangfolge-API als JSON-Objekt gesendet. Auf diese Weise wird Flexibilität für die Aktionen und ihre Features erreicht, sodass sie im Lauf der Zeit zunehmen, sich ändern oder abnehmen können, wodurch die Personalisierung Trends folgen kann.

Features für Kaffee und Tee wären beispielsweise:

* Herkunftsland der Kaffeebohne (beispielsweise Kenia oder Brasilien)
* Handelt es sich um Kaffee oder Tee aus biologischem Anbau?
* Helle oder dunkle Röstung des Kaffees

Die obige Liste enthält drei Features für Kaffee, aber nur eins für Tee. Übergeben Sie nur Features an die Personalisierung, die für die Aktion sinnvoll sind. Übergeben Sie keinen leeren Wert für ein Feature, wenn es für die Aktion nicht relevant ist.

#### <a name="context-features"></a>Kontextfeatures

Mithilfe von Kontextfeatures kann die Personalisierung den Kontext der Umgebung nachvollziehen – etwa anhand des Anzeigegeräts, des Benutzers, des Standorts oder anderer Features, die für Ihren Anwendungsfall relevant sind.

Der Kontext für diesen Chatbot umfasst Folgendes:

* Art des Wetters (verschneit, regnerisch, sonnig)
* Wochentag

Die Featureauswahl erfolgt in diesem Chatbot nach dem Zufallsprinzip. Verwenden Sie bei einem echten Bot echte Daten für Ihre Kontextfeatures.

### <a name="design-considerations-for-this-bot"></a>Überlegungen zum Entwurf für diesen Bot

Im Zusammenhang mit dieser Unterhaltung sind ein paar Dinge zur berücksichtigen:
* **Bot-Interaktion:** Die Unterhaltung ist sehr einfach gehalten, da Rangfolge und Relevanz anhand eines einfachen Anwendungsfalls demonstriert werden. Sie zeigt nicht den vollen Funktionsumfang des Bot Framework SDK oder des Emulators.
* **Personalisierung:** Die Features werden nach dem Zufallsprinzip ausgewählt, um die Nutzung zu simulieren. In einem für die Produktion vorgesehenen Personalisierungsszenario dürfen Features nicht zufällig festgelegt werden.
* **Language Understanding (LUIS):** Die wenigen Beispieläußerungen des LUIS-Modells sind nur für dieses Beispiel gedacht. Verwenden Sie nicht so wenige Beispieläußerungen in Ihrer LUIS-Anwendung für die Produktion.


## <a name="install-required-software"></a>Installieren der erforderlichen Software
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Das herunterladbare Beispielrepository enthält entsprechende Anweisungen, falls Sie lieber die .NET Core-CLI verwenden möchten.
- [Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator) ist eine Desktopanwendung, mit der Bot-Entwickler ihre Bots auf „localhost“ oder per Remoteausführung über einen Tunnel testen und debuggen können.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Herunterladen des Beispielcodes für den Chatbot

Der Chatbot steht im Repository mit den Personalisierungsbeispielen zur Verfügung. Klonen Sie das Repository, oder [laden Sie es herunter](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip), und öffnen Sie anschließend das Beispiel im Verzeichnis `/samples/ChatbotExample` mit Visual Studio 2019.

Verwenden Sie zum Klonen des Repositorys den folgenden Git-Befehl in einer Bash-Shell (Terminal):

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Erstellen und Konfigurieren der Personalisierungs- und LUIS-Ressourcen

### <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Um diesen Chatbot verwenden zu können, müssen Azure-Ressourcen für die Personalisierung und für Language Understanding (LUIS) erstellt werden.

* [Erstellen Sie LUIS-Ressourcen.](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) Wählen Sie im Erstellungsschritt die Option **Beide** aus, da Sie sowohl Erstellungs- als auch Vorhersageressourcen benötigen.
* [Erstellen Sie die Personalisierungsressource](how-to-create-resource.md), und kopieren Sie den Schlüssel und den Endpunkt aus dem Azure-Portal. Diese Werte müssen in der Datei `appsettings.json` des .NET-Projekts festgelegt werden.

### <a name="create-luis-app"></a>Erstellen der LUIS-App

Sollten Sie noch nicht mit LUIS gearbeitet haben, müssen Sie sich [anmelden](https://www.luis.ai) und sofort Ihr Konto migrieren. Sie müssen keine neuen Ressourcen erstellen. Wählen Sie stattdessen die Ressourcen aus, die Sie im vorherigen Abschnitt dieses Tutorials erstellt haben.

1. Wählen Sie zum Erstellen einer neuen LUIS-Anwendung im [LUIS-Portal](https://www.luis.ai) Ihr Abonnement und Ihre Erstellungsressource aus.
1. Wählen Sie auf der gleichen Seite **+ Neue App für Unterhaltung** und anschließend **Als JSON importieren** aus.
1. Wählen Sie im Popupdialogfeld die Option **Datei auswählen** und anschließend die Datei `/samples/ChatbotExample/CognitiveModels/coffeebot.json` aus. Geben Sie den Namen `Personalizer Coffee bot` ein.
1. Wählen Sie rechts oben im Navigationsbereich des LUIS-Portals die Schaltfläche **Trainieren** aus.
1. Wählen Sie die Schaltfläche **Veröffentlichen** aus, um die App im **Produktionsslot** für die Vorhersageruntime zu veröffentlichen.
1. Wählen Sie **Verwalten** > **Einstellungen** aus. Kopieren Sie den Wert der **App-ID**. Dieser Wert muss in der Datei `appsettings.json` des .NET-Projekts festgelegt werden.
1. Wählen Sie im Abschnitt **Verwalten** die Option **Azure-Ressourcen** aus. Daraufhin werden die der App zugeordneten Ressourcen angezeigt.
1. Wählen Sie **Vorhersageressource hinzufügen** aus. Wählen Sie im Popupdialogfeld Ihr Abonnement und die weiter oben in diesem Tutorial erstellte Vorhersageressource aus. Wählen Sie anschließend **Fertig** aus.
1. Kopieren Sie die Werte für **Primärschlüssel** und **Endpunkt-URL**. Diese Werte müssen in der Datei `appsettings.json` des .NET-Projekts festgelegt werden.

### <a name="configure-bot-with-appsettingsjson-file"></a>Konfigurieren des Bots mit der Datei „appsettings.json“

1. Öffnen Sie die Chatbot-Projektmappendatei (`ChatbotSamples.sln`) mit Visual Studio 2019.
1. Öffnen Sie `appsettings.json` im Stammverzeichnis des Projekts.
1. Legen Sie alle fünf Einstellungen fest, die Sie im vorherigen Abschnitt dieses Tutorials kopiert haben.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>Erstellen und Ausführen des Bots

Nachdem Sie die Datei `appsettings.json` konfiguriert haben, können Sie den Chatbot erstellen und ausführen. Daraufhin wird ein Browser mit der Ausführungswebsite (`http://localhost:3978`) geöffnet.

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Screenshot: Browser mit Chatbot-Website":::

Lassen Sie die Website geöffnet, um mit dem Bot interagieren zu können, während in diesem Tutorial die Aktionen des Bots erläutert werden.


## <a name="set-up-the-bot-emulator"></a>Einrichten des Bot-Emulators

1. Öffnen Sie Bot Framework Emulator, und wählen Sie **Open Bot** (Bot öffnen) aus.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Screenshot: Startbildschirm des Bot-Emulators":::


1. Konfigurieren Sie den Bot mit der folgenden **Bot-URL**, und wählen Sie anschließend **Verbinden** aus:

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Screenshot: Bot-Emulator mit geöffneten Bot-Einstellungen":::

    Der Emulator stellt eine Verbindung mit dem Chatbot her und zeigt die Anleitung sowie Protokollierungs- und Debuginformationen an, die für die lokale Entwicklung hilfreich sind.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Screenshot: Bot-Emulator im ersten Turn der Unterhaltung":::

## <a name="use-the-bot-in-the-bot-emulator"></a>Verwenden des Bots im Bot-Emulator

1. Bitten Sie durch Eingabe von `I would like to see the menu` um die Karte. Der Chatbot zeigt die Elemente an.
1. Geben Sie `Please suggest a drink for me.` ein, um den Bot ein Element vorschlagen zu lassen. Der Emulator zeigt die Rangfolgeanforderung und die Antwort im Chatfenster an, damit Sie den vollständigen JSON-Code nachvollziehen können. Außerdem macht der Bot einen Vorschlag (beispielsweise `How about Latte?`).
1. Antworten Sie mit `I like it.`, um zu signalisieren, dass Ihnen dieser Vorschlag gefällt und sie die am höchsten bewertete Auswahloption der Personalisierung akzeptieren. Der Emulator zeigt die Relevanzanforderung mit der Relevanzbewertung 1 sowie die Antwort im Chatfenster an, damit Sie und Response im Chatfenster anzeigt, damit Sie den vollständigen JSON-Code nachvollziehen können. Der Bot antwortet mit `That’s great! I’ll keep learning your preferences over time.` und `Would you like to get a new suggestion or reset the simulated context to a new day?`.

    Wenn Sie auf die Auswahl mit `no` antworten, wird die Relevanzbewertung 0 an die Personalisierung gesendet.


## <a name="understand-the-net-code-using-personalizer"></a>Grundlegendes zum .NET-Code mit Personalisierung

Die .NET-Lösung ist ein einfacher Bot Framework-Chatbot. Der mit der Personalisierung zusammenhängende Code befindet sich in den folgenden Ordnern:
* `/samples/ChatbotExample/Bots`
    * Datei `PersonalizerChatbot.cs` für die Interaktion zwischen Bot und Personalisierung
* `/samples/ChatbotExample/ReinforcementLearning`: Verwaltet die Aktionen und Features für das Personalisierungsmodell
* `/samples/ChatbotExample/Model`: Dateien für die Personalisierungsaktionen und -features sowie für die LUIS-Absichten

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs: Verwenden der Personalisierung

Die Klasse `PersonalizerChatbot` leitet sich von `Microsoft.Bot.Builder.ActivityHandler` ab. Sie verfügt über drei Eigenschaften und Methoden zur Verwaltung des Unterhaltungsflusses.

> [!CAUTION]
> Kopieren Sie keinen Code aus diesem Tutorial. Verwenden Sie den Beispielcode aus dem [Repository mit den Personalisierungsbeispielen](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

Die Methoden mit dem Präfix `Send` dienen zur Verwaltung der Unterhaltung mit dem Bot und mit LUIS. Die Methoden `ChooseRankAsync` und `RewardAsync` interagieren mit der Personalisierung.

#### <a name="calling-rank-api-and-display-results"></a>Aufrufen der Rangfolge-API und Anzeigen der Ergebnisse

Die Methode `ChooseRankAsync` dient zum Erstellen der JSON-Daten, die an die Rangfolge-API der Personalisierung gesendet werden. Hierzu werden die Aktionen mit Features sowie die Kontextfeatures erfasst.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Aufrufen der Relevanz-API und Anzeigen der Ergebnisse

Die Methode `RewardAsync` dient zum Erstellen der JSON-Daten, die an die Relevanz-API der Personalisierung gesendet werden. Hierzu wird die Bewertung bestimmt. Zur Bestimmung der Bewertung wird die LUIS-Absicht herangezogen, die im Text des Benutzers erkannt wird mithilfe der Methode `OnTurnAsync` gesendet wurde.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Überlegungen zum Entwurf für einen Bot

In diesem Beispiel wird eine einfache End-to-End-Lösung der Personalisierung in einem Bot gezeigt. Ihr Anwendungsfall kann komplexer sein.

Wenn Sie die Personalisierung in einem Bot für die Produktion verwenden möchten, planen Sie Folgendes ein:
* Echtzeitzugriff auf die Personalisierung, _wann immer_ Sie eine Auswahl mit Rangfolge benötigen. Die Rangfolge-API ist weder batch- noch cachefähig.  Der Relevanzaufruf kann verzögert oder in einen separaten Prozess ausgelagert werden. Wenn Sie nicht rechtzeitig eine Relevanz zurückgeben, wird für das Ereignis ein Standardrelevanzwert festgelegt.
* Anwendungsfallbasierte Berechnung der Relevanz: In diesem Beispiel wurden zwei Relevanzwerte (0 und 1) ohne Zwischenbereich und ohne negativen Wert für eine Bewertung gezeigt. Für Ihr System ist unter Umständen eine präzisere Bewertung erforderlich.
* Bot-Kanäle: In diesem Beispiel wird nur ein einzelner Kanal verwendet. Falls Sie mehrere Kanäle oder verschiedene Bot-Variationen in einem einzelnen Kanal verwenden möchten, muss dies ggf. im Rahmen der Kontextfeatures des Personalisierungsmodells berücksichtigt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie dieses Tutorial abgeschlossen haben, bereinigen Sie die folgenden Ressourcen:

* Löschen Sie Ihr Beispielprojektverzeichnis.
* Löschen Sie Ihre Personalisierungs- und Ihre LUIS-Ressource im Azure-Portal.

## <a name="next-steps"></a>Nächste Schritte
* [Funktionsweise der Personalisierung](how-personalizer-works.md)
* [Features](concepts-features.md): Konzepte zur Verwendung von Feature mit Aktionen und Kontext
* [Belohnungen](concept-rewards.md): Mehr über das Berechnen von Belohnungen
