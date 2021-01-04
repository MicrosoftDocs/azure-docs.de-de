---
title: 'Tutorial: Migrieren von Webdiensten aus Bing Karten | Microsoft Azure Maps'
description: Tutorial zum Migrieren von Webdiensten aus Bing Karten zu Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d257c66de8fb62fb57c573d91966f3e7d8d1b123
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904957"
---
# <a name="tutorial---migrate-web-service-from-bing-maps"></a>Tutorial: Migrieren von Webdiensten aus Bing Karten

Sowohl Azure als auch Bing Karten ermöglichen den Zugriff auf räumliche APIs über REST-Webdienste. Die API-Schnittstellen für diese Plattformen bieten ähnliche Funktionen, verwenden jedoch unterschiedliche Namenskonventionen und Antwortobjekte.

In der folgenden Tabelle finden Sie die Azure Maps-Dienst-APIs, die ähnliche Funktionen wie die aufgelisteten Dienst-APIs von Bing Karten bereitstellen.

| Bing Karten-Dienst-API                 | Azure Maps-Dienst-API      |
|---------------------------------------|-----------------------------|
| Vorschlagssuche                           | [Search](/rest/api/maps/search)     |
| Wegbeschreibungen (einschließlich LKW)          | [Wegbeschreibungen](/rest/api/maps/route/getroutedirections)                          |
| Entfernungsmatrix                       | [Routenmatrix](/rest/api/maps/route/postroutematrixpreview)                          |
| Bilder – statische Karte                  | [Rendern](/rest/api/maps/render/getmapimage)                                   |
| Isochrone                            | [Routenbereich](/rest/api/maps/route/getrouterange)                                    |
| Lokale Einblicke                        | [Suche](/rest/api/maps/search) + [Routenbereich](/rest/api/maps/route/getrouterange)    |
| Lokale Suche                          | [Suche](/rest/api/maps/search)     |
| Standortbestimmung (POIs)           | [Suche](/rest/api/maps/search)     |
| Standorte (Geocodierung vorwärts/reversiert) | [Suche](/rest/api/maps/search)                                               |
| Ausrichtung an Straße                          | [Veröffentlichen von Wegbeschreibungen](/rest/api/maps/route/postroutedirections)                         |
| Spatial Data Services (SDS)           | [Suche](/rest/api/maps/search) + [Route](/rest/api/maps/route) + weitere Azure-Dienste |
| Zeitzone                             | [Zeitzone](/rest/api/maps/timezone)  |
| Verkehrsmeldungen                     | [Verkehrsinfodetails](/rest/api/maps/traffic/gettrafficincidentdetail)                     |
| Elevation                             | [Höhe (Vorschauversion)](/rest/api/maps/elevation)

Die folgenden Dienst-APIs sind derzeit noch nicht in Azure Maps verfügbar:

-   Optimierte Reiserouten – in Planung. Azure Maps Route-API unterstützt die Traveling Salesman-Optimierung für ein einzelnes Fahrzeug.
-   Metadaten für Bilder – In Bing Karten hauptsächlich für das Abrufen der Kachel-URLs verwendet. Azure Maps verfügt über einen eigenständigen Dienst für den direkten Zugriff auf Kartenkacheln.

Azure Maps verfügt über mehrere zusätzliche REST-Webdienste, die möglicherweise von Interesse sind:

-   [Azure Maps Creator (Vorschau)](./creator-indoor-maps.md): Erstellen Sie einen benutzerdefinierten privaten digitalen Zwilling von Gebäuden und Räumen.
-   [Räumliche Operationen](/rest/api/maps/spatial): Lagern Sie komplexe räumliche Berechnungen und Operationen wie etwa Geofencing an einen Dienst aus.
-   [Kartenkacheln](/rest/api/maps/render/getmaptile): Greifen Sie auf Straßen- und Bildkacheln aus Azure Maps als Raster- und Vektorkacheln zu.
-   [Batchrouting](/rest/api/maps/route/postroutedirectionsbatchpreview): Ermöglicht die Ausführung von bis zu 1.000 Routenanforderungen in einem einzelnen Batch über einen bestimmten Zeitraum. Die Routen werden zwecks schnellerer Verarbeitung parallel auf dem Server berechnet.
-   [Verkehrsfluss](/rest/api/maps/traffic): Greifen Sie in Echtzeit auf Daten zum Verkehrsfluss in Form von Raster- und Vektorkacheln zu.
-   [Geolocation-API (Vorschau)](/rest/api/maps/geolocation/getiptolocationpreview): Rufen Sie den Standort zu einer IP-Adresse ab.
-   [Wetterdienste](/rest/api/maps/weather): Erhalten Sie Zugriff auf Echtzeit- und prognostizierte Wetterdaten.

Lesen Sie unbedingt auch die folgenden Anleitungen zu bewährten Methoden:

-   [Bewährte Suchmethoden](./how-to-use-best-practices-for-search.md)
-   [Bewährte Methoden für den Azure Maps-Routendienst](./how-to-use-best-practices-for-routing.md)

## <a name="geocoding-addresses"></a>Geocodierung von Adressen

Bei der Geocodierung handelt es sich um den Prozess, bei dem eine Adresse (z. B. `"1 Microsoft way, Redmond, WA"`) in eine Koordinate (z. B. Längengrad: –122,1298, Breitengrad: 47,64005) konvertiert wird. Anschließend werden die Koordinaten dann häufig verwendet, um eine Ortsmarke auf einer Karte zu positionieren oder eine Karte zu zentrieren.

Azure Maps bietet mehrere Methoden zur Geocodierung von Adressen:

-   [Freiform-Geocodierung von Adressen](/rest/api/maps/search/getsearchaddress): Geben Sie eine einzelne Adresszeichenfolge (wie `"1 Microsoft way, Redmond, WA"`) an, und verarbeiten Sie die Anforderung sofort. Dieser Dienst wird zur schnellen Geocodierung einzelner Adressen empfohlen.
-   [Strukturierte Geocodierung von Adressen](/rest/api/maps/search/getsearchaddressstructured):  Geben Sie die Teile einer einzelnen Adresse an, z. B. den Namen der Straße, die Stadt, das Land bzw. die Region und die Postleitzahl, und verarbeiten Sie die Anforderung sofort. Dieser Dienst wird empfohlen, wenn Sie einzelne Adressen schnell geocodieren müssen und die Daten bereits als einzelne Adressbestandteile analysiert haben.
-   [Batch-Geocodierung von Adressen:](/rest/api/maps/search/postsearchaddressbatchpreview)  Erstellen Sie eine Anforderung mit bis zu 10.000 Adressen, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Adressen werden auf dem Server parallel geocodiert. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden. Dieser Dienst wird für die Geocodierung großer Datasets empfohlen.
-   [Fuzzysuche:](/rest/api/maps/search/getsearchfuzzy)  Diese API kombiniert die Geocodierung von Adressen mit der Suche nach Points of Interest (POI). Diese API übernimmt eine Freiform-Zeichenfolge, die eine Adresse, ein Ort, ein Orientierungspunkt, ein Point of Interest oder eine POI-Kategorie sein kann, und verarbeitet die Anforderung sofort. Diese API wird für Anwendungen empfohlen, in denen Benutzer im selben Textfeld nach Adressen oder Points of Interest suchen können.
-   [Batchfuzzysuche:](/rest/api/maps/search/postsearchfuzzybatchpreview)  Erstellen Sie eine Anforderung mit bis zu 10.000 Adressen, Orten, Orientierungspunkten oder Points of Interest, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Daten werden auf dem Server parallel verarbeitet. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden.

Die folgenden Tabellen enthalten Querverweise zwischen den API-Parametern von Bing Karten und den vergleichbaren API-Parametern in Azure Maps für die strukturierte und die Freiform-Geocodierung von Adressen.

**Standort nach Adresse (strukturierte Adresse)**

