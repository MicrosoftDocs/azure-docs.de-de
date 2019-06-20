---
title: Verwenden des Apache Cordova-Plug-Ins für Azure Mobile Apps
description: Verwenden des Apache Cordova-Plug-Ins für Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 3c22aab20a9260bfd21869f0b327211e2f3d8894
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62109575"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Verwenden der Apache Cordova-Clientbibliothek für Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des neuesten [Apache Cordova-Plug-Ins für Azure Mobile Apps]. Wenn Sie mit Azure Mobile Apps noch nicht vertraut sind, führen Sie zunächst den [Schnellstart von Azure Mobile Apps] durch, um ein Back-End und eine Tabelle zu erstellen und ein vorgefertigtes Apache Cordova-Projekt herunterzuladen. In dieser Anleitung konzentrieren wir uns auf das clientseitige Apache Cordova-Plug-In.

## <a name="supported-platforms"></a>Unterstützte Plattformen
Dieses SDK unterstützt Apache Cordova v6.0.0 und höher auf iOS-, Android- und Windows-Geräten.  Folgende Plattformen werden unterstützt:

* Android API 19–24 (KitKat bis Nougat)
* iOS-Version 8.0 und höher.
* Windows Phone 8.1
* Universelle Windows-Plattform

## <a name="Setup"></a>Einrichtung und Voraussetzungen
Dieses Lernprogramm setzt voraus, dass Sie ein Back-End mit einer Tabelle erstellt haben. In dieser Anleitung wird davon ausgegangen, dass die Tabelle das gleiche Schema wie die Tabellen in diesen Lernprogrammen aufweist. In dieser Anleitung wird davon ausgegangen, dass Sie Ihrem Code das Apache Cordova-Plug-In hinzugefügt haben.  Falls noch nicht geschehen, können Sie das Apache Cordova-Plug-In über die Befehlszeile Ihrem Projekt hinzufügen:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Weitere Informationen zum Erstellen [Ihrer ersten Apache Cordova-App]finden Sie in der dazugehörigen Dokumentation.

## <a name="ionic"></a>Einrichten einer Ionic v2-App

Erstellen Sie für die ordnungsgemäße Konfiguration eines Ionic v2-Projekts zunächst eine allgemeine App, und fügen Sie anschließend das Cordova-Plug-In hinzu:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Fügen Sie `app.component.ts` die folgenden Zeilen hinzu, um das Clientobjekt zu erstellen:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Nun können Sie das Projekt im Browser erstellen und ausführen:

```
ionic platform add browser
ionic run browser
```

Das Azure Mobile Apps-Cordova-Plug-In unterstützt Ionic v1- und v2-Apps.  Nur bei Ionic v2-Apps ist die zusätzliche Deklaration für das `WindowsAzure`-Objekt erforderlich.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Vorgehensweise: Authentifizieren von Benutzern
App Service unterstützt die Authentifizierung und Autorisierung von App-Benutzern mithilfe verschiedener externer Identitätsanbieter: Facebook, Google, Microsoft-Konto und Twitter. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Erste Schritte mit der Authentifizierung] .

Bei Verwendung der Authentifizierung in einer Apache Cordova-App müssen die folgenden Cordova-Plug-Ins verfügbar sein:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein Serverfluss und ein Clientfluss.  Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Vorgehensweise: Konfigurieren Ihres Mobile App Service für externe Umleitungs-URLs
Mehrere Apache Cordova-Anwendungen verwenden eine Loopback-Funktion, um Abläufe in der OAuth-Benutzeroberfläche zu verarbeiten.  Abläufe in der OAuth-Benutzeroberfläche auf localhost führen zu Problemen, da der Authentifizierungsdienst nur weiß, wie Ihr Dienst standardmäßig zu verwenden ist.  Folgende Abläufe in der OAuth-Benutzeroberfläche können problematisch sein:

* Der Ripple-Emulator.
* Live Reload mit Ionic.
* Lokale Ausführung des mobilen Back-Ends
* Ausführen des mobilen Back-Ends in einer anderen Azure App Service-Instanz als derjenigen, die die Authentifizierung bereitstellt.

