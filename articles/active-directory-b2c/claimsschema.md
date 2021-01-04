---
title: ClaimsSchema – Azure Active Directory B2C | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie das ClaimsSchema-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aadb75d7257470cf4288c6123263f3d2dfe14d21
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781718"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das **ClaimsSchema** -Element definiert die Anspruchstypen, auf die als Teil der Richtlinie verwiesen werden kann. Im Anspruchsschema deklarieren Sie Ihre Ansprüche. Bei einem Anspruch kann es sich u.a. um einen Vornamen, Nachnamen, Anzeigenamen, eine Telefonnummer handeln. Das ClaimsSchema-Element enthält eine Liste von **ClaimType** -Elementen. Das **ClaimType** -Element enthält das **Id** -Attribut, bei dem es sich um den Anspruchsnamen handelt.

```xml
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

Das **ClaimType** -Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner, der für den Anspruchsnamen verwendet wird. Andere Elemente können diesen Bezeichner in der Richtlinie verwenden. |

Das **ClaimType** -Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Der Titel, der Benutzern auf unterschiedlichen Bildschirmen angezeigt wird. Der Wert kann [lokalisiert](localization.md) sein. |
| DataType | 1:1 | Der Typ des Anspruchs. |
| DefaultPartnerClaimTypes | 0:1 | Die Partneranspruchstypen, die standardmäßig für ein angegebenes Protokoll verwendet werden sollen. Der Wert kann mit dem in den Elementen **InputClaim** oder **OutputClaim** angegebenen **PartnerClaimType** überschrieben werden. Verwenden Sie dieses Element, um den Standardnamen für ein Protokoll anzugeben.  |
| Mask | 0:1 | Eine optionale Zeichenfolge von Maskierungszeichen, die bei der Anzeige des Anspruchs angewendet werden kann. Beispielsweise kann die Telefonnummer 324-232-4343 als XXX-XXX-4343 maskiert werden. |
| UserHelpText | 0:1 | Eine Beschreibung des Anspruchstyps, der Benutzern helfen kann, dessen Zweck zu verstehen. Der Wert kann [lokalisiert](localization.md) sein. |
| UserInputType | 0:1 | Der Typ des Eingabesteuerelements, der dem Benutzer zur Verfügung stehen sollte, wenn die Anspruchsdaten für den Anspruchstypen manuell eingegeben werden. Weitere Informationen hierzu finden Sie weiter unten in den Definitionen der Benutzereingabetypen. |
| AdminHelpText | 0:1 | Eine Beschreibung des Anspruchstyps, der Administratoren helfen kann, dessen Zweck zu verstehen. |
| Einschränkung | 0:1 | Die Werteinschränkungen für diesen Anspruch, z.B. ein regulärer Ausdruck (RegEx) oder eine Liste der zulässigen Werte. Der Wert kann [lokalisiert](localization.md) sein. |
PredicateValidationReference| 0:1 | Ein Verweis auf ein **PredicateValidationsInput** -Element. Die **PredicateValidationReference** -Elemente ermöglichen Ihnen einen Überprüfungsprozess, mithilfe dessen Sie sicherstellen können, dass nur ordnungsgemäß formatierte Daten eingegeben werden. Weitere Informationen finden Sie im Artikel zu [Prädikaten](predicates.md). |



### <a name="datatype"></a>DataType

Das Element **DataType** unterstützt die folgenden Werte:

| type | BESCHREIBUNG |
| ------- | ----------- |
|boolean|Stellt einen booleschen Wert (`true` oder `false`) dar.|
|date| Stellt einen Zeitpunkt dar, der in der Regel als Tagesdatum ausgedrückt wird. Der Datumswert entspricht der Konvention ISO 8601.|
|dateTime|Stellt einen Zeitpunkt dar, der üblicherweise als Datum und Uhrzeit ausgedrückt wird. Der Datumswert entspricht der Konvention ISO 8601.|
|duration|Stellt ein Zeitintervall in Jahren, Monaten, Tagen, Stunden, Minuten und Sekunden dar. Das Format ist `PnYnMnDTnHnMnS`, wobei `P` einen positiven und `N` einen negativen Wert angibt. `nY` ist die Anzahl von Jahren, gefolgt von einem Literal `Y`. `nMo` ist die Anzahl von Monaten, gefolgt von einem Literal `Mo`. `nD` ist die Anzahl von Tagen, gefolgt von einem Literal `D`. Beispiele: `P21Y` steht für 21 Jahre. `P1Y2Mo` steht für 1 Jahr und 2 Monate. `P1Y2Mo5D` steht für 1 Jahr, 2 Monate und 5 Tage.  `P1Y2M5DT8H5M620S` steht für 1 Jahr, 2 Monate, 5 Tage, 8 Stunden, 5 Minuten und 20 Sekunden.  |
|phoneNumber|Stellt eine Telefonnummer dar. |
|INT| Stellt eine Zahl zwischen -2.147.483.648 und 2.147.483.647 dar|
|long| Stellt eine Zahl zwischen -9.223.372.036.854.775.808 und 9.223.372.036.854.775.807 dar |
|Zeichenfolge| Stellt Text als Sequenz von UTF-16-Codeeinheiten dar.|
|stringCollection|Stellt eine `string`-Auflistung dar.|
|userIdentity| Stellt eine Benutzeridentität dar.|
|userIdentityCollection|Stellt eine `userIdentity`-Auflistung dar.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

Die **DefaultPartnerClaimTypes** können das folgende Element enthalten:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Protocol | 1:n | Liste von Protokollen mit deren Standardnamen für den Partneranspruchstyp. |

Das **Protocol** -Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Name | Ja | Der Name eines von Azure AD B2C unterstützten gültigen Protokolls. Mögliche Werte:  OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Ja | Der zu verwendende Anspruchstypname. |

Im folgenden Beispiel wird der Anspruch **surname** bei der Interaktion des Identity Experience Frameworks mit einem SAML2-Identitätsanbieter oder einer Anwendung der vertrauenden Seite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` zugeordnet. Unter Verwendung der Werte „OpenIdConnect“ und „OAuth2“ wird der Anspruch `family_name` zugeordnet.

