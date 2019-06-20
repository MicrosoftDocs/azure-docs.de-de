---
title: Konfigurieren der Kennwortkomplexität mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Komplexitätsanforderungen für Kennwörter mit einer benutzerdefinierten Richtlinie in Azure Active Directory B2C konfiguriert werden.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 04a37e6faf51787457d7ca4ab8434fd253deb2ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509157"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurieren der Kennwortkomplexität mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory (Azure AD) B2C können Sie die Komplexitätsanforderungen für Kennwörter konfigurieren, die von einem Benutzer beim Erstellen eines Kontos angegeben werden. Standardmäßig verwendet Azure AD B2C **starke** Kennwörter. In diesem Artikel erfahren Sie, wie Sie Kennwortkomplexität in [benutzerdefinierten Richtlinien](active-directory-b2c-overview-custom.md) konfigurieren. Es ist auch möglich, die Kennwortkomplexität in [Benutzerflows](active-directory-b2c-reference-password-complexity.md) zu konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) beschriebenen Schritte aus.

## <a name="add-the-elements"></a>Hinzufügen der Elemente

1. Kopieren Sie die Datei *SignUpOrSignIn.xml*, die Sie mit dem Starter Pack kopiert haben, und benennen Sie sie als *SingUpOrSignInPasswordComplexity.xml*.
2. Öffnen Sie die Datei *SingUpOrSignInPasswordComplexity.xml*, und ändern Sie **PolicyId** und **PublicPolicyUri** in einen neuen Richtliniennamen. Beispiel: *B2C_1A_Registrierung_Anmeldung_Kennwortkomplexität*.
3. Fügen Sie die folgenden **ClaimType**-Elemente mit den Bezeichnern `newPassword` und `reenterPassword` hinzu:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Predicates](predicates.md) umfassen die Methodentypen `IsLengthRange` oder `MatchesRegex`. Der `MatchesRegex`-Typ wird zum Abgleich eines regulären Ausdrucks verwendet. Der `IsLengthRange`-Typ enthält einen Mindest- und Maximalwert für die Zeichenfolgenlänge. Fügen Sie ein **Predicates**-Element zum **BuildingBlocks**-Element hinzu, wenn es nicht mit den folgenden **Predicate**-Elementen vorhanden ist:

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Jedes **InputValidation**-Element wird durch Verwendung der definierten **Predicate**-Elemente konstruiert. Mit diesem Element können Sie boolesche Aggregationen ähnlich wie `and` und `or` durchführen. Fügen Sie ein **InputValidations**-Element zum **BuildingBlocks**-Element hinzu, wenn es nicht mit dem folgenden **InputValidation**-Element vorhanden ist:

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Stellen Sie sicher, dass das technische Profil **PolicyProfile** die folgenden Elemente enthält:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Speichern Sie die Richtliniendatei.

## <a name="test-your-policy"></a>Testen Ihrer Richtlinie

Wenn Sie Ihre Anwendungen in Azure AD B2C testen, kann es nützlich sein, das Azure AD B2C-Token an `https://jwt.ms` zurückzugeben, um die darin enthaltenen Ansprüche zu überprüfen.

### <a name="upload-the-files"></a>Hochladen der Dateien

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Framework für die Identitätsfunktion** aus.
5. Klicken Sie auf der Seite „Benutzerdefinierte Richtlinien“ auf **Richtlinie hochladen**.
6. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, suchen Sie nach der Datei *SingUpOrSignInPasswordComplexity.xml*, und wählen Sie die Datei aus.
7. Klicken Sie auf **Hochladen**.

### <a name="run-the-policy"></a>Ausführen der Richtlinie

1. Öffnen Sie die Richtlinie, die Sie geändert haben. Beispiel: *B2C_1A_Registrierung_Anmeldung_Kennwortkomplexität*.
2. Wählen Sie für **Anwendung** Ihre Anwendung aus, die Sie zuvor registriert haben. Um das Token anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
3. Klicken Sie auf **Jetzt ausführen**.
4. Wählen Sie **Jetzt registrieren** aus, geben Sie eine E-Mail-Adresse ein, und geben Sie ein neues Kennwort ein. Es werden Anweisungen zu Kennworteinschränkungen angezeigt. Schließen Sie die Eingabe der Benutzerinformationen ab, und klicken Sie dann auf **Erstellen**. Es sollten Ihnen jetzt die Inhalte des zurückgegebenen Tokens angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Konfigurieren der Kennwortänderung in benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


