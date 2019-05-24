---
title: Einmaliges Anmelden (Microsoft-Authentifizierungsbibliothek für JavaScript) | Azure
description: Erfahren Sie etwas über das Erstellen von Umgebungen mit einmaligem Anmelden mithilfe der Microsoft-Authentifizierungsbibliothek für JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f1f102307256852ac92616c7fb707e0e2739e5d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544153"
---
# <a name="single-sign-on-with-msaljs"></a>Einmaliges Anmelden mit MSAL.js

Einmaliges Anmelden (Single Sign-On, SSO) ermöglicht Benutzern, ihre Anmeldeinformationen einmal für die Anmeldung zu verwenden und dann eine Sitzung einzurichten, die in mehreren Anwendungen wiederverwendet werden kann, ohne dass eine erneute Authentifizierung erforderlich wird. Damit wird den Benutzern ein nahtloses Erlebnis bereitgestellt, da sie nicht ständig zur Eingabe von Anmeldeinformationen aufgefordert werden.

Azure AD bietet SSO-Funktionen für Anwendungen, indem diese ein Sitzungscookie ablegen, wenn sich ein Benutzer das erste Mal authentifiziert. Die MSAL.js-Bibliothek ermöglicht Anwendungen die Nutzung dieser Funktionen auf verschiedene Weise.

## <a name="sso-between-browser-tabs"></a>Einmaliges Anmelden zwischen Browserregisterkarten

Wenn Ihre Anwendung in mehreren Registerkarten geöffnet ist und sich der Benutzer zunächst auf einer Registerkarte anmeldet, wird er auch auf den anderen Registerkarten angemeldet, ohne dazu aufgefordert zu werden. MSAL.js speichert das ID-Token für den Benutzer im `localStorage` des Browsers und meldet den Benutzer auf den anderen offenen Registerkarten bei der Anwendung an.

Standardmäßig verwendet MSAL.js `sessionStorage`, der keine gemeinsame Verwendung der Sitzung zwischen Registerkarten erlaubt. Um SSO zwischen Registerkarten zu ermöglichen, müssen Sie `cacheLocation` in MSAL.js auf `localStorage` festlegen, wie unten dargestellt.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Einmaliges Anmelden zwischen Apps

Beim Authentifizieren eines Benutzers wird in der Azure AD-Domäne im Browser ein Sitzungscookie festgelegt. MSAL.js benötigt dieses Sitzungscookie, um einmaliges Anmelden für den Benutzer zwischen verschiedenen Anwendungen bereitzustellen. Außerdem speichert MSAL.js die ID- und Zugriffstoken des Benutzers im Browserspeicher in den einzelnen Anwendungsdomänen zwischen. Daher weicht das SSO-Verhalten in verschiedenen Situationen ab:  

### <a name="applications-on-the-same-domain"></a>Anwendungen in derselben Domäne

Wenn Anwendungen in derselben Domäne gehostet werden, kann sich der Benutzer einmal bei einer App anmelden und wird dann ohne Aufforderung bei den anderen Apps authentifiziert. MSAL.js nutzt die für den Benutzer in der Domäne zwischengespeicherten Token, um einmaliges Anmelden bereitzustellen.

### <a name="applications-on-different-domain"></a>Anwendungen in unterschiedlichen Domänen

Wenn Anwendungen in unterschiedlichen Domänen gehostet werden, kann MSAL.js in Domäne B nicht auf Token zugreifen, die in Domäne A zwischengespeichert wurden.

Wenn also ein in Domäne A angemeldeter Benutzer zu einer Anwendung in Domäne B navigiert, wird er zur Azure AD-Seite umgeleitet oder zum Besuch dieser Seite aufgefordert. Da Azure AD noch über das Sitzungscookie für den Benutzer verfügt, meldet es den Benutzer an, sodass dieser seine Anmeldeinformationen nicht erneut eingeben muss. Wenn der Benutzer in der Azure AD-Sitzung über mehrere Benutzerkonten verfügt, wird er aufgefordert, das entsprechende Konto für die Anmeldung auszuwählen.

### <a name="automatically-select-account-on-azure-ad"></a>Automatisches Auswählen eines Kontos in Azure AD

In bestimmten Fällen hat die Anwendung Zugriff auf den Kontext der Benutzerauthentifizierung und möchte verhindern, dass der Benutzer zur Auswahl des Azure AD-Kontos aufgefordert wird, wenn mehrere Konten angemeldet sind.  Dies kann auf unterschiedliche Weise erfolgen:

**Mit der Sitzungs-ID (SID)**

Die Sitzungs-ID ist ein [optionaler Anspruch](active-directory-optional-claims.md), der in den ID-Token konfiguriert werden kann. Dieser Anspruch ermöglicht der Anwendung, die Azure AD-Sitzung des Benutzers zu ermitteln – und zwar unabhängig vom Kontonamen des Benutzers oder vom Benutzernamen. Sie können die SID in den Anforderungsparametern des `acquireTokenSilent`-Aufrufs übergeben. Dadurch kann Azure AD die Kontoauswahl umgehen. Die SID ist an das Sitzungscookie gebunden und kann nicht für mehrere Browserkontexte verwendet werden.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> Die SID kann nur mit Authentifizierungsanforderungen im Hintergrund verwendet werden, die mit einem `acquireTokenSilent`-Aufruf in MSAL.js durchgeführt werden.
[Hier](active-directory-optional-claims.md) finden Sie die Schritte zum Konfigurieren optionaler Ansprüche im Anwendungsmanifest.

**Mit einem Anmeldehinweis**

Wenn kein SID-Anspruch konfiguriert wurde oder die Aufforderung zur Kontoauswahl auch bei interaktiven Authentifizierungen umgangen werden soll, geben Sie in den Anforderungsparametern einen `login_hint` an, und stellen Sie optional einen `domain_hint` als `extraQueryParameters` in den interaktiven MSAL.js-Methoden (`loginPopup`, `loginRedirect`, `acquireTokenPopup` und `acquireTokenRedirect`) bereit. Beispiel: 

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Sie erhalten die Werte für „login_hint“ und „domain_hint“ durch Auslesen der Ansprüche, die im ID-Token für den Benutzer zurückgegeben werden.

* **login_hint** sollte auf den `preferred_username`-Anspruch im ID-Token festgelegt sein.

* **domain_hint** muss nur übergeben werden, wenn Sie die /common-Autorität verwenden. Der Domänenhinweis (domain_hint) wird durch die Mandanten-ID (TID) bestimmt.  Wenn der `tid`-Anspruch im ID-Token `9188040d-6c67-4c5b-b112-36a304b66dad` lautet, handelt es sich um Consumer. Andernfalls sind es Organisationen.

[Hier](v2-oauth2-implicit-grant-flow.md) erfahren Sie mehr über die Werte für Anmelde- und Domänenhinweis.

> [!Note]
> Sitzungs-ID (SID) und Anmeldehinweis (login_hint) können nicht gleichzeitig übergeben werden. Dies führt zu einer Fehlerantwort.

## <a name="sso-without-msaljs-login"></a>Einmaliges Anmelden ohne MSAL.js-Anmeldung

Standardmäßig erfordert MSAL.js, dass eine Anmeldemethode aufgerufen wird, um einen Benutzerkontext einzurichten, bevor Token für APIs abgerufen werden. Da Anmeldemethoden interaktiv sind, wird dem Benutzer eine Aufforderung angezeigt.

Es gibt bestimmte Fälle, in denen Anwendungen Zugriff auf den authentifizierten Benutzerkontext oder das ID-Token haben, über den bzw. das die Authentifizierung in einer anderen Anwendung initiiert wurde. Diese Anwendungen können kann einmaliges Anmelden verwenden, um Token ohne vorherige Anmeldung über MSAL.js abzurufen.

Beispiel: Ein Benutzer meldet sich in einer übergeordneten Webanwendung an, die eine andere JavaScript-Anwendung hostet, die als ein Add-On oder Plug-In ausgeführt wird.

In diesem Szenario kann einmaliges Anmelden wie folgt erreicht werden:

Übergeben Sie wie folgt die `sid` (sofern vorhanden – oder `login_hint` und optional `domain_hint`) als Anforderungsparameter für den MSAL.js-Aufruf von `acquireTokenSilent`:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Einmaliges Anmelden in ADAL.js mit Update auf MSAL.js

MSAL.js bietet Featureparität mit ADAL.js für Azure AD-Authentifizierungsszenarien. Um die Migration von ADAL.js zu MSAL.js zu vereinfachen und zu vermeiden, dass Ihre Benutzer zur erneuten Anmeldung aufgefordert werden, liest die Bibliothek das ID-Token für die Sitzung des Benutzers im Cache von ADAL.js und meldet den Benutzer nahtlos in MSAL.js an.  

Um das Verhalten des einmaligen Anmeldens (SSO) bei einer Aktualisierung von ADAL.js nutzen zu können, müssen Sie sicherstellen, dass die Bibliotheken zum Zwischenspeichern von Token `localStorage` verwenden. Legen Sie bei der Initialisierung `cacheLocation` in den Konfigurationen von MSAL.js und ADAL.js wie folgt auf `localStorage` fest:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Sobald dies konfiguriert ist, kann MSAL.js den zwischengespeicherten Zustand des authentifizierten Benutzers in ADAL.js lesen und dazu verwenden, einmaliges Anmelden in MSAL.js bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Sitzungen mit einmaligem Anmelden und die Gültigkeitsdauer von Token](active-directory-configurable-token-lifetimes.md) in Azure AD.
