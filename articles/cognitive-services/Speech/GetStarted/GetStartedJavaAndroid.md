---
title: Erste Schritte mit der Spracherkennungs-API von Microsoft in Java unter Android | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die Sprach-API von Microsoft, um Android-Anwendungen zu entwickeln, die gesprochenes Audio in Text umwandeln.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 147042e300e629dd7e354d4e9079cc4855a8146c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515196"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Schnellstart: Verwenden der Bing-Spracheingabe-API in Java unter Android

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Mit der Bing-Spracheingabe-API können Sie Android-Anwendungen entwickeln, die gesprochene Audiosignale mithilfe der cloudbasierten Bing-Spracheingabe-API in Text konvertieren. Die API unterstützt Echtzeitstreaming. Hierdurch kann Ihre Anwendung während der Audioübermittlung an den Dienst gleichzeitig und asynchron partielle Erkennungsergebnisse empfangen.

Dieser Artikel veranschaulicht anhand einer Beispielanwendung, wie die Speech-Clientbibliothek für Android verwendet wird, um Spracherkennungsanwendungen in Java für Android-Geräte zu entwickeln.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="platform-requirements"></a>Plattformanforderungen

Das Beispiel wird von [Android Studio](https://developer.android.com/sdk/index.html) für Windows in Java entwickelt.

### <a name="get-the-client-library-and-sample-application"></a>Abrufen der Clientbibliothek und der Beispielanwendung

Die Speech-Clientbibliothek und die Beispiele für Android stehen im [Speech-Client-SDK für Android](https://github.com/microsoft/cognitive-speech-stt-android) zur Verfügung. Das erstellbare Beispiel befindet sich im Verzeichnis „samples/SpeechRecoExample“. Die beiden Bibliotheken, die Sie in Ihren eigenen Apps benötigen, finden Sie in „SpeechSDK/libs“ im Ordner „armeabi“ bzw. „x86“. Die Datei „libandroid_platform.so“ ist 22 MB groß, wird bei der Bereitstellung aber auf 4 MB reduziert.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Abonnieren der Sprach-API und Abrufen eines Schlüssels für ein kostenloses Probeabonnement

Die Sprach-API ist Teil von Cognitive Services (ehemals Project Oxford). Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/) können Sie Schlüssel für ein kostenloses Probeabonnement abrufen. Wählen Sie zunächst die Option für die Sprach-API aus, und klicken Sie anschließend auf **API-Schlüssel abrufen**, um den Schlüssel abzurufen. Daraufhin werden ein Primär- und ein Sekundärschlüssel zurückgegeben. Beide Schlüssel sind an das gleiche Kontingent gebunden. Es spielt daher keine Rolle, welchen Schlüssel Sie verwenden.

Wenn Sie eine *Erkennung mit Absicht* verwenden möchten, müssen Sie sich auch für [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) registrieren.

> [!IMPORTANT]
>* Rufen Sie einen Abonnementschlüssel ab. Für die Verwendung von Speech-Clientbibliotheken ist ein [Abonnementschlüssel](https://azure.microsoft.com/try/cognitive-services/) erforderlich.
>
>* Verwenden Sie Ihren Abonnementschlüssel. Aktualisieren Sie bei Verwendung der bereitgestellten Android-Beispielanwendung die Datei „samples/SpeechRecoExample/res/values/strings.xml“ mit Ihren Abonnementschlüsseln. Weitere Informationen finden Sie unter [Erstellen und Ausführen von Beispielen](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Verwenden der Speech-Clientbibliothek

Gehen Sie wie [hier](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library) beschrieben vor, um die Clientbibliothek in Ihrer Anwendung zu verwenden.

Die Referenz zur Clientbibliothek für Android befindet sich im Ordner „docs“ des [Speech-Client-SDK für Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Erstellen und Ausführen von Beispielen

Informationen zum Erstellen und Ausführen von Beispielen finden Sie auf [dieser Infoseite](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Erläuterung der Beispiele

### <a name="create-recognition-clients"></a>Erstellen von Erkennungsclients

Der Code im folgenden Beispiel veranschaulicht die Erstellung von Erkennungsclientklassen auf der Grundlage von Benutzerszenarien:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

Die Clientbibliothek bietet vorab implementierte Erkennungsclientklassen für typische Spracherkennungsszenarien:

* `DataRecognitionClient`: Spracherkennung mit PCM-Daten (etwa aus einer Datei oder Audiodatenquelle). Die Daten werden in Puffer aufgeteilt, und die einzelnen Puffer werden an den Speech-Dienst gesendet. Die Puffer bleiben unverändert. Der Benutzer kann also auf Wunsch seine eigene Stille-Erkennung anwenden. Wenn die Daten aus WAV-Dateien stammen, können Daten aus der Datei direkt an den Speech-Dienst gesendet werden. Im Falle von unformatierten Daten (etwa Bluetooth-Audio) müssen Sie zunächst einen Formatheader an den Speech-Dienst senden und anschließend die Daten.
* `MicrophoneRecognitionClient`: Spracherkennung mit Audiodaten vom Mikrofon. Vergewissern Sie sich, dass das Mikrofon eingeschaltet ist und die Daten vom Mikrofon an den Spracherkennungsdienst gesendet werden. Auf die Mikrofondaten wird eine integrierte Stille-Erkennung angewendet, bevor sie an den Erkennungsdienst gesendet werden.
* `DataRecognitionClientWithIntent` und `MicrophoneRecognitionClientWithIntent`: Diese Clients geben neben dem erkannten Text auch strukturierte Informationen zur Absicht des Sprechers zurück, die als Grundlage für weitere Anwendungsaktionen verwendet werden können. Zur Verwendung von Absichten müssen Sie zunächst mithilfe von [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ein Modell trainieren.

### <a name="recognition-language"></a>Erkennungssprache

Wenn Sie den Client mithilfe von `SpeechRecognitionServiceFactory` erstellen, müssen Sie eine Sprache auswählen. Eine vollständige Liste mit den unterstützten Sprachen des Speech-Diensts finden Sie unter [Unterstützte Sprachen](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Bei der Clienterstellung mit `SpeechRecognitionServiceFactory` muss außerdem `SpeechRecognitionMode` angegeben werden:

* `ShortPhrase`: Eine Äußerung mit einer Dauer von bis zu 15 Sekunden. Während der Datenübermittlung an den Dienst erhält der Client mehrere Teilergebnisse und ein Endergebnis mit mehreren Optionen („n“ beste Optionen).
* `LongDictation`: Eine Äußerung mit einer Dauer von bis zu zwei Minuten. Während der Datenübermittlung an den Dienst erhält der Client mehrere Teilergebnisse und mehrere Endergebnisse (abhängig davon, wo der Dienst Pausen zwischen Sätzen erkennt).

### <a name="attach-event-handlers"></a>Anfügen von Ereignishandlern

An den erstellten Client können verschiedene Ereignishandler angefügt werden:

* **Ereignisse für Teilergebnisse**: Dieses Ereignis wird jedes Mal aufgerufen, wenn der Speech-Dienst noch während des Sprechens (bei Verwendung von `MicrophoneRecognitionClient`) oder noch vor Abschluss der Datenübermittlung (bei Verwendung von `DataRecognitionClient`) versucht, das Gesagte vorauszusagen.
* **Fehlerereignisse**: Werden aufgerufen, wenn der Dienst einen Fehler erkennt.
* **Absichtsereignisse**: Werden für Clients vom Typ „WithIntent“ (nur im Modus `ShortPhrase`) aufgerufen, nachdem das Endergebnis der Erkennung analysiert und in eine strukturierte JSON-Absicht konvertiert wurde.
* **Ergebnisereignisse:**
  * Im Modus `ShortPhrase` wird dieses Ereignis aufgerufen und gibt die n besten Ergebnisse zurück, wenn Sie mit dem Sprechen fertig sind.
  * Im Modus `LongDictation` kann der Ereignishandler mehrmals aufgerufen werden (abhängig davon, wo der Dienst Pausen zwischen Sätzen erkennt).
  * **Für jede der n besten Optionen** werden ein Zuverlässigkeitswert und mehrere Formen des erkannten Texts zurückgegeben. Weitere Informationen finden Sie unter [Output format](../Concepts.md#output-format) (Ausgabeformat).

## <a name="related-topics"></a>Verwandte Themen

* [Referenz zur Clientbibliothek für Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Erste Schritte mit der Spracherkennungs-API in C# für .NET unter Windows](GetStartedCSharpDesktop.md)
* [Get started with the Speech Recognition API in Objective-C on iOS](Get-Started-ObjectiveC-iOS.md) (Erste Schritte mit der Spracherkennungs-API in Objective-C unter iOS)
* [Erste Schritte mit der Spracherkennungs-API in JavaScript](GetStartedJSWebsockets.md)
* [Get started with speech recognition by using the REST API](GetStartedREST.md) (Erste Schritte mit der Spracherkennung unter Verwendung der REST-API)
