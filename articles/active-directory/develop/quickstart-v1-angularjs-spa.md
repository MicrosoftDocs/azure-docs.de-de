---
title: Erstellen einer AngularJS-Single-Page-App für das An- und Abmelden mit Azure Active Directory | Microsoft Docs
description: Erfahren Sie, wie eine AngularJS-Single-Page-Webanwendung erstellt wird, die für die Anmeldung in Azure AD integriert wird und über OAuth durch Azure AD geschützte APIs aufruft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a1fdbcd04504181a20f5245b6f2378be5b9d405
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001212"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Schnellstart: Erstellen einer AngularJS-Single-Page-Webanwendung für das An- und Abmelden mit Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Mit Azure Active Directory (Azure AD) ist es einfach und problemlos möglich, Ihren einseitigen Apps die Anmeldung, die Abmeldung und sichere OAuth-API-Aufrufe hinzuzufügen. Ihre Apps können Benutzer dann über ihre Windows Server Active Directory-Konten authentifizieren und jede per Azure AD geschützte Web-API nutzen, z. B. die Office 365-APIs oder die Azure-API.

Für JavaScript-Anwendungen, die in einem Browser ausgeführt werden, stellt Azure AD die Active Directory Authentication Library( ADAL) bzw. „adal.js“ bereit. Die einzige Aufgabe von „adal.js“ besteht darin, Ihrer App das Abrufen von Zugriffstoken zu erleichtern.

In diesem Schnellstart erfahren Sie, wie Sie eine AngularJS-Aufgabenlistenanwendung erstellen, die die folgenden Aufgaben ausführt:

* Anmelden von Benutzern an der App mit Azure AD als Identitätsanbieter
* Anzeigen einiger Informationen zum Benutzer
* Sicheres Aufrufen der Aufgabenlisten-API einer App mit Bearertoken aus Azure AD
* Abmelden von Benutzern von der App

Zur Entwicklung einer vollständigen, funktionstüchtigen Anwendung müssen Sie folgende Schritte ausführen:

1. Registrieren Sie Ihre App in Azure AD.
2. Installieren Sie die ADAL, und konfigurieren Sie die einseitige Anwendung.
3. Verwenden Sie die ADAL, um Seiten in der einseitigen Anwendung zu schützen.

> [!NOTE]
> Wenn Sie die Anmeldung über persönliche Konten zusätzlich zu Geschäfts-, Schul- oder Unikonten aktivieren müssen, können Sie den *[Microsoft Identity Platform-Endpunkt](azure-ad-endpoint-comparison.md)* verwenden. Weitere Informationen finden Sie in [diesem JavaScript SPA-Tutorial](tutorial-v2-javascript-spa.md) und in [diesem Artikel](active-directory-v2-limitations.md), der eine Beschreibung des *Microsoft Identity Platform-Endpunkts* enthält. 

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zum Einstieg die folgenden erforderlichen Schritte aus:

* Laden Sie das [Anwendungsgerüst](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) oder [vollständige Beispiel](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) herunter.
* Stellen Sie einen Azure AD-Mandanten bereit, in dem Sie Benutzer erstellen und Ihre Anwendung registrieren können. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Schritt 1: Registrieren der Anwendung DirectorySearcher

Sie müssen die App zuerst in Ihrem Azure AD-Mandanten registrieren, um damit Benutzer authentifizieren und Token abrufen zu können:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie in mehreren Verzeichnissen angemeldet sind, müssen Sie ggf. prüfen, ob Sie das richtige Verzeichnis anzeigen. Klicken Sie dazu auf der oberen Leiste auf Ihr Konto. Wählen Sie in der **Verzeichnisliste** den Azure AD-Mandanten aus, bei dem Sie Ihre Anwendung registrieren möchten.
1. Klicken Sie im linken Bereich auf **Alle Dienste**, und wählen Sie **Azure Active Directory** aus.
1. Klicken Sie auf **App-Registrierungen**, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** einen Namen für Ihre Anwendung ein.
1. Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
1. Wählen Sie die **Webplattform** unter dem Abschnitt **Umleitungs-URI** aus, und legen Sie den Wert auf `https://localhost:44326/` (den Speicherort, an den Azure AD Token zurückgibt) fest.
1. Wenn Sie so weit sind, klicken Sie auf **Registrieren**. Notieren Sie sich auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID**.
1. Für „adal.js“ wird der implizite OAuth-Fluss genutzt, um mit Azure AD zu kommunizieren. Sie müssen den impliziten Fluss für Ihre Anwendung aktivieren. Wählen Sie im linken Navigationsbereich der registrierten Anwendung **Authentifizierung** aus.
1. Aktivieren Sie unter **Erweiterte Einstellungen** und **Implizite Gewährung** die Kontrollkästchen **ID-Token** und **Zugriffstoken**. ID-Token und Zugriffstoken sind erforderlich, da diese App Benutzer anmelden und eine API aufrufen muss.
1. Wählen Sie **Speichern** aus.
1. Erteilen Sie in Ihrem gesamten Mandanten Berechtigungen für Ihre Anwendung. Wechseln Sie zu **API-Berechtigungen**, und wählen Sie unter **Administratoreinwilligung erteilen** die Schaltfläche **Einwilligung erteilen** aus.
1. Klicken Sie auf **Ja**, um zu bestätigen.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Schritt 2: Installieren von ADAL und Konfigurieren der Single-Page-Webanwendung

