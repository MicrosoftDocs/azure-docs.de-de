---
title: 'Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer ASP.NET-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Schnellstart erfahren Sie, wie Sie „Mit Microsoft anmelden“ mit OpenID Connect in eine ASP.NET-Web-App implementieren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 658069f8c8007be2c1b424d8ccff687b986ac237
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030937"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Schnellstart: Hinzufügen der Microsoft Identity Platform-Anmeldung zu einer ASP.NET-Web-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine ASP.NET-Web-App Benutzer aus einer beliebigen Azure AD-Organisation (Azure Active Directory) anmelden kann. 

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).
> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Voraussetzungen
>
> * Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2+](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden:
> * [Express] [Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuell] [Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zum neuen Bereich [Azure-Portal – App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels
>
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `ASPNET-Quickstart`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Fügen Sie unter `https://localhost:44368/` **Umleitungs-URI** hinzu, und wählen Sie **Registrieren** aus.
> 1. Wählen Sie im linken Navigationsbereich im Abschnitt „Verwalten“ die Option **Authentifizierung** aus.
> 1. Wählen Sie im Unterabschnitt **Implizite Genehmigung** die Option **ID-Token** aus.
> 1. Wählen Sie **Speichern** aus.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
> Damit das Codebeispiel für diesen Schnellstart funktioniert, müssen Sie einen Antwort-URI als `https://localhost:44368/` hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Diese Änderung für mich vornehmen]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesem Attribut konfiguriert.

#### <a name="step-2-download-your-project"></a>Schritt 2: Herunterladen Ihres Projekts

> [!div renderon="docs"]
> [Herunterladen der Visual Studio 2019-Projektmappe](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Führen Sie das Projekt mit Visual Studio 2019 aus.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip).

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
> Wir haben das Projekt mit Werten der Eigenschaften ihrer App konfiguriert.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Schritt 3: Ausführen Ihres Visual Studio-Projekts

1. Extrahieren Sie die ZIP-Datei in einen lokalen Ordner, der sich näher am Stammordner befindet, beispielsweise **C:\Azure-Samples**.
1. Öffnen Sie die Projektmappe in Visual Studio (AppModelv2-WebApp-OpenIDConnect-DotNet.sln).
1. Abhängig von der verwendeten Visual Studio-Version müssen Sie möglicherweise mit der rechten Maustaste auf das Projekt `AppModelv2-WebApp-OpenIDConnect-DotNet` und **Wiederherstellen der NuGet-Pakete** klicken.
1. Öffnen Sie die Paket-Manager-Konsole (Anzeigen > Andere Fenster > Paket-Manager-Konsole), und führen Sie `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r` aus.

> [!div renderon="docs"]
> 5. Bearbeiten Sie **Web.config**, und ersetzen Sie die Parameter `ClientId` und `Tenant` durch Folgendes:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    ```
>    Hierbei gilt:
> - `Enter_the_Application_Id_here` ist die Anwendungs-ID für die von Ihnen registrierte Anwendung.
> - `Enter_the_Tenant_Info_Here` ist eine der folgenden Optionen:
>   - Unterstützt Ihre Anwendung **Nur meine Organisation**, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (etwa „contoso.onmicrosoft.com“).
>   - Unterstützt Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis**, ersetzen Sie diesen Wert durch `organizations`.
>   - Unterstützt Ihre Anwendung **Alle Microsoft-Kontobenutzer** ersetzen Sie diesen Wert durch `common`.
>
> > [!TIP]
> > - Die Werte für *Anwendungs-ID*, *Verzeichnis-ID (Mandant)* und *Unterstützte Kontotypen* finden Sie auf der Seite **Übersicht**.
> > - Stellen Sie sicher, dass der Wert für `redirectUri` in **Web.config** dem **Umleitungs-URI** entspricht, der für die App-Registrierung in Azure AD definiert wurde. (Ist dies nicht der Fall, navigieren Sie zum Menü **Authentifizierung** für die App-Registrierung, und aktualisieren Sie den **UMLEITUNGS-URI** entsprechend.)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Weitere Informationen

Dieser Abschnitt gibt einen Überblick über den Code, der für die Anmeldung von Benutzern erforderlich ist. Diese Übersicht kann hilfreich sein, um die Funktionsweise des Codes und die Hauptargumente zu verstehen und zu ermitteln, ob Sie einer vorhandenen ASP.NET Core-Anwendung eine Anmeldung hinzufügen möchten.

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>NuGet-Pakete der OWIN-Middleware

Sie können die Authentifizierungspipeline mit cookiebasierter Authentifizierung über OpenID Connect in ASP.NET mit OWIN-Middlewarepaketen einrichten. Sie können diese Pakete installieren, indem Sie die folgenden Befehle in der **Paket-Manager-Konsole** von Visual Studio ausführen:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN-Startklasse

Die OWIN-Middleware verwendet eine *Startklasse*, die bei der Initialisierung des Hostingprozesses ausgeführt wird. In diesem Schnellstart handelt es sich um die Datei *startup.cs* im Stammordner. Der folgende Code zeigt die Parameter, die von diesem Schnellstart verwendet werden:

```csharp
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
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Hierbei gilt:  | BESCHREIBUNG |
> |---------|---------|
> | `ClientId`     | Die Anwendungs-ID der im Azure-Portal registrierten Anwendung. |
> | `Authority`    | Der STS-Endpunkt für den zu authentifizierenden Benutzer. Normalerweise `https://login.microsoftonline.com/{tenant}/v2.0` für die öffentliche Cloud, wobei {tenant} der Name Ihres Mandanten, Ihre Mandanten-ID oder *common* für einen Verweis auf den allgemeinen Endpunkt (verwendet für mehrinstanzenfähige Anwendungen) ist. |
> | `RedirectUri`  | Die URL, an die Benutzer nach der Authentifizierung über den Microsoft Identity Platform-Endpunkt umgeleitet werden. |
> | `PostLogoutRedirectUri`     | Die URL, an die Benutzer nach der Abmeldung umgeleitet werden. |
> | `Scope`     | Die Liste der angeforderten Bereiche, getrennt durch Leerzeichen. |
> | `ResponseType`     | Die Anforderung, dass die Antwort von der Authentifizierung ein ID-Token enthält. |
> | `TokenValidationParameters`     | Eine Liste von Parametern für die Tokenüberprüfung. In diesem Fall wird `ValidateIssuer` auf `false` festgelegt, um anzugeben, dass Anmeldungen von beliebigen persönlichen oder Geschäfts-, Schul- oder Unikontotypen akzeptiert werden können. |
> | `Notifications`     | Eine Liste von Delegaten, die für verschiedene *OpenIdConnect*-Nachrichten ausgeführt werden können. |


> [!NOTE]
> Zur Vereinfachung dieser Schnellstartanleitung wird `ValidateIssuer = false` festgelegt. In echten Anwendungen müssen Sie den Aussteller überprüfen.
> Informationen dazu finden Sie in den Beispielen.

### <a name="initiate-an-authentication-challenge"></a>Initiieren einer Authentifizierungsaufforderung

Sie können den Benutzer zur Anmeldung zwingen, indem Sie eine Authentifizierungsaufforderung in Ihrem Controller anfordern:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Das Anfordern einer Authentifizierungsaufforderung mit der Methode oben ist optional und wird normalerweise verwendet, wenn Sie möchten, dass auf eine Ansicht sowohl durch authentifizierte als auch durch nicht authentifizierte Benutzer zugegriffen werden kann. Alternativ können Sie Controller mit der im nächsten Abschnitt beschriebenen Methode schützen.

### <a name="protect-a-controller-or-a-controllers-method"></a>Schützen eines Controllers oder der Methode eines Controllers

Sie können einen Controller oder Controlleraktionen mithilfe des `[Authorize]`-Attributs schützen. Dieses Attribut schränkt den Zugriff auf den Controller oder die Aktionen ein, indem es nur authentifizierten Benutzern den Zugriff auf die Aktionen im Controller erlaubt. Dies bedeutet, dass die Authentifizierungsaufforderung automatisch erfolgt, wenn ein *nicht authentifizierter* Benutzer versucht, auf eine der Aktionen oder den Controller zuzugreifen, die mit dem Attribut `[Authorize]` versehen sind.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie das ASP.NET-Tutorial aus, um eine vollständige Schritt-für-Schritt-Anleitung zum Erstellen von Anwendungen und neuen Features zu erhalten, einschließlich einer vollständigen Erläuterung dieses Schnellstarts.

> [!div class="nextstepaction"]
> [Hinzufügen von „Mit Microsoft anmelden“ zu einer ASP.NET-Web-App](tutorial-v2-asp-webapp.md)
