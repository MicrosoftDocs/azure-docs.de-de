---
title: 'Schnellstart: Speech SDK für .NET Framework (Windows): Plattformeinrichtung – Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Leitfaden, um Ihre Plattform mit dem Speech Service SDK für C# unter .NET Framework für Windows einzurichten.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: f63c97fd23d56ff508de8d5953eefac6b15ae1aa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188297"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für .NET Framework (Windows) installieren. Führen Sie `Install-Package Microsoft.CognitiveServices.Speech` in der NuGet-Konsole aus, wenn Sie nur den Paketnamen benötigen und selbständig einsteigen möchten.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Erstellen eines Visual Studio-Projekts und Installieren des Speech SDK

Sie müssen das [NuGet-Paket für das Speech SDK](https://aka.ms/csspeech/nuget) installieren, um in Ihrem Code darauf verweisen zu können. Hierzu muss ggf. zuerst ein Projekt vom Typ **helloworld** erstellt werden. Falls Sie bereits über ein Projekt mit der Workload **.NET-Desktopentwicklung** verfügen, können Sie dieses Projekt verwenden und direkt mit [Installieren des Speech SDK mithilfe des NuGet-Paket-Managers](#use-nuget-package-manager-to-install-the-speech-sdk) fortfahren.

### <a name="create-helloworld-project"></a>Erstellen eines helloworld-Projekts

1. Öffnen Sie Visual Studio 2019.

1. Wählen Sie im Fenster „Start“ die Option **Neues Projekt erstellen** aus. 

1. Wählen Sie im Fenster **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** und dann **Weiter** aus.

1. Geben Sie im Fenster **Neues Projekt konfigurieren** unter **Projektname** den Namen *helloworld* ein, und wählen Sie unter **Speicherort** den Verzeichnispfad aus, oder erstellen Sie ihn. Wählen Sie anschließend **Erstellen** aus.

1. Wählen Sie auf der Visual Studio-Menüleiste **Extras** > **Tools und Features abrufen** aus, um den Visual Studio-Installer zu öffnen und das Dialogfeld **Änderung** anzuzeigen.

1. Überprüfen Sie, ob die Workload **.NET-Desktopentwicklung** verfügbar ist. Wurde die Workload noch nicht installiert, aktivieren Sie das Kontrollkästchen daneben, und wählen Sie dann **Ändern** aus, um die Installation zu starten. Der Download und die Installation können einige Minuten dauern.

   Ist das Kontrollkästchen neben **.NET-Desktopentwicklung** bereits aktiviert, wählen Sie **Schließen** aus, um das Dialogfeld zu schließen.

   ![Aktivieren der .NET-Desktopentwicklung](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Schließen Sie den Visual Studio-Installer.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Installieren des Speech SDK mithilfe des NuGet-Paket-Managers

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **helloworld**, und wählen Sie dann **NuGet-Pakete verwalten** aus, um den NuGet-Paket-Manager anzuzeigen.

   ![NuGet-Paket-Manager](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Vergewissern Sie sich rechts oben im Dropdownfeld **Paketquelle**, dass **`nuget.org`** ausgewählt ist.

1. Wählen Sie in der oberen linken Ecke **Durchsuchen** aus.

1. Geben Sie ins Suchfeld *Microsoft.CognitiveServices.Speech* ein, und drücken Sie die **EINGABETASTE**.

1. Wählen Sie in den Suchergebnissen das Paket **Microsoft.CognitiveServices.Speech** und anschließend **Installieren** aus, um die aktuelle stabile Version zu installieren.

   ![Installieren des NuGet-Pakets Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Akzeptieren Sie alle Vereinbarungen und Lizenzen, um die Installation zu starten.

   Nachdem das Paket installiert wurde, wird eine Bestätigung im Fenster **Paket-Manager-Konsole** angezeigt.

### <a name="choose-target-architecture"></a>Auswählen der Zielarchitektur

Erstellen Sie eine Plattformkonfiguration, die der Architektur Ihres Computers entspricht, um die Konsolenanwendung zu erstellen und auszuführen.

1. Wählen Sie auf der Menüleiste **Build** > **Konfigurations-Manager** aus. Das Dialogfeld **Konfigurations-Manager** wird angezeigt.

   ![Dialogfeld „Konfigurations-Manager“](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Wählen Sie im Dropdownfeld **Aktive Projektmappenplattform** den Eintrag **Neu** aus. Das Dialogfeld **Neue Projektmappenplattform** wird angezeigt.

1. Gehen Sie im Feld **Neue Plattform eingeben oder auswählen** wie folgt vor:
   - Wenn Sie 64-Bit-Windows ausführen, wählen Sie **x64** aus.
   - Wenn Sie 32-Bit-Windows ausführen, wählen Sie **x86** aus.

1. Wählen Sie **OK** und dann **Schließen** aus.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]
