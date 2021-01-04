---
title: 'Tutorial: Erstellen einer Android-App, die Microsoft Identity Platform für die Authentifizierung verwendet | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine Android-App, die Microsoft Identity Platform zum Anmelden von Benutzern und zum Abrufen eines Zugriffstokens verwendet, um im Namen der Benutzer die Microsoft Graph-API aufzurufen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 08ee000d8f801559fcf572b8ab489161fd090b77
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996201"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer Android-Anwendung

In diesem Tutorial erstellen Sie eine Android-App, die in die Microsoft Identity Platform eingebunden wird, um Benutzer anzumelden und ein Zugriffstoken abzurufen, mit dem die Microsoft Graph-API aufgerufen werden kann.

Am Ende dieses Tutorials akzeptiert Ihre Anwendung Anmeldungen von persönlichen Microsoft-Konten (outlook.com, live.com u. a.) sowie von Geschäfts,- Schul- oder Unikonten von allen Unternehmen oder Organisationen, die Azure Active Directory nutzen.

Dieses Tutorial umfasst folgende Punkte: 

> [!div class="checklist"]
> * Erstellen eines Android-App-Projekts in *Android Studio*
> * Registrieren der App im Azure-Portal
> * Hinzufügen von Code zur Unterstützung der Benutzeranmeldung und -abmeldung
> * Hinzufügen von Code zum Aufrufen der Microsoft Graph-API
> * Testen der App

## <a name="prerequisites"></a>Voraussetzungen

* Android Studio 3.5 oder höher

## <a name="how-this-tutorial-works"></a>So funktioniert dieses Tutorial

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Mit der App in diesem Tutorial werden Benutzer angemeldet und Daten in ihrem Namen abgerufen. Auf diese Daten wird über eine geschützte API (Microsoft Graph-API) zugegriffen, für die eine Autorisierung erforderlich ist und die über Microsoft Identity Platform geschützt ist.

Dies gilt insbesondere in folgenden Fällen:

* Die App meldet den Benutzer entweder über einen Browser oder über Microsoft Authenticator und das Intune-Unternehmensportal an.
* Der Endbenutzer akzeptiert die von Ihrer Anwendung angeforderten Berechtigungen.
* Ihre App stellt ein Zugriffstoken für die Microsoft Graph-API aus.
* Das Zugriffstoken ist in der HTTP-Anforderung an die Web-API enthalten.
* Die Microsoft Graph-Antwort wird verarbeitet.

Dieses Beispiel verwendet die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Android zum Implementieren der Authentifizierung: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

MSAL erneuert Token automatisch, ermöglicht das einmalige Anmelden zwischen anderen Apps auf dem Gerät und verwaltet die Konten.

