---
title: 'Schnellstart: Hinzufügen einer Benutzeranmeldung zu einer Node.js-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie die Authentifizierung in einer Node.js-Webanwendung mit OpenID Connect implementieren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: 643305057490cc550a5a8e39a892297b000cbc8e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169408"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Schnellstart: Hinzufügen der Anmeldung mit OpenID Connect zu einer Node.js-Web-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie Sie die OpenID Connect-Authentifizierung in einer Webanwendung einrichten, die unter Verwendung von Node.js mit Express erstellt wurde. Das Beispiel ist für die Ausführung auf jeder beliebigen Plattform konzipiert.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Anwendung registrieren
1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com/) an.
1. Wenn Ihr Konto in mehreren Azure AD-Mandanten enthalten ist:
    - Wählen Sie im Menü rechts oben auf der Seite Ihr Profil und dann **Verzeichnis wechseln** aus.
    - Ändern Sie die Sitzung auf den Azure AD-Mandanten, bei dem Sie Ihre Anwendung erstellen möchten.

1. Navigieren Sie zu [Azure Active Directory > App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908), um Ihre App zu registrieren.

1. Wählen Sie **Neue Registrierung** aus.

1. Daraufhin wird die Seite **Anwendung registrieren** angezeigt. Geben Sie hier die Registrierungsinformationen Ihrer App ein:
    - Geben Sie im Abschnitt **Name** einen aussagekräftigen Namen ein. Dieser wird den Benutzern der App angezeigt. Beispiel: MyWebApp
    - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.

    Sind mehrere Umleitungs-URIs vorhanden, müssen diese nach erfolgreicher Erstellung der App auf der Registerkarte **Authentifizierung** hinzugefügt werden.

1. Wählen Sie **Registrieren** aus, um die App zu erstellen.

1. Suchen Sie auf der Seite **Übersicht** der App den Wert **Anwendungsclient-ID**, und notieren Sie ihn zur späteren Verwendung. Dieser Wert wird später in diesem Projekt benötigt, um die Anwendung zu konfigurieren.

1. Wählen Sie in der Liste mit den Seiten für die App die Option **Authentifizierung** aus.
    - Wählen Sie im Abschnitt **Umleitungs-URIs** im Kombinationsfeld die Option **Web** aus, und geben Sie den folgenden Umleitungs-URI ein: `http://localhost:3000/auth/openid/return`
    - Legen Sie im Abschnitt **Erweiterte Einstellungen** die Option **Abmelde-URL** auf `https://localhost:3000` fest.
    - Aktivieren Sie im Abschnitt **Erweiterte Einstellungen > Implizite Gewährung** die Option **ID-Token**, da in diesem Beispiel der [Flow für implizite Genehmigungen](./v2-oauth2-implicit-grant-flow.md) aktiviert werden muss, um den Benutzer anzumelden.

1. Wählen Sie **Speichern** aus.

1. Wählen Sie auf der Seite **Zertifikate & Geheimnisse** im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus.
    - Geben Sie eine Schlüsselbeschreibung ein (beispielsweise „app secret“).
    - Wählen Sie eine Schlüsseldauer aus: **In 1 Jahr, In 2 Jahren** oder **Läuft nie ab**.
    - Wenn Sie auf die Schaltfläche **Hinzufügen** klicken, wird der Schlüsselwert angezeigt. Kopieren Sie den Schlüsselwert, und speichern Sie ihn an einem sicheren Ort.

    Dieser Schlüssel wird später benötigt, um die Anwendung zu konfigurieren. Der Schlüsselwert wird nicht erneut angezeigt und kann auch nicht auf andere Weise abgerufen werden. Erfassen Sie ihn daher, sobald er im Azure-Portal angezeigt wird.

## <a name="download-the-sample-application-and-modules"></a>Herunterladen der Beispielanwendung und der Module

Klonen Sie als Nächstes das Beispielrepository, und installieren Sie die NPM-Module.

Verwenden Sie in Ihrer Shell oder Befehlszeile Folgendes:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

oder

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Führen Sie im Stammverzeichnis des Projekts den folgenden Befehl aus:

`$ npm install`

## <a name="configure-the-application"></a>Konfigurieren der Anwendung

Geben Sie in „config.js“ die Parameter in `exports.creds` gemäß den Anweisungen an.

* Aktualisieren Sie `<tenant_name>` in `exports.identityMetadata` mit dem Azure AD-Mandantennamen im Format „\*.onmicrosoft.com“.
* Aktualisieren Sie `exports.clientID` mit der Anwendungs-ID aus der App-Registrierung.
* Aktualisieren Sie `exports.clientSecret` mit dem Anwendungsgeheimnis aus der App-Registrierung.
* Aktualisieren Sie `exports.redirectUrl` mit dem Umleitungs-URI aus der App-Registrierung.

**Optionale Konfiguration für Produktions-Apps:**

* Aktualisieren Sie `exports.destroySessionUrl` in „config. js“, wenn nach der Abmeldung ein anderer Umleitungs-URI verwendet werden soll (`post_logout_redirect_uri`).

* Legen Sie `exports.useMongoDBSessionStore` in „config.js“ auf „true“ fest, wenn Sie [MongoDB](https://www.mongodb.com) oder andere [kompatible Sitzungsspeicher](https://github.com/expressjs/session#compatible-session-stores) verwenden möchten.
In diesem Beispiel wird `express-session` als Standardsitzungsspeicher verwendet. Der Standardsitzungsspeicher ist nicht für die Produktion geeignet.

* Aktualisieren Sie `exports.databaseUri`, wenn Sie den MongoDB-Sitzungsspeicher und einen anderen Datenbank-URI verwenden möchten.

* Aktualisieren Sie `exports.mongoDBSessionMaxAge`. Hier können Sie angeben, wie lange eine Sitzung in MongoDB erhalten bleiben soll. Der Wert wird in Sekunden angegeben.

## <a name="build-and-run-the-application"></a>Erstellen und Ausführen der Anwendung

Starten Sie den MongoDB-Dienst. Falls Sie in dieser App den MongoDB-Sitzungsspeicher verwenden, müssen Sie erst [MongoDB](http://www.mongodb.org/) installieren und den Dienst starten. Bei Verwendung des Standardsitzungsspeichers kann dieser Schritt übersprungen werden.

Führen Sie in der Befehlszeile den folgenden Befehl aus, um die App auszuführen:

```
$ node app.js
```

**Ist die Serverausgabe schwer zu verstehen?** In diesem Beispiel wird `bunyan` für die Protokollierung verwendet. Die Konsolenausgabe ist für Sie nur dann aufschlussreich, wenn Sie bunyan installieren, den Server wie oben angegeben ausführen und ihn per Pipe-Zeichen durch die bunyan-Binärdatei schleusen:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Sie haben es geschafft!

Nun verfügen Sie über einen Server, der erfolgreich unter `http://localhost:3000` ausgeführt wird.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu dem von Microsoft Identity Platform unterstützten Web-App-Szenario finden Sie unter:
> [!div class="nextstepaction"]
> [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md)