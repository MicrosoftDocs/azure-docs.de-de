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
ms.date: 07/05/2019
ms.author: fmegen
ms.custom: devx-track-csharp
ms.openlocfilehash: 87fa97dafe9de4a23f5eaadfd4083cd1ca517cde
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026589"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Informationen zur API für Audioeingabestreams des Speech SDK

Die API für **Audioeingabestreams** des Speech SDK bietet eine Möglichkeit zum Streamen von Audiodaten in die Erkennungen anstelle der Verwendung des Mikrofons oder der APIs für Eingabedateien.

Bei der Verwendung von Audioeingabestreams sind die folgenden Schritte erforderlich:

- Ermitteln Sie das Format des Audiodatenstroms. Das Format muss vom Speech SDK und dem Speech-Dienst unterstützt werden. Derzeit wird die nur folgende Konfiguration unterstützt:

  Audiobeispiele liegen im PCM-Format vor, ein Kanal, 16 Bit pro Abtastvorgang, 8000 oder 16000 Abtastvorgänge pro Sekunde (16000 oder 32000 Byte pro Sekunde), Ausrichtung zweier Blöcke (16 Bit mit Innenabstand für einen Abtastvorgang).

  Der entsprechende Code im SDK zum Erstellen des Audioformats sieht folgendermaßen aus:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Stellen Sie sicher, dass der Code die Audiorohdaten entsprechend diesen Spezifikationen bereitstellt. Stellen Sie darüber hinaus sicher, dass 16-Bit-Samples im Little-Endian-Format eingehen. Samples mit Vorzeichen werden ebenfalls unterstützt. Wenn die Audioquelldaten nicht mit den unterstützten Formaten übereinstimmen, muss das Audiomaterial in das erforderliche Format transcodiert werden.

- Erstellen Sie eine eigene Audioeingabestream-Klasse, die von `PullAudioInputStreamCallback` abgeleitet ist. Implementieren Sie die Member `Read()` und `Close()`. Die genaue Funktionssignatur ist sprachabhängig, der Code entspricht jedoch in etwa dem folgenden Codebeispiel:

  ```csharp
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

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/cognitive-services/)
- [Erkennen von Sprache in C#](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)