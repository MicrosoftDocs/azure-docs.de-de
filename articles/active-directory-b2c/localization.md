---
title: Lokalisierung – Azure Active Directory B2C
description: Erfahren Sie, wie Sie das Localization-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 88244ec3ba4bbebe7d6096fa3ac49bd4f1b8f661
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108619"
---
# <a name="localization-element"></a>Localization-Element

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mithilfe des **Localization**-Elements können Sie mehrere Gebietsschemas oder Sprachen in der Richtlinie für die User Journeys unterstützen. Die Unterstützung der Lokalisierung in den Richtlinien ermöglicht Folgendes:

- Einrichten einer expliziten Liste der unterstützten Sprachen in einer Richtlinie und Auswählen einer Standardsprache
- Angeben von sprachspezifischen Zeichenfolgen und Sammlungen

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

Das **Localization**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| Aktiviert | Nein | Mögliche Werte: `true` oder `false`. |

Das **Localization**-Element enthält die folgenden XML-Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Liste der unterstützten Sprachen |
| LocalizedResources | 0:n | Liste der lokalisierten Ressourcen |

## <a name="supportedlanguages"></a>SupportedLanguages

Das **SupportedLanguages**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| DefaultLanguage | Ja | Die Sprache, die standardmäßig für lokalisierte Ressourcen verwendet werden soll |
| MergeBehavior | Nein | Enumerationswerte von Werten, die mit jedem Anspruchstyp in übergeordneten Richtlinien mit dem gleichen Bezeichner zusammengeführt werden. Verwenden Sie dieses Attribut, wenn Sie einen Anspruch, der in der Basisrichtlinie angegeben ist, überschreiben. Mögliche Werte: `Append`, `Prepend` oder `ReplaceAll`. Der Wert `Append` gibt an, dass die Sammlung von Daten an das Ende der Sammlung, die in der übergeordneten Richtlinie angegebenen ist, angefügt werden soll. Der Wert `Prepend` gibt an, dass die Sammlung von Daten vor der Sammlung, die in der übergeordneten Richtlinie angegebenen ist, eingefügt werden soll. Der Wert `ReplaceAll` gibt an, dass die Sammlung von Daten, die in der übergeordneten Richtlinie definiert sind, ignoriert werden soll und stattdessen die in der aktuellen Richtlinie definierten Daten verwendet werden sollen. |

### <a name="supportedlanguages"></a>SupportedLanguages

Das **SupportedLanguages**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Zeigt Inhalt an, der einem Sprachtag nach „RFC 5646 – Tags for Identifying Languages“ (Tags für das Angeben von Sprachen) entspricht. |

## <a name="localizedresources"></a>LocalizedResources

Das **LocalizedResources**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner, der zur eindeutigen Identifizierung von lokalisierten Ressourcen verwendet wird. |

Das **LocalizedResources**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Definiert vollständige Sammlungen in verschiedenen Kulturen. Eine Sammlung kann eine unterschiedliche Anzahl von Elementen und unterschiedliche Zeichenfolgen für verschiedene Kulturen aufweisen. Beispiele für Sammlung sind die Enumerationen, die in Anspruchstypen angezeigt werden. Beispielsweise wird dem Benutzer eine Liste mit Ländern/Regionen in einer Dropdownliste angezeigt. |
| LocalizedStrings | 0:n | Definiert alle Zeichenfolgen in verschiedenen Kulturen, mit Ausnahme von Zeichenfolgen, die in Sammlungen enthalten sind. |

### <a name="localizedcollections"></a>LocalizedCollections

Das **LocalizedCollections**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Liste der unterstützten Sprachen |

#### <a name="localizedcollection"></a>LocalizedCollection

Das **LocalizedCollection**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ElementType | Ja | Verweist auf ein ClaimType-Element oder ein Element der Benutzeroberfläche in der Richtliniendatei. |
| ElementId | Ja | Eine Zeichenfolge mit einem Verweis auf einen Anspruchstyp, der bereits in dem ClaimsSchema-Abschnitt, der bei Festlegung von **ElementType** auf einen Anspruchstyp verwendet wird, definiert wurde. |
| TargetCollection | Ja | Die Zielsammlung. |

