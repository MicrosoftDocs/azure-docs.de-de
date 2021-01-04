---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: a8a34a483390e12ba49123e26d2071d3aa667c89
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425514"
---
In diesem Schnellstart erlernen Sie grundlegende Entwurfsmuster für die Sprechererkennung mit dem Sprach-SDK, einschließlich:

* Textabhängige und textunabhängige Überprüfung
* Sprecheridentifikation zum Identifizieren eines Stimmbeispiels unter einer Gruppe von Stimmen
* Löschen von Stimmenprofilen

Einen Überblick über die Konzepte der Spracherkennung finden Sie im Artikel [Was ist der Azure-Sprechererkennungsdienst?](../../../speaker-recognition-overview.md).

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [JavaScript-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Sprechererkennung wird derzeit *nur* in Azure Speech-Ressourcen unterstützt, die in der Region `westus` erstellt wurden.

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst müssen Sie das <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK für JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> installieren. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbrowser <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Verwenden Sie außerdem abhängig von der Zielumgebung eine der folgenden Funktionen:

# <a name="script"></a>[script](#tab/script)

Laden Sie die Datei *microsoft.cognitiveservices.speech.sdk.bundle.js* des <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK für JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> herunter, und extrahieren Sie sie. Speichern Sie sie in einem Ordner, auf den von der HTML-Datei zugegriffen werden kann.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Verwenden Sie für einen Webbrowser das Tag `<script>`. Das Präfix `sdk` ist nicht erforderlich. Das Präfix `sdk` ist ein Alias, mit dem das Modul `require` benannt wird.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Weitere Informationen zu `import` finden Sie unter <a href="https://javascript.info/import-export" target="_blank">Exportieren und Importieren <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Weitere Informationen zu `require` finden Sie unter <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Was ist „require“? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie die folgenden Anweisungen oben in Ihrer JS-Datei hinzu, um die Beispiele dieses Artikels auszuführen.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Mit diesen Anweisungen werden die erforderlichen Bibliotheken importiert und Ihr Schlüssel und die Region des Speech-Diensts aus Ihren Umgebungsvariablen abgerufen. Darüber hinaus werden hiermit die Pfade zu Audiodateien angegeben, die Sie in den folgenden Aufgaben verwenden.

## <a name="create-helper-function"></a>Erstellen einer Hilfsfunktion

Fügen Sie die folgende Hilfsfunktion zum Einlesen von Audiodateien in Datenströme zur Verwendung durch den Speech-Dienst hinzu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

In dieser Funktion verwenden Sie die Methoden [AudioInputStream.createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream?preserve-view=true&view=azure-node-latest#createpushstream-audiostreamformat-) und [AudioConfig.fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-), um ein [AudioConfig](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest)-Objekt zu erstellen. Dieses `AudioConfig`-Objekt stellt einen Audiodatenstrom dar. In den folgenden Aufgaben verwenden Sie mehrere dieser `AudioConfig`-Objekte.

## <a name="text-dependent-verification"></a>Textabhängige Überprüfung

Mit der Sprecherüberprüfung wird bestätigt, dass ein Sprecher mit einer bekannten oder **registrierten** Stimme übereinstimmt. Der erste Schritt ist das **Registrieren** eines Stimmenprofils, damit der Dienst über etwas verfügt, mit dem zukünftige Stimmbeispiele verglichen werden können. In diesem Beispiel registrieren Sie das Profil mit einer **textabhängigen** Strategie, bei der eine bestimmte Passphrase erforderlich ist, die sowohl für die Registrierung als auch für die Überprüfung verwendet wird. Eine Liste mit den unterstützten Passphrasen finden Sie in den [Referenzdokumenten](/rest/api/speakerrecognition/).

### <a name="textdependentverification-function"></a>Funktion „TextDependentVerification“

Erstellen Sie zunächst die Funktion `TextDependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Mit dieser Funktion wird mit der [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-)-Methode ein [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest)-Objekt erstellt. Es gibt drei [Typen](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype?preserve-view=true&view=azure-node-latest) von `VoiceProfile`:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

In diesem Fall übergeben Sie `VoiceProfileType.TextDependentVerification` an `VoiceProfileClient.createProfileAsync`.

Anschließend rufen Sie zwei Hilfsfunktionen auf, die Sie als Nächstes definieren: `AddEnrollmentsToTextDependentProfile` und `SpeakerVerify`. Rufen Sie abschließend [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) auf, um das Profil zu entfernen.

### <a name="addenrollmentstotextdependentprofile-function"></a>Funktion „AddEnrollmentsToTextDependentProfile“

Definieren Sie die folgende Funktion, um ein Stimmenprofil zu registrieren.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

In dieser Funktion rufen Sie die Funktion `GetAudioConfigFromFile` auf, die Sie weiter oben definiert haben, um `AudioConfig`-Objekte aus Audiobeispielen zu erstellen. Diese Audiobeispiele enthalten eine Passphrase, z. B. „My voice is my passport, verify me“ (Meine Stimme ist mein Pass, überprüfen Sie mich). Anschließend registrieren Sie diese Audiobeispiele, indem Sie die [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-)-Methode verwenden.

### <a name="speakerverify-function"></a>Funktion „SpeakerVerify“

Definieren Sie `SpeakerVerify` wie folgt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

In dieser Funktion erstellen Sie ein [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?preserve-view=true&view=azure-node-latest)-Objekt mit der [SpeakerVerificationModel.FromProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?preserve-view=true&view=azure-node-latest#fromprofile-voiceprofile-)-Methode, indem Sie das zuvor erstellte [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest)-Objekt übergeben.

Als Nächstes rufen Sie die [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-)-Methode auf, um ein Audiobeispiel zu überprüfen, das die gleiche Passphrase wie die zuvor registrierten Audiobeispiele enthält. `SpeechRecognizer.recognizeOnceAsync` gibt ein [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?preserve-view=true&view=azure-node-latest)-Objekt zurück, dessen `score`-Eigenschaft ein Ähnlichkeitsergebnis im Bereich von 0,0 bis 1,0 enthält. Das `SpeakerRecognitionResult`-Objekt enthält auch eine `reason`-Eigenschaft vom Typ [ResultReason](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason?preserve-view=true&view=azure-node-latest). Wenn die Überprüfung erfolgreich war, sollte die `reason`-Eigenschaft den Wert `RecognizedSpeaker` aufweisen.

## <a name="text-independent-verification"></a>Textunabhängige Überprüfung

Dies unterscheidet die **textabhängige** Überprüfung von der **textunabhängigen** Überprüfung:

* Hierbei ist es nicht erforderlich, dass eine bestimmte Passphrase gesprochen wird, sondern es können beliebige Wörter sein.
* Es sind nicht drei Audiobeispiele erforderlich, aber es *müssen* insgesamt 20 Sekunden an Audiodaten vorhanden sein.

### <a name="textindependentverification-function"></a>Funktion „TextIndependentVerification“

Erstellen Sie zunächst die Funktion `TextIndependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Wie auch bei der Funktion `TextDependentVerification`, wird mit dieser Funktion mit der [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-)-Methode ein [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest)-Objekt erstellt.

In diesem Fall übergeben Sie `VoiceProfileType.TextIndependentVerification` an `createProfileAsync`.

Anschließend rufen Sie zwei Hilfsfunktionen auf: `AddEnrollmentsToTextIndependentProfile`, die Sie als Nächstes definieren, und `SpeakerVerify`, die Sie bereits definiert haben. Rufen Sie abschließend [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) auf, um das Profil zu entfernen.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definieren Sie die folgende Funktion, um ein Stimmenprofil zu registrieren.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

In dieser Funktion rufen Sie die Funktion `GetAudioConfigFromFile` auf, die Sie weiter oben definiert haben, um `AudioConfig`-Objekte aus Audiobeispielen zu erstellen. Anschließend registrieren Sie diese Audiobeispiele, indem Sie die [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-)-Methode verwenden.

## <a name="speaker-identification"></a>Sprecheridentifikation

Anhand der Sprecheridentifikation wird bestimmt, **welche** Stimme aus einer bestimmten Gruppe registrierter Stimmen spricht. Der Prozess ähnelt der **textunabhängigen Überprüfung** , wobei der Hauptunterschied darin besteht, dass eine Überprüfung anhand mehrerer Stimmenprofile gleichzeitig erfolgen kann, anstatt nur anhand eines einzelnen Profils.

### <a name="textindependentidentification-function"></a>Funktion „TextIndependentIdentification“

Erstellen Sie zunächst die Funktion `TextIndependentIdentification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Wie auch bei den Funktionen `TextDependentVerification` und `TextIndependentVerification`, wird mit dieser Funktion mit der [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-)-Methode ein [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest)-Objekt erstellt.

In diesem Fall übergeben Sie `VoiceProfileType.TextIndependentIdentification` an `VoiceProfileClient.createProfileAsync`.

Anschließend rufen Sie zwei Hilfsfunktionen auf: `AddEnrollmentsToTextIndependentProfile`, die Sie bereits definiert haben, und `SpeakerIdentify`, die Sie als Nächstes definieren. Rufen Sie abschließend [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) auf, um das Profil zu entfernen.

### <a name="speakeridentify-function"></a>Funktion „SpeakerIdentify“

Definieren Sie die Funktion `SpeakerIdentify` wie folgt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

In dieser Funktion erstellen Sie ein [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?preserve-view=true&view=azure-node-latest)-Objekt mit der [SpeakerIdentificationModel.fromProfiles](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?preserve-view=true&view=azure-node-latest#fromprofiles-voiceprofile---)-Methode, indem Sie das zuvor erstellte [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest)-Objekt übergeben.

Als Nächstes rufen Sie die [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-)-Methode auf und übergeben ein Audiobeispiel.
Über `SpeechRecognizer.recognizeOnceAsync` wird versucht, die Stimme für dieses Audiobeispiel basierend auf den `VoiceProfile`-Objekten zu identifizieren, die Sie zum Erstellen von `SpeakerIdentificationModel` verwendet haben. Es wird ein [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?preserve-view=true&view=azure-node-latest)-Objekt zurückgegeben, mit dessen `profileId`-Eigenschaft das übereinstimmende `VoiceProfile`-Objekt identifiziert wird (falls vorhanden). Die `score`-Eigenschaft enthält ein Ähnlichkeitsergebnis im Bereich von 0,0 bis 1,0.

## <a name="main-function"></a>main-Funktion

Definieren Sie abschließend die Funktion `main` wie folgt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Mit dieser Funktion wird ein [VoiceProfileClient](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest)-Objekt erstellt, das zum Erstellen, Registrieren und Löschen von Stimmenprofilen verwendet wird. Anschließend werden die Funktionen aufgerufen, die Sie zuvor definiert haben.