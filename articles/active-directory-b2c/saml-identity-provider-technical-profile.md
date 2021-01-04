---
title: Definieren eines technischen SAML-Profils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Informationen zum Definieren eines technischen SAML-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3f8ba563345f5aa4f35bcf07546abdad05c3b6ed
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509783"
---
# <a name="define-a-saml-identity-provider-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Profils für einen SAML-Identitätsanbieter in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) bietet Unterstützung für den SAML 2.0-Identitätsanbieter. In diesem Artikel werden die Einzelheiten eines technischen Profils für die Interaktion mit einem Anspruchsanbieter thematisiert, der dieses standardisierte Protokoll unterstützt. Mit einem technischen SAML-Profil können Sie einen Verbund mit einem SAML-basierten Identitätsanbieter wie [AD FS](identity-provider-adfs2016-custom.md) oder [Salesforce](identity-provider-salesforce.md) erstellen. Über diesen Verbund können sich Ihre Benutzer mit ihren vorhandenen Identitäten aus sozialen Netzwerken oder Ihrem Unternehmen anmelden.

## <a name="metadata-exchange"></a>Metadatenaustausch

Metadaten sind die Daten, die in SAML-Protokollen verwendet werden, um die Konfiguration der SAML-Partei (z.B. dem Dienst- oder dem Identitätsanbieter) zur Verfügung zu stellen. Die Metadaten definieren den Ort der Dienste, z.B. Anmelden und Abmelden, Zertifikate und die Anmeldemethode. Der Identitätsanbieter verwendet die Metadaten, um zu erfahren, wie mit Azure AD B2C kommuniziert werden soll. Die Metadaten werden im XML-Format konfiguriert und können mit einer digitalen Signatur signiert werden, damit die jeweils andere Partei die Integrität der Metadaten überprüfen kann. Wenn sich der Azure AD B2C-Dienst mit einem SAML-Identitätsanbieter verbindet, fungiert dieser als Dienstanbieter, der eine SAML-Anforderung sendet und eine SAML-Antwort erwartet. Teilweise wird eine nicht angeforderte SAML-Authentifizierung akzeptiert, die auch als „IDP Initiated SSO“ (vom Identitätsanbieter initiiertes SSO) bezeichnet wird.

Die Metadaten können in beiden Parteien als „Statische Metadaten“ oder „Dynamische Metadaten“ konfiguriert werden. Kopieren Sie im Modus „Statisch“ die gesamten Metadaten aus der einen Partei und fügen Sie sie der anderen hinzu. Legen Sie im Modus „Dynamisch“ die URL zu den Metadaten fest, während die andere Partei die Konfiguration auf dynamische Weise liest. Das Vorgehen ist das gleiche: Sie legen die Metadaten des technischen Azure AD B2C-Profils in Ihrem Dienstanbieter und die Metadaten des Dienstanbieters in Azure AD B2C fest.

Die Vorgehensweise zum Bereitstellen und Festlegen eines Dienstanbieters unterscheidet sich je nach SAML-Identitätsanbieter. In diesem Fall ist Azure AD B2C der Dienstanbieter und die betreffenden Metadaten werden im Identitätsanbieter festgelegt. Weitere Informationen und Anleitungen dazu finden Sie in der Dokumentation zu Ihrem Identitätsanbieter.

Das folgende Beispiel zeigt eine URL der SAML-Metadaten eines technischen Azure AD B2C-Profils:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersetzen Sie die folgenden Werte:

- **your-tenant-name** durch den Namen Ihres Mandanten, z.B. „fabrikam.b2clogin.com“.
- **your-policy** durch den Namen Ihrer Richtlinie. Verwenden Sie die Richtlinie, in der Sie das technische Profil des SAML-Anbieters konfigurieren, oder eine Richtlinie, die von dieser Richtlinie erbt.
- **your-technical-profile** durch den Namen des technischen Profils des SAML-Identitätsanbieters.

## <a name="digital-signing-certificates-exchange"></a>Austauschen von digitalen Signaturzertifikaten

Sie müssen ein gültiges X509-Zertifikat mit dem privaten Schlüssel bereitstellen, um Vertrauen zwischen Azure AD B2C und Ihrem SAML-Identitätsanbieter herzustellen. Laden Sie das Zertifikat mit dem privaten Schlüssel (PFX-Datei) in den Azure AD B2C-Richtlinienschlüsselspeicher hoch. Azure AD B2C erstellt mithilfe des von Ihnen angegebenen Zertifikats eine digitale Signatur für die SAML-Anmeldeanforderung.

