---
title: Registrieren und Anmelden mit einem Salesforce-SAML-Anbieter mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Registrieren und Anmelden mit einem Salesforce-SAML-Anbieter mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e565822c006191615dbc10b980da24dcd9ed787a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508300"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Registrieren und Anmelden mit einem Salesforce-SAML-Anbieter mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel erfahren Sie, wie Sie die Anmeldung für Benutzer aus einer Salesforce-Organisation mithilfe [benutzerdefinierter Richtlinien](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C aktivieren. Sie ermöglichen die Anmeldung, indem Sie einer benutzerdefinierten Richtlinie ein [technisches SAML-Profil](saml-technical-profile.md) hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie die unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) beschriebenen Schritte aus.
- Falls Sie dies noch nicht getan haben, registrieren Sie sich für ein [kostenloses Developer Edition-Konto](https://developer.salesforce.com/signup). In diesem Artikel wird die [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ) verwendet.
- [Einrichtung einer eigenen Domäne](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) für Ihre Salesforce-Organisation.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Einrichten von Salesforce als Identitätsanbieter

1. [Anmelden Sie sich bei Salesforce an](https://login.salesforce.com/). 
2. Erweitern Sie im Menü auf der linken Seite unter **Einstellungen** die Option **Identität**, und wählen Sie dann **Identitätsanbieter** aus.
3. Wählen Sie **Enable Identity Provider** (Identitätsanbieter aktivieren) aus.
4. Wählen Sie unter **Select the certificate** (Zertifikat auswählen) das Zertifikat aus, das Salesforce für die Kommunikation mit Azure AD B2C verwendet soll. Sie können das Standardzertifikat verwenden. 
5. Klicken Sie auf **Speichern**. 

### <a name="create-a-connected-app-in-salesforce"></a>Erstellen einer verbundenen App in Salesforce

1. Wählen Sie auf der Seite **Identitätsanbieter** folgende Option aus: **Service Providers are now created via Connected Apps. Click here.** (Dienstanbieter werden jetzt mit verbundenen Apps erstellt. Hier klicken.).
2. Geben Sie unter **Grundlegende Informationen** die erforderlichen Werte für die verbundene App ein.
3. Aktivieren Sie unter **Web-App-Einstellungen** das Kontrollkästchen **SAML aktivieren**.
4. Geben Sie im Feld **Entitäts-ID** die folgende URL ein. Ersetzen Sie den Wert von `your-tenant` durch den Namen Ihres Azure AD B2C-Mandanten.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Geben Sie im Feld **ACS-URL** die folgende URL ein. Ersetzen Sie den Wert von `your-tenant` durch den Namen Ihres Azure AD B2C-Mandanten.
      
      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Scrollen Sie zum Ende der Liste, und klicken Sie dann auf **Speichern**.

### <a name="get-the-metadata-url"></a>Abrufen der Metadaten-URL

1. Klicken Sie auf der Übersichtsseite der verbundenen App auf **Verwalten**.
2. Kopieren Sie den Wert für **Metadata Discovery Endpoint** (Metadaten-Ermittlungsendpunkt), und speichern Sie ihn. Sie werden ihn später in diesem Artikel brauchen.

### <a name="set-up-salesforce-users-to-federate"></a>Einrichten von Salesforce-Benutzern für einen Verbund

1. Klicken Sie auf der Seite **Verwalten** der verbundenen App auf **Profile verwalten**.
2. Wählen Sie die Profile (oder Benutzergruppen) aus, für die ein Verbund mit Azure AD B2C hergestellt werden soll. Wählen Sie als Systemadministrator das Kontrollkästchen **Systemadministrator** aus, damit Sie den Verbund mit Ihrem Salesforce-Konto herstellen können.

## <a name="generate-a-signing-certificate"></a>Generieren eines Signaturzertifikats

An Salesforce gesendete Anforderungen müssen von Azure AD B2C signiert worden sein. Um ein Signaturzertifikat zu generieren, öffnen Sie Azure PowerShell, und führen Sie die folgenden Befehle.

> [!NOTE]
> Stellen Sie sicher, dass Sie den Namen des Mandanten und das Kennwort in den oberen beiden Zeilen aktualisieren.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen das erstellte Zertifikat in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie auf der Seite „Übersicht“ die Option **Identity Experience Framework – PREVIEW** (Framework für die Identitätsfunktion – VORSCHAU) aus.
5. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
6. Klicken Sie unter **Optionen** auf `Upload`.
7. Geben Sie einen **Namen** für die Richtlinie ein. Beispiel: SAMLSigningCert. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Navigieren Sie zum Zertifikat „B2CSigningCert.pfx“, das Sie erstellt haben, und wählen Sie es aus. 
9. Geben Sie das **Kennwort** für das Zertifikat ein.
3. Klicken Sie auf **Create**.

## <a name="add-a-claims-provider"></a>Hinzufügen eines Anspruchsanbieters

Wenn Sie möchten, dass sich Benutzer mit einem Salesforce-Konto anmelden, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde. 

Sie können ein Salesforce-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ändern Sie den Wert von **PartnerEntity** in die Salesforce-Metadaten-URL, die Sie zuvor kopiert haben.
5. Ändern Sie den Wert beider Instanzen von **StorageReferenceId** in den Namen des Schlüssels Ihres Signaturzertifikats. Beispiel: B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Hochladen der Erweiterungsdatei zur Überprüfung

Nun haben Sie Ihre Richtlinie so konfiguriert, dass Azure AD B2C mit Ihrem Salesforce-Konto kommunizieren kann. Versuchen Sie, die Erweiterungsdatei Ihrer Richtlinie hochzuladen, um sich zu vergewissern, dass soweit keine Probleme vorliegen.

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten auf der Seite **Benutzerdefinierte Richtlinien** die Option **Richtlinie hochladen** aus.
2. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, navigieren Sie dann zur Datei *TrustFrameworkExtensions.xml*, und wählen Sie die Datei aus.
3. Klicken Sie auf **Hochladen**.

## <a name="register-the-claims-provider"></a>Registrieren des Anspruchsanbieters

Der Identitätsanbieter wurde nun eingerichtet, aber er ist auf keinem der Registrierungs- oder Anmeldebildschirme vorhanden. Um ihn verfügbar zu machen, erstellen Sie ein Duplikat einer vorhandenen User Journey-Vorlage und ändern diese dann so, dass sie ebenfalls das Salesforce-Konto als Identitätsanbieter aufweist.

1. Öffnen Sie die Datei *TrustFrameworkBase.xml* aus dem Starter Pack.
2. Suchen und kopieren Sie den gesamten Inhalt des **UserJourney**-Elements, das `Id="SignUpOrSignIn"` enthält.
3. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*, und suchen Sie nach dem **UserJourneys**-Element. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
4. Fügen Sie den gesamten Inhalt des kopierten **UserJourney**-Element als untergeordnetes Element des **UserJourneys**-Elements ein.
5. Benennen Sie die ID der User Journey um. Beispiel: `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Anzeigen der Schaltfläche

Das **ClaimsProviderSelection**-Element entspricht einer Schaltfläche für einen Identitätsanbieter auf einem Registrierungs- oder Anmeldebildschirm. Wenn Sie ein **ClaimsProviderSelection**-Element für ein LinkedIn-Konto hinzufügen, wird eine neue Schaltfläche angezeigt, wenn ein Benutzer zu der Seite gelangt.

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="1"` in der User Journey enthält, die Sie gerade erstellt haben.
2. Fügen Sie unter **ClaimsProviderSelects** das folgende Element hinzu. Legen Sie den Wert von **TargetClaimsExchangeId** auf einen geeigneten Wert (z.B. auf `SalesforceExchange`) fest:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Verknüpfen der Schaltfläche mit einer Aktion

Nachdem Sie eine Schaltfläche implementiert haben, müssen Sie sie mit einer Aktion verknüpfen. In diesem Fall soll bei der Aktion Azure AD B2C mit dem Salesforce-Konto kommunizieren, um ein Token zu empfangen.

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="2"` in der User Journey enthält.
2. Fügen Sie das folgende **ClaimsExchange**-Element hinzu, um sicherzustellen, dass Sie für **Id** den gleichen Wert verwenden, den Sie für **TargetClaimsExchangeId** verwendet haben:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```
    
    Ändern Sie den Wert von **TechnicalProfileReferenceId** in die **Id** des technischen Profils, das Sie zuvor erstellt haben. Beispiel: `LinkedIn-OAUTH`.

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

Aktualisieren Sie als Nächstes die Datei der vertrauenden Seite, mit der die gerade erstellte User Journey initiiert wird:

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Kopie der Datei *SignUpOrSignIn.xml*, und benennen Sie sie um. Benennen Sie die Datei z.B. in *SignUpSignInSalesforce.xml* um.
2. Öffnen Sie die neue Datei, und aktualisieren Sie den Wert des Attributs **PolicyId** für **TrustFrameworkPolicy** mit einem eindeutigen Wert. Beispiel: `SignUpSignInSalesforce`.
3. Aktualisieren Sie den Wert von **PublicPolicyUri** mit dem URI für die Richtlinie. Beispiel: `http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Ändern Sie den Wert des Attributs **ReferenceId** im **DefaultUserJourney**-Element so, dass er der ID der neuen User Journey entspricht, die Sie erstellt haben (SignUpSignInSalesforce).
5. Speichern Sie Ihre Änderungen, laden Sie die Datei hoch, und wählen Sie dann in der Liste die neue Richtlinie aus.
6. Stellen Sie sicher, dass die von Ihnen erstellte Azure AD B2C-Anwendung im Feld **Anwendung auswählen** ausgewählt ist, und klicken Sie dann auf **Jetzt ausführen**, um sie zu testen.
