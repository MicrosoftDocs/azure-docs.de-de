---
title: Senden von Pushbenachrichtigungen an Android mit Azure Notification Hubs und Firebase SDK-Version 0.6| Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs und Google Firebase Cloud Messaging Pushbenachrichtigungen an Android-Geräte senden (Version 0.6).
services: notification-hubs
documentationcenter: android
keywords: Pushbenachrichtigungen,Pushbenachrichtigung,Android-Pushbenachrichtigung,FCM,Firebase Cloud Messaging
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: c5485dacc4d9e3210ad69819caf4e36f96c626da
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428377"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-06"></a>Tutorial: Senden von Pushbenachrichtigungen an Android-Geräte mit Firebase SDK-Version 0.6

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs und Firebase Cloud Messaging (FCM) SDK-Version 0.6 Pushbenachrichtigungen an eine Android-Anwendung senden. In diesem Tutorial erstellen Sie eine leere Android-App, die Pushbenachrichtigungen mithilfe von Firebase Cloud Messaging (FCM) empfängt.

Den vollständigen Code für dieses Tutorial können Sie [von GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp) herunterladen.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen eines Android Studio-Projekts.
> * Erstellen eines Firebase-Projekts, das Firebase Cloud Messaging unterstützt.
> * Erstellen Sie einen Hub.
> * Verbinden Sie Ihre App mit dem Hub.
> * Testen der App.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Einen Monat kostenlos testen](https://azure.microsoft.com/free/). 

Sie benötigen außerdem folgende Elemente: 

* Aktuelle Version von [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 oder höher für Firebase Cloud Messaging
* Google Repository Version 27 oder höher für Firebase Cloud Messaging
* Google Play Services 9.0.2 oder höher für Firebase Cloud Messaging

Das Abschließen dieses Tutorial ist eine Voraussetzung für alle anderen Notification Hubs-Tutorials für Android-Apps.

## <a name="create-an-android-studio-project"></a>Erstellen eines Android Studio-Projekts

1. Starten Sie Android Studio.
2. Wählen Sie **File** (Datei), zeigen Sie auf **New** (Neu), und wählen Sie dann **New Project** (Neues Projekt). 
2. Wählen Sie auf der Seite **Choose your project** (Projekt auswählen) die Option **Empty Activity** (Leere Aktivität) und dann **Next** (Weiter) aus. 
3. Führen Sie auf der Seite **Configure your project** (Projekt konfigurieren) die folgenden Schritte aus: 
    1. Geben Sie einen Namen für die Anwendung ein.
    2. Geben Sie einen Speicherort an, in dem die Projektdateien gespeichert werden. 
    3. Wählen Sie **Fertig stellen** aus. 

        ![Konfigurieren des Projekts](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Erstellen eines Firebase-Projekts, das FCM unterstützt

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurieren eines Hubs

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurieren von Firebase Cloud Messaging-Einstellungen für den Hub

1. Wählen Sie im linken Bereich unter **Einstellungen** die Option **Google (GCM/FCM)** aus. 
2. Geben Sie den **Serverschlüssel** für das FCM-Projekt ein, den Sie zuvor gespeichert haben. 
3. Wählen Sie auf der Symbolleiste **Save** (Speichern) aus. 

    ![Azure Notification Hub – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Im Azure-Portal wird in den Warnungen in einer Meldung angezeigt, dass der Hub aktualisiert wurde. Die Schaltfläche **Save** (Speichern) ist deaktiviert. 

Der Hub ist jetzt für die Verwendung mit Firebase Cloud Messaging konfiguriert. Sie verfügen außerdem über die Verbindungszeichenfolgen, die erforderlich sind, um Benachrichtigungen an ein Gerät zu senden und eine App für den Empfang von Benachrichtigungen zu registrieren.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

### <a name="add-google-play-services-to-the-project"></a>Hinzufügen von Google Play Services zum Projekt

1. Wählen Sie in Android Studio im Menü die Option **Tools** und dann **SDK Manager** aus. 
2. Wählen Sie die Zielversion des Android SDK aus, das in Ihrem Projekt verwendet wird. Wählen Sie dann **Paketdetails anzeigen** aus. 

    ![Android SDK Manager – Auswählen der Zielversion](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Wählen Sie die Option **Google-APIs** , falls diese Installation noch nicht durchgeführt wurde.

    ![Android SDK Manager – „Google-APIs“ ausgewählt](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Wechseln Sie auf die Registerkarte **SDK Tools** (SDK-Tools). Wählen Sie wie in der folgenden Abbildung dargestellt die Option **Google Play Services** , falls Google Play Services noch nicht installiert ist. Wählen Sie dann **Anwenden** aus, um die Installation auszuführen. Notieren Sie den SDK-Pfad, den Sie in einem späteren Schritt angeben müssen.

    ![Android SDK Manager – „Google Play Services“ ausgewählt](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Wählen Sie **OK** , wenn das Dialogfeld **Änderung bestätigen** angezeigt wird. Die gewünschten Komponenten werden mit dem entsprechenden Installationsprogramm installiert. Wählen Sie **Fertig stellen** , wenn die Installation der Komponenten abgeschlossen ist.
4. Wählen Sie **OK** , um das Dialogfeld **Settings for New Projects** (Einstellungen für neue Projekte) zu schließen.  
1. Öffnen Sie die Datei „AndroidManifest.xml“, und fügen Sie dann das folgende Tag zum Tag *application* hinzu.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Hinzufügen von Azure Notification Hubs-Bibliotheken

1. Fügen Sie in der Datei „Build.Gradle“ der App im Abschnitt „dependencies“ die folgenden Zeilen hinzu.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. Fügen Sie nach dem Abschnitt „dependencies“ das folgende Repository hinzu:

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Hinzufügen von Unterstützung für Google Firebase

1. Fügen Sie in der Datei „Build.Gradle“ für die App im Abschnitt **dependencies** die folgenden Zeilen hinzu, sofern sie noch nicht vorhanden sind. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Fügen Sie am Ende der Datei das folgende Plug-In hinzu, sofern es noch nicht vorhanden ist. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Wählen Sie auf der Symbolleiste **Sync Now** (Jetzt synchronisieren) aus.

### <a name="update-the-androidmanifestxml-file"></a>Aktualisieren der Datei „AndroidManifest.xml“

1. Nachdem Sie das FCM-Registrierungstoken erhalten haben, verwenden Sie es für die [Registrierung bei Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Sie unterstützen diese Registrierung im Hintergrund mit einem `IntentService` mit dem Namen `RegistrationIntentService`. Mit diesem Dienst wird auch das FCM-Registrierungstoken aktualisiert. Darüber hinaus erstellen Sie eine Klasse namens `FirebaseService` als Unterklasse von `FirebaseMessagingService` und überschreiben die Methode `onMessageReceived`, um Benachrichtigungen zu erhalten und zu verarbeiten. 

    Fügen Sie der Datei „AndroidManifest.xml“ im Tag `<application>` die unten angegebene Dienstdefinition hinzu.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Fügen Sie unterhalb des Tags `</application>` die folgenden erforderlichen FCM-bezogenen Berechtigungen hinzu.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Code hinzufügen

1. Erweitern Sie in der Projektansicht die Knoten **app** > **src** > **main** > **java** . Klicken Sie mit der rechten Maustaste unter **java** auf Ihren Paketordner. Wählen Sie **Neu** und dann **Java-Klasse** aus. Geben Sie **NotificationSettings** als Name ein, und wählen Sie **OK** aus.

    Aktualisieren Sie diese drei Platzhalter im weiter unten angegebenen Code für die `NotificationSettings`-Klasse:

   * **HubListenConnectionString:** Die **DefaultListenAccessSignature** -Verbindungszeichenfolge für Ihren Hub. Sie können diese Verbindungszeichenfolge kopieren, indem Sie im [Azure portal] im Hub auf **Zugriffsrichtlinien** klicken.
   * **HubName:** Verwenden Sie den Namen des Hubs, der im [Azure portal] auf der Hubseite angezeigt wird.

     `NotificationSettings` -Code:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Geben Sie für den Hub den **Namen** und einen Wert für **DefaultListenSharedAccessSignature** ein, bevor Sie fortfahren. 

2. Fügen Sie dem Projekt eine weitere neue Klasse namens `RegistrationIntentService`hinzu. Diese Klasse implementiert die `IntentService`-Schnittstelle. Außerdem wird mit dieser Klasse die [Aktualisierung des FCM-Tokens](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) und die [Registrierung beim Notification Hub](notification-hubs-push-notification-registration-management.md) verarbeitet.

    Verwenden Sie für diese Klasse den folgenden Code.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
               else if (!(storedToken = sharedPreferences.getString("FCMtoken", "")).equals(FCM_token)) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. Fügen Sie in der `MainActivity`-Klasse oberhalb der Klassendeklaration die folgenden `import`-Anweisungen hinzu.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Fügen Sie im oberen Bereich der Klasse die folgenden Member hinzu. Sie verwenden diese Felder zum [Überprüfen der Verfügbarkeit von Google Play Services gemäß Empfehlung von Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. Fügen Sie in der `MainActivity`-Klasse die folgende Methode hinzu, um die Verfügbarkeit von Google Play Services zu überprüfen.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

6. Fügen Sie in der `MainActivity`-Klasse den folgenden Code hinzu, mit dem eine Überprüfung auf Google Play Services durchgeführt wird, bevor Sie `IntentService` aufrufen, um Ihr FCM-Registrierungstoken abzurufen und die Registrierung beim Hub vorzunehmen:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

7. Fügen Sie in der `OnCreate`-Methode der `MainActivity`-Klasse den folgenden Code hinzu, um den Registrierungsprozess zu starten, wenn die Aktivität erstellt wird:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Fügen Sie dem `MainActivity`-Element diese zusätzlichen Methoden hinzu, um den App-Zustand zu überprüfen und den Status in Ihrer App zu melden:

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

9. Für die `ToastNotify`-Methode wird das `TextView`-Steuerelement *„Hello World“* verwendet, um Status und Benachrichtigungen dauerhaft in der App zu melden. Fügen Sie im Layout von **res** > **layout** > **activity_main.xml** die folgende ID für dieses Steuerelement hinzu.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Screenshot: android:id="@+id/text_hello"-ID, die auf das TextView-Steuerelement angewendet wird.](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Als Nächstes fügen Sie eine Unterklasse für den Empfänger hinzu, den Sie in „AndroidManifest.xml“ definiert haben. Fügen Sie dem Projekt eine weitere neue Klasse namens `FirebaseService`hinzu.

11. Fügen Sie am Anfang von `FirebaseService.java` die folgenden Importanweisungen hinzu:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Fügen Sie für die `FirebaseService`-Klasse den folgenden Code hinzu, um sie als Unterklasse von `FirebaseMessagingService` festzulegen.

    Dieser Code überschreibt die `onMessageReceived`-Methode und meldet empfangene Benachrichtigungen. Er sendet außerdem die Pushbenachrichtigung mit der `sendNotification()`-Methode an den Android-Benachrichtigungs-Manager. Rufen Sie die `sendNotification()`-Methode auf, wenn die App nicht ausgeführt wird und eine Benachrichtigung eingeht.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. Wählen Sie in Android Studio auf der Menüleiste die Optionen **Build** > **Rebuild Project** aus, um sicherzustellen, dass im Code keine Fehler enthalten sind. Wenn eine Fehlermeldung zum Symbol `ic_launcher` angezeigt wird, entfernen Sie die folgende Anweisung aus der Datei „AndroidManifest.xml“: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Stellen Sie sicher, dass Sie über ein virtuelles Gerät zum Ausführen der App verfügen. Wenn Sie keines haben, fügen Sie wie folgt eines hinzu:
    1. ![Geräte-Manager öffnen](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Virtuelles Gerät erstellen](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Führen Sie die App auf Ihrem ausgewählten Gerät aus, und vergewissern Sie sich, dass sie richtig beim Hub registriert wird.

    > [!NOTE]
    > Bei der Registrierung treten beim ersten Starten unter Umständen Fehler auf, bis die `onTokenRefresh()`-Methode des Instanz-ID-Diensts aufgerufen wird. Durch die Aktualisierung sollte eine erfolgreiche Registrierung beim Notification Hub initiiert werden.

    ![Erfolgreiche Geräteregistrierung](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Testsendung der Benachrichtigung über den Notification Hub

Sie können Pushbenachrichtigungen mit folgenden Schritten über das [Azure portal] senden:

1. Wählen Sie im Azure-Portal auf der Seite „Notification Hub“ für Ihren Hub im Abschnitt **Problembehandlung** die Option **Testsendevorgang** aus.
3. Wählen Sie für **Plattformen** die Option **Android** aus.
4. Wählen Sie **Senden** aus.  Es wird noch keine Benachrichtigung auf dem Android-Gerät angezeigt, da Sie die mobile App nicht darauf ausgeführt haben. Wählen Sie nach dem Ausführen der mobilen App erneut die Schaltfläche **Senden** aus, um die Benachrichtigung anzuzeigen.
5. Sie sehen das Ergebnis des Vorgangs in der Liste unten im Fenster.

    ![Azure Notification Hubs – Testsendung](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Die Benachrichtigung wird auf Ihrem Gerät angezeigt. 

    ![Benachrichtigung auf Gerät](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Ausführen der mobilen App im Emulator

Vor dem Testen von Pushbenachrichtigungen in einem Emulator müssen Sie sicherstellen, dass das Emulatorimage die Google-API-Ebene unterstützt, die Sie für die App ausgewählt haben. Wenn das Image keine nativen Google-APIs unterstützt, wird unter Umständen die Ausnahme **SERVICE\_NOT\_AVAILABLE** angezeigt.

Stellen Sie zusätzlich sicher, dass Ihr Google-Konto dem ausgeführten Emulator unter **Einstellungen** > **Konten** hinzugefügt wurde. Andernfalls führt die Registrierung bei FCM möglicherweise zur Ausnahme **AUTHENTICATION\_FAILED** .

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mit Firebase Cloud Messaging Benachrichtigungen an alle Android-Geräte gesendet, die bei dem Dienst registriert wurden. Um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte Geräte senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Tutorial: Senden von Pushbenachrichtigungen an bestimmte Android-Geräte](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure portal]: https://portal.azure.com
