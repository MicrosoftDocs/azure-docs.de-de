---
title: REST-API-Anspruchsaustausch als Validierung
titleSuffix: Azure AD B2C
description: Eine exemplarische Vorgehensweise zum Erstellen einer Azure AD B2C User Journey, die mit RESTful-Diensten interagiert.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951926"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey zum Überprüfen der Benutzereingabe

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das Identity Experience Framework, das Azure Active Directory B2C (Azure AD B2C) zugrunde liegt, ermöglicht Identitätsentwicklern die Integration einer Interaktion mit einer RESTful-API in eine User Journey.  Am Ende dieser exemplarischen Vorgehensweise sind Sie in der Lage, eine Azure AD B2C User Journey zu erstellen, die mit [RESTful-Diensten](custom-policy-rest-api-intro.md) interagiert, um Benutzereingaben zu überprüfen.

In diesem Szenario fügen wir die Möglichkeit für Benutzer hinzu, auf der Azure AD B2C-Registrierungsseite eine Treuebonusnummer einzugeben. Wir überprüfen, ob diese Kombination aus E-Mail und Treuebonusnummer einem Aktionscode zugeordnet ist, indem wir diese Daten an eine REST-API senden. Wenn die REST-API einen Aktionscode für diesen Benutzer findet, wird dieser an Azure AD B2C zurückgegeben. Schließlich wird der Aktionscode in die Tokenansprüche so eingefügt, dass er von der Anwendung genutzt werden kann.

