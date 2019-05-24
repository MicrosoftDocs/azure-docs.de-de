---
title: Schützen einer Web-API mit Azure AD | Microsoft Docs
description: Erfahren Sie, wie Sie eine Node.js REST-Web-API erstellen, die für die Authentifizierung in Azure AD integriert wird.
services: active-directory
documentationcenter: nodejs
author: rwike77
manager: CelesteDG
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dcbe10c08d581e2ec01e1616da49b7a1b31018d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545977"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>Schnellstart: Schützen einer Web-API mit Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

In diesem Schnellstart erfahren Sie, wie ein [Restify](http://restify.com/)-API-Endpunkt mit [Passport](http://passportjs.org/) mithilfe des [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad)-Moduls zur Verarbeitung der Kommunikation mit Azure Active Directory (Azure AD) geschützt wird.

Dieser Schnellstart behandelt die Aspekte des Schützens von API-Endpunkten. Die Aspekte der Anmeldung und Aufbewahrung von Authentifizierungstoken sind hier nicht implementiert und gehören zur Verantwortlichkeit einer Clientanwendung. Näheres zu Clientimplementierungen erfahren Sie in [An- und Abmeldung bei NodeJS-Web-Apps mit Azure AD](quickstart-v1-openid-connect-code.md).

Das vollständige Codebeispiel zu diesem Artikel ist auf [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zunächst die folgenden erforderlichen Schritte aus:

### <a name="create-the-sample-project"></a>Erstellen des Beispielprojekts

Die Serveranwendung erfordert einige Paketabhängigkeiten zur Unterstützung von Restify und Passport sowie Kontoinformationen, die an Azure AD übergeben werden.

Kopieren Sie zu Anfang den folgenden Code in eine Datei namens `package.json`:

```Shell
{
  "name": "active-directory-webapi-nodejs",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "4.0.0",
    "restify": "7.7.0"
  }
}
```

Sobald `package.json` erstellt ist, führen Sie `npm install` in der Befehlszeile aus, um die Paketabhängigkeiten zu installieren.

#### <a name="configure-the-project-to-use-active-directory"></a>Konfigurieren des Projekts zur Verwendung von Active Directory

Um mit der Konfiguration der Anwendung zu beginnen, rufen Sie einige kontospezifische Werte mit der Azure CLI ab. Die einfachste Möglichkeit zum Einstieg in die CLI ist die Verwendung der Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Geben Sie in der Cloud Shell-Instanz folgenden Befehl ein:

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

Zu den [Argumenten](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) des `create`-Befehls zählen:

| Argument  | BESCHREIBUNG |
|---------|---------|
|`display-name` | Ein Anzeigename der Registrierung |
|`homepage` | Die URL, unter der sich Benutzer bei Ihrer Anwendung anmelden und sie nutzen können |
|`identifier-uris` | Durch Leerzeichen getrennte eindeutige URIs, die Azure AD für diese Anwendung verwenden kann |

Bevor Sie eine Verbindung mit Azure Active Directory herstellen können, benötigen Sie die folgenden Informationen:

| NAME  | BESCHREIBUNG | Variablenname in der Config-Datei |
| ------------- | ------------- | ------------- |
| Mandantenname  | Der [Mandantenname](quickstart-create-new-tenant.md), den Sie für die Authentifizierung verwenden möchten. | `tenantName`  |
| Client-ID  | Client-ID ist der OAuth-Begriff, der für die _AAD-Anwendungs-ID_ verwendet wird. |  `clientID`  |

Kopieren Sie aus der Registrierungsantwort in der Azure Cloud Shell den Wert `appId`, und erstellen Sie eine neue Datei namens `config.js`. Fügen Sie als Nächstes den folgenden Code hinzu, und ersetzen Sie ihre Werte durch die in Klammern gesetzten Token:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```

Um weitere Informationen zu den einzelnen Konfigurationseinstellungen zu erhalten, lesen Sie die Dokumentation zum [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage)-Modul.

### <a name="implement-the-server"></a>Implementieren des Servers

Das [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage)-Modul verfügt über zwei Authentifizierungsstrategien: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) und [Bearer](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy). Der in diesem Artikel implementierte Server verwendet die Bearer-Strategie zum Schützen des API-Endpunkts.

### <a name="step-1-import-dependencies"></a>Schritt 1: Importieren von Abhängigkeiten

Erstellen Sie eine neue Datei namens `app.js`, und fügen Sie folgenden Text ein:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require ('restify').plugins
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

In diesem Codeabschnitt:

- Auf `restify` und Plug-In-Module wird verwiesen, um einen Restify-Server einzurichten.
- Die Module `passport` und `passport-azure-ad` sind für die Kommunikation mit Azure AD verantwortlich.
- Die Variable `config` wird mit Werten aus der im vorherigen Schritt erstellten Datei `config.js` initialisiert.
- Für `authenticatedUserTokens` wird ein Array erstellt, um Benutzertoken zu speichern, wenn sie an geschützte Endpunkte übergeben werden.
- Der `serverPort` wird entweder vom Port der Prozessumgebung oder der Konfigurationsdatei aus definiert.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Schritt 2: Instanziieren einer Authentifizierungsstrategie

Beim Schützen eines Endpunkts müssen Sie eine Strategie bereitstellen, um zu ermitteln, ob die aktuelle Anforderung von einem authentifizierten Benutzer stammt. Hier ist die Variable `authenticatonStrategy` eine Instanz der `passport-azure-ad` `BearerStrategy`-Klasse. Fügen Sie nach den `require`-Anweisungen den folgenden Code hinzu.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```

Diese Implementierung verwendet die automatische Registrierung durch Hinzufügen von Authentifizierungstoken im `authenticatedUserTokens`-Array, wenn sie nicht bereits vorhanden sind.

Sobald eine neue Instanz der Strategie erstellt wurde, müssen Sie sie mit der `use`-Methode in Passport übergeben. Fügen Sie `app.js` folgenden Code zum Verwenden der Strategie in Passport hinzu.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Schritt 3: Serverkonfiguration

Sobald die Authentifizierungsstrategie definiert ist, können Sie den Restify-Server mithilfe einiger Grundeinstellungen einrichten und festlegen, dass er Passport für die Sicherheit verwendet.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Dieser Server wird zum Analysieren von Autorisierungsheadern initialisiert und konfiguriert und dann zur Verwendung von Passport eingerichtet.

### <a name="step-4-define-routes"></a>Schritt 4: Definieren von Routen

Jetzt können Sie Routen definieren und entscheiden, welche mit Azure AD geschützt werden. Dieses Projekt enthält zwei Routen, in denen die Stammebene geöffnet ist, und für die `/api`-Route ist festgelegt, dass eine Authentifizierung erforderlich ist.

Fügen Sie in `app.js` den folgenden Code für die Stammebenenroute hinzu:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Alle Anforderungen können über die Stammroute geleitet werden, und sie gibt eine Meldung zurück, die einen Befehl zum Testen der `/api`-Route enthält. Im Gegensatz dazu ist die `/api`-Route mit [`passport.authenticate` ](http://passportjs.org/docs/authenticate) gesperrt. Fügen Sie nach der Stammroute den folgenden Code hinzu.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Diese Konfiguration lässt nur authentifizierte Anforderungen zu, die einen Bearer-Token-Zugriff auf `/api` enthalten. Die Option `session: false` soll verhindern, dass Sitzungen fordern, dass mit jeder Anforderung an die API ein Token übergeben wird.

Schließlich wird durch Aufrufen der `listen`-Methode festgelegt, dass der Server auf den konfigurierten Port lauscht.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Jetzt ist der Server implementiert, und Sie können ihn starten, indem Sie eine Eingabeaufforderung öffnen und Folgendes eingeben:

```shell
npm start
```

Wenn der Server ausgeführt wird, können Sie eine Anforderung an ihn senden, um die Ergebnisse zu testen. Um die Antwort der Stammroute anzuzeigen, öffnen Sie eine Bash-Shell, und geben Sie den folgenden Code ein:

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

Wenn Sie Ihren Server ordnungsgemäß konfiguriert haben, sollte die Antwort folgendermaßen aussehen:

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Als Nächstes können Sie die Route testen, die Authentifizierung erfordert, indem Sie den folgenden Befehl in der Bash-Shell eingeben:

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

Wenn Sie den Server ordnungsgemäß konfiguriert haben, sollte er mit dem Status `Unauthorized` antworten.

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

Nun haben Sie eine sichere API erstellt und können einen Client implementieren, der Authentifizierungstoken an die API übergeben kann.

## <a name="next-steps"></a>Nächste Schritte

* Sie müssen ein Clientgegenstück zum Herstellen der Verbindung mit dem Server implementieren, das das Anmelden, Abmelden und Verwalten von Token verarbeitet. Codebasierte Beispiele finden Sie in den Clientanwendungen in [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) und [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android).
* Ein Schritt-für-Schritt-Tutorial finden Sie unter [An- und Abmeldung bei NodeJS-Web-Apps mit Azure AD](quickstart-v1-openid-connect-code.md)
