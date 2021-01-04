---
title: 'Tutorial: Erstellen einer Blazor Server-Chat-App: Azure SignalR'
description: In diesem Tutorial erfahren Sie, wie Sie mit Azure SignalR Service eine Blazor Server-App erstellen und ändern.
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: ea55762aa40360d8eea94223a030f08aad504206
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485391"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Tutorial: Erstellen einer Blazor Server-Chat-App

In diesem Tutorial erfahren Sie, wie Sie eine Blazor Server-App erstellen und ändern. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines einfachen Chatrooms mit Blazor Server-App
> * Ändern von Razor-Komponenten
> * Verwenden Sie von Ereignisbehandlung und Datenbindung in Komponenten
> * Schnelles Bereitstellen für Azure App Service in Visual Studio
> * Migrieren der lokalen SignalR-Instanz zu Azure SignalR Service

## <a name="prerequisites"></a>Voraussetzungen
* Installieren Sie das [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (Mindestversion: 3.0.100).
* Installieren Sie [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (Mindestversion: 16.3).
> Sie können auch die Visual Studio 2019-Vorschauversion verwenden. Diese wird mit der neuesten Blazor Server-App-Vorlage für die neuere .NET Core-Version veröffentlicht.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Erstellen eines lokalen Chatrooms in einer Blazor Server-App

Ab der Version 16.2.0 von Visual Studio 2019 ist Azure SignalR Service in den Web-App-Veröffentlichungsprozess integriert, was die Verwaltung von Abhängigkeiten zwischen Web-App und SignalR Service erheblich vereinfacht. Sie können SignalR in der lokalen Entwicklungsumgebung nutzen und gleichzeitig in Azure SignalR Service für Azure App Service arbeiten, ohne Codeänderungen vornehmen zu müssen.

1. Erstellen einer Blazor-Chat-App
   
   Wählen Sie in Visual Studio „Neues Projekt erstellen“ > „Blazor-App“ aus, geben Sie einen Namen für die App an, wählen Sie einen Ordner aus, und wählen Sie anschließend „Blazor Server-App“ aus. Vergewissern Sie sich, dass Sie mindestens die Version 3.0 des .NET Core SDK installiert haben, damit das Zielframework ordnungsgemäß von Visual Studio erkannt wird.

   [ ![Unter „Neues Projekt erstellen“ sind die Blazor-App-Vorlagen ausgewählt.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Oder führen Sie den folgenden Befehl aus:
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. Fügen Sie eine Datei vom Typ `BlazorChatSampleHub.cs` hinzu, um den `Hub` für den Chat zu implementieren.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Fügen Sie in `Startup.Configure()` einen Endpunkt für den Hub hinzu.
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Installieren Sie das Paket `Microsoft.AspNetCore.SignalR.Client`, um den SignalR-Client zu verwenden.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Erstellen Sie `ChatRoom.razor` unter dem Ordner `Pages`, um den SignalR-Client zu implementieren. Führen Sie die im Anschluss angegebenen Schritte aus, oder kopieren Sie einfach die Datei [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Fügen Sie einen Seitenlink und einen Verweis hinzu.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Fügen Sie dem neuen SignalR-Client Code zum Senden und Empfangen von Nachrichten hinzu.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Fügen Sie vor `@code` den Renderingteil hinzu, damit die Benutzeroberfläche mit dem SignalR-Client interagieren kann.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Aktualisieren Sie `NavMenu.razor`, um ein Eingabemenü für den Chatroom unter `NavMenuCssClass` einzufügen (wie beim Rest).

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Aktualisieren Sie `site.css`, um eine Optimierung für die Blasenansichten des Chatbereichs zu implementieren. Fügen Sie den folgenden Code am Ende ein.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Drücken Sie F5, um die App auszuführen.<kbd></kbd> Sie können wie unten zu sehen chatten.

   [ ![Ein animierter Chat zwischen Bob und Alice. Alice sagt „Hello“, und Bob sagt „Hi“.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

   Bis jetzt basiert die Blazor-App auf einer lokalen SignalR-Instanz. Für die Bereitstellung in Azure App Service empfiehlt es sich, [Azure SignalR Service](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service) zu verwenden, um eine Blazor Server-App für eine große Anzahl gleichzeitiger SignalR-Verbindungen hochskalieren zu können. Außerdem tragen die globale Reichweite und die Hochleistungsrechenzentren des SignalR-Diensts erheblich zur Verringerung der geografiebedingten Latenz bei.

> [!IMPORTANT]
> In der Blazor Server-App werden Benutzeroberflächenzustände serverseitig verwaltet. Das bedeutet, dass in diesem Fall Serverpersistenz erforderlich ist. Ist nur ein einzelner App-Server vorhanden, ist die Serverpersistenz standardmäßig gewährleistet. Bei mehreren App-Servern kann es jedoch vorkommen, dass für die Clientaushandlung und für die Verbindung jeweils unterschiedliche Server verwendet werden, was Benutzeroberflächenfehler in der Blazor-App zur Folge hat. Daher muss in `appsettings.json` wie folgt die Serverpersistenz aktiviert werden:
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Klicken Sie mit der rechten Maustaste auf das Projekt, und navigieren Sie zu `Publish`.

   * Ziel: Azure
   * Spezifisches Ziel: Alle Arten von **Azure App Service** werden unterstützt.
   * App Service: Erstellen Sie eine neue Instanz, oder wählen Sie eine bereits vorhandene App Service-Instanz aus.

   [ ![Animation: Auswählen von „Azure“ als Ziel und „Azure App Service“ als spezifisches Ziel](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Hinzufügen der Azure SignalR Service-Abhängigkeit

   Nach der Erstellung des Veröffentlichungsprofils wird unter **Dienstabhängigkeiten** eine Empfehlung angezeigt. Klicken Sie auf **Konfigurieren**, um eine neue Instanz zu erstellen, oder wählen Sie in dem Bereich eine bereits vorhandene Azure SignalR Service-Instanz aus.

   [ ![Hervorgehobener Konfigurationslink beim Veröffentlichen](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   Von der Dienstabhängigkeit werden die im Anschluss aufgeführten Schritte ausgeführt, damit Ihre App in Azure automatisch zu Azure SignalR Service wechseln kann:

   * Aktualisieren von [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) für die Verwendung von Azure SignalR Service
   * Hinzufügen des Verweises auf das Azure SignalR Service-NuGet-Paket
   * Aktualisieren der Profileigenschaften, um die Abhängigkeitseinstellungen zu speichern
   * Konfigurieren des Geheimnisspeichers (abhängig von Ihrer Auswahl)
   * Hinzufügen der Konfiguration `appsettings`, um die ausgewählte Azure SignalR Service-Instanz als Ziel für Ihre App festzulegen

   [ ![In der Zusammenfassung der Änderungen werden die Kontrollkästchen verwendet, um alle Abhängigkeiten auszuwählen.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Veröffentlichen der App

   Ihre App ist nun veröffentlichungsbereit. Nach Abschluss der Veröffentlichung wird die Seite automatisch im Browser angezeigt. 
   > [!NOTE]
   > Aufgrund von Wartezeit beim Start der Azure App Service-Bereitstellung funktioniert sie möglicherweise nicht gleich beim ersten Besuch. Versuchen Sie in diesem Fall, die Seite zu aktualisieren.
   > Darüber hinaus können Sie durch Drücken von F12 den Debugmodus des Browsers verwenden, um sich zu vergewissern, dass Datenverkehr bereits zu Azure SignalR Service umgeleitet wurde.<kbd></kbd>

   [ ![Blazor-SignalR-Chatbeispiel mit einem Textfeld für Ihren Namen und einer mit „Chat!“ beschrifteten Schaltfläche zum Starten eines Chats](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Weiteres Thema: Aktivieren von Azure SignalR Service in der lokalen Entwicklung

1. Fügen Sie einen Verweis auf das Azure SignalR SDK hinzu.

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Fügen Sie in `Startup.ConfigureServices()` einen Aufruf für Azure SignalR Service hinzu.

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Konfigurieren Sie die Verbindungszeichenfolge (`ConnectionString`) von Azure SignalR Service in `appsetting.json` oder mithilfe des [Geheimnis-Manager-Tools](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager).

> [!NOTE]
> Schritt 2 kann durch die Verwendung von [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) für das SignalR SDK ersetzt werden.
> 
> 1. Fügen Sie in `appsetting.json` die Konfiguration zum Aktivieren von Azure SignalR Service hinzu.
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ServerStickyMode": "Required",
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Weisen Sie die Hostingstartassembly für die Verwendung des Azure SignalR SDK hinzu. Bearbeiten Sie `launchSettings.json`, und fügen Sie in `environmentVariables` eine Konfiguration wie die folgende hinzu:
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zum Bereinigen der im Rahmen dieses Tutorials erstellten Ressourcen löschen Sie die Ressourcengruppe über das Azure-Portal.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines einfachen Chatrooms mit Blazor Server-App
> * Ändern von Razor-Komponenten
> * Verwenden Sie von Ereignisbehandlung und Datenbindung in Komponenten
> * Schnelles Bereitstellen für Azure App Service in Visual Studio
> * Migrieren der lokalen SignalR-Instanz zu Azure SignalR Service

Informieren Sie sich ausführlicher über Hochverfügbarkeit.
> [!div class="nextstepaction"]
> [Resilienz und Notfallwiederherstellung](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Einführung in ASP.NET Core Blazor](/aspnet/core/blazor)
