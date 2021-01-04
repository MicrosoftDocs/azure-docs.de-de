---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001094"
---
Zum Erstellen eines Visual Studio-Projekts für die Windows-Entwicklung müssen Sie das Projekt erstellen, Visual Studio für die .NET-Desktopentwicklung einrichten, das Speech SDK installieren und die Zielarchitektur auswählen.

### <a name="create-the-project-and-add-the-workload"></a>Erstellen des Projekts und Hinzufügen der Workload

Erstellen Sie zunächst das Projekt in Visual Studio, und vergewissern Sie sich, dass Visual Studio für die .NET-Desktopentwicklung eingerichtet ist:

1. Öffnen Sie Visual Studio 2019.

1. Wählen Sie im Fenster „Start“ die Option **Neues Projekt erstellen** aus. 

1. Wählen Sie im Fenster **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** und dann **Weiter** aus.

1. Geben Sie im Fenster **Neues Projekt konfigurieren** unter **Projektname** den Namen *helloworld* ein, und wählen Sie unter **Speicherort** den Verzeichnispfad aus, oder erstellen Sie ihn. Wählen Sie anschließend **Erstellen** aus.

1. Wählen Sie auf der Visual Studio-Menüleiste **Extras** > **Tools und Features abrufen** aus, um den Visual Studio-Installer zu öffnen und das Dialogfeld **Änderung** anzuzeigen.

1. Überprüfen Sie, ob die Workload **.NET-Desktopentwicklung** verfügbar ist. Wurde die Workload noch nicht installiert, aktivieren Sie das Kontrollkästchen daneben, und wählen Sie dann **Ändern** aus, um die Installation zu starten. Der Download und die Installation können einige Minuten dauern.

   Ist das Kontrollkästchen neben **.NET-Desktopentwicklung** bereits aktiviert, wählen Sie **Schließen** aus, um das Dialogfeld zu schließen.

   ![Aktivieren der .NET-Desktopentwicklung](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Schließen Sie den Visual Studio-Installer.

### <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Der nächste Schritt ist die Installation des [Speech-SDK-NuGet-Pakets](https://aka.ms/csspeech/nuget), damit Sie im Code darauf verweisen können.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **helloworld**, und wählen Sie dann **NuGet-Pakete verwalten** aus, um den NuGet-Paket-Manager anzuzeigen.

   ![NuGet-Paket-Manager](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Navigieren Sie oben rechts zum Dropdownfeld **Paketquelle**, und stellen Sie sicher, dass **nuget.org** ausgewählt ist.

1. Wählen Sie in der oberen linken Ecke **Durchsuchen** aus.

1. Geben Sie ins Suchfeld *Microsoft.CognitiveServices.Speech* ein, und drücken Sie die **EINGABETASTE**.

1. Wählen Sie in den Suchergebnissen das Paket **Microsoft.CognitiveServices.Speech** und anschließend **Installieren** aus, um die aktuelle stabile Version zu installieren.

   ![Installieren des NuGet-Pakets Microsoft.CognitiveServices.Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Akzeptieren Sie alle Vereinbarungen und Lizenzen, um die Installation zu starten.

   Nachdem das Paket installiert wurde, wird eine Bestätigung im Fenster **Paket-Manager-Konsole** angezeigt.

### <a name="choose-the-target-architecture"></a>Auswählen der Zielarchitektur

Erstellen Sie jetzt eine Plattformkonfiguration, die der Architektur Ihres Computers entspricht, um die Konsolenanwendung zu erstellen und auszuführen.

1. Wählen Sie auf der Menüleiste **Build** > **Konfigurations-Manager** aus. Das Dialogfeld **Konfigurations-Manager** wird angezeigt.

   ![Dialogfeld „Konfigurations-Manager“](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Wählen Sie im Dropdownfeld **Aktive Projektmappenplattform** den Eintrag **Neu** aus. Das Dialogfeld **Neue Projektmappenplattform** wird angezeigt.

1. Gehen Sie im Feld **Neue Plattform eingeben oder auswählen** wie folgt vor:
   - Wenn Sie 64-Bit-Windows ausführen, wählen Sie **x64** aus.
   - Wenn Sie 32-Bit-Windows ausführen, wählen Sie **x86** aus.

1. Wählen Sie **OK** und dann **Schließen** aus.
