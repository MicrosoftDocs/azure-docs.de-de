---
title: Sitzungsverwaltung für einmaliges Anmelden mit benutzerdefinierten Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie SSO-Sitzungen mithilfe benutzerdefinierter Richtlinien in Azure AD B2C verwalten.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 246e00418c784ee463170d78543e4a9aae3d7da8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509052"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Sitzungsverwaltung für einmaliges Anmelden in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mithilfe der Sitzungsverwaltung für einmaliges Anmelden (SSO) in Azure Active Directory (Azure AD) B2C kann ein Administrator die Interaktion mit einem Benutzer steuern, nachdem dieser bereits authentifiziert wurde. Der Administrator kann z. B. steuern, ob die Auswahl von Identitätsanbietern angezeigt wird, oder ob lokale Kontodetails erneut eingegeben werden müssen. In diesem Artikel wird das Konfigurieren der Einstellungen für einmaliges Anmelden für Azure AD B2C beschrieben.

Die SSO-Sitzungsverwaltung besteht aus zwei Teilen. Der erste Teil befasst sich mit den direkten Benutzerinteraktionen mit Azure AD B2C und der zweite Teil behandelt Benutzerinteraktionen mit externen Parteien, z. B. mit Facebook. Azure AD B2C setzt SSO-Sitzungen nicht außer Kraft oder umgeht diese, die von externen Parteien abgehalten werden. Stattdessen wird die Route über Azure AD B2C zur externen Partei „gespeichert“, wodurch vermieden wird, dass der Benutzer erneut aufgefordert werden muss, seinen Identitätsanbieter auszuwählen. Die ultimative SSO-Entscheidung verbleibt der externen Partei.

Die SSO-Sitzungsverwaltung verwendet dieselbe Semantik wie jedes andere technische Profil in benutzerdefinierten Richtlinien. Wenn ein Orchestrierungsschritt ausgeführt wird, wird das technische Profil, das dem Schritt zugeordnet ist, nach einem `UseTechnicalProfileForSessionManagement`-Verweis abgefragt. Falls vorhanden, wird der referenzierte SSO-Sitzungsanbieter überprüft, um festzustellen, ob der Benutzer ein Sitzungsteilnehmer ist. In diesem Fall wird der SSO-Sitzungsanbieter dazu verwendet, um die Sitzung wieder aufzufüllen. Wenn die Ausführung eines Orchestrierungsschritts abgeschlossen ist, wird entsprechend der Anbieter verwendet, um Informationen in der Sitzung zu speichern, wenn ein SSO-Sitzungsanbieter angegeben wurde.

Azure AD B2C hat eine Reihe von SSO-Sitzungsanbietern definiert, die verwendet werden können:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO Verwaltungsklassen werden mithilfe des `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />`-Elements eines technischen Profils angegeben.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Wie der Name besagt, ist dieser Anbieter untätig. Dieser Anbieter kann zum Unterdrücken des SSO-Verhaltens für ein bestimmtes technisches Profil verwendet werden.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Dieser Anbieter kann zum Speichern von Ansprüchen in einer Sitzung verwendet werden. Auf diesen Anbieter wird in der Regel in einem technischen Profil verwiesen, über das lokale Konten verwaltet werden. Wenn Sie den Anbieter DefaultSSOSessionProvider zum Speichern von Ansprüchen in einer Sitzung verwenden, müssen Sie sicherstellen, dass alle Ansprüche, die an die Anwendung zurückgegeben werden müssen oder in späteren Schritten von Vorbedingungen genutzt werden, in der Sitzung gespeichert oder durch Lesen aus dem Benutzerprofil im Verzeichnis ergänzt werden. Dadurch wird sichergestellt, dass bei Ihrer Authentifizierung bei fehlenden Ansprüche keine Fehler auftreten.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Verwenden Sie das `<PersistedClaims>`-Element des technischen Profils, um Ansprüche in der Sitzung hinzuzufügen. Wenn der Anbieter dazu verwendet wird, um die Sitzung erneut aufzufüllen, werden die bestehen gebliebenen Ansprüche zum Anspruchsbehälter hinzugefügt. `<OutputClaims>` dient zum Abrufen von Ansprüchen aus der Sitzung.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Dieser Anbieter wird dazu verwendet, um den Bildschirm „Identitätsanbieter auswählen“ zu unterdrücken. Er wird in der Regel in einem technischen Profil für einen externen Identitätsanbieter referenziert, z. B. Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Dieser Anbieter wird zum Verwalten der Azure AD B2C SAML-Sitzungen zwischen Apps sowie zwischen externen SAML-Identitätsanbietern verwendet.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Es gibt zwei Metadatenelemente im technischen Profil:

| Item | Standardwert | Mögliche Werte | BESCHREIBUNG
| --- | --- | --- | --- |
| IncludeSessionIndex | true | True/False | Gibt dem Anbieter an, dass der Sitzungsindex gespeichert werden soll. |
| RegisterServiceProviders | true | True/False | Gibt an, dass der Anbieter alle SAML-Dienstanbieter registrieren soll, die eine Assertion ausgestellt haben. |

Wenn der Anbieter zum Speichern einer SAML-Identitätsanbietersitzung verwendet wird, müssen die beiden vorhergehenden Elemente „false“ sein. Wenn der Anbieter zum Speichern der B2C SAML-Sitzungen verwendet wird, müssen die obigen Elemente „true“ sein oder ausgelassen werden, da die Standardwerte „true“ sind. Für die Abmeldung von der SAML-Sitzung ist erforderlich, dass `SessionIndex` und `NameID` abgeschlossen sind.

