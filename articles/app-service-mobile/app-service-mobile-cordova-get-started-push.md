---
title: Hinzufügen von Pushbenachrichtigungen zu einer Apache Cordova-App mit dem Mobile Apps-Feature von Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Verwendung von Mobile Apps zum Senden von Pushbenachrichtigungen an Ihre Apache Cordova-App.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 40a7552ffd0bfcab173d2e35c52313a94ec3d0bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114339"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Hinzufügen von Pushbenachrichtigungen zu Ihrer Apache Cordova-App

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Übersicht

In diesem Tutorial fügen Sie Pushbenachrichtigungen zum [Apache Cordova-Schnellstartprojekt][5] hinzu, damit jedes Mal, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung an das Gerät gesendet wird.

Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Erweiterungspaket für Pushbenachrichtigungen hinzufügen. Weitere Informationen finden Sie unter [Arbeiten mit dem .NET Back-End-Server SDK für Mobile Apps][1].

## <a name="prerequisites"></a>Voraussetzungen

Bei diesem Tutorial wird davon ausgegangen, dass Sie über eine Apache Cordova-Anwendung verfügen, die mit Visual Studio 2015 entwickelt wurde. Diese Anwendung sollte auf dem Google Android-Emulator, einem Android-Gerät, einem Windows-Gerät oder einem iOS-Gerät ausgeführt werden.

Für dieses Tutorial benötigen Sie Folgendes:

* Einen PC mit [Visual Studio Community 2015][2] oder höher
* [Visual Studio-Tools für Apache Cordova][4]
* Ein [aktives Azure-Konto][3]
* Ein abgeschlossenes [Apache Cordova-Schnellstartprojekt][5]
* (Android) Ein [Google-Konto][6] mit einer verifizierten E-Mail-Adresse
* (iOS) Eine [Apple Developer Program-Mitgliedschaft][7] und ein iOS-Gerät (der iOS-Simulator unterstützt keine Pushvorgänge)
* (Windows) Ein [Microsoft Store-Entwicklerkonto][8] und ein Windows 10-Gerät

## <a name="configure-hub"></a>Konfigurieren eines Notification Hubs

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Sehen Sie sich ein Video mit den Schritten in diesem Abschnitt an][9].

## <a name="update-the-server-project"></a>Aktualisieren des Serverprojekts

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Ändern Ihrer Cordova-App

Um sicherzustellen, dass Ihr Apache Cordova-App-Projekt zur Verarbeitung von Pushbenachrichtigungen bereit ist, installieren Sie das Cordova-Push-Plug-In sowie alle plattformspezifischen Pushdienste.

#### <a name="update-the-cordova-version-in-your-project"></a>Aktualisieren Sie die Cordova-Version in Ihrem Projekt.

Falls Ihr Projekt eine Apache Cordova-Version vor Version 6.1.1 verwendet, aktualisieren Sie das Clientprojekt. Führen Sie die folgenden Schritte aus, um das Projekt zu aktualisieren:

* Klicken Sie mit der rechten Maustaste auf `config.xml`, um den Konfigurations-Designer zu öffnen.
* Wählen Sie die Registerkarte **Plattformen** aus.
* Wählen Sie im Textfeld **Cordova CLI** die Option **6.1.1** aus. 
* Zum Aktualisieren des Projekts wählen Sie nacheinander **Build** und **Projektmappe erstellen** aus.

#### <a name="install-the-push-plugin"></a>Installieren des Push-Plug-Ins

Apache Cordova-Anwendungen bieten keine native Verarbeitung von Geräte- und Netzwerkfunktionen.  Diese Funktionen werden über Plug-Ins bereitgestellt, die entweder über [npm][10] oder über GitHub veröffentlicht werden. Das `phonegap-plugin-push`-Plug-In verarbeitet Netzwerk-Pushbenachrichtigungen.

Sie können das Push-Plug-In auf eine der folgenden Arten installieren:

**Über die Befehlszeile:**

Führen Sie den folgenden Befehl aus:

    cordova plugin add phonegap-plugin-push

**In Visual Studio:**

1. Öffnen Sie im Projektmappen-Explorer die Datei `config.xml`. Wählen Sie anschließend **Plug-Ins** > **Benutzerdefiniert** aus. Wählen Sie dann **Git** als Installationsquelle aus.

2. Geben Sie `https://github.com/phonegap/phonegap-plugin-push` als Quelle ein.

    ![Datei „config.xml“ im Projektmappen-Explorer öffnen][img1]

3. Klicken Sie auf den Pfeil neben der Installationsquelle.

