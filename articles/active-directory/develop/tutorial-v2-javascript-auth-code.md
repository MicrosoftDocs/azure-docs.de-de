---
title: 'Tutorial: Erstellen einer den Autorisierungscodefluss verwendenden JavaScript-Single-Page-Webanwendung | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine JavaScript-Single-Page-Webanwendung, mit der sich Benutzer anmelden und den Authentifizierungscodefluss verwenden können, um ein Zugriffstoken von der Microsoft Identity Platform zu erhalten und die Microsoft Graph-API aufzurufen.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 79fe821e2da494ab8c9e4cb407e2c2b025f75568
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169102"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer JavaScript-Single-Page-Webanwendung (SPA) mithilfe des Autorisierungscodeflusses

In diesem Tutorial wird eine JavaScript-SPA (Single-Page-Webanwendung) erstellt, die Benutzer anmeldet und Microsoft Graph über den Autorisierungscodeflow mit PKCE aufruft. Die von Ihnen erstellte SPA verwendet die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für JavaScript v2.0.

Dieses Tutorial umfasst folgende Punkte:
> [!div class="checklist"]
> * Ausführen des OAuth 2.0-Autorisierungscodeflusses mit PKCE
> * Anmelden von persönlichen Microsoft-Konten sowie von Geschäfts-, Schul- oder Unikonten
> * Abrufen eines Zugriffstokens
> * Aufrufen der Microsoft Graph-API oder Ihrer eigenen API, für die Zugriffstoken vom Microsoft Identity Platform-Endpunkt abgerufen werden müssen.

MSAL.js 2.0 verbessert MSAL.js 1.0 durch Unterstützung des Autorisierungscodeflusses im Browser anstelle des impliziten Genehmigungsflusses. MSAL.js 2.0 unterstütz den impliziten Fluss **NICHT**.

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/) zum Ausführen eines lokalen Webservers
* [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

## <a name="how-the-tutorial-app-works"></a>Funktionsweise der Tutorial-App

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagramm, das den Autorisierungscodefluss in einer Single-Page-Webanwendung darstellt.":::

Die in diesem Tutorial erstellte Anwendung ermöglicht einer JavaScript-SPA das Abfragen der Microsoft Graph-API, indem Sicherheitstoken vom Microsoft Identity Platform-Endpunkt abgerufen werden. In diesem Szenario wird nach einer Benutzeranmeldung ein Zugriffstoken angefordert und den HTTP-Anforderungen im Autorisierungsheader hinzugefügt. Tokenabruf und -verlängerung werden von der Microsoft Authentication Library für JavaScript (MSAL.js) verarbeitet.

In diesem Tutorial wird die folgende Bibliothek verwendet:

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) Microsoft Authentication Library für JavaScript v2.0: Browserpaket

## <a name="get-the-completed-code-sample"></a>Abrufen des abgeschlossenen Codebeispiels

