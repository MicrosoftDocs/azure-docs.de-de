---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: baf0f07002a21a8e4e60bc17186107b471243202
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178598"
---
1. Erstellen Sie eine neue Klasse im Projekt `ToDoBroadcastReceiver`.
2. Fügen Sie der **ToDoBroadcastReceiver** -Klasse die folgenden using-Anweisungen hinzu:

    ```csharp
    using Gcm.Client;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. Fügen Sie die folgenden Berechtigungsanforderungen zwischen den **using**-Anweisungen und der **namespace**-Deklaration hinzu:

    ```csharp
    [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
    [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
    [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
    //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
    [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
    [assembly: UsesPermission(Name = "android.permission.INTERNET")]
    [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
    ```

4. Ersetzen Sie die vorhandene **ToDoBroadcastReceiver** -Klassendefinition durch folgende:

    ```csharp
    [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
    Categories = new string[] { "@PACKAGE_NAME@" })]
    public class ToDoBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
    {
        // Set the Google app ID.
        public static string[] senderIDs = new string[] { "<PROJECT_NUMBER>" };
    }
    ```

    Ersetzen Sie im obigen Code *`<PROJECT_NUMBER>`* durch die von Google beim Bereitstellen Ihrer App im Google-Entwicklerportal zugewiesene Projektnummer. 

5. Fügen Sie in der der Projektdatei "ToDoBroadcastReceiver.cs" den folgenden Code zur Definition der **PushHandlerService** -Klasse hinzu:

    ```csharp
    // The ServiceAttribute must be applied to the class.
    [Service]
    public class PushHandlerService : GcmServiceBase
    {
        public static string RegistrationID { get; private set; }
        public PushHandlerService() : base(ToDoBroadcastReceiver.senderIDs) { }
    }
    ```

    Beachten Sie, dass die Klasse von **GcmServiceBase** abgeleitet ist, und dass diese über das Attribut **Service** verfügen muss.

    > [!NOTE]
    > Die **GcmServiceBase**-Klasse implementiert die Methoden **OnRegistered()** , **OnUnRegistered()** , **OnMessage()** und **OnError()** . Sie müssen diese Methoden in der **PushHandlerService** -Klasse überschreiben.

6. Fügen Sie folgenden Code der **PushHandlerService**-Klasse hinzu, mit dem der **OnRegistered**-Ereignishandler überschrieben wird.

    ```csharp
    protected override void OnRegistered(Context context, string registrationId)
    {
        System.Diagnostics.Debug.WriteLine("The device has been registered with GCM.", "Success!");

        // Get the MobileServiceClient from the current activity instance.
        MobileServiceClient client = ToDoActivity.CurrentActivity.CurrentClient;
        var push = client.GetPush();

        // Define a message body for GCM.
        const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

        // Define the template registration as JSON.
        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyGCM }
        };

        try
        {
            // Make sure we run the registration on the same thread as the activity, 
            // to avoid threading errors.
            ToDoActivity.CurrentActivity.RunOnUiThread(

                // Register the template with Notification Hubs.
                async () => await push.RegisterAsync(registrationId, templates));

            System.Diagnostics.Debug.WriteLine(
                string.Format("Push Installation Id", push.InstallationId.ToString()));
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(
                string.Format("Error with Azure push registration: {0}", ex.Message));
        }
    }
    ```

    Diese Methode verwendet die zurückgegebene GCM-Registrierungs-ID für die Pushbenachrichtigungsregistrierung bei Azure. Tags können der Registrierung erst hinzugefügt werden, nachdem diese erstellt wurde. Weitere Informationen finden Sie unter [Gewusst wie: Hinzufügen von Tags zu einer Geräteinstallation für Push an Tags](../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

7. Überschreiben Sie die Methode **OnMessage** in **PushHandlerService** mit dem folgenden Code:

    ```csharp
    protected override void OnMessage(Context context, Intent intent)
    {
        string message = string.Empty;

        // Extract the push notification message from the intent.
        if (intent.Extras.ContainsKey("message"))
        {
            message = intent.Extras.Get("message").ToString();
            var title = "New item added:";

            // Create a notification manager to send the notification.
            var notificationManager = 
                GetSystemService(Context.NotificationService) as NotificationManager;

            // Create a new intent to show the notification in the UI. 
            PendingIntent contentIntent =
                PendingIntent.GetActivity(context, 0,
                new Intent(this, typeof(ToDoActivity)), 0);

            // Create the notification using the builder.
            var builder = new Notification.Builder(context);
            builder.SetAutoCancel(true);
            builder.SetContentTitle(title);
            builder.SetContentText(message);
            builder.SetSmallIcon(Resource.Drawable.ic_launcher);
            builder.SetContentIntent(contentIntent);
            var notification = builder.Build();

            // Display the notification in the Notifications Area.
            notificationManager.Notify(1, notification);

        }
    }
    ```

8. Überschreiben Sie die **OnUnRegistered()** -Methode und die **OnError()** -Methode mit folgendem Code:

    ```csharp
    protected override void OnUnRegistered(Context context, string registrationId)
    {
        throw new NotImplementedException();
    }

    protected override void OnError(Context context, string errorId)
    {
        System.Diagnostics.Debug.WriteLine(
            string.Format("Error occurred in the notification: {0}.", errorId));
    }
    ```
