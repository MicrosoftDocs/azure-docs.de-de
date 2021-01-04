---
title: Senden von Benachrichtigungen an bestimmte Android-Anwendungen mit Azure Notification Hubs
description: Hier erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an bestimmte Android-Benutzer senden.
documentationcenter: android
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c137cbf0c2290bdbe228f7902e616e7f79cb459e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016980"
---
# <a name="tutorial-send-push-notification-to-specific-android-users-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Tutorial: Senden von Pushbenachrichtigungen an bestimmte Android-Benutzer mit Azure Notification Hubs und Google Cloud Messaging (veraltet)

> [!WARNING]
> Seit dem 10. April 2018 wird Google Cloud Messaging (GCM) von Google nicht mehr unterstützt. Der GCM-Server und die Client-APIs sind veraltet und werden am 29. Mai 2019 entfernt. Weitere Informationen finden Sie unter [GCM and FCM Frequently Asked Questions](https://developers.google.com/cloud-messaging/faq) (Häufig gestellte Fragen zu GCM und FCM).

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Ein ASP.NET WebAPI-Back-End wird verwendet, um Clients zu authentifizieren. Zum Authentifizieren von Clients und Generieren von Benachrichtigungen wird ein ASP.NET WebAPI-Back-End verwendet, wie im Artikel [Registrierungsverwaltung über ein Back-End](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) gezeigt. Dieses Tutorial baut auf dem Benachrichtigungs-Hub auf, den Sie unter [Tutorial: Senden von Pushbenachrichtigungen an Android-Geräte mit Azure Notification Hubs und Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md) erstellt haben.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen des Back-End-Web-API-Projekts, das Benutzer authentifiziert.  
> * Aktualisieren der Android-Anwendung.
> * Testen der App

## <a name="prerequisites"></a>Voraussetzungen

Absolvieren Sie [Tutorial: Senden von Pushbenachrichtigungen an Android-Geräte mit Azure Notification Hubs und Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md), bevor Sie mit diesem Tutorial beginnen.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Erstellen des Android-Projekts

Aktualisieren Sie als Nächstes die Android-Anwendung, die Sie in [Tutorial: Senden von Pushbenachrichtigungen an Android-Geräte mit Azure Notification Hubs und Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md) erstellt haben.

1. Öffnen Sie die Datei `res/layout/activity_main.xml`, und ersetzen Sie die folgenden Inhaltsdefinitionen:

    So werden neue EditText-Steuerelemente für die Anmeldung als Benutzer hinzugefügt. Zudem wird ein Feld für eine Benutzernamenmarkierung hinzugefügt, die in den von Ihnen gesendeten Benachrichtigungen enthalten ist.

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <EditText
        android:id="@+id/usernameText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/usernameHint"
        android:layout_above="@+id/passwordText"
        android:layout_alignParentEnd="true" />
    <EditText
        android:id="@+id/passwordText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/passwordHint"
        android:inputType="textPassword"
        android:layout_above="@+id/buttonLogin"
        android:layout_alignParentEnd="true" />
    <Button
        android:id="@+id/buttonLogin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/loginButton"
        android:onClick="login"
        android:layout_above="@+id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="24dp" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="WNS on"
        android:textOff="WNS off"
        android:id="@+id/toggleButtonWNS"
        android:layout_toLeftOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="GCM on"
        android:textOff="GCM off"
        android:id="@+id/toggleButtonGCM"
        android:checked="true"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="APNS on"
        android:textOff="APNS off"
        android:id="@+id/toggleButtonAPNS"
        android:layout_toRightOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessageTag"
        android:layout_below="@id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_tag_hint" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_below="@+id/editTextNotificationMessageTag"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_hint" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:onClick="sendNotificationButtonOnClick"
        android:layout_below="@+id/editTextNotificationMessage"
        android:layout_centerHorizontal="true" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:id="@+id/text_hello"
    />  
    </RelativeLayout>
    ```
2. Öffnen Sie die Datei `res/values/strings.xml`, und ersetzen Sie die Definition `send_button` durch die folgenden Zeilen, um die Zeichenfolge für `send_button` neu zu definieren und Zeichenfolgen für die anderen Steuerelemente hinzuzufügen:

    ```xml
    <string name="usernameHint">Username</string>
    <string name="passwordHint">Password</string>
    <string name="loginButton">1. Sign in</string>
    <string name="send_button">2. Send Notification</string>
    <string name="notification_message_hint">Notification message</string>
    <string name="notification_message_tag_hint">Recipient username</string>
    ```

    Das grafische Layout von `main_activity.xml` sollte nun wie folgt aussehen:

    ![Screenshot einer App mit Feldern für Benutzername, Kennwort, Empfänger und Nachricht sowie mit Schaltflächen zum Anmelden und Senden von Benachrichtigungen.][A1]
3. Erstellen Sie in dem Paket, in dem sich auch die Klasse `MainActivity` befindet, eine neue Klasse mit dem Namen `RegisterClient`. Verwenden Sie den folgenden Code für die neue Klassendatei.

    ```java
    import java.io.IOException;
    import java.io.UnsupportedEncodingException;
    import java.util.Set;

    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.methods.HttpPut;
    import org.apache.http.client.methods.HttpUriRequest;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONException;
    import org.json.JSONObject;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.util.Log;

    public class RegisterClient {
        private static final String PREFS_NAME = "ANHSettings";
        private static final String REGID_SETTING_NAME = "ANHRegistrationId";
        private String Backend_Endpoint;
        SharedPreferences settings;
        protected HttpClient httpClient;
        private String authorizationHeader;

        public RegisterClient(Context context, String backendEndpoint) {
            super();
            this.settings = context.getSharedPreferences(PREFS_NAME, 0);
            httpClient =  new DefaultHttpClient();
            Backend_Endpoint = backendEndpoint + "/api/register";
        }

        public String getAuthorizationHeader() {
            return authorizationHeader;
        }

        public void setAuthorizationHeader(String authorizationHeader) {
            this.authorizationHeader = authorizationHeader;
        }

        public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
            String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

            JSONObject deviceInfo = new JSONObject();
            deviceInfo.put("Platform", "gcm");
            deviceInfo.put("Handle", handle);
            deviceInfo.put("Tags", new JSONArray(tags));

            int statusCode = upsertRegistration(registrationId, deviceInfo);

            if (statusCode == HttpStatus.SC_OK) {
                return;
            } else if (statusCode == HttpStatus.SC_GONE){
                settings.edit().remove(REGID_SETTING_NAME).commit();
                registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                statusCode = upsertRegistration(registrationId, deviceInfo);
                if (statusCode != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            } else {
                Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                throw new RuntimeException("Error upserting registration");
            }
        }

        private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                throws UnsupportedEncodingException, IOException,
                ClientProtocolException {
            HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
            request.setEntity(new StringEntity(deviceInfo.toString()));
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            request.addHeader("Content-Type", "application/json");
            HttpResponse response = httpClient.execute(request);
            int statusCode = response.getStatusLine().getStatusCode();
            return statusCode;
        }

        private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
            if (settings.contains(REGID_SETTING_NAME))
                return settings.getString(REGID_SETTING_NAME, null);

            HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            HttpResponse response = httpClient.execute(request);
            if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                throw new RuntimeException("Error creating Notification Hubs registrationId");
            }
            String registrationId = EntityUtils.toString(response.getEntity());
            registrationId = registrationId.substring(1, registrationId.length()-1);

            settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

            return registrationId;
        }
    }
    ```

    Diese Komponente implementiert die REST-Aufrufe, die für das Kontaktieren des App-Back-Ends erforderlich sind, um sich für Pushbenachrichtigungen zu registrieren. Außerdem werden die vom Notification Hub erstellten *registrationIds* lokal gespeichert, wie unter [Registrierung vom App-Back-End aus](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)beschrieben. Wenn Sie die Schaltfläche **Anmelden** verwenden, wird ein Authentifizierungstoken aus dem lokalen Speicher verwendet.
4. Entfernen Sie in Ihrer Klasse Ihr privates Feld für `NotificationHub`, oder kommentieren Sie es aus, und fügen Sie ein Feld für die `RegisterClient`-Klasse und eine Zeichenfolge für den Endpunkt Ihres ASP.NET-Back-Ends hinzu. Ersetzen Sie `<Enter Your Backend Endpoint>` unbedingt durch den tatsächlichen Back-End-Endpunkt, den Sie zuvor abgerufen haben. Beispiel: `http://mybackend.azurewebsites.net`.

    ```java
    //private NotificationHub hub;
    private RegisterClient registerClient;
    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

5. Entfernen Sie in der `MainActivity`-Klasse in der `onCreate`-Methode die Initialisierung des `hub`-Felds, oder kommentieren Sie sie aus, und rufen Sie die `registerWithNotificationHubs`-Methode auf. Fügen Sie dann Code zum Initialisieren einer Instanz der `RegisterClient` -Klasse hinzu. Die Methode sollte die folgenden Zeilen enthalten:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);

        //hub = new NotificationHub(HubName, HubListenConnectionString, this);
        //registerWithNotificationHubs();

        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

        setContentView(R.layout.activity_main);
    }
    ```