| Bing Karten-API-Parameter              | Vergleichbarer Azure Maps-API-Parameter                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`, `streetName` oder `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` und `countryCode`                     |
| `locality`                         | `municipality` oder `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | N/V: von Azure Maps immer zurückgegeben, falls verfügbar.   |
| `include` (`incl`)               | N/V – Der ISO2-Ländercode wird von Azure Maps immer zurückgegeben. |
| `key`                              | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)                  | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md). |
| `userRegion` (`ur`)              | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views). |

Azure Maps unterstützt darüber hinaus:

-   `countrySecondarySubdivision`: Landkreise, Verwaltungsbezirke
-   `countryTertiarySubdivision`: Benannte Gebiete, Stadtteile, Kantone, Gemeinden
-   `ofs`: Durchblättern der Ergebnisse in Kombination mit dem `maxResults`-Parameter.

**Standort nach Abfrage (Freiform-Adresszeichenfolge)**

| Bing Karten-API-Parameter              | Vergleichbarer Azure Maps-API-Parameter      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | N/V: von Azure Maps immer zurückgegeben, falls verfügbar.  |
| `include` (`incl`)               | N/V – Der ISO2-Ländercode wird von Azure Maps immer zurückgegeben.  |
| `key`                              | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)                  | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md).  |
| `userRegion` (`ur`)              | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views). |

Azure Maps unterstützt darüber hinaus:

-   `typeahead`: Gibt an, ob die Abfrage als Teileingabe interpretiert wird und die Suche in den Vorhersagemodus (Vorschlagssuche/AutoComplete) wechselt.
-   `countrySet`: Eine kommagetrennte Liste mit ISO2-Ländercodes, auf die die Suche eingeschränkt werden soll.
-   `lat`/`lon`, `topLeft`/`btmRight`, `radius`: Geben Sie den Standort und das Gebiet des Benutzers an, um für den Ort relevantere Ergebnisse zu erzielen.
-   `ofs`: Durchblättern der Ergebnisse in Kombination mit dem `maxResults`-Parameter.

Ein Beispiel für die Verwendung des Suchdiensts finden Sie [hier](./how-to-search-for-address.md). Lesen Sie unbedingt die Dokumentation mit [bewährten Methoden für die Suche](./how-to-use-best-practices-for-search.md).

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Umgekehrte Geocodierung einer Koordinate (Suchen eines Standorts anhand eines Punkts)

Bei der umgekehrten Geocodierung handelt es sich um den Prozess der Konvertierung geografischer Koordinaten (z. B. Längengrad: –122,1298, Breitengrad: 47,64005) in die ungefähre Adresse (z. B. `"1 Microsoft way, Redmond, WA"`).

Azure Maps bietet mehrere Methoden für die umgekehrte Geocodierung:

-   [Umgekehrte Geocodierung von Adressen](/rest/api/maps/search/getsearchaddressreverse): Geben Sie eine einzelne geografische Koordinate an, um ihre ungefähre Adresse zu erhalten, und verarbeiten Sie die Anforderung sofort.
-   [Umgekehrte Geocodierung nach Straßenkreuzungen](/rest/api/maps/search/getsearchaddressreversecrossstreet): Geben Sie eine einzelne geografische Koordinate an, um in der Nähe gelegene Straßenkreuzungen (z. B. „1st & main“) abzurufen und die Anforderung sofort zu verarbeiten.
-   [Umgekehrte Batch-Geocodierung von Adressen:](/rest/api/maps/search/postsearchaddressreversebatchpreview)  Erstellen Sie eine Anforderung mit bis zu 10.000 Koordinaten, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Daten werden auf dem Server parallel verarbeitet. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden.

In der folgenden Tabelle werden die Parameter der Bing Karten-API den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter              | Vergleichbarer Azure Maps-API-Parameter       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType`: Siehe die Entitätstyp-Vergleichstabelle unten.    |
| `includeNeighborhood` (`inclnb`)     | N/V: von Azure Maps immer zurückgegeben, falls verfügbar.         |
| `include` (`incl`)                   | N/V – Der ISO2-Ländercode wird von Azure Maps immer zurückgegeben.    |
| `key`                                | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)                      | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md).   |
| `userRegion` (`ur`)                  | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views). |

Lesen Sie unbedingt die Dokumentation mit [bewährten Methoden für die Suche](./how-to-use-best-practices-for-search.md).

Die Azure Maps-API für die umgekehrte Geocodierung bietet einige zusätzliche Funktionen, die in Bing Karten nicht verfügbar sind und bei der Migration Ihrer App hilfreich sein können:

-   Abrufen von Daten zur Geschwindigkeitsbegrenzung
-   Abrufen von Informationen zur Straßennutzung: lokale Straße, Umgehungsstraße, eingeschränkte Zufahrt, Bodenschwellen usw.
-   Die Straßenseite, auf der die Koordinate liegt

**Entitätstyp-Vergleichstabelle**

Die folgende Tabelle enthält Querverweise zwischen den Werten des Bing Karten-Entitätstyps und den entsprechenden Eigenschaftsnamen in Azure Maps.

| Entitätstyp in Bing Karten | Vergleichbarer Azure Maps-Entitätstyp               | Beschreibung                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Adresse*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Viertel*                             |
| `PopulatedPlace`      | `Municipality` oder `MunicipalitySubdivision`     | *Stadt*, *Vorort* oder *Metropolregion*     |
| `Postcode1`           | `PostalCodeArea`                                | *Postleitzahl*                 |
| `AdminDivision1`      | `CountrySubdivision`                            | *Bundesland* oder *Kanton*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Landkreis* oder *Verwaltungsbezirk*                    |
| `CountryRegion`       | `Country`                                       | *Name des Landes*                             |
|                       | `CountryTertiarySubdivision`                    | *Stadtteile*, *Kantone*, *Gemeinden*          |

## <a name="get-location-suggestions-autosuggest"></a>Abrufen von Standortvorschlägen (Vorschlagssuche)

Mehrere der Azure Maps Such-APIs unterstützen den Vorhersagemodus, der für Szenarien mit Vorschlagssuche verwendet werden kann. Die Azure Maps [Fuzzysuche](/rest/api/maps/search/getsearchfuzzy)-API ähnelt am ehesten der Bing Maps-Vorschlagssuch-API. Die folgenden APIs unterstützen den Vorhersagemodus ebenfalls, fügen Sie der Abfrage `&typeahead=true` hinzu:

-   [Freiform-Geocodierung von Adressen](/rest/api/maps/search/getsearchaddress): Geben Sie eine einzelne Adresszeichenfolge (wie `"1 Microsoft way, Redmond, WA"`) an, und verarbeiten Sie die Anforderung sofort. Dieser Dienst wird zur schnellen Geocodierung einzelner Adressen empfohlen.
-   [Fuzzysuche:](/rest/api/maps/search/getsearchfuzzy)  Diese API kombiniert die Geocodierung von Adressen mit der Suche nach Points of Interest (POI). Diese API übernimmt eine Freiform-Zeichenfolge, die eine Adresse, ein Ort, ein Orientierungspunkt, ein Point of Interest oder eine POI-Kategorie sein kann, und verarbeitet die Anforderung sofort. Diese API wird für Anwendungen empfohlen, in denen Benutzer im selben Textfeld nach Adressen oder Points of Interest suchen können.
-   [POI-Suche:](/rest/api/maps/search/getsearchpoi)  Sucht nach Points of Interest nach dem Namen. Beispielsweise `"starbucks"`.
-   [POI-Kategoriesuche:](/rest/api/maps/search/getsearchpoicategory)  Sucht nach Points of Interest nach der Kategorie. Beispiel: „Restaurant“.

## <a name="calculate-routes-and-directions"></a>Berechnen von Routen und Wegbeschreibungen

Sie können Azure Maps zum Berechnen von Routen und Wegbeschreibungen verwenden. Azure Maps verfügt über viele der gleichen Funktionen wie der Routenplanungsdienst von Bing Karten, z. B.:

-   Ankunfts- und Abfahrzeiten
-   Routen unter Berücksichtigung des echtzeitbasierten und vorhergesagten Verkehrsaufkommens
-   Unterschiedliche Transportmodi wie mit dem Auto, zu Fuß oder per LKW
-   Optimierung der Wegpunktreihenfolge (Traveling Salesmen)

> [!NOTE]
> Bei Azure Maps müssen alle Wegpunkte Koordinaten sein. Adressen müssen zunächst geocodiert werden.

Der Routenplanungsdienst von Azure Maps stellt die folgenden APIs zum Berechnen von Routen bereit:

-   [Berechnen der Route:](/rest/api/maps/route/getroutedirections)  Berechnen Sie die Route, und lassen Sie die Anforderung sofort verarbeiten. Diese API unterstützt sowohl GET- als auch POST-Anforderungen. POST-Anforderungen werden empfohlen, wenn eine große Anzahl von Wegpunkten angegeben wird oder wenn viele der Routenoptionen verwendet werden, um sicherzustellen, dass die URL-Anforderung nicht zu lang wird und Probleme verursacht.
-   [Batchroute:](/rest/api/maps/route/postroutedirectionsbatchpreview)  Erstellen Sie eine Anforderung mit bis zu 1.000 Routenanforderungen, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Daten werden auf dem Server parallel verarbeitet. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden.
-   [Mobilitätsdienste (Vorschauversion)](/rest/api/maps/mobility): Berechnen Sie Routen und Wegbeschreibungen mit dem öffentlichen Nahverkehr.

In der folgenden Tabelle werden die Parameter der Bing Karten-API den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter                                    | Vergleichbarer Azure Maps-API-Parameter               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` oder `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | –                                               |
| `distanceUnit` (`du`)                                      | Nicht verfügbar: Bei Azure Maps wird ausschließlich das metrische System verwendet.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType`, `minDeviationDistance` und `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` und `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` oder `arriveAt`                          |
| `tolerances` (`tl`)                                        | –                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` (`wp.n`) oder `viaWaypoint.n` (`vwp.n`)         | `query`: Koordinaten im Format `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)                                            | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md). |
| `userRegion` (`ur`)                                        | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views). |

