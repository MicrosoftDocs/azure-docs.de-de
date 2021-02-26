---
title: Definieren eines technischen OAuth2-Profils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie ein technisches OAuth2-Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f79360269c19f6770fa12120ec34497b29015e7e
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050684"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen OAuth2-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) bietet Unterstützung für Identitätsanbieter mit dem OAuth2-Protokoll. OAuth2 ist das primäre Protokoll für die Autorisierung und die delegierte Authentifizierung. Weitere Informationen finden Sie unter [RFC 6749: The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749) (Das OAuth 2.0-Autorisierungsframework). Mit einem technischen OAuth2-Profil können Sie einen Verbund mit einem OAuth2-basierten Identitätsanbieter wie Facebook erstellen. Über einen Verbund mit einem Identitätsanbieter können sich Benutzer mit ihren vorhandenen Identitäten aus sozialen Netzwerken oder Unternehmen anmelden.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `OAuth2` festgelegt werden. Das Protokoll für das technische Profil **Facebook-OAUTH** ist z.B. `OAuth2`:

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Eingabeansprüche

Die Elemente **InputClaims** und **InputClaimsTransformations** sind nicht erforderlich. Sie können diese zusätzlichen Parameter aber an Ihren Identitätsanbieter senden. Im folgenden Beispiel wird der Autorisierungsanforderung der Parameter **domain_hint** der Abfragezeichenfolge mit dem Wert `contoso.com` hinzugefügt.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Ausgabeansprüche

Das **OutputClaims**-Element enthält eine Liste von Ansprüchen, die vom OAuth2-Identitätsanbieter zurückgegeben wurden. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der für den Identitätsanbieter definiert wurde, zuordnen. Sie können auch Ansprüche, die nicht vom Identitätsanbieter zurückgegeben wurden, einfügen, sofern Sie das `DefaultValue`-Attribut festlegen.

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

Das folgende Beispiel zeigt die Ansprüche, die vom Identitätsanbieter Facebook zurückgegeben wurden:

- Der Anspruch **first_name** wird dem Anspruch **givenName** zugeordnet.
- Der Anspruch **last_name** wird dem Anspruch **surname** zugeordnet.
- Der Anspruch **displayName** erhält keine Namenszuordnung.
- Dem Anspruch **email** wird kein Name zugeordnet.

Das technische Profil gibt auch Ansprüche zurück, die vom Identitätsanbieter nicht zurückgegeben werden:

- Der Anspruch **identityProvider** enthält den Namen des Identitätsanbieters.
- Der Anspruch **authenticationSource** enthält als Standardwert **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| client_id | Ja | Die Anwendungs-ID des Identitätsanbieters. |
| IdTokenAudience | Nein | Die Zielgruppe von id_token. Wenn eine Angabe erfolgt, überprüft Azure AD B2C, ob das Token in einem Anspruch, der vom Identitätsanbieter zurückgegeben wurde, enthalten und mit dem angegebenen Token identisch ist. |
| authorization_endpoint | Ja | Die URL des Autorisierungsendpunkts gemäß RFC 6749. |
| AccessTokenEndpoint | Ja | Die URL des Tokenendpunkts gemäß RFC 6749. |
| ClaimsEndpoint | Ja | Die URL des Endpunkts für Benutzerinformationen gemäß RFC 6749. |
| end_session_endpoint | Ja | Die URL des Endpunkts zum Beenden der Sitzung nach RFC 6749. |
| AccessTokenResponseFormat | Nein | Das Format für Aufrufe an den Zugriffstoken-Endpunkt. Facebook erfordert z.B. eine HTTP GET-Methode, während die Antwort mit dem Zugriffstoken im JSON-Format ist. |
| AdditionalRequestQueryParameters | Nein | Zusätzliche Abfrageparameter für die Anforderung. Sie können diese zusätzlichen Parameter z.B. an Ihren Identitätsanbieter senden. Sie können mehrere Parameter mit einem Komma als Trennzeichen einfügen. |
| ClaimsEndpointAccessTokenName | Nein | Der Name des Parameters mit der Abfragezeichenfolge für das Zugriffstoken. Die Anspruchsendpunkte einiger Identitätsanbieter unterstützen HTTP GET-Anforderungen. In diesem Fall wird das Bearertoken über einen Parameter für eine Abfragezeichenfolge anstelle eines Autorisierungsheaders gesendet. Standardwert. `access_token`. |
| ClaimsEndpointFormatName | Nein | Der Name des Formatparameters für die Abfragezeichenfolge. Sie können im LinkedIn-Anspruchsendpunkt `https://api.linkedin.com/v1/people/~?format=json` beispielsweise als Namen `format` festlegen. |
| ClaimsEndpointFormat | Nein | Der Wert des der Formatparameters für die Abfragezeichenfolge. Sie können im LinkedIn-Anspruchsendpunkt `https://api.linkedin.com/v1/people/~?format=json` beispielsweise als Wert `json` festlegen. |
| BearerTokenTransmissionMethod | Nein | Gibt an, wie das Token gesendet wird. Die Standardmethode ist eine Abfragezeichenfolge. Um das Token als Anforderungsheader zu senden, legen Sie es auf `AuthorizationHeader` fest. |
| ProviderName | Nein | Der Name des Identitätsanbieters. |
| response_mode | Nein | Die Methode, die der Identitätsanbieter verwendet, um das Ergebnis zurück an Azure AD B2C zu senden. Mögliche Werte: `query`, `form_post` (Standard) oder `fragment`. |
| scope | Nein | Der Bereich für die Anforderung gemäß der Spezifikation des OAuth2-Identitätsanbieters. Beispiele: `openid`, `profile` und `email`. |
| HttpBinding | Nein | Die erwartete HTTP-Bindung an die Endpunkte für Zugriffs- und Anspruchstoken. Mögliche Werte: `GET` oder `POST`.  |
| ResponseErrorCodeParamName | Nein | Der Name des Parameters mit der Fehlermeldung, die über HTTP 200 (OK) zurückgegeben wurde. |
| ExtraParamsInAccessTokenEndpointResponse | Nein | Enthält die zusätzlichen Parameter, die in der Antwort auf **AccessTokenEndpoint** von einigen Identitätsanbietern zurückgegeben werden können. Beispielsweise enthält die Antwort von **AccessTokenEndpoint** einen zusätzlichen Parameter wie `openid`, der neben access_token in der Abfragezeichenfolge einer **ClaimsEndpoint**-Anforderung ein erforderlicher Parameter ist. Mehrere Parameternamen sollten mit einem Escapezeichen versehen und durch ein Komma (,) voneinander getrennt werden. |
| ExtraParamsInClaimsEndpointRequest | Nein | Enthält die zusätzlichen Parameter, die in der **ClaimsEndpoint**-Anforderung von einigen Identitätsanbietern zurückgegeben werden können. Mehrere Parameternamen sollten mit einem Escapezeichen versehen und durch ein Komma (,) voneinander getrennt werden. |
| IncludeClaimResolvingInClaimsHandling  | Nein | Gibt bei Eingabe- und Ausgabeansprüchen an, ob die [Anspruchsauflösung](claim-resolver-overview.md) im technischen Profil enthalten ist. Mögliche Werte sind `true` oder `false` (Standardwert). Wenn Sie im technischen Profil eine Anspruchsauflösung verwenden möchten, legen Sie für diese Einstellung den Wert `true` fest. |
| ResolveJsonPathsInJsonTokens  | Nein | Gibt an, ob das technische Profil JSON-Pfade auflöst. Mögliche Werte sind `true` oder `false` (Standardwert). Verwenden Sie diese Metadaten, um Daten aus einem geschachtelten JSON-Element zu lesen. Legen Sie in einem Ausgabeanspruch ([OutputClaim](technicalprofiles.md#output-claims)) den Partneranspruchstyp (`PartnerClaimType`) auf das auszugebende JSON-Pfadelement fest. Beispiel: `firstName.localized` oder `data.0.to.0.email`|
|token_endpoint_auth_method| Nein| Gibt an, wie Azure AD B2C den Authentifizierungsheader an den Tokenendpunkt sendet. Mögliche Werte sind `client_secret_post` (Standardwert) und `client_secret_basic` (öffentliche Vorschau). Weitere Informationen finden Sie im Abschnitt [OpenID Connect-Clientauthentifizierung](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
|SingleLogoutEnabled| Nein| Gibt an, ob das technische Profil bei der Anmeldung versucht, sich beim Verbundidentitätsanbieter abzumelden. Weitere Informationen finden Sie unter [Abmelden von der Azure AD B2C-Sitzung](session-behavior.md#sign-out). Mögliche Werte: `true` (Standard) oder `false`.|
| UsePolicyInRedirectUri | Nein | Gibt an, ob beim Erstellen des Umleitungs-URI eine Richtlinie verwendet werden soll. Wenn Sie Ihre Anwendung im Identitätsanbieter konfigurieren, müssen Sie den Umleitungs-URI angeben. Der Umleitungs-URI verweist auf Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Bei Angabe von `true` müssen Sie einen Umleitungs-URI für jede verwendete Richtlinie hinzufügen. Beispiel: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Das **CryptographicKeys**-Element enthält das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| client_secret | Ja | Der geheime Clientschlüssel der Anwendung des Identitätsanbieters. Der kryptografische Schlüssel ist nur erforderlich, wenn die **response_type**-Metadaten auf `code` festgelegt sind. Azure AD B2C führt in diesem Fall einen weiteren Aufruf zum Austauschen des Autorisierungscode gegen ein Zugriffstoken durch. Wenn die Metadaten auf `id_token` festgelegt wurden, können Sie den kryptografischen Schlüssel weglassen. |

## <a name="redirect-uri"></a>Umleitungs-URI

Wenn Sie den Umleitungs-URI Ihres Identitätsanbieters konfigurieren, geben Sie `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/oauth2/authresp` an. Ersetzen Sie `{tenant-name}` durch den Namen Ihres Mandanten (z. B. „contosob2c“). Der Umleitungs-URI muss klein geschrieben sein.

Beispiele:

- [Hinzufügen von Google+ als OAuth2-Identitätsanbieter mithilfe benutzerdefinierter Richtlinien](identity-provider-google.md)
