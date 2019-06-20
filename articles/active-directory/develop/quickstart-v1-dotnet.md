---
title: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer .NET Desktop-App (WPF-App) | Microsoft Docs
description: Erfahren Sie, wie eine .NET Windows Desktop-Anwendung erstellt wird, die sich für die Anmeldung in Azure AD integriert und über OAuth 2.0 durch Azure AD geschützte APIs aufruft.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2d9639c21e201db1df5145caf1345d4f0879af6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121940"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Schnellstart: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer .NET Desktop-App (WPF-App)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Für native .NET-Clients, die auf geschützte Ressourcen zugreifen müssen, bietet Azure Active Directory (Azure AD) die Active Directory-Authentifizierungsbibliothek (ADAL). Durch ADAL ist es für Ihre App einfach, Zugriffstoken abzurufen. 

In diesem Schnellstart erfahren Sie, wie Sie eine .NET WPF-Aufgabenlistenanwendung erstellen, die die folgenden Aufgaben ausführt:

* Abrufen der Zugriffstoken zum Aufrufen der Azure AD Graph-API mit dem OAuth 2.0-Authentifizierungsprotokoll.
* Durchsuchen eines Verzeichnisses nach Benutzern mit einem bestimmten Aliasnamen
* Abmelden von Benutzern

Zur Entwicklung einer vollständigen, funktionstüchtigen Anwendung müssen Sie folgende Schritte ausführen:

1. Registrieren Ihrer Anwendung bei Azure AD
2. Installieren und Konfigurieren der ADAL
3. Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zum Einstieg die folgenden erforderlichen Schritte aus:

* Laden Sie das [Anwendungsgerüst](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) oder das [vollständige Beispiel](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip) herunter.
* Stellen Sie einen Azure AD-Mandanten zum Erstellen von Benutzern und zum Registrieren der Anwendung bereit. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Schritt 1: Registrieren der Anwendung DirectorySearcher

Damit Ihre Anwendung Token abrufen kann, müssen Sie sie zunächst bei Ihrem Azure AD-Mandanten registrieren und ihr die Berechtigung für den Zugriff auf die Azure AD Graph-API erteilen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf der oberen Leiste Ihr Konto aus, und wählen Sie in der Liste **Verzeichnis** den Active Directory-Mandanten aus, für den Sie Ihre Anwendungen registrieren möchten.
3. Wählen Sie im linken Navigationsbereich **Alle Dienste** aus, und wählen Sie **Azure Active Directory** aus.
4. Wählen Sie unter **App-Registrierungen** die Option **Registrierung einer neuen Anwendung** aus.
5. Folgen Sie den Eingabeaufforderungen zum Erstellen einer neuen Clientanwendung.
    * **Name** ist der Anwendungsname und beschreibt Ihre Anwendung für Endbenutzer.
    * Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
    * **Umleitungs-URI** ist eine Kombination aus einem Schema und einer Zeichenfolge, die Azure AD für die Rückgabe der Tokenantworten verwendet. Geben Sie einen Wert ein, der spezifisch für Ihre Anwendung ist (z. B. `http://DirectorySearcher`) und auf den vorherigen Umleitungs-URI-Informationen basiert. Wählen Sie außerdem in der Dropdownliste den Eintrag **Öffentlicher Client (Mobilgerät und Desktop)** aus. 

6. Nach Abschluss der Registrierung weist AAD Ihrer App eine eindeutige Anwendungs-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten. Daher sollten Sie ihn von der Anwendungsseite kopieren.
7. Wählen Sie auf der Seite **API-Berechtigungen** die Option **Berechtigung hinzufügen** aus. Wählen Sie in **Hiermit wählen Sie eine API aus** die Option ***Microsoft Graph*** aus.
8. Wählen Sie unter **Delegierte Berechtigungen** die Berechtigung **User.Read** aus, und klicken Sie dann auf **Hinzufügen**, um den Vorgang zu speichern. Mit dieser Berechtigung kann die Anwendung die Azure AD Graph-API nach Benutzern abfragen.

## <a name="step-2-install-and-configure-adal"></a>Schritt 2: Installieren und Konfigurieren der ADAL

Nachdem Sie nun eine Anwendung in Azure AD erstellt haben, können Sie ADAL installieren und Ihren identitätsbezogenen Code schreiben. Damit ADAL mit Azure AD kommunizieren kann, müssen Sie einige Informationen zur App-Registrierung bereitstellen.

1. Beginnen Sie, indem Sie ADAL dem `DirectorySearcher`-Projekt über die Paket-Manager-Konsole hinzufügen.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. Öffnen Sie im `DirectorySearcher`-Projekt die Datei `app.config`.
1. Ersetzen Sie die Werte der Elemente im Abschnitt `<appSettings>` durch die Werte, die Sie im Azure-Portal eingegeben haben. Sobald Ihr Code ADAL verwendet, verweist er auf diese Werte.
   * `ida:Tenant` ist die Domäne Ihres Azure AD-Mandanten, z.B. contoso.onmicrosoft.com.
   * `ida:ClientId` ist die Client-ID Ihrer Anwendung, die Sie aus dem Portal kopiert haben.
   * `ida:RedirectUri` ist die Umleitungs-URL, die Sie im Portal registriert haben.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Schritt 3: Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

