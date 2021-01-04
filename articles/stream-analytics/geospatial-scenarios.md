---
title: Geofencing und räumliche Aggregation per Azure Stream Analytics
description: In diesem Artikel wird beschrieben, wie Sie Azure Stream Analytics für das Geofencing und die räumliche Aggregation verwenden.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 90d8c70ac0a34470e8121d2234fd6b898cfed1ab
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130594"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Szenarien mit Geofencing und räumlicher Aggregation per Azure Stream Analytics

Bei integrierten räumlichen Funktionen können Sie Azure Stream Analytics verwenden, um beispielsweise Anwendungen für Szenarien wie Flottenmanagement, Fahrgemeinschaften, vernetzte Autos und Asset Tracking zu entwickeln.

## <a name="geofencing"></a>Geofencing

Azure Stream Analytics unterstützt Echtzeit-Geofencingberechnungen mit geringer Latenz in der Cloud und für die IoT Edge-Runtime.

### <a name="geofencing-scenario"></a>Geofencingszenario

Ein Fertigungsunternehmen muss Objekte (Assets) in seinen Gebäuden nachverfolgen. Jedes Gerät wurde mit einem GPS-Modul ausgestattet, und es sollen Benachrichtigungen eingehen, wenn ein Gerät einen bestimmten Bereich verlässt.

Die in diesem Beispiel verwendeten Referenzdaten enthalten die Geofenceinformationen für die Gebäude und die Geräte, die in den einzelnen Gebäuden zulässig sind. Beachten Sie hierbei, dass Referenzdaten entweder statisch sein können oder sich nur langsam ändern. Für dieses Szenario werden statische Referenzdaten genutzt. Ein Datenstrom gibt fortlaufend die Geräte-ID und die aktuelle Position aus.

### <a name="define-geofences-in-reference-data"></a>Definieren von Geofences in Referenzdaten

Ein Geofence kann mit einem GeoJSON-Objekt definiert werden. Für Aufträge mit Kompatibilitätsversion 1.2 und höher können Geofences auch im WKT-Format (Well Known Text) als `NVARCHAR(MAX)` definiert werden. WKT ist ein OGC-Standard (Open Geospatial Consortium) zum Darstellen von räumlichen Daten im Textformat.

Für die integrierten räumlichen Funktionen können definierte Geofences verwendet werden, um zu ermitteln, ob sich ein Element innerhalb oder außerhalb eines bestimmten Geofencepolygons befindet.

Die folgende Tabelle enthält ein Beispiel für Geofence-Referenzdaten, die in Azure-Blobspeicher oder in einer Azure SQL-Tabelle gespeichert werden können. Jeder Standort (Site) wird durch ein räumliches Polygon repräsentiert, und jedem Gerät ist eine zulässige Standort-ID zugeordnet.

|SiteID|SiteName|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|„Redmond Building 41“|„POLYGON((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))“|„B“|
|2|„Redmond Building 40“|„POLYGON((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))“|„A“|
|3|„Redmond Building 22“|„POLYGON((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))“|„C“|

### <a name="generate-alerts-with-geofence"></a>Generieren von Warnungen mit Geofence

Geräte können ihre ID und ihren Standort minütlich über einen Datenstrom mit dem Namen `DeviceStreamInput` ausgeben. Die folgende Tabelle enthält einen Eingabedatenstrom.

|DeviceID|GeoPosition|
|--------|-----------|
|„A“|„POINT(-122.13292341559497 47.636318374032726)“|
|„B“|„POINT(-122.13338475554553 47.63743531308874)“|
|„C“|„POINT(-122.13354001095752 47.63627622505007)“|

Sie können eine Abfrage schreiben, mit der der Gerätedatenstrom mit den Geofence-Referenzdaten verknüpft wird und jedes Mal eine Warnung generiert wird, wenn sich ein Gerät außerhalb eines zulässigen Gebäudes befindet.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

In der folgenden Abbildung sind die Geofences dargestellt. Sie können anhand des Eingabedatenstroms verfolgen, wo sich die Geräte befinden.

![Geofences von Gebäuden](./media/geospatial-scenarios/building-geofences.png)

Gerät „C“ befindet sich innerhalb des Gebäudes mit der ID „2“, und dies ist laut den Referenzdaten nicht zulässig. Dieses Gerät sollte sich im Gebäude mit der ID „3“ befinden. Beim Ausführen dieses Auftrags wird eine Warnung für diesen spezifischen Verstoß generiert.