Um die Konfiguration Ihrer lokalen Einstellungen hinzuzufügen, gehen Sie wie folgt vor:

1. Melden Sie sich beim [Azure-Portal]
2. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihrer mobilen App.
3. Klicken Sie auf **Tools**
4. Klicken Sie im Menü ÜBERWACHEN auf **Ressourcen-Explorer**, und klicken Sie dann auf **Start**.  Es wird ein neues Fenster oder eine neue Registerkarte geöffnet.
5. Erweitern Sie im linken Navigationsbereich die Knoten **Konfiguration** und **Authentifizierungseinstellungen** für Ihre Website.
6. Klicken Sie auf **Bearbeiten**
7. Suchen Sie nach dem Element allowedExternalRedirectUrls.  Das Element ist möglicherweise auf Null oder ein Wertearray festgelegt.  Ändern Sie den Wert folgendermaßen:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Ersetzen Sie die URLs mit den URLs Ihres Diensts.  Beispiele hierfür sind `http://localhost:3000` (für den Node.js-Beispieldienst) oder `http://localhost:4400` (für den Ripple-Dienst).  Diese URLs sind jedoch nur Beispiele – Ihre Situation kann auch für die Dienste, die im Beispiel dargestellt sind, ganz anders sein.
8. Klicken Sie in der oberen rechten Ecke des Bildschirms auf die Schaltfläche **Lesen/Schreiben** .
9. Klicken Sie auf die grüne Schaltfläche **PUT** .

An diesem Punkt werden die Einstellungen gespeichert.  Schließen Sie das Browserfenster nicht, ehe die Einstellungen gespeichert wurden.
Sie müssen auch die Loopback-URLs zu den CORS-Einstellungen für Ihre App Service-Instanz hinzufügen:

1. Melden Sie sich beim [Azure-Portal]
2. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihrer mobilen App.
3. Das Blatt „Einstellungen“ wird automatisch geöffnet.  Wenn dies nicht der Fall ist, klicken Sie auf **Alle Einstellungen**.
4. Klicken Sie im API-Menü auf **CORS** .
5. Geben Sie in das bereitgestellte Feld die URL ein, die Sie hinzufügen möchten, und drücken Sie die EINGABETASTE.
6. Geben Sie ggf. zusätzliche URLs ein.
7. Klicken Sie auf **Speichern** , um die Einstellungen zu speichern.

Es dauert ungefähr 10-15 Sekunden, bis die neuen Einstellungen wirksam sind.

## <a name="register-for-push"></a>Vorgehensweise: Registrieren für Pushbenachrichtigungen
Installieren Sie das Plug-In [phonegap-plug-in-push] zum Verarbeiten von Pushbenachrichtigungen.  Dieses Plug-In kann problemlos mithilfe des `cordova plugin add` -Befehls über die Befehlszeile oder den Git-Installer für Plug-Ins in Visual Studio hinzugefügt werden.  Der folgende Code in der Apache Cordova-App registriert Ihr Gerät für Pushbenachrichtigungen:

```javascript
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Verwenden Sie das Notification Hubs SDK zum Senden von Pushbenachrichtigungen vom Server.  Senden Sie Pushbenachrichtigungen niemals direkt von Clients. Diese Vorgehensweise könnte dazu missbraucht werden, einen Denial-of-Service-Angriff auf Notification Hubs oder den Pushbenachrichtigungsdienst auszulösen.  Als Folge solcher Angriffe könnte der Pushbenachrichtigungsdienst Ihren Datenverkehr sperren.

## <a name="more-information"></a>Weitere Informationen

Ausführliche Informationen zur API finden Sie in unserer [API-Dokumentation](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure-Portal]: https://portal.azure.com
[Schnellstart von Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Erste Schritte mit der Authentifizierung]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova-Plug-Ins für Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[Ihrer ersten Apache Cordova-App]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plug-in-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
