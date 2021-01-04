---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188327"
---
Wenn Sie ein Visual Studio-Projekt für die plattformübergreifende .NET-Entwicklung mobiler Apps mit Xamarin erstellen möchten, sind folgende Schritte erforderlich:
- Einrichten von Visual Studio-Entwicklungsoptionen
- Erstellen des Projekts und Auswählen der Zielarchitektur 
- Installieren des Speech SDK

### <a name="set-up-visual-studio-development-options"></a>Einrichten von Visual Studio-Entwicklungsoptionen

Vergewissern Sie sich zunächst, dass Visual Studio ordnungsgemäß für die plattformübergreifende Entwicklung mobiler Apps mit .NET eingerichtet ist:

1. Öffnen Sie Visual Studio 2019.

1. Wählen Sie auf der Visual Studio-Menüleiste **Extras** > **Tools und Features abrufen** aus, um den Visual Studio-Installer zu öffnen und das Dialogfeld **Änderung** anzuzeigen.

   ![Screenshot: Registerkarte „Workloads“, Dialogfeld „Änderung“ und Visual Studio-Installer.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Suchen Sie auf der Registerkarte **Workloads** unter **Windows** nach der Workload **Mobile-Entwicklung mit .NET**. Wenn das Kontrollkästchen neben dieser Workload bereits aktiviert ist, schließen Sie das Dialogfeld **Ändern**, und fahren Sie mit Schritt 5 fort.

1. Aktivieren Sie das Kontrollkästchen **Mobile-Entwicklung mit .NET**, und wählen Sie **Ändern** aus. Wählen Sie im Dialogfeld **Bevor wir loslegen** die Option **Weiter** aus, um die Workload „Mobile-Entwicklung mit .NET“ zu installieren. Die Installation des neuen Features kann etwas dauern.

1. Schließen Sie den Visual Studio-Installer.

### <a name="create-the-project"></a>Erstellen des Projekts

1. Wählen Sie auf der Visual Studio-Menüleiste **Datei** > **Neu** > **Projekt** aus, um das Fenster **Neues Projekt erstellen** anzuzeigen.

   ![Screenshot: Erstellen eines neuen Projekts in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Suchen Sie nach der Option **Mobile App (Xamarin.Forms)** , und wählen Sie sie aus.

1. Wählen Sie **Weiter** aus, um den Bildschirm **Neues Projekt konfigurieren** anzuzeigen.

   ![Screenshot: Konfigurieren Ihres neuen Projekts in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Geben Sie unter **Projektname** die Zeichenfolge *helloworld* ein.

1. Navigieren Sie unter **Speicherort** zu dem Ordner, in dem das Projekt gespeichert werden soll, und wählen Sie ihn aus, oder erstellen Sie einen entsprechenden Ordner.

1. Wählen Sie **Erstellen** aus, um zum Fenster **New Mobile App Xamarin Forms Project** (Neues Mobile App-Xamarin Forms-Projekt) zu navigieren.

   ![Screenshot des Dialogfelds „New Mobile App Xamarin Forms Project“ (Neues Mobile App-Xamarin Forms-Projekt) in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Wählen Sie die Vorlage **Leer** aus.

1. Aktivieren Sie unter **Plattform** die Kontrollkästchen für **Android**, **iOS** und **Windows (UWP)** .

1. Klicken Sie auf **OK**. Sie werden in die Visual Studio-IDE zurückgeleitet. Das neue Projekt ist erstellt und wird im Bereich **Projektmappen-Explorer** angezeigt.

   ![Projekt „helloworld“ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Wählen Sie nun die Architektur Ihrer Zielplattform und das Startprojekt aus. Suchen Sie auf der Visual Studio-Symbolleiste das Dropdownfeld **Projektmappenplattformen**. (Sollte es nicht angezeigt werden, wählen Sie **Ansicht** > **Symbolleisten** > **Standard** aus, um die Symbolleiste mit **Projektmappenplattformen** anzuzeigen.) Wählen Sie bei Verwendung der 64-Bit-Version von Windows **x64** im Dropdownfeld aus. Falls gewünscht, können Sie auch **x86** auswählen, da die 64-Bit-Version von Windows auch für 32-Bit-Anwendungen geeignet ist. Legen Sie im Dropdownfeld **Startprojekte** die Option **helloworld.UWP (Universal Windows)** fest.

### <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Installieren Sie das [Speech SDK-NuGet-Paket](https://aka.ms/csspeech/nuget), und verweisen Sie in Ihrem Projekt auf das Speech SDK.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihre Projektmappe. Wählen Sie **NuGet-Pakete für Projektmappe verwalten** aus, um zum Fenster **NuGet – Projektmappe** zu gelangen.

1. Wählen Sie **Durchsuchen** aus.

   ![Screenshot: Dialogfeld „Pakete für Projektmappe verwalten“ beim Installieren des Speech SDK.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Wählen Sie unter **Paketquelle** die Option „nuget.org“ aus.

1. Geben Sie *Microsoft.CognitiveServices.Speech* in das Feld **Suche** ein. Wählen Sie dann in den Suchergebnissen das entsprechende Paket aus.

   ![Screenshot, der das Paket Microsoft.CognitiveServices.Speech hervorhebt.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > Für die iOS-Bibliothek in `Microsoft.CognitiveServices.Speech` (NuGet) ist Bitcode nicht aktiviert. Falls Sie die Bitcodebibliothek für Ihre Anwendung benötigen, verwenden Sie `Microsoft.CognitiveServices.Speech.Xamarin.iOS` (NuGet) für das spezifische iOS-Projekt.

1. Wählen Sie im Paketstatusbereich neben den Suchergebnissen alle Projekte aus: **helloworld**, **helloworld.Android**, **helloworld.iOS** und **helloworld.UWP**.

1. Wählen Sie **Installieren** aus.

1. Wählen Sie im Dialogfeld **Vorschau der Änderungen anzeigen** die Option **OK**.

1. Zeigen Sie im Dialogfeld **Zustimmung zur Lizenz** die Lizenz an, und wählen Sie dann **Ich stimme zu**. Installieren Sie die Speech SDK-Paketreferenz für alle Projekte. Nach erfolgreichem Abschluss der Installation wird für „helloworld.iOS“ unter Umständen die folgende Warnung angezeigt. Dies ist ein bekanntes Problem, das sich nicht auf die App-Funktionalität auswirken sollte.

   > Der Verweis „C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a“ konnte nicht aufgelöst werden. Ist dieser Verweis für Ihren Code erforderlich, treten unter Umständen Kompilierungsfehler auf.
