---
title: Features der Regel-Engine für Azure CDN Premium von Verizon
description: Referenzdokumentation zu den Features der Regel-Engine für Azure CDN Premium von Verizon.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020414"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Features der Regel-Engine für Azure CDN Premium von Verizon

Dieser Artikel bietet ausführliche Beschreibungen der verfügbaren Features für das Azure CDN-[Regelmodul](cdn-verizon-premium-rules-engine.md) (Content Delivery Network).

Der dritte Teil einer Regel ist das Feature. Ein Feature definiert die Art von Aktion, die auf den Anforderungstyp angewendet wird, der anhand verschiedener Übereinstimmungsbedingungen bestimmt wurde.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Referenz zu den Features der Regel-Engine für Azure CDN Premium von Verizon

Folgende Arten von Features sind verfügbar:

* [zugreifen](#access)
* [Zwischenspeichern](#caching)
* [Comment](#comment)
* [Headers](#headers)
* [Protokolle](#logs)
* [Optimieren](#optimize)
* [Ursprung](#origin)
* [Sonderfunktionen](#specialty)
* [URL](#url)
* [Web Application Firewall](#waf)

### <a name="access"></a><a name="access"></a>Zugriff

Diese Features dienen zum Steuern des Zugriffs auf Inhalte.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [Deny Access (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Bestimmt, ob alle Anfragen mit der Antwort „403 – Verboten“ abgelehnt werden. |
| [Token Auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Bestimmt, ob die tokenbasierte Authentifizierung auf eine Anforderung angewendet wird. |
| [Token Auth Denial Code](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Bestimmt die Art der Antwort, die einem Benutzer zurückgegeben wird, wenn eine Anforderung aufgrund der tokenbasierten Authentifizierung verweigert wird. |
| [Token Auth Ignore URL Case](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Bestimmt, ob bei von der tokenbasierten Authentifizierung durchgeführten URL-Vergleichen Groß-/Kleinschreibung unterschieden wird. |
| [Token Auth Parameter](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Bestimmt, ob der Abfragezeichenfolgen-Parameter der tokenbasierten Authentifizierung umbenannt werden soll. |

**[Nach oben](#top)**

### <a name="caching"></a><a name="caching"></a>Zwischenspeichern

Diese Features dienen zum Anpassen des Zeitpunkts und der Art der Zwischenspeicherung von Inhalten.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [Bandwidth Parameters](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Bestimmt, ob Parameter zur Bandbreitenbeschränkung (z. B. „ec_rate“ und „ec_prebuf“) aktiv sein werden. |
| [Bandwidth Throttling](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Schränkt die Bandbreite für die Antwort ein, die von unseren Edgeservern bereitgestellt wird. |
| [Bypass Cache](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Bestimmt, ob die Anforderung unsere Cachetechnologie nutzen kann. |
| [Cache-Control Header Treatment](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Steuert die Generierung von „Cache-Control“-Headern durch den Edgeserver, wenn das Feature „External Max-Age“ aktiv ist. |
| [Cache-Key Query String](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Bestimmt, ob der **Cacheschlüssel** _ einer Anforderung zugeordnete Parameter für Abfragezeichenfolgen ein- oder ausschließt. <br> __ Ein relativer Pfad, der ein Objekt zum Zweck der Zwischenspeicherung identifiziert.  Unsere Edgeserver verwenden diesen relativen Pfad bei der Suche nach zwischengespeicherten Inhalten.  Standardmäßig enthält ein Cacheschlüssel keine Parameter für Abfragezeichenfolgen._ |
| [Cache-Key Rewrite](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Schreibt den einer Anforderung zugeordneten Cacheschlüssel neu. |
| [Complete Cache Fill](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Bestimmt, was passiert, wenn eine Anforderung in einem teilweisen Cachefehler auf einem Edgeserver resultiert. |
| [Compress File Types](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Definiert die Dateiformate, die auf dem Server komprimiert werden. | 
| [Default Internal Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Bestimmt das Standardintervall für maximales Alter für die erneute Überprüfung des Caches von Edge- und Ursprungsserver. |
| [Expires Header Treatment](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Steuert die Generierung von „Expires“-Headern durch einen Edgeserver, wenn das Feature „External Max-Age“ aktiv ist. |
| [External Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Bestimmt das „max-age“-Intervall für die erneute Überprüfung des Caches von Browser und Edgeserver. |
| [Force Internal Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Bestimmt das „max-age“-Intervall für die erneute Überprüfung des Caches von Edge- und Ursprungsserver. |
| [H.264 Support (HTTP Progressive Download)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Bestimmt die Typen von H.264-Dateiformaten, die zum Streamen von Inhalten verwendet werden können. |
| [H.264 Support Video Seek Params](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Überschreibt die Namen, die Parametern zugewiesen werden, die das Durchsuchen von H.264-Medien steuern, wenn „HTTP Progressive Download“ verwendet wird. |
| [Honor No-Cache Request](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Bestimmt, ob „No-Cache“-Anforderungen eines HTTP-Clients an den Ursprungsserver weitergeleitet werden. |
| [Ignore Origin No-Cache](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Bestimmt, ob unser CDN bestimmte Direktiven ignoriert, die von einem Ursprungsserver bereitgestellt werden. |
| [Ignore Unsatisfiable Ranges](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Bestimmt die Antwort, die an Clients zurückgegeben wird, wenn eine Anforderung den Statuscode „416 – Angeforderter Bereich nicht erfüllbar“ generiert. |
| [Internal Max-Stale](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Steuert, wie lange nach Überschreiten der normalen Ablaufzeit ein Cacheobjekt von einem Edgeserver bereitgestellt werden kann, wenn der Edgeserver das Cacheobjekt nicht im Abgleich mit dem Ursprungsserver erneut überprüfen kann. |
| [Partial Cache Sharing](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Bestimmt, ob eine Anforderung teilweise zwischengespeicherte Inhalte erstellen kann. |
| [Prevalidate Cached Content](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Bestimmt, ob zwischengespeicherte Inhalte für eine frühzeitige erneute Überprüfung in Frage kommen, ehe ihre Gültigkeitsdauer abläuft. |
| [Refresh Zero-Byte Cache Files](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Bestimmt, wie eine Anforderung eines HTTP-Clients eines Cacheobjekts mit 0 Byte von unseren Edgeservern verarbeitet wird. |
| [Set Cacheable Status Codes](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Definiert die Gruppe von Statuscodes, die in zwischengespeicherten Inhalten resultieren können. |
| [Stale Content Delivery on Error](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Bestimmt, ob abgelaufene Cache-Inhalte übermittelt werden, wenn während der erneuten Überprüfung des Caches ein Fehler auftritt oder der angeforderte Inhalt vom Kundenursprungsserver abgerufen wird. | 
| [Stale While Revalidate](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Verbessert die Leistung, indem unseren Edgeservern erlaubt wird, dem Anfordernden einen veralteten Client bereitzustellen, während die erneute Überprüfung erfolgt. |

**[Nach oben](#top)**

### <a name="comment"></a><a name="comment"></a>Kommentar

Dieses Feature erlaubt das Hinzufügen eines Hinweises in einer Regel.

**[Nach oben](#top)**

### <a name="headers"></a><a name="headers"></a>Header

Diese Features dienen zum Hinzufügen, Ändern oder Löschen von Headern in der Anforderung oder Antwort.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [Age Response Header](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Bestimmt, ob ein „Age Response Header“ in die Antwort eingeschlossen wird, die an den Anfordernden gesendet wird. |
| [Debug Cache Response Headers](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Bestimmt, ob eine Antwort den [X-EC-Debug Response Header](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) enthalten kann, der Informationen zur Cacherichtlinie für das angeforderte Objekt enthält. |
| [Modify Client Request Header](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Dient zum Überschreiben, Anfügen oder Löschen eines Headers in einer Anforderung. |
| [Modify Client Response Header](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Dient zum Überschreiben, Anfügen oder Löschen eines Headers in einer Antwort. |
| [Set Client IP Custom Header](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Erlaubt das Hinzufügen der IP-Adresse des anfordernden Clients zur Anforderung als benutzerdefinierter Anforderungsheader. |

**[Nach oben](#top)**

### <a name="logs"></a><a name="logs"></a>Protokolle

Diese Features dienen zum Anpassen der Daten, die in unformatierten Protokolldateien gespeichert sind.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [Custom Log Field 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Bestimmt das Format und den Inhalt, das/der dem benutzerdefinierten Protokollfeld in einer unformatierten Protokolldatei zugewiesen wird. |
| [Log Query String](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Bestimmt, ob eine Abfragezeichenfolge zusammen mit der URL in Zugriffsprotokollen gespeichert wird. |

**[Nach oben](#top)**

### <a name="optimize"></a><a name="optimize"></a>Optimieren

Diese Features bestimmen, ob eine Anforderung den vom Edge Optimizer vorgeschlagenen Optimierungen unterzogen wird.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [Edge Optimizer](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Bestimmt, ob der Edge Optimizer auf eine Anforderung angewendet werden kann. |
| [Edge Optimizer – Instantiate Configuration](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Instanziiert oder aktiviert die Edge Optimizer-Konfiguration, die mit einem Standort verknüpft ist. |

**[Nach oben](#top)**

### <a name="origin"></a><a name="origin"></a>Ursprung

Diese Funktionen dienen zum Steuern, wie das CDN mit einem Ursprungsserver kommuniziert.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [Maximum Keep-Alive Requests](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Definiert die maximale Anzahl von Anforderungen für eine „Keep Alive“-Verbindung, bevor diese geschlossen wird. |
| [Proxy Special Headers](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Definiert die [CDN-spezifischen Anforderungsheader](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders), die von einem Edgeserver an einen Ursprungsserver weitergeleitet werden. |

**[Nach oben](#top)**

### <a name="specialty"></a><a name="specialty"></a>Sonderfunktionen

Diese Features bieten erweiterte Funktionalität, die nur von erfahrenen Benutzern verwendet werden sollte.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [Cacheable HTTP Methods](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Bestimmt zusätzliche HTTP-Methoden, die in unserem Netzwerk zwischengespeichert werden können. |
| [Cacheable Request Body Size](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Definiert den Schwellenwert zum Bestimmen, ob eine POST-Antwort zwischengespeichert werden kann. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Bestimmt, ob der Client darüber informiert wird, dass unser CDN-Dienst QUIC unterstützt. |
| [Streaming Optimization](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Passt Ihre Konfiguration für die Zwischenspeicherung an, um die Leistung für Livestreams zu optimieren und die Last auf dem Ursprungsserver zu reduzieren. |
| [User Variable](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Weist einen Wert zu einer benutzerdefinierten Variable zu, die an die maßgeschneiderte Lösung für die Datenverkehrsverarbeitung übergeben wird. |

**[Nach oben](#top)**

### <a name="url"></a><a name="url"></a>URL

Diese Funktionen ermöglichen, dass eine Anforderung zu einer anderen URL umgeleitet bzw. in eine andere URL umgeschrieben wird.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [Follow Redirects](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Bestimmt, ob Anforderungen zum Hostnamen umgeleitet werden können, der im „Location“-Header definiert ist, der vom Kundenursprungsserver zurückgegeben wird. |
| [URL Redirect](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Leitet Anfragen über den „Location“-Header weiter. |
| [URL Rewrite](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | Schreibt die Anforderungs-URL um. |

**[Nach oben](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall

Das Feature [Web Application Firewall](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) bestimmt, ob eine Anforderung von der Web Application Firewall untersucht wird.

**[Nach oben](#top)**

Informationen zu den neuesten Funktionen finden Sie in der [Dokumentation zur Verizon-Regel-Engine](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Nächste Schritte

- [Regel-Engine – Referenz](cdn-verizon-premium-rules-engine-reference.md)
- [Regel-Engine – bedingte Ausdrücke](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Übereinstimmungsbedingungen der Regel-Engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Überschreiben des HTTP-Verhaltens mithilfe der Regel-Engine](cdn-verizon-premium-rules-engine.md)
- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)
