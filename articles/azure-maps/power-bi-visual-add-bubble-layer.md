---
title: Hinzufügen einer Blasenebene zum Azure Maps-Visual in Power BI | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie die Blasenebene im Microsoft Azure Maps-Visual für Power BI verwenden.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896224"
---
# <a name="add-a-bubble-layer"></a>Hinzufügen einer Blasenebene

Die **Blasenebene** rendert Daten für einen Ort als skalierte Kreise auf der Karte.

> [!div class="mx-imgBorder"]
> ![Karte mit Punktdaten, die mit der Blasenebene angezeigt werden](media/power-bi-visual/bubble-layer-with-legend-color.png)

Zu Beginn ist die Füllfarbe bei allen Blasen gleich. Wenn ein Feld an den Bucket **Legende** im Bereich **Felder** übergeben wird, werden die Blasen basierend auf ihrer Kategorisierung eingefärbt. Die Konturen der Blasen sind standardmäßig weiß, können jedoch in eine neue Farbe oder durch Aktivieren der Option „High-contrast outline“ (Kontur mit hohem Kontrast) geändert werden. Mit der Option **High-contrast outline** (Kontur mit hohem Kontrast) wird dynamisch eine Konturfarbe zugewiesen, bei der es sich um eine Variante der Füllfarbe mit hohem Kontrast handelt. Dadurch wird sichergestellt, dass die Blasen unabhängig vom Stil der Karte deutlich erkennbar sind. Im Folgenden sind die Haupteinstellungen im Bereich **Format** aufgeführt, die im Abschnitt **Bubble layer** (Blasenebene) verfügbar sind.