Sie können die Interaktion auch als Orchestrierungsschritt entwerfen. Dies bietet sich an, wenn die REST-API die Daten auf dem Bildschirm nicht überprüft und stets Ansprüche zurückgibt. Weitere Informationen finden Sie unter [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustausch-Vorgängen in Ihre Azure AD B2C User Journey als Orchestrierungsschritt](custom-policy-rest-api-claims-exchange.md)

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie die Schritte unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](custom-policy-get-started.md) aus. Sie sollten eine funktionierende benutzerdefinierte Richtlinie für die Registrierung und Anmeldung bei lokalen Konten besitzen.
- Weitere Informationen finden Sie unter [Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre benutzerdefinierte Azure AD B2C-Richtlinie](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Vorbereiten eines REST-API-Endpunkts

Für diese exemplarische Vorgehensweise benötigen Sie eine REST-API, die überprüft, ob eine E-Mail-Adresse in Ihrem Back-End-System mit einer Treuepunkt-ID registriert ist. Falls ja, muss die REST-API einen Aktionscode für die Registrierung zurückgeben, den der Kunde zum Kauf von Waren in Ihrer Anwendung angeben kann. Andernfalls sollte die REST-API die HTTP-Fehlermeldung 409 zurückgeben: „Die Treuepunkt-ID {loyalty ID} ist nicht mit der E-Mail-Adresse {Email} verknüpft.“

Der folgende JSON-Code veranschaulicht die Daten, die Azure AD B2C an Ihren REST-API-Endpunkt sendet. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Sobald Ihre REST-API die Daten überprüft, muss sie HTTP 200 (OK) mit den folgenden JSON-Daten zurückgeben:

```json
{
    "promoCode": "24534"
}
```

Wenn die Prüfung fehlgeschlagen ist, muss die REST-API den HTTP-Fehler 409 (Konflikt) mit dem JSON-Element `userMessage` zurückgeben. Das IEF erwartet den Anspruch `userMessage`, den die REST-API zurückgibt. Dieser Anspruch wird dem Benutzer als Zeichenfolge dargestellt, wenn die Überprüfung fehlschlägt.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

Die Einrichtung des REST-API-Endpunkts wird in diesem Artikel nicht behandelt. Wir haben ein [Azure Functions](../azure-functions/functions-reference.md)-Beispiel erstellt. Sie können auf [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function) auf den gesamten Azure-Funktionscode zugreifen.

## <a name="define-claims"></a>Definieren von Ansprüchen

Ein Anspruch bietet eine temporäre Speicherung von Daten während der Ausführung einer Azure AD B2C-Richtlinie. Sie können Ansprüche im Abschnitt [Anspruchsschema](claimsschema.md) deklarieren. 

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** die folgenden Ansprüche hinzu.  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Hinzufügen des technischen Profils für die RESTful-API 

Ein [technisches RESTful-Profil](restful-technical-profile.md) bietet Unterstützung bei der Anbindung an Ihren eigenen RESTful-Dienst. Azure AD B2C sendet Daten an den RESTful-Dienst in einer `InputClaims`-Sammlung und erhält Daten in einer `OutputClaims`-Sammlung zurück. Suchen Sie das Element **ClaimsProviders**, und fügen Sie wie folgt einen neuen Anspruchsanbieter hinzu:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

In diesem Beispiel wird `userLanguage` als `lang` innerhalb der JSON-Nutzlast an den REST-Dienst gesendet. Der Wert des Anspruchs `userLanguage` enthält die ID der aktuellen Benutzersprache. Weitere Informationen finden Sie unter [Anspruchskonfliktlöser](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Konfigurieren des technischen Profils für die RESTful-API 

Nachdem Sie Ihre REST-API bereitgestellt haben, legen Sie die Metadaten des technischen Profils `REST-ValidateProfile` so fest, dass sie Ihre eigene REST-API wiedergeben, darunter:

- **ServiceUrl**. Legt die URL des REST-API-Endpunkts fest.
- **SendClaimsIn**. Gibt an, wie die Eingabeansprüche an den RESTful-Anspruchsanbieter gesendet werden.
- **AuthenticationType**. Legt den Typ der Authentifizierung fest, die vom RESTful-Anspruchsanbieter ausgeführt wird. 
- **AllowInsecureAuthInProduction**. Sorgen Sie in einer Produktionsumgebung dafür, dass diese Metadaten auf `true` festgelegt werden.
    
Weitere Konfigurationen finden Sie in den [Metadaten für das technische RESTful-Profil](restful-technical-profile.md#metadata).

Die Kommentare `AuthenticationType` und `AllowInsecureAuthInProduction` oben geben Änderungen an, die Sie beim Wechsel zu einer Produktionsumgebung vornehmen sollten. Informationen zum Schützen Ihrer RESTful-APIs für die Produktionsumgebung finden Sie unter [Schützen von RESTful-APIs](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Überprüfen der Benutzereingaben

Um die Treuebonusnummer des Benutzers bei der Registrierung abzurufen, müssen Sie dem Benutzer erlauben, diese Daten auf dem Bildschirm einzugeben. Fügen Sie den Ausgabeanspruch **loyaltyId** zur Anmeldeseite hinzu, indem Sie ihn dem Element `OutputClaims` des bestehenden technischen Registrierungsprofilabschnitts hinzufügen. Geben Sie die gesamte Liste von Ausgabeansprüchen an, um die Reihenfolge zu steuern, in der die Ansprüche auf dem Bildschirm angezeigt werden.  

Fügen Sie den Verweis auf das technische Überprüfungsprofil dem technischen Profil für die Registrierung hinzu, das `REST-ValidateProfile` aufruft. Das neue technische Überprüfungsprofil wird der in der Basisrichtlinie definierten Sammlung `<ValidationTechnicalProfiles>` oben hinzugefügt. Dieses Verhalten bedeutet, dass Azure AD B2C erst nach erfolgreicher Überprüfung mit der Erstellung des Kontos im Verzeichnis fortfahren kann.   

1. Suchen Sie nach dem Element **ClaimsProviders**. Fügen Sie einen neuen Anspruchsanbieter wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Einschließen eines Anspruchs in das Token 

Um den Anspruch „Aktionscode“ an die Anwendung der vertrauenden Seite zurückzugeben, fügen Sie der Datei <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> einen Ausgabeanspruch hinzu. Der Ausgabeanspruch ermöglicht es, den Anspruch nach einer erfolgreichen User Journey in das Token einzufügen, der dann an die Anwendung gesendet wird. Ändern Sie im Abschnitt für die vertrauende Seite das Element des technischen Profils, um `promoCode` als Ausgabeanspruch hinzuzufügen.
 
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
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten enthält, indem Sie im oberen Menü den **Verzeichnis- und Abonnementfilter** und dann das Verzeichnis auswählen, das Ihren Azure AD-Mandanten enthält.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die geänderten Richtliniendateien hoch: *TrustFrameworkExtensions.xml*, und *SignUpOrSignin.xml*. 
1. Wählen Sie die hochgeladene Registrierungs- oder Anmelderichtlinie aus, und klicken Sie auf die Schaltfläche **Jetzt ausführen**.
1. Sie sollten sich mit einer E-Mail-Adresse registrieren können.
1. Klicken Sie auf den Link **Jetzt registrieren**.
1. Geben Sie 1234 in **Ihre Treuebonus-ID** ein, und klicken Sie auf **Weiter**. An dieser Stelle sollten Sie eine Prüfungsfehlermeldung erhalten.
1. Ändern Sie den Wert, und klicken Sie auf **Weiter**.
1. Das Token, das an die Anwendung zurückgesendet wird, enthält den Anspruch `promoCode`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Schützen Ihrer APIs finden Sie in den folgenden Artikeln:

- [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustausch-Vorgängen in Ihre Azure AD B2C User Journey als Orchestrierungsschritt](custom-policy-rest-api-claims-exchange.md)
- [Schützen Ihrer RESTful-API](secure-rest-api.md)
- [Referenz: Technisches Profil „RESTful“](restful-technical-profile.md)