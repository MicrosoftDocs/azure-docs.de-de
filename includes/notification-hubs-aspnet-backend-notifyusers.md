---
title: include file
description: Includedatei, die Code zum Erstellen eines Back-End-ASP .NET-WebAPI-Projekts enthält.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 6f490b6f25112ed8a10bbd865070bd07ea3ee84f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016943"
---
## <a name="create-the-webapi-project"></a>Erstellen des WebAPI-Projekts

In den folgenden Abschnitten wird die Erstellung eines neuen ASP.NET-WebAPI-Back-Ends erläutert. Dieser Prozess hat drei Hauptfunktionen:

- **Authentifizieren von Clients:** Sie fügen einen Meldungshandler hinzu, um Clientanforderungen zu authentifizieren und den Benutzer der Anforderung zuzuordnen.
- **Registrieren für Benachrichtigungen unter Verwendung des WebAPI-Back-Ends:** Sie fügen einen Controller hinzu, um neue Registrierungen für ein Clientgerät zum Empfangen von Benachrichtigungen zu verarbeiten. Der authentifizierte Benutzername wird der Registrierung automatisch als [Tag](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md) hinzugefügt.
- **Senden von Benachrichtigungen an Clients:** Sie fügen einen Controller hinzu, um Benutzern die Möglichkeit zu geben, einen sicheren Pushvorgang an Geräte und Clients auszulösen, die dem Tag zugeordnet sind.

Gehen Sie zum Erstellen des neuen ASP.NET-WebAPI-Back-Ends wie folgt vor:

> [!IMPORTANT]
> Falls Sie Visual Studio 2015 oder eine ältere Version verwenden, vergewissern Sie sich vor Beginn dieses Tutorials, dass bei Ihnen die neueste Version des NuGet-Paket-Managers für Visual Studio installiert ist.
>
>Um dies zu überprüfen, starten Sie Visual Studio. Wählen Sie im Menü **Extras** die Option **Erweiterungen und Updates** aus. Suchen Sie in Ihrer Version von Visual Studio nach **NuGet-Paket-Manager**, und vergewissern Sie sich, dass Sie über die neueste Version verfügen. Deinstallieren Sie andernfalls Ihre Version, und installieren Sie den NuGet-Paket-Manager erneut.

![Screenshot: Dialogfeld „Erweiterungen und Updates“ mit hervorgehobenem Paket für den NuGet-Paket-Manager für Visual Studio][B4]

> [!NOTE]
> Stellen Sie sicher, dass Sie das Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) für die Websitebereitstellung installiert haben.

1. Starten Sie Visual Studio oder Visual Studio Express.

2. Wählen Sie **Server-Explorer** aus, und melden Sie sich bei Ihrem Azure-Konto an. Zur Erstellung der Websiteressourcen für Ihr Konto müssen Sie angemeldet sein.

3. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Visual Studio-Projektmappe, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Projekt**.
4. Erweitern Sie **Visual C#** , wählen Sie **Web** aus, und klicken Sie auf **ASP.NET-Webanwendung**.

5. Geben Sie im Feld **Name** die Zeichenfolge **AppBackend** ein, und wählen Sie dann **OK** aus.

    ![Das Fenster „Neues Projekt“][B1]

6. Aktivieren Sie im Fenster **Neues ASP.NET-Projekt** das Kontrollkästchen **Web-API**, und wählen Sie anschließend **OK** aus.

    ![Das Fenster „Neues ASP.NET-Projekt“][B2]

7. Wählen Sie im Fenster **Microsoft Azure-Web-App konfigurieren** ein Abonnement aus, und führen Sie anschließend in der Liste **App Service-Plan** eine der folgenden Aktionen aus:

    * Wählen Sie einen bereits erstellten App Service-Plan aus.
    * Wählen Sie **Einen neuen App Services-Plan erstellen** aus, und erstellen Sie einen neuen Plan.

   Sie benötigen für dieses Lernprogramm keine Datenbank. Wählen Sie nach der Wahl Ihres App Service-Plans **OK** aus, um das Projekt zu erstellen.

    ![Das Fenster „Microsoft Azure-Web-App konfigurieren“][B5]

    Wird diese Seite zum Konfigurieren des App Service-Plans nicht angezeigt, fahren Sie mit dem Tutorial fort. Sie können ihn später beim Veröffentlichen der App konfigurieren. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>Authentifizieren von Clients beim WebAPI-Back-End

