---
title: „Angemeldet bleiben“ in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Option „Angemeldet bleiben“ in Azure Active Directory B2C festlegen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0ae6f19ea9a04aa6b2547fa031dbb09d03b887c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509430"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Aktivieren von „Angemeldet bleiben“ in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Sie können die Funktion „Angemeldet bleiben“ für Ihre Webanwendungen und nativen Anwendungen in Azure Active Directory (Azure AD) B2C aktivieren. Dieses Feature gewährt wiederkehrenden Benutzern Zugriff auf Anwendungen, ohne dass sie dazu aufgefordert werden, ihren Benutzernamen und das zugehörige Kennwort erneut einzugeben. Dieser Zugriff wird widerrufen, wenn sich der Benutzer abmeldet. 

Benutzer sollten diese Option nicht auf öffentlichen Computern aktivieren. 

![Aktivieren des Angemeldetbleibens](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure AD B2C-Mandant ist zur Zulassung der Registrierung/Anmeldung für ein lokales Konto konfiguriert. Falls Sie noch keinen Mandanten besitzen, können Sie zum Erstellen die Schritte unter [Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten](tutorial-create-tenant.md) ausführen.

## <a name="add-a-content-definition-element"></a>Hinzufügen eines Inhaltsdefinitionselements 

Fügen Sie unter dem Element **BuildingBlocks** Ihrer Erweiterungsdatei ein Element **ContentDefinitions** hinzu. 

1. Fügen Sie unter dem Element **ContentDefinitions** ein Element **ContentDefinition** mit dem Bezeichner `api.signuporsigninwithkmsi` hinzu.
2. Fügen Sie unter dem Element **ContentDefinition** die Elemente **LoadUri**, **RecoveryUri** und **DataUri** hinzu. Der `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0`-Wert des Elements **DataUri** ist ein maschinenlesbarer Bezeichner, durch den auf den Anmeldeseiten ein Kontrollkästchen „Angemeldet bleiben“ angezeigt wird. Dieser Wert darf nicht geändert werden. 

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>                       
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Hinzufügen eines Anspruchsanbieters für die Anmeldung für ein lokales Konto  

Sie können die Anmeldung mit einem lokalen Konto als Anspruchsanbieter mit dem Element **ClaimsProvider** in der Erweiterungsdatei Ihrer Richtlinie definieren:

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml* aus Ihrem Arbeitsverzeichnis. 
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu. Das [Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) enthält einen Anspruchsanbieter für die Anmeldung mit einem lokalen Konto. 
3. Fügen Sie wie im folgenden Beispiel gezeigt ein Element **ClaimsProvider** mit **DisplayName** und **TechnicalProfile** hinzu:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Hinzufügen der Anwendungsbezeichner zu Ihrer benutzerdefinierten Richtlinie

Fügen Sie die Anwendungsbezeichner zur Datei *TrustFrameworkExtensions.xml* hinzu.

1. Suchen Sie in der Datei *TrustFrameworkExtensions.xml* nach dem Element **TechnicalProfile** mit dem Bezeichner `login-NonInteractive` und nach dem Element **TechnicalProfile** mit dem Bezeichner `login-NonInteractive-PasswordChange`. Ersetzen Sie dann alle Werte von `IdentityExperienceFrameworkAppId` durch den Anwendungsbezeichner der Identity Experience Framework-Anwendung gemäß Beschreibung in [Erste Schritte](active-directory-b2c-get-started-custom.md).

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Ersetzen Sie alle Werte von `ProxyIdentityExperienceFrameworkAppId` durch den Anwendungsbezeichner der Proxy Identity Experience Framework-Anwendung, wie unter [Erste Schritte](active-directory-b2c-get-started-custom.md) beschrieben.
3. Speichern Sie die Erweiterungsdatei.

## <a name="create-a-kmsi-enabled-user-journey"></a>Erstellen einer User Journey mit aktivierter Option „Angemeldet bleiben“

Fügen Sie den Anspruchsanbieter für die Anmeldung für ein lokales Konto zu Ihrer User Journey hinzu. 

1. Öffnen Sie die Basisdatei Ihrer Richtlinie. Öffnen Sie z.B. *TrustFrameworkBase.xml*.
2. Suchen Sie nach dem Element **UserJourneys**, und kopieren Sie den gesamten Inhalt des Elements **UserJourney**, das den Bezeichner `SignUpOrSignIn` verwendet.
3. Öffnen Sie die Erweiterungsdatei. Öffnen Sie z.B. *TrustFrameworkExtensions.xml*, und suchen Sie nach dem Element **UserJourneys**. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
4. Fügen Sie das gesamte kopierte Element **UserJourney** als untergeordnetes Objekt des Elements **UserJourneys** ein.
5. Ändern Sie den Wert des Bezeichners für die neue User Journey. Beispiel: `SignUpOrSignInWithKmsi`.
6. Ändern Sie abschließend im ersten Orchestrierungsschritt den Wert von **ContentDefinitionReferenceId** in `api.signuporsigninwithkmsi`. Durch das Festlegen dieses Werts wird das Kontrollkästchen in der User Journey aktiviert. 
7. Speichern Sie die Erweiterungsdatei, und laden Sie sie hoch. Stellen Sie sicher, dass alle Validierungen erfolgreich waren.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Erstellen einer Datei der vertrauenden Seite

Aktualisieren Sie als Nächstes die Datei der vertrauenden Seite, mit der die erstellte User Journey initiiert wird.

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Kopie der Datei *SignUpOrSignIn.xml*, und benennen Sie sie um. Geben Sie der Datei beispielsweise den Namen *SignUpOrSignInWithKmsi.xml*.
2. Öffnen Sie die neue Datei, und aktualisieren Sie das Attribut **PolicyId** für **TrustFrameworkPolicy** mit einem eindeutigen Wert. Dies ist der Name Ihrer Richtlinie. Beispiel: `SignUpOrSignInWithKmsi`.
3. Ändern Sie das Attribut **ReferenceId** für das Element **DefaultUserJourney**, damit es dem Bezeichner der neuen User Journey entspricht, die Sie erstellt haben. Beispiel: `SignUpOrSignInWithKmsi`.

    KMSI wird mithilfe des Elements **UserJourneyBehaviors** mit **SingleSignOn**, **SessionExpiryType** und **SessionExpiryInSeconds** als erste untergeordnete Elemente konfiguriert. Das Attribut **KeepAliveInDays** steuert, wie lange der Benutzer angemeldet bleibt. Im folgenden Beispiel läuft die Sitzung „Angemeldet bleiben“ nach `7` Tagen automatisch ab – unabhängig davon, wie häufig der Benutzer die Authentifizierung im Hintergrund ausführt. Durch Festlegen des Werts **KeepAliveInDays** auf `0` wird die Funktionalität „Angemeldet bleiben“ deaktiviert. Standardmäßig ist dieser Wert auf `0` festgelegt. Wenn **SessionExpiryType** auf den Wert `Rolling` festgelegt ist, wird die Sitzung „Angemeldet bleiben“ jedes Mal um `7` Tage erweitert, wenn der Benutzer die Authentifizierung im Hintergrund durchführt.  Bei Auswahl von `Rolling` sollten Sie die Anzahl von Tagen auf ein Minimum beschränken. 

    Der Wert von **SessionExpiryInSeconds** repräsentiert die Ablaufzeit einer SSO-Sitzung. Anhand dieses Werts wird intern von Azure AD B2C überprüft, ob die Sitzung für „Angemeldet bleiben“ abgelaufen ist oder nicht. Der Wert von **KeepAliveInDays** bestimmt den Expires/Max-Age-Wert des SSO-Cookies im Webbrowser. Im Gegensatz zu **SessionExpiryInSeconds** wird **KeepAliveInDays** verwendet, um den Browser daran zu hindern, das Cookie zu löschen, wenn der Browser geschlossen wird. Ein Benutzer kann nur dann eine Anmeldung im Hintergrund durchführen, wenn das SSO-Sitzungscookie vorhanden (per **KeepAliveInDays** gesteuert) und nicht abgelaufen ist (per **SessionExpiryInSeconds** gesteuert). 
    
    Wenn ein Benutzer auf der Seite für die Registrierung und Anmeldung die Option **Angemeldet bleiben** nicht aktiviert, läuft eine Sitzung ab, nachdem der unter **SessionExpiryInSeconds** angegebene Zeitraum abgelaufen ist oder wenn der Browser geschlossen wird. Falls ein Benutzer die Option **Angemeldet bleiben** aktiviert, überschreibt der Wert von **KeepAliveInDays** den Wert von **SessionExpiryInSeconds** und gibt den Ablaufzeitpunkt für die Sitzung vor. Auch wenn Benutzer den Browser schließen und dann erneut öffnen, ist die Anmeldung im Hintergrund möglich, solange dies innerhalb des Zeitraums von **KeepAliveInDays** erfolgt. Es wird empfohlen, den Wert von **SessionExpiryInSeconds** auf einen kurzen Zeitraum (1.200 Sekunden) und den Wert von **KeepAliveInDays** auf einen relativ langen Zeitraum (7 Tage) festzulegen. Dies ist im folgenden Beispiel dargestellt:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Speichern Sie Ihre Änderungen, und laden Sie die Datei dann hoch.
5. Um die benutzerdefinierte Richtlinie zu testen, die Sie hochgeladen haben, wechseln Sie im Azure-Portal zur Richtlinienseite, und klicken Sie dann auf **Jetzt ausführen**.

Die Beispielrichtlinie finden Sie [hier](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








