---
title: Senden von Pushbenachrichtigungen an Kindle-Apps mit Azure Notification Hubs | Microsoft-Dokumentation
description: In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Kindle-Anwendung senden.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927018"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Erste Schritte mit Notification Hubs für Kindle-Apps

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In diesem Lernprogramm wird gezeigt, wie Sie mit Azure Benachrichtigungshubs Pushbenachrichtigungen an eine Kindle-Anwendung senden können. Sie erstellen eine leere Kindle-App, die Pushbenachrichtigungen mithilfe von Amazon Device Messaging (ADM) empfängt.

In diesem Tutorial erstellen/aktualisieren Sie Code, um die folgenden Aufgaben auszuführen:

> [!div class="checklist"]
> * Hinzufügen einer neuen App zum Entwicklerportal
> * Erstellen eines API-Schlüssels
> * Erstellen und Konfigurieren eines Notification Hubs
> * Einrichten der Anwendung
> * Erstellen des ADM-Meldungshandlers
> * Hinzufügen Ihres API-Schlüssels zur App
> * Ausführen der App
> * Senden einer Testbenachrichtigung

## <a name="prerequisites"></a>Voraussetzungen

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE)
- Führen Sie die Schritte unter [Setting Up Your Development Environment](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) zum Einrichten der Entwicklungsumgebung für Kindle aus.

## <a name="add-a-new-app-to-the-developer-portal"></a>Hinzufügen einer neuen App zum Entwicklerportal

