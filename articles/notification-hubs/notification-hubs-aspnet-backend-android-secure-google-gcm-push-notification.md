---
title: Senden von sicheren Pushbenachrichtigungen mit Azure Notification Hubs
description: Erfahren Sie mehr über das Senden sicherer Pushbenachrichtigungen an eine Android-App von Azure. Die Codebeispiele wurden in Java und C# geschrieben.
documentationcenter: android
keywords: push notification,push notifications,push messages,android push notifications
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 27536b0a3d7e0858a5660b4c7b33cb6679b5fbf1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60874549"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Senden von sicheren Pushbenachrichtigungen mit Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Übersicht

> [!IMPORTANT]
> Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Durch die Unterstützung von Pushbenachrichtigungen in Microsoft Azure haben Sie Zugriff auf eine benutzerfreundliche, plattformübergreifende und horizontal skalierte Pushnachrichteninfrastruktur, die die Implementierung von Pushbenachrichtigungen sowohl für Endbenutzer- als auch für Unternehmensanwendungen für mobile Plattformen erheblich vereinfacht.

Es kann vorkommen, dass eine Anwendung etwas in die Benachrichtigung einschließen möchte, dass aufgrund gesetzlicher oder sicherheitsbedingter Einschränkungen nicht über die Standardinfrastruktur von Pushbenachrichtigungen übertragen werden kann. In diesem Tutorial wird beschrieben, wie Sie die gleiche Funktionalität erzielen, indem Sie vertrauliche Informationen über eine sichere, authentifizierte Verbindung zwischen dem Android-Clientgerät und dem App-Back-End senden.

Der generelle Ablauf sieht folgendermaßen aus:

1. Das App-Back-End:
   * Speichert die sichere Nutzlast in einer Back-End-Datenbank.
   * Sendet die ID dieser Benachrichtigung an das Android-Gerät (es werden keine sicheren Informationen gesendet).
2. Bei Erhalt der Benachrichtigung reagiert die App auf dem Gerät folgendermaßen:
   * Das Android-Gerät kontaktiert das Back-End und fordert die sichere Nutzlast an.
   * Die App kann die Nutzlast als Benachrichtigung auf dem Gerät anzeigen.

Beachten Sie, dass im obigen Ablauf (und in diesem Tutorial) angenommen wird, dass das Gerät ein Authentifizierungstoken im lokalen Speicher speichert, nachdem sich der Benutzer angemeldet hat. Dieser Ansatz gewährleistet einen nahtlosen Ablauf, da das Gerät mit diesem Token die sichere Nutzlast der Benachrichtigung abrufen kann. Wenn Ihre Anwendung keine Authentifizierungstoken auf dem Gerät speichert oder diese Token ablaufen können, sollte die Geräte-App nach Erhalt der Pushbenachrichtigung eine generische Benachrichtigung anzeigen, in der der Benutzer zum Starten der App aufgefordert wird. Anschließend authentifiziert die App den Benutzer und zeigt die Nutzlast der Benachrichtigung an.

Dieses Tutorial zeigt, wie Sie sichere Pushbenachrichtigungen senden. Es baut auf dem Tutorial [Benachrichtigen von Benutzern](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) auf; daher sollten Sie die Schritte in diesem Tutorial zuerst durchführen, falls Sie dies noch nicht getan haben.

> [!NOTE]
> In diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md)beschrieben erstellt und konfiguriert haben.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Ändern des Android-Projekts

Nachdem Sie Ihr App-Back-End so geändert haben, dass lediglich die *ID* einer Pushbenachrichtigung gesendet wird, müssen Sie Ihre Android-App so ändern, dass sie diese Benachrichtigung verarbeitet und einen Rückruf an das Back-End sendet, um die anzuzeigende sichere Nachricht abzurufen.
Dazu müssen Sie sicherstellen, dass Ihre Android-App weiß, wie sie sich bei Ihrem Back-End authentifizieren muss, wenn sie die Pushbenachrichtigungen erhält.

Ändern Sie nun den Ablauf der *Anmeldung*, um den Wert des Authentifizierungsheaders in den freigegebenen Einstellungen Ihrer App zu speichern. Sie können entsprechende Mechanismen verwenden, um beliebige Authentifizierungstoken (z.B. OAuth-Token) zu speichern, die die App verwenden muss, ohne dass Benutzeranmeldeinformationen eingegeben werden müssen.

1. Fügen Sie in Ihrem Android-App-Projekt am Anfang der Klasse `MainActivity` die folgenden Konstanten hinzu:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Aktualisieren Sie in der Klasse `MainActivity` die Methode `getAuthorizationHeader()` mit folgendem Code:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. Fügen Sie am Anfang der Datei `MainActivity` die folgenden `import`-Anweisungen hinzu:

    ```java
    import android.content.SharedPreferences;
    ```

Ändern Sie nun den Handler, der beim Empfang der Benachrichtigung aufgerufen wird.

1. Ändern Sie in der Klasse `MyHandler` die Methode `OnReceive()` so, dass sie Folgendes enthält:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Fügen Sie dann die `retrieveNotification()`-Methode hinzu, und ersetzen Sie dabei den Platzhalter `{back-end endpoint}` durch den Back-End-Endpunkt, den Sie beim Bereitstellen des Back-Ends erhalten haben:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Diese Methode ruft Ihr Back-End auf, um den Benachrichtigungsinhalt unter Verwendung der in den freigegebenen Einstellungen gespeicherten Anmeldeinformationen abzurufen, und zeigt ihn als normale Benachrichtigung an. Die Benachrichtigung sieht für den App-Benutzer genauso aus wie jede andere Pushbenachrichtigung.

Eine fehlende Authentifizierungsheader-Eigenschaft oder eine Ablehnung sollte nach Möglichkeit über das Back-End behandelt werden. Wie diese Fälle im Einzelnen zu behandeln sind, hängt größtenteils von der Umgebung des Zielbenutzers ab. Eine Möglichkeit besteht darin, eine Benachrichtigung mit einer generischen Eingabeaufforderung anzuzeigen, damit sich der Benutzer zum Abrufen der eigentlichen Benachrichtigung authentifiziert.

## <a name="run-the-application"></a>Ausführen der Anwendung

Gehen Sie zum Ausführen der Anwendung wie folgt vor:

1. Stellen Sie sicher, dass **AppBackend** in Azure bereitgestellt ist. Falls Sie Visual Studio verwenden, führen Sie die Web-API-Anwendung **AppBackend** aus. Daraufhin wird eine ASP.NET-Webseite angezeigt.
2. Unter Eclipse führen Sie die App auf einem physischen Android-Gerät oder im Emulator aus.
3. Geben Sie in der Android-App-UI einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben.
4. Klicken Sie in der Android-App-UI auf **Log in**. Klicken Sie anschließend auf **Send push**.
