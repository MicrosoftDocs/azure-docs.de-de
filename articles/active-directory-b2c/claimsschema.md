---
title: ClaimsSchema – Azure Active Directory B2C | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie das ClaimsSchema-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e09c4530fc6dce00e6d807908c7de598422a440b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511847"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das **ClaimsSchema**-Element definiert die Anspruchstypen, auf die als Teil der Richtlinie verwiesen werden kann. Im Anspruchsschema deklarieren Sie Ihre Ansprüche. Bei einem Anspruch kann es sich u.a. um einen Vornamen, Nachnamen, Anzeigenamen, eine Telefonnummer handeln. Das ClaimsSchema-Element enthält eine Liste von **ClaimType**-Elementen. Das **ClaimType**-Element enthält das **Id**-Attribut, bei dem es sich um den Anspruchsnamen handelt. 

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

Das **ClaimType**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| id | Ja | Ein Bezeichner, der für den Anspruchsnamen verwendet wird. Andere Elemente können diesen Bezeichner in der Richtlinie verwenden. |

Das **ClaimType**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Der Titel, der Benutzern auf unterschiedlichen Bildschirmen angezeigt wird. Der Wert kann [lokalisiert](localization.md) sein. |
| DataType | 0:1 | Der Typ des Anspruchs. Die Datentypen „boolean“, „date“, „dateTime“, „int“, „long“, „string“, „stringCollection“, „alternativeSecurityIdCollection“ können verwendet werden. |
| DefaultPartnerClaimTypes | 0:1 | Die Partneranspruchstypen, die standardmäßig für ein angegebenes Protokoll verwendet werden sollen. Der Wert kann mit dem in den Elementen **InputClaim** oder **OutputClaim** angegebenen **PartnerClaimType** überschrieben werden. Verwenden Sie dieses Element, um den Standardnamen für ein Protokoll anzugeben.  |
| Mask | 0:1 | Eine optionale Zeichenfolge von Maskierungszeichen, die bei der Anzeige des Anspruchs angewendet werden kann. Beispielsweise kann die Telefonnummer 324-232-4343 als XXX-XXX-4343 maskiert werden. |
| UserHelpText | 0:1 | Eine Beschreibung des Anspruchstyps, der Benutzern helfen kann, dessen Zweck zu verstehen. Der Wert kann [lokalisiert](localization.md) sein. |
| UserInputType | 0:1 | Der Typ des Eingabesteuerelements, der dem Benutzer zur Verfügung stehen sollte, wenn die Anspruchsdaten für den Anspruchstypen manuell eingegeben werden. Weitere Informationen hierzu finden Sie weiter unten in den Definitionen der Benutzereingabetypen. |
| Einschränkung | 0:1 | Die Werteinschränkungen für diesen Anspruch, z.B. ein regulärer Ausdruck (RegEx) oder eine Liste der zulässigen Werte. Der Wert kann [lokalisiert](localization.md) sein. |
PredicateValidationReference| 0:1 | Ein Verweis auf ein **PredicateValidationsInput**-Element. Die **PredicateValidationReference**-Elemente ermöglichen Ihnen einen Überprüfungsprozess, mithilfe dessen Sie sicherstellen können, dass nur ordnungsgemäß formatierte Daten eingegeben werden. Weitere Informationen finden Sie im Artikel zu [Prädikaten](predicates.md). |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

Die **DefaultPartnerClaimTypes** können das folgende Element enthalten:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Protocol | 0:n | Liste von Protokollen mit deren Standardnamen für den Partneranspruchstyp. |

Das **Protocol**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Name | Ja | Der Name eines von Azure AD B2C unterstützten gültigen Protokolls. Mögliche Werte:  OAuth1, OAuth2, SAML2, OpenIdConnect, WsFed oder WsTrust. |
| PartnerClaimType | Ja | Der zu verwendende Anspruchstypname. |

