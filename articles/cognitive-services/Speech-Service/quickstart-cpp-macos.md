---
title: 'Schnellstart: Erkennen von Sprache, C++ (macOS): Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie mit dem Speech SDK Sprache in C++ unter macOS erkennen.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: wolfma
ms.openlocfilehash: 498246292b3cf591433a709139621b039ac3af33
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002171"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Schnellstart: Erkennen von Sprache in C++ unter macOS mit dem Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In diesem Artikel erstellen Sie eine C++-Konsolenanwendung für macOS 10.13 und höhere Versionen. Sie verwenden das [Speech SDK](speech-sdk.md) von Cognitive Services, um Sprache über das Mikrofon Ihres Mac in Echtzeit in Text zu transkribieren. Die Anwendung wird mit dem [Speech SDK für macOS](https://aka.ms/csspeech/macosbinary) und dem standardmäßigen C++-Compiler Ihres Mac (beispielsweise `g++`) erstellt.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie einen Abonnementschlüssel für die Speech-Dienste. Sie können einen solchen Schlüssel kostenlos abrufen. Ausführliche Informationen finden Sie unter [Kostenloses Testen von Speech Services](get-started.md).

## <a name="install-speech-sdk"></a>Installieren des Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Die aktuelle Version des Cognitive Services Speech SDK ist `1.5.1`.

Das Speech SDK für macOS kann als gezipptes Frameworkpaket unter https://aka.ms/csspeech/macosbinary heruntergeladen werden.

Laden Sie das SDK wie folgt herunter, und installieren Sie es:

1. Wählen Sie ein Verzeichnis aus, in das die Dateien des Speech SDK extrahiert werden sollen, und legen Sie die Umgebungsvariable `SPEECHSDK_ROOT` so fest, dass sie auf dieses Verzeichnis verweist. Diese Variable erleichtert es, in zukünftigen Befehlen auf das Verzeichnis zu verweisen. Wenn Sie z.B. das Verzeichnis `speechsdk` in Ihrem Basisverzeichnis verwenden möchten, verwenden Sie einen Befehl wie den folgenden:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Erstellen Sie das Verzeichnis, wenn es noch nicht vorhanden ist:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Laden Sie das Archiv `.zip` mit dem Speech SDK-Framework herunter, und extrahieren Sie es:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Überprüfen Sie den Inhalt des Verzeichnisses auf oberster Ebene des extrahierten Pakets:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Die Verzeichnisliste muss den Drittanbieterhinweis und die Lizenzdateien sowie ein Verzeichnis namens `MicrosoftCognitiveServicesSpeech.framework` enthalten.

## <a name="add-sample-code"></a>Hinzufügen von Beispielcode

1. Erstellen Sie eine C++-Datei namens `helloworld.cpp`, und fügen Sie den folgenden Code in sie ein:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. Ersetzen Sie in dieser neuen Datei die Zeichenfolge `YourSubscriptionKey` durch Ihren Abonnementschlüssel für die Speech-Dienste.

1. Ersetzen Sie die Zeichenfolge `YourServiceRegion` durch die [Region](regions.md), die mit Ihrem Abonnement verknüpft ist (z. B. `westus` für das kostenlose Testabonnement).

## <a name="build-the-app"></a>Erstellen der App

> [!NOTE]
> Achten Sie darauf, dass Sie die folgenden Befehle als eine _einzelne Befehlszeile_ eingeben. Die einfachste Möglichkeit besteht darin, den Befehl zu kopieren, indem Sie die Schaltfläche **Kopieren** neben jedem Befehl verwenden und ihn dann in die Eingabeaufforderung Ihrer Shell einfügen.

* Führen Sie den folgenden Befehl aus, um die Anwendung zu erstellen:

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Ausführen der App

1. Konfigurieren Sie den Bibliothekspfad des Ladeprogramms so, dass er auf die Speech SDK-Bibliothek verweist.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Führen Sie die Anwendung aus.

   ```sh
   ./helloworld
   ```

1. Im Konsolenfenster wird eine Eingabeaufforderung angezeigt, die Sie auffordert, etwas zu sagen. Sprechen Sie einen englischen Ausdruck oder Satz. Ihre Spracheingabe wird an die Speech-Dienste übermittelt, in Text transkribiert und im gleichen Fenster angezeigt.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [C++-Beispiele auf GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Weitere Informationen

- [Tutorial: Erstellen eines benutzerdefinierten Akustikmodells](how-to-customize-acoustic-models.md)
- [Tutorial: Erstellen eines benutzerdefinierten Sprachmodells](how-to-customize-language-model.md)