6. Löschen Sie in der `MainActivity`-Klasse die gesamte `registerWithNotificationHubs`-Methode, oder kommentieren Sie sie aus. Sie wird in diesem Lernprogramm nicht verwendet.
7. Fügen Sie der Datei `MainActivity.java` die folgenden `import`-Anweisungen hinzu:

    ```java
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;

    import android.widget.Button;
    import android.widget.ToggleButton;
    import java.io.UnsupportedEncodingException;
    import android.content.Context;
    import java.util.HashSet;
    import android.widget.Toast;
    import org.apache.http.client.ClientProtocolException;
    import java.io.IOException;
    import org.apache.http.HttpStatus;

    import android.os.AsyncTask;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;

    import android.app.AlertDialog;
    import android.content.DialogInterface;
    ```
8. Ersetzen Sie den Code in der onStart-Methode durch den folgenden Code:

    ```java
    super.onStart();
    Button sendPush = (Button) findViewById(R.id.sendbutton);
    sendPush.setEnabled(false);
    ```
9. Fügen Sie dann die folgenden Methoden zum Verarbeiten des Click-Ereignisses der Schaltfläche **Anmelden** und zum Senden von Pushbenachrichtigungen hinzu.

    ```java
    public void login(View view) throws UnsupportedEncodingException {
        this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

        final Context context = this;
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    String regid = gcm.register(NotificationSettings.SenderId);
                    registerClient.register(regid, new HashSet<String>());
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to register", e.getMessage());
                    return e;
                }
                return null;
            }

            protected void onPostExecute(Object result) {
                Button sendPush = (Button) findViewById(R.id.sendbutton);
                sendPush.setEnabled(true);
                Toast.makeText(context, "Logged in and registered.",
                        Toast.LENGTH_LONG).show();
            }
        }.execute(null, null, null);
    }

    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
        return basicAuthHeader;
    }

    /**
        * This method calls the ASP.NET WebAPI backend to send the notification message
        * to the platform notification service based on the pns parameter.
        *
        * @param pns     The platform notification service to send the notification message to. Must
        *                be one of the following ("wns", "gcm", "apns").
        * @param userTag The tag for the user who will receive the notification message. This string
        *                must not contain spaces or special characters.
        * @param message The notification message string. This string must include the double quotes
        *                to be used as JSON content.
        */
    public void sendPush(final String pns, final String userTag, final String message)
            throws ClientProtocolException, IOException {
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    String uri = BACKEND_ENDPOINT + "/api/notifications";
                    uri += "?pns=" + pns;
                    uri += "&to_tag=" + userTag;

                    HttpPost request = new HttpPost(uri);
                    request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                    request.setEntity(new StringEntity(message));
                    request.addHeader("Content-Type", "application/json");

                    HttpResponse response = new DefaultHttpClient().execute(request);

                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        DialogNotify("MainActivity - Error sending " + pns + " notification",
                            response.getStatusLine().toString());
                        throw new RuntimeException("Error sending notification");
                    }
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                    return e;
                }

                return null;
            }
        }.execute(null, null, null);
    }
    ```

    Der `login`-Handler für die Schaltfläche **Anmelden** generiert unter Verwendung des eingegebenen Benutzernamens und Kennworts ein Standardauthentifizierungstoken (dies kann ein beliebiges in Ihrem Authentifizierungsschema verwendetes Token sein) und verwendet dann `RegisterClient` zum Aufrufen des Back-Ends für die Registrierung.

    Die `sendPush` -Methode ruft das Back-End auf, um basierend auf der Benutzermarkierung eine sichere Benachrichtigung auszulösen. Der Plattformbenachrichtigungsdienst, auf den `sendPush` verweist, hängt von der übergebenen `pns`-Zeichenfolge ab.

