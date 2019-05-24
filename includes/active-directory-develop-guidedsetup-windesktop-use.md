---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3f72f6a5097221c904faff633b5a4ee5a6e023c1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121641"
---
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
    ```

<!--start-collapse-->
### <a name="more-information"></a>Weitere Informationen

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

Das Aufrufen der `AcquireTokenInteractive`-Methode führt zu einem Fenster, in dem Benutzer zum Anmelden aufgefordert werden. Bei Anwendungen müssen sich Benutzer interaktiv anmelden, wenn sie zum ersten Mal auf eine geschützte Ressource zugreifen müssen. Die Anmeldung kann auch erforderlich sein, wenn bei dem automatischen Vorgang zum Beziehen eines Tokens ein Fehler auftritt (beispielsweise bei einem abgelaufenen Kennwort des Benutzers).

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Die `AcquireTokenSilent`-Methode dient zum Verwalten des Abrufens und Erneuerns von Token ohne Benutzereingriff. Nachdem `AcquireTokenInteractive` zum ersten Mal ausgeführt wurde, ist `AcquireTokenSilent` die übliche Methode zum Abrufen von Token, die für den Zugriff auf geschützte Ressourcen bei nachfolgenden Aufrufen verwendet werden. Der Grund hierfür ist, dass Aufrufe zum Anfordern oder Verlängern von Token automatisch erfolgen.

Für die `AcquireTokenSilent`-Methode tritt schließlich ein Fehler auf. Gründe für den Fehler können sein, dass der Benutzer sich entweder abgemeldet oder sein Kennwort auf einem anderen Gerät geändert hat. Wenn die MSAL feststellt, dass das Problem durch Anfordern einer interaktiven Aktion gelöst werden kann, löst sie eine `MsalUiRequiredException`-Ausnahme aus. Ihre Anwendung kann diese Ausnahme auf zwei Arten handhaben:

* Sie kann sofort `AcquireTokenInteractive` aufrufen. Dieser Aufruf führt dazu, dass der Benutzer zum Anmelden aufgefordert wird. Dieses Muster wird in der Regel in Onlineanwendungen verwendet, in denen keine Offlineinhalte für den Benutzer verfügbar sind. Das Beispiel, das in diesem Setup mit Anleitung generiert wird, basiert auf diesem Muster. Sie können dies bei der ersten Ausführung des Beispiels in Aktion sehen. 

* Da bisher noch kein Benutzer die Anwendung genutzt hat, enthält `PublicClientApp.Users.FirstOrDefault()` einen NULL-Wert, und es wird die Ausnahme `MsalUiRequiredException` ausgelöst. 

* Der Code im Beispiel behandelt die Ausnahme dann durch das Aufrufen von `AcquireTokenInteractive`. Dies führt dazu, dass der Benutzer zum Anmelden aufgefordert wird.

* Stattdessen kann für Benutzer auch ein visueller Hinweis gegeben werden, dass eine interaktive Anmeldung erforderlich ist, damit diese den richtigen Zeitpunkt für die Anmeldung auswählen können. Oder die Anwendung kann später noch einmal versuchen, `AcquireTokenSilent` auszuführen. Dieses Muster wird häufig verwendet, wenn Benutzer andere Anwendungsfunktionen ohne Störungen nutzen können, z.B. wenn Offlineinhalte in der Anwendung verfügbar sind. In diesem Fall können Benutzer entscheiden, wann sie sich anmelden, um entweder auf die geschützte Ressource zuzugreifen oder die veralteten Informationen zu aktualisieren. Alternativ dazu kann die Anwendung auch versuchen, `AcquireTokenSilent` erneut auszuführen, wenn das Netzwerk nach einem kurzzeitigen Ausfall wiederhergestellt wurde.
<!--end-collapse-->

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

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Weitere Informationen zum Richten eines REST-Aufrufs an eine geschützte API

In dieser Beispielanwendung verwenden Sie die `GetHttpContentWithToken`-Methode zum Senden einer HTTP `GET`-Anforderung an eine geschützte Ressource, für die ein Token erforderlich ist, und zum anschließenden Zurückgeben des Inhalts an den Aufrufer. Diese Methode fügt das abgerufene Token in den HTTP-Autorisierungsheader ein. In diesem Beispiel ist die Ressource der Endpunkt *me* der Microsoft Graph-API, der die Profilinformationen des Benutzers anzeigt.
<!--end-collapse-->

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

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Weitere Informationen zur Abmeldung von Benutzern

Die `SignOutButton_Click`-Methode entfernt Benutzer aus dem MSAL-Benutzercache. Hierdurch wird die MSAL praktisch angewiesen, den aktuellen Benutzer zu vergessen, sodass eine künftige Anforderung zum Abrufen eines Tokens nur erfolgreich ist, wenn diese interaktiv gestaltet wird.

Die Anwendung in diesem Beispiel unterstützt zwar einzelne Benutzer, aber die MSAL verfügt über Unterstützung für Szenarien, bei denen mehrere Konten gleichzeitig angemeldet werden können. Ein Beispiel hierfür ist eine E-Mail-Anwendung, in der ein Benutzer mehrere Konten besitzt.
<!--end-collapse-->

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

<!--start-collapse-->
### <a name="more-information"></a>Weitere Informationen

Zusätzlich zu dem Zugriffstoken, das nach der Anmeldung des Benutzers zum Aufrufen der Microsoft Graph-API verwendet wird, wird mit der MSAL auch ein ID-Token bezogen. Dieses Token enthält eine kleine Teilmenge von Informationen zu den Benutzern. Die `DisplayBasicTokenInfo`-Methode zeigt die grundlegenden Informationen an, die im Token enthalten sind. Dies sind beispielsweise der Anzeigename und die ID des Benutzers sowie das Ablaufdatum des Tokens und die Zeichenfolge, die das Zugriffstoken selbst darstellt. Wenn Sie mehrmals auf die Schaltfläche *Microsoft Graph-API* klicken, sehen Sie, dass dasselbe Token für nachfolgende Anforderungen wiederverwendet wurde. Sie können auch feststellen, dass das Ablaufdatum verlängert wurde, wenn die MSAL entscheidet, dass es Zeit ist, das Token zu verlängern.
<!--end-collapse-->
