---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c242bbb803e359664eb2fb63262f0c540723f9c6
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424852"
---
## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Installieren Sie das Speech SDK für Ihre Entwicklungsumgebung, und erstellen Sie ein leeres Beispielprojekt.<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Erstellen einer LUIS-App für die Absichtserkennung

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Öffnen Sie Ihr Projekt in Visual Studio.

Öffnen Sie als Nächstes Ihr Projekt in Visual Studio.

1. Starten Sie Visual Studio 2019.
2. Laden Sie das Projekt, und öffnen Sie `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Beginnen mit Codebausteinen

Fügen Sie Code hinzu, der als Gerüst für das Projekt fungiert. Beachten Sie, dass Sie eine asynchrone Methode mit dem Namen `RecognizeIntentAsync()` erstellt haben.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Erstellen einer Speech-Konfiguration

Bevor Sie ein `IntentRecognizer`-Objekt initialisieren können, müssen Sie eine Konfiguration erstellen, die den Schlüssel und Speicherort Ihrer LUIS-Vorhersageressource verwendet.

> [!IMPORTANT]
> Der Starterschlüssel und die Erstellungsschlüssel funktionieren nicht. Sie müssen den Vorhersageschlüssel und den Vorhersagespeicherort verwenden, die Sie zuvor erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer LUIS-App für die Absichtserkennung](#create-a-luis-app-for-intent-recognition).

Fügen Sie diesen Code in die Methode `RecognizeIntentAsync()` ein. Stellen Sie sicher, dass Sie folgende Werte aktualisieren:

* Ersetzen Sie `"YourLanguageUnderstandingSubscriptionKey"` durch Ihren LUIS-Vorhersageschlüssel.
* Ersetzen Sie `"YourLanguageUnderstandingServiceRegion"` durch Ihren LUIS-Speicherort. Verwenden Sie den **Regionsbezeichner** der [Region](../../../../regions.md).

>[!TIP]
> Informationen dazu, wie Sie diese Werte ermitteln, finden Sie unter [Erstellen einer LUIS-App für die Absichtserkennung](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

In diesem Beispiel wird die Methode `FromSubscription()` zum Erstellen von `SpeechConfig` verwendet. Eine vollständige Liste der verfügbaren Methoden finden Sie unter [SpeechConfig-Klasse](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet).

Das Speech SDK verwendet für die Erkennung standardmäßig amerikanisches Englisch (en-us). Informationen zum Auswählen der Ausgangssprache finden Sie unter [Angeben der Ausgangssprache für die Spracherkennung](../../../../how-to-specify-source-language.md).

## <a name="initialize-an-intentrecognizer"></a>Initialisieren eines IntentRecognizer-Objekts

Als Nächstes erstellen wir ein Objekt vom Typ `IntentRecognizer`. Dieses Objekt wird innerhalb einer using-Anweisung erstellt, um die ordnungsgemäße Freigabe nicht verwalteter Ressourcen sicherzustellen. Fügen Sie diesen Code in die Methode `RecognizeIntentAsync()` direkt unterhalb der Speech-Konfiguration ein.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Hinzufügen von LanguageUnderstandingModel und Absichten

Sie müssen der Absichtserkennung ein `LanguageUnderstandingModel` zuordnen und die zu erkennenden Absichten hinzufügen. Wir verwenden Absichten aus der vordefinierten Domäne für die Gebäudeautomatisierung. Fügen Sie diesen Code in die using-Anweisung aus dem vorherigen Abschnitt ein. Stellen Sie sicher, dass Sie `"YourLanguageUnderstandingAppId"` durch die ID Ihrer LUIS-App ersetzen.

>[!TIP]
> Informationen dazu, wie Sie diesen Wert ermitteln, finden Sie unter [Erstellen einer LUIS-App für die Absichtserkennung](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

In diesem Beispiel wird die Funktion `AddIntent()` verwendet, um Absichten individuell hinzuzufügen. Wenn Sie alle Absichten aus einem Modell hinzufügen möchten, verwenden Sie `AddAllIntents(model)`, und übergeben Sie das Modell. 

## <a name="recognize-an-intent"></a>Erkennen einer Absicht

Rufen Sie die Methode `RecognizeOnceAsync()` über das Objekt `IntentRecognizer` auf. Diese Methode teilt dem Spracherkennungsdienst mit, dass Sie einen einzelnen Ausdruck zur Erkennung senden, und dass die Spracherkennung beendet werden soll, sobald der Ausdruck ermittelt wurde.

Fügen Sie in der using-Anweisung unterhalb Ihres Modells den folgenden Code hinzu:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Anzeigen von Erkennungsergebnissen (oder Fehlern)

Wenn das Erkennungsergebnis vom Speech-Dienst zurückgegeben wird, möchten Sie es natürlich in irgendeiner Form verwenden. In diesem Beispiel geben wir die Ergebnisse einfach in der Konsole aus.

Fügen Sie in der using-Anweisung unterhalb von `RecognizeOnceAsync()` den folgenden Code ein:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Überprüfen des Codes

Ihr Code sollte nun wie folgt aussehen:

> [!NOTE]
> Wir haben dieser Version einige Kommentare hinzugefügt.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Erstellen und Ausführen der App

Sie können nun Ihre App erstellen und die Spracherkennung mit dem Speech-Dienst testen.

1. **Kompilieren des Codes** : Wählen Sie auf der Menüleiste von Visual Studio **Build** > **Projektmappe erstellen** aus.
2. **Starten der App** : Wählen Sie auf der Menüleiste **Debuggen** > **Debuggen starten** aus, oder drücken Sie <kbd>F5</kbd>.
3. **Starten der Erkennung** : Sie werden aufgefordert, etwas auf Englisch zu sagen. Ihre Spracheingabe wird an den Speech-Dienst gesendet, in Text transkribiert und in der Konsole ausgegeben.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [footer](./footer.md)]