---
title: Hinzufügen von AD FS als SAML-Identitätsanbieter mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Einrichten von AD FS 2016 mit dem SAML-Protokoll und benutzerdefinierten Richtlinien in Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: df4acf72a1a6ea134e1192512fda1d8cf1e92f0a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65767989"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Hinzufügen von AD FS als SAML-Identitätsanbieter mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel erfahren Sie, wie Sie die Anmeldung für ein AD FS-Benutzerkonto mithilfe [benutzerdefinierter Richtlinien](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C aktivieren. Sie ermöglichen die Anmeldung, indem Sie einer benutzerdefinierten Richtlinie ein [technisches SAML-Profil](saml-technical-profile.md) hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie die unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) beschriebenen Schritte aus.
- Vergewissern Sie sich, dass Sie Zugriff auf die PFX-Zertifikatsdatei mit einem privaten Schlüssel haben. Sie können ein eigenes signiertes Zertifikat generieren und in Azure AD B2C hochladen. Azure AD B2C verwendet dieses Zertifikat zum Signieren der SAML-Anforderung, die an Ihren SAML-Identitätsanbieter gesendet wird.

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen Ihr Zertifikat in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Klicken Sie im oberen Menü auf **Verzeichnis- und Abonnementfilter**, und wählen Sie das Verzeichnis aus, das Ihren Mandanten enthält.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
5. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
6. Klicken Sie unter **Optionen** auf `Upload`.
7. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `SamlCert`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Navigieren Sie zur PFX-Datei Ihres Zertifikats mit dem privaten Schlüssel, und wählen Sie sie aus.
9. Klicken Sie auf **Create**.

## <a name="add-a-claims-provider"></a>Hinzufügen eines Anspruchsanbieters

Wenn Sie möchten, dass sich Benutzer mit einem AD FS-Konto anmelden, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde. 

Sie können ein AD FS-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

4. Ersetzen Sie `your-ADFS-domain` durch den Namen Ihrer AD FS-Domäne und den Wert des **identityProvider**-Ausgabeanspruchs durch Ihren DNS (beliebiger Wert, der Ihre Domäne angibt).
5. Speichern Sie die Datei .

### <a name="upload-the-extension-file-for-verification"></a>Hochladen der Erweiterungsdatei zur Überprüfung

Nun haben Sie Ihre Richtlinie so konfiguriert, dass Azure AD B2C mit Ihrem AD FS-Konto kommunizieren kann. Versuchen Sie, die Erweiterungsdatei Ihrer Richtlinie hochzuladen, um sich zu vergewissern, dass soweit keine Probleme vorliegen.

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten auf der Seite **Benutzerdefinierte Richtlinien** die Option **Richtlinie hochladen** aus.
2. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, navigieren Sie dann zur Datei *TrustFrameworkExtensions.xml*, und wählen Sie die Datei aus.
3. Klicken Sie auf **Hochladen**.

## <a name="register-the-claims-provider"></a>Registrieren des Anspruchsanbieters

Der Identitätsanbieter wurde nun eingerichtet, aber er ist auf keinem der Registrierungs- oder Anmeldebildschirme vorhanden. Um ihn verfügbar zu machen, erstellen Sie ein Duplikat einer vorhandenen User Journey-Vorlage und ändern diese dann so, dass sie ebenfalls das AD FS-Konto als Identitätsanbieter aufweist.

1. Öffnen Sie die Datei *TrustFrameworkBase.xml* aus dem Starter Pack.
2. Suchen und kopieren Sie den gesamten Inhalt des **UserJourney**-Elements, das `Id="SignUpOrSignIn"` enthält.
3. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*, und suchen Sie nach dem **UserJourneys**-Element. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
4. Fügen Sie den gesamten Inhalt des kopierten **UserJourney**-Element als untergeordnetes Element des **UserJourneys**-Elements ein.
5. Benennen Sie die ID der User Journey um. Beispiel: `SignUpSignInADFS`.

### <a name="display-the-button"></a>Anzeigen der Schaltfläche

