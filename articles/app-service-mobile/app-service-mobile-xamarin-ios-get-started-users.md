---
title: Erste Schritte mit der Authentifizierung für mobile Apps in Xamarin iOS
description: Erfahren Sie, wie Sie Mobile Apps zum Authentifizieren Ihrer Xamarin iOS-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: crdun
ms.openlocfilehash: 132909931291daf3aefddd5e1a44273050d98e06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62120908"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Hinzufügen der Authentifizierung zu Ihrer Xamarin.iOS-App
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App Service-App über Ihre Clientanwendung veranschaulicht. In diesem Lernprogramm fügen Sie dem Xamarin.iOS-Schnellstartprojekt durch Verwenden eines von App Service unterstützten Identitätsanbieters eine Authentifizierungsfunktion hinzu. Nach der erfolgreichen Authentifizierung und Autorisierung durch Ihre mobile App wird die Benutzer-ID angezeigt und Sie erhalten Zugriff auf beschränkte Tabellendaten.

Sie müssen zunächst das Lernprogramm [Erstellen einer Xamarin.iOS-App]abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Authentifizierungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)(in englischer Sprache).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Hinzufügen Ihrer App zu den zulässigen externen Umleitungs-URLs

Eine sichere Authentifizierung erfordert, dass Sie ein neues URL-Schema für Ihre App definieren. Dies ermöglicht dem Authentifizierungssystem die erneute Umleitung an Ihre App, sobald der Authentifizierungsprozess abgeschlossen ist. In diesem Tutorial verwenden wir ausschließlich das URL-Schema _appname_. Sie können jedoch ein beliebiges URL-Schema auswählen und verwenden. Es sollte für Ihre mobile Anwendung eindeutig sein. So aktivieren Sie die Umleitung auf der Serverseite

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) App Service aus.

2. Klicken Sie auf die Menüoption **Authentifizierung/Autorisierung**.

3. Geben Sie in **Zulässige externe Umleitungs-URLs** `url_scheme_of_your_app://easyauth.callback` ein.  **url_scheme_of_your_app** steht in dieser Zeichenfolge für das URL-Schema für Ihre mobile Anwendung.  Es sollte der normalen URL-Spezifikation für ein Protokoll folgen (nur aus Buchstaben und Zahlen bestehen und mit einem Buchstaben beginnen).  Notieren Sie sich die gewählte Zeichenfolge, da Sie später Ihren mobilen Anwendungscode mehrfach mit dem URL-Schema anpassen müssen.

4. Klicken Sie auf **OK**.

5. Klicken Sie auf **Speichern**.

## <a name="restrict-permissions-to-authenticated-users"></a>Einschränken von Berechtigungen für authentifizierte Benutzer
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Führen Sie das Clientprojekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Emulator aus. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde. Der Fehler wird in der Konsole des Debuggers protokolliert. Daher sollte in Visual Studio der Fehler im Ausgabefenster angezeigt werden.

    Dieser Fehler des Typs „Nicht autorisiert“ kommt vor, weil die App versucht, als nicht authentifizierter Benutzer auf Ihr Mobile App-Back-End zuzugreifen. Die Tabelle *TodoItem* erfordert jetzt eine Authentifizierung.

Als Nächstes aktualisieren Sie die Client-App, um Ressourcen vom mobilen App-Back-End mit einem authentifizierten Benutzer zu aktualisieren.

## <a name="add-authentication-to-the-app"></a>Hinzufügen von Authentifizierung zur App
In diesem Abschnitt modifizieren Sie die App, sodass vor der Anzeige von Daten ein Anmeldebildschirm angezeigt wird. Wenn die App gestartet wird, baut sie keine Verbindung zu App Service auf und zeigt keinerlei Daten an. Nachdem der Benutzer die Aktualisierungsgeste durchführt, wird der Anmeldebildschirm angezeigt, nach der erfolgreichen Anmeldung dann die Liste von To-Do-Objekten.

1. Öffnen Sie im Clientprojekt die Datei **QSTodoService.cs**, und fügen Sie der QSTodoService-Klasse nachstehende using-Anweisung und `MobileServiceUser` mit Accessor hinzu:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Fügen Sie die neue Methode **Authenticate** mit der folgenden Definition zu **QSTodoService** hinzu:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an **LoginAsync** übergebenen Wert in einen der folgenden Werte: _MicrosoftAccount_, _Twitter_, _Google_ oder _WindowsAzureActiveDirectory_.

3. Öffnen Sie **QSTodoListViewController.cs**. Ändern Sie die Methodendefinition von **ViewDidLoad**, und entfernen Sie den Aufruf von **RefreshAsync()** am Ende:

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. Ändern Sie die Methode **RefreshAsync** für die Authentifizierung, wenn die Eigenschaft **User** null ist. Fügen Sie folgenden Code oben in der Methodendefinition hinzu:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. Öffnen Sie **AppDelegate.cs**, und fügen Sie die folgende Methode hinzu:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Öffnen Sie die Datei **Info.plist**, und navigieren Sie im Abschnitt **Erweitert** zu **URL-Typen**. Konfigurieren Sie nun **Bezeichner** und **URL-Schemas** Ihres URL-Typs, und klicken Sie auf **URL-Typ hinzufügen**. **URL-Schemas** sollte mit „{url_scheme_of_your_app}“ identisch sein.
7. Führen Sie in Visual Studio mit Verbindung zum Mac-Host oder in Visual Studio für Mac das Clientprojekt auf einem Gerät oder Emulator aus. Überprüfen Sie, dass die App keine Daten anzeigt.

    Führen Sie die Aktualisierungsgeste durch Herunterziehen der Objektliste, was die Anzeige des Anmeldebildschirms auslöst. Nachdem Sie erfolgreich gültige Anmeldeinformationen eingegeben haben, zeigt die App die Liste der To-Do-Objekte an, und Sie können die Daten ändern.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Erstellen einer Xamarin.iOS-App]: app-service-mobile-xamarin-ios-get-started.md
