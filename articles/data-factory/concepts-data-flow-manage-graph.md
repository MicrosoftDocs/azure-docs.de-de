---
title: Verwalten des Graphen des Zuordnungsdatenflusses
description: Erfahren Sie, wie Sie den Graphen des Zuordnungsdatenflusses effektiv verwalten und bearbeiten können.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: 15a4a0ea78ef169480d4494683cb929cfefd16f2
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026971"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Verwalten des Graphen des Zuordnungsdatenflusses

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zuordnungsdatenflüsse werden mithilfe einer Entwurfsoberfläche, dem sog. Datenflussgraphen, erstellt. Im Graphen ist die Transformationslogik von links nach rechts aufgebaut. Weitere Datenströme werden von oben nach unten hinzugefügt. Wählen Sie zum Hinzufügen einer neuen Transformation unten rechts in einer vorhandenen Transformation das Pluszeichen aus.

![Canvas](media/data-flow/canvas2.png "Canvas")

Wenn Ihre Datenflüsse komplexer werden, gehen Sie wie folgt vor, um den Datenflussgraphen effektiv zu nutzen und zu verwalten. 

## <a name="moving-transformations"></a>Verschieben von Transformationen

In Zuordnungsdatenflüssen wird zusammenhängende Transformationslogik als **Datenstrom** bezeichnet. Das Feld **Eingehender Datenstrom** bestimmt, welcher Datenstrom die aktuelle Transformation mit Informationen versorgt. Jede Transformation hat je nach Aufgabe einen oder zwei eingehende Datenströme und stellt einen Ausgangsdatenstrom dar. Das Ausgabeschema der eingehenden Datenströme bestimmt, auf welche Spaltenmetadaten die aktuelle Transformation verweisen kann.

![Knoten verschieben](media/data-flow/move-nodes.png "Knoten verschieben")

Im Gegensatz zur Canvas für Pipelines werden Datenflusstransformationen nicht mithilfe eines Drag & Drop-Modells bearbeitet. Um den eingehenden Datenstrom zu ändern oder eine Transformation zu verschieben, wählen Sie in der Dropdownliste **Eingehender Datenstrom** einen anderen Wert aus. Wenn Sie dies tun, werden alle nachgeordneten Transformationen zusammen mit der bearbeiteten Transformation verschoben. Der Graph wird automatisch entsprechend dem neuen logischen Datenfluss aktualisiert. Wenn Sie den eingehenden Datenstrom in eine Transformation ändern, die bereits eine nachgeordnete Transformation hat, wird eine neue Verzweigung oder ein paralleler Datenstrom erstellt. Erfahren Sie mehr zu [neuen Verzweigungen im Zuordnungsdatenfluss](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Ausblenden und Anzeigen des Diagramms

Beim Bearbeiten Ihrer Transformation können Sie das Konfigurationspanel so erweitern, dass es die gesamte Canvas einnimmt und der Graph ausgeblendet wird. Klicken Sie auf der rechten Seite der Canvas auf das nach oben zeigende Chevron.

![Diagramm ausblenden](media/data-flow/hide-graph.png "Graphen ausblenden")

Wenn das Diagramm ausgeblendet ist, können Sie zwischen Transformationen innerhalb eines Datenstroms wechseln, indem Sie auf **Weiter** oder **Zurück** klicken. Klicken Sie auf das nach unten zeigende Chevron, um den Graphen anzuzeigen.

![Graphen anzeigen](media/data-flow/show-graph.png "Graphen anzeigen")

## <a name="searching-for-transformations"></a>Suchen nach Transformationen

Um eine Transformation in Ihrem Graphen schnell zu finden, klicken Sie über der Zoomeinstellung auf das Symbol **Suchen**.

![Screenshot: Schaltfläche „Suchen“](media/data-flow/search-1.png "Diagramm suchen")

Sie können nach Name oder Beschreibung der Transformation suchen, um eine Transformation zu finden.

![Screenshot: Textfeld für die Suche](media/data-flow/search-2.png "Diagramm suchen")

## <a name="hide-reference-nodes"></a>Ausblenden von Referenzknoten

Wenn der Datenfluss Transformationen des Typs „Join“, „Lookup“, „Exists“ oder „Union“ aufweist, enthält der Datenfluss Referenzknoten für alle eingehenden Datenströme. Wenn Sie den eingenommenen vertikalen Platzbedarf minimieren möchten, können Sie die Referenzknoten minimieren. Klicken Sie dazu mit der rechten Maustaste auf die Canvas, und wählen Sie **Referenzknoten ausblenden** aus.

![Ausblenden von Referenzknoten](media/data-flow/hide-reference-nodes.png "Ausblenden von Referenzknoten")

## <a name="next-steps"></a>Nächste Schritte

Aktivieren Sie nach Fertigstellen der Datenflusslogik den [Debugmodus](concepts-data-flow-debug-mode.md), und testen Sie sie in einer Datenvorschau.
