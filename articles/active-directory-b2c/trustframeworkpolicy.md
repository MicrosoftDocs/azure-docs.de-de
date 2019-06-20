---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das TrustFrameworkPolicy-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1102aee9d7fa5fe3df0e048398cdc8bd71ff0a3c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512302"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Eine benutzerdefinierte Richtlinie wird als eine oder mehrere XML-formatierte Dateien dargestellt, die aufeinander in einer hierarchischen Kette verweisen. Die XML-Elemente definieren Elemente der Richtlinie wie das Anspruchsschema, Anspruchstransformationen, Inhaltsdefinitionen, Anspruchsanbieter, technische Profile, User Journeys und Orchestrierungsschritte. Jede Richtliniendatei wird im **TrustFrameworkPolicy**-Element der obersten Ebene einer Richtliniendatei definiert. 

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


Das **TrustFrameworkPolicy**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ja | Die Schemaversion, die zum Ausführen der Richtlinie verwendet werden soll. Der Wert muss `0.3.0.0` sein. |
| TenantObjectId | Nein | Der eindeutige Objektbezeichner des Azure Active Directory (Azure AD) B2C-Mandanten. |
| TenantId | Ja | Der eindeutige Bezeichner des Mandanten, zu dem diese Richtlinie gehört. |
| `PolicyId` | Ja | Der eindeutige Bezeichner für die Richtlinie. Diesem Bezeichner muss das Präfix *B2C_1A_* vorangestellt werden. |
| PublicPolicyUri | Ja | Der URI für die Richtlinie, bei dem es sich um eine Kombination der Mandanten-ID und der Richtlinien-ID handelt. |
| DeploymentMode | Nein | Mögliche Werte: `Production`, `Debugging` oder `Development`. `Production` ist die Standardeinstellung. Verwenden Sie diese Eigenschaft, um Ihre Richtlinie zu debuggen. Weitere Informationen finden Sie unter [Sammeln von Protokollen](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Nein | Der Endpunkt, der verwendet wird, wenn **DeploymentMode** auf `Development` festgelegt ist. Der Wert muss `urn:journeyrecorder:applicationinsights` sein. Weitere Informationen finden Sie unter [Sammeln von Protokollen](active-directory-b2c-troubleshoot-custom.md). |


Das folgende Beispiel zeigt, wie das **TrustFrameworkPolicy**-Element angegeben wird:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Vererbungsmodell

Diese Typen von Richtliniendateien werden in der Regel in einer User Journey verwendet:

- Eine **Basisdatei** enthält die meisten Definitionen. Um die Problembehandlung und langfristige Wartung Ihrer Richtlinien zu vereinfachen, empfiehlt es sich, an dieser Datei nur eine minimale Anzahl von Änderungen vorzunehmen.
- Eine **Erweiterungendatei** enthält die eindeutigen Konfigurationsänderungen für Ihren Mandanten. Diese Richtliniendatei wird von der Basisdatei abgeleitet. Verwenden Sie diese Datei, um neue Funktionalität hinzufügen oder vorhandene Funktionen zu überschreiben. Sie verwenden diese Datei z.B. für einen Verbund mit neuen Identitätsanbietern.
- Eine **Datei der vertrauenden Seite** stellt die einzige aufgabenorientierte Datei dar, die direkt von der Anwendung der vertrauenden Seite aufgerufen wird, z.B. Ihren Web-, mobilen oder Desktopanwendungen. Jede eindeutige Aufgabe wie das Registrieren oder Anmelden, das Zurücksetzen des Kennworts oder die Profilbearbeitung erfordert eine eigene Richtliniendatei der vertrauenden Seite. Diese Richtliniendatei wird von der Erweiterungsdatei abgeleitet. 

Eine Anwendung der vertrauenden Seite ruft die Richtliniendatei der vertrauenden Seite auf, um eine bestimmte Aufgabe auszuführen. Dies erfolgt beispielsweise, um den Anmeldungsablauf zu initiieren. Das Identity Experience Framework in Azure AD B2C fügt zunächst alle Elemente der Basisdatei, anschließend alle Elemente der Erweiterungsdatei und dann alle Elemente der Richtliniendatei der vertrauenden Seite hinzu, um die aktuell gültige Richtlinie zusammenzustellen. Elemente desselben Typs und mit demselben Namen in der Datei der vertrauenden Seite überschreiben jene in der Erweiterungsdatei, und die Elemente in der Erweiterungsdatei überschreiben wiederum jene in der Basisdatei. Das folgende Diagramm zeigt die Beziehung zwischen den Richtliniendateien und den Anwendungen der vertrauenden Seite.

![Vererbungsmodell](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Das Vererbungsmodell lautet wie folgt:

- Die übergeordnete Richtlinie und die untergeordnete Richtlinie weisen das gleiche Schema auf.
- Die untergeordnete Richtlinie kann auf jeder Ebene von der übergeordneten Richtlinie erben und diese durch Hinzufügen neuer Elemente erweitern.
- Die Anzahl von Ebenen ist nicht begrenzt.

Weitere Informationen finden Sie unter [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Basisrichtlinie

Damit eine Richtlinie von einer anderen Richtlinie erben kann, muss ein **BasePolicy**-Element unter dem **TrustFrameworkPolicy**-Element der Richtliniendatei deklariert werden. Das **BasePolicy**-Element ist ein Verweis auf die Basisrichtlinie, von der diese Richtlinie abgeleitet ist.  

Das **BasePolicy**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | --------|
| TenantId | 1:1 | Der Bezeichner Ihres Azure AD B2C-Mandanten. |
| `PolicyId` | 1:1 | Der Bezeichner der übergeordneten Richtlinie. |


Im folgenden Beispiel wird gezeigt, wie Sie eine Basisrichtlinie angeben. Diese Richtlinie **B2C_1A_TrustFrameworkExtensions** ist von der Richtlinie **B2C_1A_TrustFrameworkBase** abgeleitet. 

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Richtlinienausführung

Eine Anwendung der vertrauenden Seite, z.B. eine mobile, Web- oder Desktopanwendung, ruft die [Richtlinie der vertrauenden Seite (Relying Party, RP)](relyingparty.md) auf. Die Richtliniendatei der vertrauenden Seite führt eine bestimmte Aufgabe aus, z.B. eine Anmeldung, das Zurücksetzen eines Kennworts oder eine Profilbearbeitung. Die Richtlinie der vertrauenden Seite konfiguriert die Liste der Ansprüche, die die Anwendung der vertrauenden Seite als Teil des ausgegebenen Tokens empfängt. Mehrere Anwendungen können dieselbe Richtlinie verwenden. Alle Anwendungen erhalten das gleiche Token mit Ansprüchen, und der Benutzer durchläuft die gleiche User Journey. Eine einzelne Anwendung kann mehrere Richtlinien verwenden.

Geben Sie in der Richtliniendatei der vertrauenden Seite das **DefaultUserJourney**-Element an, das auf [UserJourney](userjourneys.md) verweist. Die User Journey wird in der Regel in der Basis- oder Erweiterungsrichtlinie definiert.

Richtlinie „B2C_1A_signup_signin“:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

„B2C_1A_TrustFrameWorkBase“ oder „B2C_1A_TrustFrameworkExtensionPolicy“:

```XML
<UserJourneys>
  <UserJourney Id="SignOrSignIn">
  ...
```

Eine User Journey definiert die Geschäftslogik, die ein Benutzer durchläuft. Jede User Journey umfasst einen Satz von Orchestrierungsschritten, mit dem nacheinander eine Reihe von Aktionen in Bezug auf die Authentifizierung und Informationserfassung ausführt wird. 

Die Richtliniendatei **SocialAndLocalAccounts** im [Starter Pack](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies) enthält die User Journeys SignUpOrSignIn, ProfileEdit und PasswordReset. Sie können weitere User Journeys für andere Szenarien, wie z.B. das Ändern einer E-Mail-Adresse oder das Verknüpfen und Aufheben der Verknüpfung mit einem Social Media-Konto hinzufügen. 

Die Orchestrierungsschritte können ein [technisches Profil](technicalprofiles.md) aufrufen. Ein technisches Profil bietet ein Framework mit einem integrierten Mechanismus für die Kommunikation mit verschiedenen Typen von Parteien. Mit einem technischen Profil können unter anderem folgende Aktionen ausgeführt werden:

- Rendern einer Benutzeroberfläche
- Zulassen, dass sich Benutzer mit Social Media- oder Unternehmenskonten, wie Facebook, Microsoft-Konto, Google, Salesforce oder einem anderen Identitätsanbieter, anmelden
- Einrichten der Telefonüberprüfung für die mehrstufige Authentifizierung
- Lesen und Schreiben von Daten in und aus einem Azure AD B2C-Identitätsspeicher
- Aufrufen eines benutzerdefinierten RESTful-API-Dienst

![Richtlinienausführung](./media/trustframeworkpolicy/custom-policy-execution.png)

 Das **TrustFrameworkPolicy**-Element enthält die folgenden Elemente:

- BasePolicy wie oben angegeben
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
