---
title: Einrichten der Anmeldung mit einem Azure Active Directory-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Einrichten der Anmeldung mit einem Azure Active Directory-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9ad4a1a9fff8e011638eb7c532bf7619c87c1996
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785189"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung mit einem Azure Active Directory-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel erfahren Sie, wie Sie die Anmeldung für Benutzer einer Azure Active Directory-Organisation (Azure AD) mittels [benutzerdefinierter Richtlinien](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C aktivieren.

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
7. Geben Sie als **Anmelde-URL** die folgende URL in Kleinbuchstaben ein, und ersetzen Sie dabei `your-B2C-tenant-name` durch den Namen Ihres Azure AD B2C-Mandanten.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Beispiel: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

8. Klicken Sie auf **Create**. Kopieren Sie die **Anwendungs-ID** zur späteren Verwendung.
9. Wählen Sie die Anwendung und dann **Einstellungen** aus.
10. Wählen Sie **Schlüssel** aus, geben Sie die Schlüsselbeschreibung ein, wählen Sie eine Dauer aus, und klicken Sie dann auf **Speichern**. Kopieren Sie den angezeigten Wert des Schlüssels zur späteren Verwendung.

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den von Ihnen erstellten Anwendungsschlüssel in Ihrem Azure AD B2C-Mandanten speichern.

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
2. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
3. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
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
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <OutputTokenFormat>JWT</OutputTokenFormat>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ändern Sie unter dem Element **ClaimsProvider** den Wert für **Domain** in einen eindeutigen Wert, der eine Unterscheidung von anderen Identitätsanbietern ermöglicht. Beispiel: `Contoso`. Am Ende dieser Domäneneinstellung wird nicht `.com` angefügt.
5. Aktualisieren Sie unter dem Element **ClaimsProvider** den Wert für **DisplayName** in einen Anzeigenamen für den Anspruchsanbieter. Dieser Wert wird derzeit nicht verwendet.

### <a name="update-the-technical-profile"></a>Aktualisieren des technischen Profils

Um ein Token vom Azure AD-Endpunkt zu erhalten, müssen Sie die Protokolle definieren, die Azure AD B2C zur Kommunikation mit Azure AD verwenden soll. Dies erfolgt im **TechnicalProfile**-Element von **ClaimsProvider**.

1. Aktualisieren Sie die ID des **TechnicalProfile**-Elements. Diese ID wird als Verweis auf dieses technische Profil aus anderen Teilen der Richtlinie verwendet.
2. Aktualisieren Sie den Wert für **DisplayName**. Dieser Wert wird auf dem Anmeldebildschirm auf der Anmeldeschaltfläche angezeigt.
3. Aktualisieren Sie den Wert von **Beschreibung**.
4. Azure AD verwendet das OpenID Connect-Protokoll. Stellen Sie daher sicher, dass der Wert für **Protokoll** `OpenIdConnect` lautet.
5. Legen Sie den Wert von **METADATA** auf `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration` fest, wobei `your-AD-tenant-name` der Name Ihres Azure AD-Mandanten ist. Zum Beispiel, `https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`
6. Öffnen Sie Ihren Browser, und wechseln Sie zu der URL **METADATA**, die Sie gerade aktualisiert haben, suchen Sie nach dem **issuer**-Objekt, kopieren Sie den Wert, und fügen Sie ihn in den Wert für **ProviderName** in der XML-Datei ein.
8. Legen Sie **client_id** und **IdTokenAudience** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
9. Aktualisieren Sie unter **CryptograhicKeys** den Wert für **StorageReferenceId** auf den Richtlinienschlüssel, den Sie definiert haben. Beispiel: `ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Hochladen der Erweiterungsdatei zur Überprüfung

Nun haben Sie Ihre Richtlinie so konfiguriert, dass Azure AD B2C mit Ihrem Azure AD-Verzeichnis kommunizieren kann. Versuchen Sie, die Erweiterungsdatei Ihrer Richtlinie hochzuladen, um sich zu vergewissern, dass soweit keine Probleme vorliegen.

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten auf der Seite **Benutzerdefinierte Richtlinien** die Option **Richtlinie hochladen** aus.
2. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, navigieren Sie dann zur Datei *TrustFrameworkExtensions.xml*, und wählen Sie die Datei aus.
3. Klicken Sie auf **Hochladen**.

## <a name="register-the-claims-provider"></a>Registrieren des Anspruchsanbieters

An dieser Stelle wurde der Identitätsanbieter eingerichtet, ist jedoch auf keinem der Registrierungs-/Anmeldebildschirme vorhanden. Um ihn verfügbar zu machen, erstellen Sie ein Duplikat einer vorhandenen User Journey-Vorlage und ändern diese dann so, dass sie ebenfalls Azure AD als Identitätsanbieter aufweist:

1. Öffnen Sie die Datei *TrustFrameworkBase.xml* aus dem Starter Pack.
2. Suchen und kopieren Sie den gesamten Inhalt des **UserJourney**-Elements, das `Id="SignUpOrSignIn"` enthält.
3. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*, und suchen Sie nach dem **UserJourneys**-Element. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
4. Fügen Sie den gesamten Inhalt des kopierten **UserJourney**-Element als untergeordnetes Element des **UserJourneys**-Elements ein.
5. Benennen Sie die ID der User Journey um. Beispiel: `SignUpSignInContoso`.

### <a name="display-the-button"></a>Anzeigen der Schaltfläche

Das **ClaimsProviderSelection**-Element entspricht einer Schaltfläche für einen Identitätsanbieter auf einem Registrierungs- oder Anmeldebildschirm. Wenn Sie ein **ClaimsProviderSelection**-Element für Azure AD hinzufügen, wird eine neue Schaltfläche angezeigt, wenn ein Benutzer zu der Seite gelangt.

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="1"` in der User Journey enthält, die Sie erstellt haben.
2. Fügen Sie unter **ClaimsProviderSelections** das folgende Element hinzu. Legen Sie den Wert von **TargetClaimsExchangeId** auf einen geeigneten Wert (z.B. auf `ContosoExchange`) fest:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Verknüpfen der Schaltfläche mit einer Aktion

Nachdem Sie eine Schaltfläche implementiert haben, müssen Sie sie mit einer Aktion verknüpfen. In diesem Fall soll bei der Aktion Azure AD B2C mit Azure AD kommunizieren, um ein Token zu empfangen. Dies gelingt Ihnen, indem Sie das technische Profil für Ihren Azure AD-Anspruchsanbieter verknüpfen:

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="2"` in der User Journey enthält.
2. Fügen Sie das folgende **ClaimsExchange**-Element hinzu, um sicherzustellen, dass Sie für **Id** den gleichen Wert verwenden, den Sie für **TargetClaimsExchangeId** verwendet haben:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```
    
    Ändern Sie den Wert von **TechnicalProfileReferenceId** in die **Id** des technischen Profils, das Sie zuvor erstellt haben. Beispiel: `ContosoProfile`.

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

