---
title: 'Schnellstart: Schützen einer ASP.NET Core-Web-API mit Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung wird ein Codebeispiel heruntergeladen und geändert, um zu veranschaulichen, wie Sie eine ASP.NET Core-Web-API schützen, indem Sie Microsoft Identity Platform für die Autorisierung verwenden.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: aa0a001f9c35202939eeb4a7752803b998a3acf7
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562014"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-microsoft-identity-platform"></a>Schnellstart: Schützen einer ASP.NET Core-Web-API mit Microsoft Identity Platform

In dieser Schnellstartanleitung laden Sie ein ASP.NET Core-Web-API-Codebeispiel herunter und überprüfen den Code, der den Zugriff auf Ressourcen auf autorisierte Konten beschränkt. Das Beispiel unterstützt die Autorisierung von persönlichen Microsoft-Konten sowie Konten in einer beliebigen Azure AD-Organisation (Azure Active Directory).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Voraussetzungen
>
> - Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Azure Active Directory-Mandant](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) oder [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Schritt 1: Registrieren der Anwendung
>
> Registrieren Sie zunächst die Web-API in Ihrem Azure AD-Mandanten, und fügen Sie einen Bereich hinzu:
>
> 1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann **Neue Registrierung** aus.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `AspNetCoreWebApi-Quickstart`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie **Registrieren**.
> 1. Wählen Sie unter **Verwalten** die Option **Eine API verfügbar machen** aus.
> 1. Wählen Sie **Bereich hinzufügen** und anschließend **Speichern und fortfahren** aus, um den standardmäßigen **Anwendungs-ID-URI** zu akzeptieren.
> 1. Geben Sie im Bereich **Bereich hinzufügen** die folgenden Werte ein:
>    - **Bereichsname**: `access_as_user`
>    - **Zum Einwilligen berechtigte Personen**: **Administratoren und Benutzer**
>    - **Anzeigename der Administratoreinwilligung**: `Access AspNetCoreWebApi-Quickstart`
>    - **Beschreibung der Administratoreinwilligung**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Anzeigename der Benutzereinwilligung**: `Access AspNetCoreWebApi-Quickstart`
>    - **Beschreibung der Benutzereinwilligung**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Status:** **Aktiviert**
> 1. Wählen Sie **Bereich hinzufügen** aus, um das Hinzufügen des Bereichs abzuschließen.

## <a name="step-2-download-the-aspnet-core-project"></a>Schritt 2: Herunterladen des ASP.NET Core-Projekts

> [!div renderon="docs"]
> [Laden Sie die ASP.NET Core-Lösung von GitHub herunter.](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip)

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Schritt 3: Konfigurieren des ASP.NET Core-Projekts
>
> In diesem Schritt wird der Beispielcode für die Verwendung der zuvor erstellten App-Registrierung konfiguriert.
>
> 1. Extrahieren Sie das ZIP-Archiv in einem Ordner in der Nähe des Stammverzeichnisses Ihres Laufwerks. Beispiel: *C:\Azure-Samples*.
> 1. Öffnen Sie die Lösung aus dem Ordner *webapi* in Ihrem Code-Editor.
> 1. Öffnen Sie die Datei *appsettings.json*, und ändern Sie Folgendes:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Ersetzen Sie `Enter_the_Application_Id_here` durch die **Anwendungs-ID (Client)** der im Azure-Portal registrierten Anwendung. Den Wert für **Anwendungs-ID (Client)** finden Sie auf der Seite **Übersicht** der App.
>    - Ersetzen Sie `Enter_the_Tenant_Info_Here` durch eine der folgenden Optionen:
>       - Wenn Ihre Anwendung **Nur Konten in diesem Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch die **Verzeichnis-ID (Mandant)** (eine GUID) oder durch den **Mandantennamen** (beispielsweise `contoso.onmicrosoft.com`). Die **Verzeichnis-ID (Mandant)** finden Sie auf der Seite **Übersicht** der App.
>       - Unterstützt Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis**, ersetzen Sie diesen Wert durch `organizations`.
>       - Wenn Ihre Anwendung **Alle Microsoft-Kontobenutzer** unterstützt, behalten Sie `common` für diesen Wert bei.
>
> Ändern Sie in dieser Schnellstartanleitung keine anderen Werte in der Datei *appsettings.json*.

## <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

Die Web-API empfängt ein Token von einer Clientanwendung, und das Token wird durch den Code in der Web-API überprüft. Eine ausführlichere Erläuterung dieses Szenarios finden Sie unter [Szenario: Geschützte Web-API](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Startklasse

Von der Middleware *Microsoft.AspNetCore.Authentication* wird eine Klasse vom Typ `Startup` verwendet, die ausgeführt wird, wenn der Hostprozess initialisiert wird. In der zugehörigen Methode `ConfigureServices` wird die von *Microsoft.Identity.Web* bereitgestellte Erweiterungsmethode `AddMicrosoftIdentityWebApi` aufgerufen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

Durch die Methode `AddAuthentication()` wird der Dienst konfiguriert, um die JwtBearer-basierte Authentifizierung hinzuzufügen.

Durch die Zeile mit `.AddMicrosoftIdentityWebApi` wird Ihrer Web-API die Microsoft Identity Platform-Autorisierung hinzugefügt. Diese wird anschließend für die Überprüfung von Zugriffstoken konfiguriert, die vom Microsoft Identity Platform-Endpunkt ausgegeben werden – basierend auf den Informationen im Abschnitt `AzureAD` der Konfigurationsdatei *appsettings.json*:

| *appsettings.json* (Schlüssel) | Beschreibung                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Anwendungs-ID (Client)** der im Azure-Portal registrierten Anwendung.                                                                                       |
| `Instance`             | STS-Endpunkt (Security Token Service, Sicherheitstokendienst) für den zu authentifizierenden Benutzer. Dieser Wert ist in der Regel `https://login.microsoftonline.com/` (öffentliche Azure-Cloud). |
| `TenantId`             | Name Ihres Mandanten, zugehörige Mandanten-ID (eine GUID) oder *common* für die Anmeldung von Benutzern mit Geschäfts-, Schul- oder Unikonto oder mit persönlichem Microsoft-Konto.                             |

Die Methode `Configure()` enthält mit `app.UseAuthentication()` und `app.UseAuthorization()` zwei wichtige Methoden, um die genannte Funktion zu aktivieren:

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-a-controllers-method-or-a-razor-page"></a>Schützen eines Controllers, der Methode eines Controllers oder einer Razor-Seite

Sie können einen Controller oder Controllermethoden mithilfe des `[Authorize]`-Attributs schützen. Dieses Attribut schränkt den Zugriff auf den Controller oder seine Methoden ein, indem es nur authentifizierte Benutzer zulässt. Das bedeutet, dass eine Authentifizierungsaufforderung für den Zugriff auf den Controller gestartet werden kann, wenn der Benutzer nicht authentifiziert ist.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validate-the-scope-in-the-controller"></a>Überprüfen des Bereichs im Controller

Anschließend wird durch den Code in der API mithilfe von `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` überprüft, ob die erforderlichen Bereiche im Token enthalten sind.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Das GitHub-Repository mit diesem Codebeispiel für die ASP.NET Core-Web-API enthält Anleitungen und weitere Codebeispiele für Folgendes:

- Hinzufügen einer Authentifizierung zu einer neuen ASP.NET Core-Web-API
- Aufrufen der Web-API über eine Desktopanwendung
- Aufrufen von Downstream-APIs wie Microsoft Graph und anderen Microsoft-APIs

> [!div class="nextstepaction"]
> [Tutorials für die ASP.NET Core-Web-API auf GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
