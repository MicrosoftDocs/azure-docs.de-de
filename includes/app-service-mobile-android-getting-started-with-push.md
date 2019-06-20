---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178605"
---
1. Öffnen Sie im **App**-Projekt die Datei `AndroidManifest.xml`. Fügen Sie den folgenden Code nach dem Starttag `application` ein:

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Öffnen Sie die Datei `ToDoActivity.java`, und nehmen Sie die folgenden Änderungen vor:

    - Fügen Sie diese Import-Anweisung hinzu:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Ändern Sie die Definition von `MobileServiceClient` von **private** in **private static**, sodass sich dies ergibt:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Fügen Sie die Methode `registerPush` hinzu:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Aktualisieren Sie die **onCreate**-Methode der Klasse `ToDoActivity`. Achten Sie darauf, nach dem Instanziieren von `MobileServiceClient` diesen Code hinzuzufügen.

        ```java
        registerPush();
        ```

3. Fügen Sie eine neue Klasse zum Behandeln von Benachrichtigungen hinzu. Öffnen Sie im Projektexplorer die Knoten **app** > **java** > **Namespace-Ihres-Projekts**, und klicken Sie mit der rechten Maustaste auf den Paketnamenknoten. Klicken Sie auf **Neu** und anschließend auf **Java Class** (Java-Klasse). Geben Sie unter Name `ToDoMessagingService` ein, und klicken Sie dann auf „OK“. Ersetzen Sie anschließend die Klassendeklaration durch:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Fügen Sie eine weitere Klasse zum Verarbeiten von Tokenaktualisierungen hinzu. Erstellen Sie die Java-Klasse `ToDoInstanceIdService`, und ersetzen Sie die Klassendeklaration durch:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Ihre App kann Pushbenachrichtigungen nun unterstützen.
