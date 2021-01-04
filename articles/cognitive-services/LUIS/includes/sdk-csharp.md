---
title: include file
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: a79f164a1733c46a4cca807403a7ead8df446f0a
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303671"
---
Verwenden Sie die LUIS-Clientbibliotheken (Language Understanding) für .NET für Folgendes:
* Erstellen einer App
* Hinzufügen einer Absicht (eine durch maschinelles Lernen erworbene Entität) mit einer Beispieläußerung
* Trainieren und Veröffentlichen der App
* Abfragen der Vorhersagelaufzeit

[Referenzdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | Quellcode für [Erstellungs-](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) und [Vorhersagebibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | NuGet für [Erstellung](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) und [Vorhersage](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C#-Beispiel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>Voraussetzungen

* Aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) und [.NET Core-CLI](/dotnet/core/tools/)
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Sobald Sie über Ihr Azure-Abonnement verfügen, [erstellen Sie eine Language Understanding-Erstellungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) im Azure-Portal, um an Ihren Schlüssel und Ihren Endpunkt zu gelangen. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen [erstellten](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) Ressource, um Ihre Anwendung mit der Language Understanding-Erstellung zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Erstellen Sie eine neue .NET Core-Anwendung in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE.

1. Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl dotnet `new` zum Erstellen einer neuen Konsolen-App mit dem Namen `language-understanding-quickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Wechseln Sie zum Ordner der neu erstellten App.

    ```dotnetcli
    cd language-understanding-quickstart
    ```

1. Sie können die Anwendung mit folgendem Befehl erstellen:

    ```dotnetcli
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

### <a name="install-the-nuget-libraries"></a>Installieren der NuGet-Bibliotheken

Installieren Sie innerhalb des Anwendungsverzeichnisses die LUIS-Clientbibliotheken (Language Understanding) für .NET mithilfe der folgenden Befehle:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>Erstellungsobjektmodell

Der LUIS-Erstellungsclient (Language Understanding) ist ein Objekt vom Typ [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet), das sich bei Azure authentifiziert und Ihren Erstellungsschlüssel enthält.

## <a name="code-examples-for-authoring"></a>Codebeispiele für die Erstellung

Nach der Erstellung des Clients können Sie damit unter anderem auf folgende Funktionen zugreifen:

* Apps: [Erstellen](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [Löschen](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [Veröffentlichen](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Beispieläußerungen: [Hinzufügen](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions?view=azure-dotnet), [Löschen anhand der ID](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Features: Verwalten von [Ausdruckslisten](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Modell: Verwalten von [Absichten](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) und Entitäten
* Muster: Verwalten von [Mustern](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Trainieren: [Trainieren](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) der App und Abfragen des [Trainingsstatus](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versionen:](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) Verwalten mittels Klonen, Exportieren und Löschen

## <a name="prediction-object-model"></a>Vorhersageobjektmodell

Der LUIS-Vorhersagelaufzeit-Client (Language Understanding) ist ein Objekt vom Typ [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet), das sich bei Azure authentifiziert und Ihren Ressourcenschlüssel enthält.

## <a name="code-examples-for-prediction-runtime"></a>Codebeispiele für die Vorhersagelaufzeit

Nach der Erstellung des Clients können Sie damit unter anderem auf folgende Funktionen zugreifen:

* Vorhersage nach [Staging- oder Produktionsslot](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Vorhersage durch [Version](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Hinzufügen der Abhängigkeiten

Öffnen Sie aus dem Projektverzeichnis die Datei *Program.cs* in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE. Ersetzen Sie den vorhandenen `using`-Code durch die folgenden `using`-Anweisungen:

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Hinzufügen von Codebausteinen

1. Ändern Sie die Signatur der `Main`-Methode, um asynchrone Aufrufe zuzulassen:

    ```csharp
    public static async Task Main()
    ```

1. Fügen Sie den restlichen Code in der `Main`-Methode der `Program`-Klasse hinzu, sofern nicht anders angegeben.

## <a name="create-variables-for-the-app"></a>Erstellen von Variablen für die App

Erstellen Sie zwei Sätze von Variablen: Sie ändern den ersten Satz, der zweite bleibt, wie er im Codebeispiel angezeigt wird. 

1. Erstellen Sie Variablen für Ihren Erstellungsschlüssel und Ihre Ressourcennamen.

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. Erstellen Sie Variablen für Ihre Endpunkte, den App-Namen, die Version und den Namen der Absicht.

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Erstellen Sie ein Objekt vom Typ [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein Objekt vom Typ [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) zu erstellen.

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Erstellen einer LUIS-App

Eine LUIS-App enthält das NLP-Modell (Natural Language Processing, Verarbeitung natürlicher Sprache) mit Absichten, Entitäten und Beispieläußerungen.

Erstellen Sie ein Objekt vom Typ [ApplicationCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Name und Sprachkultur sind erforderliche Eigenschaften. Rufen Sie die Methode [Apps.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) auf. Die Antwort ist die App-ID.

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Erstellen der Absicht für die App
Das primäre Objekt im Modell einer LUIS-App ist die Absicht. Die Absicht orientiert sich an einer Gruppierung der _Absichten_ von Benutzeräußerungen. Ein Benutzer kann eine Frage stellen oder etwas sagen, um eine bestimmte _beabsichtigte_ Antwort von einem Bot (oder von einer anderen Clientanwendung) zu erhalten. Beispiele für Absichten wären etwa das Buchen eines Flugs, das Einholen von Wetterinformationen für eine Zielstadt oder das Erfragen von Kontaktinformationen für den Kundendienst.

Erstellen Sie ein Objekt vom Typ [ModelCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) mit dem Namen der eindeutigen Absicht, und übergeben Sie dann die App-ID, die Versions-ID und das ModelCreateObject-Objekt an die Methode [Model.AddIntentAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet). Die Antwort ist die Absichts-ID.

Der Wert `intentName` wird im Abschnitt [Erstellen von Variablen für die App](#create-variables-for-the-app) als `OrderPizzaIntent` hartcodiert und ist Teil der Variablen.

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Erstellen von Entitäten für die App

Entitäten sind zwar nicht erforderlich, aber in den meisten Apps vorhanden. Die Entität extrahiert Informationen aus der Benutzeräußerung, die zur Erfüllung der Absicht des Benutzers erforderlich sind. Es gibt mehrere Arten von [vordefinierten](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) und benutzerdefinierten Entitäten, die jeweils über ein eigenes DTO-Modell (Data Transformation Object) verfügen.  Zu den gängigen vordefinierten Entitäten, die Sie Ihrer App hinzufügen können, zählen etwa [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) und [ordinal](../luis-reference-prebuilt-ordinal.md).

Wichtig: Entitäten sind nicht mit einer Absicht gekennzeichnet. Sie gelten in der Regel für mehrere Absichten. Nur die exemplarischen Benutzeräußerungen sind für eine bestimmte, einzelne Absicht gekennzeichnet.

Erstellungsmethoden für Entitäten sind Teil der Klasse [Model](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet). Jeder Entitätstyp verfügt über ein eigenes DTO-Modell (Data Transformation Object), das in der Regel das Wort `model` im Namespace [Models](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) enthält.

Mit dem Code für die Entitätserstellung wird eine Machine Learning-Entität mit untergeordneten Entitäten und Features erstellt, die auf die untergeordneten Entitäten vom Typ `Quantity` angewendet werden.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Ausschnitt aus einem Screenshot des Portals: erstellte Entität, Machine Learning-Entität mit untergeordneten Entitäten und Features, die auf die untergeordneten Entitäten vom Typ „Quantity“ angewendet werden":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

Verwenden Sie die folgende Methode für die Klasse, um die ID der untergeordneten Entität vom Typ „Quantity“ zu ermitteln und dieser untergeordneten Entität die entsprechenden Features zuzuweisen.

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Hinzufügen einer Beispieläußerung zu einer Absicht

Um die Absicht einer Äußerung ermitteln und Entitäten extrahieren zu können, benötigt die App Beispiele für Äußerungen. Die Beispiele müssen auf eine bestimmte, einzelne Absicht ausgerichtet sein und alle benutzerdefinierten Entitäten markieren. Vordefinierte Entitäten müssen nicht markiert werden.

Fügen Sie Beispieläußerungen hinzu, indem Sie eine Liste mit Objekten vom Typ [ExampleLabelObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) erstellen (jeweils ein Objekt pro Beispieläußerung). Jedes Beispiel muss alle Entitäten mit einem Wörterbuch mit Name-Wert-Paaren (Entitätsname und Entitätswert) markieren. Der Entitätswert muss exakt dem Vorkommen im Text der Beispieläußerung entsprechen.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Ausschnitt eines Screenshots mit der bezeichneten Beispieläußerung im Portal":::

Rufen Sie [Examples.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions?view=azure-dotnet) mit der App-ID, der Versions-ID und dem Beispiel auf.

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Trainieren der App

Nach Erstellung des Modells muss die LUIS-App für diese Version des Modells trainiert werden. Ein trainiertes Modell kann in einem [Container](../luis-container-howto.md) verwendet oder in den Staging- oder Produktslots [veröffentlicht](../luis-how-to-publish-app.md) werden.

Für die Methode [Train.TrainVersionAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) müssen die App-ID und die Versions-ID angegeben werden.

Bei sehr kleinen Modellen (wie etwa in dieser Schnellstartanleitung) dauert das Training nicht sehr lang. Bei Produktionsanwendungen muss das App-Training auch einen Abfrageaufruf für die Methode [GetStatusAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) enthalten, um zu bestimmen, wann bzw. ob das Training erfolgreich war. Die Antwort ist eine Liste mit Objekten vom Typ [ModelTrainingInfo](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet), die jeweils einen separaten Status für die einzelnen Objekte besitzen. Alle Objekte müssen erfolgreich sein, damit das Training als abgeschlossen gilt.

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Veröffentlichen der App im Produktionsslot

Veröffentlichen Sie die LUIS-App mithilfe der Methode [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet). Dadurch wird die aktuelle trainierte Version im angegebenen Slot am Endpunkt veröffentlicht. Ihre Clientanwendung verwendet diesen Endpunkt zum Senden von Benutzeräußerungen für die Absichtsvorhersage und die Entitätsextraktion.

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Authentifizieren des Vorhersageruntimeclients

Verwenden Sie ein Objekt vom Typ [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) mit Ihrem Schlüssel und mit Ihrem Endpunkt, um ein Objekt vom Typ [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) zu erstellen.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>Abrufen der Vorhersage aus der Laufzeit

Fügen Sie den folgenden Code hinzu, um die Anforderung an die Vorhersageruntime zu erstellen.

Die Benutzeräußerung ist Teil des [PredictionRequest](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet)-Objekts.

Die **GetSlotPredictionAsync**-Methode benötigt mehrere Parameter, wie z. B. die App-ID, den Slotnamen, das Vorhersageanforderungsobjekt, um die Anforderung zu erfüllen. Die anderen Optionen, wie z. B. „Ausführlich“, „Alle Absichten anzeigen“ und „Protokollieren“, sind optional.

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnetcli
dotnet run
```