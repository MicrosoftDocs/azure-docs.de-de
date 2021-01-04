---
title: Erkennen von Absichten anhand gesprochener Sprache mit dem Speech SDK C#
titleSuffix: Azure Cognitive Services
description: In diesem Leitfaden erfahren Sie, wie Sie mit dem Speech SDK für C# Absichten anhand gesprochener Sprache erkennen können.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 93a3adf00203e317be912e3e72de7a3f7ca666c6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001093"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Erkennen von Absichten anhand gesprochener Sprache mit dem Speech SDK für C#

Das [Speech SDK](speech-sdk.md) von Cognitive Services ist in den [Language Understanding Service (LUIS)](https://www.luis.ai/home) integriert, um **Absichtserkennung** zu ermöglichen. Eine Absicht ist eine Aufgabe, die der Benutzer ausführen möchte: einen Flug buchen, sich über das Wetter informieren oder einen Anruf tätigen. Der Benutzer kann beliebige Begriffe verwenden, die sich für ihn natürlich anfühlen. Mithilfe von Machine Learning ordnet LUIS den Absichten, die Sie definiert haben, Benutzeranforderungen zu.

> [!NOTE]
> Eine LUIS-Anwendung definiert die Absichten und Entitäten, die Sie erkennen möchten. Sie unterscheidet sich von der C#-Anwendung, die den Speech Service verwendet. In diesem Artikel bedeutet „App“ die LUIS-App, während „Anwendung“ den C#-Code bedeutet.

In diesem Leitfaden verwenden Sie das Speech SDK, um eine C#-Konsolenanwendung zu entwickeln, die Absichten aus Benutzeräußerungen über das Mikrofon Ihres Geräts ableitet. Sie lernen Folgendes:

> [!div class="checklist"]
>
> - Erstellen eines Visual Studio-Projekts, das auf das Speech SDK NuGet-Paket verweist
> - Erstellen einer Sprachkonfiguration und Abrufen einer Absichtserkennung
> - Abrufen des Modells für Ihre LUIS-App und Hinzufügen der benötigten Absicht
> - Angeben der Sprache für die Spracherkennung
> - Erkennen von Sprache aus einer Datei
> - Verwenden von asynchroner, ereignisgesteuerter kontinuierlicher Erkennung

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Elemente vorhanden sind, bevor Sie mit diesem Leitfaden beginnen:

- Ein LUIS-Konto. Sie können ein kostenloses Konto über das [LUIS-Portal](https://www.luis.ai/home) erhalten.
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (beliebige Edition)

## <a name="luis-and-speech"></a>LUIS und Spracherkennung

LUIS ist in Speech Service integriert, um Absichten aus Sprache zu erkennen. Ein Speech Service-Abonnement ist nicht erforderlich, nur LUIS.

LUIS verwendet drei Arten von Schlüsseln:

| Schlüsseltyp  | Zweck                                               |
| --------- | ----------------------------------------------------- |
| Erstellen | Ermöglicht Ihnen das programmgesteuerte Erstellen und Ändern von LUIS-Apps. |
| Starter   | Hiermit können Sie Ihre LUIS-Anwendung nur mit Text testen.   |
| Endpunkt  | Autorisiert den Zugriff auf eine bestimmte LUIS-App.            |

Für diesen Leitfaden benötigen Sie den Endpunktschlüsseltyp. Dieser Leitfaden verwendet die LUIS-Beispiel-App für die Gebäudeautomatisierung, die Sie erstellen können, indem Sie die Anleitung im Schnellstart [Verwenden der vordefinierten Home Automation-App](../luis/luis-get-started-create-app.md) befolgen. Wenn Sie selbst eine LUIS-App erstellt haben, können Sie diese stattdessen verwenden.

Wenn Sie eine LUIS-App erstellen, wird automatisch ein Startschlüssel generiert, mit dem Sie die App mithilfe von Textabfragen testen können. Mit diesem Schlüssel ist keine Integration des Speech-Diensts möglich und eignet sich nicht für diesen Leitfaden. Erstellen Sie eine LUIS-Ressource im Azure-Dashboard, und weisen Sie sie der LUIS-App zu. Sie können den Tarif für ein kostenloses Abonnement für diesen Leitfaden verwenden.

Nachdem Sie die LUIS-Ressource im Azure-Dashboard erstellt haben, melden Sie sich beim [LUIS-Portal](https://www.luis.ai/home) an, wählen Sie Ihre Anwendung auf der Seite **Eigene Anwendungen** aus, und wechseln Sie dann zur Seite **Verwalten** der App. Wählen Sie schließlich auf der Seitenleiste die Option **Schlüssel und Endpunkte** aus.

![Schlüssel und Endpunkteinstellungen im LUIS-Portal](media/sdk/luis-keys-endpoints-page.png)

Gehen Sie auf der Seite **Schlüssel- und Endpunkteinstellungen** folgendermaßen vor:

1. Scrollen Sie nach unten zum Abschnitt **Ressourcen und Schlüssel**, und wählen Sie **Ressource zuweisen** aus.
1. Nehmen Sie im Dialogfeld **App einen Schlüssel zuweisen** die folgenden Änderungen vor:

   - Wählen Sie unter **Mandant** die Option **Microsoft** aus.
   - Wählen Sie unter **Abonnementname** das Azure-Abonnement aus, das die LUIS-Ressource enthält, die Sie verwenden möchten.
   - Wählen Sie unter **Schlüssel** die LUIS-Ressource aus, die Sie mit der App verwenden möchten.

   Nach einem kurzen Moment wird das neue Abonnement in der Tabelle unten auf der Seite angezeigt.

1. Wählen Sie das Symbol neben einem Schlüssel aus, um diesen in die Zwischenablage zu kopieren. (Sie können einen der beiden Schlüssel verwenden.)

![Abonnementschlüssel der LUIS-App](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Erstellen eines Speech-Projekts in Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Hinzufügen des Codes

Als Nächstes fügen Sie dem Projekt Code hinzu.

1. Öffnen Sie im **Projektmappen-Explorer** die Datei **Program.cs**.

1. Ersetzen Sie den Block mit `using`-Anweisungen am Anfang der Datei durch die folgenden Deklarationen:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Ersetzen Sie die angegebene Methode `Main()` durch die folgende asynchrone Entsprechung:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Erstellen Sie eine leere asynchrone `RecognizeIntentAsync()`-Methode, wie nachfolgend gezeigt:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. Fügen Sie im Textkörper dieser neuen Methode den folgenden Code hinzu:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Ersetzen Sie die Platzhalter in dieser Methode wie folgt durch Ihren LUIS-Abonnementschlüssel, die Region und die App-ID.

   | Platzhalter | Ersetzen durch |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Ihren LUIS-Endpunktschlüssel. Auch hier müssen Sie dieses Element aus Ihrem Azure-Dashboard und nicht aus einem Startschlüssel abrufen. Sie finden es auf der Seite **Schlüssel und Endpunkte** Ihrer App (unter **Verwalten**) im [LUIS-Portal](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Der kurze Bezeichner für die Region Ihres LUIS-Abonnements, z.B. `westus` für „USA, Westen“. Siehe [Regionen](regions.md). |
   | `YourLanguageUnderstandingAppId` | die LUIS-App-ID Sie finden sie auf der Seite **Einstellungen** Ihrer App im [LUIS-Portal](https://www.luis.ai/home). |

Nachdem Sie diese Änderungen vorgenommen haben, können Sie die Anwendung erstellen (**STRG+UMSCHALT+B**) und ausführen (**F5**). Wenn Sie dazu aufgefordert werden, sprechen Sie „Turn off the lights“ (Licht ausschalten) in das Mikrofon Ihres PCs. Die Anwendung zeigt das Ergebnis im Konsolenfenster an.

Die folgenden Abschnitte enthalten eine Erläuterung des Codes.

## <a name="create-an-intent-recognizer"></a>Erstellen einer Absichtserkennung

Zunächst müssen Sie eine Sprachkonfiguration aus dem LUIS-Endpunktschlüssel und der Region erstellen. Sprachkonfigurationen können verwendet werden, um Erkennungen für die verschiedenen Funktionen des Speech SDK zu erstellen. Die Sprachkonfiguration bietet mehrere Möglichkeiten, um das Abonnement anzugeben, das Sie verwenden möchten. Hier verwenden wir `FromSubscription`. Diese Angabe übernimmt den Abonnementschlüssel und die Region.

> [!NOTE]
> Verwenden Sie den Schlüssel und die Region Ihres LUIS-Abonnements und nicht die des Abonnements für den Speech-Dienst.

Als nächstes erstellen Sie eine Absichtserkennung mit `new IntentRecognizer(config)`. Da die Konfiguration bereits weiß, welches Abonnement verwendet werden soll, entfällt die erneute Angabe von Abonnementschlüssel und Endpunkt beim Erstellen der Erkennung.

## <a name="import-a-luis-model-and-add-intents"></a>Importieren eines LUIS-Modells und Hinzufügen von Absichten

Importieren Sie nun das Modell aus der LUIS-App mit `LanguageUnderstandingModel.FromAppId()`, und fügen Sie die LUIS-Absichten hinzu, die Sie über die `AddIntent()`-Methode der Erkennung erkennen möchten. Diese beiden Schritte verbessern die Genauigkeit der Spracherkennung, indem sie Wörter angeben, die der Benutzer wahrscheinlich in seinen Anforderungen verwenden wird. Sie müssen nicht alle Absichten der App hinzufügen, wenn Sie sie nicht alle in Ihrer Anwendung erkennen müssen.

Für das Hinzufügen von Absichten sind drei Argumente erforderlich: das zuvor erstellte LUIS-Modell mit dem Namen `model`, der Absichtsname und eine Absichts-ID. Der Unterschied zwischen der ID und dem Namen ist wie folgt.

| Argument `AddIntent()`&nbsp; | Zweck |
| --------------------------- | ------- |
| `intentName` | Der Name der Absicht, wie in der LUIS-App definiert. Dieser Wert muss genau mit dem Namen der LUIS-Absicht übereinstimmen. |
| `intentID` | Eine ID, die vom Speech SDK einer erkannten Absicht zugewiesen wird. Dieser Wert kann beliebig sein und muss nicht dem Absichtsnamen entsprechen, der in der LUIS-App definiert ist. Wenn beispielsweise mehrere Absichten von demselben Code verarbeitet werden, können Sie für sie die gleiche ID verwenden. |

Die LUIS-App für die Gebäudeautomatisierung verwendet zwei Absichten: eine zum Einschalten eines Geräts und eine zum Ausschalten eines Geräts. Die folgenden Zeilen fügen diese Absichten der Erkennung hinzu. Ersetzen Sie die drei `AddIntent`-Zeilen in der `RecognizeIntentAsync()`-Methode durch diesen Code.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Anstatt einzelne Absichten hinzuzufügen, können Sie auch die Methode `AddAllIntents` verwenden, um der Erkennung alle Absichten in einem Modell hinzuzufügen.

## <a name="start-recognition"></a>Starten der Erkennung

Sobald die Erkennung erstellt und die Absichten hinzugefügt wurden, kann die Erkennung beginnen. Das Speech SDK unterstützt sowohl einstufige als auch fortlaufende Erkennung.

| Erkennungsmodus | Aufzurufende Methoden | Ergebnis |
| ---------------- | --------------- | ------ |
| Einstufig | `RecognizeOnceAsync()` | Gibt die erkannte Absicht (falls vorhanden) nach einer Äußerung zurück. |
| Fortlaufend | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Erkennt mehrere Äußerungen; gibt Ereignisse (z. B. `IntermediateResultReceived`) aus, wenn Ergebnisse verfügbar sind. |

Die Anwendung verwendet den einstufigen Modus und ruft daher `RecognizeOnceAsync()` auf, um die Erkennung zu beginnen. Das Ergebnis ist ein `IntentRecognitionResult`-Objekt, das Informationen über die erkannte Absicht enthält. Die LUIS JSON-Antwort wird durch den folgenden Ausdruck extrahiert:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Die Anwendung analysiert das JSON-Ergebnis nicht. Sie zeigt lediglich den JSON-Text im Konsolenfenster an.

![Einzelne Ergebnisse der LUIS-Erkennung](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Angeben der Erkennungssprache

Standardmäßig erkennt LUIS Absichten in amerikanischem Englisch (`en-us`). Durch das Zuweisen eines Gebietsschemacodes zur `SpeechRecognitionLanguage`-Eigenschaft der Sprachkonfiguration können Sie Absichten in anderen Sprachen erkennen. Fügen Sie z. B. `config.SpeechRecognitionLanguage = "de-de";` in unserer Anwendung hinzu, bevor Sie die Erkennung erstellen, um Absichten in Deutsch zu erkennen. Weitere Informationen finden Sie unter [LUIS-Sprachunterstützung](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Kontinuierliche Erkennung aus einer Datei

Der folgende Code zeigt zwei weitere Möglichkeiten der Absichtserkennung mit dem Speech SDK. Die erste (bereits erwähnte) Möglichkeit ist die kontinuierliche Erkennung, bei der die Erkennung Ereignisse ausgibt, wenn Ergebnisse verfügbar sind. Diese Ereignisse können dann von Ereignishandlern verarbeitet werden, die Sie bereitstellen. Bei kontinuierlicher Erkennung rufen Sie anstelle von `RecognizeOnceAsync()` die Methode `StartContinuousRecognitionAsync()` der Erkennung auf, um die Erkennung zu starten.

Die andere Möglichkeit ist das Lesen der Audiodaten aus einer WAV-Datei, die die zu verarbeitende Spracheingabe enthalten. Bei der Implementierung wird eine Audiokonfiguration erstellt, die beim Erstellen der Absichtserkennung verwendet werden kann. Die Datei muss einen Kanal (Mono) mit einer Samplingrate von 16 kHz enthalten.

Damit Sie diese Funktionen testen können, löschen Sie den Text der `RecognizeIntentAsync()`-Methode, oder kommentieren Sie ihn aus, und fügen Sie den folgenden Code an der Stelle ein.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Überarbeiten Sie den Code, sodass er Ihren LUIS-Endpunktschlüssel, Ihre Region und Ihre App-ID enthält, und fügen Sie die Absichten der Heimautomatisierung wie zuvor hinzu. Ändern Sie `whatstheweatherlike.wav` in den Namen Ihrer aufgezeichneten Audiodatei. Erstellen Sie anschließend die Audiodatei, kopieren Sie sie in das Buildverzeichnis, und führen Sie die Anwendung aus.

Wenn Sie beispielsweise in Ihrer aufgezeichneten Audiodatei „Turn off the lights“ (Licht ausschalten) sagen, eine Pause machen, und dann „Turn on the lights“ (Licht einschalten) sagen, erscheint eine Konsolenausgabe wie die folgende:

![Ergebnisse der LUIS-Erkennung zur Audiodatei](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Den Code aus diesem Artikel finden Sie im Ordner **samples/csharp/sharedcontent/console**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Erkennen von Spracheingaben per Mikrofon](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)