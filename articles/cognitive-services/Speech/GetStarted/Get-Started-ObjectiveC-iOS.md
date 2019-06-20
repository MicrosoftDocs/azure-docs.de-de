---
title: Erste Schritte mit der Bing-Spracheingabe-API in Objective-C unter iOS | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die Bing-Spracheingabe-API, um iOS-Anwendungen zu entwickeln, mit denen Sprachaudiodaten in Text konvertiert werden.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 09b7e8961e59bd6fad49408c28e9ee9a4a209cae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515289"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Schnellstart: Verwenden der Bing-Spracheingabe-API in Objective-C unter iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Mit der Spracherkennungs-API können Sie iOS-Anwendungen entwickeln, die Sprachaudiodaten mithilfe des cloudbasierten Speech-Diensts in Text umwandeln. Die API unterstützt Echtzeitstreaming. Hierdurch kann Ihre Anwendung während der Audioübermittlung an den Dienst gleichzeitig und asynchron partielle Erkennungsergebnisse empfangen.

In diesem Artikel wird eine Beispielanwendung verwendet, um die Grundlagen zu den ersten Schritten mit der Spracherkennungs-API zur Entwicklung einer iOS-Anwendung zu veranschaulichen. Eine vollständige API-Referenz finden Sie in der [Referenz zur Speech-SDK-Clientbibliothek](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Voraussetzungen

### <a name="platform-requirements"></a>Plattformanforderungen

Stellen Sie sicher, dass die Mac XCode IDE installiert ist.

### <a name="get-the-client-library-and-examples"></a>Abrufen der Clientbibliothek und der Beispiele

Die Speech-Clientbibliothek und die Beispiele für iOS sind im [Speech-Client-SDK für iOS](https://github.com/microsoft/cognitive-speech-stt-ios) verfügbar.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Abonnieren der Spracherkennungs-API und Abrufen eines Schlüssels für ein kostenloses Probeabonnement

Die Sprach-API ist Teil von Cognitive Services (ehemals Project Oxford). Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/) können Sie Schlüssel für ein kostenloses Probeabonnement abrufen. Wählen Sie zunächst die Option für die Sprach-API aus, und klicken Sie anschließend auf **API-Schlüssel abrufen**, um den Schlüssel abzurufen. Daraufhin werden ein Primär- und ein Sekundärschlüssel zurückgegeben. Beide Schlüssel sind an das gleiche Kontingent gebunden. Es spielt daher keine Rolle, welchen Schlüssel Sie verwenden.

Wenn Sie eine *Erkennung mit Absicht* verwenden möchten, müssen Sie sich auch für [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) registrieren.

> [!IMPORTANT]
> * Rufen Sie einen Abonnementschlüssel ab. Für die Verwendung von Speech-Clientbibliotheken ist ein [Abonnementschlüssel](https://azure.microsoft.com/try/cognitive-services/) erforderlich.
>
> * Verwenden Sie Ihren Abonnementschlüssel. Mit der bereitgestellten iOS-Beispielanwendung müssen Sie die Datei „Samples/SpeechRecognitionServerExample/settings.plist“ mit Ihrem Abonnementschlüssel aktualisieren. Weitere Informationen finden Sie unter [Erstellen und Ausführen von Beispielen](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Verwenden der Speech-Clientbibliothek

Befolgen Sie diese [Anleitung](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library), um die Clientbibliothek einem XCode-Projekt hinzuzufügen.

Die Referenz zur Clientbibliothek für iOS finden Sie auf dieser [Webseite](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Erstellen und Ausführen von Beispielen

Informationen zum Erstellen und Ausführen von Beispielen finden Sie auf [dieser Infoseite](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Erläuterung der Beispiele

### <a name="create-recognition-clients"></a>Erstellen von Erkennungsclients

Der folgende Code im Beispiel veranschaulicht die Erstellung von Erkennungsclientklassen auf der Grundlage von Benutzerszenarien:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

Die Clientbibliothek bietet vorab implementierte Erkennungsclientklassen für typische Spracherkennungsszenarien:

* `DataRecognitionClient`: Spracherkennung mit PCM-Daten (etwa aus einer Datei oder Audiodatenquelle). Die Daten werden in Puffer aufgeteilt, und die einzelnen Puffer werden an den Speech-Dienst gesendet. Die Puffer bleiben unverändert. Benutzer können also auf Wunsch eine eigene Stille-Erkennung anwenden. Wenn die Daten aus WAV-Dateien stammen, können Daten aus der Datei direkt an den Server gesendet werden. Im Falle von unformatierten Daten (z.B. Bluetooth-Audio) müssen Sie zunächst einen Formatheader an den Server senden, gefolgt von den Daten.
* `MicrophoneRecognitionClient`: Spracherkennung mit Audiodaten vom Mikrofon. Vergewissern Sie sich, dass das Mikrofon eingeschaltet ist und diese Daten vom Mikrofon an den Spracherkennungsdienst gesendet werden. Auf die Mikrofondaten wird eine integrierte Stille-Erkennung angewendet, bevor sie an den Erkennungsdienst gesendet werden.
* `DataRecognitionClientWithIntent` und `MicrophoneRecognitionClientWithIntent`: Neben dem erkannten Text geben diese Clients auch strukturierte Informationen zur Absicht des Sprechers zurück, die von Ihren Anwendungen als Grundlage für weitere Aktionen verwendet werden können. Zur Verwendung von Absichten müssen Sie zunächst mithilfe von [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ein Modell trainieren.

### <a name="recognition-language"></a>Erkennungssprache

Wenn Sie den Client mithilfe von `SpeechRecognitionServiceFactory` erstellen, müssen Sie eine Sprache auswählen. Eine vollständige Liste mit den unterstützten Sprachen des Speech-Diensts finden Sie unter [Unterstützte Sprachen](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Bei der Clienterstellung mit `SpeechRecognitionServiceFactory` muss außerdem `SpeechRecognitionMode` angegeben werden:

* `SpeechRecognitionMode_ShortPhrase`: Eine Äußerung mit einer Dauer von bis zu 15 Sekunden. Während der Datenübermittlung an den Dienst erhält der Client mehrere Teilergebnisse und ein Endergebnis mit mehreren Optionen („n“ beste Optionen).
* `SpeechRecognitionMode_LongDictation`: Eine Äußerung mit einer Dauer von bis zu zwei Minuten. Während der Datenübermittlung an den Dienst erhält der Client mehrere Teil- und Endergebnisse (abhängig davon, wo der Server Pausen zwischen Sätzen erkennt).

### <a name="attach-event-handlers"></a>Anfügen von Ereignishandlern

An den erstellten Client können verschiedene Ereignishandler angefügt werden:

* **Ereignis für Teilergebnisse**: Dieses Ereignis wird jedes Mal aufgerufen, wenn der Speech-Dienst noch während des Sprechens (bei Verwendung von `MicrophoneRecognitionClient`) oder noch vor Abschluss der Datenübermittlung (bei Verwendung von `DataRecognitionClient`) versucht, das Gesagte vorherzusagen.
* **Fehlerereignisse**: Werden aufgerufen, wenn der Dienst einen Fehler erkennt.
* **Absichtsereignisse**: Werden für Clients vom Typ „WithIntent“ (nur im ShortPhrase-Modus) aufgerufen, nachdem das Endergebnis der Erkennung analysiert und in eine strukturierte JSON-Absicht konvertiert wurde.
* **Ergebnisereignisse:**
  * Im Modus `SpeechRecognitionMode_ShortPhrase` wird dieses Ereignis aufgerufen und gibt die n besten Ergebnisse zurück, wenn Sie mit dem Sprechen fertig sind.
  * Im Modus `SpeechRecognitionMode_LongDictation` kann der Ereignishandler mehrmals aufgerufen werden (abhängig davon, wo der Dienst Pausen zwischen Sätzen erkennt).
  * **Für jede der n besten Optionen** werden ein Zuverlässigkeitswert und mehrere Formen des erkannten Texts zurückgegeben. Weitere Informationen finden Sie unter [Output format](../Concepts.md#output-format) (Ausgabeformat).

## <a name="related-topics"></a>Verwandte Themen

* [Referenz zur Clientbibliothek für iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Get started with speech recognition in Java on Android](GetStartedJavaAndroid.md) (Erste Schritte mit der Spracherkennung von Microsoft bzw. mit Absichten in Java unter Android)
* [Get started with the Speech Recognition API in JavaScript](GetStartedJSWebsockets.md) (Erste Schritte mit der Spracherkennungs-API von Microsoft in JavaScript)
* [Get started with speech recognition by using the REST API](GetStartedREST.md) (Erste Schritte mit der Spracherkennung unter Verwendung der REST-API)
