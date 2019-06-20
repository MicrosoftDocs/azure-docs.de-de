---
title: Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre User Journey für Azure Active Directory B2C | Microsoft-Dokumentation
description: Integrieren Sie REST-API-Anspruchsaustauschvorgänge in Ihre Azure AD B2C-User Journey zum Validieren der Benutzereingabe.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b3b896b2c423f2f9155ddb7803e59e719bd027cf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510724"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey als Validierung der Benutzereingabe

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das Framework für die Identitätsfunktion, das Azure Active Directory B2C (Azure AD B2C) zugrunde liegt, ermöglicht Ihnen die Integration in eine User Journey per RESTful-API. In dieser exemplarischen Vorgehensweise erfahren Sie, wie Azure AD B2C mit .NET Framework-RESTful-Diensten (Web-API) interagiert.

## <a name="introduction"></a>Einführung
Mit Azure AD B2C können Sie einer User Journey Ihre eigene Geschäftslogik hinzufügen, indem Sie Ihren eigenen RESTful-Dienst aufrufen. Das Framework für die Identitätsfunktion sendet Daten an den RESTful-Dienst in einer Sammlung für *Eingabeansprüche* und erhält Daten über RESTful in einer Sammlung für *Ausgabeansprüche* zurück. Mit der RESTful-Dienstintegration haben Sie folgende Möglichkeiten:

* **Überprüfen von Benutzereingabedaten**: Mit dieser Aktion verhindern Sie, dass falsch formatierte Daten in Azure AD beibehalten werden. Wenn der Wert des Benutzers nicht gültig ist, gibt Ihr RESTful-Dienst eine Fehlermeldung zurück, in der der Benutzer angewiesen wird, einen Eintrag anzugeben. Beispielsweise können Sie überprüfen, ob die vom Benutzer angegebene E-Mail-Adresse in Ihrer Kundendatenbank vorhanden ist.
* **Überschreiben von Eingabeansprüchen**: Wenn z.B. ein Benutzer den Vornamen vollständig in Kleinbuchstaben oder Großbuchstaben eingibt, können Sie den Namen so formatieren, dass nur der erste Buchstabe groß geschrieben wird.
* **Erweitern von Benutzerdaten durch eine stärkere Integration in die Branchenanwendungen des Unternehmens**: Ihr RESTful-Dienst kann die E-Mail-Adresse des Benutzers empfangen, die Kundendatenbank abfragen und die Treuenummer des Benutzers an Azure AD B2C zurückgeben. Die Rückgabeansprüche können unter dem AAD-Benutzerkonto gespeichert, in den nächsten *Orchestrierungsschritten* ausgewertet oder in das Zugriffstoken eingebunden werden.
* **Ausführen von benutzerdefinierter Geschäftslogik**: Sie können Pushbenachrichtigungen senden, Unternehmensdatenbanken aktualisieren, einen Benutzermigrationsprozess ausführen, Berechtigungen verwalten, Datenbanken überwachen und andere Aktionen ausführen.

Sie können die Integration in die RESTful-Dienste auf folgende Weise entwerfen:

* **Technisches Validierungsprofil**: Der Aufruf des RESTful-Diensts erfolgt im technischen Validierungsprofil des angegebenen technischen Profils. Mit dem technischen Validierungsprofil werden die vom Benutzer bereitgestellten Daten überprüft, bevor die User Journey fortgesetzt wird. Beim technischen Validierungsprofil haben Sie folgende Möglichkeiten:
   * Senden von Eingabeansprüchen
   * Überprüfen der Eingabeansprüche und Auslösen von benutzerdefinierten Fehlermeldungen
   * Zurücksenden von Ausgabeansprüchen

* **Anspruchsaustausch**: Dieser Entwurf ähnelt dem technischen Validierungsprofil, er erfolgt aber innerhalb eines Orchestrierungsschritts. Diese Definition ist auf folgende Vorgänge beschränkt:
   * Senden von Eingabeansprüchen
   * Zurücksenden von Ausgabeansprüchen

