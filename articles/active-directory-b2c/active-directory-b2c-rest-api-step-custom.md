---
title: REST-API-Anspruchsaustauschvorgänge in Azure Active Directory B2C | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie REST-API-Anspruchsaustauschvorgänge in Active Directory B2C benutzerdefinierten Richtlinien hinzufügen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bc0cea765816bfac066b05aca65f668fbce0c8ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508763"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Hinzufügen von REST-API-Anspruchsaustauschvorgängen zu benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Sie können die Interaktion mit einer RESTful-API Ihren [benutzerdefinierten Richtlinien](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C hinzufügen. In diesem Artikel wird veranschaulicht, wie Sie eine Azure AD B2C User Journey erstellen, die mit RESTful-Diensten interagiert.

Die Interaktion umfasst einen Anspruchsaustausch von Informationen zwischen den REST-API-Ansprüchen und Azure AD B2C. Anspruchsaustauschvorgänge verfügen über die folgenden Merkmale:

- Kann als Orchestrierungsschritt entworfen werden.
- Kann eine externe Aktion auslösen. Beispielsweise kann ein Ereignis in einer externen Datenbank protokolliert werden.
- Kann verwendet werden, um einen Wert abzurufen und diesen anschließend in der Benutzerdatenbank zu speichern.
- Kann zum Ändern des Ausführungsablaufs verwendet werden. 

Das Szenario, das in diesem Artikel dargestellt ist, umfasst die folgenden Aktionen:

1. Suchen Sie in einem externen System nach dem Benutzer.
2. Rufen Sie die Stadt ab, in der der Benutzer registriert ist.
3. Geben Sie dieses Attribut als Anspruch an die Anwendung zurück.

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie die Schritte unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) aus.
- Ein REST-API-Endpunkt, mit dem interagiert werden kann. In diesem Artikel wird eine einfache Azure-Funktion als Beispiel verwendet. Informationen zum Erstellen der Azure-Funktion finden Sie unter [Erstellen Ihrer ersten Funktion im Azure-Portal](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Vorbereiten der API

In diesem Abschnitt bereiten Sie die Azure-Funktion so vor, dass ein Wert für `email` empfangen und anschließend der Wert für `city` zurückgegeben wird, der von Azure AD B2C als Anspruch verwendet werden kann.

Ändern Sie die von Ihnen erstellte Datei „run.csx“ für die Azure-Funktion so, dass der folgende Code verwendet wird: 

```
public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Konfigurieren des Anspruchsaustauschvorgangs

Ein technisches Profil enthält die Konfiguration für den Anspruchsaustauschvorgang. 

Öffnen Sie die Datei *TrustFrameworkExtensions.xml*, und fügen Sie im **ClaimsProvider**-Element die folgenden XML-Elemente hinzu.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Mit dem **InputClaims**-Element werden die Ansprüche definiert, die an den REST-Dienst gesendet werden. In diesem Beispiel wird der Wert des Anspruchs `givenName` als Anspruch `email` an den REST-Dienst gesendet. Mit dem **OutputClaims**-Element werden die Ansprüche definiert, die vom REST-Dienst erwartet werden.

## <a name="add-the-claim-definition"></a>Hinzufügen der Anspruchsdefinition

Fügen Sie eine Definition für `city` im **BuildingBlocks**-Element hinzu. Dieses Element befindet sich am Anfang der Datei „TrustFrameworkExtensions.xml“.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Hinzufügen eines Orchestrierungsschritts

Es gibt viele Anwendungsfälle, in denen der REST-API-Aufruf als Orchestrierungsschritt verwendet werden kann. Als Orchestrierungsschritt kann er für das Aktualisieren eines externen Systems verwendet werden, nachdem ein Benutzer eine Aufgabe wie die erstmalige Registrierung oder ein Profilupdate durchgeführt hat, um die Informationen synchron zu halten. In diesem Fall wird er zum Erweitern der Informationen verwendet, die für die Anwendung nach der Profilbearbeitung bereitgestellt werden.

Fügen Sie der User Journey für die Profilbearbeitung einen Schritt hinzu. Gehen Sie wie folgt vor, nachdem der Benutzer authentifiziert wurde (Orchestrierungsschritte 1 bis 4 im folgenden XML-Code) und die aktualisierten Profilinformationen bereitgestellt wurden (Schritt 5): Kopieren Sie den XML-Code der User Journey für die Profilbearbeitung aus der Datei *TrustFrameworkBase.xml*, und fügen Sie ihn in Ihre Datei *TrustFrameworkExtensions.xml* im **UserJourneys**-Element ein. Führen Sie die Änderung dann in Schritt 6 durch.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Der endgültige XML-Code für die User Journey sollte wie in diesem Beispiel aussehen:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Hinzufügen des Anspruchs

Bearbeiten Sie die Datei *ProfileEdit.xml*, und fügen Sie dem **OutputClaims**-Element die Zeichenfolge `<OutputClaim ClaimTypeReferenceId="city" />` hinzu.

Nachdem Sie den neuen Anspruch hinzugefügt haben, sieht das technische Profil wie in diesem Beispiel aus:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Hochladen Ihrer Änderungen und Durchführen eines Tests

1. (Optional:) Speichern Sie die vorhandene Version der Dateien (per Download), bevor Sie fortfahren.
2. Laden Sie die Dateien *TrustFrameworkExtensions.xml* und *ProfileEdit.xml* hoch, und wählen Sie die Option zum Überschreiben der vorhandenen Datei.
3. Wählen Sie **B2C_1A_ProfileEdit**.
4. Wählen Sie auf der Übersichtsseite der benutzerdefinierten Richtlinie unter **Anwendung auswählen** die Webanwendung namens *webapp1* aus, die Sie zuvor registriert haben. Stellen Sie sicher, dass die **Antwort-URL** `https://jwt.ms` lautet.
4. Wählen Sie die Option **Jetzt ausführen**. Melden Sie sich mit Ihren Kontoanmeldeinformationen an, und klicken Sie auf **Weiter**.

Wenn alles richtig eingerichtet wurde, enthält das Token den neuen Anspruch `city` mit dem Wert `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Nächste Schritte

- Sie können die Interaktion auch als Validierungsprofil entwerfen. Weitere Informationen finden Sie unter [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey als Validierung der Benutzereingabe](active-directory-b2c-rest-api-validation-custom.md).
- [Ändern der Profilbearbeitung zum Sammeln von zusätzlichen Informationen von Ihren Benutzern](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
