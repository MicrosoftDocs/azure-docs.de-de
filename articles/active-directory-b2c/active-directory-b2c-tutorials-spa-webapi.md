---
title: 'Tutorial: Gewähren des Zugriffs auf eine ASP.NET Core-Web-API über eine Single-Page-Webanwendung – Azure Active Directory B2C | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie mit Active Directory B2C eine .NET Core-Web-API schützen und sie über eine einseitige App aufrufen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 609e7bc4104a445c5d77109c9470fb487b2f1336
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507746"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Gewähren des Zugriffs auf eine ASP.NET Core-Web-API über eine Single-Page-Webanwendung mithilfe von Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie eine durch Azure Active Directory (Azure AD) B2C geschützte ASP.NET Core-Web-API-Ressource über eine Single-Page-Webanwendung aufrufen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Konfigurieren des Beispiels für die Verwendung der Anwendung

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die erforderlichen Schritte unter [Tutorial: Aktivieren der Authentifizierung einseitiger Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md) aus.

## <a name="add-a-web-api-application"></a>Hinzufügen einer Web-API-Anwendung

Web-API-Ressourcen müssen bei Ihrem Mandanten registriert werden, damit sie geschützte Ressourcenanforderungen von Clientanwendungen, die ein Zugriffstoken bereitstellen, akzeptieren und darauf reagieren können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein. Beispiel: *webapi1*.
6. Wählen Sie für **Web-App/Web-API einschließen** und n**Impliziten Ablauf zulassen** die Option **Ja**.
7. Geben Sie für **Antwort-URLs** einen Endpunkt ein, an den Azure AD B2C von Ihrer App angeforderte Token zurückgibt. Das Beispiel in diesem Tutorial wird lokal ausgeführt und lauscht an `https://localhost:5000`.
8. Geben Sie für **App-ID-URI** den für Ihre Web-API verwendeten Bezeichner ein. Der vollständige Bezeichner-URI (einschließlich der Domäne) wird für Sie generiert. Beispiel: `https://contosotenant.onmicrosoft.com/api`.
9. Klicken Sie auf **Create**.
10. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der Webanwendung verwenden.

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. So können beispielsweise einige Benutzer Lese- und Schreibzugriff haben, während andere Benutzer nur über Leseberechtigungen verfügen. In diesem Tutorial definieren Sie Leseberechtigungen für die Web-API.

1. Wählen Sie **Anwendungen** und dann *webapi1* aus.
2. Wählen Sie **Veröffentlichte Bereiche** aus.
3. Geben Sie unter **Bereich** den Bereich `Hello.Read` und als Beschreibung `Read access to hello` ein.
4. Geben Sie unter **Bereich** den Bereich `Hello.Write` und als Beschreibung `Write access to hello` ein.
5. Klicken Sie auf **Speichern**.

Mit den veröffentlichten Bereichen können einer Client-App Berechtigungen für die Web-API gewährt werden.

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Wenn Sie über eine Anwendung eine geschützte Web-API aufrufen möchten, müssen Sie Ihrer Anwendung Berechtigungen für die API erteilen. Im vorbereitenden Tutorial haben Sie in Azure AD B2C eine Webanwendung namens *webapp1* erstellt. Sie verwenden diese Anwendung für den Aufruf der Web-API.

1. Wählen Sie **Anwendungen** und dann *webapp1* aus.
2. Wählen Sie **API-Zugriff** und dann **Hinzufügen** aus.
3. Wählen Sie in der Dropdownliste **API auswählen** die Web-API *webapi1* aus.
4. Wählen Sie in der Dropdownliste **Bereiche auswählen** die Bereiche **Hello.Read** und **Hello.Write** aus, die Sie zuvor festgelegt haben.
5. Klicken Sie auf **OK**.

**My sample single page app** wird zum Aufrufen der geschützten API **Hello Core API** registriert. Ein Benutzer authentifiziert sich mit Azure AD B2C, um die Single-Page-Webanwendung zu verwenden. Die Single-Page-Webanwendung holt eine Autorisierungsgewährung von Azure AD B2C ein, um auf die geschützte Web-API zuzugreifen.

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