10. Fügen Sie der `MainActivity`-Klasse die folgende `DialogNotify`-Methode hinzu.

    ```java
    protected void DialogNotify(String title, String message)
    {
        AlertDialog alertDialog = new AlertDialog.Builder(MainActivity.this).create();
        alertDialog.setTitle(title);
        alertDialog.setMessage(message);
        alertDialog.setButton(AlertDialog.BUTTON_NEUTRAL, "OK",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog, int which) {
                        dialog.dismiss();
                    }
                });
        alertDialog.show();
    }
    ```
11. Aktualisieren Sie in der `MainActivity`-Klasse die `sendNotificationButtonOnClick`-Methode so, dass sie wie folgt die `sendPush`-Methode mit den ausgewählten Plattformbenachrichtigungsdiensten des Benutzers aufruft.

    ```java
    /**
    * Send Notification button click handler. This method sends the push notification
    * message to each platform selected.
    *
    * @param v The view
    */
    public void sendNotificationButtonOnClick(View v)
            throws ClientProtocolException, IOException {

        String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                .getText().toString();
        String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                .getText().toString();

        // JSON String
        nhMessage = "\"" + nhMessage + "\"";

        if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
        {
            sendPush("wns", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
        {
            sendPush("gcm", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
        {
            sendPush("apns", nhMessageTag, nhMessage);
        }
    }
    ```
