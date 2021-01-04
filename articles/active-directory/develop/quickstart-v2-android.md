---
title: 'Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Android-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie aus einer Android-App eine API aufrufen, für die Zugriffstoken erforderlich sind, die über die Microsoft Identity Platform ausgegeben werden.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: f5909a3a824149d9bb4247c78eaaa895b040c6f2
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95993974"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Schnellstart: Benutzeranmeldung und Aufrufen der Microsoft Graph-API aus einer Android-App

In diesem Schnellstart laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Android-Anwendung Benutzer anmelden und ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen. 

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

Anwendungen müssen durch ein App-Objekt in Azure Active Directory dargestellt werden, damit Microsoft Identity Platform Token für Ihre Anwendung bereitstellen kann.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Android Studio
* Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
>  Damit das Codebeispiel für diese Schnellstartanleitung funktioniert, müssen Sie einen mit dem Authentifizierungsbroker kompatiblen Umleitungs-URI hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-android/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.
>
> ### <a name="step-2-download-the-project"></a>Schritt 2: Herunterladen des Projekts
> [!div class="sxs-lookup" renderon="portal"]
> Führen Sie das Projekt mit Android Studio aus.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
> Wir haben das Projekt mit Werten Ihrer App-Eigenschaften konfiguriert. Es ist nun ausführungsbereit.
> Die Beispiel-App wird auf dem Bildschirm **Single Account Mode** (Einzelkontomodus) gestartet. Standardmäßig wird der Standardbereich **user.read** angegeben. Dieser wird beim Lesen Ihrer eigenen Profildaten während des Aufrufs der Microsoft Graph-API verwendet. Die URL für die Microsoft Graph-API wird standardmäßig bereitgestellt. Sie können beide Werte ggf. ändern.
>
> ![MSAL-Beispiel-App mit Nutzung durch einzelnes Konto und mehrere Konten](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Verwenden Sie das App-Menü, um zwischen dem Einzelkontomodus und dem Modus für mehrere Konten zu wechseln.
>
> Melden Sie sich im Einzelkontomodus mit einem Home- oder Geschäftskonto oder an:
>
> 1. Wählen Sie **Get graph data interactively** (Graph-Daten interaktiv abrufen) aus, um Benutzer nach ihren Anmeldeinformationen abzufragen. Die Ausgabe des Aufrufs der Microsoft Graph-API wird im Bildschirm unten angezeigt.
> 2. Wählen Sie nach der Anmeldung **Get graph data silently** (Graph-Daten im Hintergrund abrufen) aus, um die Microsoft Graph-API erneut aufzurufen, ohne dass die Anmeldeinformationen des Benutzers erneut abgefragt werden. Die Ausgabe des Aufrufs der Microsoft Graph-API wird im Bildschirm unten angezeigt.
>
> Im Modus für mehrere Konten können Sie dieselben Schritte ausführen.  Außerdem können Sie das angemeldete Konto entfernen. Dadurch werden auch die zwischengespeicherten Token für dieses Konto entfernt.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Schritt 1: Abrufen der Beispiel-App
>
> [Laden Sie den Code herunter](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Schritt 2: Ausführen der Beispiel-App
>
> Wählen Sie in der Dropdownliste **Verfügbare Geräte** von Android Studio Ihren Emulator oder Ihr physisches Gerät aus, und führen Sie die App aus.
>
> Die Beispiel-App wird auf dem Bildschirm **Single Account Mode** (Einzelkontomodus) gestartet. Standardmäßig wird der Standardbereich **user.read** angegeben. Dieser wird beim Lesen Ihrer eigenen Profildaten während des Aufrufs der Microsoft Graph-API verwendet. Die URL für die Microsoft Graph-API wird standardmäßig bereitgestellt. Sie können beide Werte ggf. ändern.
>
> ![MSAL-Beispiel-App mit Nutzung durch einzelnes Konto und mehrere Konten](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Verwenden Sie das App-Menü, um zwischen dem Einzelkontomodus und dem Modus für mehrere Konten zu wechseln.
>
> Melden Sie sich im Einzelkontomodus mit einem Home- oder Geschäftskonto oder an:
>
> 1. Wählen Sie **Get graph data interactively** (Graph-Daten interaktiv abrufen) aus, um Benutzer nach ihren Anmeldeinformationen abzufragen. Die Ausgabe des Aufrufs der Microsoft Graph-API wird im Bildschirm unten angezeigt.
> 2. Wählen Sie nach der Anmeldung **Get graph data silently** (Graph-Daten im Hintergrund abrufen) aus, um die Microsoft Graph-API erneut aufzurufen, ohne dass die Anmeldeinformationen des Benutzers erneut abgefragt werden. Die Ausgabe des Aufrufs der Microsoft Graph-API wird im Bildschirm unten angezeigt.
>
> Im Modus für mehrere Konten können Sie dieselben Schritte ausführen.  Außerdem können Sie das angemeldete Konto entfernen. Dadurch werden auch die zwischengespeicherten Token für dieses Konto entfernt.

## <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
![Screenshot der Beispiel-App](media/quickstart-v2-android/android-intro.svg)


Der Code ist in Fragmente unterteilt, in denen veranschaulicht wird, wie Sie eine MSAL-App für ein einzelnes Konto und für mehrere Konten schreiben. Die Codedateien sind wie folgt organisiert:

| Datei  | Zeigt  |
|---------|---------|
| MainActivity | Verwaltet die Benutzeroberfläche |
| MSGraphRequestWrapper  | Ruft die Microsoft Graph-API mit dem von MSAL bereitgestellten Token auf |
| MultipleAccountModeFragment  | Initialisiert eine Anwendung für mehrere Konten, lädt ein Benutzerkonto und ruft ein Token für den Aufruf der Microsoft Graph-API ab |
| SingleAccountModeFragment | Initialisiert eine Anwendung für ein einzelnes Konto, lädt ein Benutzerkonto und ruft ein Token für den Aufruf der Microsoft Graph-API ab |
| res/auth_config_multiple_account.json  | Die Konfigurationsdatei für mehrere Konten |
| res/auth_config_single_account.json  | Die Konfigurationsdatei für ein Konto |
| Gradle Scripts/build.grade (Module:app) | Die Abhängigkeiten der MSAL-Bibliothek werden hier hinzugefügt |

Diese Dateien werden nun ausführlicher betrachtet, und der MSAL-spezifische Code in jeder wird hervorgehoben.

### <a name="adding-msal-to-the-app"></a>Hinzufügen von MSAL zur App

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) ist die Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird. Gradle 3.0+ installiert die Bibliothek, wenn **Gradle Scripts** > **build.gradle (Module: app)** unter **Dependencies** folgenden Code hinzufügen:

```gradle
implementation 'com.microsoft.identity.client:msal:2.+'
```

Dies wird im Beispielprojekt in build.gradle (Module: app) veranschaulicht:

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:2.+'
    ...
}
```

Dadurch wird Gradle angewiesen, MSAL von Maven Central herunterzuladen und zu kompilieren.

### <a name="msal-imports"></a>MSAL-Importe

Die relevanten Importe für die MSAL-Bibliothek sind `com.microsoft.identity.client.*`.  So wird beispielsweise `import com.microsoft.identity.client.PublicClientApplication;` angezeigt. Dies ist der Namespace für die `PublicClientApplication`-Klasse, die Ihre öffentliche Clientanwendung darstellt.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Diese Datei veranschaulicht, wie Sie eine Einzelkonto-MSAL-App erstellen und eine Microsoft Graph-API aufrufen.

Einzelkonto-Apps werden nur von einem einzigen Benutzer verwendet.  Sie verfügen beispielsweise nur über ein Konto, mit dem Sie sich bei ihrer Mapping-App anmelden.

#### <a name="single-account-msal-initialization"></a>Einzelkonto-MSAL-Initialisierung

In `auth_config_single_account.json` wird in `onCreateView()` eine Einzelkonto-`PublicClientApplication` mit den in der Datei `auth_config_single_account.json` gespeicherten Konfigurationsinformationen erstellt.  So initialisieren Sie die MSAL-Bibliothek für die Verwendung in einer Einzelkonto-MSAL-App:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                 * This test app assumes that the app is only going to support one account.
                 * This requires "account_mode" : "SINGLE" in the config json file.
                 **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Anmelden eines Benutzers

In `SingleAccountModeFragment.java` befindet sich der Code zum Anmelden eines Benutzers in `initializeUI()` im `signInButton`-Click-Handler.

Rufen Sie `signIn()` auf, bevor Sie versuchen, Token abzurufen. `signIn()` verhält sich wie bei einem Aufruf von `acquireToken()`. Dadurch wird eine interaktive Anmeldeaufforderung für den Benutzer erhalten.

Das Anmelden eines Benutzers ist ein asynchroner Vorgang. Es wird ein Rückruf übergeben, der die Microsoft Graph-API aufruft und bei Anmeldung des Benutzers die Benutzeroberfläche aktualisiert:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Abmelden eines Benutzers

In `SingleAccountModeFragment.java` befindet sich der Code zum Abmelden eines Benutzers in `initializeUI()` im `signOutButton`-Click-Handler.  Das Abmelden eines Benutzers ist ein asynchroner Vorgang. Durch das Abmelden des Benutzers wird auch der Tokencache für das betreffende Konto gelöscht. Es wird ein Rückruf erstellt, mit dem die Benutzeroberfläche aktualisiert wird, sobald das Benutzerkonto abgemeldet wurde:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Interaktives Abrufen eines Tokens oder Abrufen eines Tokens im Hintergrund

Um dem Benutzer eine möglichst geringe Anzahl von Eingabeaufforderungen zu präsentieren, wird ein Token im Allgemeinen im Hintergrund abgerufen. Sollte dabei ein Fehler auftreten, wird versucht, das Token interaktiv abzurufen. Der erste Aufruf von `signIn()` durch die App fungiert als Aufruf von `acquireToken()`, durch den der Benutzer nach Anmeldeinformationen abgefragt wird.

Es gibt einige Situationen, in denen der Benutzer aufgefordert werden kann, sein Konto auszuwählen, seine Anmeldeinformationen einzugeben oder den von der App angeforderten Berechtigungen zuzustimmen:

* Wenn sich der Benutzer erstmalig bei der Anwendung anmeldet
* Wenn ein Benutzer sein Kennwort zurücksetzt, muss er seine Anmeldeinformationen eingeben.
* Wenn die Zustimmung widerrufen wurde
* Wenn die App explizit eine Zustimmung erfordert
* Wenn Ihre Anwendung zum ersten Mal Zugriff auf eine Ressource anfordert
* Wenn MFA oder andere Richtlinien für bedingten Zugriff erforderlich sind

Der Code zum interaktiven Abrufen eines Tokens (d. h. über die Benutzeroberfläche, in welcher der Benutzer Aktionen ausführen muss) befindet sich in `SingleAccountModeFragment.java` in `initializeUI()` im `callGraphApiInteractiveButton`-Click-Handler:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Wenn sich der Benutzer bereits angemeldet hat, gestattet `acquireTokenSilentAsync()` Apps das Anfordern von Token im Hintergrund. Dies wird in `initializeUI()` im `callGraphApiSilentButton`-Click-Handler gezeigt:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Laden eines Kontos

Der Code zum Laden eines Kontos befindet sich in `SingleAccountModeFragment.java` in `loadAccount()`.  Das Laden des Benutzerkontos ist ein asynchroner Vorgang, daher werden Rückrufe für die Verarbeitung beim Laden oder Wechseln des Kontos oder bei auftretenden Fehlern an MSAL übergeben.  Im folgenden Code wird außerdem `onAccountChanged()` behandelt; dies tritt auf, wenn ein Konto entfernt wird, der Benutzer zu einem anderen Konto wechselt usw.

```java
private void loadAccount() {
    ...

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
```

#### <a name="call-microsoft-graph"></a>Aufrufen von Microsoft Graph

Wenn ein Benutzer angemeldet ist, wird der Aufruf von Microsoft Graph über eine HTTP-Anforderung durch `callGraphAPI()` gemäß der Definition in `SingleAccountModeFragment.java` ausgeführt. Diese Funktion ist ein Wrapper. Dieser vereinfacht das Beispiel, indem einige Aufgaben wie Abrufen des Zugriffstokens vom `authenticationResult`, Verpacken des Aufrufs in den MSGraphRequestWrapper und Anzeigen der Ergebnisse des Aufrufs ausgeführt werden.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Dies ist die Konfigurationsdatei für eine MSAL-App, die ein einziges Konto verwendet.

Eine Erläuterung dieser Felder finden Sie unter [Android-MSAL-Konfigurationsdatei](msal-configuration.md).

Beachten Sie, dass `"account_mode" : "SINGLE"` vorhanden ist; hierdurch wird diese App für die Verwendung eines einzigen Kontos konfigurieren.

`"client_id"` ist für die Verwendung einer App-Objekt-Registrierung vorkonfiguriert, die von Microsoft verwaltet wird.
`"redirect_uri"` ist so vorkonfiguriert, dass der Signaturschlüssel aus dem Codebeispiel verwendet wird.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Diese Datei veranschaulicht, wie Sie eine MSAL-App für mehrere Konten erstellen und eine Microsoft Graph-API aufrufen.

Ein Beispiel für eine App mit mehreren Konten ist eine E-Mail-App, die Ihnen das Arbeiten mit mehreren Benutzerkonten ermöglich (z. B. ein Geschäftskonto und ein privates Konto).

#### <a name="multiple-account-msal-initialization"></a>MSAL-Initialisierung für mehrere Konten

In der Datei `MultipleAccountModeFragment.java` wird in `onCreateView()` ein App-Objekt für mehrere Konten (`IMultipleAccountPublicClientApplication`) mit den in der Datei `auth_config_multiple_account.json file` gespeicherten Konfigurationsinformationen erstellt:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

Das erstellte `MultipleAccountPublicClientApplication`-Objekt wird in einer Klassenmember-Variablen gespeichert, sodass es für die Interaktion mit der MSAL-Bibliothek verwendet werden kann, um Token abzurufen und das Benutzerkonto zu laden und zu entfernen.

#### <a name="load-an-account"></a>Laden eines Kontos

Apps für mehrere Konten rufen im Allgemeinen `getAccounts()` auf, um das gewünschte Konto für MSAL-Vorgänge auszuwählen. Der Code zum Laden eines Kontos befindet sich in der Datei `MultipleAccountModeFragment.java` in `loadAccounts()`.  Das Laden des Benutzerkontos ist ein asynchroner Vorgang. Daher werden die Situationen, in denen ein Konto geladen oder gewechselt oder ein Fehler auftritt, von einem Rückruf behandelt.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Interaktives Abrufen eines Tokens oder Abrufen eines Tokens im Hintergrund

Es gibt einige Situationen, in denen der Benutzer aufgefordert werden kann, sein Konto auszuwählen, seine Anmeldeinformationen einzugeben oder den von der App angeforderten Berechtigungen zuzustimmen:

* Erstmaliges Anmelden von Benutzern bei der Anwendung.
* Wenn ein Benutzer sein Kennwort zurücksetzt, muss er seine Anmeldeinformationen eingeben.
* Wenn die Zustimmung widerrufen wurde
* Wenn die App explizit eine Zustimmung erfordert
* Wenn Ihre Anwendung zum ersten Mal Zugriff auf eine Ressource anfordert
* Wenn MFA oder andere Richtlinien für bedingten Zugriff erforderlich sind

Apps für mehrere Konten rufen Token im Allgemeinen interaktiv (d. h. der Benutzer muss über die Benutzeroberfläche Aktionen ausführen) mit einem Aufruf von `acquireToken()` ab.  Der Code zum interaktiven Abrufen eines Tokens befindet sich in der Datei `MultipleAccountModeFragment.java` in `initializeUI()` im `callGraphApiInteractiveButton`-Click-Handler:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Apps sollten nicht fordern, dass sich die Benutzer jedes Mal anmelden, wenn sie ein Token anfordern. Wenn sich der Benutzer bereits angemeldet hat, gestattet `acquireTokenSilentAsync()` Apps das Anfordern von Token ohne Eingabeaufforderung für den Benutzer. Dies wird in der Datei `MultipleAccountModeFragment.java` in `initializeUI()` im `callGraphApiSilentButton`-Click-Handler gezeigt:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Entfernen eines Kontos

Der Code zum Entfernen eines Kontos und aller zwischengespeicherten Token für das Konto befindet sich in der Datei `MultipleAccountModeFragment.java` in `initializeUI()` im Handler für die Schaltfläche zum Entfernen eines Kontos. Bevor Sie ein Konto entfernen können, benötigen Sie ein Kontoobjekt, das Sie von MSAL-Methoden wie `getAccounts()` und `acquireToken()` abrufen können. Da das Entfernen eines Kontos ein asynchroner Vorgang ist, wird der `onRemoved`-Rückruf zum Aktualisieren der Benutzeroberfläche bereitgestellt.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Dies ist die Konfigurationsdatei für eine MSAL-App, die mehrere Konten verwendet.

Eine Erläuterung der verschiedenen Felder finden Sie unter [Android-MSAL-Konfigurationsdatei](msal-configuration.md).

Im Unterschied zur Konfigurationsdatei [auth_config_single_account.json](#auth_config_single_accountjson) enthält diese Konfigurationsdatei `"account_mode" : "MULTIPLE"` anstelle von `"account_mode" : "SINGLE"`, da es sich um eine App für mehrere Konten handelt.

`"client_id"` ist für die Verwendung einer App-Objekt-Registrierung vorkonfiguriert, die von Microsoft verwaltet wird.
`"redirect_uri"` ist so vorkonfiguriert, dass der Signaturschlüssel aus dem Codebeispiel verwendet wird.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
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

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem Android-Tutorial fort, in dem Sie eine Android-App erstellen, aus der ein Zugriffstoken von der Microsoft Identity Platform abgerufen wird, das dann in der App verwendet wird, um die Microsoft Graph-API aufzurufen.

> [!div class="nextstepaction"]
> [Tutorial: Anmelden von Benutzern und Aufrufen von Microsoft Graph aus einer Android-App](tutorial-v2-android.md)