Im folgenden Beispiel wird der Anspruch **surname** bei der Interaktion des Identity Experience Frameworks mit einem SAML2-Identitätsanbieter oder einer Anwendung der vertrauenden Seite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` zugeordnet. Unter Verwendung der Werte „OpenIdConnect“ und „OAuth2“ wird der Anspruch `family_name` zugeordnet.

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Infolgedessen gibt das von Azure AD B2C ausgestellte JWT-Token `family_name` anstelle des Anspruchstypnamens **surname** aus.
 
```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

Das **Mask**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Type` | Ja | Der Typ der Anspruchsmaske. Mögliche Werte: `Simple` oder `Regex`. Der Wert `Simple` gibt an, dass auf den vorderen Teil eines Zeichenfolgenanspruchs eine einfache Textmaske angewandt wird. Der Wert `Regex` gibt an, dass ein regulärer Ausdruck auf den Zeichenfolgenausdruck als Ganzes angewandt wird.  Wenn der Wert `Regex` angegeben wird, muss mit dem zu verwendenden regulären Ausdruck auch ein optionales Attribut definiert werden. |
| `Regex` | Nein | Wenn **`Type`** auf `Regex` festgelegt wird, geben Sie den zu verwendenden regulären Ausdruck an.

Im folgenden Beispiel wird ein **PhoneNumber**-Anspruch mit der Maske `Simple` konfiguriert:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>  
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Das Identity Experience Framework rendert die Telefonnummer, wobei die ersten sechs Ziffern ausgeblendet werden:

![Verwenden eines Anspruchstyps mit Maske](./media/claimsschema/mask.png)

Im folgenden Beispiel wird ein **AlternateEmail**-Anspruch mit der Maske `Regex` konfiguriert:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Das Identity Experience Framework rendert nur den ersten Buchstaben der E-Mail-Adresse und den E-Mail-Domänennamen:

![Verwenden eines Anspruchstyps mit Maske](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Einschränkung

Das **Restriction**-Element enthält möglicherweise das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| MergeBehavior | Nein | Die Methode, die zum Zusammenführen von Enumerationswerten mit einem Anspruchstyp in einer übergeordneten Richtlinie mit dem gleichen Bezeichner verwendet wird. Verwenden Sie dieses Attribut, wenn Sie einen Anspruch überschreiben, der in der Basisrichtlinie angegeben ist. Mögliche Werte: `Append`, `Prepend` oder `ReplaceAll`. Beim Wert `Append` handelt es sich um eine Sammlung von Daten, die an das Ende der in der übergeordneten Richtlinie angegebenen Sammlung angefügt werden soll. Beim Wert `Prepend` handelt es sich um eine Sammlung von Daten, die vor der in der übergeordneten Richtlinie angegebenen Sammlung hinzugefügt werden soll. Beim Wert `ReplaceAll` handelt es sich um eine Sammlung von in der übergeordneten Richtlinie angegebenen Daten, die ignoriert werden sollen. |

Das **Restriction**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Enumeration | 1:n | Die verfügbaren Optionen auf der Benutzeroberfläche, über die ein Benutzer einen Anspruch, z.B. einen Wert in einer Dropdownliste, auswählen kann. |
| Muster | 1:1 | Der zu verwendende reguläre Ausdruck. |

### <a name="enumeration"></a>Enumeration

Das **Enumeration**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Text | Ja | Die Anzeigezeichenfolge, die dem Benutzer auf der Benutzeroberfläche für diese Option angezeigt wird. |
|Wert | Ja | Der Wert des Anspruchs, der der Auswahl dieser Option zugeordnet ist. |
| SelectByDefault | Nein | Gibt an, ob diese Option auf der Benutzeroberfläche standardmäßig ausgewählt werden soll. Mögliche Werte: „True“ oder „False“. |

Im folgenden Beispiel wird ein **city**-Dropdownlistenanspruch mit dem Standardwert `New York` konfiguriert:

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```
„city“-Dropdownliste mit dem Standardwert „New York“:

![„city“-Dropdownliste](./media/claimsschema/dropdownsingleselect.png)


### <a name="pattern"></a>Muster