4. Falls Sie bereits über eine numerische Projekt-ID für das Google Developer Console-Projekt verfügen, können Sie diese hier unter **SENDER_ID** hinzufügen. Geben Sie andernfalls einen Platzhalterwert ein, z.B. „777777“. Falls Sie Android nutzen möchten, können Sie diesen Wert später in der Datei „config.xml“ aktualisieren.

    >[!NOTE]
    >Ab Version 2.0.0 muss die Datei „google-services.json“ im Stammordner Ihres Projekts installiert sein, damit die Sender-ID konfiguriert werden kann. Weitere Informationen finden Sie in der [Installationsdokumentation](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md).

5. Wählen Sie **Hinzufügen**.

Das Push-Plug-In ist jetzt installiert.

#### <a name="install-the-device-plugin"></a>Installieren des Geräte-Plug-Ins

Verwenden Sie das gleiche Verfahren wie beim Installieren des Push-Plug-Ins. Fügen Sie das Geräte-Plug-In aus der Liste der Kern-Plug-Ins hinzu. (Um es zu suchen, wählen Sie **Plug-Ins** > **Kern** aus.) Sie benötigen dieses Plug-In zum Abrufen des Plattformnamens.

#### <a name="register-your-device-when-the-application-starts"></a>Registrieren des Geräts beim Start der Anwendung 

Zu Beginn fügen wir etwas Code für Android ein. Sie können die App später für die Ausführung unter iOS oder Windows 10 anpassen.

1. Fügen Sie während des Rückrufs für den Anmeldeprozess einen Aufruf von **registerForPushNotifications** hinzu. Alternativ dazu können Sie den Aufruf auch am Ende der **onDeviceReady**-Methode hinzufügen:

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    Dieses Beispiel zeigt den Aufruf von **RegisterForPushNotifications** nach erfolgreicher Authentifizierung. Sie können `registerForPushNotifications()` so oft aufrufen wie erforderlich.

2. Fügen Sie die neue **registerForPushNotifications** -Methode wie folgt hinzu:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. (Android) Ersetzen Sie im vorherigen Code `Your_Project_ID` durch die numerische Projekt-ID für Ihre App aus der [Google Developers Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Optional) Konfigurieren und Ausführen der App unter Android

Arbeiten Sie diesen Abschnitt durch, um Pushbenachrichtigungen für Android zu aktivieren.

#### <a name="enable-gcm"></a>Aktivieren von Firebase Cloud Messaging

Da Ihr Ziel zunächst die Google Android-Plattform ist, müssen Sie Firebase Cloud Messaging aktivieren.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurieren des Mobile App-Back-Ends zum Senden von Pushanforderungen per FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurieren der Cordova-App für Android

