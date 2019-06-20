---
title: Einrichten der Anmeldung für einen mehrinstanzenfähigen Azure AD-Identitätsanbieter mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Hinzufügen eines mehrinstanzenfähigen Azure AD-Identitätsanbieters mithilfe von benutzerdefinierten Richtlinien – Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 72d01d6927ee421d01a831244acf65c44a084354
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508663"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung für einen mehrinstanzenfähigen Azure Active Directory-Identitätsanbieter mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel wird erläutert, wie Sie die Anmeldung für Benutzer des mehrinstanzenfähigen Endpunkts für Azure Active Directory (Azure AD) mithilfe [benutzerdefinierter Richtlinien](active-directory-b2c-overview-custom.md) in Azure AD B2C aktivieren. Dadurch können Benutzer von mehreren Azure AD-Mandanten sich ohne Konfiguration eines technischen Anbieters für jeden Mandanten bei Azure AD B2C anmelden. Allerdings können sich Gastmitglieder in diesen Mandanten **nicht** anmelden. Dazu müssen Sie [jeden Mandanten einzeln konfigurieren](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>In den folgenden Anweisungen wird `Contoso.com` für den Azure AD-Mandanten der Organisation und `fabrikamb2c.onmicrosoft.com` als Azure AD B2C-Mandant verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) beschriebenen Schritte aus.

## <a name="register-an-application"></a>Registrieren einer Anwendung

Um die Anmeldung für Benutzer von einer bestimmten Azure AD-Organisation zu aktivieren, müssen Sie eine Anwendung im Azure AD-Mandanten der Organisation registrieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das den Azure AD-Mandanten der Organisation („contoso.com“) enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das Verzeichnis auswählen, das Ihren Mandanten enthält.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Registrierung einer neuen Anwendung** aus.
5. Geben Sie einen Namen für Ihre Anwendung ein. Beispiel: `Azure AD B2C App`.
6. Wählen Sie als **Anwendungstyp** die Option `Web app / API` aus.
7. Geben Sie als **Anmelde-URL** die folgende URL in Kleinbuchstaben ein, und ersetzen Sie dabei `your-tenant` durch den Namen Ihres Azure AD B2C-Mandanten („fabrikamb2c.onmicrosoft.com“):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```
    
8. Klicken Sie auf **Create**. Kopieren Sie die **Anwendungs-ID** zur späteren Verwendung.
9. Wählen Sie die Anwendung und dann **Einstellungen** aus.
10. Wählen Sie **Schlüssel** aus, geben Sie die Schlüsselbeschreibung ein, wählen Sie eine Dauer aus, und klicken Sie dann auf **Speichern**. Kopieren Sie den angezeigten Wert des Schlüssels zur späteren Verwendung.
11. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus, legen Sie **Mehrinstanzenfähig** auf `Yes` fest, und klicken Sie dann auf **Speichern**.

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den von Ihnen erstellten Anwendungsschlüssel in Ihrem Azure AD B2C-Mandanten speichern.

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
2. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
3. Wählen Sie auf der Seite „Übersicht“ die Option **Identity Experience Framework – PREVIEW** (Framework für die Identitätsfunktion – VORSCHAU) aus.
4. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
5. Klicken Sie unter **Optionen** auf `Manual`.
6. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `ContosoAppSecret`.  Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
7. Geben Sie im Feld **Geheimnis** den Anwendungsschlüssel ein, den Sie zuvor notiert haben.
8. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
9. Klicken Sie auf **Create**.

## <a name="add-a-claims-provider"></a>Hinzufügen eines Anspruchsanbieters

Wenn Sie möchten, dass sich Benutzer mithilfe von Azure AD anmelden, müssen Sie Azure AD als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde. 

Sie können Azure AD als Anspruchsanbieter definieren, indem Sie Azure AD in der Erweiterungsdatei Ihrer Richtlinie dem Element **ClaimsProvider** hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ändern Sie unter dem Element **ClaimsProvider** den Wert für **Domain** in einen eindeutigen Wert, der eine Unterscheidung von anderen Identitätsanbietern ermöglicht.
5. Aktualisieren Sie unter dem Element **TechnicalProfile** den Wert für **DisplayName**. Dieser Wert wird auf dem Anmeldebildschirm auf der Schaltfläche „Anmelden“ angezeigt.
6. Legen Sie **client_id** auf die Anwendungs-ID aus der Registrierung der mehrinstanzenfähigen Azure AD-App fest.

### <a name="restrict-access"></a>Beschränken des Zugriffs

> [!NOTE]
> Wenn `https://sts.windows.net` als Wert für **ValidTokenIssuerPrefixes** verwendet wird, können sich alle Azure AD-Benutzer bei Ihrer Anwendung anmelden.