1. Melden Sie sich am [Amazon-Entwicklerportal](https://developer.amazon.com/apps-and-games/console/apps/list.html) an.
2. Wählen Sie **Add New App** und dann **Android**.  

    ![Schaltfläche „Add New App“](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. Führen Sie auf der Seite **New App Submission** diese Schritte aus, um den **Application Key** (Anwendungsschlüssel) abzurufen:
    1. Geben Sie unter **App title** einen Namen ein.
    2. Wählen Sie unter **Category** eine beliebige Kategorie aus (z. B. Education).
    4. Geben Sie im Feld **Customer support email address** eine E-Mail-Adresse ein. 
    5. Wählen Sie **Speichern** aus.

        ![Seite „New App Submission“](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  Wechseln Sie oben zur Registerkarte **Mobile Ads**, und führen Sie die folgenden Schritte aus: 
    1. Geben Sie an, ob Ihre App vor allem für Kinder unter 13 Jahren bestimmt ist. Wählen Sie in diesem Tutorial die Option **No**.
    2. Klicken Sie auf **Submit** (Senden). 

        ![Seite „Mobile Ads“](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Kopieren Sie den Anwendungsschlüssel (**application key**) von der Seite **Mobile Ads**. 

        ![Anwendungsschlüssel](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Wählen Sie oben das Menü **Apps & Services** und dann in der Liste Ihre Anwendung aus. 

    ![Auswählen Ihrer App in der Liste](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. Wechseln Sie zur Registerkarte **Device Messaging**, und führen Sie diese Schritte aus: 
    1. Wählen Sie **Create a New Security Profile**.
    2. Geben Sie unter **Name** einen Namen für Ihr Sicherheitsprofil ein. 
    3. Geben Sie unter **Description** eine Beschreibung für Ihr Sicherheitsprofil ein. 
    4. Wählen Sie **Speichern** aus. 
    5. Wählen Sie auf der Seite mit den Ergebnissen die Option **View Security Profile**. 
5. Führen Sie auf der Seite **Security Profile** die folgenden Schritte aus: 
    1. Wechseln Sie zur Registerkarte **Web Settings**, und kopieren Sie die Werte unter **Client ID** und **Client Secret** zur späteren Verwendung. 

        ![Abrufen der Client-ID und des Geheimnisses](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Wechseln Sie zur Seite **Android/Kindle Settings**, und lassen Sie die Seite geöffnet. Sie geben diese Werte im nächsten Abschnitt ein. 

## <a name="create-an-api-key"></a>Erstellen eines API-Schlüssels
1. Öffnen Sie eine Eingabeaufforderung mit Administratorberechtigungen.
2. Navigieren Sie zum Android SDK-Ordner.
3. Geben Sie den folgenden Befehl ein:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. Geben Sie als **keystore**-Kennwort **android** ein.
5. Kopieren Sie die **MD5**- und **SHA256**-Fingerabdrücke. 
6. Wechseln Sie zurück zum Entwicklerportal, und führen Sie auf der Registerkarte **Android/Kindle Settings** die folgenden Schritte aus: 
    1. Geben Sie unter **Name for the API key** einen Namen für den API-Schlüssel ein. 
    2. Geben Sie unter **Name of the package** den Namen des Pakets für Ihre App (z. B. **com.fabrikam.mykindleapp**) und den Wert für **MD5** ein.
        
        >[!IMPORTANT]
        > Verwenden Sie beim Erstellen einer App in Android Studio den gleichen Paketnamen, den Sie hier angegeben haben. 
    1. Fügen Sie unter **MD5 signature** die zuvor kopierte MD5-Signatur ein. 
    2. Fügen Sie unter **SHA256 signature** die zuvor kopierte SHA256-Signatur ein.  
    3. Wählen Sie **Generate New Key**.

        ![Einstellungen für Android/Kindle: Schlüssel generieren](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. Wählen Sie in der Liste dann **Show**, um den API-Schlüssel anzuzeigen. 

        ![Einstellungen für Android/Kindle: API-Schlüssel anzeigen](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. Kopieren Sie im Fenster **API Key Details** den API-Schlüssel, und speichern Sie ihn an einem sicheren Ort. Wählen Sie anschließend oben rechts **X**, um das Fenster zu schließen. 


## <a name="create-and-configure-a-notification-hub"></a>Erstellen und Konfigurieren eines Notification Hubs

1. Führen Sie die Schritte im Artikel [Erstellen einer Azure Notification Hub-Instanz über das Azure-Portal](create-notification-hub-portal.md) aus, um einen Notification Hub zu erstellen. 
2. Wählen Sie unter **Einstellungen** die Option **Amazon (ADM)** .
3. Fügen Sie die zuvor gespeicherten Angaben für **Client-ID** und **Geheimer Clientschlüssel** ein. 
4. Wählen Sie auf der Symbolleiste **Speichern** aus. 

    ![Konfigurieren von ADM-Einstellungen für einen Notification Hub](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Wählen Sie im Menü auf der linken Seite die Option **Zugriffsrichtlinien** und dann die Schaltfläche **Kopieren** für die Verbindungszeichenfolge für die Richtlinie **DefaultListenSharedAccessSignature**. Speichern Sie die Zeichenfolge. Sie verwenden diese Angabe später im Quellcode. 

    ![Notification Hub: Verbindungszeichenfolge für Lauschen](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Einrichten der Anwendung

1. Starten Sie Android Studio. 
2. Wählen Sie **File** (Datei), zeigen Sie auf **New** (Neu), und wählen Sie dann **New Project** (Neues Projekt). 
3. Wählen Sie im Fenster **Choose your project** (Projekt auswählen) auf der Registerkarte **Phone and Tablet** (Telefon und Tablet) die Option **Empty Activity** (Leere Aktivität) und dann **Next** (Weiter). 
4. Führen Sie im Fenster **Configure your project** (Projekt konfigurieren) die folgenden Schritte aus:
    1. Geben Sie einen **Namen** für Ihre Anwendung ein. Hier können Sie beispielsweise den Namen der Anwendung eingeben, die Sie im Amazon-Entwicklerportal erstellt haben. 
    2. Geben Sie einen **Namen** für das Paket ein. 
        
        >[!IMPORTANT]
        >Der Paketname muss mit dem Paketnamen übereinstimmen, den Sie im Amazon-Entwicklerportal angegeben haben.
    3. Überprüfen und aktualisieren Sie die restlichen Werte wie gewünscht. 
    4. Wählen Sie **Fertig stellen** aus. 

        ![Konfigurieren des Android-Projekts](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Laden Sie die Bibliothek [Amazon Developer SDK for Android](https://developer.amazon.com/sdk-download) auf Ihre Festplatte herunter. Extrahieren Sie die SDK-ZIP-Datei.
6. Ändern Sie in Android Studio die Ordnerstruktur von **Android** in **Project** (Projekt), falls nicht bereits **Project** ausgewählt ist. 

    ![Android Studio: Zur Projektstruktur wechseln](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Erweitern Sie **app**, um den Ordner **libs** in der Strukturansicht anzuzeigen.     
8. Navigieren Sie in einem Datei-Explorer-Fenster zu dem Ordner, in den Sie die Amazon Android SDKs heruntergeladen haben.
9. Drücken Sie **STRG**, und ziehen Sie die Datei **amazon-device-messaging-1.0.1.jar** auf den Knoten **lib** in der Strukturansicht. 

    ![Android Studio: Amazon Device Messaging-JAR-Datei hinzufügen](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. Wählen Sie im Fenster **Copy** (Kopieren) die Option **OK**. Wenn anstelle des Fensters **Copy** (Kopieren) das Fenster **Move** (Verschieben) angezeigt wird, sollten Sie es schließen. Versuchen Sie anschließend, das Ziehen und Ablegen mit gedrückter **STRG**-TASTE durchzuführen. 

    ![Android Studio: JAR kopieren](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Fügen Sie der Datei **build.gradle** der App im Abschnitt **dependencies** die folgende Anweisung hinzu: `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio: ADM zur Datei „build.gradle“ der App hinzufügen](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. Fügen Sie in der Datei `Build.Gradle` der **App** im Abschnitt **dependencies** die folgenden Zeilen hinzu: 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. Fügen Sie **nach** dem Abschnitt **dependencies** das folgende Repository hinzu:

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. Wählen Sie im Editor für die Datei **build.gradle** in der Symbolleiste für die **App** die Option **Sync now** (Jetzt synchronisieren). 

    ![Android Studio: Datei „build.gradle“ der App synchronisieren](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Wechseln Sie in der Strukturansicht zurück zur Android-Struktur.  Fügen Sie den Amazon-Namespace in das Manifest-Stammelement ein:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![Amazon-Namespace im Manifest](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. Fügen Sie Berechtigungen als erstes Element unter dem Manifestelement hinzu. Ersetzen Sie **[YOUR PACKAGE NAME]** durch das Paket, mit dem Sie die App erstellt haben.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Fügen Sie das folgende Element als erstes untergeordnetes Element des Anwendungselements ein. Ersetzen Sie **[YOUR PACKAGE NAME]** durch den Namen des Pakets, mit dem Sie Ihre App erstellt haben. Im nächsten Schritt erstellen Sie die MyADMMessageHandler-Klasse. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Erstellen des ADM-Meldungshandlers

1. Fügen Sie dem Paket `com.fabrikam.mykindleapp` in dem Projekt, das Daten von `com.amazon.device.messaging.ADMMessageHandlerBase` erbt, eine neue Klasse hinzu, und geben Sie ihr den Namen `MyADMMessageHandler`. Dies ist in der folgenden Abbildung dargestellt:

    ![Erstellen der MyADMMessageHandler-Klasse](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Fügen Sie der `MyADMMessageHandler`-Klasse die folgenden `import`-Anweisungen hinzu:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Fügen Sie den folgenden Code zur soeben erstellten Klasse hinzu. Ersetzen Sie `[HUB NAME]` und `[LISTEN CONNECTION STRING]` durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für das Lauschen: 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>Hinzufügen Ihres API-Schlüssels zur App
1. Führen Sie diese Schritte aus, um dem Projekt den Ordner „assets“ hinzuzufügen. 
    1. Wechseln Sie zur Ansicht **Project** (Projekt). 
    2. Klicken Sie mit der rechten Maustaste auf **app**.
    3. Wählen Sie **Neu**aus.
    4. Wählen Sie **Folder** (Ordner). 
    5. Wählen Sie anschließend **Assets Folder** (Ordner „assets“). 

        ![Menü zum Hinzufügen des Ordners „assets“](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. Führen Sie auf der Seite **Configure Component** (Komponente konfigurieren) die folgenden Schritte aus:
        1. Wählen Sie **Change folder location** (Ordnerspeicherort ändern).
        2. Vergewissern Sie sich, dass der Ordner auf `src/main/assets` festgelegt ist.
        3. Wählen Sie **Fertig stellen** aus. 
        
            ![Konfigurieren des Ordners „assets“](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Fügen Sie dem Ordner **assets** eine Datei mit dem Namen **api_key.txt** hinzu. Erweitern Sie in der Strukturansicht nacheinander **app**, **src** und **main**, und klicken Sie mit der rechten Maustaste auf **assets**. Zeigen Sie auf **New** (Neu), und wählen Sie **File** (Datei). Geben Sie **api_key.txt** als Dateinamen ein. 3. 
5. Kopieren Sie den API-Schlüssel, den Sie im Amazon-Entwicklerportal generiert haben, in die Datei „api_key.txt“. 
6. Erstellen Sie das Projekt. 

## <a name="run-the-app"></a>Ausführen der App
1. Wischen Sie auf dem Kindle-Gerät vom oberen Rand nach unten, und klicken Sie auf **Settings** (Einstellungen) und anschließend auf **My account** (Mein Konto). Registrieren Sie sich mit einem gültigen Amazon-Konto.
2. Führen Sie die App über Android Studio auf einem Kindle-Gerät aus. 

> [!NOTE]
> Falls Probleme auftreten, überprüfen Sie die Uhrzeit des Emulators (oder des Geräts). Die Uhrzeit muss stimmen. Um die Uhrzeit des Kindle-Emulators zu ändern, können Sie folgenden Befehl aus dem Tools-Verzeichnis der Android SDK-Plattform ausführen:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Senden einer Benachrichtigungsmeldung

So senden Sie eine Nachricht mithilfe von .NET:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

Beispielcode finden Sie in [diesem Beispiel auf GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Broadcastbenachrichtigungen an alle Kindle-Geräte gesendet, die beim Back-End registriert sind. Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte Kindle-Geräte senden:  In diesem Tutorial wird erläutert, wie Sie Pushbenachrichtigungen an bestimmte Android-Geräte senden. Die gleiche Logik kann aber auch zum Senden von Pushbenachrichtigungen an bestimmte Kindle-Geräte verwendet werden.

> [!div class="nextstepaction"]
>[Senden von Pushbenachrichtigungen an bestimmte Geräte](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