Das **LocalizedCollection**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Element | 0:n | Definiert eine verfügbare Option für den Benutzer, über die dieser einen Anspruch in der Benutzeroberfläche auswählen kann, z.B. einen Wert in einer Dropdownliste. |

Das **Item**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Text | Ja | Die benutzerfreundliche Anzeigezeichenfolge, die dem Benutzer auf der Benutzeroberfläche für diese Option angezeigt werden soll. |
| Wert | Ja | Der Wert des Zeichenfolgenanspruchs, der der Auswahl dieser Option zugeordnet ist. |
| SelectByDefault | Nein | Gibt an, ob diese Option auf der Benutzeroberfläche standardmäßig ausgewählt werden soll. Mögliche Werte: „True“ oder „False“. |

Das folgende Beispiel zeigt die Verwendung des **LocalizedCollections**-Elements. Es enthält zwei **LocalizedCollection**-Elemente, eines für Englisch und ein anderes für Spanisch. Beide legen die **Restriction**-Sammlung des Anspruchs `Gender` auf eine Liste von Elementen für Englisch und Spanisch fest.

```xml
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

Das **LocalizedStrings**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Eine lokalisierte Zeichenfolge. |

Das **LocalizedString**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ElementType | Ja | Mögliche Werte: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predicate](#predicate), [InputValidation](#inputvalidation) oder [UxElement](#uxelement).   | 
| ElementId | Ja | Wenn **ElementType** auf `ClaimType`, `Predicate` oder `InputValidation` festgelegt wurde, enthält dieses Element einen Verweis auf einen Anspruchstyp, der bereits im ClaimsSchema-Abschnitt definiert wurde. |
| StringId | Ja | Wenn **ElementType** auf `ClaimType` festgelegt ist, enthält dieses Element einen Verweis auf ein Attribut eines Anspruchstyps. Mögliche Werte: `DisplayName`, `AdminHelpText` oder `PatternHelpText`. Der Wert `DisplayName` wird verwendet, um den Anzeigenamen des Anspruchs festzulegen. Der Wert `AdminHelpText` wird verwendet, um den Namen des Hilfetexts des Anspruchsbenutzers festzulegen. Der Wert `PatternHelpText` wird verwendet, um den Hilfetext des Anspruchsmusters festzulegen. Wenn **ElementType** auf `UxElement` festgelegt ist, enthält dieses Element einen Verweis auf ein Attribut eines Elements der Benutzeroberfläche. Wenn **ElementType** auf `ErrorMessage` festgelegt ist, gibt dieses Element die ID einer Fehlermeldung an. Unter [Lokalisierungszeichenfolgen-IDs](localization-string-ids.md) finden Sie eine vollständige Liste der `UxElement`-Bezeichner.|

## <a name="elementtype"></a>ElementType

Der „ElementType“-Verweis auf einen Anspruchstyp, eine Anspruchstransformation oder ein Benutzeroberflächenelement in der zu lokalisierenden Richtlinie.

| Zu lokalisierendes Element | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Identitätsanbietername |`ClaimsProvider`| | Die ID des „ClaimsExchange“-Elements|
| Anspruchstypattribute|`ClaimType`|Der Name des Anspruchstyps| Das Attribut des zu lokalisierenden Anspruchs. Mögliche Werte: `AdminHelpText`, `DisplayName`, `PatternHelpText` und `UserHelpText`.|
|Fehlermeldung|`ErrorMessage`||Die ID der Fehlermeldung |
|Kopiert lokalisierte Zeichenfolgen in Ansprüche|`GetLocalizedStringsTra nsformationClaimType`||Der Name des Ausgabeanspruchs|
|Prädikat, Benutzermeldung|`Predicate`|Der Name des Prädikats| Das Attribut des zu lokalisierenden Prädikats. Mögliche Werte: `HelpText`.|
|Prädikatgruppe, Benutzermeldung|`InputValidation`|Die ID des „PredicateValidation“-Elements.|Die ID des „PredicateGroup“-Elements. Die Prädikatgruppe muss ein untergeordnetes Element des Prädikatüberprüfungselements gemäß der ElementId sein.|
|Benutzeroberflächenelemente |`UxElement` | | Die ID des zu lokalisierenden Benutzeroberflächenelements.|
|[Anzeigesteuerelement](display-controls.md) |`DisplayControl` |Die ID des Anzeigesteuerelements. | Die ID des zu lokalisierenden Benutzeroberflächenelements.|

## <a name="examples"></a>Beispiele

### <a name="claimsprovider"></a>ClaimsProvider

Der Wert „ClaimsProvider“ wird verwendet, um einen der Anzeigenamen eines Anspruchsanbieters zu lokalisieren. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

Im folgenden Beispiel wird gezeigt, wie der Anzeigename von Anspruchsanbietern lokalisiert wird.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

Der Wert „ClaimType“ wird verwendet, um eines der Anspruchsattribute zu lokalisieren. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Im folgenden Beispiel wird gezeigt, wie die Attribute „DisplayName“, „UserHelpText“ und „PatternHelpText“ des Anspruchstyps „email“ lokalisiert werden.

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

Der Wert „ErrorMessage“ wird verwendet, um eine der Systemfehlermeldungen zu lokalisieren. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

Im folgenden Beispiel wird gezeigt, wie die Fehlermeldung „UserMessageIfClaimsPrincipalAlreadyExists“ lokalisiert wird.


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

Der Wert „GetLocalizedStringsTransformationClaimType“ wird verwendet, um lokalisierte Zeichenfolgen in Ansprüche zu kopieren. Weitere Informationen finden Sie unter [Anspruchstransformation „GetLocalizedStringsTransformation“](string-transformations.md#getlocalizedstringstransformation).


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

Im folgenden Beispiel wird gezeigt, wie Ausgabeansprüche der Anspruchstransformation „GetLocalizedStringsTransformation“ lokalisiert werden.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicate

Der Wert „Predicate“ wird verwendet, um eine der [Predicate](predicates.md)-Fehlermeldungen zu lokalisieren. 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Im folgenden Beispiel wird gezeigt, wie der Hilfetext für Prädikate lokalisiert wird.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

Der Wert „InputValidation“ wird verwendet, um eine der Fehlermeldungen der [PredicateValidation](predicates.md)-Gruppe zu lokalisieren. 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Im folgenden Beispiel wird gezeigt, wie der Hilfetext für eine Prädikatüberprüfungsgruppe lokalisiert wird.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

Der Wert „UxElement“ wird verwendet, um eines der Benutzeroberflächenelemente zu lokalisieren. Im folgenden Beispiel wird gezeigt, wie die Schaltflächen „continue“ (Weiter) und „Cancel“ (Abbrechen) lokalisiert werden.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

### <a name="displaycontrol"></a>DisplayControl

Der Wert „DisplayControl“ wird zum Lokalisieren eines der Benutzeroberflächenelemente des Typs [Anzeigesteuerelement](display-controls.md) verwendet. Im folgenden Beispiel wird gezeigt, wie die Schaltflächen „send“ (Senden) und „verify“ (Überprüfen) lokalisiert werden. 

```xml
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
```

Im Abschnitt „Metadaten“ eines selbstbestätigten technischen Profils muss für das „ContentDefinition“-Element, auf das verwiesen wird, der Wert „DataUri“ auf [Seitenlayoutversion](page-layout.md) 2.1.0 oder höher festgelegt sein. Beispiel:

```xml
<ContentDefinition Id="api.selfasserted">
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.1.0</DataUri>
  ...
```

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten Lokalisierungsbeispiele:

- [Sprachanpassung mit benutzerdefinierter Richtlinie in Azure Active Directory B2C](language-customization.md)
- [Sprachanpassung mit Benutzerflows in Azure Active Directory B2C](language-customization.md)
