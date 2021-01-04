---
title: 'Azure App Configuration-REST-API: Header'
description: Referenzseiten für mit der Azure App Configuration-REST-API verwendete Header
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932573"
---
# <a name="headers"></a>Header

Dieser Artikel enthält Links zu Referenzseiten für Header, die mit der Azure App Configuration-REST-API verwendet werden.

## <a name="request-headers"></a>Anforderungsheader

In der folgenden Tabelle werden Anforderungsheader beschrieben, die in Azure App Configuration häufig verwendet werden.

| Header | Beschreibung | Beispiel |
|--|--|--|
| **Autorisierung** | Wird zum [Authentifizieren](./rest-api-authentication-index.md) einer Anforderung an den Dienst verwendet. Siehe [Abschnitt 14.8](https://tools.ietf.org/html/rfc2616#section-14.8). | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Annehmen** | Informiert den Server darüber, welchen Medientyp der Client in einer HTTP-Antwort akzeptiert. Siehe [Abschnitt 14.1](https://tools.ietf.org/html/rfc2616#section-14.1). | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-Datetime** | Fordert den Server auf, seinen Inhalt als Darstellung des vorherigen Zustands zurückzugeben. Der Wert dieses Headers ist der angeforderte datetime-Wert dieses Zustands. Siehe [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1). | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | Enthält den Medientyp des Inhalts im HTTP-Anforderungstext. Siehe [Abschnitt 14.17](https://tools.ietf.org/html/rfc2616#section-14.17). | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Date** | Der datetime-Wert, zu dem die HTTP-Anforderung ausgegeben wurde. Dieser Header wird in der [HMAC-Authentifizierung](./rest-api-authentication-hmac.md) verwendet. Siehe [Abschnitt 14.18](https://tools.ietf.org/html/rfc2616#section-14.18). | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Host** | Gibt den Mandanten an, für den die Anforderung ausgegeben wurde. Dieser Header wird in der [HMAC-Authentifizierung](./rest-api-authentication-hmac.md) verwendet. Siehe [Abschnitt 14.23](https://tools.ietf.org/html/rfc2616#section-14.23). | `Host: contoso.azconfig.io` |
| **If-Match** | Wird verwendet, um eine HTTP-Anforderung als bedingt festzulegen. Diese Anforderung darf nur dann erfolgreich sein, denn das ETag der Zielressource mit dem Wert dieses Headers übereinstimmt. Der Wert „*“ entspricht einem beliebigen ETag. Siehe [Abschnitt 14.24](https://tools.ietf.org/html/rfc2616#section-14.24). | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | Wird verwendet, um eine HTTP-Anforderung als bedingt festzulegen. Diese Anforderung darf nur dann erfolgreich sein, denn das ETag der Zielressource nicht mit dem Wert dieses Headers übereinstimmt. Der Wert „*“ entspricht einem beliebigen ETag. Siehe [Abschnitt 14.26](https://tools.ietf.org/html/rfc2616#section-14.26). | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-Token** | Wird verwendet, um während einer Sequenz von Anforderungen Echtzeitkonsistenz zu ermöglichen. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-client-request-id** | Eine vom Client bereitgestellte eindeutige ID, mit der ein Roundtrip einer Anforderung nachverfolgt wird. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-ms-content-sha256** | Ein SHA256-Hash des HTTP-Anforderungstexts. Dieser Header wird in der [HMAC-Authentifizierung](./rest-api-authentication-hmac.md) verwendet. | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-date** | Dieser Header kann anstelle des `Date`-Headers festgelegt und verwendet werden, wenn auf den date-Header nicht zugegriffen werden kann. Dieser Header wird in der [HMAC-Authentifizierung](./rest-api-authentication-hmac.md) verwendet. | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-return-client-request-id** | Wird zusammen mit dem `x-ms-client-request-id`-Header verwendet. Wenn der Wert dieses Headers TRUE lautet, wird der Server angewiesen, den Wert des `x-ms-client-request-id`-Anforderungsheaders zurückzugeben. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Antwortheader

Der Server kann die folgenden HTTP-Header in seinen Antworten einschließen.

| Header | Beschreibung | Beispiel |
|--|--|--|
| **Content-Type** | Enthält den Medientyp des Inhalts im HTTP-Antworttext. Siehe [Abschnitt 14.17](https://tools.ietf.org/html/rfc2616#section-14.17). | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | Ein nicht transparentes Token, das den Zustand einer bestimmten Ressource darstellt. Kann in bedingten Vorgängen verwendet werden. Siehe [Abschnitt 14.19](https://tools.ietf.org/html/rfc2616#section-14.19). | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Last-Modified** | Beschreibt, wann die angeforderte Ressource zuletzt geändert wurde. Ist als [HTTP-Date](https://tools.ietf.org/html/rfc2616#section-3.3.1) formatiert. Siehe [Abschnitt 14.29](https://tools.ietf.org/html/rfc2616#section-14.29). | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Link** | Stellt Links zu Ressourcen bereit, die mit der Antwort im Zusammenhang stehen. Dieser Header wird zur Paginierung mithilfe des _next_-Links verwendet. Siehe [RFC 5988](https://tools.ietf.org/html/rfc5988). | `Link: </kv?after={token}>; rel="next"` |
| **Memento-Datetime** | Gibt an, dass der Inhalt einer Antwort einen vorherigen Zustand darstellt. Der Wert dieses Headers ist der datetime-Wert dieses Zustands. Siehe [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1). | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **retry-after-ms** | Gibt einen empfohlenen Zeitraum (in Millisekunden) an, den der Client abwarten soll, bevor er eine fehlerhafte Anforderung erneut versucht. | `retry-after-ms: 10` |
| **x-ms-request-id** | Eine vom Server generierte eindeutige ID, mit der die Anforderung im Dienst nachverfolgt wird. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Authenticate** | Wird verwendet, um Clients zur Authentifizierung aufzufordern und einen Grund anzugeben, wenn bei einem Authentifizierungsversuch ein Fehler aufgetreten ist. Siehe [Abschnitt 14.47](https://tools.ietf.org/html/rfc2616#section-14.47). | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
