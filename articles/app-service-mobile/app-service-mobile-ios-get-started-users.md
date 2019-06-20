---
title: Hinzufügen von Authentifizierung auf iOS mit Azure Mobile Apps
description: Erfahren Sie, wie Sie Azure Mobile Apps zum Authentifizieren Ihrer iOS-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: 8c1c52790065015977add7e32a06063057b24dad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128148"
---
# <a name="add-authentication-to-your-ios-app"></a>Hinzufügen der Authentifizierung zu Ihrer iOS-App
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In diesem Lernprogramm fügen Sie dem [iOS-Schnellstartprojekt] mithilfe eines unterstützten Identitätsanbieters eine Authentifizierung hinzu. Dieses Lernprogramm baut auf dem [iOS-Schnellstartprojekt] auf, das Sie zuerst abschließen müssen.

## <a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Hinzufügen Ihrer App zu den zulässigen externen Umleitungs-URLs

Eine sichere Authentifizierung erfordert, dass Sie ein neues URL-Schema für Ihre App definieren.  Dies ermöglicht dem Authentifizierungssystem die erneute Umleitung an Ihre App, sobald der Authentifizierungsprozess abgeschlossen ist.  In diesem Tutorial verwenden wir ausschließlich das URL-Schema _appname_.  Sie können jedoch ein beliebiges URL-Schema auswählen und verwenden.  Es sollte für Ihre mobile Anwendung eindeutig sein.  So aktivieren Sie die Umleitung auf der Serverseite:

1. Wählen Sie im [Azure-Portal] App Service aus.

2. Klicken Sie auf die Menüoption **Authentifizierung/Autorisierung**.

3. Klicken Sie unter dem Abschnitt **Authentifizierungsanbieter** auf **Azure Active Directory**.

4. Legen Sie den **Verwaltungsmodus** auf **Erweitert** fest.

5. Geben Sie in **Zulässige externe Umleitungs-URLs** `appname://easyauth.callback` ein.  Das _appname_-Element in dieser Zeichenfolge ist das URL-Schema für Ihre mobile Anwendung.  Es sollte der normalen URL-Spezifikation für ein Protokoll folgen (nur aus Buchstaben und Zahlen bestehen und mit einem Buchstaben beginnen).  Notieren Sie sich die gewählte Zeichenfolge, da Sie später Ihren mobilen Anwendungscode mehrfach mit dem URL-Schema anpassen müssen.

6. Klicken Sie auf **OK**.

7. Klicken Sie auf **Speichern**.

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Klicken Sie in Xcode auf **Run** , um die App zu starten. Eine Ausnahme wird ausgelöst, da die App als nicht authentifizierter Benutzer auf das Back-End zugreift, aber die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App
**Objective-C:**

1. Öffnen Sie auf dem Mac *QSTodoListViewController.m* in Xcode, und fügen Sie folgende Methode hinzu:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Wenn Sie Google nicht als Identitätsanbieter nutzen, ändern Sie *google* in *microsoftaccount*, *twitter*, *facebook* oder *windowsazureactivedirectory*. Wenn Sie Facebook verwenden, [müssen Sie die Facebook-Domänen der Positivliste in Ihrer App hinzufügen][1].

    Ersetzen Sie **urlScheme** mit einem eindeutigen Namen für Ihre Anwendung.  Der urlScheme-Wert muss mit dem URL-Schema-Protokoll übereinstimmen, das Sie im Azure-Portal im Feld **Zulässige externe Umleitungs-URLs** angegeben haben. Der urlScheme-Wert wird vom Authentifizierungsrückruf verwendet, um nach Abschluss der Authentifizierungsanforderung zurück zu Ihrer Anwendung zu wechseln.

2. In *QSTodoListViewController.m* ersetzen Sie `[self refresh]` in `viewDidLoad` durch folgenden Code:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Öffnen Sie die Datei `QSAppDelegate.h`, und fügen Sie den folgenden Code hinzu:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Öffnen Sie die Datei `QSAppDelegate.m`, und fügen Sie den folgenden Code hinzu:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Fügen Sie diesen Code direkt vor der Zeile `#pragma mark - Core Data stack` hinzu.  Ersetzen Sie _appname_ durch den in Schritt 1 verwendeten urlScheme-Wert.

