---
title: Definieren eines selbstbestätigten technischen Profils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Definieren Sie ein selbstbestätigtes technisches Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 14195ad4638c724cf0c8dd46945a0da79ec0e4ec
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509699"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines selbstbestätigten technischen Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Alle Interaktionen in Azure Active Directory B2C (Azure AD B2C), bei denen vom Benutzer eine Eingabe erwartet wird, sind selbstbestätigte technische Profile. Beispiele: Registrierungsseite, Anmeldeseite oder Seite für die Kennwortzurücksetzung.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly als selbstbestätigt enthalten, die von Azure AD B2C verwendet wird: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Das folgende Beispiel zeigt ein selbstbestätigtes technisches Profil für eine Registrierung per E-Mail:

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Eingabeansprüche

In einem selbstbestätigten technischen Profil können Sie die Elemente **InputClaims** und **InputClaimsTransformations** verwenden, um den Wert der Ansprüche vorab mit Daten aufzufüllen, die auf der selbstbestätigten Seite angezeigt werden (Anzeigeansprüche). In der Richtlinie zur Profilbearbeitung liest beispielsweise die User Journey zunächst das Benutzerprofil aus dem Azure AD B2C-Verzeichnisdienst. Anschließend legt das selbstbestätigte technische Profil die Eingabeansprüchen mit den im Benutzerprofil gespeicherten Daten fest. Diese Ansprüche werden aus dem Benutzerprofil gesammelt und anschließend dem Benutzer vorgelegt, der die vorhandenen Daten dann bearbeiten kann.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Anzeigeansprüche

Diese Funktion „Anzeigeansprüche“ steht derzeit als **Vorschau** zur Verfügung.

Das **DisplayClaims**-Element enthält eine Liste mit anzuzeigenden Ansprüchen zum Erfassen von Daten vom Benutzer. Um Anzeigeansprüche vorab mit Werten aufzufüllen, verwenden Sie die zuvor beschriebenen Eingabeansprüche. Das Element kann darüber hinaus auch einen Standardwert enthalten.

Die Reihenfolge der Ansprüche in **DisplayClaims** bestimmt die Reihenfolge, in der Azure AD B2C die Ansprüche auf dem Bildschirm rendert. Um den Benutzer zur Eingabe eines Werts für einen bestimmten Anspruch zu zwingen, legen Sie das Attribut **Required** des **DisplayClaim**-Elements auf `true` fest.

