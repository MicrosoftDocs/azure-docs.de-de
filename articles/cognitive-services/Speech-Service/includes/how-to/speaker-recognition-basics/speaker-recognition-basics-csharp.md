---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 2b5a34e8f3e7132a16ad3683b846d57e9ece2cb6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015468"
---
In diesem Schnellstart erlernen Sie grundlegende Entwurfsmuster für die Sprechererkennung mit dem Sprach-SDK, einschließlich:

* Textabhängige und textunabhängige Überprüfung
* Sprecheridentifikation zum Identifizieren eines Stimmbeispiels unter einer Gruppe von Stimmen
* Löschen von Stimmenprofilen

Einen Überblick über die Konzepte der Spracherkennung finden Sie im Artikel [Was ist der Azure-Sprechererkennungsdienst?](../../../speaker-recognition-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Sprechererkennung wird derzeit *nur* in Azure Speech-Ressourcen unterstützt, die in der Region `westus` erstellt wurden.

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Um die Beispiele in diesem Artikel auszuführen, fügen Sie die folgenden `using`-Anweisungen oben in Ihr Skript ein.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet)) erstellt werden. In diesem Beispiel erstellen Sie das Objekt [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) mit einem Abonnementschlüssel und einer Region. Sie können für den restlichen Teil dieses Artikels auch einfache Codebausteine erstellen, an denen Sie dann jeweils die entsprechenden Anpassungen vornehmen.

Beachten Sie, dass als Region `westus` festgelegt ist, da sie die einzige unterstützte Region für den Dienst ist.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Textabhängige Überprüfung

Mit der Sprecherüberprüfung wird bestätigt, dass ein Sprecher mit einer bekannten oder **registrierten** Stimme übereinstimmt. Der erste Schritt ist das **Registrieren** eines Stimmenprofils, damit der Dienst über etwas verfügt, mit dem zukünftige Stimmbeispiele verglichen werden können. In diesem Beispiel registrieren Sie das Profil mit einer **textabhängigen** Strategie, die eine bestimmte Passphrase für Registrierung und Überprüfung erfordert. Eine Liste der unterstützten Passphrasen finden Sie in den [Referenzdokumenten](/rest/api/speakerrecognition/).

Erstellen Sie zunächst die folgende Funktion in der `Program`-Klasse, um ein Stimmenprofil zu registrieren.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

In dieser Funktion erstellt `await client.CreateProfileAsync()` wirklich das neue Stimmenprofil. Nach der Erstellung geben Sie an, wie Sie Audiobeispiele eingeben werden, indem Sie in diesem Beispiel mit `AudioConfig.FromDefaultMicrophoneInput()` Audiodaten von Ihrem Standardeingabegerät erfassen. Als nächstes registrieren Sie Audiobeispiele in einer `while`-Schleife, die die Anzahl der verbleibenden und für die Registrierung erforderlichen Beispiele nachverfolgt. In jeder Iteration werden Sie von `client.EnrollProfileAsync(profile, audioInput)` aufgefordert, die Passphrase in Ihr Mikrofon zu sprechen, und das Beispiel wird dem Sprachprofil hinzugefügt.

Nachdem die Registrierung abgeschlossen ist, rufen Sie `await SpeakerVerify(config, profile, profileMapping)` auf, um das soeben erstellte Profil zu überprüfen. Fügen Sie eine weitere Funktion hinzu, um `SpeakerVerify` zu definieren.

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

In dieser Funktion übergeben Sie das soeben erstellte `VoiceProfile`-Objekt, um ein Modell zu initialisieren, anhand dessen überprüft werden soll. Im nächsten Schritt werden Sie von `await speakerRecognizer.RecognizeOnceAsync(model)` aufgefordert, die Passphrase erneut zu sprechen, aber dieses Mal wird sie anhand Ihres Stimmenprofils überprüft und ein Ähnlichkeitsergebnis von 0,0-1,0 zurückgegeben. Das `result`-Objekt gibt auch `Accept` oder `Reject` zurück, je nachdem, ob die Passphrase übereinstimmt.

Ändern Sie als nächstes die `Main()`-Funktion, um die neuen Funktionen aufzurufen, die Sie erstellt haben. Beachten Sie außerdem, dass Sie ein `Dictionary<string, string>` erstellen, das als Verweis über die Funktionsaufrufe übergeben werden soll. Der Grund hierfür ist, dass der Dienst das Speichern eines lesbaren Namens mit einem erstellten `VoiceProfile` nicht zulässt und nur eine ID-Nummer zu Datenschutzzwecken speichert. In der `VerificationEnroll`-Funktion fügen Sie diesem Wörterbuch einen Eintrag mit der neu erstellten ID zusammen mit einem Textnamen hinzu. In Anwendungsentwicklungsszenarien, in denen Sie einen lesbaren Namen anzeigen müssen, **müssen Sie diese Zuordnung irgendwo speichern, da der Dienst sie nicht speichern kann**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Führen Sie das Skript aus, und Sie werden aufgefordert, die Phrase *My voice is my passport, verify me* (Meine Stimme ist mein Pass, überprüfen Sie mich) dreimal zur Registrierung und ein weiteres Mal für die Überprüfung zu sprechen. Als Ergebnis wird das Ähnlichkeitsergebnis zurückgegeben, mit dem Sie eigene benutzerdefinierte Schwellenwerte für die Überprüfung erstellen können.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Textunabhängige Überprüfung