| Einstellung               | BESCHREIBUNG    |
|-----------------------|----------------|
| Size                  | Hiermit wird die Größe der einzelnen Blasen eingestellt. Diese Option ist ausgeblendet, wenn ein Feld an den Bucket **Größe** im Bereich **Felder** übergeben wird. Weitere Optionen werden wie im Abschnitt [Skalieren der Blasengröße](#bubble-size-scaling) weiter unten in diesem Artikel beschrieben angezeigt. |
| Füllfarbe            | Hiermit wird die Farbe der einzelnen Blasen eingestellt. Diese Option ist ausgeblendet, wenn ein Feld an den Bucket **Legende** im Bereich **Felder** übergeben wird. Im Bereich **Format** wird dann ein separater Abschnitt **Datenfarben** angezeigt. |
| Füllungstransparenz     | Transparenz der einzelnen Blasen |
| High-contrast outline (Kontur mit hohem Kontrast) | Sorgt zur Verbesserung der Barrierefreiheit für einen Kontrast zwischen der Konturfarbe und der Füllfarbe mithilfe einer Variante der Füllfarbe mit hohem Kontrast |
| Konturfarbe         | Hiermit wird die Farbe für die Blasenkonturen eingestellt. Diese Option ist ausgeblendet, wenn die Option **High-contrast outline** (Kontur mit hohem Kontrast) aktiviert ist. |
| Outline transparency (Konturtransparenz)  | Transparenz der Konturen |
| Outline width (Konturbreite)         | Breite der Konturen in Pixeln |
| Blur (Weichzeichner)                  | Hiermit wird der Grad des auf die Kontur angewendeten Weichzeichners eingestellt. Bei einem Wert von 1 werden die Blasen so stark weichgezeichnet, dass nur der Mittelpunkt nicht transparent ist. Bei einem Wert von 0 wird kein Weichzeichnereffekt angewendet. |
| Pitch alignment (Ausrichtung bei Neigung)       | Diese Einstellung gibt an, wie die Blasen aussehen, wenn die Karte geneigt wird. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Viewport: Die Blasen werden auf der Karte relativ zum Viewport als auf dem Rand stehend angezeigt. (Standard)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Karte: Die Blasen werden flach auf der Oberfläche der Karte gerendert. |
| Zoom scale (Zoomskalierung)            | Hiermit wird festgelegt, wie stark sich die Größe der Blasen relativ zum Zoomfaktor ändern soll. Eine Zoomskalierung von 1 bedeutet keine Skalierung. Bei hohen Werten werden die Blasen beim Verkleinern der Karte kleiner und beim Vergrößern größer. Dies trägt dazu bei, die Übersichtlichkeit der Karte beim Verkleinern zu verbessern, stellt aber auch sicher, dass Punkte beim Vergrößern mehr herausstechen. Bei einem Wert von 1 wird keine Skalierung angewendet. |
| Min zoom (Minimalzoom)              | Minimaler Zoomfaktor, bei dem Kacheln verfügbar sind |
| Max zoom (Maximalzoom)              | Maximaler Zoomfaktor, bei dem Kacheln verfügbar sind |
| Layer position (Ebenenposition)        | Gibt die relative Position der Ebene in Bezug auf andere Kartenebenen an |

## <a name="bubble-size-scaling"></a>Skalieren der Blasengröße

Wenn ein Feld an den Bucket **Größe** im Bereich **Felder** übergeben wird, werden die Blasen relativ zum Measurewert des jeweiligen Datenpunkts skaliert. Die Option **Größe** im Abschnitt **Bubble layer** (Blasenebene) des Bereichs **Format** ist ausgeblendet, wenn ein Feld an den Bucket **Größe** übergeben wird, da die Radien der Blasen dann zwischen einem Minimal- und einem Maximalwert skaliert werden. Die folgenden Optionen werden im Abschnitt **Bubble layer** (Blasenebene) des Bereichs **Format** angezeigt, wenn für den Bucket **Größe** ein Feld angegeben ist.

| Einstellung             | BESCHREIBUNG  |
|---------------------|--------------|
| Mindestgröße            | Minimale Blasengröße beim Skalieren der Daten|
| Max. Größe            | Maximale Blasengröße beim Skalieren der Daten|
| Size scaling method (Größenskalierungsmethode) | Der zum Festlegen der relativen Blasengröße verwendete Skalierungsalgorithmus<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Linear: Bereich von Eingabedaten wird linear der minimalen und maximalen Größe zugeordnet (Standard)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Log: Bereich von Eingabedaten wird logarithmisch der minimalen und maximalen Größe zugeordnet<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier (Kubische Bézierkurve): Geben Sie die Werte für die Parameter X1, Y1, X2 und Y2 einer kubischen Bézierkurve an, um eine benutzerdefinierte Skalierungsmethode zu erstellen. |

Wenn **Log** als **Größenskalierungsmethode** festgelegt ist, stehen die folgenden Optionen zur Verfügung.

| Einstellung   | BESCHREIBUNG      |
|-----------|------------------|
| Log scale (Logarithmische Skalierung) | Die logarithmische Skalierung, die beim Berechnen der Größe der Blasen angewendet werden soll |

Wenn **Cubic-Bezier** (Kubische Bézierkurve) als **Größenskalierungsmethode** festgelegt ist, stehen die folgenden Optionen zum Anpassen der Skalierungskurve zur Verfügung.

| Einstellung | BESCHREIBUNG                           |
|---------|---------------------------------------|
| X1      | X1-Parameter einer kubischen Bézierkurve |
| Y1      | X2-Parameter einer kubischen Bézierkurve |
| X2      | Y2-Parameter einer kubischen Bézierkurve |
| Y2      | Y2-Parameter einer kubischen Bézierkurve |

> [!TIP]
> Unter [https://cubic-bezier.com/](https://cubic-bezier.com/) finden Sie ein praktisches Tool zum Erstellen der Parameter für kubische Bézierkurven.

## <a name="next-steps"></a>Nächste Schritte

Ändern Sie, wie Ihre Daten auf der Karte angezeigt werden:

> [!div class="nextstepaction"]
> [Hinzufügen einer Balkendiagrammebene](power-bi-visual-add-bar-chart-layer.md)

Fügen Sie der Karte mehr Kontext hinzu:

> [!div class="nextstepaction"]
> [Hinzufügen einer Referenzebene](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Anzeigen von Echtzeitverkehrsinfos](power-bi-visual-show-real-time-traffic.md)

Passen Sie das Visual an:

> [!div class="nextstepaction"]
> [Tipps und Tricks zur Farbformatierung in Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Anpassen von Visualisierungstiteln, -hintergründen und -legenden](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)