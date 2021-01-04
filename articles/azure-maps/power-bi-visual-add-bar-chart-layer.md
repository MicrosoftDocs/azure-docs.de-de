---
title: Hinzufügen einer Balkendiagrammebene zum Azure Maps-Visual in Power BI | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie die Balkendiagrammebene im Microsoft Azure Maps-Visual für Power BI verwenden.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 136676b46df6e32f98dca99fccba19d4aa369dbe
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896275"
---
# <a name="add-a-bar-chart-layer"></a>Hinzufügen einer Balkendiagrammebene

Mit der **Balkendiagrammebene** können Sie Ihren Daten eine weitere Dimension hinzufügen, denn diese erlauben die Visualisierung von Standortdaten als 3D-Balken oder -Zylinder auf der Karte. Ähnlich wie die Blasenebene kann die Balkendiagrammebene mithilfe von Farbe und relativer Höhe ganz einfach zwei Metriken gleichzeitig visualisieren. Damit die Balken eine Höhe aufweisen, muss dem Bucket **Größe** des Bereichs **Felder** ein Measure hinzugefügt werden. Andernfalls werden Balken ohne Höhe abhängig von der Option **Balkenform** als flache Quadrate oder Kreise dargestellt.

> [!div class="mx-imgBorder"]
> ![Karte mit Punktdaten, die anhand der Balkendiagrammebene angezeigt werden](media/power-bi-visual/bar-chart-layer-styled.png)

Benutzer können die Karte neigen und drehen, um Ihre Daten aus unterschiedlichen Perspektiven anzuzeigen. Das Kippen oder Neigen der Karte ist mithilfe einer der folgenden Methoden möglich.

-   Aktivieren Sie die Option **Navigationssteuerelemente** in den **Karteneinstellungen** des Bereichs **Format** . Dadurch wird eine Schaltfläche zum Neigen der Karte hinzugefügt.
-   Halten Sie die rechte Maustaste gedrückt, und ziehen Sie die Maus nach oben oder unten.
-   Berühren Sie auf einem Touchscreen die Karte mit zwei Fingern, und ziehen Sie diese zusammen nach oben oder unten.
-   Setzen Sie den Fokus auf die Karte, halten Sie die **UMSCHALTTASTE** gedrückt, und drücken Sie dann die **NACH-OBEN-** oder **NACH-UNTEN-TASTE** .

Das Drehen der Karte ist mithilfe einer der folgenden Methoden möglich.

-   Aktivieren Sie die Option **Navigationssteuerelemente** in den **Karteneinstellungen** des Bereichs **Format** . Dadurch wird eine Schaltfläche zum Drehen der Karte hinzugefügt.
-   Halten Sie die rechte Maustaste gedrückt, und ziehen Sie die Maus nach rechts oder links.
-   Berühren Sie auf einem Touchscreen die Karte mit zwei Fingern, um sie zu drehen.
-   Setzen Sie den Fokus auf die Karte, halten Sie die **UMSCHALTTASTE** gedrückt, und drücken Sie dann die **NACH-LINKS-** oder **NACH-RECHTS-TASTE** .

Im Folgenden finden Sie alle Einstellungen im Bereich **Format** , die im Abschnitt **Balkendiagrammebene** verfügbar sind.

| Einstellung              | BESCHREIBUNG      |
|----------------------|------------------|
| Balkenform            | Die Form des 3D-Balkens.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Rechteck: Die Balken werden als Quader gerendert.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Zylinder: Die Balken werden als Zylinder gerendert. |
| Höhe               | Die Höhe der einzelnen Balken. Wenn ein Feld an den Bucket **Größe** des Bereichs **Felder** übergeben wird, werden die Balken relativ zu diesem Höhenwert skaliert. |
| Höhe beim Zoomen skalieren | Gibt an, ob die Höhe der Balken relativ zum Zoomfaktor skaliert werden soll. |
| Breite                | Die Breite der einzelnen Spalten.  |
| Breite beim Zoomen skalieren  | Gibt an, ob die Breite der Balken relativ zum Zoomfaktor skaliert werden soll.  |
| Füllfarbe           | Die Farbe der einzelnen Balken. Diese Option wird ausgeblendet, wenn ein Feld dem Bucket **Legende** im Bereich **Felder** übergeben wird. Im Bereich **Format** wird dann ein separater Abschnitt **Datenfarben** angezeigt. |
| Transparenz         | Transparenz der einzelnen Balken. |
| Mindestzoom             | Mindestzoomfaktor, bei dem Kacheln verfügbar sind. |
| Maximaler Zoom             | Maximaler Zoomfaktor, bei dem Kacheln verfügbar sind. |
| Ebenenposition       | Gibt die relative Position der Ebene in Bezug auf andere Kartenebenen an. |

> [!NOTE]
> Wenn die Balken einen niedrigen Breitenwert aufweisen und die Option **Breite beim Zoomen skalieren** deaktiviert ist, werden die Balken beim Herauszoomen möglicherweise nicht mehr dargestellt, weil ihre gerenderte Breite weniger als einen Pixel beträgt. Ist die Option **Breite beim Zoomen skalieren** jedoch aktiviert, werden bei Änderung des Zoomfaktors zusätzliche Berechnungen durchgeführt, die sich bei großen Datasets auf die Leistung auswirken können.

## <a name="next-steps"></a>Nächste Schritte

Fügen Sie der Karte mehr Kontext hinzu:

> [!div class="nextstepaction"]
> [Hinzufügen einer Referenzebene](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Anzeigen von Verkehrsinfo in Echtzeit](power-bi-visual-show-real-time-traffic.md)

Passen Sie das Visual an:

> [!div class="nextstepaction"]
> [Tipps und Tricks zur Farbformatierung in Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Anpassen der Titel, Hintergründe und Legenden von Visualisierungen](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)