---
title: Funktionsweise der Zwischenspeicherung | Microsoft-Dokumentation
description: Unter Zwischenspeichern versteht man das lokale Speichern von Daten, um im Fall einer erneuten Anforderung dieser Daten schneller darauf zugreifen zu können.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: a226682c2580a871e1b2fc4db71f369f3bcc3abb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010162"
---
# <a name="how-caching-works"></a>Funktionsweise der Zwischenspeicherung

Dieser Artikel enthält einen Überblick über allgemeine Konzepte zum Zwischenspeichern und darüber, wie das [Azure Content Delivery Network (CDN)](cdn-overview.md) mithilfe der Zwischenspeicherung die Leistung verbessert. Wenn Sie mehr darüber erfahren möchten, wie Sie das Verhalten beim Zwischenspeichern bei Ihrem CDN-Endpunkt anpassen können, lesen Sie [Steuern des Verhaltens beim Zwischenspeichern im Azure Content Delivery Network mit Cacheregeln](cdn-caching-rules.md) und [Steuern des Azure Content Delivery Network-Zwischenspeicherverhaltens mit Abfragezeichenfolgen](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Einführung zum Zwischenspeichern

Unter Zwischenspeichern versteht man das lokale Speichern von Daten, um im Fall einer erneuten Anforderung dieser Daten schneller darauf zugreifen zu können. Bei dem am häufigsten verwendeten Cachetyp, dem Webbrowsercache, speichert ein Webbrowser Kopien von statischen Daten auf einer lokalen Festplatte. Durch das Zwischenspeichern kann der Webbrowser verhindern, dass mehrere Roundtrips zum Server ausgeführt werden, und stattdessen lokal auf diese Daten zugreifen, wodurch Zeit und Ressourcen gespart werden. Das Zwischenspeichern ist besonders für die lokale Verwaltung kleiner, statischer Daten (z.B. statischen Bildern, CSS-Dateien und JavaScript-Dateien) geeignet.

Gleichermaßen wird die Funktion zum Zwischenspeichern von einem Content Delivery Network auf Edgeservern in der Nähe des Benutzers verwendet, um zu vermeiden, dass Anforderungen zum Ursprung zurückkehren, und die Latenzen für Endbenutzer zu verringern. Im Gegensatz zu einem Webbrowsercache, der nur für einen einzelnen Benutzer verwendet wird, verfügt das CDN über einen freigegebenen Cache. In einem freigegebenen CDN-Cache kann auf eine von einem Benutzer angeforderte Datei später von anderen Benutzern zugegriffen werden, was die Anzahl der Anforderungen an den Ursprungsserver stark reduziert.

Dynamische Ressourcen, die häufig geändert werden oder für einen einzelnen Benutzer eindeutig sind, können nicht zwischengespeichert werden. Bei diesen Arten von Ressourcen kann zur Verbesserung der Leistung jedoch die DSA-Optimierung (Dynamic Site Acceleration) im Azure Content Delivery Network eingesetzt werden.

Eine Zwischenspeicherung kann auf mehreren Ebenen zwischen dem Ursprungsserver und dem Endbenutzer erfolgen:

- Webserver: Verwendet einen freigegebenen Cache (für mehrere Benutzer).
- Content Delivery Network: Verwendet einen freigegebenen Cache (für mehrere Benutzer).
- Internetdienstanbieter (ISP): Verwendet einen freigegebenen Cache (für mehrere Benutzer).
- Webbrowser: Verwendet einen privaten Cache (für einen Benutzer).

Die einzelnen Caches stellen in der Regel die Aktualität ihrer eigenen Ressourcen sicher und führen eine Überprüfung durch, wenn eine Datei veraltet ist. Dieses Verhalten ist in der HTTP-Zwischenspeicherungsspezifikation [RFC 7234](https://tools.ietf.org/html/rfc7234) definiert.

### <a name="resource-freshness"></a>Ressourcenaktualität

Da eine zwischengespeicherte Ressource möglicherweise veraltet sein kann (im Vergleich zu der entsprechenden Ressource auf dem Ursprungsserver), ist es wichtig, dass Mechanismen zum Zwischenspeichern kontrollieren, wann der Inhalt aktualisiert wird. Um Zeit und Bandbreitenkapazitäten zu sparen, wird eine zwischengespeicherte Ressource nicht mit der Version auf dem Ursprungsserver abgeglichen, wenn auf diesen zugegriffen wird. Solange eine zwischengespeicherte Ressource als aktuell gilt, wird stattdessen davon ausgegangen, dass es sich bei dieser um die neueste Version handelt, und sie wird direkt an den Client gesendet. Eine zwischengespeicherte Ressource gilt als aktuell, wenn diese neuer ist als der durch eine Cacheeinstellung definierte Zeitraum. Wenn ein Browser beispielsweise eine Webseite neu lädt, stellt er sicher, dass jede zwischengespeicherte Ressource auf Ihrer Festplatte aktuell ist, und lädt diese. Wenn die Ressource nicht aktuell ist (d.h. veraltet), wird eine aktuelle Kopie vom Server geladen.

### <a name="validation"></a>Überprüfen

Wenn eine Ressource als veraltet gilt, wird der Ursprungsserver aufgefordert, diese zu überprüfen, d.h. festzustellen, ob die Daten im Cache noch mit den Daten auf dem Ursprungsserver übereinstimmen. Wenn die Datei auf dem Ursprungsserver geändert wurde, aktualisiert der Cache die jeweilige Version der Ressource. Wenn die Ressource aktuell ist, werden die Daten direkt aus dem Cache bereitgestellt, ohne dass dieser vorher überprüft wird.

### <a name="cdn-caching"></a>CDN-Caches

Die Zwischenspeicherung ist für die Funktionsweise eines CDN von zentraler Bedeutung, um die Bereitstellung zu beschleunigen und die Auslastung des Ursprungsservers im Hinblick auf statische Ressourcen wie Bilder, Schriften und Videos zu reduzieren. Bei CDN-Caches werden statische Ressourcen selektiv auf strategisch platzierten Servern gespeichert, die sich näher an einem Benutzer befinden und folgende Vorteile bieten:

- Da der Großteil des Webdatenverkehrs statisch ist (z.B. Bilder, Schriften und Videos), reduzieren CDN-Caches die Netzwerklatenzen, indem sie Inhalte näher an den Benutzer verlagern und so die Entfernung der Datenübertragung verringern.

- Durch die Auslagerung von Aufgaben in ein CDN können der Netzwerkdatenverkehr und die Auslastung des Ursprungsservers durch Zwischenspeicherung reduziert werden. Hierdurch werden der Kosten- und Ressourcenaufwand für die Anwendung selbst bei einer großen Benutzeranzahl reduziert.

Ähnlich wie bei der Implementierung der Zwischenspeicherung in einem Webbrowser können Sie durch Senden von Headern mit Cacheanweisungen steuern, wie das Zwischenspeichern in einem CDN durchgeführt wird. Header mit Cacheanweisungen sind HTTP-Header, die üblicherweise vom Ursprungsserver hinzugefügt werden. Obwohl die meisten dieser Header ursprünglich für die Zwischenspeicherung in Clientbrowsern konzipiert wurden, werden sie nun auch von sämtlichen Caches zwischen Server und Browser – also auch von CDNs – verwendet. 

Zum Definieren der Cacheaktualität können zwei Header verwendet werden: `Cache-Control` und `Expires`. `Cache-Control` ist neuer und hat gegenüber `Expires` Vorrang, wenn beide vorhanden sind. Es gibt auch zwei Arten von Headern, die zur Überprüfung verwendet werden (sogenannte Validierungssteuerelemente): `ETag` und `Last-Modified`. `ETag` ist neuer und hat gegenüber `Last-Modified` Vorrang, wenn beide definiert sind.  

## <a name="cache-directive-headers"></a>Header mit Cacheanweisungen

> [!IMPORTANT]
> Standardmäßig ignoriert ein für die DSA optimierter Azure CDN-Endpunkt Header mit Cacheanweisungen und umgeht die Zwischenspeicherung. Für die Profile **Azure CDN Standard von Verizon** und **Azure CDN Standard von Akamai** können Sie die Behandlung dieser Header durch einen Azure CDN-Endpunkt anpassen, indem Sie [CDN-Cacheregeln](cdn-caching-rules.md) verwenden, um die Zwischenspeicherung zu aktivieren. Nur bei **Azure CDN Premium von Verizon**-Profilen verwenden Sie die [Regel-Engine](./cdn-verizon-premium-rules-engine.md), um die Zwischenspeicherung zu aktivieren.

Azure CDN unterstützt die folgenden HTTP-Header mit Cacheanweisungen, die die Cachedauer und die -freigabe definieren.

**Cache-Control:**
- Es wurde in HTTP 1.1 eingeführt, um Webpublishern eine größere Kontrolle über ihre Inhalte zu ermöglichen und die Beschränkungen des `Expires`-Headers zu umgehen.
- Überschreibt den `Expires`-Header, wenn beide sowie `Cache-Control` definiert sind.
- Wenn das Headerfeld `Cache-Control` in einer HTTP-Anforderung vom Client an den CDN-PoP verwendet wird, wird es standardmäßig von allen Azure CDN-Profilen ignoriert.
- Wenn es in einer HTTP-Antwort vom Client an den CDN-PoP verwendet wird, gilt Folgendes:
     - **Azure CDN Standard/Premium von Verizon** und **Azure CDN Standard von Microsoft** unterstützen alle `Cache-Control`-Anweisungen.
     - **Azure CDN Standard von Akamai** unterstützt nur die folgenden `Cache-Control`-Anweisungen, während alle anderen ignoriert werden:
         - `max-age`: Ein Cache kann den Inhalt für den in Sekunden angegebenen Zeitraum speichern. Beispiel: `Cache-Control: max-age=5`. Diese Anweisung gibt den maximalen Zeitraum an, in dem ein Inhalt als aktuell gilt.
         - `no-cache`: Der Inhalt wird im Cache gespeichert, aber vor jeder Bereitstellung aus dem Cache überprüft. Entspricht `Cache-Control: max-age=0`.
         - `no-store`: Der Inhalt wird niemals im Cache gespeichert. Entfernen Sie Inhalte, wenn sie zuvor gespeichert wurden.

**Expires:**
- Die in HTTP 1.0 eingeführten Legacyheader werden zur Bereitstellung von Abwärtskompatibilität unterstützt.
- Verwendet eine datumsbasierte Ablaufzeit mit sekundengenauer Genauigkeit. 
- Ähnlich wie `Cache-Control: max-age`.
- Wird verwendet, wenn `Cache-Control` nicht vorhanden ist.

**Pragma:**
   - Wird standardmäßig nicht vom Azure-CDN berücksichtigt.
   - Die in HTTP 1.0 eingeführten Legacyheader werden zur Bereitstellung von Abwärtskompatibilität unterstützt.
   - Wird als Clientanforderungsheader mit der folgenden Anweisung verwendet: `no-cache`. Diese Anweisung weist den Server dazu an, eine neue Version der Ressource zu übermitteln.
   - `Pragma: no-cache` entspricht `Cache-Control: no-cache`.

## <a name="validators"></a>Validierungssteuerelemente

Wenn der Cache veraltet ist, werden Validierungssteuerelemente des HTTP-Caches verwendet, um die zwischengespeicherte Version einer Datei mit der Version auf dem Ursprungsserver abzugleichen. **Azure CDN Standard/Premium von Verizon** unterstützt standardmäßig sowohl `ETag`- als auch `Last-Modified`-Validierungssteuerelemente, wohingegen **Azure CDN Standard von Microsoft** und **Azure CDN Standard von Akamai** standardmäßig nur `Last-Modified` unterstützen.

**ETag:**
- **Azure CDN Standard/Premium von Verizon** unterstützt standardmäßig `ETag`, was bei **Azure CDN Standard von Microsoft** und **Azure CDN Standard von Akamai** nicht der Fall ist.
- `ETag` definiert eine Zeichenfolge, die für jede Datei und Dateiversion eindeutig ist. Beispiel: `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Es wurde in HTTP 1.1 eingeführt und ist neuer als `Last-Modified`. Ist nützlich, wenn die Ermittelung des Datums der letzten Änderung schwierig ist.
- Unterstützt sowohl eine sichere als auch eine unsichere Überprüfung, wobei das Azure CDN jedoch nur eine sichere Überprüfung unterstützt. Für eine sichere Überprüfung müssen die beiden Ressourcendarstellungen Byte für Byte identisch sein. 
- Ein Cache überprüft eine Datei mit `ETag`, indem er einen `If-None-Match`-Header mit einem oder mehreren `ETag`-Validierungssteuerelementen in der Anforderung sendet. Beispiel: `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Wenn die Serverversion mit einem `ETag`-Validierungssteuerelement in der Liste übereinstimmt, wird der Statuscode 304 (Nicht geändert) in der Antwort gesendet. Wenn die Version unterschiedlich ist, antwortet der Server mit dem Statuscode 200 (OK) und der aktualisierten Ressource.

**Last-Modified:**
- Nur bei **Azure CDN Standard/Premium von Verizon** wird `Last-Modified` verwendet, wenn `ETag` nicht Teil der HTTP-Antwort ist. 
- Gibt das Datum und die Uhrzeit an, an dem der Ursprungsserver festgestellt hat, dass die Ressource zuletzt geändert wurde. Beispiel: `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Ein Cache überprüft eine Datei mit `Last-Modified`, indem er einen `If-Modified-Since`-Header mit Datum und Uhrzeit in der Anforderung sendet. Der Ursprungsserver gleicht dieses Datum mit dem `Last-Modified`-Header der aktuellen Ressource ab. Wenn die Ressource seit dem angegebenen Zeitpunkt nicht geändert wurde, gibt der Server in seiner Antwort den Statuscode 304 (Nicht geändert) zurück. Wenn die Ressource geändert wurde, gibt der Server den Statuscode 200 (OK) und die aktualisierte Ressource zurück.

## <a name="determining-which-files-can-be-cached"></a>Ermitteln der zwischenspeicherbaren Dateien

Nicht alle Ressourcen können zwischengespeichert werden. Die folgende Tabelle zeigt, welche Ressourcen abhängig von der Art der HTTP-Antwort zwischengespeichert werden können. Mit HTTP-Antworten bereitgestellte Ressourcen, die nicht alle Bedingungen erfüllen, können nicht zwischengespeichert werden. Nur bei **Azure CDN Premium von Verizon** können Sie mithilfe der Regel-Engine einige dieser Bedingungen anpassen.

|                       | Azure CDN von Microsoft          | Azure CDN von Verizon | Azure CDN von Akamai        |
|-----------------------|-----------------------------------|------------------------|------------------------------|
| **HTTP-Statuscodes (Azure Cognitive Search)** | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| **HTTP-Methoden**      | GET, HEAD                         | GET                    | GET                          |
| **Dateigrößenbeschränkungen**  | 300 GB                            | 300 GB                 | – Optimierung der allgemeinen Webbereitstellung: 1,8 GB<br />– Medienstreaming-Optimierungen: 1,8 GB<br />– Optimierung großer Dateien: 150 GB |

Damit das Zwischenspeichern von **Azure CDN Standard von Microsoft** bei einer Ressource funktioniert, muss der Ursprungsserver HEAD- und GET-HTTP-Anforderungen unterstützen, und die Inhaltslängenwerte müssen für alle HEAD- und GET-HTTP-Antworten für die Ressource identisch sein. Bei einer HEAD-Anforderung muss der Ursprungsserver die HEAD-Anforderung unterstützen und mit den gleichen Headern antworten wie beim Empfang einer GET-Anforderung.

## <a name="default-caching-behavior"></a>Standardverhalten beim Zwischenspeichern

In der folgenden Tabelle wird das Standardverhalten beim Zwischenspeichern bei den Azure CDN-Produkten und deren Optimierungen beschrieben.

|    | Microsoft: Allgemeine Webbereitstellung | Verizon: Allgemeine Webbereitstellung | Verizon: DSA | Akamai: Allgemeine Webbereitstellung | Akamai: DSA | Akamai: Download großer Dateien | Akamai: Allgemeines oder VoD-Medienstreaming |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Berücksichtigung des Ursprungs**       | Ja    | Ja   | Nein   | Ja    | Nein   | Ja   | Ja    |
| **CDN-Cachedauer** | 2 Tage |7 Tage | Keine | 7 Tage | Keine | 1 Tag | 1 Jahr |

**Berücksichtigung des Ursprungs**: Gibt an, ob die unterstützten Header mit Cacheanweisungen berücksichtigt werden sollen, wenn sie in der HTTP-Antwort des Ursprungsservers enthalten sind.

**CDN-Cachedauer**: Gibt den Zeitraum an, in dem eine Ressource im Azure CDN zwischengespeichert wird. Wenn **Berücksichtigung des Ursprungs** jedoch auf „Ja“ festgelegt ist und die HTTP-Antwort des Ursprungsservers den Header mit Cacheanweisungen `Expires` oder `Cache-Control: max-age` enthält, verwendet das Azure CDN stattdessen den vom Header angegebenen Wert für die Dauer. 

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Anpassen und Überschreiben des Standardverhaltens beim Zwischenspeichern im CDN anhand von Cacheregeln finden Sie unter [Steuern des Verhaltens beim Zwischenspeichern im Azure CDN mit Cacheregeln](cdn-caching-rules.md). 
- Weitere Informationen zur Steuerung des Verhaltens beim Zwischenspeichern mithilfe von Abfragezeichenfolgen finden Sie unter [Steuern des Azure Content Delivery Network-Zwischenspeicherverhaltens mit Abfragezeichenfolgen](cdn-query-string.md).