---
title: Senden von Benachrichtigungen an bestimmte Benutzer mit Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe UWP-Anwendungen (Universelle Windows-Plattform) Benachrichtigungen an bestimmte Benutzer senden.
documentationcenter: windows
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 97a6a45ab01fc113b79a48ba7fcb246d528684be
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019479"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Tutorial: Senden von Benachrichtigungen an bestimmte Benutzer mit Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Übersicht

In diesem Tutorial wird beschrieben, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Mit dem authentifizierten Clientbenutzer wird automatisch ein Tag vom Back-End zur Benachrichtigungsregistrierung hinzugefügt. Wenn das Back-End einen Benutzer einer Clientanwendung authentifiziert, fügt es der Benachrichtigungsregistrierung automatisch ein Tag hinzu. Das Back-End nutzt dieses Tag, um Benachrichtigungen an den jeweiligen Benutzer zu senden.

> [!NOTE]
> Den vollständigen Code für dieses Tutorial finden Sie [auf GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen des WebAPI-Projekts
> * Authentifizieren von Clients beim WebAPI-Back-End
> * Registrieren für Benachrichtigungen unter Verwendung des WebAPI-Back-Ends
> * Senden von Benachrichtigungen über das WebAPI-Back-End
> * Veröffentlichen des neuen WebAPI-Back-Ends
> * Aktualisieren des Codes für das Clientprojekt
> * Testen der Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial baut auf dem Benachrichtigungs-Hub und dem Visual Studio-Projekt auf, die Sie unter [Tutorial: Senden von Benachrichtigungen an Apps für die universelle Windows-Plattform mit Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) erstellt haben. Dieses müssen Sie daher zunächst abschließen, bevor Sie mit diesem Tutorial beginnen.

> [!NOTE]
> Wenn Sie Mobile Apps in Azure App Service als Back-End-Dienst verwenden, finden Sie weitere Informationen in der [Mobile Apps-Version](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push) dieses Tutorials.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-uwp-client"></a>Aktualisieren des Codes für den UWP-Client

In diesem Abschnitt aktualisieren Sie den Code des Projekts, das Sie unter [Tutorial: Senden von Benachrichtigungen an Apps für die universelle Windows-Plattform mit Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) erstellt haben. Das Projekt sollte bereits mit dem Windows Store verknüpft sein. Es sollte auch so konfiguriert sein, dass es Ihren Benachrichtigungs-Hub verwendet. In diesem Abschnitt fügen Sie Code hinzu, um das neue WebAPI-Back-End aufzurufen und es zum Registrieren und Senden von Benachrichtigungen zu verwenden.

1. Öffnen Sie in Visual Studio die Projektmappe, die Sie unter [Tutorial: Senden von Benachrichtigungen an Apps für die universelle Windows-Plattform mit Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) erstellt haben.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das UWP-Projekt (Universelle Windows-Plattform), und klicken Sie dann auf **NuGet-Pakete verwalten**.
3. Wählen Sie auf der linken Seite **Durchsuchen** aus.
4. Geben Sie in das **Suchfeld** den Begriff **Http Client** ein.
5. Klicken Sie in der Ergebnisliste auf **System.Net.Http** und dann auf **Installieren**. Schließen Sie die Installation ab.
6. Geben Sie in das NuGet-**Suchfeld** die Zeichenfolge **Json.net** ein. Installieren Sie das Paket **Newtonsoft.json**, und schließen Sie dann das Fenster des NuGet-Paket-Managers.
7. Doppelklicken Sie im Projektmappen-Explorer im Projekt **WindowsApp** auf **MainPage.xaml**, um die Datei im Visual Studio-Editor zu öffnen.
8. Ersetzen Sie in der Datei `MainPage.xaml` den Abschnitt `<Grid>` durch den folgenden Code: Dieser Code fügt ein Textfeld für den Benutzernamen und das Kennwort hinzu, mit denen sich der Benutzer authentifiziert. Darüber hinaus werden Textfelder für die Benachrichtigungsmeldung und das Benutzernamentag hinzugefügt, das die Benachrichtigung empfangen soll:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleFCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="FCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```

9. Öffnen Sie im Projektmappen-Explorer die Datei `MainPage.xaml.cs` für die Projekte **(Windows 8.1)** und **(Windows Phone 8.1)** . Fügen Sie am Anfang der beiden Dateien die folgenden `using` -Anweisungen ein:

    ```csharp
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```

10. Fügen Sie in `MainPage.xaml.cs` für das Projekt **WindowsApp** das folgende Element der `MainPage`-Klasse hinzu. Ersetzen Sie `<Enter Your Backend Endpoint>` unbedingt durch den tatsächlichen Back-End-Endpunkt, den Sie zuvor abgerufen haben. Beispiel: `http://mybackend.azurewebsites.net`.

    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

11. Fügen Sie den folgenden Code der MainPage-Klasse in `MainPage.xaml.cs` für die Projekte **(Windows 8.1)** und **(Windows Phone 8.1)** hinzu.

    Die `PushClick` -Methode ist ein Klick-Handler für die Schaltfläche **Send Push** . Sie ruft den Back-End auf, um eine Benachrichtigung an alle Geräte mit einem Benutzernamentag zu senden, das dem `to_tag` -Parameter entspricht. Die Benachrichtigungsmeldung wird als JSON-Inhalt im Anforderungstext gesendet.

    Die `LoginAndRegisterClick`-Methode ist ein Klickhandler für die Schaltfläche **Login and register**. Sie speichert das Token für die Standardauthentifizierung (kann ein beliebiges von Ihrem Authentifizierungsschema verwendetes Token sein) im lokalen Speicher und verwendet dann `RegisterClient` zur Registrierung für Benachrichtigungen mithilfe des Back-Ends.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleFCM.IsChecked.Value)
        {
            await sendPush("fcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS.
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```

12. Öffnen Sie `App.xaml.cs`, und suchen Sie im Ereignishandler `InitNotificationsAsync()` nach dem Aufruf von `OnLaunched()`. Kommentieren Sie den Aufruf von `InitNotificationsAsync()`aus, oder löschen Sie ihn. Der Schaltflächenhandler initialisiert Benachrichtigungsregistrierungen:

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```

13. Klicken Sie mit der rechten Maustaste auf das Projekt **WindowsApp**, klicken Sie auf **Hinzufügen** und dann auf **Klasse**. Nennen Sie die Klasse `RegisterClient.cs`, und klicken Sie dann auf **OK**, um die Klasse zu generieren.

    Diese Klasse umschließt die REST-Aufrufe, die für das Kontaktieren des App-Back-Ends erforderlich sind, um sich für Pushbenachrichtigungen zu registrieren. Außerdem werden die vom Notification Hub erstellten *registrationIds* lokal gespeichert, wie unter [Registrierung vom App-Back-End aus](/previous-versions/azure/azure-services/dn743807(v=azure.100))beschrieben. Wenn Sie auf die Schaltfläche **Log in and register** klicken, wird ein Authentifizierungstoken aus dem lokalen Speicher verwendet.
14. Fügen Sie die folgenden `using` -Anweisungen am Anfang der Datei "RegisterClient.cs" hinzu:

    ```csharp
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```

15. Fügen Sie innerhalb der `RegisterClient`-Klassendefinition den folgenden Code hinzu:

    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```

16. Speichern Sie alle Änderungen.

## <a name="test-the-application"></a>Testen der Anwendung

1. Starten Sie die Anwendung unter beiden Windows-Versionen.
2. Geben Sie einen **Benutzernamen** und ein **Kennwort** ein, wie im folgenden Bildschirm dargestellt. Sie sollten einen anderen Benutzernamen und ein anders Kennwort als in Windows Phone eingeben.
3. Klicken Sie auf **Login and register** , und überprüfen Sie, ob ein Dialogfeld anzeigt, dass Sie sich angemeldet haben. Durch diesen Code wird auch die Schaltfläche **Send Push** aktiviert.

    ![Screenshot der Notification Hubs-Anwendung mit ausgefülltem Benutzernamen und Kennwort.][14]
4. Geben Sie dann im Feld **Recipient Username Tag** den registrierten Benutzernamen ein. Geben Sie eine Benachrichtigungsmeldung ein, und klicken Sie auf **Send Push**.
5. Nur die Geräte, die mit dem entsprechenden Benutzernamentag registriert wurden, erhalten eine Benachrichtigungsmeldung.

    ![Screenshot der Notification Hubs-Anwendung, die die Meldung anzeigt, die gepusht wurde.][15]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Pushbenachrichtigungen an bestimmte Benutzer senden, deren Registrierungen Tags zugeordnet sind. Um zu erfahren, wie Sie standortbasierte Pushbenachrichtigungen senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden standortbasierter Pushbenachrichtigungen](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png

<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
