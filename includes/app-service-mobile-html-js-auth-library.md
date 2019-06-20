---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178602"
---
### <a name="server-auth"></a>Gewusst wie: Authentifizieren bei einem Anbieter (Serverflow)
Sie müssen Ihre Mobile Apps bei Ihrem Identitätsanbieter registrieren, um Mobile Services die Verwaltung des Authentifizierungsprozesses in Ihrer App zu ermöglichen. Anschließend müssen Sie in Ihrem Azure App Service die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren.
Weitere Informationen finden Sie im Lernprogramm [Authentifizierung zu Ihrer App hinzufügen](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

Rufen Sie nach der Registrierung bei Ihrem Identitätsanbieter die `.login()`-Methode mit dem Namen Ihres Anbieters auf. Verwenden Sie also beispielsweise für die Facebook-Anmeldung den folgenden Code:

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Gültige Anbieterwerte sind „aad“, „facebook“, „google“, „microsoftaccount“ und „twitter“.

> [!NOTE]
> Die Authentifizierung über Google ist zurzeit nicht per Serverfluss möglich.  Für die Authentifizierung über Google muss eine [Clientflussmethode](#client-auth) verwendet werden.

In diesem Fall verwaltet Azure App Service den OAuth 2.0-Authentifizierungsfluss.  Dabei wird die Anmeldeseite des ausgewählten Anbieters angezeigt und nach erfolgreicher Anmeldung beim Identitätsanbieter ein App Service-Authentifizierungstoken generiert. Die Anmeldefunktion gibt nach Abschluss ein JSON-Objekt zurück, das sowohl die Benutzer-ID als auch das App Service-Authentifizierungstoken in den Feldern „userId“ bzw. „authenticationToken“ verfügbar macht. Dieses Token kann zwischengespeichert und wiederverwendet werden, bis es abläuft.

### <a name="client-auth"></a>Gewusst wie: Authentifizieren bei einem Anbieter (Clientflow)

Ihre Anwendung kann den Identitätsanbieter auch unabhängig kontaktieren und das zurückgegebene Token zur Authentifizierung Ihrem App Service vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen.

#### <a name="social-authentication-basic-example"></a>Einfaches Beispiel für die Authentifizierung über soziale Profile

Dieses Beispiel verwendet die Client-SDK von Facebook für die Authentifizierung:

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
Dieses Beispiel geht davon aus, dass das vom jeweiligen Anbieter gelieferte Token in der token-Variable gespeichert wird.

### <a name="auth-getinfo"></a>Gewusst wie: Abrufen von Informationen zum authentifizierten Benutzer

Die Authentifizierungsinformationen können mithilfe eines HTTP-Aufrufs mit einer beliebigen AJAX-Bibliothek vom `/.auth/me`-Endpunkt abgerufen werden.  Stellen Sie sicher, dass der `X-ZUMO-AUTH` -Header auf Ihr Authentifizierungstoken festgelegt ist.  Das Authentifizierungstoken wird in `client.currentUser.mobileServiceAuthenticationToken`gespeichert.  Geben Sie beispielsweise Folgendes ein, um die API abzurufen:

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Der Abruf ist als [npm-Paket](https://www.npmjs.com/package/whatwg-fetch) oder als Browserdownload über [CDNJS](https://cdnjs.com/libraries/fetch) verfügbar. Sie können auch JQuery oder eine andere AJAX-API zum Abrufen der Informationen verwenden.  Daten werden als JSON-Objekt empfangen.
