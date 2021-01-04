---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 967861363d5056a5a466aa88840cda2aafbeb86b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188317"
---
Zum Erstellen eines Visual Studio-Projekts für die C++-Desktopentwicklung müssen Sie Visual Studio-Entwicklungsoptionen einrichten, das Projekt erstellen, die Zielarchitektur auswählen und das Speech SDK installieren.

### <a name="set-up-visual-studio-development-options"></a>Einrichten von Visual Studio-Entwicklungsoptionen

Stellen Sie zunächst sicher, dass Visual Studio ordnungsgemäß für die C++-Desktopentwicklung eingerichtet ist:

1. Öffnen Sie Visual Studio 2019, um das **Startfenster** anzuzeigen.

   ![Screenshot: Fenster „Start“ von Visual Studio 2019.](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Wählen Sie **Ohne Code fortfahren** aus, um zur Visual Studio-IDE zu wechseln.

1. Wählen Sie auf der Visual Studio-Menüleiste **Extras** > **Tools und Features abrufen** aus, um den Visual Studio-Installer zu öffnen und das Dialogfeld **Änderung** anzuzeigen.

   ![Screenshot: Registerkarte „Workloads“ des Dialogfelds „Änderung“ für den Visual Studio-Installer.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Suchen Sie auf der Registerkarte **Workloads** unter **Windows** nach der Workload **Desktopentwicklung mit C++** . Wenn das Kontrollkästchen neben dieser Workload nicht bereits aktiviert ist, aktivieren Sie es.

1. Suchen Sie auf der Registerkarte **Einzelne Komponenten** das Kontrollkästchen **NuGet-Paket-Manager**. Aktivieren Sie das Kontrollkästchen, sofern es noch nicht aktiviert ist.

1. Wählen Sie in der Ecke entweder die Schaltfläche **Schließen** oder **Ändern** aus. (Der Name der Schaltfläche variiert je nachdem, ob Sie Features für die Installation ausgewählt haben.) Wenn Sie **Ändern** auswählen, beginnt die Installation, die einige Zeit in Anspruch nehmen kann.

1. Schließen Sie den Visual Studio-Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Erstellen des Projekts und Auswählen der Zielarchitektur

Erstellen Sie als nächstes Ihr Projekt:

1. Wählen Sie in der Visual Studio-Menüleiste **Datei** > **Neu** > **Projekt** aus, um das Fenster **Neues Projekt erstellen** anzuzeigen.

   ![Erstellen eines neuen Projekts: C++ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Suchen Sie die Option **Konsolen-App**, und wählen Sie sie aus. Achten Sie darauf, die C++-Version dieses Projekttyps auswählen (nicht C# oder Visual Basic).

1. Wählen Sie **Weiter** aus, um den Bildschirm **Neues Projekt konfigurieren** anzuzeigen.

   ![Konfigurieren Ihres neuen Projekts, C++ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Geben Sie unter **Projektname** die Zeichenfolge `helloworld` ein.

1. Navigieren Sie unter **Speicherort** zum Ordner, in dem das Projekt gespeichert werden soll, oder erstellen Sie einen.

Wählen Sie nun die Architektur Ihrer Zielplattform aus. Suchen Sie auf der Visual Studio-Symbolleiste das Dropdownfeld **Projektmappenplattformen**. (Wenn Sie es nicht sehen, wählen Sie **Ansicht** > **Symbolleisten** > **Standard** aus, um die Symbolleiste mit **Projektmappenplattformen** anzuzeigen.) Wenn Sie 64-Bit-Windows ausführen, wählen Sie im Dropdownfeld **x64** aus. Mit 64-Bit-Windows können auch 32-Bit-Anwendungen ausgeführt werden, sodass Sie **x86** wählen können, wenn Sie möchten.

### <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Installieren Sie schließlich das [Speech SDK-NuGet-Paket](https://aka.ms/csspeech/nuget), und verweisen Sie in Ihrem Projekt auf das Speech SDK:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **NuGet-Pakete für Projektmappe verwalten**, um zum Fenster **NuGet – Projektmappe** zu wechseln.

1. Wählen Sie **Durchsuchen** aus.

   ![NuGet: Registerkarte „Projektmappe“, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Wählen Sie unter **Paketquelle** die Option **nuget.org** aus.

1. Geben Sie `Microsoft.CognitiveServices.Speech` in das **Suchfeld** ein, und wählen Sie dann das Paket aus, nachdem es in den Suchergebnissen angezeigt wird.

   ![Installieren des C++-Pakets „Microsoft.CognitiveServices.Speech“: Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. Wählen Sie im Statusbereich des Pakets neben den Suchergebnissen Ihr **helloworld**-Projekt aus.

1. Wählen Sie **Installieren** aus.

1. Wählen Sie im Dialogfeld **Vorschau der Änderungen anzeigen** die Option **OK**.

1. Zeigen Sie im Dialogfeld **Zustimmung zur Lizenz** die Lizenz an, und wählen Sie dann **Ich stimme zu**. Die Paketinstallation beginnt, und nach Abschluss der Installation wird im **Ausgabebereich** eine Meldung mit etwa dem folgenden Text angezeigt: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.13.0' to helloworld`.
