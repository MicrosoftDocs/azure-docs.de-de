---
title: Informationen zu technischen Profilen in benutzerdefinierten Azure Active Directory B2C-Richtlinien | Microsoft-Dokumentation
description: Erfahren Sie, wie technische Profile in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C verwendet werden.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 80b8969ba657506705db2b1a3bbc5b389d0a992c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512453"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informationen zu technischen Profilen in benutzerdefinierten Azure Active Directory B2C-Richtlinien

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ein technisches Profil bietet ein Framework mit einem integrierten Mechanismus für die Kommunikation mit verschiedenen Typen von Parteien mithilfe einer benutzerdefinierten Richtlinie in Azure Active Directory (Azure AD) B2C. Technische Profile werden verwendet, um mit Ihrem Azure AD B2C-Mandanten zu kommunizieren, einen Benutzer zu erstellen oder ein Benutzerprofil zu lesen. Ein selbstbestätigtes technisches Profil kann die Interaktion mit dem Benutzer ermöglichen. Beispiel: Sammeln der Anmeldeinformationen des Benutzers für die Anmeldung und anschließendes Rendern der Anmeldeseite oder der Seite zum Zurücksetzen des Kennworts. 

## <a name="type-of-technical-profiles"></a>Typen technischer Profile

Ein technisches Profil ermöglicht die folgenden Szenarien:

- [Azure Active Directory](active-directory-technical-profile.md) – Bietet Unterstützung für die Azure Active Directory B2C-Benutzerverwaltung.
- [JWT-Tokenaussteller](jwt-issuer-technical-profile.md) – Gibt ein JWT-Token aus, das an die Anwendung der vertrauenden Seite zurückgegeben wird. 
- **Telefonfaktoranbieter** – Mehrstufige Authentifizierung (Multi-Factor Authentication, MFA).
- [OAuth1](oauth1-technical-profile.md) – Verbund mit einem beliebigen Identitätsanbieter für das Protokoll OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) – Verbund mit einem beliebigen Identitätsanbieter für das Protokoll OAuth 2.0.
- [OpenIdConnect](openid-connect-technical-profile.md) – Verbund mit einem beliebigen Identitätsanbieter für das OpenId Connect-Protokoll.
- [Anspruchstransformation](claims-transformation-technical-profile.md) – Aufrufen von Transformationen für Ausgabeansprüche, um Anspruchswerte zu ändern, Ansprüche zu überprüfen oder Standardwerte für eine Gruppe von Ausgabeansprüchen festzulegen.
- [RESTful-Anbieter](restful-technical-profile.md) – Aufrufen von REST-API-Diensten, z. B. Überprüfung von Benutzereingaben, Ergänzung von Benutzerdaten oder Integration von Branchenanwendungen.
- [SAML2](saml-technical-profile.md) – Verbund mit einem beliebigen Identitätsanbieter für das SAML-Protokoll.
- [Selbstbestätigt](self-asserted-technical-profile.md) – Interaktion mit dem Benutzer. Beispiel: Sammeln der Anmeldeinformationen des Benutzers für die Anmeldung, Rendern der Anmeldeseite oder Kennwortzurücksetzung.
- **WsFed** – Verbund mit einem beliebigen Identitätsanbieter für das WsFed-Protokoll. 
- [Sitzungsverwaltung](active-directory-b2c-reference-sso-custom.md) – Verarbeiten verschiedener Typen von Sitzungen. 
- **Application Insights**

## <a name="technical-profile-flow"></a>Fluss technischer Profile

Allen Typen von technischen Profilen liegt das gleiche Konzept zugrunde. Sie senden Eingabeansprüche, führen Anspruchstransformationen aus und kommunizieren mit der konfigurierten Partei, z.B. mit einem Identitätsanbieter, der REST-API oder Azure AD-Verzeichnisdiensten. Nach Abschluss des Prozesses gibt das technische Profil die Ausgabeansprüche zurück und führt möglicherweise Ausgabeanspruchstransformationen aus. Im folgenden Diagramm ist dargestellt, wie die im technischen Profil referenzierten Transformationen und Zuordnungen verarbeitet werden. Unabhängig von der Partei, mit der das technische Profil interagiert, werden die Ausgabeansprüche aus dem technischen Profil nach der Ausführung von Anspruchstransformationen sofort im Anspruchsbehälter gespeichert. 

