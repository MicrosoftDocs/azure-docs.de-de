---
title: 'Schnellstart: Synthetisieren von Sprache, C++ (Linux): Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mit dem Speech SDK Sprache in C++ unter Linux synthetisieren.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: yinhew
ms.openlocfilehash: b5b768e52a40652840e9e3d3069df8a9c895f77b
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001622"
---
# <a name="quickstart-synthesize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Schnellstart: Synthetisieren von Sprache in C++ unter Linux mit dem Speech SDK

Schnellstarts sind auch für [Spracherkennung](quickstart-cpp-linux.md) verfügbar.

In diesem Artikel erstellen Sie eine C++-Konsolenanwendung für Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Mit dem [Speech SDK](speech-sdk.md) von Cognitive Services können Sie Text in Echtzeit in Sprache synthetisieren und das Ergebnis über den Lautsprecher Ihres PCs ausgeben. Die Anwendung wird mit dem [Speech SDK für Linux](https://aka.ms/csspeech/linuxbinary) und dem C++-Compiler Ihrer Linux-Distribution (z.B. `g++`) erstellt.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie einen Abonnementschlüssel für die Speech-Dienste. Sie können einen solchen Schlüssel kostenlos abrufen. Ausführliche Informationen finden Sie unter [Kostenloses Testen von Speech Services](get-started.md).

## <a name="install-speech-sdk"></a>Installieren des Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Die aktuelle Version des Cognitive Services Speech SDK ist `1.5.1`.

Das Speech SDK für Linux kann verwendet werden, um sowohl 32-Bit- als auch 64-Bit-Anwendungen zu erstellen. Die erforderlichen Bibliotheken und Headerdateien können als TAR-Datei von https://aka.ms/csspeech/linuxbinary heruntergeladen werden.

Laden Sie das SDK wie folgt herunter, und installieren Sie es:

1. Stellen Sie sicher, dass die Abhängigkeiten des SDK installiert sind.

   * Unter Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Unter Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

1. Wählen Sie ein Verzeichnis aus, in das die Dateien des Speech SDK extrahiert werden sollen, und legen Sie die Umgebungsvariable `SPEECHSDK_ROOT` so fest, dass sie auf dieses Verzeichnis verweist. Diese Variable erleichtert es, in zukünftigen Befehlen auf das Verzeichnis zu verweisen. Wenn Sie z.B. das Verzeichnis `speechsdk` in Ihrem Basisverzeichnis verwenden möchten, verwenden Sie einen Befehl wie den folgenden:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Erstellen Sie das Verzeichnis, wenn es noch nicht vorhanden ist:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Laden Sie das `.tar.gz`-Archiv mit den Speech SDK-Binärdateien herunter, und extrahieren Sie diese Dateien:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Überprüfen Sie den Inhalt des Verzeichnisses auf oberster Ebene des extrahierten Pakets:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Die Verzeichnisliste sollte die Benachrichtigungs- und Lizenzdateien von Drittanbietern enthalten sowie ein `include`-Verzeichnis mit Headerdateien (`.h`) und ein `lib`-Verzeichnis mit Bibliotheken.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Hinzufügen von Beispielcode

1. Erstellen Sie eine C++-Datei namens `helloworld.cpp`, und fügen Sie den folgenden Code in sie ein:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-linux/helloworld.cpp#code)]

1. Ersetzen Sie in dieser neuen Datei die Zeichenfolge `YourSubscriptionKey` durch Ihren Abonnementschlüssel für die Speech-Dienste.

1. Ersetzen Sie die Zeichenfolge `YourServiceRegion` durch die [Region](regions.md), die mit Ihrem Abonnement verknüpft ist (z. B. `westus` für das kostenlose Testabonnement).

## <a name="build-the-app"></a>Erstellen der App

> [!NOTE]
> Achten Sie darauf, dass Sie die folgenden Befehle als eine _einzelne Befehlszeile_ eingeben. Die einfachste Möglichkeit besteht darin, den Befehl zu kopieren, indem Sie die Schaltfläche **Kopieren** neben jedem Befehl verwenden und ihn dann in die Eingabeaufforderung Ihrer Shell einfügen.

* Führen Sie auf einem **x64**-Computer (64-Bit-System) den folgenden Befehl aus, um die Anwendung zu erstellen.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Führen Sie auf einem **x86**-Computer (32-Bit-System) den folgenden Befehl aus, um die Anwendung zu erstellen.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Ausführen der App

1. Konfigurieren Sie den Bibliothekspfad des Ladeprogramms so, dass er auf die Speech SDK-Bibliothek verweist.

   * Auf einem **x64**-System (64-Bit) geben Sie den folgenden Befehl ein.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Auf einem **x86**-System (32-Bit) geben Sie den folgenden Befehl ein.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Führen Sie die Anwendung aus.

   ```sh
   ./helloworld
   ```

1. In dem Konsolenfenster wird eine Eingabeaufforderung angezeigt. Geben Sie einige Wörter oder einen Satz ein. Der eingegebene Text wird an die Speech-Dienste übermittelt und in Sprache synthetisiert, die dann über Ihren Lautsprecher ausgegeben wird.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [C++-Beispiele auf GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Weitere Informationen

- [Anpassen von Voicefonts](how-to-customize-voice-font.md)
- [Aufnehmen von Sprachbeispielen](record-custom-voice-samples.md)