Das **Pattern**-Element kann die folgenden Attribute enthalten:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| RegularExpression | Ja | Der reguläre Ausdruck, mit dem Ansprüche dieses Typs übereinstimmen müssen, damit sie gültig sind. |
| HelpText | Nein | Das Muster oder der reguläre Ausdruck für diesen Anspruch. |

Im folgenden Beispiel wird ein **email**-Anspruch mit Eingabeüberprüfung mit regulärem Ausdruck und Hilfetext konfiguriert:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Das Identity Experience Framework rendert den E-Mail-Adressenanspruch mit Eingabeüberprüfung des E-Mail-Formats:

![Verwenden des Anspruchstyps mit Muster](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>UserInputType

Azure AD B2C unterstützt eine Vielzahl von Benutzereingabetypen wie ein Textfeld, Kennwort und eine Dropdownliste, die bei der manuellen Eingabe von Anspruchsdaten für den Anspruchstyp verwendet werden können. Sie müssen **UserInputType** angeben, wenn Sie Informationen vom Benutzer sammeln, indem Sie ein [selbstbestätigtes technisches Profil](self-asserted-technical-profile.md) verwenden.

### <a name="textbox"></a>TextBox

Der Benutzereingabetyp **TextBox** wird für die Bereitstellung eines einzeiligen Textfelds verwendet.

![Verwenden des Anspruchstyps mit Textfeld](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

Der Benutzereingabetyp **EmailBox** wird für die Bereitstellung eines einfachen E-Mail-Eingabefelds verwendet.

![Verwenden des Anspruchstyps mit E-Mail-Feld](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

### <a name="password"></a>Kennwort

Der Benutzereingabetyp **Password** wird für die Aufzeichnung eines vom Benutzer eingegebenen Kennworts verwendet.

![Verwenden des Anspruchstyps mit Kennwort](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

Der Benutzereingabetyp **DateTimeDropdown** wird für die Bereitstellung von Dropdownlisten verwendet, über die ein Tag, Monat und Jahr ausgewählt werden können. Sie können Prädikate und „PredicateValidations“-Elemente verwenden, um die minimalen und maximalen Datumswerte zu kontrollieren. Weitere Informationen finden Sie im Abschnitt **Configure a date range (Konfigurieren eines Datumsbereichs)** im Artikel [Predicates and PredicateValidations (Prädikate und PredicateValidations)](predicates.md).

![Verwenden des Anspruchstyps mit „datetimedropdown“](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

Der Benutzereingabetyp **RadioSingleSelect** wird für die Bereitstellung einer Sammlung von Optionsfeldern verwendet, mithilfe derer Benutzer eine Option auswählen können.

![Verwenden des Anspruchstyps mit „radiosingleselect“](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>    
```

### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Der Benutzereingabetyp **DropdownSingleSelect** wird für die Bereitstellung einer Dropdownliste verwendet, mithilfe derer Benutzer eine Option auswählen können.

![Verwenden des Anspruchstyps mit „dropdownsingleselect“](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Der Benutzereingabetyp **CheckboxMultiSelect** wird für die Bereitstellung einer Sammlung von Kontrollkästchen verwendet, mithilfe derer Benutzer mehrere Optionen auswählen können.

![Verwenden des Anspruchstyps mit „checkboxmultiselect“](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="readonly"></a>Readonly

Der Benutzereingabetyp **Readonly** wird für die Bereitstellung eines schreibgeschützten Felds zum Anzeigen des Anspruchs und des Werts verwendet.

![Verwenden des Anspruchstyps mit „readonly“](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Paragraph

Der Benutzereingabetyp **Paragraph** wird für die Bereitstellung eines Felds verwendet, das Text nur in einem Absatztag anzeigt. Zum Beispiel: &lt;p&gt;Text&lt;/p&gt;.

![Verwenden des Anspruchstyps mit „paragraph“](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

Verwenden Sie zum Anzeigen der **Enumeration**-Werte in einem **responseMsg**-Anspruch die Anspruchstransformationen `GetMappedValueFromLocalizedCollection` oder `CreateStringClaim`. Weitere Informationen finden Sie unter [String Claims Transformations (Transformationen von Zeichenfolgeansprüchen)](string-transformations.md). 
