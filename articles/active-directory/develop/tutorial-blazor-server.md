---
title: 'Tutorial: Erstellen einer Blazor Server-App, die Microsoft Identity Platform für die Authentifizierung verwendet | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial richten Sie die Authentifizierung mithilfe von Microsoft Identity Platform in einer Blazor Server-App ein.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: d499a0e7d3ca7933632b52d5edbd8094a29dbcaa
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979894"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Tutorial: Erstellen einer Blazor Server-App, die Microsoft Identity Platform für die Authentifizierung verwendet

In diesem Tutorial haben Sie eine Blazor Server-App erstellt, mit der unter Verwendung von Microsoft Identity Platform und durch Registrierung der App in Azure Active Directory (Azure AD) Benutzer angemeldet und Daten von Microsoft Graph abgerufen werden.

Es gibt auch ein [Tutorial für Blazor WASM](tutorial-blazor-webassembly.md).

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Erstellen einer neuen Blazor Server-App, die für die Verwendung von Azure Active Directory (Azure AD) für die Authentifizierung konfiguriert ist
> * Abwickeln der Authentifizierung und Autorisierung mit Microsoft.Identity.Web
> * Abrufen von Daten aus einer geschützten Web-API, Microsoft Graph

## <a name="prerequisites"></a>Voraussetzungen

