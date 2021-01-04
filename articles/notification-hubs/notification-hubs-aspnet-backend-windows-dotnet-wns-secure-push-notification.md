---
title: 'Azure Notification Hubs: Sichere Pushbenachrichtigungen für Windows'
description: Erfahren Sie mehr über das Senden von sicheren Pushbenachrichtigungen in Azure. Die Codebeispiele wurden in C# mithilfe der .NET-API geschrieben.
author: sethmanheim
manager: femila
editor: thsomasu
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 98e587103e63cd5cc26eab5b00864d00e0b9007f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019418"
---
# <a name="send-secure-push-notifications-from-azure-notification-hubs"></a>Senden sicherer Pushbenachrichtigungen aus Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Übersicht

Durch die Unterstützung von Pushbenachrichtigungen in Microsoft Azure haben Sie Zugriff auf eine benutzerfreundliche, plattformübergreifende und horizontal skalierte Pushinfrastruktur, die die Implementierung von Pushbenachrichtigungen sowohl für Endbenutzer- als auch für Unternehmensanwendungen für mobile Plattformen erheblich vereinfacht.

Es kann vorkommen, dass eine Anwendung etwas in die Benachrichtigung einschließen möchte, dass aufgrund gesetzlicher oder sicherheitsbedingter Einschränkungen nicht über die Standardinfrastruktur von Pushbenachrichtigungen übertragen werden kann. In diesem Lernprogramm wird beschrieben, wie Sie die gleiche Funktionalität erzielen, indem Sie vertrauliche Informationen über eine sichere authentifizierte Verbindung zwischen dem Clientgerät und dem App-Back-End senden.

Der generelle Ablauf sieht folgendermaßen aus:

1. Das App-Back-End:
   * Speichert die sichere Nutzlast in einer Back-End-Datenbank.
   * Sendet die ID dieser Benachrichtigung an das Gerät (es werden keine sicheren Informationen gesendet).
2. Bei Erhalt der Benachrichtigung reagiert die App auf dem Gerät folgendermaßen:
   * Das Gerät kontaktiert das Back-End und fordert die sichere Nutzlast an.
   * Die App kann die Nutzlast als Benachrichtigung auf dem Gerät anzeigen.

Beachten Sie, dass im obigen Ablauf (und in diesem Tutorial) angenommen wird, dass das Gerät ein Authentifizierungstoken im lokalen Speicher speichert, nachdem sich der Benutzer angemeldet hat. Dies gewährleistet einen vollständig nahtlosen Ablauf, da das Gerät mit diesem Token die sichere Nutzlast der Benachrichtigung abrufen kann. Wenn Ihre Anwendung keine Authentifizierungstoken auf dem Gerät speichert oder diese Token ablaufen können, sollte die Geräte-App nach Erhalt der Benachrichtigung eine generische Benachrichtigung anzeigen, in der der Benutzer zum Starten der App aufgefordert wird. Anschließend authentifiziert die App den Benutzer und zeigt die Nutzlast der Benachrichtigung an.

Dieses Tutorial zeigt, wie Sie sichere Pushbenachrichtigungen senden. Es baut auf dem Tutorial [Benachrichtigen von Benutzern](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) auf. Daher sollten Sie die Schritte in diesem Tutorial zuerst durchführen.