Nach dem Registrieren der Web-API und dem Definieren von Bereichen konfigurieren Sie den Web-API-Code für die Verwendung Ihres Azure AD B2C-Mandanten. In diesem Tutorial konfigurieren Sie eine .NET Core-Beispielwebanwendung, die Sie von GitHub herunterladen können. [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip), oder klonen Sie die Beispiel-Web-App aus GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurieren der Web-API

1. Öffnen Sie die Projektmappe **B2C-WebAPI.sln** in Visual Studio.
2. Öffnen Sie die Datei **appsettings.json**. Aktualisieren Sie die folgenden Werte, um die Web-API zur Verwendung Ihres Mandanten zu konfigurieren:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Aktivieren von CORS

Damit Ihre Single-Page-Webanwendung die ASP.NET Core-Web-API aufrufen kann, müssen Sie [CORS](https://docs.microsoft.com/aspnet/core/security/cors) aktivieren.

1. Fügen Sie in **Startup.cs** CORS zur `ConfigureServices()`-Methode hinzu.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. Konfigurieren Sie in **Startup.cs** CORS in der `Configure()`-Methode.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Öffnen Sie die Datei **launchSettings.json** unter **Eigenschaften**, suchen Sie nach der Einstellung **iisSettings** *applicationURL*, und legen Sie die Portnummer auf die Nummer fest, die für die API-Antwort-URL `http://localhost:5000` registriert ist.

### <a name="configure-the-single-page-application"></a>Konfigurieren der Single-Page-Webanwendung

Die Single-Page-Webanwendung verwendet Azure AD B2C für die Benutzerregistrierung und -anmeldung und ruft die geschützte ASP.NET Core-Web-API auf. Sie aktualisieren die Single-Page-Webanwendung, sodass sie die .NET Core-Web-API aufruft.

Gehen Sie zum Ändern der App-Einstellungen wie folgt vor:

1. Öffnen Sie die Datei `index.html` .
2. Konfigurieren Sie das Beispiel mit den Registrierungsinformationen für den Azure AD B2C-Mandanten. Fügen Sie im folgenden Code Ihren Mandantennamen zu **b2cScopes** hinzu, und ändern Sie den **webApi**-Wert in den zuvor verzeichneten *applicationURL*-Wert:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Ausführen der SPA-Anwendung und der Web-API

Sie müssen die Node.js-Single-Page-Webanwendung und die .NET Core-Web-API ausführen.

### <a name="run-the-aspnet-core-web-api"></a>Ausführen der ASP.NET Core-Web-API 

Drücken Sie **F5**, um die Projektmappe **B2C-WebAPI.sln** in Visual Studio zu debuggen.

Beim Starten des Projekts wird in Ihrem Standardbrowser eine Webseite mit dem Hinweis angezeigt, dass die Web-API für Anforderungen zur Verfügung steht.

### <a name="run-the-single-page-app"></a>Ausführen der einseitigen App

1. Starten Sie eine Node.js-Eingabeaufforderung.
2. Wechseln Sie zum Verzeichnis mit dem Node.js-Beispiel. Beispiel: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Führen Sie die folgenden Befehle aus:
    ```
    npm install && npm update
    node server.js
    ```

    Im Konsolenfenster wird die Portnummer angezeigt, unter der die Anwendung gehostet wird.
    
    ```
    Listening on port 6420...
    ```

4. Navigieren Sie in einem Browser zur Adresse `http://localhost:6420`, um die Anwendung anzuzeigen.
5. Melden Sie sich mit der E-Mail-Adresse und dem Kennwort an, die Sie im Tutorial [Aktivieren der Authentifizierung einseitiger Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md) verwendet haben.
6. Klicken Sie auf **API aufrufen**.

Nach dem Registrieren oder Anmelden mit einem Benutzerkonto ruft das Beispiel die geschützte Web-API auf und gibt ein Ergebnis zurück.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Konfigurieren des Beispiels für die Verwendung der Anwendung

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