Nachdem Sie nun eine Anwendung in Azure AD erstellt haben, können Sie „adal.js“ installieren und Ihren identitätsbezogenen Code schreiben.

### <a name="configure-the-javascript-client"></a>Konfigurieren des JavaScript-Clients

Beginnen Sie, indem Sie „adal.js“ über die Paket-Manager-Konsole dem Projekt mit der einseitigen Aufgabenlisten-Anwendung (TodoSPA) hinzufügen:

1. Laden Sie [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) herunter, und fügen Sie die Datei dem Projektverzeichnis `App/Scripts/` hinzu.
2. Laden Sie [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) herunter, und fügen Sie die Datei dem Projektverzeichnis `App/Scripts/` hinzu.
3. Laden Sie jedes Skript vor dem Ende von `</body>` in `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurieren des Back-End-Servers

Damit die Back-End-Aufgabenlisten-API der einseitigen Anwendung Token aus dem Browser akzeptieren kann, benötigt das Back-End Konfigurationsinformationen zur App-Registrierung. Öffnen Sie `web.config`im Projekt mit der einseitigen Aufgabenlisten-Anwendung. Ersetzen Sie die Werte der Elemente im Abschnitt `<appSettings>` durch die Werte, die Sie im Azure-Portal verwendet haben. Sobald Ihr Code ADAL verwendet, verweist er auf diese Werte.

   * `ida:Tenant` ist die Domäne Ihres Azure AD-Mandanten, z. B. „contoso.onmicrosoft.com“.
   * `ida:Audience` ist die Client-ID der Anwendung, die Sie aus dem Portal kopiert haben.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Schritt 3: Verwenden von ADAL zum Schützen von Seiten in der Single-Page-Webanwendung

„adal.js“ wurde für die Integration in die AngularJS-Route und in HTTP-Anbieter erstellt, damit Sie einzelne Ansichten in Ihrer einseitigen Anwendung schützen können.

1. Binden Sie in `App/Scripts/app.js` das Modul „adal.js“ ein:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Initialisieren Sie `adalProvider` mit den Konfigurationswerten Ihrer Anwendungsregistrierung, ebenfalls in `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Zum Schützen der Ansicht `TodoList` in der App ist nur eine Codezeile erforderlich: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Zusammenfassung

Sie verfügen jetzt über eine sichere einseitige Anwendung, mit der Benutzer angemeldet und per Bearertoken geschützte Anforderungen für die Back-End-API ausgegeben werden können. Wenn ein Benutzer nun auf den Link **TodoList** klickt, erfolgt durch „adal.js“ automatisch eine Umleitung zu Azure AD, um bei Bedarf die Anmeldung auszuführen. Außerdem ordnet „adal.js“ allen AJAX-Anforderungen, die an das Back-End der Anwendung gesendet werden, automatisch ein Zugriffstoken zu.

Die obigen Angaben beschreiben die erforderlichen Mindestvoraussetzungen zum Erstellen einer einseitigen Anwendung per „adal.js“. Einige andere Features sind jedoch in einseitigen Anwendungen nützlich:

* Zum expliziten Ausgeben von Anmeldungs- und Abmeldungsanforderungen können Sie Funktionen in Ihren Controllern definieren, mit denen „adal.js“ aufgerufen wird. In `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Möglicherweise möchten Sie in der App-UI Benutzerinformationen präsentieren. Der ADAL-Dienst wurde dem `userDataCtrl`-Controller bereits hinzugefügt, sodass Sie auf das `userInfo`-Objekt in der zugeordneten Ansicht (`App/Views/UserData.html`) zugreifen können:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* In manchen Fällen möchten Sie überprüfen, ob der Benutzer angemeldet ist. Sie können auch das `userInfo` -Objekt verwenden, um diese Informationen zu sammeln. In `index.html` können Sie beispielsweise je nach Authentifizierungsstatus entweder die Schaltfläche **Anmelden** oder **Abmelden** anzeigen:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Ihre in Azure AD integrierte einseitige Anwendung kann Benutzer authentifizieren, das Back-End mit OAuth 2.0 sicher aufrufen und grundlegende Informationen zum Benutzer abrufen. Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen. Führen Sie Ihre einseitige Aufgabenlistenanwendung aus, und melden Sie sich mit einem der Benutzer an. Fügen Sie der Aufgabenliste des Benutzers Aufgaben hinzu, melden Sie sich ab, und melden Sie sich dann wieder an.

Adal.js erleichtert Ihnen die Integration allgemeiner Identitätsfunktionen in Ihrer Anwendung. Es übernimmt die unangenehmen Verwaltungsarbeiten für Sie – die Cacheverwaltung, die Unterstützung des OAuth-Protokolls, die Anzeige einer Anmeldeschnittstelle für den Benutzer, die Aktualisierung abgelaufener Tokens und vieles mehr.

Das vollständige Beispiel (ohne Ihre Konfigurationswerte) steht in [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

Sie können sich nun weiteren Szenarien zuwenden.

> [!div class="nextstepaction"]
> [Aufrufen einer CORS-Web-API aus einer Single-Page-App](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
