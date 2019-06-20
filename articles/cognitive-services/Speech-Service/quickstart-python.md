---
title: 'Schnellstart: Erkennen von Sprache, Python – Spracherkennungsdienste'
titleSuffix: Azure Cognitive Services
description: In dieser Anleitung erfahren Sie, wie Sie eine Konsolenanwendung zur Spracherkennung erstellen, die das Speech SDK für Python verwendet. Danach können Sie das Mikrofon Ihres Computers verwenden, um Sprache in Echtzeit zu transkribieren.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: chlandsi
ms.openlocfilehash: 30e25fa8ea9e49b3005badc3db8f6ada3c0aed50
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65885168"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Schnellstart: Erkennen von Sprache mit dem Speech SDK für Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In diesem Artikel wird erläutert, wie die Speech-Dienste über das Speech SDK für Python verwendet werden. Es wird veranschaulicht, wie Sie Sprache in einer Mikrofoneingabe erkennen können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnementschlüssel für die Speech-Dienste. [Hier erhalten Sie einen kostenlosen Schlüssel.](get-started.md)
* [Python 3.5 oder höher](https://www.python.org/downloads/).
* Das Python Speech SDK-Paket ist für die folgenden Betriebssysteme verfügbar:
    * Windows: x64 und x86.
    * Mac: macOS X Version 10.12 oder höher
    * Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9 unter x64
* Führen Sie unter Linux die folgenden Befehle aus, um die erforderlichen Pakete zu installieren:

  * Unter Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * Unter Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) für Ihre Plattform.

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Mit diesem Befehl wird das Python-Paket über [PyPI](https://pypi.org/) für das Speech SDK installiert:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Support und Updates

Updates für das Speech SDK-Paket für Python werden über PyPI verteilt und in den [Versionshinweisen](./releasenotes.md) angekündigt.
Wenn eine neue Version verfügbar ist, können Sie mit dem Befehl `pip install --upgrade azure-cognitiveservices-speech` auf diese Version aktualisieren.
Die derzeit installierte Version können Sie der Variablen `azure.cognitiveservices.speech.__version__` entnehmen.

Falls Probleme auftreten oder ein Feature fehlt, sehen Sie sich die [Support- und Hilfeoptionen](./support.md) an.

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Erstellen einer Python-Anwendung, die das Speech SDK verwendet

### <a name="run-the-sample"></a>Ausführen des Beispiels

Sie können den [Beispielcode](#sample-code) aus diesem Schnellstart in eine Quelldatei (`quickstart.py`) kopieren und in Ihrer integrierten Entwicklungsumgebung (Integrated Development Environment, IDE) oder in der Konsole ausführen:

```sh
python quickstart.py
```

Alternativ können Sie dieses Schnellstarttutorial als [Jupyter](https://jupyter.org) Notebook aus dem [Beispielrepository für das Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) herunterladen und als Notebook ausführen.

### <a name="sample-code"></a>Beispielcode

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installieren und Verwenden des Speech SDK mit Visual Studio Code

1. Laden Sie eine 64-Bit-Version (3.5 oder höher) von [Python](https://www.python.org/downloads/) herunter, und installieren Sie sie auf Ihrem Computer.
1. Laden Sie [Visual Studio Code](https://code.visualstudio.com/Download) herunter, und installieren Sie die Software.
1. Öffnen Sie Visual Studio Code, und installieren Sie die Python-Erweiterung. Wählen Sie im Menü **Datei** > **Einstellungen** > **Erweiterungen**. Suchen Sie nach **Python**.

   ![Installieren der Python-Erweiterung](media/sdk/qs-python-vscode-python-extension.png)

1. Erstellen Sie einen Ordner zum Speichern des Projekts. Ein Beispiel ist die Verwendung von Windows-Explorer.
1. Wählen Sie in Visual Studio Code das Symbol **Datei**. Öffnen Sie dann den von Ihnen erstellten Ordner.

   ![Öffnen eines Ordners](media/sdk/qs-python-vscode-python-open-folder.png)

1. Erstellen Sie eine neue Python-Quelldatei (`speechsdk.py`), indem Sie das Symbol „Neue Datei“ auswählen.

   ![Erstellen von Dateien](media/sdk/qs-python-vscode-python-newfile.png)

1. Kopieren Sie den [Python-Code](#sample-code), fügen Sie ihn in die neu erstellte Datei ein, und speichern Sie die Datei.
1. Fügen Sie Ihre Abonnementinformationen für die Speech-Dienste ein.
1. Wenn ein Python-Interpreter ausgewählt wurde, wird er auf der linken Seite der Statusleiste am unteren Rand des Fensters angezeigt.
   Zeigen Sie andernfalls eine Liste der verfügbaren Python-Interpreter an. Öffnen Sie die Befehlspalette (STRG+UMSCHALT+P), und geben Sie **Python: Select Interpreter** ein. Wählen Sie einen passenden Interpreter aus.
1. Sie können das Speech SDK-Paket für Python über Visual Studio Code installieren. Führen Sie diesen Schritt aus, wenn es für den ausgewählten Python-Interpreter noch nicht installiert wurde.
   Öffnen Sie zum Installieren des Speech SDK-Pakets ein Terminal. Rufen Sie erneut die Befehlspalette (STRG+UMSCHALT+P) auf, und geben Sie **Terminal: Neues integriertes Terminal erstellen** eingeben.
   Geben Sie im Terminal, das geöffnet wird, den Befehl `python -m pip install azure-cognitiveservices-speech` bzw. den entsprechenden Befehl für Ihr System ein.
1. Klicken Sie zum Ausführen des Beispielcodes mit der rechten Maustaste an einer beliebigen Stelle im Editor. Wählen Sie **Run Python File in Terminal** (Python-Datei im Terminal ausführen) aus.
   Sagen Sie bei entsprechender Aufforderung einige Wörter. Der transkribierte Text wird kurz danach angezeigt.

   ![Ausführen eines Beispiels](media/sdk/qs-python-vscode-python-run.png)

Falls Sie Probleme beim Ausführen dieser Anweisungen haben, können Sie das ausführlichere [Visual Studio Code-Tutorial für Python](https://code.visualstudio.com/docs/python/python-tutorial) zurate ziehen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Python-Beispiele auf GitHub](https://aka.ms/csspeech/samples)
