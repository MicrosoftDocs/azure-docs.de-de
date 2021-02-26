---
title: 'Tutorial: Erstellen einer Windows Presentation Foundation (WPF)-App, die Microsoft Identity Platform für die Authentifizierung verwendet | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine WPF-Anwendung, die Microsoft Identity Platform zum Anmelden von Benutzern und zum Abrufen eines Zugriffstokens verwendet, um im Namen der Benutzer die Microsoft Graph-API aufzurufen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d9db845bfa4a7c0c117220b8932b370eb230f6dc
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102987"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Tutorial: Aufrufen der Microsoft Graph-API aus einer Windows Desktop-App

In diesem Tutorial erstellen Sie eine native Windows Desktop .NET-App (XAML-App), die Benutzer anmeldet und ein Zugriffstoken abruft, um die Microsoft Graph-API aufzurufen. 

Am Ende dieses Leitfadens kann Ihre Anwendung eine geschützte API aufrufen, die persönliche Konten (outlook.com, live.com und andere) verwendet. Die Anwendung kann auch Geschäfts-, Schul- und Unikonten aus Unternehmen oder Organisationen nutzen, die Azure Active Directory verwenden.

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Erstellen eines *Windows Presentation Foundation (WPF)* -Projekts in Visual Studio
> * Installieren der Microsoft Authentication Library (MSAL) für .NET
> * Registrieren der Anwendung im Azure-Portal
> * Hinzufügen von Code zur Unterstützung der Benutzeranmeldung und -abmeldung
> * Hinzufügen von Code zum Aufrufen der Microsoft Graph-API
> * Testen der App

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funktionsweise der über diesen Leitfaden generierten Beispiel-App

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Die in diesem Leitfaden erstellte Beispielanwendung ermöglicht einer Windows Desktop-Anwendung das Abfragen der Microsoft Graph-API oder einer Web-API, die Token von einem Microsoft Identity Platform-Endpunkt akzeptiert. In diesem Szenario fügen Sie HTTP-Anforderungen ein Token über den Autorisierungsheader hinzu. Tokenabruf und -erneuerung werden von der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) gehandhabt.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Verarbeiten des Beziehens von Token für den Zugriff auf geschützte Web-APIs

Nach der Authentifizierung des Benutzers empfängt die Beispielanwendung ein Token, mit dem eine per Microsoft Identity Platform geschützte Microsoft Graph-API oder Web-API abgefragt werden kann.

APIs wie Microsoft Graph erfordern ein Token, um den Zugriff auf bestimmte Ressourcen zu ermöglichen. Ein Token ist beispielsweise erforderlich, um das Profil eines Benutzers zu lesen, auf den Kalender eines Benutzers zuzugreifen oder eine E-Mail zu senden. Ihre Anwendung kann unter Verwendung von MSAL ein Zugriffstoken anfordern, um auf diese Ressourcen durch Angeben von API-Bereichen zuzugreifen. Dieses Zugriffstoken wird dann dem HTTP-Autorisierungsheader für jeden Aufruf hinzugefügt, der für die geschützte Ressource erfolgt.

MSAL nimmt Ihrer Anwendung die Verwaltung der Zwischenspeicherung und Aktualisierung von Zugriffstoken ab.

## <a name="nuget-packages"></a>NuGet-Pakete

In dieser Anleitung werden die folgenden NuGet-Pakete verwendet:

|Bibliothek|Beschreibung|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>Einrichten des Projekts

In diesem Abschnitt erstellen Sie ein neues Projekt, um zu veranschaulichen, wie Sie eine Windows Desktop .NET-Anwendung (XAML) mit *Mit Microsoft anmelden* so integrieren können, dass Sie Web-APIs abfragen kann, die ein Token erfordern.

Die Anwendung, die Sie anhand dieser Anleitung erstellen, zeigt eine Schaltfläche zum Aufrufen eines Diagramms, einen Ergebnisbereich und eine Abmeldeschaltfläche an.

