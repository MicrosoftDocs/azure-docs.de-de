---
title: 'Erste Schritte mit Android: Microsoft Identity Platform | Azure'
description: So kann eine Android-App ein Zugriffstoken von Microsoft Identity Platform abrufen und die Microsoft Graph-API oder APIs aufrufen, die Zugriffstoken erfordern
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6112facfc0c10d7a0a0495cd778fa6c3cb6130a7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962153"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Anmelden von Benutzern und Aufrufen von Microsoft Graph aus einer Android-App

In diesem Tutorial wird die Integration einer Android-Anwendung in Microsoft Identity Platform beschrieben. Mit Ihrer App wird ein Benutzer angemeldet, ein Zugriffstoken zum Aufrufen der Microsoft Graph-API abgerufen und eine Anforderung an die Microsoft Graph-API gesendet.  

Am Ende dieses Leitfadens akzeptiert Ihre Anwendung Anmeldungen von persönlichen Microsoft-Konten (z.B. outlook.com, live.com u.a.) sowie von Geschäfts,- Schul- oder Unikonten von allen Unternehmen oder Organisationen, die Azure Active Directory nutzen.

## <a name="how-this-tutorial-works"></a>So funktioniert dieses Tutorial

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Mit der App in diesem Beispiel werden Benutzer angemeldet und Daten in deren Namen abgerufen.  Der Zugriff auf diese Daten erfolgt über eine geschützte API (in diesem Fall Microsoft Graph-API), die eine Autorisierung erfordert.

Dies gilt insbesondere in folgenden Fällen:

* Die App meldet den Benutzer entweder über einen Browser oder über Microsoft Authenticator und das Intune-Unternehmensportal an.
* Der Endbenutzer akzeptiert die von Ihrer Anwendung angeforderten Berechtigungen. 
* Ihre App stellt ein Zugriffstoken für die Microsoft Graph-API aus.
* Das Zugriffstoken ist in der HTTP-Anforderung an die Web-API enthalten.
* Die Microsoft Graph-Antwort wird verarbeitet.

Dieses Beispiel verwendet Microsoft Authentication Library (MSAL) für Android zum Implementieren der Authentifizierung. Mit MSAL werden Token automatisch verlängert, das einmalige Anmelden zwischen anderen Apps auf dem Gerät wird bereitgestellt, und die Konten werden verwaltet.

## <a name="prerequisites"></a>Voraussetzungen

* In diesem angeleiteten Setup wird Android Studio verwendet.
* Android 16 oder höher ist erforderlich (19+ empfohlen).

## <a name="library"></a>Bibliothek

In dieser Anleitung wird die folgende Authentifizierungsbibliothek verwendet:

|Bibliothek|BESCHREIBUNG|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="set-up-your-project"></a>Einrichten des Projekts

In diesem Tutorial wird ein neues Projekt erstellt. Wenn Sie stattdessen das abgeschlossene Tutorial herunterladen möchten, [laden Sie den Code](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) herunter.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

1. Öffnen Sie Android Studio, und wählen Sie **Start a new Android Studio project** (Neues Android Studio-Projekt starten) aus.
    - Wenn Android Studio bereits geöffnet ist, wählen Sie **File** > **New** > **New Project** (Datei > Neu > Neues Projekt).
2. Lassen Sie **Empty Activity** (Leere Aktivität) unverändert, und wählen Sie **Next** (Weiter).
3. Geben Sie Ihrer Anwendung einen Namen, legen Sie `Minimum API level` auf **API 19 or newer** (API 19 oder höher) fest, klicken Sie auf **Finish** (Fertig stellen).
5. Legen Sie In Ihrer `app/build.gradle`-Datei die `targetedSdkVersion`-Version auf 27 fest. 

## <a name="register-your-application"></a>Anwendung registrieren

Es gibt zwei Methoden für die Registrierung Ihrer Anwendung. Diese werden in den beiden folgenden Abschnitten beschrieben.

### <a name="register-your-app"></a>Registrieren der App

