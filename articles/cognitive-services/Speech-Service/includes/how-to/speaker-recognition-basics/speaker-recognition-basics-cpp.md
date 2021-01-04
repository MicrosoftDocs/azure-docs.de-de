---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 489514068d83f7b2953732415ba066a2d4555df8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015491"
---
In diesem Schnellstart erlernen Sie grundlegende Entwurfsmuster für die Sprechererkennung mit dem Sprach-SDK, einschließlich:

* Textabhängige und textunabhängige Überprüfung
* Sprecheridentifikation zum Identifizieren eines Stimmbeispiels unter einer Gruppe von Stimmen
* Löschen von Stimmenprofilen

Einen Überblick über die Konzepte der Spracherkennung finden Sie im Artikel [Was ist der Azure-Sprechererkennungsdienst?](../../../speaker-recognition-overview.md).

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [C++-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Sprechererkennung wird derzeit *nur* in Azure Speech-Ressourcen unterstützt, die in der Region `westus` erstellt wurden.

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie die folgenden Anweisungen oben in Ihrer CPP-Datei ein, um die Beispiele dieses Artikels auszuführen.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Textabhängige Überprüfung

Mit der Sprecherüberprüfung wird bestätigt, dass ein Sprecher mit einer bekannten oder **registrierten** Stimme übereinstimmt. Der erste Schritt ist das **Registrieren** eines Stimmenprofils, damit der Dienst über etwas verfügt, mit dem zukünftige Stimmbeispiele verglichen werden können. In diesem Beispiel registrieren Sie das Profil mit einer **textabhängigen** Strategie, bei der eine bestimmte Passphrase erforderlich ist, die sowohl für die Registrierung als auch für die Überprüfung verwendet wird. Eine Liste mit den unterstützten Passphrasen finden Sie in den [Referenzdokumenten](/rest/api/speakerrecognition/).

### <a name="textdependentverification-function"></a>Funktion „TextDependentVerification“

Erstellen Sie zunächst die Funktion `TextDependentVerification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Mit dieser Funktion wird ein [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-Objekt mit der [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync)-Methode erstellt. Es gibt drei [Typen](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) von `VoiceProfile`:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

In diesem Fall übergeben Sie `VoiceProfileType::TextDependentVerification` an `CreateProfileAsync`.

Anschließend rufen Sie zwei Hilfsfunktionen auf, die Sie als Nächstes definieren: `AddEnrollmentsToTextDependentProfile` und `SpeakerVerify`. Abschließend rufen Sie [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) auf, um das Profil zu bereinigen.

### <a name="addenrollmentstotextdependentprofile-function"></a>Funktion „AddEnrollmentsToTextDependentProfile“

Definieren Sie die folgende Funktion, um ein Stimmenprofil zu registrieren.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

Bei dieser Funktion registrieren Sie Audiobeispiele in einer `while`-Schleife, mit der die Anzahl von verbleibenden und für die Registrierung erforderlichen Beispiele nachverfolgt wird. Bei jeder Iteration werden Sie von [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) aufgefordert, die Passphrase in Ihr Mikrofon zu sprechen, und das Beispiel wird dann dem Stimmenprofil hinzugefügt.

### <a name="speakerverify-function"></a>Funktion „SpeakerVerify“

Definieren Sie `SpeakerVerify` wie folgt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

In dieser Funktion erstellen Sie ein [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel)-Objekt mit der [SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile)-Methode, indem Sie das zuvor erstellte [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-Objekt übergeben.

Im nächsten Schritt werden Sie von [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) aufgefordert, die Passphrase erneut zu sprechen, aber dieses Mal wird sie anhand Ihres Stimmenprofils überprüft und ein Ähnlichkeitsergebnis im Bereich von 0,0 bis 1,0 zurückgegeben. Vom [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult)-Objekt wird auch `Accept` oder `Reject` zurückgegeben – je nachdem, ob sich für die Passphrase eine Übereinstimmung ergibt.

## <a name="text-independent-verification"></a>Textunabhängige Überprüfung

Dies unterscheidet die **textabhängige** Überprüfung von der **textunabhängigen** Überprüfung:

* Hierbei ist es nicht erforderlich, dass eine bestimmte Passphrase gesprochen wird, sondern es können beliebige Wörter sein.
* Es sind nicht drei Audiobeispiele erforderlich, aber es *müssen* insgesamt 20 Sekunden an Audiodaten vorhanden sein.

### <a name="textindependentverification-function"></a>Funktion „TextIndependentVerification“

Erstellen Sie zunächst die Funktion `TextIndependentVerification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

Wie auch bei der Funktion `TextDependentVerification`, wird mit dieser Funktion ein [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-Objekt mit der [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync)-Methode erstellt.

In diesem Fall übergeben Sie `VoiceProfileType::TextIndependentVerification` an `CreateProfileAsync`.

Anschließend rufen Sie zwei Hilfsfunktionen auf: `AddEnrollmentsToTextIndependentProfile`, die Sie als Nächstes definieren, und `SpeakerVerify`, die Sie bereits definiert haben. Abschließend rufen Sie [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) auf, um das Profil zu bereinigen.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definieren Sie die folgende Funktion, um ein Stimmenprofil zu registrieren.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

Bei dieser Funktion registrieren Sie Audiobeispiele in einer `while`-Schleife, bei der die Anzahl von Sekunden nachverfolgt wird, die verbleiben bzw. die für die Registrierung erforderlich sind. Bei jeder Iteration werden Sie von [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) aufgefordert, in Ihr Mikrofon zu sprechen, und das Beispiel wird dann dem Stimmenprofil hinzugefügt.

## <a name="speaker-identification"></a>Sprecheridentifikation

Anhand der Sprecheridentifikation wird bestimmt, **welche** Stimme aus einer bestimmten Gruppe registrierter Stimmen spricht. Der Prozess ähnelt stark der **textunabhängigen** Überprüfung, wobei der Hauptunterschied ist, dass eine Überprüfung anhand mehrerer Stimmenprofile gleichzeitig erfolgen kann, anstatt einer Überprüfung anhand eines einzelnen Profils.

### <a name="textindependentidentification-function"></a>Funktion „TextIndependentIdentification“

Erstellen Sie zunächst die Funktion `TextIndependentIdentification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

Wie auch bei den Funktionen `TextDependentVerification` und `TextIndependentVerification`, wird mit dieser Funktion ein [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-Objekt mit der [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync)-Methode erstellt.

In diesem Fall übergeben Sie `VoiceProfileType::TextIndependentIdentification` an `CreateProfileAsync`.

Anschließend rufen Sie zwei Hilfsfunktionen auf: `AddEnrollmentsToTextIndependentProfile`, die Sie bereits definiert haben, und `SpeakerIdentify`, die Sie als Nächstes definieren. Abschließend rufen Sie [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) auf, um das Profil zu bereinigen.

### <a name="speakeridentify-function"></a>Funktion „SpeakerIdentify“

Definieren Sie die Funktion `SpeakerIdentify` wie folgt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

In dieser Funktion erstellen Sie ein [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel)-Objekt mit der [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles)-Methode. `SpeakerIdentificationModel::FromProfiles` akzeptiert eine Liste mit [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-Objekten. In diesem Fall übergeben Sie das zuvor erstellte `VoiceProfile`-Objekt. Bei Bedarf können Sie aber auch mehrere `VoiceProfile`-Objekte übergeben, die jeweils mit Audiobeispielen einer anderen Stimme registriert werden.

Als Nächstes werden Sie von [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) aufgefordert, erneut zu sprechen. Dieses Mal wird Ihre Stimme mit den registrierten Stimmenprofilen verglichen und das Stimmenprofil mit der größten Ähnlichkeit zurückgegeben.

## <a name="main-function"></a>main-Funktion

Definieren Sie abschließend die Funktion `main` wie folgt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Mit dieser Funktion werden einfach die zuvor definierten Funktionen aufgerufen. Zunächst wird aber ein [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient)- und ein [SpeakerRecognizer](/cpp/cognitive-services/speech/speakerrecognizer)-Objekt erstellt.

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

`VoiceProfileClient` wird verwendet, um Stimmenprofile zu erstellen, zu registrieren und zu löschen. `SpeakerRecognizer` wird verwendet, um Sprachbeispiele anhand von mindestens einem registrierten Stimmenprofil zu validieren.

## <a name="changing-audio-input-type"></a>Ändern des Audioeingabetyps

In den Beispielen dieses Artikels wird das standardmäßige Gerätemikrofon zur Eingabe der Audiobeispiele verwendet. In Szenarien, in denen Sie anstelle der Eingabe per Mikrofon Audiodateien verwenden müssen, ändern Sie einfach die folgende Zeile:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

in:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Alternativ können Sie alle Vorkommen von `audio_config` durch [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput) ersetzen. Sie können auch gemischte Eingaben verwenden, z. B. ein Mikrofon zur Registrierung und Dateien zur Überprüfung.