---
title: Azure API Management-Richtlinien für die Zugriffsbeschränkung | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Richtlinien für die Zugriffsbeschränkung, die für die Verwendung in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: vladvino
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.topic: article
ms.date: 11/23/2020
ms.author: apimpm
ms.openlocfilehash: 70be2000d3b01e55cd52d161072c3249870310b9
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122574"
---
# <a name="api-management-access-restriction-policies"></a>API Management-Richtlinien für die Zugriffsbeschränkung

Dieses Thema enthält eine Referenz für die folgenden API Management-Richtlinien. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](./api-management-policies.md).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> Richtlinien für die Zugriffsbeschränkung

-   [HTTP-Header überprüfen](#CheckHTTPHeader) – Erfordert das Vorhandensein und/oder einen Wert eines HTTP-Headers.
-   [Limit call rate (Aufrufrate begrenzen)](#LimitCallRate) – verhindert API-Lastspitzen, indem die Aufrufrate jeweils pro Abonnement beschränkt wird.
-   [Limit call rate by key (Aufrufrate nach Schlüssel begrenzen)](#LimitCallRateByKey) – Verhindert API-Lastspitzen, indem die Aufrufrate jeweils pro Schlüssel beschränkt wird.
-   [Beschränkung für Aufrufer-IP](#RestrictCallerIPs) – Filtert (erlaubt/blockiert) Aufrufe von bestimmten IP-Adressen und/oder Adressbereichen.
-   [Set usage quota by subscription (Nutzungskontingent nach Abonnement festlegen)](#SetUsageQuota) – ermöglicht die Durchsetzung eines erneuerbaren oder für die Lebensdauer gültigen Kontingents für Aufrufe und/oder Bandbreite auf Grundlage des Abonnements.
-   [Set usage quota by key (Nutzungskontingent nach Schlüssel festlegen)](#SetUsageQuotaByKey) – Ermöglicht die Durchsetzung eines erneuerbaren oder für die Lebensdauer gültigen Kontingents für Aufrufe und/oder Bandbreite auf Grundlage des Schlüssels.
-   [JWT überprüfen](#ValidateJWT) – Erzwingt das Vorhandensein und die Gültigkeit eines JWT, das entweder aus einem angegebenen HTTP-Header oder aus einem angegebenen Abfrageparameter extrahiert wurde.

> [!TIP]
> Sie können Richtlinien für die Zugriffsbeschränkung in verschiedenen Bereichen zu unterschiedlichen Zwecken verwenden. Beispielsweise können Sie die gesamte API mit AAD-Authentifizierung sichern, indem Sie die `validate-jwt`-Richtlinie auf API-Ebene anwenden, oder Sie können sie auf der Ebene der API-Vorgänge anwenden und `claims` für eine präzisere Steuerung verwenden.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> HTTP-Header überprüfen

Verwenden Sie die `check-header`-Richtlinie, um zu erzwingen, dass eine Anforderung einen angegebenen HTTP-Header enthält. Sie können optional überprüfen, ob der Header einen bestimmten Wert aufweist, oder einen Bereich zulässiger Werte überprüfen. Wenn die Überprüfung nicht erfolgreich abgeschlossen wird, beendet die Richtlinie die Verarbeitung und gibt den von der Richtlinie festgelegten HTTP-Statuscode und die zugehörige Fehlermeldung zurück.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Beispiel

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elemente

| Name         | BESCHREIBUNG                                                                                                                                   | Erforderlich |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | Stammelement                                                                                                                                 | Ja      |
| value        | Zulässiger HTTP-Headerwert. Wenn mehrere Wertelemente angegeben sind, wird die Überprüfung als erfolgreich gewertet, wenn für einen beliebigen dieser Werte eine Übereinstimmung vorhanden ist. | Nein       |

### <a name="attributes"></a>Attributes

| Name                       | BESCHREIBUNG                                                                                                                                                            | Erforderlich | Standard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Die Fehlermeldung, die im HTTP-Antworttext zurückgegeben wird, wenn der Header nicht vorhanden ist oder einen ungültigen Wert aufweist. In dieser Meldung müssen alle Sonderzeichen ordnungsgemäß mit Escapezeichen versehen sein. | Ja      | –     |
| failed-check-httpcode      | Der HTTP-Statuscode, der zurückgeben werden wird, wenn der Header nicht vorhanden ist oder einen ungültigen Wert aufweist.                                                                                        | Ja      | –     |
| header-name                | Der Name des zu überprüfenden HTTP-Headers.                                                                                                                                  | Ja      | –     |
| ignore-case                | Kann auf „true“ oder „false“ festgelegt werden. Wenn dieses Attribut auf „true“ festgelegt ist, wird beim Vergleichen des Headerwerts mit dem Satz zulässiger Werte die Groß- und Kleinschreibung ignoriert.                                    | Ja      | –     |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound, outbound

-   **Richtlinienbereiche:** alle Bereiche

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> Aufrufrate nach Abonnement begrenzen

Die `rate-limit`-Richtlinie verhindert API-Nutzungsspitzen auf Abonnementbasis, indem sie die Aufrufrate auf eine angegebene Anzahl pro angegebenem Zeitraum begrenzt. Wenn diese Richtlinie ausgelöst wird, empfängt der Aufrufer einen `429 Too Many Requests`-Antwortstatuscode.

> [!IMPORTANT]
> Diese Richtlinie kann pro Richtliniendokument nur einmal verwendet werden.
>
> [Richtlinienausdrücke](api-management-policy-expressions.md) können in keinem der Richtlinienattribute für diese Richtlinie werden.

> [!CAUTION]
> Da die Einschränkungsarchitektur verteilt ist, ist die Begrenzung der Rate nie ganz genau. Der Unterschied zwischen konfigurierter und tatsächlicher Anzahl zulässiger Anforderungen variiert basierend auf Anforderungsvolumen und -rate, Back-End-Wartezeit und anderen Faktoren.

> [!NOTE]
> Informationen zu den Unterschieden zwischen Ratenbegrenzungen und Kontingenten finden Sie unter [Ratenbegrenzungen und Kontingente](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas).

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Beispiel

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemente

| Name       | Beschreibung                                                                                                                                                                                                                                                                                              | Erforderlich |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | Stammelement                                                                                                                                                                                                                                                                                            | Ja      |
| api        | Fügen Sie mindestens eins dieser Elemente hinzu, um eine Aufrufratenbegrenzung für APIs innerhalb des Produkts zu erzwingen. Produkt- und API-Aufrufratenbegrenzungen werden unabhängig voneinander angewendet. Auf „api“ kann über `name` oder `id` verwiesen werden. Wenn beide Attribute bereitgestellt werden, wird `id` verwendet und `name` ignoriert.                    | Nein       |
| operation  | Fügen Sie mindestens eins dieser Elemente hinzu, um eine Aufrufratenbegrenzung auf Vorgänge innerhalb einer API zu erzwingen. Aufrufratenbegrenzungen für Produkte, APIs und Vorgänge werden unabhängig voneinander angewendet. Auf „operation“ kann über `name` oder `id` verwiesen werden. Wenn beide Attribute bereitgestellt werden, wird `id` verwendet und `name` ignoriert. | Nein       |

### <a name="attributes"></a>Attributes

| Name           | Beschreibung                                                                                           | Erforderlich | Standard |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Der Name der API, auf die die Ratenbegrenzung angewendet werden soll.                                                | Ja      | N/V     |
| calls          | Die maximale Gesamtanzahl von Aufrufen, die während des in der `renewal-period` angegebenen Zeitraums zulässig sind. | Ja      | N/V     |
| renewal-period | Der Zeitraum in Sekunden, nach dem das Kontingent zurückgesetzt wird.                                              | Ja      | –     |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound

-   **Richtlinienbereiche:** Produkt, API, Vorgang

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> Aufrufrate nach Schlüssel begrenzen

> [!IMPORTANT]
> Diese Funktion ist auf der Ebene **Verbrauch** von API Management nicht verfügbar.

Die `rate-limit-by-key`-Richtlinie verhindert API-Nutzungsspitzen auf Schlüsselbasis, indem sie die Aufrufrate auf eine angegebene Anzahl pro angegebenem Zeitraum beschränkt. Der Schlüssel kann einen beliebigen Zeichenfolgenwert aufweisen und wird in der Regel über einen Richtlinienausdruck angegeben. Optional kann eine inkrementelle Bedingung hinzugefügt werden, um anzugeben, welche Anforderungen für den Grenzwert gezählt werden sollen. Wenn diese Richtlinie ausgelöst wird, empfängt der Aufrufer einen `429 Too Many Requests`-Antwortstatuscode.

Weitere Informationen und Beispiele zu dieser Richtlinie finden Sie unter [Erweiterte Anforderungsbegrenzung mit Azure API Management](./api-management-sample-flexible-throttling.md).

> [!CAUTION]
> Da die Einschränkungsarchitektur verteilt ist, ist die Begrenzung der Rate nie ganz genau. Der Unterschied zwischen konfigurierter und tatsächlicher Anzahl zulässiger Anforderungen variiert basierend auf Anforderungsvolumen und -rate, Back-End-Wartezeit und anderen Faktoren.

> [!NOTE]
> Informationen zu den Unterschieden zwischen Ratenbegrenzungen und Kontingenten finden Sie unter [Ratenbegrenzungen und Kontingente](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas).

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Ratenbegrenzung anhand der IP-Adresse des Aufrufers bestimmt.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemente

| Name              | Beschreibung   | Erforderlich |
| ----------------- | ------------- | -------- |
| rate-limit-by-key | Stammelement | Ja      |

### <a name="attributes"></a>Attributes

| Name                | Beschreibung                                                                                           | Erforderlich | Standard |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| calls               | Die maximale Gesamtanzahl von Aufrufen, die während des in der `renewal-period` angegebenen Zeitraums zulässig sind. | Ja      | N/V     |
| counter-key         | Der Schlüssel, der für die Ratenbegrenzungsrichtlinie verwendet werden soll.                                                             | Ja      | N/V     |
| increment-condition | Der boolesche Ausdruck, der angibt, ob die Anforderung für das Kontingent gezählt werden soll (`true`).        | Nein       | N/V     |
| renewal-period      | Der Zeitraum in Sekunden, nach dem das Kontingent zurückgesetzt wird.                                              | Ja      | –     |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound

-   **Richtlinienbereiche:** alle Bereiche

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> Aufrufer-IPs beschränken

Die `ip-filter`-Richtlinie filtert (erlaubt/blockiert) Aufrufe von bestimmten IP-Adressen und/oder Adressbereichen.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Beispiel

Im folgenden Beispiel lässt die Richtlinie nur Anfragen zu, die entweder von der angegebenen einzelnen IP-Adresse oder von dem angegebenen IP-Adressbereich stammen.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elemente

| Name                                      | Beschreibung                                         | Erforderlich                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Stammelement                                       | Ja                                                            |
| address                                   | Gibt eine einzelne IP-Adresse an, nach der gefiltert werden soll.   | Mindestens ein `address`- oder `address-range`-Element ist erforderlich. |
| address-range from="Adresse" to="Adresse" | Gibt einen Bereich von IP-Adressen an, nach dem gefiltert werden soll. | Mindestens ein `address`- oder `address-range`-Element ist erforderlich. |

### <a name="attributes"></a>Attributes

| Name                                      | Beschreibung                                                                                 | Erforderlich                                           | Standard |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="Adresse" to="Adresse" | Ein IP-Adressbereich, für den diese Richtlinie gelten soll.                                        | Erforderlich, wenn das `address-range`-Element verwendet wird. | N/V     |
| ip-filter action="allow &#124; forbid"    | Gibt an, ob Aufrufe für die angegebenen IP-Adressen oder -Adressbereiche erlaubt oder blockiert werden sollen. | Ja                                                | –     |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound
-   **Richtlinienbereiche:** alle Bereiche

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> Nutzungskontingent nach Abonnement festlegen

Die `quota`-Richtlinie erzwingt ein erneuerbares oder für die Lebensdauer gültiges Aufruf- und/oder Bandbreitenkontingent pro Abonnement.

> [!IMPORTANT]
> Diese Richtlinie kann pro Richtliniendokument nur einmal verwendet werden.
>
> [Richtlinienausdrücke](api-management-policy-expressions.md) können in keinem der Richtlinienattribute für diese Richtlinie verwendet werden.

> [!NOTE]
> Informationen zu den Unterschieden zwischen Ratenbegrenzungen und Kontingenten finden Sie unter [Ratenbegrenzungen und Kontingente](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas).

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Beispiel

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemente

| Name      | Beschreibung                                                                                                                                                                                                                                                                                  | Erforderlich |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Stammelement                                                                                                                                                                                                                                                                                | Ja      |
| api       | Fügen Sie mindestens eines dieser Elemente hinzu, um ein Aufrufkontingent für APIs innerhalb des Produkts zu erzwingen. Produkt- und API-Aufrufkontingente werden unabhängig voneinander angewendet. Auf „api“ kann über `name` oder `id` verwiesen werden. Wenn beide Attribute bereitgestellt werden, wird `id` verwendet und `name` ignoriert.                    | Nein       |
| operation | Fügen Sie mindestens eines dieser Elemente hinzu, um ein Aufrufkontingent für Vorgänge innerhalb einer API zu erzwingen. Aufrufkontingente für Produkte, APIs und Vorgänge werden unabhängig voneinander angewendet. Auf „operation“ kann über `name` oder `id` verwiesen werden. Wenn beide Attribute bereitgestellt werden, wird `id` verwendet und `name` ignoriert. | Nein       |

### <a name="attributes"></a>Attributes

| Name           | Beschreibung                                                                                               | Erforderlich                                                         | Standard |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Der Name der API oder des Vorgangs, für die bzw. den das Kontingent gilt.                                             | Ja                                                              | N/V     |
| bandwidth      | Die maximale Gesamtanzahl von Kilobytes, die während des in der `renewal-period` angegebenen Zeitraums zulässig sind. | Es müssen entweder `calls` oder `bandwidth` oder beide Attribute zusammen angegeben werden. | N/V     |
| calls          | Die maximale Gesamtanzahl von Aufrufen, die während des in der `renewal-period` angegebenen Zeitraums zulässig sind.     | Es müssen entweder `calls` oder `bandwidth` oder beide Attribute zusammen angegeben werden. | N/V     |
| renewal-period | Der Zeitraum in Sekunden, nach dem das Kontingent zurückgesetzt wird.                                                  | Ja                                                              | –     |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound
-   **Richtlinienbereiche**: Produkt

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> Nutzungskontingent nach Schlüssel festlegen

> [!IMPORTANT]
> Diese Funktion ist auf der Ebene **Verbrauch** von API Management nicht verfügbar.

Die `quota-by-key`-Richtlinie erzwingt ein erneuerbares oder für die Lebensdauer gültiges Aufruf- und/oder Bandbreitenkontingent pro Schlüssel. Der Schlüssel kann einen beliebigen Zeichenfolgenwert aufweisen und wird in der Regel über einen Richtlinienausdruck angegeben. Optional kann eine inkrementelle Bedingung hinzugefügt werden, um anzugeben, welche Anforderungen für das Kontingent gezählt werden sollen. Wenn der gleiche Schlüsselwert durch mehrere Richtlinien erhöht würde, erfolgt nur eine Erhöhung pro Anforderung. Wenn der Aufrufgrenzwert erreicht wird, empfängt der Aufrufer einen `403 Forbidden`-Antwortstatuscode.

Weitere Informationen und Beispiele zu dieser Richtlinie finden Sie unter [Erweiterte Anforderungsbegrenzung mit Azure API Management](./api-management-sample-flexible-throttling.md).

> [!NOTE]
> Informationen zu den Unterschieden zwischen Ratenbegrenzungen und Kontingenten finden Sie unter [Ratenbegrenzungen und Kontingente](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas).

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird das Kontingent anhand der IP-Adresse des Aufrufers bestimmt.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemente

| Name  | Beschreibung   | Erforderlich |
| ----- | ------------- | -------- |
| quota | Stammelement | Ja      |

### <a name="attributes"></a>Attributes

| Name                | Beschreibung                                                                                               | Erforderlich                                                         | Standard |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandwidth           | Die maximale Gesamtanzahl von Kilobytes, die während des in der `renewal-period` angegebenen Zeitraums zulässig sind. | Es müssen entweder `calls` oder `bandwidth` oder beide Attribute zusammen angegeben werden. | –     |
| calls               | Die maximale Gesamtanzahl von Aufrufen, die während des in der `renewal-period` angegebenen Zeitraums zulässig sind.     | Es müssen entweder `calls` oder `bandwidth` oder beide Attribute zusammen angegeben werden. | –     |
| counter-key         | Der Schlüssel, der für die Kontingentrichtlinie verwendet werden soll.                                                                      | Ja                                                              | –     |
| increment-condition | Der boolesche Ausdruck, der angibt, ob die Anforderung für das Kontingent gezählt werden soll (`true`).             | Nein                                                               | –     |
| renewal-period      | Der Zeitraum in Sekunden, nach dem das Kontingent zurückgesetzt wird.                                                  | Ja                                                              | –     |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound
-   **Richtlinienbereiche:** alle Bereiche

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> JWT überprüfen

Die `validate-jwt`-Richtlinie erzwingt die Existenz und die Gültigkeit eines JSON-Webtokens (JWT), das entweder aus einem angegebenen HTTP-Header oder einem angegebenen Abfrageparameter extrahiert wurde.

> [!IMPORTANT]
> Die `validate-jwt`-Richtlinie erfordert, dass der über `exp` registrierte Anspruch in das JWT einbezogen wird, sofern nicht das `require-expiration-time`-Attribut angegeben und auf `false` festgelegt ist.
> Die `validate-jwt`-Richtlinie unterstützt die Signaturalgorithmen HS256 und RS256. Für HS256 muss der Schlüssel inline innerhalb der Richtlinie in Base64-codiertem Format angegeben werden. Für RS256 kann der Schlüssel entweder über einen Open ID-Konfigurationsendpunkt oder durch Angabe der ID eines hochgeladenen Zertifikats bereitgestellt werden, das den öffentlichen Schlüssel oder das Modulus-Exponent-Paar des öffentlichen Schlüssels enthält.
> Die `validate-jwt`-Richtlinie unterstützt Token, die mithilfe der folgenden Verschlüsselungsalgorithmen mit symmetrischen Schlüsseln verschlüsselt wurden: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>Beispiele

#### <a name="simple-token-validation"></a>Einfache Tokenüberprüfung

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="token-validation-with-rsa-certificate"></a>Tokenvalidierung mit RSA-Zertifikat

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certficate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory-Tokenüberprüfung

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C-Tokenüberprüfung

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorisieren des Zugriffs auf Vorgänge basierend auf Tokenansprüchen

Dieses Beispiel zeigt die Verwendung der Richtlinie [JWT überprüfen](api-management-access-restriction-policies.md#ValidateJWT), um den Zugriff auf Vorgänge basierend auf dem Wert für Tokenansprüche zu autorisieren.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>Elemente

| Element             | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                           | Erforderlich |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Stammelement                                                                                                                                                                                                                                                                                                                                         | Ja      |
| audiences           | Enthält eine Liste der zulässigen audience-Ansprüche, die im Token vorhanden sein können. Wenn mehrere audience-Werte vorhanden sind, wird jeder Wert ausprobiert, bis entweder alle verbraucht sind (in diesem Fall gibt es einen Überprüfungsfehler) oder ein Wert erfolgreich ist. Mindestens ein audience-Wert muss angegeben werden.                                                                     | Nein       |
| issuer-signing-keys | Eine Liste von Base64-codierten Sicherheitsschlüsseln, die zum Überprüfen von signierten Token verwendet werden. Wenn mehrere Sicherheitsschlüssel vorhanden sind, wird jeder Schlüssel ausprobiert, bis entweder alle verbraucht sind (in diesem Fall gibt es einen Validierungsfehler) oder ein Schlüssel erfolgreich ist (hilfreich für ein Tokenrollover). Schlüsselelemente verfügen über ein optionales `id`-Attribut, das für einen Abgleich mit dem `kid`-Anspruch verwendet wird. <br/><br/>Alternativ dazu geben Sie folgendermaßen einen Signaturschlüssel eines Zertifikatausstellers an:<br/><br/> – `certificate-id` im Format `<key certificate-id="mycertificate" />` zur Angabe des Bezeichners einer Zertifikatentität, die in API Management [hochgeladen](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) wurde<br/>– Wertepaar aus RSA-Modulus `n` und -Exponent `e` im Format `<key n="<modulus>" e="<exponent>" />` zur Angabe der RSA-Parameter im Base64URL-codierten Format               | Nein       |
| decryption-keys     | Eine Liste der Base64-codierten Schlüssel zum Entschlüsseln der Token. Wenn mehrere Sicherheitsschlüssel vorhanden sind, wird jeder Schlüssel ausprobiert, bis entweder alle verbraucht sind (in diesem Fall gibt es einen Validierungsfehler) oder ein Schlüssel erfolgreich ist. Schlüsselelemente verfügen über ein optionales `id`-Attribut, das für einen Abgleich mit dem `kid`-Anspruch verwendet wird.<br/><br/>Alternativ dazu geben Sie folgendermaßen einen Entschlüsselungsschlüssel an:<br/><br/> – `certificate-id` im Format `<key certificate-id="mycertificate" />` zur Angabe des Bezeichners einer Zertifikatentität, die in API Management [hochgeladen](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) wurde                                                 | Nein       |
| issuers             | Eine Liste der zulässigen Prinzipale, die das Token ausgestellt haben. Wenn mehrere issuer-Werte vorhanden sind, wird jeder Wert ausprobiert, bis entweder alle verbraucht sind (in diesem Fall gibt es einen Überprüfungsfehler) oder ein Wert erfolgreich ist.                                                                                                                                         | Nein       |
| openid-config       | Das Element, das zum Angeben eines kompatiblen Open ID-Konfigurationsendpunkts verwendet wird, von dem Signaturschlüssel und Aussteller abgerufen werden können.                                                                                                                                                                                                                        | Nein       |
| required-claims     | Enthält eine Liste von Ansprüchen, deren Vorhandensein im Token erwartet wird, damit dieses als gültig eingestuft wird. Wenn das `match`-Attribut auf `all` festgelegt ist, muss jeder Anspruchswert in der Richtlinie im Token vorhanden sein, damit die Überprüfung erfolgreich ist. Wenn das `match`-Attribut auf `any` festgelegt ist, muss mindestens ein Anspruch im Token vorhanden sein, damit die Überprüfung erfolgreich ist. | Nein       |

### <a name="attributes"></a>Attributes

| Name                            | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                                                                                            | Erforderlich                                                                         | Standard                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | Zeitspanne. Verwenden Sie diese Option, um die maximal erwartete Zeitdifferenz zwischen den Systemuhren des Tokenausstellers und der API Management-Instanz anzugeben.                                                                                                                                                                                                                                                                                                               | Nein                                                                               | 0 Sekunden                                                                         |
| failed-validation-error-message | Die Fehlermeldung, die im HTTP-Antworttext zurückgegeben werden soll, wenn das JWT die Überprüfung nicht besteht. In dieser Meldung müssen alle Sonderzeichen ordnungsgemäß mit Escapezeichen versehen sein.                                                                                                                                                                                                                                                                                                 | Nein                                                                               | Die Standardfehlermeldung hängt vom Überprüfungsproblem ab, z.B. „JWT nicht vorhanden“. |
| failed-validation-httpcode      | Der HTTP-Statuscode, der zurückgegeben werden soll, wenn das JWT die Überprüfung nicht besteht.                                                                                                                                                                                                                                                                                                                                                                                         | Nein                                                                               | 401                                                                               |
| header-name                     | Der Name des HTTP-Headers, der das Token enthält.                                                                                                                                                                                                                                                                                                                                                                                                         | Eines von `header-name`, `query-parameter-name` oder `token-value` muss angegeben werden. | –                                                                               |
| query-parameter-name            | Der Name des Abfrageparameters, der das Token enthält.                                                                                                                                                                                                                                                                                                                                                                                                     | Eines von `header-name`, `query-parameter-name` oder `token-value` muss angegeben werden. | –                                                                               |
| token-value                     | Ausdruck, der eine Zeichenfolge mit JWT-Token zurückgibt                                                                                                                                                                                                                                                                                                                                                                                                     | Eines von `header-name`, `query-parameter-name` oder `token-value` muss angegeben werden. | –                                                                               |
| id                              | Das `id`-Attribut im `key`-Element ermöglicht Ihnen die Angabe der Zeichenfolge, die mit dem `kid`-Anspruch im Token (sofern vorhanden) verglichen wird, um den geeigneten Schlüssel für die Signaturüberprüfung zu ermitteln.                                                                                                                                                                                                                                           | Nein                                                                               | N/V                                                                               |
| match                           | Das `match`-Attribut im `claim`-Element gibt an, ob jeder Anspruchswert in der Richtlinie im Token vorhanden sein muss, damit die Überprüfung erfolgreich ist. Mögliche Werte:<br /><br /> - `all` – jeder Anspruchswert in der Richtlinie muss im Token vorhanden sein, damit die Überprüfung erfolgreich ist.<br /><br /> - `any` – mindestens ein Anspruchswert in der Richtlinie muss im Token vorhanden sein, damit die Überprüfung erfolgreich ist.                                                       | Nein                                                                               | alle                                                                               |
| require-expiration-time         | Boolesch. Gibt an, ob ein Ablaufanspruch im Token erforderlich ist.                                                                                                                                                                                                                                                                                                                                                                               | Nein                                                                               | true                                                                              |
| require-scheme                  | Der Name des Tokenschemas, z.B. „Bearer“. Wenn dieses Attribut festgelegt ist, stellt die Richtlinie sicher, das das angegebene Schema im Wert für den Autorisierungsheader vorhanden ist.                                                                                                                                                                                                                                                                                    | Nein                                                                               | N/V                                                                               |
| require-signed-tokens           | Boolesch. Gibt an, ob ein Token signiert sein muss.                                                                                                                                                                                                                                                                                                                                                                                           | Nein                                                                               | true                                                                              |
| Trennzeichen                       | Zeichenfolge. Gibt ein Trennzeichen (z.B. „,“) zum Extrahieren eines Satzes von Werten aus einem mehrwertigen Anspruch an.                                                                                                                                                                                                                                                                                                                                          | Nein                                                                               | –                                                                               |
| url                             | URL des Open ID-Konfigurationsendpunkts, von dem die Open ID-Konfigurationsmetadaten abgerufen werden können. Die Antwort sollte den Spezifikationen entsprechen, wie sie unter URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata` definiert sind. Verwenden Sie für Azure Active Directory diese URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration`. Verwenden Sie dabei den Namen Ihres Verzeichnismandanten, z.B. `contoso.onmicrosoft.com`. | Ja                                                                              | –                                                                               |
| output-token-variable-name      | Eine Zeichenfolge. Der Name der Kontextvariablen, die den Tokenwert bei erfolgreicher Überprüfung als ein Objekt des Typs [`Jwt`](api-management-policy-expressions.md) empfängt                                                                                                                                                                                                                                                                                     | Nein                                                                               | –                                                                               |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound
-   **Richtlinienbereiche:** alle Bereiche

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

-   [Richtlinien in Azure API Management](api-management-howto-policies.md)
-   [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
-   Unter [Richtlinien für die API-Verwaltung](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
-   [API Management-Richtlinienbeispiele](./policy-reference.md)