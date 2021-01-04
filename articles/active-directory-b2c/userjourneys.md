---
title: UserJourneys | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das UserJourneys-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b89126b837f9c197a8babf81abb17bfd98002e4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344996"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

User Journeys geben explizite Pfade an, über die eine Richtlinie einer Anwendung der vertrauenden Seite die gewünschten Ansprüche für einen Benutzer abrufen kann. Der Benutzer wird über diese Pfade weitergeleitet, um die Ansprüche abzurufen, die an die vertrauenden Seite übermittelt werden sollen. Das heißt, User Journeys definieren die Geschäftslogik, die ein Endbenutzer durchläuft, während das Framework für die Identitätsfunktion von Azure AD B2C die Anforderung verarbeitet.

Diese User Journeys können als Vorlagen gesehen werden, die verfügbar sind, um die Grundbedürfnisse der verschiedenen vertrauenden Seiten der Interessengemeinschaft abzudecken. User Journeys unterstützen die Definition des Teils der vertrauenden Seite einer Richtlinie. Eine Richtlinie kann mehrere User Journeys definieren. Jede User Journey besteht aus einer Sequenz von Orchestrierungsschritten.

Ein **UserJourneys**-Element wird unter dem allgemeinsten Element der Richtliniendatei hinzugefügt, um die von der Richtlinie unterstützten User Journeys zu definieren.

Das **UserJourneys**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Eine User Journey, die alle für den Benutzerflow erforderlichen Konstrukte definiert. |

Das **UserJourney**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner einer User Journey, der verwendet werden kann, um über andere Elemente in der Richtlinie auf sie zu verweisen. Das **DefaultUserJourney**-Element der [Richtlinie der vertrauenden Seite](relyingparty.md) zeigt auf dieses Attribut. |

Das **UserJourney**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Eine Orchestrierungssequenz, die für eine erfolgreiche Transaktion durchlaufen werden muss. Jede User Journey besteht aus einer geordneten List von Orchestrierungsschritten, die nacheinander ausgeführt werden. Wenn ein Schritt fehlschlägt, schlägt die Transaktion fehl. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Eine User Journey wird als Orchestrierungssequenz dargestellt, die für eine erfolgreiche Transaktion durchlaufen werden muss. Wenn ein Schritt fehlschlägt, schlägt die Transaktion fehl. Diese Orchestrierungsschritte verweisen sowohl auf die Bausteine als auch auf die Anspruchsanbieter, die in der Richtliniendatei zugelassen werden. Jeder Orchestrierungsschritt, der für das Anzeigen oder Rendern einer Benutzeroberfläche verantwortlich ist, verfügt auch über einen Verweis auf den Bezeichner für die entsprechende Inhaltsdefinition.

Orchestrierungsschritte können anhand von Voraussetzungen, die im OrchestrationSteps-Element definiert werden, bedingungsabhängig ausgeführt werden. Beispielsweise können Sie konfigurieren, dass ein Orchestrierungsschritt nur ausgeführt wird, wenn bestimmte Ansprüche vorhanden sind oder aber ein Anspruch dem angegebenen Wert entspricht oder nicht.

Ein **OrchestrationSteps**-Element wird als Teil der Richtlinie hinzugefügt, um die geordnete Liste der Orchestrierungsschritte festzulegen. Dieses Element ist erforderlich.

Das **OrchestrationSteps**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Ein geordneter Orchestrierungsschritt. |