- [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Ein Azure AD-Mandant, bei dem Sie eine App registrieren können. Wenn Sie nicht über Zugriff auf einen Azure AD-Mandanten verfügen, können Sie einen erhalten, indem Sie sich beim [Microsoft 365-Entwicklerprogramm](https://developer.microsoft.com/microsoft-365/dev-program) registrieren oder ein [kostenloses Azure-Konto](https://azure.microsoft.com/free) erstellen.

## <a name="register-the-app-in-the-azure-portal"></a>Registrieren der App im Azure-Portal

Alle Apps, die Azure Active Directory (Azure AD) für die Authentifizierung verwenden, müssen bei Azure AD registriert werden. Folgen Sie den Anweisungen unter [Registrieren einer Anwendung](quickstart-register-app.md) mit den folgenden Ergänzungen:

- Wählen Sie für **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
- Übernehmen Sie in der Dropdownliste **Umleitungs-URI** den Eintrag **Web**, und geben Sie `https://localhost:5001/signin-oidc` ein. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App an einem anderen Port verfügbar ist, geben Sie anstelle von `5001` die entsprechende Portnummer an.

Aktivieren Sie unter **Authentifizierung** > **Implizite Genehmigung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**, und wählen Sie dann die Schaltfläche **Speichern** aus.

Und weil die App eine geschützte API (in diesem Fall Microsoft Graph) aufruft, benötigt sie schließlich einen geheimen Clientschlüssel, um ihre Identität zu überprüfen, wenn sie ein Zugriffstoken zum Aufrufen dieser API anfordert.

1. Wählen Sie in derselben App-Registrierung unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
2. Erstellen Sie einen **neuen geheimen Clientschlüssel**, der nie abläuft.
3. Notieren Sie sich den **Wert** des geheimen Clientschlüssels, da Sie ihn im nächsten Schritt benötigen. Sie können nicht mehr darauf zugreifen, sobald Sie aus diesem Bereich navigieren. Sie können ihn jedoch bei Bedarf neu erstellen.

## <a name="create-the-app-using-the-net-cli"></a>Erstellen der App mithilfe der .NET-CLI

Führen Sie den folgenden Befehl aus, um die Vorlagen für Microsoft.Identity.Web herunterzuladen, die wir in diesem Tutorial verwenden werden.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Führen Sie dann den folgenden Befehl aus, um die Anwendung zu erstellen. Ersetzen Sie die Platzhalter im Befehl durch die entsprechenden Informationen aus der Seite „Übersicht“ Ihrer App, und führen Sie den Befehl in einer Befehlsshell aus. Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Platzhalter   | Name im Azure-Portal       | Beispiel                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Anwendungs-ID (Client) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Verzeichnis-ID (Mandant)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Navigieren Sie jetzt in Ihrem Editor zu Ihrer neuen Blazor-App, und fügen Sie der Datei *appsettings.json* den geheimen Clientschlüssel hinzu, indem Sie den Text „secret-from-app-registration“ ersetzen.

```json
"ClientSecret": "xkAlNiG70000000_UI~d.OS4Dl.-Cy-1m3",
```

## <a name="test-the-app"></a>Testen der App

Jetzt können Sie die App erstellen und ausführen. Wenn Sie diese Vorlagen-App ausführen, müssen Sie das auszuführende Framework mit „--framework“ angeben. In diesem Tutorial wird das .NET Core 3.1 SDK verwendet.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

Navigieren Sie in Ihrem Browser zu `https://localhost:5001`, und melden Sie sich mit einem Azure AD-Benutzerkonto an, um zu sehen, dass die App ausgeführt wird.

## <a name="retrieving-data-from-microsoft-graph"></a>Abrufen von Daten aus Microsoft Graph

[Microsoft Graph](/graph/overview) bietet eine Reihe von APIs, die den Zugriff auf die Microsoft 365-Daten Ihrer Benutzer ermöglichen. Wenn Sie Microsoft Identity Platform als Identitätsanbieter für Ihre App verwenden, können Sie einfacher auf diese Informationen zugreifen, weil Microsoft Graph die von Microsoft Identity Platform ausgestellten Token direkt unterstützt. In diesem Abschnitt fügen Sie Code hinzu, um die E-Mails des angemeldeten Benutzers auf der Seite „Daten abrufen“ der Anwendung anzuzeigen.

Bevor Sie beginnen, müssen Sie sich bei Ihrer App abmelden, weil Sie Änderungen an den erforderlichen Berechtigungen vornehmen und Ihr aktuelles Token nicht funktioniert. Wenn Sie dies noch nicht getan haben, führen Sie Ihre App erneut aus, und wählen Sie **Abmelden** aus, bevor Sie den folgenden Code aktualisieren.

Jetzt aktualisieren Sie die Registrierung Ihrer App und den Code, um die E-Mails eines Benutzers per Pull abzurufen und die Nachrichten in der App anzuzeigen. Sie erreichen dies, indem Sie zunächst die Berechtigungen für die App-Registrierung in Azure AD erweitern, um den Zugriff auf die E-Mail-Daten zu ermöglichen. Fügen Sie dann der Blazor-App Code hinzu, um diese Daten auf einer der Seiten abzurufen und anzuzeigen.

1. Wählen Sie im Azure-Portal unter **App-Registrierungen** Ihre App aus.
1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**.
1. Wählen Sie **Berechtigung hinzufügen** > **Microsoft Graph** aus.
1. Wählen Sie **Delegierte Berechtigungen** aus, suchen Sie nach der Berechtigung **Mail.Read**, und wählen Sie diese dann aus.
1. Wählen Sie **Berechtigungen hinzufügen** aus.

Aktualisieren Sie in der Datei *appsettings.json* Ihren Code so, dass er das geeignete Token mit den richtigen Berechtigungen abruft. Fügen Sie „mail.read“ nach dem Bereich „user.read“ unter „DownstreamAPI“ hinzu. Damit wird angegeben, für welche Bereiche (oder Berechtigungen) die App Zugriff anfordert.

```json
"Scopes": "user.read mail.read"
```

Aktualisieren Sie als Nächstes in der Datei *FetchData.razor* den Code, um E-Mail-Daten anstelle der standardmäßigen (zufälligen) Wetterdetails abzurufen. Ersetzen Sie den Code in dieser Datei durch den folgenden Code:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Starten Sie die App. Sie werden dann aufgefordert, die neu hinzugefügten Berechtigungen anzugeben. Dies deutet darauf hin, dass alles wie erwartet funktioniert. Jetzt fordert die App zusätzlich zu den grundlegenden Benutzerprofildaten den Zugriff auf E-Mail-Daten an.

Nachdem Sie die Zustimmung erteilt haben, können Sie zur Seite „Daten abrufen“ navigieren, um E-Mails zu lesen.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Screenshot der fertigen App. Sie weist die Überschrift „Hello Nicholas“ auf, und es wird eine Liste von E-Mails von Nicholas angezeigt.":::

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich im Rahmen der mehrteiligen Szenarioreihe über das Aufrufen und Entwerfen von Web-Apps, mit denen Benutzer angemeldet werden.

> [!div class="nextstepaction"]
> [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md)
