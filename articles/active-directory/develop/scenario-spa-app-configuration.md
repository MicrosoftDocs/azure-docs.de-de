---
title: Konfigurieren einer Single-Page-App – Microsoft Identity Platform | Azure
description: Informationen zum Erstellen einer Single-Page-Webanwendung (Codekonfiguration der App)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 30a9b710ffbf98ebc523217a3b8a7fd9a2640c49
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443040"
---
# <a name="single-page-application-code-configuration"></a>Single-Page-Webanwendung: Codekonfiguration

Erfahren Sie, wie Sie den Code für Ihre Single-Page-Webanwendung (SPA) konfigurieren.

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>MSAL-Bibliotheken für SPAs und unterstützte Authentifizierungsflows

Microsoft Identity Platform stellt die folgende Microsoft Authentication Library für JavaScript (MSAL.js) zur Unterstützung des impliziten Flows und des Autorisierungscodeflows mit PKCE mithilfe von empfohlenen branchenüblichen Sicherheitsmethoden bereit:

| MSAL-Bibliothek | Flow | BESCHREIBUNG |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2.x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Autorisierungscodeflow (PKCE) | Einfache JavaScript-Bibliothek zur Verwendung in einer beliebigen clientseitigen Web-App, die über JavaScript- oder SPA-Frameworks wie Angular, Vue.js und React.js erstellt wurde. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1.x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Impliziter Flow | Einfache JavaScript-Bibliothek zur Verwendung in einer beliebigen clientseitigen Web-App, die über JavaScript- oder SPA-Frameworks wie Angular, Vue.js und React.js erstellt wurde. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Impliziter Flow | Wrapper der MSAL.js-Kernbibliothek für eine einfachere Verwendung in Single-Page-Webanwendungen, die über das Angular-Framework erstellt wurden. |

## <a name="application-code-configuration"></a>Codekonfiguration der Anwendung

In einer MSAL-Bibliothek werden die Anwendungsregistrierungsinformationen während der Initialisierung der Bibliothek als Konfiguration übergeben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Weitere Informationen zu den konfigurierbaren Optionen finden Sie unter [Initialisieren von Clientanwendungen mithilfe von MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Anmelden und Abmelden](scenario-spa-sign-in.md).
