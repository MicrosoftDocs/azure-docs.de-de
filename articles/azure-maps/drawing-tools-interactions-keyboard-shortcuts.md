---
title: Interaktionsarten und Tastenkombinationen für Zeichentools auf einer Karte | Microsoft Azure Maps
description: Zeichnen und Bearbeiten von Formen mithilfe einer Maus, eines Touchscreens oder einer Tastatur im Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b609a21348222150faf5fb828f2cc38ca6658f5d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895816"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Interaktionsarten und Tastenkombinationen für Zeichentools im Zeichentools-Modul

In diesem Artikel werden die verschiedenen Möglichkeiten zum Zeichnen und Bearbeiten von Formen mithilfe einer Maus, eines Touchscreens oder von Tastenkombinationen beschrieben.

Der Zeichnungs-Manager unterstützt drei verschiedene Arten der Interaktion mit der Karte zum Zeichnen von Formen.

* `click`: Koordinaten werden hinzugefügt, wenn mit der Maus oder per Toucheingabe geklickt wird.
* `freehand `: Koordinaten werden hinzugefügt, wenn die Maus auf die Karte gezogen bzw. die gleiche Geste per Toucheingabe vollzogen wird.
* `hybrid`: Koordinaten werden hinzugefügt, wenn mit der Maus oder per Toucheingabe geklickt oder gezogen wird.

## <a name="how-to-draw-shapes"></a>Zeichnen von Formen

 Bevor eine Form gezeichnet werden kann, legen Sie die `drawingMode`-Option des Zeichnungs-Managers auf eine unterstützte Zeicheneinstellung fest. Diese Einstellung kann programmiert oder durch Drücken einer der Zeichenschaltflächen auf der Symbolleiste aufgerufen werden. Der Zeichenmodus bleibt auch nach dem Zeichnen einer Form aktiviert, sodass es einfach ist, zusätzliche Formen desselben Typs zu zeichnen. Legen Sie den Zeichenmodus programmgesteuert auf einen Leerlaufzustand fest. Sie können auch in einen Leerlaufzustand wechseln, indem Sie auf der Symbolleiste auf die Schaltfläche für die aktuellen Zeichenmodi klicken.

In den folgenden Abschnitten werden alle verschiedenen Weisen behandelt, wie Formen auf die Karte gezeichnet werden können.

### <a name="how-to-draw-a-point"></a>Zeichnen eines Punkts

Wenn sich der Zeichnungs-Manager im `draw-point`-Zeichenmodus befindet, können die folgenden Aktionen ausgeführt werden, um Punkte auf der Karte zu zeichnen. Diese Methoden funktionieren mit allen Interaktionsarten.

**Zeichnen beginnen**
 - Klicken Sie mit der linken Maustaste, oder tippen Sie auf die Karte, um der Karte einen Punkt hinzuzufügen. 
 - Wenn sich die Maus über der Karte befindet, drücken Sie die `F`-TASTE, dann wird ein Punkt an der Koordinate des Mauszeigers hinzugefügt. Diese Methode bietet höhere Genauigkeit beim Hinzufügen eines Punkts zur Karte. Es findet eine geringere Bewegung der Maus als beim Drücken der linken Maustaste statt.
 - Fahren Sie mit dem Klicken, Tippen oder Drücken von `F` fort, um der Karte weitere Punkte hinzuzufügen.
 
**Zeichnen beenden**
 - Klicken Sie auf der Zeichnen-Symbolleiste auf eine beliebige Schaltfläche. 
 - Legen Sie den Zeichenmodus programmgesteuert fest. 
 - Drücken Sie die `C`-Taste.

**Zeichnen abbrechen**
 - Drücken Sie die `Escape`-Taste.

### <a name="how-to-draw-a-line"></a>Zeichnen einer Linie

Wenn sich der Zeichnungs-Manager im `draw-line`-Modus befindet, können abhängig vom Interaktionsmodus die folgenden Aktionen ausgeführt werden, um Punkte auf der Karte zu zeichnen.