In diesem Abschnitt erstellen Sie für das neue Back-End eine neue Meldungshandlerklasse namens **AuthenticationTestHandler**. Diese Klasse wird von [DelegatingHandler](/previous-versions/visualstudio/hh193679(v=vs.118)) abgeleitet und als Meldungshandler hinzugefügt, damit alle beim Back-End eingehenden Anforderungen verarbeitet werden können.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **AppBackend**, und wählen Sie **Hinzufügen** und anschließend **Klasse** aus.
2. Geben Sie der neuen Klasse den Namen **AuthenticationTestHandler.cs**, und wählen Sie **Hinzufügen** aus, um die Klasse zu generieren. Mit dieser Klasse werden Benutzer der Einfachheit halber unter Verwendung der *Standardauthentifizierung* authentifiziert. Ihre App kann ein beliebiges Authentifizierungsschema verwenden.
3. Fügen Sie in der Datei "AuthenticationTestHandler.cs" die folgenden `using` -Anweisungen hinzu:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. Ersetzen Sie in der Datei „AuthenticationTestHandler.cs“ die Definition der Klasse `AuthenticationTestHandler` durch den folgenden Code:

    Der Handler autorisiert die Anforderung, wenn die folgenden drei Bedingungen erfüllt sind:

   * Die Anforderung enthält einen *Autorisierungsheader*.
   * Für die Anforderung wird die *Standardauthentifizierung* verwendet.
   * Bei der Benutzernamen-Zeichenfolge und der Kennwortzeichenfolge handelt es sich um die gleiche Zeichenfolge.

   Andernfalls wird die Anforderung abgelehnt. Diese Authentifizierung ist keine echte Vorgehensweise zur Authentifizierung und Autorisierung. Hierbei handelt es sich lediglich um ein einfaches Beispiel für dieses Tutorial.

   Wenn die Anforderungsnachricht von `AuthenticationTestHandler` authentifiziert und autorisiert wurde, wird der Benutzer der Standardauthentifizierung an die aktuelle Anforderung in [HttpContext](/dotnet/api/system.web.httpcontext.current) angefügt. Die Benutzerinformationen in „HttpContext“ werden später von einem anderen Controller (RegisterController) verwendet, um der Registrierungsanforderung für die Benachrichtigung ein [Tag](/previous-versions/azure/azure-services/dn530749(v=azure.100)) hinzuzufügen.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (VerifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool VerifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Sicherheitshinweis: Die Klasse `AuthenticationTestHandler` ermöglicht keine wirkliche Authentifizierung. Sie wird nur verwendet, um eine Standardauthentifizierung zu imitieren, und ist nicht sicher. Sie müssen einen sicheren Authentifizierungsmechanismus in Ihren Produktionsanwendungen und -diensten implementieren.
5. Fügen Sie am Ende der Methode `Register` in der Klasse **App_Start/WebApiConfig.cs** den folgenden Code hinzu, um den Meldungshandler zu registrieren:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Speichern Sie die Änderungen.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Registrieren für Benachrichtigungen unter Verwendung des WebAPI-Back-Ends

In diesem Abschnitt wird dem WebAPI-Back-End ein neuer Controller hinzugefügt, um Anforderungen zum Registrieren eines Benutzers und eines Geräts für Benachrichtigungen unter Verwendung der Clientbibliothek für Notification Hubs zu verarbeiten. Der Controller fügt ein Benutzertag für den Benutzer hinzu, der durch `AuthenticationTestHandler` authentifiziert und an „HttpContext“ angefügt wurde. Die Markierung hat das Zeichenfolgenformat `"username:<actual username>"`.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **AppBackend**, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich **Online** aus, und geben Sie dann im Feld **Suche** die Zeichenfolge **Microsoft.Azure.NotificationHubs** ein.

3. Wählen Sie in der Ergebnisliste die Option **Microsoft Azure Notification Hubs** und anschließend **Installieren** aus. Schließen Sie die Installation ab, und schließen Sie dann das Fenster des NuGet-Paket-Managers.

    Dadurch wird mithilfe des [Microsoft.Azure.NotificationHubs-NuGet-Pakets](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ein Verweis auf das Azure Notification Hubs-SDK hinzugefügt.

4. Erstellen Sie eine neue Klassendatei, die die Verbindung mit dem Notification Hub darstellt, der zum Senden von Benachrichtigungen verwendet wird. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Modelle**, und wählen Sie dann **Hinzufügen** und anschließend **Klasse** aus. Nennen Sie die neue Klasse **Notifications.cs**, und wählen Sie dann **Hinzufügen** aus, um die Klasse zu generieren.

    ![Das Fenster „Neues Element hinzufügen“][B6]

5. Fügen Sie die folgende `using` -Anweisung am Anfang der Datei "Notifications.cs" hinzu:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Ersetzen Sie die Definition der Klasse `Notifications` durch den folgenden Code und die beiden Platzhalter durch die Verbindungszeichenfolge (mit Vollzugriff) für Ihren Notification Hub bzw. durch den Namen des Hubs (verfügbar im [Azure-Portal](https://portal.azure.com)):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > Geben Sie für den Hub den **Namen** und einen Wert für **DefaultFullSharedAccessSignature** ein, bevor Sie fortfahren. 
    
7. Erstellen Sie als Nächstes einen neuen Controller namens **RegisterController**. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie dann **Hinzufügen** und **Controller** aus.

8. Wählen Sie **Web-API-2-Controller – Leer** und anschließend **Hinzufügen** aus.

    ![Das Fenster „Gerüst hinzufügen“][B7]

9. Geben Sie im Feld **Controllername** die Zeichenfolge **RegisterController** ein, um die neue Klasse zu benennen, und wählen Sie anschließend **Hinzufügen** aus.

    ![Das Fenster „Controller hinzufügen“][B8]

10. Fügen Sie in der Datei "RegisterController.cs" die folgenden `using` -Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Fügen Sie innerhalb der `RegisterController` -Klassendefinition den folgenden Code hinzu. Mit diesem Code fügen Sie ein Benutzertag für den Benutzer hinzu, der HttpContext angefügt ist. Der Benutzer wurde mit dem von Ihnen hinzugefügten Nachrichtenfilter `AuthenticationTestHandler` authentifiziert und HttpContext angefügt. Sie können auch optionale Überprüfungen hinzufügen, um zu überprüfen, dass der Benutzer die richtigen Berechtigungen besitzt, um sich für die angeforderten Tags zu registrieren.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Speichern Sie die Änderungen.

## <a name="send-notifications-from-the-webapi-backend"></a>Senden von Benachrichtigungen über das WebAPI-Back-End

In diesem Abschnitt wird ein neuer Controller hinzugefügt, der Clientgeräten das Senden einer Benachrichtigung ermöglicht. Die Benachrichtigung basiert auf dem Benutzernamentag, das die Azure Notification Hubs .NET-Bibliothek des ASP.NET WebAPI-Back-Ends verwendet.

1. Erstellen Sie einen weiteren neuen Domänencontroller namens **NotificationsController**. Verwenden Sie dazu die gleiche Vorgehensweise wie bei der Erstellung von **RegisterController** im vorherigen Abschnitt.

2. Fügen Sie in der Datei "NotificationsController.cs" die folgenden `using` -Anweisungen hinzu:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Fügen Sie der Klasse **NotificationsController** die folgende Methode hinzu:

    Mit diesem Code wird ein Benachrichtigungstyp gesendet, der auf dem Parameter `pns` des Plattformbenachrichtigungssystems (Plattform Notification System, PNS) basiert. Der Wert von `to_tag` dient zum Festlegen des *username* -Tags in der Nachricht. Dieses Tag muss mit einem Benutzernamentag einer aktiven Notification Hub-Registrierung übereinstimmen. Die Benachrichtigung wird aus dem Text der POST-Anforderung abgerufen und für den Ziel-PNS formatiert.

    Abhängig von dem PNS, mit dem Ihre unterstützten Geräte Benachrichtigungen empfangen, werden Benachrichtigungen mit unterschiedlichen Formaten unterstützt. Ein Beispiel: Angenommen, Sie verwenden auf Windows-Geräten eine [Popupbenachrichtigung mit WNS](/uwp/schemas/tiles/toastschema/schema-root), die nicht direkt durch ein anderes PNS unterstützt wird. In diesem Fall muss Ihr Back-End die Benachrichtigung so formatieren, dass sie mit dem PNS der zu unterstützenden Geräte kompatibel ist. Verwenden Sie anschließend die entsprechende Sende-API für die [NotificationHubClient-Klasse](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient).

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Drücken Sie **F5**, um die Anwendung auszuführen und sich zu vergewissern, dass soweit alles funktioniert. Die App öffnet einen Webbrowser und wird auf der Homepage von ASP.NET angezeigt.

## <a name="publish-the-new-webapi-backend"></a>Veröffentlichen des neuen WebAPI-Back-Ends

Als Nächstes wird die App auf einer Azure-Website bereitgestellt, damit sie für alle Geräte zur Verfügung steht.

1. Klicken Sie mit der rechten Maustaste auf das Projekt **AppBackend**, und wählen Sie **Veröffentlichen** aus.

2. Wählen Sie **Microsoft Azure App Service** als Veröffentlichungsziel und anschließend **Veröffentlichen aus. Das Fenster „App Service erstellen“ wird geöffnet. Hier können Sie alle Azure-Ressourcen erstellen, die zum Ausführen der ASP.NET-Web-App in Azure benötigt werden.

    ![Die Kachel „Microsoft Azure App Service“][B15]

3. Wählen Sie im Fenster **App Service erstellen** Ihr Azure-Konto aus. Wählen Sie **Typ ändern** > **Web App** aus. Behalten Sie den Standardwert für **Web-App-Name** bei, und wählen Sie **Abonnement**, **Ressourcengruppe** und **App Service-Plan** aus.

4. Klicken Sie auf **Erstellen**.

5. Notieren Sie sich die **Website-URL** aus der **Zusammenfassung**. Diese URL ist später im Tutorial Ihr *Back-End-Endpunkt*.

6. Wählen Sie **Veröffentlichen**.

Nach Abschluss des Assistenten wird die ASP.NET Web-App in Azure veröffentlicht und anschließend im Standardbrowser geöffnet.  Ihre Anwendung kann in Azure App Services angezeigt werden.

In der URL wird der von Ihnen angegebene Web-App-Name im Format „http://<App-Name>.azurewebsites.net“ verwendet.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG