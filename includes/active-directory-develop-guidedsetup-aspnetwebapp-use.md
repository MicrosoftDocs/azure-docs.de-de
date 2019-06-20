---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2b9d696ca896d0c8f0801f055000b9763d65d7ff
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121835"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Hinzufügen eines Controllers zum Verarbeiten von An- und Abmeldeanforderungen

Dieser Schritt veranschaulicht das Erstellen eines neuen Controllers, um An- und Abmeldeverfahren verfügbar zu machen.

1.  Klicken Sie mit der rechten Maustaste auf den Ordner `Controllers`, und wählen Sie `Add` > `Controller` aus.
2.  Wählen Sie `MVC (.NET version) Controller – Empty` aus.
3.  Klicken Sie auf *Hinzufügen*.
4.  Vergeben Sie den Namen `HomeController`, und klicken Sie auf *Hinzufügen*.
5.  Fügen Sie der Klasse *OWIN*-Verweise hinzu:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Fügen Sie die beiden nachstehenden Methoden hinzu, um die An- und Abmeldung bei Ihrem Controller zu verarbeiten, indem Sie eine Authentifizierungsaufforderung per Code auslösen:
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Erstellen der Startseite der App zum Anmelden von Benutzern über eine Anmeldeschaltfläche

Erstellen Sie in Visual Studio eine neue Ansicht, um die Schaltfläche „Anmelden“ hinzufügen und Benutzerinformationen nach der Authentifizierung anzuzeigen:

1.  Klicken Sie mit der rechten Maustaste auf den Ordner `Views\Home`, und wählen Sie `Add View` aus.
2.  Vergeben Sie den Namen `Index`.
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

<!--start-collapse-->
> ### <a name="more-information"></a>Weitere Informationen
> Auf dieser Seite wird eine Anmeldeschaltfläche im SVG-Format mit schwarzem Hintergrund hinzugefügt:<br/>![Bei Microsoft anmelden](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Weitere Anmeldeschaltflächen finden Sie auf [dieser Seite](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Brandingrichtlinien").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Hinzufügen eines Controllers zum Anzeigen von Ansprüchen des Benutzers
Dieser Controller veranschaulicht die Verwendungen des `[Authorize]`-Attributs, um einen Controller zu schützen. Dieses Attribut beschränkt den Zugriff auf den Controller, indem nur authentifizierte Benutzer zugelassen werden. Der folgende Code verwendet das Attribut zum Anzeigen von Benutzeransprüchen, die als Teil der Anmeldung abgerufen wurden.

1.  Klicken Sie mit der rechten Maustaste auf den Ordner `Controllers`, und wählen Sie `Add` > `Controller` aus.
2.  Wählen Sie `MVC {version} Controller – Empty` aus.
3.  Klicken Sie auf *Hinzufügen*.
4.  Vergeben Sie den Namen `ClaimsController`.
5.  Ersetzen Sie den Code Ihrer „Controller“-Klasse durch den folgenden Code. Dadurch wird das `[Authorize]`-Attribut der Klasse hinzugefügt:

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
    
            //You get the user’s first and last name below:
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

<!--start-collapse-->
> ### <a name="more-information"></a>Weitere Informationen
> Aufgrund der Verwendung des `[Authorize]`-Attributs können alle Methoden dieses Controllers nur ausgeführt werden, wenn der Benutzer authentifiziert ist. Wenn der Benutzer nicht authentifiziert ist und versucht, auf den Controller zuzugreifen, löst OWIN eine Authentifizierungsaufforderung aus und zwingt den Benutzer, sich zu authentifizieren. Der vorherige Code sucht in der Liste der Ansprüche nach bestimmten Benutzerattributen, die im ID-Token des Benutzers enthalten sind. Diesen Attribute enthalten den vollständigen Namen des Benutzers und den Benutzername sowie die GUID des Antragstellers. Sie enthalten auch die *Mandanten-ID*, die die ID der Organisation des Benutzers darstellt. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Erstellen einer Ansicht zum Anzeigen der Ansprüche des Benutzers

Erstellen Sie in Visual Studio eine neue Ansicht zum Anzeigen der Ansprüche des Benutzers auf einer Webseite:

1.  Klicken Sie mit der rechten Maustaste auf den Ordner `Views\Claims`, und wählen Sie `Add View` aus.
2.  Vergeben Sie den Namen `Index`.
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
