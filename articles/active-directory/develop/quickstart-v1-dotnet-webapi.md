---
title: Erstellen einer.NET-Web-API, die für Authentifizierung und Autorisierung in Azure AD integriert wird | Microsoft Docs
description: In diesem Thema erfahren Sie, wie eine .NET-MVC-Web-API erstellt wird, die sich für die Authentifizierung und Autorisierung in Azure AD integriert.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2eca253bc5d1495d26506e0e6f8a83762e8bc5
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001108"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Schnellstart: Erstellen einer.NET-Web-API, die zur Authentifizierung und Autorisierung in Azure AD integriert wird

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Wenn Sie eine Anwendung erstellen, die Zugriff auf geschützte Ressourcen bietet, müssen Sie wissen, wie Sie diese Ressourcen vor unbefugtem Zugriff schützen. Mit Azure Active Directory (Azure AD) lässt sich eine Web-API mit nur wenigen Codezeilen unkompliziert und einfach mithilfe von OAuth 2.0-Bearerzugriffstoken schützen.

Für ASP.NET-Web-Apps erreichen Sie diesen Schutz mithilfe der Microsoft-Implementierung der von der Community gestützten und in .NET Framework 4.5 enthaltenen Middleware OWIN. Hier verwenden Sie OWIN zum Erstellen einer Web-API mit einer Aufgabenliste, die folgende Aufgaben ausführt:

* Festlegen, welche APIs geschützt sind.
* Prüfen, ob die Web-API-Aufrufe ein gültiges Zugriffstoken enthalten.

In diesem Schnellstart erstellen Sie die Aufgabenlisten-API und erfahren, wie die folgenden Aufgaben ausgeführt werden:

1. Registrieren Sie eine Anwendung in Azure AD.
2. Konfigurieren Sie die App für die Verwendung der OWIN-Authentifizierungspipeline.
3. Konfigurieren Sie eine Clientanwendung für den Aufruf der Web-API.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zum Einstieg die folgenden erforderlichen Schritte aus:

* Laden Sie das [Anwendungsgerüst](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) oder [vollständige Beispiel](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) herunter. Bei beidem handelt es sich um eine Visual Studio 2013-Lösung.
* Stellen Sie einen Azure AD-Mandanten bereit, bei dem Sie Ihre Anwendung registrieren. Wenn Sie noch keinen Mandanten haben, [erfahren Sie hier, wie Sie einen erhalten](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Schritt 1: Registrieren einer Anwendung in Azure AD

Zum Schutz Ihrer Anwendung müssen Sie in Ihrem Mandanten zunächst eine Anwendung erstellen und Azure AD einige wichtige Informationen bereitstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie Ihren Azure AD-Mandanten aus, indem Sie Ihr Konto in der oberen rechten Ecke der Seite, das Navigationselement **Verzeichnis wechseln** und dann den entsprechenden Mandanten auswählen.
    * Überspringen Sie diesen Schritt, wenn Ihr Konto nur einen Azure AD-Mandanten enthält oder wenn Sie bereits den entsprechenden Azure AD-Mandanten ausgewählt haben.

3. Wählen Sie im linken Navigationsbereich **Azure Active Directory** aus.
4. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
5. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** einen Namen für Ihre Anwendung ein.
Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
6. Wählen Sie die **Webplattform** unter dem Abschnitt **Umleitungs-URI** aus, und legen Sie den Wert auf `https://localhost:44321/` (den Speicherort, an den Azure AD Token zurückgibt) fest.
7. Wenn Sie so weit sind, klicken Sie auf **Registrieren**. Notieren Sie sich auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID**.
6. Wählen Sie **Eine API verfügbar machen** aus, und aktualisieren Sie dann den Anwendungs-ID-URI, indem Sie auf **Festlegen** klicken. Geben Sie einen mandantenspezifischen Bezeichner ein. Geben Sie z. B. Folgendes ein: `https://contoso.onmicrosoft.com/TodoListService`.
7. Speichern Sie die Konfiguration. Lassen Sie das Portal geöffnet, da Sie in Kürze auch Ihre Clientanwendung registrieren müssen.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Schritt 2: Konfigurieren der App für die Verwendung der OWIN-Authentifizierungspipeline

Um eingehende Anforderungen und Token zu überprüfen, müssen Sie Ihre Anwendung für die Kommunikation mit Azure AD einrichten.

1. Öffnen Sie dazu zunächst die Projektmappe, und fügen Sie dem Projekt „TodoListService“ über die Paket-Manager-Konsole die NuGet-Pakete der OWIN-Middleware hinzu.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Fügen Sie dem Projekt „TodoListService“ eine OWIN-Startklasse mit dem Namen `Startup.cs`hinzu.  Klicken Sie mit der rechten Maustaste auf das Projekt, wählen Sie **Hinzufügen > Neues Element** aus, und suchen Sie dann nach **OWIN**. Die OWIN-Middleware ruft beim Starten Ihrer Anwendung die Methode `Configuration(…)` auf.

3. Ändern Sie die Klassendeklaration in `public partial class Startup`. Wir haben für Sie einen Teil dieser Klasse in einer anderen Datei bereits implementiert. Rufen Sie in der `Configuration(…)`-Methode `ConfgureAuth(…)` auf, um die Authentifizierung für Ihre Web-App einzurichten.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Öffnen Sie die Datei `App_Start\Startup.Auth.cs`, und implementieren Sie die Methode `ConfigureAuth(…)`. Die Parameter, die Sie in `WindowsAzureActiveDirectoryBearerAuthenticationOptions` bereitstellen, dienen als Koordinaten für Ihre App zur Kommunikation mit Azure AD. Um sie verwenden zu können, müssen Sie Klassen im Namespace `System.IdentityModel.Tokens` verwenden.

    ```csharp
    using System.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. Verwenden Sie die `[Authorize]`-Attribute, um Ihre Controller und Aktionen durch eine JWT-Bearerauthentifizierung (JSON Web Token) zu schützen. Ergänzen Sie die `Controllers\TodoListController.cs`-Klasse durch ein Autorisierungstag, das den Benutzer zwingt, sich vor dem Zugriff auf diese Seite anzumelden.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Wenn ein autorisierter Aufrufer eine der `TodoListController` -APIs erfolgreich aufruft, muss die Aktion möglicherweise auf Informationen über den Aufrufer zugreifen können. OWIN stellt über das Objekt `ClaimsPrincpal` Zugriff auf die Ansprüche innerhalb des Bearer-Tokens bereit.  

6. Im Allgemeinen müssen Web-APIs jedoch die „Bereiche“ innerhalb des Tokens überprüfen. So wird sichergestellt, dass der Endbenutzer den für den Zugriff auf den Dienst „TodoListService“ erforderlichen Berechtigungen zugestimmt hat.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Öffnen Sie im Stammverzeichnis des Projekts „TodoListService“ die Datei `web.config`, und geben Sie Ihre Konfigurationswerte im Abschnitt `<appSettings>` ein.
    * `ida:Tenant`: Der Name Ihres Azure AD-Mandanten, z.B. „contoso.onmicrosoft.com“.
    * `ida:Audience`: Der App-ID-URI der Anwendung, den Sie in das Azure-Portal eingegeben haben.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Schritt 3: Konfigurieren einer Clientanwendung und Ausführen des Diensts

Um „TodoListService“ in Aktion sehen zu können, müssen Sie den TodoList-Client so konfigurieren, dass er Token von Azure AD erhält und Aufrufe an den Dienst richten kann.

1. Wechseln Sie zurück zum [Azure-Portal](https://portal.azure.com).
1. Erstellen Sie eine neue Anwendungsregistrierung in Ihrem Azure AD-Mandanten.  Geben Sie einen **Namen** ein, der die Anwendung für Benutzer beschreibt, geben Sie `http://TodoListClient/` als Wert für den **Umleitungs-URI** ein, und wählen Sie in der Dropdownliste den Eintrag **Öffentlicher Client (Mobilgerät und Desktop)** aus.
1. Nach Abschluss der Registrierung weist Azure AD Ihrer App eine eindeutige Anwendungs-ID zu. Diesen Wert benötigen Sie in den nächsten Schritten. Daher sollten Sie ihn von der Anwendungsseite kopieren.
1. Wählen Sie **API-Berechtigungen** und dann **Berechtigung hinzufügen** aus.  Suchen Sie den „TodoListService“, wählen Sie ihn aus, und fügen Sie unter **Delegierte Berechtigungen** die Berechtigung **user_impersonation Access TodoListService** hinzu. Wählen Sie anschließend **Berechtigungen hinzufügen** aus.
1. Öffnen Sie in Visual Studio im Projekt „TodoListClient“ die Datei `App.config`, und geben Sie dann Ihre Konfigurationswerte im Abschnitt `<appSettings>` ein.

    * `ida:Tenant`: Der Name Ihres Azure AD-Mandanten, z.B. contoso.onmicrosoft.com.
    * `ida:ClientId`: Die App-ID, die Sie aus dem Azure-Portal kopiert haben.
    * `todo:TodoListResourceId`: Der App-ID-URI der Anwendung „TodoListService“, den Sie im Azure-Portal eingegeben haben.

1. Bereinigen und erstellen Sie jedes Projekt, und führen Sie es dann aus.
1. Wenn dies noch nicht geschehen ist, erstellen Sie in Ihrem Mandanten einen neuen Benutzer mit einer *.onmicrosoft.com-Domäne.
1. Melden Sie sich unter diesem Benutzer beim TodoList-Client an, und fügen Sie der Aufgabenliste des Benutzers einige Aufgaben hinzu.

## <a name="next-steps"></a>Nächste Schritte

* Zu Referenzzwecken können Sie das vollständige Beispiel (ohne Ihre Konfigurationswerte) von [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) herunterladen. Sie können sich nun weiteren Identitätsszenarien zuwenden.