Das **ClaimType**-Element in der **DisplayClaims**-Sammlung muss das **UserInputType**-Element auf einen von Azure AD B2C unterstützten Benutzereingabetyp festlegen. Zum Beispiel: `TextBox` oder `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Hinzufügen eines Verweises auf ein DisplayControl-Element

In die Sammlung der Anzeigeansprüche können Sie einen Verweis auf ein [DisplayControl](display-controls.md)-Element einbeziehen, das Sie erstellt haben. Ein Anzeigesteuerelement ist ein Benutzeroberflächenelement mit spezieller Funktionalität, das mit dem Azure AD B2C-Back-End-Dienst interagiert. Es ermöglicht dem Benutzer das Durchführen von Aktionen auf der Seite, die ein technisches Validierungsprofil auf dem Back-End aufrufen. Beispielsweise das Überprüfen einer E-Mail-Adresse, Telefonnummer oder Kundentreuenummer.

Das folgende Beispiel `TechnicalProfile` veranschaulicht die Verwendung von Anzeigeansprüchen mit Anzeigesteuerelementen.

* Der erste Anzeigeanspruch verweist auf das `emailVerificationControl`-Anzeigesteuerelement, das die E-Mail-Adresse erfasst und überprüft.
* Der fünfte Anzeigeanspruch verweist auf das `phoneVerificationControl`-Anzeigesteuerelement, das eine Telefonnummer erfasst und überprüft.
* Die anderen Anzeigeansprüche sind ClaimTypes, die vom Benutzer erfasst werden sollen.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Wie bereits erwähnt, kann ein Anzeigeanspruch mit einem Verweis auf ein Anzeigesteuerelement seine eigene Überprüfung durchführen, z.B. die Überprüfung der E-Mail-Adresse. Außerdem unterstützt die selbstbestätigte Seite die Verwendung eines technischen Validierungsprofils, um die gesamte Seite einschließlich aller Benutzereingaben (Anspruchstypen oder Anzeigesteuerelemente) zu validieren, bevor Sie mit dem nächsten Orchestrierungsschritt fortfahren.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Kombinieren Sie die Verwendung von Anzeigeansprüchen und Ausgabeansprüchen sorgfältig

Wenn Sie mindestens ein **DisplayClaim**-Element in einem selbstbestätigten technischen Profil angeben, müssen Sie einen DisplayClaim für *jeden* Anspruch verwenden, der auf dem Bildschirm angezeigt und vom Benutzer erfasst werden soll. Von einem selbstbestätigten technischen Profil, das mindestens einen Anzeigeanspruch enthält, werden keine Ausgabeansprüche angezeigt.

Sehen Sie sich das folgende Beispiel an, in dem ein `age`-Anspruch als **Ausgabe** anspruch in einer Basisrichtlinie definiert wird. Bevor dem selbstbestätigten technischen Profil Anzeigeansprüche hinzugefügt werden, wird der `age`-Anspruch auf dem Bildschirm für die Datensammlung angezeigt, die vom Benutzer erfasst wurde:

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Wenn eine Blattrichtlinie, die diese Basis nachfolgend erbt, `officeNumber` als **Anzeige** anspruch angibt:

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

Der `age`-Anspruch in der Basisrichtlinie wird dem Benutzer nicht mehr auf dem Bildschirm angezeigt. Er wird praktisch „ausgeblendet“. Zum Anzeigen des `age`-Anspruchs und zum Erfassen des Alterswerts vom Benutzer müssen Sie ein `age` **DisplayClaim**-Element hinzufügen.

## <a name="output-claims"></a>Ausgabeansprüche

Das **OutputClaims**-Element enthält eine Liste mit an den nächsten Orchestrierungsschritt zurückzugebenden Ansprüchen. Das **DefaultValue**-Attribut wird erst wirksam, wenn der Anspruch noch nie festgelegt wurde. Wenn er in einem vorherigen Orchestrierungsschritt festgelegt wurde, wird der Standardwert selbst dann nicht wirksam, wenn der Benutzer den Wert leer lässt. Um die Verwendung eines Standardwerts zu erzwingen, legen Sie das Attribut **AlwaysUseDefaultValue** auf `true` fest.

Aus Sicherheitsgründen ist ein Kennwortanspruchswert (`UserInputType` auf `Password`festgelegt) nur für die technischen Validierungsprofile des selbstbestätigten technischen Profils verfügbar. Der Kennwortanspruch kann in den nächsten Orchestrierungsschritten nicht verwendet werden. 

> [!NOTE]
> In früheren Versionen von Identity Experience Framework (IEF) wurden Ausgabeansprüche verwendet, um Daten vom Benutzer zu erfassen. Verwenden Sie zum Erfassen von Daten vom Benutzer stattdessen eine **DisplayClaims**-Sammlung.

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

### <a name="when-you-should-use-output-claims"></a>Unter welchen Umständen Ausgabeansprüche verwendet werden sollten

In einem selbstbestätigten technischen Profil gibt die Sammlung der Ausgabeansprüche die Ansprüche an den nächsten Orchestrierungsschritt zurück.

Verwenden Sie die Ausgabeansprüche in folgenden Fällen:

- **Ansprüche werden von der Ausgabeanspruchstransformation ausgegeben**.
- **Festlegen eines Standardwerts in einem Ausgabeanspruch**: Ohne Daten vom Benutzer zu erfassen oder die Daten aus dem technischen Validierungsprofil zurückgeben. Das selbstbestätigte technische Profil `LocalAccountSignUpWithLogonEmail` legt den Anspruch **executed-SelfAsserted-Input** auf `true` fest.
- **Ein technisches Validierungsprofil gibt die Ausgabeansprüche zurück**: Ihr technisches Profil ruft möglicherweise ein technisches Validierungsprofil auf, das einige Ansprüche zurückgibt. Sie sollten die Ansprüche sammeln und an die nächsten Orchestrierungsschritte in der User Journey zurückzugeben. Bei der Anmeldung mit einem lokalen Konto ruft beispielsweise das selbstbestätigte technische Profil mit dem Namen `SelfAsserted-LocalAccountSignin-Email` das technische Validierungsprofil mit dem Namen `login-NonInteractive` auf. Dieses technische Profil überprüft die Anmeldeinformationen des Benutzers und gibt das Benutzerprofil zurück. Beispiele: „userPrincipalName“, „displayName“, „givenName“ und „surName“.
- **Ein Anzeigesteuerelement gibt die Ausgabeansprüche zurück**: Ihr technisches Profil weist möglicherweise einen Verweis auf ein [Anzeigesteuerelement](display-controls.md) auf. Das Anzeigesteuerelement gibt einige Ansprüche zurück, beispielsweise die überprüfte E-Mail-Adresse. Sie sollten die Ansprüche sammeln und an die nächsten Orchestrierungsschritte in der User Journey zurückzugeben. Diese Funktion „Anzeigesteuerelement“ steht derzeit als **Vorschau** zur Verfügung.

Im folgenden Beispiel wird die Verwendung eines selbstbestätigten technischen Profils veranschaulicht, das sowohl Anzeigeansprüche als auch Ausgabeansprüche verwendet.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="output-claims-sign-up-or-sign-in-page"></a>Ausgabeansprüche für eine Registrierungs- oder Anmeldeseite

Bei einer kombinierten Registrierungs- und Anmeldeseite müssen Sie Folgendes beachten, wenn Sie ein [DataUri](contentdefinitions.md#datauri)-Element für die Inhaltsdefinition verwenden, das den Seitentyp `unifiedssp` oder `unifiedssd` festlegt:

- Nur die Ansprüche für den Benutzernamen und das Kennwort werden gerendert.
- Bei den ersten zwei Ausgabeansprüchen muss es sich um den Benutzernamen und das Kennwort handeln (in dieser Reihenfolge). 
- Alle anderen Ansprüche werden nicht gerendert. Für diese Ansprüche müssen Sie entweder `defaultValue` festlegen oder ein technisches Profil für die Validierung des Anspruchsformulars aufrufen. 

## <a name="persist-claims"></a>Beibehalten von Ansprüchen

Das PersistedClaims-Element wird nicht verwendet. Das selbstbestätigte technische Profil speichert die Daten nicht in Azure AD B2C. Stattdessen wird ein technisches Validierungsprofil aufgerufen, das für die Beibehaltung der Daten verantwortlich ist. So verwendet beispielsweise die Registrierungsrichtlinie das selbstbestätigte technische Profil `LocalAccountSignUpWithLogonEmail`, um das neue Benutzerprofil zu erfassen. Das technische Profil `LocalAccountSignUpWithLogonEmail` ruft das technische Validierungsprofil auf, um das Konto in Azure AD B2C zu erstellen.

## <a name="validation-technical-profiles"></a>Verwenden von technischen Validierungsprofilen

Ein technisches Validierungsprofil wird zur Überprüfung von einigen oder allen Ausgabeansprüchen des verweisenden technischen Profils verwendet. Die Eingabeansprüche des technischen Validierungsprofils müssen in den Ausgabeansprüchen des selbstbestätigten technischen Profils enthalten sein. Das technische Validierungsprofil überprüft die Benutzereingabe und kann einen Fehler an den Benutzer zurückgeben.

Das technische Validierungsprofil kann ein technisches Profil in der Richtlinie sein. Beispiele hierfür sind [Azure Active Directory](active-directory-technical-profile.md) oder ein technisches [REST-API](restful-technical-profile.md)-Profil. Im vorherigen Beispiel überprüft das technische Profil `LocalAccountSignUpWithLogonEmail`, ob der „signinName“ im Verzeichnis vorhanden ist. Ist dies nicht der Fall, erstellt das technische Validierungsprofil ein lokales Konto und gibt „objectId“, „authenticationSource“ und „newUser“ zurück. Das technische Profil `SelfAsserted-LocalAccountSignin-Email` ruft das technische Validierungsprofil `login-NonInteractive` auf, um die Anmeldeinformationen des Benutzers zu überprüfen.

Mit Ihrer Geschäftslogik können Sie durch eine weitere Integration in die Branchenanwendung auch ein technisches REST-API-Profil aufrufen, Eingabeansprüche überschreiben oder Benutzerdaten erweitern. Weitere Informationen finden Sie unter [Technisches Validierungsprofil](validation-technical-profile.md).

## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| Nein | Bei einer Anmeldeseite steuert diese Eigenschaft das Verhalten des Benutzernamensfelds also z.B. die Eingabeüberprüfung und Fehlermeldungen. Erwartete Werte: `Username` oder `Email`.  |
| AllowGenerationOfClaimsWithNullValues| Nein| Ermöglicht das Generieren eines Anspruchs mit Nullwert. Beispielsweise für den Fall, dass ein Benutzer ein Kontrollkästchen nicht aktiviert.|
| ContentDefinitionReferenceId | Ja | Der Bezeichner der [Inhaltsdefinition](contentdefinitions.md), die diesem technischen Profil zugeordnet ist. |
| EnforceEmailVerification | Nein | Für die Registrierungs- oder Profilbearbeitung, erzwingt eine E-Mail-Überprüfung. Mögliche Werte: `true` (Standard) oder `false`. |
| setting.retryLimit | Nein | Legt fest, wie viele Versuche ein Benutzer zur Eingabe der Daten hat, die anhand des technischen Validierungsprofils überprüft werden. Beispiel: Ein Benutzer versucht, ein Konto zu registrieren, das bereits vorhanden ist, und wiederholt den Vorgang, bis der Grenzwert erreicht ist.
| SignUpTarget <sup>1</sup>| Nein | Der Austauschbezeichner für das Registrierungsziel. Wenn der Benutzer auf die Schaltfläche „Registrieren“ klickt, führt Azure AD B2C den angegebenen Austauschbezeichner aus. |
| setting.showCancelButton | Nein | Zeigt die Schaltfläche „Abbrechen“ an. Mögliche Werte: `true` (Standard) oder `false` |
| setting.showContinueButton | Nein | Zeigt die Schaltfläche „Weiter“ an. Mögliche Werte: `true` (Standard) oder `false` |
| setting.showSignupLink <sup>2</sup>| Nein | Zeigt die Schaltfläche „Registrieren“ an. Mögliche Werte: `true` (Standard) oder `false` |
| setting.forgotPasswordLinkLocation <sup>2</sup>| Nein| Zeigt den Link „Kennwort vergessen“ an. Mögliche Werte: `AfterInput` (Standard) Der Link wird unten auf der Seite angezeigt, oder `None` entfernt den Link „Kennwort vergessen“.|
| setting.enableRememberMe <sup>2</sup>| Nein| Zeigt das Kontrollkästchen [Angemeldet bleiben](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) an. Mögliche Werte sind `true` oder `false` (Standardwert). |
| setting.inputVerificationDelayTimeInMilliseconds <sup>3</sup>| Nein| Verbessert die Benutzererfahrung, indem gewartet wird, bis der Benutzer die Eingabe beendet hat. Dann wird der Wert überprüft. Der Standardwert ist 2.000 Millisekunden. |
| IncludeClaimResolvingInClaimsHandling  | Nein | Gibt bei Eingabe- und Ausgabeansprüchen an, ob die [Anspruchsauflösung](claim-resolver-overview.md) im technischen Profil enthalten ist. Mögliche Werte sind `true` oder `false` (Standardwert). Wenn Sie im technischen Profil eine Anspruchsauflösung verwenden möchten, legen Sie für diese Einstellung den Wert `true` fest. |

Hinweise:
1. Verfügbar für die Inhaltsdefinition: [DataUri](contentdefinitions.md#datauri), Typ `unifiedssp` oder `unifiedssd`.
1. Verfügbar für die Inhaltsdefinition: [DataUri](contentdefinitions.md#datauri), Typ `unifiedssp` oder `unifiedssd`. [Seitenlayoutversion](page-layout.md) 1.1.0 und höher.
1. Verfügbar für die [Seitenlayoutversion](page-layout.md) 1.2.0 und höher.

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Das Element **CryptographicKeys** wird nicht verwendet.
