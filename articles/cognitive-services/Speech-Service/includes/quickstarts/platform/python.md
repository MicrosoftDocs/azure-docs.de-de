---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: trbye
ms.openlocfilehash: d50d4c554f47629f6e04adf957e02f8ffcc48fe5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509520"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Sprach-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für Python installieren. Führen Sie `pip install azure-cognitiveservices-speech` aus, wenn Sie nur den Paketnamen benötigen und selbständig einsteigen möchten.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

- Das Python Speech SDK-Paket ist für die folgenden Betriebssysteme verfügbar:
  - Windows: x64 und x86
  - Mac: macOS X Version 10.12 oder höher
  - Linux: Zeigen Sie die Liste mit den [unterstützten Linux-Distributionen und Zielarchitekturen](~/articles/cognitive-services/speech-service/speech-sdk.md) an.

## <a name="prerequisites"></a>Voraussetzungen

- Für unterstützte Linux-Plattformen müssen bestimmte Bibliotheken installiert sein (`libssl` für die Unterstützung von Secure Sockets Layer und `libasound2` für Audiounterstützung). Im Anschluss finden Sie die Befehle für die Installation der richtigen Versionen dieser Bibliotheken für Ihre Distribution.

  - Führen Sie unter Ubuntu/Debian die folgenden Befehle aus, um die erforderlichen Pakete zu installieren:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Wenn libssl1.0.0 nicht verfügbar ist, sollten Sie stattdessen libssl1.0.x (wobei x größer als 0 ist) oder libssl1.1 installieren.

  - Führen Sie unter RHEL/CentOS die folgenden Befehle aus, um die erforderlichen Pakete zu installieren:

    ```sh
    sudo yum update
    sudo yum install alsa-lib openssl python3
    ```

> [!NOTE]
> - Befolgen Sie in RHEL/CentOS 7 die Anweisungen zum [Konfigurieren von RHEL/CentOS 7 für das Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Befolgen Sie unter RHEL/CentOS 8 die Anweisungen zum [Konfigurieren von OpenSSL für Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) für Ihre Plattform. Beachten Sie, dass Sie bei der erstmaligen Installation vielleicht einen Windows-Neustart ausführen müssen, bevor Sie mit diesem Handbuch fortfahren.
- Und schließlich benötigen Sie [Python 3.5 bis 3.8](https://www.python.org/downloads/). Um die Installation zu überprüfen, öffnen Sie eine Eingabeaufforderung, geben den Befehl `python --version` ein und überprüfen das Ergebnis. Bei ordnungsgemäßer Installation erhalten Sie die Antwort „Python 3.5.1“ oder ähnliches.

## <a name="install-the-speech-sdk-from-pypi"></a>Installieren des Speech SDK über PyPI

Wenn Sie Ihre eigene Umgebung oder Ihre eigenen Buildtools verwenden, führen Sie den folgenden Befehl aus, um das Speech SDK über [PyPI](https://pypi.org/) zu installieren. Fahren Sie als Benutzer von Visual Studio Code mit dem nächsten Unterabschnitt zur geführten Installation fort.

```sh
pip install azure-cognitiveservices-speech
```

Auf macOS müssen Sie möglicherweise den folgenden Befehl ausführen, damit der obige `pip`-Befehl funktioniert:

```sh
python3 -m pip install --upgrade pip
```

Nachdem Sie `pip` erfolgreich für die Installation von `azure-cognitiveservices-speech` verwendet haben, können Sie das Sprach-SDK verwenden, indem Sie den Namespace in Ihre Python-Projekte importieren.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Installieren des Sprach-SDK unter Verwendung von Visual Studio Code

1. Laden Sie die neueste unterstützte Version von [Python](https://www.python.org/downloads/) für Ihre Plattform (3.5 bis 3.8) herunter, und installieren Sie sie.
   - Als Windows-Benutzer stellen Sie sicher, dass Sie während des Installationsvorgangs „Add Python to your PATH“ (Fügen Sie Python Ihrem PATH hinzu) auswählen.
1. Laden Sie [Visual Studio Code](https://code.visualstudio.com/Download) herunter, und installieren Sie die Software.
1. Öffnen Sie Visual Studio Code, und installieren Sie die Python-Erweiterung. Wählen Sie im Menü **Datei** > **Einstellungen** > **Erweiterungen**. Suchen Sie nach **Python**, und klicken Sie auf **Installieren**.

   ![Installieren der Python-Erweiterung](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Installieren Sie das Sprach-SDK-Paket für Python auch von Visual Studio Code aus über die integrierte Befehlszeile:
   1. Öffnen Sie ein Terminal (in den Dropdownmenüs **Terminal** > **Neues Terminal**).
   1. Geben Sie im geöffneten Terminal den Befehl `python -m pip install azure-cognitiveservices-speech` ein.

Wenn Sie noch nicht mit Visual Studio Code vertraut sind, lesen Sie die ausführlichere [Visual Studio Code-Dokumentation](https://code.visualstudio.com/docs). Weitere Informationen zu Visual Studio Code und Python finden Sie im [Visual Studio Code-Python-Tutorial](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Support und Updates

Updates für das Speech SDK-Paket für Python werden über PyPI verteilt und in den [Versionshinweisen](~/articles/cognitive-services/speech-service/releasenotes.md) angekündigt.
Wenn eine neue Version verfügbar ist, können Sie mit dem Befehl `pip install --upgrade azure-cognitiveservices-speech` auf diese Version aktualisieren.
Die derzeit installierte Version können Sie der Variablen `azure.cognitiveservices.speech.__version__` entnehmen.

Falls Probleme auftreten oder ein Feature fehlt, sehen Sie sich die [Support- und Hilfeoptionen](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) an.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]