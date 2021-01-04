---
title: Senden von Pushbenachrichtigungen an Xamarin.Android-Apps mit Azure Notification Hubs | Microsoft-Dokumentation
description: In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.Android-App senden.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: 95e244f956940f9d75df50243714f0fb8f8d0654
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424160"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Tutorial: Senden von Pushbenachrichtigungen an Xamarin.Android-Apps mit Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.Android-App senden. Sie erstellen eine leere Xamarin.Android-App, die Pushbenachrichtigungen mithilfe von Firebase Cloud Messaging (FCM) empfängt. Sie können über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der fertige Code steht in der [NotificationHubs-Beispiel-App](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) zur Verfügung.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen eines Firebase-Projekts und Aktivieren von Firebase Cloud Messaging
> * Erstellen eines Notification Hubs
> * Erstellen einer Xamarin.Android-App und Herstellen einer Verbindung mit dem Notification Hub
> * Senden von Testbenachrichtigungen im Azure-Portal

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* [Visual Studio mit Xamarin] unter Windows oder [Visual Studio für Mac] unter OS X.
* Ein aktives Google-Konto

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Erstellen eines Firebase-Projekts und Aktivieren von Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Erstellen eines Notification Hubs

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Konfigurieren von GCM/FCM-Einstellungen für den Notification Hub

1. Wählen Sie im linken Menü im Abschnitt **Settings** (Einstellungen) die Option **Google (GCM/FCM)** aus.
2. Geben Sie den **Serverschlüssel** ein, den Sie in der Google Firebase-Konsole notiert haben.
3. Wählen Sie auf der Symbolleiste **Speichern** aus.

    ![Screenshot von Notification Hub im Azure-Portal mit hervorgehobener und rot markierter Option „Google GCM/FCM“.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Der Notification Hub ist für die Arbeit mit FCM konfiguriert, und Sie besitzen die Verbindungszeichenfolgen, um die App für den Empfang von Benachrichtigungen zu registrieren und Pushbenachrichtigungen zu versenden.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Erstellen einer Xamarin.Android-App und Herstellen einer Verbindung mit dem Notification Hub

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Erstellen eines Visual Studio-Projekts und Hinzufügen von NuGet-Paketen

> [!NOTE]
> Die in diesem Tutorial dokumentierten Schritte gelten für Visual Studio 2017. 

1. Öffnen Sie in Visual Studio das Menü **Datei** , und wählen Sie die Optionen **Neu** und **Projekt**. Gehen Sie im Fenster **Neues Projekt** wie folgt vor:
    1. Erweitern Sie **Installiert**  > **Visual C#** , und klicken Sie auf **Android**.
    2. Wählen Sie in der Liste die Option **Android-App (Xamarin)** aus.
    3. Geben Sie einen **Namen** für das Projekt ein.
    4. Wählen Sie einen **Speicherort** für das Projekt aus.
    5. Klicken Sie auf **OK**.

        ![Dialogfeld "Neues Projekt"](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Wählen Sie im Dialogfeld **Neue Android-App** die Option **Leere App** und anschließend **OK** aus.

    ![Screenshot: Hervorgehobene Vorlage „Leere App“](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Erweitern Sie im **Projektmappen-Explorer** -Fenster **Eigenschaften** , und klicken Sie auf **AndroidManifest.xml**. Aktualisieren Sie den Paketnamen entsprechend dem Paketnamen, den Sie eingegeben haben, als Sie Ihrem Projekt in der Google Firebase-Konsole Firebase Cloud Messaging hinzugefügt haben.

    ![Paketname in GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Führen Sie die folgenden Schritte aus, um die Android-Zielversion für das Projekt auf **Android 9.0 (Pie)** festzulegen: 
    1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Eigenschaften** aus. 
    1. Wählen Sie für das Feld **Compile using Android version: (Target framework)** (Mit Android-Version kompilieren: Zielframework)) die Option **Android 9.0 (Pie)** aus. 
    1. Wählen Sie im Meldungsfeld **Ja** aus, um die Änderung des Zielframeworks fortzusetzen.
1. Fügen Sie mit den folgenden Schritten dem Projekt die erforderlichen NuGet-Pakete hinzu:
    1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten...** aus.
    1. Wechseln Sie zur Registerkarte **Installiert** , und wählen Sie **Xamarin.Android.Support.Design** und dann im rechten Bereich **Aktualisieren** aus, um das Paket auf die aktuelle Version zu aktualisieren.
    1. Wechseln Sie zur Registerkarte **Durchsuchen**. Suchen Sie nach **Xamarin.GooglePlayServices.Base**. Wählen Sie **Xamarin.GooglePlayServices.Base** in der Ergebnisliste aus. Wählen Sie dann **Installieren** aus.

        ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Suchen Sie im Fenster **NuGet-Paket-Manager** nach **Xamarin.Firebase.Messaging**. Wählen Sie **Xamarin.Firebase.Messaging** in der Ergebnisliste aus. Wählen Sie dann **Installieren** aus.
    7. Suchen Sie jetzt nach **Xamarin.Azure.NotificationHubs.Android**. Wählen Sie **Xamarin.Azure.NotificationHubs.Android** in der Ergebnisliste aus. Wählen Sie dann **Installieren** aus.

### <a name="add-the-google-services-json-file"></a>Hinzufügen der JSON-Datei von Google Services

1. Kopieren Sie die Datei `google-services.json`, die Sie aus der Google Firebase-Konsole in den Projektordner heruntergeladen haben.
2. Fügen Sie `google-services.json` zum Projekt hinzu.
3. Wählen Sie im **Projektmappen-Explorer** die Datei `google-services.json` aus.
4. Legen Sie im Bereich **Eigenschaften** die Buildaktion auf **GoogleServicesJson** fest. Wenn **GoogleServicesJson** nicht angezeigt wird, schließen Sie Visual Studio, starten Sie es neu, öffnen Sie das Projekt erneut, und versuchen Sie es erneut.

    ![Buildaktion GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Einrichten von Notification Hubs in Ihrem Projekt

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrieren bei Firebase Cloud Messaging

1. Öffnen Sie die Datei `AndroidManifest.xml`, und fügen Sie die folgenden `<receiver>`-Elemente in das `<application>`-Element ein:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Fügen Sie die folgenden Anweisungen **vor dem Anwendungselement** hinzu.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Erfassen Sie für Ihre Android-App und den Notification Hub folgende Informationen:

   * **Verbindungszeichenfolge für Überwachung:** Wählen Sie im [Azure portal] auf dem Dashboard die Option **Verbindungszeichenfolgen anzeigen**. Kopieren Sie die Verbindungszeichenfolge `DefaultListenSharedAccessSignature` für diesen Wert.
   * **Hub-Name:** Der Name des Hubs aus dem [Azure portal]. Beispiel: *mynotificationhub2*.
4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das **Projekt** , und wählen Sie **Hinzufügen** und anschließend **Klasse** aus.
5. Erstellen Sie eine `Constants.cs`-Klasse für Ihr Xamarin-Projekt, und definieren Sie die folgenden Konstantenwerte in der Klasse. Ersetzen Sie die Platzhalter durch Ihre Werte.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Fügen Sie `MainActivity.cs` die folgenden using-Anweisungen hinzu:

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```

7. Fügen Sie der MainActivity-Klasse die folgenden Eigenschaften hinzu. Die TAG-Variable dient zum Anzeigen eines Warndialogfelds, wenn die App ausgeführt wird:

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```

8. Fügen Sie der MainActivity-Klasse die folgende Methode hinzu. Sie überprüft, ob **Google Play Services** auf dem Gerät verfügbar ist.

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }

        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```

9. Fügen Sie der MainActivity-Klasse die folgende Methode hinzu, um einen Benachrichtigungskanal zu erstellen.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }

        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };

        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```

10. Fügen Sie `OnCreate` in `MainActivity.cs` nach `base.OnCreate(savedInstanceState)` den folgenden Code hinzu:

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }

    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```

15. Fügen Sie dem Projekt eine Klasse namens `MyFirebaseMessagingService` hinzu. 
16. Fügen Sie `MyFirebaseMessagingService.cs` die folgenden using-Anweisungen hinzu:

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;    
    using WindowsAzure.Messaging;
    ```

17. Fügen Sie oberhalb Ihrer Klassendeklaration Folgendes hinzu, und legen Sie fest, dass Ihre Klasse von `FirebaseMessagingService` erbt:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```

18. Fügen Sie den folgenden Code in der `MyFirebaseMessagingService`-Klasse hinzu, um die empfangenen Nachrichten zu verarbeiten. 

    ```csharp
        const string TAG = "MyFirebaseMsgService";
        NotificationHub hub;
    
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if (message.GetNotification() != null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());
    
            }
        }
    
        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