Möchten Sie stattdessen lieber das abgeschlossene Beispielprojekt dieses Tutorials herunterladen? Um das Projekt mit einem lokalen Webserver auszuführen, z. B. Node.js, klonen Sie das Repository [ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2):

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Um das Codebeispiel zu konfigurieren, bevor Sie es ausführen, wechseln Sie dann zum [Konfigurationsschritt](#register-your-application).

Um mit dem Tutorial fortzufahren und die Anwendung selbst zu erstellen, fahren Sie mit dem nächsten Abschnitt, [Voraussetzungen](#prerequisites), fort.

## <a name="create-your-project"></a>Erstellen Ihres Projekts

Sobald Sie [Node.js](https://nodejs.org/en/download/) installiert haben, erstellen Sie einen Ordner, um Ihre Anwendung zu hosten, z. B. *msal-spa-tutorial*.

Implementieren Sie als Nächstes einen kleinen [Express](https://expressjs.com/)-Webserver für die Bereitstellung Ihrer *index.html*-Datei.

1. Wechseln Sie zunächst in Ihrem Terminal zu Ihrem Projektverzeichnis, und führen Sie dann die folgenden `npm`-Befehle aus:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Erstellen Sie als Nächstes eine Datei namens *server.js*, und fügen Sie den folgenden Code hinzu:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Sie verfügen jetzt über einen kleinen Server zum Bereitstellen Ihrer SPA. Nachdem Sie den Rest des Tutorials abgeschlossen haben, sollte die Datei- und Ordnerstruktur Ihres Projekts in etwa wie folgt aussehen:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Erstellen der Benutzeroberfläche für die Single-Page-Webanwendung (SPA)

1. Erstellen Sie einen Ordner *app* in Ihrem Projektverzeichnis, und erstellen Sie darin eine Datei *index.html* für Ihre JavaScript-Single-Page-Webanwendung. Mit dieser Datei wird eine Benutzeroberfläche implementiert, die mit dem **Bootstrap 4-Framework** erstellt wurde, und es werden Skriptdateien für die Konfiguration, Authentifizierung und API-Aufrufe importiert.

    Fügen Sie der Datei *index.html* den folgenden Code hinzu:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
          </div>
        </div>
        </div>
        <br>
        <br>
          <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
          </div>
          <div class="col-md-5">
            <div class="tab-content" id="nav-tabContent">
            </div>
          </div>
        </div>
        <br>
        <br>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Erstellen Sie als Nächstes ebenfalls im Ordner *app* eine Datei mit dem Namen *ui.js*, und fügen Sie den folgenden Code hinzu. Diese Datei wird auf DOM-Elemente zugreifen und diese aktualisieren.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>Anwendung registrieren

Befolgen Sie die Schritte in [Single-Page-Webanwendung: App-Registrierung](scenario-spa-app-registration.md), um eine App-Registrierung für Ihre SPA zu erstellen.

Geben Sie im Schritt [Umleitungs-URI: MSA.js 2.0 mit Auorisierungscodefluss](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) `http://localhost:3000` ein, den Standardort, an dem die Anwendung dieses Tutorials ausgeführt wird.

Wenn Sie einen anderen Port verwenden möchten, geben Sie `http://localhost:<port>` ein, wobei `<port>` Ihre bevorzugte TCP-Portnummer ist. Wenn Sie eine andere Portnummer als `3000` angeben, aktualisieren Sie auch *server.js* mit Ihrer bevorzugten Portnummer.

### <a name="configure-your-javascript-spa"></a>Konfigurieren Ihrer JavaScript-SPA

Erstellen Sie eine Datei mit dem Namen *authConfig.js* im Ordner *app*, die die Konfigurationsparameter für die Authentifizierung enthält, und fügen Sie dann den folgenden Code hinzu:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Ändern Sie die Werte im Abschnitt `msalConfig`, wie hier beschrieben:

- `Enter_the_Application_Id_Here`: Die **Anwendungs-ID (Client)** der von Ihnen registrierten Anwendung.
- `Enter_the_Cloud_Instance_Id_Here`: Die Azure Cloud-Instanz, in der Ihre Anwendung registriert ist.
  - Geben Sie für die Azure-Hauptcloud (oder die *globale* Cloud) `https://login.microsoftonline.com` ein.
  - Geeignete Werte für **nationale** Clouds (z. B. für China) finden Sie unter [Nationale Clouds](authentication-national-cloud.md).
- `Enter_the_Tenant_info_here` sollte einer der folgenden Werte sein:
  - Wenn Ihre Anwendung *Nur Konten in diesem Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen**. Beispiel: `contoso.microsoft.com`.
  - Falls Ihre Anwendung *Konten in einem beliebigen Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch `organizations`.
  - Unterstützt Ihre Anwendung *Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten*, ersetzen Sie diesen Wert durch `common`.
  - Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch `consumers`.
- `Enter_the_Redirect_Uri_Here` ist `http://localhost:3000`

Der `authority`-Wert in Ihrer *authConfig.js* sollte ähnlich wie folgt aussehen, wenn Sie die globale Azure Cloud verwenden:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Erstellen Sie immer noch im Ordner *app* eine Datei namens *graphConfig.js*. Fügen Sie den folgenden Code hinzu, um Ihrer Anwendung die Konfigurationsparameter zum Aufrufen der Microsoft Graph-API bereitzustellen:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Ändern Sie die Werte im Abschnitt `graphConfig`, wie hier beschrieben:

- `Enter_the_Graph_Endpoint_Here` ist die Instanz der Microsoft Graph-API, mit der die Anwendung kommunizieren soll.
  - Ersetzen Sie beide Vorkommen dieser Zeichenfolge für den **globalen** Microsoft Graph-API-Endpunkt durch `https://graph.microsoft.com`.
  - Informationen zu Endpunkten in **nationalen** Cloudbereitstellungen finden Sie in der Microsoft Graph Dokumentation unter [Bereitstellungen nationaler Clouds](/graph/deployments).

Die Werte `graphMeEndpoint` und `graphMailEndpoint` in Ihrer *graphConfig.js* sollte ähnlich wie folgt aussehen, wenn Sie den globalen Endpunkt verwenden:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-microsoft-authentication-library-msal-to-sign-in-user"></a>Verwenden der MSAL (Microsoft Authentication Library) für die Benutzeranmeldung

### <a name="pop-up"></a>Popup

Erstellen Sie im Ordner *app* eine Datei mit dem Namen *authPopup.js*, und fügen Sie den folgenden Authentifizierungs- und Tokenerfassungscode für das Popup der Anmeldung hinzu:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Umleiten

Erstellen Sie eine Datei mit dem Namen *authRedirect.js* im Ordner *app*, und fügen Sie den folgenden Authentifizierungs- und Tokenerfassungscode für die Anmeldungsumleitung hinzu:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Funktionsweise des Codes

Wenn ein Benutzer das erste Mal die Schaltfläche **Anmelden** auswählt, ruft die `signIn`-Methode `loginPopup` auf, um den Benutzer anzumelden. Die `loginPopup`-Methode öffnet ein Popupfenster mit dem *Microsoft Identity Platform-Endpunkt*, um die Anmeldeinformationen des Benutzers anzufordern und zu überprüfen. Nach einer erfolgreichen Anmeldung wird von *msal.js* der [Autorisierungcodefluss](v2-oauth2-auth-code-flow.md) initiiert.

An diesem Punkt wird ein mit PKCE geschützter Autorisierungscode an den mit CORS geschützten Tokenendpunkt gesendet und gegen Token ausgetauscht. Ein ID-Token, Zugriffstoken und Aktualisierungstoken werden von Ihrer Anwendung empfangen und von *msal.js* verarbeitet, und die in den Token enthaltenen Informationen werden jeweils zwischengespeichert.

Das ID-Token enthält grundlegende Informationen zum Benutzer, z. B. dessen Anzeigenamen. Wenn Sie planen, von dem ID-Token bereitgestellte Daten zu nutzen, *muss* Ihr Back-End-Server dieses überprüfen, um zu garantieren, dass das Token für einen gültigen Benutzer Ihrer Anwendung ausgestellt wurde.

Das Zugriffstoken hat eine begrenzte Lebensdauer und läuft nach 24 Stunden ab. Über das Aktualisierungstoken können im Hintergrund neue Zugriffstoken bezogen werden.

Die SPA, die Sie in diesem Tutorial erstellt haben, ruft `acquireTokenSilent` bzw. `acquireTokenPopup` auf, um ein *Zugriffstoken* zu beziehen, das zum Abfragen von Informationen zum Benutzerprofil von der Microsoft Graph-API verwendet wird. Ein Beispiel zur Überprüfung des ID-Tokens finden Sie in der Beispielanwendung [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) auf GitHub. In diesem Beispiel wird für die Tokenüberprüfung eine ASP.NET-Web-API verwendet.

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

Nach der erstmaligen Anmeldung sollte Ihr App die Benutzer nicht jedes Mal erneut zur Authentifizierung auffordern, wenn sie auf eine geschützte Ressource zugreifen müssen (d. h. ein Token anzufordern). Rufen Sie `acquireTokenSilent` auf, um diese Aufforderungen zur erneuten Authentifizierung zu verhindern. Es gibt aber einige Situationen, in denen Sie die Interaktion mit dem Microsoft Identity Platform-Endpunkt ggf. erzwingen müssen. Beispiel:

- Benutzer müssen ihre Anmeldeinformationen erneut eingeben, weil das Kennwort abgelaufen ist.
- Ihre Anwendung fordert Zugriff auf eine Ressource an, und die Einwilligung des Benutzers ist erforderlich.
- Die zweistufige Authentifizierung ist erforderlich.

Beim Aufrufen von `acquireTokenPopup` wird ein Popupfenster geöffnet (oder Benutzer werden über `acquireTokenRedirect` an den Microsoft Identity Platform-Endpunkt umgeleitet). In diesem Fenster müssen Benutzer ihre Anmeldeinformationen bestätigen, ihre Zustimmung für die erforderliche Ressource geben oder die zweistufige Authentifizierung durchführen.

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Die Methode `acquireTokenSilent` wickelt den Bezug und die Verlängerung von Token ohne Eingreifen des Benutzers ab. Nach erstmaligem Ausführen von `loginPopup` (oder `loginRedirect`) wird normalerweise die Methode `acquireTokenSilent` zum Beziehen von Token verwendet, die für den Zugriff auf geschützte Ressourcen für nachfolgende Aufrufe genutzt werden. (Aufrufe zum Anfordern oder Verlängern von Token werden im Hintergrund ausgeführt.) `acquireTokenSilent` ist in bestimmten Fällen möglicherweise nicht erfolgreich – etwa, wenn das Kennwort des Benutzers abgelaufen ist. Ihre Anwendung kann diese Ausnahme auf zwei Arten handhaben:

1. Durch sofortiges Aufrufen von `acquireTokenPopup`, um eine Aufforderung zur Benutzeranmeldung auszulösen. Dieses Muster wird in der Regel in Onlineanwendungen verwendet, in denen kein nicht authentifizierter Inhalt in der Anwendung für den Benutzer verfügbar ist. Die in diesem Leitfaden generierte Beispielanwendung verwendet dieses Muster.
1. Durch eine visuelle Benachrichtigung mit dem Hinweis, dass eine interaktive Anmeldung erforderlich ist, damit der Benutzer den richtigen Zeitpunkt zum Anmelden auswählen oder damit die Anwendung `acquireTokenSilent` zu einem späteren Zeitpunkt wiederholen kann. Dieses Verfahren wird normalerweise verwendet, wenn der Benutzer andere Funktionen der Anwendung nutzen kann, ohne unterbrochen zu werden – beispielsweise, wenn in der Anwendung nicht authentifizierte Inhalte vorhanden sind. In diesem Fall kann der Benutzer entscheiden, wann er sich anmelden möchte, um auf die geschützte Ressource zuzugreifen oder die veralteten Informationen zu aktualisieren.

> [!NOTE]
> In diesem Tutorial werden standardmäßig die Methoden `loginPopup` und `acquireTokenPopup` verwendet. Wenn Sie Internet Explorer verwenden, empfiehlt es sich, die Methoden `loginRedirect` und `acquireTokenRedirect` zu verwenden, da ein [bekanntes Problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) mit Internet Explorer und Popupfenstern vorliegt. Ein Beispiel, wie Sie dasselbe Ergebnis erreichen können, indem Sie Umleitungsmethoden verwenden, finden Sie unter [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) auf GitHub.

## <a name="call-the-microsoft-graph-api"></a>Aufrufen der Microsoft Graph-API

Erstellen Sie eine Datei mit dem Namen *graph.js* im Ordner *app*, und fügen Sie den folgenden Code hinzu, der REST-Aufrufe der Microsoft Graph-API durchführt:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

In der in diesem Tutorial erstellten Beispielanwendung wird die Methode `callMSGraph()` verwendet, um eine HTTP-Anforderung vom Typ `GET` für eine geschützte Ressource auszuführen, die ein Token erfordert. Der Inhalt wird anschließend an den Aufrufer zurückgegeben. Diese Methode fügt das abgerufene Token in den *HTTP-Autorisierungsheader* ein. In der in diesem Tutorial erstellten Beispielanwendung ist die geschützte Ressource der Endpunkt *me* der Microsoft Graph-API, der die Profilinformationen des angemeldeten Benutzers anzeigt.

## <a name="test-your-application"></a>Testen Ihrer Anwendung

Sie haben die Erstellung der Anwendung abgeschlossen und sind nun bereit, den Node.js-Webserver zu starten und die Funktionalität der App zu testen.

1. Starten Sie den Node.js-Webserver, indem Sie den folgenden Befehl im Stammverzeichnis Ihres Projektordners ausführen:

   ```console
   npm start
   ```
1. Navigieren Sie in Ihrem Browser zu `http://localhost:3000` oder `http://localhost:<port>`, wobei `<port>` der Port ist, an dem Ihr Webserver lauscht. Der Inhalt der Datei *index.html* und die Schaltfläche **Anmelden** sollten angezeigt werden.

### <a name="sign-in-to-the-application"></a>Anmelden bei der Anwendung

Nachdem der Browser die Datei *index.html* geladen hat, klicken Sie auf **Anmelden**. Sie werden aufgefordert, sich mit dem Microsoft Identity Platform-Endpunkt anzumelden:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Webbrowser mit dem Anmeldedialogfeld.":::

### <a name="provide-consent-for-application-access"></a>Zustimmen zum Anwendungszugriff

Wenn Sie sich zum ersten Mal bei Ihrer Anwendung anmelden, werden Sie aufgefordert, ihr Zugriff auf Ihr Profil zu gewähren und sich anzumelden:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Im Webbrowser angezeigtes Inhaltsdialogfeld.":::

Wenn Sie den angeforderten Berechtigungen zustimmen, zeigt die Webanwendung Ihren Benutzernamen an, was eine erfolgreiche Anmeldung signalisiert:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Ergebnisse einer erfolgreichen Anmeldung im Webbrowser.":::

### <a name="call-the-graph-api"></a>Aufrufen der Graph-API

Nachdem Sie sich angemeldet haben, wählen Sie **Profil anzeigen** aus, um die Benutzerprofilinformationen anzuzeigen, die in der Antwort des Aufrufs der Microsoft Graph-API zurückgegeben werden:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Im Browser angezeigte Profilinformationen aus Microsoft Graph.":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Weitere Informationen zu Bereichen und delegierten Berechtigungen

Die Microsoft Graph-API benötigt den Bereich *user.read*, um das Benutzerprofil zu lesen. Dieser Bereich wird standardmäßig jeder Anwendung automatisch hinzugefügt, die im Azure-Portal registriert ist. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Die Microsoft Graph-API benötigt beispielsweise den Bereich *Mail.Read*, um die E-Mail des Benutzers aufzuführen.

Wenn Sie Bereiche hinzufügen, werden Ihre Benutzer möglicherweise aufgefordert, zusätzliche Zustimmung für die hinzugefügten Bereiche zu erteilen.

Wenn eine Back-End-API keinen Bereich benötigt, was nicht empfohlen wird, können Sie `clientId` bei den Aufrufen zum Beschaffen von Token als Bereich verwenden.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie sich noch ausführlicher mit der Entwicklung von JavaScript-Single-Page-Webanwendungen auf der Microsoft Identity Platform beschäftigen möchten, können Sie sich die mehrteilige Szenarioreihe ansehen:

> [!div class="nextstepaction"]
> [Szenario: Einseitige Anwendung](scenario-spa-overview.md)