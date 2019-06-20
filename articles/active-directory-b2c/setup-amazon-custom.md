---
title: Einrichten der Anmeldung mit einem Amazon-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Einrichten der Anmeldung mit einem Amazon-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1fcac4bcfb5cd37ddf8b351514c8f4f1622367c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512578"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung mit einem Amazon-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel erfahren Sie, wie Sie die Anmeldung für Benutzer über ein Amazon-Konto mithilfe [benutzerdefinierter Richtlinien](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie die Schritte unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) aus.
- Erstellen Sie ein Amazon-Konto unter [https://www.amazon.com/](https://www.amazon.com/), sofern Sie noch keines besitzen.

## <a name="register-the-application"></a>Registrieren der Anwendung

Um das einmalige Anmelden für Benutzer mit einem Amazon-Konto zu aktivieren, müssen Sie eine Amazon-Anwendung erstellen.

1. Melden Sie sich beim [Amazon Developer Center](https://login.amazon.com/) mit den Anmeldeinformationen für Ihr Amazon-Konto an.
2. Wenn Sie dies nicht bereits getan haben, klicken Sie auf **Sign Up**, führen Sie die Schritte zur Registrierung von Entwicklern aus, und akzeptieren Sie die Richtlinie.
3. Wählen Sie **Register new application** (Neue Anwendung registrieren) aus.
4. Geben Sie einen **Namen**, unter **Description** eine Beschreibung und unter **Privacy Notice URL** eine URL für den Datenschutzhinweis ein, und klicken Sie dann auf **Save** (Speichern). Die Seite „Datenschutzhinweise“ wird von Ihnen verwaltet und bietet Benutzern Informationen zum Datenschutz.
5. Kopieren Sie im Abschnitt **Web Settings** (Webeinstellungen) den Wert für **Client ID** (Client-ID). Wählen Sie **Show Secret** (Geheimnis anzeigen) aus, um den geheimen Clientschlüssel abzurufen, und kopieren Sie ihn dann. Sie benötigen beide Angaben, um ein Amazon-Konto als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. **geheime Clientschlüssel** ist eine wichtige Sicherheitsanmeldeinformation.
6. Wählen Sie im Abschnitt **Web Settings** (Webeinstellungen) die Option **Edit** (Bearbeiten) aus, und geben Sie dann `https://your-tenant-name.b2clogin.com` unter **Allowed JavaScript Origins** (Zulässige JavaScript-Quellen) und `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **Allowed Return URLs** (Zulässige Rückgabe-URIs) ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten. Verwenden Sie bei der Eingabe Ihres Mandantennamens nur Kleinbuchstaben, auch wenn der Mandant in Azure AD B2C Großbuchstaben enthält.
7. Klicken Sie auf **Speichern**.

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie auf der Seite „Übersicht“ die Option **Identity Experience Framework – PREVIEW** (Framework für die Identitätsfunktion – VORSCHAU) aus.
5. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
6. Klicken Sie unter **Optionen** auf `Manual`.
7. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `AmazonSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
9. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
10. Klicken Sie auf **Create**.

## <a name="add-a-claims-provider"></a>Hinzufügen eines Anspruchsanbieters

Wenn Sie möchten, dass sich Benutzer mit einem Amazon-Konto anmelden, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde. 

Sie können ein Amazon-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.


1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:  

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
5. Speichern Sie die Datei .

### <a name="upload-the-extension-file-for-verification"></a>Hochladen der Erweiterungsdatei zur Überprüfung

Nun haben Sie Ihre Richtlinie so konfiguriert, dass Azure AD B2C mit Ihrem Azure AD-Verzeichnis kommunizieren kann. Versuchen Sie, die Erweiterungsdatei Ihrer Richtlinie hochzuladen, um sich zu vergewissern, dass soweit keine Probleme vorliegen.

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten auf der Seite **Benutzerdefinierte Richtlinien** die Option **Richtlinie hochladen** aus.
2. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, navigieren Sie dann zur Datei *TrustFrameworkExtensions.xml*, und wählen Sie die Datei aus.
3. Klicken Sie auf **Hochladen**.

## <a name="register-the-claims-provider"></a>Registrieren des Anspruchsanbieters

An dieser Stelle wurde der Identitätsanbieter eingerichtet, ist jedoch auf keinem der Registrierungs-/Anmeldebildschirme vorhanden. Um ihn verfügbar zu machen, erstellen Sie ein Duplikat einer vorhandenen User Journey-Vorlage und ändern diese dann so, dass sie ebenfalls das Amazon-Konto als Identitätsanbieter aufweist.

1. Öffnen Sie die Datei *TrustFrameworkBase.xml* aus dem Starter Pack.
2. Suchen und kopieren Sie den gesamten Inhalt des **UserJourney**-Elements, das `Id="SignUpOrSignIn"` enthält.
3. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*, und suchen Sie nach dem **UserJourneys**-Element. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
4. Fügen Sie den gesamten Inhalt des kopierten **UserJourney**-Element als untergeordnetes Element des **UserJourneys**-Elements ein.
5. Benennen Sie die ID der User Journey um. Beispiel: `SignUpSignInAmazon`.

### <a name="display-the-button"></a>Anzeigen der Schaltfläche

Das **ClaimsProviderSelection**-Element entspricht einer Schaltfläche für einen Identitätsanbieter auf einem Registrierungs- oder Anmeldebildschirm. Wenn Sie ein **ClaimsProviderSelection**-Element für ein Amazon-Konto hinzufügen, wird eine neue Schaltfläche angezeigt, wenn ein Benutzer zu der Seite gelangt.

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="1"` in der User Journey enthält, die Sie erstellt haben.
2. Fügen Sie unter **ClaimsProviderSelects** das folgende Element hinzu. Legen Sie den Wert von **TargetClaimsExchangeId** auf einen geeigneten Wert (z.B. auf `AmazonExchange`) fest:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Verknüpfen der Schaltfläche mit einer Aktion

Nachdem Sie eine Schaltfläche implementiert haben, müssen Sie sie mit einer Aktion verknüpfen. In diesem Fall soll bei der Aktion Azure AD B2C mit einem Amazon-Konto kommunizieren, um ein Token zu empfangen.

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="2"` in der User Journey enthält.
2. Fügen Sie das folgende **ClaimsExchange**-Element hinzu, und stellen Sie dabei sicher, dass Sie denselben Wert für die ID verwenden, den Sie für **TargetClaimsExchangeId** verwendet haben:

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```
    
    Ändern Sie den Wert von **TechnicalProfileReferenceId** in die ID des technischen Profils, das Sie zuvor erstellt haben. Beispiel: `Amazon-OAuth`.

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

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Kopie der Datei *SignUpOrSignIn.xml*, und benennen Sie sie um. Benennen Sie die Datei z.B. in *SignUpSignInAmazon.xml* um.
2. Öffnen Sie die neue Datei, und aktualisieren Sie den Wert des Attributs **PolicyId** für **TrustFrameworkPolicy** mit einem eindeutigen Wert. Beispiel: `SignUpSignInAmazon`.
3. Aktualisieren Sie den Wert von **PublicPolicyUri** mit dem URI für die Richtlinie. Beispiel: `http://contoso.com/B2C_1A_signup_signin_amazon`
4. Ändern Sie den Wert des Attributs **ReferenceId** im **DefaultUserJourney**-Element so, dass er der ID der neuen User Journey entspricht, die Sie erstellt haben (SignUpSignInAmazon).
5. Speichern Sie Ihre Änderungen, laden Sie die Datei hoch, und wählen Sie dann in der Liste die neue Richtlinie aus.
6. Stellen Sie sicher, dass die von Ihnen erstellte Azure AD B2C-Anwendung im Feld **Anwendung auswählen** ausgewählt ist, und klicken Sie dann auf **Jetzt ausführen**, um sie zu testen.
