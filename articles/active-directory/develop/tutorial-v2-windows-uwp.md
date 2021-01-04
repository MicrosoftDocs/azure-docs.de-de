---
title: 'Tutorial: Erstellen einer UWP-App (Universelle Windows-Plattform), die Microsoft Identity Platform für die Authentifizierung verwendet | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine UWP-Anwendung, die Microsoft Identity Platform zum Anmelden von Benutzern und zum Abrufen eines Zugriffstokens verwendet, um im Namen der Benutzer die Microsoft Graph-API aufzurufen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 774c17af88e45e25cf1e8edc0df60ab55fe53e0e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95974328"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Tutorial: Aufrufen der Microsoft Graph-API über eine UWP-Anwendung (UWP = Universelle Windows-Plattform)

In diesem Tutorial erstellen Sie eine native UWP-App (Universelle Windows-Plattform), die Benutzer anmeldet und ein Zugriffstoken abruft, um die Microsoft Graph-API aufzurufen. 

Am Ende dieser Anleitung kann Ihre Anwendung eine geschützte API sowohl mit persönlichen Konten (z.B. outlook.com, live.com u.a.) als auch Geschäfts,- Schul- und Unikonten von Unternehmen oder Organisationen aufrufen, die mit Azure Active Directory (Azure AD) arbeiten.

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Erstellen eines Projekts namens *Universelle Windows-Plattform (UWP)* in Visual Studio
> * Registrieren der Anwendung im Azure-Portal
> * Hinzufügen von Code zur Unterstützung der Benutzeranmeldung und -abmeldung
> * Hinzufügen von Code zum Aufrufen der Microsoft Graph-API
> * Testen der App

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) mit installierter Workload [Entwicklung für die universelle Windows-Plattform](/windows/uwp/get-started/get-set-up)

## <a name="how-this-guide-works"></a>Funktionsweise dieser Anleitung

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

In diesem Leitfaden wird eine UWP-Beispielanwendung erstellt, von der die Microsoft Graph-API abgefragt wird. In diesem Szenario wird ein Token mit dem Autorisierungsheader HTTP-Anforderungen hinzugefügt. Tokenbezug und -verlängerungen werden von der Microsoft-Authentifizierungsbibliothek gehandhabt.

## <a name="nuget-packages"></a>NuGet-Pakete

In dieser Anleitung wird das folgende NuGet-Paket verwendet:

|Bibliothek|BESCHREIBUNG|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|
|[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)|Microsoft Graph-Clientbibliothek|

## <a name="set-up-your-project"></a>Einrichten des Projekts

Dieser Abschnitt enthält detaillierte Anweisungen zum Integrieren einer Windows Desktop .NET-Anwendung (XAML) in „Mit Microsoft anmelden“. Von der Anwendung können dann Web-APIs abgefragt werden, für die ein Token erforderlich ist (beispielsweise die Microsoft Graph-API).

In diesem Leitfaden wird eine Anwendung mit einer Schaltfläche zum Abfragen der Microsoft Graph-API sowie mit einer Schaltfläche zum Abmelden erstellt. Darüber hinaus werden Textfelder mit den Ergebnissen der Aufrufe angezeigt.

