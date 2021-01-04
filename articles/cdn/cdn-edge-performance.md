---
title: Analysieren der Edgeknotenleistung in Azure CDN | Microsoft-Dokumentation
description: Analysieren der Leistung von Edgeknoten in Microsoft Azure CDN. Das Edge Performance Analytics-Modul liefert differenzierte Informationen zu Datenverkehr und Bandbreitenauslastung im CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d8119c56c26ed32db136064d93775d21a71f238f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778655"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analysieren der Leistung von Edgeknoten in Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Übersicht
Das Edge Performance Analytics-Modul liefert differenzierte Informationen zu Datenverkehr und Bandbreitenauslastung im CDN. Diese Informationen können verwendet werden, um Trendstatistiken zu generieren, mit denen Sie sich einen Einblick verschaffen können, wie Ihre Assets im Cache zwischengespeichert und an Ihre Clients übermittelt werden. Anschließend können Sie eine Strategie dazu entwickeln, wie die Übermittlung Ihrer Inhalte optimiert werden kann und bestimmen, welche Probleme angegangen werden sollten, um das CDN besser zu nutzen. Die Folge ist, dass Sie nicht nur die Datenübermittlungsleistung steigern, sondern auch Ihre CDN-Kosten senken können.

> [!NOTE]
> Alle Berichte verwenden zur Angabe von Datum/Uhrzeit die UTC-/GMT-Notation.
> 
> 

## <a name="reports-and-log-collection"></a>Berichte und Protokollsammlung
CDN-Aktivitätsdaten müssen vom Edge Performance Analytics-Modul erst gesammelt werden, bevor Berichte dazu erstellt werden können. Diese Sammlung erfolgt einmal täglich und deckt die Aktivität am vorhergehenden Tag ab. Dies bedeutet, dass die Statistiken eines Berichts eine Stichprobe der Statistiken dieses Tags zum Zeitpunkt ihrer Verarbeitung sind und nicht unbedingt die komplette Datenmenge für den aktuellen Tag enthalten. Die primäre Aufgabe dieser Berichte ist das Bewerten der Leistung. Sie sind nicht für Abrechnungszwecke oder exakte numerische Statistiken gedacht.

> [!NOTE]
> Die Rohdaten, anhand derer Edge Performance Analytics-Berichte generiert werden, stehen mindestens 90 Tage zur Verfügung.
> 
> 

## <a name="dashboard"></a>Dashboard
Das Edge Performance Analytics-Dashboard verfolgt den aktuellen und vorherigen CDN-Datenverkehr mithilfe eines Diagramms und von Statistiken. Nutzen Sie dieses Dashboard, um aktuelle und langfristige Trends bei der Leistung des CDN-Datenverkehrs für Ihr Konto auszumachen.

Dieses Dashboard besteht aus folgenden Elementen:

* Einem interaktiven Diagramm, das die Visualisierung der wichtigsten Metriken und Trends ermöglicht.
* Einer Zeitachse zum Ausmachen langfristiger Muster für die wichtigsten Metriken und Trends.
* Wichtigen Metriken und statistischen Informationen dazu, wie unser CDN den Datenverkehr basierend auf der Messung der gesamten Leistung, Nutzung und Effizienz verbessert.

