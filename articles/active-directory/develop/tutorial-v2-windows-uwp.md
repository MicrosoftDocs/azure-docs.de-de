---
title: Erste Schritte mit der Microsoft Identity Platform UWP | Azure
description: Informationen, wie die Universelle Windows-Plattform-Anwendungen (UWP) eine API aufrufen können, die Zugriffstoken vom Microsoft Identity Platform-Endpunkt anfordert.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b7e59e39adbb485738ca66b7ad8e5ba8293ddc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784989"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Aufrufen der Microsoft Graph-API über eine UWP-Anwendung (XAML)

> [!div renderon="docs"]

Diese Anleitung veranschaulicht, wie eine native Universelle Windows-Plattform-Anwendung (UWP) ein Zugriffstoken abrufen und anschließend die Microsoft Graph-API aufrufen kann. Die Anleitung bezieht sich außerdem auf andere APIs, die Zugriffstoken von einem Microsoft Identity Platform-Endpunkt anfordern.

Am Ende dieser Anleitung kann Ihre Anwendung eine geschützte API sowohl mit persönlichen Konten (z.B. outlook.com, live.com u.a.) als auch Geschäfts,- Schul- und Unikonten von Unternehmen oder Organisationen aufrufen, die mit Azure Active Directory (Azure AD) arbeiten.

>[!NOTE]
> Für diesen Leitfaden ist Visual Studio 2017 mit einer installierten Entwicklung für die Universelle Windows-Plattform erforderlich. Im Artikel [Vorbereiten](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) finden Sie Anweisungen zum Herunterladen und Konfigurieren von Visual Studio für die Entwicklung der Apps der Universellen Windows-Plattform.

## <a name="how-this-guide-works"></a>Funktionsweise dieser Anleitung

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Die über diesen Leitfaden erstellte Beispiel-UWP-Anwendung fragt die Microsoft Graph-API oder eine Web-API ab, die Token von einem Microsoft Identity Platform-Endpunkt akzeptiert. In diesem Szenario wird ein Token über den Autorisierungsheader HTTP-Anforderungen hinzugefügt. Tokenabrufe und -verlängerungen werden von der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) gehandhabt.

## <a name="nuget-packages"></a>NuGet-Pakete

In dieser Anleitung werden die folgenden NuGet-Pakete verwendet:

|Bibliothek|BESCHREIBUNG|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

## <a name="set-up-your-project"></a>Einrichten des Projekts

Dieser Abschnitt enthält detaillierte Anweisungen zum Integrieren einer Windows Desktop .NET-Anwendung (XAML) in *Mit Microsoft anmelden*, damit Web-APIs abgefragt werden können, die ein Token anfordern (z.B. die Microsoft Graph-API).

In der in diesem Leitfaden erstellten Anwendung werden eine Schaltfläche, über welche die Graph-API abgefragt werden kann, eine Abmeldeschaltfläche sowie Textfelder mit den Ergebnissen der Aufrufe angezeigt.