5. Öffnen Sie die Datei `AppName-Info.plist` (ersetzen Sie „AppName“ mit dem Namen Ihrer App), und fügen Sie den folgenden Code hinzu:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Dieser Code sollte innerhalb des `<dict>`-Elements platziert werden.  Ersetzen Sie die _appname_-Zeichenfolge (innerhalb des Arrays für **CFBundleURLSchemes**) durch den App-Namen, den Sie in Schritt 1 ausgewählt haben.  Sie können diese Änderungen auch im plist-Editor vornehmen: Klicken Sie in XCode auf die Datei `AppName-Info.plist`, um den plist-Editor zu öffnen.

    Ersetzen Sie die `com.microsoft.azure.zumo`-Zeichenfolge für **CFBundleURLName** durch Ihre Apple-Paket-ID.

6. Klicken Sie auf *Ausführen*, um die App zu starten, und melden Sie sich an. Nach der Anmeldung sollten Sie die Todo-Liste anzeigen und Änderungen vornehmen können.

**Swift:**

1. Öffnen Sie auf dem Mac *ToDoTableViewController.swift* in Xcode, und fügen Sie folgende Methode hinzu:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Wenn Sie Google nicht als Identitätsanbieter nutzen, ändern Sie *google* in *microsoftaccount*, *twitter*, *facebook* oder *windowsazureactivedirectory*. Wenn Sie Facebook verwenden, [müssen Sie die Facebook-Domänen der Positivliste in Ihrer App hinzufügen][1].

    Ersetzen Sie **urlScheme** mit einem eindeutigen Namen für Ihre Anwendung.  Der urlScheme-Wert muss mit dem URL-Schema-Protokoll übereinstimmen, das Sie im Azure-Portal im Feld **Zulässige externe Umleitungs-URLs** angegeben haben. Der urlScheme-Wert wird vom Authentifizierungsrückruf verwendet, um nach Abschluss der Authentifizierungsanforderung zurück zu Ihrer Anwendung zu wechseln.

2. Entfernen Sie in *ToDoTableViewController.swift* die Zeilen `self.refreshControl?.beginRefreshing()` und `self.onRefresh(self.refreshControl)` am Ende von `viewDidLoad()`. Fügen Sie an deren Stelle einen Aufruf von `loginAndGetData()` ein:

    ```swift
    loginAndGetData()
    ```

3. Öffnen Sie die Datei `AppDelegate.swift`, und fügen Sie der `AppDelegate`-Klasse die folgende Zeile hinzu:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Ersetzen Sie _appname_ durch den in Schritt 1 verwendeten urlScheme-Wert.

4. Öffnen Sie die Datei `AppName-Info.plist` (ersetzen Sie „AppName“ mit dem Namen Ihrer App), und fügen Sie den folgenden Code hinzu:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Dieser Code sollte innerhalb des `<dict>`-Elements platziert werden.  Ersetzen Sie die _appname_-Zeichenfolge (innerhalb des Arrays für **CFBundleURLSchemes**) durch den App-Namen, den Sie in Schritt 1 ausgewählt haben.  Sie können diese Änderungen auch im plist-Editor vornehmen: Klicken Sie in XCode auf die Datei `AppName-Info.plist`, um den plist-Editor zu öffnen.

    Ersetzen Sie die `com.microsoft.azure.zumo`-Zeichenfolge für **CFBundleURLName** durch Ihre Apple-Paket-ID.

5. Klicken Sie auf *Ausführen*, um die App zu starten, und melden Sie sich an. Nach der Anmeldung sollten Sie die Todo-Liste anzeigen und Änderungen vornehmen können.

Die App Service-Authentifizierung verwendet Inter-App Communication von Apple.  Weitere Informationen zu diesem Thema finden Sie in der [Apple-Dokumentation][2].
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure-Portal]: https://portal.azure.com

[iOS-Schnellstartprojekt]: app-service-mobile-ios-get-started.md