### <a name="site-with-multiple-allowed-devices"></a>Standort mit mehreren zulässigen Geräten

Wenn an einem Standort mehrere Geräte zulässig sind, kann in `AllowedDeviceID` ein Array mit Geräte-IDs definiert werden. Darüber hinaus kann eine benutzerdefinierte Funktion für die `WHERE`-Klausel verwendet werden, um zu überprüfen, ob die Geräte-ID aus dem Datenstrom mit einer Geräte-ID in dieser Liste übereinstimmt. Weitere Informationen finden Sie im Tutorial [JavaScript-UDF](stream-analytics-javascript-user-defined-functions.md) (Cloudaufträge) bzw. im Tutorial [C#-UDF](stream-analytics-edge-csharp-udf.md) (Edgeaufträge).

## <a name="geospatial-aggregation"></a>Räumliche Aggregation

Azure Stream Analytics unterstützt die räumliche Aggregation in Echtzeit mit geringer Latenz in der Cloud und für die IoT Edge-Runtime.

### <a name="geospatial-aggregation-scenario"></a>Szenario für räumliche Aggregation

Ein Taxiunternehmen möchte eine Echtzeitanwendung entwickeln, um Fahrerinnen und Fahrer mit freier Kapazität in die Bereiche der Stadt zu leiten, in denen derzeit eine hohe Nachfrage besteht.

Das Unternehmen speichert logische Regionen der Stadt als Referenzdaten. Jede Region wird per RegionID, RegionName und Geofence definiert.

### <a name="define-the-geofences"></a>Definieren der Geofences

Die folgende Tabelle enthält ein Beispiel für Geofence-Referenzdaten, die in Azure-Blobspeicher oder in einer Azure SQL-Tabelle gespeichert werden können. Jede Region wird durch ein räumliches Polygon repräsentiert, das genutzt wird, um eine Korrelation mit den Anforderungen aus den Streamingdaten zu erzielen.

Diese Polygone dienen nur zu Referenzzwecken und stellen keine wirklichen logischen oder physischen Trennungen der Stadt dar.

|RegionID|RegionName|Geofence|
|--------|----------|--------|
|1|„SoHo“|„POLYGON((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264))“|
|2|„Chinatown“|„POLYGON((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))“|
|3|„Tribeca“|„POLYGON((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))“|

### <a name="aggregate-data-over-a-window-of-time"></a>Aggregieren von Daten über ein Zeitfenster

Die folgende Tabelle enthält Streamingdaten zu Taxifahrten.

|UserID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|„A“|„POINT(-74.00726861389182 40.71610611981975)“|„POINT(-73.98615095917779 40.703107386025835)“|„2019-03-12T07:00:00Z“|
|„B“|„POINT(-74.00249841021645 40.723827238895666)“|„POINT(-74.01160699942085 40.71378884930115)“|„2019-03-12T07:01:00Z“|
|„C“|„POINT(-73.99680120565864 40.716439898624024)“|„POINT(-73.98289663412544 40.72582343969828)“|„2019-03-12T07:02:00Z“|
|„D“|„POINT(-74.00741090068288 40.71615626755086)“|„POINT(-73.97999843120539 40.73477895807408)“|„2019-03-12T07:03:00Z“|

Mit der folgenden Abfrage wird der Gerätedatenstrom mit den Geofence-Referenzdaten verknüpft, und in einem Zeitfenster von 15 Minuten wird jede Minute die Anzahl von Anforderungen pro Region berechnet.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Mit dieser Abfrage wird für die letzten 15 Minuten jede Minute die Anzahl von Anforderungen für jede Region der Stadt ausgegeben. Diese Informationen können über das Power BI-Dashboard leicht angezeigt oder in Form von SMS an alle Fahrer übermittelt werden, wenn eine Integration in Dienste wie beispielsweise Azure Functions erfolgt.

In der Abbildung unten ist die Ausgabe der Abfrage für das Power BI-Dashboard dargestellt. 

![Ergebnisausgabe im Power BI-Dashboard](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Stream Analytics-Geofunktionen](stream-analytics-geospatial-functions.md)
* [GeoSpatial Functions (Azure Stream Analytics)](/stream-analytics-query/geospatial-functions) (Geofunktionen (Azure Stream Analytics))