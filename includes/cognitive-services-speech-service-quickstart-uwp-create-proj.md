---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: faa93b75bde3a14e48baa7d27a3eb6439a137e44
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482309"
---
1. Starten Sie Visual Studio 2019.

1. Stellen Sie sicher, dass die Workload **Entwicklung für die universelle Windows-Plattform** verfügbar ist. Wählen Sie **Tools** > **Tools und Features abrufen** in der Visual Studio-Menüleiste aus, um den Visual Studio-Installer zu öffnen. Wenn diese Workload bereits aktiviert ist, schließen Sie das Dialogfeld.

    ![Screenshot des Visual Studio-Installers mit der hervorgehobenen Registerkarte „Workloads“](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    Aktivieren Sie andernfalls das Kontrollkästchen neben **Plattformübergreifende .NET-Entwicklung**, und wählen Sie **Ändern** in der unteren rechten Ecke des Dialogfelds aus. Die Installation des neuen Features nimmt etwas Zeit in Anspruch.

1. Erstellen Sie eine leere universelle Visual C#-Windows-App. Wählen Sie zunächst **Datei** > **Neu** > **Projekt** aus dem Menü aus. Erweitern Sie im Dialogfeld **Neues Projekt** die Option **Installiert** > **Visual C#**  > **Windows universell** im linken Bereich. Wählen Sie dann **Leere App (universelles Windows)** aus. Geben Sie als Projektnamen *helloworld* ein.

    ![Screenshot: Dialogfeld „Neues Projekt“](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Die Speech SDK erfordert, dass Ihre Anwendung für das Windows 10 Fall Creators Update oder höher erstellt wird. Wählen Sie im Fenster **Neues UWP-Projekt (Universelle Windows-Plattform)** , das angezeigt wird, **Windows 10 Fall Creators Update (10.0, Build 16299)** als **Mindestversion** aus. Wählen Sie im Feld **Zielversion** diese oder eine spätere Version aus, und klicken Sie dann auf **OK**.

    ![Screenshot des Fensters „Neues UWP-Projekt (Universelle Windows-Plattform)“](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Wenn Sie 64-Bit-Windows ausführen, können Sie Ihre Buildplattform in `x64` ändern, indem Sie das Dropdownmenü auf der Visual Studio-Symbolleiste verwenden. (Mit 64-Bit-Windows können 32-Bit-Anwendungen ausgeführt werden, sodass Sie die Einstellung `x86` beibehalten können, wenn Sie möchten.)

   ![Screenshot der Visual Studio-Symbolleiste mit hervorgehobener Einstellung „x64“](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Das Speech SDK unterstützt nur Intel-kompatible Prozessoren. ARM wird zurzeit nicht unterstützt.

1. Installieren Sie das [Speech-SDK-NuGet-Paket](https://aka.ms/csspeech/nuget). Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **NuGet-Pakete für Projektmappe verwalten** aus.

    ![Screenshot des Projektmappen-Explorers mit der hervorgehobenen Option „NuGet-Pakete für Projektmappe verwalten“](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. Wählen Sie rechts oben im Feld **Paketquelle** die Option **Nuget.org** aus. Suchen Sie nach dem Paket `Microsoft.CognitiveServices.Speech`, und installieren Sie es im Projekt **helloworld**.

    ![Screenshot des Dialogfelds „Pakete für Projektmappe verwalten](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "NuGet-Paket installieren“")

1. Akzeptieren Sie die angezeigten Lizenzbedingungen, um die Installation des NuGet-Pakets zu starten.

    ![Screenshot des Dialogfelds zur Zustimmung zu den Lizenzbedingungen](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Lizenzbedingungen annehmen")

1. Die folgende Ausgabezeile wird in der Paket-Manager-Konsole angezeigt.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.5.0' to helloworld
   ```

1. Da die Anwendung das Mikrofon für die Spracheingabe verwendet, fügen Sie dem Projekt die Funktion **Mikrofon** hinzu. Doppelklicken Sie im Projektmappen-Explorer auf **Package.appxmanifest**, um Ihr Anwendungsmanifest zu bearbeiten. Wechseln Sie dann auf die Registerkarte **Funktionen**, aktivieren Sie das Kontrollkästchen für die Funktion **Mikrofon**, und speichern Sie Ihre Änderungen.

   ![Screenshot des Visual Studio-Anwendungsmanifests mit hervorgehobenen Funktionen und Mikrofon](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
