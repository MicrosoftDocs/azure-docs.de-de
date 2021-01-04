---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: d71a7f6451cda5e2e50b5410140ac88361bf1735
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509491"
---
Laden Sie zunächst die Schlüsselwortmodelldatei mithilfe der statischen Funktion `FromFile()`, die ein `KeywordRecognitionModel` zurückgibt. Verwenden Sie den Pfad zu der `.table`-Datei, die Sie von Speech Studio heruntergeladen haben. Zusätzlich erstellen Sie eine `AudioConfig` unter Verwendung des Standardmikrofons und instanziieren dann ein neues `KeywordRecognizer` unter Verwendung der Audiokonfiguration.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Als nächstes erfolgt die Ausführung der Schlüsselworterkennung mit einem Aufruf von `RecognizeOnceAsync()` durch Übergabe Ihres Modellobjekts. Dadurch wird eine Sitzung zur Schlüsselworterkennung gestartet, die so lange dauert, bis das Schlüsselwort erkannt wird. Daher verwenden Sie dieses Entwurfsmuster in der Regel in Multithreadanwendungen oder in Anwendungsfällen, in denen Sie möglicherweise auf unbestimmte Zeit auf ein Aktivierungswort warten.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Das hier gezeigte Beispiel verwendet die lokale Schlüsselworterkennung, da es kein `SpeechConfig`-Objekt für den Authentifizierungskontext benötigt und das Back-End nicht kontaktiert. Sie können jedoch sowohl die Schlüsselworterkennung als auch die Überprüfung [unter Verwendung einer kontinuierlichen Back-End-Verbindung](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword) ausführen.