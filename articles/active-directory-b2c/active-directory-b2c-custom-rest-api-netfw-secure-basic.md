---
title: Schützen Ihrer RESTful-Dienste unter Verwendung der HTTP-Standardauthentifizierung in Azure Active Directory B2C | Microsoft-Dokumentation
description: Schützen Ihres benutzerdefinierten REST-API-Anspruchsaustauschs in Azure AD B2C unter Verwendung der HTTP-Standardauthentifizierung.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e07a3fee2cc6c588dee608a68415b554e6fc77f3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507586"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Schützen Ihrer RESTful-Dienste unter Verwendung der HTTP-Standardauthentifizierung

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In einem [thematisch ähnlichen Azure AD B2C-Artikel](active-directory-b2c-custom-rest-api-netfw.md) haben wir einen RESTful-Dienst (Web-API) erstellt, der sich ohne Authentifizierung in Azure Active Directory B2C-User Journeys (Azure AD B2C) integriert.

In diesem Artikel fügen Sie Ihrem RESTful-Dienst die HTTP-Standardauthentifizierung hinzu, sodass nur verifizierte Benutzer, einschließlich B2C, auf Ihre API zugreifen können. Mit der HTTP-Standardauthentifizierung legen Sie die Anmeldeinformationen des Benutzers (App-ID und App-Geheimnis) in Ihrer benutzerdefinierten Richtlinie fest.

