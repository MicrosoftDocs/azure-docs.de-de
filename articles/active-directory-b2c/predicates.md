---
title: „Predicates“ und „PredicateValidations“
titleSuffix: Azure AD B2C
description: Verhindern mit benutzerdefinierten Richtlinien in Azure Active Directory B2C, dass falsch formatierte Daten dem Azure AD B2C-Mandanten hinzugefügt werden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 46f04c55b40d4f1bdbbf5fd55eb648d1d3294056
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108415"
---
# <a name="predicates-and-predicatevalidations"></a>„Predicates“ und „PredicateValidations“

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mithilfe der Elemente **Predicates** und **PredicateValidations** können Sie einen Überprüfungsprozess ausführen, um sicherzustellen, dass nur ordnungsgemäß formatierte Daten in Ihren Azure Active Directory B2C-Mandanten (Azure AD B2C) eingegeben werden.

Im folgenden Diagramm ist die Beziehung zwischen den Elementen dargestellt:

![Diagramm mit Beziehung zwischen Prädikaten und Prädikatüberprüfungen](./media/predicates/predicates.png)

## <a name="predicates"></a>Prädikate

Mit dem Element **Predicate** wird für den Wert eines Anspruchstyps eine grundlegende Überprüfung definiert und `true` oder `false` zurückgegeben. Die Überprüfung wird mit einem angegebenen **Method**-Element und einer Reihe von **Parameter**-Elementen ausgeführt, die für die Methode relevant sind. So können Sie mit einem „Predicate“-Element beispielsweise überprüfen, ob die Länge eines Zeichenfolgen-Anspruchswerts innerhalb des Bereichs der angegebenen minimalen und maximalen Parameter liegt oder ob ein Zeichenfolgen-Anspruchswert einen bestimmten Zeichensatz enthält. Das **UserHelpText**-Element stellt eine Fehlermeldung für Benutzer bereit, wenn die Überprüfung einen Fehler ergibt. Der Wert des **UserHelpText**-Elements kann mithilfe der [Sprachanpassung](localization.md) lokalisiert werden.

Das **Predicates**-Element muss direkt nach dem **ClaimsSchema**-Element im [BuildingBlocks](buildingblocks.md)-Element aufgeführt werden.

Das **Predicates**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Predicate | 1:n | Eine Liste mit Prädikaten. |