Das **OrchestrationStep**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Order` | Ja | Die Reihenfolge der Orchestrierungsschritte. |
| `Type` | Ja | Der Typ des Orchestrierungsschritts. Mögliche Werte: <ul><li>**ClaimsProviderSelection:** Gibt an, dass der Orchestrierungsschritt verschiedene Anspruchsanbieter darstellt, von denen der Benutzer einen auswählen kann.</li><li>**CombinedSignInAndSignUp:** Gibt an, dass der Orchestrierungsschritt eine kombinierte Seite für die Anmeldung eines Social Media-Anbieters und die Registrierung eines lokalen Kontos darstellt.</li><li>**ClaimsExchange:** Gibt an, dass der Orchestrierungsschritt Ansprüche mit einem Anspruchsanbieter austauscht.</li><li>**GetClaims:** Gibt an, dass der Orchestrierungsschritt Anspruchsdaten verarbeiten soll, die von der vertrauenden Seite über ihre `InputClaims`-Konfiguration an Azure AD B2C gesendet werden.</li><li>**InvokeSubJourney**: Gibt an, dass der Orchestrierungsschritt Ansprüche mit einer [untergeordneten Journey](subjourneys.md) (in der öffentlichen Vorschau) austauscht.</li><li>**SendClaims:** Gibt an, dass der Orchestrierungsschritt die Ansprüche an die vertrauende Seite mit einem Token übermittelt, das von einem Anspruchsaussteller ausgestellt wurde.</li></ul> |
| ContentDefinitionReferenceId | Nein | Der Bezeichner der [Inhaltsdefinition](contentdefinitions.md), die diesem Orchestrierungsschritt zugeordnet ist. In der Regel wird der Bezeichner für den Verweis auf die Inhaltsdefinition im selbstbestätigten technischen Profil definiert. Jedoch gibt es einige Fälle, in denen Azure AD B2C etwas ohne technisches Profil anzeigen muss. Hier sind zwei Beispiele: Wenn der Orchestrierungsschritt vom Typ `ClaimsProviderSelection` oder `CombinedSignInAndSignUp` ist, muss Azure AD B2C die Auswahl des Identitätsanbieters auch ohne technisches Profil anzeigen. |
| CpimIssuerTechnicalProfileReferenceId | Nein | Der Typ des Orchestrierungsschritts ist `SendClaims`. Diese Eigenschaft definiert den Bezeichner für das technische Profil des Anspruchsanbieters, der das Token für die vertrauende Seite ausstellt.  Wenn sie nicht vorhanden ist, wird kein Token für die vertrauende Seite erstellt. |

Das **OrchestrationStep**-Element kann die folgenden Elemente enthalten:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | Eine Liste von Voraussetzungen, die für die Ausführung des Orchestrierungsschritts erfüllt sein müssen. |
| ClaimsProviderSelections | 0:n | Eine Liste von Auswahloptionen für Anspruchsanbieter für den Orchestrierungsschritts. |
| ClaimsExchanges | 0:n | Eine Liste von Anspruchsaustauschvorgängen für den Orchestrierungsschritt. |
| JourneyList | 0:1 | Eine Liste der möglichen untergeordneten Journeys für den Orchestrierungsschritt. |

### <a name="preconditions"></a>Preconditions

Das **Preconditions**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Precondition | 1:n | Abhängig vom verwendeten technischen Profil, wird der Client entweder gemäß der Anspruchsanbieterauswahl weitergeleitet oder ein Serveraufruf wird zum Austauschen von Ansprüchen ausgeführt. |


#### <a name="precondition"></a>Precondition

Das **Precondition**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| `Type` | Ja | Der Typ der Überprüfung oder Abfrage, die für diese Voraussetzung ausgeführt werden soll. Dieser Wert kann **ClaimsExist** sein, wodurch festgelegt wird, dass die Aktionen durchgeführt werden sollen, wenn die angegebenen Ansprüche in den aktuellen Ansprüchen des Benutzers vorhanden sind. Alternativ kann der Wert **ClaimEquals** sein, wodurch festgelegt wird, dass die Aktionen durchgeführt werden sollen, wenn der angegebene Anspruch vorhanden ist und sein Wert dem angegebenen Wert gleicht. |
| `ExecuteActionsIf` | Ja | Verwenden Sie einen booleschen Test (TRUE oder FALSE), um zu entscheiden, ob die Aktionen in der Voraussetzung ausgeführt werden sollen. |

Das **Precondition**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Wert | 1:n | Ein ClaimTypeReferenceId-Wert, der abgefragt werden soll. Ein anderes Wertelement enthält den Wert, der überprüft werden soll.</li></ul>|
| Aktion | 1:1 | Die Aktion, die ausgeführt werden soll, wenn die Überprüfung der Voraussetzungen innerhalb eines Orchestrierungsschritts TRUE ergibt. Wenn der Wert von `Action` auf `SkipThisOrchestrationStep` festgelegt ist, wird das zugeordnete `OrchestrationStep`-Element nicht ausgeführt. |

#### <a name="preconditions-examples"></a>Beispiele für Voraussetzungen

Die folgenden Voraussetzungen überprüfen, ob der objectId-Wert des Benutzers vorhanden ist. Der Benutzer hat in der User Journey die Anmeldung mit einem lokalen Konto ausgewählt. Überspringen Sie diesen Orchestrierungsschritt, wenn der objectID-Wert vorhanden ist.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Die folgenden Voraussetzungen überprüfen, ob der Benutzer sich mit einem Social Media-Konto angemeldet hat. Es wird versucht, das Benutzerkonto im Verzeichnis zu finden. Überspringen Sie diesen Orchestrierungsschritt, wenn sich der Benutzer mit einem lokalen Konto anmeldet oder registriert.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Mit einem Preconditions-Element können mehrere Voraussetzungen überprüft werden. Im folgenden Beispiel wird überprüft, ob die Werte „objectId“ und „email“ vorhanden sind. Wenn die erste Bedingung TRUE lautet, springt die User Journey zum nächsten Orchestrierungsschritt.

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Ein Orchestrierungsschritt vom Typ `ClaimsProviderSelection` oder `CombinedSignInAndSignUp` kann eine Liste von Anspruchsanbietern enthalten, mit denen ein Benutzer sich anmelden kann. Die Reihenfolge der Elemente in `ClaimsProviderSelections`-Elementen gibt die Reihenfolge vor, in der die Identitätsanbieter dem Benutzer gezeigt werden.

Das **ClaimsProviderSelections**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | Stellt die Liste von Anspruchsanbietern bereit, die ausgewählt werden können.|

Das **ClaimsProviderSelections**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| DisplayOption| Nein | Steuert das Verhalten in dem Fall, wenn nur eine Anspruchsanbieterauswahl verfügbar ist. Mögliche Werte: `DoNotShowSingleProvider` (Standardwert) – der Benutzer wird sofort an den Verbundidentitätsanbieter umgeleitet. Ein weiterer möglicher Wert ist `ShowSingleProvider` – Azure AD B2C zeigt die Anmeldeseite mit der Auswahl eines einzelnen Identitätsanbieters an. Damit dieses Attribut verwendet werden kann, muss die [Version der Inhaltsdefinition](page-layout.md) `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` oder höher lauten.|

Das **ClaimsProviderSelection**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Nein  | Der Bezeichner des Anspruchsaustauschs, der im nächsten Orchestrierungsschritt der Auswahl des Anspruchsanbieters ausgeführt wird. Dieses oder das ValidationClaimsExchangeId-Attribut muss angegeben werden, aber nicht beide. |
| ValidationClaimsExchangeId | Nein | Der Bezeichner des Anspruchsaustauschs, der im aktuellen Orchestrierungsschritt zur Validierung der Auswahl des Anspruchsanbieters ausgeführt wird. Dieses oder das TargetClaimsExchangeId-Attribut muss angegeben werden, aber nicht beide. |

### <a name="claimsproviderselection-example"></a>Beispiel für das ClaimsProviderSelection-Element

Im folgenden Orchestrierungsschritt kann der Benutzer auswählen, ob er sich über Facebook, LinkedIn, Twitter, Google oder ein lokales Konto anmelden möchte. Wenn der Benutzer einen der Social Media-Identitätsanbieter auswählt, wird der zweite Orchestrierungsschritt mit dem ausgewählten Anspruchsaustausch ausgeführt, der im `TargetClaimsExchangeId`-Attribut angegeben wurde. Der zweite Orchestrierungsschritt leitet den Benutzer an den Social Media-Identitätsanbieter weiter, um den Anmeldevorgang abzuschließen. Wenn der Benutzer sich dazu entscheidet, sich mit einem lokalen Konto anzumelden, bleibt Azure AD B2C beim gleichen Orchestrierungsschritt (die gleiche Registrierungs- oder Anmeldeseite) und überspringt den zweiten Orchestrierungsschritt.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

Das **ClaimsExchanges**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | Abhängig vom verwendeten technischen Profil, wird der Client entweder gemäß des ClaimsProviderSelection-Elements weitergeleitet oder ein Serveraufruf wird zum Austauschen von Ansprüchen ausgeführt. |

Das **ClaimsExchange**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Id | Ja | Ein Bezeichner für den Schritt mit dem Anspruchsaustausch. Der Bezeichner wird verwendet, um in der Richtlinie auf den Anspruchsaustausch eines Schritts für die Auswahl des Anspruchsanbieters zu verweisen. |
| TechnicalProfileReferenceId | Ja | Der Bezeichner des technischen Profils, das ausgeführt werden soll. |

## <a name="journeylist"></a>JourneyList

Das **JourneyList**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Kandidat | 1:1 | Ein Verweis auf eine untergeordnete Journey, die aufgerufen werden soll. |

### <a name="candidate"></a>Kandidat

Das **Candidate**-Element enthält die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Ja | Der Bezeichner der [untergeordneten Journey](subjourneys.md), die ausgeführt werden soll. |
