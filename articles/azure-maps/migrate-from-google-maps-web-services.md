---
title: 'Tutorial: Migrieren von Webdiensten aus Google Maps | Microsoft Azure Maps'
description: Tutorial zum Migrieren von Webdiensten aus Google Maps zu Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 813cb567ab3edddd6fb37cee050dc5e38ee4289f
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904889"
---
# <a name="tutorial---migrate-web-service-from-google-maps"></a>Tutorial: Migrieren von Webdiensten aus Google Maps

Sowohl Azure als auch Google Maps ermöglichen den Zugriff auf räumliche APIs über REST-Webdienste. Die API-Schnittstellen dieser Plattformen bieten ähnliche Funktionen. Die Namenskonventionen und Antwortobjekte unterscheiden sich jedoch jeweils.

In diesem Tutorial lernen Sie Folgendes:

> * Geocodierung vorwärts/reversiert
> * Suchen nach Points of Interest
> * Berechnen von Routen und Wegbeschreibungen
> * Abrufen eines Kartenbilds
> * Berechnen einer Entfernungsmatrix
> * Abrufen von Zeitzonendetails

Außerdem lernen Sie Folgendes: 

> [!div class="checklist"]
> * Wahl des Azure Maps-REST-Diensts für die Migration von einem Google Maps-Webdienst
> * Tipps für die optimale Nutzung der Azure Maps-Dienste
> * Einblicke in andere verwandte Azure Maps Dienste

Die folgende Tabelle enthält die Dienst-APIs von Azure Maps, die über ähnliche Funktionen verfügen wie die aufgeführten Dienst-APIs von Google Maps:

