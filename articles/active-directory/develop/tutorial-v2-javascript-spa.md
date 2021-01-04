---
title: 'Tutorial: Erstellen einer JavaScript-Single-Page-App mit Microsoft Identity Platform für die Authentifizierung | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine JavaScript-Single-Page-App, die Microsoft Identity Platform zum Anmelden von Benutzern und zum Abrufen eines Zugriffstokens verwendet, um im Namen der Benutzer die Microsoft Graph-API aufzurufen.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: ded54628a307f3cf4441e804f7f1025a0e943b51
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979945"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer JavaScript-SPA (Single-Page-Webanwendung)

In diesem Tutorial wird eine JavaScript-SPA (Single-Page-Webanwendung) erstellt, die Benutzer anmeldet und Microsoft Graph über den impliziten Fluss aufruft. Die von Ihnen erstellte SPA verwendet die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für JavaScript v1.0.

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Erstellen eines JavaScript-Projekts mit `npm`
> * Registrieren der Anwendung im Azure-Portal
> * Hinzufügen von Code zur Unterstützung der Benutzeranmeldung und -abmeldung
> * Hinzufügen von Code zum Aufrufen der Microsoft Graph-API
> * Testen der App

>[!TIP]
> In diesem Tutorial wird MSAL.js v1.x verwendet. Diese Version ist auf die Verwendung des Flows zur impliziten Genehmigung für Single-Page-Webanwendungen beschränkt. Es wird empfohlen, dass alle neuen Anwendungen stattdessen [MSAL.js 2.x und den Autorisierungscodeflow mit PKCE- und CORS-Unterstützung](tutorial-v2-javascript-auth-code.md) verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/) zum Ausführen eines lokalen Webservers
* [Visual Studio Code](https://code.visualstudio.com/download) oder einen anderen Editor zum Bearbeiten von Projektdateien
* Ein zeitgemäßer Webbrowser. **Internet Explorer** wird von der App, die Sie in diesem Tutorial erstellen, aufgrund der Verwendung der [ES6](http://www.ecma-international.org/ecma-262/6.0/)-Konventionen **nicht unterstützt**.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funktionsweise der über diesen Leitfaden generierten Beispiel-App

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

Die über diesen Leitfaden erstellte Beispiel-App ermöglicht einer JavaScript-SPA das Abfragen der Microsoft Graph-API oder einer Web-API, die Token vom Microsoft Identity Platform-Endpunkt akzeptiert. In diesem Szenario wird nach der Benutzeranmeldung ein Zugriffstoken angefordert und den HTTP-Anforderungen über den Autorisierungsheader hinzugefügt. Dieses Token wird verwendet, um das Profil und die E-Mails des Benutzers über die **MS Graph-API** abzurufen.

Tokenabruf und -verlängerung werden von der [Microsoft Authentication Library (MSAL) für JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) verarbeitet.

## <a name="set-up-your-web-server-or-project"></a>Einrichten Ihres Webservers oder Projekts

> Möchten Sie stattdessen das Projekt dieses Beispiels herunterladen? [Laden Sie die Projektdateien herunter](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Wenn Sie das Codebeispiel vor der Ausführung konfigurieren möchten, navigieren Sie direkt zum [Konfigurationsschritt](#register-your-application).

## <a name="create-your-project"></a>Erstellen Ihres Projekts

Vergewissern Sie sicher, dass [Node.js](https://nodejs.org/en/download/) installiert ist, und erstellen Sie einen Ordner für Ihre Anwendung. Dort implementieren wir einen einfachen [Express](https://expressjs.com/)-Webserver, um die `index.html` Datei zu verarbeiten.

1. Suchen Sie mithilfe eines Terminals (etwa mithilfe des integrierten Terminals von Visual Studio Code) Ihren Projektordner, und geben Sie Folgendes ein:

   ```console
   npm init
   ```

2. Installieren Sie anschließend die erforderlichen Abhängigkeiten:

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. Erstellen Sie nun eine JS-Datei mit dem Namen `server.js`, und fügen Sie dann den folgenden Code hinzu:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

Sie verfügen jetzt über einen einfachen Server zum Bereitstellen Ihrer SPA. Die vorgesehene Ordnerstruktur am Ende dieses Tutorials lautet wie folgt:

![eine Textdarstellung der vorgesehenen SPA-Ordnerstruktur](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Erstellen der Benutzeroberfläche für die Single-Page-Webanwendung (SPA)

1. Erstellen Sie die Datei `index.html` für Ihre JavaScript-SPA. Diese Datei implementiert eine Benutzeroberfläche, die mit dem **Bootstrap 4-Framework** erstellt wurde, und importiert Skriptdateien für die Konfiguration, Authentifizierung und API-Aufrufe.

   Fügen Sie in der Datei `index.html` den folgenden Code hinzu:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > Sie können die Version von „MSAL.js“ im obigen Skript durch die neueste veröffentlichte Version unter [MSAL.js-Releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) ersetzen.

2. Erstellen Sie nun eine JS-Datei mit dem Namen `ui.js`, die auf DOM-Elemente zugreift und diese aktualisiert, und fügen Sie den folgenden Code hinzu:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
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

Bevor Sie mit der Authentifizierung fortfahren, registrieren Sie die Anwendung in **Azure Active Directory**.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wenn Sie in Ihrem Konto auf mehrere Mandanten zugreifen können, wählen Sie rechts oben das Konto aus, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Navigieren Sie auf der Microsoft Identity Platform für Entwickler zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908).
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** einen Namen für Ihre Anwendung ein.
1. Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
1. Wählen Sie im Abschnitt **Umleitungs-URI** in der Dropdownliste die Plattform **Web** aus, und legen Sie den Wert auf die URL der Anwendung (basierend auf Ihrem Webserver) fest.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich für die spätere Verwendung auf der Seite **Übersicht** den Wert von **Anwendungs-ID (Client)** .
1. Für diesen Schnellstart muss der [Flow zur impliziten Genehmigung](v2-oauth2-implicit-grant-flow.md) aktiviert werden. Wählen Sie im linken Bereich der registrierten Anwendung die Option **Authentifizierung** aus.
1. Aktivieren Sie unter **Erweiterte Einstellungen** und **Implizite Gewährung** die Kontrollkästchen **ID-Token** und **Zugriffstoken**. ID- und Zugriffstoken sind erforderlich, da diese App Benutzer anmelden und eine API aufrufen muss.
1. Wählen Sie **Speichern** aus.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Festlegen einer Umleitungs-URL für Node.js
>
> Für Node.js können Sie den Webserverport in der Datei *server.js* festlegen. In diesem Tutorial wird zwar der Port 3000 verwendet, Sie können aber auch einen beliebigen anderen verfügbaren Port verwenden.
>
> Wenn Sie eine Umleitungs-URL in den Registrierungsinformationen der Anwendung einrichten möchten, kehren Sie zum Bereich **Anwendungsregistrierung** zurück und führen eine der folgenden Aktionen aus:
>
> - Legen Sie *`http://localhost:3000/`* als **Umleitungs-URL** fest.
> - Wenn Sie einen benutzerdefinierten TCP-Port nutzen, verwenden Sie *`http://localhost:<port>/`* (wobei *\<port>* die benutzerdefinierte TCP-Portnummer ist).
>   1. Kopieren Sie den Wert von **URL**.
>   1. Kehren Sie zurück zum Bereich **Anwendungsregistrierung**, und fügen Sie den kopierten Wert als **Umleitungs-URL** ein.
>

### <a name="configure-your-javascript-spa"></a>Konfigurieren Ihrer JavaScript-SPA

Erstellen Sie eine neue JS-Datei mit dem Namen `authConfig.js`, die die Konfigurationsparameter für die Authentifizierung enthält, und fügen Sie den folgenden Code hinzu:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 Hierbei gilt:
 - *\<Enter_the_Application_Id_Here>* ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
 - *\<Enter_the_Cloud_Instance_Id_Here>* ist die Instanz der Azure-Cloud. Geben Sie für die Azure-Hauptcloud oder für die globale Azure-Cloud einfach *https://login.microsoftonline.com* ein. Informationen zu **nationalen** Clouds (etwa für China) finden Sie unter [Nationale Clouds](./authentication-national-cloud.md).
 - *\<Enter_the_Tenant_info_here>* wird auf eine der folgenden Optionen festgelegt:
   - Wenn Ihre Anwendung *Nur Konten in diesem Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (beispielsweise *contoso.microsoft.com*).
   - Wenn Ihre Anwendung *Konten in einem beliebigen Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch **Organisationen**.
   - Wenn Ihre Anwendung *Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten* unterstützt, ersetzen Sie diesen Wert durch **common**. Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch **consumers**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Verwenden der MSAL (Microsoft Authentication Library) für die Benutzeranmeldung

Erstellen Sie eine neue JS-Datei mit dem Namen `authPopup.js`, die Ihre Authentifizierungs- und Tokenerfassungslogik enthält, und fügen Sie den folgenden Code hinzu:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>Weitere Informationen

Wenn ein Benutzer das erste Mal die Schaltfläche **Anmelden** auswählt, ruft die `signIn`-Methode `loginPopup` auf, um den Benutzer anzumelden. Diese Methode öffnet ein Popupfenster mit dem *Microsoft Identity Platform-Endpunkt*, um die Anmeldeinformationen des Benutzers anzufordern und zu überprüfen. Nach erfolgreicher Anmeldung wird der Benutzer zur ursprünglichen Indexseite (*index.html*) umgeleitet. Ein Token wird empfangen und von `msal.js` verarbeitet, und die im Token enthaltenen Informationen werden zwischengespeichert. Dieses Token ist das sogenannte *ID-Token*, das grundlegende Informationen zum Benutzer enthält, beispielsweise den Benutzeranzeigenamen. Wenn Sie von diesem Token bereitgestellte Daten nutzen möchten, müssen Sie sicherstellen, dass das Token durch Ihren Back-End-Server überprüft wird. So wird garantiert, dass das Token für einen gültigen Benutzer Ihrer Anwendung ausgestellt wurde.

Die in diesem Leitfaden generierte SPA ruft `acquireTokenSilent` bzw. `acquireTokenPopup` auf, um ein *Zugriffstoken* zu beziehen, das zum Abfragen von Informationen zum Benutzerprofil von der Microsoft Graph-API verwendet wird. Wenn Sie ein Beispiel benötigen, das das ID-Token überprüft, sehen Sie sich [diese Beispielanwendung](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub-Beispiel „active-directory-javascript-singlepageapp-dotnet-webapi-v2“") auf GitHub an. In diesem Beispiel wird für die Tokenüberprüfung eine ASP.NET-Web-API verwendet.

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

Nach der erstmaligen Anmeldung sollen die Benutzer nicht jedes Mal erneut zur Authentifizierung aufgefordert werden, wenn sie ein Token für den Zugriff auf eine Ressource anfordern müssen. Aus diesem Grund sollten Token in den meisten Fällen mit *acquireTokenSilent* bezogen werden. Es gibt jedoch Situationen, in denen Sie die Interaktion mit dem Microsoft Identity Platform-Endpunkt erzwingen müssen. Beispiele:

- Benutzer müssen ihre Anmeldeinformationen erneut eingeben, da das Kennwort abgelaufen ist.
- Ihre Anwendung fordert Zugriff auf eine Ressource an, und die Zustimmung des Benutzers ist erforderlich.
- Die zweistufige Authentifizierung ist erforderlich.

Durch Aufrufen von *acquireTokenPopup* wird ein Popupfenster geöffnet. (Mit *acquireTokenRedirect* werden Benutzer zum Microsoft Identity Platform-Endpunkt umgeleitet.) In diesem Fenster müssen Benutzer ihre Anmeldeinformationen bestätigen, ihre Zustimmung für die erforderliche Ressource geben oder die zweistufige Authentifizierung durchführen.

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Die Methode `acquireTokenSilent` wickelt den Bezug und die Verlängerung von Token ohne Eingreifen des Benutzers ab. Nach erstmaligem Ausführen von `loginPopup` (oder `loginRedirect`) wird normalerweise die Methode `acquireTokenSilent` zum Beziehen von Token verwendet, die für den Zugriff auf geschützte Ressourcen für nachfolgende Aufrufe genutzt werden. (Aufrufe zum Anfordern oder Verlängern von Token werden im Hintergrund ausgeführt.) `acquireTokenSilent` ist in bestimmten Fällen möglicherweise nicht erfolgreich – etwa, wenn das Kennwort des Benutzers abgelaufen ist. Ihre Anwendung kann diese Ausnahme auf zwei Arten handhaben:

1. Durch sofortiges Aufrufen von `acquireTokenPopup`, um eine Benutzeranmeldeaufforderung auszulösen. Dieses Muster wird in der Regel in Onlineanwendungen verwendet, in denen kein nicht authentifizierter Inhalt in der Anwendung für den Benutzer verfügbar ist. Die in diesem Leitfaden generierte Beispielanwendung verwendet dieses Muster.

1. Anwendungen können dem Benutzer auch visuell zu verstehen geben, dass eine interaktive Anmeldung erforderlich ist, damit der Benutzer den richtigen Zeitpunkt zum Anmelden wählen oder die Anwendung `acquireTokenSilent` zu einem späteren Zeitpunkt wiederholen kann. Dies wird normalerweise verwendet, wenn der Benutzer andere Funktionen der Anwendung nutzen kann, ohne unterbrochen zu werden – beispielsweise, wenn in der Anwendung nicht authentifizierte Inhalte vorhanden sind. In diesem Fall kann der Benutzer entscheiden, wann er sich anmelden möchte, um auf die geschützte Ressource zuzugreifen oder die veralteten Informationen zu aktualisieren.

> [!NOTE]
> In diesem Schnellstart werden standardmäßig die Methoden `loginPopup` und `acquireTokenPopup` verwendet. Wenn Sie Internet Explorer als Browser verwenden, empfiehlt es sich, die Methoden `loginRedirect` und `acquireTokenRedirect` zu verwenden, da ein [bekanntes](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) Problem damit vorliegt, wie Popupfenster in Internet Explorer behandelt werden. Wenn Sie erfahren möchten, wie Sie mit `Redirect methods`dasselbe Ergebnis erzielen, sehen Sie [hier](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) nach.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Aufrufen der Microsoft Graph-API unter Verwendung des zuvor bezogenen Tokens

1. Erstellen Sie zunächst eine JS-Datei mit dem Namen `graphConfig.js`, in der die REST-Endpunkte gespeichert werden. Fügen Sie den folgenden Code hinzu:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Hierbei gilt:
   - *\<Enter_the_Graph_Endpoint_Here>* ist die Instanz der MS Graph-API. Ersetzen Sie diese Zeichenfolge für den globalen MS Graph-API-Endpunkt einfach durch `https://graph.microsoft.com`. Informationen zu Bereitstellungen in der nationalen Cloud finden Sie in der [Graph-API-Dokumentation](/graph/deployments).

1. Erstellen Sie als nächstes eine JS-Datei mit dem Namen `graph.js`, die ein REST-Aufruf an die Microsoft Graph-API durchführt, und fügen Sie den folgenden Code hinzu:

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Weitere Informationen zum Richten eines REST-Aufrufs an eine geschützte API

In der Beispielanwendung aus diesem Leitfaden wird die Methode `callMSGraph()` verwendet, um eine HTTP-Anforderung vom Typ `GET` für eine geschützte Ressource auszuführen, die ein Token erfordert. Der Inhalt wird anschließend an den Aufrufer zurückgegeben. Diese Methode fügt das abgerufene Token in den *HTTP-Autorisierungsheader* ein. In der Beispielanwendung aus diesem Leitfaden ist die Ressource der Endpunkt *me* der Microsoft Graph-API, der die Profilinformationen des Benutzers anzeigt.

## <a name="test-your-code"></a>Testen Ihres Codes

1. Konfigurieren Sie den Server zum Lauschen am TCP-Port, der auf dem Speicherort Ihrer Datei *index.html* basiert. Starten Sie für Node.js den Webserver, um an dem Port zu lauschen, indem Sie an einer Eingabeaufforderung im Anwendungsordner die folgenden Befehle ausführen:

   ```bash
   npm install
   npm start
   ```
1. Geben Sie in Ihrem Browser **http://localhost:3000** oder **http://localhost:{port}** ein, wobei *port* der Port ist, an dem der Webserver lauscht. Der Inhalt der Datei *index.html* und die Schaltfläche **Anmelden** sollten angezeigt werden.

Nachdem der Browser die Datei *index.html* geladen hat, klicken Sie auf **Anmelden**. Sie werden aufgefordert, sich mit dem Microsoft Identity Platform-Endpunkt anzumelden:

![Das Fenster zum Anmelden bei Ihrem JavaScript SPA-Konto](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Zustimmen zum Anwendungszugriff

Wenn Sie sich zum ersten Mal bei Ihrer Anwendung anmelden, werden Sie aufgefordert, ihr Zugriff auf Ihr Profil zu gewähren und sich anzumelden:

![Das Fenster „Angeforderte Berechtigungen“](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Anzeigen von Anwendungsergebnissen

Nach der Anmeldung werden Ihre Benutzerprofilinformationen in der angezeigten Antwort der Microsoft Graph-API zurückgegeben:

![Ergebnisse des Aufrufs der Microsoft Graph-API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Weitere Informationen zu Bereichen und delegierten Berechtigungen

Die Microsoft Graph-API benötigt den Bereich *user.read*, um das Benutzerprofil zu lesen. Dieser Bereich wird standardmäßig jeder Anwendung automatisch hinzugefügt, die beim Registrierungsportal registriert wird. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Die Microsoft Graph-API benötigt beispielsweise den Bereich *Mail.Read*, um die E-Mails des Benutzers aufzuführen.

> [!NOTE]
> Wenn Sie die Anzahl der Bereiche erhöhen, werden Benutzer ggf. zu weiteren Genehmigungen aufgefordert.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Vertiefen Sie Ihr Wissen über die SPA-Entwicklung mit Microsoft Identity Platform in der mehrteiligen Szenarioreihe.

> [!div class="nextstepaction"]
> [Szenario: Einseitige Anwendung](scenario-spa-overview.md)
