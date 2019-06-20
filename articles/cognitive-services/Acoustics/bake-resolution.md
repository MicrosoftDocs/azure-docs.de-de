---
title: 'Projekt Akustik: Bakingauflösung'
titlesuffix: Azure Cognitive Services
description: Diese konzeptuelle Übersicht beschreibt den Unterschied zwischen groben und feinen Auflösungen beim Akustikbaking.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 7dbf63ba39c5dcdebb363cfc37a45f0216a07497
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335446"
---
# <a name="project-acoustics-bake-resolution"></a>Projekt Akustik: Bakingauflösung
Diese konzeptuelle Übersicht beschreibt den Unterschied zwischen groben und feinen Auflösungen beim Akustikbaking. Sie wählen diese Einstellung während des Schritts zur Stichprobenerstellung des Bakingworkflows aus.

## <a name="Coarse-vs-Fine-Resolution"></a>Niedrige und hohe Auflösung im Vergleich

Der einzige Unterschied zwischen den Auflösungseinstellungen „Coarse“ (Niedrig) und „Fine“ (Hoch) ist die Häufigkeit, mit der die Simulation ausgeführt wird. Bei der Einstellung „Fine“ (Hoch) wird eine doppelt so hohe Häufigkeit wie bei „Coarse“ (Niedrig) verwendet. Dies hat einige Auswirkungen auf die Akustiksimulation:

* Die Wellenlänge ist bei „Coarse“ (Niedrig) doppelt so lang wie bei „Fine“ (Hoch), daher sind die Voxel doppelt so groß.
* Die Simulationszeit hängt direkt von der Voxelgröße ab, daher ist ein Bakingvorgang mit der Einstellung „Coarse“ (Niedrig) bis zu 16-mal schneller als mit der Einstellung „Fine“ (Hoch).
* „Portale“ (z. B. Türen oder Fenster), die kleiner als die Voxelgröße sind, können nicht simuliert werden. Die Einstellung „Grob“ kann dazu führen, dass einige der kleineren Portale nicht simuliert werden, sodass diese zur Laufzeit keinen Sound übergeben. Sie können feststellen, ob dieser Fall eintritt, indem Sie die Voxels anzeigen.
* Die niedrigere Simulationshäufigkeit führt zu einer verringerten Beugung von Ecken und Kanten.
* Soundquellen können nicht in „aufgefüllten“ Voxeln (also Voxeln, die Geometrie enthalten) platziert werden. Andernfalls werden keine Sounds wiedergegeben. Es ist schwieriger, Soundquellen so zu platzieren, dass sie sich nicht in den größeren Voxeln der Einstellung „Grob“ befinden, als es bei der Einstellung „Fein“ der Fall ist.
* Die größeren Voxels überschneiden sich wie im Folgenden dargestellt mehr mit Portalen. Das erste Bild dieses Türbereichs wurde mit der Auflösung „Coarse“ (Niedrig) erstellt, das zweite mit der Auflösung „Fine“ (Hoch). An den roten Markierungen ist erkennbar, dass die Überschneidung mit der Einstellung „Fine“ (Hoch) wesentlich geringer ist. Die blaue Linie stellt den durch die Geometrie definierten Eingang dar, während die rote Linie das Akustikportal darstellt, das von der Voxelgröße definiert wird. Wie diese Überschneidung sich in einer bestimmten Situation auswirkt, hängt vollständig davon ab, wie die Voxels und die Geometrie des Portals ausgerichtet sind. Dies wird von der Größe und Position der Objekte in der Szene bestimmt.

![Screenshot der Voxel mit niedriger Auflösung im Eingang in Unreal](media/unreal-coarse-bake.png)

![Screenshot der Voxel mit hoher Auflösung im Eingang in Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die Auflösungseinstellungen „Grob“ und „Fein“ einmal selbst mithilfe unserer Plug-Ins für [Unreal](unreal-baking.md) oder [Unity](unity-baking.md).