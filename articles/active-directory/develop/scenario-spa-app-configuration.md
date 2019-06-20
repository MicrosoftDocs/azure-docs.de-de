---
title: Single-Page-Webanwendung (Codekonfiguration der App) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Single-Page-Webanwendung (Codekonfiguration der App) erstellen
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65406435"
---
# <a name="single-page-application---code-configuration"></a>Single-Page-Webanwendung – Codekonfiguration

Erfahren Sie, wie Sie den Code für Ihre Single-Page-Webanwendung (SPA) konfigurieren.

## <a name="msal-libraries-supporting-implicit-flow"></a>MSAL-Bibliotheken zur Unterstützung des impliziten Flusses

Microsoft Identity Platform bietet eine MSAL.js-Bibliothek zur Unterstützung des impliziten Flusses unter Verwendung der für die Branche empfohlenen sicheren Methoden.  

Folgende Bibliotheken unterstützen den impliziten Fluss:

| MSAL-Bibliothek | BESCHREIBUNG |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Einfache JavaScript-Bibliothek zur Verwendung in einer beliebigen clientseitigen Web-App, die mit JavaScript- oder SPA-Frameworks wie Angular, Vue.js, React.js usw. erstellt wurde. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper der MSAL.js-Kernbibliothek für eine einfachere Verwendung in Single-Page-Webanwendungen, die mit dem Angular-Framework erstellt wurden. Diese Bibliothek ist als Vorschauversion verfügbar und weist [bekannte Probleme](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) mit bestimmten Angular-Versionen und Browsern auf. |

## <a name="application-code-configuration"></a>Codekonfiguration der Anwendung

In der MSAL-Bibliothek werden die Informationen der Anwendungsregistrierung während der Initialisierung der Bibliothek als Konfiguration übergeben.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Weitere Informationen zu den verfügbaren konfigurierbaren Optionen finden Sie unter [Initializing application with MSAL.js (Initialisieren von Anwendungen mit MSAL.js)](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [An- und Abmeldung](scenario-spa-sign-in.md)
