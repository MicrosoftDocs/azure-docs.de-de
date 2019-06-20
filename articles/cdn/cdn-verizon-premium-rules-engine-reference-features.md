---
title: Features der Regel-Engine für Azure CDN Premium von Verizon | Microsoft-Dokumentation
description: Referenzdokumentation zu den Features der Regel-Engine für Azure CDN Premium von Verizon.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: dab0b11a350a10a209d67ddc69db5531a2cc292c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482004"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Features der Regel-Engine für Azure CDN Premium von Verizon

Dieser Artikel bietet ausführliche Beschreibungen der verfügbaren Features für das Azure CDN-[Regelmodul](cdn-verizon-premium-rules-engine.md) (Content Delivery Network).

Der dritte Teil einer Regel ist das Feature. Ein Feature definiert die Art von Aktion, die auf den Anforderungstyp angewendet wird, der anhand verschiedener Übereinstimmungsbedingungen bestimmt wurde.

## <a name="access-features"></a>Zugriffsfeatures

Diese Features dienen zum Steuern des Zugriffs auf Inhalte.

NAME | Zweck
-----|--------
[Deny Access (403)](#deny-access-403) | Bestimmt, ob alle Anfragen mit der Antwort „403 – Verboten“ abgelehnt werden.
[Token Auth](#token-auth) | Bestimmt, ob die tokenbasierte Authentifizierung auf eine Anforderung angewendet wird.
[Token Auth Denial Code](#token-auth-denial-code) | Bestimmt die Art der Antwort, die einem Benutzer zurückgegeben wird, wenn eine Anforderung aufgrund der tokenbasierten Authentifizierung verweigert wurde.
[Token Auth Ignore URL Case](#token-auth-ignore-url-case) | Bestimmt, ob bei von der tokenbasierten Authentifizierung durchgeführten URL-Vergleichen Groß-/Kleinschreibung unterschieden wird.
[Token Auth Parameter](#token-auth-parameter) | Bestimmt, ob der Abfragezeichenfolgen-Parameter der tokenbasierten Authentifizierung umbenannt werden soll.

## <a name="caching-features"></a>Cachingfeatures

Diese Features dienen zum Anpassen des Zeitpunkts und der Art der Zwischenspeicherung von Inhalten.

NAME | Zweck
-----|--------
[Bandwidth Parameters](#bandwidth-parameters) | Bestimmt, ob Parameter zur Bandbreitenbeschränkung (beispielsweise „ec_rate“ und „ec_prebuf“) aktiv sind.
[Bandwidth Throttling](#bandwidth-throttling) | Drosselt die Bandbreite für die Antwort, die vom POP (Point of Presence) bereitgestellt wird.
[Bypass Cache](#bypass-cache) | Bestimmt, ob die Anforderung das Zwischenspeichern umgehen soll.
[Cache-Control Header Treatment](#cache-control-header-treatment) | Steuert die Generierung von `Cache-Control`-Headern durch den POP, wenn das Feature „External Max-Age“ aktiv ist.
[Cache-Key Query String](#cache-key-query-string) | Bestimmt, ob der Cacheschlüssel Abfragezeichenfolgenparameter, die einer Anforderung zugeordnet sind, ein- oder ausschließt.
[Cache-Key Rewrite](#cache-key-rewrite) | Schreibt den einer Anforderung zugeordneten Cacheschlüssel neu.
[Complete Cache Fill](#complete-cache-fill) | Bestimmt, was passiert, wenn eine Anforderung in einem teilweisen Cachefehler auf einem POP resultiert.
[Compress File Types](#compress-file-types) | Definiert die Dateiformate für die Dateien, die auf dem Server komprimiert werden.
[Default Internal Max-Age](#default-internal-max-age) | Bestimmt das max-age-Standardintervall für die erneute Überprüfung des Caches von POP und Ursprungsserver.
[Expires Header Treatment](#expires-header-treatment) | Steuert die Generierung von `Expires`-Headern durch einen POP, wenn das Feature „External Max-Age“ aktiv ist.
[External Max-Age](#external-max-age) | Bestimmt das max-age-Intervall für die erneute Überprüfung des Caches von Browser und POP.
[Force Internal Max-Age](#force-internal-max-age) | Bestimmt das max-age-Intervall für die erneute Überprüfung des Caches von POP und Ursprungsserver.
[H.264 Support (HTTP Progressive Download)](#h264-support-http-progressive-download) | Bestimmt die Typen von H.264-Dateiformaten, die zum Streamen von Inhalten verwendet werden können.
[Honor No-Cache Request](#honor-no-cache-request) | Bestimmt, ob „no-cache“-Anforderungen eines HTTP-Clients an den Ursprungsserver weitergeleitet werden.
[Ignore Origin No-Cache](#ignore-origin-no-cache) | Bestimmt, ob das CDN bestimmte Direktiven ignoriert, die von einem Ursprungsserver bereitgestellt werden.
[Ignore Unsatisfiable Ranges](#ignore-unsatisfiable-ranges) | Bestimmt die Antwort, die an Clients zurückgegeben wird, wenn eine Anforderung den Statuscode „416: Angeforderter Bereich nicht erfüllbar“ generiert.
[Internal Max-Stale](#internal-max-stale) | Steuert, wie lange nach Überschreiten der normalen Ablaufzeit ein zwischengespeichertes Asset von einem POP bereitgestellt werden kann, wenn der POP das zwischengespeicherte Asset nicht im Abgleich mit dem Ursprungsserver erneut überprüfen kann.
[Partial Cache Sharing](#partial-cache-sharing) | Bestimmt, ob eine Anforderung teilweise zwischengespeicherte Inhalte erstellen kann.
[Prevalidate Cached Content](#prevalidate-cached-content) | Bestimmt, ob zwischengespeicherte Inhalte für eine frühzeitige erneute Überprüfung in Frage kommen, ehe ihre Gültigkeitsdauer abläuft.
[Refresh Zero-Byte Cache Files](#refresh-zero-byte-cache-files) | Bestimmt, wie eine Anforderung eines HTTP-Clients eines Cacheassets mit 0 Byte von den POPs verarbeitet wird.
[Set Cacheable Status Codes](#set-cacheable-status-codes) | Definiert die Gruppe von Statuscodes, die in zwischengespeicherten Inhalten resultieren können.
[Stale Content Delivery on Error](#stale-content-delivery-on-error) | Bestimmt, ob abgelaufene zwischengespeicherte Inhalte übermittelt werden, wenn während der erneuten Überprüfung des Caches ein Fehler auftritt oder der angeforderte Inhalt vom Kundenursprungsserver abgerufen wird.
[Stale While Revalidate](#stale-while-revalidate) | Verbessert die Leistung, indem den POPs erlaubt wird, dem Anfordernden einen veralteten Client bereitzustellen, während die erneute Überprüfung erfolgt.

## <a name="comment-feature"></a>Kommentarfeatures

Diese Funktion dient zum Bereitstellen zusätzlicher Informationen innerhalb einer Regel.

NAME | Zweck
-----|--------
[Comment](#comment) | Erlaubt das Hinzufügen eines Hinweises in einer Regel.

## <a name="header-features"></a>Headerfeatures

Diese Features dienen zum Hinzufügen, Ändern oder Löschen von Headern in der Anforderung oder Antwort.

NAME | Zweck
-----|--------
[Age Response Header](#age-response-header) | Legt fest, ob ein „Age Response Header“ in die Antwort an die anfordernde Person einbezogen wird.
[Debug Cache Response Headers](#debug-cache-response-headers) | Bestimmt, ob eine Antwort den „X-EC-Debug Response Header“ enthalten kann, der Informationen zur Cacherichtlinie für das angeforderte Objekt enthält.
[Modify Client Request Header](#modify-client-request-header) | Dient zum Überschreiben, Anfügen oder Löschen eines Headers in einer Anforderung.
[Modify Client Response Header](#modify-client-response-header) | Dient zum Überschreiben, Anfügen oder Löschen eines Headers in einer Antwort.
[Set Client IP Custom Header](#set-client-ip-custom-header) | Erlaubt das Hinzufügen der IP-Adresse des anfordernden Clients zur Anforderung als benutzerdefinierter Anforderungsheader.

## <a name="logging-features"></a>Protokollierungsfeatures

Diese Features dienen zum Anpassen der Daten, die in unformatierten Protokolldateien gespeichert sind.

NAME | Zweck
-----|--------
[Custom Log Field 1](#custom-log-field-1) | Bestimmt das Format und den Inhalt, das/der dem benutzerdefinierten Protokollfeld in einer unformatierten Protokolldatei zugewiesen wird.
[Log Query String](#log-query-string) | Bestimmt, ob eine Abfragezeichenfolge zusammen mit der URL in Zugriffsprotokollen gespeichert wird.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled


### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Ursprungsfeatures

Diese Funktionen dienen zum Steuern, wie das CDN mit einem Ursprungsserver kommuniziert.

NAME | Zweck
-----|--------
[Maximum Keep-Alive Requests](#maximum-keep-alive-requests) | Definiert die maximale Anzahl von Anforderungen für eine „Keep Alive“-Verbindung, bevor diese geschlossen wird.
[Proxy Special Headers](#proxy-special-headers) | Definiert die CDN-spezifischen Anforderungsheader, die von einem POP an einen Ursprungsserver weitergeleitet werden.

## <a name="specialty-features"></a>Spezielle Features

Diese Features bieten erweiterte Funktionalität für erfahrene Benutzer.

NAME | Zweck
-----|--------
[Cacheable HTTP Methods](#cacheable-http-methods) | Bestimmt zusätzliche HTTP-Methoden, die im Netzwerk zwischengespeichert werden können.
[Cacheable Request Body Size](#cacheable-request-body-size) | Definiert den Schwellenwert zum Bestimmen, ob eine POST-Antwort zwischengespeichert werden kann.
[User Variable](#user-variable) | Nur zur internen Verwendung.

## <a name="url-features"></a>URL-Funktionen

Diese Funktionen ermöglichen, dass eine Anforderung zu einer anderen URL umgeleitet bzw. in eine andere URL umgeschrieben wird.

NAME | Zweck
-----|--------
[Follow Redirects](#follow-redirects) | Bestimmt, ob Anforderungen zum Hostnamen umgeleitet werden können, der im „Location“-Header definiert ist, der vom Kundenursprungsserver zurückgegeben wird.
[URL Redirect](#url-redirect) | Leitet Anfragen über den „Location“-Header weiter.
[URL Rewrite](#url-rewrite)  | Schreibt die Anforderungs-URL um.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Referenz zu den Features der Regel-Engine für Azure CDN Premium von Verizon

---

### <a name="age-response-header"></a>Age Response Header

**Zweck:** Legt fest, ob ein „Age Response Header“ in die Antwort an die anfordernde Person einbezogen wird.

Wert|Ergebnis
--|--
Enabled | Der „Age Response Header“ wird in die Antwort einbezogen, die an die anfordernde Person gesendet wird.
Deaktiviert | Der „Age Response Header“ wird aus der Antwort ausgeschlossen, die an die anfordernde Person gesendet wird.

**Standardverhalten**: Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Bandwidth Parameters

**Zweck:** Bestimmt, ob Parameter zur Bandbreitenbeschränkung (beispielsweise „ec_rate“ und „ec_prebuf“) aktiv sind.

Parameter zur Bandbreiteneinschränkung legen fest, ob die Datenübertragungsrate für eine Clientanforderung auf eine benutzerdefinierte Rate beschränkt wird.

Wert|Ergebnis
--|--
Enabled|Erlaubt den POPs, die Anforderungen der Bandbreitendrosselung zu berücksichtigen.
Deaktiviert|Veranlasst die POPs, Parameter zur Bandbreitendrosselung zu ignorieren. Der angeforderte Inhalt wird normal (also ohne Bandbreiteneinschränkung) verarbeitet.

**Standardverhalten:** Aktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Bandwidth Throttling

**Zweck:** Drosselt die Bandbreite für die Antwort, die von den POPs bereitgestellt wird.

Beide der folgenden Optionen müssen definiert werden, um die Bandbreitendrosselung ordnungsgemäß einzurichten.

Option|BESCHREIBUNG
--|--
Kbytes per second|Legen Sie diese Option auf die maximale Bandbreite (KB pro Sekunde) fest, die zum Übermitteln der Antwort verwendet werden kann.
Prebuf seconds|Legen Sie diese Option auf die Anzahl von Sekunden fest, die die POPs warten sollen, bis die Bandbreite gedrosselt wird. Der Zweck dieses Zeitraums mit uneingeschränkter Bandbreite besteht darin zu verhindern, dass bei einem Media Player aufgrund der Bandbreitendrosselung die Wiedergabe stottert oder Pufferprobleme auftreten.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Bypass Cache

**Zweck:** Bestimmt, ob die Anforderung das Zwischenspeichern umgehen soll.

Wert|Ergebnis
--|--
Enabled|Hiermit gehen alle Anforderungen auch dann an den Ursprungsserver, wenn der Inhalt vorher auf POPs zwischengespeichert wurde.
Deaktiviert|Hiermit werden Assets entsprechend der in den Antwortheadern definierten Cacherichtlinie von den POPs zwischengespeichert.

**Standardverhalten:**

- **HTTP Large:** Deaktiviert

<!---
- **ADN:** Enabled

--->

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Cacheable HTTP Methods

**Zweck:** Bestimmt zusätzliche HTTP-Methoden, die im Netzwerk zwischengespeichert werden können.

Wichtige Informationen:

- Dieses Feature geht davon aus, dass die GET-Antworten immer zwischengespeichert werden sollen. Demzufolge sollte die GET HTTP-Methode beim Festlegen dieses Features nicht einbezogen werden.
- Dieses Feature unterstützt nur die POST HTTP-Methode. Aktivieren Sie die Zwischenspeicherung von POST-Antworten, indem Sie dieses Feature auf `POST` festlegen.
- Standardmäßig werden nur Anforderungen zwischengespeichert, deren Text kleiner als 14 KB ist. Verwenden Sie das Feature „Cacheable Request Body Size“, um die maximale Größe des Anforderungstexts festzulegen.

**Standardverhalten:** Nur GET-Antworten werden zwischengespeichert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Cacheable Request Body Size

**Zweck:** Definiert den Schwellenwert zum Bestimmen, ob eine POST-Antwort zwischengespeichert werden kann.

Dieser Schwellenwert wird durch die Angabe einer maximalen Größe für den Anforderungstext festgelegt. Anforderungen, die einen größeren Anforderungstext enthalten, werden nicht zwischengespeichert.

Wichtige Informationen:

- Dieses Feature ist nur anwendbar, wenn POST-Antworten für die Zwischenspeicherung qualifiziert sind. Verwenden Sie das Feature „Cacheable HTTP Methods“, um die Zwischenspeicherung von POST-Anforderungen zu aktivieren.
- Der Anforderungstext wird für Folgendes berücksichtigt:
    - x-www-form-urlencoded values
    - Sicherstellen eines eindeutigen Cacheschlüssels
- Das Definieren einer großen maximalen Anforderungstextgröße kann die Leistung bei der Datenübermittlung beeinträchtigen.
    - **Empfohlener Wert:** 14 KB
    - **Mindestwert:** 1 KB

**Standardverhalten:** 14 KB

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Cache-Control Header Treatment

**Zweck:** Steuert die Generierung von `Cache-Control`-Headern durch den POP, wenn das Feature „External Max-Age“ aktiv ist.

Die einfachste Möglichkeit zum Erreichen dieser Art der Konfiguration besteht darin, die Features „External Max-Age“ und „Cache-Control Header Treatment“ in derselben Anweisung zu verwenden.

Wert|Ergebnis
--|--
Überschreiben|Stellt sicher, dass die folgenden Aktionen ausgeführt werden:<br/> – Überschreibt den vom Ursprungsserver generierten `Cache-Control`-Header. <br/>– Fügt der Antwort den `Cache-Control`-Header hinzu, der durch das Feature „External Max-Age“ generiert wird.
Pass Through|Stellt sicher, dass der durch das Feature „External Max-Age“ generierte `Cache-Control`-Header der Antwort niemals hinzugefügt wird. <br/> Falls der Ursprungsserver einen `Cache-Control`-Header generiert, wird dieser an den Endbenutzer weitergegeben. <br/> Wenn der Ursprungsserver keinen `Cache-Control`-Header generiert, kann diese Option bewirken, dass der Antwortheader keinen `Cache-Control`-Header enthält.
Add if Missing|Wenn vom Ursprungsserver kein `Cache-Control`-Header empfangen wurde, fügt diese Option den `Cache-Control`-Header hinzu, der vom Feature „External Max-Age“ generiert wird. Diese Option ist hilfreich, um sicherzustellen, dass allen Objekten ein `Cache-Control`-Header zugewiesen wird.
Remove (Entfernen)| Diese Option stellt sicher, dass in der Headerantwort kein `Cache-Control`-Header enthalten ist. Wenn ein `Cache-Control`-Header bereits zugewiesen wurde, wird er aus der Headerantwort entfernt.

**Standardverhalten:** Überschreiben.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Cache-Key Query String

**Zweck:** Bestimmt, ob der Cacheschlüssel Abfragezeichenfolgenparameter, die einer Anforderung zugeordnet sind, ein- oder ausschließt.

Wichtige Informationen:

- Geben Sie mindestens einen Namen von Abfragezeichenfolgenparametern an, und trennen Sie mehrere Parameternamen durch ein einzelnes Leerzeichen.
- Dieses Feature legt fest, ob Abfragezeichenfolgenparameter in den Cacheschlüssel einbezogen oder davon ausgeschlossen werden. In der nachfolgenden Tabelle werden zu jeder Option zusätzliche Informationen bereitgestellt.

Type|BESCHREIBUNG
--|--
 Include|  Gibt an, dass jeder angegebene Parameter in den Cacheschlüssel einbezogen werden soll. Ein eindeutiger Cacheschlüssel wird für jede Anforderung generiert, die einen eindeutigen Wert für einen in diesem Feature definierten Abfragezeichenfolgenparameter enthält.
 Include All  |Gibt an, dass ein eindeutiger Cacheschlüssel für jede Anforderung an ein Objekt erstellt wird, die eine eindeutige Abfragezeichenfolge enthält. Von dieser Art der Konfiguration wird in der Regel abgeraten, da sie zu einem geringen Prozentsatz an Cachetreffern führen könnte. Eine geringe Anzahl von Cachetreffern erhöht die Auslastung des Ursprungsservers, da er mehr Anforderungen verarbeiten muss. Diese Konfiguration dupliziert das Zwischenspeicherungsverhalten, das auf der Seite „Query-String Caching“ als „unique-cache“ bezeichnet wird.
 Exclude | Gibt an, dass nur die angegebenen Parameter vom Cacheschlüssel ausgeschlossen werden. Alle anderen Abfragezeichenfolgenparameter werden in den Cacheschlüssel einbezogen.
 Exclude All  |Gibt an, dass alle Abfragezeichenfolgenparameter aus dem Cacheschlüssel ausgeschlossen werden. Diese Konfiguration dupliziert das standardmäßige Zwischenspeicherungsverhalten, das auf der Seite „Query-String Caching“ als „standard-cache“ bezeichnet wird.  

Durch die Regel-Engine können Sie die Implementierung der Zwischenspeicherung von Abfragezeichenfolgen anpassen. Beispielsweise können Sie angeben, dass die Zwischenspeicherung von Abfragezeichenfolgen nur für bestimmte Standorte oder Dateitypen ausgeführt wird.

Um das als „no-cache“ bezeichnete Verhalten für das Zwischenspeichern von Abfragezeichenfolgen auf der Seite „Query-String Caching“ zu duplizieren, erstellen Sie eine Regel, die eine Übereinstimmungsbedingung „URL Query Wildcard“ und ein Feature namens „Bypass Cache“ enthält. Legen Sie die Übereinstimmungsbedingung „URL Query Wildcard“ auf ein Sternchen (*) fest.

>[!IMPORTANT]
> Wenn die Tokenberechtigung für einen beliebigen Pfad für dieses Konto aktiviert ist, ist der Standardcachemodus der einzige Modus, der für die Zwischenspeicherung von Abfragezeichenfolgen verwendet werden kann. Weitere Informationen finden Sie unter [Steuern des Azure CDN-Zwischenspeicherverhaltens mit Abfragezeichenfolgen](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Beispielszenarien

Das folgende Verwendungsbeispiel für dieses Feature umfasst eine Beispielanforderung und den Standardcacheschlüssel:

- **Beispielanforderung:** http://wpc.0001.&lt;Domäne&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=DE&userid=01
- **Standardcacheschlüssel:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Include

Konfigurationsbeispiel:

- **Typ:** Include
- **Parameter:** language

Durch diese Art der Konfiguration wird der folgende Cacheschlüssel für den Abfragezeichenfolgenparameter generiert:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Include All

Konfigurationsbeispiel:

- **Typ:** Include All

Durch diese Art der Konfiguration wird der folgende Cacheschlüssel für den Abfragezeichenfolgenparameter generiert:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Konfigurationsbeispiel:

- **Typ:** Exclude
- **Parameter:** sessionid userid

Durch diese Art der Konfiguration wird der folgende Cacheschlüssel für den Abfragezeichenfolgenparameter generiert:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Exclude All

Konfigurationsbeispiel:

- **Typ:** Exclude All

Durch diese Art der Konfiguration wird der folgende Cacheschlüssel für den Abfragezeichenfolgenparameter generiert:

    /800001/Origin/folder/asset.htm

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Cache-Key Rewrite

**Zweck:** Schreibt den einer Anforderung zugeordneten Cacheschlüssel neu.

Ein Cacheschlüssel ist der relative Pfad, der ein Asset zum Zweck der Zwischenspeicherung identifiziert. Die Server suchen also in dem Pfad, der durch den zugehörigen Cacheschlüssel definiert wird, nach einer zwischengespeicherten Version eines Objekts.

Konfigurieren Sie dieses Feature durch Definieren der beiden folgenden Optionen:

Option|BESCHREIBUNG
--|--
Original Path| Definieren Sie den relativen Pfad für die Typen von Anforderungen, deren Cacheschlüssel umgeschrieben wird. Ein relativer Pfad kann durch Auswählen eines Basisursprungspfads und durch anschließendes Definieren eines Muster für reguläre Ausdrücke definiert werden.
New Path|Definieren Sie den relativen Pfad für den neuen Cacheschlüssel. Ein relativer Pfad kann durch Auswählen eines Basisursprungspfads und durch anschließendes Definieren eines Muster für reguläre Ausdrücke definiert werden. Dieser relative Pfad kann durch die Verwendung von [HTTP-Variablen](cdn-http-variables.md) dynamisch zusammengestellt werden.

**Standardverhalten:** Der Cacheschlüssel einer Anforderung wird durch den Anforderungs-URI festgelegt.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Comment

**Zweck:** Erlaubt das Hinzufügen eines Hinweises in einer Regel.

Ein Einsatzbereich für dieses Feature besteht darin, zusätzliche Informationen zum allgemeinen Zweck einer Regel oder den Grund bereitzustellen, aus dem der Regel eine bestimmte Übereinstimmungsbedingung hinzugefügt wurde.

Wichtige Informationen:

- Maximal 150 Zeichen können angegeben werden.
- Verwenden Sie ausschließlich alphanumerische Zeichen.
- Dieses Feature wirkt sich nicht auf das Verhalten der Regel aus. Es soll lediglich einen Bereich bereitstellen, in dem Sie Informationen zur zukünftigen Referenz oder als Hilfe bei der Problembehandlung für die Regel angeben können.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Complete Cache Fill

**Zweck:** Bestimmt, was passiert, wenn eine Anforderung in einem teilweisen Cachefehler auf einem POP resultiert.

Ein Teilcachefehler beschreibt den Cachestatus für ein Asset, das nicht vollständig auf einen POP heruntergeladen wurde. Wenn ein Asset nur teilweise auf einem POP zwischengespeichert wird, wird die nächste Anforderung für dieses Asset wieder an den Ursprungsserver weitergeleitet.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Ein Teilcachefehler tritt normalerweise auf, nachdem ein Benutzer einen Download abgebrochen hat, oder für Objekte, die ausschließlich über HTTP-Bereichsanforderungen angefordert werden. Dieses Feature eignet sich am besten für große Objekte, die von den Benutzern in der Regel nicht komplett heruntergeladen werden (beispielsweise Videos). Dieses Feature ist daher auf der HTTP Large-Plattform standardmäßig aktiviert. Auf allen anderen Plattformen ist sie deaktiviert.

Behalten Sie die Standardkonfiguration für die HTTP Large-Plattform bei, weil sie die Last auf Ihrem Kundenursprungsserver verringert und die Geschwindigkeit erhöht, mit der Ihre Kunden Ihre Inhalte herunterladen.

Wert|Ergebnis
--|--
Enabled|Stellt das Standardverhalten wieder her. Standardmäßig wird der POP gezwungen, einen Hintergrundabruf des Assets vom Ursprungsserver zu initiieren. Anschließend befindet sich das Asset im lokalen Cache des POP.
Deaktiviert|Verhindert, dass ein POP einen Hintergrundabruf des Assets ausführt. Das Ergebnis: Die nächste Anforderung für das Asset aus der betreffenden Region führt dazu, dass ein POP es vom Kundenursprungsserver anfordert.

**Standardverhalten:** Aktiviert.

#### <a name="compatibility"></a>Kompatibilität

Aufgrund der Art und Weise, in welcher Cacheeinstellungen nachverfolgt werden, kann dieses Feature nicht den folgenden Übereinstimmungsbedingungen zugeordnet sein:

- AS Number
- Client IP Address
- Cookie Parameter
- Cookie Parameter Regex
- Country
- Gerät
- Microsoft Edge-CNAME
- Referring Domain
- Request Header Literal
- Request Header Regex
- Request Header Wildcard
- Request Method
- Request Scheme
- URL Query Literal
- URL Query Regex
- URL Query Wildcard
- URL Query Parameter

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Compress File Types

**Zweck:** Definiert die Dateiformate für die Dateien, die auf dem Server komprimiert werden.

Ein Dateiformat kann anhand seines Internetmedientyps (beispielsweise „Content-Type“) angegeben werden. Der Internetmedientyp entspricht plattformunabhängigen Metadaten, die den Servern das Identifizieren des Dateiformats eines bestimmten Objekts ermöglichen. Eine Liste gängiger Internetmedientypen finden Sie unten.

Internetmedientyp|BESCHREIBUNG
--|--
text/plain|Nur-Text-Dateien
text/html| HTML-Dateien
text/css|Cascading Stylesheets (CSS)
application/x-javascript|JavaScript
application/javascript|JavaScript

Wichtige Informationen:

- Geben Sie mehrere Internetmedientypen an, indem Sie die einzelnen Typen durch ein einzelnes Leerzeichen voneinander trennen.
- Durch dieses Feature werden nur Objekte komprimiert, die weniger als 1 MB groß sind. Größere Objekte werden durch die Server nicht komprimiert.
- Bestimmte Inhaltstypen wie Bild-, Video- und Audiomedienobjekte (beispielsweise JPG, MP3 und MP4) sind bereits komprimiert. Da die Dateigröße durch eine weitere Komprimierung dieser Objekttypen nicht signifikant reduziert wird, wird davon abgeraten, die Komprimierung für diese zu aktivieren.
- Platzhalterzeichen, wie z.B. Sternchen, werden nicht unterstützt.
- Bevor Sie dieses Feature einer Regel hinzufügen, vergewissern Sie sich, dass die Option „Compression Disabled“ auf der Seite „Compression“ für die Plattform festgelegt ist, auf die diese Regel angewendet werden soll.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Custom Log Field 1

**Zweck:** Bestimmt das Format und den Inhalt, das/der dem benutzerdefinierten Protokollfeld in einer unformatierten Protokolldatei zugewiesen wird.

Dieses benutzerdefinierte Feld ermöglicht das Festlegen, welche Anforderungs- und Antwortheaderwerte in Ihren Protokolldateien gespeichert werden.

Standardmäßig heißt das benutzerdefinierte Protokollfeld „x-ec_custom-1“. Der Name dieses Felds kann auf der Seite „Raw Log Settings“ angepasst werden.

Das Format zum Angeben der Anforderungs- und Antwortheader ist folgendermaßen definiert:

Headertyp|Format|Beispiele
-|-|-
Anforderungsheader|%{[RequestHeader]()}[i]() | %{Accept-Encoding}i <br/> {Referrer}i <br/> %{Authorization}i
Antwortheader|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Wichtige Informationen:

- Ein benutzerdefiniertes Protokollfeld kann eine beliebige Kombination aus Headerfeldern und Nur-Text enthalten.
- Gültige Zeichen für dieses Feld sind: alphanumerische Zeichen (0–9, a–z und A–Z), Gedankenstriche, Doppelpunkte, Semikolons, Apostrophe, Kommas, Punkte, Unterstriche, Gleichheitszeichen, runde Klammern, eckige Klammern und Leerzeichen. Das Prozentzeichen und geschweifte Klammern sind nur zulässig, wenn sie zur Angabe eines Headerfelds verwendet werden.
- Die Schreibweise für die einzelnen angegebenen Headerfelder muss mit dem gewünschten Anforderungs-/Antwortheadernamen übereinstimmen.
- Wenn Sie mehrere Header angeben möchten, verwenden Sie zur Angabe der einzelnen Header eine Trennlinie. Beispielsweise können Sie für jeden Header eine Abkürzung verwenden:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**Standardwert:**  -

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Debug Cache Response Headers

**Zweck:** Legt fest, ob eine Antwort [X-EC-Debug-Antwortheader](cdn-http-debug-headers.md) mit Informationen zur Cacherichtlinie für das angeforderte Objekt enthalten kann.

„Debug Cache Response Headers“ werden in die Antwort einbezogen, wenn die folgenden beiden Bedingungen erfüllt sind:

- Das Feature für Debugcache-Antwortheader wurde für die angegebene Anforderung aktiviert.
- Die angegebene Anforderung definiert den Satz der Debugcache-Antwortheader, die in die Antwort einbezogen werden.

Zum Anfordern von Debugcache-Antwortheadern müssen der folgende Header und die angegebenen Anweisungen in die Anforderung aufgenommen werden:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Beispiel:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Wert|Ergebnis
-|-
Enabled|Anforderungen für „Debug Cache Response Headers“ geben eine Antwort zurück, die den X-EC-Debug-Header enthält.
Deaktiviert|Der „X-EC-Debug Response Header“ wird aus der Antwort ausgeschlossen.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Default Internal Max-Age

**Zweck:** Bestimmt das max-age-Standardintervall für die erneute Überprüfung des Caches von POP und Ursprungsserver. Mit anderen Worten: Dies ist die Zeitspanne, die verstreichen muss, bevor ein POP prüft, ob ein zwischengespeichertes Asset mit dem auf dem Ursprungsserver gespeicherten Asset übereinstimmt.

Wichtige Informationen:

- Diese Aktion findet nur für Antworten von einem Ursprungsserver statt, der im `Cache-Control`- oder `Expires`-Header keinen max-age-Hinweis zugewiesen hat.
- Diese Aktion findet nicht für Medienobjekte statt, die als nicht zwischenspeicherbar eingestuft werden.
- Diese Aktion wirkt sich nicht auf erneute Cacheüberprüfungen vom Browser zum POP aus. Diese Arten von erneuten Überprüfungen werden durch den an den Browser gesendeten `Cache-Control`- oder `Expires`-Header festgelegt, der mit dem Feature „External Max-Age“ angepasst werden kann.
- Die Ergebnisse dieser Aktion haben keine erkennbaren Auswirkungen auf die Antwortheader und den von POPs für Ihren Inhalt zurückgegebenen Inhalt. Sie können sich jedoch möglicherweise auf die Menge an Datenverkehr für die erneute Überprüfung auswirken, der von den POPs an Ihren Ursprungsserver gesendet wird.
- Konfigurieren Sie dieses Feature folgendermaßen:
    - Wählen Sie den Statuscode aus, für den ein interner Standardwert für max-age angewendet werden kann.
    - Geben Sie einen ganzzahligen Wert an, und wählen Sie dann die gewünschte Zeiteinheit aus (beispielsweise Sekunden, Minuten oder Stunden). Dieser Wert definiert das interne Standardintervall für max-age.

- Durch Festlegen der Zeiteinheit auf „Off“ wird für Anforderungen, denen im `Cache-Control`- oder `Expires`-Header kein max-age-Hinweis zugewiesen wurde, ein internes max-age-Standardintervall von 7 Tagen festgelegt.

**Standardwert:** 7 Tage

#### <a name="compatibility"></a>Kompatibilität

Aufgrund der Art und Weise, in welcher Cacheeinstellungen nachverfolgt werden, kann dieses Feature nicht den folgenden Übereinstimmungsbedingungen zugeordnet sein:
- AS Number
- Client IP Address
- Cookie Parameter
- Cookie Parameter Regex
- Country
- Gerät
- Edge Cname
- Referring Domain
- Request Header Literal
- Request Header Regex
- Request Header Wildcard
- Request Method
- Request Scheme
- URL Query Literal
- URL Query Regex
- URL Query Wildcard
- URL Query Parameter

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Deny Access (403)

**Zweck:** Bestimmt, ob alle Anfragen mit der Antwort „403 – Verboten“ abgelehnt werden.

Wert | Ergebnis
------|-------
Enabled| Hiermit werden alle Anforderungen, die die Übereinstimmungskriterien erfüllen, mit der Antwort „403 – Verboten“ abgelehnt.
Deaktiviert| Stellt das Standardverhalten wieder her. Standardmäßig wird dem Ursprungsserver gestattet, den zurückzugebenden Antworttyp festzulegen.

**Standardverhalten**: Deaktiviert

> [!TIP]
   > Eine Verwendungsmöglichkeit für dieses Feature besteht darin, es einer Anforderungsheader-Übereinstimmungsbedingung zuzuordnen, um den Zugriff auf HTTP-Verweiser zu blockieren, die Inlinelinks auf Ihre Inhalte verwenden.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Expires Header Treatment

**Zweck:** Steuert die Generierung von `Expires`-Headern durch einen POP, wenn das Feature „External Max-Age“ aktiv ist.

Die einfachste Möglichkeit zum Erreichen dieser Art der Konfiguration besteht darin, die Features „External Max-Age“ und „Expires Header Treatment“ in derselben Anweisung zu verwenden.

Wert|Ergebnis
--|--
Überschreiben|Stellt sicher, dass die folgenden Aktionen ausgeführt werden:<br/>– Überschreibt den vom Ursprungsserver generierten `Expires`-Header.<br/>– Fügt der Antwort den `Expires`-Header hinzu, der durch das Feature „External Max-Age“ generiert wird.
Pass Through|Stellt sicher, dass der durch das Feature „External Max-Age“ generierte `Expires`-Header der Antwort niemals hinzugefügt wird. <br/> Falls der Ursprungsserver einen `Expires`-Header generiert, wird dieser an den Endbenutzer weitergegeben. <br/>Wenn der Ursprungsserver keinen `Expires`-Header generiert, kann diese Option bewirken, dass der Antwortheader keinen `Expires`-Header enthält.
Add if Missing| Wenn vom Ursprungsserver kein `Expires`-Header empfangen wurde, fügt diese Option den `Expires`-Header hinzu, der vom Feature „External Max-Age“ generiert wird. Diese Option ist hilfreich, um sicherzustellen, dass allen Objekten ein `Expires`-Header zugewiesen wird.
Remove (Entfernen)| Stellt sicher, dass in der Headerantwort kein `Expires`-Header enthalten ist. Wenn ein `Expires`-Header bereits zugewiesen wurde, wird er aus der Headerantwort entfernt.

**Standardverhalten:** Überschreiben

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>External Max-Age

**Zweck:** Bestimmt das max-age-Intervall für die erneute Überprüfung des Caches von Browser und POP. Mit anderen Worten: Dies ist die Zeitspanne, die verstreichen muss, bevor ein Browser nach einer neuen Version eines Assets von einem POP suchen kann.

Durch Aktivieren dieses Features werden die `Cache-Control: max-age`- und `Expires`-Header von den POPs generiert und an den HTTP-Client gesendet. Standardmäßig werden die vom Ursprungsserver erstellten Header durch diese Header überschrieben. Allerdings können die Features „Cache-Control Header Treatment“ und „Expires Header Treatment“ verwendet werden, um dieses Verhalten zu ändern.

Wichtige Informationen:

- Diese Aktion wirkt sich nicht auf erneute Cacheüberprüfungen vom POP zum Ursprungsserver aus. Diese Arten von erneuten Überprüfungen werden durch die vom Ursprungsserver empfangenen `Cache-Control`-und `Expires`-Header festgelegt und können mit den Features „Default Internal Max-Age“ und „Force Internal Max-Age“ angepasst werden.
- Konfigurieren Sie dieses Feature, indem Sie einen ganzzahligen Wert angeben und die gewünschte Zeiteinheit auswählen (beispielsweise Sekunden, Minuten oder Stunden).
- Wenn Sie dieses Feature auf einen negativen Wert festlegen, senden die POPs mit jeder Antwort an den Browser den Wert `Cache-Control: no-cache` und eine `Expires`-Zeit, die in der Vergangenheit liegt. Auch wenn ein HTTP-Client die Antwort nicht zwischenspeichert, können die POPs die Antwort vom Ursprungsserver trotz dieser Einstellung zwischenspeichern.
- Durch Festlegen der Zeiteinheit auf „Off“ wird dieses Feature deaktiviert. Die mit der Antwort des Ursprungsservers zwischengespeicherten `Cache-Control`- und `Expires`-Header werden an den Browser weitergegeben.

**Standardverhalten:** Aus

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Follow Redirects

**Zweck:** Bestimmt, ob Anforderungen zum Hostnamen umgeleitet werden können, der im „Location“-Header definiert ist, der vom Kundenursprungsserver zurückgegeben wird.

Wichtige Informationen:

- Anforderungen können nur an Edge-CNAMEs umgeleitet werden, die derselben Plattform entsprechen.

Wert|Ergebnis
-|-
Enabled|Anforderungen können umgeleitet werden.
Deaktiviert|Anforderungen werden nicht umgeleitet.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Force Internal Max-Age

**Zweck:** Bestimmt das max-age-Intervall für die erneute Überprüfung des Caches von POP und Ursprungsserver. Mit anderen Worten: Dies ist die Zeitspanne, die verstreichen muss, bevor ein POP prüfen kann, ob ein zwischengespeichertes Asset mit dem auf dem Ursprungsserver gespeicherten Asset übereinstimmt.

Wichtige Informationen:

- Durch dieses Feature wird das max-age-Intervall überschrieben, das im von einem Ursprungsserver generierten `Cache-Control`- oder `Expires`-Header definiert ist.
- Dieses Feature wirkt sich nicht auf erneute Cacheüberprüfungen vom Browser zum POP aus. Diese Arten von erneuten Überprüfungen werden durch den an den Browser gesendeten `Cache-Control`- oder `Expires`-Header festgelegt.
- Dieses Feature hat keine erkennbare Auswirkungen auf die Antwort, die durch einen POP an die anfordernde Person übermittelt wird. Allerdings kann sie sich auf die Menge des Datenverkehrs auswirken, der für die erneute Überprüfung von den POPs an den Ursprungsserver gesendet wird.
- Konfigurieren Sie dieses Feature folgendermaßen:
    - Wählen Sie den Statuscode aus, für den ein interner max-age-Wert angewendet werden kann.
    - Geben Sie einen ganzzahligen Wert an, und wählen Sie die gewünschte Zeiteinheit aus (beispielsweise Sekunden, Minuten oder Stunden). Dieser Wert definiert das max-age-Intervall der Anforderung.

- Durch Festlegen der Zeiteinheit auf „Off“ wird dieses Feature deaktiviert. Ein internes max-age-Intervall wird den angeforderten Assets nicht zugewiesen. Wenn der ursprüngliche Header keine Anweisungen für die Zwischenspeicherung enthält, wird das Asset gemäß der aktiven Einstellung im Feature „Default Internal Max-Age“ zwischengespeichert.

**Standardverhalten:** Aus

#### <a name="compatibility"></a>Kompatibilität

Aufgrund der Art und Weise, in welcher Cacheeinstellungen nachverfolgt werden, kann dieses Feature nicht den folgenden Übereinstimmungsbedingungen zugeordnet sein:
- AS Number
- Client IP Address
- Cookie Parameter
- Cookie Parameter Regex
- Country
- Gerät
- Edge Cname
- Referring Domain
- Request Header Literal
- Request Header Regex
- Request Header Wildcard
- Request Method
- Request Scheme
- URL Query Literal
- URL Query Regex
- URL Query Wildcard
- URL Query Parameter

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H.264 Support (HTTP Progressive Download)

**Zweck:** Bestimmt die Typen von H.264-Dateiformaten, die zum Streamen von Inhalten verwendet werden können.

Wichtige Informationen:

- Definieren Sie einen durch Leerzeichen getrennten Satz mit zulässigen H.264-Dateierweiterungen in der Option „File Extensions“. Die Option „File Extensions“ setzt das Standardverhalten außer Kraft. Behalten Sie die MP4- und F4V-Unterstützung bei, indem Sie beim Festlegen dieser Option diese Dateierweiterungen einschließen.
- Die einzelnen Dateierweiterungen müssen jeweils mit einem Punkt angegeben werden (beispielsweise _.mp4_ oder _.f4v_).

**Standardverhalten:** HTTP Progressive Download unterstützt standardmäßig MP4- und F4V-Medien.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Honor No-Cache Request

**Zweck:** Bestimmt, ob „No-Cache“-Anforderungen eines HTTP-Clients an den Ursprungsserver weitergeleitet werden.

Eine no-cache-Anforderung erfolgt, wenn der HTTP-Client in der HTTP-Anforderung einen `Cache-Control: no-cache`- und/oder `Pragma: no-cache`-Header sendet.

Wert|Ergebnis
--|--
Enabled|Ermöglicht das Weiterleiten von no-cache-Anforderungen eines HTTP-Clients an den Ursprungsserver. Der Ursprungsserver gibt die Antwortheader und den Text über den POP an den HTTP-Client zurück.
Deaktiviert|Stellt das Standardverhalten wieder her. Standardmäßig wird verhindert, dass no-cache-Anforderungen an den Ursprungsserver weitergeleitet werden.

Für den gesamten Produktionsdatenverkehr wird dringend empfohlen, dieses Feature in seinem Standardzustand (deaktiviert) zu belassen. Andernfalls werden die Ursprungsserver nicht vor Endbenutzern, die beim Aktualisieren von Webseiten versehentlich viele no-cache-Anforderungen auslösen können, oder vor den zahlreichen beliebten Media Playern geschützt, die so codiert sind, dass bei jeder Videoanforderung ein no-cache-Header mitgesendet wird. Trotzdem kann es von Vorteil sein, diese Funktion auf bestimmte nicht produktive Staging- oder Testverzeichnisse anzuwenden, damit neuer Inhalt bei Bedarf vom Ursprungsserver abgerufen werden kann.

Der Cachestatus, der für eine Anforderung gemeldet wird, die aufgrund dieses Features an einen Ursprungsserver weitergeleitet werden kann, lautet `TCP_Client_Refresh_Miss`. Der Cachestatusbericht, der im Kernberichtsmodul verfügbar ist, stellt Statistikinformationen nach Cachestatus bereit. Anhand dieses Berichts können Sie die Anzahl und den Prozentsatz von Anforderungen nachverfolgen, die aufgrund dieses Features an einen Ursprungsserver weitergeleitet werden.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignore Origin No-Cache

**Zweck:** Legt fest, ob das CDN die folgenden Anweisungen ignoriert, die von einem Ursprungsserver bereitgestellt werden:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Wichtige Informationen:

- Konfigurieren Sie dieses Feature durch die Definition einer durch Leerzeichen getrennten Liste von Statuscodes, für die die oben genannten Anweisungen ignoriert werden sollen.
- Der Satz gültiger Statuscodes für dieses Feature ist: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 und 505.
- Deaktivieren Sie diese Funktion, indem Sie sie auf einen leeren Wert festlegen.

**Standardverhalten:** Standardmäßig werden die oben genannten Anweisungen berücksichtigt.

#### <a name="compatibility"></a>Kompatibilität

Aufgrund der Art und Weise, in welcher Cacheeinstellungen nachverfolgt werden, kann dieses Feature nicht den folgenden Übereinstimmungsbedingungen zugeordnet sein:
- AS Number
- Client IP Address
- Cookie Parameter
- Cookie Parameter Regex
- Country
- Gerät
- Edge Cname
- Referring Domain
- Request Header Literal
- Request Header Regex
- Request Header Wildcard
- Request Method
- Request Scheme
- URL Query Literal
- URL Query Regex
- URL Query Wildcard
- URL Query Parameter

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignore Unsatisfiable Ranges

**Zweck:** Bestimmt die Antwort, die an Clients zurückgegeben wird, wenn eine Anforderung den Statuscode „416 – Angeforderter Bereich nicht erfüllbar“ generiert.

Standardmäßig wird dieser Statuscode zurückgegeben, wenn die angegebene byte-range-Anforderung durch einen POP nicht erfüllt werden kann und kein If-Range-Anforderungsheaderfeld angegeben wurde.

Wert|Ergebnis
-|-
Enabled|Verhindert, dass die POPs eine ungültige byte-range-Anforderung mit dem Statuscode „416 – Angeforderter Bereich nicht erfüllbar“ beantwortet. Stattdessen übermitteln die Server das angeforderte Objekt und geben „200 – OK“ an den Client zurück.
Deaktiviert|Stellt das Standardverhalten wieder her. Standardmäßig wird der Statuscode „416 – Angeforderter Bereich nicht erfüllbar“ berücksichtigt.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Internal Max-Stale

**Zweck:** Steuert, wie lange nach Überschreiten der normalen Ablaufzeit ein zwischengespeichertes Asset von einem POP bereitgestellt werden kann, wenn der POP das zwischengespeicherte Asset nicht im Abgleich mit dem Ursprungsserver erneut überprüfen kann.

Normalerweise sendet der POP bei Ablauf der max-age-Zeit eines Assets eine Anforderung zur erneuten Überprüfung an den Ursprungsserver. Der Ursprungsserver antwortet entweder mit „304 – Nicht geändert“, um dem POP eine neue Lease für das zwischengespeicherte Asset zu geben, oder mit „200 – OK“, um dem POP eine aktualisierte Version des zwischengespeicherten Assets bereitzustellen.

Wenn der POP bei dem Versuch einer solchen erneuten Überprüfung keine Verbindung mit dem Ursprungsserver herstellen kann, steuert dieses Feature „Internal Max-Stale“, ob und wie lange der POP das jetzt veraltete Asset noch bedienen darf.

Beachten Sie, dass dieses Zeitintervall mit Ablauf des max-age-Werts des Assets beginnt, nicht bei Eintritt der fehlerhaften erneuten Überprüfung. Daher entspricht der maximale Zeitraum, während dessen ein Asset ohne erfolgreiche erneute Überprüfung bedient werden kann, der Zeitspanne, die durch die Kombination von max-age und max-stale angegeben wird. Wenn ein Objekt beispielsweise um 9:00 Uhr mit einem max-age-Wert von 30 Minuten und einem max-stale-Wert von 15 Minuten zwischengespeichert wurde, führt ein fehlerhafter Versuch zur erneuten Überprüfung um 9:44 Uhr dazu, dass ein Endbenutzer das veraltete zwischengespeicherte Objekt erhält, während ein fehlerhafter Versuch zur erneuten Überprüfung um 9:46 Uhr dazu führt, dass der Endbenutzer den Statuscode „504 – Gatewaytimeout“ empfängt.

Jeder für dieses Feature konfigurierte Wert wird durch die `Cache-Control: must-revalidate`- oder `Cache-Control: proxy-revalidate`-Header ersetzt, die vom Ursprungsserver empfangen werden. Wenn einer dieser Header beim ersten Zwischenspeichern eines Assets vom Ursprungsserver empfangen wird, bedient der POP kein veraltetes zwischengespeichertes Asset. Wenn der POP in einem solchen Fall bei Ablauf des max-age-Intervalls für das Asset die erneute Überprüfung mit dem Ursprung nicht durchführen kann, gibt der POP einen Fehler vom Typ „504 – Gatewaytimeout“ zurück.

Wichtige Informationen:

- Konfigurieren Sie dieses Feature folgendermaßen:
    - Wählen Sie den Statuscode aus, für den ein max-stale-Wert angewendet werden kann.
    - Geben Sie einen ganzzahligen Wert an, und wählen Sie dann die gewünschte Zeiteinheit aus (beispielsweise Sekunden, Minuten oder Stunden). Dieser Wert definiert den internen max-stale-Wert, der angewendet wird.

- Durch Festlegen der Zeiteinheit auf „Off“ wird dieses Feature deaktiviert. Ein zwischengespeichertes Asset wird nicht über seine normale Ablaufzeit hinaus bedient.

**Standardverhalten:** Zwei Minuten

#### <a name="compatibility"></a>Kompatibilität

Aufgrund der Art und Weise, in welcher Cacheeinstellungen nachverfolgt werden, kann dieses Feature nicht den folgenden Übereinstimmungsbedingungen zugeordnet sein:
- AS Number
- Client IP Address
- Cookie Parameter
- Cookie Parameter Regex
- Country
- Gerät
- Edge Cname
- Referring Domain
- Request Header Literal
- Request Header Regex
- Request Header Wildcard
- Request Method
- Request Scheme
- URL Query Literal
- URL Query Regex
- URL Query Wildcard
- URL Query Parameter

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Log Query String

**Zweck:** Bestimmt, ob eine Abfragezeichenfolge zusammen mit der URL in Zugriffsprotokollen gespeichert wird.

Wert|Ergebnis
-|-
Enabled|Ermöglicht die Speicherung von Abfragezeichenfolgen bei der Aufzeichnung von URLs in einem Zugriffsprotokoll. Wenn eine URL keine Abfragezeichenfolge enthält, besitzt diese Option keine Auswirkungen.
Deaktiviert|Stellt das Standardverhalten wieder her. Standardmäßig werden Abfragezeichenfolgen bei der Aufzeichnung von URLs in einem Zugriffsprotokoll ignoriert.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximum Keep-Alive Requests

**Zweck:** Definiert die maximale Anzahl von Anforderungen für eine „Keep Alive“-Verbindung, bevor diese geschlossen wird.

Die maximale Anzahl von Anforderungen sollte nicht auf einen niedrigen Wert festgelegt werden, da dies zu Leistungseinbußen führen kann.

Wichtige Informationen:

- Geben Sie diesen Wert als eine ganze Zahl ein.
- Schließen Sie keine Kommas oder Punkte in den angegebenen Wert ein.

**Standardwert:** 10.000 Anforderungen

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Modify Client Request Header

**Zweck:** Jede Anforderung enthält einen Satz von Anforderungsheadern, die sie beschreiben. Diese Feature kann folgende Aktionen ausführen:

- Anfügen oder Überschreiben des Werts, der einem Anforderungsheader zugewiesen ist. Wenn der angegebene Anforderungsheader nicht vorhanden ist, wird er der Anforderung durch dieses Feature hinzugefügt.
- Löschen eines Anforderungsheaders aus der Anforderung.

Anforderungen, die an einen Ursprungsserver weitergeleitet werden, spiegeln die von diesem Feature vorgenommenen Änderungen wider.

Für einen Anforderungsheader kann eine der folgenden Aktionen ausgeführt werden:

Option|BESCHREIBUNG|Beispiel
-|-|-
Anfügen|Der angegebene Wert wird am Ende des vorhandenen Werts des Anforderungsheaders hinzugefügt.|**Wert des Anforderungsheaders (Client):**<br/>Wert1<br/>**Wert des Anforderungsheaders (Regel-Engine):**<br/>Wert2 <br/>**Neuer Wert des Anforderungsheaders:** <br/>Value1Value2
Überschreiben|Der Wert des Anforderungsheaders wird auf den angegebenen Wert festgelegt.|**Wert des Anforderungsheaders (Client):**<br/>Wert1<br/>**Wert des Anforderungsheaders (Regel-Engine):**<br/>Wert2<br/>**Neuer Wert des Anforderungsheaders:**<br/> Wert2 <br/>
Löschen|Löscht den angegebenen Anforderungsheader.|**Wert des Anforderungsheaders (Client):**<br/>Wert1<br/>**Konfiguration von „Modify Client Request Header“:**<br/>Der betreffende Anforderungsheader wird gelöscht.<br/>**Ergebnis:**<br/>Der angegebene Anforderungsheader wird nicht an den Ursprungsserver weitergeleitet.

Wichtige Informationen:

- Vergewissern Sie sich, dass der in der Option „Name“ angegebene Wert genau mit dem gewünschten Anforderungsheader übereinstimmt.
- Die Groß-/Kleinschreibung wird bei der Identifikation eines Headers nicht berücksichtigt. Beispielsweise können alle folgenden Variationen des `Cache-Control`-Headernamens zur Identifizierung verwendet werden:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Headernamen dürfen nur alphanumerische Zeichen, Bindestriche und Unterstriche enthalten.
- Durch das Löschen eines Headers wird verhindert, dass er von den POPs an einen Ursprungsserver weitergeleitet wird.
- Die folgenden Header sind reserviert und können nicht von diesem Feature geändert werden:
    - forwarded
    - host
    - via
    - Warnung
    - x-forwarded-for
    - Alle Headernamen, die mit „x-ec“ beginnen, sind reserviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Modify Client Response Header

Jede Antwort enthält einen Satz von Antwortheadern, die sie beschreiben. Diese Feature kann folgende Aktionen ausführen:

- Anfügen oder Überschreiben des Werts, der einem Antwortheader zugewiesen ist. Wenn der angegebene Antwortheader nicht vorhanden ist, wird er der Antwort durch dieses Feature hinzugefügt.
- Löschen eines Antwortheaders aus der Antwort.

Standardmäßig werden Werte für Antwortheader durch einen Ursprungsserver und durch die POPs definiert.

Für einen Antwortheader kann eine der folgenden Aktionen ausgeführt werden:

Option|BESCHREIBUNG|Beispiel
-|-|-
Anfügen|Der angegebene Wert wird am Ende des vorhandenen Werts des Antwortheaders hinzugefügt.|**Wert des Antwortheaders (Client):**<br />Wert1<br/>**Wert des Antwortheaders (Regel-Engine):**<br/>Wert2<br/>**Neuer Wert des Antwortheaders:**<br/>Value1Value2
Überschreiben|Der Wert des Antwortheaders wird auf den angegebenen Wert festgelegt.|**Wert des Antwortheaders (Client):**<br/>Wert1<br/>**Wert des Antwortheaders (Regel-Engine):**<br/>Wert2 <br/>**Neuer Wert des Antwortheaders:**<br/>Wert2 <br/>
Löschen|Löscht den angegebenen Antwortheader.|**Wert des Antwortheaders (Client):**<br/>Wert1<br/>**Konfiguration von „Modify Client Response Header“:**<br/>Der betreffende Antwortheader wird gelöscht.<br/>**Ergebnis:**<br/>Der angegebene Antwortheader wird nicht an die anfordernde Person weitergeleitet.

Wichtige Informationen:

- Vergewissern Sie sich, dass der in der Option „Name“ angegebene Wert genau mit dem gewünschten Antwortheader übereinstimmt.
- Die Groß-/Kleinschreibung wird bei der Identifikation eines Headers nicht berücksichtigt. Beispielsweise können alle folgenden Variationen des `Cache-Control`-Headernamens zur Identifizierung verwendet werden:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Durch das Löschen eines Headers wird dieser nicht an die anfordernde Person weitergeleitet.
- Die folgenden Header sind reserviert und können nicht von diesem Feature geändert werden:
    - accept-encoding
    - age
    - connection
    - content-encoding
    - Inhaltslänge
    - content-range
    - date
    - server
    - trailer
    - transfer-encoding
    - Upgrade
    - vary
    - via
    - Warnung
    - Alle Headernamen, die mit „x-ec“ beginnen, sind reserviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Partial Cache Sharing

**Zweck:** Bestimmt, ob eine Anforderung teilweise zwischengespeicherte Inhalte erstellen kann.

Dieser Teilcache kann dann verwendet werden, um neue Anforderungen für diese Inhalte zu erfüllen, bis der angeforderte Inhalt vollständig zwischengespeichert wird.

Wert|Ergebnis
-|-
Enabled|Anforderungen können teilweise zwischengespeicherte Inhalte erstellen.
Deaktiviert|Anforderungen können nur eine vollständig zwischengespeicherte Version des angeforderten Inhalts generieren.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Prevalidate Cached Content

**Zweck:** Bestimmt, ob zwischengespeicherte Inhalte für eine frühzeitige erneute Überprüfung in Frage kommen, ehe ihre Gültigkeitsdauer abläuft.

Definieren Sie die Zeitspanne vor Ablauf der Gültigkeitsdauer (TTL) der angeforderten Inhalte, während der sie für eine frühe erneute Überprüfung qualifiziert sind.

Wichtige Informationen:

- Wenn Sie „Off“ als Zeiteinheit auswählen, muss die erneute Überprüfung nach Ablauf der Gültigkeitsdauer des zwischengespeicherten Inhalts erfolgen. Die Zeit darf nicht angegeben werden und wird ignoriert.

**Standardverhalten:** Aus. Die erneute Überprüfung kann erst stattfinden, nachdem die Gültigkeitsdauer des zwischengespeicherten Inhalts abgelaufen ist.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Proxy Special Headers

**Zweck:** Definiert den Satz von [Verizon-spezifischen HTTP-Anforderungsheadern](cdn-verizon-http-headers.md), die von einem POP an einen Ursprungsserver weitergeleitet werden.

Wichtige Informationen:

- Jeder in diesem Feature definierte CDN-spezifische Anforderungsheader wird an einen Ursprungsserver weitergeleitet. Ausgeschlossene Header werden nicht weitergeleitet.
- Wenn Sie die Weiterleitung eines CDN-spezifischen Anforderungsheaders unterbinden möchten, entfernen Sie ihn aus der durch Leerzeichen getrennten Liste im Headerlistenfeld.

Die Standardliste enthält folgende HTTP-Header:
- Via
- X-Forwarded-For
- X-Forwarded-Proto
- X-Host
- X-Midgress
- X-Gateway-List
- X-EC-Name
- Host

**Standardverhalten:** Alle CDN-spezifischen Anforderungsheader werden an den Ursprungsserver weitergeleitet.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Refresh Zero-Byte Cache Files

**Zweck:** Bestimmt, wie eine Anforderung eines HTTP-Clients eines Cacheassets mit 0 Byte von den POPs verarbeitet wird.

Gültige Werte sind:

Wert|Ergebnis
--|--
Enabled|Führt dazu, dass der POP das Asset erneut vom Ursprungsserver abruft.
Deaktiviert|Stellt das Standardverhalten wieder her. Standardmäßig werden gültige Cacheassets auf Anforderung bedient.

Dieses Feature ist für eine korrekte Zwischenspeicherung und Inhaltsübermittlung nicht erforderlich, aber möglicherweise als Problemumgehung hilfreich. Dynamische Inhaltsgeneratoren auf Ursprungsservern können beispielsweise dazu führen, dass versehentlich 0-Byte-Antworten an die POPs gesendet werden. Diese Typen von Antworten werden in der Regel durch die POPs zwischengespeichert. Wenn Sie wissen, dass eine 0-Byte-Antwort nie eine gültige Antwort für solche Inhalte ist, kann dieses Feature verhindern, dass diese Assettypen an Ihre Clients ausgegeben werden.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Set Cacheable Status Codes

**Zweck:** Definiert die Gruppe von Statuscodes, die in zwischengespeicherten Inhalten resultieren können.

Standardmäßig ist das Zwischenspeichern nur für „200 – OK“-Antworten aktiviert.

Definieren Sie einen durch Leerzeichen getrennten Satz mit den gewünschten Statuscodes.

Wichtige Informationen:

- Aktivieren Sie das Feature „Ignore Origin-No-Cache“. Wenn dieses Feature nicht aktiviert ist, werden andere Antworten als „200 – OK“ möglicherweise nicht zwischengespeichert.
- Der Satz gültiger Statuscodes für dieses Feature ist: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 und 505.
- Das Feature kann nicht verwendet werden, um die Zwischenspeicherung für Antworten zu deaktivieren, die einen Statuscode „200 – OK“ generieren.

**Standardverhalten:** Die Zwischenspeicherung ist nur für Antworten aktiviert, die den Statuscode „200 – OK“ generieren.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Set Client IP Custom Header

**Zweck:** Fügt einen benutzerdefinierten Header hinzu, der den anfordernden Client anhand der IP-Adresse für die Anforderung identifiziert.

Die Headernamensoption definiert den Namen des benutzerdefinierten Anforderungsheaders, in dem die IP-Adresse des Clients gespeichert wird.

Anhand dieses Features kann ein Kundenursprungsserver Client-IP-Adressen über einen benutzerdefinierten Anforderungsheader ermitteln. Wenn die Anforderung aus dem Cache bedient wird, wird der Ursprungsserver nicht über die IP-Adresse des Clients informiert. Aus diesem Grund wird empfohlen, dieses Feature mit Objekten zu verwenden, die nicht zwischengespeichert sind.

Der angegebene Headername darf keinem der folgenden Namen entsprechen:

- Namen von Standardanforderungsheadern. Eine Liste der Standardheadernamen ist in [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) zu finden.
- Reservierte Headernamen:
    - forwarded-for
    - host
    - vary
    - via
    - Warnung
    - x-forwarded-for
    - Alle Headernamen, die mit „x-ec“ beginnen, sind reserviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Stale Content Delivery on Error

**Zweck:** Bestimmt, ob abgelaufene Cache-Inhalte übermittelt werden, wenn während der erneuten Überprüfung des Caches ein Fehler auftritt oder der angeforderte Inhalt vom Kundenursprungsserver abgerufen wird.

Wert|Ergebnis
-|-
Enabled|Veralteter Inhalt wird an die anfordernde Person ausgegeben, wenn während einer Verbindung mit einem Ursprungsserver ein Fehler auftritt.
Deaktiviert|Der Fehler des Ursprungsservers wird an die anfordernde Person weitergeleitet.

**Standardverhalten:** Deaktiviert

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Stale While Revalidate

**Zweck:** Verbessert die Leistung, indem den POPs erlaubt wird, veraltete Inhalte für die anfordernde Person bereitzustellen, während die erneute Überprüfung erfolgt.

Wichtige Informationen:

- Das Verhalten dieses Features hängt von der ausgewählten Zeiteinheit ab.
    - **Time Unit:** Geben Sie einen Zeitraum an, und wählen Sie eine Zeiteinheit aus (z. B. Sekunden, Minuten oder Stunden), um die Übermittlung veralteter Inhalte zu ermöglichen. Dieser Setuptyp ermöglicht dem CDN, die Zeitdauer zu verlängern, während der Inhalte übermittelt werden können, bevor eine Überprüfung gemäß der folgenden Formel erforderlich ist: **TTL** + **Stale While Revalidate Time**
    - **Off:** Wählen Sie „Off“ aus, um eine erneute Überprüfung anzufordern, bevor eine Anforderung von veralteten Inhalten verarbeitet werden darf.
        - Geben Sie keine Zeitspanne an, weil sie nicht anwendbar ist und ignoriert wird.

**Standardverhalten:** Aus. Eine erneute Überprüfung muss erfolgen, damit der angeforderte Inhalt bereitgestellt werden kann.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token Auth

**Zweck:** Bestimmt, ob die tokenbasierte Authentifizierung auf eine Anforderung angewendet wird.

Wenn die tokenbasierte Authentifizierung aktiviert ist, werden nur Anforderungen berücksichtigt, die ein verschlüsseltes Token bereitstellen und den Anforderungen dieses Tokens entsprechen.

Der Verschlüsselungsschlüssel, der zum Verschlüsseln und Entschlüsseln von Tokenwerten verwendet wird, wird durch die Optionen „Primary Key“ und „Backup Key“ auf der Seite „Token Auth“ festgelegt. Beachten Sie, dass Verschlüsselungsschlüssel plattformspezifisch sind.

**Standardverhalten:** Deaktiviert.

Dieses Feature hat Vorrang vor den meisten Features (mit Ausnahme von „URL Rewrite“).

Wert | Ergebnis
------|---------
Enabled | Schützt den angeforderten Inhalt durch tokenbasierte Authentifizierung. Nur Anforderungen von Clients, die ein gültiges Token bereitstellen und dessen Anforderungen erfüllen, werden berücksichtigt. FTP-Transaktionen werden von der tokenbasierten Authentifizierung ausgeschlossen.
Deaktiviert| Stellt das Standardverhalten wieder her. Standardmäßig wird Ihrer tokenbasierten Authentifizierungskonfiguration gestattet festzulegen, ob eine Anforderung gesichert wird.

#### <a name="compatibility"></a>Kompatibilität

Verwenden Sie „Token Auth“ nicht mit der Übereinstimmungsbedingung „Always“.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Token Auth Denial Code

**Zweck:** Bestimmt die Art der Antwort, die an einen Benutzer zurückgegeben wird, wenn eine Anforderung aufgrund der tokenbasierten Authentifizierung verweigert wird.

Die verfügbaren Antwortcodes sind in der folgenden Tabelle aufgeführt.

Antwortcode|Antwortname|BESCHREIBUNG
-------------|-------------|--------
301|Permanent verschoben|Dieser Statuscode leitet nicht autorisierte Benutzer auf die URL um, die im Adressheader angegeben ist.
302|Gefunden|Dieser Statuscode leitet nicht autorisierte Benutzer auf die URL um, die im Adressheader angegeben ist. Dieser Statuscode entspricht der Industriestandardmethode zum Ausführen einer Umleitung.
307|Temporäre Umleitung|Dieser Statuscode leitet nicht autorisierte Benutzer auf die URL um, die im Adressheader angegeben ist.
401|Nicht autorisiert|Durch Kombinieren dieses Statuscodes mit dem Antwortheader „WWW-Authenticate“ können Sie einen Benutzer zur Authentifizierung auffordern.
403|Verboten|Dies ist die standardmäßige Statusmeldung „403 – Verboten“, die einem nicht autorisierten Benutzer angezeigt wird, wenn er versucht, auf geschützte Inhalte zuzugreifen.
404|Datei nicht gefunden|Dieser Statuscode gibt an, dass der HTTP-Client mit dem Server kommunizieren konnte, der angeforderte Inhalt jedoch nicht gefunden wurde.

#### <a name="compatibility"></a>Kompatibilität

Verwenden Sie „Token Auth Denial Code“ nicht mit der Übereinstimmungsbedingung „Always“. Verwenden Sie stattdessen den Abschnitt **Custom Denial Handling** auf der Seite **Token Auth** des **Verwaltungsportals**. Weitere Informationen finden Sie unter [Schützen von Azure CDN-Assets mit Tokenauthentifizierung](cdn-token-auth.md).

#### <a name="url-redirection"></a>URL-Umleitung

Dieses Feature unterstützt die URL-Umleitung an eine benutzerdefinierte URL, wenn es für die Rückgabe eines 3xx-Statuscodes konfiguriert ist. Diese benutzerdefinierte URL kann angegeben werden, indem Sie die folgenden Schritte ausführen:

1. Wählen Sie einen 3xx-Antwortcode für das Feature „Token Auth Denial Code“.
2. Wählen Sie unter „Optional Header Name“ die Option „Location“ aus.
3. Legen Sie die Option „Optional Header Value“ auf die gewünschte URL fest.

Wenn für einen 3xx-Statuscode keine URL definiert wurde, wird dem Benutzer die Standardantwortseite für einen 3xx-Statuscode zurückgegeben.

Die URL-Umleitung gilt nur für 3xx-Antwortcodes.

Die Option „Optional Header Value“ unterstützt alphanumerische Zeichen, Anführungszeichen und Leerzeichen.

#### <a name="authentication"></a>Authentication

Dieses Feature unterstützt die Möglichkeit, den WWW-Authenticate-Header in die Antwort auf eine nicht autorisierte Anforderung für Inhalte einzuschließen, die durch die tokenbasierte Authentifizierung geschützt werden. Wenn der WWW-Authenticate-Header in Ihrer Konfiguration auf „basic“ festgelegt wurde, wird der nicht autorisierte Benutzer zur Eingabe von Kontoanmeldeinformationen aufgefordert.

Die oben genannte Konfiguration kann über die folgenden Schritte erreicht werden:

1. Wählen Sie „401“ als Antwortcode für das Feature „Token Auth Denial Code“.
2. Wählen Sie unter „Optional Header Name“ die Option „WWW-Authenticate“ aus.
3. Legen Sie die Option „Optional Header Value“ auf „basic“ fest.

Der WWW-Authenticate-Header gilt nur für 401-Antwortcodes.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Token Auth Ignore URL Case

**Zweck:** Bestimmt, ob bei von der tokenbasierten Authentifizierung durchgeführten URL-Vergleichen Groß-/Kleinschreibung unterschieden wird.

Folgende Parameter sind von diesem Feature betroffen:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Gültige Werte sind:

Wert|Ergebnis
---|----
Enabled|Bewirkt, dass der POP Groß- und Kleinschreibung beim Vergleichen von URLs für Parameter der tokenbasierten Authentifizierung ignoriert.
Deaktiviert|Stellt das Standardverhalten wieder her. Standardmäßig wird bei URL-Vergleichen für die Tokenauthentifizierung die Groß-/Kleinschreibung beachtet.

**Standardverhalten:** Deaktiviert.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Token Auth Parameter

**Zweck:** Bestimmt, ob der Abfragezeichenfolgen-Parameter der tokenbasierten Authentifizierung umbenannt werden soll.

Wichtige Informationen:

- Die Option „Value“ definiert den Namen des Abfragezeichenfolgenparameters, über den ein Token angegeben werden kann.
- Die Option „Value“ kann nicht auf „ec_token“ festgelegt werden.
- Vergewissern Sie sich, dass der in der Option „Value“ definierte Name nur gültige URL-Zeichen enthält.

Wert|Ergebnis
----|----
Enabled|Die Option „Value“ definiert den Namen des Abfragezeichenfolgenparameters, über den Token definiert werden.
Deaktiviert|Ein Token kann als ein nicht definierter Abfragezeichenfolgenparameter in der Anforderungs-URL angegeben werden.

**Standardverhalten:** Deaktiviert. Ein Token kann als ein nicht definierter Abfragezeichenfolgenparameter in der Anforderungs-URL angegeben werden.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL Redirect

**Zweck:** Leitet Anfragen über den „Location“-Header weiter.

Zur Konfiguration dieses Features müssen die folgenden Optionen festgelegt werden:

Option|BESCHREIBUNG
-|-
Code|Wählen Sie den Antwortcode aus, der an die anfordernde Person zurückgegeben wird.
Source & Pattern| Diese Einstellungen definieren ein Anforderungs-URI-Muster, das die Art der Anforderungen identifiziert, die umgeleitet werden können. Nur Anforderungen, deren URL beide der folgenden Kriterien erfüllt, werden umgeleitet: <br/> <br/> **Source (or content access point):** Wählen Sie einen relativen Pfad aus, der einen Ursprungsserver identifiziert. Dieser Pfad ist der Abschnitt _/XXXX/_ und Ihr Endpunktname. <br/><br/> **Source (pattern):** Ein Muster, das Anforderungen nach relativem Pfad identifiziert, muss definiert werden. Dieses Muster für reguläre Ausdrücke muss einen Pfad definieren, der direkt nach dem zuvor ausgewählten Inhaltszugriffspunkt gestartet wird (siehe oben). <br/> - Vergewissern Sie sich, dass die oben definierten URI-Kriterien der Anforderung („Source & Pattern“) nicht mit für diese Funktion definierten Übereinstimmungsbedingungen in Konflikt stehen. <br/> - Geben Sie ein Muster an. Andernfalls werden alle Zeichenfolgen abgeglichen.
Ziel| Definieren Sie die URL, zu der die oben genannten Anforderungen umgeleitet werden. <br/><br/> Stellen Sie diese URL unter Verwendung folgender Elemente dynamisch zusammen: <br/> - Muster für regulären Ausdruck <br/>- [HTTP-Variablen](cdn-http-variables.md) <br/><br/> Setzen Sie die im Quellmuster erfassten Werte unter Verwendung von $_n_ in das Zielmuster ein. Dabei identifiziert _n_ einen Wert in der Reihenfolge, in der er erfasst wurde. Beispielsweise steht $1 für den ersten im Quellmuster erfassten Wert, während $2 den zweiten Wert darstellt. <br/>

Es wird dringend empfohlen, eine absolute URL zu verwenden. Bei Verwendung einer relativen URL werden CDN-URLs möglicherweise an einen ungültigen Pfad umgeleitet.

**Beispielszenario**

Dieses Beispiel zeigt, wie eine Edge-CNAME-URL umgeleitet wird, die in diese Basis-CDN-URL aufgelöst wird: http:\//marketing.azureedge.net/brochures/

Qualifizierte Anforderungen werden an diese Basis-Edge-CNAME-URL umgeleitet: http:\//cdn.mydomain.com/resources

Diese URL-Umleitung kann durch die folgende Konfiguration erreicht werden: ![URL Redirect](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Die wichtigsten Punkte:**

- Das Feature „URL Redirect“ definiert die Anforderungs-URLs, die umgeleitet werden sollen. Daher sind zusätzliche Übereinstimmungsbedingungen nicht erforderlich. Auch wenn die Übereinstimmungsbedingung als „Always“ definiert wurde, werden nur Anforderungen umgeleitet, die auf den Ordner „brochures“ auf dem Kundenursprung „marketing“ verweisen.
- Alle übereinstimmenden Anforderungen werden an die in der Option „Destination“ definierte Edge-CNAME-URL umgeleitet.
    - Beispielszenario 1:
        - Beispielanforderung (CDN-URL): http:\//marketing.azureedge.net/brochures/widgets.pdf
        - Anforderungs-URL (nach der Umleitung): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Beispielszenario 2:
        - Beispielanforderung (Edge-CNAME-URL): http:\//marketing.mydomain.com/brochures/widgets.pdf
        - Anforderungs-URL (nach der Umleitung): http:\//cdn.mydomain.com/resources/widgets.pdf
    - Beispielszenario 3:
        - Beispielanforderung (Edge-CNAME-URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - Anforderungs-URL (nach der Umleitung): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- Die Variable „Request Scheme (%{scheme})“ wird in der Option „Destination“ verwendet. Dadurch wird sichergestellt, dass das Schema der Anforderung nach der Umleitung unverändert bleibt.
- Die aus der Anforderung erfassten URL-Segmente werden über „$1“ an die neue URL angefügt.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL Rewrite

**Zweck:** Schreibt die Anforderungs-URL um.

Wichtige Informationen:

- Zur Konfiguration dieses Features müssen die folgenden Optionen festgelegt werden:

Option|BESCHREIBUNG
-|-
 Source & Pattern | Diese Einstellungen definieren ein Anforderungs-URI-Muster, das die Art der Anforderungen identifiziert, die umgeschrieben werden können. Nur Anforderungen, deren URL beide der folgenden Kriterien erfüllt, werden umgeschrieben: <br/><br/>  - **Source (or content access point):** Wählen Sie einen relativen Pfad aus, der einen Ursprungsserver identifiziert. Dieser Pfad ist der Abschnitt _/XXXX/_ und Ihr Endpunktname. <br/><br/> - **Source (pattern):** Ein Muster, das Anforderungen nach relativem Pfad identifiziert, muss definiert werden. Dieses Muster für reguläre Ausdrücke muss einen Pfad definieren, der direkt nach dem zuvor ausgewählten Inhaltszugriffspunkt gestartet wird (siehe oben). <br/> Vergewissern Sie sich, dass die oben definierten URI-Kriterien der Anforderung („Source & Pattern“) mit keinen für diese Funktion definierten Übereinstimmungsbedingungen in Konflikt stehen. Geben Sie ein Muster an. Andernfalls werden alle Zeichenfolgen abgeglichen.
 Ziel  |Definieren Sie folgendermaßen die relative URL, in die die oben genannten Anforderungen umgeschrieben werden: <br/>    1. Wählen Sie einen Inhaltszugriffspunkt, der einen Ursprungsserver identifiziert. <br/>    2. Definieren Sie einen relativen Pfad anhand folgender Elemente: <br/>        - Muster für regulären Ausdruck <br/>        - [HTTP-Variablen](cdn-http-variables.md) <br/> <br/> Setzen Sie die im Quellmuster erfassten Werte unter Verwendung von $_n_ in das Zielmuster ein. Dabei identifiziert _n_ einen Wert in der Reihenfolge, in der er erfasst wurde. Beispielsweise steht $1 für den ersten im Quellmuster erfassten Wert, während $2 den zweiten Wert darstellt.

 Dieses Feature ermöglicht den POPs das Umschreiben der URL, ohne dass eine herkömmliche Umleitung ausgeführt werden muss. Die anfordernde Person erhält also den gleichen Antwortcode, den sie auch beim Anfordern der umgeschriebenen URL erhalten hätte.

**Beispielszenario 1**

Dieses Beispiel zeigt, wie eine Edge-CNAME-URL umgeleitet wird, die in diese Basis-CDN-URL aufgelöst wird: http:\//marketing.azureedge.net/brochures/

Qualifizierte Anforderungen werden an diese Basis-Edge-CNAME-URL umgeleitet: http:\//MyOrigin.azureedge.net/resources/

Diese URL-Umleitung kann durch die folgende Konfiguration erreicht werden: ![URL Redirect](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Beispielszenario 2**

Dieses Beispiel zeigt, wie eine Edge-CNAME-URL mithilfe von regulären Ausdrücken aus GROSSBUCHSTABEN an kleinbuchstaben umgeleitet wird.

Diese URL-Umleitung kann durch die folgende Konfiguration erreicht werden: ![URL Redirect](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Die wichtigsten Punkte:**

- Das Feature „URL Rewrite“ definiert die Anforderungs-URLs, die umgeschrieben werden sollen. Daher sind zusätzliche Übereinstimmungsbedingungen nicht erforderlich. Auch wenn die Übereinstimmungsbedingung als „Always“ definiert wurde, werden nur Anforderungen umgeschrieben, die auf den Ordner „brochures“ auf dem Kundenursprung „marketing“ verweisen.

- Die aus der Anforderung erfassten URL-Segmente werden über „$1“ an die neue URL angefügt.

#### <a name="compatibility"></a>Kompatibilität

Dieses Feature umfasst Übereinstimmungskriterien, die erfüllt sein müssen, bevor es auf eine Anforderung angewendet werden kann. Um die Einrichtung widersprüchlicher Übereinstimmungskriterien zu verhindern, ist dieses Feature mit den folgenden Übereinstimmungsbedingungen nicht kompatibel:

- AS Number
- CDN Origin
- Client IP Address
- Customer Origin
- Request Scheme
- URL Path Directory
- URL Path Extension
- URL Path Filename
- URL Path Literal
- URL Path Regex
- URL Path Wildcard
- URL Query Literal
- URL Query Parameter
- URL Query Regex
- URL Query Wildcard

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>User Variable

**Zweck:** Nur zur internen Verwendung.

[Nach oben](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Nächste Schritte

- [Regel-Engine – Referenz](cdn-verizon-premium-rules-engine-reference.md)
- [Regel-Engine – bedingte Ausdrücke](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Übereinstimmungsbedingungen der Regel-Engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Überschreiben des HTTP-Verhaltens mithilfe der Regel-Engine](cdn-verizon-premium-rules-engine.md)
- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)