### <a name="accessing-the-edge-performance-dashboard"></a>Zugreifen auf das Edge Performance Analytics-Dashboard
1. Klicken Sie auf dem Blatt „CDN-Profil“ auf die Schaltfläche **Verwalten** .
   
    ![Schaltfläche „Verwalten“ auf dem CDN-Profilblatt](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Das CDN-Verwaltungsportal wird geöffnet.
2. Zeigen Sie auf die Registerkarte **Analysen** und anschließend auf das Flyout **Edge Perfomance Analytics** .  Klicken Sie auf **Dashboard** .
   
    Das Edge Performance Analytics-Dashboard wird angezeigt.

### <a name="chart"></a>Diagramm
Das Dashboard enthält ein Diagramm, das eine Metrik über den Zeitraum nachverfolgt, der auf der sich direkt darunter befindenden Zeitachse ausgewählt wird.  Eine Zeitachse, auf der die CDN-Aktivität der letzten beiden Jahre maximal abgebildet ist, wird direkt unter dem Diagramm angezeigt.

#### <a name="using-the-chart"></a>Verwenden des Diagramms
* Standardmäßig wird der Effizienzgrad des Caches in den letzten 30 Tagen im Diagramm gezeigt.
* Dieses Diagramm basiert auf Daten, die täglich gesammelt werden.
* Wenn Sie auf einen Tag auf dem Liniendiagramm zeigen, werden ein Datum und der Wert der Metrik an diesem Datum angezeigt.
* Klicken Sie auf „Wochenenden markieren“, um eine Überlagerung aus hellgrauen vertikalen Balken einzublenden, die in diesem Diagramm Wochenenden darstellen. Diese Art von Überlagerung ist nützlich zum Ausmachen von Datenverkehrsmustern an Wochenenden.
* Klicken Sie auf „Ansicht vor einem Jahr“, um eine Überlagerung der Aktivität des vergangenen Jahres für den gleichen Zeitraum im Diagramm einzublenden. Diese Art von Vergleich liefert Einblicke in langfristige CDN-Nutzungsmuster. Rechts oben im Diagramm befindet sich eine Legende, die die Farbe für jedes Liniendiagramm angibt.

#### <a name="updating-the-chart"></a>Aktualisieren des Diagramms
* Zeitbereich: Führen Sie einen der folgenden Schritte aus:
  * Wählen Sie auf der Zeitachse den gewünschten Bereich aus. Das Diagramm wird mit zum ausgewählten Zeitraum gehörigen Daten aktualisiert.
  * Doppelklicken Sie auf das Diagramm, um alle verfügbaren Verlaufsdaten bis zu einem Maximum von zwei Jahren anzuzeigen.
* Metrik: Klicken Sie auf das Diagrammsymbol, das neben der gewünschten Metrik angezeigt wird. Diagramm und Zeitachse werden mit Daten für die entsprechende Metrik aktualisiert.

### <a name="key-metrics-and-statistics"></a>Die wichtigsten Metriken und Statistiken
#### <a name="efficiency-metrics"></a>Metriken zur Effizienz
Zweck dieser Metriken ist es festzustellen, ob die Cache-Effizienz verbessert werden kann. Ein effizienter Cache bietet folgende wesentlichen Vorteile:

* Geringere Auslastung auf dem Ursprungsserver, die zu Folgendem führen kann:
  * Bessere Leistung des Webservers
  * Senkung der Betriebskosten
* Schnellere Datenübermittlung, da mehr Anforderungen direkt vom CDN erfüllt werden.

| Feld | BESCHREIBUNG |
| --- | --- |
| Cache Efficiency |Gibt den Prozentsatz der übertragenen Daten an, die aus dem Cache bereitgestellt wurden. Diese Metrik misst, ob eine im Cache zwischengespeicherte Version des angeforderten Inhalts dem Anfordernden (bzw. Webbrowser) direkt vom CDN (von Edgeservern) bereitgestellt wurde. |
| Hit Rate |Gibt den Prozentsatz der Anforderungen an, die mithilfe des Caches erfüllt wurden. Diese Metrik misst, ob eine im Cache zwischengespeicherte Version des angeforderten Inhalts dem Anfordernden (bzw. Webbrowser) direkt vom CDN (von Edgeservern) bereitgestellt wurde. |
| % of Remote Bytes - No Cache Config |Gibt den Prozentsatz des Datenverkehrs von Ursprungsservern zum CDN (Edgeserver) an, der als Folge des Features „Bypass Cache“ (HTTP-Regel-Engine) nicht zwischengespeichert wird. |
| % of Remote Bytes - Expired Cache |Gibt den Prozentsatz des Datenverkehrs von Ursprungsservern zum CDN (Edgeserver) als Folge der erneuten Überprüfung veralteter Inhalte an. |

#### <a name="usage-metrics"></a>Nutzungsmetriken
Zweck dieser Metriken ist das Ermöglichen von Einblicken in die folgenden kostensenkenden Maßnahmen:

* Minimierung der Betriebskosten durch das CDN
* Reduzieren die CDN-Ausgaben mittels Cache-Effizienz und Komprimierung

> [!NOTE]
> Zahlen zum Datenverkehrsvolumen stellen Datenverkehr dar, der in Berechnungen von Verhältnissen und Prozentsätzen verwendet wurde, und zeigen bei Kunden mit hohen Volumen ggf. nur einen Teil des gesamten Datenverkehrs.
> 
> 

| Feld | BESCHREIBUNG |
| --- | --- |
| Ave Bytes Out |Gibt die durchschnittliche Anzahl von Bytes an, die für jede Anforderung dem Anfordernden (z. B. Webbrowser) aus dem CDN (von Edgeservern) bereitgestellt wurde. |
| No Cache Config Byte Rate |Gibt den Prozentsatz des Datenverkehrs vom CDN (Edgeserver) zum Anfordernden (z. B. Webbrowser) an, der als Folge des Features „Bypass Cache“ nicht zwischengespeichert wird. |
| Compressed Byte Rate |Gibt den Prozentsatz des Datenverkehrs vom CDN (Edgeserver) zum Anfordernden (z. B. Webbrowser) in einem komprimierten Format an. |
| Bytes Out |Gibt die Datenmenge in Bytes an, die aus dem CDN (vom Edgeserver) an den Anfordernden (z. B. Webbrowser) übermittelt wurde. |
| Bytes In |Gibt die Datenmenge in Bytes an, die vom Anfordernden (z. B. Webbrowser) an das CDN (Edgeserver) gesendet wurde. |
| Bytes Remote |Gibt die Datenmenge in Bytes an, die vom CDN und von Kundenursprungsservern an das CDN (Edgeserver) gesendet wurde. |

#### <a name="performance-metrics"></a>Leistungsmetriken
Diese Metriken dient zum Nachverfolgen der CDN-Gesamtleistung für den Datenverkehr.

| Feld | BESCHREIBUNG |
| --- | --- |
| Transfer Rate |Gibt die durchschnittliche Rate an, mit der Inhalte aus dem CDN an den Anfordernden übertragen wurden. |
| Duration |Gibt die durchschnittliche Zeit in Millisekunden an, die das Übermitteln eines Assets an einen Anfordernden (z. B. Webbrowser) gedauert hat. |
| Compressed Request Rate |Gibt den Prozentsatz der Treffer an, die vom CDN (von Edgeservern) an den Anfordernden (z. B. Webbrowser) in einem komprimierten Format übermittelt wurde. |
| 4xx Error Rate |Gibt den Prozentsatz der Treffer an, die einen Statuscode vom Typ „4xx“ generiert haben. |
| 5xx Error Rate |Gibt den Prozentsatz der Treffer an, die einen Statuscode vom Typ „5xx“ generiert haben. |
| Treffer |Gibt die Anzahl der Anforderungen von CDN-Inhalten an. |

#### <a name="secure-traffic-metrics"></a>Metriken zu geschütztem Datenverkehr
Diese Metriken dient zum Nachverfolgen der CDN-Leistung für HTTPS-Datenverkehr.

| Feld | BESCHREIBUNG |
| --- | --- |
| Secure Cache Efficiency |Gibt den Prozentsatz von für HTTPS-Anforderungen übertragenen Daten an, die mithilfe des Caches erfüllt wurden. Diese Metrik misst, ob eine im Cache zwischengespeicherte Version des angeforderten Inhalts direkt vom CDN (von Edgeservern) dem Anfordernden (bzw. Webbrowser) über HTTPS bereitgestellt wurde. |
| Secure Transfer Rate |Gibt die durchschnittliche Rate an, mit der Inhalte vom CDN (von Edgeservern) an den Anfordernden (z. B. Webserver) über HTTPS übertragen wurden. |
| Average Secure Duration |Gibt die durchschnittliche Zeit in Millisekunden an, die das Übermitteln eines Assets an einen Anfordernden (z. B. Webbrowser) über HTTPS gedauert hat. |
| Secure Hits |Gibt die Anzahl der HTTPS-Anforderungen von CDN-Inhalten an. |
| Secure Bytes Out |Gibt den Umfang des HTTPS-Datenverkehrs an, der vom CDN (von Edgeservern) an den Anfordernden (z. B. Webbrowser) übermittelt wurde. |

## <a name="reports"></a>Berichte
Jeder Bericht in diesem Modul enthält ein Diagramm und Statistiken zu Bandbreitenbreitenauslastung und Datenverkehr für verschiedene Arten von Metriken (z. B. HTTP-Statuscodes, Cachestatuscodes, Anforderungs-URL usw.). Anhand dieser Informationen kann detailliert bestimmt werden, wie Inhalte Ihren Clients zur Verfügung gestellt werden, und das CDN-Verhalten zum Verbessern der Datenübermittlungsleistung optimiert werden.

### <a name="accessing-the-edge-performance-reports"></a>Zugreifen auf Edge Performance Analytics-Berichte
1. Klicken Sie auf dem Blatt „CDN-Profil“ auf die Schaltfläche **Verwalten** .
   
    ![Schaltfläche „Verwalten“ auf dem CDN-Profilblatt](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Das CDN-Verwaltungsportal wird geöffnet.
2. Zeigen Sie auf die Registerkarte **Analysen** und anschließend auf das Flyout **Edge Perfomance Analytics** .  Klicken Sie auf **Großes HTTP-Objekt** .
   
    Der Bildschirm mit Edge Performance Analytics-Berichten wird angezeigt.

| Bericht | BESCHREIBUNG |
| --- | --- |
| Tägliche Zusammenfassung |Ermöglicht das Prüfen täglicher Datenverkehrstrends über einen bestimmten Zeitraum. Jeder Balken in diesem Diagramm stellt ein bestimmtes Datum dar. Die Größe des Balkens gibt die Gesamtanzahl der Treffer an diesem Datum an. |
| Stündliche Zusammenfassung |Ermöglicht das Prüfen stündlicher Datenverkehrstrends über einen bestimmten Zeitraum. Jeder Balken in diesem Diagramm stellt eine bestimmte Stunde an einem bestimmten Datum dar. Die Größe des Balkens gibt die Gesamtanzahl der Treffer in dieser Stunde an. |
| Protokolle |Zeigt die Aufschlüsselung des Datenverkehrs zwischen den Protokollen HTTP und HTTPS. Ein Ringdiagramm gibt den Prozentsatz der Treffer für jeden Protokolltyp an. |
| HTTP-Methoden |Bietet rasch einen Überblick darüber, welche HTTP-Methoden zum Anfordern Ihrer Daten genutzt werden. Die gängigsten HTTP-Anforderungsmethoden sind GET, HEAD und POST. Ein Ringdiagramm gibt den Prozentsatz der Treffer für alle Typen von HTTP-Anforderungsmethoden an. |
| URLs |Enthält ein Diagramm der 10 am häufigsten angeforderten URLs. Für jede URL wird ein Balken angezeigt. Die Höhe des Balkens gibt die Anzahl der Treffer an, die diese URL im Berichtszeitraum generiert hat. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten angeforderten URLs angezeigt. |
| CNAMEs |Enthält ein Diagramm der 10 CNAMEs, die im Berichtszeitraum am häufigsten zum Anfordern von Assets verwendet wurden. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten angeforderten CNAMEs angezeigt. |
| Ursprünge |Enthält ein Diagramm der 10 am häufigsten verwendeten CDN- oder Kundenursprungsserver, von denen im angegebenen Zeitraum Assets angefordert wurden. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten angeforderten CDN- oder Kundenursprungsservern angezeigt. Kundenursprungsserver werden anhand des Namens bestimmt, der in der Option „Verzeichnisname“ definiert wurde. |
| Geo-POPs |Zeigt an, wie viel von Datenverkehr durch einen bestimmten Point of Presence (POP) geleitet wird. Die aus drei Buchstaben bestehende Abkürzung stellt einen POP in unserem CDN-Netzwerk dar. |
| Clients |Enthält ein Diagramm der 10 Clients, die Assets im angegebenen Zeitraum am häufigsten angefordert haben. Für diesen Berichts werden alle Anforderungen, die von derselben IP-Adresse stammen, demselben Client zugeordnet. Direkt unter diesem Diagramm werden Statistiken zu den 100 aktivsten Clients angezeigt. Dieser Bericht dient zum Bestimmen von Downloadaktivitätsmustern Ihrer aktivsten Clients. |
| Cachestatus |Bietet eine detaillierte Aufschlüsselung des Cacheverhaltens, woraus sich ggf. Ansätze zum Verbessern der allgemeinen Endbenutzererfahrung ergeben. Da die schnellste Leistung durch Cachetreffer erzielt wird, können Sie die Geschwindigkeit der Datenübermittlung optimieren, indem Sie Cachefehler und abgelaufene Cachetreffer auf ein Minimum reduzieren. |
| NONE Details |Enthält ein Diagramm der 10 am häufigsten verwendeten URLs für Assets, für die im angegebenen Zeitraum die Aktualität des Cache-Inhalts nicht geprüft wurde. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten verwendeten URLs für diese Arten von Assets angezeigt. |
| CONFIG_NOCACHE Details |Enthält ein Diagramm der 10 am häufigsten verwendeten URLs für Assets, die aufgrund der CDN-Konfiguration des Kunden nicht im Cache zwischengespeichert wurden. Diese Arten von Assets werden direkt vom Ursprungsserver bereitgestellt. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten verwendeten URLs für diese Arten von Assets angezeigt. |
| UNCACHEABLE Details |Enthält ein Diagramm der 10 am häufigsten verwendeten URLs für Assets, die aufgrund von Daten im Anforderungsheader nicht im Cache zwischengespeichert werden konnten. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten verwendeten URLs für diese Arten von Assets angezeigt. |
| TCP_HIT Details |Enthält ein Diagramm der 10 am häufigsten verwendeten URLs für Assets, die direkt aus dem Cache bereitgestellt werden. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten verwendeten URLs für diese Arten von Assets angezeigt. |
| TCP_MISS Details |Enthält ein Diagramm der 10 am häufigsten verwendeten URLs für Assets mit dem Cachestatus TCP_MISS. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten verwendeten URLs für diese Arten von Assets angezeigt. |
| TCP_EXPIRED_HIT Details |Enthält ein Diagramm der 10 am häufigsten verwendeten URLs für veraltete Assets, die direkt vom POP bereitgestellt wurden. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten verwendeten URLs für diese Arten von Assets angezeigt. |
| TCP_EXPIRED_MISS Details |Enthält ein Diagramm der 10 am häufigsten verwendeten URLs für veraltete Assets, für die eine neue Version vom Ursprungsserver abgerufen werden musste. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten verwendeten URLs für diese Arten von Assets angezeigt. |
| TCP_CLIENT_REFRESH_MISS Details |Enthält ein Balkendiagramm der 10 am häufigsten verwendeten URLs für Assets, die aufgrund einer Anforderung vom Typ „no-cache“ des Clients von einem Ursprungsserver abgerufen wurden. Direkt unter diesem Diagramm werden Statistiken zu den 100 am häufigsten verwendeten URLs für diese Arten von Anforderungen angezeigt. |
| Clientanforderungstypen |Gibt den Typ von Anforderungen an, die durch HTTP-Clients (z. B. Browser) erfolgt sind. Dieser Bericht enthält ein Ringdiagramm, das zeigt, wie Anforderungen verarbeitet wurden. Bandbreiten- und Datenverkehrsinformationen für jeden Anforderungstyp werden unter dem Diagramm angezeigt. |
| Benutzer-Agent |Enthält ein Balkendiagramm der 10 am häufigsten verwendeten Benutzer-Agents zum Anfordern von Inhalten über unser CDN. Ein Benutzer-Agent ist meist ein Webbrowser, Media Player oder Browser eines Mobiltelefons. Direkt unter diesem Diagramm werden Statistiken zu den 100 aktivsten Benutzer-Agents angezeigt. |
| Verweise |Enthält ein Balkendiagramm der 10 am häufigsten verwendeten Verweise auf Inhalte, auf die über unser CDN zugegriffen wird. Normalerweise ist ein Verweis die URL der Webseite oder Ressource, die mit Ihren Inhalten verknüpft ist. Unter dem Diagramm werden detaillierte Informationen zu den 100 häufigsten Verweisen angezeigt. |
| Komprimierungstypen |Enthält ein Ringdiagramm mit einer Aufschlüsselung angeforderter Assets dahingehend, ob sie von unseren Edgeservern komprimiert wurden. Der Prozentsatz komprimierter Assets wird anhand des verwendeten Komprimierungstyp aufgeteilt. Unter dem Diagramm werden detaillierte Informationen zu jedem Komprimierungstyp und -status angezeigt. |
| Dateitypen |Enthält ein Balkendiagramm mit den 10 Dateitypen, die für Ihr Konto am häufigsten über unser CDN angefordert wurden. Für diesen Bericht wird ein Dateityp anhand der Dateinamenerweiterung des Assets und des Internetmedientyps (z.B. .html \[text/html\], .htm \[text/html\], .aspx \[text/html\] usw.) definiert. Unter dem Diagramm werden detaillierte Informationen zu den 100 häufigsten Dateitypen angezeigt. |
| Eindeutige Dateien |Enthält ein Diagramm mit der Gesamtanzahl der eindeutigen Assets, die über einen angegebenen Zeitraum an einem bestimmten Tag angefordert wurden. |
| Token Auth Summary |Enthält ein Kreisdiagramm mit einem Überblick, ob angeforderte Assets durch die tokenbasierte Authentifizierung geschützt waren. Geschützte Assets werden im Diagramm entsprechend den Ergebnissen ihrer versuchten Authentifizierung angezeigt. |
| Token Auth Deny Details |Enthält ein Balkendiagramm der 10 Anforderungen, die aufgrund der tokenbasierten Authentifizierung am häufigsten verweigert wurden. |
| HTTP Response Codes |Bietet eine Aufschlüsselung der HTTP-Statuscodes (z. B. 200 OK , 403 Verboten, 404 Nicht gefunden usw.), die von unseren Edgeservern an Ihre HTTP-Clients übermittelt wurden. Mithilfe eines Kreisdiagramms können schnell erkennen, wie Ihre Assets bereitgestellt wurden. Für jeden Antwortcode werden unter dem Diagramm detaillierte statistische Daten bereitgestellt. |
| 404 Errors |Enthält ein Balkendiagramm der 10 häufigsten Anforderungen, die zum Antwortcode „404 Nicht gefunden“ geführt haben. |
| 403 Errors |Enthält ein Balkendiagramm der 10 häufigsten Anforderungen, die zum Antwortcode „403 Verboten“ geführt haben. Der Antwortcode „403 Verboten“ tritt auf, wenn eine Anforderung von einem Kundenursprungsserver oder einem Edgeserver auf unserem POP verweigert wird. |
| 4xx Errors |Enthält ein Balkendiagramm der 10 häufigsten Anforderungen, die zu einem Antwortcode im 400-er Bereich geführt haben. Von diesem Bericht ausgeschlossen sind die Antwortcodes „403 Verboten“ und „404 Nicht gefunden“. Normalerweise tritt ein 4xx-Antwortcode auf, wenn eine Anforderung aufgrund eines Clientfehlers verweigert wird. |
| 504 Errors |Enthält ein Balkendiagramm der 10 häufigsten Anforderungen, die zum Antwortcode „504 Gatewaytimeout“ geführt haben. Der Antwortcode „504 Gatewaytimeout“ tritt auf, wenn beim Versuch eines HTTP-Proxys, mit einem anderen Server zu kommunizieren, ein Timeout auftritt. Bei unserem CDN tritt der Antwortcode „504 Gatewaytimeout“ üblicherweise dann auf, wenn ein Edgeserver keine Kommunikation mit einem Kundenursprungsserver herstellen kann. |
| 502 Errors |Enthält ein Balkendiagramm der 10 häufigsten Anforderungen, die zum Antwortcode „502 Ungültiges Gateway“ geführt haben. Der Antwortcode „502 Ungültiges Gateway“ tritt auf, wenn zwischen einem Server und HTTP-Proxy ein Protokollfehler auftritt. Bei unserem CDN tritt der Antwortcode „502 Ungültiges Gateway“ üblicherweise dann auf, wenn ein Kundenursprungsserver eine ungültige Antwort an einen Edgeserver zurückgibt. Eine Antwort ist ungültig, wenn sie nicht analysiert werden kann oder unvollständig ist. |
| 5xx Errors |Enthält ein Balkendiagramm der 10 häufigsten Anforderungen, die zu einem Antwortcode im 500-er Bereich geführt haben.  Von diesem Bericht ausgeschlossen sind die Antwortcodes „502 Ungültiges Gateway“ und „504 Gatewaytimeout“. |

## <a name="see-also"></a>Weitere Informationen
* [Übersicht über das Azure CDN](cdn-overview.md)
* [Echtzeitstatistiken in Microsoft Azure CDN](cdn-real-time-stats.md)
* [Überschreiben des HTTP-Standardverhaltens mithilfe der Regel-Engine](./cdn-verizon-premium-rules-engine.md)
* [Erweiterte HTTP-Berichte](cdn-advanced-http-reports.md)