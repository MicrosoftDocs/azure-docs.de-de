---
title: Lokalisierung – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Localization-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a02983c5019870e8b17db48184b2f238a82f8a40
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510580"
---
# <a name="localization"></a>Lokalisierung

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mithilfe des **Localization**-Elements können Sie mehrere Gebietsschemas oder Sprachen in der Richtlinie für die User Journeys unterstützen. Die Unterstützung der Lokalisierung in den Richtlinien ermöglicht Folgendes:

- Einrichten einer expliziten Liste der unterstützten Sprachen in einer Richtlinie und Auswählen einer Standardsprache
- Angeben von sprachspezifischen Zeichenfolgen und Sammlungen

```XML
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

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Enabled | Nein | Mögliche Werte: `true` oder `false`. |

Das **Localization**-Element enthält die folgenden XML-Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Liste der unterstützten Sprachen | 
| LocalizedResources | 0:n | Liste der lokalisierten Ressourcen |

## <a name="supportedlanguages"></a>SupportedLanguages

Das **SupportedLanguages**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
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

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| id | Ja | Ein Bezeichner, der zur eindeutigen Identifizierung von lokalisierten Ressourcen verwendet wird. |

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

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ElementType | Ja | Verweist auf ein ClaimType-Element oder ein Element der Benutzeroberfläche in der Richtliniendatei. |
| ElementId | Ja | Eine Zeichenfolge mit einem Verweis auf einen Anspruchstyp, der bereits in dem ClaimsSchema-Abschnitt, der bei Festlegung von **ElementType** auf einen Anspruchstyp verwendet wird, definiert wurde. |
| TargetCollection | Ja | Die Zielsammlung. |

Das **LocalizedCollection**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Item | 0:n | Definiert eine verfügbare Option für den Benutzer, über die dieser einen Anspruch in der Benutzeroberfläche auswählen kann, z.B. einen Wert in einer Dropdownliste. |

Das **Item**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Text | Ja | Die benutzerfreundliche Anzeigezeichenfolge, die dem Benutzer auf der Benutzeroberfläche für diese Option angezeigt werden soll. |
| Wert | Ja | Der Wert des Zeichenfolgenanspruchs, der der Auswahl dieser Option zugeordnet ist. |

Das folgende Beispiel zeigt die Verwendung des **LocalizedCollections**-Elements. Es enthält zwei **LocalizedCollection**-Elemente, eines für Englisch und ein anderes für Spanisch. Beide legen die **Restriction**-Sammlung des Anspruchs `Gender` auf eine Liste von Elementen für Englisch und Spanisch fest.

```XML
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

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ElementType | Ja | Ein Verweis auf ein ClaimType-Element oder ein Element der Benutzeroberfläche in der Richtlinie. Mögliche Werte: `ClaimType`, `UxElement`, `ErrorMessage` oder `Predicate`. Der Wert `ClaimType` wird verwendet, um eines der Anspruchsattribute gemäß der StringId zu lokalisieren. Der Wert `UxElement` wird verwendet, um eines der Elemente der Benutzeroberfläche gemäß der StringId zu lokalisieren. Der Wert `ErrorMessage` wird verwendet, um eine der Systemfehlermeldungen gemäß der StringId zu lokalisieren. Der Wert `Predicate` wird verwendet, um eine der [Predicate](predicates.md)-Fehlermeldungen gemäß der StringId zu lokalisieren. Der Wert `InputValidation` wird verwendet, um eine der Fehlermeldungen der [PredicateValidation](predicates.md)-Gruppe gemäß der StringId zu lokalisieren. |
| ElementId | Ja | Wenn **ElementType** auf `ClaimType`, `Predicate` oder `InputValidation` festgelegt wurde, enthält dieses Element einen Verweis auf einen Anspruchstyp, der bereits im ClaimsSchema-Abschnitt definiert wurde. | 
| StringId | Ja | Wenn **ElementType** auf `ClaimType` festgelegt ist, enthält dieses Element einen Verweis auf ein Attribut eines Anspruchstyps. Mögliche Werte: `DisplayName`, `AdminHelpText` oder `PatternHelpText`. Der Wert `DisplayName` wird verwendet, um den Anzeigenamen des Anspruchs festzulegen. Der Wert `AdminHelpText` wird verwendet, um den Namen des Hilfetexts des Anspruchsbenutzers festzulegen. Der Wert `PatternHelpText` wird verwendet, um den Hilfetext des Anspruchsmusters festzulegen. Wenn **ElementType** auf `UxElement` festgelegt ist, enthält dieses Element einen Verweis auf ein Attribut eines Elements der Benutzeroberfläche. Wenn **ElementType** auf `ErrorMessage` festgelegt ist, gibt dieses Element die ID einer Fehlermeldung an. Unter [Lokalisierungszeichenfolgen-IDs](localization-string-ids.md) finden Sie eine vollständige Liste der `UxElement`-Bezeichner.|


