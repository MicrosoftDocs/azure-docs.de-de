---
title: Datenstrukturen von Mobilitätsdiensten in Microsoft Azure Maps (Vorschau)
description: Grundlegende Informationen dazu, wie Daten in Mobilitätsdiensten von Azure Maps (Vorschau) in Stadtbereiche unterteilt werden. Sehen Sie, in welchen Feldern Informationen zu Haltestellen und Linien öffentlicher Verkehrsmittel gespeichert werden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904719"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Datenstrukturen in Mobilitätsdiensten von Azure Maps (Vorschau) 

> [!IMPORTANT]
> Mobilitätsdienste von Azure Maps befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



In diesem Artikel wird das Konzept von Stadtbereichen in [Mobilitätsdiensten von Azure Maps](/rest/api/maps/mobility) vorgestellt. Es werden einige der üblichen Felder diskutiert, die bei der Abfrage dieses Diensts nach Haltestellen und Linien des öffentlichen Verkehrs zurückgegeben werden. Es wird empfohlen, diesen Artikel vor der Entwicklung mit den APIs für Mobilitätsdienste zu lesen.

## <a name="metro-area"></a>Stadtbereich

Daten von Mobilitätsdiensten (Vorschau) werden nach unterstützten Stadtbereichen gruppiert. Stadtbereiche folgen nicht den Stadtgrenzen. Ein Stadtbereich kann mehrere Städte, eine dicht besiedelte Stadt und umliegende Städte umfassen. Tatsächlich kann ein Land/eine Region ein einzelner Stadtbereich sein. 

Die `metroID` ist die ID eines Stadtbereichs, die zum Aufrufen der [API „Get Metro Area Info“](/rest/api/maps/mobility/getmetroareainfopreview) (Stadtbereichsinformationen abrufen) verwendet werden kann. Verwenden Sie die API „Get Metro“ von Azure Maps, um Typen von öffentlichen Verkehrsmitteln, entsprechende Anbieter, aktive Warnungen und zusätzliche Details für den gewählten Stadtbereich anzufordern. Sie können auch die unterstützten Stadtbereiche und zugehörigen IDs (metroID) anfordern. IDs für den Stadtbereichs können sich ändern.

**metroID:** 522   **Name:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Haltestellen-IDs

Auf Haltestellen kann durch zwei Arten von IDs verwiesen werden: durch die [GFTS-ID](http://gtfs.org/) (General Transit Feed Specification) sowie durch die Azure Maps-Haltestellen-ID. Die GFTS-ID wird als „stopKey“ und die Azure Maps-Haltestellen-ID als „stopID“ bezeichnet. Wenn Sie sich häufig auf Haltestellen von öffentlichen Verkehrsmitteln beziehen, sollten Sie die Azure Maps-Haltestellen-ID verwenden. „stopID“ ist stabiler und wird wahrscheinlich unverändert bleiben, solange die physische Haltestelle besteht. Die GTFS-Haltestellen-ID wird öfter aktualisiert. Beispielsweise kann die GTFS-Haltestellen-ID auf Anforderung des GTFS-Anbieters oder bei der Veröffentlichung einer neuen GTFS-Version aktualisiert werden. Obwohl die physische Haltestelle keine Änderung aufwies, kann sich die GTFS-Haltestellen-ID ändern.

Zum Einstieg können Sie mithilfe der [API zum Abrufen nahegelegener öffentlicher Verkehrsmittel](/rest/api/maps/mobility/getnearbytransitpreview) in der Nähe befindliche Haltestellen anfordern.

## <a name="line-groups-and-lines"></a>Liniengruppen und Linien

Für Mobilitätsdienste (Vorschau) wird ein paralleles Datenmodell für Linien und Liniengruppen verwendet. Dieses Modell wird verwendet, um Änderungen, die von [GTFS](http://gtfs.org/)-Routen und den Fahrtdaten geerbt werden, besser verarbeiten zu können.


### <a name="line-groups"></a>Liniengruppen

Eine Liniengruppe ist eine Entität, in der alle Linien zusammengefasst werden, die logisch zur gleichen Gruppe gehören. Eine Liniengruppe enthält in der Regel zwei Linien: eine von Punkt A zu Punkt B und eine von Punkt B zu Punkt A. Beide Linien würden zum gleichen öffentlichen Verkehrsanbieter gehören und dieselbe Liniennummer aufweisen. Eine Liniengruppe kann aber auch mehr als zwei Linien oder nur eine einzelne Linie enthalten.


### <a name="lines"></a>Linien

Wie oben bereits erwähnt, setzt sich jede Liniengruppe aus einer Gruppe von Linien zusammen. Jede Liniengruppe besteht aus zwei Linien, und jede Linie beschreibt eine Richtung.  Es gibt jedoch Fälle, in denen mehrere Linien eine Liniengruppe bilden. Es gibt z. B. eine Linie, die manchmal einen Umweg durch ein bestimmtes Viertel macht und manchmal nicht. In beiden Fällen operiert sie unter derselben Liniennummer. Außerdem kann eine Liniengruppe aus einer einzelnen Linie bestehen. Eine kreisförmige Linie mit einer einzelnen Richtung ist eine Liniengruppe mit einer Linie.

Zum Einstieg können Sie mithilfe der [API zum Abrufen von Linien öffentlicher Verkehrsmittel](/rest/api/maps/mobility/gettransitlineinfopreview) Liniengruppen anfordern.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie mithilfe von Mobilitätsdiensten (Vorschau) Daten zu öffentlichen Verkehrsmitteln bzw. Routen anfordern:

> [!div class="nextstepaction"]
> [Anfordern von Daten zu öffentlichen Verkehrsmitteln bzw. Routen](how-to-request-transit-data.md)

Erfahren Sie, wie Sie mithilfe von Mobilitätsdiensten (Vorschau) Echtzeitdaten anfordern:

> [!div class="nextstepaction"]
> [Anfordern von Echtzeitdaten](how-to-request-real-time-data.md)

Machen Sie sich mit der API-Dokumentation für Azure Maps-Mobilitätsdienste (Vorschau) vertraut

> [!div class="nextstepaction"]
> [API-Dokumentation zu Mobilitätsdiensten](/rest/api/maps/mobility)