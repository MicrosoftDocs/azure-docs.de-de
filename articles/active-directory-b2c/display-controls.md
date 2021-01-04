---
title: Referenz zu Anzeigesteuerelementen
titleSuffix: Azure AD B2C
description: Referenz zu Anzeigesteuerelementen für Azure AD B2C Verwendung von Anzeigesteuerelementen zur Anpassung von Benutzerjourneys, die in benutzerdefinierten Richtlinien definiert sind
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387053"
---
# <a name="display-controls"></a>Anzeigesteuerelemente

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ein **Anzeigesteuerelement** ist ein Benutzeroberflächenelement mit spezieller Funktionalität, das mit dem Back-End-Dienst von Azure Active Directory B2C (Azure AD B2C) interagiert. Es ermöglicht dem Benutzer das Durchführen von Aktionen auf der Seite, die ein [technisches Validierungsprofil](validation-technical-profile.md) auf dem Back-End aufrufen. Anzeigesteuerelemente werden auf der Seite angezeigt, und ein [selbstbestätigtes technisches Profil](self-asserted-technical-profile.md) verweist auf diese.

In der folgenden Abbildung wird eine selbstbestätigte Registrierungsseite mit zwei Anzeigesteuerelementen veranschaulicht, die eine primäre und sekundäre E-Mail-Adresse überprüfen.

