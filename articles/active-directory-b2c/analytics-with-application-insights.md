---
title: Nachverfolgen des Benutzerverhaltens mit Application Insights
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie Ereignisprotokolle in Application Insights von Azure AD B2C-User Journeys aus aktivieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218552"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Nachverfolgen des Benutzerverhaltens in Azure Active Directory B2C mithilfe von Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) unterstützt das direkte Senden von Ereignisdaten an [Application Insights](../azure-monitor/app/app-insights-overview.md) unter Verwendung des für Azure AD B2C bereitgestellten Instrumentierungsschlüssels. Mit einem technischen Application Insights-Profil können Sie ausführliche und angepasste Ereignisprotokolle für Ihre User Journeys erhalten. Diese ermöglichen Folgendes:

* Gewinnen von Einblicken in Benutzerverhalten.
* Beheben von Problemen mit eigenen Richtlinien in der Entwicklung oder in der Produktion.
* Messen der Leistung.
* Erstellen von Benachrichtigungen von Application Insights.

## <a name="overview"></a>Übersicht

Wenn Sie benutzerdefinierte Ereignisprotokolle aktivieren möchten, fügen Sie ein technisches Application Insights-Profil hinzu. Im technischen Profil definieren Sie den Application Insights-Instrumentierungsschlüssel, den Ereignisnamen und die Ansprüche, die aufgezeichnet werden sollen. Um ein Ereignis zu senden, wird das technische Profil dann als Orchestrierungsschritt in einer [User Journey](userjourneys.md) hinzugefügt.

Wenn Sie Application Insights verwenden, beachten Sie Folgendes:

- Es tritt eine kurze Verzögerung auf (in der Regel weniger als fünf Minuten), bevor neue Protokolle in Application Insights verfügbar sind.
- Azure AD B2C ermöglicht Ihnen, die aufzuzeichnenden Ansprüche auszuwählen. Beziehen Sie keine Ansprüche mit personenbezogenen Daten ein.
- Zum Aufzeichnen einer Benutzersitzung können die Ereignisse mithilfe einer Korrelations-ID vereinheitlicht werden. 
- Rufen Sie das technische Application Insights-Profil direkt von einer [User Journey](userjourneys.md) oder [Sub Journey](subjourneys.md) aus auf. Verwenden Sie das technische Application Insights-Profil nicht als [technisches Validierungsprofil](validation-technical-profile.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Erstellen einer Application Insights-Ressource

Wenn Sie Application Insights mit Azure AD B2C verwenden, müssen Sie lediglich eine Ressource erstellen und den Instrumentierungsschlüssel abrufen. Informationen finden Sie unter [Erstellen einer Application Insights-Ressource](../azure-monitor/app/create-new-resource.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihr Azure-Abonnement enthält, indem Sie im oberen Menü den Filter **Verzeichnis und Abonnement** auswählen und dann das Verzeichnis auswählen, das Ihr Abonnement enthält. Bei diesem Mandanten handelt es sich nicht um Ihren Azure AD B2C-Mandanten.
3. Wählen Sie links oben im Azure-Portal **Ressource erstellen** aus, und suchen Sie dann nach **Application Insights**, und wählen Sie dann diese Option aus.
4. Klicken Sie auf **Erstellen**.
5. Geben Sie einen **Namen** für die Ressource ein.
6. Wählen Sie als **Anwendungstyp** die Option **ASP.NET-Webanwendung** aus.
7. Wählen Sie als **Ressourcengruppe** eine vorhandene Gruppe aus, oder geben Sie den Namen für eine neue Gruppe ein.
8. Klicken Sie auf **Erstellen**.
4. Nachdem Sie die Application Insights-Ressource erstellt haben, öffnen Sie sie, erweitern Sie **Zusammenfassung**, und kopieren Sie den Instrumentierungsschlüssel.

![Application Insights – Übersicht und Instrumentierungsschlüssel](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definieren von Ansprüchen

Ein Anspruch stellt eine temporäre Speicherung von Daten während der Ausführung einer Azure AD B2C-Richtlinie bereit. Im [Anspruchsschema](claimsschema.md) deklarieren Sie Ihre Ansprüche.

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** die folgenden Ansprüche hinzu. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Hinzufügen neuer technischer Profile

Technische Profile können in der benutzerdefinierten Richtlinie als Funktionen angesehen werden. In dieser Tabelle werden die technischen Profile definiert, die zum Öffnen einer Sitzung und Senden von Ereignissen verwendet werden. Die Lösung verwendet den Ansatz der [Einbindung des technischen Profils](technicalprofiles.md#include-technical-profile). Dabei enthält ein technisches Profil ein anderes technisches Profil, um Einstellungen zu ändern oder neue Funktionalität hinzuzufügen.

| Technisches Profil | Aufgabe |
| ----------------- | -----|
| AppInsights-Common | Dies ist ein allgemeines technisches Profil mit dem allgemeinen Konfigurationssatz. Application Insights-Instrumentierungsschlüssel, Sammlung der aufzuzeichnenden Ansprüche und Entwicklermodus sind darin enthalten. Die folgenden technischen Profile enthalten das allgemeine technische Profil und fügen weitere Ansprüche wie den Ereignisnamen hinzu. |
| AppInsights-SignInRequest | Zeichnet ein `SignInRequest`-Ereignis mit einem Satz von Ansprüchen auf, wenn eine Anmeldeanforderung empfangen wird. |
| AppInsights-UserSignUp | Zeichnet ein `UserSignUp`-Ereignis auf, wenn der Benutzer die Registrierungsoption in einer Registrierungs- oder Anmeldungsjourney auslöst. |
| AppInsights-SignInComplete | Zeichnet ein `SignInComplete`-Ereignis für den erfolgreichen Abschluss einer Authentifizierung auf, wenn ein Token an die Anwendung der vertrauenden Seite gesendet wurde. |

Fügen Sie die Profile zur Datei *TrustFrameworkExtensions.xml* aus dem Starter Pack hinzu. Fügen Sie diese Elemente dem Element **ClaimsProviders** hinzu:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Ändern Sie den Instrumentierungsschlüssel im technischen Profil `AppInsights-Common` in die GUID, die von Ihrer Application Insights-Ressource bereitgestellt wurde.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Hinzufügen der technischen Profil als Orchestrierungsschritte

Rufen Sie `AppInsights-SignInRequest` als Orchestrierungsschritt 2 auf, um nachzuverfolgen, ob eine Anmeldungs-/Registrierungsanforderung empfangen wurde:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Fügen Sie direkt *vor* dem Orchestrierungsschritt `SendClaims` einen neuen Schritt ein, der `AppInsights-UserSignup` aufruft. Es wird ausgelöst, wenn der Benutzer die Registrierungsschaltfläche in einer Registrierungs-/Anmeldungsjourney auswählt.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Rufen Sie direkt nach dem Orchestrierungsschritt `SendClaims` den Befehl `AppInsights-SignInComplete` auf. Dieser Schritt zeigt eine erfolgreich abgeschlossene Journey an.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Nummerieren Sie nach dem Hinzufügen der neuen Orchestrierungsschritte die Schritte nacheinander von 1 bis N neu, ohne einen Integer zu überspringen.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Hochladen Ihrer Datei, Ausführen der Richtlinie und Anzeigen von Ereignissen

Speichern Sie die Datei *TrustFrameworkExtensions.xml*, und laden Sie sie hoch. Rufen Sie dann die Richtlinie für die vertrauende Seite Ihrer Anwendung ab, oder verwenden Sie **Jetzt ausführen** im Azure-Portal. Warten Sie etwa eine Minute, bis Ihre Ereignisse in Application Insights verfügbar sind.

1. Öffnen Sie die **Application Insights**-Ressource in Ihrem Azure Active Directory-Mandanten.
2. Wählen Sie **Verwendung** und dann **Ereignisse** aus.
3. Legen Sie **Während** auf **letzte Stunde** und **Für** auf **3 Minuten** fest.  Sie müssen möglicherweise **Aktualisieren** auswählen, um Ihre Ergebnisse anzuzeigen.

![Application Insights-Nutzungsereignisbasis](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Erfassen weiterer Daten

Um Ihre geschäftlichen Anforderungen zu erfüllen, können Sie weitere Ansprüche aufzeichnen. Um einen Anspruch hinzuzufügen, müssen Sie zuerst [einen Anspruch definieren](#define-claims) und dann der Eingabeansprüchesammlung hinzufügen. Ansprüche, die Sie dem technischen *AppInsights-Common*-Profil hinzufügen, werden in allen Ereignissen angezeigt. Ansprüche, die Sie einem bestimmten technischen Profil hinzufügen, werden nur in diesem Ereignis angezeigt. Das Eingabeanspruchselement enthält die folgenden Attribute:

- **ClaimTypeReferenceId** ist der Verweis auf einen Anspruchstyp. 
- **PartnerClaimType** ist der Name der Eigenschaft, die in Azure Insights angezeigt wird. Verwenden Sie die Syntax von `{property:NAME}`, wobei `NAME` die Eigenschaft ist, die dem Ereignis hinzugefügt wird.
- **DefaultValue** ist ein vordefinierter Wert, der aufgezeichnet werden soll, z. B. ein Ereignisname. Ein Anspruch, der in der User Journey verwendet wird, z. B. der Identitätsanbietername. Wenn kein Anspruch aufgeführt wird, wird der Standardwert verwendet. Beispielsweise wird der Anspruch `identityProvider` durch die technischen Profile des Verbunds festgelegt, z. B. Facebook. Wenn kein Anspruch aufgeführt wird, bedeutet dies, dass sich der Benutzer mit einem lokalen Konto anmeldet. Folglich ist dieser Wert standardmäßig auf *Lokal* festgelegt. Sie können auch einen [Anspruchskonfliktlöser](claim-resolver-overview.md) mit einem Kontextwert aufzeichnen, z. B. der Anwendungs-ID oder Benutzer-IP-Adresse.

### <a name="manipulating-claims"></a>Bearbeiten von Ansprüchen

Sie können mit [Eingabeanspruchstransformationen](custom-policy-trust-frameworks.md#manipulating-your-claims) die Eingabeansprüche ändern oder neue generieren, bevor Sie sie an Application Insights senden. Im folgenden Beispiel enthält das technische Profil die Eingabeanspruchstransformation *CheckIsAdmin*. 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Ereignisse hinzufügen

Um ein Ereignis hinzuzufügen, erstellen Sie ein neues technisches Profil, das das technische *AppInsights-Common*-Profil enthält. Fügen Sie das technische Profil dann als Orchestrierungsschritt der [User Journey](custom-policy-trust-frameworks.md#orchestration-steps) hinzu. Verwenden Sie [Preconditions](userjourneys.md#preconditions), um das Ereignis bei Bedarf auszulösen. Lassen Sie das Ereignis beispielsweise nur melden, wenn Benutzer die MFA durchlaufen.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Da Sie nun über ein technisches Profil verfügen, fügen Sie das Ereignis der User Journey hinzu. Nummerieren Sie die Schritte dann nacheinander von 1 bis N neu, ohne eine Ganzzahl zu überspringen.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Aktivieren des Entwicklermodus

Wenn Sie Application Insights zum Definieren von Ereignissen verwenden, können Sie angeben, ob der Entwicklermodus aktiviert ist. Der Entwicklermodus steuert die Pufferung von Ereignissen. In einer Entwicklungsumgebung mit minimalem Ereignisaufkommen führt die Aktivierung des Entwicklermodus dazu, dass Ereignisse sofort an Application Insights gesendet werden. Standardwert: `false`. Aktivieren Sie den Entwicklermodus nicht in Produktionsumgebungen.

Um den Entwicklermodus zu aktivieren, ändern Sie im technischen *AppInsights-Common*-Profil die `DeveloperMode`-Metadaten in `true`: 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Telemetrie deaktivieren

Um die Application Insights-Protokolle zu deaktivieren, ändern Sie im technischen *AppInsights-Common*-Profil die `DisableTelemetry`-Metadaten in `true`: 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [benutzerdefinierte KPI-Dashboards mithilfe von Azure Application Insights](../azure-monitor/learn/tutorial-app-dashboards.md) erstellen. 

::: zone-end