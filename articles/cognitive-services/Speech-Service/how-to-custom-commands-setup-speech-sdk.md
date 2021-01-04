---
title: Integrieren in eine Client-App mithilfe des Speech SDK
titleSuffix: Azure Cognitive Services
description: So stellen Sie Anforderungen an eine veröffentlichte Anwendung für benutzerdefinierte Befehle aus dem Speech SDK, das in einer UWP-Anwendung ausgeführt wird.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa3a6d16b79800043bdcd3f183dd86fa278dd1a9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026026"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Integrieren in eine Clientanwendung mithilfe des Speech SDK

In diesem Artikel erfahren Sie, wie Sie aus dem Speech SDK, das in einer UWP-Anwendung ausgeführt wird, Anforderungen an eine veröffentlichte Anwendung für benutzerdefinierte Befehlen stellen können. Zum Einrichten einer Verbindung mit der Anwendung für benutzerdefinierte Befehle ist Folgendes erforderlich:

- Veröffentlichen einer Anwendung für benutzerdefinierte Befehle und Erhalten eines Anwendungsbezeichners (App-ID)
- Erstellen einer UWP-Client-App (Universelle Windows-Plattform) mit dem Sprach-SDK, damit Sie mit Ihrer Anwendung für benutzerdefinierte Befehle kommunizieren können

## <a name="prerequisites"></a>Voraussetzungen

Eine Anwendung für benutzerdefinierte Befehle ist erforderlich, um diesen Artikel durchzuarbeiten. Wenn Sie noch keine Anwendung für benutzerdefinierte Befehle erstellt haben, können Sie dies nach den Schnellstartanleitungen erledigen:
> [!div class = "checklist"]
> * [Erstellen einer Anwendung für benutzerdefinierte Befehle](quickstart-custom-commands-application.md)