Öffnen Sie die Datei **config.xml** in Ihrer Cordova-App. Ersetzen Sie dann `Your_Project_ID` durch die numerische Projekt-ID für Ihre App aus der [Google Developer Console][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Öffnen Sie **index.js**. Aktualisieren Sie dann den Code, sodass Ihre numerische Projekt-ID verwendet wird.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Konfigurieren Ihres Android-Geräts für USB-Debugging

Bevor Sie Ihre Anwendung auf Ihrem Android-Gerät bereitstellen können, müssen Sie das USB-Debugging aktivieren. Führen Sie die folgenden Schritte auf Ihrem Android-Telefon aus:

1. Wechseln Sie zu **Einstellungen** > **Telefoninfo**. Tippen Sie dann mehrfach auf die **Buildnummer**, bis der Entwicklermodus aktiviert wird (ungefähr siebenmal).
2. Aktivieren Sie unter **Einstellungen** > **Entwickleroptionen** die Option **USB-Debugging**. Verbinden Sie dann Ihr Android-Telefon über ein USB-Kabel mit Ihrem Entwicklungs-PC.

Wir haben diese Vorgehensweise mit einem Google Nexus 5X-Gerät unter Android 6.0 (Marshmallow) getestet. Das Verfahren ist jedoch für alle modernen Android-Versionen gleich.

#### <a name="install-google-play-services"></a>Installieren der Google Play-Dienste

Das Push-Plug-In nutzt Android Google Play-Dienste für Pushbenachrichtigungen.

1. Wählen Sie in Visual Studio die Optionen **Tools** > **Android** > **Android SDK-Manager** aus. Erweitern Sie dann den Ordner **Extras**. Aktivieren Sie die entsprechenden Kontrollkästchen, um sicherzustellen, dass jedes der folgenden SDKs installiert wird:

   * Android 2.3 oder höher
   * Google Repository Version 27 oder höher
   * Google Play Services 9.0.2 oder höher

2. Klicken Sie auf **Pakete installieren**. Warten Sie, bis die Installation abgeschlossen ist.

Die aktuellen erforderlichen Bibliotheken werden in der [phonegap-plugin-push installation documentation][19] (Dokumentation zur Pushinstallation des PhoneGap-Plug-Ins) aufgelistet.

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testen von Pushbenachrichtigungen in der App unter Android

Sie können die Pushbenachrichtigungen jetzt testen, indem Sie die App ausführen und Elemente in die TodoItem-Tabelle einfügen. Zum Testen können Sie das gleiche Gerät oder ein zweites Gerät verwenden, sofern Sie das gleiche Back-End nutzen. Testen Sie Ihre Cordova-App auf der Android-Plattform mit einer der folgenden Methoden:

* *Auf einem physischen Gerät:* Schließen Sie Ihr Android-Gerät mit einem USB-Kabel an den Entwicklungscomputer an.  Wählen Sie anstelle von **Google Android Emulator** den Eintrag **Gerät** aus. Visual Studio stellt die Anwendung auf dem Gerät bereit und führt sie aus. Anschließend können Sie mit der Anwendung auf dem Gerät interagieren.

  Anwendungen für die Bildschirmfreigabe (beispielsweise [Mobizen][20]) können Sie bei der Entwicklung von Android-Anwendungen unterstützen. Mobizen projiziert Ihren Android-Bildschirm in einen Webbrowser auf Ihrem PC.

* *Mit einem Android-Emulator:* Bei Verwendung eines Emulators sind zusätzliche Konfigurationsschritte erforderlich.

    Stellen Sie sicher, dass die Bereitstellung auf einem virtuellen Gerät erfolgt, bei dem Google-APIs als Ziel festgelegt sind, wie beim AVD-Manager (Android Virtual Device) zu sehen.

    ![Android Virtual Device Manager](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Wenn Sie einen schnelleren x86-Emulator verwenden möchten, [installieren Sie den HAXM-Treiber][11], und konfigurieren Sie den Emulator so, dass er diesen Treiber verwendet.

    Fügen Sie auf dem Android-Gerät ein Google-Konto hinzu, indem Sie auf **Apps** > **Einstellungen** > **Konto hinzufügen** tippen. Folgen Sie dann den Anweisungen.

    ![Hinzufügen eines Google-Kontos zum Android-Gerät](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Führen Sie wie zuvor die todolist-App aus und fügen Sie ein neues todo-Element ein. Dieses Mal wird im Benachrichtigungsbereich ein Benachrichtigungssymbol angezeigt. Öffnen Sie die Benachrichtigungsschublade, um den vollständigen Text der Benachrichtigung anzuzeigen.

    ![Anzeigen einer Benachrichtigung](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Optional) Konfigurieren und Ausführen unter iOS

In diesem Abschnitt wird die Ausführung des Cordova-Projekts auf iOS-Geräten beschrieben. Wenn Sie nicht mit iOS-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Installieren und Ausführen des iOS-remotebuild-Agents auf einem Macintosh-Computer oder in einem Clouddienst

Bevor Sie eine Cordova-App mithilfe von Visual Studio unter iOS ausführen können, müssen Sie im [iOS Setup Guide][12] (iOS-Einrichtungsleitfaden) die Schritte zum Installieren und Ausführen des Remotebuild-Agents ausführen.

Vergewissern Sie sich, dass Sie die App für iOS erstellen können. Die Schritte im Einrichtungsleitfaden sind erforderlich, um die App für iOS in Visual Studio zu erstellen. Falls Sie nicht über einen Mac-Computer verfügen, können Sie den Remotebuild-Agent in einem Dienst wie z.B. MacInCloud nutzen, um Apps für iOS zu erstellen. Weitere Informationen finden Sie unter [Build and simulate a Cordova iOS app in the cloud][21] (Erstellen und Simulieren einer Cordova-iOS-App in der Cloud).

> [!NOTE]
> Zur Verwendung des Push-Plug-Ins unter iOS wird mindestens Xcode 7 benötigt.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Suchen nach der ID zur Verwendung als App-ID

Öffnen Sie vor dem Registrieren Ihrer App für Pushbenachrichtigungen die Datei „config.xml“ in Ihrer Cordova-App, suchen Sie im Widgetelement nach dem Attributwert `id`, und kopieren Sie diesen zur späteren Verwendung. Im folgenden XML-Code lautet die ID `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Verwenden Sie diesen Bezeichner später beim Erstellen einer App-ID im Entwicklerportal von Apple. Falls Sie im Entwicklerportal eine andere App-ID erstellen, sind im weiteren Verlauf dieses Tutorials einige zusätzliche Schritte erforderlich. Die ID im Widget-Element muss mit der App-ID im Entwicklerportal übereinstimmen.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrieren der App für Pushbenachrichtigungen im Apple-Entwicklerportal

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Ansehen eines Videos mit ähnlichen Schritten](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurieren von Azure zum Senden von Pushbenachrichtigungen

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Überprüfen, ob die App-ID mit der Cordova-App übereinstimmt

Wenn die App-ID, die Sie in Ihrem Apple Developer-Konto erstellt haben, bereits mit der ID des Widgetelements in der Datei „config.xml“ übereinstimmt, können Sie diesen Schritt überspringen. Gehen Sie wie folgt vor, wenn die IDs nicht übereinstimmen:

1. Löschen Sie den Ordner „platforms“ aus Ihrem Projekt.
2. Löschen Sie den Ordner „plugins“ aus Ihrem Projekt.
3. Löschen Sie den Ordner „node_modules“ aus Ihrem Projekt.
4. Aktualisieren Sie das Attribut „id“ des Widgetelements in der Datei „config.xml“, um die App-ID zu verwenden, die Sie im Apple Developer-Konto erstellt haben.
5. Erstellen Sie das Projekt neu.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testen von Pushbenachrichtigungen in der iOS-App

1. Stellen Sie in Visual Studio sicher, dass **iOS** als Bereitstellungsziel ausgewählt ist. Wählen Sie dann **Gerät** aus, um die Pushbenachrichtigungen auf Ihrem verbundenen iOS-Gerät auszuführen.

    Sie können die Pushbenachrichtigungen auf einem iOS-Gerät ausführen, das über iTunes mit Ihrem PC verbunden ist. Der iOS-Simulator unterstützt keine Pushbenachrichtigungen.

2. Klicken Sie in Visual Studio auf die Schaltfläche **Ausführen**, oder drücken Sie die Taste **F5**, um das Projekt zu erstellen und die App auf einem iOS-Gerät zu starten. Wählen Sie dann **OK** aus, um Pushbenachrichtigungen zu akzeptieren.

   > [!NOTE]
   > Bei der erstmaligen Ausführung fordert die App eine Bestätigung für Pushbenachrichtigungen an.

3. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen ( **+** ).
4. Überprüfen Sie, ob eine Benachrichtigung empfangen wurde. Wählen Sie dann **OK**, um die Benachrichtigung zu verwerfen.

## <a name="optional-configure-and-run-on-windows"></a>(Optional) Konfigurieren und Ausführen unter Windows

Dieser Abschnitt beschreibt die Ausführung des Apache Cordova-App-Projekts auf Windows 10-Geräten (das PhoneGap-Push-Plug-In wird unter Windows 10 unterstützt). Wenn Sie nicht mit Windows-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrieren der Windows-App für Pushbenachrichtigungen mit WNS

Um die Store-Optionen in Visual Studio zu nutzen, wählen Sie in der Liste mit den Projektmappenplattformen ein Windows-Ziel aus, z.B. **Windows-x64** oder **Windows-x86**. (Meiden Sie **Windows-AnyCPU** für Pushbenachrichtigungen.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Video mit ähnlichen Schritten ansehen][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurieren des Notification Hubs für WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurieren der Cordova-App für die Unterstützung von Windows-Pushbenachrichtigungen

Klicken Sie mit der rechten Maustaste auf **config.xml**, um den Konfigurations-Designer zu öffnen. Wählen Sie anschließend **Ansicht-Designer**. Klicken Sie dann auf die Registerkarte **Windows**, und wählen Sie dann unter **Windows-Zielversion** die Option**Windows 10** aus.

Öffnen Sie die Datei **build.json**, um Pushbenachrichtigungen in Ihren Standardbuilds (Debugbuilds) zu unterstützen. Kopieren Sie dann die „Releasekonfiguration“ in Ihre Debugkonfiguration.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Nach dem Update sollte die Datei **build.json** folgenden Code enthalten:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Erstellen Sie die App, und stellen Sie sicher, dass keine Fehler auftreten. Ihre Client-App sollte sich nun für die Benachrichtigungen vom Mobile Apps-Back-End registrieren. Wiederholen Sie die Schritte in diesem Abschnitt für alle Windows-Projekte Ihrer Projektmappe.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testen von Pushbenachrichtigungen in Ihrer Windows-App

Stellen Sie in Visual Studio sicher, dass eine Windows-Plattform als Bereitstellungsziel ausgewählt ist, z.B. **Windows-x64** oder **Windows-x86**. Wählen Sie zum Ausführen der App auf einem Windows 10-PC, auf dem Visual Studio gehostet wird, die Option **Lokaler Computer**.

1. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App zu starten.

2. Geben Sie in der App einen Namen für ein neues TodoItem ein, und klicken Sie dann auf das Pluszeichen ( **+** ), um es hinzuzufügen.

Überprüfen Sie, ob beim Hinzufügen des Elements eine Benachrichtigung empfangen wird.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Pushbenachrichtigungen finden Sie unter [Notification Hubs][17].
* Setzen Sie das Tutorial mit dem Schritt [Hinzufügen von Authentifizierung][14] zu Ihrer Apache Cordova-App fort, sofern Sie diesen Schritt nicht bereits ausgeführt haben.

Erfahren Sie, wie Sie die folgenden SDKs verwenden:

* [Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
