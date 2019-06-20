---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145504"
---
1. Starten Sie Visual Studio 2017.

1. Wählen Sie auf der Menüleiste in Visual Studio **Tools > Tools herunterladen** aus, und vergewissern Sie sich, dass die Workload **.NET-Desktopentwicklung** verfügbar ist. Wurde die Workload noch nicht installiert, aktivieren Sie das Kontrollkästchen, und klicken Sie auf **Ändern**, um die Installation zu starten. Der Download und die Installation können einige Minuten dauern.

   Ist das Kontrollkästchen neben **.NET-Desktopentwicklung** aktiviert, können Sie das Dialogfeld jetzt schließen.

   ![Aktivieren der .NET-Desktopentwicklung](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Als Nächstes erstellen wir ein Projekt. Wählen Sie auf der Menüleiste **Datei > Neu > Projekt** aus. Erweitern Sie im angezeigten Dialogfeld im linken Bereich die Abschnitte **Installiert > Visual C# > Windows Desktop**, und wählen Sie **Konsolen-App (.NET Framework)** aus. Nennen Sie dieses Projekt *helloworld*.

    ![Erstellen einer Visual C#-Konsolen-App (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Erstellen einer Visual C#-Konsolen-App (.NET Framework)")

1. Das Projekt ist eingerichtet. Nun müssen Sie das [NuGet-Paket für das Speech SDK](https://aka.ms/csspeech/nuget) installieren und im Code darauf verweisen. Suchen Sie den Projektmappen-Explorer, und klicken Sie mit der rechten Maustaste auf „helloworld“. Wählen Sie im Menü die Option **NuGet-Pakete verwalten...** aus.

   ![Klicken Sie mit der rechten Maustaste auf „NuGet-Pakete für Projektmappen verwalten“](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "NuGet-Pakete für Projektmappe verwalten")

1. Navigieren Sie im NuGet-Paket-Manager oben rechts zum Dropdownmenü **Paketquelle**, und stellen Sie sicher, dass **nuget.org** ausgewählt ist. Wählen Sie dann **Durchsuchen** aus, suchen Sie nach dem Paket `Microsoft.CognitiveServices.Speech`, und installieren Sie die letzte stabile Version.

   ![NuGet-Paket Microsoft.CognitiveServices.Speech installieren](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "NuGet-Paket installieren")

1. Akzeptieren Sie alle Vereinbarungen und Lizenzen, um die Installation zu starten.

   ![Akzeptieren der Lizenzbedingungen](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Akzeptieren der Lizenzbedingungen")

    Nachdem das Paket installiert wurde, wird eine Bestätigung in der Paket-Manager-Konsole angezeigt.

1. Im nächsten Schritt erstellen Sie eine Plattformkonfiguration, die der Architektur des Computers entspricht, mit dem Sie die Konsolenanwendung erstellen und ausführen. Wählen Sie auf der Menüleiste **Build** > **Konfigurations-Manager** aus.

    ![Starten des Konfigurations-Managers](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Starten des Konfigurations-Managers")

1. Klicken Sie im Dialogfeld **Konfigurations-Manager** auf die Dropdownliste **Aktive Projektmappenplattform**, und wählen Sie **Neu** aus.

    ![Hinzufügen einer neuen Plattform im Fenster des Konfigurations-Managers](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Hinzufügen einer neuen Plattform im Fenster des Konfigurations-Managers")

1. Wenn Sie bei Verwendung einer 64-Bit-Windows-Version zum **Eingeben oder Auswählen der neuen Plattform** aufgefordert werden, wählen Sie `x64` aus. Wird eine 32-Bit-Version von Windows ausgeführt, wählen Sie `x86` aus. Klicken Sie abschließend auf **OK**.

    ![Fügen Sie unter Windows 64-Bit eine neue Plattform mit dem Namen „x64“ hinzu](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "x64-Plattform hinzufügen")
