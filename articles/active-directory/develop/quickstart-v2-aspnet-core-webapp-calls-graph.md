---
title: 'Schnellstart: ASP.NET Core-Web-App zum Anmelden von Benutzern und Aufrufen von Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine App mithilfe von Microsoft.Identity.Web und OpenID Connect die Microsoft-Anmeldung in einer ASP.NET Core-Web-App implementiert und Microsoft Graph aufruft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 0a5d44f0cc42b80fa344e06f31275e87d265800e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103837"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Schnellstart: ASP.NET Core-Web-App zum Anmelden von Benutzern und Aufrufen von Microsoft Graph in deren Namen

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus. In diesem Beispiel wird gezeigt, wie eine ASP.NET Core-Web-App Benutzer aus einer beliebigen Azure Active Directory (Azure AD)-Organisation anmelden und Microsoft Graph aufrufen kann.  

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Voraussetzungen
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) oder [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registrieren und Herunterladen der Schnellstart-App
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden:
> * [Express] [Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuell] [Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zur Umgebung des Schnellstarts <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">Azure-Portal – App-Registrierungen</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels
>
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `AspNetCoreWebAppCallsGraph-Quickstart`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Geben Sie als **Umleitungs-URI** den URI `https://localhost:44321/signin-oidc` ein.
> 1. Wählen Sie **Registrieren**.
> 1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
> 1. Geben Sie `https://localhost:44321/signout-oidc` als **URL für Front-Channel-Abmeldung** an.
> 1. Wählen Sie **Speichern** aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **Zertifikate und Geheimnisse** > **Neuer geheimer Clientschlüssel**.
> 1. Geben Sie eine **Beschreibung** ein, zum Beispiel `clientsecret1`.
> 1. Wählen Sie **In 1 Jahr** als Ablaufdatum für das Geheimnis aus.
> 1. Wählen Sie **Hinzufügen** aus, und notieren Sie sofort den **Wert** des Geheimnisses zur späteren Verwendung. Der Wert des Geheimnisses wird *an keiner anderen Stelle angezeigt* und kann auch nicht auf andere Weise abgerufen werden. Verwahren Sie den Wert wie ein Kennwort an einem sicheren Ort.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
> Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen Sie den **Umleitungs-URI** `https://localhost:44321/signin-oidc` sowie `https://localhost:44321/signout-oidc` als **URL für Front-Channel-Abmeldung** in der App-Registrierung hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Diese Änderung für mich vornehmen]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-aspnet-core-project"></a>Schritt 2: Herunterladen des ASP.NET Core-Projekts

> [!div renderon="docs"]
> [ASP.NET Core-Lösung herunterladen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Führen Sie das Projekt aus.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip).

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
> Wir haben das Projekt mit Werten Ihrer App-Eigenschaften konfiguriert. Es ist nun ausführungsbereit.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Schritt 3: Konfigurieren Ihres ASP.NET Core-Projekts
> 1. Extrahieren Sie das ZIP-Archiv in einem lokalen Ordner in der Nähe des Stammverzeichnisses Ihres Laufwerks. Beispiel: *C:\Azure-Samples*.
> 1. Öffnen Sie die Projektmappe in Visual Studio 2019.
> 1. Öffnen Sie die Datei *appsettings.json*, und ändern Sie Folgendes:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - Ersetzen Sie `Enter_the_Application_Id_here` durch die **Anwendungs-ID (Client)** der im Azure-Portal registrierten Anwendung. Den Wert für **Anwendungs-ID (Client)** finden Sie auf der Seite **Übersicht** der App.
>    - Ersetzen Sie `common` durch eine der folgenden Optionen:
>       - Wenn Ihre Anwendung **Nur Konten in diesem Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch die **Verzeichnis-ID (Mandant)** (eine GUID) oder durch den **Mandantennamen** (beispielsweise `contoso.onmicrosoft.com`). Die **Verzeichnis-ID (Mandant)** finden Sie auf der Seite **Übersicht** der App.
>       - Unterstützt Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis**, ersetzen Sie diesen Wert durch `organizations`.
>       - Wenn Ihre Anwendung **Alle Microsoft-Kontobenutzer** unterstützt, behalten Sie `common` für diesen Wert bei.
>    - Ersetzen Sie `Enter_the_Client_Secret_Here` durch den **geheimen Clientschlüssel**, den Sie in einem früheren Schritt erstellt und notiert haben.
> 
> Ändern Sie in dieser Schnellstartanleitung keine anderen Werte in der Datei *appsettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Schritt 4: Erstellen und Ausführen der Anwendung
>
> Erstellen Sie die App in Visual Studio, und führen Sie sie aus. Wählen Sie hierzu im Menü **Debuggen** die Option **Debuggen starten** aus, oder drücken Sie `F5`.
>
> Sie werden zur Eingabe Ihrer Anmeldeinformationen aufgefordert und anschließend gebeten, Ihre Einwilligung für die von Ihrer App benötigten Berechtigungen zu geben. Wählen Sie in der Zustimmungsaufforderung **Akzeptieren** aus.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Einwilligungsdialogfeld mit den Berechtigungen, die die App vom Benutzer anfordert":::
>
> Nachdem Sie Ihre Zustimmung zu den angeforderten Berechtigungen erteilt haben, wird in der App angezeigt, dass Sie sich erfolgreich mit Ihren Azure Active Directory-Anmeldeinformationen angemeldet haben. Außerdem wird auf der Seite im Abschnitt „API-Ergebnis“ Ihre E-Mail-Adresse angezeigt. Diese wurde mithilfe von Microsoft Graph extrahiert.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Webbrowser mit der ausgeführten Web-App und dem angemeldeten Benutzer":::

## <a name="about-the-code"></a>Informationen zum Code

Dieser Abschnitt enthält eine Übersicht über den Code, der zum Anmelden von Benutzern und zum Aufrufen der Microsoft Graph-API in deren Namen erforderlich ist. Diese Übersicht kann hilfreich sein, um die Funktionsweise des Codes und die Hauptargumente zu verstehen und zu ermitteln, ob Sie einer vorhandenen ASP.NET Core-Anwendung eine Anmeldung hinzufügen und Microsoft Graph aufrufen möchten. Dabei wird [Microsoft.Identity.Web](microsoft-identity-web.md), ein Wrapper um [MSAL.NET](msal-overview.md), verwendet.

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Startklasse

Von der Middleware *Microsoft.AspNetCore.Authentication* wird eine Klasse vom Typ `Startup` verwendet. Sie wird ausgeführt, wenn der Hostprozess initialisiert wird:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

Die Methode `AddAuthentication()` konfiguriert den Dienst, um eine cookiebasierte Authentifizierung hinzuzufügen, die in Browserszenarien zum Einsatz kommt und dazu dient, die Aufforderung auf OpenID Connect festzulegen.

Die Zeile, die `.AddMicrosoftIdentityWebApp` enthält, fügt Ihrer Anwendung die Microsoft Identity Platform-Authentifizierung hinzu. Diese wird von [Microsoft.Identity.Web](microsoft-identity-web.md) bereitgestellt. Sie wird anschließend für die Anmeldung unter Verwendung von Microsoft Identity Platform konfiguriert – basierend auf den Informationen im Abschnitt `AzureAD` der Konfigurationsdatei *appsettings.json*:

| *appsettings.json* (Schlüssel) | Beschreibung                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Anwendungs-ID (Client)** der im Azure-Portal registrierten Anwendung.                                                                                       |
| `Instance`             | STS-Endpunkt (Security Token Service, Sicherheitstokendienst) für den zu authentifizierenden Benutzer. Dieser Wert ist in der Regel `https://login.microsoftonline.com/` (öffentliche Azure-Cloud). |
| `TenantId`             | Name Ihres Mandanten, zugehörige Mandanten-ID (eine GUID) oder *common* für die Anmeldung von Benutzern mit Geschäfts-, Schul- oder Unikonto oder mit persönlichem Microsoft-Konto.                             |

Mit der Methode `EnableTokenAcquisitionToCallDownstreamApi` kann Ihre Anwendung ein Token zum Aufrufen geschützter APIs abrufen. Mit `AddMicrosoftGraph` können Ihre Controller oder Razor Pages direkt von den Methoden `GraphServiceClient` (durch Abhängigkeitsinjektion) und `AddInMemoryTokenCaches` profitieren, die Ihrer App die Nutzung eines Tokencaches ermöglichen.

Die Methode `Configure()` enthält mit `app.UseAuthentication()` und `app.UseAuthorization()` zwei wichtige Methoden, um die genannte Funktion zu aktivieren. Außerdem müssen Sie in der `Configure()`-Methode die Routen von Microsoft Identity Web über mindestens einen Aufruf von `endpoints.MapControllerRoute()` oder einen Aufruf von `endpoints.MapControllers()` registrieren.

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Schützen eines Controllers oder der Methode eines Controllers

Durch Zuweisen des Attributs `[Authorize]` zur Controllerklasse oder zu mindestens einer zugehörigen Methode können Sie einen Controller oder dessen Methoden schützen. Das Attribut `[Authorize]` beschränkt den Zugriff auf authentifizierte Benutzer. Wenn sich der Benutzer noch nicht authentifiziert hat, kann für den Zugriff auf den Controller eine Authentifizierungsabfrage gestartet werden. In dieser Schnellstartanleitung werden die Bereiche aus der Konfigurationsdatei gelesen:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Das GitHub-Repository mit dem in diesem Schnellstart referenzierten ASP.NET Core-Codebeispiel enthält auch Anleitungen und weitere Codebeispiele für folgende Aufgaben:

- Hinzufügen einer Authentifizierung zu einer neuen ASP.NET Core-Webanwendung
- Aufrufen von Microsoft Graph, anderer Microsoft-APIs oder Ihrer eigenen Web-APIs
- Autorisierung hinzufügen
- Anmelden von Benutzern in nationalen Clouds oder mit Identitäten sozialer Netzwerke

> [!div class="nextstepaction"]
> [Tutorials für ASP.NET Core-Web-Apps auf GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
