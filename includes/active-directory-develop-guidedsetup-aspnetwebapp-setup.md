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
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: dcfc341b89a3cfebcb5538f88481fd2fbb2936a7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121805"
---
## <a name="set-up-your-project"></a>Einrichten des Projekts

Dieser Abschnitt zeigt die Schritte zum Installieren und Konfigurieren der Authentifizierungspipeline über OWIN-Middleware für ein ASP.NET-Projekt mithilfe von OpenID Connect.

> Möchten Sie stattdessen das Visual Studio-Projekt dieses Beispiels herunterladen? [Laden Sie ein Projekt herunter](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip), und fahren Sie mit dem Schritt [Konfiguration](#register-your-application) fort, um das Codebeispiel vor der Ausführung zu konfigurieren.

### <a name="create-your-aspnet-project"></a>Erstellen des ASP.NET-Projekts

1. In Visual Studio: `File` > `New` > `Project`
2. Wählen Sie `ASP.NET Web Application (.NET Framework)` unter *Visual C# \Web* aus.
3. Geben Sie der Anwendung einen Namen, und klicken Sie auf *OK*.
4. Wählen Sie `Empty` aus, und aktivieren Sie das Kontrollkästchen, um `MVC`-Verweise hinzuzufügen.

## <a name="add-authentication-components"></a>Hinzufügen von Authentifizierungskomponenten

1. In Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Fügen Sie *NuGet-Pakete für OWIN-Middleware* hinzu, indem Sie im Fenster „Paket-Manager-Konsole“ Folgendes eingeben:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Informationen zu diesen Bibliotheken
> Die oben genannten Bibliotheken ermöglichen einmaliges Anmelden (Single Sign-On, SSO) mithilfe von OpenID Connect über die cookiebasierte Authentifizierung. Nachdem die Authentifizierung abgeschlossen ist und das Token, das den Benutzer darstellt, an Ihre Anwendung gesendet wurde, erstellt die OWIN-Middleware ein Sitzungscookie. Der Browser verwendet dann dieses Cookie bei nachfolgenden Anforderungen, damit der Benutzer sein Kennwort nicht erneut eingeben muss. Außerdem ist keine weitere Überprüfung erforderlich.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurieren der Authentifizierungspipeline

Die folgenden Schritte dienen zum Erstellen der OWIN-Middleware-Klasse „Startup“, um die OpenID Connect-Authentifizierung zu konfigurieren. Diese Klasse wird automatisch ausgeführt, wenn der IIS-Prozess gestartet wird.

> [!TIP]
> Wenn das Projekt nicht die Datei `Startup.cs` im Stammordner enthält:
> 1. Klicken Sie mit der rechten Maustaste auf den Stammordner des Projekts: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Vergeben Sie den Namen `Startup.cs`.
>
>> Stellen Sie sicher, dass die ausgewählte Klasse eine OWIN-Klasse des Typs „Startup“ und keine C#-Standardklasse ist. Bestätigen Sie dies, indem Sie überprüfen, ob `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` über dem Namespace angezeigt wird.

1. Fügen Sie *OWIN*- und *Microsoft.IdentityModel*-Verweise zu `Startup.cs` hinzu:

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

2. Ersetzen Sie die „Startup“-Klasse durch den folgenden Code:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
> Zur Vereinfachung dieser Schnellstartanleitung wird `ValidateIssuer = false` festgelegt. In echten Anwendungen müssen Sie den Aussteller überprüfen. Sehen Sie sich die Beispiele an, um sich mit der Vorgehensweise vertraut zu machen.

<!--start-collapse-->
> ### <a name="more-information"></a>Weitere Informationen
> Die Parameter, die Sie in *OpenIDConnectAuthenticationOptions* bereitstellen, dienen als Koordinaten für die Anwendung zur Kommunikation mit Azure AD. Da die OpenID Connect-Middleware im Hintergrund Cookies verwendet, müssen Sie außerdem die Cookieauthentifizierung einrichten (siehe den Code oben). Der *ValidateIssuer*-Wert weist OpenIdConnect an, den Zugriff nicht auf eine bestimmte Organisation zu beschränken.
<!--end-collapse-->
