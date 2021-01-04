---
title: 'Web-App-Tutorial: Schreiben einer Webanwendung'
description: Dieses Tutorial führt Sie Schritt für Schritt durch ein Beispiel für die Bereitstellung einer einfachen Webanwendung. In diesem Abschnitt des Tutorials wird das Schreiben der Webanwendung erläutert.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023294"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Schreiben einer Azure-Webanwendung zum Lesen von FHIR-Daten
Sie können eine Verbindung mit Ihrem FHIR-Server und den POST-Daten herstellen und nun eine Webanwendung zum Lesen von FHIR-Daten schreiben. In diesem abschließenden Schritt des Tutorials wird das Schreiben einer Webanwendung und das Zugreifen darauf erläutert.

## <a name="create-web-application"></a>Erstellen einer Webanwendung
Wählen Sie in Azure **Ressource erstellen** und **Web-App** aus. Geben Sie der Webanwendung unbedingt den Namen, den Sie im Umleitungs-URI für Ihre Clientanwendung angegeben haben, oder gehen Sie zurück, und ersetzen Sie den Umleitungs-URI durch den neuen Namen. 

![Erstellen einer Webanwendung](media/tutorial-web-app/create-web-app.png)

Wenn die Webanwendung verfügbar ist, **navigieren Sie zur Ressource**. Wählen Sie unter „Entwicklungstools“ auf der rechten Seite die Option **App Service-Editor (Vorschau)** und dann **Los** aus. Wenn Sie „Los“ auswählen, wird der App Service-Editor geöffnet. Klicken Sie mit der rechten Maustaste auf den grauen Bereich unter *Durchsuchen*, und erstellen Sie eine neue Datei namens **index.html**.

Weiter unten sehen Sie den Code, den Sie in **index.html** eingeben können. Sie müssen die folgenden Angaben aktualisieren:
* **clientId**: Geben Sie Ihre Clientanwendungs-ID ein. Dabei handelt es sich um dieselbe ID, die Sie beim Abrufen des Tokens abgerufen haben.
* **authority**: Aktualisieren Sie die Angabe mit Ihrer Azure AD-Mandanten-ID.
* **FHIRendpoint**: Geben Sie für FHIRendpoint Ihren FHIR-Dienstnamen an.
* **scopes**: Ersetzen Sie diesen Wert durch die vollständige URL für Ihre Zielgruppe.

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

Von hier aus können Sie zu Ihrer Webanwendungsressource zurückkehren und die URL auf der Übersichtsseite öffnen. Melden Sie sich an, um den von Ihnen zuvor erstellten Patienten James Tiberious Kirk anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte
Sie haben erfolgreich Azure API for FHIR bereitgestellt, eine öffentliche Clientanwendung registriert, den Zugriff getestet und eine kleine Webanwendung erstellt. Im nächsten Schritt erfahren Sie mehr über die von Azure API for FHIR unterstützten Features.

>[!div class="nextstepaction"]
>[Unterstützte Features](fhir-features-supported.md)





