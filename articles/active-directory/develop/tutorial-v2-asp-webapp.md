---
title: 'Tutorial: Erstellen einer ASP.NET-Web-App, die Microsoft Identity Platform für die Authentifizierung verwendet'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine ASP.NET-Webanwendung, die Microsoft Identity Platform und die OWIN-Middleware verwendet, um die Benutzeranmeldung zu ermöglichen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 0dad7add63102d462a2111f1ecf12ae43dae123c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996184"
---
# <a name="tutorial-add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Tutorial: Hinzufügen der Anmeldung bei Microsoft zu einer ASP.NET-Web-App

In diesem Tutorial erstellen Sie eine ASP.NET MVC-Web-App, in der Benutzer über die OWIN-Middleware (Open Web Interface for .NET) und die Microsoft Identity Platform angemeldet werden.

Am Ende dieser Anleitung kann Ihre Anwendung Anmeldungen von persönlichen Konten (etwa outlook.com und live.com) akzeptieren. Darüber hinaus können sich Geschäfts-, Schul- und Unikonten von Unternehmen oder Organisationen, die in Microsoft Identity Platform integriert wurden, bei Ihrer App anmelden.

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Erstellen eines Projekts namens *ASP.NET-Webanwendung* in Visual Studio
> * Hinzufügen der offenen Webschnittstelle für die .NET (OWIN)-Middlewarekomponenten
> * Hinzufügen von Code zum Unterstützen der Benutzeranmeldung und -abmeldung
> * Registrieren der App im Azure-Portal
> * Testen der App

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) mit installierter Workload **ASP.NET und Webentwicklung**

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funktionsweise der über diesen Leitfaden generierten Beispiel-App

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Die erstellte Beispielanwendung basiert auf dem Szenario, in dem Sie den Browser für den Zugriff auf eine ASP.NET-Website verwenden und aufgefordert werden, sich über die Schaltfläche „Anmelden“ zu authentifizieren. In diesem Szenario wird ein Großteil der Arbeit zum Rendern der Webseite auf dem Server erledigt.

## <a name="libraries"></a>Bibliotheken

In dieser Anleitung werden die folgenden Bibliotheken verwendet:

|Bibliothek|BESCHREIBUNG|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware, die einer Anwendung das Verwenden von OpenIDConnect für die Authentifizierung ermöglicht|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, die einer Anwendung das Beibehalten der Benutzersitzung mithilfe von Cookies ermöglicht|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware, die das Ausführen von OWIN-basierten Anwendungen in Internetinformationsdiensten (Internet Information Services, IIS) unter Verwendung der ASP.NET-Anforderungspipeline ermöglicht|

## <a name="set-up-your-project"></a>Einrichten des Projekts

In diesem Abschnitt werden das Installieren und Konfigurieren der Authentifizierungspipeline über OWIN-Middleware für ein ASP.NET-Projekt mithilfe von OpenID Connect beschrieben.