Das **ClaimsProviderSelection**-Element entspricht einer Schaltfläche für einen Identitätsanbieter auf einem Registrierungs- oder Anmeldebildschirm. Wenn Sie ein **ClaimsProviderSelection**-Element für ein AD FS-Konto hinzufügen, wird eine neue Schaltfläche angezeigt, wenn ein Benutzer zu der Seite gelangt.

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="1"` in der User Journey enthält, die Sie erstellt haben.
2. Fügen Sie unter **ClaimsProviderSelections** das folgende Element hinzu. Legen Sie den Wert von **TargetClaimsExchangeId** auf einen geeigneten Wert (z.B. auf `ContosoExchange`) fest:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Verknüpfen der Schaltfläche mit einer Aktion

Nachdem Sie eine Schaltfläche implementiert haben, müssen Sie sie mit einer Aktion verknüpfen. In diesem Fall soll bei der Aktion Azure AD B2C mit einem AD FS-Konto kommunizieren, um ein Token zu empfangen.

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="2"` in der User Journey enthält.
2. Fügen Sie das folgende **ClaimsExchange**-Element hinzu, und stellen Sie dabei sicher, dass Sie denselben Wert für die ID verwenden, den Sie für **TargetClaimsExchangeId** verwendet haben:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Ändern Sie den Wert von **TechnicalProfileReferenceId** in die ID des technischen Profils, das Sie zuvor erstellt haben. Beispiel: `Contoso-SAML2`.

3. Speichern Sie die Datei *TrustFrameworkExtensions.xml*, und laden Sie die Datei zur Überprüfung erneut hoch.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurieren einer AD FS-Vertrauensstellung der vertrauenden Seite

Um AD FS als Identitätsanbieter in Azure AD B2C verwenden zu können, müssen Sie eine AD FS-Vertrauensstellung der vertrauenden Seite mit den SAML-Metadaten von Azure AD B2C erstellen. Das folgende Beispiel zeigt eine URL der SAML-Metadaten eines technischen Azure AD B2C-Profils:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersetzen Sie die folgenden Werte:

- **your-tenant** durch Ihren Mandantennamen, z.B. „ihr-mandant.onmicrosoft.com“
- **your-policy** durch den Namen Ihrer Richtlinie. Beispiel: B2C_1A_signup_signin_adfs.
- **your-technical-profile** durch den Namen des technische Profil des SAML-Identitätsanbieters. Beispiel: Contoso-SAML2
 
Öffnen Sie einen Browser, und browsen Sie zu dieser URL. Stellen Sie sicher, dass Sie die richtige URL eingeben und dass Sie Zugriff auf die XML-Metadatendatei haben. Um eine neue Vertrauensstellung der vertrauenden Seite mithilfe des AD FS-Verwaltungs-Snap-Ins hinzuzufügen und die Einstellungen manuell zu konfigurieren, führen Sie das folgende Verfahren auf einem Verbundserver aus. Eine Mitgliedschaft in **Administratoren** oder Entsprechendem auf dem lokalen Computer ist die Mindestvoraussetzung, um dieses Verfahren abzuschließen.

