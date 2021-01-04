---
title: 'Tutorial: Language Understanding-Bot Node.js v4'
description: Erstellen Sie mithilfe dieses Tutorials einen in Language Understanding (LUIS) integrierten Chatbot. Dieser Chatbot implementiert mithilfe der Human Resources-App schnell eine Bot-Lösung.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/30/2020
ms.custom: devx-track-js
ms.openlocfilehash: dd5f97f2b1c2ad51dda3446412cd936e45c5982e
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436894"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: Verwenden eines Web-App-Bots mit aktiviertem Language Understanding in Node.js

Erstellen Sie mithilfe von Node.js einen in Language Understanding (LUIS) integrierten Chatbot. Der Bot wird mit der [Web-App-Bot](/azure/bot-service/)-Ressource von Azure und [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) V4 erstellt.

**In diesem Tutorial lernen Sie Folgendes:**

> [!div class="checklist"]
> * Erstellen eines Web-App-Bots. In diesem Vorgang wird eine neue LUIS-App erstellt.
> * Herunterladen des vom Webbotdienst erstellten Botprojekts
> * Lokales Starten von Bot und Emulator auf dem eigenen Computer
> * Anzeigen von Äußerungsergebnissen im Bot

## <a name="prerequisites"></a>Voraussetzungen