**Zeichnen beginnen**
 - Klickmodus
   * Klicken Sie mit der linken Maustaste, oder tippen Sie auf die Karte, um der Karte jeden Punkt einer Linie hinzuzufügen. Bei jedem Klicken oder Tippen wird der Linie eine Koordinate hinzugefügt. 
   * Wenn sich die Maus über der Karte befindet, drücken Sie die `F`-TASTE, dann wird ein Punkt an der Koordinate des Mauszeigers hinzugefügt. Diese Methode bietet höhere Genauigkeit beim Hinzufügen eines Punkts zur Karte. Es findet eine geringere Bewegung der Maus als beim Drücken der linken Maustaste statt.
   * Klicken Sie weiter, bis der Linie alle gewünschten Punkte hinzugefügt wurden.
 - Freihandmodus
   * Drücken Sie die linke Maustaste, oder tippen Sie auf die Karte, und ziehen Sie die Maus, oder ziehen Sie mit Fingereingabe. Der Linie werden Koordinaten hinzugefügt, während sich der Mauszeiger oder Berührungspunkt auf der Karte bewegt. Sobald das Maus- oder Berührungsereignis ausgelöst wird, ist das Zeichnen abgeschlossen. Die Häufigkeit, mit der Koordinaten hinzugefügt werden, wird durch die Option `freehandInterval` des Zeichnungs-Managers bestimmt.
 - Hybridmodus
   * Wechseln Sie beim Zeichnen einer einzelnen Linie nach Belieben zwischen Klick- und Freihandmodus. Klicken Sie beispielsweise auf einige Punkte, halten und ziehen Sie die Maus, um eine Reihe von Punkten hinzuzufügen, und klicken Sie dann auf einige weitere Punkte. 

**Zeichnen beenden**
 - Hybrid-/Klickmodus
   * Doppelklicken Sie am letzten Punkt auf die Karte. 
   * Klicken Sie auf der Zeichnen-Symbolleiste auf eine beliebige Schaltfläche. 
   * Legen Sie den Zeichenmodus programmgesteuert fest. 
 - Freihandmodus
   * Lassen Sie die Maustaste oder den Berührungspunkt los.
 - Drücken Sie die `C`-Taste.

**Zeichnen abbrechen**
 - Drücken Sie die `Escape`-Taste.

### <a name="how-to-draw-a-polygon"></a>Zeichnen eines Polygons

Wenn sich der Zeichnungs-Manager im `draw-polygon`-Modus befindet, können abhängig vom Interaktionsmodus die folgenden Aktionen ausgeführt werden, um Punkte auf der Karte zu zeichnen.

**Zeichnen beginnen**
 - Klickmodus
   * Klicken Sie mit der linken Maustaste, oder tippen Sie auf die Karte, um der Karte jeden Punkt eines Polygons hinzuzufügen. Bei jedem Klicken oder Tippen wird dem Polygon eine Koordinate hinzugefügt. 
   * Wenn sich die Maus über der Karte befindet, drücken Sie die `F`-TASTE, dann wird ein Punkt an der Koordinate des Mauszeigers hinzugefügt. Diese Methode bietet höhere Genauigkeit beim Hinzufügen eines Punkts zur Karte. Es findet eine geringere Bewegung der Maus als beim Drücken der linken Maustaste statt.
   * Klicken Sie weiter, bis dem Polygon alle gewünschten Punkte hinzugefügt wurden.
 - Freihandmodus
   * Drücken Sie die linke Maustaste, oder tippen Sie auf die Karte, und ziehen Sie die Maus, oder ziehen Sie mit Fingereingabe. Dem Polygon werden Koordinaten hinzugefügt, während sich der Mauszeiger oder Berührungspunkt auf der Karte bewegt. Sobald das Maus- oder Berührungsereignis ausgelöst wird, ist das Zeichnen abgeschlossen. Die Häufigkeit, mit der Koordinaten hinzugefügt werden, wird durch die Option `freehandInterval` des Zeichnungs-Managers bestimmt.
 - Hybridmodus
   * Wechseln Sie beim Zeichnen eines einzelnen Polygons nach Belieben zwischen Klick- und Freihandmodus. Klicken Sie beispielsweise auf einige Punkte, halten und ziehen Sie die Maus, um eine Reihe von Punkten hinzuzufügen, und klicken Sie dann auf einige weitere Punkte. 