| Google Maps-Dienst-API | Azure Maps-Dienst-API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Wegbeschreibungen              | [Route](/rest/api/maps/route)                                     |                         
| Entfernungsmatrix         | [Routenmatrix](/rest/api/maps/route/postroutematrixpreview)       |                         
| Geocodierung               | [Suchen,](/rest/api/maps/search)                                   |                         
| Suche nach Orten           | [Suchen,](/rest/api/maps/search)                                   |                         
| AutoVervollständigen von Orten      | [Suchen,](/rest/api/maps/search)                                   |                         
| Ausrichtung an Straße            | Weitere Informationen finden Sie im Abschnitt [Berechnen von Routen und Wegbeschreibungen](#calculate-routes-and-directions).            
| Geschwindigkeitsbegrenzungen            | Weitere Informationen finden Sie unter [Umgekehrte Geocodierung einer Koordinate](#reverse-geocode-a-coordinate).                  
| Statische Karte              | [Render](/rest/api/maps/render/getmapimage)                       |                         
| Zeitzone               | [Zeitzone](/rest/api/maps/timezone)                              |                         
| Elevation               | [Höhe (Vorschauversion)](/rest/api/maps/elevation)                   |                         |

Die folgenden Dienst-APIs stehen in Azure Maps derzeit nicht zur Verfügung:

- Geolocation
- Details zu Orten und Fotos: Telefonnummern und Website-URL stehen in der Azure Maps-Such-API zur Verfügung.
- Karten-URLs
- Nächstgelegene Straßen: Diese Information kann wie [hier](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
) beschrieben über das Web-SDK ermittelt werden, sie steht aktuell jedoch nicht als Dienst zur Verfügung.
- Statische Straßenansicht

Azure Maps verfügt über mehrere zusätzliche REST-Webdienste, die möglicherweise von Interesse sind:

- [Räumliche Vorgänge:](/rest/api/maps/spatial) Lagern Sie komplexe räumliche Berechnungen und Vorgänge, wie z. B. Geofencing, in einen Dienst aus.
- [Datenverkehr:](/rest/api/maps/traffic) Greifen Sie in Echtzeit auf den Datenverkehrsfluss und die Vorfallsdaten zu.

## <a name="prerequisites"></a>Voraussetzungen 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
2. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt) Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

## <a name="geocoding-addresses"></a>Geocodierung von Adressen

Bei der Geocodierung wird eine Adresse in eine Koordinate konvertiert. Die Adresse „1 Microsoft Way, Redmond, WA“ wird beispielsweise in eine Koordinate mit dem Längengrad -122,1298 und dem Breitengrad 47,64005 konvertiert. Koordinaten können dann für verschiedene Zwecke verwendet werden – etwa, um einen Marker auf einer Karte zu zentrieren.

Azure Maps bietet mehrere Methoden zur Geocodierung von Adressen:

- [**Freiform-Geocodierung von Adressen:**](/rest/api/maps/search/getsearchaddress) Geben Sie eine einzelne Adresszeichenfolge an, und verarbeiten Sie die Anforderung sofort. Ein Beispiel für eine einzelne Adresszeichenfolge wäre etwa „1 Microsoft Way, Redmond, WA“. Diese API wird zur schnellen Geocodierung einzelner Adressen empfohlen.
- [**Strukturierte Geocodierung von Adressen:**](/rest/api/maps/search/getsearchaddressstructured) Geben Sie die Teile einer einzelnen Adresse an, z. B. den Namen der Straße, die Stadt, das Land bzw. die Region und die Postleitzahl, und verarbeiten Sie die Anforderung sofort. Diese API wird empfohlen, wenn Sie einzelne Adressen schnell geocodieren müssen und die Daten bereits als einzelne Adressbestandteile analysiert haben.
- [**Batch-Geocodierung von Adressen:**](/rest/api/maps/search/postsearchaddressbatchpreview) Erstellen Sie eine Anforderung mit bis zu 10.000 Adressen, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Adressen werden auf dem Server parallel geocodiert. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden. Dies wird für die Geocodierung großer Datasets empfohlen.
- [**Fuzzysuche:**](/rest/api/maps/search/getsearchfuzzy) Diese API kombiniert die Geocodierung von Adressen mit der Suche nach Points of Interest (POI). Diese API akzeptiert eine Freiform-Zeichenfolge. Bei dieser Zeichenfolge kann es sich um eine Adresse, einen Ort, einen Orientierungspunkt, einen Point of Interest oder eine POI-Kategorie handeln. Die Anforderung wird von der API nahezu in Echtzeit verarbeitet. Diese API wird für Anwendungen empfohlen, in denen Benutzer für die Suche nach Adressen oder Points of Interest das gleiche Textfeld verwenden.
- [**Batchfuzzysuche:**](/rest/api/maps/search/postsearchfuzzybatchpreview) Erstellen Sie eine Anforderung mit bis zu 10.000 Adressen, Orten, Orientierungspunkten oder Points of Interest, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Daten werden auf dem Server parallel verarbeitet. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden.

In der folgenden Tabelle werden die Parameter der Google Maps-API den vergleichbaren API-Parametern in Azure Maps gegenübergestellt.

| Google Maps-API-Parameter | Vergleichbarer Azure Maps-API-Parameter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` und `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`: Stadt/Ort<br/>`municipalitySubdivision`: Umgebung, Vorort/Metropolenregion<br/>`countrySubdivision`: Bundesland oder Kanton<br/>`countrySecondarySubdivision`: Landkreis<br/>`countryTertiarySubdivision`: Verwaltungsbezirk<br/>`countryCode`: Länder-/Regionscode (zwei Ziffern) |
| `key`                       | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](supported-languages.md).  |
| `region`                    | `countrySet`                       |

Ein Beispiel für die Verwendung des Suchdiensts finden Sie [hier](how-to-search-for-address.md). Machen Sie sich unbedingt mit den [bewährten Methoden für die Suche](how-to-use-best-practices-for-search.md) vertraut.

> [!TIP]
> Die Freiform-Geocodierung von Adressen und die APIs für die Fuzzysuche können im AutoVervollständigen-Modus verwendet werden, indem der Anforderungs-URL `&typeahead=true` hinzugefügt wird. Dadurch wird dem Server mitgeteilt, dass der Eingabetext wahrscheinlich unvollständig ist, woraufhin die Suche in den Vorhersagemodus wechselt.

## <a name="reverse-geocode-a-coordinate"></a>Umgekehrte Geocodierung einer Koordinate

Bei der umgekehrten Geocodierung werden geografische Koordinaten in eine ungefähre Adresse konvertiert. Koordinaten mit dem Längengrad -122,1298 und dem Breitengrad 47,64005 werden beispielsweise in "1 Microsoft Way, Redmond, WA“ konvertiert.

Azure Maps bietet mehrere Methoden für die umgekehrte Geocodierung:

- [**Umgekehrte Geocodierung von Adressen:**](/rest/api/maps/search/getsearchaddressreverse) Geben Sie eine einzelne geografische Koordinate an, um die ungefähre Adresse zu erhalten, die dieser Koordinate entspricht. Die Anforderung wird nahezu in Echtzeit verarbeitet.
- [**Umgekehrte Geocodierung nach Straßenkreuzungen:**](/rest/api/maps/search/getsearchaddressreversecrossstreet) Geben Sie eine einzelne geografische Koordinate an, um in der Nähe gelegene Straßenkreuzungen abzurufen. Die Anforderung wird sofort verarbeitet. Ein Beispiel für ein Ergebnis wäre etwa die Straßenkreuzung „1st Ave und Main St.“.
- [**Umgekehrte Batch-Geocodierung von Adressen:**](/rest/api/maps/search/postsearchaddressreversebatchpreview) Erstellen Sie eine Anforderung mit bis zu 10.000 Koordinaten, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Daten werden parallel auf dem Server verarbeitet. Nach Abschluss der Anforderung kann das vollständige Resultset heruntergeladen werden.

In der folgenden Tabelle werden die API-Parameter von Google Maps den vergleichbaren API-Parametern in Azure Maps gegenübergestellt:

| Google Maps-API-Parameter   | Vergleichbarer Azure Maps-API-Parameter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](supported-languages.md).  |
| `latlng`                    | `query`  |
| `location_type`             | *N/V*     |
| `result_type`               | `entityType`    |

Machen Sie sich mit den [bewährten Methoden für die Suche](how-to-use-best-practices-for-search.md) vertraut.

Die Azure Maps-API für die umgekehrte Geocodierung bietet einige zusätzliche Features, die in Google Maps nicht verfügbar sind. Es empfiehlt sich ggf., diese Features im Zuge der App-Migration in Ihre Anwendung zu integrieren:

- Abrufen von Daten zu Geschwindigkeitsbegrenzungen
- Abrufen von Informationen zur Straßennutzung: lokale Straße, Umgehungsstraße, eingeschränkte Zufahrt, Auffahrt und Ähnliches
- Abrufen der Straßenseite einer Koordinate

## <a name="search-for-points-of-interest"></a>Suchen nach Points of Interest

Daten zu Points of Interest können in Google Maps mithilfe der Such-API für Orte gesucht werden. Diese API bietet drei verschiedene Möglichkeiten zum Suchen nach Points of Interest:

- **Suchen von Orten aus Text:** Sucht nach einem Point of Interest anhand des Namens, der Adresse oder der Telefonnummer.
- **Umgebungssuche:** Sucht nach Points of Interest innerhalb einer bestimmten Entfernung zum Standort.
- **Textsuche:** Sucht nach Orten anhand von Freiformtext (mit Point of Interest und Standortangabe). Beispiele sind „Pizza in New York“ oder „Restaurants auf der Hauptstraße“.

Azure Maps stellt mehrere Such-APIs für Points of Interest bereit:

- [**POI-Suche:**](/rest/api/maps/search/getsearchpoi) Sucht nach Points of Interest nach dem Namen. Beispiel: „Starbucks“.
- [**POI-Kategoriesuche:**](/rest/api/maps/search/getsearchpoicategory) Sucht nach Points of Interest nach der Kategorie. Beispiel: „Restaurant“.
- [**Umgebungssuche:**](/rest/api/maps/search/getsearchnearby) Sucht nach Points of Interest innerhalb einer bestimmten Entfernung zum Standort.
- [**Fuzzysuche:**](/rest/api/maps/search/getsearchfuzzy) Diese API kombiniert die Geocodierung von Adressen mit der Suche nach Points of Interest (POI). Diese API akzeptiert eine Freiform-Zeichenfolge, die eine Adresse, ein Ort, ein Orientierungspunkt, ein Point of Interest oder eine POI-Kategorie sein kann. Die Anforderung wird nahezu in Echtzeit verarbeitet. Diese API wird für Anwendungen empfohlen, in denen Benutzer für die Suche nach Adressen oder Points of Interest das gleiche Textfeld verwenden.
- [**Suche innerhalb einer Geometrie:**](/rest/api/maps/search/postsearchinsidegeometry) Sucht nach Points of Interest innerhalb einer angegebenen Geometrie. Dadurch kann beispielsweise nach einem Point of Interest innerhalb eines Polygons gesucht werden.
- [**Suche entlang einer Route:**](/rest/api/maps/search/postsearchalongroute) Sucht nach Points of Interest entlang einer angegebenen Route.
- [**Batchfuzzysuche:**](/rest/api/maps/search/postsearchfuzzybatchpreview) Erstellen Sie eine Anforderung mit bis zu 10.000 Adressen, Orten, Orientierungspunkten oder Points of Interest. Die Anforderung wird über einen bestimmten Zeitraum verarbeitet. Alle Daten werden parallel auf dem Server verarbeitet. Nach Abschluss der Verarbeitung kann das vollständige Resultset heruntergeladen werden.

Azure Maps verfügt aktuell über keine API, die mit der Textsuche-API in Google Maps vergleichbar ist.

> [!TIP]
> Die APIs für die POI-Suche, die POI-Kategoriesuche und die Fuzzysuche können im AutoVervollständigen-Modus verwendet werden, indem der Anforderungs-URL `&typeahead=true` hinzugefügt wird. Dadurch wird dem Server mitgeteilt, dass der Eingabetext wahrscheinlich unvollständig ist, woraufhin die Suche im Vorhersagemodus durchgeführt wird.

Lesen Sie die Dokumentation mit [bewährten Methoden für die Suche](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Suchen von Orten aus Text

Verwenden Sie die [POI-Suche](/rest/api/maps/search/getsearchpoi) und die [Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) von Azure Maps, um anhand von Name oder Adresse nach Points of Interest zu suchen.

In der folgenden Tabelle werden die API-Parameter von Google Maps den vergleichbaren API-Parametern von Azure Maps gegenübergestellt:

| Google Maps-API-Parameter | Vergleichbarer Azure Maps-API-Parameter |
|---------------------------|-------------------------------------|
| `fields`                  | *N/V*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/V*                               |
| `key`                     | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](azure-maps-authentication.md). |
| `language`                | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](supported-languages.md).  |
| `locationbias`            | `lat`, `lon` und `radius`<br/>`topLeft` und `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Umgebungssuche

Verwenden Sie in Azure Maps die API für die [Umgebungssuche](/rest/api/maps/search/getsearchnearby), um nahe gelegene Points of Interest abzurufen.

Die folgende Tabelle enthält die API-Parameter von Google Maps sowie die vergleichbaren API-Parameter von Azure Maps:

| Google Maps-API-Parameter | Vergleichbarer Azure Maps-API-Parameter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](azure-maps-authentication.md). |
| `keyword`                   | `categorySet` und `brandSet`        |
| `language`                  | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](supported-languages.md).  |
| `location`                  | `lat` und `lon`                     |
| `maxprice`                  | *N/V*                               |
| `minprice`                  | *N/V*                               |
| `name`                      | `categorySet` und `brandSet`        |
| `opennow`                   | *N/V*                               |
| `pagetoken`                 | `ofs` und `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/V*                               |
| `type`                      | `categorySet –` Weitere Informationen finden Sie in der Dokumentation zu den [unterstützten Suchkategorien](supported-search-categories.md).   |

## <a name="calculate-routes-and-directions"></a>Berechnen von Routen und Wegbeschreibungen

Berechnen Sie Routen und Wegbeschreibungen mithilfe von Azure Maps. Azure Maps verfügt über viele der gleichen Funktionen wie der Routenplanungsdienst von Google Maps, z. B.:

- Ankunfts- und Abfahrzeiten
- Routen anhand des echtzeitbasierten und vorhergesagten Verkehrsaufkommens
- Unterschiedliche Fortbewegungsarten (beispielsweise mit dem Auto, zu Fuß oder mit dem Fahrrad)

> [!NOTE]
> Bei Azure Maps müssen alle Wegpunkte Koordinaten sein. Adressen müssen zuerst geocodiert werden.

Der Routenplanungsdienst von Azure Maps stellt die folgenden APIs zum Berechnen von Routen bereit:

- [**Berechnen der Route:**](/rest/api/maps/route/getroutedirections) Berechnen Sie die Route, und lassen Sie die Anforderung sofort verarbeiten. Diese API unterstützt sowohl GET- als auch POST-Anforderungen. POST-Anforderungen werden empfohlen, wenn eine große Anzahl von Wegpunkten angegeben wird oder wenn viele der Routenoptionen verwendet werden, um sicherzustellen, dass die URL-Anforderung nicht zu lang wird und Probleme verursacht. Für die POST-Anforderung im Zusammenhang mit der Routenbeschreibung in Azure Maps steht eine Option zur Verfügung, die Tausende unterstützende Punkte ([SupportingPoints](/rest/api/maps/route/postroutedirections#supportingpoints)) akzeptiert und diese verwendet, um eine logische Route zwischen ihnen zu erstellen (Ausrichtung an Straße). 
- [**Batchroute:**](/rest/api/maps/route/postroutedirectionsbatchpreview) Erstellen Sie eine Anforderung mit bis zu 1.000 Routenanforderungen, und lassen Sie sie über einen bestimmten Zeitraum verarbeiten. Alle Daten werden auf dem Server parallel verarbeitet. Nach Abschluss des Vorgangs kann das vollständige Resultset heruntergeladen werden.
- [**Mobilitätsdienste (Vorschauversion)**](/rest/api/maps/mobility): Berechnen Sie Routen und Wegbeschreibungen mit dem öffentlichen Nahverkehr.

In der folgenden Tabelle werden die API-Parameter von Google Maps den vergleichbaren API-Parametern in Azure Maps gegenübergestellt:

| Google Maps-API-Parameter    | Vergleichbarer Azure Maps-API-Parameter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`: Koordinaten im Format `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](azure-maps-authentication.md). |
| `language`                     | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](supported-languages.md).   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *Nicht verfügbar:* Dieses Feature hängt mit der Geocodierung zusammen. Verwenden Sie den Parameter *countrySet*, wenn Sie die Geocodierung-API von Azure Maps verwenden.  |
| `traffic_model`               | *Nicht verfügbar:* Es kann nur angegeben werden, ob Verkehrsinfos mit dem Parameter *traffic* verwendet werden sollen. |
| `transit_mode`                | Weitere Informationen finden Sie in der [Dokumentation zu den Mobilitätsdiensten (Vorschauversion)](/rest/api/maps/mobility). |
| `transit_routing_preference` | Weitere Informationen finden Sie in der [Dokumentation zu den Mobilitätsdiensten (Vorschauversion)](/rest/api/maps/mobility). |
| `units`                        | *Nicht verfügbar:* Bei Azure Maps wird ausschließlich das metrische System verwendet.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Von der Routen-API von Azure Maps wird gibt standardmäßig nur eine Zusammenfassung zurückgegeben. Sie gibt die Entfernung und Zeiten sowie die Koordinaten für die Route zurück. Verwenden Sie den Parameter `instructionsType`, um ausführliche Anweisungen abzurufen. Verwenden Sie außerdem den Parameter `routeRepresentation`, um die Zusammenfassung und die Route herauszufiltern.

Die Routen-API von Azure Maps verfügt über zusätzliche Features, die in Google Maps nicht verfügbar sind. Wenn Sie Ihre App migrieren, empfiehlt es sich ggf., diese Features zu verwenden, da sie unter Umständen hilfreich sind.

- Unterstützung für den Routentyp: kürzeste, schnellste, attraktivste und kraftstoffeffizienteste
- Unterstützung für zusätzliche Reisemodi: Bus, Motorrad, Taxi, Lkw und Kleinbus
- Unterstützung für 150 Wegpunkte
- Berechnung mehrerer Fahrzeiten in einer einzelnen Anforderung: bisheriger Verkehr, aktueller Verkehr, kein Verkehr
- Vermeidung zusätzlicher Straßentypen: Straßen von Fahrgemeinschaften, ungepflasterte Straßen, bereits verwendete Straßen
- Eigene Angabe zu vermeidender Bereiche
- Höhenbegrenzung für die Route
- Auf Motorangaben basierende Route: Berechnen Sie Routen für Fahrzeuge mit Verbrennungs- oder Elektromotor basierend auf Motorangaben und dem verbleibenden Kraftstoff bzw. Ladestand.
- Unterstützung von Routenparametern für Nutzfahrzeuge (Fahrzeugabmessungen, Gewicht, Anzahl der Achsen, Art der Ladung und Ähnliches)
- Angabe der maximale Fahrzeuggeschwindigkeit

Darüber hinaus unterstützt der Routendienst in Azure Maps auch die [Berechnung von Bereichen für Routen](/rest/api/maps/route/getrouterange). Diese werden auch als Isochrone bezeichnet. Hierzu wird ein Polygon für einen Bereich generiert, der von einem Ausgangspunkt aus in beliebiger Richtung zu erreichen ist (innerhalb eines bestimmten Zeitraums oder mit einer bestimmten Kraftstoffmenge bzw. einer bestimmten Akkuladung).

Lesen Sie die Dokumentation mit [bewährten Methoden für die Routenplanung](how-to-use-best-practices-for-routing.md).

## <a name="retrieve-a-map-image"></a>Abrufen eines Kartenbilds

Azure Maps stellt eine API für das Rendern der statischen Kartenbilder mit überlagerten Daten bereit. Die [Kartenbildrendering](/rest/api/maps/render/getmapimagerytile)-API in Azure Maps ist mit der Google Maps-API für statische Karten vergleichbar.

> [!NOTE]
> Bei Azure Maps müssen der Mittelpunkt, alle Marker sowie die Pfadorte als Koordinaten im Format „Längengrad,Breitengrad“ vorliegen. Von Google Maps wird dagegen das Format „Breitengrad,Längengrad“ verwendet. Adressen müssen zunächst geocodiert werden.

In der folgenden Tabelle werden die API-Parameter von Google Maps den vergleichbaren API-Parametern in Azure Maps gegenübergestellt:

| Google Maps-API-Parameter | Vergleichbarer Azure Maps-API-Parameter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`: wird als Teil des URL-Pfads angegeben. Derzeit wird nur PNG unterstützt. |
| `key`                       | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](supported-languages.md).  |
| `maptype`                   | `layer` und `style`: Lesen Sie die Dokumentation zu den [unterstützten Kartenstilen](supported-map-styles.md). |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *Nicht verfügbar:* Dies ist ein Feature der Geocodierung. Verwenden Sie den Parameter `countrySet`, wenn Sie die Geocodierung-API von Azure Maps verwenden.  |
| `scale`                     | *N/V*                              |
| `size`                      | `width` und `height`: Es sind Größen bis 8.192 × 8.192 möglich. |
| `style`                     | *N/V*                              |
| `visible`                   | *N/V*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Im Kachelsystem von Azure Maps sind die Kacheln doppelt so groß wie die in Google Maps verwendeten Kartenkacheln. Aus diesem Grund ist der Wert der Zoomebene in Azure Maps im Vergleich zu Google Maps um eine Zoomebene niedriger. Verringern Sie zur Kompensierung die Zoomstufe in den Anforderungen, die Sie migrieren.