> [!NOTE]
> Möchten Sie stattdessen das Visual Studio-Projekt dieses Beispiels herunterladen? [Laden Sie ein Projekt herunter](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip), und fahren Sie mit dem [Konfigurationsschritt](#register-your-application) fort, um das Codebeispiel vor der Ausführung zu konfigurieren.
>

Gehen Sie zum Erstellen der Anwendung wie folgt vor:

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.
2. Wählen Sie unter **Vorlagen** die Option **Visual C#** aus.
3. Wählen Sie abhängig von der verwendeten Visual Studio-Version **WPF-App (.NET Framework)** aus.

## <a name="add-msal-to-your-project"></a>Hinzufügen von MSAL zu Ihrem Projekt

1. Klicken Sie in Visual Studio auf **Tools** > **NuGet-Paket-Manager**> **Paket-Manager-Konsole**.
2. Fügen Sie im Fenster „Paket-Manager-Konsole“ den folgenden Azure PowerShell-Befehl ein:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > Dieser Befehl installiert die Microsoft-Authentifizierungsbibliothek. MSAL übernimmt die Erfassung, Zwischenspeicherung und Aktualisierung von Benutzertoken für den Zugriff auf die durch Azure Active Directory v2.0 geschützten APIs.
    >

## <a name="register-your-application"></a>Anwendung registrieren

Sie können Ihre Anwendung auf zwei Arten registrieren.

### <a name="option-1-express-mode"></a>Option 1: Expressmodi

Gehen Sie zur schnellen Registrierung Ihrer Anwendung wie folgt vor:
1. Navigieren Sie zur Umgebung des Schnellstarts <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure-Portal – App-Registrierungen</a>.
1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.

### <a name="option-2-advanced-mode"></a>Option 2: Erweiterter Modus

Wenn Sie Ihre Anwendung registrieren und die Anwendungsregistrierungsinformationen Ihrer Projektmappe hinzufügen möchten, führen Sie folgende Schritte aus:
1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `Win-App-calling-MsGraph`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis, mehrere Mandanten) und persönliche Microsoft-Konten (z. B. Skype, Xbox)** aus.
1. Wählen Sie **Registrieren**.
1. Wählen Sie unter **Verwalten** die Optionen **Authentifizierung** > **Plattform hinzufügen** aus.
1. Wählen Sie **Mobilgerät- und Desktopanwendungen** aus.
1. Wählen Sie **https://login.microsoftonline.com/common/oauth2/nativeclient** im Abschnitt **Umleitungs-URIs** aus.
1. Wählen Sie **Konfigurieren** aus.
1. Öffnen Sie in Visual Studio die Datei *App.xaml.cs*, und ersetzen Sie im folgenden Codeausschnitt `Enter_the_Application_Id_here` durch die Anwendungs-ID, die Sie soeben registriert und kopiert haben.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Hinzufügen des Codes zum Initialisieren der MSAL

In diesem Schritt erstellen Sie eine Klasse zur Handhabung der Interaktion mit MSAL (beispielsweise die Handhabung von Token).

1. Öffnen Sie die Datei *App.xaml.cs*, und fügen Sie der Klasse den Verweis für MSAL hinzu:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Aktualisieren Sie die App-Klasse wie folgt:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Erstellen der Anwendungsbenutzeroberfläche

In diesem Abschnitt erfahren Sie, wie eine Anwendung einen geschützten Back-End-Server wie Microsoft Graph abfragen kann.

Die Datei *MainWindow.xaml* sollte als Teil Ihrer Projektvorlage automatisch erstellt werden. Öffnen Sie diese Datei, und ersetzen Sie den Knoten *\<Grid>* Ihrer Anwendung durch den folgenden Code:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Verwenden der MSAL zum Abrufen eines Tokens für die Microsoft Graph-API

In diesem Abschnitt nutzen Sie die MSAL, um ein Token für die Microsoft Graph-API abzurufen.

