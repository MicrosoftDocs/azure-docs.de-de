---
title: Zoomfaktoren und Kachelraster in Azure Maps | Microsoft-Dokumentation
description: Erfahren Sie mehr über Zoomfaktoren und Kachelraster in Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e7dcdb960fbd9196aca8b667269a4c6e5a1fb8f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60794953"
---
# <a name="zoom-levels-and-tile-grid"></a>Zoomfaktoren und Linienraster
Azure Maps verwendet das Koordinatensystem der sphärischen Mercator-Projektion (EPSG: 3857).

Die ganzen Welt wird dabei in quadratische Kacheln unterteilt. Azure Maps enthält Raster- und Vektorkacheln für 23 Zoomfaktoren (nummeriert von 0 bis 22). Die ganze Welt würde bei Zoomfaktor 0 auf einer einzigen Kachel passen:

![Weltkachel](./media/zoom-levels-and-tile-grid/world0.png)

Der Zoomfaktor 1 verwendet vier Kacheln zum Rendering der Welt: ein Quadrat aus 2 x 2 Kacheln.

![Weltkachel oben links](media/zoom-levels-and-tile-grid/world1a.png)     ![Weltkachel oben rechts](media/zoom-levels-and-tile-grid/world1c.png) 

![Weltkachel unten links](media/zoom-levels-and-tile-grid/world1b.png)     ![Weltkachel unten rechts](media/zoom-levels-and-tile-grid/world1d.png) 

Jedes zusätzliche Zoomfaktorquadrat unterteilt die Kacheln des vorherigen Zoomfaktors, wodurch ein Raster mit 2<sup>Zoom</sup> x 2<sup>Zoom</sup> entsteht. Zoomfaktor 22 ist ein Raster aus 2<sup>22</sup> x 2<sup>22</sup> oder 4.194.304 x 4.194.304 Kacheln (insgesamt 17.592.186.044.416 Kacheln).

Die interaktiven Kartensteuerelemente in Azure Maps für Web und Android unterstützen 25 Zoomfaktoren, nummeriert von 0 bis 24. Obwohl Straßendaten bei den Zoomfaktoren nur dann verfügbar sind, wenn die jeweiligen Kacheln verfügbar sind.

Die folgende Tabelle enthält die vollständigen Listenwerte für die Zoomfaktoren:

|Zoomfaktor|Meter/Pixel|Seite Meter/Kachel|
|--- |--- |--- |
|0|156543|40075008|
|1|78271,5|20037504|
|2|39135,8|10018764,8|
|3|19567,9|5009382,4|
|4|9783,9|2504678,4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611,5|156544|
|9|305,7|78259,2|
|10|152,9|39142,4|
|11|76,4|19558,4|
|12|38,2|9779,2|
|13|19,1|4889,6|
|14|9,6|2457,6|
|15|4.8|1228,8|
|16|2.4|614,4|
|17|1.2|307,2|
|18|0,6|152,8|
|19|0,3|76,4|
|20|0,15|38,2|
|21|0,075|19,1|
|22|0,0375|9,55|
|23|0,01875|4,775|
|24|0,009375|2,3875|

Kacheln werden durch den Zoomfaktor und die x- und y-Koordinaten entsprechend der Kachelposition im Raster für diesen Zoomfaktor angegeben.

Denken Sie bei der Ermittlung des zu verwendenden Zoomfaktors daran, dass jeder Standort eine feste Position auf der Kachel darstellt. Dies bedeutet, dass die Anzahl der Kacheln, die für die Darstellung einer bestimmten Fläche des Gebiets erforderlich ist, von der jeweiligen Platzierung des Zoomrasters auf der Welt abhängig ist. Wenn zwei Punkte z.B. 900 Meter auseinander liegen, sind *möglicherweise* nur drei Kacheln notwendig, um eine Route zwischen diesen im Zoomfaktor 17 anzuzeigen. Wenn sich der westliche Punkt allerdings rechts von der Kachel und der östliche Punkt links von der Kachel befindet, sind möglicherweise vier Kacheln erforderlich:

![Beispielzoomskala](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Sobald der Zoomfaktor ermittelt wurde, können die x- und y-Werte berechnet werden. Die obere linke Kachel in jedem Zoomraster entspricht x=0, y=0, während die Kachel unten rechts x=2<sup>Zoom -1</sup>, y=2<sup>Zoom-1</sup> entspricht.

Hier wird das Zoomraster für Zoomfaktor 1 dargestellt:

![Zoomraster für Zoomfaktor 1](media/zoom-levels-and-tile-grid/api_x_y.png)