Das Grundprinzip von ADAL ist wie folgt: Wann immer Ihre Anwendung ein Zugriffstoken benötigt, ruft Ihre App einfach `authContext.AcquireTokenAsync(...)` auf, und ADAL erledigt alles Weitere.

1. Öffnen Sie im `DirectorySearcher`-Projekt die Datei `MainWindow.xaml.cs`.
1. Suchen Sie nach der `MainWindow()`-Methode. 
1. Initialisieren Sie den `AuthenticationContext` Ihrer App (die primäre Klasse von ADAL). In `AuthenticationContext` übergeben Sie ADAL die zur Kommunikation mit Azure AD notwendigen Koordinaten und weisen das Verzeichnis an, wie Token zwischengespeichert werden sollen.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Suchen Sie nach der `Search(...)`-Methode, die aufgerufen wird, wenn der Benutzer auf die **Suchschaltfläche** in der Benutzeroberfläche der App klickt. Diese Methode übergibt der Azure AD Graph-API eine GET-Anforderung für die Suche nach Benutzern, deren UPNs mit dem angegebenen Suchbegriff beginnen.
1. Für die Abfrage der Graph-API müssen Sie dem `Authorization`-Header der Anforderung ein access_token hinzufügen. Hier kommt ADAL ins Spiel.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    Wenn Ihre Anwendung mit einem `AcquireTokenAsync(...)`-Aufruf ein Token anfordert, versucht ADAL ein Token zurückzugeben, ohne den Benutzer nach seinen Anmeldeinformationen zu fragen.
    * Stellt ADAL fest, dass sich der Benutzer zum Abrufen eines Tokens anmelden muss, zeigt es einen Anmeldedialog an, erfasst die Anmeldeinformationen des Benutzers und gibt nach erfolgreicher Authentifizierung ein Token zurück. 
    * Wenn ADAL aus welchem Grund auch immer kein Token zurückgeben kann, löst es eine `AdalException`aus.

1. Beachten Sie, dass das Objekt `AuthenticationResult` ein `UserInfo`-Objekt enthält, mit dem von Ihrer Anwendung benötigte Informationen erfasst werden können. In DirectorySearcher wird `UserInfo` zum Anpassen der Benutzeroberfläche der App durch die ID des Benutzers verwendet.
1. Wenn der Benutzer die Schaltfläche **Abmelden** auswählt, soll sichergestellt sein, dass er beim nächsten Aufruf von `AcquireTokenAsync(...)` zur Anmeldung aufgefordert wird. Sie können dies mit ADAL ganz einfach erreichen, indem Sie den Tokencache löschen:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Wenn der Benutzer nicht auf die Schaltfläche **Abmelden** klickt, sollte die Benutzersitzung für die nächste Ausführung von DirectorySearcher erhalten bleiben. Beim Starten der Anwendung kann der Tokencache von ADAL nach einem vorhandenen Token durchsucht und die Benutzeroberfläche entsprechend aktualisiert werden.

1. Rufen Sie in der `CheckForCachedToken()`-Methode ein weiteres Mal `AcquireTokenAsync(...)` auf. Übergeben Sie dieses Mal den `PromptBehavior.Never`-Parameter. `PromptBehavior.Never` informiert ADAL, dass der Benutzer nicht zur Anmeldung aufgefordert werden soll, sondern dass ADAL stattdessen eine Ausnahme auslösen soll, wenn kein Token zurückgeben werden kann.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without signing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

Glückwunsch! Sie verfügen nun über eine funktionierende .NET WPF-Anwendung, die Benutzer authentifizieren, Web-APIs über OAuth 2.0 sicher aufrufen und grundlegende Benutzerinformationen abfragen kann. Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen. Führen Sie danach Ihre DirectorySearcher-Anwendung aus, und melden Sie sich unter einem dieser Benutzer an. Suchen Sie anhand des UPN nach anderen Benutzern. Schließen Sie die Anwendung, und führen Sie sie erneut aus. Beachten Sie, dass die Benutzersitzung erhalten bleibt. Melden Sie sich ab und unter einem anderen Benutzer wieder an.

ADAL erleichtert Ihnen die Integration dieser allgemeinen Identitätsfunktionen in Ihre Anwendung. Die Datei übernimmt die unangenehmen Verwaltungsarbeiten für Sie, z.B. die Cacheverwaltung, die Unterstützung des OAuth-Protokolls, die Anzeige einer Benutzeroberfläche für die Anmeldung für den Benutzer, die Aktualisierung abgelaufener Token und vieles mehr. Das Einzige, womit Sie sich noch beschäftigen müssen, ist der API-Aufruf `authContext.AcquireTokenAsync(...)`.

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [auf GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip) bereit.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie eine Web-API mithilfe von OAuth 2.0-Bearerzugriffstoken geschützt werden kann.
> [!div class="nextstepaction"]
> [Schützen einer .NET Web-API mit Azure AD &gt;&gt;](quickstart-v1-dotnet-webapi.md)