```xml
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

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

Das **Mask** -Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Type` | Ja | Der Typ der Anspruchsmaske. Mögliche Werte: `Simple` oder `Regex`. Der Wert `Simple` gibt an, dass auf den vorderen Teil eines Zeichenfolgenanspruchs eine einfache Textmaske angewandt wird. Der Wert `Regex` gibt an, dass ein regulärer Ausdruck auf den Zeichenfolgenausdruck als Ganzes angewandt wird.  Wenn der Wert `Regex` angegeben wird, muss mit dem zu verwendenden regulären Ausdruck auch ein optionales Attribut definiert werden. |
| `Regex` | Nein | Wenn **`Type`** auf `Regex` festgelegt wird, geben Sie den zu verwendenden regulären Ausdruck an.

Im folgenden Beispiel wird ein **PhoneNumber** -Anspruch mit der Maske `Simple` konfiguriert:

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Das Identity Experience Framework rendert die Telefonnummer, wobei die ersten sechs Ziffern ausgeblendet werden:

![Telefonnummeranspruch im Browser mit den ersten sechs Ziffern als X](./media/claimsschema/mask.png)

Im folgenden Beispiel wird ein **AlternateEmail** -Anspruch mit der Maske `Regex` konfiguriert:

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Das Identity Experience Framework rendert nur den ersten Buchstaben der E-Mail-Adresse und den E-Mail-Domänennamen:

![E-Mail-Anspruch im Browser mit Zeichen als Sternchen](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Einschränkung

Das **Restriction** -Element enthält möglicherweise das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| MergeBehavior | Nein | Die Methode, die zum Zusammenführen von Enumerationswerten mit einem Anspruchstyp in einer übergeordneten Richtlinie mit dem gleichen Bezeichner verwendet wird. Verwenden Sie dieses Attribut, wenn Sie einen Anspruch überschreiben, der in der Basisrichtlinie angegeben ist. Mögliche Werte: `Append`, `Prepend` oder `ReplaceAll`. Beim Wert `Append` handelt es sich um eine Sammlung von Daten, die an das Ende der in der übergeordneten Richtlinie angegebenen Sammlung angefügt werden soll. Beim Wert `Prepend` handelt es sich um eine Sammlung von Daten, die vor der in der übergeordneten Richtlinie angegebenen Sammlung hinzugefügt werden soll. Beim Wert `ReplaceAll` handelt es sich um eine Sammlung von in der übergeordneten Richtlinie angegebenen Daten, die ignoriert werden sollen. |

Das **Restriction** -Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Enumeration | 1:n | Die verfügbaren Optionen auf der Benutzeroberfläche, über die ein Benutzer einen Anspruch, z.B. einen Wert in einer Dropdownliste, auswählen kann. |
| Muster | 1:1 | Der zu verwendende reguläre Ausdruck. |

#### <a name="enumeration"></a>Enumeration

Das **Enumeration** -Element definiert die auf der Benutzeroberfläche verfügbaren Optionen, die der Benutzer für einen Anspruch auswählen kann, z. B. einen Wert in einem `CheckboxMultiSelect`-, `DropdownSingleSelect`- oder `RadioSingleSelect`-Element. Alternativ können Sie verfügbare Optionen mit dem [LocalizedCollections](localization.md#localizedcollections)-Element definieren und lokalisieren. Um nach einem Element aus einer **Enumeration** -Anspruchssammlung zu suchen, verwenden Sie die Anspruchstransformation [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection).

Das **Enumeration** -Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Text | Ja | Die Anzeigezeichenfolge, die dem Benutzer auf der Benutzeroberfläche für diese Option angezeigt wird. |
|Wert | Ja | Der Wert des Anspruchs, der der Auswahl dieser Option zugeordnet ist. |
| SelectByDefault | Nein | Gibt an, ob diese Option auf der Benutzeroberfläche standardmäßig ausgewählt werden soll. Mögliche Werte: „True“ oder „False“. |

Im folgenden Beispiel wird ein **city** -Dropdownlistenanspruch mit dem Standardwert `New York` konfiguriert:

```xml
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

![Dropdownsteuerelement im Browser mit Standardwert](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Muster

Das **Pattern** -Element kann die folgenden Attribute enthalten:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| RegularExpression | Ja | Der reguläre Ausdruck, mit dem Ansprüche dieses Typs übereinstimmen müssen, damit sie gültig sind. |
| HelpText | Nein | Eine Fehlermeldung für Benutzer, wenn die Überprüfung des regulären Ausdrucks einen Fehler ergibt. |

Im folgenden Beispiel wird ein **email** -Anspruch mit Eingabeüberprüfung mit regulärem Ausdruck und Hilfetext konfiguriert:

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Das Identity Experience Framework rendert den E-Mail-Adressenanspruch mit Eingabeüberprüfung des E-Mail-Formats:

![Textfeld mit einer Fehlermeldung, die von RegEx-Einschränkung ausgelöst wird](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C unterstützt eine Vielzahl von Benutzereingabetypen wie ein Textfeld, Kennwort und eine Dropdownliste, die bei der manuellen Eingabe von Anspruchsdaten für den Anspruchstyp verwendet werden können. Sie müssen **UserInputType** angeben, wenn Sie mit einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) und [Anzeigesteuerelementen](display-controls.md) Informationen vom Benutzer sammeln.

Das Element **UserInputType** verfügt über folgende Benutzereingabetypen:

| UserInputType | ClaimType wird unterstützt | BESCHREIBUNG |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Dropdownfeld mit Mehrfachauswahl. Der Anspruchswert wird durch eine Zeichenfolge der ausgewählten Werte mit Kommatrennzeichen dargestellt. |
|DateTimeDropdown | `date`, `dateTime` |Dropdownfeld zur Auswahl von Tag, Monat und Jahr. |
|DropdownSingleSelect |`string` |Dropdownfeld mit Einfachauswahl. Der Anspruchswert ist der ausgewählte Wert.|
|EmailBox | `string` |E-Mail-Eingabefeld. |
|Paragraph | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Feld, in dem Text nur in einem Absatztag angezeigt wird. |
|Kennwort | `string` |Kennworttextfeld.|
|RadioSingleSelect |`string` | Auflistung von Optionsfeldern. Der Anspruchswert ist der ausgewählte Wert.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Schreibgeschütztes Textfeld. |
|TextBox |`boolean`, `int`, `string` |Einzeiliges Textfeld. |


#### <a name="textbox"></a>TextBox

Der Benutzereingabetyp **TextBox** wird für die Bereitstellung eines einzeiligen Textfelds verwendet.

![Textfeld mit den im Anspruchstyp angegebenen Eigenschaften](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

Der Benutzereingabetyp **EmailBox** wird für die Bereitstellung eines einfachen E-Mail-Eingabefelds verwendet.

![E-Mail-Feld mit im Anspruchstyp angegebenen Eigenschaften](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Kennwort

Der Benutzereingabetyp **Password** wird für die Aufzeichnung eines vom Benutzer eingegebenen Kennworts verwendet.

![Verwenden des Anspruchstyps mit Kennwort](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

Der Benutzereingabetyp **DateTimeDropdown** wird für die Bereitstellung von Dropdownlisten verwendet, über die ein Tag, Monat und Jahr ausgewählt werden können. Sie können Prädikate und „PredicateValidations“-Elemente verwenden, um die minimalen und maximalen Datumswerte zu kontrollieren. Weitere Informationen finden Sie im Abschnitt **Configure a date range (Konfigurieren eines Datumsbereichs)** im Artikel [Predicates and PredicateValidations (Prädikate und PredicateValidations)](predicates.md).

![Verwenden des Anspruchstyps mit „datetimedropdown“](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

Der Benutzereingabetyp **RadioSingleSelect** wird für die Bereitstellung einer Sammlung von Optionsfeldern verwendet, mithilfe derer Benutzer eine Option auswählen können.

![Verwenden des Anspruchstyps mit „radiosingleselect“](./media/claimsschema/radiosingleselect.png)

```xml
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

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Der Benutzereingabetyp **DropdownSingleSelect** wird für die Bereitstellung einer Dropdownliste verwendet, mithilfe derer Benutzer eine Option auswählen können.

![Verwenden des Anspruchstyps mit „dropdownsingleselect“](./media/claimsschema/dropdownsingleselect.png)

```xml
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

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Der Benutzereingabetyp **CheckboxMultiSelect** wird für die Bereitstellung einer Sammlung von Kontrollkästchen verwendet, mithilfe derer Benutzer mehrere Optionen auswählen können.

![Verwenden des Anspruchstyps mit „checkboxmultiselect“](./media/claimsschema/checkboxmultiselect.png)

```xml
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

#### <a name="readonly"></a>Readonly

Der Benutzereingabetyp **Readonly** wird für die Bereitstellung eines schreibgeschützten Felds zum Anzeigen des Anspruchs und des Werts verwendet.

![Verwenden des Anspruchstyps mit „readonly“](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Paragraph

Der Benutzereingabetyp **Paragraph** wird für die Bereitstellung eines Felds verwendet, das Text nur in einem Absatztag anzeigt.  Zum Beispiel: &lt;p&gt;Text&lt;/p&gt;. Für einen Benutzereingabetyp **Paragraph** vom Typ `OutputClaim` des selbstbestätigten technischen Profils muss das Attribut `Required` auf `false` (Standardwert) eingestellt sein.

![Verwenden des Anspruchstyps mit „paragraph“](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