12. Fügen Sie dem Abschnitt `android` in der Datei `build.gradle` nach dem Abschnitt `buildTypes` die folgende Zeile hinzu:

    ```java
    useLibrary 'org.apache.http.legacy'
    ```
13. Erstellen Sie das Projekt.

## <a name="test-the-app"></a>Testen der App

1. Führen Sie die Anwendung unter Verwendung von Android Studio auf einem Gerät oder einen Emulator aus.
2. Geben Sie in der Android-App einen Benutzernamen und ein Kennwort ein. Beide müssen den gleichen Zeichenfolgenwert aufweisen und dürfen keine Leerzeichen oder Sonderzeichen enthalten.
3. Tippen Sie in der Android-App auf **Anmelden**. Warten Sie, bis die Popupmeldung **Logged in and registered** angezeigt wird. Dadurch wird die Schaltfläche **Send Notification (Benachrichtigung senden)** aktiviert.

    ![Screenshot einer App. Eine Popupmeldung, die bestätigt, dass der Benutzer angemeldet und registriert ist, ist sichtbar, und die Schaltfläche „Benachrichtigung senden“ ist aktiviert.][A2]
4. Klicken Sie auf die Umschaltflächen, um alle Plattformen zu aktivieren, auf denen Sie die App ausgeführt und einen Benutzer registriert haben.
5. Geben Sie den Namen des Benutzers ein, der die Benachrichtigungsmeldung erhält. Dieser Benutzer muss für Benachrichtigungen auf dem Zielgerät registriert werden.
6. Geben Sie eine Nachricht für den Benutzer ein, die als Pushbenachrichtigungsmeldung empfangen wird.
7. Tippen Sie auf **Send Notification**.  Die Pushbenachrichtigung wird auf jedem Gerät empfangen, das für die übereinstimmende Benutzernamensmarkierung registriert ist.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Pushbenachrichtigungen an bestimmte Benutzer senden, deren Registrierungen Tags zugeordnet sind. Um zu erfahren, wie Sie standortbasierte Pushbenachrichtigungen senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden standortbasierter Pushbenachrichtigungen](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png
