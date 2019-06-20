---
title: Azure Front Door Service – Zwischenspeicherung | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Azure Front Door Service die Integrität Ihrer Back-Ends überwacht.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 42ee1dea8c9735592f6d6c9e0542ca094a6be383
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962917"
---
# <a name="caching-with-azure-front-door-service"></a>Zwischenspeicherung mit Azure Front Door Service
In diesem Dokument wird das Verhalten von Azure Front Door Service für Routingregeln mit aktivierter Zwischenspeicherung erläutert.

## <a name="delivery-of-large-files"></a>Übermittlung großer Dateien
Azure Front Door Service übermittelt große Dateien ohne Beschränkung der Dateigröße. Bei Azure Front Door Service kommt eine Technik namens Objektblockerstellung zum Einsatz. Wenn eine große Datei angefordert wird, ruft Azure Front Door Service kleinere Teile der Datei vom Back-End ab. Nach dem Empfang einer vollständigen oder auf einen Bytebereich beschränkten Anforderung, fordert eine Azure Front Door Service-Umgebung die Datei in Blöcken von 8 MB vom Back-End an.

</br>Nachdem der Block in der Azure Front Door Service-Umgebung angekommen ist, wird er zwischengespeichert und sofort für den Benutzer bereitgestellt. Azure Front Door Service ruft den nächsten Block dann parallel dazu ab. Durch diesen Vorabruf wird sichergestellt, dass der Inhalt dem Benutzer immer einen Block voraus ist, sodass sich die Wartezeit reduziert. Dieser Prozess wird fortgesetzt, bis die gesamte Datei heruntergeladen wurde (falls angefordert), alle Bytebereiche verfügbar sind (falls angefordert) oder der Client die Verbindung beendet.