Die Routenplanungs-API von Azure Maps unterstützt außerdem die Wegführung von LKW in der gleichen API. In der folgenden Tabelle werden die zusätzlichen Parameter der Bing Karten-API für die LKW-Wegführung den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter                  | Vergleichbarer Azure Maps-API-Parameter        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | N/V: Es werden nur Dimensionen in Metern unterstützt. |
| `weightUnit` (`wu`)                      | N/V: Es werden nur Gewichte in Kilogramm unterstützt. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **N/V**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **N/V**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **N/V**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **N/V**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **N/V**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Standardmäßig gibt die Routen-API von Azure Maps nur eine Zusammenfassung (Entfernung und Zeiten) sowie die Koordinaten für die Route zurück. Verwenden Sie den Parameter `instructionsType`, um ausführliche Anweisungen abzurufen. Mit dem Parameter `routeRepresentation` können die Zusammenfassung und die Route herausgefiltert werden.

Arbeiten Sie auch unbedingt die Dokumentation mit [bewährten Methoden für die Routenplanung](./how-to-use-best-practices-for-routing.md) durch.

Die Routenplanungs-API von Azure Maps bietet viele zusätzliche Funktionen, die in Bing Karten nicht verfügbar sind und bei der Migration Ihrer App hilfreich sein können:

-   Unterstützung für den Routentyp: kürzeste, schnellste, attraktivste und kraftstoffeffizienteste
-   Unterstützung für zusätzliche Reisemodi: Fahrrad, Bus, Motorrad, Taxi, Lkw und Kleinbus.
-   Unterstützung für 150 Wegpunkte
-   Berechnung mehrerer Fahrzeiten in einer einzelnen Anforderung: bisheriger Verkehr, aktueller Verkehr, kein Verkehr
-   Vermeidung zusätzlicher Straßentypen: Straßen von Fahrgemeinschaften, ungepflasterte Straßen, bereits verwendete Straßen
-   Routenplanung nach Motorisierung: Berechnen Sie Routen für Verbrennungs- oder Elektrofahrzeuge basierend auf den Angaben zum verbleibenden Kraftstoff bzw. der verbleibenden Laufzeit.
-   Angabe der maximale Fahrzeuggeschwindigkeit

## <a name="snap-coordinates-to-road"></a>Ausrichten von Koordinaten an Straßen

Es gibt mehrere Möglichkeiten, in Azure Maps Koordinaten an Straßen auszurichten.

-   Verwenden Sie die Wegbeschreibungs-API, um Koordinaten an einer logischen Wegführung entlang des Straßennetzwerks auszurichten.
-   Verwenden Sie das Azure Maps Web SDK, um einzelne Koordinaten auf Vektorkacheln an der nächstgelegenen Straße auszurichten.
-   Verwenden Sie die Azure Maps-Vektorkacheln direkt, um einzelne Koordinaten auszurichten.

**Verwenden der Wegbeschreibungs-API zum Ausrichten von Koordinaten**

Azure Maps kann mithilfe der [Wegbeschreibungs](/rest/api/maps/route/postroutedirections)-API Koordinaten an Straßen ausrichten. Dieser Dienst kann verwendet werden, um eine logische Wegführung zwischen einem Satz von Koordinaten zu rekonstruieren, und ist mit der „Ausrichtung an Straße“-API von Bing Karten vergleichbar.

Es gibt zwei verschiedene Möglichkeiten, die Wegbeschreibungs-API zum Ausrichten von Koordinaten an Straßen zu verwenden.

-   Bei 150-Koordinaten oder weniger kann die Übergabe als Wegpunkte in der API zum Abrufen von Wegbeschreibungen erfolgen. Bei diesem Ansatz können zwei verschiedene Typen von ausgerichteten Daten abgerufen werden: Die Anweisungen zur Wegführung enthalten die einzelnen ausgerichteten Wegpunkte, während der Wegführungspfad einen Satz interpolierter Koordinaten aufweist, die den gesamten Pfad zwischen den Koordinaten ausfüllen.
-   Bei mehr als 150 Koordinaten kann die API zum Veröffentlichen von Wegbeschreibungen verwendet werden. Die Anfangs- und Endkoordinaten müssen im Abfrageparameter übergeben werden, aber alle Koordinaten können in den `supportingPoints`-Parameter im Textkörper der POST-Anforderung im Format einer GeoJSON-Geometriesammlung von Punkten übergeben werden. Die einzigen ausgerichteten Daten, die bei diesem Ansatz verfügbar sind, sind der Wegführungspfad, bei dem es sich um einen Satz interpolierter Koordinaten handelt, die den gesamten Pfad zwischen den Koordinaten ausfüllen. [Hier sehen Sie ein Beispiel](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) für diesen Ansatz, das das Dienstmodul im Azure Maps Web SDK verwendet.