Das **Predicate**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner, der für das Prädikat verwendet wird. Andere Elemente können diesen Bezeichner in der Richtlinie verwenden. |
| Methode | Ja | Der für die Überprüfung zu verwendende Methodentyp. Mögliche Werte: [IsLengthRange](#islengthrange), [MatchesRegex](#matchesregex), [IncludesCharacters](#includescharacters) oder [IsDateRange](#isdaterange).  |
| HelpText | Nein | Eine Fehlermeldung für Benutzer, wenn die Überprüfung einen Fehler ergibt. Diese Zeichenfolge kann mithilfe der [Sprachanpassung](localization.md) lokalisiert werden. |

Das **Predicate**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | (Veraltet) Eine Fehlermeldung für Benutzer, wenn die Überprüfung einen Fehler ergibt. |
| Parameter | 1:1 | Die Parameter für den Methodentyp der Zeichenfolgenüberprüfung. |

Das **Parameters**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Parameter | 1:n | Die Parameter für den Methodentyp der Zeichenfolgenüberprüfung. |

Das **Parameter**-Element enthält die folgenden Attribute:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Id | 1:1 | Der Bezeichner des Parameters. |

### <a name="predicate-methods"></a>Prädikatmethoden

#### <a name="islengthrange"></a>IsLengthRange

Mit der Methode „IsLengthRange“ wird überprüft, ob die Länge eines Zeichenfolgen-Anspruchswerts innerhalb des Bereichs der angegebenen minimalen und maximalen Parameter liegt. Das „predicate“-Element unterstützt die folgenden Parameter:

| Parameter | Erforderlich | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Maximum | Ja | Die maximale Anzahl von Zeichen, die eingegeben werden können. |
| Minimum | Ja | Die minimale Anzahl von Zeichen, die eingegeben werden müssen. |


Das folgende Beispiel zeigt eine „IsLengthRange“-Methode mit den Parametern `Minimum` und `Maximum`, die den Längenbereich der Zeichenfolge angeben:

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchesRegex

Mit der Methode „MatchesRegex“ wird überprüft, ob ein Zeichenfolgen-Anspruchswert einem regulären Ausdruck entspricht. Das „predicate“-Element unterstützt die folgenden Parameter:

| Parameter | Erforderlich | Beschreibung |
| ------- | ----------- | ----------- |
| RegularExpression | Ja | Das Muster eines regulären Ausdrucks, mit dem Übereinstimmungen gefunden werden sollen. |

Das folgende Beispiel zeigt eine `MatchesRegex`-Methode mit dem Parameter `RegularExpression`, der einen regulären Ausdruck angibt:

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>IncludesCharacters

Mit der Methode „IncludesCharacters“ wird überprüft, ob ein Zeichenfolgen-Anspruchswert einen Zeichensatz enthält. Das „predicate“-Element unterstützt die folgenden Parameter:

| Parameter | Erforderlich | Beschreibung |
| ------- | ----------- | ----------- |
| CharacterSet | Ja | Der Zeichensatz, der eingegeben werden kann. Dies sind beispielsweise Kleinbuchstaben `a-z`, Großbuchstaben `A-Z`, Ziffern `0-9` oder eine Liste von Symbolen wie `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!`. |

Das folgende Beispiel zeigt eine `IncludesCharacters`-Methode mit dem Parameter `CharacterSet`, der den Zeichensatz angibt:

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

Mit der Methode „IsDateRange“ wird überprüft, ob ein Datumsanspruchswert innerhalb eines Bereichs von angegebenen minimalen und maximalen Parametern liegt. Das „predicate“-Element unterstützt die folgenden Parameter:

| Parameter | Erforderlich | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Maximum | Ja | Das größtmögliche Datum, das eingegeben werden kann. Das Format des Datums entspricht der Konvention `yyyy-mm-dd` oder `Today`. |
| Minimum | Ja | Das kleinstmögliche Datum, das eingegeben werden kann. Das Format des Datums entspricht der Konvention `yyyy-mm-dd` oder `Today`.|

Das folgende Beispiel zeigt eine `IsDateRange`-Methode mit den Parametern `Minimum` und `Maximum`, die den Datumsbereich im Format `yyyy-mm-dd` und `Today` angeben:

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Während mit dem „Predicates“-Element die Überprüfung anhand eines Anspruchstyps definiert wird, werden mit dem **PredicateValidations**-Element mehrere „Predicates“-Elemente zu einer Benutzereingabe-Überprüfung gruppiert, die auf einen Anspruchstyp angewendet werden kann. Jedes **PredicateValidation**-Element enthält mehrere **PredicateGroup**-Elemente, die eine Reihe von **PredicateReference**-Elementen enthalten, die auf ein **Predicate**-Element zeigen. Um die Überprüfung zu bestehen, muss der Wert des Anspruchs alle Tests aller „Predicate“-Elemente in allen **PredicateGroup**-Elementen mit ihren **PredicateReference**-Elementen bestehen.

Das **PredicateValidations**-Element muss direkt nach dem **Predicates**-Element im [BuildingBlocks](buildingblocks.md)-Element aufgeführt werden.

```xml
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

Das **PredicateValidations**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | Eine Liste mit Prädikatüberprüfungen. |

Das **PredicateValidation**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner, der für die Prädikatüberprüfung verwendet wird. Das **ClaimType**-Element kann diesen Bezeichner in der Richtlinie verwenden. |

Das **PredicateValidation**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | Eine Liste mit Prädikatgruppen. |

Das **PredicateGroups**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | Eine Liste mit Prädikaten. |

Das **PredicateGroups**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner, der für die Prädikatgruppe verwendet wird.  |

Das **PredicateGroups**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  Eine Beschreibung des Prädikats, die nützlich sein kann, damit Benutzer wissen, welchen Wert sie eingeben müssen. |
| PredicateReferences | 1:n | Eine Liste mit Prädikatverweisen. |

Das **PredicateReferences**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| MatchAtLeast | Nein | Gibt an, dass der Wert mindestens der Anzahl von Prädikatdefinitionen für die Eingabe entsprechen muss, um angenommen zu werden. Ohne diese Angabe muss der Wert allen Prädikatdefinitionen entsprechen. |

Das **PredicateReferences**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | Ein Verweis auf ein Prädikat. |

Das **PredicateReference**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner, der für die Prädikatüberprüfung verwendet wird.  |


## <a name="configure-password-complexity"></a>Konfigurieren der Kennwortkomplexität

Mit den Elementen **Predicates** und **PredicateValidationsInput** können Sie die Anforderungen in Bezug auf die Komplexität von Kennwörtern steuern, die von Benutzern beim Erstellen eines Kontos angegeben werden. Standardmäßig verwendet Azure AD B2C starke Kennwörter. Azure AD B2C unterstützt außerdem Konfigurationsoptionen zur Steuerung der Komplexität von Kennwörtern, die Kunden verwenden können. Sie können die Kennwortkomplexität mithilfe der folgenden „Predicate“-Elementen definieren:

- **IsLengthBetween8And64** überprüft mithilfe der `IsLengthRange`-Methode, ob das Kennwort zwischen 8 und 64 Zeichen aufweist.
- **Lowercase** überprüft mithilfe der `IncludesCharacters`-Methode, ob das Kennwort Kleinbuchstaben enthält.
- **Uppercase** überprüft mithilfe der `IncludesCharacters`-Methode, ob das Kennwort Großbuchstaben enthält.
- **Number** überprüft mithilfe der `IncludesCharacters`-Methode, ob das Kennwort Ziffern enthält.
- **Symbol** überprüft mithilfe der `IncludesCharacters`-Methode, ob das Kennwort eines von verschiedenen Symbolzeichen enthält.
- **PIN** überprüft mithilfe der `MatchesRegex`-Methode, ob das Kennwort nur Zahlen enthält.
- **AllowedAADCharacters** überprüft mithilfe der `MatchesRegex`-Methode, ob das Kennwort keine ungültigen Zeichen enthält.
- **DisallowedWhitespace** überprüft mithilfe der `MatchesRegex`-Methode, ob das Kennzeichen mit einem Leerzeichen beginnt oder endet.

```xml
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
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

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Nachdem Sie die grundlegenden Überprüfungen definiert haben, können Sie sie kombinieren und eine Reihe von Kennwortrichtlinien erstellen, die Sie in Ihrer Richtlinie verwenden können:

- **SimplePassword** überprüft die Elemente „DisallowedWhitespace“, „AllowedAADCharacters“ und „IsLengthBetween8And64“.
- **StrongPassword** überprüft die Elemente „DisallowedWhitespace“, „AllowedAADCharacters“ und „IsLengthBetween8And64“. Die letzte `CharacterClasses`-Gruppe führt ein zusätzliche Reihe von „Predicate“-Elementen aus, wobei `MatchAtLeast` auf 3 festgelegt ist. Das Benutzerkennwort muss aus 8 bis 16 Zeichen bestehen, wobei drei der Zeichen den folgenden Elementen entsprechen müssen: „Lowercase“, „Uppercase“, „Number“ oder „Symbol“.
- **CustomPassword** überprüft nur die Elemente „DisallowedWhitespace“ und „AllowedAADCharacters“. Der Benutzer kann somit ein beliebiges Kennwort mit einer beliebigen Länge eingeben, solange die Zeichen gültig sind.

```xml
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Fügen Sie in Ihrem Anspruchstyp das **PredicateValidationReference**-Element hinzu, und geben Sie den Bezeichner als eine der Prädikatüberprüfungen an wie etwa „SimplePassword“, „StrongPassword“ oder „CustomPassword“.

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Das folgende Beispiel zeigt, wie die Elemente angeordnet sind, wenn Azure AD B2C die Fehlermeldung anzeigt:

![Diagramm mit Beispiel für Kennwortkomplexität bei Prädikat und Prädikatgruppe](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurieren eines Datumsbereichs

Mit den Elementen **Predicates** und **PredicateValidations** können Sie die minimalen und maximalen Datumswerte von **UserInputType** mithilfe von `DateTimeDropdown` festlegen. Erstellen Sie hierzu ein **Predicate**-Element mit der `IsDateRange`-Methode, und geben Sie die minimalen und maximalen Parameter an.

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Fügen Sie ein **PredicateValidation**-Element mit einem Verweis auf das `DateRange`-Prädikat hinzu.

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Fügen Sie in Ihrem Anspruchstyp ein **PredicateValidationReference**-Element hinzu, und geben Sie den Bezeichner als `CustomDateRange` an.

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Konfigurieren der Kennwortkomplexität mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C](password-complexity.md) unter Verwendung von Prädikatüberprüfungen.