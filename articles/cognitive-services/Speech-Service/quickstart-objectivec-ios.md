---
title: 'Schnellstart: Erkennen von Sprache in Objective-C – Spracherkennungsdienste'
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zum Erkennen von Sprache in Objective-C unter iOS mit dem Speech SDK.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: chlandsi
ms.openlocfilehash: 7d405c1e4ac5de7591f92b391071cfd66371c088
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003145"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Schnellstart: Erkennen von Sprache in Objective-C unter iOS mit dem Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In diesem Artikel erfahren Sie, wie Sie eine iOS-App in Objective-C mit dem Cognitive Services Speech SDK erstellen, um Sprache über das Mikrofon oder aus einer Datei mit Audioaufzeichnung in Text zu transkribieren.

## <a name="prerequisites"></a>Voraussetzungen

Folgendes wird vorausgesetzt:

* Ein [Abonnementschlüssel](get-started.md) für den Speech-Dienst
* Ein macOS-Computer mit [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) oder einer höheren Version
* Ziel mindestens auf die iOS-Version 9.3 festgelegt

## <a name="get-the-speech-sdk-for-ios"></a>Abrufen des Speech SDK für iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Die aktuelle Version des Cognitive Services Speech SDK ist `1.5.1`.

Das Cognitive Services Speech SDK für iOS wird derzeit als Cocoa-Framework bereitgestellt.
Es kann [hier](https://aka.ms/csspeech/iosbinary) heruntergeladen werden. Laden Sie die Datei in Ihr Basisverzeichnis herunter.

## <a name="create-an-xcode-project"></a>Erstellen eines Xcode-Projekts

Starten Sie Xcode, und starten Sie ein neues Projekt, indem Sie auf **Datei** > **Neu** > **Projekt** klicken.
Wählen Sie im Dialogfeld zum Auswählen der Vorlage die Vorlage „Einzelansicht-App (iOS)“ aus.

Treffen Sie in den nachfolgenden Dialogfeldern die folgende Auswahl:

1. Dialogfeld „Projektoptionen“
    1. Geben Sie einen Namen für die Schnellstart-App ein, z.B. `helloworld`.
    1. Geben Sie einen entsprechenden Organisationsnamen und die Organisations-ID ein, wenn Sie bereits über ein Apple-Entwicklerkonto verfügen. Zu Testzwecken können Sie einfach einen beliebigen Namen wie `testorg` auswählen. Um die App zu signieren, benötigen Sie ein ordnungsgemäßes Bereitstellungsprofil. Details finden Sie auf der [Apple Developer-Website](https://developer.apple.com/).
    1. Stellen Sie sicher, dass Objective-C als Sprache für das Projekt ausgewählt wird.
    1. Deaktivieren Sie alle Kontrollkästchen für Tests und Kerndaten.
    ![Projekteinstellungen](media/sdk/qs-objectivec-project-settings.png)
1. Auswählen des Projektverzeichnisses
    1. Wählen Sie das Basisverzeichnis aus, in dem das Projekt gespeichert werden soll. Dadurch wird das Verzeichnis `helloworld` in Ihrem Basisverzeichnis erstellt, das alle Dateien für das Xcode-Projekt enthält.
    1. Deaktivieren Sie die Erstellung eines Git-Repositorys für dieses Beispielprojekt.
    1. Passen Sie die Pfade zum SDK in den *Projekteinstellungen* an.
        1. Fügen Sie auf der Registerkarte **Allgemein** unter der Überschrift **Eingebettete Binärdateien** die SDK-Bibliothek als Framework hinzu: **Eingebettete Binärdateien hinzufügen** > **Andere hinzufügen...**. Navigieren Sie dann zu Ihrem Basisverzeichnis, und wählen Sie die Datei `MicrosoftCognitiveServicesSpeech.framework` aus. Dadurch wird automatisch die SDK-Bibliothek dem Header **Verknüpftes Framework und Bibliotheken** hinzugefügt.
        ![Hinzugefügtes Framework](media/sdk/qs-objectivec-framework.png)
        1. Navigieren Sie zur Registerkarte **Buildeinstellungen**, und aktivieren Sie **Alle** Einstellungen.
        1. Fügen Sie das Verzeichnis `$(SRCROOT)/..` den *Frameworksuchpfaden* unter der Überschrift **Suchpfade** hinzu.
        ![Einstellung „Frameworksuchpfad“](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Einrichten der Benutzeroberfläche

Die Benutzeroberfläche der Beispiel-App ist sehr einfach: Sie umfasst lediglich zwei Schaltflächen, um die Spracherkennung entweder mit einer Datei oder mit einer Mikrofoneingabe zu starten, sowie eine Beschriftung zum Anzeigen des Ergebnisses.
Die Benutzeroberfläche wird im `Main.storyboard`-Teil des Projekts eingerichtet.
Öffnen Sie die XML-Ansicht des Storyboards, indem Sie mit der rechten Maustaste auf den Eintrag `Main.storyboard` der Projektstruktur klicken und **Öffnen als...** > **Quellcode** auswählen.
Ersetzen Sie den automatisch generierten XML-Code durch diesen Code:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Hinzufügen des Beispielcodes

1. Laden Sie die [WAV-Beispieldatei](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) herunter, indem Sie mit der rechten Maustaste auf den Link klicken und dann **Ziel speichern unter...** auswählen. Fügen Sie die WAV-Datei dem Projekt als Ressource hinzu, indem Sie sie aus einem Suchfenster in die Stammebene der Projektansicht ziehen.
   Klicken Sie im nachfolgenden Dialogfeld auf **Fertig stellen**, ohne die Einstellungen zu ändern.
1. Ersetzen Sie den Inhalt der automatisch generierten Datei `ViewController.m` durch Folgendes:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Ersetzen Sie die Zeichenfolge `YourSubscriptionKey` durch Ihren Abonnementschlüssel.
1. Ersetzen Sie die Zeichenfolge `YourServiceRegion` durch die [Region](regions.md), die mit Ihrem Abonnement verknüpft ist (z. B. `westus` für das kostenlose Testabonnement).
1. Fügen Sie die Anforderung für den Zugriff auf das Mikrofon hinzu. Klicken Sie mit der rechten Maustaste auf den Eintrag `Info.plist` der Projektstruktur, und wählen Sie anschließend **Öffnen als...** > **Quellcode**. Fügen Sie die folgenden Zeilen im Abschnitt `<dict>` hinzu, und speichern Sie die Datei dann.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Erstellen und Ausführen des Beispiels

1. Zeigen Sie die Debugausgabe an (**Ansicht** > **Debugbereich** > **Konsole aktivieren**).
1. Wählen Sie als Ziel für die App in der Liste des Menüs **Produkt** -> **Ziel** entweder den iOS-Simulator oder ein mit dem Entwicklungscomputer verbundenes iOS-Gerät aus.
1. Erstellen Sie den Beispielcode im iOS-Simulator, und führen Sie ihn aus, indem Sie **Produkt** -> **Ausführen** aus dem Menü auswählen oder auf die Schaltfläche **Wiedergeben** klicken.
   Das Speech SDK unterstützt zurzeit nur 64-Bit-iOS-Plattformen.
1. Nachdem Sie in der App auf die Schaltfläche „Recognize (File)“ (Erkennen (Datei)) geklickt haben, sollte der Inhalt der Audiodatei „What's the weather like?“ (Wie ist das Wetter?) im unteren Teil des Bildschirms angezeigt werden.

   ![Simulierte iOS-App](media/sdk/qs-objectivec-simulated-app.png)

1. Nachdem Sie in der App auf die Schaltfläche „Recognize (Microphone)“ (Erkennen (Mikrofon)) geklickt und ein paar Worte gesagt haben, sollte der gesprochene Text im unteren Teil des Bildschirms angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Objective-C-Beispiele auf GitHub](https://aka.ms/csspeech/samples)

