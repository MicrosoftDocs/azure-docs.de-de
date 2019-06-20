---
title: Verwalten von SSO und der Tokenanpassung mit benutzerdefinierten Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Verwalten von SSO und der Tokenanpassung mit benutzerdefinierten Richtlinien in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f3621b176e4bbfdfbd171339d6d01a1f91ed0ae7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509298"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Verwalten von SSO und der Tokenanpassung mit benutzerdefinierten Richtlinien in Azure Active Directory B2C

Dieser Artikel enthält Informationen zum Verwalten der Token-, Sitzungs- und SSO-Konfiguration (Single Sign-On, einmaliges Anmelden) mithilfe von [benutzerdefinierten Richtlinien](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Tokengültigkeitsdauern und Anspruchskonfiguration

Zum Ändern der Einstellungen für Ihre Tokengültigkeitsdauern fügen Sie in der Datei der vertrauenden Seite für die gewünschte Richtlinie ein [ClaimsProviders](claimsproviders.md)-Element hinzu.  Das **ClaimsProviders**-Element ist dem [TrustFrameworkPolicy](trustframeworkpolicy.md)-Element untergeordnet. 

Fügen Sie das ClaimsProviders-Element zwischen dem BasePolicy-Element und dem RelyingParty-Element der Datei der vertrauenden Seite ein.

Sie müssen dort die Informationen einfügen, die sich auf die Gültigkeitsdauer Ihrer Token auswirken. Die XML sieht wie im folgenden Beispiel aus:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

Im vorstehenden Beispiel werden die folgenden Werte festgelegt:

- **Lebensdauer des Zugriffstokens:** Der Wert für die Lebensdauer des Zugriffstokens wird mit dem Metadatenelement **token_lifetime_secs** festgelegt. Der Standardwert beträgt 3.600 Sekunden (60 Minuten).
- **Lebensdauer des ID-Tokens:** Der Wert für die Lebensdauer des ID-Tokens wird mit dem Metadatenelement **id_token_lifetime_secs** festgelegt. Der Standardwert beträgt 3.600 Sekunden (60 Minuten).
- **Lebensdauer des Aktualisierungstokens:** Der Wert für die Lebensdauer des Aktualisierungstokens wird mit dem Metadatenelement **refresh_token_lifetime_secs** festgelegt. Der Standardwert beträgt 1.209.600 Sekunden (14 Tage).
- **Lebensdauer für gleitendes Fenster des Aktualisierungstokens:** Wenn Sie eine Gültigkeitsdauer für das gleitende Fenster des Aktualisierungstokens festlegen möchten, legen Sie den Wert des Metadatenelements **rolling_refresh_token_lifetime_secs** fest. Der Standardwert ist 7.776.000 (90 Tage). Falls Sie keine Gültigkeitsdauer für das gleitende Fenster erzwingen möchten, ersetzen Sie das Element durch `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Ausstelleranspruch (iss):** Der Ausstelleranspruch (iss) wird mit dem Metadatenelement **IssuanceClaimPattern** festgelegt. Die gültigen Werte sind `AuthorityAndTenantGuid` und `AuthorityWithTfp`.
- **Festlegen des Anspruchs zur Darstellung der Richtlinien-ID:** Die Optionen zum Festlegen dieses Werts sind `TFP` (Vertrauensframework-Richtlinie) und `ACR` (Authentifizierungskontext-Referenz). Der empfohlene Wert ist `TFP`. Legen Sie für **AuthenticationContextReferenceClaimPattern** den Wert `None` fest. 

    Fügen Sie im **ClaimsSchema**-Element dieses Element hinzu: 
    
    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```
    
    Fügen Sie im **OutputClaims**-Element dieses Element hinzu:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Entfernen Sie für ACR das Element **AuthenticationContextReferenceClaimPattern**.

- **Anspruch „Antragsteller“:** Diese Option hat standardmäßig den Wert „ObjectID“. Ersetzen Sie die folgende Zeile, um diese Einstellung in `Not Supported` zu ändern: 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    Durch diese Zeile:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Sitzungsverhalten und SSO

Zum Ändern Ihres Sitzungsverhaltens und der SSO-Konfigurationen ist es erforderlich, dass Sie im [RelyingParty](relyingparty.md)-Element ein **UserJourneyBehaviors**-Element hinzufügen.  Das **UserJourneyBehaviors**-Element muss unmittelbar nach **DefaultUserJourney** eingefügt werden. Das **UserJourneyBehavors**-Element sollte Folgendes enthalten:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Im vorstehenden Beispiel werden die folgenden Werte konfiguriert:

- **Einmaliges Anmelden (Single Sign-On, SSO):** Einmaliges Anmelden wird mit **SingleSignOn** konfiguriert. Die gültigen Werte sind `Tenant`, `Application`, `Policy` und `Suppressed`. 
- **Lebensdauer der Web-App-Sitzung (Minuten):** Die Gültigkeitsdauer der Web-App-Sitzung wird mit dem **SessionExpiryInSeconds**-Element festgelegt. Der Standardwert beträgt 86.400 Sekunden (1.440 Minuten).
- **Timeout für Web-App-Sitzung:** Das Timeout für die Web-App-Sitzung wird mit dem **SessionExpiryType**-Element festgelegt. Die gültigen Werte sind `Absolute` und `Rolling`.
