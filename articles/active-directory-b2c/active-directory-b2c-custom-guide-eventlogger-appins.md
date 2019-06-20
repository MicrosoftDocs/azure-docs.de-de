---
title: Nachverfolgen des Benutzerverhaltens mithilfe von Ereignissen in Application Insights von Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ereignisprotokolle in Application Insights von Azure AD B2C-User Journeys mit benutzerdefinierten Richtlinien (Vorschau) aktivieren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0c2f9a2a3d431e2948c7d50541b576b23c3ece6a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507533"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Nachverfolgen des Benutzerverhaltens in Azure Active Directory B2C mithilfe von Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Bei Verwendung von Azure Active Directory (Azure AD) B2C zusammen mit Azure Application Insights können Sie detaillierte und angepasste Ereignisprotokolle für Ihre User Journeys abrufen. In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Gewinnen von Einblicken in Benutzerverhalten.
* Beheben von Problemen mit eigenen Richtlinien in der Entwicklung oder in der Produktion.
* Messen der Leistung.
* Erstellen von Benachrichtigungen von Application Insights.

## <a name="how-it-works"></a>So funktioniert's

Das Framework für die Identitätsfunktion in Azure AD B2C enthält den Anbieter `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Er sendet Ereignisdaten mithilfe des für Azure AD B2C bereitgestellten Instrumentierungsschlüssels direkt an Application Insights.

Ein technisches Profil verwendet diesen Anbieter zum Definieren eines Ereignisses aus Azure AD B2C. Das Profil gibt den Namen des Ereignisses, die aufzuzeichnenden Ansprüche und den Instrumentierungsschlüssel an. Um ein Ereignis zu senden, wird das technische Profil dann als `orchestration step` oder `validation technical profile` einer benutzerdefinierten User Journey hinzugefügt.

Application Insights kann die Ereignisse mithilfe einer Korrelations-ID zum Aufzeichnen einer Benutzersitzung vereinheitlichen. Application Insights macht das Ereignis und die Sitzung innerhalb von Sekunden verfügbar und stellt viele Visualisierungs-, Export- und Analysetools bereit.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) aus. In diesem Artikel wird davon ausgegangen, dass Sie das Startpaket für die benutzerdefinierte Richtlinie verwenden. Aber das Startpaket ist nicht erforderlich.

## <a name="create-an-application-insights-resource"></a>Erstellen einer Application Insights-Ressource

Wenn Sie Application Insights mit Azure AD B2C verwenden, müssen Sie lediglich eine Ressource erstellen und den Instrumentierungsschlüssel abrufen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihr Azure-Abonnement enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das Verzeichnis auswählen, das Ihr Abonnement enthält. Bei diesem Mandanten handelt es sich nicht um Ihren Azure AD B2C-Mandanten.
3. Wählen Sie links oben im Azure-Portal **Ressource erstellen** aus, und suchen Sie dann nach **Application Insights**, und wählen Sie dann diese Option aus.
4. Klicken Sie auf **Create**.
5. Geben Sie einen **Namen** für die Ressource ein.
6. Wählen Sie als **Anwendungstyp** die Option **ASP.NET-Webanwendung** aus.
7. Wählen Sie als **Ressourcengruppe** eine vorhandene Gruppe aus, oder geben Sie den Namen für eine neue Gruppe ein.
8. Klicken Sie auf **Create**.
4. Nachdem Sie die Application Insights-Ressource erstellt haben, öffnen Sie sie, erweitern Sie **Zusammenfassung**, und kopieren Sie den Instrumentierungsschlüssel.

![Application Insights – Übersicht und Instrumentierungsschlüssel](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Hinzufügen neuer ClaimType-Definitionen

Öffnen Sie die Datei *TrustFrameworkExtensions.xml* aus dem Starter-Pack, und fügen Sie dem Element [BuildingBlocks](buildingblocks.md) die folgenden Elemente hinzu:

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>Hinzufügen neuer technischer Profile

Technische Profile können im Azure AD B2C-Framework für die Identitätsfunktion als Funktionen angesehen werden. In dieser Tabelle werden die technischen Profile definiert, die zum Öffnen einer Sitzung und Senden von Ereignissen verwendet werden.

| Technisches Profil | Aufgabe |
| ----------------- | -----|
| AzureInsights-Common | Erstellt einen gemeinsamer Satz von Parametern, die in alle technischen Profile von AzureInsights einbezogen werden sollen. | 
| AzureInsights-SignInRequest | Erstellt ein „SignIn“-Ereignis mit einem Satz von Ansprüchen, wenn eine Anmeldeanforderung empfangen wird. | 
| AzureInsights-UserSignup | Erstellt ein „UserSignup“-Ereignis, wenn der Benutzer die Registrierungsoption in einer Registrierungs-/Anmeldungsjourney auslöst. | 
| AzureInsights-SignInComplete | Zeichnet den erfolgreichen Abschluss einer Authentifizierung auf, wenn ein Token an die Anwendung der vertrauenden Seite gesendet wurde. | 

Fügen Sie die Profile zur Datei *TrustFrameworkExtensions.xml* aus dem Starter Pack hinzu. Fügen Sie diese Elemente dem Element **ClaimsProviders** hinzu:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Ändern Sie den Instrumentierungsschlüssel im technischen Profil `ApplicationInsights-Common` in die GUID, die von Ihrer Application Insights-Ressource bereitgestellt wurde.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Hinzufügen der technischen Profil als Orchestrierungsschritte

Rufen Sie `Azure-Insights-SignInRequest` als Orchestrierungsschritt 2 auf, um nachzuverfolgen, ob eine Anmeldungs-/Registrierungsanforderung empfangen wurde:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Fügen Sie direkt *vor* dem Orchestrierungsschritt `SendClaims` einen neuen Schritt ein, der `Azure-Insights-UserSignup` aufruft. Es wird ausgelöst, wenn der Benutzer die Registrierungsschaltfläche in einer Registrierungs-/Anmeldungsjourney auswählt.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Rufen Sie direkt nach dem Orchestrierungsschritt `SendClaims` den Befehl `Azure-Insights-SignInComplete` auf. Dieser Schritt zeigt eine erfolgreich abgeschlossene Journey an.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Nummerieren Sie nach dem Hinzufügen der neuen Orchestrierungsschritte die Schritte nacheinander von 1 bis N neu, ohne einen Integer zu überspringen.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Hochladen Ihrer Datei, Ausführen der Richtlinie und Anzeigen von Ereignissen

Speichern Sie die Datei *TrustFrameworkExtensions.xml*, und laden Sie sie hoch. Rufen Sie dann die Richtlinie für die vertrauende Seite Ihrer Anwendung ab, oder verwenden Sie **Jetzt ausführen** im Azure-Portal. In wenigen Sekunden sind die Ereignisse in Application Insights verfügbar.

1. Öffnen Sie die **Application Insights**-Ressource in Ihrem Azure Active Directory-Mandanten.
2. Wählen Sie **Verwendung** > **Ereignisse** aus.
3. Legen Sie **Während** auf **letzte Stunde** und **Für** auf **3 Minuten** fest.  Sie müssen möglicherweise **Aktualisieren** auswählen, um Ihre Ergebnisse anzuzeigen.

![Application Insights-Nutzungsereignisbasis](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Nächste Schritte

Fügen Sie Ihrer User Journey entsprechend Ihren Anforderungen Anspruchstypen und Ereignisse hinzu. Sie können [Anspruchskonfliktlöser](claim-resolver-overview.md) oder einen beliebigen Zeichenfolgenanspruchstyp verwenden, die Ansprüche hinzufügen, indem Sie dem Application Insights-Ereignis oder dem technischen Profil „AzureInsights-Common“ ein **Eingabeanspruch**-Element hinzufügen. 

- **ClaimTypeReferenceId** ist der Verweis auf einen Anspruchstyp.
- **PartnerClaimType** ist der Name der Eigenschaft, die in Azure Insights angezeigt wird. Verwenden Sie die Syntax von `{property:NAME}`, wobei `NAME` die Eigenschaft ist, die dem Ereignis hinzugefügt wird. 
- **DefaultValue** verwendet einen beliebigen Zeichenfolgenwert oder den Anspruchskonfliktlöser. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

