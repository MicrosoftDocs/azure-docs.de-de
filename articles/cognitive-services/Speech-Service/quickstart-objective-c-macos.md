---
title: 'Schnellstart: Erkennen von Sprache in Objective-C – Spracherkennungsdienste'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Sprache in Objective-C unter macOS mit dem Speech SDK erkennen.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: 67f2531b24796de1e00505fdc757f3c2244c5054
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002351"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Schnellstart: Erkennen von Sprache in Objective-C unter macOS mit dem Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In diesem Artikel erfahren Sie, wie Sie eine macOS-App in Objective-C mit dem Cognitive Services Speech SDK erstellen, um mit dem Mikrofon aufgezeichnete Sprache in Text zu transkribieren.

## <a name="prerequisites"></a>Voraussetzungen

Folgendes wird vorausgesetzt:

* Ein [Abonnementschlüssel](get-started.md) für den Speech-Dienst
* Ein macOS-Computer mit [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) oder höher oder macOS 10.13 oder höher

## <a name="get-the-speech-sdk-for-macos"></a>Herunterladen des Speech SDK für macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Die aktuelle Version des Cognitive Services Speech SDK ist `1.5.1`.

Das Cognitive Services Speech SDK für Mac wird als Frameworkpaket bereitgestellt.
Es kann in Xcode-Projekten als [CocoaPod](https://cocoapods.org/) verwendet oder von https://aka.ms/csspeech/macosbinary heruntergeladen und manuell verknüpft werden. In dieser Anleitung wird ein CocoaPod verwendet.

## <a name="create-an-xcode-project"></a>Erstellen eines Xcode-Projekts

Starten Sie Xcode, und starten Sie ein neues Projekt, indem Sie auf **Datei** > **Neu** > **Projekt** klicken.
Wählen Sie im Dialogfeld zum Auswählen der Vorlage die Vorlage „Cocoa App“ aus.

Treffen Sie in den nachfolgenden Dialogfeldern die folgende Auswahl:

1. Dialogfeld „Projektoptionen“
    1. Geben Sie einen Namen für die Schnellstart-App ein, z.B. `helloworld`.
    1. Geben Sie einen entsprechenden Organisationsnamen und eine Organisations-ID ein, wenn Sie bereits über ein Apple-Entwicklerkonto verfügen. Zu Testzwecken können Sie einfach einen beliebigen Namen wie `testorg` auswählen. Um die App zu signieren, benötigen Sie ein ordnungsgemäßes Bereitstellungsprofil. Details finden Sie auf der [Apple Developer-Website](https://developer.apple.com/).
    1. Stellen Sie sicher, dass Objective-C als Sprache für das Projekt ausgewählt wird.
    1. Deaktivieren Sie die Kontrollkästchen zum Verwenden von Storyboards und Erstellen einer dokumentbasierten Anwendung. Die einfache Benutzeroberfläche für die Beispiel-App wird programmgesteuert erstellt.
    1. Deaktivieren Sie alle Kontrollkästchen für Tests und Kerndaten.
    ![Projekteinstellungen](media/sdk/qs-objectivec-macos-project-settings.png)
1. Auswählen des Projektverzeichnisses
    1. Wählen Sie ein Verzeichnis aus, in dem das Projekt gespeichert werden soll. Dadurch wird das Verzeichnis `helloworld` in Ihrem Basisverzeichnis erstellt, das alle Dateien für das Xcode-Projekt enthält.
    1. Deaktivieren Sie die Erstellung eines Git-Repositorys für dieses Beispielprojekt.
1. Legen Sie die Berechtigungen für den Netzwerk- und Mikrofonzugriff fest. Klicken Sie auf der linken Seite in der ersten Zeile der Übersicht auf den App-Namen, um die App-Konfiguration anzuzeigen, und wählen Sie dann die Registerkarte „Capabilities“ (Funktionen) aus.
    1. Aktivieren Sie die Einstellung „App sandbox“ (App-Sandbox) für die App.
    1. Aktivieren Sie die Kontrollkästchen „Outgoing Connections“ (Ausgehende Verbindungen) und „Microphone“ (Mikrofon).
    ![Sandboxeinstellungen](media/sdk/qs-objectivec-macos-sandbox.png)
1. Die App muss die Verwendung des Mikrofons zudem in der Datei `Info.plist` deklarieren. Klicken Sie in der Übersicht auf die Datei, und fügen Sie den Schlüssel „Privacy – Microphone Usage Description“ (Datenschutz – Beschreibung der Mikrofonverwendung) mit einem Wert wie „Mikrofon ist zur Spracherkennung erforderlich“ hinzu.
    ![Einstellungen in der Datei „Info.plist“](media/sdk/qs-objectivec-macos-info-plist.png)
1. Schließen Sie das Xcode-Projekt. Sie verwenden später nach dem Einrichten von CocoaPods eine andere Instanz des Projekts.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installieren des SDK als CocoaPod

1. Installieren Sie den CocoaPod-Abhängigkeits-Manager entsprechend den Anweisungen in der [Installationsanleitung](https://guides.cocoapods.org/using/getting-started.html).
1. Navigieren Sie zum Verzeichnis Ihrer Beispiel-App (`helloworld`). Speichern Sie eine Textdatei mit dem Namen `Podfile` und dem folgenden Inhalt in diesem Verzeichnis:
    ```
    target 'helloworld' do
        platform :osx, '10.13'
        pod 'MicrosoftCognitiveServicesSpeech-macOS', '~> 1.5.1'
    end
    ```
1. Navigieren Sie in einem Terminal zum Verzeichnis `helloworld`, und führen Sie den Befehl `pod install` aus. Dadurch wird ein Xcode-Arbeitsbereich `helloworld.xcworkspace` generiert, der die Beispiel-App und das Speech SDK als Abhängigkeit enthält. Dieser Arbeitsbereich wird im Folgenden verwendet.

## <a name="add-the-sample-code"></a>Hinzufügen des Beispielcodes

1. Öffnen Sie den Arbeitsbereich `helloworld.xcworkspace` in Xcode.
1. Ersetzen Sie den Inhalt der automatisch generierten Datei `AppDelegate.m` durch Folgendes:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Ersetzen Sie die Zeichenfolge `YourSubscriptionKey` durch Ihren Abonnementschlüssel.
1. Ersetzen Sie die Zeichenfolge `YourServiceRegion` durch die [Region](regions.md), die mit Ihrem Abonnement verknüpft ist (z. B. `westus` für das kostenlose Testabonnement).

## <a name="build-and-run-the-sample"></a>Erstellen und Ausführen des Beispiels

1. Zeigen Sie die Debugausgabe an (**Ansicht** > **Debugbereich** > **Konsole aktivieren**).
1. Erstellen Sie den Beispielcode, und führen Sie ihn aus, indem Sie im Menü **Product** -> **Run** (Produkt > Ausführen) auswählen oder auf die Schaltfläche **Play** (Wiedergeben) klicken.
1. Nachdem Sie in der App auf die Schaltfläche geklickt und ein paar Worte gesagt haben, sollte der gesprochene Text im unteren Teil des Bildschirms angezeigt werden. Bei der erstmaligen Ausführung der App sollten Sie dazu aufgefordert werden, der App Zugriff auf das Mikrofon Ihres Computers zu gewähren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Objective-C-Beispiele auf GitHub](https://aka.ms/csspeech/samples)

