---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3f1a24db459cabd65d9ce17b89105c3b9ab8abb7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188322"
---
1. Starten Sie Visual Studio 2019.

1. Stellen Sie sicher, dass die Workload **Plattformübergreifende .NET-Entwicklung** verfügbar ist. Wählen Sie **Tools** > **Tools und Features abrufen** in der Visual Studio-Menüleiste aus, um den Visual Studio-Installer zu öffnen. Wenn diese Workload bereits aktiviert ist, schließen Sie das Dialogfeld.

   ![Screenshot des Visual Studio-Installers mit der hervorgehobenen Registerkarte „Workloads“](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Aktivieren Sie andernfalls das Kontrollkästchen neben **Plattformübergreifende .NET Core-Entwicklung**, und wählen Sie **Ändern** in der unteren rechten Ecke des Dialogfelds aus. Die Installation des neuen Features nimmt etwas Zeit in Anspruch.

1. Erstellen Sie eine neue Visual C# .NET Core-Konsolen-App. Erweitern Sie im Dialogfeld **Neues Projekt** im linken Bereich **Installiert** > **Visual C#**  >  **.NET Core**. Wählen Sie dann **Konsolen-App (.NET Core)** aus. Geben Sie als Projektnamen *helloworld* ein.

   ![Screenshot: Dialogfeld „Neues Projekt“](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Erstellen einer Visual C#-Konsolen-App (.NET Core)")

1. Installieren Sie das [Speech-SDK-NuGet-Paket](https://aka.ms/csspeech/nuget). Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **NuGet-Pakete für Projektmappe verwalten** aus.

   ![Screenshot: Projektmappen-Explorer mit hervorgehobener Option „NuGet-Pakete für Projektmappe verwalten“](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Verwalten von NuGet-Paketen für die Projektmappe")

1. Wählen Sie rechts oben im Feld **Paketquelle** die Option **Nuget.org** aus. Suchen Sie nach dem Paket `Microsoft.CognitiveServices.Speech`, und installieren Sie es im Projekt **helloworld**.

   ![Screenshot: Dialogfeld „Pakete für Projektmappe verwalten“.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Installieren des NuGet-Pakets")

1. Akzeptieren Sie die angezeigten Lizenzbedingungen, um die Installation des NuGet-Pakets zu starten.

   ![Screenshot: Dialogfeld zur Zustimmung zu den Lizenzbedingungen](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Akzeptieren der Lizenzbedingungen")

Nachdem das Paket installiert wurde, wird eine Bestätigung in der Paket-Manager-Konsole angezeigt.