Außerdem benötigen Sie:
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) oder höher. Dieses Handbuch basiert auf Visual Studio 2019.
> * Azure-Abonnementschlüssel für Spracherkennungsdienste. [Beziehen Sie einen kostenlos](overview.md#try-the-speech-service-for-free), oder erstellen Sie ihn im [Azure-Portal](https://portal.azure.com)
> * [Aktivieren Ihres Geräts für die Entwicklung](/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>Schritt 1: Veröffentlichen der Anwendung für benutzerdefinierte Befehle

1. Öffnen Sie Ihre zuvor erstellte Anwendung für benutzerdefinierte Befehle.
1. Wechseln Sie zu **Einstellungen**, und wählen Sie **LUIS-Ressource** aus.
1. Wenn **Vorhersageressource** nicht zugewiesen ist, wählen Sie einen Vorhersageschlüssel aus, oder erstellen Sie einen neuen

    Vor dem Veröffentlichen einer Anwendung ist immer ein Abfragevorhersageschlüssel erforderlich. Weitere Informationen zu LUIS-Ressourcen finden Sie unter [Erstellen einer LUIS-Ressource](../luis/luis-how-to-azure-subscription.md).

1. Wechseln Sie zurück zu den Bearbeitungsbefehlen, und wählen Sie **Veröffentlichen** aus.

   > [!div class="mx-imgBorder"]
   > ![Veröffentlichen der Anwendung](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Kopieren der App-ID aus der Veröffentlichungsbenachrichtigung zur späteren Verwendung
1. Kopieren Sie den Sprachressourcenschlüssel zur späteren Verwendung

## <a name="step-2-create-a-visual-studio-project"></a>Schritt 2: Erstellen eines Visual Studio-Projekts

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Schritt 3: Hinzufügen von Beispielcode

In diesem Schritt fügen wir den XAML-Code ein, der die Benutzeroberfläche der Anwendung definiert, sowie die C#-CodeBehind-Implementierung hinzu.

### <a name="xaml-code"></a>XAML-Code

Erstellen Sie die Benutzeroberfläche der Anwendung, indem Sie den XAML-Code hinzufügen.

1. Öffnen Sie `MainPage.xaml` im **Projektmappen-Explorer**.

1. Ersetzen Sie in der XAML-Ansicht des Designers den gesamten Inhalt mit dem folgenden Codeausschnitt:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

Die Designansicht wird aktualisiert und zeigt dann die Benutzeroberfläche der Anwendung an.

### <a name="c-code-behind-source"></a>C#-CodeBehind-Quelle

Fügen Sie die CodeBehind-Quelle hinzu, damit die Anwendung erwartungsgemäß funktioniert. Die CodeBehind-Quelle umfasst:

- Erforderliche `using`-Anweisungen für die Namespaces `Speech` und `Speech.Dialog`
- Eine einfache Implementierung zur Sicherstellung des Mikrofonzugriffs über eine verkabelte Tastenbedienung
- Grundlegende UI-Hilfsprogramme zum Anzeigen von Meldungen und Fehlern in der Anwendung
- Ein Landepunkt für den Pfad des Initialisierungscodes, der später aufgefüllt wird
- Ein Hilfsprogramm für die Wiedergabe von Sprachsynthese (ohne Streamingunterstützung)
- Ein leerer Schaltflächenhandler, um mit dem Lauschen zu beginnen, der später aufgefüllt wird

Fügen Sie die CodeBehind-Quelle wie folgt hinzu:

1. Öffnen Sie im **Projektmappen-Explorer** die CodeBehind-Quelldatei `MainPage.xaml.cs` (gruppiert unter `MainPage.xaml`).

1. Ersetzen Sie den Inhalt der Datei durch folgenden Code: 

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.IO;
   using System.Text;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```
    > [!NOTE]
    > Wenn Sie diesen Fehler sehen: „The type ‚Object‘ is defined in an assembly that is not referenced“ (Der Typ „Objekt“ ist in einer Assembly definiert, auf die nicht verwiesen wird)
    > 1. Klicken Sie mit der rechten Maustaste auf Ihre Projektmappe.
    > 1. Wählen Sie **Verwalten von NuGet-Paketen für die Projektmappe** und dann **Updates** aus. 
    > 1. Wenn **Microsoft.NETCore.UniversalWindowsPlatform** in der Updateliste angezeigt wird, aktualisieren Sie **Microsoft.NETCore.UniversalWindowsPlatform** auf die neueste Version.

1. Fügen Sie dem Methodenkörper von `InitializeDialogServiceConnector` folgenden Code hinzu.

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Ersetzen Sie die Zeichenfolgen `YourApplicationId`, `YourSpeechSubscriptionKey`, und `YourServiceRegion` durch Ihre eigenen Werte für App, Speech-Abonnement und [Region](regions.md).

1. Fügen Sie den folgenden Codeausschnitt am Ende des Methodenkörpers von `InitializeDialogServiceConnector` an.

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. Fügen Sie dem Methodenkörper der `ListenButton_ButtonClicked`-Methode in der `MainPage`-Klasse den folgenden Codeausschnitt hinzu.

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Wählen Sie in der Menüleiste **Datei** > **Alle speichern** aus, um Ihre Änderungen zu speichern.

## <a name="try-it-out"></a>Ausprobieren

1. Wählen Sie auf der Menüleiste **Erstellen** > **Projektmappe erstellen** aus, um die Anwendung zu erstellen. Der Code sollte ohne Fehler kompiliert werden.

1. Wählen Sie **Debuggen** > **Debuggen starten** aus (oder drücken Sie **F5**), um die Anwendung zu starten. Das Fenster **helloworld** wird angezeigt.

   ![Beispielanwendung für den virtuellen UWP-Assistenten in C#: Schnellstart](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Wählen Sie **Mikrofon aktivieren** aus. Wenn Sie gefragt werden, ob Sie Zugriffsberechtigungen wünschen, wählen Sie **Ja** aus.

   ![Berechtigungsanforderung für den Mikrofonzugriff](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wählen Sie **Sprechen** aus, und sprechen Sie einen englischen Ausdruck oder Satz in das Mikrofon Ihres Geräts. Ihre Spracheingabe wird an den Direct Line Speech-Kanal übermittelt, in Text transkribiert und im Fenster angezeigt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vorgehensweise: Senden einer Aktivität an eine Clientanwendung (Vorschau)](./how-to-custom-commands-send-activity-to-client.md)