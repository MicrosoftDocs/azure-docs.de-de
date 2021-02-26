---
title: 'Tutorial: Gewähren des Zugriffs auf eine Node.js-Web-API über eine Desktopanwendung'
description: In diesem Tutorial erfahren Sie, wie Sie mit Active Directory B2C eine Node.js-Web-API schützen und sie über eine .NET-Desktop-App aufrufen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 12951f25feb6f3710b8d35fbc682caeeb480e788
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555619"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Gewähren des Zugriffs auf eine Node.js-Web-API über eine Desktop-App unter Verwendung von Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie eine durch Azure Active Directory B2C (Azure AD B2C) geschützte Node.js-Web-API über eine WPF-Desktop-App (Windows Presentation Foundation) aufrufen, die ebenfalls durch Azure AD B2C geschützt ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Aktualisieren des Beispiels für die Verwendung der Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die erforderlichen Schritte unter [Tutorial: Authentifizieren von Benutzern in einem nativen Desktopclient](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Hinzufügen einer Web-API-Anwendung

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. So können beispielsweise einige Benutzer Lese- und Schreibzugriff haben, während andere Benutzer nur über Leseberechtigungen verfügen. In diesem Tutorial definieren Sie Lese- und Schreibberechtigungen für die Web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Notieren Sie sich den Wert unter **BEREICHE** für den Bereich `demo.read`, der in einem späteren Schritt bei der Konfiguration der Desktopanwendung verwendet werden soll. Der vollständige Bereichswert ist ähnlich wie bei `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Um eine geschützte Web-API über eine native Clientanwendung aufzurufen, müssen Sie die Berechtigungen der registrierten nativen Clientanwendung der Web-API erteilen, die Sie in Azure AD B2C registriert haben.

Im vorbereitenden Tutorial haben Sie eine native Clientanwendung mit dem Namen *nativeapp1* registriert. In den folgenden Schritten wird die Registrierung der nativen Anwendung mit den API-Bereichen konfiguriert, die Sie im vorherigen Abschnitt für *webapi1* verfügbar gemacht haben. Dadurch kann die Desktopanwendung ein Zugriffstoken von Azure AD B2C abrufen, mit dem die Web-API bereichsbezogenen Zugriff auf ihre Ressourcen überprüfen und gewähren kann. Die Codebeispiele für die Desktopanwendung und die Web-API werden später im Tutorial konfiguriert und ausgeführt.

Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie unsere neue einheitliche Benutzeroberfläche **App-Registrierungen** oder unsere alte Benutzeroberfläche **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Wählen Sie **App-Registrierungen** und anschließend die native Clientanwendung aus, die Zugriff auf die API erhalten soll. Beispiel: *nativeapp1*.
1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**.
1. Wählen Sie unter **Konfigurierte Berechtigungen** die Option **Berechtigung hinzufügen** aus.
1. Wählen Sie die Registerkarte **Meine APIs** aus.
1. Wählen Sie die API aus, für die der nativen Clientanwendung Zugriff gewährt werden soll. Beispiel: *webapi1*.
1. Erweitern Sie unter **Berechtigung** den Eintrag **Demo**, und wählen Sie dann die zuvor definierten Bereiche aus. Beispiel: *demo.read* und *demo.write*
1. Wählen Sie **Berechtigungen hinzufügen** aus. Warten Sie einige Minuten, bevor Sie mit dem nächsten Schritt fortfahren.
1. Wählen Sie **Administratorzustimmung für (Name Ihres Mandanten) erteilen** aus.
1. Wählen Sie das derzeit angemeldete Administratorkonto aus, oder melden Sie sich mit einem Konto bei Ihrem Azure AD B2C-Mandanten an, dem mindestens die Rolle *Cloudanwendungsadministrator* zugewiesen wurde.
1. Wählen Sie **Akzeptieren** aus.
1. Wählen Sie **Aktualisieren** aus, und vergewissern Sie sich, dass für beide Bereiche unter **Status** der Status „Gewährt für...“ angezeigt wird. Es kann einige Minuten dauern, bis die Berechtigungen weitergegeben wurden.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Wählen Sie **Anwendungen (Legacy)** und dann *nativeapp1* aus.
1. Wählen Sie **API-Zugriff** und dann **Hinzufügen** aus.
1. Wählen Sie in der Dropdownliste **API auswählen** die Web-API *webapi1* aus.
1. Wählen Sie in der Dropdownliste **Bereiche auswählen** die Bereiche aus, die Sie zuvor definiert haben. Beispiel: *demo.read* und *demo.write*
1. Klicken Sie auf **OK**.

* * *

Ein Benutzer authentifiziert sich mit Azure AD B2C, um die WPF-Desktopanwendung zu verwenden. Die Desktopanwendung bezieht eine Autorisierungsgewährung von Azure AD B2C, um auf die geschützte Web-API zuzugreifen.

## <a name="configure-the-samples"></a>Konfigurieren der Beispiele

Die Web-API ist registriert, und Sie haben Bereiche und Berechtigungen konfiguriert. Nun konfigurieren Sie die Desktopanwendung und Web-API-Beispiele für die Verwendung Ihres Azure AD B2C-Mandanten.

### <a name="update-the-desktop-application"></a>Aktualisieren der Desktopanwendung

Als Voraussetzung für diesen Artikel haben Sie eine [WPF-Desktopanwendung](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) geändert, um die Anmeldung mit einem Benutzerflow bei Ihrem Azure AD B2C-Mandanten zu ermöglichen. In diesem Abschnitt aktualisieren Sie dieselbe Anwendung, um auf die zuvor registrierte Web-API *webapi1* zu verweisen.

1. Öffnen Sie die Projektmappe **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) in Visual Studio.
1. Öffnen Sie im Projekt **active-directory-b2c-wpf** die Datei *App.xaml.cs*, und suchen Sie die folgenden Variablendefinitionen.
    1. Ersetzen Sie den Wert der Variablen `ApiScopes` durch den Wert, den Sie zuvor beim Definieren des Bereichs **demo.read** notiert haben.
    1. Ersetzen Sie den Wert der Variablen `ApiEndpoint` durch den **Umleitungs-URI**, den Sie zuvor beim Registrieren der Web-API (z. B. *webapi1*) in Ihrem Mandanten notiert haben.

    Hier sehen Sie ein Beispiel:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Abrufen und Aktualisieren des Node.js-API-Beispiels

Laden Sie als Nächstes das Codebeispiel für die Node.js-Web-API von GitHub herunter, und konfigurieren Sie es für die Verwendung der Web-API, die Sie in Ihrem Azure AD B2C-Mandanten registriert haben.

[Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip), oder klonen Sie die Beispiel-Web-App aus GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Das Node.js-Web-API-Beispiel verwendet die Bibliothek „Passport.js“, um Azure AD B2C zum Schützen von API-Aufrufen zu aktivieren.

1. Öffnen Sie die Datei `index.js` .
1. Aktualisieren Sie diese Variablendefinitionen mit den folgenden Werten. Ändern Sie `<web-API-application-ID>` in die **Anwendungs-ID (Client)** der Web-API, die Sie zuvor registriert haben (*webapi1*). Ändern Sie `<your-b2c-tenant>` in den Namen des Azure AD B2C-Mandanten.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Da Sie die API lokal ausführen, aktualisieren Sie den Pfad in der Route für die GET-Methode in `/` und nicht in den Speicherort `/hello` der Demo-App:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Ausführen der Beispiele

### <a name="run-the-nodejs-web-api"></a>Ausführen der Node.js-Web-API

1. Starten Sie eine Node.js-Eingabeaufforderung.
2. Wechseln Sie zum Verzeichnis mit dem Node.js-Beispiel. Beispiel: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Führen Sie die folgenden Befehle aus:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Ausführen der Desktopanwendung

1. Öffnen Sie die Projektmappe **active-directory-b2c-wpf** in Visual Studio.
2. Drücken Sie **F5**, um die Desktop-App auszuführen.
3. Melden Sie sich mit der E-Mail-Adresse und dem Kennwort an, die Sie im Tutorial [Aktivieren der Authentifizierung von Desktop-Apps mit Konten unter Verwendung von Azure Active Directory B2C](tutorial-desktop-app.md) verwendet haben.
4. Wählen Sie die Schaltfläche **API aufrufen** aus.

Die Desktop Anwendung sendet eine Anforderung an die lokal ausgeführte Web-API, und bei Bestätigung eines gültigen Zugriffstokens wird der Anzeigename des angemeldeten Benutzers angezeigt.

![Anzeigename im oberen Bereich der WPF-Desktopanwendung](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Ihre durch Azure AD B2C geschützte Desktopanwendung ruft die lokal ausgeführt Web-API auf, die ebenfalls durch Azure AD B2C geschützt ist.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Aktualisieren des Beispiels für die Verwendung der Anwendung

> [!div class="nextstepaction"]
> [Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](add-identity-provider.md)