> [!NOTE]
> Dieses Tutorial enthält vereinfachte Beispiele für die Verwendung von MSAL für Android. Der Einfachheit halber wird nur der Einzelkontomodus verwendet. Komplexere Szenarios finden Sie in einem vollständigen, [funktionierenden Codebeispiel](https://github.com/Azure-Samples/ms-identity-android-java/) auf GitHub.

## <a name="create-a-project"></a>Erstellen eines Projekts
Falls Sie noch nicht über eine Android-Anwendung verfügen, gehen Sie wie folgt vor, um ein neues Projekt einzurichten.

1. Öffnen Sie Android Studio, und wählen Sie **Start a new Android Studio project** (Neues Android Studio-Projekt starten) aus.
2. Wählen Sie **Basic Activity** (Standardaktivität) aus, und klicken Sie dann auf **Next** (Weiter).
3. Benennen Sie Ihre Anwendung.
4. Speichern Sie den Paketnamen. Sie geben diesen später im Azure-Portal ein.
5. Ändern Sie die Sprache von **Kotlin** in **Java**.
6. Legen Sie **Minimum API level** (Mindestebene für API) auf **API 19** oder höher fest, und klicken Sie auf **Finish**.
7. Wählen Sie in der Projektansicht in der Dropdownliste die Option **Project** aus, um die Projektdateien mit und ohne Quelle anzuzeigen, öffnen Sie **app/build.gradle**, und legen Sie `targetSdkVersion` auf `28` fest.

## <a name="integrate-with-microsoft-authentication-library"></a>Integrieren mit Microsoft Authentication Library

### <a name="register-your-application"></a>Anwendung registrieren

1. Öffnen Sie das [Azure-Portal](https://aka.ms/MobileAppReg).
2. Öffnen Sie das Blatt [App-Registrierungen](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), und klicken Sie auf **+ Neue Registrierung**.
3. Geben Sie unter **Name** einen Namen für Ihre App ein, und klicken Sie auf **Registrieren**, **ohne** einen Umleitungs-URI festzulegen.
4. Wählen Sie im Abschnitt **Verwalten** des angezeigten Bereichs die Option **Authentifizierung** > **+ Plattform hinzu** > **Android** aus. (Möglicherweise müssen Sie oben auf dem Blatt „Switch to the new experience“ (Zur neuen Benutzeroberfläche wechseln) auswählen, um diesen Abschnitt anzeigen zu können.)
5. Geben Sie den Paketnamen Ihres Projekts ein. Wenn Sie den Code heruntergeladen haben, lautet dieser `com.azuresamples.msalandroidapp`.
6. Klicken Sie im Abschnitt **Signaturhash** der Seite **Android-App konfigurieren** auf **Generieren eines Signaturhashs für die Entwicklung**, und kopieren Sie den KeyTool-Befehl, um ihn auf Ihrer Plattform zu verwenden.

   > [!Note]
   > „KeyTool. exe“ wird als Teil des Java Development Kit (JDK) installiert. Sie müssen auch das OpenSSL-Tool installieren, um den KeyTool-Befehl auszuführen. Weitere Informationen finden Sie in der [Android-Dokumentation für die Schlüsselerstellung](https://developer.android.com/studio/publish/app-signing#generate-key).

7. Geben Sie den von KeyTool generierten **Signaturhash** ein.
8. Klicken Sie auf `Configure`, und speichern Sie die **MSAL-Konfiguration**, die auf der Seite **Android-Konfiguration** angezeigt wird, damit Sie diese später beim Konfigurieren Ihrer App eingeben können.  Klicken Sie auf **Fertig**.

### <a name="configure-your-application"></a>Konfigurieren der Anwendung

1. Navigieren Sie im Projektbereich von Android Studio zu **app\src\main\res**.
2. Klicken Sie mit der rechten Maustaste auf **res**, und wählen Sie **New** > **Directory** („Neu“ > „Verzeichnis“) aus. Geben Sie `raw` als neuen Verzeichnisnamen ein, und klicken Sie auf **OK**.
3. Erstellen Sie unter **app** > **src** > **main** > **res** > **raw** eine neue JSON-Datei namens `auth_config_single_account.json`, und fügen Sie die MSAL-Konfiguration ein, die Sie zuvor gespeichert haben.

    Fügen Sie unterhalb des Umleitungs-URIs Folgendes ein:
    ```json
      "account_mode" : "SINGLE",
    ```
    Ihre Konfigurationsdatei sollte in etwa wie folgt aussehen:
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```

   >[!NOTE]
   >In diesem Tutorial wird nur gezeigt, wie Sie eine App im Einzelkontomodus konfigurieren. Weitere Informationen zum [Modus für einzelne und mehrere Konten](./single-multi-account.md) sowie zum [Konfigurieren Ihrer App](./msal-configuration.md) finden Sie in der Dokumentation.

4. Fügen Sie in **app** > **src** > **main** > **AndroidManifest.xml** die folgende `BrowserTabActivity`-Aktivität zum Hauptteil der Anwendung hinzu. Dieser Eintrag ermöglicht Microsoft den Rückruf an Ihre Anwendung nach Abschluss der Authentifizierung:

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

    Ersetzen Sie den Wert `android:host=` durch den Paketnamen, den Sie im Azure-Portal registriert haben.
    Ersetzen Sie den Wert `android:path=` durch den Schlüsselhash, den Sie im Azure-Portal registriert haben. Der Signaturhash darf **nicht** als URL codiert sein. Der Signaturhash muss mit einem führenden Schrägstrich (`/`) beginnen.
    >[!NOTE]
    >Der Paketname, durch den Sie den Wert `android:host` ersetzen, muss in etwa wie folgt aussehen: „com.azuresamples.msalandroidapp“. Der Signaturhash, durch den Sie den Wert `android:path` ersetzen, muss in etwa wie folgt aussehen: „/1wIqXSqBj7w+h11ZifsnqwgyKrY=“. Diese Werte finden Sie auch auf dem Authentifizierungsblatt Ihrer App-Registrierung. Ihr Umleitungs-URI sieht in etwa wie folgt aus: „msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D“. Am Ende dieses Werts ist der Signaturhash zwar als URL codiert, im Wert `android:path` darf der Signaturhash allerdings **nicht** als URL codiert sein.

## <a name="use-msal"></a>Verwendung von MSAL

### <a name="add-msal-to-your-project"></a>Hinzufügen von MSAL zu Ihrem Projekt

1. Navigieren Sie im Android Studio-Projektfenster zu **app** > **src** > **build.gradle**, und fügen Sie Folgendes hinzu:

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [Weitere Informationen zum Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Erforderliche Importe

Fügen Sie am Anfang von **app** > **src** > **main**> **java** > **com.example(yourapp)**  > **MainActivity.java** Folgendes hinzu:

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Instanziieren von „PublicClientApplication“
#### <a name="initialize-variables"></a>Initialisieren von Variablen
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
Verweisen Sie in der Klasse `MainActivity` auf die folgende onCreate()-Methode, um MSAL unter Verwendung von `SingleAccountPublicClientApplication` zu instanziieren.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Lauschen Sie auf Schaltflächen, und rufen Sie Methoden auf bzw. protokollieren Sie ggf. Fehler.
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Die Abmeldung mit MSAL entfernt alle bekannten Informationen über einen Benutzer aus der Anwendung. Der Benutzer verfügt aber dennoch über eine aktive Sitzung auf seinem Gerät. Wenn der Benutzer versucht, sich erneut anzumelden, wird möglicherweise eine Benutzeroberfläche zum Anmelden angezeigt, die Anmeldeinformationen müssen jedoch möglicherweise nicht erneut eingegeben werden, da die Gerätesitzung noch aktiv ist.

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Rückruf für interaktive Anforderungen:

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Rückruf für automatische Anforderungen:
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Aufrufen der Microsoft Graph-API

Der folgende Code zeigt das Aufrufen der Graph-API unter Verwendung des Graph SDK:

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Hinzufügen der UI
### <a name="activity"></a>Aktivität
Wenn Sie Ihre Benutzeroberfläche anhand dieses Tutorials modellieren möchten, können Sie die folgenden Methoden als Leitfaden für die Aktualisierung von Text sowie für das Lauschen auf Schaltflächen verwenden.

#### <a name="updateui"></a>updateUI
Aktivieren/Deaktivieren von Schaltflächen auf der Grundlage des Anmeldezustands und Festlegen von Text:
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Methode zum Aktualisieren von Text auf der Benutzeroberfläche, um die Abmeldung anzugeben:

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout

Beispieldatei vom Typ `activity_main.xml` zum Anzeigen von Schaltflächen und Textfeldern:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testen Ihrer App

### <a name="run-locally"></a>Lokales Ausführen

Erstellen Sie die App, und stellen Sie sie auf einem Testgerät oder in einem Emulator bereit. Sie sollten sich anmelden und Token für Azure AD oder persönliche Microsoft-Konten abrufen können.

Nach der Anmeldung zeigt die App die vom Microsoft Graph-Endpunkt `/me` zurückgegebenen Daten an.
PR 4
### <a name="consent"></a>Zustimmung

Wenn sich ein Benutzer zum ersten Mal bei Ihrer App anmeldet, wird er von Microsoft aufgefordert, den angeforderten Berechtigungen zuzustimmen. Bei manchen Azure AD-Mandanten ist die Benutzereinwilligung deaktiviert, sodass Administratoren im Namen aller Benutzer einwilligen müssen. Zur Unterstützung dieses Szenarios müssen Sie entweder einen eigenen Mandanten erstellen oder eine Administratoreinwilligung einholen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie das im Schritt [Registrieren Ihrer Anwendung](#register-your-application) erstellte App-Objekt, wenn Sie es nicht mehr benötigen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

In der mehrteiligen Szenarioreihe erfahren Sie mehr über das Entwickeln von mobilen Apps, die geschützte Web-APIs abrufen.

> [!div class="nextstepaction"]
> [Szenario: Mobile App, die Web-APIs aufruft](scenario-mobile-overview.md)