> Möchten Sie stattdessen das Visual Studio-Projekt dieses Beispiels herunterladen? [Laden Sie ein Projekt herunter](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip), und fahren Sie mit dem Schritt [Registrieren Ihrer Anwendung](#register-your-application) fort, um das Codebeispiel vor der Ausführung zu konfigurieren.

### <a name="create-your-aspnet-project"></a>Erstellen des ASP.NET-Projekts

1. In Visual Studio: Klicken Sie auf **Datei** > **Neu** > **Projekt**.
2. Wählen Sie unter **Visual C#\Web** die Option **ASP.NET-Webanwendung (.NET Framework)** aus.
3. Geben Sie der Anwendung einen Namen, und klicken Sie auf **OK**.
4. Wählen Sie **Leer** aus, und aktivieren Sie dann das Kontrollkästchen, um **MVC**-Verweise hinzuzufügen.

## <a name="add-authentication-components"></a>Hinzufügen von Authentifizierungskomponenten

1. In Visual Studio: Navigieren Sie zu **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.
2. Fügen Sie *NuGet-Pakete für OWIN-Middleware* hinzu, indem Sie im Fenster „Paket-Manager-Konsole“ Folgendes eingeben:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>Informationen zu diesen Bibliotheken
Diese Bibliotheken ermöglichen einmaliges Anmelden (Single Sign-On, SSO) mithilfe von OpenID Connect über die cookiebasierte Authentifizierung. Nachdem die Authentifizierung abgeschlossen ist und das Token, das den Benutzer darstellt, an Ihre Anwendung gesendet wurde, erstellt die OWIN-Middleware ein Sitzungscookie. Der Browser verwendet dann dieses Cookie bei nachfolgenden Anforderungen, damit der Benutzer sein Kennwort nicht erneut eingeben muss. Außerdem ist keine weitere Überprüfung erforderlich.

## <a name="configure-the-authentication-pipeline"></a>Konfigurieren der Authentifizierungspipeline

Die folgenden Schritte dienen zum Erstellen der Startup-Klasse der OWIN-Middleware, um die OpenID Connect-Authentifizierung zu konfigurieren. Diese Klasse wird automatisch ausgeführt, wenn der IIS-Prozess gestartet wird.

> [!TIP]
> Wenn das Projekt nicht die Datei `Startup.cs` im Stammordner enthält:
> 1. Klicken Sie mit der rechten Maustaste auf den Stammordner des Projekts, und wählen sie dann **Hinzufügen** > **Neues Element** > **OWIN-Startup-Klasse** aus.<br/>
> 2. Nennen Sie es **Startup.cs**.
>
>> Stellen Sie sicher, dass die ausgewählte Klasse eine OWIN-Klasse des Typs „Startup“ und keine C#-Standardklasse ist. Überprüfen Sie dazu, ob [assembly: OwinStartup(typeof({NameSpace}.Startup))] oberhalb des Namespace angezeigt wird.

1. Fügen Sie *OWIN*- und *Microsoft.IdentityModel*-Verweise zu „Startup.cs“ hinzu:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Ersetzen Sie die Startup-Klasse durch den folgenden Code:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Zur Vereinfachung dieser Schnellstartanleitung wird `ValidateIssuer = false` festgelegt. In echten Anwendungen müssen Sie den Aussteller überprüfen.
> Informationen dazu finden Sie in den Beispielen.

### <a name="more-information"></a>Weitere Informationen

Die Parameter, die Sie in *OpenIDConnectAuthenticationOptions* bereitstellen, dienen als Koordinaten für die Anwendung zur Kommunikation mit Microsoft Identity Platform. Da die OpenID Connect-Middleware im Hintergrund Cookies verwendet, müssen Sie außerdem die Cookieauthentifizierung einrichten, wie im Code oben gezeigt. Der *ValidateIssuer*-Wert weist OpenIdConnect an, den Zugriff nicht auf eine bestimmte Organisation zu beschränken.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Hinzufügen eines Controllers zum Verarbeiten von An- und Abmeldeanforderungen

Führen Sie die folgenden Schritte zum Erstellen eines neuen Controllers aus, um An- und Abmeldeverfahren verfügbar zu machen.

1.  Klicken Sie mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie **Hinzufügen** > **Controller** aus.
2.  Wählen Sie **MVC (.NET-Version)-Controller – Leer** aus.
3.  Wählen Sie **Hinzufügen**.
4.  Geben Sie ihm den Namen **HomeController**, und wählen Sie dann **Hinzufügen** aus.
5.  Fügen Sie der Klasse OWIN-Verweise hinzu:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Fügen Sie die folgenden beiden Methoden hinzu, um die An- und Abmeldung bei Ihrem Controller zu verarbeiten, indem Sie eine Authentifizierungsaufforderung auslösen:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }

    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Erstellen der App-Startseite für die Benutzeranmeldung

Erstellen Sie in Visual Studio eine neue Ansicht, um die Schaltfläche „Anmelden“ hinzuzufügen und Benutzerinformationen nach der Authentifizierung anzuzeigen:

1.  Klicken Sie mit der rechten Maustaste auf den Ordner **Ansichten\Start**, und wählen Sie **Ansicht hinzufügen** aus.
2.  Nennen Sie die neue Ansicht **Index**.
3.  Fügen Sie der Datei den folgenden HTML-Code hinzu, der die Schaltfläche „Anmelden“ enthält:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