![Fluss technischer Profile](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** – Die Eingabeansprüche jeder [Eingabeanspruchstransformation](claimstransformations.md) werden dem Anspruchsbehälter entnommen, und nach der Ausführung werden die Ausgabeansprüche wieder im Anspruchsbehälter abgelegt. Bei den Ausgabeansprüchen einer Eingabeanspruchstransformation kann es sich um Eingabeansprüche einer nachfolgenden Eingabeanspruchstransformation handeln.
2. **InputClaims** – Ansprüche werden dem Anspruchsbehälter entnommen und für das technische Profil verwendet. Ein [selbstbestätigtes technisches Profil](self-asserted-technical-profile.md) verwendet beispielsweise die Eingabeansprüche, um die Ausgabeansprüche im Vorhinein auszufüllen, die der Benutzer angibt. Ein technisches REST-API-Profil verwendet die Eingabeansprüche, um Eingabeparameter an den REST-API-Endpunkt zu senden. Azure Active Directory verwendet einen Eingabeanspruch als eindeutigen Bezeichner zum Lesen, Aktualisieren oder Löschen eines Kontos.
3. **Ausführung des technischen Profils** – Das technische Profil tauscht die Ansprüche mit der konfigurierten Partei aus. Beispiel:
    - Der Benutzer wird an den Identitätsanbieter umgeleitet, um die Anmeldung abzuschließen. Nach der erfolgreichen Anmeldung kehrt der Benutzer zurück, und die Ausführung des technischen Profils wird fortgesetzt.
    - Rufen Sie eine REST-API auf, während Sie Parameter als InputClaims senden und Informationen als OutputClaims zurückerhalten.
    - Erstellen oder aktualisieren Sie das Benutzerkonto.
    - Die MFA-Textnachricht wird gesendet und überprüft.
4. **ValidationTechnicalProfiles** – Für ein [selbstbestätigtes technisches Profil](self-asserted-technical-profile.md) können Sie eine Eingabe für ein [technisches Validierungsprofil](validation-technical-profile.md) aufrufen. Das technische Validierungsprofil überprüft die vom Benutzer profilierten Daten und gibt eine Fehlermeldung oder „OK“ mit oder ohne Ausgabeansprüche zurück. Beispiel: Bevor Azure AD B2C ein neues Konto erstellt, wird überprüft, ob der Benutzer bereits in den Verzeichnisdiensten vorhanden ist. Sie können ein technisches REST-API-Profil aufrufen, um Ihre eigene Geschäftslogik hinzuzufügen.<p>Der Umfang der Ausgabeansprüche eines technischen Validierungsprofils ist auf das technische Profil, das das technische Validierungsprofil aufruft, und andere technische Validierungsprofile unter demselben technischen Profil beschränkt. Wenn Sie die Ausgabeansprüche im nächsten Orchestrierungsschritt verwenden möchten, müssen Sie die Ausgabeansprüche zu dem technischen Profil hinzufügen, das das technische Validierungsprofil aufruft.
5. **OutputClaims**: Ansprüche werden an den Anspruchsbehälter zurückgegeben. Sie können diese Ansprüche im nächsten Orchestrierungsschritt bzw. Transformationen von Ausgabeansprüchen verwenden.
6. **OutputClaimsTransformations** – Die Eingabeansprüche jeder [Ausgabeanspruchstransformation](claimstransformations.md) werden dem Anspruchsbehälter entnommen. Bei den Ausgabeansprüchen des technischen Profils aus den vorherigen Schritten kann es sich um Eingabeansprüche einer Ausgabeanspruchstransformation handeln. Nach der Ausführung werden die Ausgabeansprüche wieder im Anspruchsbehälter abgelegt. Bei den Ausgabeansprüchen einer Ausgabeanspruchstransformation kann es sich auch um Eingabeansprüche einer nachfolgenden Ausgabeanspruchstransformation handeln.
7. **Sitzungsverwaltung für einmaliges Anmelden (Single Sign-On, SSO)**  - [Die SSO-Sitzungsverwaltung](active-directory-b2c-reference-sso-custom.md) steuert die Interaktion mit einem Benutzer, nachdem der Benutzer bereits authentifiziert wurde. Der Administrator kann z. B. steuern, ob die Auswahl von Identitätsanbietern angezeigt wird, oder ob lokale Kontodetails erneut eingegeben werden müssen.

Ein technisches Profil kann von einem anderen technischen Profil erben, um Einstellungen zu ändern oder neue Funktionalität hinzuzufügen.  Das Element **IncludeTechnicalProfile** ist ein Verweis auf das technische Basisprofil, von dem ein technisches Profil abgeleitet wird.  

Das technische Profil **AAD-UserReadUsingAlternativeSecurityId-NoError** enthält z. B. das Profil **AAD-UserReadUsingAlternativeSecurityId**. Dieses technische Profil legt das Metadatenelement **RaiseErrorIfClaimsPrincipalDoesNotExist** auf `true` fest und löst einen Fehler aus, wenn ein Konto für ein soziales Netzwerk nicht im Verzeichnis vorhanden ist. **AAD-UserReadUsingAlternativeSecurityId-NoError** setzt dieses Verhalten außer Kraft und deaktiviert die Fehlermeldung, wenn der Benutzer nicht vorhanden ist.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
``` 

**AAD-UserReadUsingAlternativeSecurityId** enthält das technische Profil `AAD-Common`.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId-NoError** und **AAD-UserReadUsingAlternativeSecurityId** geben das erforderliche **Protocol**-Element nicht an, weil es im technischen Profil **AAD-Common** angegeben ist.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Ein technisches Profil kann ein anderes technisches Profil enthalten oder davon erben, das wiederum ein weiteres technisches Profil enthalten kann. Die Anzahl von Ebenen ist nicht begrenzt. Je nach Geschäftsanforderungen kann Ihre User Journey das Profil **AAD-UserReadUsingAlternativeSecurityId** aufrufen, das einen Fehler auslöst, wenn ein Benutzerkonto für ein soziales Netzwerk nicht vorhanden ist, oder **AAD-UserReadUsingAlternativeSecurityId-NoError** aufrufen, das keinen Fehler auslöst.