Weitere Informationen finden Sie in der [Schrittanleitung zur Rendering-API für Kartenbilder](how-to-render-custom-data.md).

Neben der Möglichkeit, ein statisches Kartenbild zu generieren, bietet der Azure Maps-Renderingdienst die Möglichkeit, direkt auf Kartenkacheln im Rasterformat (PNG) und im Vektorformat zuzugreifen:

- [**Kartenkachel:**](/rest/api/maps/render/getmaptile) Ruft Raster- (PNG) und Vektorkacheln für die Basiskarten (Straßen, Grenzen, Hintergrund) ab.
- [**Kachel für Kartenbilder:**](/rest/api/maps/render/getmapimagerytile) Ruft Kacheln für Luft- und Satellitenbilder ab.

> [!TIP]
> Viele Google Maps-Anwendungen wurden vor einigen Jahren von interaktiven Kartenbildern auf statische Kartenbilder umgestellt. Diese Maßnahme wurde ergriffen, um Kosten zu sparen. In Azure Maps ist es in der Regel kostengünstiger, das interaktive Kartensteuerelement im Web SDK zu verwenden. Die Abrechnung für das interaktive Kartensteuerelement basiert auf der Anzahl von Kachelladevorgängen. In Azure Maps werden große Kartenkacheln verwendet. Häufig sind nur wenige Kacheln erforderlich, um die gleiche Kartenansicht zu erhalten wie bei einer statischen Karte. Kartenkacheln werden vom Browser automatisch zwischengespeichert. Daher generiert das interaktive Kartensteuerelement bei der Nachbildung einer statischen Kartenansicht häufig nur einen Bruchteil einer Transaktion. Durch Schwenken und Zoomen werden weitere Kacheln geladen, es gibt jedoch Optionen im Kartensteuerelement, mit denen dieses Verhalten deaktiviert werden kann. Das interaktive Kartensteuerelement bietet außerdem deutlich mehr Visualisierungsoptionen als die statischen Kartendienste.