### <a name="more-information"></a>Weitere Informationen
Auf dieser Seite wird eine Anmeldeschaltfläche im SVG-Format mit schwarzem Hintergrund hinzugefügt:<br/>![Schaltfläche „Anmelden mit Microsoft“](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Weitere Anmeldeschaltflächen finden Sie in den [Brandingrichtlinien](./howto-add-branding-in-azure-ad-apps.md "Brandingrichtlinien").

## <a name="add-a-controller-to-display-users-claims"></a>Hinzufügen eines Controllers zum Anzeigen von Ansprüchen des Benutzers
Dieser Controller veranschaulicht die Verwendungen des `[Authorize]`-Attributs, um einen Controller zu schützen. Dieses Attribut beschränkt den Zugriff auf den Controller, indem nur authentifizierte Benutzer zugelassen werden. Der folgende Code verwendet das Attribut zum Anzeigen von Benutzeransprüchen, die als Teil der Anmeldung abgerufen wurden:

1.  Klicken Sie mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie dann **Hinzufügen** > **Controller** aus.
2.  Wählen Sie **MVC {Version}-Controller – Leer** aus.
3.  Wählen Sie **Hinzufügen**.
4.  Nennen Sie diesen Controller **ClaimsController**.
5.  Ersetzen Sie den Code Ihrer Controllerklasse durch den folgenden Code. Dadurch wird der Klasse das Attribut `[Authorize]` hinzugefügt:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;

            //You get the user's first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;

            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;

            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;

            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;

            return View();
        }
    }
    ```

### <a name="more-information"></a>Weitere Informationen
Aufgrund der Verwendung des `[Authorize]`-Attributs können alle Methoden dieses Controllers nur ausgeführt werden, wenn der Benutzer authentifiziert ist. Wenn der Benutzer nicht authentifiziert ist und versucht, auf den Controller zuzugreifen, löst OWIN eine Authentifizierungsaufforderung aus und zwingt den Benutzer, sich zu authentifizieren. Der vorherige Code sucht in der Liste der Ansprüche nach bestimmten Benutzerattributen, die im ID-Token des Benutzers enthalten sind. Diese Attribute enthalten den vollständigen Namen des Benutzers und den Benutzername sowie die GUID des Antragstellers. Sie enthalten auch die *Mandanten-ID*, die die ID der Organisation des Benutzers darstellt.

## <a name="create-a-view-to-display-the-users-claims"></a>Erstellen einer Ansicht zum Anzeigen der Ansprüche des Benutzers

Erstellen Sie in Visual Studio eine neue Ansicht zum Anzeigen der Ansprüche des Benutzers auf einer Webseite:

1.  Klicken Sie mit der rechten Maustaste auf den Ordner **Ansichten\Ansprüche**, und wählen Sie **Ansicht hinzufügen** aus.
2.  Nennen Sie die neue Ansicht **Index**.
3.  Fügen Sie der Datei den folgenden HTML-Code hinzu:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Anwendung registrieren

Wenn Sie Ihre Anwendung registrieren und die Anwendungsregistrierungsinformationen Ihrer Projektmappe hinzufügen möchten, haben Sie zwei Möglichkeiten:

### <a name="option-1-express-mode"></a>Option 1: Expressmodi

Führen Sie zur schnellen Registrierung Ihrer Anwendung die folgenden Schritte aus:

1. Navigieren Sie zum neuen Bereich [Azure-Portal – App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.

### <a name="option-2-advanced-mode"></a>Option 2: Erweiterter Modus

Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:

1. Öffnen Sie Visual Studio, und führen Sie die folgenden Schritte aus:
   1. Wählen Sie im Projektmappen-Explorer das Projekt aus, und zeigen Sie das Eigenschaftenfenster an. (Drücken Sie F4, wenn es nicht angezeigt wird.)
   1. Ändern Sie „SSL aktiviert“ in `True`.
   1. Klicken Sie mit der rechten Maustaste in Visual Studio auf das Projekt, und wählen Sie **Eigenschaften** und dann die Registerkarte **Web** aus. Ändern Sie im Abschnitt **Server** die Einstellung **Projekt-URL** in die **SSL-URL**.
   1. Kopieren Sie die SSL-URL. Sie fügen diese URL im nächsten Schritt der Liste mit den Umleitungs-URLs im Registrierungsportal hinzu.<br/><br/>![Projekteigenschaften](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, wählen Sie rechts oben Ihr Konto aus, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Navigieren Sie auf der Microsoft Identity Platform für Entwickler zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908).
1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein:
   1. Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise **ASPNET-Tutorial**).
   1. Fügen Sie die SSL-URL, die Sie in Schritt 1 aus Visual Studio kopiert haben (z. B. `https://localhost:44368/`) unter **Antwort-URL** hinzu, und wählen Sie **Registrieren** aus.