Ein Zertifikat kann folgendermaßen verwendet werden:

- Azure AD B2C generiert und signiert eine SAML-Anforderung mit dem privaten Azure AD B2C-Zertifikatschlüssel. Die SAML-Anforderung wird an den Identitätsanbieter gesendet und von diesem mithilfe des öffentlichen Azure AD B2C-Schlüssels des Zertifikats überprüft. Auf das öffentliche Azure AD B2C-Zertifikat kann über die technischen Profilmetadaten zugegriffen werden. Stattdessen können Sie aber auch manuell die CER-Datei in Ihren SAML-Identitätsanbieter hochladen.
- Der Identitätsanbieter signiert die an Azure AD B2C gesendeten Daten mit dem privaten Zertifikatschlüssel des Identitätsanbieters. Azure AD B2C überprüft die Daten mit dem öffentlichen Zertifikat des Identitätsanbieters. Das Setup unterscheidet sich je nach Identitätsanbieter. Eine Anleitung finden Sie in der Dokumentation zu Ihrem Identitätsanbieter. In Azure AD B2C benötigt Ihre Richtlinie Zugriff auf den öffentlichen Zertifikatschlüssel über die Metadaten des Identitätsanbieters.

In den meisten Szenarios genügt ein selbstsigniertes Zertifikat. In Produktionsumgebungen wird die Verwendung eines X509-Zertifikats empfohlen, das von einer Zertifizierungsstelle ausgestellt wird. Wie später in diesem Dokument beschrieben können Sie die SAML-Signierung für Umgebungen, bei denen es sich nicht um Produktionsumgebungen handelt, auf beiden Seiten deaktivieren.

Im folgenden Diagramm wird der Austausch von Metadaten und Zertifikaten dargestellt:

![Austausch von Metadaten und Zertifikaten](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digitale Verschlüsselung

Der Identitätsanbieter verwendet immer einen öffentlicher Schlüssel eines Verschlüsselungszertifikats in einem technischen Azure AD B2C-Profil, um die SAML-Antwortassertion zu verschlüsseln. Zum Entschlüsseln der Daten verwendet Azure AD B2C den privaten Teil des Verschlüsselungszertifikats.

So entschlüsseln Sie die SAML-Antwortassertion:

1. Laden Sie das gültige X509-Zertifikat mit dem privaten Schlüssel (PFX-Datei) in den Azure AD B2C-Richtlinienschlüsselspeicher hoch.
2. Fügen Sie ein **CryptographicKey**-Element mit einem `SamlAssertionDecryption`-Bezeichner in die **CryptographicKeys**-Sammlung des technischen Profils ein. Legen Sie **StorageReferenceId** auf den Namen des Richtlinienschlüssels fest, den Sie in Schritt 1 erstellt haben.
3. Legen Sie die **WantsEncryptedAssertions**-Metadaten des technischen Profils auf `true` fest.
4. Aktualisieren Sie den Identitätsanbieter mit den neuen Metadaten des technischen Azure AD B2C-Profils. Dann sollte **KeyDescriptor** mit der auf `encryption` festgelegten **use**-Eigenschaft mit dem öffentlichen Zertifikatschlüssel angezeigt werden.

Das folgende Beispiel zeigt den Abschnitt „Schlüsseldeskriptor“ der SAML-Metadaten, die für die Verschlüsselung verwendet werden:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des Protocol-Elements muss auf `SAML2` festgelegt werden.

## <a name="input-claims"></a>Eingabeansprüche

Das Element **InputClaims** dient zum Senden einer **NameId** innerhalb von **Subject** der SAML AuthN-Anforderung. Um dies zu erreichen, fügen Sie, wie unten gezeigt, einen Eingabeanspruch mit auf `subject` festgelegtem **PartnerClaimType** hinzu.

```xml
<InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="subject" />
</InputClaims>
```

## <a name="output-claims"></a>Ausgabeansprüche

Das **OutputClaims**-Element enthält eine Liste von Ansprüchen, die vom SAML-Identitätsanbieter im Abschnitt `AttributeStatement` zurückgegeben wurden. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der für den Identitätsanbieter definiert wurde, zuordnen. Sie können auch Ansprüche, die nicht vom Identitätsanbieter zurückgegeben wurden, einfügen, sofern Sie das `DefaultValue`-Attribut festlegen.

### <a name="subject-name-output-claim"></a>Ausgabeanspruch für Antragstellername

Zum Lesen der SAML-Assertion **NameId** in **Subject** als normalisierten Anspruch legen Sie den Anspruch **PartnerClaimType** auf den Wert des `SPNameQualifier`-Attributs fest. Wenn das `SPNameQualifier`-Attribut nicht angezeigt wird, legen Sie den Anspruch **PartnerClaimType** auf den Wert des `NameQualifier`-Attributs fest. 


SAML-Assertion: 

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
      <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Ausgabeanspruch:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Wenn keines der Attribute `SPNameQualifier` und `NameQualifier` in der SAML-Assertion angezeigt wird, legen Sie den Anspruch **PartnerClaimType** auf `assertionSubjectName` fest. Stellen Sie sicher, dass die **NameId** der erste Wert im XML-Code der Assertion ist. Wenn Sie mehrere Assertionen definieren, verwendet Azure AD B2C den Antragstellerwert aus der letzten Assertion.

Im folgenden Beispiel werden die Ansprüche angezeigt, die von einem SAML-Identitätsanbieter zurückgegeben wurden:

- Der Anspruch **issuerUserId** wird dem Anspruch **assertionSubjectName** zugeordnet.
- Der Anspruch **first_name** wird dem Anspruch **givenName** zugeordnet.
- Der Anspruch **last_name** wird dem Anspruch **surname** zugeordnet.
- Der Anspruch **displayName** wird dem Anspruch **name** zugeordnet.
- Dem Anspruch **email** wird kein Name zugeordnet.

Das technische Profil gibt auch Ansprüche zurück, die vom Identitätsanbieter nicht zurückgegeben werden:

- Der Anspruch **identityProvider** enthält den Namen des Identitätsanbieters.
- Der Anspruch **authenticationSource** enthält als Standardwert **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| PartnerEntity | Ja | URL zu den Metadaten des SAML-Identitätsanbieters. Kopieren Sie die Metadaten des Identitätsanbieters, und fügen Sie diese in das CDATA-Element `<![CDATA[Your IDP metadata]]>` ein. |
| WantsSignedRequests | Nein | Gibt an, ob das technische Profil verlangt, dass alle ausgehenden Authentifizierungsanforderungen signiert werden. Mögliche Werte: `true` oder `false`. Standardwert: `true`. Wenn der Wert auf `true` festgelegt ist, muss der kryptografische **SamlMessageSigning**-Schlüssel angegeben sein, und alle ausgehenden Authentifizierungsanforderungen werden signiert. Wenn der Wert auf `false` festgelegt ist, werden die Parameter **SigAlg** und **Signature** (Abfragezeichenfolgen- oder POST-Paramater) in der Anforderung ausgelassen. Diese Metadaten steuern auch das Metadatenattribut **AuthnRequestsSigned**, das in den Metadaten des technischen Azure AD B2C-Profils ausgegeben wird, das für den Identitätsanbieter freigegeben wird. Azure AD B2C signiert die Anforderung nicht, wenn der Wert von **WantsSignedRequests** in den Metadaten des technischen Profils auf `false` und in den Metadaten des Identitätsanbieters **WantAuthnRequestsSigned** auf `false` festgelegt ist oder nicht angegeben wurde. |
| XmlSignatureAlgorithm | Nein | Die Methode, die Azure AD B2C zur Signierung der SAML-Anforderung verwendet. Diese Metadaten steuern den Wert des **SigAlg**-Parameters (Abfragezeichenfolgen- oder POST-Parameter) in der SAML-Anforderung. Mögliche Werte: `Sha256`, `Sha384`, `Sha512` oder `Sha1` (Standardwert). Vergewissern Sie sich, dass Sie den Signaturalgorithmus auf beiden Seiten mit demselben Wert konfigurieren. Verwenden Sie nur den Algorithmus, den Ihr Zertifikat unterstützt. |
| WantsSignedAssertions | Nein | Gibt an, ob das technische Profil verlangt, dass alle eingehenden Assertionsanweisungen signiert werden. Mögliche Werte: `true` oder `false`. Standardwert: `true`. Wenn der Wert auf `true` festgelegt ist, müssen alle Assertionsbereiche für `saml:Assertion` signiert werden, die vom Identitätsanbieter an Azure AD B2C gesendet werden. Wenn der Wert auf `false` festgelegt ist, sollte der Identitätsanbieter die Assertionsanweisungen nicht signieren. Wenn er dies allerdings doch tut, überprüft Azure AD B2C die Signatur nicht. Diese Metadaten steuern außerdem das Metadatenflag **WantsAssertionsSigned**, das in den Metadaten des technischen Azure AD B2C-Profils ausgegeben wird, das für den Identitätsanbieter freigegeben wird. Wenn Sie die Assertionsvalidierung deaktivieren, sollten Sie auch die Validierung der Antwortsignatur deaktivieren (weitere Informationen finden Sie unter **ResponsesSigned**). |
| ResponsesSigned | Nein | Mögliche Werte: `true` oder `false`. Standardwert: `true`. Wenn der Wert auf `false` festgelegt ist, sollte der Identitätsanbieter die SAML-Antwort nicht signieren. Wenn er dies allerdings doch tut, überprüft Azure AD B2C die Signatur nicht. Wenn der Wert auf `true` festgelegt ist, wird die vom Identitätsanbieter an Azure AD B2C gesendete SAML-Antwort signiert und muss überprüft werden. Wenn Sie die Validierung der SAML-Antwort deaktivieren, sollten Sie auch die Validierung der Assertionssignatur deaktivieren (weitere Informationen finden Sie unter **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nein | Gibt an, ob das technische Profil verlangt, dass alle eingehenden Assertionsanweisungen verschlüsselt werden. Mögliche Werte: `true` oder `false`. Standardwert: `false`. Wenn der Wert auf `true` festgelegt ist, müssen die vom Identitätsanbieter an Azure AD B2C gesendeten Assertionsanweisungen signiert, und der kryptografische **SamlAssertionDecryption**-Schlüssel muss angegeben werden. Wenn der Wert auf `true` festgelegt ist, umfassen die Metadaten des technischen Azure AD B2C-Profils den **Verschlüsselungsbereich**. Der Identitätsanbieter liest die Metadaten und verschlüsselt die SAML-Antwortassertion mit dem öffentlichen Schlüssel, der in den Metadaten des technischen Azure AD B2C-Profils enthalten ist. Wenn Sie die Assertionsverschlüssung aktivieren, müssen Sie möglicherweise gleichzeitig auch die Validierung der Antwortsignatur deaktivieren (weitere Informationen finden Sie unter **ResponsesSigned**). |
| NameIdPolicyFormat | Nein | Gibt Einschränkungen für die Namens-ID an, die zum Darstellen des angeforderten Antragstellers verwendet werden. Wenn keine Angabe erfolgt, kann ein beliebiger ID-Typ, der vom Identitätsanbieter für den angeforderten Antragsteller unterstützt wird, verwendet werden. z. B. `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** kann mit **NameIdPolicyAllowCreate** verwendet werden. Suchen Sie in der Dokumentation Ihres Identitätsanbieters nach Anweisungen dazu, welche Richtlinien für Namens-IDs unterstützt werden. |
| NameIdPolicyAllowCreate | Nein | Bei Verwendung von **NameIdPolicyFormat** können Sie auch die `AllowCreate`-Eigenschaft von **NameIDPolicy** angeben. Der Wert dieser Metadaten ist `true` oder `false` und gibt an, ob der Identitätsanbieter während des Anmeldeflows ein neues Konto erstellen darf. Weitere Informationen und Anleitungen dazu finden Sie in der Dokumentation zu Ihrem Identitätsanbieter. |
| AuthenticationRequestExtensions | Nein | Optionale Erweiterungselemente zur Protokollnachricht, die zwischen Azure AD BC und dem betreffenden Identitätsanbieter vereinbart wurden. Die Erweiterung wird im XML-Format angezeigt. Sie fügen die XML-Daten im CDATA-Element `<![CDATA[Your IDP metadata]]>` hinzu. Sehen Sie in der Dokumentation Ihres Identitätsanbieters nach, ob das Erweiterungselement unterstützt wird. |
| IncludeAuthnContextClassReferences | Nein | Gibt einen oder mehrere URI-Verweise an, die Kontextklassen für die Authentifizierung angeben. Damit sich ein Benutzer beispielsweise nur mit Benutzername und Kennwort anmelden kann, legen Sie den Wert auf `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` fest. Wenn Sie die Anmeldung mit Benutzername und Kennwort über eine geschützte Sitzung (SSL/TLS) erlauben möchten, geben Sie `PasswordProtectedTransport` an. Suchen Sie in der Dokumentation Ihres Identitätsanbieters nach Anweisungen dazu, welche **AuthnContextClassRef**-URIs unterstützt werden. Geben Sie mehrere URIs als eine durch Kommas getrennte Liste an. |
| IncludeKeyInfo | Nein | Gibt an, ob die SAML-Authentifizierungsanforderung den öffentlichen Schlüssel des Zertifikats enthält, wenn die Bindung auf `HTTP-POST` festgelegt ist. Mögliche Werte: `true` oder `false`. |
| IncludeClaimResolvingInClaimsHandling  | Nein | Gibt bei Eingabe- und Ausgabeansprüchen an, ob die [Anspruchsauflösung](claim-resolver-overview.md) im technischen Profil enthalten ist. Mögliche Werte sind `true` oder `false` (Standardwert). Wenn Sie im technischen Profil eine Anspruchsauflösung verwenden möchten, legen Sie für diese Einstellung den Wert `true` fest. |
|SingleLogoutEnabled| Nein| Gibt an, ob das technische Profil bei der Anmeldung versucht, sich beim Verbundidentitätsanbieter abzumelden. Weitere Informationen finden Sie unter [Abmelden von der Azure AD B2C-Sitzung](session-behavior.md#sign-out).  Mögliche Werte: `true` (Standard) oder `false`.|

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Das **CryptographicKeys**-Element enthält die folgenden Attribute:

| attribute |Erforderlich | BESCHREIBUNG |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Ja | Das X509-Zertifikat (RSA-Schlüssel) zum Signieren der SAML-Nachrichten. Azure AD B2C verwendet diesen Schlüssel, um die Anforderungen zu signieren und an den Identitätsanbieter zu senden. |
| SamlAssertionDecryption |Nein | Das X509-Zertifikat (RSA-Schlüsselsatz). Ein SAML-Identitätsanbieter verwendet den öffentlichen Teil des Zertifikats, um die Assertion der SAML-Antwort zu verschlüsseln. Azure AD B2C verwendet den privaten Teil des Zertifikats, um die Assertion zu entschlüsseln. |
| MetadataSigning |Nein | Das X509-Zertifikat (RSA-Schlüssel) zum Signieren der SAML-Metadaten. Azure AD B2C verwendet diesen Schlüssel zum Signieren der Metadaten.  |

## <a name="saml-entityid-customization"></a>Anpassung der SAML-Entitäts-ID

Wenn Sie mehrere SAML-Anwendungen haben, die von unterschiedlichen Entitäts-ID-Werten (entityID) abhängen, können Sie den `issueruri`-Wert in der Datei der vertrauenden Seite überschreiben. Dazu kopieren Sie das technische Profil mit der „Saml2AssertionIssuer“-ID aus der Basisdatei, und überschreiben Sie den `issueruri`-Wert.

> [!TIP]
> Kopieren Sie den Abschnitt `<ClaimsProviders>` aus der Basisdatei, und bewahren Sie diese Elemente im Anspruchsanbieter auf: `<DisplayName>Token Issuer</DisplayName>`, `<TechnicalProfile Id="Saml2AssertionIssuer">` und `<DisplayName>Token Issuer</DisplayName>`.
 
Beispiel:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="next-steps"></a>Nächste Schritte

Beispiele für die Arbeit mit SAML-Identitätsanbietern in Azure AD B2C finden Sie in den folgenden Artikeln:

- [Add ADFS as a SAML identity provider using custom policies (Hinzufügen von ADFS als SAML-Identitätsanbieter mithilfe benutzerdefinierter Richtlinien)](identity-provider-adfs.md)
- [Sign in by using Salesforce accounts via SAML (Anmelden mit Salesforce-Konten per SAML)](identity-provider-salesforce.md)