Sie müssen die Liste der gültigen Tokenaussteller aktualisieren und den Zugriff auf eine bestimmte Liste von Azure AD-Mandanten beschränken, über die sich Benutzer anmelden können. Zum Abrufen der Werte müssen Sie sich die Metadaten für jeden einzelnen Azure AD-Mandanten ansehen, über den sich die Benutzer anmelden können sollen. Das Format der Daten sieht wie folgt aus: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, wobei `your-tenant` der Name Ihres Azure AD-Mandanten ist („contoso.com“ oder ein anderer Azure AD-Mandant).

1. Öffnen Sie Ihren Browser, navigieren Sie zur **METADATEN**-URL, suchen Sie nach dem **issuer**-Objekt, und kopieren Sie dessen Wert. Es sollte in etwa wie folgt aussehen: `https://sts.windows.net/tenant-id/`.
2. Kopieren Sie den Wert für den Schlüssel **ValidTokenIssuerPrefixes**, und fügen Sie ihn ein. Sie können mehrere Werte hinzufügen, indem Sie diese durch ein Komma trennen. Ein Beispiel dafür ist im obigen XML-Beispiel auskommentiert.

### <a name="upload-the-extension-file-for-verification"></a>Hochladen der Erweiterungsdatei zur Überprüfung

Nun haben Sie Ihre Richtlinie so konfiguriert, dass Azure AD B2C mit Ihrem Azure AD-Verzeichnis kommunizieren kann. Versuchen Sie, die Erweiterungsdatei Ihrer Richtlinie hochzuladen, um sich zu vergewissern, dass soweit keine Probleme vorliegen.

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten auf der Seite **Benutzerdefinierte Richtlinien** die Option **Richtlinie hochladen** aus.
2. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, navigieren Sie dann zur Datei *TrustFrameworkExtensions.xml*, und wählen Sie die Datei aus.
3. Klicken Sie auf **Hochladen**.

## <a name="register-the-claims-provider"></a>Registrieren des Anspruchsanbieters

An dieser Stelle wurde der Identitätsanbieter eingerichtet, ist jedoch auf keinem der Registrierungs-/Anmeldebildschirme vorhanden. Um ihn verfügbar zu machen, erstellen Sie ein Duplikat einer vorhandenen User Journey-Vorlage und ändern diese dann so, dass sie ebenfalls Azure AD als Identitätsanbieter aufweist.

1. Öffnen Sie die Datei *TrustFrameworkBase.xml* aus dem Starter Pack.
2. Suchen und kopieren Sie den gesamten Inhalt des **UserJourney**-Elements, das `Id="SignUpOrSignIn"` enthält.
3. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*, und suchen Sie nach dem **UserJourneys**-Element. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
4. Fügen Sie den gesamten Inhalt des kopierten **UserJourney**-Element als untergeordnetes Element des **UserJourneys**-Elements ein.
5. Benennen Sie die ID der User Journey um. Beispiel: `SignUpSignInContoso`.

### <a name="display-the-button"></a>Anzeigen der Schaltfläche