### <a name="marker-url-parameter-format-comparison"></a>Vergleich der Formate von Marker-URL-Parametern

**Vorher: Google Maps**

Fügen Sie Marker mithilfe des Parameters `markers` in der URL hinzu. Der Parameter `markers` übernimmt einen Stil und eine Liste von Positionen, die auf der Karte mit diesem Stil gerendert werden sollen, wie im Folgenden dargestellt:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Wenn Sie weitere Stile hinzufügen möchten, fügen Sie der URL Parameter vom Typ `markers` mit einem anderen Stil und einem anderen Satz von Positionen hinzu.

Geben Sie Markerpositionen im Format „Breitengrad,Längengrad“ an.

Fügen Sie Markerstile im Format `optionName:value` hinzu, und trennen Sie mehrere Stile jeweils durch einen senkrechten Strich (\|) voneinander. Beispiel: „Optionsname1:Wert1\|Optionsname2:Wert2“. Beachten Sie, dass die Optionsnamen und -werte durch einen Doppelpunkt (:) getrennt sind. Verwenden sie die folgenden Stiloptionsnamen für die Gestaltung von Markern in Google Maps:

- `color`: die Farbe des Standardmarkersymbols. Dabei kann es sich um eine hexadezimale 24-Bit-Farbe (`0xrrggbb`) oder einen der folgenden Werte handeln: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`: ein einzelnes alphanumerisches Zeichen in Großschreibung, das oberhalb des Symbols angezeigt wird.
- `size`: die Größe des Markers. Kann `tiny`, `mid` oder `small` sein.

Verwenden Sie die folgenden Stiloptionsnamen für benutzerdefinierte Symbole in Google Maps:

- `anchor`: gibt an, wie das Symbolbild an der Koordinate ausgerichtet wird. Es kann sich um einen Pixelwert (x,y) oder einen der folgenden Werte handeln: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft` oder `bottomright`.
- `icon`: eine URL, die auf das Symbolbild verweist.

