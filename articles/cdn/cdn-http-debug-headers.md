---
title: HTTP-Header „X-EC-Debug“ für Azure CDN-Regel-Engine | Microsoft Docs
description: Der Debugcache-Anforderungsheader „X-EC-Debug“ stellt zusätzliche Informationen zur Cacherichtlinie bereit, die auf das angeforderte Objekt angewendet wird. Diese Header sind spezifisch für Verizon.
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
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: e5693e0e191b36aa8d4552824c649a38d2f17b5b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475284"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>HTTP-Header „X-EC-Debug“ für Azure CDN-Regel-Engine
Der Debugcache-Anforderungsheader (`X-EC-Debug`) stellt zusätzliche Informationen zur Cacherichtlinie bereit, die auf das angeforderte Objekt angewendet wird. Diese Header sind spezifisch für **Azure CDN Premium-Produkte von Verizon**.

## <a name="usage"></a>Verwendung
Die an einen Benutzer von den POP-Servern gesendete Antwort enthält die `X-EC-Debug`-Header nur, wenn die folgenden Bedingungen erfüllt sind:

- Das Feature [Debug Cache Response Headers](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) wurde für die Regel-Engine für die angegebene Anforderung aktiviert.
- Die angegebene Anforderung definiert den Satz der Debugcache-Antwortheader, die in die Antwort einbezogen werden.

## <a name="requesting-debug-cache-information"></a>Anfordern von Debugcacheinformationen
Verwenden Sie die folgenden Anweisungen in der angegebenen Anforderung, um die Debugcacheinformationen zu definieren, die in der Antwort enthalten sein werden:

Anforderungsheader | BESCHREIBUNG |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Cachestatuscode](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Cachestatuscode](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Zwischenspeicherbar](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Cacheschlüssel](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Cachestatus](#cache-state-response-header)

### <a name="syntax"></a>Syntax

Zum Anfordern von Debugcache-Antwortheadern müssen der folgende Header und die angegebenen Anweisungen in die Anforderung aufgenommen werden:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>X-EC-Debug-Beispielheader

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Cachestatus-Codeinformationen
Der X-EC-Debug-Antwortheader kann einen Server identifizieren und durch die folgenden Anweisungen feststellen, wie er die Antwort verarbeitet hat:

Header | BESCHREIBUNG
-------|------------
X-EC-Debug: x-ec-cache | Dieser Header wird gemeldet, wenn Inhalt über das CDN weitergeleitet wird. Er identifiziert den POP-Server, der die Anforderung erfüllt hat.
X-EC-Debug: x-ec-cache-remote | Dieser Header wird nur dann gemeldet, wenn der angeforderte Inhalt auf einem Shield-Ursprungsserver oder einem ADN-Gatewayserver zwischengespeichert wurde.

### <a name="response-header-format"></a>Format des Antwortheaders

Der Header „X-EC-Debug“ meldet Cachestatus-Codeinformationen im folgenden Format:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Die oben in der Antwortheadersyntax verwendeten Begriffe sind folgendermaßen definiert:
- StatusCode: Gibt an, wie der angeforderte Inhalt vom CDN verarbeitet wurde. Dies wird über einen Cachestatuscode dargestellt.
    
    Der Statuscode TCP_DENIED kann anstelle von NONE gemeldet werden, wenn eine nicht autorisierte Anforderung aufgrund von tokenbasierter Authentifizierung verweigert wird. Allerdings wird der Statuscode NONE weiterhin verwendet, wenn Cachestatusberichte oder Protokollrohdaten angezeigt werden.

- Plattform: Gibt die Plattform an, auf der der Inhalt angefordert wurde. Die folgenden Codes sind für dieses Feld gültig:

    Code  | Plattform
    ------| --------
    ECAcc | HTTP groß
    ECS   | HTTP klein
    ECD   | Application Delivery Network (ADN)

- POP: Gibt den [POP](cdn-pop-abbreviations.md)-Standort an, der die Anforderung verarbeitet hat. 

### <a name="sample-response-headers"></a>Beispielantwortheader

Die folgenden Beispielheader stellen Cachestatus-Codeinformationen für eine Anforderung bereit:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Zwischenspeicherbarer Antwortheader
Der Antwortheader `X-EC-Debug: x-ec-check-cacheable` gibt an, ob der angeforderte Inhalt hätte zwischengespeichert werden können.

Dieser Antwortheader gibt nicht an, ob das Zwischenspeichern stattgefunden hat. Stattdessen gibt er an, ob die Anforderung für Zwischenspeichern geeignet gewesen wäre.

### <a name="response-header-format"></a>Format des Antwortheaders

Der Antwortheader `X-EC-Debug`, der meldet, ob eine Anforderung hätte zwischengespeichert werden können, verwendet das folgende Format:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Der oben in der Antwortheadersyntax verwendete Begriff ist folgendermaßen definiert:

Wert  | BESCHREIBUNG
-------| --------
JA    | Gibt an, dass der angeforderte Inhalt für das Zwischenspeichern geeignet war.
NO     | Gibt an, dass der angeforderte Inhalt für das Zwischenspeichern nicht geeignet war. Dieses Status ergibt sich möglicherweise aufgrund einer der folgenden Ursachen: <br /> – Kundenspezifische Konfiguration: Eine für Ihr Konto spezifische Konfiguration kann verhindern, dass die POP-Server ein Objekt zwischenspeichern. Beispielsweise kann die Regel-Engine verhindern, dass ein Objekt zwischengespeichert wird, indem sie das Feature „Cache umgehen“ für die betreffenden Anforderungen aktiviert.<br /> – Cacheantwortheader: Die Header „Cache-Control“ und „Expires“ des angeforderten Objekts können verhindern, dass POP-Server das Objekt zwischenspeichern.
UNKNOWN | Gibt an, dass die Server nicht beurteilen konnten, ob das angeforderte Objekt zwischenspeicherbar war. Dieser Status tritt normalerweise auf, wenn die Anforderung aufgrund von tokenbasierter Authentifizierung abgelehnt wird.

### <a name="sample-response-header"></a>Beispielantwortheader

Der folgende Beispielantwortheader gibt an, ob der angeforderte Inhalt hätte zwischengespeichert werden können:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cache-Key-Antwortheader
Der `X-EC-Debug: x-ec-cache-key`-Antwortheader gibt den physischen cache-key an, der dem angeforderten Inhalt zugeordnet ist. Ein physischer cache-key besteht aus dem relativen Pfad, der ein Objekt zum Zweck der Zwischenspeicherung identifiziert. Die Server suchen also in dem Pfad, der durch den zugehörigen Cacheschlüssel definiert wird, nach einer zwischengespeicherten Version eines Objekts.

Dieser physische cache-key beginnt mit einem doppelten Schrägstrich (//), gefolgt vom Protokoll, das zum Anfordern des Inhalts (HTTP oder HTTPS) verwendet wurde. Auf dieses Protokoll folgt der relative Pfad zum angeforderten Objekt, der mit dem Inhaltszugriffspunkt beginnt (z.B. _/000001/_ ).

Standardmäßig sind HTTP-Plattformen für die Verwendung von *standard-cache* konfiguriert, was bedeutet, dass Abfragezeichenfolgen vom Zwischenspeichermechanismus ignoriert werden. Dieser Art der Konfiguration verhindert, dass der cache-key Daten der Abfragezeichenfolge umfasst.

Wenn eine Abfragezeichenfolge im cache-key aufgezeichnet wird, wird sie in ihr Hashäquivalent konvertiert und dann zwischen dem Namen des angeforderten Objekts und seiner Dateierweiterung eingefügt (z.B. Objekt&lt;Hashwert&gt;.html).

### <a name="response-header-format"></a>Format des Antwortheaders

Der `X-EC-Debug`-Antwortheader meldet physische cache-key-Informationen im folgenden Format:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Beispielantwortheader

Der folgende Beispielantwortheader gibt den physischen cache-key für den angeforderten Inhalt an:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Cachestatus-Antwortheader
Der `X-EC-Debug: x-ec-cache-state`-Antwortheader gibt den Cachestatus des angeforderten Inhalts zum Zeitpunkt der Anforderung an.

### <a name="response-header-format"></a>Format des Antwortheaders

Der `X-EC-Debug`-Antwortheader meldet Cachestatusinformationen im folgenden Format:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Die oben in der Antwortheadersyntax verwendeten Begriffe sind folgendermaßen definiert:

- MASeconds: Gibt den max-age-Wert (in Sekunden) an, wie durch die Cache-Control-Header des angeforderten Inhalts definiert.

- MATimePeriod: Konvertiert den max-age-Wert (d.h. MASeconds) in das ungefähre Äquivalent einer größeren Einheit (z.B. Tage). 

- UnixTime: Gibt den Cachezeitstempel des angeforderten Inhalts in Unix-Zeit an (auch als POSIX-Zeit oder Unix-Epoche bezeichnet). Der Cachezeitstempel gibt Datum/Uhrzeit des Starts an. Aus diesen Angaben wird die Gültigkeitsdauer (TTL) eines Objekts berechnet. 

    Wenn der Ursprungsserver keinen HTTP-Cacheserver eines Drittanbieters verwendet oder dieser Server den Age-Antwortheader nicht zurückgibt, ist der Cachezeitstempel immer das Datum/Uhrzeit, an dem/der das Objekt abgerufen oder erneut validiert wurde. Andernfalls verwenden die POP-Server das Feld „Age“, um die Gültigkeitsdauer (TTL) des Objekts wie folgt zu berechnen: Retrieval/RevalidateDateTime – Age.

- ttt, tt MMM jjjj HH:mm:ss GMT: Gibt den Cachezeitstempel des angeforderten Inhalts an. Weitere Informationen finden Sie weiter oben unter dem Begriff „UnixTime“.

- CASeconds: Gibt die Anzahl von Sekunden an, die seit dem Cachezeitstempel vergangen sind.

- RTSeconds: Gibt die Anzahl der verbleibenden Sekunden an, für die der zwischengespeicherte Inhalt als aktuell betrachtet wird. Dieser Wert wird wie folgt berechnet: RTSeconds = max-age – cache age.

- RTTimePeriod: Konvertiert den verbleibenden TTL-Wert (d.h. RTSeconds) in das ungefähre Äquivalent einer größeren Einheit (z.B. Tage).

- ExpiresSeconds: Gibt die Anzahl von Sekunden an, die vor dem im `Expires`-Antwortheader angegebenen Datums-/Uhrzeitwert verbleiben. Wenn der `Expires`-Antwortheader nicht in der Antwort enthalten war, ist der Wert dieses Ausdrucks *none*.

### <a name="sample-response-header"></a>Beispielantwortheader

Der folgende Beispielantwortheader gibt den Cachestatus des angeforderten Inhalts zum Zeitpunkt der Anforderung an:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

