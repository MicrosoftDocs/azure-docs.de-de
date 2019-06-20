---
title: Speech SDK-Audioeingabestream – Konzepte
titleSuffix: Azure Cognitive Services
description: Eine Übersicht über die Funktionen der API für Audioeingabestreams des Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: f34504dddaa21cc162476d1fdb462e7be5c48629
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60697912"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Informationen zur API für Audioeingabestreams des Speech SDK

Die API für **Audioeingabestreams** des Speech SDK bietet eine Möglichkeit zum Streamen von Audiodatenströmen in die Erkennungen anstelle der Verwendung des Mikrofons oder der APIs für Eingabedateien.

Bei der Verwendung von Audioeingabestreams sind die folgenden Schritte erforderlich:

- Ermitteln Sie das Format des Audiodatenstroms. Das Format muss vom Speech SDK und dem Speech-Dienst unterstützt werden. Derzeit wird die nur folgende Konfiguration unterstützt:

  Audiobeispiele im PCM-Format, ein Kanal, 16.000 Abtastvorgänge pro Sekunde, 32.000 Byte pro Sekunde, Ausrichtung zweier Blöcke (16 Bit mit Innenabstand für einen Abtastvorgang), 16 Bit pro Abtastvorgang

  Der entsprechende Code im SDK zum Erstellen des Audioformats sieht folgendermaßen aus:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Stellen Sie sicher, dass der Code die Audiorohdaten entsprechend diesen Spezifikationen bereitstellen kann. Wenn die Audioquelldaten nicht mit den unterstützten Formaten übereinstimmen, muss das Audiomaterial in das erforderliche Format transcodiert werden.

- Erstellen Sie eine eigene Audioeingabestream-Klasse, die von `PullAudioInputStreamCallback` abgeleitet ist. Implementieren Sie die Member `Read()` und `Close()`. Die genaue Funktionssignatur ist sprachabhängig, der Code entspricht jedoch in etwa dem folgenden Codebeispiel:

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Erstellen Sie eine Audiokonfiguration basierend auf Ihrem Audioformat und dem Eingabestream. Übergeben Sie die reguläre Speech-Konfiguration und die Konfiguration für Audioeingabestreams, wenn Sie die Erkennung erstellen. Beispiel:

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Nächste Schritte

* [Abrufen Ihres Testabonnements für Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Erkennen von Sprache in C#](quickstart-csharp-dotnet-windows.md)
