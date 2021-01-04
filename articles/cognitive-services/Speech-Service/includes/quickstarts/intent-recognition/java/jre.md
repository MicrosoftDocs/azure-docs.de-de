---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 09b537d82ce4e9e44c36df628d120623e69abfaf
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425187"
---
## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">Installieren Sie das Speech SDK für Ihre Entwicklungsumgebung, und erstellen Sie ein leeres Beispielprojekt.<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Erstellen einer LUIS-App für die Absichtserkennung

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Öffnen Ihres Projekts

1. Öffnen Sie Ihre bevorzugte IDE.
2. Laden Sie das Projekt, und öffnen Sie `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Beginnen mit Codebausteinen

Fügen Sie Code hinzu, der als Gerüst für das Projekt fungiert.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Erstellen einer Speech-Konfiguration

Bevor Sie ein `IntentRecognizer`-Objekt initialisieren können, müssen Sie eine Konfiguration erstellen, die den Schlüssel und Speicherort Ihrer LUIS-Vorhersageressource verwendet.  

Fügen Sie den Code in `main()` in den try/catch-Block ein. Stellen Sie sicher, dass Sie folgende Werte aktualisieren:

* Ersetzen Sie `"YourLanguageUnderstandingSubscriptionKey"` durch Ihren LUIS-Vorhersageschlüssel.
* Ersetzen Sie `"YourLanguageUnderstandingServiceRegion"` durch Ihren LUIS-Speicherort. Verwenden Sie den **Regionsbezeichner** der [Region](../../../../regions.md).

>[!TIP]
> Informationen dazu, wie Sie diese Werte ermitteln, finden Sie unter [Erstellen einer LUIS-App für die Absichtserkennung](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

In diesem Beispiel wird die Methode `FromSubscription()` zum Erstellen von `SpeechConfig` verwendet. Eine vollständige Liste der verfügbaren Methoden finden Sie unter [SpeechConfig-Klasse](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet).

Das Speech SDK verwendet für die Erkennung standardmäßig amerikanisches Englisch (en-us). Informationen zum Auswählen der Ausgangssprache finden Sie unter [Angeben der Ausgangssprache für die Spracherkennung](../../../../how-to-specify-source-language.md).

## <a name="initialize-an-intentrecognizer"></a>Initialisieren eines IntentRecognizer-Objekts

Als Nächstes erstellen wir ein Objekt vom Typ `IntentRecognizer`. Fügen Sie den folgenden Code direkt unterhalb Ihrer Speech-Konfiguration ein:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Hinzufügen von LanguageUnderstandingModel und Absichten

Sie müssen der Absichtserkennung ein `LanguageUnderstandingModel` zuordnen und die zu erkennenden Absichten hinzufügen. Wir verwenden Absichten aus der vordefinierten Domäne für die Gebäudeautomatisierung.

Fügen Sie diesen Code unterhalb von `IntentRecognizer` ein. Stellen Sie sicher, dass Sie `"YourLanguageUnderstandingAppId"` durch die ID Ihrer LUIS-App ersetzen.

>[!TIP]
> Informationen dazu, wie Sie diesen Wert ermitteln, finden Sie unter [Erstellen einer LUIS-App für die Absichtserkennung](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

In diesem Beispiel wird die Funktion `addIntent()` verwendet, um Absichten individuell hinzuzufügen. Wenn Sie alle Absichten aus einem Modell hinzufügen möchten, verwenden Sie `addAllIntents(model)`, und übergeben Sie das Modell.

## <a name="recognize-an-intent"></a>Erkennen einer Absicht

Rufen Sie die Methode `recognizeOnceAsync()` über das Objekt `IntentRecognizer` auf. Diese Methode teilt dem Spracherkennungsdienst mit, dass Sie einen einzelnen Ausdruck zur Erkennung senden, und dass die Spracherkennung beendet werden soll, sobald der Ausdruck ermittelt wurde.

Fügen Sie diesen Code unterhalb Ihres Modells ein:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Anzeigen der Erkennungsergebnisse (oder Fehler)

Wenn das Erkennungsergebnis vom Speech-Dienst zurückgegeben wird, möchten Sie es natürlich in irgendeiner Form verwenden. In diesem Beispiel geben wir das Ergebnis einfach in der Konsole aus.

Fügen Sie diesen Code unterhalb Ihres Aufrufs von `recognizeOnceAsync()` ein:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Freigeben von Ressourcen

Es ist wichtig, die Speech-Ressourcen freizugeben, wenn Sie sie nicht mehr benötigen. Fügen Sie diesen Code am Ende des try/catch-Blocks ein:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Überprüfen des Codes

Ihr Code sollte nun wie folgt aussehen:

> [!NOTE]
> Wir haben dieser Version einige Kommentare hinzugefügt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Erstellen und Ausführen der App

Drücken Sie F11, oder wählen Sie **Ausführen** > **Debuggen** aus. <kbd></kbd>
Die nächsten 15 Sekunden der Spracheingabe vom Mikrofon werden erkannt und im Konsolenfenster protokolliert.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [footer](./footer.md)]