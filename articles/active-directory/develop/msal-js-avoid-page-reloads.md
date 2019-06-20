---
title: Vermeiden des erneuten Ladens von Seiten (Microsoft Authentication Library für JavaScript) | Azure
description: Erfahren Sie, wie Sie das erneute Laden von Seiten vermeiden, wenn Sie Token mithilfe der Microsoft Authentication Library für JavaScript (MSAL.js) automatisch abrufen und erneuern.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420404"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Vermeiden des erneuten Ladens von Seiten, wenn Sie Token mithilfe von MSAL.js automatisch abrufen und erneuern
Die Microsoft Authentication Library für JavaScript (MSAL.js) verwendet ausgeblendete `iframe`-Elemente, um Token automatisch im Hintergrund abzurufen und zu erneuern. Azure AD gibt das Token wieder an den registrierten redirect_uri zurück, der in der Tokenanforderung angegeben ist (dies ist standardmäßig die Stammseite der App). Da es sich bei der Antwort um den Statuscode 302 handelt, wird folglich der HTML-Code, der dem `redirect_uri` entspricht, in `iframe` geladen. Normalerweise entspricht der App-`redirect_uri` der Stammseite, was dazu führt, dass sie erneut geladen wird.

In anderen Fällen, in denen das Navigieren zur Stammseite der App eine Authentifizierung erfordert, kann dies zu einer Schachtelung von `iframe`-Elementen oder zu einem `X-Frame-Options: deny`-Fehler führen.

Da MSAL.js den von Azure AD ausgegebenen Statuscode 302 nicht verwerfen kann und das zurückgegebene Token verarbeiten muss, kann nicht verhindert werden, dass der `redirect_uri` in `iframe` geladen wird.

Um zu verhindern, dass die gesamte App erneut geladen wird oder andere Fehler auftreten, verwenden Sie die folgenden Problemumgehungen.

## <a name="specify-different-html-for-the-iframe"></a>Angeben eines anderen HTML-Codes für „iframe“

Legen Sie die `redirect_uri`-Eigenschaft in der Konfigurationsdatei auf eine einfache Seite fest, die keine Authentifizierung erfordert. Stellen Sie sicher, dass sie zu dem im Azure-Portal registrierten `redirect_uri` passt. Dies hat keine Auswirkungen auf die Benutzeranmeldung, da die Startseite von MSAL zu Beginn des Anmeldeprozesses gespeichert wird und nach Abschluss der Anmeldung wieder eine Umleitung an den genauen Ausgangsort stattfindet.

## <a name="initialization-in-your-main-app-file"></a>Initialisierung in der App-Hauptdatei

Wenn die App so strukturiert ist, dass die App-Initialisierung, das Routing und andere Vorgänge in einer zentralen JavaScript-Datei definiert sind, können Sie die App-Module bedingt laden, je nachdem, ob die App in `iframe` geladen wird oder nicht. Beispiel:

In AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

In Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie im Thema zum [Erstellen einer Single-Page-Anwendung (SPA)](scenario-spa-overview.md) mithilfe von MSAL.js.