In der folgenden Tabelle werden die Parameter der Bing Karten-API den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter    | Vergleichbarer Azure Maps-API-Parameter                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints`: Übergibt diese Punkte an den Text der Post-Anforderung.  |
| `interpolate`              | –                                                                 |
| `includeSpeedLimit`        | –                                                                 |
| `includeTruckSpeedLimit`   | –                                                                 |
| `speedUnit`                | –                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)            | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md).   |
| `userRegion` (`ur`)        | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views).   |

Die Azure Maps-API zur Routenplanung unterstützt außerdem Parameter zur Wegführung für LKW in der gleichen API, um sicherzustellen, dass logische Pfade berechnet werden. In der folgenden Tabelle werden die zusätzlichen Parameter der Bing Karten-API für die LKW-Wegführung den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter                 | Vergleichbarer Azure Maps-API-Parameter        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | N/V: Es werden nur Dimensionen in Metern unterstützt. |
| `weightUnit` (`wu`)                     | N/V: Es werden nur Gewichte in Kilogramm unterstützt. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **N/V**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **N/V**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **N/V**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **N/V**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **N/V**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Da bei diesem Ansatz die Wegbeschreibungs-API verwendet wird, können sämtliche Optionen in diesem Dienst verwendet werden, um die Logik anzupassen, mit der die Koordinaten an Straßen ausgerichtet werden. Beispielsweise würde das Angeben einer Abfahrtszeit dazu führen, dass Verkehrsdaten aus der Vergangenheit berücksichtigt würden.

Die Azure Maps-Wegbeschreibungs-API gibt zurzeit keine Daten zu Geschwindigkeitsbeschränkungen zurück, diese können jedoch mithilfe der Azure Maps-API für umgekehrtes Geocoding abgerufen werden.

**Verwenden des Web SDK zum Ausrichten von Koordinaten**

Das Azure Maps Web SDK verwendet zum Rendern der Karten Vektorkacheln. Diese Vektorkacheln enthalten die rohen Geometrieinformationen der Straße und können verwendet werden, um den nächstgelegenen Weg zu einer Koordinate für das einfache Ausrichten einzelner Koordinaten zu berechnen. Dies ist nützlich, wenn Sie die Koordinaten visuell über den Straßen anzeigen möchten und bereits das Azure Maps Web SDK zum Visualisieren der Daten verwenden.

Bei diesem Ansatz erfolgt die Ausrichtung jedoch nur an den Straßensegmenten, die in der Kartenansicht geladen sind. Beim Herauszoomen zur Landesebene sind möglicherweise keine Straßendaten vorhanden, sodass keine Ausrichtung erfolgen kann. Allerdings kann bei dieser Zoomstufe ein einzelnes Pixel die Fläche mehrerer Häuserblocks in einer Stadt darstellen, sodass eine Ausrichtung nicht erforderlich ist. Zum Beheben dieses Problems kann die Ausrichtungslogik jedes Mal angewendet werden, wenn die Bewegung der Karte abgeschlossen ist. [Hier sehen Sie ein Codebeispiel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) zur Veranschaulichung.

**Direkte Verwendung der Azure Maps-Vektorkacheln zum Ausrichten von Koordinaten**

Die Azure Maps-Vektorkacheln enthalten die rohen Geometriedaten der Straße, die verwendet werden können, um für ein einfaches Ausrichten einzelner Koordinaten den nächstgelegenen Punkt auf einem Weg zu einer Koordinate zu berechnen. Alle Straßensegmente werden in den Sektoren mit Zoomstufe 15 dargestellt, es ist also sinnvoll, Kacheln von dort abzurufen. Anschließend können Sie die [Quadtree-Kachelpyramidenberechnung](./zoom-levels-and-tile-grid.md) verwenden, um die benötigten Kacheln zu bestimmen und die Kacheln in Geometrien zu konvertieren. Von diesem Punkt an kann eine räumliche mathematische Bibliothek wie [turf js](http://turfjs.org/) oder [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) verwendet werden, um die nächstgelegenen Liniensegmente zu berechnen.

## <a name="retrieve-a-map-image-static-map"></a>Abrufen eines Kartenbilds (statische Karte)

Azure Maps stellt eine API für das Rendern der statischen Kartenbilder mit überlagerten Daten bereit. Die [Kartenbildrendering](/rest/api/maps/render/getmapimagerytile)-API von Azure Maps ist mit der Bing Karten-API für statische Karten vergleichbar.

> [!NOTE]
> Für Azure Maps müssen die Zentrums-, alle Ortsmarken- und Pfadposition als Koordinaten im Format `longitude,latitude` vorliegen, während Bing Karten das Format `latitude,longitude` verwendet.</p>
<p>Adressen müssen zunächst geocodiert werden.

In der folgenden Tabelle werden die Parameter der Bing Karten-API den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter  | Vergleichbarer Azure Maps-API-Parameter            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format`: wird als Teil des URL-Pfads angegeben. Derzeit wird nur PNG unterstützt.  |
| `heading`                | N/V: Straßenansicht wird nicht unterstützt.                |
| `imagerySet`             | `layer` und `style`: Lesen Sie die Dokumentation zu den [unterstützten Kartenstilen](./supported-map-styles.md).   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | –                                            |
| `mapSize` (`ms`)         | `width` und `height`: Es sind Größen bis 8.192 × 8.192 möglich. |
| `declutterPins` (`dcl`)  | –                                            |
| `dpi`                    | –                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | –                                            |
| `pitch`                  | N/V: Straßenansicht wird nicht unterstützt.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N/V: Zentrum oder Begrenzungsrahmen muss verwendet werden.     |
| `highlightEntity` (`he`) | –                                            |
| `style`                  | –                                            |
| Routenparameter         | –                                            |
| `key`                    | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)          | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md).   |
| `userRegion` (`ur`)      | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views). |

> [!NOTE]
> Azure Maps verwendet ein Kachelsystem mit Kacheln, die doppelt so groß sind wie die in Bing Karten verwendeten Kartenkacheln. Aus diesem Grund ist der Wert der Zoomebene in Azure Maps im Vergleich zu Bing Karten um eine Zoomstufe niedriger. Verringern Sie die Zoomstufe in den Anforderungen, die Sie migrieren, um 1, um dies zu kompensieren.

Weitere Informationen finden Sie in der [Schrittanleitung zur Rendering-API für Kartenbilder](./how-to-render-custom-data.md).

Damit der Azure Maps-Renderingdienst in der Lage ist, ein statisches Kartenbild zu generieren, bietet er auch die Möglichkeit, direkt auf Kartenkacheln im Raster- (PNG) und im Vektorformat zuzugreifen:

-   [Kartenkachel](/rest/api/maps/render/getmaptile): Ruft Raster- (PNG) und Vektorkacheln für die Basiskarten (Straßen, Grenzen, Hintergrund) ab.
-   [Kachel für Kartenbilder](/rest/api/maps/render/getmapimagerytile): Ruft Kacheln für Luft- und Satellitenbilder ab.

### <a name="pushpin-url-parameter-format-comparison"></a>Vergleich der URL-Formate für Ortsmarken

**Vorher: Bing Karten

In Bing Karten können Ortsmarken einem statischen Kartenbild mithilfe des Parameters `pushpin` in der URL hinzugefügt werden. Der `pushpin`-Parameter nimmt einen Standort im Format `latitude,longitude` an, einen Symbolstil und eine Textbezeichnung (bis zu drei Zeichen), wie unten dargestellt:

> `&pushpin=latitude,longitude;iconStyle;label`

Weitere Ortsmarken können hinzugefügt werden, indem der URL zusätzliche `pushpin`-Parameter mit einer anderen Wertmenge hinzugefügt werden. Die Symbolstile für Ortsmarken sind auf eine der vordefinierten Formatvorlagen beschränkt, die in der Bing Karten-API zur Verfügung stehen.

In Bing Karten kann z. B. der Karte mit dem folgenden URL-Parameter eine rote Ortsmarke an den Koordinaten (Längengrad: –110, Breitengrad: 45) hinzugefügt werden:

> `&pushpin=45,-110;7;AB`

<center>

