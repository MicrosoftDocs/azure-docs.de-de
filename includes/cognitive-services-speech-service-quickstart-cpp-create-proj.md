---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 27fcf32a9a268488da318567d3edc55d23bd8967
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482422"
---
1. Starten Sie Visual Studio 2019.

1. Stellen Sie sicher, dass die Workload **Desktopentwicklung mit C++** verfügbar ist. Wählen Sie **Tools** > **Tools und Features abrufen** in der Visual Studio-Menüleiste aus, um den Visual Studio-Installer zu öffnen. Wenn diese Workload bereits aktiviert ist, fahren Sie mit dem nächsten Schritt fort.

    ![Screenshot der Registerkarte „Visual Studio-Workloads“](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    Aktivieren Sie andernfalls das Kontrollkästchen neben **Desktopentwicklung mit C++** .

1. Stellen Sie sicher, dass die Komponente **NuGet-Paket-Manager** verfügbar ist. Wechseln Sie zur Registerkarte **Einzelne Komponenten** des Dialogfelds „Visual Studio-Installer“, und wählen Sie **NuGet-Paket-Manager** aus, wenn diese Option nicht bereits aktiviert ist.

      ![Screenshot der Registerkarte „Einzelne Visual Studio-Komponenten“](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Wenn Sie entweder die C++-Workload oder NuGet aktivieren mussten, wählen Sie **Ändern** (in der unteren rechten Ecke des Dialogfelds) aus. Die Installation der neuen Features nimmt etwas Zeit in Anspruch. Wenn beide Features bereits aktiviert wurden, schließen Sie stattdessen das Dialogfeld.

1. Erstellen Sie eine neue Windows-Konsolenanwendung in Visual C++ unter Windows Desktop. Wählen Sie zunächst **Datei** > **Neu** > **Projekt** aus dem Menü aus. Erweitern Sie im Dialogfeld **Neues Projekt** die Option **Installiert** > **Visual C++**  > **Windows Desktop** im linken Bereich. Wählen Sie dann **Windows-Konsolenanwendung** aus. Geben Sie als Projektnamen *helloworld* ein.

    ![Screenshot: Dialogfeld „Neues Projekt“](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Wenn Sie 64-Bit-Windows ausführen, können Sie Ihre Buildplattform ggf. in `x64` ändern, indem Sie das Dropdownmenü auf der Visual Studio-Symbolleiste verwenden. (64-Bit-Versionen von Windows können 32-Bit-Anwendungen ausführen, daher ist dies nicht erforderlich.)

    ![Screenshot der Visual Studio-Symbolleiste mit hervorgehobener Option „x64“](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **NuGet-Pakete für Projektmappe verwalten** aus.

    ![Screenshot des Projektmappen-Explorers mit der hervorgehobenen Option „NuGet-Pakete für Projektmappe verwalten“](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Wählen Sie rechts oben im Feld **Paketquelle** die Option **Nuget.org** aus. Suchen Sie nach dem Paket `Microsoft.CognitiveServices.Speech`, und installieren Sie es im Projekt **helloworld**.

    ![Screenshot des Dialogfelds „Pakete für Projektmappe verwalten“](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Die aktuelle Version des Cognitive Services Speech SDK ist `1.5.0`.

1. Akzeptieren Sie die angezeigten Lizenzbedingungen, um die Installation des NuGet-Pakets zu starten.

    ![Screenshot des Dialogfelds zur Zustimmung zu den Lizenzbedingungen](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

Nachdem das Paket installiert wurde, wird eine Bestätigung in der Paket-Manager-Konsole angezeigt.