19. Fügen Sie der Klasse „MyFirebaseMessagingService“ die folgenden Methoden hinzu (direkt unter dem im vorherigen Schritt hinzugefügten Code), um das FCM-Registrierungstoken zu erhalten und an die Notification Hubs-Instanz (hub) zu senden. 

    ```csharp
        public override void OnNewToken(string token)
        {
            Log.Debug(TAG, "FCM token: " + token);
            SendRegistrationToServer(token);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                        Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
1. **Erstellen** Sie Ihr Projekt.
1. **Ausführen** Ihrer App auf Ihrem Gerät oder im geladenen Emulator

## <a name="send-test-notification-from-the-azure-portal"></a>Senden einer Testbenachrichtigung im Azure-Portal

Mit der Option **Testsendevorgang** im [Azure portal] können Sie den Empfang von Benachrichtigungen in Ihrer App testen. Diese Option sendet zu Testzwecken eine Pushbenachrichtigung an Ihr Gerät.

![Azure-Portal – Testsendung](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Pushbenachrichtigungen werden normalerweise in einem Back-End-Dienst, z.B. Mobile Services oder ASP.NET, über eine kompatible Bibliothek gesendet. Falls für Ihr Back-End keine Bibliothek verfügbar ist, können Sie Benachrichtigungen auch direkt über die REST-API senden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Broadcastbenachrichtigungen an alle Android-Geräte gesendet, die beim Back-End registriert sind. Um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte Android-Geräte senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden von Pushbenachrichtigungen an bestimmte Geräte](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio mit Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio für Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android