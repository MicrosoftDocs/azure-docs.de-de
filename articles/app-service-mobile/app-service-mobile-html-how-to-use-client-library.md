---
title: Verwenden des JavaScript-SDKs für Azure Mobile Apps
description: Vorgehensweise beim Verwenden von V for Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 16871bdc59d141334bc2c95f26929f270d7971cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106742"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Verwenden der JavaScript-Clientbibliothek für Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des neuesten [JavaScript-SDKs für Azure Mobile Apps]. Wenn Sie mit Azure Mobile Apps noch nicht vertraut sind, führen Sie zunächst den [Schnellstart von Azure Mobile Apps] durch, um ein Back-End und eine Tabelle zu erstellen. In diesem Leitfaden liegt der Schwerpunkt auf der Verwendung des mobilen Back-Ends in HTML/JavaScript-Webanwendungen.

## <a name="supported-platforms"></a>Unterstützte Plattformen
Wir schränken die Browserunterstützung auf die aktuellen und neusten Versionen der wichtigsten Browser ein:  Google Chrome, Microsoft Edge, Microsoft Internet Explorer und Mozilla Firefox.  Wir gehen davon aus, dass das SDK mit jedem relativ modernen Browser funktioniert.

Das Paket wird als universelles JavaScript-Modul bereitgestellt und unterstützt daher globale Formate ebenso wie AMD- und CommonJS-Formate.

## <a name="Setup"></a>Einrichtung und Voraussetzungen
Dieses Lernprogramm setzt voraus, dass Sie ein Back-End mit einer Tabelle erstellt haben. In dieser Anleitung wird davon ausgegangen, dass die Tabelle das gleiche Schema wie die Tabellen in diesen Lernprogrammen aufweist.

Die Installation des JavaScript SDKs für Azure Mobile Apps kann durch den Befehl `npm` vorgenommen werden.

```
npm install azure-mobile-apps-client --save
```

Die Bibliothek kann auch als ES2015-Modul in CommonJS-Umgebungen verwendet werden, wie z. B. Browserify und Webpack sowie als eine AMD-Bibliothek.  Beispiel:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Sie können auch eine vorab erstellte Version des SDK verwenden, die Sie direkt aus unserem CDN herunterladen:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Gewusst wie: Authentifizieren von Benutzern
App Service unterstützt die Authentifizierung und Autorisierung von App-Benutzern mithilfe verschiedener externer Identitätsanbieter: Facebook, Google, Microsoft-Konto und Twitter. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Erste Schritte mit der Authentifizierung] .

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein Serverfluss und ein Clientfluss.  Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische SDKs verwendet werden.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Gewusst wie: Konfigurieren Ihres Mobile App Service für externe Umleitungs-URLs
Mehrere JavaScript-Anwendungen verwenden eine Loopback-Funktion, um Abläufe in der OAuth-Benutzeroberfläche zu verarbeiten.  Diese Funktionen umfassen:

* Lokale Ausführung Ihres Dienstes
* Verwendung von Live Reload mit den Ionic-Framework
* Umleitung an den App Service zur Authentifizierung.

Lokale Ausführung kann problematisch sein, da die App Service-Authentifizierung in der Standardkonfiguration nur Zugriffe von Ihrem Back-End für mobile Apps zulässt. Führen Sie die folgenden Schritte aus, um die App Service-Einstellungen zu ändern und die Authentifizierung bei lokaler Serverausführung zu ermöglichen:

1. Melden Sie sich beim [Azure-Portal]
2. Navigieren Sie zum Back-End für mobile Apps.
3. Wählen Sie **Ressourcen-Explorer** im Menü **ENTWICKLUNGSTOOLS**.
4. Klicken Sie auf **Gehe zu** , um den Ressourcen-Explorer für Ihr Back-End für mobile Apps in einer neuen Registerkarte oder einem neuen Fenster zu öffnen.
5. Erweitern Sie den Knoten **config** > **authsettings** für Ihre App.
6. Klicken Sie auf die Schaltfläche **Bearbeiten** , um die Bearbeitung der Ressource zu ermöglichen.
7. Suchen Sie nach dem Element **allowedExternalRedirectUrls** , das den Wert NULL haben sollte. Fügen Sie Ihre URLs in einem Array hinzu:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersetzen Sie die URLs im Array durch die URLs Ihres Diensts (in diesem Beispiel `http://localhost:3000` für den lokalen Node.js-Beispieldienst). Alternativ könnten Sie auch `http://localhost:4400` für den Ripple-Dienst oder eine andere URL verwenden (je nach Konfiguration Ihrer App).
8. Klicken Sie oben auf der Seite auf **Lesen/Schreiben** und anschließend auf **PUT**, um Ihre Änderungen zu speichern.

Nun müssen auch die gleichen Loopback-URLs den CORS-Positivlisteneinstellungen hinzugefügt werden:

1. Navigieren Sie zurück zum [Azure-Portal].
2. Navigieren Sie zum Back-End für mobile Apps.
3. Klicken Sie im **API**-Menü auf **CORS**.
4. Geben Sie jede URL in das leere Textfeld **Zulässige Ursprünge** ein.  Es wird ein neues Textfeld erstellt.
5. Klicken Sie auf **SPEICHERN**

Nach der Aktualisierung des Back-Ends können Sie die neuen Loopback-URLs in Ihrer App verwenden.

<!-- URLs. -->
[Schnellstart von Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Erste Schritte mit der Authentifizierung]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure-Portal]: https://portal.azure.com/
[JavaScript-SDKs für Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