In diesem Beispiel fügen wir der Karte einen roten, mittelgroßen Marker mit dem Längengrad -110 und dem Breitengrad 45 hinzu:

```
&markers=color:red|size:mid|45,-110
```


![Google Maps-Marker](media/migrate-google-maps-web-services/google-maps-marker.png)

**Nachher: Azure Maps**

Geben Sie in der URL den Parameter `pins` an, um einem statischen Kartenbild Marker hinzuzufügen. Geben Sie wie in Google Maps einen Stil und eine Liste mit Positionen im Parameter an. Der Parameter `pins` kann mehrmals angegeben werden, um Marker mit unterschiedlichen Stilen zu unterstützen.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Wenn Sie weitere Stile verwenden möchten, fügen Sie der URL zusätzliche Parameter vom Typ `pins` mit einem anderen Stil und einem anderen Satz von Positionen hinzu.

In Azure Maps muss die Stecknadelposition im Format „Längengrad Breitengrad“ angegeben werden. Von Google Maps wird das Format „Breitengrad,Längengrad“ verwendet. Im Azure Maps-Format sind Längen- und Breitengrad nicht durch ein Komma, sondern durch ein Leerzeichen getrennt.

`iconType` gibt die Art der zu erstellenden Stecknadel an. Die folgenden Werte sind möglich:

- `default`: das Standardsymbol für Pins.
- `none`: Es wird kein Symbol angezeigt, sondern nur Bezeichnungen gerendert.
- `custom`: gibt an, dass ein benutzerdefiniertes Symbol verwendet werden soll. Eine URL, die auf das Symbolbild verweist, kann am Ende des Parameters `pins` nach den Informationen zur Pinposition hinzugefügt werden.
- `{udid}`: eine Unique Data ID (UDID) für ein Symbol, das in der Azure Maps-Datenspeicher-Plattform gespeichert ist.

Fügen Sie Stecknadelstile im Format `optionNameValue` hinzu. Trennen Sie mehrere Stile jeweils durch einen senkrechten Strich (\|). Beispiel: `iconType|optionName1Value1|optionName2Value2`. Optionsname und -wert werden nicht voneinander getrennt. Verwenden Sie die folgenden Stiloptionsnamen für die Gestaltung von Markern:

- `al`: gibt die Deckkraft (Alpha) des Markers an. Wählen Sie eine Zahl zwischen 0 und 1 aus.
- `an`: gibt den Pinanker an. Geben Sie x- und y-Pixelwerte im Format „x y“ an.
- `co`: die Farbe des Pins. Geben Sie eine hexadezimale 24-Bit-Farbe an: `000000` bis `FFFFFF`.
- `la`: gibt den Bezeichnungsanker an. Geben Sie x- und y-Pixelwerte im Format „x y“ an.
- `lc`: die Farbe der Bezeichnung. Geben Sie eine hexadezimale 24-Bit-Farbe an: `000000` bis `FFFFFF`.
- `ls`: die Größe der Bezeichnung in Pixel. Wählen Sie eine Zahl größer Null aus.
- `ro`: ein Wert in Grad, um den das Symbol gedreht werden soll. Wählen Sie eine Zahl zwischen -360 und 360 aus.
- `sc`: ein Skalierungswert für das Pinsymbol. Wählen Sie eine Zahl größer Null aus.

Geben Sie Bezeichnungswerte für jede Stecknadelposition an. Diese Vorgehensweise ist effizienter als einen einzelnen Bezeichnungswert auf alle Marker in der Positionsliste anzuwenden. Der Bezeichnungswert kann eine Zeichenfolge mit mehreren Zeichen sein. Schließen Sie die Zeichenfolge in einfache Anführungszeichen ein, um sicherzustellen, dass sie nicht mit einem Stil- oder Positionswert verwechselt wird.

In diesem Beispiel fügen wir ein rotes (`FF0000`) Standardsymbol mit der Bezeichnung „Space Needle“ darunter (15 50) hinzu. Das Symbol hat den Längengrad -122,349300 und den Breitengrad 47,620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

![Azure Maps-Marker](media/migrate-google-maps-web-services/azure-maps-marker.png)

Fügen Sie drei Stecknadeln mit den Bezeichnungswerten „1“, „2“ und „3“ hinzu:

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

