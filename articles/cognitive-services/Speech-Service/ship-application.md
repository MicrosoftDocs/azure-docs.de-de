---
title: Entwickeln von Apps mit dem Speech SDK – Speech Services
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Apps mit dem Speech SDK erstellen.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: d8330ff663a7adc2f1cae3ed6bdf1cec2fa972c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808453"
---
# <a name="ship-an-application"></a>Ausliefern einer Anwendung

Beachten Sie beim Verteilen des Azure Cognitive Services Speech SDK die [Lizenzbedingungen für das Speech SDK](https://aka.ms/csspeech/license201809) sowie die [Hinweise und Informationen zu Drittanbietersoftware](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html). Lesen Sie außerdem die [Datenschutzerklärung von Microsoft](https://aka.ms/csspeech/privacy).

Je nach Plattform bestehen unterschiedliche Abhängigkeiten zum Ausführen Ihrer Anwendung.

## <a name="windows"></a>Windows

Das Cognitive Services Speech SDK wird unter Windows 10 und unter Windows Server 2016 getestet.

Für das Cognitive Services Speech SDK muss [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) auf dem System installiert sein. Installationsprogramme für die neueste Version von `Microsoft Visual C++ Redistributable for Visual Studio 2019` können Sie hier herunterladen:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Wenn Ihre Anwendung verwalteten Code verwendet, muss `.NET Framework 4.6.1` oder höher auf dem Zielcomputer installiert sein.

Für die Mikrofoneingabe müssen die Media Foundation-Bibliotheken installiert sein. Diese Bibliotheken sind Bestandteil von Windows 10 und Windows Server 2016. Das Speech SDK kann ohne diese Bibliotheken verwendet werden, wenn als Audioeingabegerät kein Mikrofon verwendet wird.

Die erforderlichen Speech SDK-Dateien können im gleichen Verzeichnis wie die Anwendung bereitgestellt werden. Auf diese Weise kann die Anwendung direkt auf die Bibliotheken zugreifen. Stellen Sie sicher, dass Sie die richtige, der Anwendung entsprechende Version (Win32/x64) auswählen.

| NAME | Funktion
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, erforderlich für die native und verwaltete Bereitstellung
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Erforderlich für die verwaltete Bereitstellung

>[!NOTE]
> Ab Release 1.3.0 ist die Datei `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (im Lieferumfang von früheren Releases enthalten) nicht mehr erforderlich. Die Funktion ist jetzt in das Core-SDK integriert.

## <a name="linux"></a>Linux

Das Speech SDK unterstützt derzeit die Distributionen Ubuntu 16.04, 18.04 und Debian 9.
Für eine native Anwendung müssen Sie die Speech SDK-Bibliothek `libMicrosoft.CognitiveServices.Speech.core.so` ausliefern.
Stellen Sie sicher, dass Sie die der Anwendung entsprechende Version (x86, x64) auswählen. Je nach verwendeter Linux-Version müssen Sie möglicherweise auch die folgenden Abhängigkeiten einbinden:

* Freigegebene Bibliotheken der GNU C-Bibliothek (einschließlich der POSIX Threads Programming-Bibliothek `libpthreads`)
* OpenSSL-Bibliothek (`libssl.so.1.0.0` oder `libssl.so.1.0.2`)
* Freigegebene Bibliothek für ALSA-Anwendungen (`libasound.so.2`)

Unter Ubuntu sollten die GNU C-Bibliotheken bereits standardmäßig installiert sein. Die letzten drei Bibliotheken können mit folgenden Befehlen installiert werden:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Installieren Sie unter Debian 9 die folgenden Pakete:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Nächste Schritte

* [Abrufen Ihres Testabonnements für Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Erkennen von Sprache in C#](quickstart-csharp-dotnet-windows.md)