> [!NOTE]
> In diesem Tutorial wird davon ausgegangen, dass Sie Ihre Notification Hub-Instanz wie unter [Senden von Benachrichtigungen an Apps für Universelle Windows-Plattform](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) beschrieben erstellt und konfiguriert haben.
> Beachten Sie außerdem, dass für Windows Phone 8.1 Anmeldedaten für Windows (nicht Windows Phone) erforderlich sind und dass Hintergrundaufgaben in Windows Phone 8.0 oder Silverlight 8.1 nicht funktionieren. Bei Windows Store-Anwendungen können nur dann Benachrichtigungen über eine Hintergrundaufgabe empfangen werden, wenn für die App der Sperbildschirmaktiviert ist (klicken Sie auf das Kontrollkästchen im AppManifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Ändern des Windows Phone-Projekts

1. Fügen Sie im Projekt **NotifyUserWindowsPhone** den folgenden Code zur Datei App.xaml.cs hinzu, um die Push-Hintergrundaufgabe zu registrieren. Fügen Sie am Ende der `OnLaunched()` -Methode folgenden Code hinzu:

    ```csharp
    RegisterBackgroundTask();
    ```

2. Fügen Sie in "App.xaml.cs" den folgenden Code direkt nach der `OnLaunched()` -Methode ein:

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```

3. Fügen Sie die folgenden `using` -Anweisungen am Anfang der Datei "App.xaml.cs" hinzu:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```

4. Klicken Sie im Menü **Datei** in Visual Studio auf **Alles speichern**.

## <a name="create-the-push-background-component"></a>Erstellen der Push-Hintergrundkomponente

Im nächsten Schritt erstellen Sie die Push-Hintergrundkomponente.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Knoten der obersten Ebene der Projektmappe (in diesem Fall **Solution SecurePush**). Klicken Sie dann auf **Hinzufügen** und **Neues Projekt**.
2. Erweitern Sie **Store-Apps**, und klicken Sie anschließend auf **Windows Phone-Apps** und **Komponente für Windows-Runtime (Windows Phone)** . Benennen Sie das Projekt **PushBackgroundComponent**, und klicken Sie dann auf **OK**, um das Projekt zu erstellen.

    ![Screenshot des Dialogfelds „Neues Projekt hinzufügen“ mit hervorgehobener Option „Windows-Runtimekomponente (Windows Phone) – Visual C#“.][12]
3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **PushBackgroundComponent (Windows Phone 8.1)** , und klicken Sie dann auf **Hinzufügen** und **Klasse**. Geben Sie der neuen Klasse den Namen `PushBackgroundTask.cs`. Klicken Sie auf **Hinzufügen** , um die Klasse zu erstellen.
4. Ersetzen Sie den gesamten Inhalt der Namespacedefinition `PushBackgroundComponent` durch den folgenden Code, und ersetzen Sie dabei den Platzhalter `{back-end endpoint}` durch den Back-End-Endpunkt, den Sie bei der Bereitstellung des Back-Ends abgerufen haben:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```

5. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **PushBackgroundComponent (Windows Phone 8.1)** , und klicken Sie dann auf **NuGet-Pakete verwalten**.
6. Klicken Sie auf der linken Seite auf **Online**.
7. Geben Sie in das **Suchfeld** den Begriff **Http Client** ein.
8. Klicken Sie in der Ergebnisliste auf **Microsoft HTTP-Clientbibliotheken** und dann auf **Installieren**. Schließen Sie die Installation ab.
9. Geben Sie in das NuGet-**Suchfeld** die Zeichenfolge **Json.net** ein. Installieren Sie das **Json.NET** -Paket, und schließen Sie dann das Fenster des NuGet-Paket-Managers.
10. Fügen Sie am Anfang der Datei `PushBackgroundTask.cs` die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```

11. Klicken Sie im Projektmappen-Explorer im Projekt **NotifyUserWindowsPhone (Windows Phone 8.1)** mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **Verweis hinzufügen**. Aktivieren Sie im Dialogfeld „Verweis-Manager“ das Kontrollkästchen neben **PushBackgroundComponent**, und klicken Sie dann auf **OK**.
12. Doppelklicken Sie im Projektmappen-Explorer im Projekt **NotifyUserWindowsPhone (Windows Phone 8.1)** auf **Package.appxmanifest**. Legen Sie unter **Benachrichtigungen** die Einstellung **Toastfähig** auf **Ja** fest.

    ![Screenshot des Projektmappen-Explorer-Fensters mit Fokus auf der Datei „Package.appxmanifest“ mit auf „Ja“ festgelegter Option „Toastfähig“ (rot umrandet).][3]
13. Klicken Sie in **Package.appxmanifest** im oberen Bereich auf das Menü **Deklarationen**. Klicken Sie im Dropdownmenü **Verfügbare Deklarationen** auf **Hintergrundaufgaben**, und klicken Sie dann auf **Hinzufügen**.
14. Aktivieren Sie in **Package.appxmanifest** unter **Eigenschaften** die Option **Pushbenachrichtigung**.
15. Geben Sie in **Package.appxmanifest** unter **App-Einstellungen** die Zeichenfolge **PushBackgroundComponent.PushBackgroundTask** in das Feld **Einstiegspunkt** ein.

    ![Screenshot des Projektmappen-Explorer-Fensters mit Fokus auf der Datei „Package.appxmanifest“ mit den rot umrandeten Optionen „Verfügbare Deklarationen“, „Unterstützte Deklarationen“, „Pushbenachrichtigungen“ und „Einstiegspunkt“.][13]
16. Klicken Sie im Menü **Datei** auf **Alle speichern**.

## <a name="run-the-application"></a>Ausführen der Anwendung

Gehen Sie zum Ausführen der Anwendung folgendermaßen vor:

1. Führen Sie in Visual Studio die Web-API-Anwendung **AppBackend** aus. Daraufhin wird eine ASP.NET-Webseite angezeigt.
2. Führen Sie in Visual Studio die Windows Phone-App **NotifyUserWindowsPhone (Windows Phone 8.1)** aus. Der Windows Phone-Emulator wird ausgeführt und lädt die App automatisch.
3. Geben Sie in der UI der **NotifyUserWindowsPhone** -App einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben.
4. Klicken Sie auf der Benutzeroberfläche der App **NotifyUserWindowsPhone** auf **Log in and register**. Klicken Sie anschließend auf **Send push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
