---
title: Azure Maps-Glossar | Microsoft-Dokumentation
description: Ein Glossar häufig verwendeter Begriffe im Zusammenhang mit Azure Maps, standortbasierten Diensten und GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 05071be4fc1705787a649d331613e8d9399f983f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897176"
---
# <a name="glossary"></a>Glossar

Die folgende Liste enthält Beschreibungen gängiger Begriffe, die im Zusammenhang mit Azure Maps-Diensten verwendet werden.

## <a name="a"></a>Ein

<a name="address-validation"></a> **Adressüberprüfung** : Der Vorgang, bei dem das Vorhandensein einer Adresse überprüft wird.

<a name="advanced-routing"></a> **Erweitertes Routing** : Eine Sammlung von Diensten, die erweiterte Vorgänge mit Straßenroutendaten durchführen, etwa die Berechnung erreichbarer Bereiche (Isochronen), Entfernungsmatrizen und Batchroutenanforderungen.

<a name="aerial-imagery"></a> **Luftaufnahmen** : Siehe [Satellitenbilder](#satellite-imagery). 

<a name="along-a-route-search"></a> **Entlang einer Route suchen** : Eine Abfrage nach räumlichen Daten, die nach Daten innerhalb einer angegebenen Zeit oder Entfernung für die Umleitung von einem Streckenweg sucht.

<a name="altitude"></a> **Höhe** : Die Höhe oder die vertikale Erhebung eines Punkts über einer Referenzfläche. Höhenmessungen basieren auf einem bestimmten Referenzdatum wie z.B. das Normalnull. Siehe auch „Erstreckung“.

<a name="ambiguous"></a> **Mehrdeutig** : Ein gewisser Grad an Unbestimmtheit hinsichtlich der Datenklassifizierung, der vorliegt, wenn einem Objekt entsprechend mindestens zwei Werte für ein bestimmtes Attribut zugewiesen sind. Dies kommt beispielsweise bei der Geocodierung von „CA“ vor, bei der zwei mehrdeutige Ergebnisse zurückgegeben werden: Kanada (Canada) und Kalifornien (California). „CA“ bezeichnet nämlich einerseits das Land/die Region Kanada und ist gleichzeitig der Bundesstaatcode für Kalifornien. 

<a name="annotation"></a> **Anmerkung** : Textelemente oder Grafiken, die auf der Karte angezeigt werden, um Informationen für den Benutzer bereitzustellen. Anmerkungen können eine bestimmte Kartenentität darstellen bzw. beschreiben, allgemeine Informationen zu einem Bereich auf der Karte liefern oder Informationen zur Karte selbst angeben.

<a name="antimeridian"></a> **Antimeridian** : Wird auch als 180. Meridian bezeichnet. <sup></sup> Die Linie des 180. Längengrads in östlicher und westlicher Richtung. Er liegt auf der gegenüberliegenden Seite des Nullmeridians auf dem Globus.

<a name="application-programming-interface-api"></a> **Anwendungsprogrammierschnittstelle (Application Programming Interface, API)** : Eine Spezifikation, die Entwicklern das Erstellen von Anwendungen ermöglicht.

<a name="api-key"></a> **API-Schlüssel** : Siehe [Authentifizierung mit gemeinsam verwendetem Schlüssel](#shared-key-authentication).

<a name="area-of-interest-aoi"></a> **Interessenbereich** : Der Umfang, in dem ein Fokusbereich zur Erstellung einer Karte oder Datenbank definiert wird.

<a name="asset-tracking"></a> **Assetnachverfolgung** : Die Nachverfolgung des Standorts eines Assets (etwa einer Person, eines Fahrzeugs oder eines anderen Objekts).

<a name="asynchronous-request"></a> **Asynchrone Anforderung** : Eine HTTP-Anforderung, die eine Verbindung öffnet, eine Anforderung an den Server sendet, der einen Bezeichner für die asynchrone Anforderung zurückgibt, und anschließend die Verbindung schließt. Der Server fährt mit der Verarbeitung der Anforderung fort, während der Benutzer den Status mithilfe des Bezeichners überprüfen kann. Wenn die Verarbeitung der Anforderung abgeschlossen ist, kann der Benutzer die Antwort dann herunterladen. Diese Art von Anforderung wird bei Prozessen mit langer Ausführungsdauer verwendet.

<a name="autocomplete"></a> **AutoVervollständigen** : Ein Feature in einer Anwendung, das den Rest eines Worts vorhersagt, das ein Benutzer gerade eingibt. 

<a name="autosuggest"></a> **Vorschlagssuche** : Ein Feature in einer Anwendung, das die logischen Möglichkeiten für die Benutzereingabe vorhersagt.

<a name="azure-location-based-services-lbs"></a> **Azure Location Based Services (LBS)** : Der frühere Name von Azure Maps in der Vorschau.

<a name="azure-active-directory"></a> **Azure Active Directory (Azure AD)** : Azure AD ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Die Azure AD-Integration in Azure Maps ist derzeit in der Vorschau für alle Azure Maps-APIs verfügbar. Azure AD unterstützt die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um einen differenzierten Zugriff auf Azure Maps-Ressourcen zu ermöglichen. Weitere Informationen zur Azure AD-Integration in Azure Maps finden Sie unter [Azure Maps und Azure AD](azure-maps-authentication.md) sowie [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a> **Azure Maps-Schlüssel** : Siehe [Authentifizierung mit gemeinsam verwendetem Schlüssel](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a> **Basiskarte** : Der Teil einer Kartenanwendung, der Hintergrundreferenzinformationen wie Straßen, Orientierungspunkte und politische Grenzen angezeigt.

<a name="batch-request"></a> **Batchanforderung** : Der Vorgang, bei dem mehrere Anforderungen in einer einzelnen Anforderung kombiniert werden.

<a name="bearing"></a> **Peilung** : Die horizontale Richtung eines Punkts in Bezug auf einen anderen Punkt. Dies wird als Winkel relativ zu Norden ausgedrückt (von 0 Grad bis 360 Grad im Uhrzeigersinn). 

<a name="boundary"></a> **Grenze** : Eine Linie oder ein Polygon, die bzw. das angrenzende politische Einheiten (z.B. Länder/Regionen, Ortsteile und Ländereien) trennt. Eine Grenze kann, muss jedoch nicht entlang von physischen Gegebenheiten wie Flüssen, Bergen oder Mauern verlaufen.

<a name="bounds"></a> **Begrenzungen** : Siehe [Begrenzungsrahmen](#bounding-box).

<a name="bounding-box"></a> **Begrenzungsrahmen** : Eine Reihe von Koordinaten, die einen rechteckigen Bereich auf der Karte darstellen. 

## <a name="c"></a>C

<a name="cadastre"></a> **Grundbuch** : Ein Register, in dem Flur- bzw. Grundstücke verzeichnet werden. Siehe auch [Parzelle](#parcel).

<a name="camera"></a> **Kamera** : Im Kontext eines interaktiven Kartensteuerelements eine Kamera, die das Sichtfeld der Karte definiert. Der Viewport der Kamera richtet sich nach verschiedenen Kartenparametern: Mittelpunkt, Zoomfaktor, Neigung, Peilung. 

<a name="centroid"></a> **Schwerpunkt** : Der geometrische Mittelpunkt einer Figur. Der Schwerpunkt einer Linie wäre der Mittelpunkt, wohingegen der Schwerpunkt bei einem Polygon eine Fläche in der Mitte wäre.

<a name="choropleth-map"></a> **Choroplethenkarte** : Eine thematische Karte, auf der Gebiete im Verhältnis zur Verteilungsdichte einer statistischen Variablen eingefärbt, schattiert, gepunktet oder schraffiert dargestellt sind. Diese statistische Variable wird auf der Karte angezeigt. Ein Beispiel hierfür ist die Einfärbung der Grenze jedes US-Bundesstaats auf Grundlage der relativen Bevölkerung im Vergleich zu allen anderen Bundesstaaten.

<a name="concave-hull"></a> **Konkave Hülle** : Eine Form, die eine mögliche konkave Geometrie darstellt, die alle Formen im angegebenen Dataset einschließt. Man kann sich dies in etwa so vorstellen, als ob die Daten in Plastikfolie eingehüllt und anschließend erhitzt werden, sodass große Bereiche zwischen Punkten zu anderen Datenpunkten gezogen werden.

<a name="consumption-model"></a> **Verbrauchsmodell** : Informationen, die die Rate definieren, mit der ein Fahrzeug Treibstoff oder Elektrizität verbraucht. Weitere Informationen finden Sie unter [Verbrauchsmodell](consumption-model.md).

<a name="control"></a> **Steuerung** : Eine eigenständige oder wiederverwendbare Komponente mit einer grafischen Benutzeroberfläche, die eine Reihe von Verhaltensweisen für die Schnittstelle definiert. Ein Kartensteuerelement ist beispielsweise in der Regel der Teil der Benutzeroberfläche, die eine interaktive Karte lädt.

<a name="convex-hull"></a> **Konvexe Hülle** : Eine Form, die die minimale konvexe Geometrie darstellt, die alle Formen im angegebenen Dataset einschließt. Man kann sich dies in etwa so vorstellen, als ob ein Gummiband um das Dataset gelegt wird.

<a name="coordinate"></a> **Koordinate** : Besteht aus Längen- und Breitengradwerten, die einen Standort auf einer Karte darstellen.

<a name="coordinate-system"></a> **Koordinatensystem** : Ein Referenzframework, das verwendet wird, um die Positionen von Punkten im Raum in zwei oder drei Dimensionen zu definieren.

<a name="country-code"></a> **Ländercode** : Ein eindeutiger Bezeichner für ein Land/eine Region basierend auf dem ISO-Standard. ISO2 ist ein Code mit zwei Buchstaben für ein Land/eine Region (z. B. „US“), das bei ISO3 als Code mit drei Zeichen (z. B. „USA“) dargestellt wird.

<a name="country-subdivision"></a> **Primäre Untereinheit eines Landes** : Eine Untereinheit eines Landes/einer Region auf oberster Ebene, z.B. ein Bundesstaat, ein Bundesland, ein Kanton oder eine Provinz.

<a name="country-secondary-subdivision"></a> **Sekundäre Untereinheit eines Landes** : Eine Untereinheit eines Landes/einer Region auf zweiter Ebene, z.B. ein Landkreis.

<a name="country-tertiary-subdivision"></a> **Tertiäre Untereinheit eines Landes** : Eine Untereinheit eines Landes/einer Region auf dritter Ebene, z.B. ein Bezirk.

<a name="cross-street"></a> **Querstraße** : Ein Punkt, an dem sich mindestens zwei Straßen kreuzen.

<a name="cylindrical-projection"></a> **Zylinderprojektion** : Eine Projektion, die Punkte von einem Sphäroid oder einer Kugel in einen Tangens oder Sekanszylinder transformiert. Der Zylinder wird dann von oben nach unten in Schnitte aufgeteilt und zu einer Ebene verflacht.

## <a name="d"></a>D

<a name="datum"></a> **Datum** : Die Referenzspezifikationen eines Messsystems, eines Systems aus Koordinatenpositionen auf einer Fläche (einem horizontalen Datum) oder von Abständen über oder unter einer Fläche (einem vertikalen Datum).

<a name="dbf-file"></a> **DBF-Datei** : Ein Datenbankdateiformat, das in Kombination mit Shape-Dateien (SHP) verwendet wird.

<a name="degree-minutes-seconds-dms"></a> **Degree Minutes Seconds (DMS)** : Die Maßeinheit, die den Breiten- und Längengrad beschreibt. Ein Grad ist der 360. Teil eines Kreises. Ein Grad ist wiederum in 60 Minuten unterteilt, und eine Minute in 60 Sekunden.

<a name="delaunay-triangulation"></a> **Delaunay-Triangulierung** : Eine Methode zum Erstellen eines Netzes aus zusammenhängenden, nicht überlappenden Dreiecken aus einem Dataset von Punkten. Der Umkreis, der die einzelnen Dreiecke umgibt, enthält keine anderen Punkte aus dem Dataset im Inneren.

<a name="demographics"></a> **Demografie** : Die statistischen Eigenschaften (z.B. Alter, Geburtsrate und Einkommen) einer Bevölkerung.

<a name="destination"></a> **Ziel** : Ein Endpunkt oder Ort, den ein Benutzer bereist.

<a name="digital-elevation-model-dem"></a> **Digitales Höhenmodell (DHM)** : Ein Dataset mit Höhenwerten in Bezug zu einer Fläche, die innerhalb einer Fläche mit einem gängigen Datum in regelmäßigen Abständen erfasst werden. DHMs werden üblicherweise verwendet, um Geländereliefs darzustellen.

<a name="dijkstra's-algorithm"></a> **Dijkstra-Algorithmus** : Ein Algorithmus, der die Konnektivität eines Netzes untersucht, um den kürzesten Pfad zwischen zwei Punkten zu finden.

<a name="distance-matrix"></a> **Entfernungsmatrix** : Eine Matrix, die Informationen zu Fahrtzeit und -strecke zwischen einer Gruppe von Herkunfts- und Zielorten enthält. 

## <a name="e"></a>E

<a name="elevation"></a> **Erstreckung** : Der vertikale Abstand eines Punkts oder eines Objekts über oder unter einer Referenzfläche oder über oder unter einem Nullpunkt. Als Referenzfläche wird üblicherweise Normalnull verwendet. Die „Erstreckung“ bezieht sich in der Regel auf die vertikale Höhe eines Lands.

<a name="envelope"></a> **Umschlag** : Siehe [Begrenzungsrahmen](#bounding-box).

<a name="extended-postal-code"></a> **Erweiterte Postleitzahl** : Eine Postleitzahl, die eventuell zusätzliche Informationen enthält. In den USA sind Postleitzahlen beispielsweise fünfstellig. Eine erweiterte Postleitzahl (ZIP+4) enthält jedoch vier zusätzliche Stellen. Diese zusätzlichen Stellen dienen zur Identifizierung eines geografischen Segments innerhalb des fünfstelligen Zustellungsbereichs (etwa ein Viertel, eine Wohnanlage oder ein Postfach). Die Angabe des geografischen Segments trägt zur effizienten Sortierung und Zustellung der Post bei.

<a name="extent"></a> **Erweiterung** : Siehe [Begrenzungsrahmen](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Verbundauthentifizierung** : Eine Authentifizierungsmethode, bei der durch eine einmalig durchgeführte Anmeldung-/Authentifizierungsmethode mehrere Web- und mobile Apps verwendet werden können. 

<a name="feature"></a> **Feature** : Ein Objekt, das eine Geometrie mit zusätzlichen Metadateninformationen kombiniert. 

<a name="feature-collection"></a> **Merkmalsauswahl** : Eine Sammlung von Merkmalsobjekten.

<a name="find-along-route"></a> **Suche entlang einer Route** : Eine Abfrage nach räumlichen Daten, die nach Daten innerhalb einer angegebenen Zeit oder Entfernung für die Umleitung von einem Streckenweg sucht.

<a name="find-nearby"></a> **In der Nähe finden** : Eine Abfrage nach räumlichen Daten, die nach einer festgelegten kürzesten Entfernung (z.B. Luftlinie) von einem Punkt sucht.

<a name="fleet-management"></a> **Flottenmanagement** : Die Verwaltung von gewerblich genutzten Fahrzeugen, z.B. Autos, Lkw, Schiffen und Flugzeugen. Das Flottenmanagement kann eine Reihe von Aufgaben umfassen, z.B. die Fahrzeugfinanzierung, Wartung, Telematik (Überwachung und Diagnose) sowie die Verwaltung von Fahrzeugführern, Geschwindigkeit, Kraftstoffstand und Gesundheitsschutz und Arbeitssicherheit. Das Flottenmanagement wird von Unternehmen im Transportwesen verwendet. Die Unternehmen möchten Risiken minimieren sowie die Gesamtkosten für Transport und Personal senken und gleichzeitig die Einhaltung von gesetzlichen Bestimmungen sicherstellen.

<a name="free-flow-speed"></a> **Free Flow Speed** : Die erwartete Geschwindigkeit bei sehr geringer Verkehrsstärke unter optimalen Bedingungen. In der Regel ist dies das Tempolimit.

<a name="free-form-address"></a> **Freiformadresse** : Eine vollständige Adresse, die als einzelne Textzeile dargestellt wird.

<a name="fuzzy-search"></a> **Fuzzysuche** : Eine Suche, bei der eine Textzeichenfolge in Freiform wie eine Adresse oder ein POI verarbeitet wird. 

## <a name="g"></a>G

<a name="geocode"></a> **Geocode** : Eine Adresse oder ein Ort, der in eine Koordinate konvertiert wurde, um diesen Ort auf einer Karte anzeigen zu können. 

<a name="geocoding"></a> **Geocodierung** : Der Prozess der Konvertierung von Adressdaten anhand von Koordinaten, auch als „Forward Geocoding“ bezeichnet wird. 

<a name="geodesic-path"></a> **Geodätischer Pfad** : Der kürzeste Pfad zwischen zwei Punkten auf einer gekrümmten Oberfläche. Beim Rendering in Azure Maps wird dieser Pfad basierend auf der Mercator-Projektion als gekrümmte Linie angezeigt.

<a name="geofence"></a> **Geofence** : Eine definierte geografische Region, bei der Ereignisse ausgelöst werden können, wenn ein Gerät die Region erreicht oder sich in dieser befindet.

<a name="geojson"></a> **GeoJSON** : Ein gängiges JSON-basiertes Dateiformat zum Speichern von geografischen Vektordaten wie Punkten, Linien und Polygonen. **Hinweis** : Azure Maps verwendet eine erweiterte Version von GeoJSON, wie in [diesem Artikel](extend-geojson.md) erläutert wird.

<a name="geometry"></a> **Geometrie** : Ein räumliches Objekt wie z.B. ein Punkt, eine Linie oder ein Polygon.

<a name="geometrycollection"></a> **GeometryCollection** : Eine Sammlung von Geometrieobjekten.

<a name="geopol"></a> **GeoPol** : Bezieht sich auf geopolitisch vertrauliche Daten, z.B. umstrittene Grenzen und Ortsnamen.

<a name="georeference"></a> **Georeferenz** : Der Vorgang, bei dem geografische Daten oder Bilder auf ein bekanntes Koordinatensystem ausgerichtet werden. Dieser Vorgang kann das Verschieben, Drehen, Skalieren oder die Erkennung von Datenschiefe bezeichnen.

<a name="georss"></a> **GeoRSS** : Eine XML-Erweiterung zum Hinzufügen von räumlichen Daten zu RSS-Feeds.

<a name="gis"></a> **GIS** : Ein Akronym für „Geographic Information System“, d.h. Geoinformationssysteme. Dies ist eine häufig verwendete Benennung im Kartierungsbereich.

<a name="gml"></a> **GML** : Das Akronym für „Geography Markup Language“. Eine XML-Erweiterung zum Speichern von räumlichen Daten.

<a name="gps"></a> **GPS** : Das globale Positionsbestimmungssystem „Global Positioning System“, ein System von Satelliten für die Bestimmung einer Geräteposition auf der Erde. Die Satelliten in der Erdumlaufbahn übertragen Signale, mit denen ein GPS-Empfänger an einem beliebigen Ort auf der Erde seinen eigenen Standort durch Trilateration berechnen kann.

<a name="gpx"></a> **GPX** : Bezeichnet das Format „GPS eXchange“, ein XML-Dateiformat, das häufig bei GPS-Geräten zur Anwendung kommt.  

<a name="great-circle-distance"></a> **Großkreisentfernung** : Die kürzeste Entfernung zwischen zwei Punkten auf der Oberfläche einer Kugel.

<a name="greenwich-mean-time-gmt"></a> **Greenwich Mean Time (GMT)** : Die Uhrzeit am Nullmeridian, der durch das Royal Greenwich Observatory in Greenwich, England, Großbritannien, verläuft.

<a name="guid"></a> **GUID** : Ein global eindeutiger Bezeichner (Globally Unique Identifier). Eine Zeichenfolge, die eine Schnittstelle, Klasse, Typbibliothek, Komponentenkategorie oder einen Datensatz eindeutig identifiziert.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Haversine-Formel** : Eine allgemeine Gleichung, die zur Berechnung der Großkreisentfernung zwischen zwei Punkten auf einer Kugel verwendet wird.

<a name="hd-maps"></a> **HD-Karten** : Die sogenannten hochauflösenden High Definition-Karten enthalten Straßennetzinformationen wie z.B. Fahrbahnmarkierungen, Beschilderung und Lichtsignale zur Darstellung der Fahrtrichtung, die für autonomes Fahren erforderlich sind.

<a name="heading"></a> **Heading** : Die Richtung, in die ein Objekt zeigt oder gewandt ist. Siehe auch [Peilung](#heading).

<a name="heatmap"></a> **Wärmebild** : Eine Datenvisualisierung, in der ein Bereich von Farben die Dichte der Punkte in einem bestimmten Bereich darstellt. Siehe auch „Thematische Karte“.

<a name="hybrid-imagery"></a> **Hybridluftaufnahmen** : Satelliten- oder Luftaufnahmen mit überlagerten Straßendaten und -bezeichnungen.

## <a name="i"></a>I

<a name="iana"></a> **IANA** : Ein Akronym für „Internet Assigned Numbers Authority“. Ein gemeinnütziges Gremium, das für die globale Zuteilung von IP-Adressen zuständig ist.

<a name="isochrone"></a> **Isochrone** : Eine Isochrone definiert den Bereich, den ein Mensch innerhalb einer angegebenen Zeit mit einer Beförderungsart in beliebiger Richtung von einem bestimmten Ort aus bereisen kann. Siehe auch [Erreichbarer Bereich](#reachable-range).

<a name="isodistance"></a> **Isodistanz** : Eine Isochrone definiert in Bezug auf einen Ort den Bereich, den ein Mensch innerhalb einer angegebenen Distanz mit einer Beförderungsart in beliebiger Richtung bereisen kann. Siehe auch [Erreichbarer Bereich](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML** : Steht für „Keyhole Markup Language“. Ein allgemeines XML-Dateiformat zum Speichern von geografischen Vektordaten wie Punkten, Linien und Polygonen. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat** : Multispektrale, von der NASA entwickelte Satelliten, die die Erde umkreisen und Landbilder erfassen. Diese Bilder werden in zahlreichen Branchen wie Landwirtschaft, Forstwirtschaft und Kartografie genutzt.

<a name="latitude"></a> **Breitengrad** : Das in Grad angegebene Winkelmaß, das die Entfernung vom Äquator in nördlicher oder südlicher Richtung angibt.

<a name="level-of-detail"></a> **Detailebene** : Siehe „Zoomfaktor“.

<a name="lidar"></a> **LIDAR** : Akronym für „Light Detection and Ranging“. Eine Technik zur Fernerkundung, bei der die Entfernung zu reflektierenden Oberflächen mittels Laser gemessen wird.

<a name="linear-interpolation"></a> **Lineare Interpolation** : Die Schätzung eines unbekannten Werts, bei der die Luftlinie zwischen bekannten Werten herangezogen wird.

<a name="linestring"></a> **LineString** : Eine Geometrie zur Darstellung einer Linie. Diese wird auch als „Polylinie“ bezeichnet. 

<a name="localization"></a> **Lokalisierung** : Unterstützung für verschiedene Sprachen und Kulturen.

<a name="logistics"></a> **Logistik** : Die koordinierte Beförderung von Personen, Fahrzeugen, Gütern oder Ressourcen.

<a name="longitude"></a> **Längengrad** : Das in Grad angegebene Winkelmaß, das die Entfernung vom Äquator in östlicher oder westlicher Richtung angibt.

## <a name="m"></a>M

<a name="map-tile"></a> **Kartenkachel** : Ein rechteckiges Bild, das einen Teil einer Kartencanvas darstellt. Weitere Informationen finden Sie unter [Zoomfaktoren und Grobraster](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Marker** : Wird auch als „Pin“ oder „Ortsmarke“ bezeichnet und stellt ein Symbol für einen Ort auf einer Karte dar.

<a name="mercator-projection"></a> **Mercator-Projektion** : Eine Kartenzylinderprojektion, die zur standardmäßigen Kartenprojektion für nautische Einsatzzwecke geworden ist. Dies ist auf die Möglichkeit zurückzuführen, Linien gleichbleibenden Kurses (auch als „Loxodronen“ bezeichnet) als gerade Segmente darzustellen, bei denen die Winkel mit den Meridianen beibehalten werden. Bei allen herkömmlichen Kartenprojektionen werden die Formen oder die Größen der Karte im Vergleich zur tatsächlichen Anordnung der Erdoberfläche verzerrt. Die Mercator-Projektion hebt weit vom Äquator entfernte Bereiche hervor, sodass kleinere Bereiche auf der Karte mit sinkendem Abstand zu den Polen größer wirken. 

<a name="multilinestring"></a> **MultiLineString** : Eine Geometrie, die eine Sammlung von LineString-Objekten darstellt. 

<a name="multipoint"></a> **MultiPoint** : Eine Geometrie, die eine Sammlung von Point-Objekten darstellt.

<a name="multipolygon"></a> **MultiPolygon** : Eine Geometrie, die eine Sammlung von Polygon-Objekten darstellt. Zur Darstellung der Grenze von Hawaii wäre beispielsweise jede Insel von einem Polygon umgeben. Die Grenze von Hawaii wäre somit ein MultiPolygon.

<a name="municipality"></a> **Gemeinde** : Eine Stadt oder ein Dorf. 

<a name="municipality-subdivision"></a> **Primäre Untereinheit einer Gemeinde** : Eine Untereinheit einer Gemeinde wie etwa eine Bezeichnung für ein Stadtviertel oder einen Ortsteil (z.B. „Innenstadt“).

## <a name="n"></a>N

<a name="navigation-bar"></a> **Navigationsleiste** : Die Gruppe von Steuerelementen auf einer Karte, die zum Anpassen des Zoomfaktors, der Neigung, der Drehung sowie zum Wechseln der Basiskartenebene verwendet werden.

<a name="nearby-search"></a> **Umgebungssuche** : Eine Abfrage nach räumlichen Daten, die nach einer festgelegten kürzesten Entfernung (z.B. Luftlinie) von einem Punkt sucht.

<a name="neutral-ground-truth"></a> **Neutral Ground Truth** : Eine Karte, die Bezeichnungen in der offiziellen Sprache der Region, die diese repräsentiert, und in lokalen Skripts rendert, falls verfügbar.

## <a name="o"></a>O

<a name="origin"></a> **Ursprung** : Ein Startpunkt bzw. -ort, an dem sich ein Benutzer befindet.

## <a name="p"></a>P

<a name="panning"></a> **Schwenken** : Der Vorgang, bei dem die Karte in eine beliebige Richtung bei konstantem Zoomfaktor bewegt wird.

<a name="parcel"></a> **Parzelle** : Ein abgegrenztes Flur- oder Grundstück.

<a name="pitch"></a> **Neigung** : Der Grad der Neigung einer Karte zur Vertikalen, wobei „0“ die Ansicht frontal nach unten auf die Karte zeigt.

<a name="point"></a> **Punkt** : Eine Geometrie, die eine einzelne Position auf der Karte darstellt. 

<a name="points-of-interest-poi"></a> **Point of Interest (POI)** : Ein Unternehmen, ein Orientierungspunkt oder ein allgemeiner Interessenbereich.

<a name="polygon"></a> **Polygon** : Eine Volumengeometrie, die einen Bereich auf einer Karte darstellt. 

<a name="polyline"></a> **Polylinie** : Eine Geometrie zur Darstellung einer Linie. Diese wird auch als „LineString“ bezeichnet. 

<a name="position"></a> **Position** : Der Längengrad, der Breitengrad und die Höhe (x-y-z-Koordinaten) eines Punkts.

<a name="post-code"></a> **Postleitzahl** : Siehe [Postleitzahl](#postal-code).

<a name="postal-code"></a> **Postleitzahl** : Eine Reihe von Buchstaben oder Zahlen (oder eine Mischung aus beidem) in einem bestimmten Format. Die Postleitzahl wird im Postwesen eines Landes/einer Region verwendet, um geografische Bereiche in Zonen zu unterteilen und so die Postzustellung zu vereinfachen.

<a name="primary-key"></a> **Primärschlüssel** : Der erste von zwei Abonnementschlüsseln, die Azure Maps für die Authentifizierung mit gemeinsam verwendetem Schlüssel bereitgestellt werden. Siehe [Authentifizierung mit gemeinsam verwendetem Schlüssel](#shared-key-authentication).

<a name="prime-meridian"></a> **Nullmeridian** : Die Linie des Längengrads, der 0 Grad darstellt. Längengradwerte nehmen in der Regel in westlicher Richtung auf bis zu -180 Grad ab und in östlicher Richtung auf bis zu 180 Grad zu. 

<a name="prj"></a> **PRJ** : Eine Textdatei, die häufig zu einer Shape-Datei gehört und Informationen zum projizierten Koordinatensystem enthält, in dem sich das Dataset befindet.

<a name="projection"></a> **Projektion** : Ein projiziertes Koordinatensystem auf Grundlage einer Kartenprojektion, wie z.B. Universal Transverse Mercator, Albers, Flächentreue und Robinson. Diese ermöglichen die Projektion von Karten der kugelförmigen Erdoberfläche auf einer zweidimensionalen kartesischen Koordinatenebene. Projizierte Koordinatensysteme werden mitunter auch als „Kartenprojektionen“ bezeichnet.

## <a name="q"></a>Q

<a name="quadkey"></a> **Quadkey** : Ein Basis-4-Adressindex für eine Kachel in einem Quadtree-Kachelsystem. Weitere Informationen finden Sie unter [Zoomfaktoren und Grobraster](zoom-levels-and-tile-grid.md).

<a name="quadtree"></a> **Quadtree** : Eine Datenstruktur, bei der jeder Knoten genau vier untergeordnete Elemente aufweist. Das in Azure Maps verwendete Kachelsystem basiert auf einer QuadTree-Struktur. Diese sorgt dafür, dass die einzelnen Kartenkacheln jeweils in vier Unterkacheln unterteilt werden, wenn der Benutzer die Karte um eine Stufe vergrößert.  Weitere Informationen finden Sie unter [Zoomfaktoren und Grobraster](zoom-levels-and-tile-grid.md).

<a name="queries-per-second-qps"></a> **Abfragen pro Sekunde (Queries Per Second, QPS)** : Die Anzahl der Abfragen oder Anforderungen, die innerhalb einer Sekunde an einen Dienst oder eine Plattform gesendet werden können. 

## <a name="r"></a>R

<a name="radial-search"></a> **Radiale Suche** : Eine Abfrage nach räumlichen Daten, die nach einer festgelegten kürzesten Entfernung (z.B. Luftlinie) von einem Punkt sucht. 

<a name="raster-data"></a> **Rasterdaten** : Eine Matrix von Zellen (oder Pixeln), die in Zeilen und Spalten (oder einem Raster) unterteilt sind, wobei jede Zelle einen Wert enthält, der Informationen wie etwa die Temperatur darstellt. Zu Rastern zählen digitale Luftaufnahmen, Bilder von Satelliten, digitale Bilder und eingelesene Karten.

<a name="raster-layer"></a> **Rasterebene** : Eine Kachelebene, die aus Rasterbildern besteht.

<a name="reachable-range"></a> **Erreichbarer Bereich** : Ein erreichbarer Bereich definiert den Bereich, den ein Mensch innerhalb einer angegebenen Zeit oder Strecke mit einer Beförderungsart in beliebiger Richtung von einem Ort aus bereisen kann. Siehe auch [Isochrone](#isochrone) und [Isodistanz](#isodistance).

<a name="remote-sensing"></a> **Fernerkundung** : Der Vorgang, bei dem Sensordaten aus der Ferne gesammelt und ausgewertet werden.

<a name="rest-service"></a> **REST-Dienst** : Das Akronym „REST“ steht für „Representational State Transfer“. Ein REST-Dienst ist ein URL-basierter Webdienst, der auf einfacher Webtechnologie für die Kommunikation basiert. Die gängigsten Methoden sind HTTP-GET- und POST-Anforderungen. Diese Arten von Diensten sind tendenziell schneller und geringer als herkömmliche SOAP-basierte Dienste.

<a name="reverse-geocode"></a> **Reverse Geocoding** : Der Vorgang, bei dem anhand einer Koordinate die Adresse ermittelt wird, mit der diese auf einer Karte dargestellt wird.

<a name="reproject"></a> **Umprojizieren** : Siehe [Transformation](#transformation).

<a name="rest-service"></a> **REST-Dienst** : Das Akronym steht für „Representational State Transfer“. Eine Architektur für den Informationsaustausch zwischen Peers in einer dezentralisierten, verteilten Umgebung. Mit REST können Programme auf unterschiedlichen Computern betriebssystem- und plattformunabhängig kommunizieren. Ein Dienst kann eine HTTP-Anforderung (Hypertext Transfer Protocol) an eine URL (Uniform Resource Locator) senden und daraufhin Daten erhalten.

<a name="route"></a> **Route** : Ein Pfad zwischen mindestens zwei Orten, der zudem zusätzliche Informationen wie Anweisungen für Wegpunkte entlang der Route enthalten kann.

<a name="requests-per-second-rps"></a> **Anforderungen pro Sekunde (RPS)** : Siehe [Abfragen pro Sekunde (QPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS** : Abhängig von der Quelle das Akronym für „Really Simple Syndication“, „Resource Description Framework (RDF) Site Summary“ oder „Rich Site Summary“. Ein einfaches, strukturiertes XML-Format für die Freigabe von Inhalten für andere Websites. RSS-Dokumente enthalten Metadatenelemente, z.B. Datum, Autor, Titel, eine kurze Beschreibung und einen Hyperlink. Diese Informationen unterstützen einen Benutzer (oder einen RSS-Veröffentlichungsdienst) bei der Entscheidung, bei welchen Materialien eine weitergehende Untersuchung lohnt.

## <a name="s"></a>E

<a name="satellite-imagery"></a> **Satellitenbilder** : Bilder, die von Flugzeugen und direkt nach unten zeigenden Satelliten aufgezeichnet wurden.

<a name="secondary-key"></a> **Sekundärschlüssel** : Der zweite von zwei Abonnementschlüsseln, die Azure Maps für die Authentifizierung mit gemeinsam verwendetem Schlüssel bereitgestellt werden. Siehe [Authentifizierung mit gemeinsam verwendetem Schlüssel](#shared-key-authentication).

<a name="shapefile-shp"></a> **Shape-Datei (SHP)** : Wird auch als „ESRI-Shape-Datei“ bezeichnet und ist ein Vektordaten-Speicherformat zum Speichern der Position, Form und Attribute geografischer Merkmale. Eine Shape-Datei wird in einer Gruppe verwandter Dateien gespeichert.

<a name="shared-key-authentication"></a> **Authentifizierung mit gemeinsam verwendetem Schlüssel** : Bei der Authentifizierung mit einem gemeinsam verwendeten Schlüssel werden bei jeder Anforderung an Azure Maps Schlüssel übergeben, die über das Azure Maps-Konto generiert werden. Diese Schlüssel werden oft als Abonnementschlüssel bezeichnet. Aus Sicherheitsgründen empfiehlt es sich, Schlüssel regelmäßig neu zu generieren. Es werden zwei Schlüssel bereitgestellt, damit Sie die Verbindungen mit einem Schlüssel aufrechterhalten können, während Sie den anderen neu generieren. Bei der Neugenerierung Ihrer Schlüssel müssen Sie alle Anwendungen, die auf dieses Konto zugreifen, so aktualisieren, dass die neuen Schlüssel verwendet werden. Weitere Informationen zur Azure Maps-Authentifizierung finden Sie unter [Azure Maps und Azure AD](azure-maps-authentication.md) sowie [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a> **Software Development Kit (SDK)** : Eine Sammlung von Dokumentation, Beispielcodes und Beispiel-Apps, mit denen ein Entwickler Apps mittels einer API erstellen kann.

<a name="spherical-mercator-projection"></a> **Spherical Mercator-Projektion** : Siehe [Web Mercator](#web-mercator). 

<a name="spatial-query"></a> **Abfrage nach räumlichen Daten** : Eine Anforderung an einen Dienst, der einen räumlichen Vorgang ausführt. Beispiele: Radiale Suche oder Suche entlang einer Route.

<a name="spatial-reference"></a> **Räumlicher Verweis** : Ein koordinatenbasiertes lokales, regionales oder globales System, um geografische Entitäten genau zu lokalisieren. Es ist als Koordinatensystem definiert, das verwendet wird, um tatsächlichen Orten auf der Welt Kartenkoordinaten zuzuordnen. Räumliche Verweise stellen sicher, dass räumliche Daten aus verschiedenen Ebenen oder Quellen zur genauen Darstellung oder Analyse integriert werden können. Azure Maps verwendet das Referenzkoordinatensystem [EPSG:3857](https://epsg.io/3857) und WGS 84 für die Eingabe von Geometriedaten.

<a name="sql-spatial"></a> **SQL – räumlich** : Bezieht sich auf die räumlichen Funktionen, die in SQL Azure und SQL Server 2008 und höher integriert sind. Diese räumliche Funktionalität ist auch als .NET-Bibliothek verfügbar, die unabhängig von SQL Server verwendet werden kann. Weitere Informationen finden Sie unter [Räumliche Daten (SQL Server)](/sql/relational-databases/spatial/spatial-data-sql-server).

<a name="subscription-key"></a> **Abonnementschlüssel** : Siehe [Authentifizierung mit gemeinsam verwendetem Schlüssel](#shared-key-authentication).

<a name="synchronous-request"></a> **Synchrone Anforderung** : Eine HTTP-Anforderung, die eine Verbindung öffnet und auf eine Antwort wartet. Browser beschränken die Anzahl der gleichzeitigen HTTP-Anforderungen, die über eine Seite durchgeführt werden können. Wenn gleichzeitig mehrere synchrone Anforderungen mit langer Ausführungsdauer durchgeführt werden, kann dieser Grenzwert überschritten werden. Anforderungen werden in diesem Fall verzögert, bis eine der anderen Anforderungen abgeschlossen ist.

## <a name="t"></a>T

<a name="telematics"></a> **Telematik** : Bezeichnet das Senden, Empfangen und Speichern von Informationen über Telekommunikationsgeräte in Verbindung mit der Steuerung für Remoteobjekte. 

<a name="temporal-data"></a> **Temporale Daten** : Daten, die sich speziell auf Zeiten oder Daten beziehen. Temporale Daten beziehen sich auf diskrete Ereignisse, z.B. Blitzeinschläge, bewegliche Objekte wie Züge oder wiederholte Beobachtungen wie die Anzahl von Verkehrssensoren.

<a name="terrain"></a> **Gelände** : Eine Landfläche mit bestimmten Merkmalen wie sandiges oder bergiges Gelände.

<a name="thematic-maps"></a> **Thematische Karten** : Eine thematische Karte ist eine einfache Karte, die ein Thema zu einem geografischen Gebiet widerspiegeln soll. Ein häufiges Szenario für diesen Kartentyp ist die Einfärbung von Verwaltungsgebieten wie Ländern/Regionen, die auf bestimmten Datenmetriken basieren.

<a name="tile-layer"></a> **Kachelebene** : Eine Ebene, die durch das Zusammenstellen von Kartenkacheln (rechteckige Abschnitte) in einer zusammenhängenden Ebene angezeigt werden. Bei den Kacheln handelt es sich entweder um Rasterbildkacheln oder Vektorkacheln. Rasterkachelebenen werden in der Regel vorzeitig gerendert und als Bilder auf einem Server gespeichert. Rasterkachelebenen beanspruchen unter Umständen viel Speicherplatz. Vektorkachelebenen werden nahezu in Echtzeit innerhalb der Clientanwendung gerendert. Daher sind bei Vektorkachelebenen die serverseitigen Speicheranforderungen geringer.

<a name="time-zone"></a> **Zeitzone** : Eine Region auf der Welt, die aus rechtlichen, kommerziellen und gesellschaftlichen Gründen eine einheitliche Standardzeit einhält. Zeitzonen folgen in der Regel den Grenzen der Länder/Regionen und deren Unterteilung.

<a name="transaction"></a> **Transaktion** : Azure Maps verwendet ein transaktionales Lizenzierungsmodell, bei dem Folgendes gilt:

- Pro angeforderten 15 Karten- bzw. Verkehrskacheln wird eine Transaktion erstellt.
- Für jeden API-Aufruf an einen der Dienste in Azure Maps wird eine Transaktion erstellt. Beispiele für Azure Maps-Dienste wären etwa Suche und Routenplanung.

<a name="transformation"></a> **Transformation** : Der Vorgang, bei dem Daten zwischen verschiedenen geografischen Koordinatensystemen konvertiert werden. Dies können z.B. Daten sein, die im Vereinigten Königreich erfasst wurden und auf dem geografischen Koordinatensystem OSGB 1936 basieren. Azure Maps verwendet eine Variante des Referenzkoordinatensystems [EPSG:3857](https://epsg.io/3857), nämlich WGS 84. Um die Daten ordnungsgemäß anzuzeigen, müssen die Koordinaten von einem System zum anderen transformiert werden.

<a name="traveling-salesmen-problem-tsp"></a> **Problem des Handlungsreisenden (Traveling Salesmen Problem, TSP)** :  Optimierungsproblem von Hamilton, bei dem ein Vertriebsmitarbeiter den effizientesten Weg finden muss, um eine Reihe von Orten zu besuchen, und dann zum Startort zurückkehren muss.  

<a name="trilateration"></a> **Trilateration** : Der Vorgang, bei dem die Position eines Punkts auf der Erdoberfläche in Bezug zu zwei weiteren Punkten bestimmt wird, indem die Abstände zwischen allen drei Punkten gemessen werden.

<a name="turn-by-turn-navigation"></a> **Wegbeschreibungen mit Sprachnavigation** : Eine Anwendung, die bei jedem Routenmanöver Routenanweisungen für die einzelnen Schritte einer Route bereitstellt.

## <a name="v"></a>V

<a name="vector-data"></a> **Vektordaten** : Koordinatenbasierte Daten, die als Punkte, Linien oder Polygone dargestellt werden.

<a name="vector-tile"></a> **Vektorkachel** : Eine Open Data-Spezifikation für die Speicherung von räumlichen Vektordaten mit dem gleichen Kachelsystem wie das Kartensteuerelement. Siehe auch [Kachelebene](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Tourenplanungsproblem (Vehicle Routing Problem, VRP)** : Eine Klasse von Problemen, bei denen eine Gruppe von geordneten Routen für eine Fahrzeugflotte berechnet und gleichzeitig ein Satz von Einschränkungen berücksichtigt wird. Bei diesen Einschränkungen kann es sich beispielsweise um Einschränkungen im Hinblick auf Zustellungszeitfenster, Kapazitäten für mehrere Routen und Reisedauer handeln.

<a name="voronoi-diagram"></a> **Voronoi-Diagramm** : Eine Unterteilung des Raums in Bereiche oder Zellen, die eine Gruppe von geometrischen Objekten (in der Regel Punktfeatures) umgeben. Diese Zellen oder Polygone müssen die Kriterien für Delaunay-Dreiecke erfüllen. Alle Punkte innerhalb eines Bereichs befinden sich näher am Objekt, das es umgibt, als an anderen Objekten in der Gruppe. Voronoi-Diagramme werden häufig verwendet, um Einflussbereiche um geografische Funktionen voneinander abzugrenzen. 

## <a name="w"></a>W

<a name="waypoint"></a> **Wegpunkt** : Ein angegebener geografischer Standort für Navigationszwecke, der durch Längen- und Breitengrad definiert ist. Dieser wird häufig verwendet, um einen Punkt darzustellen, durch den die Route eines Benutzers verläuft.

<a name="waypoint-optimization"></a> **Wegpunktoptimierung** : Der Vorgang, bei dem eine Gruppe von Wegpunkten neu angeordnet wird, um die Fahrtzeit oder -strecke zu minimieren, die zum Passieren aller angegebenen Wegpunkte benötigt wird. Je nach Komplexität der Optimierung wird sie entweder als [Problem des Handlungsreisenden](#traveling-salesmen-problem-tsp) oder als [Tourenplanungsproblem](#vehicle-routing-problem-vrp) bezeichnet.

<a name="web-map-service-wms"></a> **Web Map Service (WMS)** : WMS ist ein OGC-Standard (Open Geographic Consortium), der bildbasierte Kartendienste definiert. WMS-Dienste stellen nach Bedarf Kartenbilder für bestimmte Bereiche innerhalb einer Karte bereit. Bilder umfassen vorab gerenderte Symbologie und können in einem von mehreren benannten Formaten gerendert werden, sofern dies vom Dienst definiert wird.

<a name="web-mercator"></a> **Web Mercator** : Wird auch als „Spherical Mercator-Projektion“ bezeichnet. Hierbei handelt es sich um eine leicht abgewandelte Variante der Mercator-Projektion, die vorrangig in webbasierten Kartierungsprogrammen zum Einsatz kommt. Sie basiert auf den gleichen Formeln, die bei der standardmäßigen Mercator-Projektion für Karten kleinen Maßstabs verwendet werden. Allerdings werden bei Web Mercator die sphärischen Formeln in allen Maßstäben verwendet, während bei Mercator-Karten großen Maßstabs normalerweise die Ellipsenform der Projektion verwendet wird. Die Abweichung ist im globalen Maßstab zwar nicht wahrnehmbar, führt jedoch dazu, dass Karten lokaler Bereiche geringfügig von den tatsächlichen ellipsenförmigen Mercator-Karten gleichen Maßstabs abweichen.

<a name="wgs84"></a> **WGS84** : Eine Gruppe von Konstanten, mit denen räumliche Koordinaten in Bezug zu Standorten auf der Kartenoberfläche gesetzt werden. Das WGS84-Datum wird standardmäßig von den meisten Onlinekartierungsanbietern und GPS-Geräten verwendet. Azure Maps verwendet eine Variante des Referenzkoordinatensystems [EPSG:3857](https://epsg.io/3857), nämlich WGS 84.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Z-Koordinate** : Siehe [Höhe](#altitude). 

<a name="zip-code"></a> **PLZ** : Siehe [Postleitzahl](#postal-code).

<a name="Zoom level"></a> **Zoomfaktor** : Gibt die Detailstufe und den Anteil der Karte an, die sichtbar ist. Wenn die Karte bis auf Stufe 0 verkleinert wird, ist oftmals die gesamte Weltkarte zu sehen. Es werden jedoch nur wenige Details wie Ländernamen und -grenzen sowie die Namen der Ozeane gezeigt. Wenn die Karte bis hin zu Stufe 17 vergrößert wird, zeigt die Karte einen Bereich mit einigen Vierteln und detaillierten Straßeninformationen an. Die höchste Zoomstufe in Azure Maps ist 22. Weitere Informationen finden Sie unter [Zoomfaktoren und Grobraster](zoom-levels-and-tile-grid.md).