1. Rufen Sie das [Azure-Portal](https://aka.ms/MobileAppReg) auf, und wählen Sie `New registration` aus. 
2. Geben Sie einen **Namen** für Ihre App ein, und wählen Sie `Register`. **Legen Sie zu diesem Zeitpunkt keinen Umleitungs-URI fest**. 
3. Wechseln Sie im Abschnitt `Manage` zu `Authentication` > `Add a platform` > `Android`.
    - Geben Sie den Paketnamen Ihres Projekts ein. Wenn Sie den Code heruntergeladen haben, lautet dieser `com.azuresamples.msalandroidapp`. 
    - Geben Sie Ihren Signaturhash für Debuggen/Entwicklung ein. Verwenden Sie den KeyTool-Befehl im Portal, um den Signaturhashwert zu generieren. 
4. Drücken Sie `Configure`, und speichern Sie die ***MSAL Konfiguration*** zur späteren Verwendung. 

## <a name="build-your-app"></a>Erstellen der App

### <a name="configure-your-android-app"></a>Konfigurieren der Android-App

1. Klicken Sie mit der rechten Maustaste auf **res** > **New** > **Folder** > **Raw Resources Folder** (res > Neu > Ordner > Ordner „Unformatierte Ressourcen“).
2. Erstellen Sie in **app** > **res** > **raw** eine neue JSON-Datei namens `auth_config.json`, und fügen Sie Ihre ***MSAL-Konfiguration*** ein. Weitere Informationen finden Sie unter [MSAL-Konfiguration](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
3. Fügen Sie in **app** > **manifests** > **AndroidManifest.xml** die folgende `BrowserTabActivity`-Aktivität hinzu. Dieser Eintrag ermöglicht Microsoft den Rückruf an Ihre Anwendung nach Abschluss der Authentifizierung:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Hinweis: Der verwendete Signaturhash darf in **AndroidManifest.xml** NICHT URL-codiert sein. 

4. Fügen Sie in der Datei **AndroidManifest.xml** direkt über dem Tag `<application>` die folgenden Berechtigungen hinzu:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. Ersetzen Sie in `BrowserTabActivity` den ***Paketnamen*** und ***Signaturhash*** durch die im Azure-Portal registrierten Werten.

### <a name="create-the-apps-ui"></a>Erstellen der App-Benutzeroberfläche

1. Navigieren Sie zu **res** > **Layout**, und öffnen Sie die Datei **activity_main.xml**.
2. Ändern Sie das Layout der Aktivität von `android.support.constraint.ConstraintLayout` o.ä. in `LinearLayout`.
3. Fügen Sie die Eigenschaft `android:orientation="vertical"` zum Knoten `LinearLayout` hinzu.
4. Fügen Sie den folgenden Code in den Knoten `LinearLayout` ein, wobei Sie den aktuellen Inhalt ersetzen:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Hinzufügen von MSAL zu Ihrem Projekt

1. Wählen Sie in Android Studio **Gradle Scripts** > **build.gradle (Module: app)** (Gradle-Skripts > build.gradle (Module: app)).
2. Fügen Sie unter **Dependencies** (Abhängigkeiten) den folgenden Code ein:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Verwendung von MSAL 

In den nächsten Abschnitten werden Änderungen innerhalb der Datei `MainAcitivty.java` vorgenommen. Wir werden jeden Schritt durchlaufen, der für das Hinzufügen und Verwenden von MSAL in Ihrer App erforderlich ist.

#### <a name="required-imports"></a>Erforderliche Importe

Fügen Sie Ihrem Projekt die folgenden Importe hinzu: 

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiating-msal"></a>Instanziieren von MSAL 

Innerhalb der Klasse `MainActivity` müssen wir MSAL zusammen mit einigen Konfigurationen über die Aufgaben der App instanziieren, einschließlich der Bereiche und der Web-API, auf die wir zugreifen möchten. 

Kopieren Sie die folgenden Variablen in die Datei `MainActivity`:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Um MSAL zu instanziieren, kopieren Sie jetzt den folgenden Code in die `onCreate(...)`-Methode:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

Der obige Codeblock versucht, Benutzer, die Ihre Anwendung öffnen, im Hintergrund über `getAccounts(...)` und im Erfolgsfall über `acquireTokenSilentAsync(...)` anzumelden.  In den nächsten Abschnitten implementieren wir den Rückrufhandler für den Fall, dass keine angemeldeten Konten vorhanden sind. 

#### <a name="use-msal-to-get-tokens"></a>Verwenden von MSAL zum Abrufen von Token

Jetzt können wir die Verarbeitungslogik der Benutzeroberfläche der App implementieren und Token interaktiv über MSAL beziehen. 

MSAL macht zwei primäre Methoden zum Abrufen von Token verfügbar: `acquireTokenSilentAsync` und `acquireToken`.  

`acquireTokenSilentAsync` meldet einen Benutzer an und ruft Token ohne Benutzerinteraktion ab, wenn ein Konto vorhanden ist. Wenn dies gelingt, MSAL die Token an Ihre App. Gelingt es nicht, wird ein `MsalUiRequiredException`-Element generiert.  Wenn diese Ausnahme generiert wird oder Sie möchten, dass sich Ihr Benutzer interaktiv anmeldet (Anmeldeinformationen, MFA oder andere Richtlinien für bedingten Zugriff können erforderlich sein oder nicht), dann können Sie `acquireToken` verwenden.  

`acquireToken` zeigt die Benutzeroberfläche immer an, wenn versucht wird, den Benutzer anzumelden und Token abzurufen. Möglicherweise werden jedoch Sitzungscookies im Browser oder in einem Konto in Microsoft Authenticator verwendet, um eine interaktive SSO-Umgebung bereitzustellen. 

Erstellen Sie zunächst die folgenden drei Benutzeroberflächenmethoden in der `MainActivity`-Klasse:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Als nächstes fügen Sie eine Methode hinzu, um die aktuelle Aktivität abzurufen und unbeaufsichtigte und interaktive Rückrufe zu verarbeiten:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Verwenden von MSAL zur Abmeldung

Als nächstes fügen wir unserer App Unterstützung für die Abmeldung hinzu. 

Es ist wichtig zu beachten, dass die Abmeldung mit MSAL alle bekannten Informationen über einen Benutzer aus dieser Anwendung entfernt. Der Benutzer verfügt dennoch über eine aktive Sitzung auf seinem Gerät. Wenn der Benutzer versucht, sich erneut anzumelden, werden möglicherweise Interaktionen angezeigt, er muss jedoch nicht unbedingt seine Anmeldeinformationen erneut eingeben, da die Gerätesitzung aktiv ist. 

Zum Hinzufügen der Abmeldefunktion kopieren Sie die folgende Methode in Ihre App, die alle Konten durchläuft und entfernt:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Aufrufen der Microsoft Graph-API

Sobald wir ein Token erfolgreich erhalten haben, können wir eine Anforderung an die Microsoft Graph-API stellen. Das Zugriffstoken befindet sich innerhalb von `AuthenticationResult` innerhalb der `onSuccess(...)`-Methode des Authentifizierungsrückrufs. Zum Erstellen einer autorisierten Anforderung muss Ihre App das Zugriffstoken dem HTTP-Header hinzufügen:

| Headerschlüssel    | value                 |
| ------------- | --------------------- |
| Autorisierung | Bearer <access-token> |

Um dies anhand von Code durchzuführen, fügen Sie Ihrer App die folgenden beiden Methoden hinzu, um Graph aufzurufen und die Benutzeroberfläche zu aktualisieren: 

```java
    /* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

Weitere Informationen zur [Microsoft Graph-API](https://graph.microsoft.com)

#### <a name="multi-account-applications"></a>Anwendungen mit mehreren Konten

Diese App ist für ein Szenario mit einem einzelnen Konto konzipiert. MSAL unterstützt auch Szenarien mit mehreren Konten, die Apps müssen dann jedoch einige zusätzliche Schritte ausführen. Sie müssen eine Benutzeroberfläche erstellen, um dem Benutzer bei der Auswahl des Kontos zu helfen, das er für die einzelnen Aktionen verwenden möchte, die Token erfordern. Als Alternative kann Ihre Anwendung eine Heuristik implementieren, um das zu verwendende Konto über die `getAccounts(...)`-Methode auszuwählen. 

## <a name="test-your-app"></a>Testen Ihrer App

### <a name="run-locally"></a>Lokales Ausführen

Wenn Sie den obigen Code verwendet haben, versuchen Sie, die App zu erstellen und auf einem Testgerät oder Emulator bereitzustellen. Sie sollten sich anmelden und Token für Azure AD oder persönliche Microsoft-Konten abrufen können! Nach der Anmeldung eines Benutzers zeigt diese App die vom Microsoft Graph `/me`-Endpunkt zurückgegebenen Daten an. 

Wenn irgendwelche Probleme auftreten, melden Sie gerne ein Problem mit diesem Dokument oder der MSAL-Bibliothek, und geben Sie uns Bescheid. 

### <a name="consent-to-your-app"></a>Zustimmung zur App

Wenn sich ein Benutzer zum ersten Mal bei Ihrer App anmeldet, wird er von Microsoft aufgefordert, den angeforderten Berechtigungen zuzustimmen.  Die meisten Benutzer können zustimmen, allerdings haben einige Azure AD-Mandanten die Zustimmung durch Benutzer deaktiviert, sodass Administratoren im Namen aller Benutzer zustimmen müssen.  Zur Unterstützung dieses Szenarios müssen Sie die Bereiche Ihrer App im Azure-Portal registrieren.

## <a name="help-and-support"></a>Hilfe und Support

Hatten Sie Probleme mit diesem Tutorial oder mit Microsoft I Platform? Siehe [Hilfe und Support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
