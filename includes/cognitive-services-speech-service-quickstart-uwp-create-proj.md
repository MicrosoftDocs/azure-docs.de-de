---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: df9dbdf61dbc7d83f1cf74871ddc864488ed9d47
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188224"
---
Zum Erstellen eines Visual Studio-Projekts für die UWP-Entwicklung (Universelle Windows-Plattform) müssen Sie Visual Studio-Entwicklungsoptionen einrichten, das Projekt erstellen, die Zielarchitektur auswählen, Audioaufnahme einrichten und das Speech SDK installieren.

### <a name="set-up-visual-studio-development-options"></a>Einrichten von Visual Studio-Entwicklungsoptionen

Stellen Sie zunächst sicher, dass Visual Studio ordnungsgemäß für die UWP-Desktopentwicklung eingerichtet ist:

1. Öffnen Sie Visual Studio 2019, um das **Startfenster** anzuzeigen.

   ![Screenshot: Startfenster, in dem die Aktion „Ohne Code fortfahren“ hervorgehoben ist](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Wählen Sie **Ohne Code fortfahren** aus, um zur Visual Studio-IDE zu wechseln.

1. Wählen Sie auf der Visual Studio-Menüleiste **Extras** > **Tools und Features abrufen** aus, um den Visual Studio-Installer zu öffnen und das Dialogfeld **Änderung** anzuzeigen.

   ![Screenshot: Registerkarte „Workloads“ im Dialogfeld „Änderung“, auf der die Option „Entwicklung für die universelle Windows-Plattform“ hervorgehoben ist](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Suchen Sie auf der Registerkarte **Workloads** unter **Windows** nach der Workload **Entwicklung für die universelle Windows-Plattform**. Wenn das Kontrollkästchen neben dieser Workload bereits aktiviert ist, schließen Sie das Dialogfeld **Ändern**, und fahren Sie mit Schritt 6 fort.

1. Aktivieren Sie das Kontrollkästchen **Entwicklung für die universelle Windows-Plattform**, wählen Sie **Ändern** aus, und klicken Sie dann im Dialogfeld **Bevor wir loslegen** auf **Weiter**, um die Workload für UWP-Entwicklung zu installieren. Die Installation des neuen Features kann etwas Zeit in Anspruch nehmen.

1. Schließen Sie den Visual Studio-Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Erstellen des Projekts und Auswählen der Zielarchitektur

Erstellen Sie als nächstes Ihr Projekt:

1. Wählen Sie in der Visual Studio-Menüleiste **Datei** > **Neu** > **Projekt** aus, um das Fenster **Neues Projekt erstellen** anzuzeigen.

   ![Screenshot: Fenster „Neues Projekt erstellen“, in dem „Leere App (universelles Windows)“ ausgewählt und die Schaltfläche „Weiter“ hervorgehoben ist](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Suchen und wählen Sie dann **Leere App (universelles Windows)** aus. Achten Sie darauf, die C#-Version dieses Projekttyps auswählen (nicht Visual Basic).

1. Wählen Sie **Weiter** aus, um den Bildschirm **Neues Projekt konfigurieren** anzuzeigen.

   ![Screenshot: Bildschirm „Neues Projekt konfigurieren“, auf dem die Felder „Projektname“ und „Speicherort“ sowie die Schaltfläche „Erstellen“ hervorgehoben sind](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Geben Sie unter **Projektname** die Zeichenfolge `helloworld` ein.

1. Navigieren Sie unter **Speicherort** zum Ordner, in dem das Projekt gespeichert werden soll, oder erstellen Sie einen.

1. Wählen Sie **Erstellen** aus, um zum Fenster **Neues UWP-Projekt (Universelle Windows-Plattform)** zu wechseln.

   ![Screenshot: Dialogfeld „Neues UWP-Projekt (Universelle Windows-Plattform)“](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Wählen Sie unter **Mindestversion** (das zweite Dropdownfeld) die Option **Windows 10 Fall Creators Update (10.0; Build 16299)** aus. Dies ist die Mindestanforderung für das Speech SDK.

1. Wählen Sie unter **Zielversion** (das erste Dropdownfeld) einen Wert aus, der mit dem Wert in **Mindestversion** identisch oder höher ist.

1. Klicken Sie auf **OK**. Sie werden in die Visual Studio-IDE zurückgeleitet. Das neue Projekt ist erstellt und wird im Bereich **Projektmappen-Explorer** angezeigt.

   ![helloworld-Projekt: Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Wählen Sie nun die Architektur Ihrer Zielplattform aus. Suchen Sie auf der Visual Studio-Symbolleiste das Dropdownfeld **Projektmappenplattformen**. (Wenn Sie es nicht sehen, wählen Sie **Ansicht** > **Symbolleisten** > **Standard** aus, um die Symbolleiste mit **Projektmappenplattformen** anzuzeigen.) Wenn Sie 64-Bit-Windows ausführen, wählen Sie im Dropdownfeld **x64** aus. Mit 64-Bit-Windows können auch 32-Bit-Anwendungen ausgeführt werden, sodass Sie **x86** wählen können, wenn Sie möchten.

> [!NOTE]
> Das Speech SDK unterstützt alle Intel-kompatiblen Prozessoren, jedoch **nur x64**-Versionen von ARM-Prozessoren.

### <a name="set-up-audio-capture"></a>Einrichten der Audioaufnahme

Lassen Sie zu, dass das Projekt Audioeingaben erfasst:

1. Doppelklicken Sie im **Projektmappen-Explorer** auf **Package.appxmanifest**, um das Paketanwendungsmanifest zu öffnen.

1. Wählen Sie die Registerkarte **Funktionen** aus.

   ![Registerkarte „Funktionen“, Paketanwendungsmanifest: Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Aktivieren Sie das Kontrollkästchen für die Funktion **Mikrofon**.

1. Wählen Sie auf der Menüleiste **Datei** >  **„Package.appxmanifest“ speichern** aus, um Ihre Änderungen zu speichern.

### <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Installieren Sie schließlich das [Speech SDK-NuGet-Paket](https://aka.ms/csspeech/nuget), und verweisen Sie in Ihrem Projekt auf das Speech SDK:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **NuGet-Pakete für Projektmappe verwalten**, um zum Fenster **NuGet – Projektmappe** zu wechseln.

1. Wählen Sie **Durchsuchen** aus.

   ![Screenshot: Dialogfeld „NuGet-Pakete für Projektmappe verwalten“, in dem die Registerkarte „Durchsuchen“, das Suchfeld und „Paketquelle“ hervorgehoben sind](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Wählen Sie unter **Paketquelle** die Option **nuget.org** aus.

1. Geben Sie `Microsoft.CognitiveServices.Speech` in das **Suchfeld** ein, und wählen Sie dann das Paket aus, nachdem es in den Suchergebnissen angezeigt wird.

   ![Screenshot, auf dem „Microsoft.CognitiveServices.Speech“ ausgewählt ist und das Projekt sowie die Schaltfläche „Installieren“ hervorgehoben sind](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. Wählen Sie im Statusbereich des Pakets neben den Suchergebnissen Ihr **helloworld**-Projekt aus.

1. Wählen Sie **Installieren** aus.

1. Wählen Sie im Dialogfeld **Vorschau der Änderungen anzeigen** die Option **OK**.

1. Zeigen Sie im Dialogfeld **Zustimmung zur Lizenz** die Lizenz an, und wählen Sie dann **Ich stimme zu**. Die Paketinstallation beginnt, und nach Abschluss der Installation wird im **Ausgabebereich** eine Meldung mit etwa dem folgenden Text angezeigt: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.13.0' to helloworld`.
