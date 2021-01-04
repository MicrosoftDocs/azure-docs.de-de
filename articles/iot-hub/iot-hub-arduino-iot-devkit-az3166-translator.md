---
title: Erstellen eines Übersetzers mit Azure Functions, Cognitive Services, IoT DevKit
description: In diesem Artikel erfahren Sie, wie Sie das Mikrofon mit einem IoT DevKit verwenden, um eine Sprachnachricht zu empfangen, und die Nachricht anschließend mit Azure Cognitive Services in die englische Sprache übersetzen.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: be26c6fe03dac9b9ff9dbff4a2bdce391ec0837e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024162"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Verwenden von IoT DevKit AZ3166 mit Azure Functions und Cognitive Services zum Erstellen eines Sprachübersetzers

In diesem Artikel erfahren Sie, wie Sie IoT DevKit mithilfe von [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) als Sprachübersetzer nutzen. Dabei wird Ihre Stimme aufgezeichnet und in englischen Text übersetzt, der auf dem DevKit-Bildschirm angezeigt wird.

Das [MXChip IoT DevKit](https://aka.ms/iot-devkit) ist ein mit Arduino kompatibles All-in-One-Board mit umfangreichen Peripheriegeräten und Sensoren. Mit den Erweiterungspaketen [Azure IoT Device Workbench](https://aka.ms/iot-workbench) und [Azure IoT Tools](https://aka.ms/azure-iot-tools) können Sie in Visual Studio Code dafür entwickeln. Der [Projektkatalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) enthält Beispielanwendungen, die Sie beim Erstellen von Prototypen für IoT-Lösungen unterstützen.

## <a name="before-you-begin"></a>Voraussetzungen

Um die Schritte in diesem Tutorial auszuführen, erledigen Sie zuerst die folgenden Aufgaben:

* Bereiten Sie Ihr DevKit vor, indem Sie die in [Verbinden von IoT DevKit AZ3166 mit Azure IoT Hub in der Cloud](./iot-hub-arduino-iot-devkit-az3166-get-started.md) beschriebenen Schritte ausführen.

## <a name="create-azure-cognitive-service"></a>Erstellen von Azure Cognitive Services

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**, und suchen Sie nach **Speech**. Füllen Sie das Formular aus, um den Speech-Dienst zu erstellen.
  ![Speech-Dienst](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Öffnen Sie den erstellten Speech-Dienst, klicken Sie auf den Abschnitt **Schlüssel**, um den **Schlüssel1** für den Zugriff auf DevKits zu kopieren und zu notieren.
  ![Kopieren der Schlüssel](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Öffnen eines Beispielprojekts

1. Stellen Sie sicher, dass Ihr IoT-DevKit **nicht** mit Ihrem Computer verbunden ist. Starten Sie zuerst Visual Studio Code, und stellen Sie dann eine Verbindung von DevKit mit Ihrem Computer her.

1. Drücken Sie `F1`, um die Befehlspalette zu öffnen, und geben Sie **Azure IoT Device Workbench: Open Examples...** (Azure IoT Device Workbench: Beispiele öffnen) ein. Wählen Sie dann **IoT DevKit** als Board aus.

1. Suchen Sie auf der Seite mit IoT Workbench-Beispielen nach **DevKit Translator**, und klicken Sie auf **Beispiel öffnen**. Wählen Sie dann den Standardpfad zum Herunterladen des Beispielcodes aus.
  ![Beispiel öffnen](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Verwenden von Speech mit Azure Functions

1. Drücken Sie in VS Code die Taste `F1`, geben Sie **Azure IoT Device Workbench: Provision Azure Services...** (Azure IoT Workbench: Azure-Dienste bereitstellen) ein, und wählen Sie den Eintrag aus. ![Bereitstellen von Azure-Diensten](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Führen Sie die angezeigten Schritte aus, um Azure IoT Hub und Azure Functions bereitzustellen.
   ![Bereitstellungsschritte](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Notieren Sie sich den Namen des erstellten Azure IoT Hub-Geräts.

1. Öffnen Sie die Datei `Functions\DevKitTranslatorFunction.cs`, und fügen Sie den notierten Gerätenamen und den Speech-Schlüssel in die folgenden Codezeilen ein.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Klicken Sie auf `F1`, geben Sie **Azure IoT Device Workbench: Deploy to Azure** (Azure IoT Device Workbench: In Azure bereitstellen) ein, und wählen Sie den angezeigten Befehl aus. Wenn VS Code zur Bestätigung der erneuten Bereitstellung auffordert, klicken Sie auf **Ja**.
   ![Bereitstellungswarnung](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Stellen Sie sicher, dass die Bereitstellung erfolgreich war.
   ![Erfolgreiche Bereitstellung](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Navigieren Sie im Azure-Portal zum Abschnitt **Funktions-App**, und suchen Sie nach der soeben erstellten Azure Functions-App. Klicken Sie auf `devkit_translator`, und klicken Sie dann auf **</> Funktions-URL abrufen**, um die URL zu kopieren.
   ![Kopieren der Funktions-URL](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Fügen Sie die URL in die Datei `azure_config.h` ein.
   ![Azure-Konfigurationsdatei](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Wenn die Funktions-App nicht ordnungsgemäß funktioniert, finden Sie in diesem [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function)-Abschnitt Hinweise zur Fehlerbehebung.

## <a name="build-and-upload-device-code"></a>Erstellen und Hochladen von Gerätecode

1. Versetzen Sie das DevKit in den **Konfigurationsmodus**, indem Sie folgende Schritte durchführen:
   * Halten Sie die Taste **A** gedrückt.
   * Drücken Sie die Taste **Zurücksetzen**, und lassen Sie sie wieder los.

   Auf dem Bildschirm werden die DevKit-ID und **Configuration** angezeigt.

   ![DevKit-Konfigurationsmodus](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Klicken Sie auf `F1`, geben Sie **Azure IoT Device Workbench: Configure Device Settings... > Config Device Connection String** ein, und wählen Sie den angezeigten Befehl aus. Wählen Sie **Select IoT Hub Device Connection String** (IoT Hub-Geräteverbindungszeichenfolge auswählen) aus, um diese für das DevKit zu konfigurieren.
   ![Konfigurieren der Verbindungszeichenfolge](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Sobald dieser Vorgang abgeschlossen ist, wird eine Benachrichtigung angezeigt.
   ![Erfolgreiche Konfiguration der Verbindungszeichenfolge](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Klicken Sie erneut auf `F1`, geben Sie **Azure IoT Device Workbench: Upload Device Code** (Azure IoT Device Workbench: Gerätecode hochladen) ein, und wählen Sie den angezeigten Befehl aus. Daraufhin wird der Code kompiliert und auf das DevKit hochgeladen.
   ![Hochladen des Codes](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testen des Projekts

Befolgen Sie nach der App-Initialisierung die Anweisungen auf dem DevKit-Bildschirm. Die Standardquellsprache ist Chinesisch.

So wählen Sie eine andere Sprache für die Übersetzung aus

1. Drücken Sie Taste A, um in den Setupmodus zu gelangen.

2. Drücken Sie Taste B, um durch alle unterstützten Quellsprachen zu scrollen.

3. Drücken Sie Taste A, um Ihre Auswahl der Quellsprache zu bestätigen.

4. Halten Sie Taste B beim Sprechen gedrückt, und lassen Sie Taste B dann los, um die Übersetzung auszulösen.

5. Der übersetzte Text wird in englischer Sprache auf dem Bildschirm angezeigt.

![Scrollen zum Auswählen der Sprache](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Übersetzungsergebnis](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Auf dem Bildschirm mit dem Übersetzungsergebnis haben Sie folgende Möglichkeiten:

- Drücken Sie die Tasten „A“ und „B“, um durch die Quellsprachen zu scrollen und eine auszuwählen.

- Drücken Sie die Taste „B“, um zu sprechen. Lassen Sie sie los, um die Sprachnachricht zu senden und den Übersetzungstext abzurufen.

## <a name="how-it-works"></a>Funktionsweise

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Das IoT-DevKit zeichnet Ihre Stimme auf und sendet dann eine HTTP-Anforderung zum Auslösen von Azure Functions. Azure Functions ruft die Sprachübersetzer-API von Cognitive Services für die Übersetzung auf. Nachdem Azure Functions den Übersetzungstext abgerufen hat, wird eine C2D-Nachricht an das Gerät gesendet. Anschließend wird die Übersetzung auf dem Bildschirm angezeigt.

## <a name="problems-and-feedback"></a>Probleme und Feedback

Wenn Probleme auftreten, lesen Sie die [häufig gestellten Fragen zum IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), oder wenden Sie sich über folgende Kanäle an uns:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nächste Schritte

Sie haben erfahren, wie Sie das IoT DevKit mithilfe von Azure Functions und Cognitive Services als Übersetzer verwenden können. In diesen Anweisungen wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Verwenden einer Visual Studio Code-Aufgabe zum Automatisieren von Cloudbereitstellungen
> * Konfigurieren einer Verbindungszeichenfolge für Azure IoT-Geräte
> * Bereitstellen der Azure-Funktions-App
> * Testen der Übersetzung der Sprachnachricht

Wechseln Sie zu den anderen Tutorials, um Folgendes zu lernen:

> [!div class="nextstepaction"]
> [Verbinden von IoT DevKit AZ3166 mit dem Solution Accelerator für die Azure IoT-Remoteüberwachung](./iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)