> [!NOTE]
> Möchten Sie stattdessen das Visual Studio-Projekt dieses Beispiels herunterladen? [Laden Sie ein Projekt herunter](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip), und fahren Sie mit dem Schritt [Anwendungsregistrierung](#register-your-application "Schritt „Anwendungsregistrierung“") fort, um das Codebeispiel vor der Ausführung zu konfigurieren.

### <a name="create-your-application"></a>Erstellen der Anwendung

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.
2. Wählen Sie unter **Vorlagen** die Option **Visual C#** aus.
3. Wählen Sie **Leere App (universelles Windows)** aus.
4. Benennen Sie die App, und wählen Sie **OK** aus.
5. Wenn Sie dazu aufgefordert werden, wählen Sie eine Version für die Felder **Zielversion** und **Mindestversion** aus, und klicken Sie auf **OK**.

    >![Mindestversion und Zielversion](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Hinzufügen der MSAL (Microsoft Authentication Library) zu Ihrem Projekt
1. Klicken Sie in Visual Studio auf **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.
2. Kopieren Sie folgenden Befehl, und fügen Sie ihn in das Fenster **Paket-Manager-Konsole** ein:

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Der obige Befehl installiert die [Microsoft Authentication Library (MSAL)](https://aka.ms/msal-net). Die MSAL kümmert sich um das Erfassen, Zwischenspeichern und Aktualisieren von Benutzertoken für den Zugriff auf APIs, die von Microsoft Identity Platform geschützt werden.

## <a name="create-your-applications-ui"></a>Erstellen der Benutzeroberfläche Ihrer Anwendung

Die Datei **MainPage.xaml** wird als Teil Ihrer Projektvorlage automatisch erstellt. Öffnen Sie diese Datei, und befolgen Sie anschließend die nachstehenden Anweisungen:

* Ersetzen Sie den Knoten **Raster** Ihrer Anwendung durch den folgenden Code:

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Verwenden der MSAL zum Abrufen eines Tokens für die Microsoft Graph-API

Dieser Abschnitt zeigt, wie Sie über die MSAL ein Token für die Microsoft Graph-API abrufen können.

1.  Fügen Sie in der Datei **MainPage.xaml.cs** den Verweis auf die MSAL zur Klasse hinzu:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Ersetzen Sie den Code Ihrer <code>MainPage</code>-Klasse durch den folgenden Code:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    ```

### <a name="more-information"></a>Weitere Informationen

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

Das Aufrufen der `AcquireTokenInteractive`-Methode führt zum Öffnen eines Fensters, in dem Benutzer zum Anmelden aufgefordert werden. Bei Anwendungen müssen sich Benutzer in der Regel interaktiv anmelden, wenn sie zum ersten Mal auf eine geschützte Ressource zugreifen müssen. Die Anmeldung kann auch erforderlich sein, wenn beim automatischen Vorgang zum Beziehen eines Tokens ein Fehler auftritt (beispielsweise bei einem abgelaufenen Kennwort des Benutzers).

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Die `AcquireTokenSilent`-Methode dient zum Verwalten des Abrufens und Erneuerns von Token ohne Benutzereingriff. Nachdem `AcquireTokenInteractive` zum ersten Mal ausgeführt und vom Benutzer Anmeldeinformationen angefordert wurden, ist `AcquireTokenSilent` die übliche Methode zum Anfordern von Token bei nachfolgenden Aufrufen. Der Grund hierfür ist, dass der Bezug von Token automatisch erfolgt. Das Zwischenspeichern und Verlängern von Token wird von der MSAL gehandhabt.

Für die `AcquireTokenSilent`-Methode tritt schließlich ein Fehler auf. Gründe hierfür können sein, dass Benutzer sich entweder abgemeldet oder ihr Kennwort auf einem anderen Gerät geändert haben. Wenn die MSAL feststellt, dass das Problem durch Anfordern einer interaktiven Aktion gelöst werden kann, löst sie eine `MsalUiRequiredException`-Ausnahme aus. Ihre Anwendung kann diese Ausnahme auf zwei Arten handhaben:

* Sie kann sofort `AcquireTokenInteractive` aufrufen. Dieser Aufruf führt dazu, dass der Benutzer zum Anmelden aufgefordert wird. Dieses Muster wird normalerweise in Onlineanwendungen verwendet, in denen keine Offlineinhalte für den Benutzer verfügbar sind. Die in diesem Leitfaden generierte Beispielanwendung basiert auf diesem Muster. Sie können dies bei der ersten Ausführung des Beispiels in Aktion sehen.
  * Da bisher noch kein Benutzer die Anwendung genutzt hat, enthält `accounts.FirstOrDefault()` einen NULL-Wert, und es wird die Ausnahme `MsalUiRequiredException` ausgelöst.
  * Der Code im Beispiel behandelt die Ausnahme dann durch das Aufrufen von `AcquireTokenInteractive`. Dieser Aufruf führt dazu, dass der Benutzer zum Anmelden aufgefordert wird.

* Stattdessen kann für Benutzer auch ein visueller Hinweis gegeben werden, dass eine interaktive Anmeldung erforderlich ist, damit diese den richtigen Zeitpunkt für die Anmeldung auswählen können. Oder die Anwendung kann später noch einmal versuchen, `AcquireTokenSilent` auszuführen. Dieses Muster wird häufig verwendet, wenn Benutzer andere Anwendungsfunktionen ohne Störungen nutzen können, z.B. wenn Offlineinhalte in der Anwendung verfügbar sind. In diesem Fall können Benutzer entscheiden, wann sie sich anmelden, um entweder auf die geschützte Ressource zuzugreifen oder die veralteten Informationen zu aktualisieren. Alternativ dazu kann die Anwendung auch versuchen, `AcquireTokenSilent` erneut auszuführen, wenn das Netzwerk nach einem kurzzeitigen Ausfall wiederhergestellt wurde.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Aufrufen der Microsoft Graph-API mit dem zuvor bezogenen Token

* Fügen Sie folgende neue Methode zur Datei **MainPage.xaml.cs** hinzu. Diese Methode dient zum Richten einer `GET`-Anforderung an die Graph-API mithilfe eines `Authorization`-Headers:

    ```csharp
    /// <summary>
    /// Perform an HTTP GET request to a URL using an HTTP Authorization header
    /// </summary>
    /// <param name="url">The URL</param>
    /// <param name="token">The token</param>
    /// <returns>String containing the results of the GET operation</returns>
    public async Task<string> GetHttpContentWithToken(string url, string token)
    {
        var httpClient = new System.Net.Http.HttpClient();
        System.Net.Http.HttpResponseMessage response;
        try
        {
            var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
            // Add the token in Authorization header
            request.Headers.Authorization = 
              new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
            response = await httpClient.SendAsync(request);
            var content = await response.Content.ReadAsStringAsync();
            return content;
        }
        catch (Exception ex)
        {
            return ex.ToString();
        }
    }
    ```

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Weitere Informationen zum Richten eines REST-Aufrufs an eine geschützte API

In dieser Beispielanwendung dient die `GetHttpContentWithToken`-Methode zum Richten einer HTTP `GET`-Anforderung an eine geschützte Ressource, die ein Token erfordert, und zum anschließenden Zurückgeben des Inhalts an den Aufrufer. Diese Methode fügt das abgerufene Token in den **HTTP-Autorisierungsheader** ein. In diesem Beispiel ist die Ressource der Endpunkt **me** der Microsoft Graph-API, der die Profilinformationen des Benutzers anzeigt.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Hinzufügen einer Methode zum Abmelden des Benutzers

* Fügen Sie folgende Methode zur Datei **MainPage.xaml.cs** hinzu, um den Benutzer abzumelden:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> MSAL.NET verwendet asynchrone Methoden, um Token abzurufen oder Konten zu manipulieren. Deshalb müssen Sie dafür sorgen, dass auf die Benutzeroberfläche bezogene Aktionen im UI-Thread durchgeführt werden (daher `Dispatcher.RunAsync` und die Vorsichtsmaßnahmen zum Aufruf von `ConfigureAwait(false)`).

### <a name="more-information-on-sign-out"></a>Weitere Informationen zum Abmelden

Die `SignOutButton_Click`-Methode entfernt den Benutzer aus dem MSAL-Benutzercache. Dadurch wird die MSAL effektiv angewiesen, den aktuellen Benutzer zu vergessen, sodass eine künftige Anforderung zum Abrufen eines Tokens nur erfolgreich ist, wenn diese interaktiv gestaltet wird.
Obwohl die Anwendung in diesem Beispiel einen einzelnen Benutzer unterstützt, werden von der MSAL Szenarien unterstützt, in denen mehrere Konten gleichzeitig angemeldet werden können. Ein Beispiel ist eine E-Mail-Anwendung, in der ein Benutzer mehrere Konten hat.

## <a name="display-basic-token-information"></a>Anzeigen grundlegender Informationen zum Token

* Fügen Sie **MainPage.xaml.cs** die folgende Methode hinzu, um grundlegende Informationen zum Token anzuzeigen:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Weitere Informationen

Über **OpenID Connect** abgerufene ID-Token enthalten auch eine kleine Teilmenge von Informationen zum Benutzer. `DisplayBasicTokenInfo` zeigt grundlegende Informationen im Token: beispielsweise den Anzeigenamen und die ID des Benutzers sowie das Ablaufdatum des Tokens und die Zeichenfolge, die das Zugriffstoken selbst darstellt. Wenn Sie mehrmals auf die Schaltfläche **Microsoft Graph-API aufrufen** klicken, sehen Sie, dass dasselbe Token für nachfolgende Anforderungen wiederverwendet wurde. Sie können auch feststellen, dass das Ablaufdatum verlängert wurde, wenn die MSAL entscheidet, dass es Zeit ist, das Token zu verlängern.

## <a name="register-your-application"></a>Anwendung registrieren

Nun müssen Sie Ihre Anwendung im Microsoft-Anwendungsregistrierungsportal registrieren:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Ihr Konto in mehr als einem Azure AD-Mandanten vorhanden ist, wählen Sie `Directory + Subscription` in der rechten oberen Ecke im Menü aus. Wählen Sie dann für Ihre Portalsitzung den gewünschten Azure AD-Mandanten aus.
1. Navigieren Sie zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) von Microsoft Identity Platform für Entwickler.
1. Wählen Sie **Neue Registrierung** aus.
   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `UWP-App-calling-MSGraph`).
   - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.
   - Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Suchen Sie auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID** und notieren Sie ihn zur späteren Verwendung. Wechseln Sie zurück zu Visual Studio, öffnen Sie **MainPage.xaml.cs**, und ersetzen Sie den Wert von „ClientId“ durch die soeben registrierte Anwendungs-ID:
1. Wählen Sie in der Liste mit den Seiten für die App die Option **Authentifizierung** aus.
   1. Wählen Sie im Abschnitt **Umleitungs-URIs** in der Liste der Umleitungs-URIs Folgendes aus:
   1. Wählen Sie in der **TYPE**-Spalte **Öffentlicher Client (Mobilgerät und Desktop)** aus.
   1. Geben Sie in der Spalte **UMLEITUNGS-URI** den Wert `urn:ietf:wg:oauth:2.0:oob` ein.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie in der Liste mit den Seiten für die App die Option **API-Berechtigungen** aus.
   - Klicken Sie auf die Schaltfläche **Berechtigung hinzufügen**.
   - Stellen Sie sicher, dass die Registerkarte **Microsoft-APIs** aktiviert ist.
   - Klicken Sie im Abschnitt *Häufig verwendete Microsoft-APIs* auf **Microsoft Graph**.
   - Stellen Sie im Abschnitt **Delegierte Berechtigungen** sicher, dass die richtigen Berechtigungen aktiviert sind: **User.Read**. Verwenden Sie bei Bedarf das Suchfeld.
   - Wählen Sie die Schaltfläche **Berechtigungen hinzufügen** aus.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Aktivieren einer integrierten Authentifizierung für Verbunddomänen (optional)

Das Anwendungsmanifest muss zusätzliche Funktionen aktivieren, um eine integrierte Windows-Authentifizierung bei der Verwendung mit einer Azure AD-Verbunddomäne zu aktivieren:

1. Doppelklicken Sie auf **Package.appxmanifest**.
2. Wählen Sie die Registerkarte **Funktionen** aus, und stellen Sie sicher, dass folgende Einstellungen aktiviert sind:

    - Unternehmensauthentifizierung
    - Private Netzwerke (Client und Server)
    - Freigegebene Benutzerzertifikate

> [!IMPORTANT]
> Die [integrierte Windows-Authentifizierung](https://aka.ms/msal-net-iwa) ist standardmäßig nicht für dieses Beispiel konfiguriert, da für Anwendungen, welche die Funktion *Unternehmensauthentifizierung* oder *Freigegebene Benutzerzertifikate* anfordern, im Windows Store eine höhere Überprüfungsebene erforderlich ist und nicht alle Entwickler die Überprüfung auf der höheren Ebene durchführen möchten. Aktivieren Sie diese Einstellung nur, wenn Sie die integrierte Windows-Authentifizierung mit einer Azure AD-Verbunddomäne benötigen.

## <a name="test-your-code"></a>Testen Ihres Codes

Drücken Sie zum Testen Ihrer Anwendung die F5-Taste, um das Projekt in Visual Studio auszuführen. Das Hauptfenster wird angezeigt:

![Benutzeroberfläche der Anwendung](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Wenn Sie Tests durchführen möchten, wählen Sie **Microsoft Graph-API aufrufen** aus. Verwenden Sie dann zum Anmelden ein Azure AD-Konto (Organisationskonto) oder ein Microsoft-Konto (z. B. live.com oder outlook.com). Wenn Sie sich erstmals anmelden, wird ein Fenster eingeblendet, das den Benutzer auffordert, sich anzumelden:

![Anmeldeseite](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Zustimmung

Bei der allerersten Anmeldung in Ihrer Anwendung wird ein Genehmigungsbildschirm ähnlich dem folgenden angezeigt: Wählen Sie **Ja** aus, um zum Zugriff explizit Ihre Zustimmung zu geben:

![Bildschirm für die Zugriffsgenehmigung](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Erwartete Ergebnisse

Es werden Benutzerprofilinformationen angezeigt, die vom Microsoft Graph-API-Aufruf auf dem Bildschirm **API-Aufrufergebnisse** zurückgegeben werden:

![Bildschirm „API-Aufrufergebnisse“](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Grundlegende Informationen zum Token, das über `AcquireTokenInteractive` oder `AcquireTokenSilent` abgerufen wurde, werden im Feld **Tokeninformationen** angezeigt:

|Eigenschaft  |Format  |BESCHREIBUNG |
|---------|---------|---------|
|**Benutzername** |<span>user@domain.com</span> |Der zur Identifizierung des Benutzers verwendete Benutzername|
|**Ablaufdatum des Tokens** |Datetime |Die Uhrzeit, zu der das Token abläuft. MSAL verlängert die Ablauffrist, indem das Token bei Bedarf verlängert wird.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Weitere Informationen zu Bereichen und delegierten Berechtigungen

Die Microsoft Graph-API benötigt den Bereich *user.read*, um das Benutzerprofil zu lesen. Dieser Bereich wird standardmäßig jeder Anwendung automatisch hinzugefügt, die im Anwendungsregistrierungsportal registriert wird. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Die Microsoft Graph-API benötigt den Bereich *Calendars.Read*, um die Kalender des Benutzers aufzuführen.

Um auf die Kalender des Benutzers im Kontext einer Anwendung zugreifen zu können, müssen Sie den Informationen für die Anwendungsregistrierung die delegierte Berechtigung *Calendars.Read* hinzufügen. Fügen Sie dann dem Aufruf von `acquireTokenSilent` den Bereich *Calendars.Read* hinzu.

> [!NOTE]
> Wenn Sie die Anzahl der Bereiche erhöhen, werden Benutzer ggf. zu weiteren Genehmigungen aufgefordert.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="issue-1"></a>Problem 1:

Wenn Sie sich in einer Azure AD-Verbunddomäne bei Ihrer Anwendung anmelden, tritt einer der folgenden Fehler auf:

* In der Anforderung wurde kein gültiges Clientzertifikat gefunden.
* Im Zertifikatspeicher des Benutzers wurden keine gültigen Zertifikate gefunden.
* Versuchen Sie erneut, und wählen Sie eine andere Authentifizierungsmethode aus.

**Ursache:** Unternehmens- und Zertifikatsfunktionen sind nicht aktiviert.

**Lösung:** Führen Sie die Schritte unter [Integrierte Authentifizierung für Verbunddomänen](#enable-integrated-authentication-on-federated-domains-optional) aus.

### <a name="issue-2"></a>Problem 2:

Nach der Aktivierung von [Integrierte Authentifizierung für Verbunddomänen](#enable-integrated-authentication-on-federated-domains-optional) und dem Versuch, sich über Windows Hello auf einem Windows 10-Computer bei einer Umgebung mit konfigurierter mehrstufiger Authentifizierung anzumelden, wird die Liste mit den Zertifikaten angezeigt. Wenn Sie Ihre PIN verwenden möchten, wird das Fenster „PIN“ jedoch nie angezeigt.

**Ursache:** Bekannte Einschränkung des Webauthentifizierungsbrokers in UWP-Anwendungen, die unter Windows 10 Desktop ausgeführt werden (funktioniert unter Windows 10 Mobile).

**Problemumgehung:** Wählen Sie **Mit anderen Optionen anmelden** aus. Wählen Sie dann **Mit Benutzernamen und Kennwort anmelden** und **Kennwort angeben** aus, und durchlaufen Sie anschließend die Telefonauthentifizierung.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
