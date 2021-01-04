---
title: 'Tutorial: Schützen einer Node.js-Web-API mithilfe von Azure AD B2C und Gewähren von Zugriff auf eine Single-Page-Webanwendung (Single-Page Application, SPA)'
titleSuffix: Azure AD B2C
description: In diesem Tutorial erfahren Sie, wie Sie mit Active Directory B2C eine Node.js-Web-API schützen und sie über eine Single-Page-Webanwendung aufrufen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 737810a7d07d0d97b2e42acffa17fdd32986c48b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421089"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Schützen einer Node.js-Web-API und Gewähren von Zugriff auf eine Node.js-Web-API über eine Single-Page-Webanwendung mithilfe von Azure AD B2C

In diesem Tutorial erfahren Sie, wie Sie eine durch Azure Active Directory B2C (Azure AD B2C) geschützte Node.js-Web-API über eine Single-Page-Webanwendung aufrufen.

Dieses Tutorial ist der zweite Teil einer zweiteiligen Reihe und beinhaltet Folgendes:

> [!div class="checklist"]
> * Erstellen einer Web-API-Anwendungsregistrierung in Ihrem Azure AD B2C-Mandanten
> * Konfigurieren von Bereichen für die Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Ändern eines Web-API-Codebeispiels zur Verwendung mit Ihrem Mandanten

Im [ersten Tutorial](tutorial-single-page-app.md) dieser Reihe haben Sie das Codebeispiel heruntergeladen und so geändert, dass Benutzer mit einem Benutzerablauf bei Ihrem Azure AD B2C-Mandanten angemeldet werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Führen Sie die erforderlichen Schritte unter [Tutorial: Aktivieren der Authentifizierung in einer Single-Page-Webanwendung mit Azure AD B2C](tutorial-single-page-app.md) aus.
* [Visual Studio Code](https://code.visualstudio.com/) oder ein anderer Code-Editor
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Hinzufügen einer Web-API-Anwendung

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. So können beispielsweise einige Benutzer Lese- und Schreibzugriff haben, während andere Benutzer nur über Leseberechtigungen verfügen. In diesem Tutorial definieren Sie Lese- und Schreibberechtigungen für die Web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Notieren Sie sich den Wert unter **Bereiche** für den Bereich `demo.read`. Er wird in einem späteren Schritt bei der Konfiguration der Single-Page-Webanwendung verwendet. Der vollständige Bereichswert ist ähnlich wie bei `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Wenn Sie über eine andere Anwendung eine geschützte Web-API aufrufen möchten, müssen Sie der Anwendung Berechtigungen für die Web-API erteilen.

Im vorbereitenden Tutorial haben Sie eine Single-Page-Webanwendung namens *spaapp1* erstellt. In diesem Tutorial konfigurieren Sie diese Anwendung so, dass sie die Web-API aufruft, die Sie in einem vorherigen Abschnitt erstellt haben (*spaapp1*).

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Ihre Single-Page-Webanwendung verfügt nun über Berechtigungen für die geschützte Web-API für die angegebenen Bereiche. Ein Benutzer authentifiziert sich mit Azure AD B2C, um die Single-Page-Webanwendung zu verwenden. Die Single-Page-Webanwendung ruft ein Zugriffstoken aus Azure AD B2C ab, um auf die geschützte Web-API zuzugreifen.

## <a name="configure-the-sample"></a>Konfigurieren des Beispiels

Nachdem Sie die Web-API registriert und Bereiche definiert haben, konfigurieren Sie als Nächstes den Web-API-Code für die Verwendung mit Ihrem Azure AD B2C-Mandanten. In diesem Tutorial wird eine exemplarische Node.js-Web-API konfiguriert, die auf GitHub zum Download bereitsteht.

[Laden Sie ein \*ZIP-Archiv](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) herunter, oder klonen Sie das Beispiel-Web-API-Projekt von GitHub. Sie können auch direkt zum Projekt [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) auf GitHub navigieren.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurieren der Web-API

1. Öffnen Sie die Datei *config.json* in Ihrem Code-Editor.
1. Passen Sie die Variablenwerte an die zuvor erstellte Anwendungsregistrierung an. Aktualisieren Sie außerdem den Richtliniennamen (`policyName`) mit dem Benutzerablauf, den Sie im Rahmen der Voraussetzungen erstellt haben. Beispiel: *B2C_1_signupsignin1*.
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "B2C_1_signupsignin1"
    },
    "resource": {
        "scope": ["demo.read"] 
    },
    ```

#### <a name="enable-cors"></a>Aktivieren von CORS

Damit Ihre Single-Page-Webanwendung die Node.js-Web-API aufrufen kann, müssen Sie [CORS](https://expressjs.com/en/resources/middleware/cors.html) in der Web-API aktivieren. In einer Produktionsanwendung muss sorgfältig darauf geachtet werden, von welcher Domäne die Anforderung stammt. In diesem Tutorial können dagegen Anforderungen von beliebigen Domänen zugelassen werden.

Verwenden Sie die folgende Middleware, um CORS zu aktivieren. Im Node.js-Web-API-Codebeispiel in diesem Tutorial wurde sie bereits der Datei *index.js* hinzugefügt.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Konfigurieren der Single-Page-Webanwendung

Die Single-Page-Webanwendung (Single-Page Application, SPA) aus dem [vorherigen Tutorial](tutorial-single-page-app.md) der Reihe verwendet Azure AD B2C für die Benutzerregistrierung und -anmeldung und ruft standardmäßig die Node.js-Web-API auf, die durch den Demomandanten *fabrikamb2c* geschützt wird.

In diesem Abschnitt wird die Single-Page-Webanwendung aktualisiert, um die Node.js-Web-API aufzurufen, die durch *Ihren* Azure AD B2C-Mandanten geschützt wird (und die Sie auf Ihrem lokalen Computer ausführen).

So ändern Sie die Einstellungen in der SPA

1. Öffnen Sie im Projekt [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa], das Sie im vorherigen Tutorial heruntergeladen oder geklont haben, die Datei *apiConfig.js* im Ordner *JavaScriptSPA*.
1. Konfigurieren Sie das Beispiel mit dem URI für den zuvor erstellten Bereich *demo.read* und der URL der Web-API.
    1. Ersetzen Sie in der Definition von `apiConfig` den Wert für `b2cScopes` durch den vollständigen URI für den Bereich *demo.read* (der Wert für **Bereich**, den Sie sich weiter oben notiert haben).
    1. Ändern Sie die Domäne im Wert für `webApi` in den Umleitungs-URI, den Sie zuvor beim Registrieren der Web-API-Anwendung hinzugefügt haben.

    Da auf die API am Endpunkt `/hello` zugegriffen werden kann, behalten Sie */hello* im URI bei.

    Die Definition von `apiConfig` sollte in etwa wie im folgenden Codeblock aussehen (mit dem Namen Ihres B2C-Mandanten anstelle von `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Ausführen der SPA und der Web-API

Nun können Sie den bereichsbezogenen API-Zugriff der Single-Page-Webanwendung testen. Führen Sie sowohl die Node.js-Web-API als auch die exemplarische JavaScript-Single-Page-Webanwendung auf Ihrem lokalen Computer aus. Melden Sie sich anschließend bei der Single-Page-Webanwendung an, und wählen Sie die Schaltfläche **API aufrufen** aus, um eine Anforderung an die geschützte API zu initiieren.

In diesem Tutorial werden zwar beide Anwendungen lokal ausgeführt, aufgrund ihrer Konfiguration verwenden sie jedoch Azure AD B2C, um eine sichere Registrierung/Anmeldung zu ermöglichen und um Zugriff auf die geschützte Web-API zu gewähren.

### <a name="run-the-nodejs-web-api"></a>Ausführen der Node.js-Web-API

1. Öffnen Sie ein Konsolenfenster, und wechseln Sie zum Verzeichnis mit dem Node.js-Web-API-Beispiel. Beispiel:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Führen Sie die folgenden Befehle aus:

    ```console
    npm install && npm update
    node index.js
    ```

    Im Konsolenfenster wird die Portnummer angezeigt, unter der die Anwendung gehostet wird.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Ausführen der Single-Page-Webanwendung

1. Öffnen Sie ein weiteres Konsolenfenster, und wechseln Sie zum Verzeichnis mit dem JavaScript-SPA-Beispiel. Beispiel:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Führen Sie die folgenden Befehle aus:

    ```console
    npm install && npm update
    npm start
    ```

    Im Konsolenfenster wird die Portnummer angezeigt, unter der die Anwendung gehostet wird.

    ```console
    Listening on port 6420...
    ```

1. Navigieren Sie in Ihrem Browser zu `http://localhost:6420`, um die Anwendung anzuzeigen.

    ![Beispiel-App für Singe-Page-Anwendung, die im Browser angezeigt wird](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Melden Sie sich mit der E-Mail-Adresse und dem Kennwort an, die Sie im [vorherigen Tutorial](tutorial-single-page-app.md) verwendet haben. Nach erfolgreicher Anmeldung sollte die Meldung `User 'Your Username' logged-in` angezeigt werden.
1. Wählen Sie die Schaltfläche **API aufrufen** aus. Die SPA erhält eine Autorisierungsgewährung von Azure AD B2C und greift dann auf die geschützte Web-API zu, um den Namen des angemeldeten Benutzers anzuzeigen:

    ![Single-Page-Webanwendung im Browser mit dem von der API zurückgegebenen Benutzernamen (JSON-Ergebnis)](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie folgende Schritte ausgeführt:

> [!div class="checklist"]
> * Erstellen einer Web-API-Anwendungsregistrierung in Ihrem Azure AD B2C-Mandanten
> * Konfigurieren von Bereichen für die Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Ändern eines Web-API-Codebeispiels zur Verwendung mit Ihrem Mandanten

Nachdem Sie jetzt erlebt haben, wie eine SPA eine Ressource von einer geschützten Web-API anfordert, gewinnen Sie ein tieferes Verständnis dafür, wie diese Anwendungstypen miteinander und mit Azure AD B2C interagieren.

> [!div class="nextstepaction"]
> [In Azure Active Directory B2C verwendbare Anwendungstypen](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
