---
title: Feedbackschleife – Personalisierung
titleSuffix: Azure Cognitive Services
description: Personalisieren Sie Inhalt in dieser C#-Schnellstartanleitung mit dem Personalisierungsdienst.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/08/2019
ms.author: edjez
ms.openlocfilehash: a7f6c6fe25bf9dff2f102080f93fc1e0024ac660
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478632"
---
# <a name="quickstart-personalize-content-using-c"></a>Schnellstart: Personalisieren Sie Inhalt mit C# 

Zeigen Sie personalisierten Inhalt in dieser C#-Schnellstartanleitung mit dem Personalisierungsdienst an.

Dieses Beispiel veranschaulicht die Verwendung der Personalisierungsclientbibliothek für C# zur Ausführung der folgenden Aktionen: 

 * Erstellen Sie eine Bewertungsliste der Aktionen zur Personalisierung.
 * Melden Sie Anerkennungen, damit auf Grundlage der Benutzerauswahl für das angegebene Ereignis die am besten bewertete Aktion bestimmt werden kann.

Der Einstieg in die Personalisierung umfasst die folgenden Schritte:

1. Verweisen auf das SDK 
1. Schreiben von Code, um die Rangfolge von Aktionen festzulegen, die Sie Ihren Benutzern zeigen möchten,
1. Schreiben von Code zum Senden von Anerkennungen, um die Schleife zu trainieren.

## <a name="prerequisites"></a>Voraussetzungen

* Sie benötigen einen [Personalisierungsdienst](how-to-settings.md), um einen Abonnementschlüssel und die URL des Endpunktdiensts zu erhalten. 
* [Visual Studio 2015 oder 2017](https://visualstudio.microsoft.com/downloads/)
* Das SDK-NuGet-Paket [Microsoft.Azure.CognitiveServices.Personalizer](https://go.microsoft.com/fwlink/?linkid=2092272). Unten ist die Installationsanleitung angegeben.

## <a name="change-the-model-update-frequency"></a>Ändern der Häufigkeit der Modellaktualisierung

Ändern Sie in der Personalisierungsressource im Azure-Portal die **Häufigkeit der Modellaktualisierung** in 10 Sekunden. Dadurch wird der Dienst schnell trainiert, und Sie können sehen, wie sich die oberste Aktion für jede Iteration ändert.

Bei der Erstinstanziierung einer Personalisierungsschleife ist kein Modell vorhanden, da noch keine Relevanz-API-Aufrufe zum Trainieren ausgeführt wurden. Rangfolgeaufrufe geben gleiche Wahrscheinlichkeiten für jedes Element zurück. Ihre Anwendung sollte dem Inhalt dennoch immer anhand der Ausgabe von RewardActionId einen Rang zuweisen.

![Ändern der Häufigkeit der Modellaktualisierung](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Erstellen einer neuen Konsolen-App und Verweisen auf das Personalisierungs-SDK 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Erstellen Sie in Visual Studio eine neue Visual C#-Konsolen-App.
1. Installieren Sie das NuGet-Paket der Personalisierungsclientbibliothek. Wählen Sie im Menü **Extras**, **NuGet-Paket-Manager** und dann **NuGet-Pakete für Projektmappe verwalten** aus.
1. Wählen Sie die Registerkarte **Durchsuchen** aus, und geben Sie in das Feld **Suchen** `Microsoft.Azure.CognitiveServices.Personalizer`ein.
1. Wählen Sie **Microsoft.Azure.CognitiveServices.Personalizer** aus, sobald dies angezeigt wird.
1. Aktivieren Sie das Kontrollkästchen neben dem Projektnamen, und wählen Sie dann **Installieren** aus.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Hinzufügen des Codes und Einfügen Ihrer Personalisierungs- und Azure-Schlüssel

1. Ersetzen Sie „Program.cs“ durch den folgenden Code. 
1. Ersetzen Sie den Wert `serviceKey` durch Ihren gültigen Personalisierungsabonnementschlüssel.
1. Ersetzen Sie `serviceEndpoint` durch Ihren Dienstendpunkt. Ein Beispiel ist `https://westus2.api.cognitive.microsoft.com/`.
1. Führen Sie das Programm aus.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Hinzufügen von Code, um die Rangfolge von Aktionen festzulegen, die Sie Ihren Benutzern zeigen möchten

Der folgende C#-Code ist eine vollständige Auflistung zum Übergeben von Benutzerinformationen, Features und Informationen zu Ihrem Inhalt sowie _Aktionen_ an die Personalisierung, die das SDK verwendet. Die Personalisierung gibt die am besten bewertete Aktion zurück, damit Sie sie Ihren Benutzern zeigen können.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            // Get the actions list to choose from personalizer with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalizer client.
            PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
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

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
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

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
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

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
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

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Ausführen des Programms

Erstellen Sie das Programm, und führen Sie es aus. Das Schnellstartprogramm stellt ein paar Fragen zum Sammeln von Benutzereinstellungen – auch bekannt als Features – und stellt dann die am besten bewertete Aktion bereit.

![Das Schnellstartprogramm stellt ein paar Fragen zum Sammeln von Benutzereinstellungen – auch bekannt als Features – und stellt dann die am besten bewertete Aktion bereit.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Entfernen Sie nach dem Durchführen dieses Schnellstarts alle Dateien, die Sie im Rahmen der Schnellstartanleitung erstellt haben. 

## <a name="next-steps"></a>Nächste Schritte

[Funktionsweise der Personalisierung](how-personalizer-works.md)


