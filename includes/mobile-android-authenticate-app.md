---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178778"
---
1. Öffnen Sie das Projekt in Android Studio.

2. Öffnen Sie die Datei `ToDoActivity.java` im **Projektexplorer** in Android Studio, und fügen Sie die folgenden Importanweisungen hinzu:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Fügen Sie der **ToDoActivity** -Klasse die folgende Methode hinzu:

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Dieser Code erstellt eine Methode für die Verarbeitung des Google-Authentifizierungsprozesses. Ein Dialogfeld zeigt die ID des authentifizierten Benutzers an. Sie können den Vorgang nur bei erfolgreicher Authentifizierung fortsetzen.

    > [!NOTE]
    > Falls Sie einen anderen Identitätsanbieter als Google verwenden, ändern Sie den an die **login**-Methode übergebenen Wert in einen der folgenden Werte: _MicrosoftAccount_, _Facebook_, _Twitter_ oder _windowsazureactivedirectory_.

4. Fügen Sie in der **onCreate**-Methode die folgende Codezeile im Anschluss an den Code hinzu, der das `MobileServiceClient`-Objekt instanziiert.

    ```java
    authenticate();
    ```

    Dieser Aufruf startet den Authentifizierungsprozess.

5. Verschieben Sie den verbleibenden Code nach `authenticate();` in der **OnCreate**-Methode in eine neue **CreateTable**-Methode.

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Fügen Sie den folgenden Codeausschnitt von `RedirectUrlActivity` der Datei `AndroidManifest.xml` hinzu, um sicherzustellen, dass die Umleitung erwartungsgemäß funktioniert:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Fügen Sie `redirectUriScheme` dem `build.gradle` Ihrer Android-Anwendung hinzu.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Fügen Sie `com.android.support:customtabs:23.0.1` den Abhängigkeiten in Ihrem `build.gradle` hinzu:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. Klicken Sie im Menü **Ausführen** auf **App ausführen**, um die App zu starten, und melden Sie sich mit dem Identitätsanbieter Ihrer Wahl an.

> [!WARNING]
> Bei dem erwähnten URL-Schema muss die Groß-/Kleinschreibung beachtet werden. Stellen Sie sicher, dass alle Vorkommen von `{url_scheme_of_you_app}` die gleiche Groß-/Kleinschreibung verwenden.

Nach erfolgreicher Anmeldung sollte die App ohne Fehler ausgeführt werden, und Sie sollten in der Lage sein, den Back-End-Dienst abzufragen und Daten zu aktualisieren.
