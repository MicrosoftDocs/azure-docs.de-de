---
title: Bewährte Methoden für den Azure Maps-Routendienst in Microsoft Azure Maps
description: Erfahren Sie, wie Sie mit dem Routendienst von Microsoft Azure Maps Fahrzeuge effizient leiten.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 743710ea0d40eb31375236d4e59b0b138a217518
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895544"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Bewährte Methoden für den Azure Maps-Routendienst

Die Wegbeschreibungs- und Routenmatrix-APIs im [Routendienst](/rest/api/maps/route) von Azure Maps können verwendet werden, um die geschätzten Ankunftszeiten (Estimated Arrival Times, ETAs) für jede angeforderte Wegstrecke zu berechnen. Die Wegbeschreibungs-APIs berücksichtigen Faktoren wie Echtzeit-Verkehrsinformationen und Verkehrsdaten aus der Vergangenheit, wie etwa die typischen Reisegeschwindigkeiten am angeforderten Wochentag zur angeforderten Tageszeit. Die APIs geben die kürzesten oder schnellsten verfügbaren Routen zu mehreren Zielen zugleich in der Reihenfolge der Eingabe oder in einer für Zeit oder Entfernung optimierten Reihenfolge zurück. Benutzer können darüber hinaus auch spezielle Routen und Details für Wanderer, Radfahrer und Nutzfahrzeuge anfordern, wie etwa LKWs. In diesem Artikel werden die bewährten Methoden zum Aufrufen des Azure Maps-[Routendiensts](/rest/api/maps/route) erläutert, und Sie erfahren, wie Sie die folgenden Schritte ausführen:

 * Wählen zwischen den Wegbeschreibungs-APIs und der Matrix-Routenplanungs-API
 * Anfordern von zurückliegenden und vorhergesagten Reisezeiten, basierend auf Echtzeitdaten und Verkehrsdaten aus der Vergangenheit
 * Anfordern von Routendetails, wie Zeit und Entfernung, für die gesamte Route und jede Teilstrecke der Route
 * Anfordern der Route für ein Nutzfahrzeug, z. B. einen LKW
 * Anfordern von Verkehrsinformationen für eine Route, wie Staus und Mautinformationen
 * Anfordern einer Route, die aus einem oder mehreren Stopps (Wegpunkten) besteht
 * Optimieren einer Route mit einem oder mehreren Stopps, um die beste Reihenfolge für den Besuch der einzelnen Stopps (Wegpunkte) zu erhalten.
 * Optimieren alternativer Routen mithilfe von Unterstützungspunkten. Beispielsweise können alternative Routen angeboten werden, die eine Ladestation für Elektromobilität passieren.
 * Verwenden des [Routendiensts](/rest/api/maps/route) mit dem Azure Maps-Web-SDK

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

Weitere Informationen zur Abdeckung des Routendiensts finden Sie unter [Abgedeckter Routingbereich](routing-coverage.md).

