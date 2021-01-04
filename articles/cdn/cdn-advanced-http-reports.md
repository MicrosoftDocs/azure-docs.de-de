---
title: Analysieren von Nutzungsstatistiken mit den erweiterten HTTP-Berichten in Azure CDN | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie erweiterte HTTP-Berichte in Microsoft Azure CDN erstellen. Diese Berichte enthalten detaillierte Informationen zur CDN-Aktivität.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e2ef1367cbbb1fb71159c4520c3d1481904c1769
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779321"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analysieren von Nutzungsstatistiken mit den erweiterten HTTP-Berichten in Azure CDN
## <a name="overview"></a>Übersicht
In diesem Dokument werden die erweiterten HTTP-Berichte in Microsoft Azure CDN erläutert. Diese Berichte enthalten detaillierte Informationen zur CDN-Aktivität.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Aufrufen erweiterter HTTP-Berichte
1. Klicken Sie auf dem Blatt „CDN-Profil“ auf die Schaltfläche **Verwalten** .
   
    ![Schaltfläche „Verwalten“ auf dem CDN-Profilblatt](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Das CDN-Verwaltungsportal wird geöffnet.
2. Zeigen Sie auf die Registerkarte **Analysen** und dann auf das Flyout **Erweiterte HTTP-Berichte** .  Klicken Sie auf **Große HTTP-Plattform** .
   
    ![CDN-Verwaltungsportal – Menü „Erweiterte Berichte“](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Berichtsoptionen werden angezeigt.

## <a name="geography-reports-map-based"></a>Geografische Berichte (kartenbasiert)
Fünf Berichte stehen zur Verfügung, in denen mithilfe einer Karte die Regionen angegeben werden, aus denen Ihre Inhalte angefordert werden. Diese Berichte heißen „Weltkarte“, „Karte der USA“, „Karte von Kanada“, „Europakarte“ und „Asien-Pazifik-Karte“.

Jeder kartenbasierte Bericht bildet eine Rangfolge für geografische Entitäten (d. h. Länder/Regionen). Es wird eine Karte bereitgestellt, die Ihnen hilft, die Standorte zu visualisieren, von denen aus Ihr Inhalt angefordert wird. Dabei werden die einzelnen Regionen entsprechend der Nachfrage in der jeweiligen Region farbkodiert. Hellere Regionen weisen auf eine niedrigere Nachfrage nach Ihren Inhalten hin, dunklere Regionen auf eine höhere Nachfrage.

Ausführliche Informationen zu Datenverkehr und Bandbreite für die einzelnen Regionen werden direkt unterhalb der Karte bereitgestellt. Dadurch können Sie für jede Region die Gesamtanzahl von Treffern, den Prozentsatz der Treffer, die Gesamtmenge der übertragenen Daten (in GB) sowie den Prozentsatz der übertragenen Daten anzeigen. Zeigen Sie eine Beschreibung für die einzelnen Metriken an. Wenn Sie auf eine Region (d. h. ein Land bzw. eine Region, ein Bundesland oder eine Provinz) zeigen, werden der Name und der Prozentsatz der Treffer in dieser Region als QuickInfo angezeigt.

Nachfolgend finden Sie eine kurze Beschreibung der einzelnen kartenbasierten geografischen Berichte:

| Berichtsname | BESCHREIBUNG |
| --- | --- |
| Weltkarte |Mit diesem Bericht können Sie die weltweite Nachfrage nach Ihren CDN-Inhalten anzeigen. Auf der Weltkarte ist jedes Land bzw. jede Region farbkodiert, um den Prozentsatz der Treffer aus der jeweiligen Region anzugeben. |
| Karte der USA |Mit diesem Bericht können Sie die Nachfrage nach Ihren CDN-Inhalten in den USA anzeigen. Auf dieser Karte ist jeder Bundesstaat farbkodiert, um den Prozentsatz der Treffer aus der jeweiligen Region anzugeben. |
| Karte von Kanada |Mit diesem Bericht können Sie die Nachfrage nach Ihren CDN-Inhalten in Kanada anzeigen. Auf dieser Karte ist jede Provinz farbkodiert, um den Prozentsatz der Treffer aus der jeweiligen Region anzugeben. |
| Europakarte |Mit diesem Bericht können Sie die Nachfrage nach Ihren CDN-Inhalten in Europa anzeigen. Auf dieser Karte ist jedes Land bzw. jede Region farbkodiert, um den Prozentsatz der Treffer aus der jeweiligen Region anzugeben. |
| Asien-Pazifik-Karte |Mit diesem Bericht können Sie die Nachfrage nach Ihren CDN-Inhalten in Asien anzeigen. Auf dieser Karte ist jedes Land bzw. jede Region farbkodiert, um den Prozentsatz der Treffer aus der jeweiligen Region anzugeben. |

## <a name="geography-reports-bar-charts"></a>Geografische Berichte (Balkendiagramme)
Es gibt zwei weitere Berichte mit statistischen Informationen nach Geografie: „Führende Städte“ und „Führende Länder“. In diesem Berichten sind jeweils Städte und Länder/Regionen gemäß der Anzahl von Treffern aus den jeweiligen Ländern/Regionen klassifiziert. Bei diesem Berichtstyp werden in einem Balkendiagramm die zehn führenden Städte oder Länder/Regionen angegeben, die Inhalte über eine bestimmte Plattform angefordert haben. Mit diesem Balkendiagramm können Sie schnell die Regionen ermitteln, die die höchste Anzahl von Anforderungen für Ihre Inhalte generieren.

Die linke Seite des Graphen (y-Achse) gibt an, wie viele Treffer in der angegebenen Region auftraten. Direkt unterhalb des Graphen (x-Achse) befindet sich eine Bezeichnung für jede der zehn führenden Regionen.

### <a name="using-the-bar-charts"></a>Verwenden der Balkendiagramme
* Wenn Sie auf einen Balken zeigen, werden der Name und die Gesamtanzahl von Treffern in dieser Region als QuickInfo angezeigt.
* Die QuickInfo für den Bericht „Führende Städte“ bestimmt eine Stadt nach Name, Bundesland/Provinz und Abkürzung für Land/Region.
* Falls die Stadt oder die Region (d. h. das Bundesland/die Provinz), aus der eine Anforderung stammt, nicht ermittelt werden konnte, wird sie als unbekannt angegeben. Wenn das Land bzw. die Region unbekannt ist, werden zwei Fragezeichen („??“) angezeigt.
* Ein Bericht kann Metriken für Europa oder die Asien-Pazifik-Region beinhalten. Mit diesen Elementen sollen keine statistischen Informationen zu allen IP-Adressen in diesen Regionen angegeben werden. Vielmehr gelten sie nur für Anforderungen, die von IP-Adressen in Europa oder der Asien-Pazifik-Region und nicht von IP-Adressen in einem bestimmten Ort oder Land bzw. in einer bestimmten Region stammen.

Die dem Balkendiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort sehen Sie die Gesamtanzahl von Treffern, den Prozentsatz der Treffer, die übertragene Datenmenge (in GB) sowie den Prozentsatz der übertragenen Daten für die 250 führenden Regionen. Zeigen Sie eine Beschreibung für die einzelnen Metriken an.

Nachfolgend finden Sie eine kurze Beschreibung der beiden Berichtstypen:

| Berichtsname | BESCHREIBUNG |
| --- | --- |
| Führende Städte |In diesem Bericht werden Städte entsprechend der Anzahl von Treffern aus dieser Region klassifiziert. |
| Führende Länder |In diesem Bericht werden Länder/Regionen entsprechend der Anzahl von Treffern aus diesem Land bzw. dieser Region klassifiziert. |

## <a name="daily-summary"></a>Tägliche Zusammenfassung
Mit dem Bericht „Tägliche Zusammenfassung“ können Sie auf täglicher Basis die Gesamtanzahl von Treffern und über eine bestimmte Plattform übertragene Daten anzeigen. Anhand dieser Informationen werden bestimmte CDN-Aktivitätsmuster schnell erkannt. Mithilfe dieses Berichts können Sie beispielsweise ermitteln, an welchen Tagen der Datenverkehr höher oder niedriger als erwartet ausgefallen ist.

Bei diesem Berichtstyp wird anhand eines Balkendiagramms der tägliche Umfang der plattformspezifischen Nachfrage in dem vom Bericht abgedeckten Zeitraum visuell dargestellt. Dazu werden im Bericht für die einzelnen Tage Balken angezeigt. Bei Auswahl des Zeitraums „Letzte Woche“ wird beispielsweise ein Balkendiagramm mit sieben Balken erstellt. Jeder Balken zeigt jeweils die Gesamtanzahl von Treffern an dem entsprechenden Tag an.

Die linke Seite des Graphen (y-Achse) gibt an, wie viele Treffer am angegebenen Datum auftraten. Direkt unterhalb des Graphen (x-Achse) befindet sich eine Bezeichnung, die das Datum (Format: JJJJ-MM-TT) für jeden im Bericht enthaltenen Tag angibt.

> [!TIP]
> Wenn Sie auf einen Balken zeigen, wird die Gesamtanzahl von Treffern an diesem Datum als QuickInfo angezeigt.
> 
> 

Die dem Balkendiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort finden Sie die Gesamtanzahl von Treffern und die Menge der übertragenen Daten (in GB) für alle vom Bericht abgedeckten Tage.

## <a name="by-hour"></a>Nach Stunde
Mit dem Bericht „Nach Stunde“ können Sie die Gesamtanzahl von Treffern und über eine bestimmte Plattform übertragene Daten auf Stundenbasis anzeigen. Anhand dieser Informationen werden bestimmte CDN-Aktivitätsmuster schnell erkannt. Mithilfe dieses Berichts können Sie beispielsweise die Zeiträume während des Tages ermitteln, in denen der Datenverkehr höher oder niedriger als erwartet ausfällt.

Bei diesem Berichtstyp wird anhand eines Balkendiagramms der Umfang der plattformspezifischen Nachfrage pro Stunde in dem vom Bericht abgedeckten Zeitraum visuell dargestellt. Dazu werden für die einzelnen im Bericht abgedeckten Stunden Balken angezeigt. Bei Auswahl eines 24-Stunden-Zeitraums wird beispielsweise ein Balkendiagramm mit 24 Balken erstellt. Jeder Balken zeigt jeweils die Gesamtanzahl von Treffern während der entsprechenden Stunde an.

Die linke Seite des Graphen (y-Achse) gibt an, wie viele Treffer in der angegebenen Stunde auftraten. Direkt unterhalb des Graphen (x-Achse) befindet sich eine Bezeichnung, die das Datum und die Uhrzeit (Format: JJJJ-MM-TT hh:mm) für jede im Bericht enthaltene Stunde angibt. Die Zeit wird im 24-Stunden-Format (Zeitzone: UTC/GMT) angegeben.

> [!TIP]
> Wenn Sie auf einen Balken zeigen, wird die Gesamtanzahl von Treffern in dieser Stunde als QuickInfo angezeigt.
> 
> 

Die dem Balkendiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort finden Sie die Gesamtanzahl von Treffern und die Menge der übertragenen Daten (in GB) für alle vom Bericht abgedeckten Stunden.

## <a name="by-file"></a>Nach Datei
Mit dem Bericht „Nach Datei“ können Sie den Umfang der Nachfrage und den über eine bestimmte Plattform übertragenen Datenverkehr für die am häufigsten angeforderten Assets anzeigen. Bei diesem Berichtstyp wird ein Balkendiagramm für die zehn Assets erstellt, die im angegebenen Zeitraum am häufigsten angefordert wurden.

> [!NOTE]
> Für diesen Bericht werden Edge-CNAME-URLs in ihre entsprechenden CDN-URLs konvertiert. Dadurch wird unabhängig von der für die Anforderung verwendeten CDN- oder Edge-CNAME-URL ein präziser Wert für die Gesamtanzahl von Treffern für ein Asset ermittelt.
> 
> 

Die linke Seite des Graphen (y-Achse) gibt die Anzahl von Anforderungen für jedes Asset im angegebenen Zeitraum an. Direkt unterhalb des Graphen (x-Achse) befindet sich eine Bezeichnung mit dem Dateinamen für die zehn Assets, die am häufigsten angefordert wurden.

Die dem Balkendiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort sehen Sie die folgenden Informationen für die 250 Assets, die am häufigsten angefordert wurden: relativer Pfad, Gesamtanzahl von Treffern, Prozentsatz der Treffer, übertragene Datenmenge (in GB) und Prozentsatz der übertragenen Daten.

## <a name="by-file-detail"></a>Nach Dateidetails
Mit dem Bericht „Nach Dateidetails“ können Sie den Umfang der Nachfrage und den über eine bestimmte Plattform übertragenen Datenverkehr für ein bestimmtes Asset anzeigen. Ganz oben im Bericht befindet sich die Option „Dateidetails für“. Mit dieser Option wird eine Liste der am häufigsten angeforderten Assets für die ausgewählte Plattform angezeigt. Um den Bericht „Nach Dateidetail“ zu erstellen, wählen Sie das gewünschte Asset über die Option „Dateidetails für“ aus. Anschließend wird mithilfe eines Balkendiagramms der Umfang der täglichen Nachfrage angegeben, die im Laufe des angegebenen Zeitraums entstand.

Die linke Seite des Graphen (y-Achse) gibt die Gesamtanzahl von Anforderungen für ein Asset an einem bestimmten Tag an. Direkt unterhalb des Graphen (x-Achse) befindet sich eine Bezeichnung mit dem Datum (Format: JJJJ-MM-TT), für das die CDN-Nachfrage für das Asset gemeldet wurde.

Die dem Balkendiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort finden Sie die Gesamtanzahl von Treffern und die Menge der übertragenen Daten (in GB) für alle vom Bericht abgedeckten Tage.

## <a name="by-file-type"></a>Nach Dateityp
Im Bericht „Nach Dateityp“ können Sie den Umfang der Nachfrage und den Datenverkehr nach Dateityp anzeigen. Bei diesem Berichtstyp wird anhand eines Ringdiagramms der Prozentsatz der Treffer angegeben, die von den zehn führenden Dateitypen generiert wurden.

> [!TIP]
> Wenn Sie auf ein Segment im Ringdiagramm zeigen, wird der Internetmedientyp des Dateityps als QuickInfo angezeigt.
> 
> 

Die dem Ringdiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort sehen Sie die Dateinamenerweiterung/den Internetmedientyp, die Gesamtanzahl von Treffern, den Prozentsatz der Treffer, die übertragene Datenmenge (in GB) sowie den Prozentsatz der übertragenen Daten für jeden der 250 führenden Dateitypen.

## <a name="by-directory"></a>Nach Verzeichnis
Mit dem Bericht „Nach Verzeichnis“ können Sie den Umfang der Nachfrage und den über eine bestimmte Plattform übertragenen Datenverkehr für Inhalte aus einem bestimmten Verzeichnis anzeigen. Bei diesem Berichtstyp wird anhand eines Balkendiagramms die Gesamtanzahl von Treffern angegeben, die von den Inhalten in den zehn führenden Verzeichnissen generiert wurden.

### <a name="using-the-bar-chart"></a>Verwenden des Balkendiagramms
* Zeigen Sie auf einen Balken, um den relativen Pfad zum entsprechenden Verzeichnis anzuzeigen.
* In einem Unterordner eines Verzeichnisses gespeicherte Inhalte werden beim Berechnen der Nachfrage nach Verzeichnis nicht berücksichtigt. Diese Berechnung basiert ausschließlich auf der Anzahl von Anforderungen, die für die im entsprechenden Verzeichnis gespeicherten Inhalte generiert wurden.
* Für diesen Bericht werden Edge-CNAME-URLs in ihre entsprechenden CDN-URLs konvertiert. Dadurch wird unabhängig von der für die Anforderung verwendeten CDN- oder Edge-CNAME-URL ein präziser Wert für alle Statistiken für ein Asset ermittelt.

Die linke Seite des Graphen (y-Achse) gibt die Gesamtanzahl von Anforderungen für die in den zehn führenden Verzeichnissen gespeicherten Inhalte an. Jeder Balken im Diagramm stellt ein Verzeichnis dar. Weisen Sie mithilfe von Farbkodierungen einen Balken einem Verzeichnis zu, das im Abschnitt „250 führende vollständige Verzeichnisse“ aufgeführt ist.

Die dem Balkendiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort sehen Sie die folgenden Informationen für die 250 führenden Verzeichnisse: relativer Pfad, Gesamtanzahl von Treffern, Prozentsatz der Treffer, übertragene Datenmenge (in GB) und Prozentsatz der übertragenen Daten.

## <a name="by-browser"></a>Nach Browser
Mit dem Bericht „Nach Browser“ können Sie anzeigen, welche Browser zum Anfordern von Inhalten verwendet wurden. Bei diesem Berichtstyp wird anhand eines Kreisdiagramms der Prozentsatz der Anforderungen angegeben, die von den zehn führenden Browsern verarbeitet wurden.

### <a name="using-the-pie-chart"></a>Verwenden des Kreisdiagramms
* Zeigen Sie auf ein Segment im Kreisdiagramm, um den Namen und die Version eines Browsers anzuzeigen.
* Für diesen Bericht werden die verschiedenen Kombinationen aus Browser und Version jeweils als unterschiedliche Browser betrachtet.
* Das Segment „Andere“ gibt den Prozentsatz der Anforderungen an, die von allen anderen Browsern und Versionen verarbeitet wurden.

Die dem Kreisdiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort finden Sie den Browsertyp/die Versionsnummer, die Gesamtanzahl von Treffern und den Prozentsatz der Treffer für jeden der 250 führenden Browser.

## <a name="by-referrer"></a>Nach Verweis
Mit dem Bericht „Nach Verweis“ können Sie die führenden Verweise auf Inhalte für die ausgewählte Plattform anzeigen. Ein Verweis gibt den Hostnamen an, von dem eine Anforderung generiert wurde. Bei diesem Berichtstyp wird anhand eines Balkendiagramms der Umfang der Nachfrage (d. h. die Treffer) angegeben, die von den zehn führenden Verweisen generiert wurden.

Die linke Seite des Graphen (y-Achse) gibt die Gesamtanzahl von Anforderungen für ein Asset für jeden Verweis an. Jeder Balken im Diagramm stellt einen Verweis dar. Weisen Sie mithilfe von Farbkodierungen einen Balken einem Verweis zu, der im Abschnitt „250 führende Verweise“ aufgeführt ist.

Die dem Balkendiagramm zugrunde liegenden Daten werden unterhalb des Diagramms angezeigt. Dort finden Sie die URL, die Gesamtanzahl von Treffern und den Prozentsatz der Treffer, die von jedem der 250 führenden Verweise generiert wurden.

## <a name="by-download"></a>Nach Download
Mit dem Bericht „Nach Download“ können Sie Downloadmuster für die am häufigsten angeforderten Inhalte analysieren. Ein Balkendiagramm oben im Bericht vergleicht für die zehn am häufigsten abgerufenen Assets die versuchten Downloads mit den abgeschlossenen Downloads. Jeder Balken ist farbkodiert: Versuchte Downloads sind blau und abgeschlossene Downloads sind grün gekennzeichnet.

> [!NOTE]
> Für diesen Bericht werden Edge-CNAME-URLs in ihre entsprechenden CDN-URLs konvertiert. Dadurch wird unabhängig von der für die Anforderung verwendeten CDN- oder Edge-CNAME-URL ein präziser Wert für alle Statistiken für ein Asset ermittelt.
> 
> 

Die linke Seite des Graphen (y-Achse) gibt den Dateinamen für jedes der zehn am häufigsten abgerufenen Assets an. Direkt unterhalb des Graphen (x-Achse) befinden sich Bezeichnungen mit der Gesamtanzahl von versuchten/abgeschlossenen Downloads.

Direkt unterhalb des Balkendiagramms werden die folgenden Informationen für die 250 Assets aufgeführt, die am häufigsten angefordert wurden: relativer Pfad (einschließlich Dateiname), Anzahl von abgeschlossenen Downloads, Anzahl von Anforderungen und Prozentsatz der Anforderungen, die zu abgeschlossenen Downloads führten.

> [!TIP]
> Unser CDN wird nicht von einem HTTP-Client (d. h. Browser) informiert, wenn ein Asset vollständig heruntergeladen wurde. Daher muss anhand von Statuscodes und Bytebereichsanfragen ermittelt werden, ob ein Asset vollständig heruntergeladen wurde. Dabei wird zunächst überprüft, ob die Anforderung den Statuscode „200 OK“ aufweist. Wenn dies der Fall ist, wird mithilfe von Bytebereichsanfragen sichergestellt, dass das gesamte Asset abgedeckt ist. Abschließend wird die übertragene Datenmenge mit der Größe des angeforderten Assets verglichen. Wenn die übertragene Datenmenge größer oder gleich der Dateigröße ist und die Bytebereichsanfragen dem Asset entsprechen, wird der Treffer als vollständiger Download gezählt.
> 
> Berücksichtigen Sie aufgrund des Interpretationsspielraums bei diesem Bericht die folgenden Punkte, die sich auf Konsistenz und Genauigkeit des Berichts auswirken können:
> 
> * Datenverkehrsmuster können bei abweichendem Verhalten der Benutzer-Agents nicht präzise vorhergesagt werden. Dadurch können für abgeschlossene Downloads Ergebnisse von über 100 Prozent erzielt werden
> * Assets, die den progressiven Download über HTTP nutzen, können von diesem Bericht unter Umständen nicht exakt dargestellt werden. Das liegt daran, dass Benutzer verschiedene Stellen in einem Video suchen.
> 
> 

## <a name="by-404-errors"></a>Nach 404-Fehlern
Im Bericht „Nach 404-Fehlern“ können Sie den Inhaltstyp anzeigen, der am häufigsten den Statuscode „404 Nicht gefunden“ generiert. Oben im Bericht ist ein Balkendiagramm für die zehn führenden Assets enthalten, für die der Statuscode „404 Nicht gefunden“ zurückgegeben wurde. In diesem Balkendiagramm wird die Gesamtanzahl von Anforderungen mit den Anforderungen verglichen, die zum Statuscode „404 Nicht gefunden“ für diese Assets führten. Jeder Balken ist farbkodiert. Mit einem gelben Balken wird angegeben, dass für eine Anforderung der Statuscode „404 Nicht gefunden“ zurückgegeben wurde. Ein roter Balken gibt die Gesamtanzahl von Anforderungen für das Asset an.

> [!NOTE]
> Beachten Sie bei diesem Bericht Folgendes:
> 
> * Ein Treffer stellt alle Anforderungen eines Assets unabhängig vom Statuscode dar.
> * Edge-CNAME-URLs werden in ihre entsprechenden CDN-URLs konvertiert. Dadurch wird unabhängig von der für die Anforderung verwendeten CDN- oder Edge-CNAME-URL ein präziser Wert für alle Statistiken für ein Asset ermittelt.
> 
> 

Die linke Seite des Graphen (y-Achse) gibt den Dateinamen für jedes der zehn am häufigsten abgerufenen Assets an, für die der Statuscode „404 Nicht gefunden“ zurückgegeben wurde. Direkt unterhalb des Graphen (x-Achse) befinden sich Bezeichnungen mit der Gesamtanzahl von Anforderungen und der Anzahl von Anforderungen, für die der Statuscode „404 Nicht gefunden“ zurückgegeben wurde.

Direkt unterhalb des Balkendiagramms werden die folgenden Informationen für die 250 Assets aufgeführt, die am häufigsten angefordert wurden: relativer Pfad (einschließlich Dateiname), Anzahl von Anforderungen mit dem Statuscode „404 Nicht gefunden“, die Gesamtanzahl von Anforderungen für ein Asset und der Prozentsatz der Anforderungen, für die der Statuscode „404 Nicht gefunden“ zurückgegeben wurde.

## <a name="see-also"></a>Weitere Informationen
* [Übersicht über das Azure CDN](cdn-overview.md)
* [Echtzeitstatistiken in Microsoft Azure CDN](cdn-real-time-stats.md)
* [Überschreiben des HTTP-Standardverhaltens mithilfe der Regel-Engine](./cdn-verizon-premium-rules-engine.md)
* [Analysieren der Edgeleistung](cdn-edge-performance.md)