1. Fügen Sie in der Datei *MainWindow.xaml.cs* der Klasse den Verweis auf die MSAL hinzu:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Ersetzen Sie den `MainWindow`-Klassencode durch Folgendes:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
        }
    ```

### <a name="more-information"></a>Weitere Informationen

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

Das Aufrufen der `AcquireTokenInteractive`-Methode führt zu einem Fenster, in dem Benutzer zum Anmelden aufgefordert werden. Bei Anwendungen müssen sich Benutzer in der Regel interaktiv anmelden, wenn sie zum ersten Mal auf eine geschützte Ressource zugreifen müssen. Die Anmeldung kann auch erforderlich sein, wenn bei dem automatischen Vorgang zum Beziehen eines Tokens ein Fehler auftritt (beispielsweise bei einem abgelaufenen Kennwort des Benutzers).

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Die `AcquireTokenSilent`-Methode dient zum Verwalten des Abrufens und Erneuerns von Token ohne Benutzereingriff. Nachdem `AcquireTokenInteractive` zum ersten Mal ausgeführt wurde, ist `AcquireTokenSilent` die übliche Methode zum Abrufen von Token, die für den Zugriff auf geschützte Ressourcen bei nachfolgenden Aufrufen verwendet werden. Der Grund hierfür ist, dass Aufrufe zum Anfordern oder Verlängern von Token automatisch erfolgen.

Für die `AcquireTokenSilent`-Methode tritt schließlich ein Fehler auf. Gründe für den Fehler können sein, dass der Benutzer sich entweder abgemeldet oder sein Kennwort auf einem anderen Gerät geändert hat. Wenn die MSAL feststellt, dass das Problem durch Anfordern einer interaktiven Aktion gelöst werden kann, löst sie eine `MsalUiRequiredException`-Ausnahme aus. Ihre Anwendung kann diese Ausnahme auf zwei Arten handhaben:

* Sie kann sofort `AcquireTokenInteractive` aufrufen. Dieser Aufruf führt dazu, dass der Benutzer zum Anmelden aufgefordert wird. Dieses Muster wird in der Regel in Onlineanwendungen verwendet, in denen keine Offlineinhalte für den Benutzer verfügbar sind. Das Beispiel, das in diesem Setup mit Anleitung generiert wird, basiert auf diesem Muster. Sie können dies bei der ersten Ausführung des Beispiels in Aktion sehen.

* Da bisher noch kein Benutzer die Anwendung genutzt hat, enthält `PublicClientApp.Users.FirstOrDefault()` einen NULL-Wert, und es wird die Ausnahme `MsalUiRequiredException` ausgelöst.

* Der Code im Beispiel behandelt die Ausnahme dann durch das Aufrufen von `AcquireTokenInteractive`. Dies führt dazu, dass der Benutzer zum Anmelden aufgefordert wird.

* Stattdessen kann für Benutzer auch ein visueller Hinweis gegeben werden, dass eine interaktive Anmeldung erforderlich ist, damit diese den richtigen Zeitpunkt für die Anmeldung auswählen können. Oder die Anwendung kann später noch einmal versuchen, `AcquireTokenSilent` auszuführen. Dieses Muster wird häufig verwendet, wenn Benutzer andere Anwendungsfunktionen ohne Störungen nutzen können, z.B. wenn Offlineinhalte in der Anwendung verfügbar sind. In diesem Fall können Benutzer entscheiden, wann sie sich anmelden, um entweder auf die geschützte Ressource zuzugreifen oder die veralteten Informationen zu aktualisieren. Alternativ dazu kann die Anwendung auch versuchen, `AcquireTokenSilent` erneut auszuführen, wenn das Netzwerk nach einem kurzzeitigen Ausfall wiederhergestellt wurde.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Aufrufen der Microsoft Graph-API mit dem zuvor bezogenen Token

Fügen Sie `MainWindow.xaml.cs` die folgende neue Methode hinzu. Die Methode dient zum Senden einer `GET`-Anforderung an die Graph-API mithilfe eines „Authorize“-Headers:

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
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Weitere Informationen zum Richten eines REST-Aufrufs an eine geschützte API

In dieser Beispielanwendung verwenden Sie die `GetHttpContentWithToken`-Methode zum Senden einer HTTP `GET`-Anforderung an eine geschützte Ressource, für die ein Token erforderlich ist, und zum anschließenden Zurückgeben des Inhalts an den Aufrufer. Diese Methode fügt das abgerufene Token in den HTTP-Autorisierungsheader ein. In diesem Beispiel ist die Ressource der Endpunkt *me* der Microsoft Graph-API, der die Profilinformationen des Benutzers anzeigt.

## <a name="add-a-method-to-sign-out-a-user"></a>Hinzufügen einer Methode zum Abmelden eines Benutzers

Fügen Sie Ihrer Datei `MainWindow.xaml.cs` die folgende Methode hinzu, um einen Benutzer abzumelden:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>Weitere Informationen zur Abmeldung von Benutzern

Die `SignOutButton_Click`-Methode entfernt Benutzer aus dem MSAL-Benutzercache. Hierdurch wird die MSAL praktisch angewiesen, den aktuellen Benutzer zu vergessen, sodass eine künftige Anforderung zum Abrufen eines Tokens nur erfolgreich ist, wenn diese interaktiv gestaltet wird.

Die Anwendung in diesem Beispiel unterstützt zwar einzelne Benutzer, aber die MSAL verfügt über Unterstützung für Szenarien, bei denen mehrere Konten gleichzeitig angemeldet werden können. Ein Beispiel hierfür ist eine E-Mail-Anwendung, in der ein Benutzer mehrere Konten besitzt.

## <a name="display-basic-token-information"></a>Anzeigen grundlegender Informationen zum Token

Fügen Sie Ihrer Datei *MainWindow.xaml.cs* die folgende Methode hinzu, um grundlegende Informationen zum Token anzuzeigen:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>Weitere Informationen

Zusätzlich zu dem Zugriffstoken, das nach der Anmeldung des Benutzers zum Aufrufen der Microsoft Graph-API verwendet wird, wird mit der MSAL auch ein ID-Token bezogen. Dieses Token enthält eine kleine Teilmenge von Informationen zu den Benutzern. Die `DisplayBasicTokenInfo`-Methode zeigt die grundlegenden Informationen an, die im Token enthalten sind. Dies sind beispielsweise der Anzeigename und die ID des Benutzers sowie das Ablaufdatum des Tokens und die Zeichenfolge, die das Zugriffstoken selbst darstellt. Wenn Sie mehrmals auf die Schaltfläche *Microsoft Graph-API* klicken, sehen Sie, dass dasselbe Token für nachfolgende Anforderungen wiederverwendet wurde. Sie können auch feststellen, dass das Ablaufdatum verlängert wurde, wenn die MSAL entscheidet, dass es Zeit ist, das Token zu verlängern.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie in unserer mehrteiligen Szenarioreihe mehr über das Entwickeln von Desktop-Apps, die geschützte Web-APIs abrufen:

> [!div class="nextstepaction"]
> [Szenario: Desktop-App, die Web-APIs aufruft](scenario-desktop-overview.md)