* [Bot Framework Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

## <a name="create-a-web-app-bot-resource"></a>Erstellen einer Web-App-Bot-Ressource

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Neue Ressource erstellen** aus.

1. Suchen Sie im Suchfeld nach **Web-App-Bot**, und wählen Sie den Eintrag aus. Klicken Sie auf **Erstellen**.

1. Geben Sie in **Botdienst** die erforderlichen Informationen ein:

    |Einstellung|Zweck|Empfohlene Einstellung|
    |--|--|--|
    |Bothandle|Ressourcenname|`luis-nodejs-bot-` + `<your-name>`, z.B. `luis-nodejs-bot-johnsmith`|
    |Subscription|Abonnement, in dem der Bot erstellt werden soll.|Ihr primäres Abonnement.
    |Resource group|Logische Gruppe von Azure-Ressourcen|Erstellen einer neuen Gruppe zum Speichern aller Ressourcen, die mit diesen Bot verwendet werden; benennen Sie die Gruppe `luis-nodejs-bot-resource-group`.|
    |Standort|Azure-Region: muss nicht die gleiche wie die Erstellungs- oder Veröffentlichungsregion von LUIS sein.|`westus`|
    |Tarif|Für die Grenzwerte der Dienstanforderungen und die Abrechnung verwendet.|`F0` ist der kostenlose Tarif.
    |App-Name|Der Name wird als Unterdomäne verwendet, wenn Ihr Bot in der Cloud bereitgestellt wird (z.B. personalwesenbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, z.B. `luis-nodejs-bot-johnsmith`|
    |Botvorlage|Einstellungen für Bot Framework – siehe nächste Tabelle|
    |LUIS-App-Speicherort|Muss mit der LUIS-Ressourcenregion übereinstimmen|`westus`|
    |App Service-Plan/Standort|Ändern Sie den angegebenen Standardwert nicht.|
    |Application Insights|Ändern Sie den angegebenen Standardwert nicht.|
    |Microsoft-App-ID und Kennwort|Ändern Sie den angegebenen Standardwert nicht.|

1. Wählen Sie in **Bot template** (Botvorlage) die folgenden Werte und dann unterhalb dieser Einstellungen die Schaltfläche **Auswählen** aus:

    |Einstellung|Zweck|Auswahl|
    |--|--|--|
    |SDK-Sprache|Programmiersprache des Bots|**Node.js**|
    |Bot|Bottyp|**Basisbot**|

1. Klicken Sie auf **Erstellen**. Dadurch wird der Botdienst erstellt und in Azure bereitgestellt. Im Rahmen dieses Vorgangs wird eine LUIS-App mit dem Namen `luis-nodejs-bot-XXXX` erstellt. Dieser Name basiert auf dem Namen der Azure Bot Service-App.

    > [!div class="mx-imgBorder"]
    > [![Erstellen des Web-App-Bots](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Warten Sie, bis der Botdienst erstellt wurde, bevor Sie fortfahren.

1. Wählen Sie in der Benachrichtigung `Go to resource` aus, um zur Web-App-Botseite zu gelangen.

## <a name="the-bot-has-a-language-understanding-model"></a>Der Bot beinhaltet ein Language Understanding-Modell

Bei der Erstellung des Botdiensts wird auch eine neue LUIS-App mit Absichten und Beispieläußerungen erstellt. Der Bot stellt die Zuordnung der Absichten zur neuen LUIS-App für die folgenden Absichten bereit:

|LUIS-Absichten für Basisbots|Beispieläußerung|
|--|--|
|Flugbuchung|`Travel to Paris`|
|Abbrechen|`bye`|
|GetWeather|`what's the weather like?`|
|Keine|Alles, was sich außerhalb der Domäne der App befindet.|

## <a name="test-the-bot-in-web-chat"></a>Testen des Bots im Webchat

1. Wählen Sie im Azure-Portal für den neuen Bot die Option **Test in Web Chat** (Test im Webchat) aus.
1. Geben Sie im Textfeld **Nachricht eingeben** den Text `Book a flight from Seattle to Berlin tomorrow` ein. Der Bot antwortet mit der Bestätigung, ob ein Flug gebucht werden soll.

    ![Screenshot des Azure-Portals, Eingeben des Texts „hello“](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Über die Testfunktionen können Sie Ihren Bot schnell testen. Laden Sie für umfangreichere Tests, einschließlich Debuggen, den Botcode herunter, und verwenden Sie Visual Studio Code.

## <a name="download-the-web-app-bot-source-code"></a>Herunterladen des Quellcodes für den Web-App-Bot
Um den Code des Web-App-Bots zu entwickeln, laden Sie den Code herunter, und verwenden Sie ihn auf Ihrem lokalen Computer.

1. Wählen Sie im Azure-Portal im Abschnitt **Bot Management** (Botverwaltung) **Erstellen** aus.

1. Wählen Sie **Bot-Quellcode herunterladen** aus.

    [![Herunterladen des Web-App-Bot-Quellcodes für den Basisbot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Wenn im Popupdialogfeld die Frage **Include app settings in the downloaded zip file?** (App-Einstellungen in die heruntergeladene ZIP-Datei einfügen?) angezeigt wird, wählen Sie **Ja** aus. Dadurch werden die LUIS-Einstellungen bereitgestellt.

1. Wenn der Quellcode gezippt ist, wird in einer Meldung ein Link zum Herunterladen des Codes zur Verfügung gestellt. Wählen Sie den Link aus.

1. Speichern Sie die ZIP-Datei auf Ihrem lokalen Computer, und extrahieren Sie die Dateien. Öffnen Sie den Projektordner in Visual Studio Code.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Überprüfen des Codes zum Senden einer Äußerung an LUIS und Erhalten einer Antwort

1. Öffnen Sie zum Senden der Benutzeräußerung an den LUIS-Vorhersageendpunkt **dialogs > flightBookingRecognizer.js**. Das ist die Stelle, an der in den Bot eingegebene Benutzeräußerungen an LUIS gesendet werden. Die Antwort von LUIS wird über die Methode **executeLuisQuery** zurückgegeben.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. Unter **dialogs > mainDialog** wird die Äußerung erfasst und an „executeLuisQuery“ in der Methode „actStep“ gesendet.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>Starten des Botcodes

1. Öffnen Sie eine Windows-Konsole bzw. ein Linux- oder macOS-Terminal.

1. Wechseln Sie in das Verzeichnis mit dem Botcode, und geben Sie den folgenden Befehl ein, um die dotenv-Abhängigkeit zu installieren:

    ```console
    npm install dotenv --save
    ```

1. Geben Sie den folgenden Befehl ein, um den Bot zu starten:

    ```console
    node index.js
    ```

Ein Browserfenster mit der Website des Web-App-Bots wird unter der Adresse `http://localhost:3978/` geöffnet. Auf einer Startseite werden Informationen zu dem Bot angezeigt.

![Auf einer Startseite werden Informationen zu dem Bot angezeigt.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>Verwenden von Bot Framework Emulator zum Testen des Bots

Stellen einer Frage zur Absicht „Flugbuchung“ im Bot

1. Starten Sie Bot Framework Emulator, und wählen Sie **Open Bot** (Bot öffnen) aus.
1. Geben Sie im Popupdialogfeld **Open a bot** (Bot öffnen) die URL des Bots ein, z. B. `http://localhost:3978/api/messages`. Die Route `/api/messages` ist die Webadresse für den Bot.
1. Geben Sie die **Microsoft-App-ID** und das **Microsoft-App-Kennwort** ein, die sich in der Datei **env** im Stammverzeichnis des heruntergeladenen Botcodes befinden.

1. Geben Sie in Bot Framework Emulator `Book a flight from Seattle to Berlin tomorrow` ein. Sie erhalten dann die gleiche Antwort für den Basisbot, die Sie auch mit **Test in Web Chat** (Im Webchat testen) erhalten haben.

    [![Screenshot: Bot Framework Emulator](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Wählen Sie **Ja** aus. Der Bot antwortet mit einer Zusammenfassung seiner Aktionen.
1. Wählen Sie im Protokoll von Bot Framework Emulator die Zeile mit `<- trace LuisV3 Trace` aus. Dadurch wird die JSON-Antwort von LUIS für die Absicht und die Entitäten der Äußerung angezeigt.

    [![Screenshot: Bot Framework Emulator und Bereich auf der rechten Seite mit JSON-Code](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich weitere [Beispiele](https://github.com/microsoft/botframework-solutions) mit Konversationsbots an.

> [!div class="nextstepaction"]
> [Erstellen einer Language Understanding-App mit einer benutzerdefinierten Motivdomäne](./tutorial-intents-only.md)