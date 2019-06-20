---
title: Erste Schritte mit der Microsoft-Spracherkennungs-API unter Verwendung der C#-Dienstbibliothek | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die Dienstbibliothek der Bing-Spracheingabe-API, um gesprochene Sprache in Text umzuwandeln.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f445d1fff48ee7a04c0b1c1d64c808f87d824b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515230"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Schnellstart: Verwenden der Dienstbibliothek der Bing-Spracheingabe-API in C&#35; für .NET unter Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Die Dienstbibliothek ist für Entwickler gedacht, die über einen eigenen Clouddienst verfügen und in ihrem Dienst den Speech-Dienst aufrufen möchten. Verwenden Sie dieses SDK nicht, wenn Sie den Spracherkennungsdienst über gerätegebundene Apps aufrufen möchten. (Verwenden Sie in diesem Fall andere Clientbibliotheken oder REST-APIs.)

Wenn Sie die C#-Dienstbibliothek verwenden möchten, müssen Sie das [NuGet-Paket „Microsoft.Bing.Speech“](https://www.nuget.org/packages/Microsoft.Bing.Speech/) installieren. Die API-Referenz der Bibliothek finden Sie in der [C#-Dienstbibliothek für Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

In den folgenden Abschnitten erfahren Sie, wie Sie die C#-Beispielanwendung unter Verwendung der C#-Dienstbibliothek installieren, erstellen und ausführen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="platform-requirements"></a>Plattformanforderungen

Das folgende Beispiel wurde unter Verwendung von [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) für Windows 8 (und höhere Versionen) und .NET Framework 4.5 (und höhere Versionen) entwickelt.

### <a name="get-the-sample-application"></a>Abrufen der Beispielanwendung

Klonen Sie das Beispiel aus dem Repository mit dem [C#-Dienstbibliothekbeispiel für Speech](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Abonnieren der Spracherkennungs-API und Abrufen eines Schlüssels für ein kostenloses Probeabonnement

Die Sprach-API ist Teil von Cognitive Services (ehemals Project Oxford). Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/) können Sie Schlüssel für ein kostenloses Probeabonnement abrufen. Wählen Sie zunächst die Option für die Sprach-API aus, und klicken Sie anschließend auf **API-Schlüssel abrufen**, um den Schlüssel abzurufen. Daraufhin werden ein Primär- und ein Sekundärschlüssel zurückgegeben. Beide Schlüssel sind an das gleiche Kontingent gebunden. Es spielt daher keine Rolle, welchen Schlüssel Sie verwenden.