</br>Weitere Informationen zur Bytebereichsanforderung finden Sie unter [RFC 7233](https://web.archive.org/web/20171009165003/ http://www.rfc-base.org/rfc-7233.html).
Azure Front Door Service speichert alle Blöcke zwischen, sobald sie eingetroffen sind, sodass nicht die gesamte Datei im Cache von Azure Front Door Service zwischengespeichert werden muss. Nachfolgende Anforderungen für die Datei oder Bytebereiche werden über den Cache verarbeitet. Wenn nicht alle Blöcke zwischengespeichert werden, werden mittels Vorabruf Blöcke vom Back-End angefordert. Diese Optimierung setzt voraus, dass das Back-End Bytebereichsanforderungen unterstützt. Andernfalls ist diese Optimierung nicht effektiv.

## <a name="file-compression"></a>Dateikomprimierung
Azure Front Door Service komprimiert Inhalte dynamisch im Edgebereich, wodurch die Antwort an Ihre Clients kleiner ist und schneller erfolgt. Alle Dateien können komprimiert werden. Eine Datei muss allerdings einen MIME-Typ aufweisen, der in der Liste der zulässigen Typen für die Komprimierung enthalten ist. Gegenwärtig lässt Azure Front Door Service keine Änderungen an dieser Liste zu. Die aktuelle Liste umfasst folgende Typen:</br>
- application/eot
- application/font
- application/font-sfnt
- application/javascript
- "application/json"
- application/opentype
- application/otf
- application/pkcs7-mime
- application/truetype
- application/ttf
- application/vnd.ms-fontobject
- application/xhtml+xml
- application/xml
- application/xml+rss
- application/x-font-opentype
- application/x-font-truetype
- application/x-font-ttf
- application/x-httpd-cgi
- application/x-mpegurl
- application/x-opentype
- application/x-otf
- application/x-perl
- application/x-ttf
- application/x-javascript
- font/eot
- font/ttf
- font/otf
- font/opentype
- image/svg+xml
- text/css
- text/csv
- text/html
- text/javascript
- text/js, text/plain
- text/richtext
- text/tab-separated-values
- text/xml
- text/x-script
- text/x-component
- text/x-java-source

Darüber hinaus muss die Datei zwischen 1 KB und 8 MB groß sein.

Diese Profile unterstützen die folgenden Komprimierungscodierungen:
- [GZip (GNU Zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Unterstützt eine Anforderung die GZip- und Brotli-Komprimierung, hat die Brotli-Komprimierung Vorrang.</br>
Wenn in einer Anforderung für eine Ressource eine Komprimierung angegeben ist und die Anforderung zu einem Cachefehler führt, führt Azure Front Door Service die Komprimierung der Ressource direkt auf dem POP-Server durch. Anschließend wird die komprimierte Datei aus dem Cache bereitgestellt. Das resultierende Element wird mit „transfer-encoding: chunked“ zurückgegeben.

## <a name="query-string-behavior"></a>Verhalten von Abfragezeichenfolgen
Mit Azure Front Door Service können Sie steuern, wie Dateien für eine Webanforderung, die eine Abfragezeichenfolge enthält, zwischengespeichert werden. In einer Webanforderung mit einer Abfragezeichenfolge ist die Abfragezeichenfolge der Teil der Anforderung, der auf das Fragezeichen („?“) folgt. Eine Abfragezeichenfolge kann ein oder mehrere Schlüssel-Wert-Paare enthalten, wobei der Feldname und sein Wert durch ein Gleichheitszeichen („=“) getrennt sind. Die einzelnen Schlüssel-Wert-Paare sind durch ein kaufmännisches Und-Zeichen („&“) voneinander getrennt. Beispiel: http://www.contoso.com/content.mov?field1=value1&field2=value2. Falls mehrere Schlüssel-Wert-Paare in einer Abfragezeichenfolge derselben Anforderung vorhanden sind, spielt die Reihenfolge keine Rolle.
- **Ignorieren von Abfragezeichenfolgen:** Standardmodus. In diesem Modus übergibt Azure Front Door Service die Abfragezeichenfolgen bei der ersten Anforderung vom Anforderer an das Back-End und speichert die Ressource im Cache zwischen. Für alle nachfolgenden Anforderungen der Ressource, die von der Azure Front Door Service-Umgebung verarbeitet werden, werden die Abfragezeichenfolgen bis zum Ablauf der zwischengespeicherten Ressource ignoriert.

- **Zwischenspeichern jeder eindeutigen URL:** In diesem Modus wird jede Anforderung mit einer eindeutigen URL, einschließlich der Abfragezeichenfolge, als eindeutiges Objekt mit eigenem Cache behandelt. So wird beispielsweise die Antwort vom Back-End für eine Anforderung für `www.example.ashx?q=test1` in der Azure Front Door Service-Umgebung zwischengespeichert und für nachfolgende Caches mit der gleichen Abfragezeichenfolge zurückgegeben. Eine Anforderung für `www.example.ashx?q=test2` wird als separates Objekt mit eigener Einstellung für die Gültigkeitsdauer zwischengespeichert.

## <a name="cache-purge"></a>Cachebereinigung
Azure Front Door Service speichert Ressourcen zwischen, bis die Gültigkeitsdauer (Time-to-live, TTL) der Ressource abläuft. Wenn ein Client die Ressource nach Ablauf ihrer TTL anfordert, ruft die Azure Front Door Service-Umgebung eine neue aktualisierte Kopie der Ressource ab, um die Clientanforderung zu erfüllen und den Cache zu aktualisieren.
</br>Die bewährte Methode, um sicherzustellen, dass Ihre Benutzer immer die neueste Kopie Ihrer Assets abrufen, besteht darin, Ihre Assets für jedes Update mit einer Version zu versehen und sie als neue URLs zu veröffentlichen. Azure Front Door Service ruft sofort die neuen Ressourcen für die nächsten Clientanforderungen ab. Manchmal möchten Sie möglicherweise zwischengespeicherten Inhalt aus allen Edgeknoten löschen und sie zwingen, neue aktualisierte Assets abzurufen. Als Gründe hierfür kommen z. B. Updates Ihrer Webanwendung oder schnelle Aktualisierung von Assets, die falsche Informationen enthalten, infrage.

</br>Wählen Sie, welche Assets Sie aus dem Edgeknoten löschen möchten. Wenn Sie alle Ressourcen löschen möchten, aktivieren Sie das Kontrollkästchen „Alles löschen“. Geben Sie andernfalls den vollständigen Pfad jeder Ressource, die Sie löschen möchten, in das Textfeld „Pfad“ ein. Folgende Formate werden im Pfad unterstützt.
1. **Einzelne URL löschen**: Löschen Sie einzelne Ressourcen, indem Sie die vollständige URL mit der Dateierweiterung angeben. Beispiel: /pictures/strasbourg.png.
2. **Mit Platzhalter löschen**: Das Sternchen (\*) kann als Platzhalterzeichen verwendet werden. Löschen Sie alle Ordner, Unterordner und Dateien unter einem Endpunkt, indem Sie „/\*“ im Pfad angeben, oder löschen Sie alle Unterordner und Dateien unter einem bestimmten Ordner, indem Sie den Ordner gefolgt von „/\*“ angeben. Beispiel: /pictures/\*.
3. **Stammdomäne löschen**: Löschen Sie den Stamm des Endpunkts, indem Sie „/“ im Pfad angeben.

Bei Cachebereinigungen in Azure Front Door Service muss die Groß-/Kleinschreibung beachtet werden. Darüber hinaus sind Cachebereinigungen abfragezeichenfolgenagnostisch, d. h. beim Bereinigen einer URL werden alle Abfragezeichenfolgenvariationen der URL gelöscht. 

## <a name="cache-expiration"></a>Cacheablauf
Die folgende Headerreihenfolge wird verwendet, um zu bestimmen, wie lange ein Element im Cache gespeichert wird:</br>
1. Cache-Control: s-maxage=\<Sekunden>
2. Cache-Control: max-age=\<seconds>
3. Expires: \<HTTP-Datum>

Cache-Control-Antwortheader, die angeben, dass die Antwort nicht zwischengespeichert wird (z. B. „Cache-Control: private“, „Cache-Control: no-cache“ und „Cache-Control: no-store“), werden berücksichtigt. Wenn auf einem POP-Server mehrere Anforderungen für dieselbe URL ausgeführt werden, gilt die Antwort jedoch möglicherweise für alle dieser Anforderungen. Ist keine Angabe für „Cache-Control“ vorhanden, ist das Standardverhalten wie folgt: Azure Front Door Service speichert die Ressource für einen Zeitraum X zwischen. Für X wird dabei ein zufällig gewählter Wert von ein bis drei Tagen verwendet.


## <a name="request-headers"></a>Anforderungsheader

Die folgenden Anforderungsheader werden bei Verwendung der Zwischenspeicherung nicht an ein Back-End weitergeleitet.
- Autorisierung
- Content-Length
- Transfer-Encoding

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Azure Front Door Service-Konfiguration](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