In diesem Artikel wird die [Postman-App](https://www.postman.com/downloads/) zum Generieren von REST-Aufrufen verwendet, Sie können aber eine beliebige API-Entwicklungsumgebung verwenden.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Auswählen zwischen Wegbeschreibungen und Matrixrouting

Die Wegbeschreibungs-APIs geben Anweisungen einschließlich der Reisezeit und der Koordinaten für einen Routenpfad zurück. Mithilfe der Routenmatrix-API können Sie Reisezeit und Entfernungen für eine Reihe von Routen berechnen, die durch die Ausgangs- und die Zielorte definiert sind. Für jeden angegebenen Ausgangsort berechnet die Matrix-API die Kosten (Reisezeit und Entfernung) der Wegführung von diesem Ausgangsort an jedes angegebene Ziel. Bei all diesen APIs können Sie Parameter angeben, wie etwa die gewünschte Aufbruchzeit, die Ankunftszeiten und den Fahrzeugtyp, z. B. Auto oder LKW. Sie alle nutzen entsprechend Echtzeit- oder vorhergesagte Verkehrsdaten, um die optimalen Routen zurückzugeben.

Erwägen Sie den Aufruf von Wegbeschreibungs-APIs, wenn Ihr Szenario diese Aktionen vorsieht:

* Anfordern der kürzesten oder schnellsten Fahrstrecke zwischen zwei oder mehr bekannten Orten, um präzise Ankunftszeiten für Ihre Lieferfahrzeuge zu erhalten.
* Anfordern der detaillierten Streckenführung, einschließlich der Streckengeometrie, um Routen auf der Karte darzustellen
* Berechnen der kürzesten möglichen Route für eine bestehende Liste von Kundenstandorten, wenn jeder Kundenstandort besucht und auf kürzestem Weg zum Ausgangsort zurückgekehrt werden soll. Dieses Szenario wird allgemein als Problem des Handlungsreisenden bezeichnet. Sie können in einer Anforderung bis zu 150 Wegpunkte (Stopps) übergeben.
* Senden von Batches von Abfragen an die Wegbeschreibungs-Batch-API mit einem einzelnen API-Aufruf.

Erwägen Sie den Aufruf der Matrixrouting-API, wenn Ihr Szenario dies vorsieht:

* Berechnen der Reisezeit oder Entfernung zwischen einer Menge von Ausgangsorten und Zielen. Nehmen Sie beispielsweise an, Sie verfügen über 12 Fahrer und müssen den nächstgelegenen verfügbaren Fahrer ermitteln, um Essen in einem Restaurant abzuholen.
* Sortieren möglicher Routen nach ihrer tatsächlichen Reiseentfernung oder Reisezeit. Die Matrix-API gibt nur Reisezeiten und Entfernungen für jede Kombination aus Ausgangspunkt und Ziel zurück.
* Gruppieren von Daten auf der Grundlage von Reisezeit oder Entfernung. Angenommen, Ihr Unternehmen beschäftigt 50 Mitarbeiter: Es sollen alle Mitarbeiter ermittelt werden, die innerhalb eines Radius von 20 Autominuten vom Büro entfernt wohnen.

Dieser Vergleich zeigt einige Funktionen der Wegbeschreibungs- und Matrix-APIs:

| Azure Maps-API | Maximale Anzahl Abfragen in der Anforderung | Vermeiden von Gebieten | Wegführung für LKW und Elektromobile | Optimierung für Wegpunkte und Handlungsreisenden-Problem | Unterstützungspunkte |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Abrufen von Wegbeschreibungen | 1 | | X | X | |
| Veröffentlichen von Wegbeschreibungen | 1 | X | X | X | X |
| Batchveröffentlichung von Wegbeschreibungen | 700 | | X | X | |
| Veröffentlichen der Routenmatrix | 700 | | X | | |

Weitere Informationen zu den Funktionen für die Wegführung von Elektrofahrzeugen finden Sie in unserem Tutorial zur [Wegführung von Elektrofahrzeugen mithilfe von Azure Notebooks mit Python](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Anfordern von historischen und Echtzeitdaten

Standardmäßig geht der Routendienst vom Transportmittel Auto und der Abfahrtszeit jetzt aus. Er gibt eine Route auf der Grundlage von Echtzeit-Verkehrsbedingungen aus, sofern in der Anforderung zur Routenberechnung keine anderen Angaben gemacht wurden. Festgelegte zeitabhängige Verkehrsbeschränkungen, wie „Kein Linksabbiegen zwischen 16:00 und 18:00 Uhr“, werden erfasst und von der Routingengine berücksichtigt. Straßenschließungen, etwa aufgrund von Straßenbauarbeiten, werden berücksichtigt, sofern Sie nicht spezifisch eine Route anfordern, für die die aktuellen Live-Verkehrsinformationen ignoriert werden. Um den aktuellen Verkehr zu ignorieren, legen Sie `traffic` in Ihrer API-Anforderung auf `false` fest.

Der Wert **travelTimeInSeconds** beinhaltet die Verzögerung aufgrund des Verkehrs. Er wird unter Nutzung der aktuellen und historischen Daten zur Reisezeit generiert, wenn die Abfahrtszeit auf „Jetzt“ festgelegt ist. Wenn Ihre Abfahrtszeit in der Zukunft liegt, geben die APIs Vorhersagen der Reisezeiten auf der Grundlage historischer Daten zurück.

Wenn Sie den Parameter **computeTravelTimeFor=all** in Ihre Anforderung einschließen, weist das Zusammenfassungselement in der Antwort die folgenden zusätzlichen Felder einschließlich der historischen Verkehrsbedingungen auf:

| Element | BESCHREIBUNG|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Geschätzte Reisezeit, berechnet ohne Berücksichtigung von Verzögerungen aufgrund der Verkehrsbedingungen, beispielsweise Staus |
| historicTrafficTravelTimeInSeconds | Geschätzte Reisezeit, auf der Grundlage zeitabhängiger historischer Verkehrsdaten berechnet |
| liveTrafficIncidentsTravelTimeInSeconds | Geschätzte Reisezeit, auf der Grundlage von Echtzeit-Geschwindigkeitsdaten berechnet |

In den folgenden Abschnitten wird gezeigt, wie Sie mithilfe der erörterten Parameter Aufrufe der Routen-APIs vornehmen.

### <a name="sample-query"></a>Beispielabfrage

Im ersten Beispiel unten wird die Abfahrtzeit auf eine Zeit in der Zukunft festgelegt, gemessen am Entstehungszeitpunkt dieses Artikels.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

Die Antwort enthält ein Zusammenfassungselement, wie unten dargestellt. Da die Abfahrtszeit auf einen zukünftigen Zeitpunkt festgelegt ist, ist der Wert von **trafficDelayInSeconds** gleich 0 (null). Der Wert von **travelTimeInSeconds** wird anhand zeitabhängiger historischer Verkehrsdaten berechnet. In diesem Fall ist daher der Wert von **travelTimeInSeconds** gleich dem Wert von **historicTrafficTravelTimeInSeconds** .

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Beispielabfrage

Im zweiten Beispiel unten haben wir eine Echtzeit-Wegführungsanforderung mit der Abfahrtszeit „Jetzt“. Dies wird in der URL nicht explizit angegeben, da es den Standardwert darstellt.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

Die Antwort enthält eine Zusammenfassung, wie unten dargestellt. Aufgrund von Staus ist der Wert von **trafficDelaysInSeconds** größer als null. Er ist außerdem größer als **historicTrafficTravelTimeInSeconds** .

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Anfordern von Routen- und Teilstreckendetails

Standardmäßig gibt der Routendienst ein Array von Koordinaten zurück. Die Antwort enthält die Koordinaten, aus denen sich der Pfad in einer Liste mit der Bezeichnung `points` zusammensetzt. Die Routenantwort schließt darüber hinaus die Entfernung vom Anfang der Route und die geschätzte verstrichene Zeit ein. Diese Werte können verwendet werden, um die Durchschnittsgeschwindigkeit für die gesamte Route zu berechnen.

Die folgende Abbildung stellt das `points`-Element dar.

![Points-Element](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

Erweitern Sie das `point`-Element, um die Liste der Koordinaten für den Weg anzuzeigen:

![Erweitertes Points-Element](media/how-to-use-best-practices-for-routing/points-list-img.png)

Die Wegbeschreibungs-APIs unterstützen verschiedene Formate von Anweisungen, die durch Angeben des **instructionsType** -Parameters verwendet werden können. Um Anweisungen für die einfache Verarbeitung im Computer zu formatieren, verwenden Sie **instructionsType=coded** . Verwenden Sie **instructionsType=tagged** , um die Anweisungen als Text für den Benutzer anzuzeigen. Ferner können Anweisungen als Text formatiert werden, wobei einige Elemente der Anweisungen gekennzeichnet werden und die Anweisung mit einer besonderen Formatierung dargestellt wird. Weitere Informationen finden Sie unter [Liste der unterstützten Anweisungstypen](/rest/api/maps/route/postroutedirections#routeinstructionstype).

Wenn Anweisungen angefordert werden, gibt die Antwort ein neues Element mit dem Namen `guidance` zurück. Das `guidance`-Element enthält zwei Informationsarten: Streckenabschnitts-Wegbeschreibungen und zusammengefasste Anweisungen.

![Anweisungstyp](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

Das `instructions`-Element enthält Streckenabschnitts-Wegbeschreibungen für die Reise, und `instructionGroups` enthält zusammengefasste Anweisungen. Jede Anweisungszusammenfassung deckt ein Segment der Reise ab, das mehrere Straßen umfassen kann. Die APIs können Details für die Abschnitte einer Route zurückgeben, z. B. den Koordinatenbereich eines Verkehrsstaus oder die aktuelle Geschwindigkeit des fließenden Verkehrs.

![Streckenabschnittsanweisungen](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![Zusammengefasste Anweisungen](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>Anfordern einer Route für ein Nutzfahrzeug

Azure Maps Routing-APIs unterstützen die Wegführung von Nutzfahrzeugen, einschließlich der Wegführung für LKWs. Die APIs berücksichtigen die festgelegten Grenzwerte. Beispielsweise Höhe und Gewicht des Fahrzeugs und ob das Fahrzeug Gefahrgut transportiert. Wenn ein Fahrzeug z. B. brennbare Stoffe transportiert, vermeidet die Wegführungsengine bestimmte Tunnel, die sich in der Nähe von Wohngebieten befinden.

### <a name="sample-query"></a>Beispielabfrage

Mit der folgenden Beispielanforderung wird eine Route für einen Gewerbe-LKW abgefragt. Der LKW transportiert gefährliches Abfallgut der Klasse 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Die Routen-API gibt Wegbeschreibungen zurück, die die Abmessungen des LKWs und das Gefahrgut berücksichtigen. Sie können die Wegbeschreibungen lesen, indem Sie das `guidance`-Element erweitern.

![LKW mit Gefahrgut der Klasse 1](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>Beispielabfrage

Das Ändern der US-Gefahrgutklasse in der Abfrage oben führt zu einer anderen Wegstrecke, um der Änderung Rechnung zu tragen.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Die Antwort unten bezieht sich auf einen LKW, der Gefahrgut der Klasse 9 transportiert, das weniger gefährlich ist als Gefahrgut der Klasse 1. Wenn Sie das `guidance` Element erweitern, um die Anweisungen zu lesen, werden Sie bemerken, dass die Anweisungen nicht gleich sind. Es gibt mehr Streckenanweisungen für den LKW, der das Gefahrgut der Klasse 1 befördert.



![LKW mit Gefahrgut der Klasse 9](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>Anfordern von Verkehrsinformationen auf einer Route

Bei den Azure Maps-Wegbeschreibungs-APIs können Entwickler Details zu jedem Abschnittstyp anfordern, indem sie den `sectionType`-Parameter in die Anforderung einschließen. Beispielsweise können Sie Geschwindigkeitsinformationen für jeden Streckenabschnitt mit zähflüssigem Verkehr anfordern. Informationen zu den verschiedenen Details, die Sie anfordern können, finden Sie in der [Wertliste für den sectionType-Schlüssel](/rest/api/maps/route/getroutedirections#sectiontype).

### <a name="sample-query"></a>Beispielabfrage

Die folgende Abfrage legt den `sectionType` auf `traffic` fest. Sie fordert die Abschnitte an, die Verkehrsinformationen von Seattle nach San Diego enthalten.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

Die Antwort enthält die Abschnitte, die entlang der angegebenen Koordinaten hinsichtlich der Verkehrsverhältnisse geeignet sind.

![Verkehrsabschnitte](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

Diese Option kann verwendet werden, um die Abschnitte beim Rendern der Karte einzufärben, wie in der folgenden Abbildung dargestellt: 

![Auf der Karte gerenderte farbige Abschnitte](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Berechnen und Optimieren einer Route mit mehreren Stopps

Azure Maps bietet derzeit zwei Formen von Routenoptimierung:

* Optimierungen, die auf dem angeforderten Routentyp basieren, ohne die Reihenfolge der Wegpunkte zu ändern. Sie finden die [unterstützten Routentypen hier](/rest/api/maps/route/postroutedirections#routetype)

* Handlungsreisender-Optimierung, bei der die Reihenfolge der Wegpunkte geändert wird, um die beste Reihenfolge zum Aufsuchen aller Stopps zu erhalten

Für die Routenplanung mit mehreren Stopps können bis zu 150 Wegpunkte in einer einzelnen Routenanforderung angegeben werden. Die Koordinaten von Ausgangs- und Zielort können gleich sein, wie es bei einer Rundreise der Fall ist. Sie müssen aber zumindest einen weiteren Wegpunkt angeben, damit die Routenberechnung erfolgen kann. Wegpunkte können der Abfrage zwischen den Ausgangs- und Zielkoordinaten hinzugefügt werden.

Wenn Sie die Reihenfolge zum Aufsuchen der angegebenen Wegpunkte optimieren möchten, müssen Sie **computeBestOrder=true** angeben. Dieses Szenario wird auch als Optimierungsproblem des Handlungsreisenden bezeichnet.

### <a name="sample-query"></a>Beispielabfrage

Die folgende Abfrage fordert den Pfad für sechs Wegpunkte an, mit auf `false` festgelegtem `computeBestOrder`-Parameter. Dies ist auch der Standardwert für den `computeBestOrder`-Parameter.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Die Antwort beschreibt die Länge des Wegs mit 140.851 Metern und gibt an, dass das Zurücklegen dieser Strecke 9.991 Sekunden erfordern würde.

![Nicht optimierte Antwort](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

Die folgende Abbildung zeigt die Route, die sich aus dieser Abfrage ergibt. Dies ist eine der möglichen Routen. Hinsichtlich Zeit oder Entfernung ist es nicht die optimale Route.

![Nicht optimierte Abbildung](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



Die Reihenfolge der Wegpunkte ist: 0, 1, 2, 3, 4, 5 und 6.

### <a name="sample-query"></a>Beispielabfrage

Die folgende Abfrage fordert den Pfad für die gleichen sechs Wegpunkte wie im Beispiel oben an. Dieses Mal wird der `computeBestOrder`-Parameter auf `true` festgelegt (Handlungsreisenden-Optimierung).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Die Antwort beschreibt die Länge des Wegs mit 91.814 Metern und gibt an, dass das Zurücklegen dieser Strecke 7.797 Sekunden erfordern würde. Sowohl Reiseentfernung als auch Reisezeit sind hier geringer, da die API die optimierte Route zurückgegeben hat.

![Optimierte Antwort](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

Die folgende Abbildung zeigt die Route, die sich aus dieser Abfrage ergibt.

![Optimierte Abbildung](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

Die optimale Route hat die folgende Reihenfolge der Wegpunkte: 0, 5, 1, 2, 4, 3 und 6.

>[!TIP]
> Die Informationen zur optimierten Reihenfolge der Wegpunkte stellen einen Satz Indizes bereit. Diese schließen die Ausgangs- und Zielindizes aus. Sie müssen diese Werte um 1 erhöhen, um den Ausgangspunkt zu berücksichtigen. Fügen Sie anschließend Ihr Ziel am Ende hinzu, um die vollständige Liste der sortierten Wegpunkte zu erhalten.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Berechnen und Beeinflussen alternativer Routen mithilfe von Unterstützungspunkten

Es können Situationen eintreten, in denen Sie eine Route rekonstruieren möchten, um null oder mehr alternative Routen zu einer Referenzroute zu berechnen. Beispielsweise, wenn Sie Kunden alternative Routen zeigen möchten, die zu Ihrem Einzelhandelsgeschäft führen. In diesem Fall müssen Sie einen Ort mithilfe von Unterstützungspunkten beeinflussen. Dies sind die Schritte zum Beeinflussen eines Orts:

1. Berechnen Sie eine Route unverändert, und rufen Sie die Wegführung aus der Routenantwort ab
2. Verwenden Sie die Wegführung der Route, um die gewünschten Orte entlang oder nahe der Route zu finden. Beispielsweise können Sie die [Point of Interest-API](/rest/api/maps/search/getsearchpoi) von Azure Maps verwenden oder eigenen Daten in Ihrer Datenbank abfragen.  
3. Sortieren Sie die Orte nach der Entfernung von Routenanfang
4. Fügen Sie diese Orte einer neuen Routinganforderung an die [Post Route Directions-API](/rest/api/maps/route/postroutedirections) als Unterstützungspunkte hinzu. Weitere Informationen zu den Unterstützungspunkten finden Sie in der [Dokumentation zur Post Route Directions-API](/rest/api/maps/route/postroutedirections#supportingpoints). 

Beim Aufrufen der [Post Route Directions-API](/rest/api/maps/route/postroutedirections) können Sie die minimale Abweichungszeit oder die Entfernungseinschränkungen zusammen mit den Unterstützungspunkten festlegen. Verwenden Sie diese Parameter, wenn Sie alternative Routen anbieten, die Reisezeit aber zugleich begrenzen möchten. Wenn diese Einschränkungen verwendet werden, folgen die alternativen Routen für die angegebene Dauer oder Entfernung der Referenzroute vom Ausgangspunkt aus. Anders ausgedrückt, unterscheiden sich die anderen Routen gemäß den angegebenen Einschränkungen von der Referenzroute.

Die Abbildung unten stellt ein Beispiel für das Rendern alternativer Routen mit angegebenen Abweichungsgrenzwerten für Dauer und Entfernung dar.

![Alternative Routen](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>Verwenden des Routingdiensts in einer Web-App

Das Web SDK für Azure Maps stellt ein [Dienstmodul](/javascript/api/azure-maps-rest/) bereit. Dieses Modul ist eine Hilfsbibliothek, die die Verwendung der REST-APIs von Azure Maps in Web- oder Node.js-Anwendungen durch Einsatz von JavaScript oder TypeScript vereinfacht. Das Dienstmodul kann verwendet werden, um die zurückgegebenen Routen auf der Karte zu rendern. Das Modul bestimmt automatisch, welche API für GET- und POST-Anforderungen verwendet werden soll.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:

> [!div class="nextstepaction"]
> [Azure Maps-Routendienst](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Verwenden des Dienstmoduls](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Anzeigen einer Route auf der Karte](./map-route.md)

> [!div class="nextstepaction"]
> [Azure Maps NPM-Paket](https://www.npmjs.com/package/azure-maps-rest  )