![Ortsmarke auf einer statischen Karte in Bing Karten](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps können Ortsmarken einem statischen Kartenbild ebenfalls durch Angabe des Parameters `pins` in der URL hinzugefügt werden. Ortsmarken in Azure Maps werden durch Angeben eines Symbolstils und einer Liste von Standorten definiert, die diesen Symbolstil verwenden. Diese Informationen werden dann an den `pins`-Parameter übergeben und können mehrfach angegeben werden, um Ortsmarken mit verschiedenen Stilen zu unterstützen.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

Weitere Stile können verwendet werden, indem der URL zusätzliche `pins`-Parameter mit einem anderen Stil und einem anderen Satz von Positionen hinzugefügt werden.

Hinsichtlich der Positionen von Ortsmarken müssen bei Azure Maps die Koordinaten im Format `longitude latitude` angegeben werden, während Bing Karten `latitude,longitude` verwendet. Beachten Sie auch, dass in Azure Maps **ein Leerzeichen anstelle eines Kommas** als Trennzeichen zwischen Längen- und Breitengrad verwendet wird.

Der Wert `iconType` gibt den Typ der zu erstellenden Pins an. Er kann die folgenden Werte aufweisen:

-   `default`: das Standardsymbol für Pins.
-   `none`: Es wird kein Symbol angezeigt, sondern nur Bezeichnungen gerendert.
-   `custom`: gibt an, dass ein benutzerdefiniertes Symbol verwendet werden soll. Eine URL, die auf das Symbolbild verweist, kann am Ende des Parameters `pins` nach den Informationen zur Pinposition hinzugefügt werden.
-   `{udid}`: eine Unique Data ID (UDID) für ein Symbol, das in der Azure Maps-Datenspeicher-Plattform gespeichert ist.

In Azure Maps werden Pinstile im Format `optionNameValue` hinzugefügt, wobei mehrere Stile durch einen senkrechten Strich (`|`) voneinander getrennt werden, z. B. `iconType|optionName1Value1|optionName2Value2`. Beachten Sie, dass die Optionsnamen und -werte nicht voneinander getrennt werden. Die folgenden Stiloptionsnamen können für den Stil von Ortsmarken in Azure Maps verwendet werden:

-   `al`: gibt die Deckkraft (Alpha) der Ortsmarke an. Dies kann eine Zahl zwischen 0 und 1 sein.
-   `an`: gibt den Pinanker an. Die x- und y-Pixelwerte werden im Format `x y` angegeben.
-   `co`: die Farbe des Pins. Dabei muss es sich um eine hexadezimale 24-Bit-Farbe handeln: `000000` in `FFFFFF`.
-   `la`: gibt den Bezeichnungsanker an. Die x- und y-Pixelwerte werden im Format `x y` angegeben.
-   `lc`: die Farbe der Bezeichnung. Dabei muss es sich um eine hexadezimale 24-Bit-Farbe handeln: `000000` in `FFFFFF`.
-   `ls`: die Größe der Bezeichnung in Pixel. Kann eine Zahl größer als 0 sein.
-   `ro`: ein Wert in Grad, um den das Symbol gedreht werden soll. Dies kann eine Zahl zwischen –360 und 360 sein.
-   `sc`: ein Skalierungswert für das Pinsymbol. Kann eine Zahl größer als 0 sein.

Bezeichnungswerte werden für jede Pinposition angegeben, anstatt einen einzelnen Bezeichnungswert zu verwenden, der für alle Ortsmarken in der Liste der Positionen gilt. Der Bezeichnungswert kann eine Zeichenfolge mit mehreren Zeichen sein und in einfache Anführungszeichen eingeschlossen werden, um sicherzustellen, dass er nicht als Stil- oder Positionswert interpretiert wird.

In Azure Maps kann z. B. ein rotes (`FF0000`) Standardsymbol mit der Bezeichnung „Space Needle“ unter dem Symbol (15 50) an den Koordinaten (Längengrad: –122,349300, Breitengrad: 47,620180) mithilfe des folgenden URL-Parameters hinzugefügt werden:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

<center>

![Ortsmarke auf einer statischen Karte in Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)</center>

Im folgenden Beispiel werden drei Pins mit den Bezeichnungswerten „1“, „2“ und „3“ hinzugefügt:

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

<center>

![Mehrere Ortsmarken auf einer statischen Karte in Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)</center>

### <a name="draw-curve-url-parameter-format-comparison"></a>Vergleich der Formate des Draw curve-URL-Parameters

**Vorher: Bing Karten**

In Bing Karten können Linien und Polygone einem statischen Kartenbild mithilfe des Parameters `drawCurve` in der URL hinzugefügt werden. Der Parameter `drawCurve` akzeptiert einen Formtyp, einen Stiltyp und eine Liste von Positionen, die auf der Karte gerendert werden sollen, wie unten zu sehen:

> `&drawCurve=shapeType,styleType,location1,location2...`

Weitere Stile können verwendet werden, indem der URL zusätzliche `drawCurve`-Parameter mit einem anderen Stil und einem anderen Satz von Positionen hinzugefügt werden.

Positionen werden in Bing Karten im Format `latitude1,longitude1_latitude2,longitude2_…` angegeben. Positionen können auch codiert werden.

Zu den Formtypen in Bing Karten gehören Linien, Polygone, Kreis und Kurve. Zu den Stiltypen gehören Linienfarbe, Linienstärke, Konturfarbe, Konturstärke und Kreisradius.

In Bing Karten kann z. B. eine blaue Linie mit einer Deckkraft von 50 % und einer Stärke von vier Pixeln der Karte zwischen den Koordinaten (Längengrad: –110, Breitengrad: 45 und Längengrad: –100, Breitengrad: 50) mithilfe des folgenden URL-Parameters hinzugefügt werden:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

<center>

![Linie auf einer statischen Karte in Bing Karten](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)</center>

**Nachher: Azure Maps**

In Azure Maps können Linien und Polygone einem statischen Kartenbild ebenfalls durch Angabe des Parameters *path* in der URL hinzugefügt werden. Wie bei Bing Karten können auch ein Stil und eine Liste mit Positionen in diesem Parameter angegeben werden, und der Parameter *path* kann mehrmals angegeben werden, um mehrere Kreise, Linien und Polygone mit unterschiedlichen Stilen zu rendern.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

Hinsichtlich der Pfadpositionen müssen bei Azure Maps die Koordinaten im Format `longitude latitude` angegeben werden, während Bing Karten `latitude,longitude` verwendet. Beachten Sie auch, dass in Azure Maps **ein Leerzeichen anstelle eines Kommas als Trennzeichen** zwischen Längen- und Breitengrad verwendet wird. Azure Maps unterstützt aktuell keine codierten Pfade. Größere Datasets können als GeoJSON-Dateien in die Azure Maps-Datenspeicher-API hochgeladen werden, wie [hier](./how-to-render-custom-data.md#get-data-from-azure-maps-data-storage) beschrieben wird.

In Azure Maps werden Pfadstile im Format `optionNameValue` hinzugefügt, wobei mehrere Stile durch einen senkrechten Strich (`|`) voneinander getrennt werden, z. B. `optionName1Value1|optionName2Value2`. Beachten Sie, dass die Optionsnamen und -werte nicht voneinander getrennt werden. Die folgenden Stiloptionsnamen können für den Stil von Pfaden in Azure Maps verwendet werden:

-   `fa`: die Deckkraft der Füllfarbe (Alpha), die beim Rendern von Polygonen verwendet wird. Dies kann eine Zahl zwischen 0 und 1 sein.
-   `fc`: die Füllfarbe, die zum Rendern des Bereichs eines Polygons verwendet wird.
-   `la`: die Deckkraft der Linienfarbe (Alpha), die zum Rendern von Linien und der Umrandung von Polygonen verwendet wird. Dies kann eine Zahl zwischen 0 und 1 sein.
-   `lc`: die Linienfarbe, die zum Rendern von Linien und der Umrandung von Polygonen verwendet wird.
-   `lw`: die Breite der Linie in Pixel.
-   `ra`: gibt einen Kreisradius in Metern an.

In Azure Maps kann z. B. eine blaue Linie mit einer Deckkraft von 50 % und einer Stärke von vier Pixeln der Karte zwischen den Koordinaten (Längengrad: –110, Breitengrad: 45 und Längengrad: –100, Breitengrad: 50) mithilfe des folgenden URL-Parameters hinzugefügt werden:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

<center>

![Linie auf einer statischen Karte in Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)</center>

## <a name="calculate-a-distance-matrix"></a>Berechnen einer Entfernungsmatrix

Azure Maps bietet eine API zum Berechnen der Fahrzeiten und Entfernungen zwischen einer Reihe von Positionen als Entfernungsmatrix. Die Azure Maps-Entfernungsmatrix-API ist vergleichbar mit der Entfernungsmatrix-API von Bing Karten.

-   [Routenmatrix:](/rest/api/maps/route/postroutematrixpreview)  Berechnet asynchron die Reisezeiten und Entfernungen für einen Satz von Ausgangspunkten und Zielen. Es werden bis zu 700 Zellen pro Anforderung unterstützt (die Anzahl der Ausgangspunkte multipliziert mit der Anzahl der Ziele). Mit dieser Einschränkung sind beispielsweise folgende Matrixabmessungen möglich: `700x1`, `50x10`, `10x10`, `28x25`, `10x70`.

> [!NOTE]
> Eine Anforderung an die Entfernungsmatrix-API kann nur mithilfe einer POST-Anforderung erfolgen, die Informationen zum Ausgangs- und Zielpunkt im Anforderungstext enthält.</p>
<p>Außerdem erfordert Azure Maps, dass alle Ausgangspunkte und Ziele als Koordinaten angegeben werden. Adressen müssen zunächst geocodiert werden.

In der folgenden Tabelle werden die Parameter der Bing Karten-API den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter | Vergleichbarer Azure Maps-API-Parameter                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins`: wird im POST-Anforderungstext als GeoJSON angegeben.    |
| `destinations`          | `destination`: wird im POST-Anforderungstext als GeoJSON angegeben. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | –                                                         |
| `distanceUnit`          | N/V: alle Entfernungen in Metern.                              |
| `timeUnit`              | N/V: alle Zeitangaben in Sekunden.                                 |
| `key`                   | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)         | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md).  |
| `userRegion` (`ur`)     | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views).     |

> [!TIP]
> Alle erweiterten Routenplanungsoptionen, die in der Azure Maps-Routenplanungs-API verfügbar sind (Lkw-Routenplanung, Angaben zur Motorisierung, zu vermeidende Straßen, …), werden in der Azure Maps-Entfernungsmatrix-API unterstützt.

## <a name="calculate-an-isochrone"></a>Berechnen von Isochronen

Azure Maps stellt eine API für die Berechnung von Isochronen bereit, Polygonen, die einen Bereich abdecken, der von einem Ursprungspunkt innerhalb eines angegebenen Zeitraums oder einer angegebenen Treibstoffmenge bzw. angegebener Treibstoffkosten in beliebiger Richtung angesteuert werden kann. Die Azure Maps-Routenbereichs-API ist mit der Isochronen-API in Bing Karten vergleichbar:

-   [Routenbereich](/rest/api/maps/route/getrouterange)**: Berechnung eines Polygons, das einen Bereich abdeckt, der von einem Ursprungspunkt innerhalb eines angegebenen verfügbaren Zeitraums, einer Entfernung oder einer Treibstoffmenge bzw. zu bestimmten Treibstoffkosten in beliebiger Richtung angesteuert werden kann.

> [!NOTE]
> Für Azure Maps ist es erforderlich, dass der Abfrageursprung eine Koordinate ist. Adressen müssen zunächst geocodiert werden.</p>
<p>Ferner kann Bing Karten Isochrone auf der Grundlage von Zeit oder Entfernung berechnen, während Azure Maps Isochrone basierend auf Zeit, Entfernung oder verfügbarer Treibstoffmenge/Treibstoffkosten berechnen kann.

In der folgenden Tabelle werden die Parameter der Bing Karten-API den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter      | Vergleichbarer Azure Maps-API-Parameter            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | N/V: alle Zeitangaben in Sekunden.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | N/V: alle Entfernungen in Metern.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)              | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md).  |
| `userRegion` (`ur`)          | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views). |

> [!TIP]
> Alle erweiterten Routenplanungsoptionen, die in der Azure Maps-Routenplanungs-API verfügbar sind (Lkw-Routenplanung, Angaben zur Motorisierung, zu vermeidende Straßen, …), werden in der Azure Maps-Isochronen-API unterstützt.

## <a name="search-for-points-of-interest"></a>Suchen nach Points of Interest

Daten zu Points of Interest können in Bing Karten mithilfe der folgenden APIs gesucht werden.

-   **Lokale Suche**: Sucht nahe gelegene Point of Interest (radiale Suche), nach Namen oder nach Entitätstyp (Kategorie). Die Azure Maps-APIs [POI-Suche](/rest/api/maps/search/getsearchpoi) und [POI-Kategoriesuche](/rest/api/maps/search/getsearchpoicategory) sind dieser API sehr ähnlich.
-   **Standortbestimmung**: Sucht nach Points of Interest innerhalb einer bestimmten Entfernung zum Standort. Die Azure Maps-API zur [Umgebungssuche](/rest/api/maps/search/getsearchnearby) ist dieser API sehr ähnlich.
-   **Lokale Einblicke**: Sucht nach Points of Interest, die innerhalb einer angegebenen maximalen Fahrzeit oder Entfernung von einer bestimmten Koordinate liegen. Dies kann mit Azure Maps erreicht werden, indem zuerst ein Isochron berechnet und dann der API [Suche innerhalb einer Geometrie](/rest/api/maps/search/postsearchinsidegeometry) übergeben wird.

Azure Maps stellt mehrere Such-APIs für Points of Interest bereit:

-   [POI-Suche:](/rest/api/maps/search/getsearchpoi)  Sucht nach Points of Interest nach dem Namen. Beispielsweise `"starbucks"`.
-   [POI-Kategoriesuche:](/rest/api/maps/search/getsearchpoicategory)  Sucht nach Points of Interest nach der Kategorie. Beispiel: „Restaurant“.
-   [Umgebungssuche:](/rest/api/maps/search/getsearchnearby)  Sucht nach Points of Interest innerhalb einer bestimmten Entfernung zum Standort.
-   [Fuzzysuche:](/rest/api/maps/search/getsearchfuzzy)  Diese API kombiniert die Geocodierung von Adressen mit der Suche nach Points of Interest (POI). Diese API übernimmt eine Freiform-Zeichenfolge, die eine Adresse, ein Ort, ein Orientierungspunkt, ein Point of Interest oder eine POI-Kategorie sein kann, und verarbeitet die Anforderung sofort. Diese API wird für Anwendungen empfohlen, in denen Benutzer im selben Textfeld nach Adressen oder Points of Interest suchen können.
-   [Suche innerhalb einer Geometrie](/rest/api/maps/search/postsearchinsidegeometry): Sucht nach Points of Interest innerhalb einer angegebenen Geometrie (Polygon).
-   [Suche entlang einer Route:](/rest/api/maps/search/postsearchalongroute)  Sucht nach Points of Interest entlang einer angegebenen Route.
-   [Batchfuzzysuche:](/rest/api/maps/search/postsearchfuzzybatchpreview)  Erstellen Sie eine Anforderung mit bis zu 10.000 Adressen, Orten, Orientierungspunkten oder Points of Interest, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Daten werden auf dem Server parallel verarbeitet. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden.

Lesen Sie unbedingt die Dokumentation mit [bewährten Methoden für die Suche](./how-to-use-best-practices-for-search.md).

## <a name="get-traffic-incidents"></a>Abrufen von Verkehrsmeldungen

Azure Maps bietet mehrere APIs zum Abrufen von Verkehrsdaten. Zwei Typen von Verkehrsdaten sind verfügbar:

-   **Flussdaten**: Stellt Metriken zum Verkehrsfluss auf Straßenabschnitten bereit. Dies wird häufig verwendet, um Straßen farbig zu codieren. Diese Daten werden alle 2 Minuten aktualisiert.
-   **Störungsdaten**: Bietet Daten zu Baustellen, Straßensperrungen, Unfällen und anderen Vorfällen, die sich auf den Verkehr auswirken können. Diese Daten werden jede Minute aktualisiert.

Bing Karten stellt Verkehrsfluss- und Störungsdaten in seinen interaktiven Kartensteuerelementen zur Verfügung und stellt Störungsdaten auch als Dienst bereit.

Verkehrsinformationen sind auch in die interaktiven Kartensteuerelemente von Azure Maps integriert. Azure Maps bietet darüber hinaus die folgenden APIs für Verkehrsinformationen:

-   [Verkehrsfluss in Abschnitten](/rest/api/maps/traffic/gettrafficflowsegment): Stellt Informationen zu den Geschwindigkeiten und Fahrtzeiten für den Straßenabschnitt bereit, der am nächsten zu den angegebenen Koordinaten liegt.
-   [Kacheln zum Verkehrsfluss](/rest/api/maps/traffic/gettrafficflowtile): Stellt Raster- und Vektorkacheln mit Verkehrsflussdaten bereit. Diese können mit den Azure Maps-Steuerelementen oder in Kartensteuerelementen von Drittanbietern wie z. B. Leaflet verwendet werden. Die Vektorkacheln können außerdem zur erweiterten Datenanalyse dienen.
-   [Verkehrsinfodetails](/rest/api/maps/traffic/gettrafficincidentdetail): Bietet Detailinformationen zu Störungen im Straßenverkehr innerhalb eines Begrenzungsrahmens, einer Zoomstufe und eines Verkehrsmodells.
-   [Kacheln zu Verkehrsstörungen](/rest/api/maps/traffic/gettrafficincidenttile): Stellt Raster- und Vektorkacheln mit Daten zu Verkehrsstörungen bereit.
-   [Anzeigebereich für Verkehrsstörungen](/rest/api/maps/traffic/gettrafficincidentviewport): Ruft die rechtlichen und technischen Informationen für den in der Anforderung beschriebenen Anzeigebereich ab, z. B. die ID des Verkehrsmodells.

In der folgenden Tabelle werden die Parameter der Verkehrs-API von Bing Karten den vergleichbaren API-Parametern für Verkehrsstörungsdetails in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter  | Vergleichbarer Azure Maps-API-Parameter   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` und `boundingZoom`      |
| `includeLocationCodes`   | –                                   |
| `severity` (`s`)         | N/V: alle Daten zurückgegeben               |
| `type` (`t`)             | N/V: alle Daten zurückgegeben               |
| `key`                    | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)          | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md). |
| `userRegion` (`ur`)      | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views). |