> [!NOTE]
> Möchten Sie lieber das Visual Studio-Projekt dieses Beispiels herunterladen, anstatt die Erstellung durchzuführen? [Laden Sie ein Projekt herunter](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip), und fahren Sie direkt mit dem Schritt [Anwendungsregistrierung](#register-your-application "Anwendungsregistrierungsschritt") fort, um das Codebeispiel vor der Ausführung zu konfigurieren.

### <a name="create-your-application"></a>Erstellen der Anwendung

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.
1. Wählen Sie unter **Neues Projekt erstellen** die Option **Leere App (Universelles Windows)** für C# und dann **Weiter** aus.
1. Geben Sie der App unter **Neues Projekt konfigurieren** einen Namen, und wählen Sie **Erstellen** aus.
1. Wählen Sie unter **Neues UWP-Projekt (Universelle Windows-Plattform)** eine beliebige Version für **Ziel** und **Minimum** und anschließend **OK** aus.

   ![Mindestversion und Zielversion](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Hinzufügen der MSAL (Microsoft Authentication Library) zu Ihrem Projekt

1. Klicken Sie in Visual Studio auf **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.
1. Kopieren Sie die folgenden Befehle, und fügen Sie sie in das Fenster **Paket-Manager-Konsole** ein:

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > Mit dem ersten Befehl wird die [Microsoft-Authentifizierungsbibliothek (MSAL.NET)](https://aka.ms/msal-net) installiert. Von MSAL.NET werden Benutzertoken bezogen, zwischengespeichert und aktualisiert, die für den Zugriff auf durch Microsoft Identity Platform geschützte APIs benötigt werden. Mit dem zweiten Befehl wird die [.NET-Clientbibliothek für Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-dotnet) installiert, um an Microsoft Graph gerichtete Anforderungen zu authentifizieren und Dienstaufrufe durchzuführen.

### <a name="create-your-applications-ui"></a>Erstellen der Benutzeroberfläche Ihrer Anwendung

Visual Studio erstellt als Teil Ihrer Projektvorlage die Datei *MainPage.xaml*. Öffnen Sie diese Datei, und ersetzen Sie den Knoten **Grid** Ihrer Anwendung durch den folgenden Code:

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

### <a name="use-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Abrufen eines Tokens für die Microsoft Graph-API mithilfe der Microsoft-Authentifizierungsbibliothek

In diesem Abschnitt erfahren Sie, wie Sie mithilfe der Microsoft-Authentifizierungsbibliothek ein Token für die Microsoft Graph-API abrufen. Nehmen Sie Änderungen an der Datei *MainPage.xaml.cs* vor.

1. Fügen Sie in *MainPage.xaml.cs* die folgenden Verweise hinzu:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Ersetzen Sie die `MainPage`-Klasse durch den folgenden Code:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

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
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens<a name="more-information"></a>

Die `AcquireTokenInteractive`-Methode führt zum Öffnen eines Fensters, in dem Benutzer zum Anmelden aufgefordert werden. Bei Anwendungen müssen sich Benutzer in der Regel interaktiv anmelden, wenn sie zum ersten Mal auf eine geschützte Ressource zugreifen. Die Anmeldung kann auch erforderlich sein, wenn beim automatischen Vorgang zum Beziehen eines Tokens ein Fehler auftritt (beispielsweise, wenn das Kennwort eines Benutzers abgelaufen ist).

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Die `AcquireTokenSilent`-Methode dient zum Verwalten des Abrufens und Erneuerns von Token ohne Benutzereingriff. Nachdem `AcquireTokenInteractive` zum ersten Mal ausgeführt und der Benutzer zum Eingeben seiner Anmeldeinformationen aufgefordert wurde, verwenden Sie die `AcquireTokenSilent`-Methode, um Token für spätere Aufrufe anzufordern. Mit dieser Methode werden Token im Hintergrund abgerufen. Das Zwischenspeichern und Verlängern von Token wird von der Microsoft-Authentifizierungsbibliothek gehandhabt.

Für die `AcquireTokenSilent`-Methode tritt schließlich ein Fehler auf. Gründe für Fehler sind beispielsweise ein Benutzer, der sich abgemeldet oder sein Kennwort auf einem anderen Gerät geändert hat. Wenn von der Microsoft-Authentifizierungsbibliothek erkannt wird, dass für das Problem eine interaktive Aktion erforderlich ist, wird eine Ausnahme vom Typ `MsalUiRequiredException` ausgelöst. Ihre Anwendung kann diese Ausnahme auf zwei Arten handhaben:

* Ihre Anwendung ruft sofort `AcquireTokenInteractive` auf. Dieser Aufruf führt dazu, dass der Benutzer zum Anmelden aufgefordert wird. Dieser Ansatz wird normalerweise für Onlineanwendungen verwendet, in denen keine Offlineinhalte für den Benutzer verfügbar sind. Die in diesem Leitfaden generierte Beispielanwendung basiert auf diesem Muster. Sie können dies bei der ersten Ausführung des Beispiels in Aktion sehen.

   Da bisher noch kein Benutzer die Anwendung genutzt hat, enthält `accounts.FirstOrDefault()` einen NULL-Wert, und die Ausnahme `MsalUiRequiredException` wird ausgelöst.

   Der Code im Beispiel behandelt die Ausnahme dann durch das Aufrufen von `AcquireTokenInteractive`. Dieser Aufruf führt dazu, dass der Benutzer zum Anmelden aufgefordert wird.

* In Ihrer Anwendung erhalten Benutzer einen visuellen Hinweis, dass sie sich anmelden müssen, damit diese den richtigen Zeitpunkt für die Anmeldung auswählen können. Die Anwendung kann später noch einmal versuchen, `AcquireTokenSilent` auszuführen. Wählen Sie diesen Ansatz, wenn Benutzer andere Anwendungsfunktionen ohne Störungen nutzen können, z.B. wenn Offlineinhalte in der Anwendung verfügbar sind. In diesem Fall können Benutzer entscheiden, wann sie sich anmelden möchten. Die Anwendung kann versuchen, `AcquireTokenSilent` zu wiederholen, nachdem das Netzwerk vorübergehend nicht verfügbar war.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>Instanziieren des Microsoft Graph-Dienstclients durch Abrufen des Tokens aus der Methode „SignInUserAndGetTokenUsingMSAL“

Fügen Sie der Datei *MainPage.xaml.cs* die folgende neue Methode hinzu:

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Weitere Informationen zum Richten eines REST-Aufrufs an eine geschützte API

In dieser Beispielanwendung wird `GraphServiceClient` durch die Methode `GetGraphServiceClient` unter Verwendung eines Zugriffstokens instanziiert. Anschließend wird `GraphServiceClient` verwendet, um die Profilinformationen des Benutzers vom Endpunkt **me** abzurufen.

### <a name="add-a-method-to-sign-out-the-user"></a>Hinzufügen einer Methode zum Abmelden des Benutzers

Fügen Sie folgende Methode zur Datei *MainPage.xaml.cs* hinzu, um den Benutzer abzumelden:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

> [!NOTE]
> Bei MSAL.NET werden asynchrone Methoden zum Abrufen von Token oder Bearbeiten von Konten verwendet. Sie müssen dafür sorgen, dass UI-Aktionen im UI-Thread unterstützt werden. Dies ist der Grund für den `Dispatcher.RunAsync`-Aufruf und die Vorsichtsmaßnahmen zum Aufrufen von `ConfigureAwait(false)`.

#### <a name="more-information-about-signing-out"></a>Weitere Informationen zum Abmelden<a name="more-information-on-sign-out"></a>

Mit der Methode `SignOutButton_Click` wird der Benutzer aus dem Benutzercache der Microsoft-Authentifizierungsbibliothek entfernt. Durch diese Methode wird die Microsoft-Authentifizierungsbibliothek im Grunde angewiesen, den aktuellen Benutzer zu vergessen, und eine zukünftige Anforderung zum Abrufen eines Tokens ist nur erfolgreich, wenn diese interaktiv ist.

Obwohl die Anwendung in diesem Beispiel einen einzelnen Benutzer unterstützt, Von der Microsoft-Authentifizierungsbibliothek werden Szenarien unterstützt, in denen sich der Benutzer bei mehreren Konten anmelden kann. Ein Beispiel ist eine E-Mail-Anwendung, in der ein Benutzer mehrere Konten hat.

### <a name="display-basic-token-information"></a>Anzeigen grundlegender Informationen zum Token

Fügen Sie *MainPage.xaml.cs* die folgende Methode hinzu, um grundlegende Informationen zum Token anzuzeigen:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
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

#### <a name="more-information"></a>Weitere Informationen<a name="more-information-1"></a>

Mit **OpenID Connect** abgerufene ID-Token enthalten auch eine kleine Teilmenge von Informationen zum Benutzer. `DisplayBasicTokenInfo` zeigt grundlegende Informationen im Token: Zu diesen Informationen gehören der Anzeigename und die ID des Benutzers. Darüber hinaus sind auch das Ablaufdatum des Tokens und die eigentliche Zeichenfolge, die das Zugriffstoken darstellt, angegeben. Wenn Sie mehrmals auf die Schaltfläche **Microsoft Graph-API aufrufen** klicken, sehen Sie, dass dasselbe Token für nachfolgende Anforderungen wiederverwendet wurde. Darüber hinaus können Sie sehen, dass das Ablaufdatum verlängert wurde, wenn von der Microsoft-Authentifizierungsbibliothek entschieden wird, dass das Token verlängert werden muss.

### <a name="display-message"></a>Meldung anzeigen

Fügen Sie der Datei *MainPage.xaml.cs* die folgende neue Methode hinzu:

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Anwendung registrieren

Nun müssen Sie Ihre Anwendung registrieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie auf **Azure Active Directory** > **App-Registrierungen**.
1. Wählen Sie **Neue Registrierung** aus. Geben Sie einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise *UWP-App-calling-MSGraph*).
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus. Wählen Sie anschließend **Registrieren** aus, um den Vorgang fortzusetzen.
1. Suchen Sie auf der Übersichtsseite nach dem Wert **Anwendungs-ID (Client)** , und kopieren Sie ihn. Wechseln Sie zurück zu Visual Studio, öffnen Sie die Datei *MainPage.xaml.cs*, und ersetzen Sie den Wert von `ClientId` durch diesen Wert.

Konfigurieren der Authentifizierung für Ihre Anwendung:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) unter **Verwalten** die Option **Authentifizierung** aus.
1. Aktivieren Sie im Abschnitt **Umleitungs-URIs** | **Vorgeschlagene Umleitungs-URIs für öffentliche Clients (Mobilgerät, Desktop)** die Option https://login.microsoftonline.com/common/oauth2/nativeclient.
1. Wählen Sie **Speichern** aus.

Konfigurieren Sie die API-Berechtigungen für Ihre Anwendung:

1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**.
1. Wählen Sie **Berechtigung hinzufügen** aus, und vergewissern Sie sich, dass die Option **Microsoft-APIs** ausgewählt ist.
1. Wählen Sie **Microsoft Graph**.
1. Wählen Sie **Delegierte Berechtigungen** aus, suchen Sie nach *User.Read*, und vergewissern Sie sich, dass **User.Read** ausgewählt ist.
1. Wählen Sie nach dem Vornehmen aller Änderungen die Option **Berechtigungen hinzufügen** aus, um sie zu speichern.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Aktivieren einer integrierten Authentifizierung für Verbunddomänen (optional)

Über das Anwendungsmanifest müssen zusätzliche Funktionen aktiviert werden, um eine integrierte Windows-Authentifizierung bei der Verwendung mit einer Azure AD-Verbunddomäne zu ermöglichen. Wechseln Sie in Visual Studio zurück zu Ihrer Anwendung.

1. Öffnen Sie *Package.appxmanifest*.
1. Wählen Sie **Funktionen** aus, und aktivieren Sie die folgenden Einstellungen:

   * **Unternehmensauthentifizierung**
   * **Private Netzwerke (Client und Server)**
   * **Freigegebene Benutzerzertifikate**

> [!IMPORTANT]
> [Integrierte Windows-Authentifizierung](https://aka.ms/msal-net-iwa) ist für dieses Beispiel standardmäßig nicht konfiguriert. Für Anwendungen, die die Funktionen `Enterprise Authentication` oder `Shared User Certificates` anfordern, ist im Windows Store eine höhere Überprüfungsebene erforderlich. und nicht alle Entwickler die Überprüfung auf der höheren Ebene durchführen möchten. Aktivieren Sie diese Einstellung nur, wenn Sie die integrierte Windows-Authentifizierung mit einer Azure AD-Verbunddomäne verwenden möchten.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>Alternativer Ansatz für die Verwendung von „WithDefaultRedirectURI()“

Im aktuellen Beispiel wird die Methode `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` verwendet. Wenn Sie `WithDefaultRedirectURI()` verwenden möchten, führen Sie die folgenden Schritte aus:

1. Ersetzen Sie `WithRedirectUri` in *MainPage.XAML.cs* durch `WithDefaultRedirectUri`:

   **Aktueller Code**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Aktualisierter Code**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  Ermitteln Sie den Rückruf-URI für Ihre App, indem Sie in *MainPage.xaml.cs* das Feld `redirectURI` hinzufügen und einen Breakpoint dafür festlegen:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Führen Sie die App aus, und kopieren Sie bei Erreichen des Breakpoints den Wert von `redirectUri`. Der Wert sollte in etwa wie folgt aussehen: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    Anschließend können Sie die Codezeile entfernen, da sie nur einmal benötigt wird, um den Wert abzurufen.

3. Fügen Sie den zurückgegebenen Wert im App-Registrierungsportal im Bereich **Authentifizierung** unter **RedirectUri** hinzu.

## <a name="test-your-code"></a>Testen Ihres Codes

Drücken Sie zum Testen Ihrer Anwendung **F5**, um das Projekt in Visual Studio auszuführen. Das Hauptfenster wird angezeigt:

![Benutzeroberfläche der Anwendung](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Wenn Sie Tests durchführen möchten, wählen Sie **Microsoft Graph-API aufrufen** aus. Verwenden Sie dann zum Anmelden ein Azure AD-Konto (Organisationskonto) oder ein Microsoft-Konto (z. B. live.com oder outlook.com). Wenn ein Benutzer diesen Test zum ersten Mal ausführt, wird in der Anwendung ein Fenster mit einer Anmeldeaufforderung angezeigt.

### <a name="consent"></a>Zustimmung

Wenn Sie sich zum ersten Mal bei Ihrer Anwendung anmelden, wird ein Einwilligungsbildschirm angezeigt, der in etwa wie in der folgenden Abbildung aussieht. Wählen Sie **Ja** aus, um zum Zugriff explizit Ihre Zustimmung zu geben:

![Bildschirm für die Zugriffsgenehmigung](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Erwartete Ergebnisse

Es werden Benutzerprofilinformationen angezeigt, die vom Microsoft Graph-API-Aufruf auf dem Bildschirm **API-Aufrufergebnisse** zurückgegeben werden:

![Bildschirm „API-Aufrufergebnisse“](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Grundlegende Informationen zum Token, das über `AcquireTokenInteractive` oder `AcquireTokenSilent` abgerufen wurde, werden im Feld **Tokeninformationen** angezeigt:

|Eigenschaft  |Format  |BESCHREIBUNG |
|---------|---------|---------|
|`Username` |`user@domain.com` |Der zur Identifizierung des Benutzers verwendete Benutzername|
|`Token Expires` |`DateTime` |Die Uhrzeit, zu der das Token abläuft. Das Token wird bei Bedarf von der Microsoft-Authentifizierungsbibliothek verlängert, um die Ablauffrist zu verlängern.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Weitere Informationen zu Bereichen und delegierten Berechtigungen

Die Microsoft Graph-API benötigt den Bereich `user.read`, um ein Benutzerprofil zu lesen. Dieser Bereich wird standardmäßig jeder Anwendung hinzugefügt, die im Anwendungsregistrierungsportal registriert wird. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Für die Microsoft Graph-API ist beispielsweise der Bereich `Calendars.Read` erforderlich, um die Kalender des Benutzers auflisten zu können.

Fügen Sie den Informationen für die Anwendungsregistrierung die delegierte Berechtigung `Calendars.Read` hinzu, um auf die Kalender des Benutzers im Kontext einer Anwendung zugreifen zu können. Fügen Sie dem Aufruf `acquireTokenSilent` anschließend den Bereich `Calendars.Read` hinzu.

> [!NOTE]
> Wenn Sie die Anzahl der Bereiche erhöhen, werden Benutzer ggf. zu weiteren Genehmigungen aufgefordert.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="issue-1"></a>Problem 1:

Wenn Sie sich in einer Azure AD-Verbunddomäne bei Ihrer Anwendung anmelden, tritt einer der folgenden Fehler auf:

* „No valid client certificate found in the request.“ (In der Anforderung wurde kein gültiges Clientzertifikat gefunden.)
* „No valid certificates found in the user's certificate store.“ (Im Zertifikatspeicher des Benutzers wurden keine gültigen Zertifikate gefunden.)
* „Try again choosing a different authentication method.“ (Versuchen Sie erneut, und wählen Sie eine andere Authentifizierungsmethode aus.)

**Ursache:** Unternehmens- und Zertifikatsfunktionen sind nicht aktiviert.

**Lösung:** Führen Sie die Schritte unter [Aktivieren einer integrierten Authentifizierung für Verbunddomänen (optional)](#enable-integrated-authentication-on-federated-domains-optional) aus.

### <a name="issue-2"></a>Problem 2:

Nach der Aktivierung von [Integrierte Authentifizierung für Verbunddomänen](#enable-integrated-authentication-on-federated-domains-optional) und dem Versuch, sich auf einem Windows 10-Computer über Windows Hello bei einer Umgebung mit konfigurierter mehrstufiger Authentifizierung anzumelden, wird die Liste mit den Zertifikaten angezeigt. Wenn Sie Ihre PIN verwenden möchten, wird das Fenster „PIN“ nie angezeigt.

**Ursache:** Bekannte Einschränkung des Webauthentifizierungsbrokers in UWP-Anwendungen, die auf Windows 10-Desktops ausgeführt werden (funktioniert unter Windows 10 Mobile).

**Problemumgehung:** Wählen Sie **Mit anderen Optionen anmelden** aus. Wählen Sie dann **Mit Benutzernamen und Kennwort anmelden** und **Kennwort angeben** aus, und durchlaufen Sie anschließend die Telefonauthentifizierung.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Verwendung von Microsoft Authentication Library (MSAL) für die Autorisierung und Authentifizierung in .NET-Anwendungen:

> [!div class="nextstepaction"]
> [Übersicht über Microsoft Authentication Library (MSAL)](msal-overview.md)
