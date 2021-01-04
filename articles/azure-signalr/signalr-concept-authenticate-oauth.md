---
title: Leitfaden für die Authentifizierung von Azure SignalR Service-Clients
description: Erfahren Sie, wie Sie Ihre eigene Authentifizierung implementieren und in den Azure SignalR Service integrieren, indem Sie dem e2e-Beispiel folgen.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 8dd3c60c3d1b714ab75b496a94ba4bd5aec4e43d
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558469"
---
# <a name="azure-signalr-service-authentication"></a>Azure SignalR Service-Authentifizierung

Dieses Tutorial baut auf der Chatroomanwendung auf, die im Schnellstart eingeführt wurde. Wenn Sie noch keinen [Chatroom mit dem SignalR-Dienst erstellt haben](signalr-quickstart-dotnet-core.md), führen Sie diese Übung zuerst durch.

In diesem Tutorial lernen Sie, wie Sie Ihre eigene Authentifizierung implementieren und in den Microsoft Azure SignalR-Dienst integrieren.

Die Authentifizierung, die ursprünglich in der Chatroomanwendung des Schnellstarts verwendet wurde, ist für reale Szenarien zu einfach. Die Anwendung erlaubt es jedem Client eine Identität vorzugeben, und der Server akzeptiert dies einfach. In der Praxis ist dieser Ansatz nicht sehr nützlich, da ein betrügerischer Benutzer sich einfach als anderer ausgeben könnte, um auf sensible Daten zuzugreifen.