![Beispiel: gerendertes Anzeigesteuerelement](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Voraussetzungen

 Im Abschnitt [Metadaten](self-asserted-technical-profile.md#metadata) eines [selbstbestätigten technischen Profils](self-asserted-technical-profile.md) muss für das [ContentDefinition](contentdefinitions.md)-Element, auf das verwiesen wird, `DataUri` auf Seitenvertragsversion 2.0.0 oder höher festgelegt sein. Beispiel:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definieren von Anzeigesteuerelementen

Das **DisplayControl**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner, der für das Anzeigesteuerelement verwendet wird. Auf diesen kann [verwiesen](#referencing-display-controls) werden. |
| UserInterfaceControlType | Ja | Der Typ des Anzeigesteuerelements. Derzeit wird [VerificationControl](display-control-verification.md) unterstützt. |

Das **DisplayControl**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** wird verwendet, um den Wert der Ansprüche, die vom Benutzer erfasst werden sollen, vorab auszufüllen. Weitere Informationen finden Sie unter dem Element [Input Claims](technicalprofiles.md#input-claims). |
| DisplayClaims | 0:1 | **DisplayClaims** wird verwendet, um die Ansprüche darzustellen, die vom Benutzer erfasst werden sollen. Weitere Informationen finden Sie unter dem Element [DisplayClaim](technicalprofiles.md#displayclaim).|
| OutputClaims | 0:1 | **OutputClaims** wird verwendet, um die Ansprüche darzustellen, die temporär für **DisplayControl** gespeichert werden sollen. Weitere Informationen finden Sie unter dem Element [OutputClaims](technicalprofiles.md#output-claims).|
| Aktionen | 0:1 | **Actions** wird verwendet, um die technischen Validierungsprofile aufzulisten, die für Benutzeraktionen am Front-End aufgerufen werden sollen. |

### <a name="input-claims"></a>Eingabeansprüche

In einem Anzeigesteuerelement können Sie **InputClaims**-Elemente verwenden, um den Wert von Ansprüchen vorab auszufüllen, die vom Benutzer der Seite erfasst werden sollen. **InputClaimsTransformations**-Elemente können im selbstbestätigten technischen Profil definiert werden, das auf dieses Anzeigesteuerelement verweist.

Im folgenden Beispiel wird die E-Mail-Adresse, die überprüft werden soll, mit der bereits vorhandenen Adresse vorab aufgefüllt.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Anzeigeansprüche

Jede Art von Anzeigesteuerelement erfordert andere Anzeigeansprüche, [Ausgabeansprüche](#output-claims) und [Aktionen](#display-control-actions), die ausgeführt werden müssen.

Ähnlich wie die **Anzeigeansprüche** in einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md#display-claims) sind die Anzeigeansprüche die Ansprüche, die im Anzeigesteuerelement vom Benutzer erfasst werden sollen. Das **ClaimType**-Element, auf das verwiesen wird, muss das **UserInputType**-Element für einen von Azure AD B2C unterstützten Benutzereingabetyp angeben, wie etwa `TextBox` oder `DropdownSingleSelect`. Wenn der Wert eines Anzeigeanspruchs für ein **Action**-Element erforderlich ist, legen Sie das **Required**-Attribut auf `true` fest, damit der Benutzer einen Wert für diesen Anzeigeanspruch angeben muss.

Bestimmte Anzeigeansprüche sind für bestimmte Arten von Anzeigesteuerelementen erforderlich. **VerificationCode** ist beispielsweise für **VerificationControl**-Anzeigesteuerelemente erforderlich. Verwenden Sie das-Attribut **ControlClaimType**, um anzugeben, welches DisplayClaim-Element für den erforderlichen Anspruch festgelegt ist. Beispiel:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Ausgabeansprüche

Die **Ausgabeansprüche** eines Anzeigesteuerelements werden nicht an den nächsten Orchestrierungsschritt gesendet. Sie werden nur für die aktuelle Sitzung gespeichert, in der das Anzeigesteuerelement verwendet wird. Diese temporären Ansprüche können von den verschiedenen Aktionen desselben Anzeigesteuerelements gemeinsam genutzt werden.

Verwenden Sie das **OutputClaims**-Element des selbstbestätigten technischen Profils, das auf dieses Anzeigesteuerelement verweist, um die Ausgabeansprüche an den nächsten Orchestrierungsschritt weiterzugeben.

### <a name="display-control-actions"></a>Actions-Anzeigesteuerelement

Die **Actions**-Elemente eines Anzeigesteuerelements sind Prozeduren, die im Back-End von Azure AD B2C auftreten, wenn ein Benutzer clientseitig (also im Browser) bestimmte Aktionen durchführt. Dazu zählen beispielsweise die Validierungen, die durchgeführt werden müssen, wenn der Benutzer auf eine Schaltfläche auf der Seite klickt.

Eine Aktion definiert eine Liste mit **technischen Validierungsprofilen**. Diese werden zum Validieren einiger oder aller Anzeigeansprüche des Anzeigesteuerelements verwendet. Das technische Validierungsprofil überprüft die Benutzereingabe und kann einen Fehler an den Benutzer zurückgeben. Sie können **ContinueOnError**, **ContinueOnSuccess** und **Preconditions** so im Action-Element des Anzeigesteuerelements verwenden, wie diese in [technischen Validierungsprofilen](validation-technical-profile.md) in einem selbstbestätigten technischen Profil verwendet werden.

#### <a name="actions"></a>Aktionen

Das Element **Actions** enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Aktion | 1:n | Liste der auszuführenden Aktionen. |

#### <a name="action"></a>Aktion

Das Element **Action** enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Der Typ des Vorgangs. Mögliche Werte: `SendCode` oder `VerifyCode`. Der Wert `SendCode` sendet einen Code an den Benutzer. Diese Aktion kann zwei technische Validierungsprofile enthalten: eines zum Generieren eines Codes und eines zum Senden. Der `VerifyCode` Wert überprüft den Code, den der Benutzer im Eingabetextfeld eingegeben hat. |

Das Element **Action** enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | Die Bezeichner von technischen Profilen, mit denen einige oder alle Anzeigeansprüche des verweisenden technischen Profils überprüft werden. Alle Eingabeansprüche des technischen Profils, auf das verwiesen wird, müssen in den Anzeigeansprüchen des verweisenden technischen Profils enthalten sein. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

Das Element **ValidationClaimsExchange** enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Ein technisches Profil, mit dem einige oder alle Anzeigeansprüche des verweisenden technischen Profils überprüft werden sollen. |

Das Element **ValidationTechnicalProfile** enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner eines technischen Profils, das bereits in der Richtlinie oder der übergeordneten Richtlinie definiert ist. |
|ContinueOnError|Nein| Gibt an, ob die Validierung aller nachfolgenden technischen Validierungsprofile fortgesetzt werden soll, wenn dieses technische Validierungsprofil einen Fehler auslöst. Mögliche Werte: `true` oder `false` (Standard; die Verarbeitung weiterer Validierungsprofile wird beendet, und ein Fehler zurückgegeben). |
|ContinueOnSuccess | Nein | Gibt an, ob die Validierung aller nachfolgenden Validierungsprofile fortgesetzt werden soll, wenn dieses technische Validierungsprofil erfolgreich ist. Mögliche Werte: `true` oder `false`. Der Standardwert ist `true`, was bedeutet, dass die Verarbeitung weiterer Validierungsprofile fortgesetzt wird. |

Das **ValidationTechnicalProfile**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | Eine Liste von Vorbedingungen, die für die Ausführung des technischen Validierungsprofil erfüllt sein müssen. |

Das **Precondition**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| `Type` | Ja | Der Typ der Überprüfung oder Abfrage, die für die Vorbedingung ausgeführt werden soll. Mögliche Werte: `ClaimsExist` oder `ClaimEquals`. `ClaimsExist` gibt an, dass die Aktionen ausgeführt werden sollen, wenn der aktuelle Anspruchssatz des Benutzers die angegebenen Ansprüche enthält. `ClaimEquals` gibt an, dass die Aktionen ausgeführt werden sollen, wenn der angegebene Anspruch vorhanden und dessen Wert gleich dem angegebenen Wert ist. |
| `ExecuteActionsIf` | Ja | Zeigt an, ob die Aktionen in der Vorbedingung ausgeführt werden sollen, wenn der Test „true“ oder „false“ ist. |

Das **Precondition**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Wert | 1:n | Die Daten, die von der Überprüfung verwendet werden. Wenn der Typ dieser Überprüfung `ClaimsExist` ist, gibt dieses Feld eine ClaimTypeReferenceId an, die abzufragen ist. Wenn der Typ der Überprüfung `ClaimEquals` ist, gibt dieses Feld eine ClaimTypeReferenceId an, die abzufragen ist. Geben Sie den Wert an, der in einem anderen Wertelement überprüft werden soll.|
| Aktion | 1:1 | Die Aktion, die ausgeführt werden soll, wenn die Überprüfung der Vorbedingung innerhalb eines Orchestrierungsschritts „true“ ist. Der Wert der **Aktion** wird auf `SkipThisValidationTechnicalProfile` festgelegt und dadurch angegeben, dass das zugeordnete technische Validierungsprofil nicht ausgeführt werden soll. |

Im folgenden Beispiel wird mithilfe von [ technisches Profil für die Azure AD-Self-Service-Kennwortzurücksetzung](aad-sspr-technical-profile.md) die E-Mail-Adresse gesendet und überprüft.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

Im folgenden Beispiel wird ein Code – abhängig von der Auswahl des Benutzers im Anspruch **mfaType** mit Vorbedingungen – per E-Mail oder SMS gesendet.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Verweisen auf Anzeigesteuerelemente

Auf Anzeigesteuerelemente wird in den [Anzeigeansprüchen](self-asserted-technical-profile.md#display-claims) des [selbstbestätigten technischen Profils](self-asserted-technical-profile.md) verwiesen.

Beispiel:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Nächste Schritte

Beispiele für die Verwendung von Anzeigesteuerelementen finden Sie unter: 

- [Benutzerdefinierte E-Mail-Überprüfung mit Mailjet](custom-email-mailjet.md)
- [Benutzerdefinierte E-Mail-Überprüfung mit SendGrid](custom-email-sendgrid.md)