## <a name="restful-walkthrough"></a>Exemplarische Vorgehensweise für RESTful
In dieser exemplarischen Vorgehensweise entwickeln Sie eine .NET Framework-Web-API, mit der die Benutzereingabe überprüft und die Treuenummer des Benutzers angegeben wird. Beispielsweise kann Ihre Anwendung basierend auf der Treuenummer Zugriff auf *Platin-Vorteile* gewähren.

Übersicht:
* Entwickeln des RESTful-Diensts (.NET Framework-Web-API)
* Verwenden des RESTful-Diensts in der User Journey
* Senden von Eingabeansprüchen und Lesen in Ihrem Code
* Überprüfen des Vornamens des Benutzers
* Zurücksenden einer Treuenummer
* Hinzufügen der Treuenummer zum JSON Web Token (JWT)

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die Schritte im Artikel [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) aus.

## <a name="step-1-create-an-aspnet-web-api"></a>Schritt 1: Erstellen einer ASP.NET-Web-API

1. Erstellen Sie in Visual Studio ein Projekt durch Auswählen von **Datei** > **Neu** > **Projekt**.

2. Wählen Sie im Fenster **Neues Projekt** die Optionen **Visual C#**  > **Web** > **ASP.NET-Webanwendung (.NET Framework)** aus.

