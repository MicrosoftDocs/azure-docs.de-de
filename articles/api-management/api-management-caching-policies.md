---
title: Cacherichtlinien in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Cacherichtlinien, die für die Verwendung in Azure API Management verfügbar sind. Sehen Sie sich Beispiele an, und zeigen Sie zusätzliche verfügbare Ressourcen an.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 4db42d8fa8c676b20b236577ce6646b909df7c3a
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638885"
---
# <a name="api-management-caching-policies"></a>Cacherichtlinien für API Management
Dieses Thema enthält eine Referenz für die folgenden API Management-Richtlinien. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](./api-management-policies.md).

> [!IMPORTANT]
> Der integrierte Cache ist flüchtig und wird von allen Einheiten in derselben Region desselben API Management-Diensts gemeinsam genutzt.

## <a name="caching-policies"></a><a name="CachingPolicies"></a> Cacherichtlinien

- Cacherichtlinien für Antworten
    - [Aus Cache abrufen](api-management-caching-policies.md#GetFromCache): Führt eine Cachesuche aus und gibt ggf. gültige Antworten aus dem Cache zurück.
    - [In Cache ablegen](api-management-caching-policies.md#StoreToCache): Speichert Antworten gemäß der angegebenen Konfiguration für die Cachesteuerung zwischen.
- Cacherichtlinien für Werte
    - [Get value from cache (Wert aus dem Cache abrufen)](#GetFromCacheByKey) – ruft ein zwischengespeichertes Element nach Schlüssel ab.
    - [Store value in cache (Wert im Cache speichern)](#StoreToCacheByKey) – speichert ein Element im Cache auf Basis des Schlüssels.
    - [Wert aus dem Cache entfernen](#RemoveCacheByKey) – Entfernt ein Element im Cache nach Schlüssel.

## <a name="get-from-cache"></a><a name="GetFromCache"></a> Aus Cache abrufen
Verwenden Sie die `cache-lookup`-Richtlinie zum Durchführen einer Cachesuche und ggf. zum Zurückgeben einer gültigen Antwort aus dem Cache. Diese Richtlinie kann in Fällen angewendet werden, in denen sich der Inhalt von Antworten über längere Zeit nicht ändert. Das Zwischenspeichern von Antworten senkt die Bandbreitennutzung und die Prozessoranforderungen auf dem Back-End-Webserver und verringert die Latenz für API-Consumer.

> [!NOTE]
> Diese Richtlinie setzt das Vorhandensein einer entsprechenden Richtlinie [In Cache ablegen](api-management-caching-policies.md#StoreToCache) voraus.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Beispiele

#### <a name="example"></a>Beispiel

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Beispiel für die Verwendung von Richtlinienausdrücken
Dieses Beispiel zeigt die Konfiguration der Dauer des API Management-Antwortcachings. Diese entspricht dem Zwischenspeichern von Antworten des Back-End-Diensts wie durch die `Cache-Control`-Anweisung des Back-End-Diensts angegeben. Eine Demonstration der Konfiguration und Verwendung dieser Richtlinie finden Sie unter [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Cloud Cover-Folge 177 zu weiteren API Management-Funktionen mit Vlad Vinogradsky). Führen Sie einen schnellen Vorlauf bis 25:25 durch.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Weitere Informationen finden Sie unter [Richtlinienausdrücke](api-management-policy-expressions.md) und [Kontextvariable](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemente

|Name|BESCHREIBUNG|Erforderlich|
|----------|-----------------|--------------|
|cache-lookup|Stammelement|Ja|
|vary-by-header|Startet das Zwischenspeichern je nach Wert des angegebenen Headers, z.B. Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host, If-Match.|Nein|
|vary-by-query-parameter|Antworten werden je nach Wert der angegebenen Abfrageparameter zwischengespeichert. Geben Sie einen oder mehrere Parameter an. Verwenden Sie Semikolons als Trennzeichen. Wenn kein Parameter angegeben ist, werden alle Abfrageparameter verwendet.|Nein|

### <a name="attributes"></a>Attributes

| Name                           | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                 | Erforderlich | Standard           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Bei Festlegung auf `true` wird das Zwischenspeichern von Anforderungen erlaubt, die einen Autorisierungsheader enthalten.                                                                                                                                                                                                                                                                        | Nein       | false             |
| caching-type               | Wählen Sie aus den folgenden Attributwerten:<br />- `internal` zur Verwendung des integrierten API Management-Caches,<br />- `external` zur Verwendung des externen Caches (siehe [Verwenden eines externen Azure Caches für Redis in Azure API Management](api-management-howto-cache-external.md)),<br />- `prefer-external` zur Verwendung des externen Caches (sofern konfiguriert); andernfalls wird der interne Cache verwendet. | Nein       | `prefer-external` |
| downstream-caching-type        | Dieses Attribut muss auf einen der folgenden Werte festgelegt werden.<br /><br /> –   none: Downstreamzwischenspeicherung ist unzulässig.<br />–   private: Private Downstreamzwischenspeicherung ist zulässig.<br />–   public: Private und gemeinsam genutzte Downstreamzwischenspeicherung ist zulässig.                                                                                                          | Nein       | Keine              |
| must-revalidate                | Wenn die Downstreamzwischenspeicherung aktiviert ist, aktiviert oder deaktiviert dieses Attribut die `must-revalidate`-Cachesteuerungsanweisung in Gatewayantworten.                                                                                                                                                                                                                      | Nein       | true              |
| vary-by-developer              | Legen Sie den Parameter auf `true` fest, um Antworten pro Entwicklerkonto zwischenzuspeichern, das den in der Anforderung enthaltenen [Abonnementschlüssel](./api-management-subscriptions.md) besitzt.                                                                                                                                                                                                                                                                                                  | Ja      |         False          |
| vary-by-developer-groups       | Legen Sie diese Option auf `true` fest, um Antworten [pro Benutzergruppe](./api-management-howto-create-groups.md) zwischenzuspeichern.                                                                                                                                                                                                                                                                                                             | Ja      |       False            |

### <a name="usage"></a>Verwendung
Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte**: inbound
- **Richtlinienbereiche:** alle Bereiche

## <a name="store-to-cache"></a><a name="StoreToCache"></a> In Cache ablegen
Die `cache-store`-Richtlinie speichert Antworten gemäß den angegebenen Cacheeinstellungen zwischen. Diese Richtlinie kann in Fällen angewendet werden, in denen sich der Inhalt von Antworten über längere Zeit nicht ändert. Das Zwischenspeichern von Antworten senkt die Bandbreitennutzung und die Prozessoranforderungen auf dem Back-End-Webserver und verringert die Latenz für API-Consumer.

> [!NOTE]
> Diese Richtlinie setzt das Vorhandensein einer entsprechenden Richtlinie [Aus Cache abrufen](api-management-caching-policies.md#GetFromCache) voraus.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Beispiele

#### <a name="example"></a>Beispiel

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Beispiel für die Verwendung von Richtlinienausdrücken
Dieses Beispiel zeigt die Konfiguration der Dauer des API Management-Antwortcachings. Diese entspricht dem Zwischenspeichern von Antworten des Back-End-Diensts wie durch die `Cache-Control`-Anweisung des Back-End-Diensts angegeben. Eine Demonstration der Konfiguration und Verwendung dieser Richtlinie finden Sie unter [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Cloud Cover-Folge 177 zu weiteren API Management-Funktionen mit Vlad Vinogradsky). Führen Sie einen schnellen Vorlauf bis 25:25 durch.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Weitere Informationen finden Sie unter [Richtlinienausdrücke](api-management-policy-expressions.md) und [Kontextvariable](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemente

|Name|BESCHREIBUNG|Erforderlich|
|----------|-----------------|--------------|
|cache-store|Stammelement|Ja|

### <a name="attributes"></a>Attributes

| Name             | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                 | Erforderlich | Standard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | Lebensdauer der zwischengespeicherten Einträge, angegeben in Sekunden.                                                                                                                                                                                                                                                                                                   | Ja      | –               |

### <a name="usage"></a>Verwendung
Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte:** outbound
- **Richtlinienbereiche:** alle Bereiche

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> Wert aus Cache abrufen
Verwenden Sie die `cache-lookup-value`-Richtlinie zum Durchführen einer Cachesuche nach Schlüssel und zum Zurückgeben eines zwischengespeicherten Werts. Der Schlüssel kann einen beliebigen Zeichenfolgenwert aufweisen und wird in der Regel über einen Richtlinienausdruck angegeben.

> [!NOTE]
> Diese Richtlinie setzt das Vorhandensein einer entsprechenden Richtlinie [Wert in Cache ablegen](#StoreToCacheByKey) voraus.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Beispiel
Weitere Informationen und Beispiele zu dieser Richtlinie finden Sie unter [Benutzerdefiniertes Caching in Azure API Management](./api-management-sample-cache-by-key.md).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elemente

|Name|BESCHREIBUNG|Erforderlich|
|----------|-----------------|--------------|
|cache-lookup-value|Stammelement|Ja|

### <a name="attributes"></a>Attributes

| Name             | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                 | Erforderlich | Standard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Wählen Sie aus den folgenden Attributwerten:<br />- `internal` zur Verwendung des integrierten API Management-Caches,<br />- `external` zur Verwendung des externen Caches (siehe [Verwenden eines externen Azure Caches für Redis in Azure API Management](api-management-howto-cache-external.md)),<br />- `prefer-external` zur Verwendung des externen Caches (sofern konfiguriert); andernfalls wird der interne Cache verwendet. | Nein       | `prefer-external` |
| default-value    | Ein Wert, der der Variablen zugewiesen wird, wenn die Cacheschlüsselsuche zu keinem Ergebnis führt. Wenn dieses Attribut nicht angegeben wird, wird `null` zugewiesen.                                                                                                                                                                                                           | Nein       | `null`            |
| Schlüssel              | In der Suche zu verwendender Cacheschlüsselwert.                                                                                                                                                                                                                                                                                                                       | Ja      | –               |
| variable-name    | Der Name der [Kontextvariablen](api-management-policy-expressions.md#ContextVariables), der der gesuchte Wert zugewiesen wird, wenn die Suche erfolgreich ist. Wenn die Suche zu keinem Ergebnis führt, wird der Variablen der Wert des `default-value`-Attribut oder `null` zugewiesen, wenn das `default-value`-Attribut ausgelassen wird.                                       | Ja      | –               |

### <a name="usage"></a>Verwendung
Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte:** inbound, outbound, backend, on-error
- **Richtlinienbereiche:** alle Bereiche

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> Wert in Cache ablegen
`cache-store-value` führt die Cachespeicherung nach Schlüssel durch. Der Schlüssel kann einen beliebigen Zeichenfolgenwert aufweisen und wird in der Regel über einen Richtlinienausdruck angegeben.

> [!NOTE]
> Diese Richtlinie setzt das Vorhandensein einer entsprechenden Richtlinie [Wert aus Cache abrufen](#GetFromCacheByKey) voraus.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Beispiel
Weitere Informationen und Beispiele zu dieser Richtlinie finden Sie unter [Benutzerdefiniertes Caching in Azure API Management](./api-management-sample-cache-by-key.md).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elemente

|Name|BESCHREIBUNG|Erforderlich|
|----------|-----------------|--------------|
|cache-store-value|Stammelement|Ja|

### <a name="attributes"></a>Attributes

| Name             | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                 | Erforderlich | Standard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Wählen Sie aus den folgenden Attributwerten:<br />- `internal` zur Verwendung des integrierten API Management-Caches,<br />- `external` zur Verwendung des externen Caches (siehe [Verwenden eines externen Azure Caches für Redis in Azure API Management](api-management-howto-cache-external.md)),<br />- `prefer-external` zur Verwendung des externen Caches (sofern konfiguriert); andernfalls wird der interne Cache verwendet. | Nein       | `prefer-external` |
| duration         | Der Wert wird für die in Sekunden angegebene Dauer zwischengespeichert.                                                                                                                                                                                                                                                                                 | Ja      | –               |
| Schlüssel              | Der Cacheschlüssel, unter dem der Wert gespeichert wird.                                                                                                                                                                                                                                                                                                                   | Ja      | –               |
| value            | Der Wert, der zwischengespeichert werden soll.                                                                                                                                                                                                                                                                                                                                     | Ja      | –               |
### <a name="usage"></a>Verwendung
Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte:** inbound, outbound, backend, on-error
- **Richtlinienbereiche:** alle Bereiche

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> Wert aus Cache entfernen
`cache-remove-value` löscht ein zwischengespeichertes Element, das über seinen Schlüssel identifiziert wird. Der Schlüssel kann einen beliebigen Zeichenfolgenwert aufweisen und wird in der Regel über einen Richtlinienausdruck angegeben.

#### <a name="policy-statement"></a>Richtlinienanweisung

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Beispiel

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elemente

|Name|BESCHREIBUNG|Erforderlich|
|----------|-----------------|--------------|
|cache-remove-value|Stammelement|Ja|

#### <a name="attributes"></a>Attributes

| Name             | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                 | Erforderlich | Standard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Wählen Sie aus den folgenden Attributwerten:<br />- `internal` zur Verwendung des integrierten API Management-Caches,<br />- `external` zur Verwendung des externen Caches (siehe [Verwenden eines externen Azure Caches für Redis in Azure API Management](api-management-howto-cache-external.md)),<br />- `prefer-external` zur Verwendung des externen Caches (sofern konfiguriert); andernfalls wird der interne Cache verwendet. | Nein       | `prefer-external` |
| Schlüssel              | Der Schlüssel des zuvor zwischengespeicherten Werts, der aus dem Cache entfernt werden soll.                                                                                                                                                                                                                                                                                        | Ja      | –               |

#### <a name="usage"></a>Verwendung
Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte:** inbound, outbound, backend, on-error
- **Richtlinienbereiche:** alle Bereiche

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management-Richtlinienbeispiele](./policy-reference.md)