Weitere Informationen finden Sie unter [Standardauthentifizierung in der ASP.NET-Web-API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte im Artikel [Integrieren von Anspruchsaustauschvorgängen der REST-API in Ihre Azure AD B2C-User Journey](active-directory-b2c-custom-rest-api-netfw.md) durch.

## <a name="step-1-add-authentication-support"></a>Schritt 1: Hinzufügen von Authentifizierungssupport

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Schritt 1.1: Hinzufügen von Anwendungseinstellungen zur Datei „web.config“ des Projekts

1. Öffnen Sie das zuvor erstellte Visual Studio-Projekt.

2. Fügen Sie der Datei „web.config“ unter dem `appSettings`-Element folgende Anwendungseinstellungen hinzu:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Erstellen Sie ein Kennwort, und legen Sie dann den Wert `WebApp:ClientSecret` fest.

    Um ein komplexes Kennwort zu generieren, führen Sie den folgenden PowerShell-Code aus. Sie können jeden beliebigen Wert verwenden.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Schritt 1.2: Installieren von OWIN-Bibliotheken

Fügen Sie dem Projekt zunächst über die Visual Studio-Paket-Manager-Konsole die NuGet-Pakete der OWIN-Middleware hinzu:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Schritt 1.3: Hinzufügen einer Authentifizierungs-Middlewareklasse

Fügen Sie die `ClientAuthMiddleware.cs`-Klasse unter dem Ordner *App_Start* hinzu. Gehen Sie dazu wie folgt vor:

1. Klicken Sie mit der rechten Maustaste auf den Ordner *App_Start*, wählen Sie **Hinzufügen** aus, und klicken Sie dann auf **Klasse**.

   ![Hinzufügen der Klasse „ClientAuthMiddleware.cs“ im Ordner „App_Start“](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Geben Sie im Feld **Name** die Zeichenfolge **ClientAuthMiddleware.cs** ein.

   ![Erstellen Sie eine neue C#-Klasse.](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Öffnen Sie die Datei *App_Start\ClientAuthMiddleware.cs*, und ersetzen Sie den Inhalt der Datei durch folgenden Code:

    ```csharp
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Schritt 1.4: Hinzufügen einer OWIN-Startklasse

Fügen Sie der API eine OWIN-Startklasse mit dem Namen `Startup.cs` hinzu. Gehen Sie dazu wie folgt vor:
1. Klicken Sie mit der rechten Maustaste auf das Projekt, wählen Sie **Hinzufügen** > **Neues Element** aus, und suchen Sie dann nach **OWIN**.

   ![Hinzufügen einer OWIN-Startklasse](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Öffnen Sie die Datei *Startup.cs*, und ersetzen Sie den Inhalt der Datei durch folgenden Code:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Schritt 1.5: Schützen der Identity-API-Klasse

Öffnen Sie „Controllers\IdentityController.cs“, und fügen Sie der Controllerklasse das Tag `[Authorize]` hinzu. Dieses Tag schränkt den Zugriff auf den Controller für die Benutzer ein, die die Autorisierungsanforderung erfüllen.

![Hinzufügen des Autorisierungstags zum Controller](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Schritt 2: Veröffentlichen in Azure

Um das Projekt zu veröffentlichen, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **Contoso.AADB2C.API**, und wählen Sie **Veröffentlichen** aus.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Schritt 3: Hinzufügen von App-ID und App-Geheimnis für RESTful-Dienste in Azure AD B2C

Nachdem Ihr RESTful-Dienst mithilfe der Client-ID (Benutzername) und eines Geheimnisses geschützt ist, müssen Sie die Anmeldeinformationen in Ihrem Azure AD B2C-Mandanten speichern. Ihre benutzerdefinierte Richtlinie stellt die Anmeldeinformationen bereit, wenn sie Ihre RESTful-Dienste aufruft.

### <a name="step-31-add-a-restful-services-client-id"></a>Schritt 3.1: Hinzufügen einer Client-ID für RESTful-Dienste

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten **B2C-Einstellungen** > **Framework für die Identitätsfunktion** aus.


2. Wählen Sie **Richtlinienschlüssel** aus, um die in Ihrem Mandanten verfügbaren Schlüssel anzuzeigen.

3. Wählen Sie **Hinzufügen**.

4. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus.

5. Geben Sie für **Name** die Zeichenfolge **B2cRestClientId** ein.  
    Das Präfix *B2C_1A_* wird möglicherweise automatisch hinzugefügt.

6. Geben Sie im Feld **Geheimnis** die zuvor definierte App-ID ein.

7. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.

8. Klicken Sie auf **Erstellen**.

9. Vergewissern Sie sich, dass Sie den Schlüssel `B2C_1A_B2cRestClientId` erstellt haben.

### <a name="step-32-add-a-restful-services-client-secret"></a>Schritt 3.2: Hinzufügen des geheimen Clientschlüssels für RESTful-Dienste

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten **B2C-Einstellungen** > **Framework für die Identitätsfunktion** aus.

2. Wählen Sie **Policy Keys** (Richtlinienschlüssel), um die in Ihrem Mandanten verfügbaren Schlüssel anzuzeigen.

3. Wählen Sie **Hinzufügen**.

4. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus.

5. Geben Sie für **Name** die Zeichenfolge **B2cRestClientSecret** ein.  
    Das Präfix *B2C_1A_* wird möglicherweise automatisch hinzugefügt.

6. Geben Sie im Feld **Geheimnis** das zuvor definierte App-Geheimnis ein.

7. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.

8. Klicken Sie auf **Erstellen**.

9. Vergewissern Sie sich, dass Sie den Schlüssel `B2C_1A_B2cRestClientSecret` erstellt haben.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Schritt 4: Ändern des technischen Profils zum Unterstützen der Standardauthentifizierung in Ihrer Erweiterungsrichtlinie

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).

2. Suchen Sie den `<TechnicalProfile>`-Knoten, der `Id="REST-API-SignUp"` enthält.

3. Suchen Sie das `<Metadata>`-Element.

4. Ändern Sie *AuthenticationType* wie folgt in *Basic*:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `<Metadata>`-Element hinzu:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Nachdem Sie den Ausschnitt hinzugefügt haben, sollte Ihr technisches Profil wie der folgende XML-Code aussehen:
    
    ![Standardauthentifizierungs-XML-Elemente hinzufügen](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Schritt 5: Hochladen der Richtlinie in Ihren Mandanten

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum [Kontext Ihres Azure AD B2C-Mandanten](active-directory-b2c-navigate-to-b2c-context.md), und öffnen Sie anschließend **Azure AD B2C**.

2. Wählen Sie **Framework für die Identitätsfunktion** aus.

3. Öffnen Sie **Alle Richtlinien**.

4. Wählen Sie **Richtlinie hochladen** aus.

5. Aktivieren Sie das Kontrollkästchen **Richtlinie überschreiben, sofern vorhanden**.

6. Laden Sie die Datei *TrustFrameworkExtensions.xml* hoch, und stellen Sie sicher, dass sie die Überprüfung besteht.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Schritt 6: Testen der benutzerdefinierten Richtlinie mit „Jetzt ausführen“

1. Öffnen Sie **Azure AD B2C-Einstellungen**, und wählen Sie **Framework für die Identitätsfunktion** aus.

    >[!NOTE]
    >Für „Jetzt ausführen“ muss mindestens eine Anwendung vorab im Mandanten registriert werden. Informationen zum Registrieren von Anwendungen finden Sie in den Artikeln [Erste Schritte](active-directory-b2c-get-started.md) bzw. [Anwendungsregistrierung](active-directory-b2c-app-registration.md) zu Azure AD B2C.

2. Öffnen Sie **B2C_1A_signup_signin**. Dies ist die benutzerdefinierte Richtlinie der vertrauenden Seite (Relying Party, RP), die Sie hochgeladen haben. Wählen Sie anschließend **Jetzt ausführen** aus.

3. Testen Sie den Prozess, indem Sie **Test** in das Feld **Vorname** eingeben.  
    Azure AD B2C zeigt am oberen Rand des Fensters eine Fehlermeldung an.

    ![Testen Ihrer Identitäts-API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Geben Sie in das Feld **Vorname** einen Namen (nicht „Test“) ein.  
    Azure AD B2C meldet den Benutzer an und sendet dann eine Treuenummer an Ihre Anwendung. Die Treuenummer ist in diesem Beispiel enthalten:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Optional:) Herunterladen der vollständigen Richtliniendateien und des Codes

* Nachdem Sie die exemplarische Vorgehensweise unter [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) abgeschlossen haben, empfiehlt es sich, ein Szenario mit Ihren eigenen benutzerdefinierten Richtliniendateien zu erstellen. Zu Referenzzwecken haben wir [Beispiele für Richtliniendateien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic) bereitgestellt.
* Sie können den vollständigen Code unter [Sample Visual Studio solution for reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API) (Visual Studio-Beispiellösung zur Referenz) herunterladen.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden Sie zum Sichern Ihrer RESTful-API Clientzertifikate.](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)