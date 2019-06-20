---
title: Verizon-spezifische HTTP-Header für Azure CDN-Regel-Engine | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie die Verizon-spezifischen HTTP-Header mit der Azure CDN-Regel-Engine verwendet werden.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: b9f7a5332c8529753f2e22efd6af3d04cb3f44b6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479751"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-spezifische HTTP-Header für Azure CDN-Regel-Engine

Wenn bei **Azure CDN Premium-Produkten von Verizon** eine HTTP-Anforderung an den Ursprungsserver gesendet wird, kann der POP-Server (Point-of-Presence) mindestens einen reservierten Header (oder proxyspezifische Header) in der Clientanforderung an den POP-Server hinzufügen. Diese Header stellen zusätzliche Header zu den empfangenen Standardweiterleitungsheadern dar. Informationen zu Standardanforderungsheadern finden Sie unter [Anforderungsfelder](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Wenn Sie verhindern möchten, dass einer dieser reservierten Header in der Azure CDN (Content Delivery Network) POP-Anforderung an den Ursprungsserver hinzugefügt wird, müssen Sie eine Regel mit dem [Feature „Spezielle Proxyheader“](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) in der Regel-Engine erstellen. Schließen Sie in dieser Regel den Header aus, den Sie aus der Standardliste der Header im Feld „Header“ entfernen möchten. Wenn Sie das [Feature „Cacheantwortheader debuggen“](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) aktiviert haben, achten Sie darauf, dass Sie die erforderlichen `X-EC-Debug`-Header hinzufügen. 

Um beispielsweise den `Via`-Header zu entfernen, muss das headers-Feld der Regel die folgende Liste von Headern enthalten: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Regel „Spezielle Proxyheader“](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

Die folgende Tabelle beschreibt die Header, die durch den CDN POP-Server von Verizon in der Anforderung hinzugefügt werden können:

Anforderungsheader | BESCHREIBUNG | Beispiel
---------------|-------------|--------
[Via](#via-request-header) | Identifiziert den POP-Server, der als Proxy für die Anforderung an einen Ursprungsserver gedient hat. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | Gibt die IP-Adresse des Anforderers an.| 10.10.10.10
X-Forwarded-Proto | Gibt das Protokoll der Anforderung an. | http
X-Host | Gibt den Hostnamen der Anforderung an. | cdn.mydomain.com
X-Midgress | Gibt an, ob die Anforderung von einem Proxy über einen zusätzlichen CDN-Server übermittelt wurde. Beispielsweise von einem POP-Server an einen Shield-Ursprungsserver oder von einem POP-Server an einen ADN-Gatewayserver. <br />Dieser Header wird nur dann der Anforderung hinzugefügt, wenn Midgress-Datenverkehr stattfindet. In diesem Fall wird der Header auf 1 festgelegt, um anzugeben, dass die Anforderung über einen Proxy durch einen zusätzlichen CDN-Server übermittelt wurde.| 1
[Host](#host-request-header) | Identifiziert den Host und den Port für den angeforderten Inhalt. | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: Identifiziert die Failoverliste von ADN-Gatewayservern, die einem Kundenursprung zugewiesen wurden. <br />Origin Shield: Gibt die Gruppe der Origin Shield-Server an, die einem Kundenursprung zugewiesen wurden. | `icn1,hhp1,hnd1`
X-EC- _&lt;name&gt;_ | Anforderungsheader, die mit *X-EG* beginnen (z.B. X-EC-Tag, [X-EC-Debug](cdn-http-debug-headers.md)), sind für die Verwendung durch das CDN reserviert.| waf-production

## <a name="via-request-header"></a>Via-Anforderungsheader
Das Format, durch das der `Via`-Anforderungsheader einen POP-Server identifiziert, wird durch die folgende Syntax angegeben:

`Via: Protocol from Platform (POP/ID)` 

Die in der Syntax verwendeten Begriffe sind folgendermaßen definiert:
- Protokoll: Gibt die Version des Protokolls (z.B. HTTP/1.1) an, die für die Proxyübermittlung der Anforderung verwendet wird. 

- Plattform: Gibt die Plattform an, auf der der Inhalt angefordert wurde. Die folgenden Codes sind für dieses Feld gültig: 

    Code | Plattform
    -----|---------
    ECAcc | HTTP groß
    ECS   | HTTP klein
    ECD   | Application Delivery Network (ADN)

- POP: Gibt den [POP](cdn-pop-abbreviations.md)-Standort an, der die Anforderung verarbeitet hat. 

- ID: Nur zur internen Verwendung.

### <a name="example-via-request-header"></a>Via-Beispielanforderungsheader

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Host-Anforderungsheader
Die POP-Server überschreiben den `Host`-Header, wenn die beiden folgenden Bedingungen erfüllt sind:
- Die Quelle für den angeforderten Inhalt ist ein Kundenursprungsserver.
- Die entsprechende Option „HTTP-Hostheader“ des Kundenursprungs ist nicht leer.

Der `Host`-Anforderungsheader wird entsprechend dem in der Option „HTTP-Hostheader“ definierten Wert überschrieben.
Wenn die Option „HTTP-Hostheader“ des Kundenursprungs auf leer festgelegt ist, wird der `Host`-Anforderungsheader, der vom Anfordernden übermittelt wird, an den Ursprungsserver des Kunden weitergeleitet.

## <a name="x-gateway-list-request-header"></a>X-Gateway-List-Anforderungsheader
Ein POP-Server fügt den X-Gateway-List-Anforderungsheader hinzu bzw. überschreibt ihn, wenn eine der folgenden Bedingungen erfüllt wird:
- Die Anforderung verweist auf die ADN-Plattform.
- Die Anforderung wird an einen Kundenursprungsserver weitergeleitet, der durch das Feature „Origin Shield“ geschützt ist.