## <a name="get-a-time-zone"></a>Abrufen einer Zeitzone

Azure Maps bietet eine API zum Abrufen der Zeitzone, in der sich eine Koordinate befindet. Die Azure Maps-Zeitzonen-API ist mit der Zeitzonen-API von Bing Karten vergleichbar:

-   [Zeitzone nach Koordinate](/rest/api/maps/timezone/gettimezonebycoordinates): Wenn Sie eine Koordinate angeben, erhalten Sie die Details zu der Zeitzone, in der sie liegt.

In der folgenden Tabelle werden die Parameter der Bing Karten-API den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Bing Karten-API-Parameter | Vergleichbarer Azure Maps-API-Parameter          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | N/V: Standorte müssen zuerst geocodiert werden.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | N/V: von Azure Maps immer in die Antwort aufgenommen. |
| `key`                   | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)         | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](./supported-languages.md).  |
| `userRegion` (`ur`)     | `view`: Lesen Sie die Dokumentation zu den [unterstützten Ansichten](./supported-languages.md#azure-maps-supported-views).  |

Darüber hinaus bietet die Azure Maps-Plattform auch eine Reihe zusätzlicher Zeitzonen-APIs, um Konvertierungen mit Zeitzonennamen und -IDs zu unterstützen:

-   [Zeitzone nach ID:](/rest/api/maps/timezone/gettimezonebyid)  Gibt aktuelle, historische und zukünftige Zeitzoneninformationen für die angegebene IANA-Zeitzonen-ID zurück.
-   [Zeitzonenenumeration für IANA:](/rest/api/maps/timezone/gettimezoneenumiana)  Gibt eine vollständige Liste der IANA-Zeitzonen-IDs zurück. Updates für den IANA-Dienst werden innerhalb eines Tages im System übernommen. 
-   [Zeitzonenenumeration für Windows:](/rest/api/maps/timezone/gettimezoneenumwindows)  Gibt eine vollständige Liste der Windows-Zeitzonen-IDs zurück.
-   [IANA-Version der Zeitzone:](/rest/api/maps/timezone/gettimezoneianaversion)  Gibt die aktuelle IANA-Versionsnummer zurück, die von Azure Maps verwendet wird. 
-   [Zeitzone: Windows nach IANA:](/rest/api/maps/timezone/gettimezonewindowstoiana)  Gibt die entsprechende IANA-ID für eine angegebene gültige Windows-Zeitzonen-ID zurück. Es können mehrere IANA-IDs eine einzelne Windows-ID zurückgegeben werden.

## <a name="spatial-data-services-sds"></a>Spatial Data Services (SDS)

Die Dienste für räumliche Daten in Bing Karten bieten drei Hauptfunktionen:

-   Batchgeocodierung: Verarbeiten eines großen Batches von Adressengeocodes mit einer einzelnen Anforderung.
-   Abruf von Daten zu Verwaltungsgrenzen: Verwenden Sie eine Koordinate, und rufen Sie eine sich mit ihr schneidende Grenze für einen angegebenen Entitätstyp ab.
-   Hosten und Abfragen von räumlichen Geschäftsdaten: Laden Sie eine einfache 2D-Datentabelle hoch, und greifen Sie mit einigen einfachen räumlichen Abfragen darauf zu.

### <a name="batch-geocode-data"></a>Batchgeocodierung von Daten

Batchgeocodierung ist der Vorgang des Annehmens einer großen Anzahl von Adressen oder Ortsangaben, die alle in einer einzelnen Anforderung an einen Dienst übergeben und parallel geocodiert werden. Die Ergebnisse werden in einer einzelnen Antwort zurückgegeben.

Bing Karten erlaubt die Übergabe von bis zu 200.000 Adressen in einer einzelnen Geocoding-Batchanforderung. Diese Anforderung wird einer Warteschlange übergeben und in der Regel über einen Zeitraum abgearbeitet, der zwischen wenigen Minuten bis zu einigen Stunden dauern kann, abhängig von der Größe des Datasets und der Auslastung des Diensts. Für jede Adresse in der Anforderung wird eine Transaktion generiert.

Azure Maps verfügt über einen Dienst zur Batchgeocodierung, der jedoch nur die Übergabe von bis zu 10.000 Adressen in einer einzelnen Anforderung zulässt. Diese werden innerhalb von Sekunden bis hin zu einigen Minuten verarbeitet, je nach dem Umfang des Datasets und der Auslastung des Diensts. Für jede Adresse in der Anforderung wird eine Transaktion generiert. In Azure Maps steht der Dienst zur Batchgeocodierung nur im S1-Tarif zur Verfügung.

Eine weitere Möglichkeit für die Geocodierung einer großen Anzahl von Adressen mit Azure Maps besteht darin, parallele Anforderungen an die Standard-Such-APIs zu senden. Diese Dienste akzeptieren nur eine einzelne Adresse pro Anforderung, können aber im S0-Tarif verwendet werden, der auch Grenzwerte für die kostenlose Nutzung beinhaltet. Im S0-Tarif sind bis zu 50 Anforderungen pro Sekunde an die Azure Maps-Plattform von einem einzelnen Konto aus möglich. Wenn Sie die Verarbeitung so gestalten, dass dieser Grenzwert eingehalten wird, können mehr als 180.000 Adressen pro Stunde geocodiert werden. Für den Tarif S1 ist keine Beschränkung für die Anzahl der Abfragen pro Sekunde dokumentiert, die von einem Konto durchgeführt werden können. Daher können bei Verwendung dieses Tarifs viel mehr Daten schneller verarbeitet werden. Vor allem hilft der Dienst zur Batchgeocodierung aber, die Gesamtmenge der übertragenen Daten zu reduzieren, sodass sich der Datenverkehr im Netzwerk drastisch reduziert.

-   [Freiform-Geocodierung von Adressen](/rest/api/maps/search/getsearchaddress): Geben Sie eine einzelne Adresszeichenfolge (wie `"1 Microsoft way, Redmond, WA"`) an, und verarbeiten Sie die Anforderung sofort. Dieser Dienst wird zur schnellen Geocodierung einzelner Adressen empfohlen.
-   [Strukturierte Geocodierung von Adressen](/rest/api/maps/search/getsearchaddressstructured):  Geben Sie die Teile einer einzelnen Adresse an, z. B. den Namen der Straße, die Stadt, das Land bzw. die Region und die Postleitzahl, und verarbeiten Sie die Anforderung sofort. Dieser Dienst wird empfohlen, wenn Sie einzelne Adressen schnell geocodieren müssen und die Daten bereits als einzelne Adressbestandteile analysiert haben.
-   [Batch-Geocodierung von Adressen:](/rest/api/maps/search/postsearchaddressbatchpreview)  Erstellen Sie eine Anforderung mit bis zu 10.000 Adressen, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Adressen werden auf dem Server parallel geocodiert. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden. Dieser Dienst wird für die Geocodierung großer Datasets empfohlen.
-   [Fuzzysuche:](/rest/api/maps/search/getsearchfuzzy)  Diese API kombiniert die Geocodierung von Adressen mit der Suche nach Points of Interest (POI). Diese API übernimmt eine Freiform-Zeichenfolge, die eine Adresse, ein Ort, ein Orientierungspunkt, ein Point of Interest oder eine POI-Kategorie sein kann, und verarbeitet die Anforderung sofort. Diese API wird für Anwendungen empfohlen, in denen Benutzer im selben Textfeld nach Adressen oder Points of Interest suchen können.
-   [Batchfuzzysuche:](/rest/api/maps/search/postsearchfuzzybatchpreview)  Erstellen Sie eine Anforderung mit bis zu 10.000 Adressen, Orten, Orientierungspunkten oder Points of Interest, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Daten werden auf dem Server parallel verarbeitet. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden.

### <a name="get-administrative-boundary-data"></a>Abrufen von Daten zu Verwaltungsgrenzen

In Bing Karten werden Verwaltungsgrenzen für Länder, Bundesländer, Verwaltungsbezirke, Städte und Postleitzahlen über die Geodaten-API zur Verfügung gestellt. Diese API akzeptiert entweder eine Koordinate oder eine Abfrage zur Geocodierung. Wenn eine Abfrage übermittelt wird, wird sie geocodiert, und die Koordinaten des ersten Ergebnisses werden verwendet. Diese API übernimmt die Koordinaten und ruft die Begrenzung des angegebenen Entitätstyps ab, der die Koordinate schneidet. Beachten Sie, dass diese API nicht zwangsläufig die Grenze für die übergebene Abfrage zurückgibt. Wenn eine Abfrage für `"Seattle, WA"` übergeben wird, aber der Wert für den Entitätstyp auf den Bereich „Land“ festgelegt ist, wird die Grenze für die USA zurückgegeben.

Azure Maps bietet ebenfalls Zugriff auf Verwaltungsgrenzen (Länder, Bundesländer, Verwaltungsbezirke, Städte und Postleitzahlen). Zum Abrufen einer Grenze müssen Sie eine der Such-APIs nach der gewünschten Grenze abfragen (d. h. `Seattle, WA`). Wenn dem Suchergebnis eine Grenze zugeordnet ist, wird in der Ergebnisantwort eine Geometrie-ID angegeben. Die Suchpolygon-API kann dann verwendet werden, um die exakten Grenzen für eine oder mehrere Geometrie-IDs abzurufen. Dies unterscheidet sich etwas von Bing Karten, insofern als Azure Maps die Grenzen für das Gesuchte zurückgibt, während Bing Karten eine Grenze für einen angegebenen Entitätstyp an einer angegebenen Koordinate zurückgibt. Außerdem liegen die von Azure Maps zurückgegebenen Grenzdaten im GeoJSON-Format vor.

In der Zusammenfassung:

1.  Übergeben Sie eine Abfrage für die Grenze, die Sie erhalten möchten, an eine der folgenden Such-APIs.
    -   [Freiform-Geocodierung von Adressen](/rest/api/maps/search/getsearchaddress)
    -   [Strukturierte Geocodierung von Adressen](/rest/api/maps/search/getsearchaddressstructured)
    -   [Batch-Geocodierung von Adressen](/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Fuzzysuche](/rest/api/maps/search/getsearchfuzzy)
    -   [Batchfuzzysuche](/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Wenn die gewünschten Ergebnisse Geometrie-IDs aufweisen, übergeben Sie sie an die [Suchpolygon-API](/rest/api/maps/search/getsearchpolygon).

### <a name="host-and-query-spatial-business-data"></a>Hosten und Abfragen räumlicher Geschäftsdaten

Die räumlichen Datendienste in Bing Karten bieten eine einfache Speicherlösung für räumliche Daten, mit der Geschäftsdaten gehostet und als räumlicher REST-Dienst zur Verfügung gestellt werden können. Für diesen Dienst stehen vier Hauptabfragen zur Verfügung: Suche nach Eigenschaft, Umgebungssuche, Suche in Begrenzungsrahmen und Suche innerhalb einer Meile abseits von einer Route. Viele Unternehmen, die diesen Dienst verwenden, haben Ihre Geschäftsdaten oftmals bereits in einer Datenbank gespeichert und eine kleine Teilmenge in diesen Dienst hochgeladen, um Anwendungen wie Shopsuchen zu speisen. Da die schlüsselbasierte Authentifizierung nur für ein Grundniveau an Sicherheit sorgt, wurde die Empfehlung ausgesprochen, dass dieser Dienst nur mit öffentlich zugänglichen Daten verwendet werden sollte.

Die meisten Daten zu Geschäftsstandorten haben ihren Ursprung in einer Datenbank. Daher empfiehlt es sich, vorhandene Azure-Speicherlösungen wie Azure SQL oder Azure PostgreSQL (mit dem PostGIS-Plug-In) zu verwenden. Beide Speicherlösungen unterstützen räumliche Daten und bieten einen umfangreichen Satz von Funktionen für räumliche Abfragen. Wenn sich Ihre Daten in einer geeigneten Speicherlösung befinden, können Sie sie in Ihre Anwendung integrieren, indem Sie einen benutzerdefinierten Webdienst erstellen oder ein Framework wie ASP.NET oder Entity Framework verwenden. Dieser Ansatz bietet Ihnen mehr Abfragefunktionen sowie weitaus höher entwickelte Sicherheitsoptionen.

In Azure Cosmos DB steht ebenfalls eine eingeschränkte Menge räumlicher Funktionen zur Verfügung, die je nach Szenario ausreichend sein können.

Hier finden Sie einige nützliche Ressourcen zum Hosting und Abfragen räumlicher Daten in Azure.

-   [Azure SQL – Übersicht über räumliche Datentypen](/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Azure SQL Spatial – Suche des nächsten Nachbarn](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Azure Cosmos DB – Übersicht der räumlichen Funktionen](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>Clientbibliotheken

Azure Maps stellt Clientbibliotheken für die folgenden Programmiersprachen bereit:

-   JavaScript, TypeScript, Node.js: [Dokumentation](./how-to-use-services-module.md) \| [NPM-Paket](https://www.npmjs.com/package/azure-maps-rest)

Open-Source-Clientbibliotheken für andere Programmiersprachen:

-   .NET Standard 2.0: [GitHub-Projekt](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-Paket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure Maps-REST-Dienste:

> [!div class="nextstepaction"]
> [Bewährte Methoden für die Verwendung des Suchdiensts](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Bewährte Methoden für die Verwendung des Routingdienst](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Verwenden des Dienstmoduls (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Azure Maps-REST-Dienste-API – Referenzdokumentation](/rest/api/maps/)

> [!div class="nextstepaction"]
> [Codebeispiele](/samples/browse/?products=azure-maps)