1. Wählen Sie das Menü **Authentifizierung** und dann **ID-Token** unter **Implizite Genehmigung** aus. Wählen Sie dann **Speichern** aus.
1. Fügen Sie im Stammordner der Datei „web.config“ im Abschnitt `configuration\appSettings` Folgendes hinzu:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Ersetzen Sie `ClientId` durch die Anwendungs-ID, die Sie gerade registriert haben.
1. Ersetzen Sie `redirectUri` durch die SSL-URL Ihres Projekts.

## <a name="test-your-code"></a>Testen Ihres Codes

Um Ihre Anwendung in Visual Studio zu testen, drücken Sie F5, um das Projekt auszuführen. Der Browser wird geöffnet und leitet Sie zu „http://<span></span>localhost:{Port}“ weiter, wo die Schaltfläche **Bei Microsoft anmelden** angezeigt wird. Wählen Sie die Schaltfläche aus, um den Anmeldeprozess zu starten.

Wenn Sie Ihren Test ausführen möchten, verwenden Sie für die Anmeldung ein Azure AD-Konto (Geschäfts-, Schul- oder Unikonto) oder ein persönliches Microsoft-Konto (<span>live.</span>com oder <span>outlook.</span>com).

![Schaltfläche „Anmelden mit Microsoft“ auf der Anmeldeseite im Browser](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Mit einem Microsoft-Konto anmelden](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt

Anwendungen, die sich in die Microsoft Identity Platform integrieren lassen, folgen einem bestimmten Autorisierungsmodell, mit dem Benutzer und Administratoren den Zugriff auf Daten steuern können. Nachdem sich ein Benutzer mit Microsoft Identity Platform für den Zugriff auf diese Anwendung authentifiziert hat, wird er aufgefordert, seine Zustimmung für die von der Anwendung angeforderten Berechtigungen zu erteilen („Ihr grundlegendes Profil anzeigen“ und „Zugriff auf Daten beibehalten, für die Sie Zugriff erteilt haben“). Wenn der Benutzer diese Berechtigungen akzeptiert hat, wird er zu den Anwendungsergebnissen weitergeleitet. In folgenden Fällen wird dem Benutzer jedoch unter Umständen stattdessen die Seite **Need admin consent** (Administratoreinwilligung erforderlich) angezeigt:

- Der Anwendungsentwickler fügt zusätzliche Berechtigungen hinzu, für die die **Administratoreinwilligung** erforderlich ist.
- Der Mandant wurde (unter **Unternehmensanwendungen > Benutzereinstellungen**) so konfiguriert, dass Benutzer Apps den Zugriff auf Unternehmensdaten in ihrem Namen nicht gestatten können.

Weitere Informationen finden Sie unter [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](./v2-permissions-and-consent.md).

### <a name="view-application-results"></a>Anzeigen von Anwendungsergebnissen

Nachdem Sie sich angemeldet haben, wird der Benutzer zur Startseite Ihrer Website umgeleitet. Die Startseite ist die HTTPS-URL, die im Microsoft-Anwendungsregistrierungsportal in Ihre Anwendungsregistrierungsinformationen eingegeben wurde. Auf der Startseite werden die Willkommensnachricht *„Hallo \<user>“* , ein Link zum Abmelden und ein Link zum Anzeigen der Benutzeransprüche angezeigt. Über den Link für die Benutzeransprüche gelangen Sie zum zuvor erstellten Claims-Controller.

### <a name="view-the-users-claims"></a>Anzeigen der Ansprüche des Benutzers

Um die Benutzeransprüche anzuzeigen, wählen Sie den Link zum Navigieren zur Controlleransicht aus. Diese ist nur für authentifizierte Benutzer verfügbar.

#### <a name="view-the-claims-results"></a>Anzeigen der Anspruchsergebnisse

Nachdem Sie die Controlleransicht aufgerufen haben, sollten Sie eine Tabelle mit den grundlegenden Eigenschaften des Benutzers sehen:

|Eigenschaft |Wert |Beschreibung |
|---|---|---|
|**Name** |Vollständiger Name des Benutzers | Dies ist der Vorname und der Nachname des Benutzers.
|**Benutzername** |Benutzer<span>@domain.com</span> | Der zur Identifizierung des Benutzers verwendete Benutzername|
|**Subject** |Subject |Eine Zeichenfolge, die den Benutzer im Internet eindeutig identifiziert|
|**Tenant ID** |Guid | Dies ist eine **GUID**, die die Azure AD-Organisation des Benutzers eindeutig identifiziert.|

Darüber hinaus sollten Sie eine Tabelle aller Ansprüche sehen, die in der Authentifizierungsanforderung enthalten sind. Weitere Informationen finden Sie in der [Liste der in einem ID-Token enthaltenen Ansprüche](./id-tokens.md).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testen des Zugriffs auf eine Methode mit einem Authorize-Attribut (optional)

Führen Sie die folgenden Schritte aus, um den Zugriff als anonymer Benutzer auf einen mit dem `Authorize`-Attribut geschützten Controller zu testen:

1. Wählen Sie den Link zum Abmelden des Benutzers aus, und schließen Sie den Abmeldevorgang ab.
2. Geben Sie nun im Browser „http://<span></span>localhost:{Port}/claims“ ein, um auf den mit dem `Authorize`-Attribut geschützten Controller zuzugreifen.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Erwartete Ergebnisse nach dem Zugriff auf einen geschützten Controller

Sie werden für die Verwendung der geschützten Controlleransicht zur Authentifizierung aufgefordert.

## <a name="advanced-options"></a>Erweiterte Optionen

### <a name="protect-your-entire-website"></a>Schutz der gesamten Website

Zum Schutz der gesamten Website fügen Sie in der Datei **Global.asax** unter der Methode `Application_Start`dem Filter `GlobalFilters` das `AuthorizeAttribute`-Attribut hinzu:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>Einschränken des Anmeldezugriffs auf Ihre Anwendung

Wenn Sie die Anwendung nach dieser Anleitung erstellen, akzeptiert Ihre Anwendung standardmäßig sowohl Anmeldungen von persönlichen Konten (z. B. outlook.com, live.com u. a.) als auch von Geschäfts-, Schul- und Unikonten von Unternehmen oder Organisationen, die in Microsoft Identity Platform integriert wurden. Diese Option wird für SaaS-Anwendungen empfohlen.

Um den Anmeldezugriff des Benutzers auf Ihre Anwendung einzuschränken, stehen mehrere Optionen zur Verfügung:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Option 1: Beschränken der Benutzer auf die Active Directory-Instanz nur einer Organisation für die Anmeldung bei der Anwendung (Einzelmandant)

Diese Option wird häufig für *Branchenanwendungen* verwendet: Wenn Ihre Anwendung Anmeldungen nur von Konten akzeptieren soll, die zu einer bestimmten Azure AD-Instanz gehören (einschließlich *Gastkonten* dieser Instanz), gehen Sie wie folgt vor:

1. Ändern Sie in der Datei „web.config“ den Wert für den Parameter `Tenant` von `Common` in den Mandantennamen der Organisation, z. B. `contoso.onmicrosoft.com`.
2. Legen Sie in Ihrer [OWIN-Startup-Klasse](#configure-the-authentication-pipeline) das Argument `ValidateIssuer` auf `true` fest.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Option 2: Einschränken des Zugriffs auf Benutzer in einer bestimmten Liste von Organisationen

Sie können den Anmeldezugriff auf die Benutzerkonten beschränken, die in einer Azure AD-Organisation in der Liste der zulässigen Organisationen enthalten sind:
1. Legen Sie in Ihrer [OWIN-Startup-Klasse](#configure-the-authentication-pipeline) das Argument `ValidateIssuer` auf `true` fest.
2. Legen Sie den Wert des Parameters `ValidIssuers` auf die Liste der zulässigen Organisationen fest.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Option 3: Überprüfen von Ausstellern mithilfe einer benutzerdefinierten Methode

Sie können eine benutzerdefinierte Methode implementieren, um Aussteller mit dem **IssuerValidator**-Parameter zu überprüfen. Weitere Informationen zur Verwendung dieses Parameters finden Sie im Artikel zur [TokenValidationParameters-Klasse](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Aufrufen geschützter Web-APIs durch Web-Apps mit Microsoft Identity Platform:

> [!div class="nextstepaction"]
> [Aufrufen von Web-APIs durch Web-Apps](scenario-web-app-sign-user-overview.md)
