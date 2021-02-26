---
title: 'Tutorial: Anmelden von Benutzern und Aufrufen einer geschützten API über eine Blazor WebAssembly-App'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Microsoft Identity Platform in einer Blazor WebAssembly-App (WASM) Benutzer anmelden und eine geschützte API aufrufen.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 7118ff589e580fd3aa1f693e72152f1ad4c18e10
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979877"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Tutorial: Anmelden von Benutzern und Aufrufen einer geschützten API über eine Blazor WebAssembly-App

In diesem Tutorial haben Sie eine Blazor WebAssembly-App erstellt, mit der unter Verwendung von Microsoft Identity Platform und durch Registrierung der App in Azure Active Directory (Azure AD) Benutzer angemeldet und Daten von Microsoft Graph abgerufen werden. 

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
>
> * Erstellen einer neuen Blazor WebAssembly-App, die für die Verwendung von Azure Active Directory (Azure AD) für die [Authentifizierung und Autorisierung](authentication-vs-authorization.md) mithilfe der Microsoft Identity Platform konfiguriert ist
> * Abrufen von Daten aus einer geschützten Web-API, in diesem Fall [Microsoft Graph](/graph/overview)

In diesem Tutorial wird .NET Core 3.1 verwendet. Die .NET-Dokumentation enthält Anweisungen zum [Schützen einer Blazor WebAssembly-App](/aspnet/core/blazor/security/webassembly/graph-api) mithilfe von ASP.NET Core 5.0. 

Es gibt auch ein [Tutorial für Blazor Server](tutorial-blazor-server.md). 

## <a name="prerequisites"></a>Voraussetzungen

* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Ein Azure AD-Mandant, bei dem Sie eine App registrieren können. Wenn Sie nicht über Zugriff auf einen Azure AD-Mandanten verfügen, können Sie einen erhalten, indem Sie sich beim [Microsoft 365-Entwicklerprogramm](https://developer.microsoft.com/microsoft-365/dev-program) registrieren oder ein [kostenloses Azure-Konto](https://azure.microsoft.com/free) erstellen.

## <a name="register-the-app-in-the-azure-portal"></a>Registrieren der App im Azure-Portal

Alle Apps, die Azure Active Directory (Azure AD) für die Authentifizierung verwenden, müssen bei Azure AD registriert werden. Folgen Sie den Anweisungen unter [Registrieren einer Anwendung](quickstart-register-app.md) mit den folgenden Spezifikationen:

- Wählen Sie für **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
- Übernehmen Sie in der Dropdownliste **Umleitungs-URI** den Eintrag **Web**, und geben Sie `https://localhost:5001/authentication/login-callback` ein. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App an einem anderen Port verfügbar ist, geben Sie anstelle von `5001` die entsprechende Portnummer an.

Wählen Sie nach der Registrierung unter **Verwalten** die Optionen **Authentifizierung** > **Implizite Genehmigung und Hybridflows** aus. Wählen Sie **Zugriffstoken** und **ID-Token** und dann **Speichern** aus.

## <a name="create-the-app-using-the-net-core-cli"></a>Erstellen der App mithilfe der .NET Core-CLI

Zum Erstellen der App benötigen Sie die aktuellen Blazor-Vorlagen. Diese können Sie für die .NET Core-CLI mit dem folgenden Befehl installieren:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.1
```

Führen Sie dann den folgenden Befehl aus, um die Anwendung zu erstellen. Ersetzen Sie die Platzhalter im Befehl durch die entsprechenden Informationen aus der Seite „Übersicht“ Ihrer App, und führen Sie den Befehl in einer Befehlsshell aus. Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Platzhalter   | Name im Azure-Portal       | Beispiel                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | Anwendungs-ID (Client) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Verzeichnis-ID (Mandant)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>Testen der App

Jetzt können Sie die App erstellen und ausführen. Wenn Sie diese Vorlagen-App ausführen, müssen Sie das auszuführende Framework mit „--framework“ angeben. In diesem Tutorial wird .NET Standard 2.1 verwendet, aber die Vorlage unterstützt auch andere Frameworks.

```dotnetcli
dotnet run --framework netstandard2.1
```

Navigieren Sie in Ihrem Browser zu `https://localhost:5001`, und melden Sie sich mit einem Azure AD-Benutzerkonto an, um zu sehen, dass die App ausgeführt wird und Benutzer mit der Microsoft Identity Platform anmeldet.

Die Komponenten dieser Vorlage, die Anmeldungen mit Azure AD mithilfe der Microsoft Identity Platform ermöglichen, werden in der [ASP.NET-Dokumentation in diesem Thema](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package) erläutert.

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>Abrufen von Daten aus einer geschützten API (Microsoft Graph)

[Microsoft Graph](/graph/overview) enthält APIs, die Ihren Benutzern den Zugriff auf Microsoft 365-Daten ermöglichen, und unterstützt die von Microsoft Identity Platform ausgestellten Token. Daher eignet sich dieser geschützte API-Satz gut als Beispiel. In diesem Abschnitt fügen Sie Code hinzu, um Microsoft Graph aufzurufen und die E-Mails des Benutzers auf der Seite „Daten abrufen“ der Anwendung anzuzeigen.

Dieser Abschnitt wird unter Verwendung eines allgemeinen Ansatzes zum Aufrufen einer geschützten API mithilfe eines benannten Clients verfasst. Dieselbe Methode kann auch für andere geschützte APIs verwendet werden, die Sie aufrufen möchten. Wenn Sie Microsoft Graph jedoch über Ihre Anwendung aufrufen möchten, können Sie das Graph SDK verwenden, um die Anzahl von Codebausteinen zu verringern. Die .NET-Dokumentation enthält Anweisungen zur [Verwendung des Graph SDK](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0&preserve-view=true).

Bevor Sie beginnen, müssen Sie sich bei Ihrer App abmelden, weil Sie Änderungen an den erforderlichen Berechtigungen vornehmen und Ihr aktuelles Token nicht funktioniert. Wenn Sie dies noch nicht getan haben, führen Sie Ihre App erneut aus, und wählen Sie **Abmelden** aus, bevor Sie den folgenden Code aktualisieren.

Jetzt aktualisieren Sie die Registrierung Ihrer App und den Code, um die E-Mails eines Benutzers per Pull abzurufen und die Nachrichten in der App anzuzeigen.

Fügen Sie als Erstes die API-Berechtigung `Mail.Read` zur Registrierung der App hinzu, damit Azure AD erkennt, dass die App den Zugriff auf die E-Mails ihrer Benutzer anfordert.

1. Wählen Sie im Azure-Portal unter **App-Registrierungen** Ihre App aus.
1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**.
1. Wählen Sie **Berechtigung hinzufügen** > **Microsoft Graph** aus.
1. Wählen Sie **Delegierte Berechtigungen** aus, suchen Sie nach der Berechtigung **Mail.Read**, und wählen Sie diese dann aus.
1. Wählen Sie **Berechtigungen hinzufügen** aus.

Fügen Sie als Nächstes der *CSPROJ*-Projektdatei im netstandard2.1-Element **ItemGroup** Folgendes hinzu. Dadurch können Sie im nächsten Schritt den benutzerdefinierten HttpClient erstellen.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Ändern Sie dann den Code, wie in den nächsten Schritten angegeben. Durch diese Änderungen werden den an die Microsoft Graph-API gesendeten ausgehenden Anforderungen [Zugriffstoken](access-tokens.md) hinzugefügt. Dieses Muster wird unter [Zusätzliche Sicherheitsszenarien für ASP.NET Core Blazor WebAssembly](/aspnet/core/blazor/security/webassembly/additional-scenarios) ausführlicher erläutert.

Erstellen Sie zunächst mithilfe des folgenden Codes eine neue Datei namens *GraphAPIAuthorizationMessageHandler.cs*. Dieser Handler wird verwendet, um ein Zugriffstoken für die Bereiche `User.Read` und `Mail.Read` zu ausgehenden Anforderungen an die Microsoft Graph-API hinzuzufügen.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Ersetzen Sie dann den Inhalt der `Main`-Methode in der Datei *Program.cs* durch den folgenden Code. Dieser Code verwendet den neuen `GraphAPIAuthorizationMessageHandler` und fügt `User.Read` und `Mail.Read` als Standardbereiche hinzu, die von der App angefordert werden, wenn sich der Benutzer zum ersten Mal anmeldet.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Ersetzen Sie abschließend den Inhalt der Seite *FetchData.razor* durch den folgenden Code. Dieser Code ruft E-Mail-Daten des Benutzers aus der Microsoft Graph-API ab und zeigt sie als Liste an. In `OnInitializedAsync` wird der neue `HttpClient`, der das richtige Zugriffstoken verwendet, erstellt und zum Senden der Anforderung an die Microsoft Graph-API verwendet.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Starten Sie jetzt die App erneut. Sie werden aufgefordert, der App Zugriff zum Lesen Ihrer E-Mails zu gewähren. Dies ist das erwartete Verhalten, wenn eine App den Bereich `Mail.Read` anfordert.

Nachdem Sie die Zustimmung erteilt haben, können Sie zur Seite „Daten abrufen“ navigieren, um E-Mails zu lesen.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Screenshot der fertigen App. Sie weist die Überschrift „Hello Nicholas“ auf, und es wird eine Liste von E-Mails von Nicholas angezeigt.":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bewährte Methoden und Empfehlungen für Microsoft Identity Platform](./identity-platform-integration-checklist.md)