> [!IMPORTANT]
> * Rufen Sie einen Abonnementschlüssel ab. Für die Verwendung der Speech-Clientbibliotheken ist ein [Abonnementschlüssel](https://azure.microsoft.com/try/cognitive-services/) erforderlich.
>
> * Verwenden Sie Ihren Abonnementschlüssel. Bei der bereitgestellten Beispielanwendung für die C#-Dienstbibliothek müssen Sie Ihren Abonnementschlüssel als Befehlszeilenparameter angeben. Weitere Informationen finden Sie unter [Ausführen der Beispielanwendung](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Schritt 1: Installieren der Beispielanwendung

1. Starten Sie Visual Studio 2015, und klicken Sie auf **Datei** > **Öffnen** > **Projekt/Projektmappe**.

2. Doppelklicken Sie auf die SLN-Datei „SpeechClient.sln“ (eine Visual Studio 2015-Projektmappe). Die Projektmappe wird in Visual Studio geöffnet.

## <a name="step-2-build-the-sample-application"></a>Schritt 2: Erstellen der Beispielanwendung

Drücken Sie STRG+UMSCHALT+B, oder klicken Sie auf dem Menüband auf **Erstellen**. Klicken Sie anschließend auf **Projektmappe erstellen**.

## <a name="step-3-run-the-sample-application"></a>Schritt 3: Ausführen der Beispielanwendung

1. Drücken Sie nach Abschluss des Buildvorgangs F5, oder klicken Sie auf dem Menüband auf **Starten**, um das Beispiel auszuführen.

2. Öffnen Sie das Ausgabeverzeichnis für das Beispiel (beispielsweise „SpeechClientSample\bin\Debug“). Klicken Sie bei gedrückter UMSCHALTTASTE mit der rechten Maustaste, und klicken Sie auf **Eingabeaufforderung hier öffnen**.

3. Führen Sie `SpeechClientSample.exe` mit folgenden Argumenten aus:

   * Arg[0]: Geben Sie eine WAV-Audioeingabedatei an.
   * Arg[1]: Geben Sie das Audiogebietsschema an.
   * Arg[2]: Geben Sie die Spracherkennungsmodi an: *Short* für den `ShortPhrase`-Modus und *Long* für den `LongDictation`-Modus.
   * Arg[3]: Geben Sie den Abonnementschlüssel für den Zugriff auf den Spracherkennungsdienst an.

## <a name="samples-explained"></a>Erläuterung der Beispiele

### <a name="recognition-modes"></a>Erkennungsmodi

* `ShortPhrase`-Modus: Eine Äußerung mit einer Dauer von bis zu 15 Sekunden. Während der Datenübermittlung an den Server erhält der Client mehrere Teilergebnisse und ein bestmögliches Endergebnis.
* `LongDictation`-Modus: Eine Äußerung mit einer Dauer von bis zu zehn Minuten. Während der Datenübermittlung an den Server erhält der Client mehrere Teilergebnisse und mehrere Endergebnisse (abhängig davon, wo der Server Pausen zwischen Sätzen erkennt).

### <a name="supported-audio-formats"></a>Unterstützte Audioformate

Die Sprach-API unterstützt Audio/WAV mit folgenden Codecs:

* PCM (einzelner Kanal)
* Siren
* SirenSR

### <a name="preferences"></a>Einstellungen

Wenn Sie einen Speech-Client erstellen möchten, müssen Sie zunächst ein Preferences-Objekt erstellen. Bei dem Preferences-Objekt handelt es sich um eine Reihe von Parametern, die das Verhalten des Speech-Diensts konfigurieren. Es umfasst folgende Felder:

* `SpeechLanguage`: Das Gebietsschema der an den Speech-Dienst gesendeten Audiodaten.
* `ServiceUri`: Der Endpunkt zum Aufrufen des Speech-Diensts.
* `AuthorizationProvider`: Eine IAuthorizationProvider-Implementierung zum Abrufen von Token für den Zugriff auf den Speech-Dienst. In dem Beispiel wird zwar ein Cognitive Services-Autorisierungsanbieter bereitgestellt, es wird jedoch dringend empfohlen, eine eigene Implementierung für die Tokenzwischenspeicherung zu erstellen.
* `EnableAudioBuffering`: Eine erweiterte Option. Weitere Informationen finden Sie unter [Verbindungsverwaltung](#connection-management).

### <a name="speech-input"></a>Spracheingabe

Das SpeechInput-Objekt setzt sich aus zwei Feldern zusammen:

* **Audio**: Eine Datenstromimplementierung Ihrer Wahl, aus der das SDK Audiodaten bezieht. Dabei kann es sich um einen beliebigen [Datenstrom](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) handeln, der Lesevorgänge unterstützt.
   > [!NOTE]
   > Das SDK erkennt das Ende des Datenstroms, wenn der Datenstrom bei einem Lesevorgang **0** zurückgibt.

* **RequestMetadata**: Metadaten zur Sprachanforderung. Weitere Informationen finden Sie in der [Referenz](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Sprachanforderung

Nachdem Sie einen Speech-Client und SpeechInput-Objekte instanziiert haben, verwenden Sie „RecognizeAsync“, um eine Anforderung an den Speech-Dienst zu senden.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Nach Abschluss der Anforderung wird die von „RecognizeAsync“ zurückgegebene Aufgabe abgeschlossen. Das letzte Erkennungsergebnis (RecognitionResult) ist das Ende der Erkennung. Bei einem unerwarteten Dienst- oder SDK-Fehler ist die Aufgabe unter Umständen nicht erfolgreich.

### <a name="speech-recognition-events"></a>Spracherkennungsereignisse

#### <a name="partial-results-event"></a>Ereignis für Teilergebnisse

Dieses Ereignis wird jedes Mal aufgerufen, wenn der Speech-Dienst noch während des Sprechens (bei Verwendung von `MicrophoneRecognitionClient`) oder noch vor Abschluss der Datenübermittlung (bei Verwendung von `DataRecognitionClient`) versucht, das Gesagte vorauszusagen. Das Ereignis kann mithilfe von `SpeechClient.SubscribeToPartialResult()` abonniert werden. Sie können aber auch die generische Ereignisabonnementmethode `SpeechClient.SubscribeTo<RecognitionPartialResult>()` verwenden.

**Rückgabeformat** | BESCHREIBUNG |
------|------
**LexicalForm** | Dieses Format ist optimal für Anwendungen, die unformatierte, unverarbeitete Spracherkennungsergebnisse benötigen.
**DisplayText** | Der erkannte Ausdruck mit inverser Textnormalisierung, Groß-/Kleinschreibung, Zeichensetzung und Filterung anstößiger Ausdrücke. Anstößige Ausdrücke werden nach dem Anfangszeichen durch Sternchen ersetzt (Beispiel: P***). Dieses Format ist optimal für Anwendungen, bei denen die Spracherkennungsergebnisse einem Benutzer angezeigt werden.
**Confidence** | Das Konfidenzniveau des erkannten Ausdrucks für das dazugehörige Audio gemäß Definition des Spracherkennungsservers.
**MediaTime** | Die aktuelle Zeit in Relation zum Beginn des Audiostreams (Zeiteinheit: 100 Nanosekunden).
**MediaDuration** | Die aktuelle Ausdrucksdauer/-länge in Relation zum Audiosegment (Zeiteinheit: 100 Nanosekunden).

#### <a name="result-event"></a>Ergebnisereignis
Dieses Ereignis wird aufgerufen, wenn Sie mit Sprechen fertig sind (im Modus `ShortPhrase`). Ihnen werden die n besten Ergebnisoptionen bereitgestellt. Im Modus `LongDictation` kann das Ereignis mehrmals aufgerufen werden (abhängig davon, wo der Server Pausen zwischen Sätzen erkennt). Das Ereignis kann mithilfe von `SpeechClient.SubscribeToRecognitionResult()` abonniert werden. Sie können aber auch die generische Ereignisabonnementmethode `SpeechClient.SubscribeTo<RecognitionResult>()` verwenden.

**Rückgabeformat** | BESCHREIBUNG |
------|------|
**RecognitionStatus** | Der Status, der angibt, wie die Erkennung zustande gekommen ist. Sie kann beispielsweise infolge einer erfolgreichen Erkennung oder infolge eines Verbindungsabbruchs zustande gekommen sein.
**Phrases** | Die n besten erkannten Ausdrücke mit der Erkennungszuverlässigkeit.

Weitere Informationen zu Erkennungsergebnissen finden Sie unter [Ausgabeformat](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Spracherkennungsantwort

Beispiel für eine Antwort von Speech:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>Verbindungsverwaltung

Die API verwendet jeweils eine einzelne WebSocket-Verbindung pro Anforderung. Für eine optimale Benutzererfahrung versucht das SDK, die Verbindung mit dem Speech-Dienst wiederherzustellen und die Erkennung ab dem letzten empfangenen Erkennungsergebnis (RecognitionResult) fortzusetzen. Wenn die Audioanforderung also beispielsweise zwei Minuten lang ist, das SDK bei Minute 1 ein Erkennungsereignis (RecognitionEvent) empfängt und fünf Sekunden später das Netzwerk ausfällt, stellt das SDK eine neue Verbindung her, die bei Minute 1 beginnt.

>[!NOTE]
>Das SDK führt keine Rückwärtssuche zu Minute 1 durch, da dies vom Datenstrom unter Umständen nicht unterstützt wird. Stattdessen verwendet das SDK einen internen Audiodatenpuffer, der gelöscht wird, wenn RecognitionResult-Ereignisse eingehen.

## <a name="buffering-behavior"></a>Pufferverhalten

Audiodaten werden vom SDK standardmäßig gepuffert, um im Falle einer Netzwerkunterbrechung eine Wiederherstellung zu ermöglichen. In Szenarien, in denen Audiodaten, die aufgrund einer getrennten Netzwerkverbindung verloren gegangen sind, verworfen werden sollen und die Verbindung neu gestartet werden soll, empfiehlt es sich, die Audiopufferung zu deaktivieren. Legen Sie hierzu `EnableAudioBuffering` im Preferences-Objekt auf `false` fest.

## <a name="related-topics"></a>Verwandte Themen

[C#-Dienstbibliothekreferenz für Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
