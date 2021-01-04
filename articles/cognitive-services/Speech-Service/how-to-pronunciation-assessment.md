---
title: Verwenden des Speech SDK zur Bewertung der Aussprache
titleSuffix: Azure Cognitive Services
description: Das Speech SDK unterstützt die Aussprachebewertung, bei der die Qualität der Aussprache von Spracheingaben mit Indikatoren für Genauigkeit, Redefluss, Vollständigkeit usw. bewertet wird.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 1cc313daf0e76ddd14865959410b07d9bdc189d7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984920"
---
# <a name="pronunciation-assessment"></a>Aussprachebewertung

Die Aussprachebewertung bewertet die Aussprache und gibt den Rednern Feedback zur Genauigkeit und zum Redefluss der gesprochenen Audioinformationen.
Mit dieser Aussprachebewertung erhalten Menschen, die Fremdsprachen lernen, direktes Feedback und können ihre Aussprache verbessern, sodass sie selbstbewusst sprechen und sich präsentieren können.
Lehrkräfte können diese Funktion verwenden, um die Aussprache mehrerer Redner in Echtzeit auszuwerten.

In diesem Artikel erfahren Sie, wie Sie `PronunciationAssessmentConfig` einrichten und `PronunciationAssessmentResult` mit dem Speech SDK abrufen.

> [!NOTE]
> Das Feature zur Aussprachebewertung ist derzeit nur in den Regionen `westus`, `eastasia` und `centralindia` verfügbar und unterstützt nur die Sprache `en-US`.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Aussprachebewertung mit dem Speech SDK

In den folgenden Beispielen erstellen Sie eine `PronunciationAssessmentConfig`, und wenden sie dann auf einen `SpeechRecognizer` an.

Die folgenden Codeausschnitte veranschaulichen die Verwendung der automatischen Spracherkennung in ihren Apps:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Konfigurationsparameter der Aussprachebewertung

In dieser Tabelle sind die Konfigurationsparameter für die Aussprachebewertung aufgeführt.

| Parameter | BESCHREIBUNG | Erforderlich? |
|-----------|-------------|---------------------|
| ReferenceText | Der Text, für den die Aussprache ausgewertet wird. | Erforderlich |
| GradingSystem | Das Punktesystem zur Kalibrierung der Bewertung. Das `FivePoint`-System gibt eine 0-5-Gleitkommabewertung und `HundredMark` eine 0-100-Gleitkommabewertung aus. Standardwert: `FivePoint`. | Optional |
| Granularität | Die Granularität der Auswertung. Akzeptierte Werte sind `Phoneme`, für das die Bewertung auf Volltext-, Wort- und Phonemebene, `Word`, für das die Bewertung auf Volltext und Wortebene und `FullText`, für das nur die Bewertung auf der Volltextebene angezeigt wird. Standardwert: `Phoneme`. | Optional |
| EnableMiscue | Aktiviert die Fehlschlagsberechnung. Wenn diese Option aktiviert ist, werden die ausgesprochenen Wörter mit dem Referenztext verglichen und auf der Grundlage des Vergleichs mit Auslassung/Einfügung gekennzeichnet. Zulässige Werte sind `False` und `True`. Standardwert: `False`. | Optional |
| ScenarioId | Eine GUID, die ein benutzerdefiniertes Punktesystem angibt. | Optional |

### <a name="pronunciation-assessment-result-parameters"></a>Ergebnisparameter für die Aussprachebewertung

Diese Tabelle listet die Ergebnisparameter der Aussprachebewertung auf.

| Parameter | Beschreibung |
|-----------|-------------|
| `AccuracyScore` | Genauigkeit der Aussprache des Texts. Genauigkeit heißt dabei, wie exakt Phoneme der Aussprache eines Muttersprachlers entsprechen. Der Score für Genauigkeit auf Wort- und Volltextebene wird aus dem Score der Genauigkeit auf Phonemebene aggregiert. |
| `FluencyScore` | Redefluss eines gegebenen Texts. Der Redefluss bedeutet, wie exakt der ausgegebene Text mit den Pausen zwischen Wörtern eines Muttersprachlers übereinstimmt. |
| `CompletenessScore` | Vollständigkeit des Texts. Dafür wird das Verhältnis ausgesprochener Wörter zur Texteingabe berechnet. |
| `PronunciationScore` | Der Gesamtscore, der die Aussprachequalität des übergebenen Sprachtexts angibt. Dieser wird aus `AccuracyScore`, `FluencyScore` und `CompletenessScore` mit Gewichtung aggregiert. |
| `ErrorType` | Dieser Wert gibt an, ob ein Wort im Vergleich zu `ReferenceText`ausgelassen, eingefügt oder schlecht ausgesprochen wird. Die möglichen Werte sind `None` (d. h. kein Fehler in diesem Wort), `Omission`, `Insertion` und `Mispronunciation`. |

## <a name="next-steps"></a>Nächste Schritte

<!-- TODO: update the sample links after release -->

<!-- ::: zone pivot="programming-language-csharp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-cpp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-java"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-python"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-objectivec"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) on GitHub for automatic language detection
::: zone-end -->

* [Speech SDK-Referenzdokumentation](speech-sdk.md)