Dies unterscheidet die **textabhängige** Überprüfung von der **textunabhängigen** Überprüfung:

* Sie erfordert nicht, dass eine bestimmte Passphrase gesprochen wird, alles kann gesprochen werden.
* Sie erfordert nicht drei Audiobeispiele, *erfordert* jedoch 20 Sekunden Gesamtaudiodaten.

Nehmen Sie einige einfache Änderungen an ihrer `VerificationEnroll`-Funktion vor, um zur **textunabhängigen** Überprüfung zu wechseln. Ändern Sie zunächst den Überprüfungstyp in `VoiceProfileType.TextIndependentVerification`. Ändern Sie als nächstes die `while`-Schleife, sodass `result.RemainingEnrollmentsSpeechLength` verfolgt wird und Sie kontinuierlich aufgefordert werden, zu sprechen, bis 20 Sekunden Audiodaten aufgezeichnet sind.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Führen Sie das Programm erneut aus, und sprechen Sie während der Überprüfungsphase einen beliebigen Text, da keine Passphrase erforderlich ist. Auch hier wird das Ähnlichkeitsergebnis zurückgegeben.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Sprecheridentifikation

Anhand der Sprecheridentifikation wird bestimmt, **welche** Stimme aus einer bestimmten Gruppe registrierter Stimmen spricht. Der Prozess ähnelt stark der **textunabhängigen** Überprüfung, wobei der Hauptunterschied ist, dass eine Überprüfung anhand mehrerer Stimmenprofile gleichzeitig erfolgen kann, anstatt einer Überprüfung anhand eines einzelnen Profils.

Erstellen Sie eine Funktion `IdentificationEnroll`, um mehrere Stimmenprofile zu registrieren. Der Registrierungsprozess für jedes Profil ist mit dem Registrierungsprozess für die **textunabhängige Überprüfung** identisch und erfordert für jedes Profil 20 Sekunden Audiodaten. Dieser Funktion wird eine Liste `profileNames` von Zeichenfolgen übergeben und sie erstellt für jeden Namen in der Liste ein neues Stimmenprofil. Die Funktion gibt eine Liste von `VoiceProfile`-Objekten zurück, die Sie in der nächsten Funktion zum Identifizieren eines Sprechers verwenden.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Erstellen Sie die folgende Funktion `SpeakerIdentification`, um eine Identifikationsanforderung zu senden. Der Hauptunterschied zwischen dieser Funktion und einer Anforderung zur **Sprecherüberprüfung** ist die Verwendung von `SpeakerIdentificationModel.FromProfiles()`, der eine Liste von `VoiceProfile`-Objekten übergeben wird. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

Ändern Sie die `Main()`-Funktion wie folgt. Sie erstellen eine Liste `profileNames` von Zeichenfolgen, die Sie Ihrer `IdentificationEnroll()`-Funktion übergeben. Dadurch werden Sie aufgefordert, für jeden Namen in dieser Liste ein neues Stimmenprofil zu erstellen. Sie können also weitere Namen hinzufügen, um zusätzliche Profile für Freunde oder Kollegen zu erstellen.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Wenn Sie das Skript ausführen, werden Sie aufgefordert, zu sprechen, um Stimmbeispiele für das erste Profil zu registrieren. Nachdem die Registrierung abgeschlossen ist, werden Sie aufgefordert, diesen Vorgang für jeden Namen in der Liste `profileNames` zu wiederholen. Nach Ende jeder Registrierung werden Sie aufgefordert, **eine beliebige Person** sprechen zu lassen, und der Dienst versucht, diese Person aus den registrierten Stimmprofilen zu identifizieren.

In diesem Beispiel werden nur die genaueste Übereinstimmung und ihr Ähnlichkeitsergebnis zurückgegeben, aber Sie können die vollständige Antwort erhalten, die die oberen fünf Ähnlichkeitsergebnisse einschließt, indem Sie `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` Ihrer `SpeakerIdentification`-Funktion hinzufügen.

## <a name="changing-audio-input-type"></a>Ändern des Audioeingabetyps

In den Beispielen dieses Artikels wird das standardmäßige Gerätemikrofon zur Eingabe der Audiobeispiele verwendet. In Szenarien, in denen Sie anstelle von Audiodateien Mikrofoneingaben verwenden müssen, ändern Sie jedoch einfach jede Instanz von `AudioConfig.FromDefaultMicrophoneInput()` in `AudioConfig.FromWavFileInput(path/to/your/file.wav)`, um zu einer Dateieingabe zu wechseln. Sie können auch gemischte Eingaben verwenden, z. B. ein Mikrofon zur Registrierung und Dateien zur Überprüfung.

## <a name="deleting-voice-profile-enrollments"></a>Löschen von Stimmenprofilregistrierungen

Um ein registriertes Profil zu löschen, verwenden Sie die `DeleteProfileAsync()`-Funktion des `VoiceProfileClient`-Objekts. Die folgende Beispielfunktion zeigt, wie Sie ein Stimmenprofil aus einer bekannten Stimmenprofil-ID löschen.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```