Das folgende Beispiel zeigt eine lokalisierte Anmeldeseite. Die ersten drei **LocalizedString**-Werte legen das Anspruchsattribut fest. Der dritte ändert den Wert der Schaltfläche „Weiter“. Der letzte ändert die Fehlermeldung.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Das folgende Beispiel zeigt den lokalisierten **UserHelpText** von **Predicate** mit der ID `IsLengthBetween8And64` an. Außerdem zeigt es einen lokalisierten **UserHelpText** von **PredicateGroup** mit der ID `CharacterClasses` und von **PredicateValidation** mit der ID `StrongPassword` an.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>              
```

## <a name="set-up-localization"></a>Einrichten der Lokalisierung

Dieser Artikel veranschaulicht, wie Sie mehrere Gebietsschemas oder Sprachen in der Richtlinie für User Journeys unterstützen können. Die Lokalisierung erfordert drei Schritte: Einrichten der expliziten Liste der unterstützten Sprachen, Angeben der sprachspezifischen Zeichenfolgen und Sammlungen und Bearbeiten der ContentDefinition für die Seite.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Einrichten der expliziten Liste der unterstützten Sprachen

Fügen Sie unter dem **BuildingBlocks**-Element das **Localization**-Element mit der Liste der unterstützten Sprachen hinzu. Das folgende Beispiel zeigt, wie Sie die Lokalisierungsunterstützung für Englisch (Standard) und Spanisch definieren:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Angeben der sprachspezifischen Zeichenfolgen und Sammlungen 

Fügen Sie im **Localization**-Element hinter dem Ende des **SupportedLanguages**-Elements **LocalizedResources**-Elemente hinzu. Sie fügen **LocalizedResources**-Elemente für jede Seite (Inhaltsdefinition) und jede beliebige Sprache, die Sie unterstützen möchten, hinzu. Zum Anpassen der einheitlichen Seite für die Registrierung oder Anmeldung und der Seiten für die mehrstufige Authentifizierung (MFA) auf Englisch, Spanisch und Französisch fügen Sie die folgenden **LocalizedResources**-Elemente hinzu.  
- Einheitliche Seite für Registrierung oder Anmeldung, Englisch `<LocalizedResources Id="api.signuporsignin.en">`
- Einheitliche Seite für Registrierung oder Anmeldung, Spanisch `<LocalizedResources Id="api.signuporsignin.es">`
- Einheitliche Seite für Registrierung oder Anmeldung, Französisch `<LocalizedResources Id="api.signuporsignin.fr">` 
- Registrierung, Englisch `<LocalizedResources Id="api.localaccountsignup.en">`
- Registrierung, Spanisch `<LocalizedResources Id="api.localaccountsignup.es">`
- Registrierung, Französisch `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, Englisch `<LocalizedResources Id="api.phonefactor.en">`
- MFA, Spanisch `<LocalizedResources Id="api.phonefactor.es">`
- MFA, Französisch `<LocalizedResources Id="api.phonefactor.fr">`

Jedes **LocalizedResources**-Element enthält alle erforderlichen **LocalizedStrings**-Elemente mit mehreren **LocalizedString**-Elementen und **LocalizedCollections**-Elemente mit mehreren **LocalizedCollection**-Elementen.  Im folgenden Beispiel wird die Seite für Registrierung mit englischer Lokalisierung hinzugefügt: 

Hinweis: Dieses Beispiel verweist auf die Anspruchstypen `Gender` und `City`. Um dieses Beispiel verwenden zu können, müssen Sie diese Ansprüche definieren. Weitere Informationen finden Sie unter [ClaimsSchema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Die Registrierungsseite mit Lokalisierung für Spanisch.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>Bearbeiten der ContentDefinition für die Seite 

Geben Sie für jede Seite, die Sie lokalisieren möchten, die Sprachcodes an, nach denen in der **ContentDefinition** gesucht werden soll.

Im folgenden Beispiel werden der Registrierungsseite benutzerdefinierte Zeichenfolgen für Englisch („en“) und Spanisch („es“) hinzugefügt. Die **LocalizedResourcesReferenceId** für jede **LocalizedResourcesReference** ist mit ihrem Gebietsschema identisch, es kann aber jede beliebige Zeichenfolge als Bezeichner verwendet werden. Sie verweisen für jede Sprach- und Seitenkombination auf die entsprechenden **LocalizedResources**, die Sie zuvor erstellt haben.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

Das folgende Beispiel zeigt den endgültigen XML-Code:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```