Das **ClaimsProviderSelection**-Element entspricht einer Schaltfläche für einen Identitätsanbieter auf einem Registrierungs- oder Anmeldebildschirm. Wenn Sie ein **ClaimsProviderSelection**-Element für Azure AD hinzufügen, wird eine neue Schaltfläche angezeigt, wenn ein Benutzer zu der Seite gelangt.

1. Suchen Sie nach dem Element **OrchestrationStep**, das `Order="1"` in der User Journey enthält, die Sie erstellt haben.
2. Fügen Sie unter **ClaimsProviderSelects** das folgende Element hinzu. Legen Sie den Wert von **TargetClaimsExchangeId** auf einen geeigneten Wert (z.B. auf `AzureADExchange`) fest:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Verknüpfen der Schaltfläche mit einer Aktion

Nachdem Sie eine Schaltfläche implementiert haben, müssen Sie sie mit einer Aktion verknüpfen. In diesem Fall soll bei der Aktion Azure AD B2C mit Azure AD kommunizieren, um ein Token zu empfangen. Verknüpfen Sie die Schaltfläche mit einer Aktion, indem Sie das technische Profil für Ihren Azure AD-Anspruchsanbieter verknüpfen.

1. Suchen Sie nach dem Element **OrchestrationStep**, das `Order="2"` in der User Journey enthält.
2. Fügen Sie das folgende **ClaimsExchange**-Element hinzu, um sicherzustellen, dass Sie für **Id** den gleichen Wert verwenden, den Sie für **TargetClaimsExchangeId** verwendet haben:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```
    
    Ändern Sie den Wert von **TechnicalProfileReferenceId** in die **Id** des technischen Profils, das Sie zuvor erstellt haben. Beispiel: `Common-AAD`.

3. Speichern Sie die Datei *TrustFrameworkExtensions.xml*, und laden Sie die Datei zur Überprüfung erneut hoch.

## <a name="create-an-azure-ad-b2c-application"></a>Erstellen einer Azure AD B2C-Anwendung

Die Kommunikation mit Azure AD B2C erfolgt über eine Anwendung, die Sie in Ihrem Mandanten erstellen. In diesem Abschnitt werden optionale Schritte aufgeführt, die Sie ausführen können, um eine Testanwendung zu erstellen, falls Sie dies noch nicht getan haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein, z.B. *testapp1*.
6. Wählen Sie für **Web-App/Web-API** die Option `Yes` aus, und geben Sie dann für die **Antwort-URL** die Zeichenfolge `https://jwt.ms` ein.
7. Klicken Sie auf **Create**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualisieren und Testen der Datei der vertrauenden Seite

Aktualisieren Sie als Nächstes die Datei der vertrauenden Seite, mit der die erstellte User Journey initiiert wird.

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Kopie der Datei *SignUpOrSignIn.xml*, und benennen Sie sie um. Benennen Sie die Datei z. B. in *SignUpSignInContoso.xml* um.
2. Öffnen Sie die neue Datei, und aktualisieren Sie den Wert des Attributs **PolicyId** für **TrustFrameworkPolicy** mit einem eindeutigen Wert. Beispiel: `SignUpSignInContoso`.
3. Aktualisieren Sie den Wert von **PublicPolicyUri** mit dem URI für die Richtlinie. Beispiel: `http://contoso.com/B2C_1A_signup_signin_contoso`
4. Ändern Sie den Wert des Attributs **ReferenceId** im Element **DefaultUserJourney** so, dass er der ID der neuen User Journey entspricht, die Sie erstellt haben (SignUpSignInContoso).
5. Speichern Sie Ihre Änderungen, laden Sie die Datei hoch, und wählen Sie dann in der Liste die neue Richtlinie aus.
6. Stellen Sie sicher, dass die von Ihnen erstellte Azure AD B2C-Anwendung im Feld **Anwendung auswählen** ausgewählt ist, und klicken Sie dann auf **Jetzt ausführen**, um sie zu testen.