![Mehrere Azure Maps-Marker](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Vergleich der Formate von Pfad-URL-Parametern

**Vorher: Google Maps**

Fügen Sie einem statischen Kartenbild mithilfe des Parameters `path` in der URL Linien und ein Polygon hinzu. Der Parameter `path` akzeptiert einen Stil und eine Liste von Positionen, die auf der Karte gerendert werden sollen, wie im Anschluss zu sehen:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Verwenden Sie weitere Stile, indem Sie der URL zusätzliche Parameter vom Typ `path` mit einem anderen Stil und einem anderen Satz von Positionen hinzufügen.

Pfadpositionen werden im Format `latitude1,longitude1|latitude2,longitude2|…`angegeben. Pfade können codiert werden oder Adressen für Punkte enthalten.

Fügen Sie Pfadstile im Format `optionName:value` hinzu, und trennen Sie sie jeweils durch einen senkrechten Strich (\|). Optionsname und -wert müssen jeweils durch einen Doppelpunkt (:) getrennt werden: `optionName1:value1|optionName2:value2`. Die folgenden Stiloptionsnamen können für den Stil von Pfaden in Google Maps verwendet werden:

- `color`: die Farbe der Pfad- oder Polygonumrandung. Dabei kann es sich um eine hexadezimale 24-Bit-Farbe (`0xrrggbb`), eine hexadezimale 32-Bit-Farbe (`0xrrggbbbaa`) oder einen der folgenden Werte handeln: „black“, „brown“, „green“, „purple“, „yellow“, „blue“, „gray“, „orange“, „red“, „white“.
- `fillColor`: die Farbe zum Ausfüllen des Pfadbereichs (Polygon). Dabei kann es sich um eine hexadezimale 24-Bit-Farbe (`0xrrggbb`), eine hexadezimale 32-Bit-Farbe (`0xrrggbbbaa`) oder einen der folgenden Werte handeln: „black“, „brown“, „green“, „purple“, „yellow“, „blue“, „gray“, „orange“, „red“, „white“.
- `geodesic`: gibt an, ob der Pfad eine Linie sein soll, die der Erdkrümmung folgt.
- `weight`: die Stärke der Pfadlinie in Pixel.

Fügen Sie der Karte im URL-Parameter eine rote Linie mit Deckkraft und Pixelstärke zwischen den Koordinaten hinzu. Im folgenden Beispiel hat die Linie eine Deckkraft von 50 Prozent und eine Stärke von vier Pixeln. Als Koordinaten werden der Längengrad -110 und der Breitengrad 45 und Längengrad: –100, Breitengrad: 50 verwendet.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Google Maps-Polylinie](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Nachher: Azure Maps**

Geben Sie in der URL den Parameter `path` an, um einem statischen Kartenbild Linien und Polygone hinzuzufügen. Geben Sie in diesem Parameter wie in Google Maps einen Stil und eine Liste mit Positionen an. Geben Sie den Parameter `path` mehrmals an, um mehrere Kreise, Linien und Polygone mit unterschiedlichen Stilen zu rendern.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Bei Azure Maps müssen die Koordinaten für Pfadpositionen im Format „Längengrad Breitengrad“ angegeben werden. Von Google Maps wird das Format „Breitengrad,Längengrad“ verwendet. Im Azure Maps-Format sind Längen- und Breitengrad nicht durch ein Komma, sondern durch ein Leerzeichen getrennt. Azure Maps unterstützt keine codierten Pfade oder Adressen für Punkte. Laden Sie größere Datasets als GeoJSON-Dateien in die Datenspeicher-API von Azure Maps hoch, wie [hier](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage) dokumentiert.

Fügen Sie Pfadstile mit dem Format `optionNameValue` hinzu. Trennen Sie mehrere Stile jeweils durch einen senkrechten Strich (\|): `optionName1Value1|optionName2Value2`. Optionsname und -wert werden nicht voneinander getrennt. Verwenden Sie die folgenden Stiloptionsnamen für die Gestaltung von Pfaden in Azure Maps:

- `fa`: die Deckkraft der Füllfarbe (Alpha), die beim Rendern von Polygonen verwendet wird. Wählen Sie eine Zahl zwischen 0 und 1 aus.
- `fc`: die Füllfarbe, die zum Rendern des Bereichs eines Polygons verwendet wird.
- `la`: die Deckkraft der Linienfarbe (Alpha), die zum Rendern von Linien und der Umrandung von Polygonen verwendet wird. Wählen Sie eine Zahl zwischen 0 und 1 aus.
- `lc`: die Linienfarbe, die zum Rendern von Linien und der Umrandung von Polygonen verwendet wird.
- `lw`: die Breite der Linie in Pixel.
- `ra`: gibt einen Kreisradius in Metern an.

Fügen Sie im URL-Parameter eine rote Linie mit Deckkraft und Pixelstärke zwischen den Koordinaten hinzu. Im folgenden Beispiel hat die Linie eine Deckkraft von 50 Prozent und eine Stärke von vier Pixeln. Als Koordinaten werden der Längengrad -110 und der Breitengrad 45 und Längengrad: –100, Breitengrad: 50 verwendet.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Azure Maps-Polylinie](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Berechnen einer Entfernungsmatrix

In Azure Maps steht die Entfernungsmatrix-API zur Verfügung. Verwenden Sie diese API, um die Reisezeiten und Entfernungen zwischen einer Reihe von Positionen mit einer Entfernungsmatrix zu berechnen. Sie ist vergleichbar mit der Entfernungsmatrix-API in Google Maps.

- [**Routenmatrix:**](/rest/api/maps/route/postroutematrixpreview) Berechnet asynchron die Reisezeiten und Entfernungen für einen Satz von Ausgangspunkten und Zielen. Unterstützt bis zu 700 Zellen pro Anforderung. Hierbei handelt es sich um die Anzahl von Ausgangspunkten multipliziert mit der Anzahl von Zielen. Mit dieser Einschränkung sind beispielsweise folgende Matrixabmessungen möglich: 700 × 1, 50 × 10, 10 × 10, 28 × 25, 10 × 70.

> [!NOTE]
> Eine Anforderung an die Entfernungsmatrix-API kann nur mithilfe einer POST-Anforderung erfolgen, die Informationen zum Ausgangs- und Zielpunkt im Anforderungstext enthält. Außerdem erfordert Azure Maps, dass alle Ausgangspunkte und Ziele als Koordinaten angegeben werden. Adressen müssen zunächst geocodiert werden.

In der folgenden Tabelle werden die API-Parameter von Google Maps den vergleichbaren API-Parametern von Azure Maps gegenübergestellt:

| Google Maps-API-Parameter      | Vergleichbarer Azure Maps-API-Parameter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`: wird im POST-Anforderungstext als GeoJSON angegeben. |
| `key`                          | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](azure-maps-authentication.md). |
| `language`                     | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](supported-languages.md).  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`: wird im POST-Anforderungstext als GeoJSON angegeben.  |
| `region`                       | *Nicht verfügbar:* Dieses Feature hängt mit der Geocodierung zusammen. Verwenden Sie den Parameter `countrySet`, wenn Sie die Geocodierung-API von Azure Maps verwenden. |
| `traffic_model`                | *Nicht verfügbar:* Es kann nur angegeben werden, ob Verkehrsinfos mit dem Parameter `traffic` verwendet werden sollen. |
| `transit_mode`                 | *Nicht verfügbar:* Transitbasierte Entfernungsmatrizen werden zurzeit nicht unterstützt.  |
| `transit_routing_preference`   | *Nicht verfügbar:* Transitbasierte Entfernungsmatrizen werden zurzeit nicht unterstützt.  |
| `units`                        | *Nicht verfügbar:* Bei Azure Maps wird ausschließlich das metrische System verwendet. |

> [!TIP]
> In der Entfernungsmatrix-API von Azure Maps werden alle erweiterten Routenplanungsoptionen unterstützt, die in der Routenplanungs-API von Azure Maps zur Verfügung stehen. Zu diesen erweiterten Routenplanungsoptionen zählen unter anderem LKW-Streckenplanung und Motorangaben.

Lesen Sie die Dokumentation mit [bewährten Methoden für die Routenplanung](how-to-use-best-practices-for-routing.md).

## <a name="get-a-time-zone"></a>Abrufen einer Zeitzone

Azure Maps bietet eine API zum Abrufen der Zeitzone einer Koordinate. Die Azure Maps-Zeitzonen-API ist mit der Zeitzonen-API von Google Maps vergleichbar:

- [**Zeitzone nach Koordinate:**](/rest/api/maps/timezone/gettimezonebycoordinates) Geben Sie eine Koordinate an, und erhalten Sie die Zeitzonendetails der Koordinate.

In der folgenden Tabelle werden die API-Parameter von Google Maps den vergleichbaren API-Parametern in Azure Maps gegenübergestellt:

| Google Maps-API-Parameter | Vergleichbarer Azure Maps-API-Parameter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`: Weitere Informationen finden Sie auch in der Dokumentation zur [Authentifizierung mit Azure Maps](azure-maps-authentication.md).       |
| `language`                  | `language`: Lesen Sie auch die Dokumentation zu den [unterstützten Sprachen](supported-languages.md).    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Zusätzlich zu diesem API bietet Azure Maps eine Reihe von Zeitzonen-APIs. Diese APIs konvertieren die Zeit basierend auf den Namen oder IDs der Zeitzone:

- [**Zeitzone nach ID:**](/rest/api/maps/timezone/gettimezonebyid) Gibt aktuelle, historische und zukünftige Zeitzoneninformationen für die angegebene IANA-Zeitzonen-ID zurück.
- [**Zeitzonenenumeration für IANA:**](/rest/api/maps/timezone/gettimezoneenumiana) Gibt eine vollständige Liste der IANA-Zeitzonen-IDs zurück. Updates für den IANA-Dienst werden innerhalb eines Tages im System übernommen.
- [**Zeitzonenenumeration für Windows:**](/rest/api/maps/timezone/gettimezoneenumwindows) Gibt eine vollständige Liste der Windows-Zeitzonen-IDs zurück.
- [**IANA-Version der Zeitzone:**](/rest/api/maps/timezone/gettimezoneianaversion) Gibt die aktuelle IANA-Versionsnummer zurück, die von Azure Maps verwendet wird.
- [**Zeitzone: Windows nach IANA:**](/rest/api/maps/timezone/gettimezonewindowstoiana) Gibt die entsprechende IANA-ID für eine angegebene gültige Windows-Zeitzonen-ID zurück. Es können mehrere IANA-IDs eine einzelne Windows-ID zurückgegeben werden.

## <a name="client-libraries"></a>Clientbibliotheken

Azure Maps stellt Clientbibliotheken für die folgenden Programmiersprachen bereit:

- JavaScript, TypeScript, Node.js: [Dokumentation](how-to-use-services-module.md) \| [NPM-Paket](https://www.npmjs.com/package/azure-maps-rest)

Die folgenden Open-Source-Clientbibliotheken stehen für andere Programmiersprachen zur Verfügung:

- .NET Standard 2.0: [GitHub-Projekt](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-Paket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Azure Maps-REST-Diensten:

> [!div class="nextstepaction"]
> [Bewährte Suchmethoden](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Suchen nach einer Adresse](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Bewährte Methoden für den Azure Maps-Routendienst](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Azure Maps-REST-Dienste-API – Referenzdokumentation](/rest/api/maps/)

> [!div class="nextstepaction"]
> [Codebeispiele](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Verwenden des Dienstmoduls (Web SDK)](how-to-use-best-practices-for-routing.md)