**Zeichnen beenden**
 - Hybrid-/Klickmodus
   * Doppelklicken Sie am letzten Punkt auf die Karte. 
   * Klicken Sie auf den ersten Punkt im Polygon.
   * Klicken Sie auf der Zeichnen-Symbolleiste auf eine beliebige Schaltfläche. 
   * Legen Sie den Zeichenmodus programmgesteuert fest. 
 - Freihandmodus
   * Lassen Sie die Maustaste oder den Berührungspunkt los.
 - Drücken Sie die `C`-Taste.

**Zeichnen abbrechen**
 - Drücken Sie die `Escape`-Taste.

### <a name="how-to-draw-a-rectangle"></a>Zeichnen eines Rechtecks

Wenn sich der Zeichnungs-Manager im `draw-rectangle`-Modus befindet, können abhängig vom Interaktionsmodus die folgenden Aktionen ausgeführt werden, um Punkte auf der Karte zu zeichnen. Die generierte Form folgt der [erweiterten GeoJSON-Spezifikation für Rechtecke](extend-geojson.md#rectangle).

**Zeichnen beginnen**
 - Drücken Sie die linke Maustaste, oder tippen Sie auf die Karte, um die erste Ecke des Rechtecks hinzuzufügen, und ziehen Sie, um das Rechteck zu erstellen. 

**Zeichnen beenden**
 - Lassen Sie die Maustaste oder den Berührungspunkt los.
 - Legen Sie den Zeichenmodus programmgesteuert fest. 
 - Drücken Sie die `C`-Taste.

**Zeichnen abbrechen**
 - Drücken Sie die `Escape`-Taste.

### <a name="how-to-draw-a-circle"></a>Zeichnen eines Kreises

Wenn sich der Zeichnungs-Manager im `draw-circle`-Modus befindet, können abhängig vom Interaktionsmodus die folgenden Aktionen ausgeführt werden, um Punkte auf der Karte zu zeichnen. Die generierte Form folgt der [erweiterten GeoJSON-Spezifikation für Kreise](extend-geojson.md#circle).

**Zeichnen beginnen**
 - Drücken Sie die linke Maustaste, oder tippen Sie auf die Karte, um den Mittelpunkt des Kreises hinzuzufügen, und ziehen Sie dann, um dem Kreis einen Radius zu geben. 

**Zeichnen beenden**
 - Lassen Sie die Maustaste oder den Berührungspunkt los.
 - Legen Sie den Zeichenmodus programmgesteuert fest. 
 - Drücken Sie die `C`-Taste.

**Zeichnen abbrechen**
 - Drücken Sie die `Escape`-Taste.

## <a name="keyboard-shortcuts"></a>Tastenkombinationen

Die Zeichnungstools unterstützen Tastenkombinationen. Diese Tastenkombinationen sind funktionsfähig, wenn die Karte den Fokus besitzt.

| Key      | Action                            |
|----------|-----------------------------------|
| `C` | Beendet alle aktuell ausgeführten Zeichenvorgänge und legt den Zeichenmodus auf „Leerlauf“ fest. Der Fokus wird auf das Kartenelement der obersten Ebene verschoben.  |
| `Escape` | Bricht alle aktuell ausgeführten Zeichenvorgänge ab und legt den Zeichenmodus auf „Leerlauf“ fest. Der Fokus wird auf das Kartenelement der obersten Ebene verschoben.  |
| `F` | Fügt einem Punkt, einer Linie oder einem Polygon eine Koordinate hinzu, wenn sich der Mauszeiger über der Karte befindet. Äquivalente Aktion zum Klicken auf die Karte im Klick- oder Hybridmodus. Diese Tastenkombination ermöglicht präziseres und schnelleres Zeichnen. Sie können eine Hand zum Positionieren der Maus verwenden und mit der anderen die Taste drücken, ohne dass die Maus durch das Drücken bewegt wird. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Kursen im Zeichentools-Modul:

> [!div class="nextstepaction"]
> [Zeichnungs-Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Zeichnungssymbolleiste](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)