1. Wählen Sie im Server-Manager **Tools** und dann **AD FS-Verwaltung** aus.
2. Wählen Sie **Hinzufügen der Vertrauensstellung der vertrauenden Seite**.
3. Wählen Sie auf der Seite **Willkommen** die Option **Ansprüche unterstützend** aus, und klicken Sie dann auf **Start**.
4. Wählen Sie auf der Seite **Datenquelle auswählen** die Option **Import data about the relying party publish online or on a local network** (Daten über die vertrauende Seite für die Veröffentlichung online oder in einem lokalen Netzwerk importieren) aus, geben Sie Ihre Azure AD B2C-Metadaten-URL an, und klicken Sie dann auf **Weiter**.
5. Geben Sie auf der Seite **Anzeigename angeben** einen Namen in **Anzeigename** und unter **Hinweise** eine Beschreibung für diese Vertrauensstellung der vertrauenden Seite ein, und klicken Sie dann auf **Weiter**.
6. Wählen Sie auf der Seite **Zugriffssteuerungsrichtlinie auswählen** eine Richtlinie aus, und klicken Sie dann auf **Weiter**.
7. Überprüfen Sie die Einstellungen auf der Seite **Bereit zum Hinzufügen der Vertrauensstellung**, und klicken Sie dann auf **Weiter**, um die Daten Ihrer Vertrauensstellung der vertrauenden Seite zu speichern.
8. Klicken Sie auf der Seite **Fertig stellen** auf **Schließen**. Diese Aktion zeigt automatisch das Dialogfeld **Anspruchsregeln bearbeiten** an.
9. Wählen Sie **Regel hinzufügen** aus.  
10. Wählen Sie in **Anspruchsregelvorlage** die Option **Senden von LDAP-Attributen als Ansprüche** aus.
11. Geben Sie einen **Anspruchsregelnamen** an. Wählen Sie als **Attributspeicher** **Active Directory** aus. Fügen Sie die folgenden Ansprüche hinzu, und klicken Sie auf **Fertig stellen** und **OK**.

    | LDAP-Attribut | Typ des ausgehenden Anspruchs |
    | -------------- | ------------------- |
    | Benutzerprinzipalname | userPrincipalName |
    | Surname | family_name |
    | Vorname | given_name |
    | E-Mail-Adresse | email |
    | Anzeigename | name |
    
12.  Basierend auf Ihrem Zertifikattyp müssen Sie möglicherweise den Hashalgorithmus festlegen. Wählen Sie im Eigenschaftenfenster der Vertrauensstellung der vertrauenden Seite (B2C-Demo) die Registerkarte **Erweitert** aus, und ändern Sie **Sicherer Hashalgorithmus** in `SHA-256`. Klicken Sie anschließend auf **OK**.  
13. Wählen Sie im Server-Manager **Tools** und dann **AD FS-Verwaltung** aus.
14. Wählen Sie die Vertrauensstellung der vertrauenden Seite, die Sie erstellt haben, und **Update from Federation Metadata** (Von Verbundmetadaten aktualisieren) aus, und klicken Sie dann auf **Aktualisieren**. 

## <a name="create-an-azure-ad-b2c-application"></a>Erstellen einer Azure AD B2C-Anwendung

Die Kommunikation mit Azure AD B2C erfolgt über eine Anwendung, die Sie in Ihrem Mandanten erstellen. In diesem Abschnitt werden optionale Schritte aufgeführt, die Sie ausführen können, um eine Testanwendung zu erstellen, falls Sie dies noch nicht getan haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein, z.B. *testapp1*.
6. Wählen Sie für **Web-App/Web-API** die Option `Yes` aus, und geben Sie dann für die **Antwort-URL** die Zeichenfolge `https://jwt.ms` ein.
7. Klicken Sie auf **Create**.

### <a name="update-and-test-the-relying-party-file"></a>Aktualisieren und Testen der Datei der vertrauenden Seite

Aktualisieren Sie als Nächstes die Datei der vertrauenden Seite, mit der die erstellte User Journey initiiert wird.

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Kopie der Datei *SignUpOrSignIn.xml*, und benennen Sie sie um. Benennen Sie die Datei z.B. in *SignUpSignInADFS.xml* um.
2. Öffnen Sie die neue Datei, und aktualisieren Sie den Wert des Attributs **PolicyId** für **TrustFrameworkPolicy** mit einem eindeutigen Wert. Beispiel: `SignUpSignInADFS`.
3. Aktualisieren Sie den Wert von **PublicPolicyUri** mit dem URI für die Richtlinie. Beispiel: `http://contoso.com/B2C_1A_signup_signin_adfs`
4. Ändern Sie den Wert des Attributs **ReferenceId** im **DefaultUserJourney**-Element so, dass er der ID der neuen User Journey entspricht, die Sie erstellt haben (SignUpSignInADFS).
5. Speichern Sie Ihre Änderungen, laden Sie die Datei hoch, und wählen Sie dann in der Liste die neue Richtlinie aus.
6. Stellen Sie sicher, dass die von Ihnen erstellte Azure AD B2C-Anwendung im Feld **Anwendung auswählen** ausgewählt ist, und klicken Sie dann auf **Jetzt ausführen**, um sie zu testen.

