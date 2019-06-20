---
title: Hinzufügen von Pushbenachrichtigungen zur Xamarin.Forms-App | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure-Dienste verwenden, um plattformübergreifende Pushbenachrichtigungen an Ihre Xamarin.Forms-Apps zu senden.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 99f2d9fb7c9a74e57eff3cd0b007fcee459cab88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62098700"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Hinzufügen von Pushbenachrichtigungen zur Xamarin.Forms-App

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Übersicht

In diesem Tutorial fügen Sie allen Projekten, die auf dem [Xamarin.Forms-Schnellstart](app-service-mobile-xamarin-forms-get-started.md) basieren, Pushbenachrichtigungen hinzu. Dies bedeutet, dass immer dann eine Pushbenachrichtigung an alle plattformübergreifenden Clients gesendet wird, wenn ein Eintrag eingefügt wird.

Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Erweiterungspaket für Pushbenachrichtigungen hinzufügen. Weitere Informationen finden Sie unter [Arbeiten mit dem .NET Back-End-Server SDK für Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Voraussetzungen

Für iOS benötigen Sie eine [Apple-Entwicklerprogramm-Mitgliedschaft](https://developer.apple.com/programs/ios/) und ein physisches iOS-Gerät. Der [iOS-Simulator unterstützt keine Pushbenachrichtigungen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurieren eines Notification Hubs

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualisieren des Serverprojekts zum Senden von Pushbenachrichtigungen

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurieren und Ausführen des Android-Projekts (optional)

Arbeiten Sie diesen Abschnitt durch, um Pushbenachrichtigungen für das Xamarin.Forms-Droid-Projekt für Android zu aktivieren.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Aktivieren von Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurieren des Mobile Apps-Back-Ends zum Senden von Pushanforderungen per FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Hinzufügen von Pushbenachrichtigungen zum Android-Projekt

Wenn das Back-End per FCM konfiguriert wurde, können Sie dem Client für die Registrierung bei FCM Komponenten und Codes hinzufügen. Außerdem können Sie die Registrierung für Pushbenachrichtigungen mit Azure Notification Hubs über das Mobile Apps-Back-End durchführen und Benachrichtigungen erhalten.

1. Klicken Sie im Projekt **Droid** mit der rechten Maustaste auf **Verweise > NuGet-Pakete verwalten**.
1. Suchen Sie im Fenster „NuGet-Paket-Manager“ nach dem Paket **Xamarin.Firebase.Messaging**, und fügen Sie es dem Projekt hinzu.
1. Legen Sie in den Projekteigenschaften für das Projekt **Droid** fest, dass die App mit der Android-Version 7.0 oder höher kompiliert werden soll.
1. Fügen Sie die über die Firebase-Konsole heruntergeladene Datei **google-services.json** dem Stamm des Projekts **Droid** hinzu, und legen Sie den zugehörigen Buildvorgang auf **GoogleServicesJson** fest. Weitere Informationen finden Sie unter [Hinzufügen der Google Services JSON-Datei](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrieren bei Firebase Cloud Messaging

1. Öffnen Sie die Datei **AndroidManifest.xml**, und fügen Sie die folgenden `<receiver>`-Elemente in das `<application>`-Element ein:

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

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementieren des Diensts Firebase Instance ID

1. Fügen Sie dem Projekt **Droid** eine neue Klasse mit dem Namen `FirebaseRegistrationService` hinzu, und stellen Sie sicher, dass am Anfang der Datei die folgenden `using`-Anweisungen vorhanden sind:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Ersetzen Sie die leere Klasse `FirebaseRegistrationService` durch den folgenden Code:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    Die Klasse `FirebaseRegistrationService` ist für das Generieren von Sicherheitstoken zuständig, die die Anwendung autorisieren, auf FCM zuzugreifen. Die `OnTokenRefresh`-Methode wird aufgerufen, wenn die Anwendung ein Registrierungstoken von FCM empfängt. Die Methode ruft das Token von der `FirebaseInstanceId.Instance.Token`-Eigenschaft ab, die von FCM asynchron aktualisiert wird. Die `OnTokenRefresh`-Methode wird nur selten aufgerufen, weil eine Aktualisierung des Tokens nur erfolgt, wenn die Anwendung installiert oder deinstalliert wird, der Benutzer Anwendungsdaten löscht, die Anwendung die Instanz-ID löscht oder die Sicherheit des Tokens gefährdet ist. Darüber hinaus verlangt der Dienst FCM Instance ID, dass die Anwendung das Token regelmäßig (in der Regel alle 6 Monate) aktualisiert.

    Die `OnTokenRefresh`-Methode ruft auch die `SendRegistrationTokenToAzureNotificationHub`-Methode auf, die verwendet wird, um dem Azure Notification Hub das Registrierungstoken des Benutzers zuzuordnen.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrieren beim Azure Notification Hub

1. Fügen Sie dem Projekt **Droid** eine neue Klasse mit dem Namen `AzureNotificationHubService` hinzu, und stellen Sie sicher, dass am Anfang der Datei die folgenden `using`-Anweisungen vorhanden sind:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Ersetzen Sie die leere Klasse `AzureNotificationHubService` durch den folgenden Code:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    Die `RegisterAsync`-Methode erstellt eine einfache Benachrichtigungsmeldungsvorlage als JSON-Datei und registriert sich für den Empfang von Vorlagenbenachrichtigungen vom Notification Hub, wobei das Firebase-Registrierungstoken verwendet wird. Dadurch wird sichergestellt, dass alle vom Azure Notification Hub gesendeten Benachrichtigungen an das Gerät übermittelt werden, das durch das Registrierungstoken dargestellt wird.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Anzeigen des Inhalts einer Pushbenachrichtigung

1. Fügen Sie dem Projekt **Droid** eine neue Klasse mit dem Namen `FirebaseNotificationService` hinzu, und stellen Sie sicher, dass am Anfang der Datei die folgenden `using`-Anweisungen vorhanden sind:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Ersetzen Sie die leere Klasse `FirebaseNotificationService` durch den folgenden Code:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    Die `OnMessageReceived`-Methode, die aufgerufen wird, wenn eine Anwendung eine Benachrichtigung von FCM empfängt, extrahiert den Nachrichteninhalt und ruft die `SendNotification`-Methode ab. Diese Methode konvertiert den Nachrichteninhalt in eine lokale Benachrichtigung, die während der Ausführung der Anwendung gestartet wird, wobei die Benachrichtigung im Infobereich angezeigt wird.

Sie können nun Pushbenachrichtigungen in der App testen, die auf einem Android-Gerät oder im Emulator ausgeführt wird.

### <a name="test-push-notifications-in-your-android-app"></a>Testen von Pushbenachrichtigungen in der Android-App

Die ersten beiden Schritte sind nur beim Testen in einem Emulator erforderlich.

1. Stellen Sie sicher, dass Sie Bereitstellungs- oder Debugaufgaben auf einem mit Google Play Services konfigurierten Gerät oder Emulator durchführen. Dies können Sie erreichen, indem Sie überprüfen, ob die **Play**-Apps auf dem Gerät oder Emulator installiert sind.
2. Fügen Sie auf dem Android-Gerät ein Google-Konto hinzu, indem Sie auf **Apps** > **Einstellungen** > **Konto hinzufügen** klicken. Befolgen Sie anschließend die Anweisungen zum Hinzufügen eines vorhandenen Google-Kontos zum Gerät bzw. zum Erstellen eines neuen Kontos.
3. Klicken Sie in Visual Studio oder Xamarin Studio mit der rechten Maustaste auf das Projekt **Droid**, und klicken Sie dann auf **Als Startprojekt festlegen**.
4. Klicken Sie auf **Ausführen**, um das Projekt zu erstellen und die App auf Ihrem Android-Gerät oder im Emulator zu starten.
5. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen ( **+** ).
6. Überprüfen Sie, ob beim Hinzufügen eines Elements eine Benachrichtigung empfangen wird.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurieren und Ausführen des iOS-Projekts (optional)

Dieser Abschnitt bezieht sich auf das Ausführen des Xamarin iOS-Projekts für iOS-Geräte. Wenn Sie nicht mit iOS-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurieren des Notification Hubs für APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Im nächsten Schritt konfigurieren Sie die iOS-Projekteinstellung in Xamarin Studio oder Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Hinzufügen von Pushbenachrichtigungen zu Ihrer iOS-App

1. Öffnen Sie im Projekt **iOS** die Datei „AppDelegate.cs“, und fügen Sie oben in der Codedatei die folgende Anweisung hinzu.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Fügen Sie der **AppDelegate**-Klasse eine Überschreibung für das **RegisteredForRemoteNotifications**-Ereignis hinzu, um sich für Benachrichtigungen zu registrieren:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. Fügen Sie in **AppDelegate** auch die folgende Überschreibung für den **DidReceiveRemoteNotification**-Ereignishandler hinzu:

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Diese Methode verarbeitet eingehende Benachrichtigungen, während die App ausgeführt wird.

4. Fügen Sie in der **AppDelegate**-Klasse der **FinishedLaunching**-Methode den folgenden Code hinzu:

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Dies ermöglicht die Unterstützung von Remotebenachrichtigungen und fordert die Pushregistrierung an.

Ihre App kann Pushbenachrichtigungen nun unterstützen.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testen von Pushbenachrichtigungen in der iOS-App

1. Klicken Sie mit der rechten Maustaste auf das iOS-Projekt, und klicken Sie auf **Als Startprojekt festlegen**.
2. Klicken Sie auf die Schaltfläche **Ausführen**, oder drücken Sie in Visual Studio die Taste **F5**, um das Projekt zu erstellen und die App in einem iOS-Gerät zu starten. Klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren.

   > [!NOTE]
   > Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

3. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen ( **+** ).
4. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurieren und Ausführen von Windows-Projekten (optional)

In diesem Abschnitt geht es um das Ausführen der Projekte „Xamarin.Forms WinApp“ und „WinPhone81“ für Windows-Geräte. Diese Schritte eignen sich auch für UWP-Projekte (Universelle Windows-Plattform). Wenn Sie nicht mit Windows-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrieren der Windows-App für Pushbenachrichtigungen mit dem Windows-Benachrichtigungsdienst (Windows Notification Service, WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurieren des Notification Hubs für WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Hinzufügen von Pushbenachrichtigungen zu Ihrer Windows-App

1. Öffnen Sie in Visual Studio in einem Windows-Projekt die Datei **App.xaml.cs**, und fügen Sie die folgenden Anweisungen hinzu.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Ersetzen Sie `<your_TodoItemManager_portable_class_namespace>` durch den Namespace Ihres portierbaren Projekts, das die `TodoItemManager`-Klasse enthält.

2. Fügen Sie der Datei „App.xaml.cs“ die folgende **InitNotificationsAsync**-Methode hinzu:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Diese Methode ruft den Pushbenachrichtigungskanal ab und registriert eine Vorlage für das Empfangen von Vorlagenbenachrichtigungen von Ihrem Notification Hub. Eine Vorlagenbenachrichtigung, die *messageParam* unterstützt, wird an diesen Client übermittelt.

3. Aktualisieren Sie in „App.xaml.cs“ die Definition der Ereignishandlermethode **OnLaunched**, indem Sie den Modifizierer `async` hinzufügen. Fügen Sie am Ende der Methode die folgende Codezeile hinzu:

    ```csharp
    await InitNotificationsAsync();
    ```

    So wird sichergestellt, dass die Registrierung der Pushbenachrichtigung bei jedem Start der App erstellt oder aktualisiert wird. Dies ist wichtig, um sicherzustellen, dass der WNS-Pushkanal immer aktiv ist.  

4. Öffnen Sie im Projektmappen-Explorer von Visual Studio die Datei **Package.appxmanifest**, und legen Sie unter **Benachrichtigungen** für **Toastfähig** die Option **Ja** fest.
5. Erstellen Sie die App, und stellen Sie sicher, dass keine Fehler auftreten. Sie sollten nun Ihre Client-App für die Vorlagenbenachrichtigungen vom Mobile Apps-Back-End registrieren. Wiederholen Sie die Schritte in diesem Abschnitt für alle Windows-Projekte Ihrer Projektmappe.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testen von Pushbenachrichtigungen in Ihrer Windows-App

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf ein Windows-Projekt, und klicken Sie auf **Als Startprojekt festlegen**.
2. Klicken Sie auf die Schaltfläche **Ausführen** , um das Projekt zu erstellen und die App zu starten.
3. Geben Sie in der App einen Namen für ein neues TodoItem ein, und klicken Sie dann auf das Pluszeichen ( **+** ), um es hinzuzufügen.
4. Überprüfen Sie, ob beim Hinzufügen des Elements eine Benachrichtigung empfangen wird.

## <a name="next-steps"></a>Nächste Schritte

Sie können sich ausführlicher über Pushbenachrichtigungen informieren:

* [Senden von Pushbenachrichtigungen von Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Remotebenachrichtigungen mit Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Azure Notification Hubs - Diagnosis guidelines](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  (Azure Notification Hubs – Diagnoserichtlinien): Benachrichtigungen können aus unterschiedlichen Gründen verloren gehen oder nicht bei Geräten ankommen. In diesem Thema wird gezeigt, wie Sie die Grundursache von Pushbenachrichtigungsfehlern analysieren und ermitteln.

Außerdem können Sie mit einem der folgenden Tutorials fortfahren:

* [Hinzufügen von Authentifizierung zur App](app-service-mobile-xamarin-forms-get-started-users.md)  
  Hier erhalten Sie Informationen zur Authentifizierung von Benutzern der App mit einem Identitätsanbieter.
* [Aktivieren der Offlinesynchronisierung für Ihre App](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Informieren Sie sich, wie Sie die Offlineunterstützung für Ihre App mit einem Mobile Apps-Back-End hinzufügen. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (also das Anzeigen, Hinzufügen oder Ändern von Daten) auch ohne bestehende Netzwerkverbindung.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