3. Geben Sie im Feld **Name** einen Namen für die Anwendung an (beispielsweise *Contoso.AADB2C.API*), und wählen Sie dann **OK** aus.

    ![Erstellen eines neuen Visual Studio-Projekts](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. Wählen Sie im Fenster **Neue ASP.NET-Webanwendung** eine **Web-API**- oder **Azure-API-App**-Vorlage aus.

    ![Auswählen der Web-API-Vorlage](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Stellen Sie sicher, dass für die Authentifizierung **Keine Authentifizierung** festgelegt ist.

6. Wählen Sie **OK** aus, um das Projekt zu erstellen.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Schritt 2: Vorbereiten des REST-API-Endpunkts

### <a name="step-21-add-data-models"></a>Schritt 2.1: Hinzufügen von Datenmodellen
Die Modelle stehen für die Eingabeanspruchs- und Ausgabeanspruchsdaten in Ihrem RESTful-Dienst. Ihr Code liest die Eingabedaten, indem das Eingabeanspruchsmodell aus einer JSON-Zeichenfolge in ein C#-Objekt (Ihr Modell) deserialisiert wird. Mit der ASP.NET-Web-API wird das Ausgabeanspruchsmodell automatisch zurück in JSON deserialisiert, und anschließend werden die serialisierten Daten in den Hauptteil der HTTP-Antwortnachricht geschrieben.

Erstellen eines Modells, das Eingabeansprüche darstellt, indem es Folgendes ausführt:

1. Wenn der Projektmappen-Explorer nicht geöffnet ist, wählen Sie **Ansicht** > **Projektmappen-Explorer** aus.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Modelle**, und wählen Sie dann **Hinzufügen** und anschließend **Klasse** aus.

    ![Modell hinzufügen](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Geben Sie der Klasse den Namen `InputClaimsModel`, und fügen Sie der `InputClaimsModel`-Klasse die folgenden Eigenschaften hinzu:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Erstellen Sie das neue Modell `OutputClaimsModel`, und fügen Sie der `OutputClaimsModel`-Klasse die folgenden Eigenschaften hinzu:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Erstellen Sie ein weiteres Modell (`B2CResponseContent`), das Sie verwenden, um die Fehlermeldungen der Eingabevalidierung auszulösen. Fügen Sie der `B2CResponseContent`-Klasse die folgenden Eigenschaften hinzu, geben Sie die fehlenden Verweise an, und speichern Sie die Datei:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Schritt 2.2: Hinzufügen eines Controllers
In der Web-API ist ein _Controller_ ein Objekt zum Verarbeiten von HTTP-Anforderungen. Der Controller gibt Ausgabeansprüche zurück oder löst – falls der Vorname nicht gültig ist – eine Fehlermeldung aufgrund eines HTTP-Konflikts aus.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie dann **Hinzufügen** und **Controller** aus.

    ![Hinzufügen eines neuen Controllers](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **Web-API-Controller – Leer** und dann **Hinzufügen** aus.

    ![Auswählen von „Web API 2 Controller - Empty“ (Web-API 2-Controller – Leer)](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. Geben Sie dem Controller im Fenster **Controller hinzufügen** den Namen **IdentityController**, und wählen Sie dann **Hinzufügen** aus.

    ![Eingeben des Controllernamens](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Im Rahmen des Gerüstbauvorgangs wird im Ordner *Controller* die Datei *IdentityController.cs* erstellt.

4. Wenn die Datei *IdentityController.cs* noch nicht geöffnet ist, doppelklicken Sie darauf, und ersetzen Sie dann den Code in der Datei durch folgenden Code:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Schritt 3: Veröffentlichen des Projekts in Azure
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **Contoso.AADB2C.API**, und wählen Sie dann **Veröffentlichen** aus.

    ![Veröffentlichen in Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. Wählen Sie im Fenster **Veröffentlichen** die Option **Microsoft Azure App Service** und anschließend **Veröffentlichen** aus.

    ![Erstellen eines neuen Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Das Fenster **App Service erstellen** wird geöffnet. Darin können Sie damit beginnen, alle Azure-Ressourcen zu erstellen, die zum Ausführen der ASP.NET-Web-App in Azure benötigt werden.

    > [!NOTE]
    >Weitere Informationen zum Veröffentlichen finden Sie unter [Erstellen von ASP.NET-Web-Apps in Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. Geben Sie im Feld **Web-App-Name** einen eindeutigen App-Namen ein (gültige Zeichen sind a–z, A–Z, 0–9 und der Bindestrich [-]). Die URL der Web-App lautet: http://<App-Name>.azurewebsites.NET, wobei *App-Name* der Name Ihrer Web-App ist. Sie können den automatisch generierten Namen übernehmen. Dieser ist eindeutig.

    ![Bereitstellen der App Service-Eigenschaften](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Wählen Sie **Erstellen** aus, um mit der Erstellung der Azure-Ressourcen zu beginnen.  
    Nach dem Erstellen der ASP.NET-Web-App veröffentlicht der Assistent diese in Azure und öffnet sie anschließend im Standardbrowser.

6. Kopieren Sie die URL der Web-App.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Schritt 4: Hinzufügen des neuen `loyaltyNumber`-Anspruchs zum Schema der Datei „TrustFrameworkExtensions.xml“
Der `loyaltyNumber`-Anspruch ist in unserem Schema noch nicht definiert. Fügen Sie diese Definition in das `<BuildingBlocks>`-Element ein, das sich am Anfang der Datei *TrustFrameworkExtensions.xml* befindet.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Schritt 5: Hinzufügen eines Anspruchsanbieters
Jeder Anspruchsanbieter muss über mindestens ein technisches Profil verfügen, mit dem die Endpunkte und die Protokolle bestimmt werden, die für die Kommunikation mit diesem Anspruchsanbieter erforderlich sind.

Ein Anspruchsanbieter kann aus verschiedenen Gründen über mehrere technische Profile verfügen. Beispielsweise können mehrere technische Profile definiert werden, da der Anspruchsanbieter mehrere Protokolle unterstützt, Endpunkte unterschiedliche Funktionen aufweisen können oder Releases unterschiedliche Ansprüche mit verschiedenen Vertrauensgraden enthalten können. Es kann akzeptabel sein, in einer User Journey vertrauliche Ansprüche freizugeben, während dies für eine andere nicht der Fall ist.

Der folgende XML-Ausschnitt enthält einen Anspruchsanbieterknoten mit zwei technischen Profilen:

* **TechnicalProfile Id="REST-API-SignUp"** : Definiert Ihren RESTful-Dienst.
  * `Proprietary` wird als das Protokoll für einen RESTful-basierten Anbieter beschrieben.
  * `InputClaims` definiert die Ansprüche, die von Azure AD B2C an den REST-Dienst gesendet werden.

    In diesem Beispiel werden der Inhalt des Anspruchs `givenName` als `firstName` und der Inhalt des Anspruchs `surname` als `lastName` an den REST-Dienst gesendet, während `email` unverändert gesendet wird. Mit dem `OutputClaims`-Element werden die Ansprüche definiert, die vom RESTful-Dienst abgerufen werden, um zurück an Azure AD B2C gesendet zu werden.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"** : Fügt das technische Validierungsprofil einem vorhandenen technischen Profil (in der Basisrichtlinie definiert) hinzu. Während des Anmeldeablaufs ruft das technische Validierungsprofil das obige technische Profil auf. Wenn der RESTful-Dienst einen HTTP-Fehler 409 (Konfliktfehler) zurückgibt, wird die Fehlermeldung dem Benutzer angezeigt.

Suchen Sie nach dem Knoten `<ClaimsProviders>`, und fügen Sie dann unter dem Knoten `<ClaimsProviders>` den folgenden XML-Codeausschnitt hinzu:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Schritt 6: Hinzufügen des `loyaltyNumber`-Anspruchs zur Richtliniendatei der vertrauenden Seite, damit der Anspruch an Ihre Anwendung gesendet wird
Bearbeiten Sie die Datei *SignUpOrSignIn.xml* der vertrauenden Seite, und ändern Sie das Element „TechnicalProfile Id="PolicyProfile"“, um Folgendes hinzuzufügen: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Nachdem Sie den neuen Anspruch hinzugefügt haben, sieht der Code für die vertrauende Seite wie folgt aus:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Schritt 7: Hochladen der Richtlinie in Ihren Mandanten

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum [Kontext Ihres Azure AD B2C-Mandanten](active-directory-b2c-navigate-to-b2c-context.md), und öffnen Sie anschließend **Azure AD B2C**.

2. Wählen Sie **Framework für die Identitätsfunktion** aus.

3. Öffnen Sie **Alle Richtlinien**.

4. Wählen Sie **Richtlinie hochladen** aus.

5. Aktivieren Sie das Kontrollkästchen **Richtlinie überschreiben, sofern vorhanden**.

6. Laden Sie die Datei „TrustFrameworkExtensions.xml“ hoch, und stellen Sie sicher, dass sie die Überprüfung besteht.

7. Wiederholen Sie den vorherigen Schritt mit der Datei „SignUpOrSignIn.xml“.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Schritt 8: Testen der benutzerdefinierten Richtlinie mit „Jetzt ausführen“
1. Wählen Sie **Azure AD B2C-Einstellungen** aus, und navigieren Sie dann zu **Framework für die Identitätsfunktion**.

    > [!NOTE]
    > **Jetzt ausführen** macht erforderlich, dass mindestens eine Anwendung im Mandanten vorab registriert wird. Informationen zum Registrieren von Anwendungen finden Sie in den Artikeln [Erste Schritte](active-directory-b2c-get-started.md) bzw. [Anwendungsregistrierung](active-directory-b2c-app-registration.md) zu Azure AD B2C.

2. Öffnen Sie **B2C_1A_signup_signin**. Dies ist die benutzerdefinierte Richtlinie der vertrauenden Seite (Relying Party, RP), die Sie hochgeladen haben. Wählen Sie anschließend **Jetzt ausführen** aus.

    ![Das Fenster „B2C_1A_signup_signin“](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testen Sie den Prozess, indem Sie **Test** in das Feld **Vorname** eingeben.  
    Azure AD B2C zeigt am oberen Rand des Fensters eine Fehlermeldung an.

    ![Testen Ihrer Richtlinie](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. Geben Sie in das Feld **Vorname** einen Namen (nicht „Test“) ein.  
    Azure AD B2C meldet den Benutzer an und sendet dann eine „loyaltyNumber“ an Ihre Anwendung. Die Treuenummer ist in diesem JWT-Code enthalten.

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
* Nachdem Sie die exemplarische Vorgehensweise unter [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) abgeschlossen haben, empfiehlt es sich, ein Szenario mit Ihren eigenen benutzerdefinierten Richtliniendateien zu erstellen. Zu Referenzzwecken haben wir [Beispiele für Richtliniendateien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw) bereitgestellt.
* Sie können den vollständigen Code unter [Sample Visual Studio solution for reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/) (Visual Studio-Beispiellösung zur Referenz) herunterladen.

## <a name="next-steps"></a>Nächste Schritte
* [Secure your RESTful API with basic authentication (username and password)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Schützen Ihrer RESTful-API per Standardauthentifizierung (Benutzername und Kennwort))
* [Schützen Ihrer RESTful-API mit Clientzertifikaten](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
