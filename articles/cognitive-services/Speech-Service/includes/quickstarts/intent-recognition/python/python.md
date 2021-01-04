---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 44c4427540e1b94ebcaf00e6875723e0a654e9ef
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425419"
---
## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Installieren Sie das Speech SDK für Ihre Entwicklungsumgebung, und erstellen Sie ein leeres Beispielprojekt.<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Erstellen einer LUIS-App für die Absichtserkennung

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Öffnen Ihres Projekts

1. Öffnen Sie Ihre bevorzugte IDE.
2. Erstellen Sie ein neues Projekt, erstellen Sie eine Datei namens `quickstart.py`, und öffnen Sie diese Datei.

## <a name="start-with-some-boilerplate-code"></a>Beginnen mit Codebausteinen

Fügen Sie Code hinzu, der als Gerüst für das Projekt fungiert.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Erstellen einer Speech-Konfiguration

Bevor Sie ein `IntentRecognizer`-Objekt initialisieren können, müssen Sie eine Konfiguration erstellen, die den Schlüssel und Speicherort Ihrer LUIS-Vorhersageressource verwendet.

Fügen Sie diesen Code in `quickstart.py` ein. Stellen Sie sicher, dass Sie folgende Werte aktualisieren:

* Ersetzen Sie `"YourLanguageUnderstandingSubscriptionKey"` durch Ihren LUIS-Vorhersageschlüssel.
* Ersetzen Sie `"YourLanguageUnderstandingServiceRegion"` durch Ihren LUIS-Speicherort. Verwenden Sie den **Regionsbezeichner** der [Region](../../../../regions.md).

>[!TIP]
> Informationen dazu, wie Sie diese Werte ermitteln, finden Sie unter [Erstellen einer LUIS-App für die Absichtserkennung](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

In diesem Beispiel wird das Objekt `SpeechConfig` mit dem LUIS-Schlüssel und der entsprechenden Region erstellt. Eine vollständige Liste der verfügbaren Methoden finden Sie unter [SpeechConfig-Klasse](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Das Speech SDK verwendet für die Erkennung standardmäßig amerikanisches Englisch (en-us). Informationen zum Auswählen der Ausgangssprache finden Sie unter [Angeben der Ausgangssprache für die Spracherkennung](../../../../how-to-specify-source-language.md).

## <a name="initialize-an-intentrecognizer"></a>Initialisieren eines IntentRecognizer-Objekts

Als Nächstes erstellen wir ein Objekt vom Typ `IntentRecognizer`. Fügen Sie den folgenden Code direkt unterhalb Ihrer Speech-Konfiguration ein:

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Hinzufügen von LanguageUnderstandingModel und Absichten

Sie müssen der Absichtserkennung ein `LanguageUnderstandingModel` zuordnen und die zu erkennenden Absichten hinzufügen. Wir verwenden Absichten aus der vordefinierten Domäne für die Gebäudeautomatisierung.

Fügen Sie diesen Code unterhalb von `IntentRecognizer` ein. Stellen Sie sicher, dass Sie `"YourLanguageUnderstandingAppId"` durch die ID Ihrer LUIS-App ersetzen. 

>[!TIP]
> Informationen dazu, wie Sie diesen Wert ermitteln, finden Sie unter [Erstellen einer LUIS-App für die Absichtserkennung](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

In diesem Beispiel wird die Funktion `add_intents()` verwendet, um eine Liste explizit definierter Absichten hinzuzufügen. Wenn Sie alle Absichten aus einem Modell hinzufügen möchten, verwenden Sie `add_all_intents(model)`, und übergeben Sie das Modell.

## <a name="recognize-an-intent"></a>Erkennen einer Absicht

Rufen Sie die Methode `recognize_once()` über das Objekt `IntentRecognizer` auf. Diese Methode teilt dem Spracherkennungsdienst mit, dass Sie einen einzelnen Ausdruck zur Erkennung senden, und dass die Spracherkennung beendet werden soll, sobald der Ausdruck ermittelt wurde.

Fügen Sie diesen Code unterhalb Ihres Modells ein:

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Anzeigen der Erkennungsergebnisse (oder Fehler)

Wenn das Erkennungsergebnis vom Speech-Dienst zurückgegeben wird, möchten Sie es natürlich in irgendeiner Form verwenden. In diesem Beispiel geben wir das Ergebnis einfach in der Konsole aus.

Fügen Sie unterhalb des Aufrufs von `recognize_once()` den folgenden Code hinzu:

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Überprüfen des Codes

Ihr Code sollte nun wie folgt aussehen:

> [!NOTE]
> Wir haben dieser Version einige Kommentare hinzugefügt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Erstellen und Ausführen der App

Führen Sie das Beispiel über die Konsole oder in Ihrer IDE aus:

```
python quickstart.py
```

Die nächsten 15 Sekunden der Spracheingabe vom Mikrofon werden erkannt und im Konsolenfenster protokolliert.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [footer](./footer.md)]