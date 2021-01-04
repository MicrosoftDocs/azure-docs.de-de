---
title: 'REST-API-Anspruchsaustauschvorgänge: Azure Active Directory B2C'
description: Es wird beschrieben, wie Sie REST-API-Anspruchsaustauschvorgänge in Active Directory B2C benutzerdefinierten Richtlinien hinzufügen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949495"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Exemplarische Vorgehensweise: Hinzufügen von REST-API-Anspruchsaustauschvorgängen zu benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mit Azure Active Directory B2C (Azure AD B2C) können Identitätsentwickler eine Interaktion mit einer RESTful-API in eine User Journey integrieren. Am Ende dieser exemplarischen Vorgehensweise sind Sie in der Lage, eine Azure AD B2C User Journey zu erstellen, die mit [RESTful-Diensten](custom-policy-rest-api-intro.md) interagiert.

In diesem Szenario reichern wir die Tokendaten des Benutzers an, indem wir sie in einen unternehmensinternen branchenspezifischen Workflow integrieren. Während der Registrierung oder Anmeldung mit einem lokalen oder Verbundkonto ruft Azure AD B2C eine REST-API auf, um die erweiterten Profildaten des Benutzers aus einer Remotedatenquelle abzurufen. In diesem Beispiel sendet Azure AD B2C den eindeutigen Bezeichner des Benutzers (objectId). Die REST-API gibt dann den Kontostand des Benutzers zurück (eine zufällige Zahl). Nutzen Sie dieses Beispiel als Ausgangspunkt für die Integration in Ihr eigenes CRM-System, Ihre Marketingdatenbank oder einen beliebigen branchenspezifischen Workflow.

Sie können die Interaktion auch als technisches Überprüfungsprofil gestalten. Dies bietet sich an, wenn die REST-API Daten auf dem Bildschirm überprüft und Ansprüche zurückgibt. Weitere Informationen finden Sie unter [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey zum Überprüfen der Benutzereingabe](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie die Schritte unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](custom-policy-get-started.md) aus. Sie sollten eine funktionierende benutzerdefinierte Richtlinie für die Registrierung und Anmeldung bei lokalen Konten besitzen.
- Weitere Informationen finden Sie unter [Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre benutzerdefinierte Azure AD B2C-Richtlinie](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Vorbereiten eines REST-API-Endpunkts

Für diese exemplarische Vorgehensweise benötigen Sie eine REST-API, die überprüft, ob die Azure AD B2C-objectId eines Benutzers in Ihrem Back-End-System registriert ist. Falls ja, gibt die REST-API den Kontostand des Benutzers zurück. Andernfalls registriert die REST-API das neue Konto im Verzeichnis und gibt den Ausgangssaldo `50.00` zurück.

Der folgende JSON-Code veranschaulicht die Daten, die Azure AD B2C an Ihren REST-API-Endpunkt sendet. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Sobald Ihre REST-API die Daten überprüft, muss sie HTTP 200 (OK) mit den folgenden JSON-Daten zurückgeben:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Hinzufügen des technischen Profils für die RESTful-API 

Ein [technisches RESTful-Profil](restful-technical-profile.md) bietet Unterstützung bei der Anbindung an Ihren eigenen RESTful-Dienst. Azure AD B2C sendet Daten an den RESTful-Dienst in einer `InputClaims`-Sammlung und erhält Daten in einer `OutputClaims`-Sammlung zurück. Suchen Sie das Element **ClaimsProviders** in Ihrer Datei <em> **`TrustFrameworkExtensions.xml`**</em>, und fügen Sie wie folgt einen neuen Anspruchsanbieter hinzu:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
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

## <a name="add-an-orchestration-step"></a>Hinzufügen eines Orchestrierungsschritts

[User Journeys](userjourneys.md) geben explizite Pfade an, über die eine Richtlinie einer Anwendung der vertrauenden Seite die gewünschten Ansprüche für einen Benutzer abrufen kann. Eine User Journey wird als Orchestrierungssequenz dargestellt, die für eine erfolgreiche Transaktion durchlaufen werden muss. Sie können Orchestrierungsschritte hinzufügen oder entfernen. In diesem Fall fügen Sie einen neuen Orchestrierungsschritt hinzu, der verwendet wird, um die Informationen zu erweitern, die der Anwendung nach der Benutzerregistrierung oder Anmeldung über den REST-API-Aufruf zur Verfügung gestellt werden.

1. Öffnen Sie die Basisdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>.
1. Suchen Sie nach dem `<UserJourneys>`-Element. Kopieren Sie das gesamte Element, und löschen Sie es anschließend.
1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Fügen Sie `<UserJourneys>` nach dem Schließen des Elements `<ClaimsProviders>` in die Erweiterungsdatei ein.
1. Suchen Sie `<UserJourney Id="SignUpOrSignIn">`, und fügen Sie den folgenden Orchestrierungsschritt vor dem letzten hinzu.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Gestalten Sie den letzten Orchestrierungsschritt um, indem Sie `Order` in `8` ändern. Die letzten zwei Orchestrierungsschritte sollten wie folgt aussehen:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Wiederholen Sie die letzten beiden Schritte für die User Journeys **ProfileEdit** und **PasswordReset**.


## <a name="include-a-claim-in-the-token"></a>Einschließen eines Anspruchs in das Token 

Um den Anspruch `balance` an die Anwendung der vertrauenden Seite zurückzugeben, fügen Sie der Datei <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> einen Ausgabeanspruch hinzu. Durch Hinzufügen eines Ausgabeanspruchs wird der Anspruch nach einer erfolgreichen User Journey in das Token ausgegeben und an die Anwendung gesendet. Ändern Sie im Abschnitt für die vertrauende Seite das Element des technischen Profils, um `balance` als Ausgabeanspruch hinzuzufügen.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Wiederholen Sie diesen Schritt für die User Journeys **ProfileEdit.xml** und **PasswordReset.xml**.

Speichern Sie die Dateien, die Sie geändert haben: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* und *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten enthält, indem Sie im oberen Menü den **Verzeichnis- und Abonnementfilter** und dann das Verzeichnis auswählen, das Ihren Azure AD-Mandanten enthält.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die geänderten Richtliniendateien hoch: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* und *PasswordReset.xml*. 
1. Wählen Sie die hochgeladene Registrierungs- oder Anmelderichtlinie aus, und klicken Sie auf die Schaltfläche **Jetzt ausführen**.
1. Sie sollten sich mit einer E-Mail-Adresse oder einem Facebook-Konto anmelden können.
1. Das Token, das an die Anwendung zurückgesendet wird, enthält den Anspruch `balance`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Schützen Ihrer APIs finden Sie in den folgenden Artikeln:

- [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustausch-Vorgängen in Ihre Azure AD B2C User Journey als Orchestrierungsschritt](custom-policy-rest-api-claims-exchange.md)
- [Schützen Ihrer RESTful-API](secure-rest-api.md)
- [Referenz: Technisches Profil „RESTful“](restful-technical-profile.md)