[GitHub](https://github.com/) bietet Authentifizierungs-APIs, die auf einem gängigen Industriestandardprotokoll namens [OAuth](https://oauth.net/) basieren. Diese APIs ermöglichen es Anwendungen von Drittanbietern, GitHub-Konten zu authentifizieren. In diesem Tutorial werden Sie mithilfe dieser APIs die Authentifizierung über ein GitHub-Konto implementieren, bevor Sie Clientanmeldungen für die Chatroomanwendung zulassen. Nach der Authentifizierung eines GitHub-Kontos werden die Kontoinformationen als Cookie hinzugefügt, das vom Webclient zur Authentifizierung verwendet wird.

Weitere Informationen zu den über GitHub bereitgestellten APIs für die OAuth-Authentifizierung finden Sie unter [Basics of Authentication](https://developer.github.com/v3/guides/basics-of-authentication/) (Grundlagen der Authentifizierung).

Sie können einen beliebigen Code-Editor nutzen, um die Schritte dieser Schnellstartanleitung auszuführen. [Visual Studio Code](https://code.visualstudio.com/) ist aber eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

Der Code für dieses Tutorial kann aus dem [GitHub-Repository „AzureSignalR-samples“](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat) heruntergeladen werden.

![OAuth abschließen in Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Registrieren einer neuen OAuth-App bei Ihrem GitHub-Konto
> * Hinzufügen eines Authentifizierungscontrollers zur Unterstützung der GitHub-Authentifizierung
> * Bereitstellen Ihrer ASP.NET Core-Web-App in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Ein auf [GitHub](https://github.com/) erstelltes Konto
- [Git-Client](https://git-scm.com/)
- [.NET Core SDK](https://www.microsoft.com/net/download/windows)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart), für die Bash-Umgebung konfiguriert.
- Laden Sie das GitHub-Repository [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) herunter, oder klonen Sie es.

## <a name="create-an-oauth-app"></a>Erstellen einer OAuth-App

1. Öffnen Sie einen Webbrowser, navigieren Sie zu `https://github.com`, und melden Sie sich bei Ihrem Konto an.

2. Wechseln Sie für Ihr Konto zu **Settings (Einstellungen)**  > **Developer settings** (Entwicklereinstellungen), und klicken Sie unter **OAuth Apps** (OAuth-Apps) auf **Register a new application** (Neue Anwendung registieren) oder auf *New OAuth App* (Neue OAuth-App).

3. Verwenden Sie die folgenden Einstellungen für die neue OAuth-App, und klicken Sie dann auf **Register application** (Anwendung registrieren):

    | Einstellungsname | Empfohlener Wert | BESCHREIBUNG |
    | ------------ | --------------- | ----------- |
    | Anwendungsname | *Azure SignalR Chat* (Azure SignalIR-Chat) | Der GitHub-Benutzer sollte in der Lage sein, die Anwendung, mit der er sich authentifiziert, zu erkennen und zu vertrauen.   |
    | Homepage URL (URL für Startseite) | `http://localhost:5000/home` | |
    | Application description (Anwendungsbeschreibung) | *Ein Chatroombeispiel, in dem Azure SignalR Service mit GitHub-Authentifizierung verwendet wird.* | Eine sinnvolle Beschreibung der Anwendung, die Anwendungsbenutzern hilft, den Kontext der verwendeten Authentifizierung zu verstehen. |
    | Authorization callback URL (Autorisierungsrückruf-URL) | `http://localhost:5000/signin-github` | Dies ist die wichtigste Einstellung für Ihre OAuth-Anwendung. Es ist die Rückruf-URL, an die GitHub den Benutzer nach erfolgreicher Authentifizierung zurückgibt. In diesem Tutorial müssen Sie die StandardrückrufURL für das *AspNet.Security.OAuth.GitHub*-Paket */signin-github* verwenden.  |

4. Sobald die Registrierung der neuen OAuth-App abgeschlossen ist, fügen Sie *Client-ID* und *Geheimer Clientschlüssel* mit den folgenden Befehlen zum Secret Manager hinzu. Ersetzen Sie *Your_GitHub_Client_Id* und *Your_GitHub_Client_Secret* durch die Werte für Ihre OAuth-App.

    ```dotnetcli
    dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
    dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret
    ```

## <a name="implement-the-oauth-flow"></a>Implementieren des OAuth-Flows

### <a name="update-the-startup-class-to-support-github-authentication"></a>Aktualisieren der Startklasse zur Unterstützung der GitHub-Authentifizierung

1. Fügen Sie einen Verweis auf das neueste *Microsoft.AspNetCore.Authentication.Cookies*- und *AspNet.Security.OAuth.GitHub*-Paket hinzu, und stellen Sie alle Pakete wieder her.

    ```dotnetcli
    dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
    dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
    dotnet restore
    ```

1. Öffnen Sie *Startup.cs*, und fügen Sie `using`-Anweisungen für die folgenden Namespaces hinzu:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. Fügen Sie am Anfang der Klasse `Startup` Konstanten für die Schlüssel des Secret Managers hinzu, die die geheimen Daten der GitHub-OAuth-App enthalten.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Fügen Sie den folgenden Code zur Methode `ConfigureServices` hinzu, um die Authentifizierung mit der GitHub-OAuth-App zu unterstützen:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Fügen Sie die Hilfsmethode `GetUserCompanyInfoAsync` zur Klasse `Startup` hinzu.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Aktualisieren Sie die Methode `Configure` der Startup-Klasse mit der folgenden Codezeile, und speichern Sie die Datei.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Hinzufügen eines Authentifizierungscontrollers

In diesem Abschnitt wird eine `Login`-API implementiert, die Clients mit der GitHub-OAuth-App authentifiziert. Nach der Authentifizierung fügt die API ein Cookie zur Webclientantwort hinzu, bevor sie den Client zurück zur Chat-App leitet. Dieses Cookie wird dann verwendet, um den Client zu identifizieren.

1. Fügen Sie eine neue Controllercodedatei zum Verzeichnis *chattest\Controllers* hinzu. Benennen Sie die Datei *AuthController.cs*.

2. Fügen Sie den folgenden Code für den Authentifizierungscontroller hinzu. Stellen Sie sicher, dass Sie den Namespace aktualisieren, wenn Ihr Projektverzeichnis nicht *chattest* war:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Speichern Sie die Änderungen.

### <a name="update-the-hub-class"></a>Aktualisieren der Hubklasse

Wenn ein Webclient versucht, sich mit dem SignalR-Dienst zu verbinden, wird die Verbindung standardmäßig basierend auf einem intern bereitgestellten Zugriffstoken zugelassen. Dieses Zugriffstoken ist nicht mit einer authentifizierten Identität verbunden. Bei diesem Zugriff handelt es sich eigentlich um einen anonymen Zugriff.

In diesem Abschnitt aktivieren Sie die echte Authentifizierung, indem Sie der Hubklasse das Attribut `Authorize` hinzufügen und die Hubmethoden aktualisieren, um den Benutzernamen aus der Anforderung des authentifizierten Benutzers zu lesen.

1. Öffnen Sie *Hub\Chat.cs*, und fügen Sie Verweise zu diesen Namespaces hinzu:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Aktualisieren Sie den Hubcode wie unten gezeigt. Dieser Code fügt das Attribut `Authorize` zur Klasse `Chat` hinzu und verwendet die authentifizierte Identität des Benutzers in den Hubmethoden. Außerdem wird die Methode `OnConnectedAsync` hinzugefügt, die jede neue Clientverbindung als Systemmeldung im Chatroom protokolliert.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Speichern Sie die Änderungen.

### <a name="update-the-web-client-code"></a>Aktualisieren des Webclientcodes

1. Öffnen Sie *wwwwroot\index.html*, und ersetzen Sie den Code, der nach dem Benutzernamen fragt, durch Code, um das vom Authentifizierungscontroller zurückgegebene Cookie zu verwenden.

    Entfernen Sie aus *index.html* den folgenden Code:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Fügen Sie den folgenden Code anstelle des obigen Codes ein, um das Cookie zu verwenden:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Fügen Sie unterhalb der Codezeile, die Sie zur Verwendung des Cookies hinzugefügt haben, die folgende Definition für die Funktion `appendMessage` hinzu:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Aktualisieren Sie die Funktionen `bindConnectionMessage` und `onConnected` mit dem folgenden Code, um `appendMessage` zu verwenden.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. Aktualisieren Sie unten in *index.html* den Fehlerhandler für `connection.start()` wie unten gezeigt, um den Benutzer zur Anmeldung aufzufordern.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Speichern Sie die Änderungen.

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

1. Speichern Sie die Änderungen in allen Dateien.

2. Führen Sie den folgenden Befehl in der Befehlsshell aus, um die App per .NET Core-CLI zu erstellen:

    ```dotnetcli
    dotnet build
    ```

3. Führen Sie nach dem erfolgreichen Abschluss des Buildvorgangs den folgenden Befehl aus, um die Web-App lokal auszuführen:

    ```dotnetcli
    dotnet run
    ```

    Standardmäßig wird die App lokal auf Port 5000 gehostet:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Production
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
                    Application started. Press Ctrl+C to shut down.
    ```

4. Öffnen Sie ein Browserfenster, und navigieren Sie zu `http://localhost:5000`. Klicken Sie oben auf den Link **hier**, um sich bei GitHub anzumelden.

    ![OAuth abschließen in Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    Sie werden aufgefordert, den Zugriff der Chat-App auf Ihr GitHub-Konto zu autorisieren. Klicken Sie auf die Schaltfläche **Autorisieren**.

    ![Autorisieren der OAuth-App](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    Sie werden zurück zur Chatanwendung geleitet und mit Ihrem GitHub-Kontonamen angemeldet. Die Webanwendung hat Ihren Kontonamen ermittelt, indem sie Sie über die neu hinzugefügte Authentifizierungsmethode authentifiziert hat.

    ![Identifiziertes Konto](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    Nun, da die Chat-App die Authentifizierung mit GitHub durchführt und die Authentifizierungsinformationen als Cookies speichert, sollten Sie sie in Azure bereitstellen. So können sich auch andere Benutzer mit ihren Konten authentifizieren und von anderen Arbeitsstationen aus kommunizieren.

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

Vorbereiten der Umgebung für die Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

In diesem Abschnitt verwenden Sie die Azure-Befehlszeilenschnittstelle (CLI), um eine neue Web-App in [Azure App Service](../app-service/index.yml) zum Hosten Ihrer ASP.NET-Anwendung in Azure zu erstellen. Die Web-App wird so konfiguriert, dass sie die lokale Git-Bereitstellung verwendet. Die Web-App wird auch mit Ihrer SignalR-Verbindungszeichenfolge, den geheimen GitHub-OAuth-App-Daten und einem Bereitstellungsbenutzer konfiguriert.

Achten Sie beim Erstellen der folgenden Ressourcen darauf, dass Sie die gleiche Ressourcengruppe verwenden, in der sich Ihre SignalR-Dienstressource befindet. Dieser Ansatz macht eine spätere Bereinigung viel einfacher, wenn Sie alle Ressourcen entfernen wollen. Die angegebenen Beispiele setzen voraus, dass Sie den in früheren Tutorials empfohlenen Gruppennamen *SignalRTestResources* verwendet haben.

### <a name="create-the-web-app-and-plan"></a>Erstellen der Web-App und eines Plans

Kopieren Sie den Text für die folgenden Befehle, und aktualisieren Sie die Parameter. Fügen Sie das aktualisierte Skript in die Azure Cloud Shell ein, und drücken Sie die **EINGABETASTE**, um einen neuen App Service-Plan und eine Web-App zu erstellen.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parameter | BESCHREIBUNG |
| -------------------- | --------------- |
| ResourceGroupName | Dieser Ressourcengruppenname wurde in vorherigen Tutorials vorgeschlagen. Es ist ratsam, alle Tutorialressourcen zu gruppieren. Verwenden Sie dieselbe Ressourcengruppe wie in den vorherigen Tutorials. |
| WebAppPlan | Geben Sie einen neuen, eindeutigen Namen für den App Service-Plan ein. |
| WebAppName | Dies ist der Name der neuen Web-App und Teil der URL. Verwenden Sie einen eindeutigen Namen. Beispiel: signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Hinzufügen von App-Einstellungen zur Web-App

In diesem Abschnitt fügen Sie App-Einstellungen für die folgenden Komponenten hinzu:

* Verbindungszeichenfolge für die SignalR-Dienstressource
* Client-ID der GitHub-OAuth-App
* Geheimer Clientschlüssel der GitHub-OAuth-App

Kopieren Sie den Text für die folgenden Befehle, und aktualisieren Sie die Parameter. Fügen Sie das aktualisierte Skript in Ihre Azure Cloud Shell ein, und drücken Sie die **EINGABETASTE**, um die App-Einstellungen hinzuzufügen:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parameter | BESCHREIBUNG |
| -------------------- | --------------- |
| GitHubClientId | Weisen Sie dieser Variablen die geheime Client-ID für Ihre GitHub-OAuth-App zu. |
| GitHubClientSecret | Weisen Sie dieser Variablen das geheime Kennwort für Ihre GitHub-OAuth-App zu. |
| ResourceGroupName | Aktualisieren Sie diese Variable auf den gleichen Ressourcengruppennamen, den Sie im vorherigen Abschnitt verwendet haben. |
| SignalRServiceRessource | Aktualisieren Sie diese Variable mit dem Namen der SignalR-Dienstressource, die Sie im Schnellstart erstellt haben. Beispiel: signalrtestsvc48778624. |
| WebAppName | Aktualisieren Sie diese Variable mit dem Namen der neuen Web-App, die Sie im vorherigen Abschnitt erstellt haben. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>Konfigurieren Sie die Web-App für die lokale Git-Bereitstellung.

Fügen Sie in der Azure Cloud Shell das folgende Skript ein. Dieses Skript erstellt einen neuen Namen und ein neues Kennwort für den Bereitstellungsbenutzer, die Sie verwenden werden, wenn Sie Ihren Code in der Web-App mit Git bereitstellen. Das Skript konfiguriert auch die Web-App für die Bereitstellung mit einem lokalen Git-Repository und gibt die Git-Bereitstellungs-URL zurück.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parameter | BESCHREIBUNG |
| -------------------- | --------------- |
| DeploymentUserName | Wählen Sie einen neuen Namen für den Bereitstellungsbenutzer. |
| DeploymentUserPassword | Wählen Sie ein Kennwort für den neuen Bereitstellungsbenutzer. |
| ResourceGroupName | Verwenden Sie denselben Ressourcengruppennamen wie im vorherigen Abschnitt. |
| WebAppName | Dies ist der Name der neuen, zuvor von Ihnen erstellten Web-App. |

Notieren Sie sich die URL, die mit dem letzten Befehl zurückgegeben wurde, für die Git-Bereitstellung. Sie werden diese URL später benötigen.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Bereitstellen des Codes für die Azure-Web-App

Um Ihren Code bereitzustellen, führen Sie die folgenden Befehle in einer Git-Shell aus.

1. Navigieren Sie zum Stammverzeichnis Ihres Projekts. Wenn Sie das Projekt nicht mit einem Git-Repository initialisiert haben, führen Sie folgenden Befehl aus:

    ```bash
    git init
    ```

2. Fügen Sie eine Remoteinstanz für die Git-Bereitstellungs-URL hinzu, die Sie zuvor notiert haben:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Stellen Sie alle Dateien im initialisierten Repository bereit. und fügen Sie einen Commit hinzu.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Stellen Sie Ihren Code in der Web-App in Azure bereit.

    ```bash
    git push Azure main
    ```

    Sie werden aufgefordert, sich zu authentifizieren, damit der Code bei Azure bereitgestellt wird. Geben Sie den Benutzernamen und das Kennwort des oben erstellten Bereitstellungsbenutzers ein.

### <a name="update-the-github-oauth-app"></a>Aktualisieren der GitHub-OAuth-App

Jetzt müssen Sie nur noch die **URL der Startseite** und die **Autorisierungrückruf-URL** der GitHub-OAuth-App aktualisieren, um auf die neue gehostete App zu verweisen.

1. Öffnen Sie [https://github.com](https://github.com) in einem Browser, und navigieren Sie für Ihr Konto zu **Settings** (Einstellungen) > **Developer settings** (Entwicklereinstellungen) > **OAuth Apps** (OAuth-Apps).

2. Klicken Sie auf Ihre Authentifizierungs-App, und aktualisieren Sie die **URL der Startseite** und die **Autorisierungsrückruf-URL** wie unten gezeigt:

    | Einstellung | Beispiel |
    | ------- | ------- |
    | Homepage URL (URL für Startseite) | `https://signalrtestwebapp22665120.azurewebsites.net/home` |
    | Authorization callback URL (Autorisierungsrückruf-URL) | `https://signalrtestwebapp22665120.azurewebsites.net/signin-github` |

3. Navigieren Sie zu Ihrer Web-App-URL, und testen Sie die Anwendung.

    ![OAuth abschließen in Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten Tutorial fortfahren möchten, können Sie die in dieser Schnellstartanleitung erstellten Ressourcen beibehalten und im nächsten Tutorial wiederverwenden.

Wenn Sie die Schnellstart-Beispielanwendung nicht mehr benötigen, können Sie die in dieser Schnellstartanleitung erstellten Azure-Ressourcen löschen, um das Anfallen von Kosten zu vermeiden.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden also dauerhaft gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Falls Sie die Ressourcen zum Hosten dieses Beispiels in einer vorhandenen Ressourcengruppe erstellt haben, die beizubehaltende Ressourcen enthält, können Sie die Ressourcen einzeln über das jeweilige Blatt löschen, statt die Ressourcengruppe zu löschen.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.

Geben Sie im Textfeld **Nach Name filtern...** den Namen Ihrer Ressourcengruppe ein. In diesem Artikel wurde eine Ressourcengruppe mit dem Namen *SignalRTestResources* verwendet. Klicken Sie in Ihrer Ressourcengruppe in der Ergebnisliste auf **...** und dann auf **Ressourcengruppe löschen**.

![Löschen](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie den Namen der entsprechenden Ressourcengruppe ein, und klicken Sie auf **Löschen**.

Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die OAuth-Authentifizierung hinzugefügt, um einen besseren Authentifizierungsansatz mit dem Azure SignalR-Dienst bereitzustellen. Weitere Informationen zur Verwendung von Azure SignalR Server erhalten Sie, wenn Sie mit den Azure CLI-Beispielen für SignalR Service fortfahren.

> [!div class="nextstepaction"]
> [Azure SignalR-CLI-Beispiele](./signalr-reference-cli.md)
