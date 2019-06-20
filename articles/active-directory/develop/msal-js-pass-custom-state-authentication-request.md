---
title: Übergeben eines benutzerdefinierten state-Parameters in Authentifizierungsanforderungen (Microsoft Authentication Library für JavaScript) | Azure
description: Erfahren Sie, wie Sie einen benutzerdefinierten state-Parameterwert in einer Authentifizierungsanforderung mithilfe der Microsoft Authentication Library für JavaScript (MSAL.js) übergeben.
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
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420408"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Übergeben eines benutzerdefinierten state-Parameters in Authentifizierungsanforderungen mithilfe von MSAL.js
Der *state*-Parameter gemäß OAuth 2.0 ist in einer Authentifizierungsanforderung enthalten und wird auch in der Tokenantwort zurückgegeben, um Angriffe in Form siteübergreifender Anforderungsfälschungen zu verhindern. Die Microsoft Authentication Library für JavaScript (MSAL.js) übergibt standardmäßig einen zufällig generierten eindeutigen *state*-Parameterwert in Authentifizierungsanforderungen.

Der state-Parameter kann auch verwendet werden, um vor der Umleitung Zustandsinformationen zur App zu codieren. Sie können den Zustand des Benutzers in der App, z. B. die Seite oder Ansicht, zu der er navigiert ist, als Eingabe für diesen Parameter übergeben. Die MSAL.js-Bibliothek bietet die Möglichkeit, Ihren benutzerdefinierten Zustand als state-Parameter im `Request`-Objekt zu übergeben:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
};
```

Beispiel:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

Der übergebene Zustand wird an die durch MSAL.js festgelegte eindeutige GUID angefügt, wenn die Anforderung gesendet wird. Wenn die Antwort zurückgegeben wird, überprüft MSAL.js, ob Zustände übereinstimmen, und gibt dann den übergebenen benutzerdefinierten Zustand im `Response`-Objekt als `accountState` zurück.

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Weitere Informationen finden Sie im Thema zum [Erstellen einer Single-Page-Anwendung (SPA)](scenario-spa-overview.md) mithilfe von MSAL.js.