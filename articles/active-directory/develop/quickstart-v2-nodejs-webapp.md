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
ms.openlocfilehash: f7f14b91dc69eeba4ac06f6608f6151634dc38d3
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103497"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Schnellstart: Hinzufügen der Anmeldung mit OpenID Connect zu einer Node.js-Web-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie Sie die OpenID Connect-Authentifizierung in einer Webanwendung einrichten, die unter Verwendung von Node.js mit Express erstellt wurde. Das Beispiel ist für die Ausführung auf jeder beliebigen Plattform konzipiert.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Anwendung registrieren

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `MyWebApp`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
1. Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.

    Falls mehrere Umleitungs-URIs vorhanden sind, können Sie diese nach der erfolgreichen Erstellung der App dann später auf der Registerkarte **Authentifizierung** hinzufügen.

1. Wählen Sie **Registrieren** aus, um die App zu erstellen.
1. Suchen Sie auf der Seite **Übersicht** der App den Wert **Anwendungsclient-ID**, und notieren Sie ihn zur späteren Verwendung. Dieser Wert wird später in diesem Projekt benötigt, um die Anwendung zu konfigurieren.
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Wählen Sie **Plattform hinzufügen** > **Web** aus. 
1. Geben Sie im Abschnitt **Umleitungs-URIs** den URI `http://localhost:3000/auth/openid/return` ein.
1. Geben Sie `https://localhost:3000` als **URL für Front-Channel-Abmeldung** an.
1. Wählen Sie im Abschnitt **Implizite Genehmigung und Hybridflows** die Option **ID-Token** aus, da in diesem Beispiel der [Flow für implizite Genehmigungen](./v2-oauth2-implicit-grant-flow.md) aktiviert werden muss, um den Benutzer anzumelden.
1. Wählen Sie **Konfigurieren** aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **Zertifikate und Geheimnisse** > **Neuer geheimer Clientschlüssel**.
1. Geben Sie eine Schlüsselbeschreibung ein (beispielsweise „app secret“).
1. Wählen Sie eine Schlüsseldauer aus: **In 1 Jahr, In 2 Jahren** oder **Läuft nie ab**.
1. Wählen Sie **Hinzufügen** aus. Der Schlüsselwert wird angezeigt. Kopieren Sie den Schlüsselwert, und speichern Sie ihn zur späteren Verwendung an einem sicheren Ort.


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