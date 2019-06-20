---
title: Domänenübergreifende Richtlinien in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie mehr über die domänenübergreifenden Richtlinien, die für die Verwendung in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 871294703a4be36e274df1e34b9cc9bee7d19783
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071954"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (Domänenübergreifende API Management-Richtlinien)
Dieses Thema enthält eine Referenz für die folgenden API Management-Richtlinien. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a> Domänenübergreifende Richtlinien

- [Domänenübergreifende Aufrufe zulassen](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – Erlaubt API-Aufrufe aus browserbasierten Clients, die Adobe Flash und Microsoft Silverlight verwenden.
- [CORS](api-management-cross-domain-policies.md#CORS) – Fügt Unterstützung für Cross-Origin Resource Sharing (CORS) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients zu ermöglichen.
- [JSONP](api-management-cross-domain-policies.md#JSONP) – Fügt Unterstützung für JSON mit Padding (JSONP) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients mit JavaScript zu ermöglichen.

## <a name="AllowCrossDomainCalls"></a> Domänenübergreifende Aufrufe zulassen
Verwenden Sie die `cross-domain`-Richtlinie, um die API von browserbasierten Adobe Flash- und Microsoft Silverlight-Clients aus zugänglich zu machen.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Beispiel

```xml
<cross-domain>
    <cross-domain-policy>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain-policy>
</cross-domain>
```

### <a name="elements"></a>Elemente

|NAME|BESCHREIBUNG|Erforderlich|
|----------|-----------------|--------------|
|cross-domain|Stammelement Untergeordnete Elemente entsprechen der [Adobe-Dateispezifikation für domänenübergreifende Richtlinien](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ja|

### <a name="usage"></a>Verwendung
Diese Richtlinie kann in den folgenden [Abschnitten](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) und [Bereichen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte**: inbound
- **Richtlinienbereiche:** global

## <a name="CORS"></a> CORS
Die `cors`-Richtlinie fügt Unterstützung für CORS (Cross-Origin Resource Sharing) einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe von browserbasierten Clients aus zu ermöglichen.

Mit CORS können Browser und Server miteinander interagieren und ermitteln, ob bestimmte ursprungsübergreifende Anfragen (z. B. XMLHttpRequests-Aufrufe aus JavaScript in einer Webseite an andere Domänen) zulässig sind. Dies bietet mehr Flexibilität als wenn nur Anfragen gleichen Ursprungs erlaubt sind, und ist gleichzeitig sicherer als wenn alle ursprungsübergreifenden Anfragen erlaubt sind.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Beispiel
In diesem Beispiel wird die Unterstützung von Preflightanforderungen veranschaulicht, z.B. solchen mit benutzerdefinierten Headern oder mit anderen Methoden als GET und POST. Um benutzerdefinierte Header und zusätzliche HTTP-Verben zu unterstützen, verwenden Sie die Abschnitte `allowed-methods` und `allowed-headers`, wie im folgenden Beispiel gezeigt.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elemente

|NAME|BESCHREIBUNG|Erforderlich|Standard|
|----------|-----------------|--------------|-------------|
|cors|Stammelement|Ja|–|
|allowed-origins|Enthält `origin`-Elemente, die die zulässigen Ursprünge für domänenübergreifende Anforderungen beschreiben. `allowed-origins` kann entweder ein einzelnes `origin`-Element enthalten, das `*` angibt, um einen beliebigen Ursprung zuzulassen, oder ein oder mehrere `origin`-Elemente, die einen URI enthalten.|Ja|–|
|origin|Der Wert kann entweder `*` lauten, um alle Ursprünge zuzulassen, oder ein URI sein, der einen einzelnen Ursprung angibt. Die URI muss Schema, Host und Port enthalten.|Ja|Wenn der Port in einem URI ausgelassen wird, wird Port 80 für HTTP bzw. Port 443 für HTTPS verwendet.|
|allowed-methods|Dieses Element ist erforderlich, wenn andere Methoden als GET oder POST zulässig sind. Enthält `method`-Elemente, die die unterstützten HTTP-Verben angeben.|Nein|Wenn dieser Abschnitt nicht vorhanden ist, werden GET und POST unterstützt.|
|method|Gibt ein HTTP-Verb an.|Mindestens ein `method`-Element ist erforderlich, wenn der Abschnitt `allowed-methods` vorhanden ist.|–|
|allowed-headers|Dieses Element enthält `header`-Elemente, die die Namen der Header angeben, die in der Anforderung enthalten sein können.|Nein|–|
|expose-headers|Dieses Element enthält `header`-Elemente, die die Namen der Header angeben, auf die der Client zugreifen kann.|Nein|–|
|Header|Gibt einen Headernamen an.|Mindestens ein `header`-Element ist in `allowed-headers` oder `expose-headers` erforderlich, wenn der Abschnitt vorhanden ist.|–|

### <a name="attributes"></a>Attribute

|NAME|BESCHREIBUNG|Erforderlich|Standard|
|----------|-----------------|--------------|-------------|
|allow-credentials|Der `Access-Control-Allow-Credentials`-Header in der Preflightantwort wird auf den Wert dieses Attributs festgelegt und wirkt sich auf die Fähigkeit des Clients aus, Anmeldeinformationen in domänenübergreifenden Anforderungen zu senden.|Nein|false|
|preflight-result-max-age|Der `Access-Control-Max-Age`-Header in der Preflightantwort wird auf den Wert dieses Attributs festgelegt und wirkt sich auf die Fähigkeit des Benutzer-Agents aus, die Preflightantwort zwischenzuspeichern.|Nein|0|

### <a name="usage"></a>Verwendung
Diese Richtlinie kann in den folgenden [Abschnitten](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) und [Bereichen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte**: inbound
- **Richtlinienbereiche**: global, Produkt, API, Vorgang

## <a name="JSONP"></a> JSONP
Die Richtlinie `jsonp` fügt einem Vorgang oder einer API Unterstützung für JSON mit Padding (JSONP) hinzu, um domänenübergreifende Aufrufe von browserbasierten Clients mit JavaScript aus zu ermöglichen. JSONP wird in JavaScript-Programmen verwendet, um Daten von einem Server in einer anderen Domäne anzufordern. JSONP umgeht die Einschränkung der meisten Webbrowser, dass der Zugriff auf Webseiten nur innerhalb derselben Domäne möglich ist.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Beispiel

```xml
<jsonp callback-parameter-name="cb" />
```

Wenn Sie die Methode ohne den Rückrufparameter „?cb=XXX“ aufrufen, wird reines JSON zurückgegeben (ohne einen Wrapper für den Funktionsaufruf).

Mit dem Rückrufparameter `?cb=XXX` wird ein JSONP-Ergebnis zurückgegeben, und die JSON-Ausgangsergebnisse werden in der Form `XYZ('<json result goes here>');` um die Rückruffunktion gesetzt.

### <a name="elements"></a>Elemente

|NAME|BESCHREIBUNG|Erforderlich|
|----------|-----------------|--------------|
|jsonp|Stammelement|Ja|

### <a name="attributes"></a>Attribute

|NAME|BESCHREIBUNG|Erforderlich|Standard|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|Der domänenübergreifende JavaScript-Funktionsaufruf mit dem vollqualifizierten Domänennamen, in dem die Funktion liegt, als Präfix.|Ja|–|

### <a name="usage"></a>Verwendung
Diese Richtlinie kann in den folgenden [Abschnitten](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) und [Bereichen](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte:** outbound
- **Richtlinienbereiche:** global, product, API, operation

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](api-management-policy-reference.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management policy samples](policy-samples.md) (API Management-Richtlinienbeispiele)   
