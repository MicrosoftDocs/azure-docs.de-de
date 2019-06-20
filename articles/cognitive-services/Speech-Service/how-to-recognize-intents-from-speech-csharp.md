---
title: 'Tutorial: Erkennen von Absichten anhand von gesprochener Sprache mit dem Speech SDK für C#'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial lernen Sie, wie Sie mit dem Speech SDK für C# Absichten aus gesprochener Sprache erkennen können.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 0e24f66369cf990f6b271b894a31dc8395068e17
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257343"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Tutorial: Erkennen von Absichten anhand von gesprochener Sprache mit dem Speech SDK für C#

Das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) von Cognitive Services ist in den [Language Understanding Service (LUIS)](https://www.luis.ai/home) integriert, um **Absichtserkennung zu ermöglichen.** Eine Absicht ist eine Aufgabe, die der Benutzer ausführen möchte: einen Flug buchen, sich über das Wetter informieren oder einen Anruf tätigen. Der Benutzer kann beliebige Begriffe verwenden, die sich für ihn natürlich anfühlen. Mithilfe von Machine Learning ordnet LUIS Benutzeranforderungen den Absichten zu, die Sie definiert haben.

> [!NOTE]
> Eine LUIS-Anwendung definiert die Absichten und Entitäten, die Sie erkennen möchten. Sie unterscheidet sich von der C#-Anwendung, die den Speech Service verwendet. In diesem Artikel bedeutet „App“ die LUIS-App, während „Anwendung“ den C#-Code bedeutet.

In diesem Tutorial verwenden Sie das Speech SDK, um eine C#-Konsolenanwendung zu entwickeln, die Absichten aus Benutzeraussagen über das Mikrofon Ihres Geräts ableitet. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Visual Studio-Projekts, das auf das Speech SDK NuGet-Paket verweist
> * Erstellen einer Sprachkonfiguration und Abrufen einer Absichtserkennung
> * Abrufen des Modells für Ihre LUIS-App und Hinzufügen der benötigten Absicht
> * Angeben der Sprache für die Spracherkennung
> * Erkennen von Sprache aus einer Datei
> * Verwenden von asynchroner, ereignisgesteuerter kontinuierlicher Erkennung

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass Sie über Folgendes verfügen, bevor Sie mit diesem Tutorial beginnen.

* Ein LUIS-Konto. Sie können ein kostenloses Konto über das [LUIS-Portal](https://www.luis.ai/home) erhalten.
* Visual Studio 2017 (beliebige Edition).

## <a name="luis-and-speech"></a>LUIS und Spracherkennung

LUIS ist in die Speech-Dienste integriert, um Absichten anhand von Sprache zu erkennen. Ein Abonnement der Speech-Dienste ist nicht erforderlich. Sie benötigen lediglich LUIS.

LUIS verwendet zwei Arten von Schlüsseln:

|Schlüsseltyp|Zweck|
|--------|-------|
|authoring|ermöglicht Ihnen das programmgesteuerte Erstellen und Ändern von LUIS-Apps.|
|endpoint |autorisiert den Zugriff auf eine bestimmte LUIS-App.|

Der Endpunktschlüssel ist der LUIS-Schlüssel, der für dieses Tutorial erforderlich ist. Dieses Tutorial verwendet die LUIS-Beispiel-App für Hausautomatisierung, die Sie erstellen können, indem Sie die Anleitungen unter [Verwenden der vordefinierten App für Hausautomatisierung](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) befolgen. Wenn Sie selbst eine LUIS-App erstellt haben, können Sie diese stattdessen verwenden.

Wenn Sie eine LUIS-App erstellen, wird automatisch ein Startschlüssel generiert, mit dem Sie die App mithilfe von Textabfragen testen können. Mit diesem Schlüssel ist keine Speech-Dienste-Integration möglich, und er ist für dieses Tutorial nicht geeignet. Sie müssen eine LUIS-Ressource im Azure-Dashboard erstellen und diese der LUIS-App zuweisen. Sie können den Tarif für ein kostenloses Abonnement für dieses Tutorial verwenden.

Nachdem Sie die LUIS-Ressource im Azure-Dashboard erstellt haben, melden Sie sich am [LUIS-Portal](https://www.luis.ai/home) an, wählen Sie Ihre Anwendung auf der Seite „Eigene Anwendungen“ aus, und wechseln Sie dann auf die Seite „Verwalten“ der App. Klicken Sie schließlich in der Randleiste auf **Schlüssel und Endpunkte**.

![Schlüssel und Endpunkteinstellungen im LUIS-Portal](media/sdk/luis-keys-endpoints-page.png)

Gehen Sie auf der Seite der Einstellungen für „Schlüssel und Endpunkt“ folgendermaßen vor:

1. Scrollen Sie nach unten zum Abschnitt „Ressourcen und Schlüssel“, und klicken Sie auf **Ressource zuweisen**.
1. Wählen Sie im Dialogfeld **App einen Schlüssel zuweisen** Folgendes aus:

    * Wählen Sie „Microsoft“ als den Mandanten aus.
    * Wählen Sie unter „Abonnementname“ das Azure-Abonnement aus, das die LUIS-Ressource enthält, die Sie verwenden möchten.
    * Wählen Sie unter „Schlüssel“ die LUIS-Ressource aus, die Sie mit der App verwenden möchten.

Nach einem kurzen Moment wird das neue Abonnement in der Tabelle unten auf der Seite angezeigt. Klicken Sie auf das Symbol neben einem Schlüssel, um ihn in die Zwischenablage zu kopieren. (Sie können einen der beiden Schlüssel verwenden.)

![Abonnementschlüssel der LUIS-App](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Erstellen eines Speech-Projekts in Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Hinzufügen des Codes

Öffnen Sie die Datei `Program.cs` im Visual Studio-Projekt, und ersetzen Sie den Block mit `using`-Anweisungen am Anfang der Datei durch die folgenden Deklarationen.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

Fügen Sie in der bereitgestellten `Main()`-Methode den folgenden Code hinzu.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Erstellen Sie eine leere asynchrone `RecognizeIntentAsync()`-Methode wie hier gezeigt.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

Fügen Sie im Textkörper dieser neuen Methode den folgenden Code ein.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

Ersetzen Sie die Platzhalter in dieser Methode wie folgt durch Ihren LUIS-Abonnementschlüssel, die Region und die App-ID.

|Platzhalter|Ersetzen durch|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|Ihren LUIS-Endpunktschlüssel. Wie bereits erwähnt, muss es sich um einen Schlüssel aus Ihrem Azure-Dashboard handeln, nicht um einen „Startschlüssel“. Sie finden ihn auf der Seite „Schlüssel und Endpunkte“ Ihrer App (unter „Verwalten“) im [LUIS-Portal](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|Der kurze Bezeichner für die Region Ihres LUIS-Abonnements, z.B. `westus` für „USA, Westen“. Siehe [Regionen](regions.md).|
|`YourLanguageUnderstandingAppId`|die LUIS-App-ID Sie finden sie auf der Seite „Einstellungen“ Ihrer App im [LUIS Portal](https://www.luis.ai/home).|

Nachdem Sie diese Änderungen vorgenommen haben, können Sie die Tutorialanwendung erstellen (STRG+UMSCHALT+B) und ausführen (F5). Wenn Sie dazu aufgefordert werden, sprechen Sie „Turn off the lights“ (Licht ausschalten) in das Mikrofon Ihres PCs. Das Ergebnis wird im Konsolenfenster angezeigt.

Die folgenden Abschnitte enthalten eine Erläuterung des Codes.


## <a name="create-an-intent-recognizer"></a>Erstellen einer Absichtserkennung

Der erste Schritt beim Erkennen von Absichten in Sprache besteht darin, eine Sprachkonfiguration aus Ihrem LUIS-Endpunktschlüssel und der -Region zu erstellen. Sprachkonfigurationen können verwendet werden, um Erkennungen für die verschiedenen Funktionen des Speech SDK zu erstellen. Die Sprachkonfiguration bietet mehrere Möglichkeiten, das Abonnement anzugeben, das Sie verwenden möchten. Hier verwenden wir `FromSubscription`. Diese Angabe nimmt den Abonnementschlüssel und die Region an.

> [!NOTE]
> Verwenden Sie den Schlüssel und die Region Ihres LUIS-Abonnements (nicht den Schlüssel und die Region eines Speech-Dienste-Abonnements).

Als nächstes erstellen Sie eine Absichtserkennung mit `new IntentRecognizer(config)`. Da die Konfiguration bereits weiß, welches Abonnement verwendet werden soll, entfällt die erneute Angabe von Abonnementschlüssel und Endpunkt beim Erstellen der Erkennung.

## <a name="import-a-luis-model-and-add-intents"></a>Importieren eines LUIS-Modells und Hinzufügen von Absichten

Importieren Sie nun das Modell aus der LUIS-App mit `LanguageUnderstandingModel.FromAppId()`, und fügen Sie die LUIS-Absichten hinzu, die Sie über die `AddIntent()`-Methode der Erkennung erkennen möchten. Diese beiden Schritte verbessern die Genauigkeit der Spracherkennung, indem sie Wörter angeben, die der Benutzer wahrscheinlich in seinen Anforderungen verwenden wird. Es ist nicht notwendig, alle Absichten der App hinzuzufügen, wenn Sie sie nicht alle in Ihrer Anwendung erkennen müssen.

Für das Hinzufügen von Absichten sind drei Argumente erforderlich: das zuvor erstellte LUIS-Modell `model`, der Absichtsname und eine Absichts-ID. Der Unterschied zwischen der ID und dem Namen ist wie folgt.

|`AddIntent()`-Argument|Zweck|
|--------|-------|
|intentName |Der Name der Absicht, wie in der LUIS-App definiert. Muss genau mit dem Namen der LUIS-Absicht übereinstimmen.|
|intentID    |Eine ID, die vom Speech SDK einer erkannten Absicht zugewiesen wird. Kann beliebig sein und muss nicht dem Absichtsnamen entsprechen, wie er in der LUIS-App definiert ist. Wenn beispielsweise mehrere Absichten von demselben Code verarbeitet werden, können Sie für sie die gleiche ID verwenden.|

Die LUIS-App für die Gebäudeautomatisierung verwendet zwei Absichten: eine zum Einschalten eines Geräts und eine zum Ausschalten eines Geräts. Die folgenden Zeilen fügen diese Absichten der Erkennung hinzu. Ersetzen Sie die drei `AddIntent`-Zeilen in der `RecognizeIntentAsync()`-Methode durch diesen Code.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Anstatt einzelne Absichten hinzuzufügen, können Sie auch die Methode `AddAllIntents` verwenden, um der Erkennung alle Absichten in einem Modell hinzuzufügen.

## <a name="start-recognition"></a>Starten der Erkennung

Sobald die Erkennung erstellt und die Absichten hinzugefügt wurden, kann die Erkennung beginnen. Das Speech SDK unterstützt sowohl einstufige als auch fortlaufende Erkennung.

|Erkennungsmodus|Aufzurufende Methoden|Ergebnis|
|----------------|-----------------|---------|
|Einstufig|`RecognizeOnceAsync()`|Gibt die erkannte Absicht (falls vorhanden) nach einer Äußerung zurück.|
|Fortlaufend|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Erkennt mehrere Äußerungen. Gibt Ereignisse (z.B. `IntermediateResultReceived`) aus, wenn Ergebnisse verfügbar sind.|

Die Tutorialanwendung verwendet den einstufigen Modus und ruft daher `RecognizeOnceAsync()` auf, um die Erkennung zu beginnen. Das Ergebnis ist ein `IntentRecognitionResult`-Objekt, das Informationen über die erkannte Absicht enthält. Die LUIS JSON-Antwort wird durch den folgenden Ausdruck extrahiert:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Die Tutorialanwendung analysiert das JSON-Ergebnis nicht und zeigt es nur im Konsolenfenster an.

![Ergebnisse der LUIS-Erkennung](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Angeben der Erkennungssprache

Standardmäßig erkennt LUIS Absichten in amerikanischem Englisch (`en-us`). Durch das Zuweisen eines Gebietsschemacodes zur `SpeechRecognitionLanguage`-Eigenschaft der Sprachkonfiguration können Sie Absichten in anderen Sprachen erkennen. Fügen Sie z.B. `config.SpeechRecognitionLanguage = "de-de";` in unserer Tutorialanwendung hinzu, bevor Sie die Erkennung erstellen, um Absichten in Deutsch zu erkennen. Siehe [Unterstützte Sprachen](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Kontinuierliche Erkennung aus einer Datei

Der folgende Code zeigt zwei weitere Möglichkeiten der Absichtserkennung mit dem Speech SDK. Die erste (bereits erwähnte) Möglichkeit ist die kontinuierliche Erkennung, bei der die Erkennung Ereignisse ausgibt, wenn Ergebnisse verfügbar sind. Diese Ereignisse können dann von Ereignishandlern verarbeitet werden, die Sie bereitstellen. Bei kontinuierlicher Erkennung rufen Sie `StartContinuousRecognitionAsync()` der Erkennung auf, um die Erkennung anstelle von `RecognizeOnceAsync()` zu starten.

Die andere Möglichkeit ist das Lesen der Audiodaten aus einer WAV-Datei, die die zu verarbeitende Spracheingabe enthalten. Dazu wird eine Audiokonfiguration erstellt, die beim Erstellen der Absichtserkennung verwendet werden kann. Die Datei muss einen Kanal (Mono) mit einer Samplingrate von 16 kHz enthalten.

Um diese Features auszuprobieren, ersetzen Sie den Textkörper der `RecognizeIntentAsync()`-Methode durch den folgenden Code.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Überarbeiten Sie den Code, sodass er Ihren LUIS-Endpunktschlüssel, Ihre Region und Ihre App-ID enthält, und fügen Sie die Absichten der Heimautomatisierung wie zuvor hinzu. Ändern Sie `whatstheweatherlike.wav` in den Namen Ihrer Audiodatei. Erstellen Sie die Anwendung dann, und führen Sie sie aus.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Den Code aus diesem Artikel finden Sie im Ordner „samples/csharp/sharedcontent/console“.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erkennen von Sprache](how-to-recognize-speech-csharp.md)
